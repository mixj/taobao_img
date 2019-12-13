## 记录

### 起因

* 偶尔看到有淘宝官方出的买家秀照片墙
* 为了符合社会主义核心价值观我切换成了 [书店][1]
* 应广大网友号召,想把这些图片都抓下来,方便大家预览

### 过程
#### 分析页面
* 从network找到加载页面里所有加载的资源
* 找到了与之有关的请求.
![image](https://upload-images.jianshu.io/upload_images/3982860-3fafbc360a9331cb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 该请求是 GET 请求,参数都在 URL 中
![image](https://upload-images.jianshu.io/upload_images/3982860-04f6ad8c66684cad.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 接下来关注请求头
![image](https://upload-images.jianshu.io/upload_images/3982860-f2c0f06c3e6a836f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
	
#### 这时候祭出 postman 来模拟一下

* 很方便, URL 输入进去,会自动识别其中参数
![image](https://upload-images.jianshu.io/upload_images/3982860-e92be238bcdb5375.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 请求头处理也一样
![image](https://upload-images.jianshu.io/upload_images/3982860-18f15c3c4bc12374.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 发送请求,成功得到json
![image](https://upload-images.jianshu.io/upload_images/3982860-0c2c8e15ba96bd49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 筛查必要请求参数,这时候,可以将参数和请求头,取消勾选,最后剩下必要参数

必要参数 | 备注 |
---|---|
appKey|固定 `12574478`|
t|毫秒数(可固定)|
sign|文后`加密规则`|
data| 文后`加密规则`中的 `params_data` |
	
![image](https://upload-images.jianshu.io/upload_images/3982860-2291c97f4d719e10.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
	
必要头部 |
---|
Cookie|

![image](https://upload-images.jianshu.io/upload_images/3982860-5d76cfd135a3559c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
	
* 可以将请求参数中 data 进行 urldecode ,这样看的更加清晰,我直接拿控制台中的替换了
* cookie 也要进行筛选,找到必要的 key&value (以下是我挨个删除,留下最后必要的)

Cookie 必要key |
---|
\_m\_h5_tk |
\_m\_h5\_tk_enc |

![image](https://upload-images.jianshu.io/upload_images/3982860-48334a338c5bdd95.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 所有必要参数都找到了
* 通过分析参数内容,看到 `sign` 不是能看懂的,应该是通过加密的
* 在所有请求资源中找 `sign` 生成方法
* 过程就是挨个看,直接上结果了.
![image](https://upload-images.jianshu.io/upload_images/3982860-7ca857361cb37623.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image](https://upload-images.jianshu.io/upload_images/3982860-64f3e530bfd9fda1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 可以看出对以下几个参数进行加密.可以加断点查看信息.
![image](https://upload-images.jianshu.io/upload_images/3982860-38b643da4cf77d12.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 加密规则

```
let params = {
  nodeId:'',
  sellerId:sellerId,
  pagination:{
      direction:'1',
      hasMore:'true',
      pageNum:1,
      pageSize:30,
  }
};

let params_data = {
    params: JSON.stringify(params),
    cursor : 1,
    pageNum : 1,
    pageId: 5703,
    env: '1'
}

params_data = JSON.stringify(params_data);

let sign = `${token}&${timestr}&${appKey}&${params_data}`

sign = md5(sign);
```


[1]: <http://t.cn/EJpYaRj>  "书店"
