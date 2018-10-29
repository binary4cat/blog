---
title: 微信JSSDK的使用
date: 2017-03-15T20:33:03+08:00
categories: ["cSharp"]
tags: ["微信公众号开发"]
permalink:
description:
photos:
---
## 概述和官方文档
　　使用微信JSSDK可以在网页中调用手机的拍照语音、定位等手机系统功能，还可以定制微信分享、扫一扫、卡券、支付等微信功能。
　　官方文档：[http://mp.weixin.qq.com/wiki/7/aaa137b55fb2e0456bf8dd9148dd613f.html](http://mp.weixin.qq.com/wiki/7/aaa137b55fb2e0456bf8dd9148dd613f.html)
　　
## 调用步骤
### 1.绑定域名
　　首先进入[微信公众号后台](http://mp.weixin.qq.com/)，进入公众号设置=>功能设置=>JS接口安全域名设置：在这里可以设置三个调用的JSSDK的域名，然后将提示信息注意事项3中的[文件下载](https://mp.weixin.qq.com/cgi-bin/mpverifytxt?lang=zh_CN&token=1492992380)下来，放到网站的根目录下，点击保存，微信会自动测试是否设置成功。
### 2.页面引入JS文件
　　在需要使用到JSSDK的页面引入微信提供的js文件，可以支持https协议，下载：<!--more-->
 　　http://res.wx.qq.com/open/js/jweixin-1.2.0.js
 　　https://res.wx.qq.com/open/js/jweixin-1.2.0.js
```JavaScript
@section scripts{
    <script src="http://res.wx.qq.com/open/js/jweixin-1.2.0.js"></script>
}
```
### 3.请求配置参数，注入微信权限验证配置
　　在所有使用JSSDK的页面上，必须在打开页面之前注入配置信息，否则无法调用JSSDK，所以，在页面准备完成之前，我们需要操作js代码，对权限验证的参数进行获取。在这里我们需要注意两点：
　　1.假设我们在后台可以直接拿到当前公众号的appId。如果后天拿不到就要想办法在页面上拿到appid，然后发送到后台，否则无法进行权限验证。
　　2.请求配置需要的`jsapi_ticket`参数需要缓存下来，避免频繁请求，过期时间在请求成功返回的参数里面带有。
*asp.net mvc:*
```cSharp
/// <summary>
/// 保存需要返回的数据
/// <summary>
public class JsSdkConfigData{
    /// <summary>
    /// 公众号的app id
    /// </summary>
    public string AppId  { get; set; }
    /// <summary>
    /// 生成签名的时间戳
    /// </summary>
    public int Timestamp { get; set; }
    /// <summary>
    /// 生成签名的随机字符串
    /// </summary>
    public string NonceStr { get; set; }
    /// <summary>
    /// 生成的签名
    /// </summary>
    public string Signature { get; set; }
}

/// <summary>
/// 获取页面的配置
/// </summary>
/// <returns></returns>
public JsSdkConfigData GetJsSdkConfig(string appid){
    var ticket = GetJsApiTicketByAppId(appid);  //根据appid获取的jsapi_ticket
    var timestamp = GetTimeStamp(); //生成的时间戳
    var nonceStr = Guid.NewGuid().ToString("N"); //随机32位字符串
    var url = currentUrl;  //注意currentUrl是一个假参数，代表当前请求的页面的url，不能包含#之后的内容
    //拼接字符串
    var urlStr = $"jsapi_ticket={ticket}&noncestr={nonceStr}&timestamp={timestamp}&url={url}";
    //对拼接的字符串进行SHA1计算，并拿到小写后的值，就是最终的凭据
    var signature = SHA1UtilHelper.GetSha1(urlStr).ToLower();
    //将当前计算凭据的数据都保存起来，返回到后台，因为在验证的时候，凭据和生成它的参数必须保持一致
    var config=new JsSdkConfigData()
    {
        AppId= appid,
        Timestamp= timestamp,
        NonceStr= nonceStr,
        Signature= signature
    };
    return config;
}
/// <summary>
/// 根据appid获取jsapi_ticket
/// </summary>
/// <returns></returns>
public virtual string GetJsApiTicketByAppId(string appId){
    //优先读取redis中的缓存数据
    var cache = CacheManager.GetCache(JsApiTicket)
    var cacheToke = cache.GetOrDefault(appId);
    if (cacheToke != null && !cacheToke.ToString().IsNullOrEmpty()) return cacheToke.ToString();
    //不存在jsapi_ticket,重新获取
    var publicEntity = WeiXin.GetAll().Single(d => d.AppId == appId);   //从数据库读取该公众号的信息
    //借用Senparc.Weixin SDK请求jsapi_ticket(注意token同样需要缓存)
    var result = CommonApi.GetTicketByAccessToken(GetTokenByAppId(appId));  
    cache.Set(publicEntity.AppId, result.ticket, TimeSpan.FromSeconds(result.expires_in));
    return result.ticket;
}
/// <summary>
/// 根据appid获取token
/// </summary>
/// <param name="appId"></param>
/// <returns></returns>
public virtual string GetTokenByAppId(string appId)
{
    //优先读取redis中缓存的token
    var cache = CacheManager.GetCache(iCTRWeiXinCacheNames.WeiXinToken)
    var cacheToke = cache.GetOrDefault(appId);
    if (cacheToke != null && !cacheToke.ToString().IsNullOrEmpty()) return cacheToke.ToString();
    //不存在Token,重新获取
    var Entity = WeiXin.GetAll().Single(d => d.AppId == appId);  //从数据库读取该公众号的信息
    //借用Senparc.Weixin SDK请求token
    var token = AccessTokenContainer.TryGetAccessToken(appId, Entity.AppSecret, true);  
    cache.Set(Entity.AppId, token, TimeSpan.FromMinutes(110));
    return token;
}

public class SHA1UtilHelper
{
    /// <summary>
    /// 签名算法
    /// </summary>
    /// <param name="str"></param>
    /// <returns></returns>
    public static string GetSha1(string str)
    {
        //建立SHA1对象
        SHA1 sha = new SHA1CryptoServiceProvider();
        //将mystr转换成byte[] 
        ASCIIEncoding enc = new ASCIIEncoding();
        byte[] dataToHash = enc.GetBytes(str);
        //Hash运算
        byte[] dataHashed = sha.ComputeHash(dataToHash);
        //将运算结果转换成string
        string hash = BitConverter.ToString(dataHashed).Replace("-", "");
        return hash;
    }
}
```
*JavaScript:*
```JavaScript
<script>
    $(function(){
        $.get({
            url:"/WeiXin/GetJssdkConfig",
            success:function(result){
                window.wx.config({
                        debug: true, // 开启    调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
                        appId: result.appId, // 必填，公众号的唯一标识
                        timestamp: result.timestamp, // 必填，生成签名的时间戳
                        nonceStr: result.nonceStr, // 必填，生成签名的随机串
                        signature: result.signature,// 必填，签名，
                        jsApiList: [
                            'onMenuShareTimeline',
                            'onMenuShareAppMessage',
                            'onMenuShareQQ',
                            'onMenuShareWeibo'] // 必填，需要使用的JS接口列表，
                    });
            }
        });
    });
    //通过ready接口处理成功验证
    wx.ready(function () {
                wx.onMenuShareAppMessage({
                    title: '用户点击发送给朋友',
                    desc: '用户点击发送给朋友',
                    link: window.location.href,
                    imgUrl: 'http://ww4.sinaimg.cn/large/c55a7aeejw1fa2eocj9c4j20dc07ijrg.jpg',
                    trigger: function (res) {
                        alert('用户点击发送给朋友' + window.location.href);
                    },
                    success: function (res) {
                        alert('已分享');
                    },
                    cancel: function (res) {
                        alert('已取消');
                    },
                    fail: function (res) {
                        alert(JSON.stringify(res));
                    }
                });
                wx.onMenuShareTimeline({
                    title: '用户点击分享到朋友圈',
                    link: window.location.href,
                    imgUrl: 'http://ww4.sinaimg.cn/large/c55a7aeejw1fa2eocj9c4j20dc07ijrg.jpg',
                    trigger: function (res) {
                        alert('用户点击分享到朋友圈');
                    },
                    success: function (res) {
                        alert('已分享');
                    },
                    cancel: function (res) {
                        alert('已取消');
                    },
                    fail: function (res) {
                        alert(JSON.stringify(res));
                    }
                });
                wx.onMenuShareQQ({
                    title: '用户点击分享到QQ',
                    desc: '用户点击分享到QQ',
                    link: window.location.href,
                    imgUrl: 'http://ww4.sinaimg.cn/large/c55a7aeejw1fa2eocj9c4j20dc07ijrg.jpg',
                    trigger: function (res) {
                        alert('用户点击分享到QQ');
                    },
                    complete: function (res) {
                        alert(JSON.stringify(res));
                    },
                    success: function (res) {
                        alert('已分享');
                    },
                    cancel: function (res) {
                        alert('已取消');
                    },
                    fail: function (res) {
                        alert(JSON.stringify(res));
                    }
                });
                wx.onMenuShareWeibo({
                    title: '用户点击分享到微博',
                    desc: '用户点击分享到微博',
                    link: window.location.href,
                    imgUrl: 'http://ww4.sinaimg.cn/large/c55a7aeejw1fa2eocj9c4j20dc07ijrg.jpg',
                    trigger: function (res) {
                        alert('用户点击分享到微博');
                    },
                    complete: function (res) {
                        alert(JSON.stringify(res));
                    },
                    success: function (res) {
                        alert('已分享');
                    },
                    cancel: function (res) {
                        alert('已取消');
                    },
                    fail: function (res) {
                        alert(JSON.stringify(res));
                    }
                });
            });
</script>
```

## 结束
　　其他使用方法，参看官方文档。
