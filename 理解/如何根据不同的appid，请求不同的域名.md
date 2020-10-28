如何根据不同的appid，请求不同的域名？

小程序开发的过程中，我们创建了一个测试版小程序，跟正式版的小程序是不同的appid，请求的服务器域名也不一样。切换appid的同时，必须修改配置文件，应用对应的服务器域名。

那能不能根据不同的appid，自动切换到不同的域名呢？

官方给出的是使用这个方法：wx.getAccountInfoSync()

https://developers.weixin.qq.com/community/develop/doc/000ee4324589282b866afb00c56809

该方法返回

```json
{
	"miniProgram": {
        "appId": "xxxxxxxxxxxxxxx",
        "envVersion": "develop", // develop：开发版， trial：体验版，release：发布版
        "version": ""
    }
}
```

因此可以先配置config.js：

```json
{
	"env": {
        "prod": {
            "appId": "[正式版appid]",
            "baseUrl": "[正式版请求地址]"
        },
        "dev": {
            "appId": "[测试版appid]",
            "baseUrl": "[测试版请求地址]",
        }
    }
}
```

然后使用的时候：

```javascript
let config = require('./config')
let BASE_URL = ''
let wxAccount = wx.getAccountInfoSync().miniProgram
if (wxAccount.appId == config.env.prod.appId) {
  BASE_URL = config.env.prod.baseUrl
} else {
  BASE_URL = config.env.dev.baseUrl
}
```

