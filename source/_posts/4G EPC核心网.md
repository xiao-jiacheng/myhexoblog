---
title: 4G EPC核心网
author: Chaos
date: 2024/1/30
---

记录一点4g学习内容

<!-- more -->

### 终端状态管理
终端状态是网络对终端行为进行正确处理的基础，主要体现在 EMM（EPS Mobility Management，EPS 移动性管理）和 ECM（EPS Connection Management，EPS 连接管理）这两个方面。关于终端状态的详细介绍请参见“3GPP TS 23.401”

#### EMM
- EMM-DEREGISTERED
该状态下，终端对EPC来说不可达。EMM上下文不包含终端的位置信息和路由信息终端的部分上下文可以保存在终端和 EPC 中，可以避免每次附着均发起 AKA（Authentication and Key Agreement，鉴权和密钥一致性）鉴权过程。

- EMM-REGISTERED
终端可以通过成功的附着流程或位置更新流程进入此状态，在该状态下，EPC 知道终端的确切位置或者终端所在的 TA List（Tracking Area List，跟踪区列表）。

#### ECM
- ECM-IDLE
终端与 EPC 没有 NAS（Non-Access Stratum，非接入层）信令连接时，终端进入 ECM-IDLE 态。此状态下的终端在 eNodeB 内没有上下文，也没有 S1-MME 和 S1-U 连接。
- ECM-CONNECTED
终端和 EPC 有 NAS 信令连接，此状态下 EPC 知道为终端提供服务的eNodeB 的 eNodeB ID。


### attach
附着流程，终端设备在安装SIM卡，接入到网络的第一个流程(手机飞行模式开启和关闭对应附着和区附着)。通过附着流程，终端可以注册到网络中，并获取网络提供的服务，与此同时，网络将获取终端所在的位置信息，即 TA List。附着流程会建立一个缺省的 EPS 承载(默认承载id为5)，该承载可为终端提供永久在线的 IP 连接。根据业务需求，附着流程可能触发一个或多个专有承载的建立。

- 附着流程
TODO: 补充信令图

### detach
UE不需要再附着在EPC网络时发起去附着流程。
分离流程的触发条件包括：
•终端触发，终端不再需要 EPC 网络提供的服务。(手机飞行模式)
•网络触发，网络通知终端不能在接入网络，具体可分为：
MME 发起的分离，MME 发现终端长时间没与网络联系，或因为其他维护原因需要对终端发起分离流程。
HSS 发起的分离，可能因为当前的运营商策略发生变化，终端取消合约或欠费，HSS 会通知 MME 删除终端的移动性上下文以及相关承载，触发 MME 执行分离过程。

#### 显式去附着
终端触发或网络触发的分离，并且触发一方能够通知到另一方。
#### 隐式去附着
网络触发的分离，并且网络侧无法通知到终端。
TODO

### TA List Management
TA List 管理功能主要包括 EPC（MME）对终端分配和管理 TA List。TA List 一般可以通过如下消息传递给终端：
•附着流程中的 Attach Accept 消息。
•TAU 流程中的 TAU Accept 消息。
2. 生成 TA List 方式
TA List 可由如下两种方式产生：
•静态配置，直接把 TA List 规划好，配置在 EPC（MME）中。
•动态生成，终端上报所在的 TA 后，EPC（MME）将与之相邻的 TA 动态加入到一个 TA List
中。
### tau (tracking area update)

#### TAU相关基本概念
- TA tracking area (跟踪区域)
无线网络的覆盖是由很多微小的单元构成，每个微小单元被称之为小区 Cell，这些小区 Cell 最终构成了覆盖绝大部分陆地的无线网络。跟踪区由多个小区 Cell 组成，用于管理终端的位置，EPC（MME）服务的范围则由多个 TA List 组成。在 EPS 网络中，位置管理的基本单位为 TA List。通过 TA List，可以避免终端频繁发起跟踪区更新流程。

- TA LIST
多个TAI组成，EPC网络位置管理中的基本单位。(mme 位置管理，更新ue归属TA LIST)
- TAI
由 MCC、MNC 和 TAC 组成，在全球范围内唯一标识一个跟踪区(TA)。

=============================================
|                 TAI                       |
---------------------------------------------
|   MCC     |  MNC    |      TAC            |

- MCC Mobile Country Code
移动国家码，标识一个国家  (460 中国)
- MNC Mobile Network Code
移动国家码，标识一个运营商 
- TAC Tracking Area Code
跟踪区码，标识跟踪区

#### TAU原因
由终端发起。跟踪区更新分为主动发起更新和被动发起更新，其中被动发起表明终端接受网络侧的指令后发起的跟踪区更新流程。
- 主动更新  
UE 处于ECM-IDLE态周期性向核心网报告位置；UE位置移动向核心网报告位置。
- 被动更新
接受到网络侧的指令后发起的TAU流程 (切换)  

对于 EPC 来说，跟踪区更新主要有如下触发条件：
•终端进入新的 TA，该 TA 不在当前的 TA List 中。
•周期性跟踪区更新，即 Periodic TAU。
•负载重分配。
•终端能力变更。

#### TAU类型
- UE 进入新的TA  (ta update)
终端附着成功后，EPC（MME）会为其分配一个 TA List。随后处于 ECM-IDLE 状态下的终端移动到一个新的 TA，该TA不在EPC为终端分配的TA List中，则终端会发起TAU流程。
- 周期性区域更新  (periodic update)
通过附着或跟踪区更新流程中，EPC（MME）可以将网络侧配置的 T3412 定时器通过 Attach Accept
或 TAU Accept 传递给终端，定时器超时后，处于 ECM-IDLE 状态下的终端将执行 TAU 流程。
当UE处于ECM-IDLE 状态下时，若网络侧配置 T3412计时器值不为0,定时器超时后，UE会发起TAU流程(nas ie update_type_value 区分ta update 和 periodic update)
- 负载分担
终端从一个被管理的MME 切换到另一个MME
- UE能力变更
如果终端的特定参数，或网络能力发生了变化，则处于 ECM-IDLE 状态下的终端需要发起 TAU 流程通知 EPC 更新终端的上下文。

#### TAU 流程
TODO: 补充流程图

### 服务请求 service request
服务请求用于将 ECM-IDLE 状态的终端迁移到 ECM-CONNECTED状态，并重新建立之前为终端保留的 S1 用户平面承载和无线承载。

- 服务请求流程通常由以下几种情况触发：
•ECM-IDLE 状态的终端响应网络侧的寻呼。(paging)
•网络侧对 ECM-IDLE 状态的终端发起信令流程。
•网络侧有下行的数据包到达，需要终端重建 S1-U 承载。
•终端有上行数据或信令需要发送。 (ue 主动发起的service request)

### 切换  handover
切换使用 Handover 表示，处于 ECM-CONNECTED 状态的终端在不同小区间发生移动时，源eNodeB 提供的信号越来越弱，目标 eNodeB 提供的信号越来越强，此时可以在目标 eNodeB 所在的 EPC 网络中为终端预先建立承载资源，这样在源 eNodeB 侧连接释放后，可以很快地在目标eNodeB 为终端建立连接，减少在目标网络申请资源的等待时间，这种目标侧预先准备资源的过程称为切换。
#### s1 切换
基于 S1 接口的切换，eNodeB 之间不存在 X2 接口或存在 X2 接口，但该接口不可用，所采用
的切换方式，具体可分为：
MME 不变，仅 SGW 和 eNodeB 之间改变的切换。
SGW 不变，仅 MME 和 eNodeB 之间改变的切换。
MME 和 SGW 不变，仅 eNodeB 之间改变的切换。
MME、SGW 和 eNodeB 同时发生改变的切换。

#### x2 切换
基于 X2 接口的切换，
控制平面信令在两个 eNodeB 之间直接通过 X2 接口传递，具体可分为：
 MME 和 SGW 不变，仅 eNodeB 之间改变的切换。
 MME 不变，SGW 和 eNodeB 同时发生变更的切换。


