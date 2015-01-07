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
