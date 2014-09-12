# CU API

## 名词解释

* auth_id: 每个请求除了登录，都需要输入auth_id, auth_id是登录请求返回给用户的。主要是每个API的安全而设置

## 错误返回的基本格式
```
<response>
    <result>error</result>
    <error_code>错误代码</error_code>
    <error_desc>错误描述</error_desc>
</response>
```

## 每个API都可能返回的错误
* 800: auth_id不正确或已过期
* 801: 找不到user


## 查找所有省
查找所有的省

GET https://$ip:$port/user/provinces

* 成功，返回 

```
<response>
    <result>ok</result>
    <count>个数</count>
    <value>
       <id></id>
       <name></name>
    </value>
    <value>
       <id></id>
       <name></name>
    </value>
</response>
```

## 查找某省的地区
POST https://$ip:$port/user/city

```
<request>
  <province_id>省ID</province_id>
</request>
```

* 成功返回

```
<response>
    <result>ok</result>
    <count>个数</count>
    <value>
       <id></id>
       <name></name>
    </value>
    <value>
       <id></id>
       <name></name>
    </value>
</response>
```

## 查找某市的区县
POST https://$ip:$port/user/district

```
<request>
  <city_id>市ID</city_id>
</request>
```

* 成功返回

```
<response>
    <result>ok</result>
    <count>个数</count>
    <value>
       <id></id>
       <name></name>
    </value>
    <value>
       <id></id>
       <name></name>
    </value>
</response>
```

## 获取验证图片

GET https://$ip:$port/auth/picture

* 成功， 返回

```
<response>
    <url>产生的验证图片地址</url>
    <auth_pic_id>验证图片ID</auth_pic_id>
</response>
```


## 用户注册
用户注册帐号

POST https://$ip:$port/user/register

```
<request>
    <user>
        <user>用户名(中英文),数字，下划线，中划线</user>
        <password>英文，密码</password>
        <email></email>
        <auth_pic_num>验证图片上的字符</auth_pic_num>
        <auth_pic_id>验证图片ID</auth_pic_id>
    </user>
</request>
```

* 成功，返回

```
<response>
    <result>ok</result>
</response>

```

可能的错误码

* 501: user不符合规范
* 502: user已经被占用
* 509: 密码强度不够, 必须包含至少一个大写字母，一个数字, 一个小写字母, 长度至少为8
* 515: 验证码不正确
* 516: email不符合规范
* 517: email已经被占用

## 用户加盟成商家
整个系统在开始的时候会在数据库内插入一个用户，该用户为超级用户，为总管理,
可以添加各级代理，商家，业务员

POST https://$ip:$port/user/add 

```
<request>
    <auth_id>auth_id</auth_id>
    <user>
        <name>用户姓名(中英文)</name>
        <tel>固定电话(数字, +, 中划线)</tel>
        <phone>手机</phone>
        <type>类型： 1表示省代理，2表示地区代理，3表示县市代理，4表示业务员，5表示商家，6表示注册员, 7表示会员</type>
        <province_id>省ID</province_id>
        <city_id>市ID</city_id>
        <district_id>县区ID</district_id>
        <address>用户的详细地址</address>
        <company>公司名</company>
        <post_code>邮编</post_code>
        <id_card>身份证号码</id_card>
        <catelog>所属分类</catelog>
        <certification>荣誉证书url</certification>
        <license>营业执照url</license>
        <ass_user>关联上级的用户名</ass_user>
    </user>
</request>

```

* 创建成功，返回

```
<response>
    <result>ok</result>
</response>

```

可能的失败码

* 503: 固定电话不正确
* 506: email不正确
* 507: 权限不足
* 520: ass_user不存在
* 521: ass_user权限不足
* 522: 已经有正在申请的加盟
* 530: 申请用户不是会员


## 查看等待批准的申请
下级的申请需要上级批准, 该API获取所有下级的申请

POST https://$ip:$port/user/waiting

```
<request>
    <auth_id></auth_id>
    <offset>起始offset, 分页使用</offset>
    <limit>limit, 每页个数</limit>
</request>
```

* 成功，返回

```
<response>
    <result>ok</result>
    <count>总的待审核用户个数</count>
    <users>
        <user>
            <id>用户ID</id>
            <name>用户姓名(中英文)</name>
            <tel>固定电话(数字, +, 中划线)</tel>
            <phone>手机</phone>
            <type>类型： 1表示省代理，2表示地区代理，3表示县市代理，4表示业务员，5表示商家，6表示注册员, 7表示会员</type>
            <address>用户的详细地址</address>
            <company>公司名</company>
            <post_code>邮编</post_code>
            <id_card>身份证号码</id_card>
            <catelog>所属分类</catelog>
            <certification_url>荣誉证书url</certification_url>
            <license_url>营业执照url</license_url>
        </user>
    </users>
</response>

```

可能的失败码:
无

## 批准申请
批准下级的加盟申请

POST https://$ip:$port/user/approve

```
<request>
    <auth_id></auth_id>
    <id>被批准的user id</id>
</request>
```

可能的失败吗

* 523: user不存在
* 507: 权限不足

## 拒绝申请
POST https://$ip:$port/user/deny

```
<request>
    <auth_id></auth_id>
    <id>被拒绝的user id</id>
</request>
```


## 数据统计
POST https://$ip:$port/statistics

```
<request>
    <auth_id></auth_id>
    <type>统计类型: user, company, agency1, agency2, agency3, user1, user2, trade, point</type>  user为会员， comany为商家,  agency1为省代理，
                    agency2为地区代理，agency3为县代理, user1为业务员， user2为注册员, trade为交易笔数， point为积分
                    
    <date_from>起始时间 (2014-02-12 00:00:00)</date_from>
    <date_to>起始时间 (2014-02-12 00:00:00)</date_to>
    <province_id>省ID</province_id>
    <city_id>市ID</city_id>
    <district_id>县区ID</district_id>
    <assoc_with_id>相关某人的ID</assoc_with_id>
    <offset>分页offset</offset>
    <limit>分页limit</limit>
</request>
```

如果起始时间和结束时间都为空，则返回所有时间的
如果只想统计某个省的， 只需要填写省ID，以此类推

如果想统计和某个用户ID相关的东西， assoc_with_id填上该用户ID, 用户可能是商家， 代理，业务员等

成功返回:

如果查询的是user, company, agency1, agency2, agency3, user1, user2返回格式为

```
<response>
   <count>个数</count>
</response>
```

如果查询的是trade， 则返回

```
<response>
   <count>总个数</count>
   <failed>失败个数</failed>
</response>
```

如果查询的是point

```
<response>
   <income>收益点数</income>
   <outcome>支出点数</outcome>
</response>
```

* 创建失败，返回

```
<response>
    <result>error</result>
    <error_code>错误代码</error_code>
    <error_desc>错误描述</error_desc>
</response>
```

## 查看当前分成设置

POST https://$ip:$port/get/point_allocation

```
<request>
    <auth_id></auth_id>
    <province_id>省ID</province_id>
    <offset>分页offset</offset>
    <limit>分页limit</limit>
</request>
```

如果省ID为空，则查询所有的

```
<response>
    <result>ok</result>
    <count>总个数</count>
    <setting>
       <value>
          <id></id>
          <province_id>省ID</province_id>
          <province>省</province>
          <platform>平台分成比例, 小数</platform>
          <agency1>省代理分成比例, 小数</agency1>
          <agency2>区域代理分成比例, 小数</agency2>
          <agency3>县市代理分成比例, 小数</agency3>
          <user1>业务员分成比例, 小数</user1>
          <user2>注册员分成比例, 小数</user2>
          <user>会员分成比例, 小数</user>
          <company>商家分成比例, 小数</company>
       </value>
    </setting>
</response>
```

## 设置分成比例

POST https://$ip:$port/set/point_allocation

```
<request>
    <auth_id></auth_id>
    <id>分成比例ID</id>
    <platform>平台分成比例, 小数</platform>
    <agency1>省代理分成比例, 小数</agency1>
    <agency2>区域代理分成比例, 小数</agency2>
    <agency3>县市代理分成比例, 小数</agency3>
    <user1>业务员分成比例, 小数</user1>
    <user2>注册员分成比例, 小数</user2>
    <user>会员分成比例, 小数</user>
    <company>商家分成比例, 小数</company>
</request>
```

可能的错误

* 510: 分成比例不正确， 加起来不是100%

## 删除分成比例

POST https://$ip:$port/del/point_allocation

```
<request>
    <auth_id></auth_id>
    <id>分成比例ID</id>
</request>
```

## 添加分成比例

POST https://$ip:$port/add/point_allocation

```
<request>
    <auth_id></auth_id>
    <province_id>省ID</province_id>
    <platform>平台分成比例, 小数</platform>
    <agency1>省代理分成比例, 小数</agency1>
    <agency2>区域代理分成比例, 小数</agency2>
    <agency3>县市代理分成比例, 小数</agency3>
    <user1>业务员分成比例, 小数</user1>
    <user2>注册员分成比例, 小数</user2>
    <user>会员分成比例, 小数</user>
    <company>商家分成比例, 小数</company>
</request>
```

* 成功，返回

```
<response>
    <result>ok</result>
    <id>分成比例ID</id>
</response>
```

可能的错误

* 511: 该省的分成比例已经存在，不能再添加
* 524: 分成比例不正确
* 533: 找不到该省


## 用户申请提现, 就是转账申请
用户申请用积分换取货币

https://$ip:$port/user/point_to_money

```
<request>
    <auth_id></auth_id>
    <point>积分</point>
</request>
```

* 申请成功

```
<response>
    <result>ok</result>
</response>
```

可能的失败码

* 508: 积分不足

## 同意用户提现

https://$ip:$port/user/approve_ptm

```
<request>
    <auth_id></auth_id>
    <point>积分</point>
</request>
```

* 申请成功

```
<response>
    <result>ok</result>
</response>
```


## 用户修改信息

POST https://$ip:$port/user/edit

```
<request>
    <auth_id>auth_id</auth_id>
    <user_id>如果修改的人是总管理，需要填写被修改的用户id</user_id>
    <user>
        <name>用户姓名(中英文)</name>
        <tel>固定电话(数字, +, 中划线)</tel>
        <phone>手机</phone>
        <address>用户的详细地址</address>
        <company>公司名</company>
        <post_code>邮编</post_code>
        <id_card>身份证号码</id_card>
        <catelog>所属分类</catelog>
        <bank_card_id>银行卡卡号</bank_card_id>
        <bank>开户银行</bank>
        <bank_user>银行卡所有人</bank_user>
        <certification>荣誉证书url</certification>
        <license>营业执照url</license>
        <card_num>会员卡卡号</card_num>
        <gender>性别, 0表示男，1表示女</gender>
        <qq>qq号码</qq>
    </user>
</request>

```

certification和license不是必填的， 如果用户没修改，则设置为空

成功返回：

```
<response>
    <result>ok</result>
</response>
```

可能错误：

见新建用户


## 列表
列出用户想查询的东西

POST https://$ip:$port/list/kinds

```
<request>
    <auth_id></auth_id>
    <type>统计类型: user, company, agency1, agency2, agency3, user1, user2, trade</type>  user为会员， comany为商家,  agency1为省代理，
                    agency2为地区代理，agency3为县代理, user1为业务员， user2为注册员, trade为交易
                    
    <date_from>起始时间 (2014-02-12 00:00:00)</date_from>
    <date_to>起始时间 (2014-02-12 00:00:00)</date_to>
    <province_id>省ID</province_id>
    <city_id>市ID</city_id>
    <district_id>县区ID</district_id>
    <assoc_with_id>相关某人的ID</assoc_with_id>
    <trade_spec>
       <type>1. 商品交易， 3，回档交易, 4. 积分提现, 5. 充值</type>
       <is_finished>0或1， 0表示未完成， 1表示已完成</is_finished>
       <is_from>0 或 1, 是否是交易的发起方. 
       对于商品交易，回档交易. from是user， to是company
       对于积分提现 from可能是agency1, agency2, ... user,  to是admin
       对于充值     from是company,  to是admin
       </is_from>
    </trade_spec>
    <offset>分页offset</offset>
    <limit>分页limit</limit>
    <order>asc 或者 desc</order>
</request>
```

如果起始时间和结束时间都为空，则返回所有时间的
如果只想统计某个省的， 只需要填写省ID，以此类推

assoc_with_id见前面解释

trade_spec只有在type为trade时才要出现

成功返回:

如果查询的是user, company, agency1, agency2, agency3, user1返回格式为

```
<response>
   <count>个数</count>
   <items>
     <item>
        <id></id>
        <user></user>
        <name>用户姓名(中英文)</name>
        <tel>固定电话(数字, +, 中划线)</tel>
        <phone>手机</phone>
        <type>类型： 1表示省代理，2表示地区代理，3表示县市代理，4表示业务员，5表示商家，6表示注册员, 7表示会员</type>
        <province>省名称</province>
        <city>市名称</city>
        <district>县名称</district_id>
        <address>用户的详细地址</address>
        <company>公司名</company>
        <post_code>邮编</post_code>
        <id_card>身份证号码</id_card>
        <catelog>所属分类</catelog>
        <certification_url>荣誉证书url</certification_url>
        <license_url>营业执照url</license_url>
        <bank_card_id>银行卡卡号</bank_card_id>
        <bank>开户银行</bank>
        <bank_user>银行卡所有人</bank_user>
        <card_num>会员卡卡号</card_num>
        <gender>性别</gender>
        <qq>qq号码</qq>
        <point>点数</point>
        <enabled>是否已启用</enabled>
        <real_name>是否实名</real_name>
        <real_company>是否实名公司</real_company>
        <recruited_at>如果查询的是user2和user, 这会显示所属的商家. 查询其它类型，则此为空<recruited_at>
     </item>
   </items>
</response>
```

如果查询的是user2, 还会有`<member_count>`返回，用于表示该注册员注册的会员数

如果查询的是trade, 则返回:

```
<response>
   <count>个数</count>
   <items>
     <item>
        <id></id>
        <name>用户姓名(中英文)</name>
        <user_type>用户类型</user_type>
        <point_belong_to_me>自己的收益积分</point_belong_to_me>
        <card_num>会员卡卡号</card_num>
        <where>消费地点</where>
        <company>商家名</company>
        <good_name>商品名</good_name>
        <price>商品价格</price>
        <type>1现金，2积分</type>
        <used>消费钱或者积分</used>
        <produce_type>产生的类型： 1现金，2积分</produce_type>
        <producted>产生积分或金钱</producted>
        <time>交易时间</time>
        <trade_type>交易类型, 1. 商品交易 3，回档交易, 4. 积分提现, 5. 充值</trade_type>
        <is_finished>0或1</is_finished>
        <bank_spec> //积分提现， 充值时需要
            <bank_card_id>银行卡卡号</bank_card_id>
            <bank>开户银行</bank>
            <bank_user>银行卡所有人</bank_user>
        </bank_spec>
     </item>
   </items>
</response>
```

可能的错误码

* 525: 找不到该用户

## 搜索用户
搜索用户， 可以是会员, 可以是商家等

POST https://$ip:$port/search/kinds

```
<request>
    <auth_id></auth_id>
    <kind>user|name|tel|id_card|phone|address|id|card_num</kind>
    <type>类型： 1表示省代理，2表示地区代理，3表示县市代理，4表示业务员，5表示商家，6表示注册员, 7表示会员, 0表示不限定type</type>
    <value>搜索值</value>         
    <offset>分页offset</offset>
    <limit>分页limit</limit>
</request>
```

kind为组合， 如果输入name|tel 则会搜索name和tel2个选项， 如果只输入id_card, 则只搜索身份证

```
<response>
   <count>个数</count>
   <items>
     <item>
        <id></id>
        <user></user>
        <name>用户姓名(中英文)</name>
        <tel>固定电话(数字, +, 中划线)</tel>
        <phone>手机</phone>
        <type>类型： 1表示省代理，2表示地区代理，3表示县市代理，4表示业务员，5表示商家，6表示注册员, 7表示会员</type>
        <province_id>省id</province_id>
        <province>省名称</province>
        <city_id>市id</city_id>
        <city>市名称</city>
        <district_id>县id</district_id>
        <district>县名称</district>
        <address>用户的详细地址</address>
        <company>公司名</company>
        <post_code>邮编</post_code>
        <id_card>身份证号码</id_card>
        <catelog>所属分类</catelog>
        <certification_url>荣誉证书url</certification_url>
        <license_url>营业执照url</license_url>
        <bank_card_id>银行卡卡号</bank_card_id>
        <bank>开户银行</bank>
        <bank_user>银行卡所有人</bank_user>
        <card_num>会员卡卡号</card_num>
        <gender>性别</gender>
        <qq>qq号码</qq>
        <point>点数</point>
        <recruited_at>为其办会员的商家</recruited_at>
     </item>
   </items>
</response>
```

可能的错误码

* 526: 搜索条件不正确

## 会员办卡

POST https://$ip:$port/user/recruit

```
<request>
    <auth_id></auth_id>
    <user>用户名(中英文),数字，下划线，中划线</user>
    <phone>手机</phone>
    <email></email>
    <card_num>会员卡卡号</card_num>
    <name>用户真实姓名</name>
    <gender>性别</gender>
    <qq>qq号码</qq>
    <id_card>身份证号码</id_card>
</request>
```

成功返回

```
<response>
    <result>ok</result>
</response>

```

系统为会员设置默认密码666666

可能的失败码：

 * 502: user已经被占用
 * 507: 权限不足
 * 534: card_num已经被使用

## 修改密码
POST https://$ip:$port/user/change_password

```
<request>
    <auth_id></auth_id>
    <password>英文，密码</password>
    <new_password>新密码</new_password>
</request>
```

修改成功返回:
 
 ```
<response>
    <result>ok</result>
</response>
```

可能的错误:

* 512: 原密码不正确
* 513: 新密码强度不够

## 用户登录

POST https://$ip:$port/user/login

```
<request>
    <user></user>
    <password></password>
</request>
```

成功，返回

```
<response>
 <item>
    <auth_id></auth_id>
    <id></id>
    <name>用户姓名(中英文)</name>
    <user>用户名</user>
    <tel>固定电话(数字, +, 中划线)</tel>
    <phone>手机</phone>
    <type>类型： 1表示省代理，2表示地区代理，3表示县市代理，4表示业务员，5表示商家，6表示注册员, 7表示会员, 99表示总管理</type>
    <province>省名称</province>
    <city>市名称</city>
    <district>县名称</district_id>
    <address>用户的详细地址</address>
    <company>公司名</company>
    <post_code>邮编</post_code>
    <id_card>身份证号码</id_card>
    <catelog>所属分类</catelog>
    <certification_url>荣誉证书url</certification_url>
    <license_url>营业执照url</license_url>
    <bank_card_id>银行卡卡号</bank_card_id>
    <bank>开户银行</bank>
    <bank_user>银行卡所有人</bank_user>
    <card_num>会员卡卡号</card_num>
    <gender>性别</gender>
    <qq>qq号码</qq>
    <point>点数</point>
 </item>
</response>
```

可能的错误

* 518: 用户名不正确
* 519: 密码不正确
* 531: 用户已经被禁用


## 用户登出

POST https://$ip:$port/user/logout

```
<request>
  <auth_id></auth_id>
</request>
```


## 会员通知
POST https://$ip:$port/user/notify

```
<request>
    <auth_id></auth_id>
    <type>1表示所有会员， 2表示所有省代理， 3表示所有区域代理，4表示所有县市代理，5表示所有商家</type>
    <title>标题</title>
    <content>内容</content>
</request>
```
成功返回:

 ```
<response>
    <result>ok</result>
</response>
```

## 设置商品积分
POST https://$ip:$port/good/point

```
<request>
    <auth_id></auth_id>
    <name>商品名</name>
    <price>单价</price>
    <point_type>1或2， 1表示单价百分比获取积分， 2表示固定积分</point_type>
    <value>值</value>
</request>
```

成功返回

 ```
<response>
    <result>ok</result>
</response>
```

可能的失败码

* 507: 权限不足         -- 只有注册员或者商家才能设置商品积分
* 535: 商品名重复


##  获取商品积分列表
POST https://$ip:$port/good/list_point

```
<request>
    <auth_id></auth_id>
    <good_name>商品名， 可以不填</good_name>
    <offset></offset>
    <limit></limit>
</request>
```

成功返回

```
<response>
    <count>个数</count>
    <items>
       <item>
        <id></id>
        <name>商品名</name>
        <price>单价</price>
        <point_type>1或2， 1表示单价百分比获取积分， 2表示固定积分</point_type>
        <value>值</value>
       </item>
    </items>
</response>
```

## 删除商品积分

POST https://$ip:$port/good/del_point

```
<request>
    <auth_id></auth_id>
    <id>商品积分id</id>
</request>
```

## 添加交易

POST https://$ip:$port/add/trade

```
<request>
    <auth_id></auth_id>
    <good_name>商品名</good_name>
    <good_price>商品价格</good_price>
    <type>1现金，2积分</type>
    <price>价格/积分</price>
    <point>产生积分</point>
    <card_num>积分卡卡号</card_num>
</request>
```
成功返回

 ```
<response>
    <result>ok</result>
</response>

```

可能的失败码:

* 514: 积分不足
* 528: 积分卡卡号不正确
* 529: 所在省的积分方案未设定

## 设置用户积分 [will not be used]

POST https://$ip:$port/user/set_point

```
<request>
    <auth_id></auth_id>
    <id>用户id</id>
    <point>积分</point>
</request>
```

成功返回

 ```
<response>
    <result>ok</result>
</response>

```

## 批准积分申请

POST https://$ip:$port/user/approve_point

```
<request>
    <auth_id></auth_id>
    <trade_id>申请积分的id号</trade_id>
</request>
```

成功返回

 ```
<response>
    <result>ok</result>
</response>

```

## 商家向总管理申请积分
POST https://$ip:$port/user/apply_point

```
<request>
    <auth_id></auth_id>
    <point>点数</point>
</request>
```

成功返回

 ```
<response>
    <result>ok</result>
</response>

```

## 用户管理

POST https://$ip:$port/user_account/manage

```
<request>
    <auth_id></auth_id>
    <id>用户ID</id>
    <operation>disable, reset_password, confirm_real_name, confirm_real_company</operation>
</request>
```

disable表示禁用帐号， enable表示解除禁用帐号， reset_password表示重设密码， confirm_real_name表示确认实名， confirm_real_comany表示确认实体店铺

成功返回

 ```
<response>
    <result>ok</result>
</response>

```

可能的错误

* 525: 找不到该用户
