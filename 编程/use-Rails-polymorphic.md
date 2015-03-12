Title: Rails中polymorphic的使用
Date: 2014-09-17 08:01:07
Status: private


最近在帮客户做一款以学生为目标用户的[P2P借贷产品](http://wolaidai.com/)。在做资料维护Story时有这样一个需求，个人信息中有“居住地址”，父母居住地址，在联系人资料中也有联系人的居住地址，这些地址在维护时都是通过下拉列表选择省市区并填写街道地址，所以我们考虑把地址作为一个单独的model抽取出来。那么这里如何配置关系呢？比如现在有三个model，分别是Address，Profile，Liaison。我们先考虑Profile和Liaison中都只有一个Address的情况，这时我们有如下几种选择：
## has_one
Profile has_one Address
Liaison has_one Address
has_one会生成在附属model中持有主model的id，如果这样配置的话，生成的表结构如下：
这样显然不太好，随着需要Address的model增多，addresses表中的外键也越来越多。

## belongs_to
如果我们addresses表相对稳定，那么可以把让主model持有附属model的外键，配置如下：
Profile belongs_to Address
Liaison belongs_to Address
这样生成的表结构如下：

首先，代码是写给人看的，这种关系配置有违人的常识，会让人非常费解，估计每个看到这段代码的人都会吐槽：一个Profile怎么会belongs_to一个Address呢？

## polymorphic
经过研究，发现最好的方案是使用polymorphic，配置如下：
这里生成的表结构如下：

这里