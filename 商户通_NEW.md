#商户通——NEW接口规范
> **Beta:**
> 该API仍处于 Beta 阶段

```
1. 该接口适用对象：中汇自有APP
2. 该接口实现功能：用户收单理财
3. 该接口调用规范：采用HTTP请求与中汇的前置POSP进行通信
```
> **注：**
> 文中所有 `<>` 标注的字段，均需根据你的实际情况替换（无需 `<>` 符号，仅作标注之用）
> 文中所有 `:id` 标注的字段，均需根据该资源的实际 `id` 值替换
> 文中所有 `{x|y|...}` 标注的字段，均需根据你的实际情况用其中一个 `x` 或者 `y`（ `|` 分割）替换

## API 接口地址
```
暂无 # 生产环境
http://192.168.1.240:29002 # 测试环境
```

## 公共请求参数
```
appVersion: "ios.ZFT.1.3.611"//IOS 
```

## 标准请求
```sh
curl -X POST \
    http://192.168.1.240:29002/<资源路径> \
    # 其他可选参数，参数以键值对呈现...
```

## 标准响应
* 订单校验通过的情况下  
```
HTTP/1.1 200 OK
Server: Nginx
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
WSSESSION: ""//session信息
Content-Length: 100

...body...
```

* 订单校验未通过的情况下  
```
HTTP/1.1 401 Unauthorized
```
* 请求 Method 不被支持的情况下  
```
HTTP/1.1 405 Method Not Allowed
```
* 请求参数不正确的情况下  
```
HTTP/1.1 403 Forbidden
```
<a id="content-title"></a>
## 功能路径列表
| 资源名称     | 路径                                     | Content-Type         | 请求方式     | 维护人     | 是否需要登录|
|-------------|-----------------------------------------|----------------------|---------------|---------------|---------------|
| 获取验证码| [/sendMessage](#sendMessage)                      | urlencoded           | POST   | 张树彬     | 否   |
| 校验验证码| [/checkMobileMessage](#checkMobileMessage)                      | urlencoded           | POST   | 张树彬     | 否   |
| 获取验证码| [/sendCustomerMessage](#sendCustomerMessage)              | urlencoded       | POST   | 张攀攀     | 是   |
| ICkey回调接口| [/downloadFinished](#downloadFinished)                      | urlencoded           | POST   | 张攀攀     | 是   |
| 联行号查询| [/bankQuery](#bankQuery)                      | urlencoded           | GET   | 张攀攀     | 否   |
| 绑定/解绑用户银行卡| [/bindBankCard](#bindBankCard)                      | urlencoded           | GET   | 张攀攀     | 是   |
| 获取用户银行卡列表| [/listBandCard](#listBandCard)                      | urlencoded           | GET   | 李飞     | 是   |
| 注册| [/register](#register)                      | urlencoded           | POST   | 张树彬     | 否   |
| 忘记密码| [/forgetPassword](#forgetPassword)                      | urlencoded           | POST   | 张树彬     | 否   |
| 登录 | [/login](#login)                      | urlencoded           | POST      | 张树彬     | 否   |
| 校验用户密码| [/checkUserPasswd](#checkUserPasswd)                      | urlencoded           | GET   | 张树彬     | 是   |
| 修改密码| [/resetPassword](#resetPassword)                      | urlencoded           | POST   | 张树彬     | 是   |
| 获取用户设备状态| [/findUserEquipment](#findUserEquipment)                      | urlencoded           | GET      | 张攀攀    | 是   |
| 激活绑定设备| [/activeAndBindEquip](#activeAndBindEquip)                    | urlencoded           | POST      | 张攀攀    | 是   |
| 获取ICKEY| [/getIcKey](#getIcKey)                    | urlencoded           | POST      | 张攀攀    | 否   |
| 实名认证| [/realNameAuth](#realNameAuth)                      | urlencoded           | POST   | 张攀攀     | 是   |
| 实名认证信息回显| [/realNameAuthStatus](#realNameAuthStatus)                      | urlencoded           | GET   | 张攀攀  | 是   |
| 商户认证| [/merchantAuth](#merchantAuth)                      | urlencoded           | POST   | 张攀攀     | 是   |
| 商户认证信息回显| [/merchantAuthStatus](#merchantAuthStatus)                      | urlencoded           | GET   | 张攀攀  | 是   |
| 账户认证| [/accountAuth](#accountAuth)                      | urlencoded           | POST   | 张攀攀     | 是   |
| 账户认证信息回显| [/accountAuthStatus](#accountAuthStatus)                      | urlencoded           | GET   | 张攀攀    | 是   |
| 签名认证| [/signatureAuth](#signatureAuth)                      | urlencoded           | POST   | 张攀攀    | 是   |
| 签名认证信息回显| [/signatureAuthStatus](#signatureAuthStatus)                      | urlencoded           | GET   | 张攀攀| 是   |
| IC回调 | [/transNotify](#transNotify)                      | urlencoded           | POST   | 张攀攀     | 是   |
| 更换设备 | [/swiperChange](#swiperChange)                      | urlencoded           | POST   | 张攀攀     | 是   |
| 查询完美账单MCC列表| [/perfectBillMCC.action](#perfectBillMCC)              | urlencoded           | GET |张攀攀| 是   |
| 获取已绑定POS商户列表|[/getBindingPosMerchantList.action](#getBindingPosMerchantList)  | urlencoded    | POST |张攀攀| 是   |
----------------------------------------------------------------------------------
<a id="sendMessage"></a>
### 获取验证码  /sendMessage
#### 1\. 通过手机号获取验证码(支持注册、找回密码)
请求：  
```
POST /sendMessage HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

mobile: "15801376995" //手机号
type: "register" //操作类型：注册:register 找回:forget
```
响应：  
```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
   "respTime":"20151125161740",
   "isSuccess":true,
   "respCode":"SUCCESS",
   "respMsg":"发送验证码成功,注意查收"
}
```

##### [返回目录↑](#content-title)
<a id="checkMobileMessage"></a>
### 校验验证码 /checkMobileMessage
#### 1\. 校验验证码
请求：  
```
POST /checkMobileMessage HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

mobile: "15801376995" // 手机号
idCode: "1234" //验证码

```
响应：  
```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151126184737",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"校验通过"
}
```

##### [返回目录↑](#content-title)
<a id="register"></a>
### 注册  /register
#### 1\. 通过手机号注册
请求：  
```
POST /register HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

mobile: "15801376995" // 手机号
password: "ERERWERIOWJERWE2112332Y_" // 密码(加密的密文，加密规则咨询维护人) 

```
响应：  
```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151126184737",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"祝贺您成功注册."
}
```

##### [返回目录↑](#content-title)
<a id="forgetPassword"></a>
### 忘记密码  /forgetPassword
#### 1\. 忘记密码
请求：  
```
POST /forgetPassword HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

password: "123456" //密码(加密的密文，加密规则咨询维护人) 
mobile: "15801376995"
```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"修改成功"
}

```
##### [返回目录↑](#content-title)
<a id="login"></a>
### 登录  /login
#### 1\. 手机号登录
请求：  
```
POST /login HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

"password": "qqqqqq" //密码(加密的密文，加密规则咨询维护人) 
"loginName": "18911156118" 
"registId": "1122312233"//推送ID

```
响应：  
```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime": "20151228143800",
    "isSuccess": true,
    "respCode": "SUCCESS",
    "respMsg": "登录成功"
}
```

##### [返回目录↑](#content-title)
<a id="checkUserPasswd"></a>
### 校验用户密码 /checkUserPasswd
#### 1\. 校验用户密码 
请求：  
```
GET /checkUserPasswd HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

password: "密码" //密码(加密的密文，加密规则咨询维护人) 

```
响应：  
```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100
{
    "respTime":"20151126184737",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"成功"
}
```

##### [返回目录↑](#content-title)
<a id="resetPassword"></a>
### 修改密码 /resetPassword
#### 1\. 修改密码
请求：  
```
POST /resetPassword HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

password: "123456" //新密码(加密的密文，加密规则咨询维护人) 
oldPassword: "123456" //旧密码(加密的密文，加密规则咨询维护人) 

```
响应：  
```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100
{
    "respTime":"20151126184737",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"成功"
}
```

##### [返回目录↑](#content-title)
<a id="findUserEquipment"></a>
### 登录  /findUserEquipment
#### 1\. 查询用户绑定设备状态
请求：
```
GET /findUserEquipment HTTP/1.1
Host: XXXXXX:XXXX
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30
WSSESSION: ow5bblf8ethknl59vqp8qbbt-52


```
响应：  
```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

//如果用户已经绑定设备
{
     "respTime": "20171118192014",
    "isSuccess": true,
    "respCode": "SUCCESS",
    "respMsg": "BIND_EQUIPMENT",
    "ksn": "9100100000000027",
    "productModel": "gwv206",
    "status": "3330",
    "merchantReason": "",
    "realReason": "",
    "signatureReason": "",
    "accountReason": "",
    "isMerchantD0": true,
   
}
//如果用户没有绑定设备
{
   "respTime":"20171115152246",
   "isSuccess":false,
   "respCode":"NO_BIND_EQUIPMENT",
   "respMsg":"未绑设备"
}

```
##### [返回目录↑](#content-title)
<a id="activeAndBindEquip"></a>
### 激活绑定设备  /activeAndBindEquip
#### 1\. 激活绑定设备
请求：  
```
POST /activeAndBindEquip HTTP/1.1

isUseActiveCode:"1"//是否使用激活码进行进件(1:是, 0:否)
ksnNo: "5010100000023402"
activeCode: "11C718FF1FD14531"//非必传项，激活码方式进件必传
product: "ZFT" //产品型号
model: "landim35" //设备型号
macAddress:"XX:XX:XX:XX"
appVersion: "ios.未知.1.1.813"
```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"激活绑定设备成功"
}
//需跳转到输入激活码界面
{
    "respTime":"20151130125253",
    "isSuccess":false,
    "respCode":"ACTIVECODE_IS_NOT_NULL",
    "respMsg":"此设备必须输入激活码"
}
//失败
{
    "respTime":"20151130125253",
    "isSuccess":false,
    "respCode":"ILLEGAL_ARGUMENT",
    "respMsg":"请求错误, 请稍候再试"
}


```
##### [返回目录↑](#content-title)
<a id="getIcKey"></a>
### 获取ICKEY /getIcKey
#### 1\. 获取ICKEY
请求：  
```
POST /getIcKey HTTP/1.1
Host: XXXXXX:XXXX
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30
```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime": "20171116193637",
    "isSuccess": true,
    "respCode": "SUCCESS",
    "respMsg": "成功获取ickey",
    "ICkey":     {
        "rids":         {
            "A00000006514": "9F0605A0000000659F220114DF050420221231DF060101DF070101DF0281F8AEED55B9EE00E1ECEB045F61D2DA9A66AB637B43FB5CDBDB22A2FBB25BE061E937E38244EE5132F530144A3F268907D8FD648863F5A96FED7E42089E93457ADC0E1BC89C5 ",
            "A00000033304": "9F0605A0000003339F220104DF050420211231DF060101DF070101DF0281F8BC853E6B5365E89E7EE9317C94B02D0ABB0DBD91C05A224A2554AA29ED9FCB9D86EB9CCBB322A57811F86188AAC7351C72BD9EF196C5A01ACEF7A4EB0D2AD63D9E6AC2E7836 ",
            "A00000033302": "9F0605A0000003339F220102DF050420211231DF060101DF070101DF028190A3 63D27A57DF040103DF031403BB335A8549A03B87AB089D006F60852E4B8060",
            "A00000000404": "9F0605A0000000049F220104DF050420171231DF060101DF070101DF028190 EE2F775E5FEC5DF040103DF0314381A035DA58B482EE2AF75F4C3F2CA469BA4AA6C",
            "A00000033303": "9F0605A0000003339F220103DF050420211231DF060101DF070101DF0281B0 B53040109D89B77CAFAF70C26C601ABDF59EEC0FDC8A99089140CD2E817E335175B03B7AA33DDF040103DF031487F0CD7C0E86F38F89A66F8C47071A8B88586F26",
            "A00000000406": "9F0605A0000000049F220106DF050420211231DF060101DF070101DF0281F8 675C4432350EA244CC34F3873CBA06592987A1D7E852ADC22EF5A2EE28132031E48F74037E3B34AB747FDF040103DF0314F910A1504D5FFB793D94F3B500765E1ABCAD72D9",
            "A00000000405": "9F0605A0000000049F220105DF050420211231DF060101DF070101DF0281B0 507FE3DC90CA310D27B3EFCCD8F83DE3052CAD1E48938C68D095AAC91B5F37E28BB49EC7ED597DF040103DF0314EBFA0D5D06D8CE702DA3EAE890701D45E274C845",
            "A00000006512": "9F0605A0000000659F220112DF050420221231DF060101DF070101DF0281B 12B009842A1ABA01ADB4A2B170668781EC92B60F605FD12B2B2A6F1FE734BE510F60DC5D189E401451B62B4E06851EC20EBFF4522AACC2E9CDC89BC5D8CDE5D633CFD77220FF6BBD4A9B441473CC3C6FEFC8D13E57C3DE97E1269FA19F655215B23563ED1D1860D8681DF040103DF0314874B379B7F607DC1CAF87A19E400B6A9E25163E8",
            "A00000000307": "9F0605A0000000039F220107DF050420171231DF060101DF070101DF028 3140433D50725FDF040103DF0314B4BC56CC4E88324932CBC643D6898F6FE593B172",
            "A00000006510": "9F0605A0000000659F220110DF050420221231DF060101DF070101DF02 BBC9BA23C0285DF040103DF0314C75E5210CBE6E8F0594A0F1911B07418CADB5BAB",
            "A00000000308": "9F0605A0000000039F220108DF050420231231DF060101DF070101DF0 DC2E568612785938BBC9B3CD3A910C1DA55A5A9218ACE0F7A21287752682F15832A678D6E1ED0BDF040103DF031420D213126955DE205ADC2FD2822BD22DE21CF9A8",
            "A00000000309": "9F0605A0000000039F220109DF050420231231DF060101DF070101DF0281F 40EC07F330AC5D67BCD75BF23D28A140826C026DBDE971A37CD3EF9B8DF644AC385010501EFC6509D7A41DF040103DF03141FF80A40173F52D7D27E0F26A146A1C8CCB29046"
        },
        "aids":         {
            "A000000003101001": "9F0608A000000003101001DF0101009F0802008C9F0902008CDF1105FC70A82000DF1205DC40A8F800DF150400000010DF130500100000009F1B0400000500DF160190DF170110DF14039F3704DF180100",
            "A000000003101002": "9F0608A000000003101002DF0101009F0802008C9F0902008CDF1105FC70A82000DF1205DC40A8F800DF150400000010DF130500100000009F1B0400000500DF160190DF170110DF14039F3704DF180100",
            "A0000003330101": "9F0607A0000003330101DF0101009F080200209F09020020DF1105D8689CF800DF1205D8689CF800DF150400000010DF130500100000009F1B0400000500DF160190DF170110DF14039F3704DF180101",
            "A0000000031020": "9F0607A0000000031020DF0101009F0802008C9F0902008CDF1105FC70A82000DF1205DC40A8F800DF150400000010DF130500100000009F1B0400000500DF160190DF170110DF14039F3704DF180100"
        }
    }
}


```
##### [返回目录↑](#content-title)
<a id="perfectBillMCC"></a>
### 查询完美账单MCC列表  /perfectBillMCC
#### 1\. 查询完美账单MCC列表
请求：  
```
GET /perfectBillMCC HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime": "20151228143800",
    "isSuccess": true,
    "respCode": "SUCCESS",
    "respMsg": "成功",
    "mccList":[    
      {
        "id": 676453,--mcc id
        "mccName": "电信服务",--行业大类
      },
    ...
    ]
}
```
##### [返回目录↑](#content-title)

<a id="realNameAuth"></a>
### 实名认证  /realNameAuth
#### 1\. 实名认证
请求：  
```
POST /realNameAuth HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"
name: "狗剩"
idNumber: "341225199005063896"
personal: 图片 //身份证正面照
personalBack: 图片 //身份证背面照
hPersonal： 图片 //手持身份证照片
```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"实名认证信息已提交,请耐心等待"
}
```
##### [返回目录↑](#content-title)
<a id="realNameAuthStatus"></a>
### 实名认证信息回显  /realNameAuthStatus
#### 1\. 实名认证信息回显
请求：  
```
GET /realNameAuthStatus HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"

```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "authStatus":(0:未认证, 1:认证成功, 2:认证失败, 3:审核中),
    "name":"张三",//真实姓名
    "idNumber":"341225199005063796",//身份证
    "personal":"d500000000620995.png",//身份证正面照图片名称
    "personalBack":"b500000000620995.png",//身份证背面照图片名称
    "hPersonal":"b500000000620995.png",//手持身份证照片名称
    "realReason":"用户签名与身份证名字不符",//认证失败原因
 "AuthFirstPass":0,// 如果 该值是0 表示四项认证没有通过过认证， 1 表示四项认证通过过认证
    "respMsg":"查询成功"
}
```
##### [返回目录↑](#content-title)
<a id="merchantAuth"></a>
### 商户认证  /merchantAuth
#### 1\. 商户认证
请求：  
```
POST /merchantAuth HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"
companyName: "企业名称"
regPlace: "经营地址"
businessLicense: "营业执照号"
business: 图片 //营业执照照片
isCheckMerchantInfo:"1",//是否校验商户资质(0:否，1:是)
```

响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"商户认证信息已提交,请耐心等待"
}
```
##### [返回目录↑](#content-title)
<a id="merchantAuthStatus"></a>
### 商户认证信息回显  /merchantAuthStatus
#### 1\. 商户认证信息回显
请求：  
```
GET /merchantAuthStatus HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"

```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "authStatus":(0:未认证, 1:认证成功, 2:认证失败, 3:审核中),
    "companyName":"大众食品商店",//企业名称
    "business":"q500000000620995.png",//营业执照图片名称
    "regPlace":"兴华大道",//经营地址
    "businessLicense":"5DSF5SDFS5DF",//营业执照号
    "merchantReason":"商户不行",//认证失败原因
    "respMsg":"查询成功"
}
```
##### [返回目录↑](#content-title)
<a id="accountAuth"></a>
### 账户认证  /accountAuth
#### 1\. 账户认证
请求：  
```
POST /accountAuth HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"
name: "姓名" //名字
identityCard : 370828199902019876 //身份证号
bankName: "银行名称" 
unionBankNo: "联行号"
accountNo: "银行卡号"
card: 图片 //银行卡图片
mobile: 15877987678 // 手机号
verificationCode : 2343 //验证码
```

响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"账户认证信息已提交,请耐心等待"
}
```
##### [返回目录↑](#content-title)
<a id="accountAuthStatus"></a>
### 账户认证信息回显  /accountAuthStatus
#### 1\. 账户认证信息回显
请求：  
```
GET /accountAuthStatus HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"

```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "authStatus":(0:未认证, 1:认证成功, 2:认证失败, 3:审核中),
    "accountNo":"6217000010012052348",//银行卡号
    "card":"c500000000620995.png",//银行卡图片名称
    "name": "姓名" //名字
    "identityCard" : "370828199902019876" //身份证号
    "mobile": "15877987678" // 手机号
    "bankName":"建设银行",//银行名称
    "unionBankNo":"56SDFSD56SDF",//联行号
    "accountReason":"账户信誉差",//认证失败原因
    "respMsg":"查询成功"
}
```
##### [返回目录↑](#content-title)
<a id="signatureAuth"></a>
### 签名认证  /signatureAuth
#### 1\. 签名认证
请求：  
```
POST /signatureAuth HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"
signature: 图片 //签名图片
```

响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"签名认证信息已提交,请耐心等待"
}
```
##### [返回目录↑](#content-title)
<a id="signatureAuthStatus"></a>
### 签名认证信息回显  /signatureAuthStatus
#### 1\. 签名认证信息回显
请求：  
```
GET /signatureAuthStatus HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"

```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "name":"张三",
    "authStatus":(0:未认证, 1:认证成功, 2:认证失败, 3:审核中),
    "signature":"s500000000620995.png",//签名图片名称
    "signatureReason":"签名丑",//认证失败原因
    "respMsg":"查询成功"
}
```
##### [返回目录↑](#content-title)

<a id="sendCustomerMessage"></a>
### 获取验证码  /sendCustomerMessage
#### 1\. 通过手机号获取验证码
请求：  
```
POST /sendCustomerMessage HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"
accountName: "张无忌" //姓名
idNumber   ： "010876765543456543" //身份证
mobile     ： "15201059026" //手机号
bankCard   ： "62202010904267897" // 银行卡号
reqTime    ： "20151125161740" //请求时间
```
响应：  
```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
   "respTime":"20151125161740",
   "isSuccess":true,
   "respCode":"SUCCESS",
   "respMsg":"发送验证码成功,注意查收"
   
}

```
##### [返回目录↑](#content-title)

<a id="bankList"></a>
### 获取18家结算银行  /bankList
#### 1\. 获取18家结算银行
请求：  
```
GET /bankList HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

card : XXXXXX
reqTime: "20151124111059"
appVersion: "ios.未知.1.1.813"

```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "list": {
        [bankName:"2222", bankCode: "333", bankLogoIndex: "333", support: "true"],
        [bankName:"4444", bankCode: "555", bankLogoIndex: "666", support: "true"]},
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"验证成功"
}
```
##### [返回目录↑](#content-title)

<a id="bindBankCard"></a>
### 绑定/解绑用户银行卡  /bindBankCard
#### 1\. 绑定/解绑用户银行卡
请求：  
```
POST /bindBankCard HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

isDelete: "false" //绑定、解绑参数
cardIds: "XXX YYYY"//解绑卡列表 空格区分
bankCard: "XXXX"
mobile: "13777775555"
reqTime: "20151124111059"
isSelf : true //true表示添加自己的银行卡 false 表示添加别人的银行卡
appVersion: "ios.未知.1.1.813"


```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "bankCard": "XXXX",
    "bankName": "xx银行",
    "bankIndex": 11,
    "card_type": "借记卡",
    "bankAccountName": XX银行,
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"验证成功"
}
```
##### [返回目录↑](#content-title)

<a id="listBandCard"></a>
### 获取商户绑定银行卡列表  /listBandCard
#### 1\. 获取商户绑定银行卡列表
请求：  
```
POST /listBandCard HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"

```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
list:[
{
    "accountNo": "XXXX",
    "bankName": "xx银行",
    "bankIndex": 1,
    "status": 1,//认证状态
    "cardId": 1
},{
    "accountNo": "XXXX",
    "bankName": "xx银行",
    "bankIndex": 3,
    "status": 1,//认证状态
    "cardId": 2
}]
]
```
##### [返回目录↑](#content-title)
<a id="perfectBillMCC"></a>
### 查询完美账单MCC列表  /perfectBillMCC
#### 1\. 查询完美账单MCC列表
请求：  
```
GET /perfectBillMCC HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime": "20151228143800",
    "isSuccess": true,
    "respCode": "SUCCESS",
    "respMsg": "成功",
    "mccList":[    
      {
        "id": 676453,--mcc id
        "mccName": "电信服务",--行业大类
      },
    ...
    ]
}
```
##### [返回目录↑](#content-title)

<a id="downloadFinished"></a>
### ICkey完成回调接口  /downloadFinished
#### 1\. ICkey完成回调接口
请求：  
```
POST /downloadFinished HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"

```
响应：  
```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151126184737",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"已更新状态."
}
```
##### [返回目录↑](#content-title)
<a id="bankQuery"></a>
### 联行号查询  /bankQuery
#### 1\. 联行号查询
请求：  
```
GET /bankQuery HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

keyWord: "中国 银行"
reqNo: "111"
max: "111"
p: "111"
reqTime: "20151124111059"
appVersion: "ios.未知.1.1.813"

```
响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "total": 100,
    "tip": "",
    "reqNo": 100,
    "banks": {[bankDeposit:"2222", unionBankNo: "333"],[bankDeposit:"33", unionBankNo: "444"]},
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"验证成功"
}
```

##### [返回目录↑](#content-title)

<a id="transNotify"></a>
### IC回调  /transNotify
#### 1\. IC回调
请求：  
```
POST /transNotify HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"
reqNo:"1234"
origTransTime:"20151212070809"
origTransType:"sale"
origReqNo:"1234"
icData:"asfakfjasklfdsa"
cardNo:"622266000000"
cardSerialNum:"01"
tradeType:1 //交易类型(1:T1, 16:D0, 17:D0秒到)
```

响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"查询成功"
}
```
##### [返回目录↑](#content-title)

<a id="swiperChange"></a>
### 更换设备  /swiperChange
#### 1\. 更换设备
请求：  
```
POST /swiperChange HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

appVersion: "ios.未知.1.1.813"
ksnNo:"XXXXXX"
model:"landim35"
macAddress:"XXXXXXX"
```

响应： 

```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime":"20151130125253",
    "isSuccess":true,
    "respCode":"SUCCESS",
    "respMsg":"查询成功"
}

```

##### [返回目录↑](#content-title)

<a id="getBindingPosMerchantList"></a>
### 获取已绑定POS商户列表  /getBindingPosMerchantList
#### 1\. 获取已绑定POS商户列表
请求：  
```
post /getBindingPosMerchantList HTTP/1.1
Host: mposp.21er.tk
Date: Thu, 03 Dec 2015 10:22:53
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Content-Length: 30

"appVersion": "android.ZFT.1.2.143",
"lastID": "" //最后一条绑定记录ID,

```
响应：  
```
HTTP/1.1 200 OK
Server: Nginx
Date: Thu, 09 Apr 2015 11:36:53 GMT
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Cache-Control: no-cache
Content-Length: 100

{
    "respTime": "20151228143800",
    "isSuccess": true,
    "respCode": "SUCCESS",
    "respMsg": "成功",
    "bindingCount": 13,
    "bindingList": [
    {
    	    "recordId": "1007",
            "merchantName": "郑******",
            "merchantNo": "Z08000000026875",
            "status": 2,//pos机验证状态 1,等待认证，2认证成功，3认证失败，4解绑
            "urlPath": "appMerchant/posInfoValidation.htm?id=1007"
    }
    ...
    ]
}
```

##### [返回目录↑](#content-title)


