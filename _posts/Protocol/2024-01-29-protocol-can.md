---
title: "Protocol: CAN"
description: 
date: 2024-01-29 10:00:00 +0800
categories: [Protocol, CAN]
tags: [Protocol, CAN]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## CAN 通讯
### CAN 是什么？
CAN 是 Controller Area Network 的缩写（以下称为 CAN），是ISO国际标准化的串行通信协议。

在当前的汽车产业中，出于对安全性、舒适性、方便性、低公害、低成本的要求，各种各样的电子控制系统被开发了出来。由于这些系统之间通信所用的数据类型及对可靠性的要求不尽相同，由多条总线构成的情况很多，线束的数量也随之增加。为适应“减少线束的数量”、“通过多个LAN，进行大量数据的高速通信”的需要，1986年德国电气商博世公司开发出面向汽车的CAN通信协议。此后，CAN通过 ISO11898 及 ISO11519 进行了标准化，现在在欧洲已是汽车网络的标准协议。

现在，CAN的高性能和可靠性已被认同，并被广泛地应用于工业自动化、船舶、医疗设备、工业设备等方面。

CAN总线通常采用屏蔽或非屏蔽的双绞线，总线接口能在极其恶劣的环境下工作。

### 总线拓扑图
CAN 控制器根据两根线上的电位差来判断总线电平。总线电平分为显性电平和隐性电平，二者必居其一。发送方通过使总线电平发生变化，将消息发送给接收方。
![输入图片说明](/imgs/protocol-can/2024-01-29/dZBjwO0x883xB4E7.png)
![输入图片说明](/imgs/protocol-can/2024-01-29/QLlZBrdk5v64Qu7c.png)

### CAN 的特点
CAN 协议具有以下特点  
1. 多主控制  
    在总线空闲时，所有的单元都可开始发送消息（多主控制）。  
    最先访问总线的单元可获得发送权。  
    多个单元同时开始发送时，发送高优先级 ID 消息的单元可获得发送权。  
    >- 安全敏感的应用（如汽车动力）对通信系统的可靠性要求很高。将总线能否正常工作归结到单一节点是非常危险的，比较合理的方案是对总线接入的去中心化，即每个节点都有接入总线的能力。这也是CAN总线采用多主控（Multi-Master）线性拓扑结构的原因。
    >- 在CAN总线上，每个节点都有往总线上发送消息的能力，而消息的发送不必遵从任何预先设定的时序，通信是事件驱动的。只有当有新的信息传递时，CAN总线才处于忙碌的状态，这使得节点接入总线速度非常快。CAN总线理论最高数据传输速率为1Mbps，对于异步事件反应迅速，基本对于ms级别的实时应用没有任何问题。

    >Tips: 总线空闲状态：当总线上的上出现连续的11位隐性电平，那么总线就处于空闲状态。也就是说对于任意一个节点而言，只要它监听到总线上连续出现了11位隐性电平，那么该节点就会认为总线当前处于空闲状态，它就会立即向总线上发送自己的报文。
    至于为什么连续出现11位隐性电平，就可以判定+总线处于空闲状态，可以结合CAN协议的帧结构来进行理解。

2. 消息的发送  
    在CAN协议中，所有的消息都以固定的格式发送。总线空闲时，所有与总线相连的单元都可以开始发送新消息。两个以上的单元同时开始发送消息时，根据标识符（Identifier 以下称为ID）决定优先级。ID并不是表示发送的目的地址，而是表示访问总线的消息的优先级。两个以上的单元同时开始发送消息时，对各消息ID的每个位进行逐个仲裁比较。仲裁获胜（被判定为优先级最高）的单元可继续发送消息，仲裁失利的单元则立刻停止发送而进行接收工作。
    >这种仲裁机制既**不会造成已发送数据的延迟**，也**不会破坏已经发送的数据**，所以称为非破坏性仲裁机制。
 
3. 系统的柔软性  
与总线相连的单元没有类似于“地址”的信息。因此在总线上增加单元时，连接在总线上的其它单元的软硬件及应用层都不需要改变。

4. 通信速度  
根据整个网络的规模，可设定适合的通信速度。在同一网络中，所有单元必须设定成统一的通信速度。即使有一个单元的通信速度与其它的不一样，此单元也会输出错误信号，妨碍整个网络的通信。不同网络间则可以有不同的通信速度。 
> 例如：汽车上一般有两条CAN总线：500kbps的驱动系统CAN总线和125kbps的舒适系统CAN总线，如果驱动系统CAN总线上的发动机节点要把自己的转速信息发送给舒适系统CAN总线上的转速表节点，那么这两条总线必须通过网关相连。
 
5. 远程数据请求  
可通过发送“遥控帧” 请求其他单元发送数据。 
 
6. 错误检测功能 · 错误通知功能 · 错误恢复功能  
所有的单元都可以检测错误（错误检测功能）。  
检测出错误的单元会立即同时通知其他所有单元（错误通知功能）。  
正在发送消息的单元一旦检测出错误，会强制结束当前的发送。强制结束发送的单元会不断反复地重新发送此消息直到成功发送为止（错误恢复功能）。 

7. 故障封闭  
CAN 可以判断出错误的类型是总线上暂时的数据错误（如外部噪声等）还是持续的数据错误（如单元内部故障、驱动器故障、断线等）。由此功能，当总线上发生持续数据错误时，可将引起此故障的单元从总线上隔离出去。 
 
8. 连接CAN总线时可同时连接多个单元的总线
可连接的单元总数理论上是没有限制的。但实际上可连接的单元数受总线上的时间延迟及电气负载的限制。
>降低通信速度，可连接的单元数增加；提高通信速度，则可连接的单元数减少。

### 错误
单元始终处于3种状态之一：
![输入图片说明](/imgs/protocol-can/2024-01-29/JJjMt25Qxc2cnDqK.png)

### CAN 协议的基本概念
CAN 协议如表3所示涵盖了ISO规定的OSI基本参照模型中的传输层、数据链路层及物理层。
![输入图片说明](/imgs/protocol-can/2024-01-29/t2wHPqagzMHjtueA.png)
数据链路层分为 MAC 子层和 LLC 子层，MAC 子层是 CAN 协议的核心部分。  
数据链路层的功能是将物理层收到的信号组织成有意义的消息，并提供传送错误控制等传输控制的流程。具体地说，就是消息的帧化、仲裁、应答、错误的检测或报告。  
数据链路层的功能通常在 CAN 控制器的硬件中执行。   

在物理层定义了信号实际的发送方式、位时序、位的编码方式及同步的步骤。但具体地说，信号电平、通信速度、采样点、驱动器和总线的电气特性、连接器的形态等均未定义。这些必须由用户根据系统需求自行确定。  
>【注】驱动器及总线的电气特性等在博世公司的 CAN 规格书中没有定义。但在 CAN 的 ISO 标准 （ISO11898、ISO11519-2 等）中分别定义了总线及驱动器的电气特性等。

![输入图片说明](/imgs/protocol-can/2024-01-31/Amics0DKeLzUD0zl.png)

### CAN 协议及标准规格
>在SPI通信中，片选、时钟信号、数据输入及数据输出这四个信号都有单独的信号线。而CAN使用的是两条差分信号线，只能表达一个信号。简洁的物理层决定了CAN必然要配上一套更为复杂的协议。

CAN 协议经 ISO 标准化后有 ISO11898 标准和 ISO11519-2 标准两种。ISO11898 和 ISO11519-2 标准对于数据 链路层的定义相同，但物理层不同。

1. 关于 ISO11898 ISO11898 是通信速度为 125kbps-1Mbps 的 CAN 高速通信标准。 目前，ISO11898 追加新规约后，成为 ISO11898-1 新标准。
 
2. 关于 ISO11519 ISO11519 是通信速度为 125kbps 以下的 CAN 低速通信标准。 ISO11519-2 是 ISO11519-1 追加新规约后的版本。 表4 表示 CAN 协议和 ISO11898 及 ISO11519-2 标准的范围。

![输入图片说明](/imgs/protocol-can/2024-01-29/8GKu7wzFmtM3VyYI.png)

CAN 收发器根据两根总线（CAN_High 和 CAN_Low）的电位差来判断总线电平。总线电平分为显性电平和隐性电平两种。总线必须处于两种电平之一。总线上执行逻辑上的线“与”时， 显性电平为“0”，隐性电平为“1”。物理层的特征如图 9 所示。
![输入图片说明](/imgs/protocol-can/2024-01-29/CoWW9ng49yXFDF2s.png)

高速CAN总线最高信号传输速率为1Mbps，支持最长距离40m。ISO11898-2要求在高速CAN总线两段安装**端接电阻**RL（端接电阻一般为120Ω，因为电缆的特性阻抗为120 Ω，为了模拟无限远的传输线。）以消除反射。低速CAN最高速度只有125Kbps，所以ISO11898-3没有端接要求。
![输入图片说明](/imgs/protocol-can/2024-01-29/6nLgypenloBe2Jme.png)

### CAN 协议
#### CAN总线发送总流程
![输入图片说明](/imgs/protocol-can/2024-01-31/4GJL8ngqzdcUyUAa.png)
#### 帧的种类
通信是通过以下 5 种类型的帧进行的。

- 数据帧
- 遥控帧
- 错误帧
- 过载帧
- 帧间隔

另外，数据帧和遥控帧有标准格式和扩展格式两种格式。
>标准格式有 11 个位的标识符（Identifier: 以下称 ID），扩展格式有 29 个位的 ID。

各种帧的用途如表所示，各种帧的构成如图所示

| 帧 | 帧用途 |
|--|--|
| 数据帧 | 用于发送单元向接收单元传送数据的帧。 |
| 遥控帧 | 用于接收单元向具有相同ID的发送单元请求数据的帧。 |
| 错误帧 | 用于当检测出错误时向其它单元通知错误的帧。 |
| 过载帧 | 用于接收单元通知其尚未做好接收准备的帧。 |
| 帧间隔 | 用于将数据帧及遥控帧与前面的帧分离开来的帧。 |

#### 数据帧
![输入图片说明](/imgs/protocol-can/2024-01-29/ZmOpUrKUSGz07f5F.png)
![输入图片说明](/imgs/protocol-can/2024-01-29/Zrpb8sqxWnUgxqNP.png)

数据帧由 7 个段构成。

1. 帧起始（标准、扩展格式相同）  
表示帧开始的段。1 个位的显性位（逻辑0）。
![输入图片说明](/imgs/protocol-can/2024-01-29/4wEUj37QsbwkEF5x.png)
![输入图片说明](/imgs/protocol-can/2024-01-31/8EiNkvnPn90g58vw.png)

2. 仲裁段  
    表示该帧优先级的段。  
    标准格式和扩展格式在此的构成有所不同。  
    >- 标准格式的 ID 有 11 个位。从 ID28 到 ID18 被依次发送。禁止高 7 位都为隐性。（禁止设定：ID=1111111XXXX）
    >- 扩展格式的 ID 有 29 个位。基本 ID 从 ID28 到 ID18，扩展 ID 由 ID17 到 ID0 表示。基本 ID 和标准格式的 ID 相同。禁止高 7 位都为隐性。（禁止设定：基本ID=1111111XXXX）

    ![输入图片说明](/imgs/protocol-can/2024-01-31/uP66ApevgkW30MiI.png)

    ![输入图片说明](/imgs/protocol-can/2024-01-29/D4t6eSXOKNbWmeph.png)

    ![输入图片说明](/imgs/protocol-can/2024-01-30/QPcTEgAqcFSLTPCf.png)

    >**仲裁段中的RTR，SRR和IDE位**：  
    >通过上面标准数据帧的仲裁过程分析，我们已经理解了CAN总线的仲裁机制。但同时也注意到仲裁段除了ID号之外，还有其他的位。
    >1. RTR位  
    >Remote Tranmission Request Bit （远程发送请求位）。  
    >**在数据帧中，RTR位恒为显性位0，在遥控帧中，恒为隐性1**。  
    >> Tips: 这么做的原因是保证数据帧优先级高于遥控帧。比如：在某一时刻t，节点Node_A发出了ID号为ID_2遥控帧报文来请求总线上的其它节点发出ID号为ID_2的数据帧报文。但是就在同一时刻t，节点Node_B发出了ID号为ID_2的数据帧报文。这个时候怎么办呢，显然依靠ID号不能仲裁出这两帧报文（一个遥控帧，一个数据帧，ID号相同）谁能占据总线的发送权，这种情况下，RTR位就起作用了，由于RTR在数据帧中恒为显性0，在遥控帧中恒为隐性1，所以在ID号相同的情况下，一定是数据帧仲裁获胜。这就解释了 **RTR位的作用：在ID号相同的情况下，保证数据帧的优先级高于遥控帧**；
    >2. SRR位  
    >Substitutes for Remote Requests Bit（替代远程请求位）
    >**在扩展帧（数据帧或遥控帧）中，SRR恒为隐性位1**，并且可以发现，扩展帧的隐性SRR位正好对应标准帧的显性RTR位，这就解释了 **SRR位的作用：在前11位ID号相同的情况下，标准数据帧的优先级高于扩展数据帧**；
    >3. IDE位  
    >Identifier Extension Bit（标识符扩展位）  
    >**在扩展帧中恒为隐性1，在标准帧中，IDE位于控制段，且恒为显性0**，且扩展帧IDE位和标准帧IDE位位置对应，这就保证了： **在前11位ID号相同的情况下，标准遥控帧的优先级一定高于扩展遥控帧**；
    >
    >**总结：** 在ID号前11位相同的情况下：
    - RTR： 保证数据帧优先级高于遥控帧
    - SRR： 保证标准数据帧的优先级高于扩展数据帧
    - IDE： 保证标准遥控帧的优先级高于扩展遥控帧
    ![输入图片说明](/imgs/protocol-can/2024-01-31/QHiz1o6zAobXgkeW.png)
    ![输入图片说明](/imgs/protocol-can/2024-01-31/Ax14TlENeeDaYHk3.png)

    ID是赋给帧的，不是直接给节点的，只是某节点知道自己要接收某个ID的帧。对总线上的节点来说它只管取总线上他应该取的ID的帧，并不管是谁发的。 即使是远程帧，发出请求的节点在获得响应时也仅仅是根据帧ID判定，并不影响其他节点的接收。

    我们要从总线的角度去看待CAN，不要从节点的角度出发。

    ID的另一个作用是配合接收方滤波使用，就是说一般接收的滤波器可以设定接收ID的范围等，用于过滤掉不需要接收的信息，减轻CPU的处理负担。

3. 控制段  
表示数据的字节数及保留位的段。  
控制段由 6 个位构成，表示数据段的字节数。标准格式和扩展格式的构成有所不同。  
>保留位（r0、r1）  
保留位必须全部以显性电平发送。但接收方可以接收显性、隐性及其任意组合的电平。
![输入图片说明](/imgs/protocol-can/2024-01-29/ey5MUUBXjrVE6s5l.png)

4. 数据段（标准、扩展格式相同）  
数据段可包含 0～8 个字节的数据。从 MSB（最高位）开始输出。  
![输入图片说明](/imgs/protocol-can/2024-01-29/Y2SHOnVJx4E8WZ40.png)

5. CRC 段（标准/扩展格式相同）  
检查帧的传输错误的段。15 个位的 CRC 顺序和 1 个位的 CRC 界定符（用于分隔的位，恒为隐性1）构成。  
>CRC 顺序是根据多项式生成的 CRC 值，CRC 的计算范围包括帧起始、仲裁段、控制段、数据段。  
>
![输入图片说明](/imgs/protocol-can/2024-01-29/Hcr3YguD3yirtmmq.png)
![输入图片说明](/imgs/protocol-can/2024-01-31/Y5qa3RBru01lFsmY.png)

6. ACK 段  
    ACK 段用来确认是否正常接收。由 ACK 槽(ACK Slot)和 ACK 界定符 2 个位构成。  
    ![输入图片说明](/imgs/protocol-can/2024-01-29/cIE4madrymOdwtmG.png)  
    >- 发送单元的 ACK 段: 发送单元在 ACK 段发送 两个隐性位，即发送方发出的报文中ACK槽为隐性1。  
    >- 接收单元的 ACK 段: 接收到正确消息的单元在 ACK 槽(ACK Slot)发送显性位0，通知发送单元正常接收结束。这称作“发送 ACK”或者“返回 ACK”。  

    我们以标准数据帧为例来分析**ACK段的工作方式**：  
    如图所示，Node_A为发送节点，Node_B为接收节点。Node_A在ACK段发送两个隐性位1。Node_B正确接收到这一报文后，在ACK段的ACK槽中填充了一个显性位0。注意，这个时候**Node_A回读到的总线上的电平为显性0**，于是这个时候，Node_A就知道自己发出去的报文至少有一个节点正确接收了。
    ![输入图片说明](/imgs/protocol-can/2024-01-30/9rUQ90FBKm60sYMK.png)
    ![输入图片说明](/imgs/protocol-can/2024-01-31/7vasU3h2R2MWO9sr.png)

7. 帧结束  
帧结束是表示该该帧的结束的段。由 7 个位的隐性位（逻辑1）构成。  
![输入图片说明](/imgs/protocol-can/2024-01-29/kTRkfnrTP9t13g3q.png)
>显性电平和隐性电平:  
>
>总线上的电平有显性电平和隐性电平两种。总线上执行逻辑上的线“与”时，显性电平的逻辑值为“0”，隐性电平为“1”。“显性”具有“优先”的意味，只要有一个单元输出显性电平，总线上即为显性电平。并且，“隐性”具有“包容”的意味，只有所有的单元都输出隐性电平，总线上才为隐性电平。（显性电平比隐性电平更强。）

#### 遥控帧
接收单元向发送单元请求发送数据所用的帧。遥控帧由 6 个段组成。遥控帧没有数据帧的数据段。
![输入图片说明](/imgs/protocol-can/2024-01-29/JUOMY7RkMtsU6qPr.png)

>比如：在某一个时刻，节点Node_A向总线发送了一个ID号为ID_2的遥控帧，那么就意味着Node_A请求总线上的其他节点发送ID号为ID_2的数据帧。  
节点Node_B能够发出ID号为ID_2的数据帧，那么Node_B就会在收到Node_A发出的遥控帧之后，立刻向总线上发送ID号为ID_2的数据帧。

>遥控帧和数据帧  
>- 数据帧和遥控帧的不同:  
遥控帧的 RTR 位为隐性位，没有数据段。  
没有数据段的数据帧和遥控帧可通过 RTR 位区别开来。  
>- 遥控帧没有数据段，数据长度码该如何表示？  
遥控帧的数据长度码以所请求数据帧的数据长度码表示。  
>- 没有数据段的数据帧有何用途？  
例如，可用于各单元的定期连接确认/应答、或仲裁段本身带有实质性信息的情况下。  

#### 错误帧
用于在接收和发送消息时检测出错误通知错误的帧。错误帧由错误标志和错误界定符构成。  
>错误标志  
错误标志包括主动错误标志和被动错误标志两种。  
>- 主动错误标志：6 个位的显性位。  
>- 被动错误标志：6 个位的隐性位。  
>![输入图片说明](/imgs/protocol-can/2024-01-29/FfEd7bj4b4HfjGuB.png)

#### 过载帧
过载帧是用于接收单元通知其尚未完成接收准备的帧。过载帧由过载标志和过载界定符构成。  
![输入图片说明](/imgs/protocol-can/2024-01-29/IgXEoFu0cRgiJwaO.png)

过载帧是**接收节点**向总线上其它节点报告自身**接收能力达到极限**的帧。  
>上面这句话可以这样理解：接收节点Node_A接收报文的能力达到极限了，于是Node_A就会发出过载帧来告诉总线上的其它节点（包括发送节点），我接收节点Node_A已经没有能力处理你们发来的报文了。

#### 帧间隔
帧间隔是用于分隔数据帧和遥控帧的帧。数据帧和遥控帧可通过插入帧间隔将本帧与前面的任何帧（数据帧、遥控帧、错误帧、过载帧）分开。

过载帧和错误帧前不能插入帧间隔。  
![输入图片说明](/imgs/protocol-can/2024-01-29/bO9BWpB9ssoBlWAM.png)
1. 间隔  
3 个位的隐性位。
2. 总线空闲  
隐性电平，无长度限制（0 亦可）。  
本状态下，可视为总线空闲，要发送的单元可开始访问总线。  
3.  延迟传送（发送暂时停止）  
8 个位的隐性位。  
只在处于被动错误状态的单元刚发送一个消息后的帧间隔中包含的段。  

帧间隔过后，如果无节点发送帧，则总线进入空闲。如果被动错误节点要发送帧，则先发送8个隐性电平的传输延迟，再发送帧。  
![输入图片说明](/imgs/protocol-can/2024-01-31/PevMYXc6NTSWPCLj.png)![](https://img-blog.csdn.net/20170122155021145)

#### 优先级的决定
在总线空闲态，最先开始发送消息的单元获得发送权。

多个单元同时开始发送时，各发送单元从仲裁段的第一位开始进行仲裁。连续输出显性电平最多的单元可继续发送。
![输入图片说明](/imgs/protocol-can/2024-01-29/EcFp6Pl0pvmlFGpn.png)
1. 数据帧和遥控帧的优先级  
具有相同 ID 的数据帧和遥控帧在总线上竞争时，仲裁段的最后一位（RTR）为显性位的数据帧具有优先权，可继续发送。
![输入图片说明](/imgs/protocol-can/2024-01-29/yTPcWpOON9X6sPRC.png)
2. 标准格式和扩展格式的优先级  
标准格式 ID 与具有相同 ID 的遥控帧或者扩展格式的数据帧在总线上竞争时，标准格式的 RTR 位为显性位的具有优先权，可继续发送。
![输入图片说明](/imgs/protocol-can/2024-01-29/nawNZiajo2Vo7MZU.png)

#### 位填充
位填充是为防止突发错误而设定的功能。当同样的电平持续 5 位时则添加一个位的反型数据。
![输入图片说明](/imgs/protocol-can/2024-01-29/lxyaPKjSltN5uGbu.png)
1. 发送单元的工作  
在发送数据帧和遥控帧时，SOF～CRC 段间的数据，相同电平如果持续 5 位，在下一个位（第 6 个位）则要插入 1 位与前 5 位反型的电平。

2. 接收单元的工作  
在接收数据帧和遥控帧时，SOF～CRC 段间的数据，相同电平如果持续 5 位，需要删除下一个位（第 6 个位）再接收。如果这个第 6 个位的电平与前 5 位相同，将被视为错误并发送错误帧。

#### 位时序
>由发送单元在非同步的情况下发送的每秒钟的位数称为位速率。 
>>**位速率**:又叫做**比特率（bit rata）**、**信息传输率**，表示的是单位时间内，总线上传输的**信息量**，即**每秒能够传输的二进制位的数量**，单位是bit per second

>CAN总线系统中的两个时钟： **晶振时钟周期**和 **CAN时钟周期**：
- **晶振时钟周期**：是由单片机振荡器的晶振频率决定的，指的是**振荡器每震荡一次所消耗的时间长度**，也是整个系统中最小的时间单位。
- **CAN时钟周期**：CAN时钟是由系统时钟分频而来的一个时间长度值，实际上就是**一个时间份额Tq**。可以按照下面的公式计算：CAN时钟周期=2×晶振时钟周期×BRP，其中BRP叫做波特率预分频值（baudrate prescaler）  
![输入图片说明](/imgs/protocol-can/2024-01-30/qptt7m3iiCLbsnky.png)
如上文所述，在CAN的位定时中，一个CAN时钟周期称为一个**时间量子 Tq**。  

如下图所示：位时间分为四个段：**同步段、传播时间段、相位缓冲段1、相位缓冲段2**，总共8~25个时间量子（Tq）。
![输入图片说明](/imgs/protocol-can/2024-01-30/CGcMyFaFXmO5aQjn.png)

一个位可分为4段，每个段又由可称为 Time Quantum（以下称为 Tq）的最小时间单位构成，这称为位时序。    

1. **同步段**（Synchronization Segment）：
-   长度固定，1个时间量子Tq；
-   一个位的传输从同步段开始；
-   同步段用于同步总线上的各个节点，一个位的跳边沿在此时间段内。

2. **传播时间段**（Propagation Time Segment）：
-   传播段用于补偿报文在总线和节点上传输时所产生的时间延迟；
-   传播段时长 ≥ 2 × 报文在总线和节点上传输时产生的时间延迟 ；
-   传播段时长可编程（1~8个时间量子Tq）。

3. **相位缓冲段1**（Phase Buffer Segment1）：
-   用于补偿节点间的晶振误差；
-   允许通过重同步对该段加长；
-   在这个时间段的末端进行总线状态的采样；
-   长度可编程（1~8个时间量子Tq）

4. **相位缓冲段2**（Phase Buffer Segment2）：
-   用于补偿节点间的晶振误差；
-   允许通过重同步对该段缩短；
-   长度可编程（1~8个时间量子Tq）

![输入图片说明](/imgs/protocol-can/2024-01-29/NUvgmOTxp2whlhgk.png)
![输入图片说明](/imgs/protocol-can/2024-01-29/MK3zJF2GhDhgqUO3.png)

#### 取得同步的方法
CAN 协议的通信方法为 NRZ（Non-Return to Zero）方式。各个位的开头或者结尾都没有附加同步信号。发送 单元以与位时序同步的方式开始发送数据。另外，接收单元根据总线上电平的变化进行同步并进行接收工作。 

但是，发送单元和接收单元存在的时钟频率误差及传输路径上的（电缆、驱动器等）相位延迟会引起同步偏 差。因此接收单元通过硬件同步或者再同步的方法调整时序进行接收。

在CAN通信中，有两种同步机制：**硬同步**与**重同步**。

同步的规则：
- **一个位时间**内只允许**一种同步方式**，要么硬同步要么重同步；  
- 任何一个从“隐性”到“显性”的**下降沿** 都可以用于同步；  
- **硬同步**发生在报文的**SOF位**，所有接收节点调整各自当前位的同步段，使其位于发送的SOF位内；  
- **重同步**发生在一个报文**SOF位之外的其它段**，当下降沿落在了同步段之外时发生重同步；  
- 在SOF到仲裁场发送的时间段内，如果有多个节点同时发送报文，那么这些发送节点对跳变沿不进行重同步

#### 硬件同步
接收单元在总线空闲状态检测出帧起始时进行的同步调整。  
在检测出边沿的地方不考虑 SJW 的值而认为是 SS 段。  
![输入图片说明](/imgs/protocol-can/2024-01-29/mh838XxcbpBrV1Ob.png)

硬同步发生在**SOF位**，所有**接收节点**调整各自**当前位的同步段**，**调整宽度不限**  
![输入图片说明](/imgs/protocol-can/2024-01-30/3kKbUBu7LqQPVRvm.png)
1. 发送节点Node_A在发送SOF位时，SOF位的下降沿在SS段；  
2. 这个时候接收节点Node_B发现自己当前位的SS段和发送节点SOF位的SS段不同步。也就是说当Node_A产生SOF位SS段时，Node_B的当前位的SS段已经在5个Tq之前产生了；  
3. 于是接收节点Node_B强行将自己当前位的SS段拉到与SOF位的SS段同步。

#### 再同步/重同步
在接收过程中检测出总线上的电平变化时进行的同步调整。

重同步发生在一个报文SOF位之外的其它位场内，当接收节点Node_B当前位的下降沿落在了发送节点Node_A当前位的同步段之外时发生重同步。  
重同步会导致**相位缓冲段1的延长**或者**相位缓冲段2的缩短**，从而保证采样点的准确。

每当检测出边沿时，根据 SJW 值通过加长 PBS1 段，或缩短 PBS2 段，以调整同步。但如果发生了超出 SJW值的误差时，最大调整量不能超过 SJW 值。
>在重同步时，有个**同步跳转宽度**（SJW，Synchro Jump Width）的概念，表示的是**PBS1和PBS2重同步时允许跳转的最大宽度**。  
>同步跳转宽度必须满足以下几个条件：
>- SJW必须小于PBS1和PBS2的最小值
>- SJW最大值不能超过4


![输入图片说明](/imgs/protocol-can/2024-01-29/sVwSjO2jDfU7eFhT.png)

重新同步：前面的硬同步只是当存在帧起始信号时才起作用，如果在一帧很长的数据内，节点信号与总线信号相位有偏移时，这种同步方式就无能为力了。因而引入重新同步方式，它利用普通数据位的高至低电平的跳变沿来同步。与硬同步方式相似的是它们都使用 SS 段来进行检测，同步的目的都是使节点内的 SS 段把跳变沿包含起来。重新同步分为超前和滞后两种情况，以总线跳变沿与 SS 段的相对位置进行区分。
- 相位超前：节点从总线的边沿跳变中，检测到它内部的时序比总线的时序相对超前 2Tq，这时控制器在下一个位时序中的 PBS1 段增加 2Tq 的时间长度，使得节点与总线时序重新同步。
- 相位滞后：节点从总线的边沿跳变中，检测到它的时序比总线的时序相对滞后 2Tq，这时控制器在前一个位时序中的 PBS2 段减少 2Tq 的时间长度，获得同步。

例1： PBS1延长
发的晚（慢），收的早（块），导致PBS1延长。
![输入图片说明](/imgs/protocol-can/2024-01-30/zgwcfgsRgnZ95qN7.png)
1. 发送节点Node_A比接收节点Node_B的时间慢了，也就是说Node_A当前位的ss段产生的时候，Node_B 当前位的ss段已经在2个Tq之前产生了；  
2. 所以这个时候接收节点Node_B就将PBS1延长2个Tq的时间；  
3. 于是这个时候Node_A当前位的采样点就和Node_B的采样点同步了。

例2：PBS2缩短
发的早（快），收的晚（慢），导致PBS2缩短。
![输入图片说明](/imgs/protocol-can/2024-01-30/9f99djSI3eThNyQz.png)
1. 发送节点Node_A当前位的SS段诞生2Tq时长之后，接收节点Node_B的当前位才产生SS段；  
2. 于是，接收节点Node_B当前位的PBS2段缩短，  
3. 这样就会导致接收节点Node_B的下一位能够提前2个Tq，从而Node_B的下一位采样点和Node_A下一位的采样点能够同步。

### Reference
1. [瑞萨-CAN入门书](https://d1.amobbs.com/bbs_upload782111/files_34/ourdev_592917X7VICE.pdf)
2. [# CAN总线终端电阻的作用？为什么是120Ω？为什么是0.25W？](https://mp.weixin.qq.com/s/Jd8ffrql5abHB5JdBB2Fpg)
3. [# CAN总线报文浅析](https://blog.csdn.net/liuligui5200/article/details/79030676)
4. [# CAN总线学习笔记（1）- CAN基础知识](https://blog.csdn.net/weixin_40528417/article/details/79476186?spm=1001.2014.3001.5502)
5. [# CAN总线学习笔记（2）- CAN协议数据帧与遥控帧 -- 转载](https://blog.csdn.net/hugh_shaw/article/details/113614768?spm=1001.2014.3001.5502)
6. [# CAN总线学习笔记（3）- CAN协议错误帧](https://blog.csdn.net/weixin_40528417/article/details/79771270?spm=1001.2014.3001.5502)
7. [# CAN总线学习笔记（4）- CAN协议过载帧和帧间隔 -- 转载](https://blog.csdn.net/hugh_shaw/article/details/113614938?spm=1001.2014.3001.5502)
8. [# CAN总线学习笔记（5）- CAN通信的位定时与同步 -- 转载](https://blog.csdn.net/hugh_shaw/article/details/113614971?spm=1001.2014.3001.5502)
9. [# CAN总线协议报文浅析](https://www.cnblogs.com/isAndyWu/p/10298670.html)
10. 《项目驱动--CAN-BUS现场总线基础教程----周立功，黄晓清》
11. 《现场总线技术及其应用第二版–清华大学出版社》。 

## "线与"逻辑的理解
CAN总线是双绞线，传输数据时，根据两根电缆之间的电压差进行传输，也称为**差分传输**。通过双绞线连接配合差分传输方式能够有效地抑制共模干扰，但是这就带来一个问题，**在没有数据传输时，两条线的电压相同，为隐性信号，逻辑信号为1**。一旦有数据传输，两条线就会出现电压不同的情况，从而**产生电压差**，CAN总线就会表现为**显性信号，逻辑信号为0**。

**但是为什么隐性为逻辑1，显性为逻辑0呢？**

我们首先忘记逻辑1和0的存在，只要记住隐性电平代表无数据传输，显性信号代表有数据传输。

首先解释**线“与”**这个概念，因为CAN总线上可以挂载很多CAN节点，节点通过**收发器**与CAN总线连接，每个节点都可以**主动向总线发送报文**。如果在同一时刻有很多节点同时发送报文，可能会出现数据干扰的问题。那么怎么解决这种问题呢？首先先来看一下单个can节点的收发器信号示意图：
![输入图片说明](/imgs/protocol-can/2024-01-29/AgrhLaz3RPqjO2lR.png)

单个节点输出隐性电平时开关断开，因为存在上拉电阻，输出电平为高电平。输出显性电平时开关闭合输出对地短路变成低电平。当很多节点存在，某个节点内部的开关闭合（输出显性电平）时将总线电平拉低，即使其它节点断开，总线电平仍然低。因为所有节点都是并联的，只要有一个或多个节点开关闭合时，线路就会输出低电平。这种逻辑与逻辑“与”相同，也就是**“线与”**。因此多个节点发送报文时，**隐性电平会被显性电平覆盖，此时不管谁是1谁是0，显性&隐性=显性**。

因此再来看第二个概念：**仲裁段**。

每个节点通过数据帧的形式发送报文（这里只需了解数据帧就是节点发送报文的载体格式），而发送的数据帧都有一个标识符（Identification）简称ID，这个ID标识了CAN数据帧的优先级。多个单元同时发送数据帧的时候，优先级较高的数据帧优先发送数据，低优先级的数据帧则等待空闲后发送。**我们规定优先级数值越小的单元优先级越高，也就是0000 0000(b)的优先级最高而1111 1111(b)的优先级最低。**此时先发送数据的节点使总线电平信号为显性。因此便称0为显性，1为隐性。

多插一句，在逻辑世界中，1、0代表两个相反的概念，并不是代表真实的电压概念。所以“1”和“0”是逻辑关系的两种可能的取值，不表示具体电压理解才是正确的。

### Reference
- [# CAN总线中隐性与显性问题](https://zhuanlan.zhihu.com/p/447088312)
