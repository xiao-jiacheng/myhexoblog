---
title: 5G UE 鉴权流程  UE Authentication 
---

>名词解释  
 SUCI （Subscription Concealed Identifier）订阅隐藏标识符  
 SUPI  （Subscription Permanent Identifier）订阅永久标识符  
 SNN    （serving network name） 网络服务名  
 5G-GUTI(5G Globally Unique Temporary UE Identity ):5G全局唯一的临时UE标识

本文在学习UDM涉及UE认证相关服务，对UDM相关服务比较详细，其他部分可能比较粗略。

5G鉴权由AMF和AUSF共同完成
5G鉴权向量不支持预取，也不支持一次获取多组鉴权向量
#### SNN
SNN(Server Network Name)
5G SNN的作用：
- 用于推导锚点密钥，作为AUSF推到Kseaf的输入参数，以及作为UE推到RES*和XRES*的输入参数。
- 将锚点密钥和服务网路绑定
- 通过服务码设置为5g来抱枕锚点密钥用于5G核心网和UE之间的鉴权认证。

SNN最长1020个字节，由两部分构成：SNN-service-code和SNN-network-identifier，中间用冒号“:”进行连接。

由于在认证和鉴权过程中，安全密钥需要在UE和SEAF（位于AMF中）中分别推导，因此，SNN作为密钥推导的输入参数，需要在UE和SEAF分别进行构造。
SNN-service-code：固定为“5G”。在通过non-3GPP接入时，也用于区分接入网络，作为Access Network Identity使用；
SNN-network-identifier：用于识别当前的移动网络。根据TS 24.501章节9.12.1规定，对于PLMN网络，该值为：mncXXX.mccXXX.3gppnetwork.org（都为小写字母）。

#### 鉴权流程的触发条件
与UE建立信令连接的过程都可以触发鉴权流程：
- UE使用SUCI发起注册流程
- AMF 没有 UE 的有效上下文
- Registration Request消息没有被完整性保护
- UE携带 5G-GUTI注册，但old AMF对注册请求消息执行完整性检查失败

### 鉴权流程
鉴权流程分为三个阶段：
- 请求阶段
- 响应阶段
- 鉴权确认和注册绑定部分

#### 请求阶段
Nausf_UEAuthentication_Authenticate Request
##### 消息方向1  AMF/SEAF -> AUSF
http方法：POST     url：{apiRoot}/nausf-auth/{v}/ue-authentications

AMF 根据 从UE 得到的SUCI（订阅隐藏标识符）或者从 old AMF中获得的SUPI，选择对该UE进行鉴权的AUSF，构造Nausf_UEAuthentication_Authenticate Request消息并发送给该AUSF。
AUSF收到请求消息后，会检查消息中的Serving Network Name是否得到了授权。如果检查失败，则返回：403 Forbidden，携带的原因值为：SERVING_NETWORK_NOT_AUTHORIZED。


##### 消息方向2  AUSF -> UDM
HTTP方法：POST
消息名称：Nudm_UEAuthentication_Get Request

该请求的资源URI为：
{apiRoot}/nudm-ueau/v1/{supiOrSuci}/security-information/ generate-auth-data

![鉴权数据请求](https://img-blog.csdnimg.cn/16272604ed7643cb89f7314c35733e0a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiA5a2Z5oKf56m6,size_20,color_FFFFFF,t_70,g_se,x_16)
该消息用于AUSF请求UDM为UE选择一种鉴权方法，并计算新的鉴权向量（如果该鉴权需要鉴权向量的话）。如果请求中包含的是SUCI，UDM收到该请求后，首先，SIDF将SUCI解密为SUPI，之后根据SUPI为UE选择鉴权方式。目前，5G使用的鉴权方式有两种：EAP-AKA 和5G AKA。

#### 响应阶段
##### UDM生成鉴权向量
HRES    Hash RESponse
HXRES    Hash eXpected RESponse
IKE    Internet Key Exchange
KSI    Key Set Identifier

- UDM/ARPF生成鉴权向量
UDM/ARPF在生成鉴权向量时，需要将Authentication Management Field (AMF)的separation bit设置为"1"。separation bit为AUTN中AMF字段的第0比特，该值为1标识生成的鉴权向量用于EPS/5G AKA鉴权，对于鉴权向量来讲，如果设置为1，AKS鉴权过程中产生的CK和IK鉴权密钥不会离开HSS。 UDM/ARPF推导出 KAUSF并计算XRES*（在推导Kausf和XRES*时都会将serving network name作为输入参数使用）。最后UDM/ARPF生成的5G HE AV包含RAND、AUTN、XRES*、KAUSF四个参数。（其中鉴权令牌AUTN = SQN Å AK || AMF || MAC）

##### AUSF获得鉴权信息回应
消息方向：UDM - >AUSF
![鉴权消息回应](https://img-blog.csdnimg.cn/5e087efae9df45628b73e39981b5b286.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiA5a2Z5oKf56m6,size_20,color_FFFFFF,t_70,g_se,x_16)

UDM对请求阶段AUSF的请求做出回应
UDM成功响应AUSF，返回200 OK响应。UDM计算出5G HE AV（RAND、AUTN、XRES*、KAUSF），并在响应消息中返回给AUSF。携带AuthenticationInfoResult内容。


(1).UDM

##### AUSF保存XRES*

AUSF临时保存从响应消息中的XRES*和SUPI，用于归属地的鉴权比较。
##### AUSF计算XRES*

AUSF根据接收到的5G HE AV（RAND、AUTN、XRES*、KAUSF）来计算5G AV（RAND、AUTN、HXRES*、KSEAF）；根据XRES*计算出HXRES*；根据KAUSF计算出KSEAF（计算KSEAF仍然需要serving network name作为输入参数）。锚点密钥Kseaf此时诞生。
##### 消息方向：AUSF -> AMF/SEAF

消息名称：Nausf_UEAuthentication_Authenticate Response

AUSF 删除KSEAF，并在Nausf_UEAuthentication_Authenticate Response消息中向AMF返回5G SE AV (RAND, AUTN, HXRES*) 。

从上述可见，AUSF产生的5G AV并没有直接发送给AMF，而是分两步发送：第一步，发送5G SE AV用于拜访地鉴权；第二步，如果拜访地鉴权成功，返回RES*归属地鉴权成功后，将锚点密钥Kseaf发送给AMF，此时才将完整的5G AV发送给了AMF。
##### 消息方向：AMF/SEAF -> UE

消息名称：Authentication Request（NAS消息）

AMF/SEAF保存接收到的HXRES*，并向UE发送Authentication Request消息，启动T3560计时器，消息中包含RAND、AUTN、ngKSI、ABBA等参数。ME转发接收到的Authentication Request消息中的RAND和AUTN 给USIM。

5G AKA流程都是由网络发起的，UE可以拒绝网络发起的鉴权请求。
##### UE计算RES*

USIM收到RAND和AUTN，需要先检查AUTN中的MAC区域，验证接收的AUTN新鲜度。验证通过后，USIM计算RES，并和CK、IK一起返回给ME。如果USIM 根据CK和IK计算出了Kc (如GPRS Kc) 也发给了ME，则ME忽略该值，在USIM和ME上都不保存该值。之后，ME根据收到的RES计算RES*（serving network name、RAND等作为输入参数）。ME根据CK||IK 、serving network  name 及AUTN中的SQN Å  AK 计算出KAUSF，进而计算出 KSEAF （serving network name作为输入参数）。最后，ME检查AUTN的AMF域的"separation bit"是否为1。

##### 消息方向： UE -> AMF/SEAF

消息名称：Authentication Response（NAS消息）

UE构造Authentication Response消息发送给AMF/SEAF。AMF/SEAF收到Authentication Response消息后停止T3560计时器。

Authentication Response消息包含在Uu接口RRC层的ULInformationTransfer消息和N2接口的Uplink NAS Transport消息中发送给AMF/SEAF。
##### AMF/SEAF计算HRES*

AMF/SEAF根据UE发送的RES*计算出HRES* （输入参数有RES*、RAND等），SEAF比较HRES*和HXRES*，如果二者一致，则认为UE在当前的拜访网络鉴权成功。之后，执行第10步，通过Nausf_UEAuthentication_Authenticate Request消息将RES*发送给AUSF进行归属地鉴权。

如果HRES*和HXRES*不一致，则认为UE在拜访网络鉴权失败，仍然会继续执行第10步，只是此时不携带RES*，而是携带空值，以通知AUSF在拜访地鉴权失败。

如果此时UE不可达，SEAF不会收到RES* ，则SEAF认为本次鉴权失败，并通知AUSF。
##### 消息方向： AMF/SEAF -> AUSF

HTTP方法：PUT（注意：EAP鉴权方式时本步骤为POST方法）

消息名称：Nausf_UEAuthentication_Authenticate Request

该请求消息调用AUSF的资源URI:

{apiRoot}/nausf-auth/v1/ue-authentications/{authCtxId}/5g-aka-confirmation

该URI在第5步的响应消息中_links字段携带，也可以由AMF自行构造。如果构造的不准确，则在返回响应消息：404 Not Found，携带原因值：CONTEXT_NOT_FOUND，表示AUSF没有找到对应的资源URI。

该消息携带的确认数据只有ConfirmationData，包含：RES*。但是该值也有为空的情况，表示通知AUSF拜访地鉴权失败。具体场景如下：

- UE不可达的情况，AMF不会收到RES*；

- 拜访地鉴权失败AMF比较HRES*和HXRES*不一致；

- AMF收到UE的authentication failure消息，如：synchronization failure或者MAC failure。

##### AUSF验证RES*

AUSF收到RES*后，首先验证5G AV是否过期。如果5G AV过期了，则认为归属网络鉴权失败。如果5G AV验证不超期，AUSF加密保存Kausf。之后，AUSF比较接收到的 RES*和保存的XRES*是否一致，如果一致，AUSF认为归属网络鉴权成功，并通知UDM鉴权结果，之后开启鉴权确认和注册绑定流程。

##### 消息方向： AUSF -> AMF/SEAF

消息名称：Nausf_UEAuthentication_Authenticate Response

如果AUSF鉴权成功，则向AMF/SEAF返回200 OK响应，携带ConfirmationDataResponse信息


#### 鉴权确认和注册绑定部分
该步骤主要用于防止AMF的虚假注册，如UE所处拜访网络没有的AMF，确发起了Nudm_UECM_Registration_Request，导致UE注册在了一个不存在的AMF上。

##### 消息方向：AUSF -> UDM

HTTP方法：POST

消息名称：Nudm_UEAuthentication_ResultConfirmation Request

该请求消息的资源URI如下：

{apiRoot}/nudm-ueau/v1/{supi}/auth-events
![鉴权确认](https://img-blog.csdnimg.cn/d745361a03e546759382565178b27a1e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiA5a2Z5oKf56m6,size_20,color_FFFFFF,t_70,g_se,x_16)
##### UDM保存AuthEvent相关信息

UDM保存UE的鉴权状态，如SUPI、鉴权结果、时间戳、serving network name等。

##### 消息方向：UDM -> AUSF

消息名称：Nudm_UEAuthentication_ResultConfirmation Response

成功响应
返回201 Created，消息体可以为空。如果不为空，则返回AuthEvent内容。HTTP消息头的Location字段包含创建的资源，URI如下：

{apiRoot}/nudm-ueau/v1/{supi}/auth-events/{authEventId}

失败响应
404 Not Found，原因值：USER_NOT_FOUND。
。
##### UDM对后续流程的鉴权

如果UDM收到了UE的后续流程，如：AMF发送的Nudm_UECM_Registration_Request消息，UDM会根据运营商策略执行相关检测和保护操作（详见TS 33.501，3GPP仅提供了建议操作）。



### UDM工作流程
#### AUSF -> UDM
见上文请求阶段ausf向UDM请求鉴权向量
请求内容：AuthenticationInfoRequest


|结构名: AuthenticationInfoRequest|||
|--|--|-- 
|字段名	|数据类型|	说明
|servingNetworkName|	String|	
resynchronizationInfo|	ResynchronizationInfo|	同步信息
suportedFeatures||		
ausfInstanceId	String	||Ausf Id

结构名:ResynchronizationInfo|||
--|--|--|
字段名|	数据类型	|说明
rand|	String	
auts|	String|	AUTS由UE计算得到


回复内容：AuthenticationInfoResult
|结构名: AuthenticationInfoResult|||
--|--|-- 
字段名|	数据类型	|说明
authtype	|AuthType enum	
authenticationVector|	AuthenticationVector|	鉴权向量
supi	|String supi|	若请求为suci，返回解密后的supi
suportedFeatures|		

在UE鉴权中UE请求包含同步信息，则根据携带的RAND回推seq信息，再计算mac与UE传过来的auts中的mac部分比较确认。认证通过则数据库同步UE中的seq。
AK是一个匿名密钥，用于隐藏序列号，因为后者可能会暴露用户的身份和位置。序列号的隐藏只是为了防止被动攻击。
 ![rand生成](https://img-blog.csdnimg.cn/7728374d624f495fb5aa928b18587fb6.png#pic_center)

序列号同步流程


#### 鉴权确认
![鉴权确认算法](https://img-blog.csdnimg.cn/731f232fb40d49d185f10b7fc068a01f.png#pic_center)

鉴权确认，AUSF向UDM发送Post请求，资源URI中包含SUPI，消息体仅包含AuthEvent参数。succees是UE认证的状态，成功UDM需要在UDR中保存Authevent信息。
