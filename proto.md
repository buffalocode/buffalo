# 设备控制协议

## 概念
该协议以终端控制设备为核心，主要定义了设备与服务器通信，终端与服务器通信的内容.
还有用户的概念. 

核心操作为各种查询，控制. 
各种其它信息为附属信息，比如此项目要用到的房间等信息，整个这些信息看作是一个统一的配置来看待

## 控制模式
主要有2种： 
 * 一种是终端直接和设备进行通信 (方式1)
 * 另外一种是终端和设备通过服务器进行交互 (方式2)

下面定义的都是通过服务器交互的数据格式，即2的格式。
对于1， 需要把2格式里面`类型` - 0x40, 比如对于2格式里面的获取设备信息(0x83)的请求，1里面就为0x43,
而`payload`是完全一致的

## 数据类型及名词解释

  * `byte`        1 byte
  * `short`       2 bytes
  * `bool`        1 byte, 0为否，1为是, 比如是否锁定， 1表示锁定，0表示不锁定
  * `int`         4 bytes
  * `uint`        4 bytes
  * `bytes`       n bytes
  * `string`      4 bytes长度字段 + string的实际数据, string数据以utf8进行编码
  * `device_id`   16 bytes 设备ID, byte 0设备的类型信息, byte 1-2为vendor编号，3-4为vendor设备型号编号, 剩下的为设备
                  实际ID

  第一个byte的含义如下表
  <table>
  <tr>
    <th>bit 0</th>
    <th>bit 1-7</th>
  </tr>
  <tr>
    <td>主/从, 0为主, 1为从</td>
    <td>设备子类型(红外[1]、恒温器[2]、空气净化器[3]、墙壁开关[4]、热水器[5]、监控[6]、排插[7]、门禁视频[8])</td>
  </tr>
  </table>
  * `time_BCD`    时间的BCD表示2014-02-11 00:23:12 需要用7个bytes表示，byte 1 0x20, byte 2 0x14
                  byte 3 0x02, byte 4 0x11, byte 5 0x00, byte 6 0x23, byte 7 0x12
  * `ip`          四个字节，比如202.112.12.13 则表示为 0xCA 0x70 0x0c 0x0d ipv4
  * `none`        表示该字段不出现， 比如int/none, 表示该字段可能是int, 也可能不出现
  * `remote_contoller_type` short 如果是0,则是自定义遥控器,否则为特定型号的遥控器
                            各种特定型号遥控器定义为: 1表示电视机、2表示机顶盒、3表示DVD、4表示空调、5表示IPTV
  * `bit X` 从左往右来规定是哪个bit, bit X表示从左往右第X位, X从0开始
  

各种整形以`big-endian` (http://en.wikipedia.org/wiki/Endianness) 进行编码


## 信息格式
所有请求格式都为
<table>
  <tr>
    <th>类型</th>
    <th>Msg ID<th>
    <th>长度</th>
    <th>请求payload</th>
  </tr>
  <tr>
    <td>byte</td>
    <td>uint<td>
    <td>int</td>
    <td>bytes</td>
  </tr>
</table>

* `Msg ID`: 请求里面的Msg ID, 保证在一定时间内不重复， 可以每发一个msg, Msg ID +1

所有返回格式都为
<table>
  <tr>
    <th>类型</th>
    <th>Msg ID<th>
    <th>是否成功</th>
    <th>长度</th>
    <th>返回payload</th>
  </tr>
  <tr>
    <td>byte</td>
    <td>uint<td>
    <td>bool</td>
    <td>int</td>
    <td>bytes</td>
  </tr>
</table>

* `Msg ID`: 和请求里面的Msg ID一致

## 失败返回payload
所有失败返回的格式都为
<table>
  <tr>
    <th>错误码</th>
    <th>错误对象</th>
  </tr>
  <tr>
    <td>short</td>
    <td>short</td>
  </tr>
</table>

可能的错误码有
<table>
  <tr>
    <th>错误码</th>
    <th>含义</th>
  </tr>
  <tr>
    <td>0x01</td>
    <td>不存在 NOT_EXISTS</td>
  </tr>
  <tr>
    <td>0x02</td>
    <td>超时 TIMEOUT</td>
  </tr>
  <tr>
    <td>0x03</td>
    <td>Server内部错误 INTERNAL_ERROR</td>
  </tr>
  <tr>
    <td>0x04</td>
    <td>已经被占用 USED</td>
  </tr>
</table>

可能的`错误对象`有
<table>
  <tr>
    <th>错误对象</th>
    <th>含义</th>
  </tr>
  <tr>
    <td>0x00</td>
    <td>未指定对象 NOT_SET</td>
  </tr>
  <tr>
    <td>0x01</td>
    <td>邮箱 EMAIL</td>
  </tr>
  <tr>
    <td>0x02</td>
    <td>密码 PASSWORD</td>
  </tr>
  <tr>
    <td>0x03</td>
    <td>设备 DEVICE</td>
  </tr>
</table>

 * 系统只用这4个错误代码， 在具体命令里面含义有一些不同， 比如USED在检测邮箱的时候可以是邮箱已经被占用的意思，
在关联设备的时候可以是设备已经被占用的意思
 * 我们假设有些错误情况在正常使用的时候是不会发生的， 比如解除设备时设备ID不存在。 协议就不会规定这样的错误
 * `超时` 所有需要发送给设备的指令都可能会返回该错误, 其错误对象为NOT_SET
 * `不存在` 所有需要发送给设备的指令都可能会返回该错误, 其错误对象为NOT_SET
 * 如果这个命令需要登录再发送，但是你没有登录，会返回NOT_EXISTS
 * 需要将错误码和错误对象结合起来看， 如果错误对象为0x00, 则只需要看错误码
 * 如果设备返回错误给服务器，需要把`错误对象`设为0x03

## 协议交互
### 手机到服务器
-->LOGIN-->(SEND ALL KINDS OF MSG)-->LOGOUT
### 设备到服务器
-->LOGIN-->(SEND ALL KINDS OF MSG)
### 手机和设备直连
SEND ALL KINDS OF MSG

## 手机->服务器

### 获取设备信息(0x83)
可以获取如温度等信息，这些信息需要向设备查询
#### 请求payload为

<table>
  <tr>
    <th>设备ID</th>
  </tr>
  <tr>
    <td>device_id</td>
  </tr>
</table>

#### 返回payload为
返回格式见下面设备的返回(0xa4)

### 控制设备(0x84)
#### 请求payload为

<table>
  <tr>
    <th>设备ID</th>
    <th>控制信息</th>
  </tr>
  <tr>
    <td>device_id</td>
    <td>bytes</td>
  </tr>
</table>
`控制信息`是一个字符串，每种设备，它的定义都不相同
单个控制命令的通用定义为
<table>
  <tr>
    <th>控制命令</th>
    <th>控制命令参数长度</th>
    <th>控制命令参数</th>
  </tr>
   <tr>
    <td>short</td>
    <td>int</td>
    <td>bytes</td>
  </tr>
</table>
下面定义下这个项目会用到的控制指令

1. 锁定设备(0x01)
  <table>
      <tr>
      <th>控制命令</th>
      <th>长度</th>
      <th>是否锁定</th>
    </tr>
    <tr>
      <td>short</td>
      <td>int</td>
      <td>byte</td>
    </tr>
  </table>

2. 组合控制命令(0x02)
  <table>
    <tr>
      <th>控制命令</th>
      <th>长度</th>
      <th>组数</th>
      <th>单个命令</th>
    </tr>
    <tr>
      <td>short</td>
      <td>int</td>
      <td>byte</td>
      <td>bytes</td>
    </tr>
  </table>

  `单个命令`的定义为
  <table>
    <tr>
      <th>控制命令</th>
      <th>控制命令参数长度</th>
      <th>控制命令参数</th>
      <th>额外参数长度</th>
      <th>额外参数</th>
    </tr>
     <tr>
      <td>short</td>
      <td>int</td>
      <td>bytes</td>
      <td>int</td>
      <td>bytes</td>
    </tr>
  </table>
  
  `可能的错误返回`: USED DEVICE

3. 学习按键的`控制指令`(0x10)

  <table>
    <tr>
      <th>控制命令</th>
      <th>长度</th>
    </tr>
    <tr>
      <td>short</td>
      <td>int</td>
    </tr>
  </table>

4. 发送红外指令(0x11)
  <table>
    <tr>
      <th>控制命令</th>
      <th>是否是红外标识符</th>
      <th>红外指令信息</th>
    </tr>
    <tr>
      <td>short</td>
      <td>bool</td>
      <td>bytes</td>
    </tr>
  </table>
如果`是否红外标识符`为1, 则`红外指令信息`格式为
  <table>
    <tr><th>红外标识符</th></tr>
    <tr><th>int</th></tr>
  </table>
如果`是否红外标识符`为0, 则`红外指令信息`格式为
  <table>
    <tr>
      <th>红外信号长度</th>
      <th>红外信号</th>
    </tr>
    <tr>
      <th>int</th>
      <th>bytes</th>
    </tr>
  </table>

#### 返回payload为
3. 学习按键的返回

  <table>
    <tr>
      <th>红外信号的标识</th>
      <th>红外信号</th>
    </tr>
    <tr>
      <td>int</td>
      <td>int+bytes</td>
    </tr>
  </table>
  
4. 空

### 关联设备(0x8c)
#### 请求payload为

<table>
  <tr>
    <th>设备ID</th>
    <th>主设备ID</th>
    <th>时区</th>
  </tr>
  <tr>
    <td>device_id</td>
    <td>device_id/none</td>
    <td>string</td>
  </tr>
</table>

`时区`的可能取值见 https://github.com/moment/moment-timezone/blob/develop/moment-timezone.json

  `可能的错误返回`: USED NOT_SET

如果该设备是主设备,则主设备ID不需要填写; 如果是从设备,则主设备ID必须填写

#### 返回payload为
空

### 解除关联设备(0x8d)
#### 请求payload为

<table>
  <tr>
    <th>设备标识</th>
  </tr>
  <tr>
    <td>device_id</td>
  </tr>
</table>

#### 返回payload为
空

### 注册用户(0x90)
#### 请求payload为
<table>
  <tr>
    <th>email</th>
    <th>密码</th>
  </tr>
  <tr>
    <td>string</td>
    <td>string</td>
  </tr>
</table>

  `可能的错误返回`: USED EMAIL, USED PASSWORD
#### 返回payload为
空

## 修改密码(0x91)
#### 请求payload为
<table>
  <tr>
    <th>email</th>
    <th>原密码</th>
    <th>新密码</th>
  </tr>
  <tr>
    <td>string</td>
    <td>string</td>
    <td>string</td>
  </tr>
</table>
  `可能的错误返回`: NOT_EXISTS EMAIL, NOT_EXISTS PASSWORD
#### 返回payload为
空

## 找回密码(0x91)
#### 请求payload为
<table>
  <tr>
    <th>email</th>
  </tr>
  <tr>
    <td>string</td>
  </tr>
</table>
  `可能的错误返回`: NOT_EXISTS EMAIL
#### 返回payload为
空

## 用户登录(0x92)
#### 请求payload为
<table>
  <tr>
    <th>email</th>
    <th>密码</th>
  </tr>
  <tr>
    <td>string</td>
    <td>string</td>
  </tr>
</table>
#### 返回payload为
<table>
  <tr>
    <th>是否改IP</th>
    <th>ip地址</th>
    <th>端口号</th>
    <th>设备数量</th>
    <th>单个设备状态描述</th>
  </tr>
  <tr>
    <td>bool</td>
    <td>ip/none</td>
    <td>int/none</td>
    <td>int</td>
    <td>bytes</td>
  </tr>
</table>

`单个设备状态描述`的定义为
<table>
  <tr>
    <th>设备ID</th>
    <th>设备状态长度</th>
    <th>设备状态</th>
  </tr>
  <tr>
    <td>device_id</td>
    <td>int</td>
    <td>bytes</td>
  </tr>
</table>
`设备状态` 在红外项目里面的定义为
<table>
  <tr>
    <th>温度</th>
    <th>是否锁定</th>
    <th>以后扩充字段</th>
  </tr>
  <tr>
    <td>short</td>
    <td>bool</td>
    <td>int</td>
  </tr>
</table>

### 用户退出(0x93)
#### 请求payload为
空
#### 返回payload为
空

### 检查邮箱(0x94)
#### 请求payload为
<table>
  <tr>
    <th>email</th>
  </tr>
  <tr>
    <td>string</td>
  </tr>
</table>
#### 返回payload为
空

`可能的错误返回`: USED EMAIL

### 上传手机配置(0x95)
#### 请求payload为
<table>
  <tr>
    <th>配置信息</th>
  </tr>
  <tr>
    <td>bytes</td>
  </tr>
</table>
`配置信息`, 对于这个红外项目的定义为
<table>
  <tr>
    <th>配置名</th>
    <th>震动</th>
    <th>声音</th>
    <th>房间信息</th>
  </tr>
  <tr>
    <td>string</td>
    <td>bool</td>
    <td>bool</td>
    <td>bytes</td>
  </tr>
</table>
`配置名`: 可以好几组配置， 如果不想用到好几组， 可以使用一组
`房间信息`定义如下
<table>
  <tr>
    <th>房间组数</th>
    <th>每个房间信息</th>
  </tr>
  <tr>
    <td>byte</td>
    <td>bytes</td>
  </tr>
</table>
`每个房间信息`定义如下
<table>
  <tr>
    <th>长度</th>
    <th>房间描述</th>
  </tr>
  <tr>
    <td>int</td>
    <td>bytes</td>
  </tr>
</table>
`房间描述`的定义如下
<table>
  <tr>
    <th>房间ID</th>
    <th>房间名称</th>
    <th>设备信息</th>
  </tr>
  <tr>
    <td>byte</td>
    <td>string</td>
    <td>bytes</td>
  </tr>
<table>

`设备信息`为设备数量(byte), 每个设备信息(bytes)
每个设备信息定义为
<table>
  <tr>
    <th>设备ID</th>
    <th>设备名称</th>
    <th>设备图片</th>
    <th>设备属性</th>
  </tr>
  <tr>
    <td>devide_id</td>
    <td>string</td>
    <td>int+bytes</td>
    <td>short</td>
  </tr>
</table>

`设备属性` 设备属性在设备ID为不同时，含义不一样， 如果设备ID里说明是红外设备，那么bit 0表示是否有附属遥控设备

如果有遥控器，则后面跟遥控器的信息
遥控器的字段定义如下
<table>
   <tr>
       <th>遥控器数量</th>
       <th>遥控器信息</th>
   </tr>
   <tr>
        <td>byte</td>
        <td>bytes</td>
   </tr>
</table>
单个遥控器的字段定义如下
<table>
   <tr>
      <th>遥控器ID</th>
      <th>遥控器类型</th>
      <th>遥控器属性</th>
   </tr>
   <tr>
      <td>byte</td>
      <td>remote_controller_type</td>
      <td>short</td>
   </tr>
</table>

对于非自定义类型的遥控器，如果`遥控器属性`bit 0为1, 则接下去为学习信号的数量(byte), 学习信号的定义(bytes)
学习信号的定义的具体格式为
<table>
   <tr>
       <th>键ID</th>
       <th>键信号ID</th>
       <th>键信号</th>
   </tr>
   <tr>
       <td>short</td>
       <td>int</td>
       <td>int+bytes</td>
   </tr>
</table>
对于非自定义类型的遥控器，如果`遥控器属性`bit 1为1, 则接下去为扩展键的数量(byte), 扩展键的定义(bytes)
扩展键定义的具体格式为
<table>
   <tr>
       <th>扩展键ID</th>
       <th>扩展键名称</th>
       <th>扩展键信号ID</th>
       <th>键信号</th>
   </tr>
   <tr>
       <td>int</td>
       <td>string</td>
       <td>int</td>
       <td>int+bytes</td>
   </tr>
</table>

如果是自定义类型的遥控器，则当`遥控器属性`bit 0为1，则接下去为自定义类型遥控器的信息
自定义类型遥控器的信息包括: 键的个数(byte), 键的定义.
单个键的定义为
<table>
   <tr>
       <th>键ID</th>
       <th>键名称</th>
       <th>键离屏幕中心点x</th>
       <th>键离屏幕中心点y</th>
       <th>键类型</th>
       <th>键信号ID</th>
       <th>键信号</th>
   </tr>
   <tr>
       <td>int</td>
       <td>string</td>
       <td>int</td>
       <td>int</td>
       <td>short</td>
       <td>int</td>
       <td>int+bytes</td>
   </tr>
</table>
`键类型`的意思是：会有各种预定义的键，比如圆的，方的，各种大小的，每个都会有个类型标识

#### 返回payload为
空

### 下载手机配置(0x96)
#### 请求payload为
空
#### 返回payload为
上传手机配置的请求相同

## 设备->服务器

### 设备登录(0xa0)
#### 请求payload为
<table>
  <tr>
    <th>设备id</th>
  </tr>
  <tr>
    <td>device_id</td>
  </tr>
</table>
#### 返回payload为
<table>
  <tr>
    <th>时间</th>
    <th>星期数</th>
    <th>更换IP</th>
    <th>ip地址</th>
  </tr>
  <tr>
    <td>time_BCD</td>
    <td>byte [1,7]</td>
    <td>bool</td>
    <td>ip, 更换ip为true时存在</td>
  </tr>
</table>

### 设备心跳(0xa1)
#### 请求payload为
空
#### 返回payload为
空

### 设备状态汇报(0xa2)
* 设备可以隔段时间给服务器发送当前设备的状态, 以便服务器可以更新其缓存
* 服务器对这个命令不做任何返回

#### 请求payload为
<table>
  <tr>
    <th>设备ID</th>
    <th>状态</th>
  </tr>
  <tr>
    <td>device_id</td>
    <td>bytes</td>
  </tr>
</table>

`状态`: bytes, 以key value的形式给出，具体key value的形式见0xa6的描述

### 控制返回(0xa5)

控制的请求格式见(0x84)

这是由服务器给设备发`控制设备`后的返回

1. 学习红外(0x10)
<table>
  <tr>
    <th>控制子类型</th>
    <th>红外信号</th>
  </tr>
  <tr>
    <td>byte</td>
    <td>bytes</td>
  </tr>
</table>

### 获取设备状态返回(0xa6)

查询的请求格式见(0x83)

这是由服务器给设备发`查询设备`后的返回

<table>
  <tr>
    <th>状态信息</th>
  </tr>
  <tr>
    <td>bytes</td>
  </tr>
</table>
每种状态都是key, value形式
<table>
  <tr>
    <th>名称(key)</th>
    <th>值(value)</th>
  </tr>
  <tr>
    <td>string</td>
    <td>depends on key</td>
  </tr>
</table>
比如如果是温度,就返回
<table>
  <tr>
    <th>名称(key)</th>
    <th>值(value)</th>
  </tr>
  <tr>
    <td>Temp</td>
    <td>30(short)</td>
  </tr>
</table>

### 控制设备(0xa5)
和手机发给服务器的一致(0x84)

### 查询设备(0xa6)
和手机发给服务器的一致(0x83)

### 同步时间(0xa7)
请求payload为
空

返回payload为
<table>
  <tr>
    <th>时间</th>
    <th>星期数</th>
  </tr>
  <tr>
    <td>time_BCD</td>
    <td>byte [1,7]</td>
  </tr>
</table>

## 举例
* 比如`同步时间(0xa7)`命令， 设备将发如下bytes给server
  
  ```
  a7 00 00 00 32 00 00 00 00
  ```
  `a7` 为类型， `00 00 00 32`为Msg ID, `00 00 00 00` 为长度

  server如果处理成功，会返回
  
  ```
  a7 00 00 00 32 01 00 00 00 08 20 14 01 20 11 21 22 6
  ```
  `a7` 为类型，`00 00 00 32`为Msg ID, `01` 表示成功, `00 00 00 08`为长度， `20 14 01 20 11 21 22` 表示2014-01-20 11:21:22 `6`表示星期六

