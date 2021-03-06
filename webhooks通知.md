##webhooks通知


###什么是webhooks通知？
通过配置 webhooks，商户可订阅需要的事件，当这些事件发生时，Paymax会将对应的事件消息推送到商户指定的地址。Paymax现支持如下webhooks：</br>

* 支付结果通知
* 退款结果通知


###如何订阅
1. 从应用列表里点击某应用

![应用中心](image/app_center.png) 

2. 点击左侧导航中webhooks

![](image/app_preview_webhooks.png)

3. 点击右上角添加按钮，开始添加webhooks

![](image/add_webhooks.png)


###可靠通知规则
由于网络等原因，商户可能未接收或未正常处理webhooks通知，Paymax会通过一定重发机制来保证通知的可靠性。

Paymax是否重发，取决于商户收到通知后response的结果是否为success字符串。若不是，才会按照一定机制重发，直到收到success为止。

当然，Paymax重发的时间间隔也会随着重发次数逐步变长。

对后台通知交互时，如果Paymax收到商户的应答不是成功或超时（正常的response是：http code 200 && content 为 success），Paymax认为通知失败，在36小时之内，一共发送17次，第一次发送失败之后，之后每次发送的间隔为：2s,4s,8s,16s,32s,64s,128s,256s,512s,1024s,2048s,4096s,8192s,16384s,32768s,65536s


###通知格式

**示例**

```

 {
 	"data": {
 		"amount": 0.01,
 		"body": "测试商品",
 		"client_ip": "127.0.0.1",
 		"currency": "CNY",
 		"description": "测试商品描述信息",
 		"id": "ch_846b3840118646249aba1e8f",
 		"livemode": false,
 		"order_no": "ETEN201606272002045rCT",
 		"status": "SUCCEED",
 		"subject": "测试商品标题",
 		"time_expire": 1467032503680,
 		"transaction_no": "201606060001365501"
 	},
 	"notifyNo": "evt_902f9912b51b4f62963382f2b796d899",
 	"timeCreated": 1467028926044,
 	"type": "CHARGE"
 }
```

**参数说明**

| 参数          | 类型                 | 描述                                       |
| ----------- | ------------------ | ---------------------------------------- |
| data        | Charge对象或者Refund对象 | 详情见 [API文档](API文档.md#支付) 中对Charge对象、Refund对象的说明 |
| notifyNo    | String             | 通知事件唯一标识                                 |
| timeCreated | Long               | 时间戳，自1970年以后的毫秒数                         |
| type        | String             | 通知类型，共以下几种:<br/> CHARGE : 支付 <br/> REFUND : 退款 |



###如何验签

每一次事件通知，Paymax都会对request body整体做签名，并将签名后的结果放request header中，header名称sign。

签名算法为SHA1WithRSA，签名时会使用Paymax官方私钥做签名，所以商户验签时需要用Paymax官方公钥来做验签。

Paymax官方公钥可通过商户后台“开发信息”中获取。





