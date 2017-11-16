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
| 注册| [/register](#register)                      | urlencoded           | POST   | 张树彬     | 否   |
| 忘记密码| [/forgetPassword](#forgetPassword)                      | urlencoded           | POST   | 张树彬     | 否   |
| 登录 | [/login](#login)                      | urlencoded           | POST      | 张树彬     | 否   |
| 校验用户密码| [/checkUserPasswd](#checkUserPasswd)                      | urlencoded           | GET   | 张树彬     | 是   |
| 修改密码| [/resetPassword](#resetPassword)                      | urlencoded           | POST   | 张树彬     | 是   |
|获取用户设备状态| [/findUserEquipment](#findUserEquipment)                      | urlencoded           | GET      | 张攀攀    | 是   |
|激活绑定设备| [/activeAndBindEquip](#activeAndBindEquip)                    | urlencoded           | POST      | 张攀攀    | 是   |
|获取ICKEY| [/getIcKey](#getIcKey)                    | urlencoded           | POST      | 张攀攀    | 否   |

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

"userId": "18911156118"  //用户名

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
    "respTime": "20151228143800",
    "isSuccess": true,
    "respCode": "BIND_EQUIPMENT",
    "respMsg": "已绑设备"
}
//如果用户没有绑定设备
{
   "respTime":"20171115152246",
   "isSuccess":false,
   "respCode":"NO_BIND_EQUIPMENT",
   "respMsg":"已绑设备"
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

##### [返回目录↑](#content-title)
<a id="getIcKey"></a>
### 登录  /getIcKey
#### 1\. 获取ickey
请求：
```
GET /getIcKey HTTP/1.1
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

 "respTime": "20171116193637",
    "isSuccess": true,
    "respCode": "SUCCESS",
    "respMsg": "成功获取ickey",
    "ICkey":     {
        "rids":         {
            "A00000006514": "9F0605A0000000659F220114DF050420221231DF060101DF070101DF0281F8AEED55B9EE00E1ECEB045F61D2DA9A66AB637B43FB5CDBDB22A2FBB25BE061E937E38244EE5132F530144A3F268907D8FD648863F5A96FED7E4208",
            "A00000033304": "9F0605A0000003339F220104DF0504205E89E7EE9317C94B02D0ABB0DBD91C05A224A2554AA29ED9FCB9D86EB9CCBB322A57811F86188AAC7351C72BD9EF196C5A01ACE",
            "A00000033302": "9F0605A0000003339F220102DF050420211231DF060101DF070101DF028190A3767ABD1B6AA69D7F3FBF28C092DE9ED1E658BA5F0909AF7A1CCD907373B7210FDEB16287BA8E78E1529F443976FD27F991EC67D95E5F4E96B127CAB",
            "A00000000404": "9F0605A0000000049F220104DF050420171231DF060101DF070101DF028190A6DA428387A502D7DDFB7A74D3F412BE762627197B25435B7A81716A700157DDD06F7CC99D6CA28C2470527E2C03616B9C59217357C2674F583B",
            "A00000033303": "9F0605A0000003339F220103DF050420211231DF060101DF070101DF0281B0B0627DEE87864F9C18C13B9A1F025448BF13C58380C91F4CEBA9F9BCB214FF8414E9B59D6ABA10F941C7331768F47B21279",
            "A00000000406": "9F0605A0000000049F220106DF050420211231DF060101DF070101DF0281F8CB26FC830B43785B2BCE37C81ED334622F9622F4C89AAE641046B2353433883F307FB7C974162DA72F7A4EC75D9D657336865B8D3023D3D645667625C9A07",
            "A00000000405": "9F0605A0000000049F220105DF050420",
            "A00000006512": "9F0605A0000000659F220112DF050420221231DF060101DF070101DF0281B0ADF05CD4C5B490B087C3467B0F3043750438848461288BFEFD6198DD576DC3AD7A7CFA07DBA128C247A8EAB30DC3A30B02FCD7F1C8167965463626FEFF",
            "A00000000307": "9F0605A0000000039F220107DF050420A6783140433D50725FDF040103DF0314B4BC56CC4E88324932CBC643D6898F6FE593B172",
            "A00000006510": "9F0605A0000000659F220110DF050420221231DF060101DF070101DF026BBC9BA23C0285DF040103DF0314C75E5210CBE6E8F0594A0F1911B07418CADB5BAB",
            "A00000000308": "9F0605A0000000039F220108DF050420231231DF060101DF070101DF0281B0D9FD6ED75D51D0E30664BD157023EAA1FFA871E4DA65672B863D255E81E137A51DE4F72BCC9E44ACE12127F87E263D3AF9DD9CF35CA4A7B01E907000BA85D24954C2F",
            "A00000000309": "9F0605A0000000039F220109DF050420231231DF060101DF070101DF0281F89D912248DE0A4E39C1A7DDE3F6D2588992C1A4095AFBD1824D1BA74847F2BC4926D2EFD904B4B54954CD189A54C5D1179654F8F9B0D2AB5F0357EB642FEDA95D3912C65"
        },
        "aids":         {
            "A000000003101001": 00010DF130500100000009F1B0400000500DF160190DF170110DF14039F3704DF180100",
            "A000000003101002": "9F0400000010DF130500100000009F1B0400000500DF160190DF170110DF14039F3704DF180100",
            "A0000003330101": "9F0607A0000003330101DF0101009F080200500DF160190DF170110DF14039F3704DF180101",
            "A0000000031020": "9F0607A0000000031020DF010100110DF14039F3704DF180100"
        }
