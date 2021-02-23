---
title: Mitre ATT&CK调研
date: 2020-02-07 13:53:36
tags: [网络安全]
categories: 网络安全
---

网安组调研内容
<!--more--> 

## 先验知识
### Cyber Kill Chain
* 杀伤链”这个概念源自军事领域，它是一个描述攻击环节的六阶段模型，理论上也可以用来预防此类攻击（即反杀伤链）。
* Cyber Kill Chain是由洛克希德-马丁公司提出的网络攻击杀伤链，本质是一种针对性的分阶段攻击。同样，这一理论可以用于网络防护。

<center><img src="https://gitee.com/know_the_emperor/picture/raw/master/CyberKillChain.png" width="300" height="500" alt="CyberKillChain"></center>

### ATT&CK
* ATT&CK是MITRE在2013年推出的模型，用以根据真实的观察数据来描述和分类对抗行为,该框架把攻击者所采用的 TTP (战术Tactics、技术Techniques、过程Procedures) 系统性地组织起来.
* ATT&CK将已知攻击者行为转换为结构化列表，将这些已知的行为汇总成战术和技术，并通过几个矩阵以及结构化威胁信息表达式（STIX）、指标信息的可信自动化交换（TAXII）来表示。

### MITRE ATT＆CK与Kill Chain的对比
* ATT&CK模型是在KillChain模型的基础上进行构建的。
* 目前ATT&CK模型分为三部分，分别是PRE-ATT&CK，ATT&CK for Enterprise和ATT&CK for Mobile。
    * PRE-ATT&CK覆盖Kill Chain模型的前两个阶段，包含了与攻击者在尝试利用特定目标网络或系统漏洞进行相关操作有关的战术和技术。
    * ATT&CK for Enterprise覆盖Kill Chain的后五个阶段。
    * ATT&CK for Mobile包含适用于移动设备的战术和技术。

![ATT＆CK与Kill Chain的对比流程图](https://gitee.com/know_the_emperor/picture/raw/master/20200207141533.png)

* ATT&CK与Kill Chain的不同之处在于，它并不遵循任何线性顺序，攻击者可以任意切换战术来实现目标。
* 除了在Kill Chain战术上更加细化之外，ATT＆CK还描述了可以在每个阶段使用的技术，而Kill Chain则没有这些内容。

## MITRE ATT＆CK框架详细介绍
* MITRE ATT＆CK以矩阵的形式来展现完整的攻击过程。
* 攻击战术展示在矩阵顶部，每列下面列出了单独的技术。一个攻击序列按照战术，至少包含一个技术，并且通过从左侧（初始访问）向右侧（影响）移动，就构建了一个完整的攻击序列。
* 攻击者不一定会使用矩阵顶部所示的所有12项战术。相反，攻击者会使用最少数量的战术来实现其目标，因为这可以提高效率并且降低被发现的几率。
![MITRE ATT＆CK矩阵](https://gitee.com/know_the_emperor/picture/raw/master/ATT%26CK%20Matrix%20for%20Enterprise.png)

### ATT&CK for Enterprise攻击战术
* 初始访问:从PRE-ATT&CK到ATT&CK的理想过渡点
* 执行:恶意软件的运行、APT攻击等
* 持久化:使用注册表Run键、启动文件夹等方法使得恶意软件在靶机上持续存在
* 提升权限:利用系统漏洞获取到root级访问权
* 防御规避:骗过防病毒产品或绕过应用白名单等技术
* 凭据访问:窃取密码来获得访问权限
* 发现:类似于Cyber Kill Chain中的侦查,较难防御
* 横向移动:攻击者在利用单个系统漏洞后，通常会尝试在网络内进行横向移动，寻找更高的访问权限，以期达成最终目标
* 收集:攻击者为了发现和收集实现目标所需的数据而采取的技术
* 命令与控制:通过命令和控制权来渗透数据、告诉恶意软件下一步执行什么指令
* 数据渗漏:攻击者获得访问权限后会四处搜寻相关数据，然后开始着手数据渗透
* 影响:攻击者试图操纵、中断或破坏企业的系统和数据

### ATT&CK for Enterprise技术描述
**在ATT&CK中所有的攻击检测都是基于数据源和策略的特征匹配，我们如果需要检测某个攻击技术，首先需要获取到这项技术所对应的数据。**
* 这些数据就是当攻击者执行某项技术攻击主机或网络后，在主机或网络设备上留下的蛛丝马迹，他们所呈现的形式往往是各种日志，可能是系统或应用内置的日志，也可能是因为安全需要而特意录制的日志数据。
* 在 MITRE ATT&CK 的每项技术描述中都有对应于该技术的数据源信息，它告诉我们可以从哪些类型的数据中找到攻击技术实施后所留下的痕迹。
* 并且MITRE ATT&CK还给出了每项技术的程序示例、侦测及缓解措施。

**在此以LSASS Driver为例来予以说明**
* 我们所分析过的wannacry正是采用了DLL注入到进程lsass.exe 中的方法来实现执行和持久化这两个步骤。

#### 技术描述
* 对于每一项技术ATT&CK都会给出一段简单的描述便于使用者去简要理解这项技术。
* 从技术描述中我们可以了解到`对手可能将lsass.exe驱动程序作为目标来获得执行和/或持久性。对手可以实现由连续LSA操作触发的任意代码执行`这和我们分析得出的Wannacry使用的技术相符合。

![describe](https://gitee.com/know_the_emperor/picture/raw/master/%E6%8A%80%E6%9C%AF%E6%8F%8F%E8%BF%B0.png)

#### 数据源信息
* 在ATT&CK的数据库中对每一项技术都有自己特有的ID，并且给出了侦测与防御它所需要的数据源。

![sourceData](https://gitee.com/know_the_emperor/picture/raw/master/20200208131508.png)

#### 侦测与防御方案
* 最后ATT&CK也给出了侦测与防御的建议。

![Detection&Defence](https://gitee.com/know_the_emperor/picture/raw/master/20200208131726.png)

#### 程序示例
* 对于每一项技术，ATT&CK还给出了使用过这种技术的攻击示例。

![example](https://gitee.com/know_the_emperor/picture/raw/master/20200208132336.png)

* 而对于每一个攻击示例，也提供了各种信息，可以用来定点防范。

![example2](https://gitee.com/know_the_emperor/picture/raw/master/20200208132730.png)

## ATT&CK的应用场景
**至目前为止，ATT&CK 知识库主要被应用在以下四大方向上：**
* 模拟攻击:基于 ATT&CK 进行红蓝攻防演练，进行红蓝军建设。
* 检测分析:基于具体的技术，有效增强检测能力，用于甲方安全建设。
* 威胁情报:使用 ATT&CK框架来识别攻击组织，用于安全情报建设。
* 评估改进:将解决方案映射到 ATT&CK 威胁模型，发现并弥补差距，用于评估安全能力。

### 模拟攻击
**此部分重点讨论如何基于ATT&CK框架来制定红蓝对抗方案以提升检测能力。**
* 传统的渗透测试侧重于突出攻击者可能在某个时间段会利用不同类型系统上的哪些漏洞。MITRE的对抗模拟方法不同于这些传统方法。其目标是让红队成员执行基于特定或许多已知攻击者的行为和技术，以测试特定系统或网络的防御效果。
* 对抗模拟演习由小型的重复性活动组成，这些活动旨在通过系统地将各种新的恶意行为引入环境，来改善和测试网络上的防御能力。
* 进行威胁模拟的红队与蓝队紧密合作(通常称为紫队)，以确保进行深入沟通交流，这对于快速磨练组织机构的防御能力至关重要。因此，与全范围的渗透测试或以任务目标为重点的红队相比，对抗模拟测试测试速度更快、测试内容更集中。
* 具体实例可参考[《基于ATT&CK框架的红蓝对抗，有效提升检测能力》](https://www.secpulse.com/archives/117592.html)

### 威胁情报
*  ATT&CK（对手战术、技术及通用知识库）是一个反映各个攻击生命周期的攻击行为的模型和知识库，而威胁情报是对攻击者及其恶意活动的可运营的知识（actionable knowledge）和洞见（insight），其中知识包括上下文、机制、指标、含义和可执行的建议。因此我们可以利用ATT&CK模型来增强威胁情报。
* 具体实例可参考[《实战化ATT&CK™：威胁情报》](https://www.secpulse.com/archives/111108.html)及[《ATT＆CK在情报中的运用》](https://www.anquanke.com/post/id/194911)两篇博文。


### ATT&CK框架的影响
* ATT&CK 框架可以成为业界的标准（能力可度量）
    * 目前 ATT&CK 框架主要包含终端相关安全知识，可以根据检测能力的矩阵覆盖率来评估部分安全产品的能力，例如：沙箱、EDR、SIEM 等。
    * 如果顺利发展的话后期也可能加入Web安全相关安全知识，那时 WAF、RASP、代码审计等 Web 相关安全产品也可以凭借框架进行评估。
* ATT&CK框架可以成为业界的通用语言（威胁数据标签化）
    * 在安全分析工作中，语言多样性同时造成语言障碍，影响协作能力。
    * 比如今天这个设备的告警叫 “smb漏洞利用”，明天相同的告警叫 “MS17-010”，若是能以后大家都称其为 T1210-Exploitation of Remote Services，SubTxxxx-MS17010，每个人的一小步，却是行业的一大步。

### ATT&CK框架的局限与不足
* 虽然 ATT&CK 框架期望实现建立一个大而全的威胁知识库，但目前的阶段还尚处于初步了解框架概念。MITRE 致力于建立一个 Cyber Analytics Repository，但由于内容太少而不足以提供丰富的检测能力。
* 在ATT&CK中，PRE-ATT&CK中才是情报建设核心能力的部分，但是这部分的能力建设门槛比较高，并且ATT&CK在这一部分中的定义的比较粗，基本都是计划、人员组织、开发测试等逻辑术语。
* 不过也有很多迫不及待的人已经提前动起了手，例如 Red Teaming Experiments、atomic-red-team，但他们更注重的是 Red Team 攻击过程的复现，我们期望的是 Red Team 代码级复现 +Blue Team 威胁特征提取。

**随着 ATT&CK 框架的认知度越来越高，其完善发展的速度一定会更快，应用的方向也会更广。就目前阶段，ATT&CK 最大作用是帮助恶意行为的检测和分析。**

## ATT&CK框架的实施与使用

### 整体实施规划
**对于不同成熟度阶段的防御方案中，ATT&CK框架有着不同的映射方法，以便各种防御方案都能从中获取到价值。**

#### 阶段1：参考和数据丰富
* MITRE ATT&CK 框架包含大量具有潜在价值的数据，在此阶段可将该框架的数据当做详细的对手资料参考源，人工丰富对事件和警报的分析。
* 为更好地消费该数据，可以运用能方便访问并共享该数据的工具，比如数据丰富工具，或带中央威胁库方便用户聚合数据并搜索对手资料的平台
。

#### 阶段2：指标或事件驱动的响应
* 阶段2建立在参考和理解MITRE ATT&CK数据的能力基础上。
* 第二阶段中安全团队要在自身运营工作流中融入该平台的各项功能，更有效地对数据进行操作。
* 通过自动关联事件及内部环境相关指标(指标源包括安全信息及事件管理(SIEM)系统、日志管理存储、案例管理系统和安全基础设施)和来自MITRE ATT&CK框架的指标，安全团队能获得相关上下文，即时知晓攻击的发起者、目标、源头、时间、动机和方式
* 一旦能够以自动化的方式简洁明了地利用ATT&CK数据，安全团队便可更有效地调查和响应事件，将威胁情报高效推送至传感器加以检测，有效追捕威胁。

#### 阶段3：战术或技术驱动的主动威胁捕捉
* 在这一阶段，威胁捕捉团队可从寻找指标转向充分利用ATT&CK数据。
* 不是专注看起来可疑的具体数据点，而是利用该平台站在更高的角度考虑对手及其相关TTP的信息。
* 威胁捕捉团队可采取主动，从风险情况入手，将风险映射到具体对手及其战术，深挖这些对手使用的技术，然后就可以在发现相关数据时立即展开调查了。

**万丈高楼平地起，在完全没开始利用MITRE ATT&CK 框架之前对更高层次的部署方案做太多调研意义不大。所以接下来的内容更注重于在阶段1中如何筛选技术并对想应的数据加以利用。**

### 技术筛选
**MITRE ATT＆CK框架中有好几百种技术，并且会随着新技术的推出以及人工智能和机器学习系统的部署而逐渐扩大,我们并不可能对所有的技术都进行检测与相应，所以需要根据具体的需求从中筛选。**

#### 威胁建模
* 对于公司来说需要了解清楚最需要防护什么，攻击者最可能盯上什么。
* 对于已经有一定防御措施的情况，可以从已有的技术出发来逐步构建与完善整个防护模型。

#### 寻找关键技术
* Red Canary通过对过去五年里，其客户环境中发生的一万多起恶意事件进行分析，得出了威胁事件利用每种ATT&CK技术的频率,并列出了Top 20的攻击技术。
* 构建防御模型也常常需要考虑覆盖到这些关键技术。

![Top20](https://gitee.com/know_the_emperor/picture/raw/master/20200208152700.png)

#### 确认攻击难度
* Tripwire 的 Travis Smith 在 ATT&CKCon 大会上做了题为“ATT&CK as a Teacher”的演讲，将 ATT&CK 矩阵按漏洞利用难度加以组织。
* 构建防御模型时可以以攻击难度为标准来选择适合自己的技术。

![技术难度分级](https://gitee.com/know_the_emperor/picture/raw/master/20200208162634.png)

#### 数据源筛选
* 当确定要在检测方案中实施相关检测技术时，需要确保有适当的数据源来实施针对该技术的检测方案，所以也需要根据自己可获取到的数据源来选择技术。
* 而较多技术所需要的数据源的获取也是防御模型的构建者所需要考虑的问题。

![数据源统计](https://gitee.com/know_the_emperor/picture/raw/master/20200208162802.png)

* 需要注意的是，ATT＆CK命名的几乎每个高级数据源都包含子数据源(该数据源的不同形式)，所以有必要了解自己可以访问哪些数据源，弄清这些子数据源提供哪些信息，只找出其中一个子数据源是不够的。
* 实现不同相关技术检测的时候，可以通过分析特定技术与自己防御模型的相关性，来减少自己需检测的子数据源数量。
    * 比如说有 66种不同技术需要文件和进程监视数据源，而我们所需要检测的技术可能只需要子数据源的一个子集就够了。
* 以及可以使用两种不同的方法来执行和检测一种技术，所以需要考虑哪个数据源对我们的防御模型更加关键，有所侧重才能更高效地执行检测。

### 数据源信息获取
* github上有[开源安全事件元数据(Open Source Security Events Metadata (OSSEM))](https://github.com/Cyb3rWard0g/OSSEM)可供使用。
* OSSEM 由 Rodriguez 兄弟创建，提供数据源相关的四类信息:
    * ATT&CK数据源：将 ATT&CK 技术的数据源映射到实际系统事件或分析，产生适用于该技术检测机制的数据。
    * 检测数据模型
    * 通用信息模型
    * 数据字典


### 数据整合
* 在了解数据源的物理来源以及事件与这些物理数据源的关系之后，需要有一个信息存储库及相应的查询方式。
* 可以使用图形数据库，并根据数据字典和公共信息模型中的信息实现类似于以下图表的内容：

![数据整合](https://gitee.com/know_the_emperor/picture/raw/master/20200208161525.png)

* 数据整合工作量非常大，这个过程可以选择一些开源工具辅助进行。

#### 开源工具
* 目前我所见到的与ATT&CK想匹配的数据整合工具有以下两个，但两个工具的具体职能是什么？是否功能重叠？各有什么局限性？这些问题还有待后续调研。
* Osquery
    * OSquery可以收集环境中各主机的信息，并将数据聚合到表格中。可以使用类似SQL的查询来访问表格中的数据并编写检测方案，因此对于接触过关系型数据库的人来说难度并不大。
    * 此外，OSquery可以创建查询集合，映射到ATT＆CK中的目标TTP，进行威胁捕获。安全人员可以即时创建和执行在线实时查询。
    * 有些查询可以识别网络攻击者，这些查询可以集成到SIEM（Security Information and Event Management，安全信息及事件管理）系统中来。
* Sysmon
    * Sysmon 是微软的一款免费的轻量级系统监控工具，它通过系统服务和驱动程序实现记录进程创建，网络连接以及文件创建时间更改的详细信息，并把相关的信息写入并展示在 windows 的日志事件里，以便用户使用SIEM（Security Information and Event Management，安全信息及事件管理）工具收集相应信息。

### 落地项目简介
**不像其它理论只是提供理论指导作用,ATT&CK 框架的可落地性很强,并且目前已经有很多落地的项目可以帮助使用者来学习如何使用该框架。**

若想了解更将详细的信息可参考《ATT&CK 实战指南》。

#### 理论学习使用
* ATT&CK™ Navigator项目：导航工具，有较好的可交互性，便于了解 ATT&CK 的各项技术。
* ATT&CK™ 的 CARET 项目：CAR(Cyber Analytics Repository 网络分析库) 项目的演示版本，有助于理解 CAR 这个项目 (具体会在 Blue Team使用中介绍)表达的内容。

#### Red Team 使用
* Red Canary™ Atomic Red Team项目：可以根据框架的技术通过脚本的自动化攻击。
结合此项目可以完善红队攻击测试库，根据实际情况不断进行测试和回归测试，让安全攻击水准达到一个比较好的水平。
* ATTACK-Tools项目：可用作模拟攻击的计划工具或ATT&CK关系型数据库的查询工具。

#### Blue Team 使用
* ATT&CK™ CAR 项目：理论架构，主要是针对 ATT&CK 的威胁检测和追踪，内容丰富度上比较欠缺。
* Endgame™ EQL 项目：EQL（Event Query Language）是一种威胁事件查询语言，可以对安全事件进行序列化、归集及分析。该项目可以进行事件日志的收集，不局限于终端数据，还可以是网络数据。
*  DeTT&CT 项目：DeTT&CT（DEtect Tactics, Techniques & Combat Threats）项目，用于帮助防御团队评估日志质量、检测覆盖度的工具。
*  Sigma 项目：Sigma项目是一个SIEM的特征库格式项目。该项目可以直接使用sigma格式进行威胁检测的描述，可以进行共享，也可以进行不同 SIEM 系统的格式转换。
* MISP 项目：恶意软件信息共享平台 MISP（Malware Information Sharing Platform）是一个开源的威胁情报平台。

#### CSO(首席信息官) 使用
• Atomic Threat Coverage 项目：组织型项目，重点组成部分是上面提到的两个项目：Red Canary™ Atomic Red Team 和 Sigma项目，二者分别负责模拟攻击和攻击检测。

## 参考文献
* [Mitre ATT&CK官网](https://attack.mitre.org/)
### 先验知识
* [简书:《Cyber Kill Chain洛克希德-马丁公司的网络杀伤链》](https://www.jianshu.com/p/174b5a081b51)
* [《洛克希德-马丁公司的七步网络杀伤链白皮书》](https://www.lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf)

### MITRE ATT＆CK框架详细介绍
* [安全脉搏:《一文看懂ATT&CK框架以及使用场景实例》](https://www.secpulse.com/archives/115412.html)

### MITRE ATT＆CK的应用场景
* [简书:《ATT&CK如何为安全产品赋能》](https://www.jianshu.com/p/a49a001fef44)
* [安全脉搏:《基于ATT&CK框架的红蓝对抗，有效提升检测能力》](https://www.secpulse.com/archives/117592.html)
* [安全客:《ATT＆CK在情报中的运用》](https://www.anquanke.com/post/id/194911)
* [安全脉搏:《实战化ATT&CK™：威胁情报》](https://www.secpulse.com/archives/111108.html)
* [安全牛:《ATT&CK 在大数据安全分析中的应用思考》](https://www.aqniu.com/tools-tech/58397.html)


### ATT&CK框架的实施与使用
* [安全牛:《加速检测与响应的最新工具：MITRE ATT&CK 框架》](https://www.aqniu.com/tools-tech/45459.html)
* [安全脉搏:《青藤细述MITRE ATT＆CK框架的实施和使用方式》](https://www.secpulse.com/archives/115559.html)
* [安全牛:《MITRE ATT&CK 框架“入坑”指南》](https://www.aqniu.com/learn/61033.html)
* [安全牛:《浅谈ATT&CK对提升主机EDR检测能力的探索》](https://www.aqniu.com/industry/60527.html)
* [DOIT:《ATT&CK框架：攻击者最常用的TOP7攻击技术及其检测策略》](https://www.doit.com.cn/p/350715.html)
* [安全内参:《ATT&CK实战指南》](https://www.secrss.com/articles/14991)

