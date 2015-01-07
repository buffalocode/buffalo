#服务器接口

## 注册用户

* POST /kbt/register/user

post content
```
{
    "phone": "",
    "email": "",
    "password": "",
    "countrycode": Number
}
```
response
```
{
    "result": "ok" | "error",
    "code": Number,
    "desc": ""
}
```

## 用户登录

* POST /kbt/login/user

post content
```
{
    "user": "email or phone",
    "password": ""
}
```
response
```
{
    "result": "ok" | "error",
    "code": Number,
    "desc": "",
    "auth_token": "can use used in other api calling",
    "version": {
        "ios": "",
        "android": "",
        "web": ""
    }
}
```

## 找回密码
* POST /kbt/forgot/password

post content
```
{
    "user": "email or phone"
}
```

response
```
{
    "result": "ok" | "error",
    "code": Number,
    "desc": "",
}
```

## 修改密码
* POST /kbt/change/password

post content
```
{
    "user": "email or phone",
    "old_password": "",
    "new_password": ""
}
```

response
```
{
    "result": "ok" | "error",
    "code": Number,
    "desc": "",
}
```

## 激活设备
* POST /kbt/register/device

post content
```
{
    "auth_token": "get by login",
    "device_id": ""
}
```

response
```
{
    "result": "ok" | "error",
    "code": Number,
    "desc": "",
}
```

## 获取某个国家用户数量, 设备数量
* POST /kbt/count/all

```
{
    "auth_token": "",
    "countrycode": "",
    "device_count_from": Number,
    "device_count_to": Number,
    "user_count_from": Number,
    "user_count_to": Number
}
```
response
```
{
    "result": "ok" | "error",
    "code": Number,
    "device_count": Number,
    "user_count": Number
}
```
countrycode如果不填， 则是全球的， device_count_from如果等于device_count_to, 则这2个选项无效, user_count_from,
user_count_to同理

## 获取设备列表
* POST /kbt/get/devices

```
{
    "auth_token": "",
    "countrycode": "",
    "offset": Number,
    "limit": Number,
    "order_by": "",
    "order_type": "desc" | "asc"
}
```
response
```
{
    "result": "ok" | "error",
    "code": Number,
    "desc": "",
    "count": Number,
    "devices": [
        {
            "register_time": "激活时间",
            "user": "激活用户"
        }
    ]
}
```

## 获取用户列表
```
{
    "auth_token": "",
    "countrycode": "",
    "offset": Number,
    "limit": Number,
    "order_by": "",
    "order_type": "desc" | "asc"
}
```
response
```
{
    "result": "ok" | "error",
    "code": Number,
    "desc": "",
    "count": Number,
    "users": [
        {
            "register_time": "注册时间",
            "phone": "手机",
            "email": "邮箱",
            "login_times": "登录次数"
        }
    ]
}
```


