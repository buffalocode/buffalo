HTTP API For Buffalo
========================

获取服务器列表
---------------
获取可以使用的API Server列表, API使用者可以往任何一个server上发送请求, 以做到负载均衡

  URL: `[GET] http://{api-host}[:port]/buffalo/list/server`
  
  response:
  
  * success
    
    `{"result": "ok", "cn": ['204.22.12.22:8081', 'www.sxit.com']}`
  
  * error
  

注册用户
-----------
  URL: `[POST] http://{api-host}[:port]/buffalo/register/user`
  
  post body type: text/json
  
  post body:
  
    {
      "name": "sample-name",
      "email": "sample@sample.com",
      "password": "sample-password"
    }
  
  response:
  
  * success
    
    `{"result": "ok"}`
  
  * error
    
    `{"result": "error", "param_errors": {"name": "已经被占用", "email": "已经被占用"}, "general_error": "sample error"}`

    **注意**: 错误分2种，一种是验证参数的错误， 会在param_errors里面, 类型是dict， 一种是无法完成某项行为的错误，
    用general_error表示, 类型是string. 以下所有API都按这种格式返回错误
    
    可能的错误有
    
      * name: 已经被占用
      * email: 已经被占用
      * password: 强度不够
    
获取登录用验证图片
-----------
  URL: `[GET] http://{api-host}[:port]/buffalo/gen/auth_pic`
  
  response:
  
  * success
    
    `{"result": "ok", "auth_pic_url": "url of auth picture"}`
  
  * error
    
    
用户登录
-----------
  URL: `[POST] http://{api-host}[:port]/buffalo/login/user`
  
  post body type: text/json
  
  post body:
  
    {
      "user": "email or name",

      "password": "sample-password",
      "keep_time": 2, keep_time指返回个auth_id的有效时间是多少, -1是仅有效一次， 0为永久有效， 其它正数为有效xxx小时
      "auth_token": "token of auth" 连续登录失败2次会需要输入auth_token
    }
  
  response:
  
  * success
    
    `{"result": "ok", "auth_id": "random string"}`
    
    auth_id在发送其它命令的时候需要用到
  
  * error
    
    可能的错误有

      * user: 登录名错误
      * password: 密码错误
      * auth_token: 不正确
      * general_error: 需要auth_token
    
修改密码
------------
  URL: `[POST] http://{api-host}[:port]/buffalo/change/password`
  
  post body type: text/json
  
  post body:
  
    {
      "user": "email or name"
      "old_password": "sample-password",
      "new_password": "sample-password"
    }
  
  response:
  
  * success
    
    `{"result": "ok"}`
  
  * error
    
    可能的错误有

      * user: 未找到
      * old_password: 不正确
      * new_password: 强度不够

找回密码
---------------
  URL: `[POST] http://{api-host}[:port]/buffalo/forgot/password`
  
  post body type: text/json
  
  post body:
  
    {
      "email": "sample-email"
    }
  
  response:
  
  * success
    
    `{"result": "ok"}`
  
  * error
    
    可能的错误有
        
      * email: 不存在

用户登出
---------------
  URL: `[POST] http://{api-host}[:port]/buffalo/logout/user`
  
  post body type: text/json
  
  post body:
  
    {
      "auth_id": "get from login"
    }
  
  response:
  
  * success
    
    `{"result": "ok"}`
  
  * error
    
    可能的错误有
    
      * general_error: 重复登出
    

上传手机配置
---------------
  URL: `[POST] http://{api-host}[:port]/buffalo/upload/setting`
  
  post body type: text/json
  
  post body:
  
    [{
      "auth_id": "get from login",
      "name": "", 配置名称
      "shake": 0/1, 震动
      "sound": 0/1, 声音
      "room": [
      {
        'id': 3, 房间id
        'name': "", 房间名称
        "device": 
        [
        {
            'id': "", 设备ID, 二进制的字符表示， 比如id为binary 0x223e, 则表示为"223e" 
            'name': "", 设备名称
            'picture': "", base64编码的图片文件
            'meta': int, 设备属性, 设备ID为不同时，含义不一样， 如果设备ID里说明是红外设备，那么bit 0表示是否有附属遥控设备
            'remote_controllers': [
                'id': int, 遥控器id
                'type': remote_controller_type, 
                'meta': short, 遥控器属性, 遥控器属性bit 0为1, 则接下去为学习信号的数量(byte), 学习信号的定义(bytes) 学习信号的定
                'keys': [ 非自定义遥控器的键的定义
                {
                    'id': int,
                    'signal_id': int,
                    'signal_content': base64编码的二进制数据
                }
                ],
                'extend_keys': [ 非自定义遥控器的扩展键的定义
                {
                    'id': int,
                    'name': string,
                    'signal_id': int,
                    'signal_content': base64编码的二进制数据
                },
                ]
                'custom_keys': [ 自定义类型遥控器的信息
                {
                    'id': int, 
                    'name': string,
                    'x2middle': int, 离屏幕中心点的x坐标
                    'y2middle': int, 离屏幕中心点的y坐标
                    'type': int, 键类型
                    'signal_id': int, 信号id
                    'signal_content': base64编码的二进制数据
                }
                ]
            ], 当meta满足某种条件时拥有
        }
        ]
      }
      ],
    }]
  
  response:
  
  * success
    
    `{"result": "ok"}`
  
  * error
    
    可能的错误有
    
      * auth_id: 未找到对应的auth_id
    
下载手机配置
---------------
  URL: `[POST] http://{api-host}[:port]/buffalo/download/setting`
  
  post body type: text/json
  
  post body:
  
    {
      "auth_id": "get from login"
    }
  
  response:
  
  * success
    
    `{"result": "ok", [和上传的格式一模一样]}`
  
  * error

获取设备状态
---------------
  URL: `[POST] http://{api-host}[:port]/buffalo/status/device`
  
  post body type: text/json
  
  post body:
  
    {
      "auth_id": "get from login", 
      "device_id": '' 设备ID, 二进制的字符表示， 比如id为binary 0x223e, 则表示为"223e" 
    }
  
  response:
  
  * success
    
    `{"result": "ok", "temperature": int, "is_online": 0/1}`
  
  * error
  
    可能的错误有
    
      * auth_id: 未找到对应的auth_id
    
关联设备
---------------
  URL: `[POST] http://{api-host}[:port]/buffalo/asso/device`
  
  post body type: text/json
  
  post body:
  
    {
      "auth_id": "get from login", 
      "device_id": '' 设备ID, 二进制的字符表示， 比如id为binary 0x223e, 则表示为"223e",
      "master_device_id": '', 如果是从设备，需要填写，否则为空
      "time_zone": '', 时区 
    }
  
  response:
  
  * success
    
    `{"result": "ok"}`
  
  * error
  
    可能的错误有
    
      * auth_id: 未找到对应的auth_id
      * device_id: 已经关联
      * master_device_id: 缺少master_device_id
    
解除关联设备
---------------
  URL: `[POST] http://{api-host}[:port]/buffalo/de_asso/device`
  
  post body type: text/json
  
  post body:
  
    {
      "auth_id": "get from login", 
      "device_id": '', 设备ID, 二进制的字符表示， 比如id为binary 0x223e, 则表示为"223e"
    }
  
  response:
  
  * success
    
    `{"result": "ok"}`
  
  * error
  
    可能的错误有
    
      * auth_id: 未找到对应的auth_id
      * device_id: 设备没有关联
    
控制设备
---------------
  URL: `[POST] http://{api-host}[:port]/buffalo/control/device`
  
  post body type: text/json
  
  post body:
  
    {
      "auth_id": "get from login", 
      "device_id": '', 设备ID, 二进制的字符表示， 比如id为binary 0x223e, 则表示为"223e"
      "cmd": '', 控制指令，包括'lock', 'learn_signal', 'send_ir', 'multi_cmd'
      'is_locked': 0/1, 当cmd为lock时需要有
      'is_ir_id': 0/1, 是否为红外标识符， 当'send_ir'时需要有
      'ir_id': int, 当is_ir_id为1时有
      'ir_signal': base64编码的二进制数据, 当is_ir_id为0时有
      'multi_cmds': [
        里面包含了单个control命令
      ]
    }
  
  response:
  
  * success
    
    `{"result": "ok", 'ir_signal': '',当为learn_signal时有 'ir_id': int 当为learn_signal时有}`
  
  * error
  
    可能的错误有
    
      * auth_id: 未找到对应的auth_id
      * device_id: 设备没有关联
