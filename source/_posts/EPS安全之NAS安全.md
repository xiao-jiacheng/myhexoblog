---
title: EPC安全之NAS安全
author: Chaos
date: 2024/3/22
---


MME中处理EPS安全上下文的原则，以及用于UE和MME之间的EPS NAS消息的安全保护的过程。安全保护包括EMM和ESM NAS消息的完整性保护和加密。

### NAS count 和 NAS sequence number
每个EPS安全上下文应与两个独立的计数器NAS COUNT相关联：一个与上行链路NAS相关消息和一个与下行链路NAS消息有关的消息。NAS计数计数器使用24位内部表示和 由UE和MME独立维护。NAS计数应构建为NAS序列号（8 最低有效位）与NAS溢出计数器（16个最高有效位）连接。 当NAS计数输入到NAS加密或NAS完整性算法时，应将其视为32位实体其应通过在最高有效位中用8个零填充24位内部表示来构造。


### 完整性保护和校验
integrity protection 完整性保护

- 发送者(sender)使用本地存储的NAS COUNT 作为完整性保护算法的输入。
- 接收者(receiver)使用接收消息中的 NAS sequence number 和 预估的NAS溢出计数器 构造NAS COUNT 去完整性验证。 # :< 不太明白
- 完整性保护应该囊括 NAS 消息8进制数组中的6到n位，比如序列数(sequence  number IE) 和 消息类型(NAS message IE).
- 服务请求(SERVICE REQUEST) 消息的完整性保护比较特殊 在TS24.301 9.9.3.28中描述.(待补充)
- 除了需要完整性保护的数据，完整性保护的运算还需要  the constant BEARER ID,DIRECTION bit, NAS COUNT and NAS integrity key  输入 
- 完整性保护验证成功后，接收器应更新其相应的本地存储NAS计数以及该NAS消息的估计NAS计数的值。
- 使用EIA0时，完整性验证不适用。