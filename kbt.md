#服务器接口

* device_type: 1) 汽车灯 2)摩托车灯 3)车载净化器

## 获取country列表

* POST /kbt/get/countries

post content
```
{
    "countrycode": Number
}
```

response:
```
{
    "result": "",
    "infos": [
        {"code": Number, "name": String},
        ...
    ]
}
```

## 修改下级密码
* POST /kbt/change_lower/password

post content
```
{
    "auth_token": String,
    "user": String,下级用户,
    "password": String, 新密码
}
```

## 注册用户

* POST /kbt/register/user

post content
```
{
    "user": "", 邮箱或手机号
    "password": "",
    "user_type": 1 | 2 | 3, 1为普通用户， 2为国家管理员， 3为总管理
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
    "password": "",
    "countrycode": Number
}
```
response
```
{
    "result": "ok" | "error",
    "code": Number,
    "desc": "",
    "countrycode": Number,
    "user_type": Number,
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
    "user": "email or phone",
    "countrycode": Number
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
    "auth_token": "",
    "old_password": "",
    "new_password": "",
    "countrycode": Number
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
    "device_id": "",
    "device_type": Number,
    "countrycode": Number
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
    "countrycode": Number,
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

## 设备数量和用户数量列表
* POST /kbt/list/all_number

```
{
    "auth_token": "",
    "countrycode": Number,
    "device_count_from": Number,
    "device_count_to": Number,
    "user_count_from": Number,
    "user_count_to": Number,
    "offset": Number,
    "limit": Number
}
```
response
```
{
    "result": "ok" | "error",
    "code": Number,
    "count": Number,
    "infos": [
       {
            "user": "",
            "countrycode": Number,
            "country": String,
            "device_count": Number,
            "user_count": Number
       }
    ]
}
```

## 获取设备列表
* POST /kbt/search/devices

```
{
    "auth_token": "",
    "countrycode": Number,
    "device_type": Number,
    "device_id": "",
    "user": "",
    "login_times_from": Number,
    "login_times_to": Number,
    "device_register_datetime_from": "2033-11-22 11:11:11",
    "device_register_datetime_to": "2033-11-22 11:11:11",
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
    "infos": [
        {
            "user": "",
            "login_times": Number,
            "device_id": "",
            "device_type": Number,
            "device_register_datetime": ""
        }
    ]
}
```



