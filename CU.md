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
        <challenge_by_text>是否用手机短信进行验证，如果是, 下面auth_pic_num和auth_pic_id不需要填写.
        如果是， 填写platform_auth_id的内容
        </challenge_by_text>
        <auth_pic_num>验证图片上的字符</auth_pic_num>
        <auth_pic_id>验证图片ID</auth_pic_id>
        <challenge_code_handler>notify/send_text_challenge接口返回里面的内容, 手机验证时需要</challenge_code_handler>
        <challenge_code>发送到手机的验证码, 手机验证时需要</challenge_code>
        <company_id>商家id, 注册会员的时候需要, 其它时候不需要</company_id>
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
* 509: 密码必须为6位数字
* 515: 验证码不正确
* 516: email不符合规范
* 517: email已经被占用
* 544: challenge_by_text不正确
* 2300: 找不到对应的商家

## 忘记密码

针对会员

POST https://$ip:$port/user/forgot_password

```
<request>
    <user>
        <phone></phone>
        <password>英文，密码</password>
        <challenge_code_handler>notify/send_text_challenge接口返回里面的内容</challenge_code_handler>
        <challenge_code>发送到手机的验证码</challenge_code>
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

* 2111: phone指的用户找不到
* 509: 密码必须为6位数字
* 515: 验证码不正确
* 544: challenge_by_text不正确


## 用户加盟
整个系统在开始的时候会在数据库内插入一个用户，该用户为超级用户，为总管理,
可以添加各级代理，商家，业务员

POST https://$ip:$port/user/add s

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
        <longitude>经度</longitude>
        <latitude>纬度<latitude>
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
    <deny_reason>拒绝理由</deny_reason>
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
   <user_recharge>该账户下级商家(包含商家本身)给会员充值金额</user_recharge>
   <trade_amount>该段时间交易总额</trade_amount>
   <trade_by_point>该段时间会员在该商家积分消费数额/该段时间该角色下级会员的积分消费数额</trade_by_point>
   <points_generated>该段时间产生积分数(除总管理, 商家，其它角色该值为0)</points_generated>
   <user_gain_points>该段时间会员获得积分数(除总管理, 商家，其它角色该值为0)</user_gain_points>
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
    <trade_id>交易id</trade_id>
</request>
```

* 申请成功

```
<response>
    <result>ok</result>
</response>
```

## 删除交易
https://$ip:$port/trade/del

目前能删除的交易有： 积分提现.

```
<request>
    <auth_id></auth_id>
    <trade_id>交易id</trade_id>
</request>
```

*  成功

```
<response>
    <result>ok</result>
</response>
```

## 删除用户

POST https://$ip:$port/user/del

```
<request>
    <auth_id></auth_id>
    <user_id>要删除的用户的id</user_id>
</request>
```

成功返回：

```
<response>
    <result>ok</result>
</response>
```

* 现在能删除用户的只有总管理
* 能被删除的有: "会员", "没有会员的商家"

## 设置会员在某个商家每天最大交易次数
POST https://$ip:$port/user/set_max_trade_count_daily

```
<request>
    <auth_id></auth_id>
    <max_trade_count_daily>每天每个会员最大交易数</max_trade_count_daily>
</request>
```

成功返回：

```
<response>
    <result>ok</result>
</response>
```

## 获取会员在某个商家每天最大交易次数
POST https://$ip:$port/user/get_max_trade_count_daily
```
<request>
    <auth_id></auth_id>
</request>
```

成功返回：

```
<response>
    <result>ok</result>
    <max_trade_count_daily></max_trade_count_daily>
</response>
```



## 总管理修改商家对应业务员的信息
POST https://$ip:$port/user/edit_company_user1

该API会将商家的上级业务员改为 "业务员name"(username) 对应的业务员

```
<request>
    <auth_id></auth_id>
    <name>业务员name</name>
</request>
```


## 用户修改信息

POST https://$ip:$port/user/edit

```
<request>
    <auth_id>auth_id</auth_id>
    <user_id>如果只想修改银行卡相关信息，这个需要填写。否则不需要填写.
    上级只能修改下级的银行卡相关信息，不能修改其它信息
    </user_id>
    <set_virtual_bank_id>0, 1 或者不填</set_virtual_bank_id>
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
        <virtual_bank_card_id>虚拟银行帐号</virtual_bank_card_id>
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
* 550: virtual_bank_card_id已经被其它人使用
* 18923: bank_car_id已经被其它人使用


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
       <type>1. 商品交易， 3，回档交易, 4. 积分提现, 5. 商家充值, 6. 会员充值, 7. 活动积分, 8. 导流消费券积分</type>
       <good_trade_use_type>如果是商品交易， 可以填1（现金）， 2（积分），也可以不填，不填表示所有</good_trade_use_type>
       <is_finished>0或1， 0表示未完成， 1表示已完成</is_finished>
       <is_from>0 或 1, 是否是交易的发起方. 
       对于商品交易，回档交易. from是user， to是company
       对于积分提现 from可能是agency1, agency2, ... user,  to是admin
       对于商家充值     from是company,  to是admin
       对于会员充值 from是user, to是company
       对于活动积分 from是user, to是company
       对于导流消费券 from是company(付出积分的公司), to是company(收入积分的公司)
       
       如果填的是3， 并且assoc_with_id填的是商家， 则查询和该商家有关的会员消费而产生的trade信息
       如果填的是4， 则auth_id必须是商家的，assoc_with_id里面填会员的ids, 这样会查assoc_with_id里的会员在该商家消费的记录
       </is_from>
    </trade_spec>
    <offset>分页offset</offset>
    <limit>分页limit</limit>
    <order>asc 或者 desc</order>
    <order_by>查询为trade,user时， 可以填写, 按什么字段进行排序</order_by>
</request>
```

如果起始时间和结束时间都为空，则返回所有时间的
如果只想统计某个省的， 只需要填写省ID，以此类推

assoc_with_id见前面解释

trade_spec只有在type为trade时才要出现

成功返回:

如果查询的是user, company, agency1, agency2, agency3, user1返回格式为
可以查询多种角色， 比如既想差agency1又想查agency2, 可以填agency1|agency2

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
        <virtual_bank_card_id>虚拟银行帐号</virtual_bank_card_id>
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
        <total_point>总积分， 总共获得的积分数</total_point>
        <ptm>提现积分</pmt>
        <recharge_money>充值金钱(商家和会员都有该项)</recharge_money>
        <user_recharge_money>会员在该商家的充值金额</user_recharge_money>
        <good_trade_point>会员使用积分进行交易时商家直接获得的积分</good_trade_point>
        <share_point>因为积分分成得到的积分</share_point>
        <used_point>积分消费总额(会员)/送出的积分(商家)</used_point>
        <created_at>创建时间</created_at>
        <user1_id>当user_type为5时输出，业务员的id</user1_id>
        <user1_name>当user_type为5时输出，业务员的name</user1_name>
        <user1_phone>当user_type为5时输出，业务员的phone</user1_phone>
     </item>
   </items>
</response>
```

如果查询的是user2,company 还会有`<member_count>`返回，用于表示该注册员注册的会员数

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
        <trade_type>交易类型, 1. 商品交易 3，回档交易, 4. 积分提现,  5. 商家充值, 6. 会员充值, 7. 活动积分</trade_type>
        <is_finished>0或1</is_finished>
        //积分提现， 充值时需要
        <bank_card_id>银行卡卡号</bank_card_id>
        <bank>开户银行</bank>
        <bank_user>银行卡所有人</bank_user>
        <virtual_bank_card_id>虚拟银行帐号</virtual_bank_card_id>
        <province_id>商品交易时有这个</province_id>
        <province>商品交易时有这个</province>
        <city_id>商品交易时有这个</city_id>
        <city>商品交易时有这个</city>
        <district_id>商品交易时有这个</district_id>
        <district>商品交易时有这个</district>
        <point_belong_to_user>会员的收益积分</point_belong_to_user>
     </item>
   </items>
</response>
```

如果查询的是trade, 并且trade_spec/type 是8， 也就是导流活动，返回是
```
<response>
   <count>个数</count>
   <items>
     <item>
        <id></id>
        <coupon_id>积分券id</coupon_id>
        <card_num></card_num> #会员卡卡号
        <time></time> #产生时间
        <from></from> #from是company(付出积分的公司)
        <from_company></from_company> #付出积分公司名称
        <to></to> #to是company(收入积分的公司)
        <to_company></to_company> #收入积分公司名称
        <company_incoming></company_incoming> #收入积分公司，收入的积分
        <platform_incoming></platform_incoming> #平台收入积分
        <user_incoming></user_incoming> #用户收入，即优惠券面额
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
    <kind>user|name|tel|id_card|phone|address|id|card_num|virtual_bank_card_id|company</kind>
    <type>类型： 1表示省代理，2表示地区代理，3表示县市代理，4表示业务员，5表示商家，6表示注册员, 7表示会员, 0表示不限定type</type>
    <value>搜索值</value>
    <limit_lower>是否只搜索auth_id的下级, 0或1, 1表示只搜索下级</limit_lower>
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
        <virtual_bank_card_id>虚拟银行帐号</virtual_bank_card_id>
        <card_num>会员卡卡号</card_num>
        <gender>性别</gender>
        <qq>qq号码</qq>
        <point>点数</point>
        <enabled>是否已启用</enabled>
        <real_name>是否实名</real_name>
        <real_company>是否实名公司</real_company>
        <recruited_at>如果查询的是user2和user, 这会显示所属的商家. 查询其它类型，则此为空<recruited_at>
        <total_point>总积分， 总共获得的积分数</total_point>
        <ptm>提现积分</pmt>
        <recharge_money>充值金钱</recharge_money>
        <good_trade_point>会员使用积分进行交易时商家直接获得的积分</good_trade_point>
        <share_point>因为积分分成得到的积分</share_point>
        <used_point>积分消费总额(会员)/送出的积分(商家)</used_point>
        <recruited_at>为其办会员的商家</recruited_at>
        <created_at>创建时间</created_at>
        <user1_id>当user_type为5时输出，业务员的id</user1_id>
        <user1_name>当user_type为5时输出，业务员的name</user1_name>
        <user1_phone>当user_type为5时输出，业务员的phone</user1_phone>
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
    <user2_id>如果是商家为会员办的卡， 需要填写注册员的id</user2_id>
    <user>用户名(中英文),数字，下划线，中划线</user>
    <password>英文，密码, 如果为空， 则系统为用户设置默认密码</password>
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

## 获取用户积分等信息
POST https://$ip:$port/user/get_point_info

```
<request>
    <auth_id></auth_id>
</request>
```

```
<response>
 <item>
    <point>当前的点数</point>
    <total_point>总积分， 总共获得的积分数</total_point>
    <ptm>提现积分</pmt>
    <recharge_money>充值金钱</recharge_money>
    <good_trade_point>会员使用积分进行交易时商家直接获得的积分</good_trade_point>
    <share_point>因为积分分成得到的积分</share_point>
    <used_point>积分消费总额(会员)/送出的积分(商家)</used_point>
    <member_count>会员个数</member_count>
 </item>
</response>
```


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
    <virtual_bank_card_id>虚拟银行帐号</virtual_bank_card_id>
    <card_num>会员卡卡号</card_num>
    <gender>性别</gender>
    <qq>qq号码</qq>
    <point>当前的点数</point>
    <total_point>总积分， 总共获得的积分数</total_point>
    <ptm>提现积分</pmt>
    <recharge_money>充值金钱</recharge_money>
    <good_trade_point>会员使用积分进行交易时商家直接获得的积分</good_trade_point>
    <share_point>因为积分分成得到的积分</share_point>
    <activity_point>做活动获得或送出的积分(用户，总管理，商家有效)</activity_point>
    <used_point>积分消费总额(会员)/送出的积分(商家)</used_point>
    <member_count>会员个数</member_count>
    <server_ver_for_android>api对于android的版本号, 例如1.0.0</server_ver_for_android>
    <server_ver_for_ios>api对于ios的版本号， 例如1.0.0</server_ver_for_ios>
    <server_ver>api对于pc客户端的版本号， 例如1.0.0</server_ver>
    <belong_to>所属商家</belong_to>
    <pool_point>导流活动积分池积分</pool_point>
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

## 用户登录下级账户
POST https://$ip:$port/user/login_lower

```
<request>
    <auth_id></auth_id>
    <lower_user_id>下级user_id</lower_user_id>
</request>
```

## 会员通知
POST https://$ip:$port/user/notify

```
<request>
    <auth_id></auth_id>
    <from_who_id>要求发送的用户的id, 如果是某个商家，填这个商家的id</from_who_id>
    <type>用户类型，1表示省代理，..., 7为会员</type>
    <content>内容</content>
</request>
```
成功返回:

 ```
<response>
    <result>ok</result>
</response>
```

* 只有总管理才有权限发送会员通知

## 设置商品积分
POST https://$ip:$port/good/point

```
<request>
    <auth_id></auth_id>
    <company_id>当设置的人是总管理或县代理的时候需要填写</company_id>
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

* 有一种特殊的商品name叫 “让利积分比例”， 该商品用来设置商家的让利积分比例

可能的失败码

* 507: 权限不足         -- 只有注册员或者商家才能设置商品积分
* 535: 商品名重复
* 558: company找不到


##  获取商品积分列表
POST https://$ip:$port/good/list_point

```
<request>
    <auth_id></auth_id>
    <company_id>当设置的人是总管理或县代理的时候需要填写</company_id>
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
        <value>值, 如果是百分比，取值范围为0-1的浮点数</value>
       </item>
    </items>
</response>
```

## 删除商品积分

POST https://$ip:$port/good/del_point

```
<request>
    <auth_id></auth_id>
    <company_id>当设置的人是总管理或县代理的时候需要填写</company_id>
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
    <password>会员密码，如果是积分交易， 需要填写</password>
    <extra_price>如果会员一部分用现金支付， 一部分用积分支付， 这填现金支付的部分</extra_price>
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

## 设置用户积分

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

可能的错误码
* 556: 用户id不正确
* 557: point不能为负数

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

可能的失败码

* 549: 申请的积分不够所欠的

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


## 发送短信验证号码给用户
POST https://$ip:$port/notify/send_text_challenge

```
<request>
    <platform_auth_id>只有手机软件和服务器知道的一个字符串，为了防止没权限的人发短信</platform_auth_id>
    <phone>手机号码</phone>
</request>
```

成功返回
```
<response>
    <result>ok</result>
    <challenge_code_handler>注册，忘记密码等接口提交时需要</challenge_code_handler>
    <challenge_code>用户需要输入的challenge_code</challenge_code>
    <expire_time>几秒后过期</expire_time>
</response>
```

## 回档交易

POST https://$ip:$port/trade/rollback

```
<request>
    <auth_id></auth_id>
    <trade_id></trade_id>
</request>
```

成功返回

 ```
<response>
    <result>ok</result>
</response>

```

可能的失败码
* 545: 交易未找到
* 546: 交易类型不正确


## 导入交易信息
POST https://$ip:$port/trade/batch_xls

```
<request>
    <auth_id></auth_id>
    <NewDataSet>
        <Table>
            <F1>行号</F1>
            <F2>卡号</F2>
            <F3>会员姓名</F3>
            <F4>消费金额累计</F4>
            <F5>积分累计</F5>
            <F6>性别</F6>
            <F7>电话</F7>
            <F8>手机</F8>
            <F9>地址</F9>
        </Table>
    </NewDataSet>
</request>
```

## 会员充值

POST https://$ip:$port/trade/recharge

```
<request>
    <auth_id></auth_id>
    <amount>充值数额</amount>
    <password>会员密码，如果是积分交易， 需要填写</password>
    <card_num>积分卡卡号</card_num>
</request>
```
成功返回

 ```
<response>
    <result>ok</result>
</response>

```

可能的错误

* 514: 积分不足
* 528: 积分卡卡号不正确
* 531: 用户已经被禁用
* 1024: 密码不正确
* 900: 商家积分不足

## 绑定JPUSH RegistrationID
POST https://$ip:$port/user/add_jpush_id

```
<request>
    <auth_id></auth_id>
    <jpush_id></jpush_id>  # 则是 JPush 提供的另外一套 RegistrationID 机制
    <platform>1</platform> # 1表示android, 2表示ios
</request>
```


成功返回

 ```
<response>
    <result>ok</result>
</response>

```

## 商家申请活动报名
POST https://$ip:$port/activity/apply

```
<request>
    <auth_id></auth_id>
    <large_than></large_than> #会员消费满足>某个金额时
    <point></point> #赠送积分
    <start_time>起始时间 (2014-02-12 00:00:00)</start_time>
    <end_time>起始时间 (2014-02-12 00:00:00)</end_time>
    <title>标题</title>
    <desc>描述</desc>
</request>
```

成功返回

 ```
<response>
    <result>ok</result>
</response>

```

## 获取当前提交的活动申请
POST https://$ip:$port/activity/get
总管理和商家2个角色可以获取

```
<request>
    <auth_id></auth_id>
    <state></state> # 0: 全部, 1: 申请中， 2: 用户取消, 3: 总管理拒绝, 4: 已过期. 5: 总管理同意
    <user></user> # 如果是总管理获取，则可按商家名user进行搜索
    <offset>起始offset, 分页使用</offset>
    <limit>limit, 每页个数</limit>
</request>
```

成功返回

```
<response>
    <result>ok</result>
    <count>个数</count>
    <activities>
        <activity>
            <id>活动id</id>
            <user_id>用户id</user_id>
            <user>用户名</user>
            <company>公司名</company>
            <state></state>
            <large_than></large_than> #会员消费满足>某个金额时
            <point></point> #赠送积分
            <start_time>起始时间 (2014-02-12 00:00:00)</start_time>
            <end_time>起始时间 (2014-02-12 00:00:00)</end_time>
            <title>标题</title>
            <desc>描述</desc>
        </activity>
    </activities>
</response>
```

## 对活动进行操作
POST https://$ip:$port/activity/change
总管理和商家2个角色可以操作

```
<request>
    <auth_id></auth_id>
    <id></id> #活动id
    <action></action> # 2： 用户取消, 3：总管理拒绝, 5：总管理同意
    <large_than></large_than> #会员消费满足>某个金额时， 如果action是5时，可以让总管理修改这个字段
    <point></point> #赠送积分, 如果action是5时，可以让总管理修改这个字段
    <start_time>起始时间 (2014-02-12 00:00:00)</start_time> #如果action是5时，可以让总管理修改这个字段
    <end_time>起始时间 (2014-02-12 00:00:00)</end_time> #如果action是5时，可以让总管理修改这个字段
    <title>标题</title> #如果action是5时，可以让总管理修改这个字段
    <desc>描述</desc> # 如果action是5时，可以让总管理修改这个字段
</request>
```

成功返回

```
<response>
    <result>ok</result>
</response>
```


## 商家往积分池充积分或者提取积分

POST https://$ip:$port/promotion/pool/change

```
<request>
    <auth_id></auth_id>
    <point></point> # 充值或提取的积分数额
    <action></action> 1或者2， 1为充值， 2为提取
</request>
```

成功返回

 ```
<response>
    <result>ok</result>
    <pool_point>变更后积分池积分</pool_point>
</response>

```

可能的错误码
* 910: 用户积分不足
* 911: 积分池积分不足，无法提取


## 商家申请导流积分券活动

POST https://$ip:$port/promotion/apply

```
<request>
    <auth_id></auth_id>
    <point></point> #赠送积分券的积分
    <start_time>起始时间 (2014-02-12 00:00:00)</start_time>
    <end_time>起始时间 (2014-02-12 00:00:00)</end_time>
    <title>标题</title>
    <desc>描述</desc>
</request>
```

成功返回

 ```
<response>
    <result>ok</result>
</response>

```

可能的错误码

* 900: 积分池积分不足
* 901: 已经有正在进行或者待批准的导流活动

## 获取导流列表

POST https://$ip:$port/promotion/get 总管理和商家2个角色可以获取

```
<request>
    <auth_id></auth_id>
    <state></state> # 0: 全部, 1: 申请中， 2: 用户取消, 3: 总管理拒绝, 4: 已过期. 5: 总管理同意
    <user></user> # 如果是总管理获取，则可按商家名user进行搜索
    <offset>起始offset, 分页使用</offset>
    <limit>limit, 每页个数</limit>
</request>
```

成功返回

```
<response>
    <result>ok</result>
    <count>个数</count>
    <items>
        <item>
            <id>活动id</id>
            <user_id>用户id</user_id>
            <user>用户名</user>
            <company>公司名</company>
            <pool_point></pool_point> #积分池积分
            <state></state>
            <point></point> #赠送积分券的积分
            <company_point></company_point> #商家（用户在该商家消费，获得积分券）获得的积分
            <admin_point></admin_point> #总管理获得的积分
            <multiple></multiple> 倍数
            <point_left></point_left> #该次活动还剩余多少积分
            <pool_point></pool_point> #商家积分池积分
            <start_time>起始时间 (2014-02-12 00:00:00)</start_time>
            <end_time>起始时间 (2014-02-12 00:00:00)</end_time>
            <title>标题</title>
            <desc>描述</desc>
        </item>
    </item>
</response>
```

## 对导流申请进行操作

POST https://$ip:$port/promotion/change 总管理和商家2个角色可以获取

```
<request>
    <auth_id></auth_id>
    <id></id> #活动id
    <action></action> # 2： 用户取消, 3：总管理拒绝, 5：总管理同意
    <company_point></company_point> #商家（用户在该商家消费，获得积分券）获得的积分, action是5的时候需要填写
    <admin_point></admin_point> #总管理获得的积分, action是5的时候需要填写
    <multiple></multiple> 倍数， action是5的时候需要填写
    <start_time>起始时间 (2014-02-12 00:00:00)</start_time> #如果action是5时，可以让总管理修改这个字段
    <end_time>起始时间 (2014-02-12 00:00:00)</end_time> #如果action是5时，可以让总管理修改这个字段
    <title>标题</title> #如果action是5时，可以让总管理修改这个字段
    <desc>描述</desc> # 如果action是5时，可以让总管理修改这个字段
</request>
```

成功返回

```
<response>
    <result>ok</result>
</response>
```

## 用户获取当前的积分券

POST https://$ip:$port/promotion/tickets 仅用户可以获取

```
<request>
    <auth_id></auth_id>
    <state></state> # 0: 全部, 1: 已使用， 2: 未使用， 3: 已过期
    <offset>起始offset, 分页使用</offset>
    <limit>limit, 每页个数</limit>
</request>
```

成功返回

```
<response>
    <result>ok</result>
    <count>个数</count>
    <items>
        <item>
            <id>活动券id</id>
            <promotion_id>对应优惠活动id</promotion_id>
            <user_id>用户id</user_id>
            <user>用户名</user>
            <company>公司名</company>
            <state></state>
            <point></point> #赠送积分券的积分
            <expired_by_pool_point>0|1</expired_by_pool_point>#因为积分池用光而过期
            <start_time>起始时间 (2014-02-12 00:00:00)</start_time>
            <end_time>起始时间 (2014-02-12 00:00:00)</end_time>
            <use_time>(2014-02-12 00:00:00)</use_time> #积分券使用时间，当state = 1的时候值才有意义
        </item>
    </item>
</response>
```
