## 前言

中国长江三峡集团是国内最大的清洁能源集团，经过多年坚持不懈的努力，已成为清洁能源产业的引领者。三峡新能源作为三峡集团新能源业务的战略实施主体，承载着发展新能源的历史使命。近年来，三峡新能源积极发展陆上风电、光伏发电，大力开发海上风电，稳健发展中小水电业务，探索推进地热能、潮汐能、风电制氢等新业务。同时，投资与新能源业务关联度高、具有优势互补和战略协同效应的相关产业，基本形成了风电、太阳能为主体，中小水电、战略投资为辅助的相互支撑、协同发展的业务格局，努力打造产业结构合理、资产质量优良、经济效益显著、管理水平先进的世界一流新能源公司。

**本文由三峡集团科学技术研究院专业师庄宇飞、助理专业师李雨欣执笔，详细介绍了三峡集团新能源发电业务中大规模储能系统管理平台建设的实践经验。**

## 储能在新能源建设中的意义与挑战

2020 年 9 月，我国在第七十五届联合国大会上就减少二氧化碳排放量做出了重要承诺，明确提出力争在2030 年前达到二氧化碳排放峰值，在 2060 年前实现碳中和。构建「清洁低碳、安全高效」的现代能源体系已成为国家核心战略，大力发展风电、光伏等新能源势在必行。

然而，风电、光伏等新能源发电具有间歇性、波动性等特征，其大规模并网将面临巨大的消纳难题。目前，由于电网无法有效消纳快速增长的可再生能源导致部分地区「弃风、弃光」现象突出，严重影响可再生能源利用效率以及投资回报率。

在可再生能源消纳的诸多解决方案中，储能技术目前被认为是应对大规模可再生能源接入的最有效手段之一。储能具备发电、用电的双向快速调节能力，可作为保障电力供应的「速效救心丸」，以及促进可再生能源消纳的“超级充电宝”。 

相对于其他储能技术，电化学储能系统具有快速功率响应、密集能量存储、灵活方便部署等优势，是目前发展最快、应用最广的储能技术之一，其规模化建设可望在电网调峰调频、波动平抑、紧急功率支援等应用场景中发挥重要作用。但海量电化学电池规模化成组构成的储能系统可靠性、经济性不足是规模化电池储能系统广泛应用的主要瓶颈。因此，面向大规模风光并网消纳的迫切需求，如何充分考虑电化学储能装置的成本、技术特性，有效集成和管理大规模电化学储能装置，进而充分发挥其应用优势尤为重要。这其中涉及到系统集成、能量管理、智能运维等一系列关键技术。

## 项目背景与目标

某地风光储项目是三峡集团为推动新能源和储能产业发展而规划的一项重要举措，项目拟建设规模为百兆瓦时级的电化学储能系统示范及管理运维平台。

从自治区能源需求及电力预测看，近年乌兰察布地区用电需求增长较快，新增电源相对较少，电力供应存在缺口，「十四五」期间预计全网电力缺口将达 260-730 万千瓦。虽然该地区具有丰富的风电、光伏资源，但在新能源的发展及应用层面面临着一定程度的弃风弃光问题，同时规模化储能、数据信息等调节性资源的价值尚未能充分挖掘。因此，**大规模储能电站建设是三峡集团风光储项目的关键部分。**

针对示范区大规模多类型储能系统运行管理的需要，项目计划基于物联网云边协同与边缘计算技术提供对**储能单元状态感知、边云协同能量管理、自律分散控制策略、储能运行可靠性评估以及储能系统运维策略**等多方面能力。在此基础上，集成系统若干边缘计算节点及系统私云，建立储能系统能量管理和智能运维平台，实现大规模电池储能系统的协同管理。

## 大规模储能场景中数据接入的特点

### 数据量大、采集频率高

以数字储能集装箱为例，物联网终端对多台集装箱内总数约数十万个点以每 100ms~200ms 的频率进行采集，一年的数据量超过 445T。大规模的数据显著增加了存储和计算资源的压力，同时超高的采集频率也对物联网终端设备的性能和稳定性提出了更高的要求。

![数据量和采集频率](https://assets.emqx.com/images/980e8a69d2d83c847a7b3b2ce1e54b7b.png)

<center>数据量和采集频率</center>

### 流式数据模型

物联网采集数据大多都是以连续的数据流的形式，从多种外部数据源持续不断地生成。在多数情况下，我们无法控制这些流数据到达的顺序和产生的速率。数据流本身可以看作是一个带有时间戳的无限增长的结构化日志模型，支持的典型操作包括追加写和区间读；它的流向是不可变的，一般不支持更新操作。

传统储能系统的数据分析通常基于批处理技术，一般在预先收集好的有限数据集上运行，基于请求-响应的命令驱动运行模式。在这种情况下，计算指令是主动的，数据是被动的，分析的结果往往不包含最新的数据，时延较高，不能够满足对数据进行实时分析计算的要求。

流处理的模式则恰恰相反，不同于传统的将静态数据集（表、文件）作为基本的存储和处理单元，我们将动态的数据流作为基本对象，即以数据作为驱动，以时间戳为特征，对滚动时间窗口内的数据或仅对最近的数据记录进行查询或处理，数据源源不断流过计算并持续更新计算结果。

### MQTT 通讯协议模式

海量物联网设备的存在使得传统的数据传输协议不再适用于物联网平台，取而代之的是支持服务自发现、动态扩展、低功耗、对网络环境包容性强，并且足够简单的通讯协议 [MQTT 协议](https://www.emqx.com/zh/mqtt)，其特点包括：

- 消息发布与订阅
  消息的发布者不会将消息直接发送给特定的接收者，而是通过消息通道广播出去，让订阅该消息主题的订阅者消费到。
- 松耦合
  可包含若干个子通讯系统，可独立管理每个子通讯系统，即使部分 down 掉也不影响整体。
- 高伸缩
  增加系统的可伸缩性，并提高了发送者的响应能力。
- 高可靠
  异步的消息传递有助于应用程序在增加的负载下继续平稳运行，并且可以更有效地处理间歇性故障。
- 灵活组网
- 测试简单

## 云边一体的储能数据接入方案

本项目采用了边云协同架构实现了对海量储能数据的有效管理和储能状态的实时感知，并基于自律分散思想建立系统的一致性控制策略，保证储能系统响应的速度和准确性。

![云边一体设计方案.png](https://assets.emqx.com/images/2f41c1d874ba8dba7e2e8c46c9cafcc1.png)

<center>总体设计方案</center>

我们**采用了 EMQ 提供的相关套件**来实现，包括以下软件：

| **组件**         | **名称**                                                     |
| ---------------- | ------------------------------------------------------------ |
| 边缘网关         | [Neuron-边缘工业协议网关软件](https://www.emqx.com/zh/products/neuron) |
| 边缘 Broker      | [NanoMQ-超轻量级边缘 MQTT消息服务器](https://www.emqx.com/zh/products/nanomq) |
| 边缘计算单元     | eKuiper-轻量级物联网边缘分析/流式处理软件                    |
| 中心 Broker/集群 | [EMQX Enterprise-云原生分布式物联网接入平台](https://www.emqx.com/zh/products/emqx) |
| 云边协同管理软件 | EMQ 云边协同管理平台                                         |

下面我们将对这一方案架构中的各部分进行详细介绍：

### 边缘计算设备

边缘网关、Broker 以及边缘计算单元运行在资源受限的 X86/ARM 服务器或嵌入式设备。

流行的 CPU 架构有：X86 AMD32、X86 AMD64、ARM32、ARM64。

操作系统为常见 Linux 发行版、OpenWrt 嵌入式系统、MacOS、Docker 等。

硬件设备如工控机、树莓派、工业网关、MEC 边缘云等。

### 工业软网关

本项目采用 Neuron 作为工业软网关，将工业数据传输协议解析成为标准的 MQTT 协议并发布到边缘 Broker，北向标准 MQTT 数据发送，根据用户指定配置，将数据发送至指定的 Broker；南向控制接口，监听控制设备的主题，将相关的控制命令转发给设备。

网关对边缘计算资源的需求主要取决于数据采集协议，ModbusTCP 协议的 Server 轮询 Client 时，单个 Neuron 节点可以支持 3000 点以上，如果点位数量大大超过网关的承载能力，可以考虑在边缘设备上部署多个网关实例。

下图所示为 Neuron 的配置界面：

![Neuron 配置界面](https://assets.emqx.com/images/b592b1a948f56afd6b32fbc52a4657c8.png)

<center>Neuron网关配置界面</center>

### 边缘侧 Broker

边缘 Broker 是经过网关协议转换后的数据的边缘中转站，中心 Broker 从边缘 Broker 订阅全量原始数据并存储到数据库，同时边缘计算单元也从边缘 Broker 订阅（或直连）原始数据流进行边缘计算。边缘计算设备算力有限，边缘 Broker 在达到尽可能高的性能和吞吐量的情况下，需要保持尽量少的资源消耗占用。 Broker 对计算资源的需求主要是内存，在我们目前方案中采用的 NanoMQ 仅需 200Mb 的内存消耗就能支持超过 80 万条每秒的消息吞吐，同时每 1G 的内存能够保持 6W 以上的设备连接。

### 边缘计算单元

边缘计算单元承担解析MQTT数据包，进行流式计算并将计算结果以MQTT协议发布到中心Broker的任务，最后由中心Broker解析后分发到不同主题，供消费者订阅。目前边缘计算单元使用开源软件eKuiper，采用基于数据源（source）- 业务处理逻辑（rule）- 目标（sink）的规则引擎来进行流式计算。 

![边缘计算单元计算流程1](https://assets.emqx.com/images/d3c02363f1fb42e23e64f105d08da3f4.png)

![边缘计算单元计算流程2](https://assets.emqx.com/images/7033aaae41c6e8c68c5a4d1c78f57f4a.png)

<center>边缘计算单元计算流程</center>

- 配置数据源

   边缘计算单元订阅来自于 Broker 的 MQTT 代理消息，并放入流数据的处理管道。

- 创建数据流

  定义输入数据流的字段类型、解析格式以及数据源。 

  ```
  create stream   
  
      stream_name   
  
      ( column_name <data_type> [ ,...n ] )
  ```

- 对流数据进行计算和转换

  eKuiper提供了类似 SQL 语法的查询语言，执行流数据的转换和计算。内置了常见的聚合函数、数学函数、字符串函数和转换函数等，同时也提供了时间窗口函数，对时态窗口中包含的数据执行操作。

  SELECT 用于从输入流中检索行，并允许从 eKuiper 中的一个或多个输入流中选择一个或多个列进行计算。

  ```
  SELECT column_name(s)
  
  FROM stream1
  
  LEFT JOIN stream2
  
  ON stream1.column_name = stream2.column_name;
  ```

  例如统计设备一分钟之内的平均温度：

  ```
  SELECT avg(celcius) FROM celciusStream WHERE device_id = "40001" 
  
  OR device_id = "40002" GROUP BY TUMBLINGWINDOW(ss, 60); 
  ```

- 插件开发

  对于大多数复杂场景，例如需要对数据流进行拆分、循环、条件筛选或者执行复杂公式计算的，基于SQL的查询语句无法满足我们的要求。eKuiper 提供了原生插件、可移植插件、外部函数扩展三种方式：

	![eKuiper 插件开发](https://assets.emqx.com/images/0a64f7f57ec8edb69be9cea8d9e702f0.png)

  本项目中通过开发 go 语言的可移植插件来实现要求的功能。可移植插件的开发需要下载、引入 eKuiper 源码和 SDK，我们从 GitHub 上下载 eKuiper 源代码作为开发编译环境，在本机进行插件的开发、调试和单元测试、编译打包完毕后加载至边缘计算设备。流程如下：

	![eKuiper 插件开发](https://assets.emqx.com/images/4908abab4b0624d2afdac5ec7898b1e3.png)

### 中心 Broker

- 高可用集群

  中心侧的核心是高吞吐量 Broker，按照 84 台集装箱计算，中心 Broker 要承载的测点数量包含所有测点原始数据的 40 万以上测点，加上边缘侧进行分析计算后额外产生的数据量。 方案实现中采用的 EMQX Enterprise，单服务器节点支持 50 万到 100 万路由，采用 4 节点或以上集群的部署方案，可以支持 1000 万点以上的路由。

- 规则引擎

  规则引擎不仅能用于简单的数据清洗、编解码、格式转换，还可用于配置 Broker 消息流与设备事件（设备上线、断线等）的处理和响应规则。命中规则后，可以将经过规则处理后的结果以不同的形式转发出去供特定消费者订阅或对接 Kafka 以及数据库。

  实施过程中，为命中分发规则的的消息添加如下动作，将消息以元素的"addr"的属性为主题进行分发，供前端消费者订阅：

	![EMQX 规则引擎](https://assets.emqx.com/images/d857f4eae1e2bcd7c39dc7fe1c48e6d0.png)

  在客户端上订阅主题"010101"时，即可获取对应的计算结果：

	![MQTT 订阅](https://assets.emqx.com/images/04473702542ba9e64d3c6e3ec37598c3.png)

- 数据持久化

  通过 Broker 的消息可以通过规则引擎的对接 Kafka 或 MQTT 消息的方式满足前端的各种消费需求，也可以通过持久化的方式落盘，或进入大数据平台，形成数据资产。

  在实施过程中，边缘 Broker 将从网关获取的全量原始数据转发至中心 Broker，边缘计算单元将边缘计算结果转发至中心 Broker，最后由中心 Broker 把这些数据持久化到时序数据库中，为大数据分析提供素材。

  例如：

  创建设备温度超级表

  ```
  create table device_temp (ts timestamp, celcius float) tags (tp binary(64), device_id binary(64));
  ```

  创建设备温度子表

  ```
  create table 40001 using device_temp tags ("01/01/01", "40001");
  
  create table 40002 using device_temp tags ("01/01/02", "40002");
  ```

  插入数据到 TDengine 库：

  ```
  insert into ems.{device_id}_temp(ts, celcius) values({ts}, ${celcius});
  ```

### 边缘管理

EMQ 边缘管理平台是基于 EMQ 核心产品构建的基础 IoT 平台框架，提供云边协同、EMQ集群管理等 IoT 平台核心功能。

由于边缘设备众多，手动进行 SSH 连接或者 Web 访问登录配置每一台边缘设备要耗费很大的工作量，很多时候用户需要从云端对边缘设备提供的 HTTP 服务进行访问（边缘设备一般没有公网 IP），方便用户在局域网内通过浏览器查看服务的状态或者进行相应的设置。通过 EMQ 云边协同管理平台建立基于 TCP 或 MQTT 协议实现的边缘端设备发现与管理的云边通道，可以通过底层 API 实现由云到边服务的访问。这样便可通过统一的入口、界面来管理所有连接的设备，在中心侧实现配置边缘计算单元的规则，安装、卸载、更新插件等功能。

## 项目收益

通过 EMQ 提供的云边协同数据接入与处理能力，我们建立了基于物联网的大规模电池储能系统监控架构，基于自律分散结构的大规模储能系统协同控制和能量管理技术，实现储能系统状态感知和系统运行可靠性在线评估，开发储能系统智能诊断与预警算法，打造了大规模储能系统能量管理和智能运维平台，并开展示范验证。平台支持数据点 100 万个以上，控制响应时间不超过 500ms，满足了大规模电池储能系统协同控制、安全和可靠运行需求。

### 实现更高的有效容量，有效提升经济效益

通过新一代储能设备管理平台有效克服传统 BMS 储能方案的短板效应，有效容量可显著提升。随着新电池的使用，其短板效应将逐渐扩大，该项目的优势则越明显，预期在电池的寿命周期内，平均有效容量可提升 15% 以上，有效提升经济效益。

### 实现更长的电池使用寿命，降低电池更换成本

本项目的容量保持率将始终高于传统的 BMS 储能方案，电池可以实现更长的循环寿命，从长期来看，可以减少电池更换的数量和次数，进而降低未来的运维成本与后期投入。

### 大幅提升储能安全，节省持续运检成本

通过实现毫秒级电池网络拓扑重构能力以及毫秒级故障电池模组在线检测及自动隔离功能，完美应对了在不同电力出力场景下电池电化学差异性所带的储能系统的本征安全挑战，预防电池故障于未然。真正做到远程在线式的电池本征安全风险的无人化实时及时的应对与管控，几乎避免了对上站人工操作的依赖，尤其在大规模储能应用场景中，可直接节省运维成本 30~50%。


<section class="promotion">
    <div>
        免费试用 EMQX Cloud
        <div class="is-size-14 is-text-normal has-text-weight-normal">全托管的云原生 MQTT 消息服务</div>
    </div>
    <a href="https://accounts-zh.emqx.com/signup?continue=https://cloud.emqx.com/console/deployments/0?oper=new" class="button is-gradient px-5">开始试用 →</a >
</section>
