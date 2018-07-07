# 计算API签名

以微信支付的公众号支付下单接口文档为例，演练Postman计算签名。

微信支付的商户接口文档：https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=9_1

设置POST请求的【Body】类型为raw，填充内容如下：

```xml
<xml>
   <appid>wx2421b1c4370ec43b</appid>
   <attach>支付测试</attach>
   <body>JSAPI支付测试</body>
   <mch_id>10000100</mch_id>
   <detail><![CDATA[{ "goods_detail":[ { "goods_id":"iphone6s_16G", "wxpay_goods_id":"1001", "goods_name":"iPhone6s 16G", "quantity":1, "price":528800, "goods_category":"123456", "body":"苹果手机" }, { "goods_id":"iphone6s_32G", "wxpay_goods_id":"1002", "goods_name":"iPhone6s 32G", "quantity":1, "price":608800, "goods_category":"123789", "body":"苹果手机" } ] }]]></detail>
   <nonce_str>1add1a30ac87aa2db72f57a2375d8fec</nonce_str>
   <notify_url>http://wxpay.wxutil.com/pub_v2/pay/notify.v2.php</notify_url>
   <openid>oUpF8uMuAJO_M2pxb1Q9zNjWeS6o</openid>
   <out_trade_no>1415659990</out_trade_no>
   <spbill_create_ip>14.23.150.211</spbill_create_ip>
   <total_fee>1</total_fee>
   <trade_type>JSAPI</trade_type>
   <sign>{{sign}}</sign>
</xml>
```

注意 sign标签内，用变量填充。Postman中变量和某些前端模板引擎的写法一致，采用两层花括号{{}}。

打开【Pre-request Script】
```javascript
jsonObj = xml2Json(request.data).xml;
keys = Object.keys(jsonObj).sort();

sign_src = '';
first_flag = true;
for (var i in keys) {
    key = keys[i];
    if (key == 'sign') {
        continue;
    }

    if (first_flag) {
        sign_src += (key + '=' + jsonObj[key]);
        first_flag = false;
    } else {
        sign_src += ('&' + key + '=' + jsonObj[key]);
    }
}
// 拼接key；假设key为123456
key = '123456';
sign_src += ('&key=' + key);

// Postman默认引入了CryptoJS
sign = CryptoJS.MD5(sign_src).toString();
// 如果采用的是HMAC-SHA256
// sign = CryptoJS.HmacSHA256(sign_src, key).toString();

// 设置全局变量
pm.globals.set("sign", sign);
```
