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

## 获取某个国家激活设备数量
* POST /kbt/get/devices_num

```
{
    "auth_token": "",
    "countrycode": ""
}
```
response
```
{
    "result": "ok" | "error",
    "code": Number,
    "device_number": Number
}
```
如果countrycode不填， 则获取全球激活设备的数量

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


