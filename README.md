# GAS文档（GASDocumentation）
我对虚幻引擎5的游戏能力系统插件（GameplayAbilitySystem plugin，GAS）的理解，附带一个简单的多人游戏示例项目。这不是官方文档，本项目和我本人均与Epic Games无关。我不保证本文信息的准确性。

本文档的目标是解释GAS中的主要概念和类，并根据我的使用经验提供一些额外的评述。社区用户中存在大量关于GAS的"部落知识"，我希望在此分享我所知的一切。

示例项目和文档当前适用于**虚幻引擎5.3**（Unreal Engine 5.3，UE5）。本文档有针对旧版虚幻引擎的分支，但它们不再受到维护，可能存在错误或过时的信息。请使用与您引擎版本匹配的分支。

[GASShooter](https://github.com/tranek/GASShooter) 是一个姊妹示例项目，展示了在多人FPS/TPS游戏中使用GAS的高级技巧。

最好的文档永远是插件源代码本身。

<a name="table-of-contents"></a>
## 目录

> 1. [游戏能力系统插件简介（Intro to the GameplayAbilitySystem Plugin）](#intro)
> 1. [示例项目（Sample Project）](#sp)
> 1. [使用GAS搭建项目（Setting Up a Project Using GAS）](#setup)
> 1. [概念（Concepts）](#concepts)
>    4.1 [能力系统组件（Ability System Component）](#concepts-asc)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [复制模式（Replication Mode）](#concepts-asc-rm)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [设置与初始化（Setup and Initialization）](#concepts-asc-setup)
>    4.2 [游戏标签（Gameplay Tags）](#concepts-gt)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [响应游戏标签的变化（Responding to Changes in Gameplay Tags）](#concepts-gt-change)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.2 [从插件 .ini 文件加载游戏标签（Loading Gameplay Tags from Plugin .ini Files）](#concepts-gt-loadfromplugin)
>    4.3 [属性（Attributes）](#concepts-a)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [属性定义（Attribute Definition）](#concepts-a-definition)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [基础值与当前值（BaseValue vs CurrentValue）](#concepts-a-value)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [元属性（Meta Attributes）](#concepts-a-meta)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [响应属性变化（Responding to Attribute Changes）](#concepts-a-changes)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [派生属性（Derived Attributes）](#concepts-a-derived)
>    4.4 [属性集（Attribute Set）](#concepts-as)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [属性集定义（Attribute Set Definition）](#concepts-as-definition)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [属性集设计（Attribute Set Design）](#concepts-as-design)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [具有独立属性的子组件（Subcomponents with Individual Attributes）](#concepts-as-design-subcomponents)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [在运行时添加和移除属性集（Adding and Removing AttributeSets at Runtime）](#concepts-as-design-addremoveruntime)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [物品属性（武器弹药）（Item Attributes (Weapon Ammo)）](#concepts-as-design-itemattributes)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [物品上的普通浮点数（Plain Floats on the Item）](#concepts-as-design-itemattributes-plainfloats)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [物品上的`AttributeSet`](#concepts-as-design-itemattributes-attributeset)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [物品上的`ASC`](#concepts-as-design-itemattributes-asc)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [定义属性（Defining Attributes）](#concepts-as-attributes)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [初始化属性（Initializing Attributes）](#concepts-as-init)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)
>    4.5 [游戏效果（Gameplay Effects）](#concepts-ge)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [游戏效果定义（Gameplay Effect Definition）](#concepts-ge-definition)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [应用游戏效果（Applying Gameplay Effects）](#concepts-ge-applying)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [移除游戏效果（Removing Gameplay Effects）](#concepts-ga-removing)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [游戏效果修改器（Gameplay Effect Modifiers）](#concepts-ge-mods)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [乘法和除法修改器（Multiply and Divide Modifiers）](#concepts-ge-mods-multiplydivide)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [修改器上的游戏标签（Gameplay Tags on Modifiers）](#concepts-ge-mods-gameplaytags)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [堆叠游戏效果（Stacking Gameplay Effects）](#concepts-ge-stacking)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [授予的能力（Granted Abilities）](#concepts-ge-ga)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [游戏效果标签（Gameplay Effect Tags）](#concepts-ge-tags)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [免疫（Immunity）](#concepts-ge-immunity)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [游戏效果规格（Gameplay Effect Spec）](#concepts-ge-spec)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [游戏效果上下文（Gameplay Effect Context）](#concepts-ge-context)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [修改器幅度计算（Modifier Magnitude Calculation）](#concepts-ge-mmc)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [游戏效果执行计算（Gameplay Effect Execution Calculation）](#concepts-ge-ec)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [向执行计算发送数据（Sending Data to Execution Calculations）](#concepts-ge-ec-senddata)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [后备数据属性计算修改器（Backing Data Attribute Calculation Modifier）](#concepts-ge-ec-senddata-backingdataattribute)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [后备数据临时变量计算修改器（Backing Data Temporary Variable Calculation Modifier）](#concepts-ge-ec-senddata-backingdatatempvariable)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [游戏效果上下文（Gameplay Effect Context）](#concepts-ge-ec-senddata-effectcontext)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [自定义应用需求（Custom Application Requirement）](#concepts-ge-car)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [消耗游戏效果（Cost Gameplay Effect）](#concepts-ge-cost)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [冷却游戏效果（Cooldown Gameplay Effect）](#concepts-ge-cooldown)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [获取冷却游戏效果的剩余时间（Get the Cooldown Gameplay Effect's Remaining Time）](#concepts-ge-cooldown-tr)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [监听冷却开始和结束（Listening for Cooldown Begin and End）](#concepts-ge-cooldown-listen)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [预测冷却（Predicting Cooldowns）](#concepts-ge-cooldown-prediction)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [更改活跃游戏效果的持续时间（Changing Active Gameplay Effect Duration）](#concepts-ge-duration)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [在运行时创建动态游戏效果（Creating Dynamic Gameplay Effects at Runtime）](#concepts-ge-dynamic)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [游戏效果容器（Gameplay Effect Containers）](#concepts-ge-containers)
>    4.6 [游戏能力（Gameplay Abilities）](#concepts-ga)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [游戏能力定义（Gameplay Ability Definition）](#concepts-ga-definition)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [复制策略（Replication Policy）](#concepts-ga-definition-reppolicy)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [服务器尊重远程能力取消（Server Respects Remote Ability Cancellation）](#concepts-ga-definition-remotecancel)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [直接复制输入（Replicate Input Directly）](#concepts-ga-definition-repinputdirectly)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [将输入绑定到ASC（Binding Input to the ASC）](#concepts-ga-input)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [绑定输入但不激活能力（Binding to Input without Activating Abilities）](#concepts-ga-input-noactivate)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [授予能力（Granting Abilities）](#concepts-ga-granting)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [激活能力（Activating Abilities）](#concepts-ga-activating)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [被动能力（Passive Abilities）](#concepts-ga-activating-passive)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.2 [激活失败标签（Activation Failed Tags）](#concepts-ga-activating-failedtags)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [取消能力（Canceling Abilities）](#concepts-ga-cancelabilities)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [获取活跃能力（Getting Active Abilities）](#concepts-ga-definition-activeability)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [实例化策略（Instancing Policy）](#concepts-ga-instancing)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [网络执行策略（Net Execution Policy）](#concepts-ga-net)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [能力标签（Ability Tags）](#concepts-ga-tags)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [游戏能力规格（Gameplay Ability Spec）](#concepts-ga-spec)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [向能力传递数据（Passing Data to Abilities）](#concepts-ga-data)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [能力消耗与冷却（Ability Cost and Cooldown）](#concepts-ga-commit)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [能力升级（Leveling Up Abilities）](#concepts-ga-leveling)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [能力集（Ability Sets）](#concepts-ga-sets)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [能力批处理（Ability Batching）](#concepts-ga-batching)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [网络安全策略（Net Security Policy）](#concepts-ga-netsecuritypolicy)
>    4.7 [能力任务（Ability Tasks）](#concepts-at)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [能力任务定义（Ability Task Definition）](#concepts-at-definition)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [自定义能力任务（Custom Ability Tasks）](#concepts-at-definition)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [使用能力任务（Using Ability Tasks）](#concepts-at-using)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [根运动源能力任务（Root Motion Source Ability Tasks）](#concepts-at-rms)
>    4.8 [游戏提示（Gameplay Cues）](#concepts-gc)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [游戏提示定义（Gameplay Cue Definition）](#concepts-gc-definition)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [触发游戏提示（Triggering Gameplay Cues）](#concepts-gc-trigger)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [本地游戏提示（Local Gameplay Cues）](#concepts-gc-local)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [游戏提示参数（Gameplay Cue Parameters）](#concepts-gc-parameters)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [游戏提示管理器（Gameplay Cue Manager）](#concepts-gc-manager)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [阻止游戏提示触发（Prevent Gameplay Cues from Firing）](#concepts-gc-prevention)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [游戏提示批处理（Gameplay Cue Batching）](#concepts-gc-batching)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [手动RPC（Manual RPC）](#concepts-gc-batching-manualrpc)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [一个游戏效果上的多个游戏提示（Multiple GCs on one GE）](#concepts-gc-batching-gcsonge)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [游戏提示事件（Gameplay Cue Events）](#concepts-gc-events)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [游戏提示可靠性（Gameplay Cue Reliability）](#concepts-gc-reliability)
>    4.9 [能力系统全局设置（Ability System Globals）](#concepts-asg)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)
>    4.10 [预测（Prediction）](#concepts-p)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [预测密钥（Prediction Key）](#concepts-p-key)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [在能力中创建新的预测窗口（Creating New Prediction Windows in Abilities）](#concepts-p-windows)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [预测性生成Actor（Predictively Spawning Actors）](#concepts-p-spawn)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [GAS中预测的未来（Future of Prediction in GAS）](#concepts-p-future)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [网络预测插件（Network Prediction Plugin）](#concepts-p-npp)
>    4.11 [目标选择（Targeting）](#concepts-targeting)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [目标数据（Target Data）](#concepts-targeting-data)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [目标Actor（Target Actors）](#concepts-targeting-actors)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [目标数据过滤器（Target Data Filters）](#concepts-target-data-filters)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [游戏能力世界准星（Gameplay Ability World Reticles）](#concepts-targeting-reticles)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [游戏效果容器目标选择（Gameplay Effect Containers Targeting）](#concepts-targeting-containers)
> 1. [常见的能力和效果实现（Commonly Implemented Abilities and Effects）](#cae)
>    5.1 [眩晕（Stun）](#cae-stun)
>    5.2 [冲刺（Sprint）](#cae-sprint)
>    5.3 [瞄准（Aim Down Sights）](#cae-ads)
>    5.4 [生命偷取（Lifesteal）](#cae-ls)
>    5.5 [在客户端和服务器上生成随机数（Generating a Random Number on Client and Server）](#cae-random)
>    5.6 [暴击（Critical Hits）](#cae-crit)
>    5.7 [非堆叠游戏效果但只有最大幅度实际影响目标（Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target）](#cae-nonstackingge)
>    5.8 [在游戏暂停时生成目标数据（Generate Target Data While Game is Paused）](#cae-paused)
>    5.9 [一键交互系统（One Button Interaction System）](#cae-onebuttoninteractionsystem)
> 1. [调试GAS（Debugging GAS）](#debugging)
>    6.1 [showdebug abilitysystem](#debugging-sd)
>    6.2 [游戏调试器（Gameplay Debugger）](#debugging-gd)
>    6.3 [GAS日志（GAS Logging）](#debugging-log)
> 1. [优化（Optimizations）](#optimizations)
>    7.1 [能力批处理（Ability Batching）](#optimizations-abilitybatching)
>    7.2 [游戏提示批处理（Gameplay Cue Batching）](#optimizations-gameplaycuebatching)
>    7.3 [能力系统组件复制模式（AbilitySystemComponent Replication Mode）](#optimizations-ascreplicationmode)
>    7.4 [属性代理复制（Attribute Proxy Replication）](#optimizations-attributeproxyreplication)
>    7.5 [ASC延迟加载（ASC Lazy Loading）](#optimizations-asclazyloading)
> 1. [生活质量建议（Quality of Life Suggestions）](#qol)
>    8.1 [游戏效果容器（Gameplay Effect Containers）](#qol-gameplayeffectcontainers)
>    8.2 [蓝图异步任务绑定到ASC委托（Blueprint AsyncTasks to Bind to ASC Delegates）](#qol-asynctasksascdelegates)
> 1. [故障排除（Troubleshooting）](#troubleshooting)
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)
>    9.2 [`ScriptStructCache` 错误](#troubleshooting-scriptstructcache)
>    9.3 [动画蒙太奇未复制到客户端（Animation Montages are not replicating to clients）](#troubleshooting-replicatinganimmontages)
>    9.4 [复制蓝图Actor导致属性集被设置为nullptr（Duplicating Blueprint Actors is setting AttributeSets to nullptr）](#troubleshooting-duplicatingblueprintactors)
>    9.5 [unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)](#troubleshooting-unresolvedexternalsymbolmarkpropertydirty)
>    9.6 [枚举名称现在用路径名表示（Enum names are now represented by path name）](#troubleshooting-enumnamesarenowpathnames)
> 1. [常见GAS缩写（Common GAS Acronyms）](#acronyms)
> 1. [其他资源（Other Resources）](#resources)
>    11.1 [与Epic Games的Dave Ratti的问答（Q&A With Epic Game's Dave Ratti）](#resources-daveratti)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [社区问题1（Community Questions 1）](#resources-daveratti-community1)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [社区问题2（Community Questions 2）](#resources-daveratti-community2)
> 1. [GAS更新日志（GAS Changelog）](#changelog)
>    * [5.3](#changelog-5.3)
>    * [5.2](#changelog-5.2)
>    * [5.1](#changelog-5.1)
>    * [5.0](#changelog-5.0)
>    * [4.27](#changelog-4.27)
>    * [4.26](#changelog-4.26)
>    * [4.25.1](#changelog-4.25.1)
>    * [4.25](#changelog-4.25)
>    * [4.24](#changelog-4.24)

<a name="intro"></a>
## 1. GameplayAbilitySystem 插件简介
来自[官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)：
>游戏能力系统（Gameplay Ability System）是一个高度灵活的框架，用于构建你在 RPG 或 MOBA 类游戏中可能遇到的能力和属性。你可以为游戏中的角色构建主动或被动能力，实现因这些动作而逐渐积累或消退的各种属性的状态效果（Status Effects），实现"冷却（Cooldown）"计时器或资源消耗来限制这些动作的使用频率，在每个等级改变能力的等级及其效果，激活粒子或音效，等等。简而言之，这个系统可以帮助你设计、实现并高效地在游戏中网络同步各种能力，从简单的跳跃到你最喜欢的角色在任何现代 RPG 或 MOBA 游戏中的完整能力集。

GameplayAbilitySystem 插件由 Epic Games 开发，随虚幻引擎一同提供。它已在 Paragon 和 Fortnite 等 AAA 商业游戏中经过了实战检验。

该插件为单人和多人游戏提供了开箱即用的解决方案，包括：
* 实现带有可选消耗和冷却的基于等级的角色能力或技能（[GameplayAbilities](#concepts-ga)）
* 操作属于 Actor 的数值 `Attributes`（[属性（Attributes）](#concepts-a)）
* 对 Actor 施加状态效果（[GameplayEffects](#concepts-ge)）
* 对 Actor 应用 `GameplayTags`（[游戏标签（GameplayTags）](#concepts-gt)）
* 生成视觉或音效效果（[GameplayCues](#concepts-gc)）
* 以上所有内容的网络同步（Replication）

在多人游戏中，GAS 提供以下内容的[客户端预测（Client-side Prediction）](#concepts-p)支持：
* 能力激活（Ability Activation）
* 播放动画蒙太奇（Animation Montages）
* 对 `Attributes` 的更改
* 应用 `GameplayTags`
* 生成 `GameplayCues`
* 通过连接到 `CharacterMovementComponent` 的 `RootMotionSource` 函数进行移动。

**GAS 必须在 C++ 中设置**，但 `GameplayAbilities` 和 `GameplayEffects` 可以由设计师在蓝图（Blueprint）中创建。

GAS 目前存在的问题：
* `GameplayEffect` 延迟调和（Latency Reconciliation）（无法预测能力冷却，导致延迟较高的玩家在低冷却能力上的射速低于延迟较低的玩家）。
* 无法预测 `GameplayEffects` 的移除。但我们可以通过预测添加具有相反效果的 `GameplayEffects` 来有效地移除它们。这并不总是合适或可行的，仍然是一个问题。
* 缺乏样板模板、多人游戏示例和文档。希望本文档对此有所帮助！

**[⬆ 返回顶部](#table-of-contents)**

<a name="sp"></a>
## 2. 示例项目
本文档附带了一个多人第三人称射击示例项目，面向不熟悉 GameplayAbilitySystem 插件但已有虚幻引擎使用经验的开发者。用户应具备 C++、蓝图（Blueprints）、UMG、网络同步（Replication）以及 UE 其他中级主题的知识。此项目提供了一个示例，展示如何为玩家/AI 控制的英雄在 `PlayerState` 类上设置 `AbilitySystemComponent`（`ASC`），以及为 AI 控制的小兵在 `Character` 类上设置 `ASC`，从而建立一个基本的第三人称射击多人就绪项目。

目标是保持项目简洁，同时展示 GAS 基础知识并演示一些常见需求的能力，附带详细注释的代码。由于面向初学者，本项目不涉及[预测弹道（Predicting Projectiles）](#concepts-p-spawn)等高级主题。

演示的概念：
* `ASC` 放在 `PlayerState` 上 vs `Character` 上
* 可同步的 `Attributes`
* 可同步的动画蒙太奇（Animation Montages）
* `GameplayTags`
* 在 `GameplayAbilities` 内部和外部应用及移除 `GameplayEffects`
* 应用经护甲减免的伤害来改变角色生命值
* `GameplayEffectExecutionCalculations`
* 眩晕效果（Stun Effect）
* 死亡与重生
* 在服务器上通过能力生成 Actor（弹道）
* 通过瞄准和冲刺预测性地改变本地玩家速度
* 冲刺时持续消耗耐力
* 使用法力施放能力
* 被动能力（Passive Abilities）
* 堆叠 `GameplayEffects`
* 目标选取（Targeting Actors）
* 在蓝图（Blueprint）中创建的 `GameplayAbilities`
* 在 C++ 中创建的 `GameplayAbilities`
* 按 `Actor` 实例化的 `GameplayAbilities`
* 非实例化的 `GameplayAbilities`（跳跃）
* 静态 `GameplayCues`（开火弹道撞击粒子效果）
* Actor `GameplayCues`（冲刺和眩晕粒子效果）

英雄类拥有以下能力：

| 能力                    | 输入绑定          | 可预测  | C++ / 蓝图 | 描述                                                                                                                                                                  |
| -------------------------- | ------------------- | ---------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 跳跃（Jump）                       | 空格键           | 是        | C++             | 使英雄跳跃。                                                                                                                                                         |
| 枪械（Gun）                        | 鼠标左键   | 否         | C++             | 从英雄的枪中发射弹道。动画可预测，但弹道不可预测。                                                                                |
| 瞄准（Aim Down Sights）            | 鼠标右键  | 是        | 蓝图（Blueprint）       | 按住按钮时，英雄移动变慢，摄像机放大以便用枪进行更精确的射击。                                                    |
| 冲刺（Sprint）                     | 左 Shift          | 是        | 蓝图（Blueprint）       | 按住按钮时，英雄跑得更快，同时消耗耐力。                                                                                                         |
| 前冲（Forward Dash）               | Q                   | 是        | 蓝图（Blueprint）       | 英雄向前冲刺，消耗耐力。                                                                              |
| 被动护甲叠加（Passive Armor Stacks）       | 被动（Passive）             | 否         | 蓝图（Blueprint）       | 每 4 秒英雄获得一层护甲叠加，最多 4 层。受到伤害时移除一层护甲。                                                    |
| 陨石（Meteor）                     | R                   | 否         | 蓝图（Blueprint）       | 玩家选择一个位置投下陨石，对敌人造成伤害并使其眩晕。目标选取可预测，但陨石生成不可预测。                     |

`GameplayAbilities` 是在 C++ 还是蓝图（Blueprint）中创建并不重要。这里混合使用了两种方式，以展示如何在每种语言中实现它们。

小兵没有预定义的 `GameplayAbilities`。红色小兵拥有更高的生命恢复，而蓝色小兵拥有更高的初始生命值。

对于 `GameplayAbility` 的命名，我使用后缀 `_BP` 表示 `GameplayAbility` 的逻辑是在蓝图（Blueprint）中创建的。没有后缀意味着逻辑是在 C++ 中创建的。

**蓝图资产命名前缀（Blueprint Asset Naming Prefixes）**

| 前缀      | 资产类型          |
| ----------- | ------------------- |
| GA_         | GameplayAbility     |
| GC_         | GameplayCue         |
| GE_         | GameplayEffect      |

**[⬆ 返回顶部](#table-of-contents)**

<a name="setup"></a>
## 3. 使用 GAS 设置项目
使用 GAS 设置项目的基本步骤：
1. 在编辑器中启用 GameplayAbilitySystem 插件
1. 编辑 `YourProjectName.Build.cs`，将 `"GameplayAbilities", "GameplayTags", "GameplayTasks"` 添加到你的 `PrivateDependencyModuleNames` 中
1. 刷新/重新生成 Visual Studio 项目文件
1. 从 4.24 到 5.2 版本，必须调用 `UAbilitySystemGlobals::Get().InitGlobalData()` 才能使用 [`TargetData`](#concepts-targeting-data)。示例项目在 `UAssetManager::StartInitialLoading()` 中执行此调用。从 5.3 版本开始会自动调用。更多信息请参阅 [`InitGlobalData()`](#concepts-asg-initglobaldata)。

这就是启用 GAS 所需的全部操作。接下来，将 [`ASC`](#concepts-asc) 和 [`AttributeSet`](#concepts-as) 添加到你的 `Character` 或 `PlayerState` 中，然后开始创建 [`GameplayAbilities`](#concepts-ga) 和 [`GameplayEffects`](#concepts-ge) 吧！

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts"></a>
## 4. GAS 概念

#### 章节目录

> 4.1 [能力系统组件（Ability System Component）](#concepts-asc)
> 4.2 [游戏标签（Gameplay Tags）](#concepts-gt)
> 4.3 [属性（Attributes）](#concepts-a)
> 4.4 [属性集（Attribute Set）](#concepts-as)
> 4.5 [游戏效果（Gameplay Effects）](#concepts-ge)
> 4.6 [游戏能力（Gameplay Abilities）](#concepts-ga)
> 4.7 [能力任务（Ability Tasks）](#concepts-at)
> 4.8 [游戏提示（Gameplay Cues）](#concepts-gc)
> 4.9 [能力系统全局设置（Ability System Globals）](#concepts-asg)
> 4.10 [预测（Prediction）](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 能力系统组件（Ability System Component）
`AbilitySystemComponent`（`ASC`）是 GAS 的核心。它是一个 `UActorComponent`（[`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)），负责处理与系统的所有交互。任何希望使用[`游戏能力（GameplayAbilities）`](#concepts-ga)、拥有[`属性（Attributes）`](#concepts-a)或接收[`游戏效果（GameplayEffects）`](#concepts-ge)的 `Actor` 都必须附加一个 `ASC`。这些对象都存在于 `ASC` 内部，并由其管理和复制（Replication）（`属性（Attributes）`除外，它们由各自的[`属性集（AttributeSet）`](#concepts-as)进行复制）。开发者可以但不强制要求对其进行子类化。

附加了 `ASC` 的 `Actor` 被称为 `ASC` 的 `OwnerActor`。`ASC` 的物理表示 `Actor` 被称为 `AvatarActor`。`OwnerActor` 和 `AvatarActor` 可以是同一个 `Actor`，例如在 MOBA 游戏中简单的 AI 小兵。它们也可以是不同的 `Actor`，例如在 MOBA 游戏中玩家控制的英雄，其中 `OwnerActor` 是 `PlayerState`，而 `AvatarActor` 是英雄的 `Character` 类。大多数 `Actor` 会将 `ASC` 放在自身上。如果你的 `Actor` 会重生，并且需要在重生之间保持`属性（Attributes）`或`游戏效果（GameplayEffects）`的持久性（例如 MOBA 中的英雄），那么 `ASC` 的理想位置是放在 `PlayerState` 上。

**注意：** 如果你的 `ASC` 在 `PlayerState` 上，那么你需要增加 `PlayerState` 的 `NetUpdateFrequency`。`PlayerState` 上的默认值非常低，可能会导致`属性（Attributes）`和`游戏标签（GameplayTags）`等内容在客户端上发生变化时出现延迟或感知到的卡顿。请确保启用[`自适应网络更新频率（Adaptive Network Update Frequency）`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency)，Fortnite 就使用了它。

`OwnerActor` 和 `AvatarActor`（如果是不同的 `Actor`）都应该实现 `IAbilitySystemInterface`。这个接口有一个必须重写的函数：`UAbilitySystemComponent* GetAbilitySystemComponent() const`，它返回指向其 `ASC` 的指针。`ASC` 之间在系统内部通过查找这个接口函数来相互交互。

`ASC` 将其当前活跃的`游戏效果（GameplayEffects）`保存在 `FActiveGameplayEffectsContainer ActiveGameplayEffects` 中。

`ASC` 将其已授予的`游戏能力（Gameplay Abilities）`保存在 `FGameplayAbilitySpecContainer ActivatableAbilities` 中。任何时候当你计划遍历 `ActivatableAbilities.Items` 时，请确保在循环上方添加 `ABILITYLIST_SCOPE_LOCK();` 来锁定列表，防止其发生变化（由于移除能力）。每个作用域内的 `ABILITYLIST_SCOPE_LOCK();` 都会递增 `AbilityScopeLockCount`，当离开作用域时再递减。不要在 `ABILITYLIST_SCOPE_LOCK();` 的作用域内尝试移除能力（清除能力的函数会在内部检查 `AbilityScopeLockCount`，以防止在列表被锁定时移除能力）。

<a name="concepts-asc-rm"></a>
### 4.1.1 复制模式（Replication Mode）
`ASC` 定义了三种不同的复制模式（Replication Mode）来复制`游戏效果（GameplayEffects）`、`游戏标签（GameplayTags）`和`游戏提示（GameplayCues）`——`Full`、`Mixed` 和 `Minimal`。`属性（Attributes）`由其`属性集（AttributeSet）`进行复制。

| 复制模式（Replication Mode） | 使用场景                                | 描述                                                                                                                           |
| ------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Full`             | 单人游戏                                | 每个`游戏效果（GameplayEffect）`都会复制到每个客户端。                                                                          |
| `Mixed`            | 多人游戏，玩家控制的 `Actor`             | `游戏效果（GameplayEffects）`仅复制到拥有者客户端。只有`游戏标签（GameplayTags）`和`游戏提示（GameplayCues）`会复制给所有人。     |
| `Minimal`          | 多人游戏，AI 控制的 `Actor`              | `游戏效果（GameplayEffects）`不会复制给任何人。只有`游戏标签（GameplayTags）`和`游戏提示（GameplayCues）`会复制给所有人。         |

**注意：** `Mixed` 复制模式要求 `OwnerActor` 的 `Owner` 是 `Controller`。`PlayerState` 的 `Owner` 默认是 `Controller`，但 `Character` 的不是。如果在 `OwnerActor` 不是 `PlayerState` 的情况下使用 `Mixed` 复制模式，则需要在 `OwnerActor` 上使用有效的 `Controller` 调用 `SetOwner()`。

从 4.24 版本开始，`PossessedBy()` 现在会将 `Pawn` 的所有者设置为新的 `Controller`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 设置与初始化（Setup and Initialization）
`ASC` 通常在 `OwnerActor` 的构造函数中创建，并显式标记为可复制。**这必须在 C++ 中完成。**

```c++
AGDPlayerState::AGDPlayerState()
{
	// Create ability system component, and set it to be explicitly replicated
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

`ASC` 需要在服务器和客户端上使用其 `OwnerActor` 和 `AvatarActor` 进行初始化。你需要在 `Pawn` 的 `Controller` 设置之后（即被占有之后）进行初始化。单人游戏只需要关注服务器路径。

对于 `ASC` 位于 `Pawn` 上的玩家控制角色，我通常在服务器端的 `Pawn` 的 `PossessedBy()` 函数中进行初始化，在客户端的 `PlayerController` 的 `AcknowledgePossession()` 函数中进行初始化。

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode replication requires that the ASC Owner's Owner be the Controller.
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

对于 `ASC` 位于 `PlayerState` 上的玩家控制角色，我通常在服务器端的 `Pawn` 的 `PossessedBy()` 函数中进行初始化，在客户端的 `Pawn` 的 `OnRep_PlayerState()` 函数中进行初始化。这确保了 `PlayerState` 在客户端上已经存在。

```c++
// Server only
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC on the Server. Clients do this in OnRep_PlayerState()
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI won't have PlayerControllers so we can init again here just to be sure. No harm in initing twice for heroes that have PlayerControllers.
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}

	//...
}
```

```c++
// Client only
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC for clients. Server does this in PossessedBy.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// Init ASC Actor Info for clients. Server will init its ASC when it possesses a new Actor.
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

如果你遇到错误信息 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`，说明你没有在客户端上初始化你的 `ASC`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gt"></a>
### 4.2 游戏标签（Gameplay Tags）
[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html) 是以 `Parent.Child.Grandchild...` 形式注册在 `GameplayTagManager` 中的层级名称。这些标签对于分类和描述对象的状态非常有用。例如，如果一个角色被眩晕了，我们可以在眩晕持续期间给它一个 `State.Debuff.Stun` `游戏标签（GameplayTag）`。

你会发现自己会用`游戏标签（GameplayTags）`来替代以前用布尔值或枚举处理的内容，并对对象是否拥有某些`游戏标签（GameplayTags）`进行布尔逻辑判断。

当给对象添加标签时，如果对象有 `ASC`，我们通常将标签添加到其 `ASC` 上，以便 GAS 可以与它们交互。`UAbilitySystemComponent` 实现了 `IGameplayTagAssetInterface`，提供了访问其拥有的`游戏标签（GameplayTags）`的函数。

多个`游戏标签（GameplayTags）`可以存储在 `FGameplayTagContainer` 中。相比使用 `TArray<FGameplayTag>`，使用 `GameplayTagContainer` 更为推荐，因为 `GameplayTagContainers` 添加了一些效率优化。虽然标签是标准的 `FNames`，但如果在项目设置中启用了`快速复制（Fast Replication）`，它们可以在 `FGameplayTagContainers` 中高效地打包在一起进行复制（Replication）。`快速复制（Fast Replication）`要求服务器和客户端拥有相同的`游戏标签（GameplayTags）`列表。这通常不会成为问题，所以你应该启用此选项。`GameplayTagContainers` 也可以返回一个 `TArray<FGameplayTag>` 用于迭代。

存储在 `FGameplayTagCountContainer` 中的`游戏标签（GameplayTags）`有一个 `TagMap`，用于存储该`游戏标签（GameplayTag）`的实例数量。一个 `FGameplayTagCountContainer` 可能仍然包含该`游戏标签（GameplayTag）`，但其 `TagMapCount` 为零。如果一个 `ASC` 仍然有某个`游戏标签（GameplayTag）`，你可能在调试时遇到这种情况。任何 `HasTag()` 或 `HasMatchingTag()` 或类似函数都会检查 `TagMapCount`，如果`游戏标签（GameplayTag）`不存在或其 `TagMapCount` 为零，则返回 false。

`游戏标签（GameplayTags）`必须在 `DefaultGameplayTags.ini` 中预先定义。虚幻引擎编辑器在项目设置中提供了一个界面，让开发者可以管理`游戏标签（GameplayTags）`，无需手动编辑 `DefaultGameplayTags.ini`。`游戏标签（GameplayTag）`编辑器可以创建、重命名、搜索引用和删除`游戏标签（GameplayTags）`。

![项目设置中的游戏标签编辑器](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

搜索`游戏标签（GameplayTag）`引用将在编辑器中调出常见的`引用查看器（Reference Viewer）`图表，显示所有引用该`游戏标签（GameplayTag）`的资产。但这不会显示引用该`游戏标签（GameplayTag）`的任何 C++ 类。

重命名`游戏标签（GameplayTags）`会创建一个重定向，这样仍然引用原始`游戏标签（GameplayTag）`的资产可以重定向到新的`游戏标签（GameplayTag）`。我更倾向于在可能的情况下创建一个新的`游戏标签（GameplayTag）`，手动将所有引用更新到新的`游戏标签（GameplayTag）`，然后删除旧的`游戏标签（GameplayTag）`，以避免创建重定向。

除了`快速复制（Fast Replication）`之外，`游戏标签（GameplayTag）`编辑器还有一个选项可以填写常用的复制`游戏标签（GameplayTags）`以进一步优化它们。

如果`游戏标签（GameplayTags）`是从`游戏效果（GameplayEffect）`添加的，则它们会被复制。`ASC` 允许你添加不会被复制且必须手动管理的 `LooseGameplayTags`。示例项目使用 `LooseGameplayTag` 来表示 `State.Dead`，这样拥有者客户端可以在其生命值降到零时立即做出响应。重生时手动将 `TagMapCount` 设置回零。只有在使用 `LooseGameplayTags` 时才手动调整 `TagMapCount`。使用 `UAbilitySystemComponent::AddLooseGameplayTag()` 和 `UAbilitySystemComponent::RemoveLooseGameplayTag()` 函数比手动调整 `TagMapCount` 更为推荐。

在 C++ 中获取`游戏标签（GameplayTag）`的引用：
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

对于高级的`游戏标签（GameplayTag）`操作，例如获取父级或子级`游戏标签（GameplayTags）`，请查看 `GameplayTagManager` 提供的函数。要访问 `GameplayTagManager`，请包含 `GameplayTagManager.h` 并使用 `UGameplayTagManager::Get().FunctionName` 进行调用。`GameplayTagManager` 实际上将`游戏标签（GameplayTags）`存储为关系节点（父级、子级等），以实现比持续字符串操作和比较更快的处理速度。

`游戏标签（GameplayTags）`和 `GameplayTagContainers` 可以使用可选的 `UPROPERTY` 说明符 `Meta = (Categories = "GameplayCue")` 来在蓝图（Blueprint）中过滤标签，仅显示父标签为 `GameplayCue` 的`游戏标签（GameplayTags）`。当你知道`游戏标签（GameplayTag）`或 `GameplayTagContainer` 变量应该仅用于`游戏提示（GameplayCues）`时，这非常有用。

此外，还有一个单独的结构体叫做 `FGameplayCueTag`，它封装了一个 `FGameplayTag`，并且会自动在蓝图中过滤`游戏标签（GameplayTags）`，仅显示父标签为 `GameplayCue` 的标签。

如果你想在函数中过滤`游戏标签（GameplayTag）`参数，请使用 `UFUNCTION` 说明符 `Meta = (GameplayTagFilter = "GameplayCue")`。函数中的 `GameplayTagContainer` 参数无法被过滤。如果你想修改引擎以支持此功能，请查看 `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp` 中 `SGameplayTagGraphPin::ParseDefaultValueData()` 如何调用 `FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);` 并在 `SGameplayTagGraphPin::GetListContent()` 中将 `FilterString` 传递给 `SGameplayTagWidget`。`Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp` 中这些函数的 `GameplayTagContainer` 版本不检查元字段属性，也不传递过滤器。

示例项目广泛使用了`游戏标签（GameplayTags）`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gt-change"></a>
### 4.2.1 响应游戏标签的变化（Responding to Changes in Gameplay Tags）
`ASC` 提供了一个委托（Delegate），用于在`游戏标签（GameplayTags）`被添加或移除时触发。它接收一个 `EGameplayTagEventType`，可以指定仅在`游戏标签（GameplayTag）`被添加/移除时触发，或在`游戏标签（GameplayTag）`的 `TagMapCount` 发生任何变化时触发。

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

回调函数有一个`游戏标签（GameplayTag）`参数和一个新的 `TagCount` 参数。
```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gt-loadfromplugin"></a>
### 4.2.2 从插件 .ini 文件加载游戏标签（Loading Gameplay Tags from Plugin .ini Files）
如果你创建了一个带有自己 .ini 文件且包含`游戏标签（GameplayTags）`的插件，你可以在插件的 `StartupModule()` 函数中加载该插件的`游戏标签（GameplayTag）` .ini 目录。

例如，这是虚幻引擎自带的 CommonConversation 插件的做法：

```c++
void FCommonConversationRuntimeModule::StartupModule()
{
	TSharedPtr<IPlugin> ThisPlugin = IPluginManager::Get().FindPlugin(TEXT("CommonConversation"));
	check(ThisPlugin.IsValid());

	UGameplayTagsManager::Get().AddTagIniSearchPath(ThisPlugin->GetBaseDir() / TEXT("Config") / TEXT("Tags"));

	//...
}
```

这会查找 `Plugins\CommonConversation\Config\Tags` 目录，并在引擎启动时（如果插件已启用）将其中包含`游戏标签（GameplayTags）`的任何 .ini 文件加载到你的项目中。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 属性（Attributes）

<a name="concepts-a-definition"></a>
#### 4.3.1 属性定义（Attribute Definition）
`属性（Attributes）`是由结构体 [`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html) 定义的浮点值。它们可以表示任何内容，从角色的生命值到角色的等级，再到药水的充能次数。如果它是属于某个 `Actor` 的与游戏玩法相关的数值，你应该考虑使用`属性（Attribute）`来表示它。`属性（Attributes）`通常应该只通过[`游戏效果（GameplayEffects）`](#concepts-ge)来修改，这样 ASC 才能对变化进行[预测（Predict）](#concepts-p)。

`属性（Attributes）`由[`属性集（AttributeSet）`](#concepts-as)定义并存在于其中。`属性集（AttributeSet）`负责复制标记为需要复制的`属性（Attributes）`。请参阅[`属性集（AttributeSets）`](#concepts-as)章节了解如何定义`属性（Attributes）`。

**提示：** 如果你不想让某个`属性（Attribute）`出现在编辑器的`属性（Attributes）`列表中，可以使用 `Meta = (HideInDetailsView)` `属性说明符（property specifier）`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 基础值与当前值（BaseValue vs CurrentValue）
一个`属性（Attribute）`由两个值组成——`基础值（BaseValue）`和`当前值（CurrentValue）`。`基础值（BaseValue）`是`属性（Attribute）`的永久值，而`当前值（CurrentValue）`是`基础值（BaseValue）`加上`游戏效果（GameplayEffects）`带来的临时修改。例如，你的`角色（Character）`可能有一个移动速度`属性（Attribute）`，其`基础值（BaseValue）`为 600 单位/秒。由于目前没有`游戏效果（GameplayEffects）`修改移动速度，`当前值（CurrentValue）`也是 600 单位/秒。如果她获得了 50 单位/秒的临时移动速度增益，`基础值（BaseValue）`保持不变为 600 单位/秒，而`当前值（CurrentValue）`现在变为 600 + 50，总计 650 单位/秒。当移动速度增益过期时，`当前值（CurrentValue）`恢复为`基础值（BaseValue）`的 600 单位/秒。

GAS 初学者经常会将`基础值（BaseValue）`与`属性（Attribute）`的最大值混淆，并尝试将其作为最大值来使用。这是不正确的做法。可以变化的或在能力（Abilities）或 UI 中被引用的`属性（Attributes）`的最大值应该被视为单独的`属性（Attributes）`。对于硬编码的最大值和最小值，可以使用 `FAttributeMetaData` 定义一个 `DataTable` 来设置最大值和最小值，但 Epic 在该结构体上方的注释称其为"进行中的工作"。更多信息请参见 `AttributeSet.h`。为了避免混淆，我建议将能力或 UI 中可引用的最大值设为单独的`属性（Attributes）`，而仅用于钳制`属性（Attributes）`的硬编码最大值和最小值则在`属性集（AttributeSet）`中定义为硬编码的浮点值。`属性（Attributes）`的钳制在 [PreAttributeChange()](#concepts-as-preattributechange) 中讨论了`当前值（CurrentValue）`的变化，在 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute) 中讨论了`游戏效果（GameplayEffects）`对`基础值（BaseValue）`的变化。

对`基础值（BaseValue）`的永久更改来自`即时（Instant）``游戏效果（GameplayEffects）`，而`持续（Duration）`和`无限（Infinite）``游戏效果（GameplayEffects）`则更改`当前值（CurrentValue）`。周期性（Periodic）`游戏效果（GameplayEffects）`被视为即时`游戏效果（GameplayEffects）`，会更改`基础值（BaseValue）`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 4.3.3 元属性（Meta Attributes）
一些`属性（Attributes）`被当作临时值的占位符，用于与其他`属性（Attributes）`交互。这些被称为`元属性（Meta Attributes）`。例如，我们通常将伤害定义为`元属性（Meta Attribute）`。与其让`游戏效果（GameplayEffect）`直接更改我们的生命值`属性（Attribute）`，不如使用一个名为伤害的`元属性（Meta Attribute）`作为占位符。这样，伤害值可以在[`游戏效果执行计算（GameplayEffectExecutionCalculation）`](#concepts-ge-ec)中通过增益和减益进行修改，还可以在`属性集（AttributeSet）`中进一步操作，例如从当前护盾`属性（Attribute）`中减去伤害，然后将剩余部分从生命值`属性（Attribute）`中扣除。伤害`元属性（Meta Attribute）`在`游戏效果（GameplayEffects）`之间没有持久性，每次都会被覆盖。`元属性（Meta Attributes）`通常不会被复制。

`元属性（Meta Attributes）`为伤害和治疗等内容提供了良好的逻辑分离，区分了"我们造成了多少伤害？"和"我们如何处理这些伤害？"。这种逻辑分离意味着我们的`游戏效果（Gameplay Effects）`和`执行计算（Execution Calculations）`不需要知道目标如何处理伤害。继续以伤害为例，`游戏效果（Gameplay Effect）`确定伤害量，然后`属性集（AttributeSet）`决定如何处理该伤害。并非所有角色都拥有相同的`属性（Attributes）`，特别是当你使用子类化的`属性集（AttributeSets）`时。基础`属性集（AttributeSet）`类可能只有生命值`属性（Attribute）`，但子类化的`属性集（AttributeSet）`可能会添加护盾`属性（Attribute）`。拥有护盾`属性（Attribute）`的子类化`属性集（AttributeSet）`将以不同于基础`属性集（AttributeSet）`类的方式分配所受伤害。

虽然`元属性（Meta Attributes）`是一种良好的设计模式，但它们并非必需。如果你只有一个`执行计算（Execution Calculation）`用于所有伤害实例，并且所有角色共享一个`属性集（Attribute Set）`类，那么你可以在`执行计算（Execution Calculation）`内部将伤害分配到生命值、护盾等，并直接修改这些`属性（Attributes）`。你只是牺牲了灵活性，但这对你来说可能是可以接受的。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 响应属性变化（Responding to Attribute Changes）
要监听`属性（Attribute）`何时发生变化以更新 UI 或其他游戏玩法，请使用 `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`。此函数返回一个委托（Delegate），你可以绑定到该委托上，当`属性（Attribute）`发生变化时，它将自动调用。该委托提供一个 `FOnAttributeChangeData` 参数，包含 `NewValue`、`OldValue` 和 `FGameplayEffectModCallbackData`。**注意：** `FGameplayEffectModCallbackData` 仅在服务器上被设置。

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

示例项目在 `GDPlayerState` 上绑定了`属性（Attribute）`值变化委托，用于更新 HUD 以及在生命值降为零时响应玩家死亡。

示例项目中包含了一个将此功能封装为 `ASyncTask` 的自定义蓝图节点。它在 `UI_HUD` UMG 控件（Widget）中用于更新生命值、法力值和体力值。这个 `AsyncTask` 会永久存在，直到手动调用 `EndTask()`，我们在 UMG 控件的 `Destruct` 事件中执行此操作。请参阅 `AsyncTaskAttributeChanged.h/cpp`。

![监听属性变化蓝图节点](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ 返回顶部](#table-of-contents)**
<a name="concepts-a-derived"></a>
#### 4.3.5 派生属性（Derived Attributes）
要创建一个部分或全部值从一个或多个其他`Attribute`派生而来的`Attribute`，可以使用带有一个或多个`Attribute Based`或 [`MMC`](#concepts-ge-mmc) [`修改器（Modifiers）`](#concepts-ge-mods)的`Infinite` `GameplayEffect`。当`派生属性（Derived Attribute）`所依赖的`Attribute`更新时，它会自动更新。

`派生属性（Derived Attribute）`上所有`修改器（Modifiers）`的最终公式与`修改器聚合器（Modifier Aggregators）`的公式相同。如果你需要计算按特定顺序进行，请在`MMC`内部完成所有计算。

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

**注意：** 如果在PIE中使用多个客户端进行测试，你需要在编辑器偏好设置中禁用`Run Under One Process`，否则`派生属性（Derived Attributes）`在除第一个客户端以外的其他客户端上更新其独立的`Attributes`时，不会自动更新。

在本示例中，我们有一个`Infinite` `GameplayEffect`，它根据`Attributes` `TestAttrB`和`TestAttrC`，通过公式`TestAttrA = (TestAttrA + TestAttrB) * ( 2 * TestAttrC)`来派生`TestAttrA`的值。`TestAttrA`会在任何`Attributes`更新其值时自动重新计算。

![Derived Attribute Example](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 属性集（Attribute Set）

<a name="concepts-as-definition"></a>
#### 4.4.1 属性集定义（Attribute Set Definition）
`AttributeSet`定义、持有和管理`Attributes`的变更。开发者应从[`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html)进行子类化。在`OwnerActor`的构造函数中创建`AttributeSet`会自动将其注册到其`ASC`。**这必须在C++中完成**。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 属性集设计（Attribute Set Design）
一个`ASC`可以拥有一个或多个`AttributeSets`。AttributeSets的内存开销可以忽略不计，因此使用多少个`AttributeSets`是由开发者决定的组织性决策。

可以使用一个大型的单体`AttributeSet`，由游戏中的每个`Actor`共享，仅在需要时使用属性（Attributes），同时忽略未使用的属性（Attributes）。

或者，你可以选择拥有多个`AttributeSet`，代表不同的`Attributes`分组，根据需要有选择地将它们添加到你的`Actors`中。例如，你可以有一个用于生命值相关`Attributes`的`AttributeSet`，一个用于法力值相关`Attributes`的`AttributeSet`，等等。在MOBA游戏中，英雄可能需要法力值，但小兵可能不需要。因此英雄会获得法力值`AttributeSet`，而小兵则不会。

此外，`AttributeSets`可以被子类化，作为另一种有选择地选择`Actor`拥有哪些`Attributes`的方式。`Attributes`在内部被引用为`AttributeSetClassName.AttributeName`。当你对`AttributeSet`进行子类化时，父类中的所有`Attributes`仍然会使用父类的名称作为前缀。

虽然你可以拥有多个`AttributeSet`，但不应该在一个`ASC`上拥有多个相同类的`AttributeSet`。如果你有多个来自同一类的`AttributeSet`，系统将不知道使用哪个`AttributeSet`，只会随机选择一个。

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 具有独立属性的子组件（Subcomponents with Individual Attributes）
在`Pawn`上有多个可受伤组件的场景中（如可独立受伤的护甲部件），我建议如果你知道`Pawn`可能拥有的最大可受伤组件数量，就在一个`AttributeSet`上创建相应数量的生命值`Attributes` —— DamageableCompHealth0、DamageableCompHealth1等 —— 来表示这些可受伤组件的逻辑"插槽"。在你的可受伤组件类实例中，分配可被`GameplayAbilities`或[`Executions`](#concepts-ge-ec)读取的插槽编号`Attribute`，以确定将伤害应用到哪个`Attribute`。拥有少于最大数量或零个可受伤组件的`Pawns`也没有问题。仅仅因为`AttributeSet`有一个`Attribute`，并不意味着你必须使用它。未使用的`Attributes`只占用极少量的内存。

如果你的子组件各自需要很多`Attributes`，子组件数量可能无限，子组件可以分离并被其他玩家使用（例如武器），或者由于任何其他原因此方法不适合你，我建议从`Attributes`切换为在组件上存储普通的浮点数。参见[物品属性（Item Attributes）](#concepts-as-design-itemattributes)。

<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 在运行时添加和移除属性集（Adding and Removing AttributeSets at Runtime）
`AttributeSets`可以在运行时从`ASC`添加和移除；但是，移除`AttributeSets`可能是危险的。例如，如果在服务器之前在客户端上移除了一个`AttributeSet`，而一个`Attribute`值变更被复制到客户端，该`Attribute`将找不到其`AttributeSet`并导致游戏崩溃。

将武器添加到库存时：
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

将武器从库存移除时：
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```
<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 物品属性（Item Attributes）（武器弹药）
有几种方法可以实现带有`Attributes`的可装备物品（武器弹药、护甲耐久度等）。所有这些方法都直接在物品上存储值。对于在其生命周期内可以被多个玩家装备的物品来说，这是必要的。

> 1. 在物品上使用普通浮点数（**推荐**）
> 1. 物品上的独立`AttributeSet`
> 1. 物品上的独立`ASC`

<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 物品上的普通浮点数（Plain Floats on the Item）
不使用`Attributes`，而是在物品类实例上存储普通浮点值。Fortnite和[GASShooter](https://github.com/tranek/GASShooter)就是用这种方式处理枪械弹药的。对于枪械，直接在枪械实例上将最大弹夹容量、弹夹中的当前弹药、储备弹药等存储为可复制的浮点数（`COND_OwnerOnly`）。如果武器共享储备弹药，你可以将储备弹药作为`Attribute`移到角色上的共享弹药`AttributeSet`中（装弹技能可以使用`Cost GE`从储备弹药中取出弹药放入枪械的浮点弹夹弹药中）。由于你没有为当前弹夹弹药使用`Attributes`，你需要重写`UGameplayAbility`中的一些函数来检查和应用枪械浮点数上的消耗。在授予技能时将枪械设为[`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec)中的`SourceObject`，意味着你可以在技能内部访问授予该技能的枪械。

为了防止枪械在自动射击期间复制回弹药数量并覆盖本地弹药数量，请在`PreReplication()`中当玩家拥有`IsFiring` `GameplayTag`时禁用复制。你本质上是在这里进行自己的本地预测。

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

优点：
1. 避免了使用`AttributeSets`的限制（见下文）

缺点：
1. 无法使用现有的`GameplayEffect`工作流（用于弹药消耗的`Cost GEs`等）
1. 需要额外工作来重写`UGameplayAbility`上的关键函数，以检查和应用枪械浮点数上的弹药消耗

<a name="concepts-as-design-itemattributes-attributeset"></a>
###### 4.4.2.3.2 物品上的`AttributeSet`
在物品上使用独立的`AttributeSet`，[在将物品添加到玩家库存时将其添加到玩家的`ASC`](#concepts-as-design-addremoveruntime)，这种方式可以工作，但有一些重大限制。我在早期版本的[GASShooter](https://github.com/tranek/GASShooter)中为武器弹药实现了这一功能。武器将其`Attributes`（如最大弹夹容量、弹夹中的当前弹药、储备弹药等）存储在武器类上的`AttributeSet`中。如果武器共享储备弹药，你可以将储备弹药移到角色上的共享弹药`AttributeSet`中。当武器在服务器上被添加到玩家库存时，武器会将其`AttributeSet`添加到玩家的`ASC::SpawnedAttributes`中。然后服务器会将此复制到客户端。如果武器从库存中移除，它会从`ASC::SpawnedAttributes`中移除其`AttributeSet`。

当`AttributeSet`存在于`OwnerActor`以外的东西上（比如武器）时，你最初会在`AttributeSet`中遇到一些编译错误。修复方法是在`BeginPlay()`中而不是在构造函数中构造`AttributeSet`，并在武器上实现`IAbilitySystemInterface`（在将武器添加到玩家库存时设置指向`ASC`的指针）。

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

你可以通过查看这个[旧版本的GASShooter](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735)来看到实际应用。

优点：
1. 可以使用现有的`GameplayAbility`和`GameplayEffect`工作流（用于弹药消耗的`Cost GEs`等）
1. 对于非常少量的物品来说设置简单

缺点：
1. 你必须为每种武器类型创建一个新的`AttributeSet`类。`ASCs`在功能上每个类只能有一个`AttributeSet`实例，因为对`Attribute`的更改会在`ASCs`的`SpawnedAttributes`数组中查找其`AttributeSet`类的第一个实例。相同`AttributeSet`类的额外实例会被忽略。
1. 由于前面提到的每个`AttributeSet`类只能有一个实例的原因，玩家库存中每种类型的武器只能有一个。
1. 移除`AttributeSet`是危险的。在GASShooter中，如果玩家被火箭弹炸死，玩家会立即从库存中移除火箭发射器（包括从`ASC`中移除其`AttributeSet`）。当服务器复制火箭发射器的弹药`Attribute`发生变化时，`AttributeSet`在客户端的`ASC`上已经不存在了，游戏就会崩溃。

<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 物品上的`ASC`
在每个物品上放置完整的`AbilitySystemComponent`是一种极端的方法。我个人没有这样做过，也没有在实际项目中见过。要使其工作需要大量的工程投入。

> 在拥有相同所有者但不同化身的情况下，是否可行拥有多个AbilitySystemComponents（例如在Pawn和武器/物品/投射物上，Owner设置为PlayerState）？
>
> 我看到的第一个问题是在拥有者Actor上实现IGameplayTagAssetInterface和IAbilitySystemInterface。前者可能是可行的：只需聚合所有ASCs的标签（但请注意——HasAllMatchingGameplayTags可能只能通过跨ASC聚合来满足。仅仅将调用转发给每个ASC并将结果进行OR运算是不够的）。但后者更棘手：哪个ASC是权威的？如果有人想应用一个GE——哪个应该接收它？也许你可以解决这些问题，但问题的这一面将是最困难的：所有者下面会有多个ASCs。
>
> 在Pawn和武器上分别使用独立的ASCs本身是有意义的。例如，区分描述武器的标签和描述拥有者Pawn的标签。也许授予武器的标签也"应用"到所有者是有意义的，而其他东西不会（例如，属性和GEs是独立的，但所有者会像我上面描述的那样聚合拥有的标签）。这可以行得通，我确信。但拥有相同所有者的多个ASCs可能会变得棘手。

*Dave Ratti来自Epic对[社区问题#6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)的回答*

优点：
1. 可以使用现有的`GameplayAbility`和`GameplayEffect`工作流（用于弹药消耗的`Cost GEs`等）
1. 可以复用`AttributeSet`类（每个武器的ASC上各一个）

缺点：
1. 未知的工程成本
1. 是否真的可行？

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 定义属性（Defining Attributes）
**`Attributes`只能在C++中定义**，在`AttributeSet`的头文件中。建议在每个`AttributeSet`头文件的顶部添加以下宏代码块。它将自动为你的`Attributes`生成getter和setter函数。

```c++
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

一个可复制的生命值属性（Attribute）定义如下：

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

同样在头文件中定义`OnRep`函数：
```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

`AttributeSet`的.cpp文件应使用预测系统所用的`GAMEPLAYATTRIBUTE_REPNOTIFY`宏来填充`OnRep`函数：
```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

最后，需要将`Attribute`添加到`GetLifetimeReplicatedProps`中：
```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always`告诉`OnRep`函数在本地值已经等于从服务器复制下来的值时也触发（由于预测）。默认情况下，如果本地值与从服务器复制下来的值相同，则不会触发`OnRep`函数。

如果`Attribute`不需要复制（如`元属性（Meta Attribute）`），则可以跳过`OnRep`和`GetLifetimeReplicatedProps`步骤。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 4.4.4 初始化属性（Initializing Attributes）
有多种方法可以初始化`Attributes`（将其`BaseValue`以及相应的`CurrentValue`设置为某个初始值）。Epic推荐使用即时`GameplayEffect`。示例项目中也使用了这种方法。

参见示例项目中的`GE_HeroAttributes`蓝图，了解如何创建即时`GameplayEffect`来初始化`Attributes`。此`GameplayEffect`的应用在C++中完成。

如果你在定义`Attributes`时使用了`ATTRIBUTE_ACCESSORS`宏，`AttributeSet`上会自动为每个`Attribute`生成一个初始化函数，你可以在C++中随时调用。

```c++
// InitHealth(float InitialValue) is an automatically generated function for an Attribute 'Health' defined with the `ATTRIBUTE_ACCESSORS` macro
AttributeSet->InitHealth(100.0f);
```

参见`AttributeSet.h`了解更多初始化`Attributes`的方法。

**注意：** 在4.24之前，`FAttributeSetInitterDiscreteLevels`不支持`FGameplayAttributeData`。它是在`Attributes`还是原始浮点数时创建的，会抱怨`FGameplayAttributeData`不是`Plain Old Data`（`POD`）。这在4.24中已修复 https://issues.unrealengine.com/issue/UE-76557。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()
`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)`是`AttributeSet`中响应`Attribute`的`CurrentValue`变更的主要函数之一，在变更发生之前触发。它是通过引用参数`NewValue`对`CurrentValue`的传入变更进行限制（clamp）的理想位置。

例如，示例项目中对移动速度修改器的限制如下：
```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// Cannot slow less than 150 units/s and cannot boost more than 1000 units/s
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```
`GetMoveSpeedAttribute()`函数由我们添加到`AttributeSet.h`中的宏代码块创建（[定义属性（Defining Attributes）](#concepts-as-attributes)）。

这会在`Attributes`的任何变更时触发，无论是使用`Attribute` setter（由`AttributeSet.h`中的宏代码块定义（[定义属性（Defining Attributes）](#concepts-as-attributes)））还是使用[`GameplayEffects`](#concepts-ge)。

**注意：** 这里发生的任何限制（clamping）都不会永久改变`ASC`上的修改器（modifier）。它只改变查询修改器（modifier）时返回的值。这意味着任何从所有修改器（modifiers）重新计算`CurrentValue`的东西，如[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)和[`ModifierMagnitudeCalculations`](#concepts-ge-mmc)，都需要再次实现限制（clamping）。

**注意：** Epic对`PreAttributeChange()`的注释说不要将其用于游戏事件，而主要用于限制（clamping）。`Attribute`变更时的游戏事件推荐使用`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`（[响应属性变更（Responding to Attribute Changes）](#concepts-a-changes)）。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()
`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)`仅在即时[`GameplayEffect`](#concepts-ge)改变`Attribute`的`BaseValue`后触发。这是在`Attribute`因`GameplayEffect`而改变时进行更多`Attribute`操作的有效位置。

例如，在示例项目中，我们在这里从生命值`Attribute`中减去最终伤害`元属性（Meta Attribute）`。如果有护盾`Attribute`，我们会先从护盾中减去伤害，然后再从生命值中减去剩余部分。示例项目还使用此位置来应用受击反应动画、显示浮动伤害数字，以及向击杀者分配经验和金币奖励。按照设计，伤害`元属性（Meta Attribute）`始终通过即时`GameplayEffect`传入，而不是通过`Attribute` setter。

其他只会通过即时`GameplayEffects`改变`BaseValue`的`Attributes`（如法力值和耐力值）也可以在这里被限制（clamp）到其对应的最大值`Attributes`。

**注意：** 当`PostGameplayEffectExecute()`被调用时，对`Attribute`的更改已经发生，但尚未复制回客户端，因此在这里限制（clamping）值不会导致向客户端发送两次网络更新。客户端只会在限制（clamping）之后收到更新。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()
`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)`在为此集合中的`Attribute`创建`聚合器（Aggregator）`时触发。它允许自定义设置[`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html)。`AggregatorEvaluateMetaData`由`聚合器（Aggregator）`在根据所有应用的[`修改器（Modifiers）`](#concepts-ge-mods)评估`Attribute`的`CurrentValue`时使用。默认情况下，`AggregatorEvaluateMetaData`仅被`聚合器（Aggregator）`用于确定哪些`修改器（Modifiers）`符合条件，例如`MostNegativeMod_AllPositiveMods`允许所有正向`修改器（Modifiers）`，但将负向`修改器（Modifiers）`限制为仅最负面的那一个。Paragon使用这个功能来确保无论玩家身上有多少减速效果，都只应用最负面的移动速度减速效果，同时应用所有正面的移动速度增益。不符合条件的`修改器（Modifiers）`仍然存在于`ASC`上，只是不会被聚合到最终的`CurrentValue`中。当条件改变时，它们可能会在之后符合条件，例如当最负面的`修改器（Modifier）`过期时，下一个最负面的`修改器（Modifier）`（如果存在的话）就会符合条件。

要在只允许最负面的`修改器（Modifier）`和所有正向`修改器（Modifiers）`的示例中使用AggregatorEvaluateMetaData：

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

你自定义的用于限定条件的`AggregatorEvaluateMetaData`应作为静态变量添加到`FAggregatorEvaluateMetaDataLibrary`中。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 游戏效果（Gameplay Effects）

<a name="concepts-ge-definition"></a>
#### 4.5.1 游戏效果定义（Gameplay Effect Definition）
[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html)（`GE`）是技能改变自身和其他对象的[`属性（Attributes）`](#concepts-a)和[`游戏标签（GameplayTags）`](#concepts-gt)的载体。它们可以造成即时的`属性（Attribute）`变化，如伤害或治疗，也可以施加长期的增益/减益状态效果，如移动速度提升或眩晕。`UGameplayEffect` 类被设计为一个**纯数据**类，用于定义单个游戏效果（Gameplay Effect）。不应向 `GameplayEffects` 添加额外的逻辑。通常设计师会创建许多 `UGameplayEffect` 的蓝图子类。

`GameplayEffects` 通过[`修改器（Modifiers）`](#concepts-ge-mods)和[`执行（Executions）`（`GameplayEffectExecutionCalculation`）](#concepts-ge-ec)来改变`属性（Attributes）`。

`GameplayEffects` 有三种持续时间（Duration）类型：`即时（Instant）`、`持续（Duration）`和`无限（Infinite）`。

此外，`GameplayEffects` 可以添加/执行[`游戏提示（GameplayCues）`](#concepts-gc)。`即时（Instant）`的`游戏效果（GameplayEffect）`会在`游戏提示（GameplayCue）`的`游戏标签（GameplayTags）`上调用 `Execute`，而`持续（Duration）`或`无限（Infinite）`的`游戏效果（GameplayEffect）`会在`游戏提示（GameplayCue）`的`游戏标签（GameplayTags）`上调用 `Add` 和 `Remove`。

| 持续时间类型（Duration Type） | 游戏提示事件（GameplayCue Event） | 使用场景                                                                                                                                                                                                                                |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Instant`     | Execute           | 用于对`属性（Attribute）`的`基础值（BaseValue）`进行即时的永久性更改。`游戏标签（GameplayTags）`不会被应用，甚至一帧都不会。                                                                                                                    |
| `Duration`    | Add & Remove      | 用于对`属性（Attribute）`的`当前值（CurrentValue）`进行临时更改，以及应用`游戏标签（GameplayTags）`——这些标签将在`游戏效果（GameplayEffect）`过期或被手动移除时被移除。持续时间在 `UGameplayEffect` 类/蓝图中指定。       |
| `Infinite`    | Add & Remove      | 用于对`属性（Attribute）`的`当前值（CurrentValue）`进行临时更改，以及应用`游戏标签（GameplayTags）`——这些标签将在`游戏效果（GameplayEffect）`被移除时被移除。它们永远不会自行过期，必须由技能或`ASC`手动移除。 |

`持续（Duration）`和`无限（Infinite）`的`游戏效果（GameplayEffects）`可以选择应用`周期性效果（Periodic Effects）`，按照其`周期（Period）`定义的每 `X` 秒应用其`修改器（Modifiers）`和`执行（Executions）`。`周期性效果（Periodic Effects）`在改变`属性（Attribute）`的`基础值（BaseValue）`和`执行（Executing）``游戏提示（GameplayCues）`时被视为`即时（Instant）`的`游戏效果（GameplayEffects）`。这对于持续伤害（DOT）类型的效果很有用。**注意：**`周期性效果（Periodic Effects）`不能被[预测（predicted）](#concepts-p)。

`持续（Duration）`和`无限（Infinite）`的`游戏效果（GameplayEffects）`在应用后，如果其`持续标签要求（Ongoing Tag Requirements）`未满足/已满足（[游戏效果标签（Gameplay Effect Tags）](#concepts-ge-tags)），则可以被临时关闭和开启。关闭一个`游戏效果（GameplayEffect）`会移除其`修改器（Modifiers）`和已应用的`游戏标签（GameplayTags）`的效果，但不会移除该`游戏效果（GameplayEffect）`本身。重新开启`游戏效果（GameplayEffect）`会重新应用其`修改器（Modifiers）`和`游戏标签（GameplayTags）`。

如果你需要手动重新计算`持续（Duration）`或`无限（Infinite）`的`游戏效果（GameplayEffect）`的`修改器（Modifiers）`（例如你有一个使用非`属性（Attributes）`数据的`MMC`），你可以使用 `UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()` 获取当前等级，然后用相同的等级调用 `UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)`。基于后备`属性（Attributes）`的`修改器（Modifiers）`会在这些后备`属性（Attributes）`更新时自动更新。`SetActiveGameplayEffectLevel()` 用于更新`修改器（Modifiers）`的关键函数是：

```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// Private function otherwise we'd call these three functions without needing to set the level to what it already is
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffects` 通常不会被直接实例化。当技能或`ASC`想要应用一个`游戏效果（GameplayEffect）`时，它会从`游戏效果（GameplayEffect）`的`类默认对象（ClassDefaultObject）`创建一个[`游戏效果规格（GameplayEffectSpec）`](#concepts-ge-spec)。成功应用的`GameplayEffectSpecs`随后会被添加到一个名为 `FActiveGameplayEffect` 的新结构体中，这是`ASC`在一个名为 `ActiveGameplayEffects` 的特殊容器结构体中所追踪的内容。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-applying"></a>
#### 4.5.2 应用游戏效果（Applying Gameplay Effects）
`GameplayEffects` 可以通过[`游戏技能（GameplayAbilities）`](#concepts-ga)上的函数和`ASC`上的函数以多种方式应用，通常采用 `ApplyGameplayEffectTo` 的形式。不同的函数本质上是便利函数，最终都会在`目标（Target）`上调用 `UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()`。

要在`游戏技能（GameplayAbility）`之外应用`GameplayEffects`，例如从投射物（projectile）中应用，你需要获取`目标（Target）`的`ASC`并使用其函数之一来调用 `ApplyGameplayEffectToSelf`。

你可以通过绑定委托来监听任何`持续（Duration）`或`无限（Infinite）`的`GameplayEffects`何时被应用到`ASC`上：
```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
回调函数：
```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

服务器无论复制模式如何都会始终调用此函数。自主代理（autonomous proxy）仅在`完整（Full）`和`混合（Mixed）`复制模式下对已复制的`GameplayEffects`调用此函数。模拟代理（simulated proxies）仅在`完整（Full）`[复制模式（replication mode）](#concepts-asc-rm)下调用此函数。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-removing"></a>
#### 4.5.3 移除游戏效果（Removing Gameplay Effects）
`GameplayEffects` 可以通过[`游戏技能（GameplayAbilities）`](#concepts-ga)上的函数和`ASC`上的函数以多种方式移除，通常采用 `RemoveActiveGameplayEffect` 的形式。不同的函数本质上是便利函数，最终都会在`目标（Target）`上调用 `FActiveGameplayEffectsContainer::RemoveActiveEffects()`。

要在`游戏技能（GameplayAbility）`之外移除`GameplayEffects`，你需要获取`目标（Target）`的`ASC`并使用其函数之一来调用 `RemoveActiveGameplayEffect`。

你可以通过绑定委托来监听任何`持续（Duration）`或`无限（Infinite）`的`GameplayEffects`何时从`ASC`上被移除：
```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```
回调函数：
```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

服务器无论复制模式如何都会始终调用此函数。自主代理（autonomous proxy）仅在`完整（Full）`和`混合（Mixed）`复制模式下对已复制的`GameplayEffects`调用此函数。模拟代理（simulated proxies）仅在`完整（Full）`[复制模式（replication mode）](#concepts-asc-rm)下调用此函数。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 4.5.4 游戏效果修改器（Gameplay Effect Modifiers）
`修改器（Modifiers）`改变`属性（Attribute）`，并且是[预测性地（predictively）](#concepts-p)改变`属性（Attribute）`的唯一方式。一个`游戏效果（GameplayEffect）`可以有零个或多个`修改器（Modifiers）`。每个`修改器（Modifier）`负责通过指定的操作只改变一个`属性（Attribute）`。

| 操作（Operation）  | 描述                                                                                                         |
| ---------- | ------------------------------------------------------------------------------------------------------------------- |
| `Add`      | 将结果加到`修改器（Modifier）`指定的`属性（Attribute）`上。使用负值进行减法运算。                    |
| `Multiply` | 将结果乘以`修改器（Modifier）`指定的`属性（Attribute）`。                                                    |
| `Divide`   | 将结果除以`修改器（Modifier）`指定的`属性（Attribute）`。                                                  |
| `Override` | 用结果覆盖`修改器（Modifier）`指定的`属性（Attribute）`。                                                   |

`属性（Attribute）`的`当前值（CurrentValue）`是其所有`修改器（Modifiers）`加到其`基础值（BaseValue）`上的聚合结果。`修改器（Modifiers）`的聚合公式在 `GameplayEffectAggregator.cpp` 中的 `FAggregatorModChannel::EvaluateWithBase` 中定义如下：
```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

任何`覆盖（Override）`的`修改器（Modifiers）`会用最后应用的`修改器（Modifier）`的值覆盖最终值（最后应用的优先）。

**注意：** 对于基于百分比的变化，请确保使用`乘法（Multiply）`操作，以使其在加法之后发生。

**注意：**[预测（Prediction）](#concepts-p)在处理百分比变化时会遇到问题。

有四种类型的`修改器（Modifiers）`：可缩放浮点数（Scalable Float）、基于属性（Attribute Based）、自定义计算类（Custom Calculation Class）和调用方设置（Set By Caller）。它们都会生成某个浮点值，然后根据`修改器（Modifier）`的操作来改变其指定的`属性（Attribute）`。

| `修改器（Modifier）`类型            | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scalable Float`           | `FScalableFloats` 是一种结构体，可以指向一个以变量为行、等级为列的数据表（Data Table）。可缩放浮点数（Scalable Floats）会自动读取技能当前等级（或���[`游戏效果规格（GameplayEffectSpec）`](#concepts-ge-spec)上覆盖的不同等级）下指定表行的值。此值可以进一步被系数修改。如果没有指定数据表/行，则将该值视为 1，因此系数可以用于在所有等级硬编码一个固定值。![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png)                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `Attribute Based`          | `基于属性（Attribute Based）`的`修改器（Modifiers）`取`来源（Source）`（创建`GameplayEffectSpec`者）或`目标（Target）`（接收`GameplayEffectSpec`者）上的后备`属性（Attribute）`的`当前值（CurrentValue）`或`基础值（BaseValue）`，并通过系数以及系数前后的加值进一步修改它。`快照（Snapshotting）`意味着后备`属性（Attribute）`在`GameplayEffectSpec`创建时被捕获，而不使用快照则意味着`属性（Attribute）`在`GameplayEffectSpec`应用时被捕获。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `Custom Calculation Class` | `自定义计算类（Custom Calculation Class）`为复杂的`修改器（Modifiers）`提供了最大的灵活性。此`修改器（Modifier）`使用一个[`ModifierMagnitudeCalculation`](#concepts-ge-mmc)类，并可以通过系数以及系数前后的加值进一步操纵结果浮点值。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `Set By Caller`            | `SetByCaller` 的`修改器（Modifiers）`是在运行时由技能或创建`GameplayEffectSpec`的任何对象在`游戏效果（GameplayEffect）`之外设置的值。例如，如果你想根据玩家按住按钮充能技能的时间来设置伤害，你就会使用`SetByCaller`。`SetByCallers` 本质上是存在于`GameplayEffectSpec`上的 `TMap<FGameplayTag, float>`。`修改器（Modifier）`只是告诉`聚合器（Aggregator）`查找与提供的`游戏标签（GameplayTag）`关联的`SetByCaller`值。`修改器（Modifiers）`使用的`SetByCallers`只能使用`游戏标签（GameplayTag）`版本的概念，`FName`版本在此被禁用。如果`修改器（Modifier）`被设置为`SetByCaller`，但`GameplayEffectSpec`上不存在具有正确`游戏标签（GameplayTag）`的`SetByCaller`，游戏将抛出运行时错误并返回值 0。在`除法（Divide）`操作的情况下，这可能会导致问题。有关如何使用`SetByCallers`的更多信息，请参阅[`SetByCallers`](#concepts-ge-spec-setbycaller)。 |

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>
##### 4.5.4.1 乘法和除法修改器（Multiply and Divide Modifiers）
默认情况下，所有`乘法（Multiply）`和`除法（Divide）`的`修改器（Modifiers）`在乘以或除以`属性（Attribute）`的`基础值（BaseValue）`之前会先相加在一起。

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}
```

```c++
float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```
*来自 `GameplayEffectAggregator.cpp`*

`乘法（Multiply）`和`除法（Divide）`的`修改器（Modifiers）`在此公式中的`偏移值（Bias）`为 `1`（`加法（Addition）`的`偏移值（Bias）`为 `0`）。所以它看起来类似于：

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

此公式会导致一些意想不到的结果。首先，此公式在乘以或除以`基础值（BaseValue）`之前会先将所���修改器相加。大多数人期望它们是相乘或相除的。例如，如果你有两个值为 `1.5` 的`乘法（Multiply）`修改器，大多数人会期望`基础值（BaseValue）`被乘以 `1.5 x 1.5 = 2.25`。但实际上，此公式将两个 `1.5` 相加，使`基础值（BaseValue）`乘以 `2`（`50% 增加 + 另外 50% 增加 = 100% 增加`）。这源自 `GameplayPrediction.h` 中的示例：`500` 基础速度上的 `10%` 速度增益为 `550`。再加一个 `10%` 速度增益，结果将是 `600`。

其次，此公式有一些未记录的规则，规定了可以使用的值，因为它是为 Paragon 设计的。

`乘法（Multiply）`和`除法（Divide）`的乘法加法公式规则：
* `（不超过一个值 < 1）且（任意数量的值在 [1, 2) 范围内）`
* `或（一个值 >= 2）`

公式中的`偏移值（Bias）`基本上从 `[1, 2)` 范围内的数字中减去整数位。第一个`修改器（Modifier）`的`偏移值（Bias）`从起始 `Sum` 值（在循环之前设置为`偏移值（Bias）`）中减去，这就是为什么任何单独的值都有效，以及为什么一个 `< 1` 的值可以与 `[1, 2)` 范围内的数字一起工作。

一些`乘法（Multiply）`的示例：
乘数：`0.5`
`1 + (0.5 - 1) = 0.5`，正确

乘数：`0.5, 0.5`
`1 + (0.5 - 1) + (0.5 - 1) = 0`，不正确，期望是 `1`？多个小于 `1` 的值对于乘法修改器的叠加没有意义。Paragon 被设计为仅使用[`乘法（Multiply）``修改器（Modifiers）`中最大的负值](#cae-nonstackingge)，因此乘入`基础值（BaseValue）`的小于 `1` 的值最多只会有一个。

乘数：`1.1, 0.5`
`1 + (0.5 - 1) + (1.1 - 1) = 0.6`，正确

乘数：`5, 5`
`1 + (5 - 1) + (5 - 1) = 9`，不正确，期望是 `10`。结果总是 `修改器之和 - 修改器数量 + 1`。

许多游戏会希望其`乘法（Multiply）`和`除法（Divide）`的`修改器（Modifiers）`在应用到`基础值（BaseValue）`之前先相互相乘和相除。要实现这一点，你需要**修改引擎代码**中的 `FAggregatorModChannel::EvaluateWithBase()`。

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	float Division = MultiplyMods(Mods[EGameplayModOp::Division], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}
```

```c++
float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>
##### 4.5.4.2 修改器上的游戏标签（Gameplay Tags on Modifiers）

可以为每个[修改器（Modifier）](#concepts-ge-mods)设置 `SourceTags` 和 `TargetTags`。它们的工作方式与`游戏效果（GameplayEffect）`的[`应用标签要求（Application Tag Requirements）`](#concepts-ge-tags)相同。因此，标签仅在效果应用时被考虑。即，当存在周期性的无限效果时，它们仅在效果首次应用时被考虑，而*不是*在每次周期性执行时。

`基于属性（Attribute Based）`的修改器还可以设置 `SourceTagFilter` 和 `TargetTagFilter`。在确定作为`基于属性（Attribute Based）`修改器来源的属性的量值时，这些过滤器用于排除该属性上的某些修改器。来源或目标没有包含过滤器所有标签的修改器会被排除。

更详细地说：来源 ASC 和目标 ASC 的标签由 `GameplayEffects` 捕获。来源 ASC 标签在`GameplayEffectSpec`创建时被捕获，目标 ASC 标签在效果执行时被捕获。在确定无限或持续效果的修改器是否"有资格"被应用（即其聚合器（Aggregator）是否有资格）且这些过滤器已设置时，被捕获的标签会与过滤器进行比较。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-stacking"></a>
#### 4.5.5 游戏效果堆叠（Stacking Gameplay Effects）
`GameplayEffects` 默认会应用新的`GameplayEffectSpec`实例，这些实例在应用时不知道也不关心之前已存在的`GameplayEffectSpec`实例。`GameplayEffects` 可以设置为堆叠（Stacking），此时不会添加新的`GameplayEffectSpec`实例，而是改变当前已存在的`GameplayEffectSpec`的堆叠计数。堆叠仅适用于`持续（Duration）`和`无限（Infinite）`的`游戏效果（GameplayEffects）`。

有两种堆叠类型：按来源聚合（Aggregate by Source）和按目标聚合（Aggregate by Target）。

| 堆叠类型（Stacking Type）       | 描述                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| 按来源聚合（Aggregate by Source） | 在目标（Target）上，每个来源`ASC`有一个独立的堆叠实例。每个来源可以应用 X 层堆叠。                     |
| 按目标聚合（Aggregate by Target） | 无论来源是什么，目标（Target）上只有一个堆叠实例。每个来源可以应用堆叠，直到共享堆叠上限。 |

堆叠还有过期、持续时间刷新和周期重置的策略。它们在`游戏效果（GameplayEffect）`蓝图中有有用的悬停提示工具。

示例项目包含一个自定义蓝图节点，用于监听`游戏效果（GameplayEffect）`堆叠变化。HUD UMG 控件使用它来更新玩家拥有的被动护甲堆叠数量。此`异步任务（AsyncTask）`将一直存在，直到手动调用 `EndTask()`，我们在 UMG 控件的 `Destruct` 事件中执行此操作。请参阅 `AsyncTaskEffectStackChanged.h/cpp`。

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 4.5.6 授予的技能（Granted Abilities）
`GameplayEffects` 可以向`ASC`授予新的[`游戏技能（GameplayAbilities）`](#concepts-ga)。只有`持续（Duration）`和`无限（Infinite）`的`游戏效果（GameplayEffects）`可以授予技能。

此功能的一个常见用例是当你想强制另一个玩家执行某些操作时，例如通过击退或拉拽移动他们。你可以对他们应用一个`游戏效果（GameplayEffect）`，该效果授予他们一个自动激活的技能（请参阅[被动技能（Passive Abilities）](#concepts-ga-activating-passive)了解如何在授予技能时自动激活它），该技能对他们执行所需的操作。

设计师可以选择`游戏效果（GameplayEffect）`授予哪些技能、授予的等级、[绑定的输入](#concepts-ga-input)以及授予技能的移除策略。

| 移除策略（Removal Policy）             | 描述                                                                                                                                                                     |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 立即取消技能（Cancel Ability Immediately） | 当授予该技能的`游戏效果（GameplayEffect）`从目标（Target）上被移除时，被授予的技能会立即被取消并移除。                                                   |
| 结束时移除技能（Remove Ability on End）      | 被授予的技能允许完成执行，然后从目标（Target）上被移除。                                                                                                   |
| 不做任何操作（Do Nothing）                 | 被授予的技能不受授予`游戏效果（GameplayEffect）`从目标（Target）移除的影响。目标（Target）将永久拥有该技能，直到稍后被手动移除。 |

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 4.5.7 游戏效果标签（Gameplay Effect Tags）
`GameplayEffects` 携带多个[`游戏标签容器（GameplayTagContainers）`](#concepts-gt)。设计师将编辑每个类别的`已添加（Added）`和`已移除（Removed）`的`游戏标签容器（GameplayTagContainers）`，编译时结果会显示在`合并（Combined）`的`游戏标签容器（GameplayTagContainer）`中。`已添加（Added）`标签是此`游戏效果（GameplayEffect）`添加的、其父类之前没有的新标签。`已移除（Removed）`标签是父类拥有但此子类没有的标签。

| 类别                          | 描述                                                                                                                                                                                                                                                                                                                                                                        |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 游戏效果资产标签（Gameplay Effect Asset Tags）        | `游戏效果（GameplayEffect）`拥有的标签。它们本身不执行任何功能，仅用于描述`游戏效果（GameplayEffect）`。                                                                                                                                                                                                                                                                        |
| 授予标签（Granted Tags）                      | 存在于`游戏效果（GameplayEffect）`上的标签，同时也会被赋予应用了`游戏效果（GameplayEffect）`的`ASC`。当`游戏效果（GameplayEffect）`被移除时，它们也会从`ASC`上被移除。这仅适用于`持续（Duration）`和`无限（Infinite）`的`游戏效果（GameplayEffects）`。                                                                                                                             |
| 持续标签要求（Ongoing Tag Requirements）          | 应用后，这些标签决定`游戏效果（GameplayEffect）`是开启还是关闭。`游戏效果（GameplayEffect）`可以在仍然被应用的状态下处于关闭状态。如果`游戏效果（GameplayEffect）`因未满足持续标签要求（Ongoing Tag Requirements）而关闭，但随后要求被满足，`游戏效果（GameplayEffect）`将重新开启并重新应用其修改器。这仅适用于`持续（Duration）`和`无限（Infinite）`的`游戏效果（GameplayEffects）`。 |
| 应用标签要求（Application Tag Requirements）      | 目标（Target）上的标签，用于确定是否可以将`游戏效果（GameplayEffect）`应用到目标（Target）上。如果不满足这些要求，`游戏效果（GameplayEffect）`将不会被应用。                                                                                                                                                                                                                      |
| 移除带有标签的游戏效果（Remove Gameplay Effects with Tags） | 当此`游戏效果（GameplayEffect）`成功应用时，目标（Target）上在其`资产标签（Asset Tags）`或`授予标签（Granted Tags）`中包含这些标签的`游戏效果（GameplayEffects）`将从目标（Target）上被移除。                                                                                                                                                                                            |

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 免疫（Immunity）
`GameplayEffects` 可以基于[`游戏标签（GameplayTags）`](#concepts-gt)授予免疫，有效地阻止其他`游戏效果（GameplayEffects）`的应用。虽然免疫可以通过其他方式有效实现（如`应用标签要求（Application Tag Requirements）`），但使用此系统会在`游戏效果（GameplayEffects）`因免疫而被阻止时提供一个委托 `UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`。

`GrantedApplicationImmunityTags` 检查来源`ASC`（包括来自来源技能的 `AbilityTags` 的标签，如果有的话）是否具有任何指定的标签。这是一种基于标签为来自特定角色或来源的所有`游戏效果（GameplayEffects）`提供免疫的方式。

`Granted Application Immunity Query` 检查传入的`GameplayEffectSpec`是否匹配任何查询，以阻止或允许其应用。

查询在`游戏效果（GameplayEffect）`蓝图中有有用的悬停提示工具。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 4.5.9 游戏效果规格（Gameplay Effect Spec）
[`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html)（`GESpec`）可以被视为`GameplayEffects`的实例化。它们持有对其所代表的`游戏效果（GameplayEffect）`类的引用、创建时的等级以及创建者。这些可以在运行时自由创建和修改后再应用，不像`GameplayEffects`应该在运行时之前由设计师创建。当应用`游戏效果（GameplayEffect）`时，会从`游戏效果（GameplayEffect）`创建一个`GameplayEffectSpec`，实际应用到目标（Target）上的就是它。

`GameplayEffectSpecs` 使用 `UAbilitySystemComponent::MakeOutgoingSpec()` 从 `GameplayEffects` 创建，该函数为 `BlueprintCallable`。`GameplayEffectSpecs` 不必立即应用。通常会将 `GameplayEffectSpec` 传递给技能创建的投射物，由投射物稍后应用到命中的目标上。当 `GameplayEffectSpecs` 成功应用时，会返回一个名为 `FActiveGameplayEffect` 的新结构体。

值得注意的`GameplayEffectSpec`内容：
* 创建此`游戏效果（GameplayEffect）`的`游戏效果（GameplayEffect）`类。
* 此`GameplayEffectSpec`的等级。通常与创建`GameplayEffectSpec`的技能等级相同，但可以不同。
* `GameplayEffectSpec`的持续时间。默认为`游戏效果（GameplayEffect）`的持续时间，但可以不同。
* 周期性效果（Periodic Effects）的`GameplayEffectSpec`的周期。默认为`游戏效果（GameplayEffect）`的周期，但可以不同。
* 此`GameplayEffectSpec`的当前堆叠计数。堆叠上限在`游戏效果（GameplayEffect）`上。
* [`游戏效果上下文句柄（GameplayEffectContextHandle）`](#concepts-ge-context)告诉我们谁创建了此`GameplayEffectSpec`。
* 由于快照（Snapshotting）在`GameplayEffectSpec`创建时捕获的`属性（Attributes）`。
* `DynamicGrantedTags`——`GameplayEffectSpec`除了`游戏效果（GameplayEffect）`授予的`游戏标签（GameplayTags）`之外，额外授予目标（Target）的标签。
* `DynamicAssetTags`——`GameplayEffectSpec`除了`游戏效果（GameplayEffect）`拥有的`资产标签（AssetTags）`之外，额外拥有的标签。
* `SetByCaller` 的 `TMaps`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 SetByCallers
`SetByCallers` 允许`GameplayEffectSpec`携带与`游戏标签（GameplayTag）`或`FName`关联的浮点值。它们分别存储在`GameplayEffectSpec`上各自的`TMaps`中：`TMap<FGameplayTag, float>` 和 `TMap<FName, float>`。这些可以用作`游戏效果（GameplayEffect）`上的`修改器（Modifiers）`，或作为传递浮点值的通用手段。通常通过`SetByCallers`将技能内部生成的数值数据传递给[`游戏效果执行计算（GameplayEffectExecutionCalculations）`](#concepts-ge-ec)或[`修改器量值计算（ModifierMagnitudeCalculations）`](#concepts-ge-mmc)。

| `SetByCaller` 用途 | 备注                                                                                                                                                                                                                                                                                                                                                                                                                               || ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Modifiers`       | 必须在 `GameplayEffect` 类中预先定义。只能使用 `GameplayTag` 版本。如果在 `GameplayEffect` 类中定义了一个，但 `GameplayEffectSpec` 没有对应的标签和浮点值对，游戏将在应用 `GameplayEffectSpec` 时产生运行时错误并返回 0。对于 `Divide` 操作来说这是一个潜在问题。参见[`修改器（Modifiers）`](#concepts-ge-mods)。 |
| 其他地方         | 不需要在任何地方预先定义。读取一个在 `GameplayEffectSpec` 上不存在的 `SetByCaller` 可以返回一个开发者定义的默认值，并可选地给出警告。                                                                                                                                                                                      |

要在蓝图（Blueprint）中分配 `SetByCaller` 值，请使用你需要的版本（`GameplayTag` 或 `FName`）对应的蓝图节点：

![分配 SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

要在蓝图中读取 `SetByCaller` 值，你需要在蓝图库（Blueprint Library）中创建自定义节点。

要在 C++ 中分配 `SetByCaller` 值，请使用你需要的版本（`GameplayTag` 或 `FName`）对应的函数：

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

要在 C++ 中读取 `SetByCaller` 值，请使用你需要的版本（`GameplayTag` 或 `FName`）对应的函数：

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

我建议使用 `GameplayTag` 版本而非 `FName` 版本。这可以避免蓝图中的拼写错误。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 4.5.10 游戏效果上下文（Gameplay Effect Context）
[`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html) 结构体保存了关于 `GameplayEffectSpec` 的发起者（instigator）和[`目标数据（TargetData）`](#concepts-targeting-data)的信息。这也是一个很好的可继承结构体，用于在[`修改器幅度计算（ModifierMagnitudeCalculations）`](#concepts-ge-mmc) / [`游戏效果执行计算（GameplayEffectExecutionCalculations）`](#concepts-ge-ec)、[`属性集（AttributeSets）`](#concepts-as)和[`游戏提示（GameplayCues）`](#concepts-gc)等位置之间传递任意数据。

要继承 `GameplayEffectContext`：

1. 继承 `FGameplayEffectContext`
1. 重写 `FGameplayEffectContext::GetScriptStruct()`
1. 重写 `FGameplayEffectContext::Duplicate()`
1. 如果你的新数据需要被复制（replicated），重写 `FGameplayEffectContext::NetSerialize()`
1. 为你的子类实现 `TStructOpsTypeTraits`，就像父结构体 `FGameplayEffectContext` 那样
1. 在你的[`AbilitySystemGlobals`](#concepts-asg) 类中重写 `AllocGameplayEffectContext()` 以返回你子类的新对象

[GASShooter](https://github.com/tranek/GASShooter) 使用了一个继承的 `GameplayEffectContext` 来添加 `TargetData`，使其可以在 `GameplayCues` 中被访问，特别是对于霰弹枪，因为它可以同时命中多个敌人。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 修改器幅度计算（Modifier Magnitude Calculation）
[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html)（`ModMagCalc` 或 `MMC`）是用作 `GameplayEffects` 中[`修改器（Modifiers）`](#concepts-ge-mods)的强大类。它们的功能类似于[`游戏效果执行计算（GameplayEffectExecutionCalculations）`](#concepts-ge-ec)，但功能较弱，最重要的是它们可以被[预测（predicted）](#concepts-p)。它们的唯一目的是从 `CalculateBaseMagnitude_Implementation()` 返回一个浮点值。你可以在蓝图和 C++ 中继承并重写这个函数。

`MMCs` 可以用于任何持续时间的 `GameplayEffects` —— `即时（Instant）`、`持续（Duration）`、`无限（Infinite）`或`周期性（Periodic）`。

`MMCs` 的优势在于它们能够捕获 `GameplayEffect` 的`来源（Source）`或`目标（Target）`上任意数量的`属性（Attributes）`的值，并且完全可以访问 `GameplayEffectSpec` 来读取 `GameplayTags` 和 `SetByCallers`。`属性（Attributes）`可以被快照（snapshotted）或不被快照。被快照的`属性`在 `GameplayEffectSpec` 创建时被捕获，而未被快照的`属性`在 `GameplayEffectSpec` 应用时被捕获，并在`属性`发生变化时自动更新（适用于`无限（Infinite）`和`持续（Duration）`类型的 `GameplayEffects`）。捕获`属性`会根据 `ASC` 上现有的修改器重新计算它们的 `CurrentValue`。此重新计算**不会**运行 `AbilitySet` 中的[`PreAttributeChange()`](#concepts-as-preattributechange)，因此任何值限制（clamping）必须在此处再次执行。

| 快照（Snapshot） | 来源或目标（Source or Target） | 在 `GameplayEffectSpec` 上捕获时机 | 当`属性`变化时是否自动更新（适用于`无限（Infinite）`或`持续（Duration）`类型的 `GE`） |
| -------- | ---------------- | -------------------------------- | -------------------------------------------------------------------------------- |
| 是      | 来源（Source）           | 创建时（Creation）                         | 否                                                                               |
| 是      | 目标（Target）           | 应用时（Application）                      | 否                                                                               |
| 否       | 来源（Source）           | 应用时（Application）                      | 是                                                                              |
| 否       | 目标（Target）           | 应用时（Application）                      | 是                                                                              |

`MMC` 产生的浮点结果可以在 `GameplayEffect` 的`修改器（Modifier）`中通过系数（coefficient）以及前后系数加法（pre and post coefficient addition）进一步修改。

以下是一个 `MMC` 示例，它捕获`目标（Target）`的法力值（mana）`属性`，在中毒效果中减少法力值，减少的数量取决于`目标`拥有多少法力值以及`目标`可能拥有的标签：
```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef defined in header FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef defined in header FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// Gather the tags from the source and target as that can affect which buffs should be used
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // Avoid divide by zero

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// Double the effect if the target has more than half their mana
		Reduction *= 2;
	}

	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// Double the effect if the target is weak to PoisonMana
		Reduction *= 2;
	}

	return Reduction;
}
```

如果你没有在 `MMC` 的构造函数中将 `FGameplayEffectAttributeCaptureDefinition` 添加到 `RelevantAttributesToCapture` 中就尝试捕获`属性`，你将会得到一个关于捕获时缺少 Spec 的错误。如果你不需要捕获`属性`，则无需向 `RelevantAttributesToCapture` 添加任何内容。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 4.5.12 游戏效果执行计算（Gameplay Effect Execution Calculation）
[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html)（`ExecutionCalculation`、`Execution`（你会在插件源代码中经常看到这个术语）或 `ExecCalc`）是 `GameplayEffects` 对 `ASC` 进行更改的最强大方式。与[`修改器幅度计算（ModifierMagnitudeCalculations）`](#concepts-ge-mmc)一样，它们可以捕获`属性`并可选地进行快照。与 `MMCs` 不同的是，它们可以更改多个`属性`，并且基本上可以执行程序员想要的任何其他操作。这种强大功能和灵活性的缺点是它们无法被[预测（predicted）](#concepts-p)，并且必须在 C++ 中实现。

`ExecutionCalculations` 只能与`即时（Instant）`和`周期性（Periodic）`类型的 `GameplayEffects` 一起使用。任何名称中包含"Execute"一词的内容通常指的是这两种类型的 `GameplayEffects`。

快照（Snapshotting）在 `GameplayEffectSpec` 创建时捕获`属性`，而不快照则在 `GameplayEffectSpec` 应用时捕获`属性`。捕获`属性`会根据 `ASC` 上现有的修改器重新计算它们的 `CurrentValue`。此重新计算**不会**运行 `AbilitySet` 中的[`PreAttributeChange()`](#concepts-as-preattributechange)，因此任何值限制（clamping）必须在此处再次执行。

| 快照（Snapshot） | 来源或目标（Source or Target） | 在 `GameplayEffectSpec` 上捕获时机 |
| -------- | ---------------- | -------------------------------- |
| 是      | 来源（Source）           | 创建时（Creation）                         |
| 是      | 目标（Target）           | 应用时（Application）                      |
| 否       | 来源（Source）           | 应用时（Application）                      |
| 否       | 目标（Target）           | 应用时（Application）                      |

要设置`属性`捕获，我们遵循 Epic 的 ActionRPG 示例项目设定的模式，定义一个结构体来保存并定义我们如何捕获`属性`，然后在结构体的构造函数中创建它的一个副本。你将为每个 `ExecCalc` 都有一个这样的结构体。**注意：**每个结构体需要一个唯一的名称，因为它们共享同一个命名空间。对结构体使用相同的名称将导致捕获`属性`时的不正确行为（主要是捕获了错误`属性`的值）。

对于`本地预测（Local Predicted）`、`仅服务器（Server Only）`和`服务器发起（Server Initiated）`的[`游戏技能（GameplayAbilities）`](#concepts-ga)，`ExecCalc` 只在服务器上调用。

基于从`来源（Source）`和`目标（Target）`上的多个属性读取的复杂公式来计算受到的伤害，是 `ExecCalc` 最常见的示例。附带的示例项目中有一个简单的 `ExecCalc` 用于计算伤害，它从 `GameplayEffectSpec` 的[`SetByCaller`](#concepts-ge-spec-setbycaller)中读取伤害值，然后根据从`目标`捕获的护甲`属性`来减轻该值。参见 `GDDamageExecCalculation.cpp/.h`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 向执行计算发送数据（Sending Data to Execution Calculations）
除了捕获`属性`之外，还有几种方法可以向`执行计算（ExecutionCalculation）`发送数据。

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller
任何[在 `GameplayEffectSpec` 上设置的 `SetByCallers`](#concepts-ge-spec-setbycaller)都可以直接在`执行计算（ExecutionCalculation）`中读取。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 后备数据属性计算修改器（Backing Data Attribute Calculation Modifier）
如果你想将值硬编码到 `GameplayEffect` 中，可以使用 `CalculationModifier` 传入，它使用一个已捕获的`属性`作为后备数据。

在这个截图示例中，我们向捕获的伤害`属性`添加了 50。你也可以将其设置为 `Override` 以仅使用硬编码的值。

![后备数据属性计算修改器](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

`执行计算（ExecutionCalculation）`在捕获`属性`时读取此值。

```c++
float Damage = 0.0f;
// Capture optional damage value set on the damage GE as a CalculationModifier under the ExecutionCalculation
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>
###### 4.5.12.1.3 后备数据临时变量计算修改器（Backing Data Temporary Variable Calculation Modifier）
如果你想将值硬编码到 `GameplayEffect` 中，可以使用 `CalculationModifier` 传入，它使用`临时变量（Temporary Variable）`（在 C++ 中称为 `Transient Aggregator`）。`临时变量`与一个 `GameplayTag` 关联。

在这个截图示例中，我们使用 `Data.Damage` `GameplayTag` 向一个`临时变量`添加了 50。

![后备数据临时变量计算修改器](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

在你的`执行计算（ExecutionCalculation）`的构造函数中添加后备`临时变量`：

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`执行计算（ExecutionCalculation）`使用类似于`属性`捕获函数的特殊捕获函数来读取此值。

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 游戏效果上下文（Gameplay Effect Context）
你可以通过 `GameplayEffectSpec` 上的自定义[`游戏效果上下文（GameplayEffectContext）`](#concepts-ge-context)向`执行计算（ExecutionCalculation）`发送数据。

在`执行计算（ExecutionCalculation）`中，你可以从 `FGameplayEffectCustomExecutionParameters` 访问 `EffectContext`。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

如果你需要更改 `GameplayEffectSpec` 或 `EffectContext` 上的某些内容：

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

在`执行计算（ExecutionCalculation）`中修改 `GameplayEffectSpec` 时请谨慎。参见 `GetOwningSpecForPreExecuteMod()` 的注释。

```c++
/** Non const access. Be careful with this, especially when modifying a spec after attribute capture. */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 4.5.13 自定义应用需求（Custom Application Requirement）
[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html)（`CAR`）类为设计师提供了高级控制，用于决定一个 `GameplayEffect` 是否可以被应用，而不仅仅依赖于 `GameplayEffect` 上简单的 `GameplayTag` 检查。这些可以在蓝图中通过重写 `CanApplyGameplayEffect()` 实现，在 C++ 中通过重写 `CanApplyGameplayEffect_Implementation()` 实现。

使用 `CARs` 的示例场景：
* `目标（Target）`需要拥有一定数量的某个`属性`
* `目标`需要拥有一定数量的某个 `GameplayEffect` 的堆叠（Stacking）层数

`CARs` 还可以做更高级的事情，比如检查这个 `GameplayEffect` 的一个实例是否已经存在于`目标`上，然后[更改现有实例的持续时间](#concepts-ge-duration)而不是应用一个新实例（对 `CanApplyGameplayEffect()` 返回 false）。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 消耗游戏效果（Cost Gameplay Effect）
[`游戏技能（GameplayAbilities）`](#concepts-ga)有一个可选的 `GameplayEffect`，专门用作技能的消耗。消耗是指 `ASC` 需要拥有多少`属性`值才能激活`游戏技能（GameplayAbility）`。如果一个 `GA` 无法承担`消耗游戏效果（Cost GE）`，那么它将无法被激活。这个`消耗游戏效果`应该是一个`即时（Instant）`类型的 `GameplayEffect`，带有一个或多个从`属性`中扣除的`修改器（Modifiers）`。默认情况下，`消耗游戏效果`旨在被预测（predicted），建议保持这种能力，这意味着不要使用`执行计算（ExecutionCalculations）`。`MMCs` 完全可以接受，并且推荐用于复杂的消耗计算。

刚开始时，你很可能会为每个有消耗的 `GA` 创建一个唯一的`消耗游戏效果`。一种更高级的技术是为多个 `GAs` 复用一个`消耗游戏效果`，只需使用 `GA` 特定数据（消耗值定义在 `GA` 上）修改从`消耗游戏效果`创建的 `GameplayEffectSpec`。**这仅适用于`实例化（Instanced）`技能。**

复用`消耗游戏效果`的两种技术：

1. **使用 `MMC`。** 这是最简单的方法。创建一个[`MMC`](#concepts-ge-mmc)，从 `GameplayAbility` 实例中读取消耗值，你可以从 `GameplayEffectSpec` 中获取该实例。

```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

在这个示例中，消耗值是我添加到 `GameplayAbility` 子类上的一个 `FScalableFloat`。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![使用 MMC 的消耗游戏效果](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **重写 `UGameplayAbility::GetCostGameplayEffect()`。** 重写此函数并[在运行时创建一个 `GameplayEffect`](#concepts-ge-dynamic)，从 `GameplayAbility` 上读取消耗值。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 冷却游戏效果（Cooldown Gameplay Effect）
[`游戏技能（GameplayAbilities）`](#concepts-ga)有一个可选的 `GameplayEffect`，专门用作技能的冷却。冷却决定了技能激活后多长时间可以再次激活。如果一个 `GA` 仍在冷却中，它无法被激活。这个`冷却游戏效果（Cooldown GE）`应该是一个`持续（Duration）`类型的 `GameplayEffect`，没有`修改器（Modifiers）`，并且在 `GameplayEffect` 的 `GrantedTags` 中每个`游戏技能（GameplayAbility）`或每个技能槽位（如果你的游戏有可互换的技能分配到共享冷却的槽位）有一个唯一的 `GameplayTag`（"`冷却标签（Cooldown Tag）`"）。`GA` 实际上检查的是`冷却标签`的存在而非`冷却游戏效果`的存在。默认情况下，`冷却游戏效果`旨在被预测（predicted），建议保持这种能力，这意味着不要使用`执行计算（ExecutionCalculations）`。`MMCs` 完全可以接受，并且推荐用于复杂的冷却计算。

刚开始时，你很可能会为每个有冷却的 `GA` 创建一个唯一的`冷却游戏效果`。一种更高级的技术是为多个 `GAs` 复用一个`冷却游戏效果`，只需使用 `GA` 特定数据（冷却持续时间和`冷却标签`定义在 `GA` 上）修改从`冷却游戏效果`创建的 `GameplayEffectSpec`。**这仅适用于`实例化（Instanced）`技能。**

复用`冷却游戏效果`的两种技术：

1. **使用[`SetByCaller`](#concepts-ge-spec-setbycaller)。** 这是最简单的方法。将共享`冷却游戏效果`的持续时间设置为带有 `GameplayTag` 的 `SetByCaller`。在你的 `GameplayAbility` 子类上，定义一个浮点数 / `FScalableFloat` 用于持续时间，一个 `FGameplayTagContainer` 用于唯一的`冷却标签`，以及一个临时的 `FGameplayTagContainer`，我们将用它作为`冷却标签`与`冷却游戏效果`标签联合的返回指针。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

然后重写 `UGameplayAbility::GetCooldownTags()` 以返回我们的`冷却标签`与任何现有`冷却游戏效果`标签的联合。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
```const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后，重写 `UGameplayAbility::ApplyCooldown()` 来注入我们的 `Cooldown Tags` 并将 `SetByCaller` 添加到冷却 `GameplayEffectSpec` 中。
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName(  OurSetByCallerTag  )), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

在这张图片中，冷却的持续时间修改器（Modifier）被设置为 `SetByCaller`，其数据标签（Data Tag）为 `Data.Cooldown`。`Data.Cooldown` 对应上面代码中的 `OurSetByCallerTag`。

![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **使用 [`MMC`](#concepts-ge-mmc)。** 该方法的设置与上面相同，不同之处在于不再将 `SetByCaller` 设为 `Cooldown GE` 的持续时间，也不在 `ApplyCooldown` 中设置。取而代之的是，将持续时间设置为自定义计算类（Custom Calculation Class），并指向我们将要创建的新 `MMC`。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// 临时容器，我们将在 GetCooldownTags() 中返回其指针。
// 这将是我们的 CooldownTags 和 Cooldown GE 的冷却标签的并集。
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

然后重写 `UGameplayAbility::GetCooldownTags()` 来返回我们的 `Cooldown Tags` 与任何已有的 `Cooldown GE` 标签的并集。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后，重写 `UGameplayAbility::ApplyCooldown()` 来将我们的 `Cooldown Tags` 注入到冷却 `GameplayEffectSpec` 中。
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![Cooldown GE with MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 获取冷却游戏效果的剩余时间（Get the Cooldown Gameplay Effect's Remaining Time）
```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

	return false;
}
```

**注意：** 在客户端查询冷却的剩余时间需要客户端能够接收到复制的游戏效果（`GameplayEffects`）。这取决于其 `ASC` 的[复制模式（Replication Mode）](#concepts-asc-rm)。

<a name="concepts-ge-cooldown-listen"></a>
##### 4.5.15.2 监听冷却开始和结束（Listening for Cooldown Begin and End）
要监听冷却何时开始，你可以通过绑定 `AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf` 来响应 `Cooldown GE` 被应用的事件，或者通过绑定 `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)` 来响应 `Cooldown Tag` 被添加的事件。我建议监听 `Cooldown GE` 被添加的时机，因为你还可以访问应用它的 `GameplayEffectSpec`。由此你可以判断该 `Cooldown GE` 是本地预测（Locally Predicted）的还是服务器修正的。

要监听冷却何时结束，你可以通过绑定 `AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()` 来响应 `Cooldown GE` 被移除的事件，或者通过绑定 `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)` 来响应 `Cooldown Tag` 被移除的事件。我建议监听 `Cooldown Tag` 被移除的时机，因为当服务器修正的 `Cooldown GE` 传入时，它会移除我们本地预测的那个，导致 `OnAnyGameplayEffectRemovedDelegate()` 被触发，即使我们仍处于冷却中。在预测的 `Cooldown GE` 被移除和服务器修正的 `Cooldown GE` 被应用期间，`Cooldown Tag` 不会发生变化。

**注意：** 在客户端监听游戏效果（`GameplayEffect`）的添加或移除需要客户端能够接收到复制的游戏效果（`GameplayEffects`）。这取决于其 `ASC` 的[复制模式（Replication Mode）](#concepts-asc-rm)。

示例项目包含一个自定义蓝图节点，用于监听冷却的开始和结束。HUD UMG 控件使用它来更新流星技能冷却的剩余时间。这个异步任务（`AsyncTask`）会一直存在，直到手动调用 `EndTask()`，我们在 UMG 控件的 `Destruct` 事件中执行此操作。请参阅 [`AsyncTaskCooldownChanged.h/cpp`](Source/GASDocumentation/Private/Characters/Abilities/AsyncTaskCooldownChanged.cpp)。

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

<a name="concepts-ge-cooldown-prediction"></a>
##### 4.5.15.3 预测冷却（Predicting Cooldowns）
目前冷却实际上无法被真正预测。我们可以在本地预测的 `Cooldown GE` 被应用时启动 UI 冷却计时器，但游戏技能（`GameplayAbility`）的实际冷却与服务器端冷却的剩余时间绑定。根据玩家的延迟情况，本地预测的冷却可能已经过期，但游戏技能（`GameplayAbility`）在服务器上仍处于冷却中，这将阻止游戏技能（`GameplayAbility`）立即重新激活，直到服务器端的冷却结束。

示例项目通过以下方式处理这个问题：当本地预测的冷却开始时，将流星技能的 UI 图标变灰，然后在服务器修正的 `Cooldown GE` 传入后开始冷却计时器。

这带来的游戏性后果是，高延迟的玩家在短冷却技能上的射击频率低于低延迟的玩家，使他们处于劣势。《堡垒之夜》通过使用自定义记账系统来避免这个问题，其武器不使用冷却游戏效果（`GameplayEffects`）。

允许真正的预测冷却（玩家可以在本地冷却到期时激活游戏技能（`GameplayAbility`），即使服务器仍在冷却中）是 Epic 希望在 [GAS 的未来迭代](#concepts-p-future)中实现的功能。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 4.5.16 更改活跃游戏效果的持续时间（Changing Active Gameplay Effect Duration）
要更改 `Cooldown GE` 或任何持续时间（`Duration`）类型的游戏效果（`GameplayEffect`）的剩余时间，我们需要更改 `GameplayEffectSpec` 的 `Duration`，更新其 `StartServerWorldTime`，更新其 `CachedStartServerWorldTime`，更新其 `StartWorldTime`，然后使用 `CheckDuration()` 重新运行持续时间检查。在服务器上执行此操作并将 `FActiveGameplayEffect` 标记为脏数据（dirty）会将更改复制到客户端。
**注意：** 这确实涉及 `const_cast`，可能不是 Epic 预期的更改持续时间的方式，但到目前为止它工作良好。

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 在运行时创建动态游戏效果（Creating Dynamic Gameplay Effects at Runtime）
在运行时创建动态游戏效果（`GameplayEffects`）是一个高级主题。你不应该需要经常这样做。

只有即时（`Instant`）类型的游戏效果（`GameplayEffects`）可以在 C++ 中从头创建。持续时间（`Duration`）和无限（`Infinite`）类型的游戏效果（`GameplayEffects`）不能在运行时动态创建，因为当它们复制时会查找不存在的游戏效果（`GameplayEffect`）类定义。要实现此功能，你应该像通常在编辑器中所做的那样创建一个原型游戏效果（`GameplayEffect`）类，然后在运行时根据需要自定义 `GameplayEffectSpec` 实例。

在运行时创建的即时（`Instant`）类型游戏效果（`GameplayEffects`）也可以从[本地预测](#concepts-p)的游戏技能（`GameplayAbility`）内部调用。但是，动态创建是否会产生副作用目前尚不清楚。

##### 示例（Examples）

示例项目创建了一个动态游戏效果，用于在角色受到致命一击时在其属性集（`AttributeSet`）中将金币和经验值发送回击杀者。

```c++
// Create a dynamic instant Gameplay Effect to give the bounties
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

第二个示例展示了在本地预测的游戏技能（`GameplayAbility`）中创建的运行时游戏效果（`GameplayEffect`）。使用时请自行承担风险（请参阅代码中的注释）！

```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// Create the GE at runtime.
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // Only instant works with runtime GE.

		// Add a simple scalable float modifier, which overrides MyAttribute with 42.
		// In real world applications, consume information passed via TriggerEventData.
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// Apply the GE.

		// Create the GESpec here to avoid the behavior of ASC to create GESpecs from the GE class default object.
		// Since we have a dynamic GE here, this would create a GESpec with the base GameplayEffect class, so we
		// would lose our modifiers. Attention: It is unknown, if this "hack" done here can have drawbacks!
		// The spec prevents the GE object being collected by the GarbageCollector, since the GE is a UPROPERTY on the spec.
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // "new", since lifetime is managed by a shared ptr within the handle
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 4.5.18 游戏效果容器（Gameplay Effect Containers）
Epic 的 [Action RPG 示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)实现了一个名为 `FGameplayEffectContainer` 的结构。它不在原生 GAS 中，但对于包含游戏效果（`GameplayEffects`）和[目标数据（`TargetData`）](#concepts-targeting-data)非常方便。它自动化了一些工作，例如从游戏效果（`GameplayEffects`）创建 `GameplayEffectSpecs` 以及在其 `GameplayEffectContext` 中设置默认值。在游戏技能（`GameplayAbility`）中创建一个 `GameplayEffectContainer` 并将其传递给生成的投射物非常简单直接。我选择不在附带的示例项目中实现 `GameplayEffectContainers`，以展示在原生 GAS 中如何工作，但我强烈建议研究它们并考虑将它们添加到你的项目中。

要访问 `GameplayEffectContainers` 内部的 `GESpecs` 来执行添加 `SetByCallers` 等操作，请拆解 `FGameplayEffectContainer` 并通过其在 `GESpecs` 数组中的索引访问 `GESpec` 引用。这要求你事先知道要访问的 `GESpec` 的索引。

![SetByCaller with a GameplayEffectContainer](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainers` 还包含一种可选的高效[目标选择（targeting）](#concepts-targeting-containers)方式。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga"></a>
### 4.6 游戏能力（Gameplay Abilities）

<a name="concepts-ga-definition"></a>
#### 4.6.1 游戏能力定义（Gameplay Ability Definition）
[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html)（`GA`）是 `Actor` 在游戏中可以执行的任何动作或技能。可以同时有多个 `GameplayAbility` 处于激活状态，例如冲刺和开枪射击。这些可以用蓝图（Blueprint）或 C++ 来制作。

`GameplayAbilities` 的示例：
* 跳跃
* 冲刺
* 开枪射击
* 每隔 X 秒被动格挡一次攻击
* 使用药水
* 打开门
* 收集资源
* 建造建筑

不应该用 `GameplayAbilities` 实现的内容：
* 基本移动输入
* 某些与 UI 的交互——不要使用 `GameplayAbility` 来从商店购买物品。

这些不是硬性规则，只是我的建议。你的设计和实现可能会有所不同。

`GameplayAbilities` 自带默认功能，可以通过等级来修改属性的变化量或更改 `GameplayAbility` 的功能。

`GameplayAbilities` 根据[`网络执行策略（Net Execution Policy）`](#concepts-ga-net)在拥有者客户端和/或服务器上运行，但不会在模拟代理（simulated proxies）上运行。`网络执行策略（Net Execution Policy）`决定了 `GameplayAbility` 是否会被本地[预测（predicted）](#concepts-p)。它们包含用于[可选的消耗（Cost）和冷却（Cooldown）`GameplayEffects`](#concepts-ga-commit)的默认行为。`GameplayAbilities` 使用 [`AbilityTasks`](#concepts-at) 来处理需要一段时间才能完成的动作，例如等待事件、等待属性变化、等待玩家选择目标，或使用 `Root Motion Source` 移动 `Character`。**模拟客户端不会运行 `GameplayAbilities`**。相反，当服务器运行能力时，任何需要在模拟代理上可视化播放的内容（如动画蒙太奇）将通过 `AbilityTasks` 进行复制或 RPC 调用，或通过 [`GameplayCues`](#concepts-gc) 处理音效和粒子等装饰性效果。

所有 `GameplayAbilities` 都需要重写 `ActivateAbility()` 函数来实现你的游戏逻辑。可以在 `EndAbility()` 中添加额外逻辑，该函数在 `GameplayAbility` 完成或被取消时运行。

简单 `GameplayAbility` 的流程图：
![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)


更复杂的 `GameplayAbility` 的流程图：
![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

复杂的能力可以使用多个相互交互（激活、取消等）的 `GameplayAbilities` 来实现。

<a name="concepts-ga-definition-reppolicy"></a>
##### 4.6.1.1 复制策略（Replication Policy）
不要使用此选项。名称具有误导性，而且你不需要它。[`GameplayAbilitySpecs`](#concepts-ga-spec) 默认会从服务器复制到拥有者客户端。如上所述，**`GameplayAbilities` 不会在模拟代理上运行**。它们使用 `AbilityTasks` 和 `GameplayCues` 来向模拟代理复制或 RPC 视觉变化。Epic 的 Dave Ratti 已经表达了他希望在未来[移除此选项](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)的意愿。

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 服务器尊重远程能力取消（Server Respects Remote Ability Cancellation）
此选项往往会带来更多问题。它意味着如果客户端的 `GameplayAbility` 因取消或自然完成而结束，它会强制服务器端的版本也结束，无论服务器端是否已完成。后者才是重要的问题，特别是对于高延迟玩家使用的本地预测 `GameplayAbilities`。通常你会希望禁用此选项。

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 直接复制输入（Replicate Input Directly）
设置此选项将始终向服务器复制输入按下和释放事件。Epic 建议不要使用此选项，而是依赖内置于现有输入相关 [`AbilityTasks`](#concepts-at) 中的 `Generic Replicated Events`，前提是你已将[输入绑定到你的 `ASC`](#concepts-ga-input)。

Epic 的注释：
```c++
/** Direct Input state replication. These will be called if bReplicateInputDirectly is true on the ability and is generally not a good thing to use. (Instead, prefer to use Generic Replicated Events). */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 将输入绑定到 ASC（Binding Input to the ASC）
`ASC` 允许你直接将输入动作绑定到它，并在授予 `GameplayAbilities` 时将这些输入分配给它们。分配了输入动作的 `GameplayAbilities` 在按下时会自动激活（如果满足 `GameplayTag` 要求）。分配的输入动作是使用响应输入的内置 `AbilityTasks` 所必需的。

除了分配用于激活 `GameplayAbilities` 的输入动作外，`ASC` 还接受通用的 `Confirm`（确认）和 `Cancel`（取消）输入。这些特殊输入被 `AbilityTasks` 用于确认诸如[`目标Actor（Target Actors）`](#concepts-targeting-actors)之类的东西或取消它们。

要将输入绑定到 `ASC`，你必须首先创建一个枚举（enum），将输入动作名称转换为字节。枚举名称必须与项目设置中使用的输入动作名称完全匹配。`DisplayName` 无关紧要。

来自示例项目：
```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```

如果你的 `ASC` 在 `Character` 上，那么在 `SetupPlayerInputComponent()` 中包含绑定到 `ASC` 的函数：
```c++
// Bind to AbilitySystemComponent
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

如果你的 `ASC` 在 `PlayerState` 上，`SetupPlayerInputComponent()` 内部存在潜在的竞态条件（race condition），即 `PlayerState` 可能尚未复制到客户端。因此，我建议在 `SetupPlayerInputComponent()` 和 `OnRep_PlayerState()` 中都尝试绑定输入。仅使用 `OnRep_PlayerState()` 是不够的，因为可能存在这样的情况：当 `PlayerState` 在 `PlayerController` 告诉客户端调用 `ClientRestart()`（该函数创建 `InputComponent`）之前复制时，`Actor` 的 `InputComponent` 可能为空。示例项目演示了在两个位置尝试绑定，并使用布尔值控制流程，确保只实际绑定一次输入。

**注意：** 在示例项目中，枚举中的 `Confirm` 和 `Cancel` 与项目设置中的输入动作名称（`ConfirmTarget` 和 `CancelTarget`）不匹配，但我们在 `BindAbilityActivationToInputComponent()` 中提供了它们之间的映射。这些是特殊的，因为我们提供了映射所以它们不必匹配，但也可以匹配。枚举中的所有其他输入必须与项目设置中的输入动作名称匹配。

对于只会由一个输入激活的 `GameplayAbilities`（它们将始终存在于同一个"槽位"中，类似 MOBA），我更倾向于在我的 `UGameplayAbility` 子类中添加一个变量来定义它们的输入。然后我可以在授予能力时从 `ClassDefaultObject` 中读取它。

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 绑定输入但不激活能力（Binding to Input without Activating Abilities）
如果你不希望 `GameplayAbilities` 在按下输入时自动激活，但仍然希望将它们绑定到输入以便与 `AbilityTasks` 一起使用，你可以在你的 `UGameplayAbility` 子类中添加一个新的布尔变量 `bActivateOnInput`，默认值为 `true`，并重写 `UAbilitySystemComponent::AbilityLocalInputPressed()`。

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// Consume the input if this InputID is overloaded with GenericConfirm/Cancel and the GenericConfim/Cancel callback is bound
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// Invoke the InputPressed event. This is not replicated here. If someone is listening, they may replicate the InputPressed event to the server.
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// Ability is not active, so try to activate it
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 4.6.3 授予能力（Granting Abilities）
将 `GameplayAbility` 授予 `ASC` 会将其添加到 `ASC` 的 `ActivatableAbilities` 列表中，只要满足 [`GameplayTag` 要求](#concepts-ga-tags)就可以随时激活该 `GameplayAbility`。

我们在服务器上授予 `GameplayAbilities`，然后自动将 [`GameplayAbilitySpec`](#concepts-ga-spec) 复制到拥有者客户端。其他客户端/模拟代理不会收到 `GameplayAbilitySpec`。

示例项目在 `Character` 类上存储了一个 `TArray<TSubclassOf<UGDGameplayAbility>>`，在游戏开始时读取并授予：
```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// Grant abilities, but only on the server
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->bCharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->bCharacterAbilitiesGiven = true;
}
```

在授予这些 `GameplayAbilities` 时，我们使用 `UGameplayAbility` 类、能力等级、绑定的输入以及 `SourceObject`（即谁将此 `GameplayAbility` 授予了此 `ASC`）来创建 `GameplayAbilitySpecs`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 激活能力（Activating Abilities）
如果 `GameplayAbility` 被分配了输入动作，它将在按下输入且满足 `GameplayTag` 要求时自动激活。这可能并不总是激活 `GameplayAbility` 的理想方式。`ASC` 提供了四种其他方法来激活 `GameplayAbilities`：通过 `GameplayTag`、`GameplayAbility` 类、`GameplayAbilitySpec` 句柄以及通过事件。通过事件激活 `GameplayAbility` 允许你[随事件传入数据负载](#concepts-ga-data)。

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec, const FGameplayEventData* GameplayEventData);
```
要通过事件激活 `GameplayAbility`，`GameplayAbility` 必须在 `GameplayAbility` 中设置好 `Triggers`（触发器）。分配一个 `GameplayTag` 并为 `GameplayEvent` 选择一个选项。要发送事件，使用函数 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`。通过事件激活 `GameplayAbility` 允许你传入带有数据的负载。

`GameplayAbility` 的 `Triggers`（触发器）还允许你在添加或移除 `GameplayTag` 时激活 `GameplayAbility`。

**注意：** 在蓝图中通过事件激活 `GameplayAbility` 时，你必须使用 `ActivateAbilityFromEvent` 节点。

**注意：** 当 `GameplayAbility` 应该终止时，不要忘记调用 `EndAbility()`，除非你有一个始终运行的 `GameplayAbility`，如被动能力。

**本地预测（locally predicted）**的 `GameplayAbilities` 的激活序列：
1. **拥有者客户端**调用 `TryActivateAbility()`
1. 调用 `InternalTryActivateAbility()`
1. 调用 `CanActivateAbility()` 并返回是否满足 `GameplayTag` 要求、`ASC` 是否负担得起消耗（Cost）、`GameplayAbility` 是否不在冷却（Cooldown）中，以及当前是否没有其他实例处于激活状态
1. 调用 `CallServerTryActivateAbility()` 并传入它生成的 `Prediction Key`（预测密钥）
1. 调用 `CallActivateAbility()`
1. 调用 `PreActivate()` Epic 将此称为"样板初始化工作"
1. 调用 `ActivateAbility()` 最终激活能力

**服务器**接收 `CallServerTryActivateAbility()`
1. 调用 `ServerTryActivateAbility()`
1. 调用 `InternalServerTryActivateAbility()`
1. 调用 `InternalTryActivateAbility()`
1. 调用 `CanActivateAbility()` 并返回是否满足 `GameplayTag` 要求、`ASC` 是否负担得起消耗（Cost）、`GameplayAbility` 是否不在冷却（Cooldown）中，以及当前是否没有其他实例处于激活状态
1. 如果成功则调用 `ClientActivateAbilitySucceed()`，通知客户端更新其 `ActivationInfo`（激活信息）表明其激活已被服务器确认，并广播 `OnConfirmDelegate` 委托。这与输入确认不同。
1. 调用 `CallActivateAbility()`
1. 调用 `PreActivate()` Epic 将此称为"样板初始化工作"
1. 调用 `ActivateAbility()` 最终激活能力

如果服务器在任何时候激活失败，它将调用 `ClientActivateAbilityFailed()`，立即终止客户端的 `GameplayAbility` 并撤销任何预测的更改。

<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 被动能力（Passive Abilities）
要实现自动激活并持续运行的被动 `GameplayAbilities`，需要重写 `UGameplayAbility::OnAvatarSet()`，该函数在 `GameplayAbility` 被授予且 `AvatarActor` 被设置时自动调用，然后调用 `TryActivateAbility()`。

我建议在你自定义的 `UGameplayAbility` 类中添加一个 `bool` 来指定 `GameplayAbility` 是否应在授予时激活。示例项目在其被动护甲叠加能力中就是这样做的。

被动 `GameplayAbilities` 通常会将[`网络执行策略（Net Execution Policy）`](#concepts-ga-net)设置为 `Server Only`（仅服务器）。

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (bActivateAbilityOnGranted)
	{
		ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

Epic 将此函数描述为启动被动能力和执行 `BeginPlay` 类型操作的正确位置。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-activating-failedtags"></a>
##### 4.6.4.2 激活失败标签（Activation Failed Tags）

能力具有默认逻辑来告诉你能力激活失败的原因。要启用此功能，你必须设置与默认失败情况对应的游戏标签（GameplayTags）。

将这些标签（或你自己的命名约定）添加到你的项目中：
```
+GameplayTagList=(Tag="Activation.Fail.BlockedByTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.CantAffordCost",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.IsDead",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.MissingTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.Networking",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.OnCooldown",DevComment="")
```

然后将它们添加到 [`GASDocumentation\Config\DefaultGame.ini`](https://github.com/tranek/GASDocumentation/blob/master/Config/DefaultGame.ini#L8-L13) 中：
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
ActivateFailIsDeadName=Activation.Fail.IsDead
ActivateFailCooldownName=Activation.Fail.OnCooldown
ActivateFailCostName=Activation.Fail.CantAffordCost
ActivateFailTagsBlockedName=Activation.Fail.BlockedByTags
ActivateFailTagsMissingName=Activation.Fail.MissingTags
ActivateFailNetworkingName=Activation.Fail.Networking
```

现在每当能力激活失败时，对应的游戏标签（GameplayTag）将包含在输出日志消息中，或在 `showdebug AbilitySystem` HUD 上可见。
```
LogAbilitySystem: Display: InternalServerTryActivateAbility. Rejecting ClientActivation of Default__GA_FireGun_C. InternalTryActivateAbility failed: Activation.Fail.BlockedByTags
LogAbilitySystem: Display: ClientActivateAbilityFailed_Implementation. PredictionKey :109 Ability: Default__GA_FireGun_C
```

![激活失败标签在 showdebug AbilitySystem 中显示](https://github.com/tranek/GASDocumentation/raw/master/Images/activationfailedtags.png)

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>
#### 4.6.5 取消能力（Canceling Abilities）
要从内部取消一个 `GameplayAbility`，你需要调用 `CancelAbility()`。这将调用 `EndAbility()` 并将其 `WasCancelled` 参数设置为 true。

要从外部取消一个 `GameplayAbility`，`ASC` 提供了以下几个函数：

```c++
/** Cancels the specified ability CDO. */
void CancelAbility(UGameplayAbility* Ability);

/** Cancels the ability indicated by passed in spec handle. If handle is not found among reactivated abilities nothing happens. */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** Cancel all abilities with the specified tags. Will not cancel the Ignore instance */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities regardless of tags. Will not cancel the ignore instance */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities and kills any remaining instanced abilities */
virtual void DestroyActiveState();
```

**注意：** 我发现如果你有 `Non-Instanced`（非实例化）的 `GameplayAbilities`，`CancelAllAbilities` 似乎无法正常工作。它似乎在遇到 `Non-Instanced` 的 `GameplayAbility` 时就会停止。`CancelAbilities` 能更好地处理 `Non-Instanced` 的 `GameplayAbilities`，这也是示例项目（Sample Project）使用的方式（跳跃是一个非实例化的 `GameplayAbility`）。实际效果可能因人而异。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 获取激活的能力（Getting Active Abilities）
初学者经常会问"我如何获取激活的能力？"也许是为了在其上设置变量或取消它。同一时间可以有多个 `GameplayAbility` 处于激活状态，因此不存在单一的"激活能力"。相反，你必须搜索 `ASC` 的 `ActivatableAbilities`（可激活能力）列表（`ASC` 拥有的已授予的 `GameplayAbilities`），找到与你正在查找的 [`Asset` 或 `Granted` `GameplayTag`](#concepts-ga-tags) 匹配的那个。

`UAbilitySystemComponent::GetActivatableAbilities()` 返回一个 `TArray<FGameplayAbilitySpec>` 供你遍历。

`ASC` 还有另一个辅助函数，它接受一个 `GameplayTagContainer` 作为参数来辅助搜索，而不需要手动遍历 `GameplayAbilitySpecs` 列表。`bOnlyAbilitiesThatSatisfyTagRequirements` 参数将只返回满足其 `GameplayTag` 要求并且当前可以被激活的 `GameplayAbilitySpecs`。例如，你可能有两个基础攻击 `GameplayAbilities`，一个使用武器，一个使用徒手，根据是否装备了武器来设置 `GameplayTag` 要求，从而激活正确的那个。有关更多信息，请参阅 Epic 对该函数的注释。
```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

一旦你找到了要找的 `FGameplayAbilitySpec`，你可以调用它的 `IsActive()` 方法。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 4.6.7 实例化策略（Instancing Policy）
`GameplayAbility` 的 `实例化策略（Instancing Policy）` 决定了 `GameplayAbility` 在激活时是否以及如何被实例化。

| `实例化策略（Instancing Policy）` | 描述 | 使用场景示例 |
| ----------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 按 Actor 实例化（Instanced Per Actor） | 每个 `ASC` 只有一个 `GameplayAbility` 实例，在多次激活之间重复使用。 | 这可能是你最常使用的 `实例化策略（Instancing Policy）`。你可以将它用于任何能力，并在多次激活之间提供持久性。设计师需要负责在激活之间手动重置需要重置的变量。 |
| 按执行实例化（Instanced Per Execution） | 每次 `GameplayAbility` 被激活时，都会创建一个新的 `GameplayAbility` 实例。 | 这类 `GameplayAbilities` 的好处是每次激活时变量都会被重置。但它们的性能比 `按 Actor 实例化（Instanced Per Actor）` 更差，因为每次激活都会生成新的 `GameplayAbilities`。示例项目没有使用此类型。 |
| 非实例化（Non-Instanced） | `GameplayAbility` 在其 `ClassDefaultObject` 上运行，不会创建实例。 | 这在三种策略中性能最好，但也是限制最多的。`非实例化（Non-Instanced）` 的 `GameplayAbilities` 无法存储状态，这意味着没有动态变量，也不能绑定到 `AbilityTask` 委托。最适合用于频繁使用的简单能力，如 MOBA 或 RTS 游戏中小兵的基础攻击。示例项目的跳跃 `GameplayAbility` 就是 `非实例化（Non-Instanced）` 的。 |

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-net"></a>
#### 4.6.8 网络执行策略（Net Execution Policy）
`GameplayAbility` 的 `网络执行策略���Net Execution Policy）` 决定了谁运行该 `GameplayAbility` 以及以什么顺序运行。

| `网络执行策略（Net Execution Policy）` | 描述 |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `仅本地（Local Only）` | `GameplayAbility` 仅在拥有它的客户端上运行。这对于仅进行本地外观变化的能力很有用。单人游戏应使用 `仅服务器（Server Only）`。 |
| `本地预测（Local Predicted）` | `本地预测（Local Predicted）` 的 `GameplayAbilities` 先在拥有它的客户端上激活，然后在服务器上激活。服务器版本将纠正客户端预测不正确的任何内容。参见[预测（Prediction）](#concepts-p)。 |
| `仅服务器（Server Only）` | `GameplayAbility` 仅在服务器上运行。被动 `GameplayAbilities` 通常是 `仅服务器（Server Only）` 的。单人游戏应使用此策略。 |
| `服务器发起（Server Initiated）` | `服务器发起（Server Initiated）` 的 `GameplayAbilities` 先在服务器上激活，然后在拥有它的客户端上激活。我个人几乎没有使用过这种策略。 |

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 能力标签（Ability Tags）
`GameplayAbilities` 附带具有内置逻辑的 `GameplayTagContainers`。这些 `GameplayTags` 都不会被复制。

| `GameplayTag 容器（GameplayTag Container）` | 描述 |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `能力标签（Ability Tags）` | `GameplayAbility` 拥有的 `GameplayTags`。这些只是用于描述 `GameplayAbility` 的 `GameplayTags`。 |
| `通过标签取消能力（Cancel Abilities with Tag）` | 当此 `GameplayAbility` 被激活时，其他在其 `能力标签（Ability Tags）` 中拥有这些 `GameplayTags` 的 `GameplayAbilities` 将被取消。 |
| `通过标签阻止能力（Block Abilities with Tag）` | 当此 `GameplayAbility` 处于激活状态时，其他在其 `能力标签（Ability Tags）` 中拥有这些 `GameplayTags` 的 `GameplayAbilities` 将被阻止激活。 |
| `激活时拥有的标签（Activation Owned Tags）` | 当此 `GameplayAbility` 处于激活状态时，这些 `GameplayTags` 会赋予 `GameplayAbility` 的拥有者。请记住这些不会被复制。 |
| `激活所需标签（Activation Required Tags）` | 只有当拥有者拥有**所有**这些 `GameplayTags` 时，此 `GameplayAbility` 才能被激活。 |
| `激活阻止标签（Activation Blocked Tags）` | 如果拥有者拥有这些 `GameplayTags` 中的**任何一个**，则此 `GameplayAbility` 不能被激活。 |
| `来源所需标签（Source Required Tags）` | 只有当 `来源（Source）` 拥有**所有**这些 `GameplayTags` 时，此 `GameplayAbility` 才能被激活。`来源（Source）` 的 `GameplayTags` 仅在 `GameplayAbility` 由事件触发时才会被设置。 |
| `来源阻止标签（Source Blocked Tags）` | 如果 `来源（Source）` 拥有这些 `GameplayTags` 中的**任何一个**，则此 `GameplayAbility` 不能被激活。`来源（Source）` 的 `GameplayTags` 仅在 `GameplayAbility` 由事件触发时才会被设置。 |
| `目标所需标签（Target Required Tags）` | 只有当 `目标（Target）` 拥有**所有**这些 `GameplayTags` 时，此 `GameplayAbility` 才能被激活。`目标（Target）` 的 `GameplayTags` 仅在 `GameplayAbility` 由事件触发时才会被设置。 |
| `目标阻止标签（Target Blocked Tags）` | 如果 `目标（Target）` 拥有这些 `GameplayTags` 中的**任何一个**，则此 `GameplayAbility` 不能被激活。`目标（Target）` 的 `GameplayTags` 仅在 `GameplayAbility` 由事件触发时才会被设置。 |

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 游戏能力规格（Gameplay Ability Spec）
`GameplayAbilitySpec` 在 `GameplayAbility` 被授予后存在于 `ASC` 上，它定义了可激活的 `GameplayAbility` —— `GameplayAbility` 类、等级、输入绑定，以及必须与 `GameplayAbility` 类分开保存的运行时状态。

当 `GameplayAbility` 在服务器上被授予时，服务器会将 `GameplayAbilitySpec` 复制到拥有它的客户端，以便客户端可以激活它。

激活一个 `GameplayAbilitySpec` 将根据其 `实例化策略（Instancing Policy）` 创建（或不创建，对于 `非实例化（Non-Instanced）` 的 `GameplayAbilities`）一个 `GameplayAbility` 的实例。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 向能力传递数据（Passing Data to Abilities）
`GameplayAbilities` 的一般范式是 `激活->生成数据->应用->结束`。有时你需要对已有的数据进行操作。GAS 提供了几种将外部数据传入 `GameplayAbilities` 的选项：

| 方法 | 描述 |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 通过事件激活 `GameplayAbility` | 使用包含数据负载的事件来激活 `GameplayAbility`。对于本地预测（Local Predicted）的 `GameplayAbilities`，事件的负载会从客户端复制到服务器。使用两个 `Optional Object` 或 [`TargetData`](#concepts-targeting-data) 变量来传递不适合现有变量的任意数据。这种方式的缺点是它阻止你通过输入绑定来激活能力。要通过事件激活 `GameplayAbility`，`GameplayAbility` 必须在其 `触发器（Triggers）` 中进行设置。分配一个 `GameplayTag` 并选择 `GameplayEvent` 的选项。要发送事件，使用函数 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`。 |
| 使用 `WaitGameplayEvent` `AbilityTask` | 使用 `WaitGameplayEvent` `AbilityTask` 来告诉 `GameplayAbility` 在激活后监听包含负载数据的事件。事件负载和发送过程与通过事件激活 `GameplayAbilities` 相同。这种方式的缺点是事件不会被 `AbilityTask` 复制，因此应仅用于 `仅本地（Local Only）` 和 `仅服务器（Server Only）` 的 `GameplayAbilities`。你可以编写自己的 `AbilityTask` 来复制事件负载。 |
| 使用 `TargetData` | 自定义的 `TargetData` 结构体是在客户端和服务器之间传递任意数据的好方法。 |
| 将数据存储在 `OwnerActor` 或 `AvatarActor` 上 | 使用存储在 `OwnerActor`、`AvatarActor` 或任何你可以获取引用的其他对象上的复制变量。这种方法最灵活，可以与通过输入绑定激活的 `GameplayAbilities` 配合使用。但是，它不能保证数据在使用时已通过复制同步。你必须提前确保这一点——这意味着如果你设置了一个复制变量然后立即激活 `GameplayAbility`，由于潜在的丢包，无法保证在接收端这些操作的顺序。 |

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-commit"></a>
#### 4.6.12 能力消耗与冷却（Ability Cost and Cooldown）
`GameplayAbilities` 附带可选的消耗和冷却功能。消耗是预定义的 `属性（Attributes）` 数量，`ASC` 必须拥有这些属性才能激活 `GameplayAbility`，通过 `即时（Instant）` `GameplayEffect`（[`消耗 GE（Cost GE）`](#concepts-ge-cost)）实现。冷却是防止 `GameplayAbility` 在过期前被重新激活的计时器，通过 `持续（Duration）` `GameplayEffect`（[`冷却 GE（Cooldown GE）`](#concepts-ge-cooldown)）实现。

在 `GameplayAbility` 调用 `UGameplayAbility::Activate()` 之前，它会调用 `UGameplayAbility::CanActivateAbility()`。该函数检查拥有的 `ASC` 是否能负担消耗（`UGameplayAbility::CheckCost()`）并确保 `GameplayAbility` 不在冷却中（`UGameplayAbility::CheckCooldown()`）。

在 `GameplayAbility` 调用 `Activate()` 之后，它可以在任何时候使用 `UGameplayAbility::CommitAbility()` 来选择性地提交消耗和冷却，该函数会调用 `UGameplayAbility::CommitCost()` 和 `UGameplayAbility::CommitCooldown()`。设计师可以选择分别调用 `CommitCost()` 或 `CommitCooldown()`，如果它们不应该同时提交的话。提交消耗和冷却会再次调用 `CheckCost()` 和 `CheckCooldown()`，这是 `GameplayAbility` 因消耗和冷却相关原因而失败的最后机会。拥有的 `ASC` 的 `属性（Attributes）` 可能在 `GameplayAbility` 被激活后发生了变化，导致在提交时无法满足消耗。如果[预测密钥（Prediction Key）](#concepts-p-key)在提交时有效，消耗和冷却的提交可以被[本地预测（Locally Predicted）](#concepts-p)。

有关实现细节，请参阅 [`消耗 GE（CostGE）`](#concepts-ge-cost) 和 [`冷却 GE（CooldownGE）`](#concepts-ge-cooldown)。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-leveling"></a>
#### 4.6.13 能力升级（Leveling Up Abilities）
有两种常见的能力升级方法：

| 升级方法 | 描述 |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 取消授予并在新等级重新授予 | 从 `ASC` 取消授予（移除）`GameplayAbility` 并在服务器上以下一等级重新授予。如果 `GameplayAbility` 在当时处于激活状态，这将终止它。 |
| 提升 `GameplayAbilitySpec` 的等级 | 在服务器上找到 `GameplayAbilitySpec`，提升其等级，并将其标记为脏数据（dirty）以便复制到拥有它的客户端。此方法不会在升级时终止处于激活状态的 `GameplayAbility`。 |

两种方法的主要区别在于你是否希望在升级时取消激活的 `GameplayAbilities`。根据你的 `GameplayAbilities`，你很可能会同时使用两种方法。我建议在你的 `UGameplayAbility` 子类中添加一个 `bool` 来指定使用哪种方法。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-sets"></a>
#### 4.6.14 能力集（Ability Sets）
`GameplayAbilitySets` 是便捷的 `UDataAsset` 类，用于保存输入绑定和角色（Characters）的启动 `GameplayAbilities` 列表，并带有授予 `GameplayAbilities` 的逻辑。子类还可以包含额外的逻辑或属性。Paragon 为每个英雄都有一个 `GameplayAbilitySet`，其中包含了所有赋予该英雄的 `GameplayAbilities`。

就我目前所见，我认为这个类并非必要。示例项目在 `GDCharacterBase` 及其子类中处理了 `GameplayAbilitySets` 的所有功能。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-batching"></a>
#### 4.6.15 能力批处理（Ability Batching）
传统的 `游戏能力（Gameplay Ability）` 生命周期涉及从客户端到服务器的至少两到三个 RPC。

1. `CallServerTryActivateAbility()`
1. `ServerSetReplicatedTargetData()`（可选）
1. `ServerEndAbility()`

如果一个 `GameplayAbility` 在一帧内以一个原子操作组执行所有这些动作，我们可以优化此工作流程，将所有两个或三个 RPC 批处理（合并）为一个 RPC。`GAS` 将此 RPC 优化称为 `能力批处理（Ability Batching）`。`能力批处理（Ability Batching）` 的常见使用场景是命中扫描枪。命中扫描枪激活、进行射线检测、将 [`TargetData`](#concepts-targeting-data) 发送到服务器，然后在一帧内以一个原子操作组结束能力。[GASShooter](https://github.com/tranek/GASShooter) 示例项目展示了其命中扫描枪的这一技术。

半自动枪是最理想的场景，将 `CallServerTryActivateAbility()`、`ServerSetReplicatedTargetData()`（子弹命中结果）和 `ServerEndAbility()` 批处理为一个 RPC 而不是三个 RPC。

全自动/连发枪将第一发子弹的 `CallServerTryActivateAbility()` 和 `ServerSetReplicatedTargetData()` 批处理为一个 RPC 而不是两个 RPC。每颗后续子弹是其自己的 `ServerSetReplicatedTargetData()` RPC。最后，当枪停止射击时，`ServerEndAbility()` 作为单独的 RPC 发送。这是最差的场景，我们只在第一发子弹上节省了一个 RPC 而不是两个。这种场景也可以通过 [`游戏事件（Gameplay Event）`](#concepts-ga-data) 来激活能力来实现，这将通过 `EventPayload` 从客户端将子弹的 `TargetData` 发送到服务器。后一种方法的缺点是 `TargetData` 必须在能力外部生成，而批处理方法在能力内部生成 `TargetData`。

`能力批处理（Ability Batching）` 在 [`ASC`](#concepts-asc) 上默认是禁用的。要启用 `能力批处理（Ability Batching）`，需要重写 `ShouldDoServerAbilityRPCBatch()` 使其返回 true：

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

现在 `能力批处理（Ability Batching）` 已启用，在激活你想要批处理的能力之前，你必须预先创建一个 `FScopedServerAbilityRPCBatcher` 结构体。这个特殊的结构体会尝试批处理其作用域内之后激活的任何能力。一旦 `FScopedServerAbilityRPCBatcher` 超出作用域，任何被激活的能力将不会尝试批处理。`FScopedServerAbilityRPCBatcher` 的工作方式是在每个可被批处理的函数中有特殊代码，拦截调用使其不发送 RPC，而是将消息打包到批处理结构体中。当 `FScopedServerAbilityRPCBatcher` 超出作用域时，它会在 `UAbilitySystemComponent::EndServerAbilityRPCBatch()` 中自动将此批处理结构体通过 RPC 发送到服务器。服务器在 `UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)` 中接收批处理 RPC。`BatchInfo` 参数将包含能力是否应该结束的标志、激活时是否按下了输入的标志，以及 `TargetData`（如果包含的话）。这是一个很好的设置断点的函数，以确认你的批处理是否正常工作。或者，使用控制台变量 `AbilitySystem.ServerRPCBatching.Log 1` 来启用特殊的能力批处理日志记录。

此机制只能在 C++ 中实现，并且只能通过 `FGameplayAbilitySpecHandle` 激活能力。

```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
	bool AbilityActivated = false;
	if (InAbilityHandle.IsValid())
	{
		FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
		AbilityActivated = TryActivateAbility(InAbilityHandle, true);

		if (EndAbilityImmediately)
		{
			FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
			if (AbilitySpec)
			{
				UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
				GSAbility->ExternalEndAbility();
			}
		}

		return AbilityActivated;
	}

	return AbilityActivated;
}
```

GASShooter 为半自动和全自动枪复用了相同的批处理 `GameplayAbility`，它从不直接调用 `EndAbility()`（而是由能力外部的一个仅本地（local-only）能力来处理，该能力管理玩家输入以及根据当前射击模式来调用批处理能力）。由于所有 RPC 必须在 `FScopedServerAbilityRPCBatcher` 的作用域内发生，我提供了 `EndAbilityImmediately` 参数，以便控制/管理用的仅本地能力可以指定此能力是否应该批处理 `EndAbility()` 调用（半自动），或者不批处理 `EndAbility()` 调用（全自动），此时 `EndAbility()` 调用将在稍后以其自己的 RPC 发生。

GASShooter 暴露了一个蓝图（Blueprint）节点来允许批处理能力，上述的仅本地能力使用它来触发批处理能力。

![激活批处理能力](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.16 网络安全策略（Net Security Policy）
`GameplayAbility` 的 `网络安全策略（NetSecurityPolicy）` 决定了能力应该在网络上的何处执行。它提供了防止客户端尝试执行受限能力的保护。

| `网络安全策略（NetSecurityPolicy）` | 描述 |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `客户端或服务器（ClientOrServer）` | 没有安全要求。客户端或服务器可以自由触发此能力的执行和终止。 |
| `仅服务器执行（ServerOnlyExecution）` | 客户端请求执行此能力将被服务器忽略。客户端仍然可以请求服务器取消或结束此能力。 |
| `仅服务器终止（ServerOnlyTermination）` | 客户端请求取消或结束此能力将被服务器忽略。客户端仍然可以请求执行此能力。 |
| `仅服务器（ServerOnly）` | 服务器控制此能力的执行和终止。客户端的任何请求都将被忽略。 |

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-at"></a>
### 4.7 能力任务（Ability Tasks）

<a name="concepts-at-definition"></a>
### 4.7.1 能力任务定义（Ability Task Definition）
`GameplayAbilities` 只在一帧内执行。这本身并不能提供太多灵活性。为了执行随时间推移发生的动作或需要响应在稍后某个时间点触发的委托，我们使用称为 `AbilityTasks` 的延迟动作（latent actions）。

GAS 自带了许多现成的 `AbilityTasks`：
* 使用 `根运动源（RootMotionSource）` 移动角色（Characters）的任务
* 播放动画蒙太奇（Animation Montages）的任务
* 响应 `属性（Attribute）` 变化的任务
* 响应 `GameplayEffect` 变化的任务
* 响应玩家输入的任务
* 以及更多

`UAbilityTask` 构造函数强制执行一个硬编码的全局最大值，即同时运行 1000 个并发 `AbilityTasks`。在为可能同时拥有数百个角色的游戏（如 RTS 游戏）设计 `GameplayAbilities` 时，请牢记这一点。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-at-definition"></a>
### 4.7.2 自定义能力任务（Custom Ability Tasks）
你经常需要创建自己的自定义 `AbilityTasks`（在 C++ 中）。示例项目附带了两个自定义 `AbilityTasks`：
1. `PlayMontageAndWaitForEvent` 是默认的 `PlayMontageAndWait` 和 `WaitGameplayEvent` `AbilityTasks` 的组合。它允许动画蒙太奇通过 `AnimNotifies` 将游戏事件（Gameplay Events）发送回启动它们的 `GameplayAbility`。使用它在动画蒙太奇的特定时间点触发动作。
1. `WaitReceiveDamage` 监听 `OwnerActor` 接收伤害。被动护甲叠加（passive armor stacks）`GameplayAbility` 在英雄接收一次伤害时移除一层护甲。

`AbilityTasks` 由以下部分组成：
* 创建 `AbilityTask` 新实例的静态函数
* 当 `AbilityTask` 完成其目的时广播的委托
* 一个 `Activate()` 函数来启动其主要工作、绑定外部委托等
* 一个 `OnDestroy()` 函数用于清理，包括它绑定的外部委托
* 它绑定的任何外部委托的回调函数
* 成员变量和任何内部辅助函数

**注意：** `AbilityTasks` 只能声明一种类型的输出委托。你所有的输出委托都必须是这种类型，无论它们是否使用参数。为未使用的委托参数传递默认值。

`AbilityTasks` 只在运行拥有该 `GameplayAbility` 的客户端或服务器上运行；然而，`AbilityTasks` 可以通过在 `AbilityTask` 构造函数中设置 `bSimulatedTask = true;`，重写 `virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`，并将任何成员变量设置为可复制来在模拟客户端（simulated clients）上运行。这只在少数情况下有用，例如移动 `AbilityTasks`，在这些场景中你不想复制每一次移动变化，而是模拟整个移动 `AbilityTask`。所有 `根运动源（RootMotionSource）` `AbilityTasks` 都是这样做的。参见 `AbilityTask_MoveToLocation.h/.cpp` 作为示例。

`AbilityTasks` 可以通过在 `AbilityTask` 构造函数中设置 `bTickingTask = true;` 并重写 `virtual void TickTask(float DeltaTime);` 来实现 `Tick`。当你需要在帧之间平滑插值（lerp）时，这非常有用。参见 `AbilityTask_MoveToLocation.h/.cpp` 作为示例。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-at-using"></a>
### 4.7.3 使用能力任务（Using Ability Tasks）
在 C++ 中创建和激活 `AbilityTask`（来自 `GDGA_FireGun.cpp`）：
```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

在蓝图（Blueprint）中，我们只需使用为 `AbilityTask` 创建的蓝图节点。我们不需要调用 `ReadyForActivation()`。这由 `Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp` 自动调用。`K2Node_LatentGameplayTaskCall` 还会自动调用 `BeginSpawningActor()` 和 `FinishSpawningActor()`（如果它们存在于你的 `AbilityTask` 类中）（参见 `AbilityTask_WaitTargetData`）。再次强调，`K2Node_LatentGameplayTaskCall` 只为蓝图自动执行这些操作。在 C++ 中，我们必须手动调用 `ReadyForActivation()`、`BeginSpawningActor()` 和 `FinishSpawningActor()`。

![蓝图 WaitTargetData AbilityTask](https://github.com/tranek/GASDocumentation/raw/master/Images/abilitytask.png)

要手动取消一个 `AbilityTask`，只需在蓝图（称为 `Async Task Proxy`）或 C++ 中调用 `AbilityTask` 对象的 `EndTask()`。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-at-rms"></a>
### 4.7.4 根运动源能力任务（Root Motion Source Ability Tasks）
GAS 附带了用于随时间移动 `角色（Characters）` 的 `AbilityTasks`，用于击退、复杂跳跃、拉拽和冲刺等功能，使用挂接到 `角色移动组件（CharacterMovementComponent）` 的 `根运动源（Root Motion Sources）`。

**注意：** 预测 `根运动源（RootMotionSource）` `AbilityTasks` 在引擎版本 4.19 和 4.25+ 上可以正常工作。预测在引擎版本 4.20-4.24 上有 bug；然而，`AbilityTasks` 在多人游戏中仍能执行其功能，只有轻微的网络修正，并且在单人游戏中完美运行。可以从 4.25 中 cherry pick [预测修复](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c) 到自定义的 4.20-4.24 引擎中。

**[⬆ 回到顶部](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 游戏提示（Gameplay Cues）

<a name="concepts-gc-definition"></a>
#### 4.8.1 游戏提示的定义（Gameplay Cue Definition）
`GameplayCues`（`GC`）用于执行与游戏玩法无关的表现效果，如音效、粒子效果、镜头抖动等。`GameplayCues` 通常会被同步复制（除非显式地在本地 `Executed`、`Added` 或 `Removed`），并且支持预测。

我们通过向 `GameplayCueManager` 发送一个带有**必须以 `GameplayCue.` 为父名称**的对应 `GameplayTag` 以及事件类型（`Execute`、`Add` 或 `Remove`）来触发 `GameplayCues`，这是通过 `ASC` 完成的。`GameplayCueNotify` 对象和其他实现了 `IGameplayCueInterface` 接口的 `Actors` 可以根据 `GameplayCue` 的 `GameplayTag`（`GameplayCueTag`）来订阅这些事件。

**注意：** 再次强调，`GameplayCue` 的 `GameplayTags` 需要以父级 `GameplayTag` `GameplayCue` 开头。例如，一个有效的 `GameplayCue` `GameplayTag` 可以是 `GameplayCue.A.B.C`。

`GameplayCueNotifies` 有两种类型：`Static`（静态）和 `Actor`（Actor 类型）。它们响应不同的事件，不同类型的 `GameplayEffects` 可以触发它们。请用你的逻辑重写对应的事件。

| `GameplayCue` 类型                                                                                                                  | 事件              | `GameplayEffect` 类型    | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant` 或 `Periodic`  | 静态 `GameplayCueNotifies` 在 `ClassDefaultObject` 上运行（即没有实例），非常适合一次性效果，如命中冲击。                                                                                                                                                                                                                                                                                                                                                                        |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` 或 `Remove` | `Duration` 或 `Infinite` | Actor 类型的 `GameplayCueNotifies` 在 `Added` 时会生成一个新实例。因为它们是实例化的，所以可以在一段时间内执行操作，直到被 `Removed`。这些非常适合循环播放的声音和粒子效果，当支持的 `Duration` 或 `Infinite` `GameplayEffect` 被移除时或通过手动调用移除时它们也会被移除。它们还提供了选项来管理同时允许 `Added` 多少个，这样同一效果的多次应用只会启动一次声音或粒子。 |

`GameplayCueNotifies` 从技术上讲可以响应任何事件，但以上是我们通常使用它们的方式。

**注意：** 使用 `GameplayCueNotify_Actor` 时，请勾选 `Auto Destroy on Remove`，否则后续对该 `GameplayCueTag` 的 `Add` 调用将不会生效。

当使用 `Full` 以外的 `ASC` [同步模式（Replication Mode）](#concepts-asc-rm)时，`Add` 和 `Remove` `GC` 事件会在服务器玩家（监听服务器）上触发两次——一次是应用 `GE` 时，另一次是通过"Minimal" `NetMultiCast` 发送给客户端时。但是，`WhileActive` 事件仍然只会触发一次。所有事件在客户端上都只会触发一次。

示例项目包含一个用于眩晕和冲刺效果的 `GameplayCueNotify_Actor`。它还有一个用于火枪弹丸撞击的 `GameplayCueNotify_Static`。这些 `GCs` 可以通过[本地触发](#concepts-gc-local)而不是通过 `GE` 同步复制来进一步优化。我在示例项目中选择展示的是初学者使用方式。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 触发游戏提示（Triggering Gameplay Cues）

在 `GameplayEffect` 成功应用（未被标签或免疫阻止）时，填写所有应该触发的 `GameplayCues` 的 `GameplayTags`。

![从 GameplayEffect 触发 GameplayCue](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility` 提供了蓝图节点来 `Execute`、`Add` 或 `Remove` `GameplayCues`。

![从 GameplayAbility 触发 GameplayCue](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

在 C++ 中，你可以直接在 `ASC` 上调用函数（或在你的 `ASC` 子类中将它们暴露给蓝图）：

```c++
/** GameplayCues can also come on their own. These take an optional effect context to pass through hit result, etc */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Add a persistent gameplay cue */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Remove a persistent gameplay cue */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);

/** Removes any GameplayCue added on its own, i.e. not as part of a GameplayEffect. */
void RemoveAllGameplayCues();
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 4.8.3 本地游戏提示（Local Gameplay Cues）
从 `GameplayAbilities` 和 `ASC` 触发 `GameplayCues` 的公开函数默认会进行同步复制。每个 `GameplayCue` 事件都是一个多播 RPC（multicast RPC）。这可能导致大量的 RPC。GAS 还强制限制每次网络更新中相同的 `GameplayCue` RPC 最多只能有两个。我们可以通过尽可能使用本地 `GameplayCues` 来避免这个问题。本地 `GameplayCues` 只会在单个客户端上 `Execute`、`Add` 或 `Remove`。

可以使用本地 `GameplayCues` 的场景：
* 弹丸撞击
* 近战碰撞撞击
* 从动画蒙太奇（Animation Montage）触发的 `GameplayCues`

你应该添加到 `ASC` 子类中的本地 `GameplayCue` 函数：

```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

如果一个 `GameplayCue` 是本地 `Added` 的，那么它应该本地 `Removed`。如果它是通过同步复制 `Added` 的，那么应该通过同步复制来 `Removed`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 4.8.4 游戏提示参数（Gameplay Cue Parameters）
`GameplayCues` 接收一个 `FGameplayCueParameters` 结构体作为参数，其中包含 `GameplayCue` 的额外信息。如果你从 `GameplayAbility` 或 `ASC` 上的函数手动触发 `GameplayCue`，那么你必须手动填充传递给 `GameplayCue` 的 `GameplayCueParameters` 结构体。如果 `GameplayCue` 是由 `GameplayEffect` 触发的，那么以下变量会自动填充到 `GameplayCueParameters` 结构体中：

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude（如果 `GameplayEffect` 在 `GameplayCue` 标签容器上方的下拉菜单中选择了一个用于量级的 `Attribute`，并且有一个对应的影响该 `Attribute` 的 `Modifier`）

`GameplayCueParameters` 结构体中的 `SourceObject` 变量是一个很好的位置，可以在手动触发 `GameplayCue` 时向其传递任意数据。

**注意：** 参数结构体中的一些变量（如 `Instigator`）可能已经存在于 `EffectContext` 中。`EffectContext` 还可以包含一个 `FHitResult`，用于确定在世界中生成 `GameplayCue` 的位置。子类化 `EffectContext` 是向 `GameplayCues` 传递更多数据的一个好方法，特别是那些由 `GameplayEffect` 触发的。

请参阅 [`UAbilitySystemGlobals`](#concepts-asg) 中填充 `GameplayCueParameters` 结构体的 3 个函数以获取更多信息。它们是虚函数，因此你可以重写它们来自动填充更多信息。

```c++
/** Initialize GameplayCue Parameters */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 4.8.5 游戏提示管理器（Gameplay Cue Manager）
默认情况下，`GameplayCueManager` 会扫描整个游戏目录以查找 `GameplayCueNotifies` 并在运行时将它们加载到内存中。我们可以通过在 `DefaultGame.ini` 中设置来更改 `GameplayCueManager` 扫描的路径。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

我们确实希望 `GameplayCueManager` 扫描并找到所有的 `GameplayCueNotifies`；但是，我们不希望它在运行时异步加载每一个。这会将每个 `GameplayCueNotify` 及其引用的所有音效和粒子都放入内存，无论它们是否在关卡中被使用。在像 Paragon 这样的大型游戏中，这可能会导致数百兆字节的不必要资源占用内存，并在启动时导致卡顿和游戏冻结。

异步加载每个 `GameplayCue` 的替代方案是仅在游戏中触发时才异步加载 `GameplayCues`。这减少了不必要的内存使用和异步加载每个 `GameplayCue` 可能导致的游戏硬冻结，代价是特定 `GameplayCue` 在游戏过程中首次触发时可能会有延迟效果。在 SSD 上这种潜在延迟几乎不存在。我没有在 HDD 上测试过。如果在 UE 编辑器中使用此选项，首次加载 GameplayCues 时可能会有轻微的卡顿或冻结（如果编辑器需要编译粒子系统的话）。这在构建版本中不是问题，因为粒子系统已经被编译好了。

首先我们必须子类化 `UGameplayCueManager` 并在 `DefaultGame.ini` 中告诉 `AbilitySystemGlobals` 类使用我们的 `UGameplayCueManager` 子类。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

在我们的 `UGameplayCueManager` 子类中，重写 `ShouldAsyncLoadRuntimeObjectLibraries()`。

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 4.8.6 阻止游戏提示触发（Prevent Gameplay Cues from Firing）
有时我们不希望 `GameplayCues` 触发。例如，如果我们格挡了一次攻击，我们可能不想播放伤害 `GameplayEffect` 上附带的命中冲击效果，或者想播放一个自定义效果来替代。我们可以在 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 中通过调用 `OutExecutionOutput.MarkGameplayCuesHandledManually()` 然后手动将 `GameplayCue` 事件发送到 `Target` 或 `Source` 的 `ASC` 来实现这一点。

如果你不希望特定 `ASC` 上的任何 `GameplayCues` 触发，你可以设置 `AbilitySystemComponent->bSuppressGameplayCues = true;`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gc-batching"></a>
#### 4.8.7 游戏提示批处理（Gameplay Cue Batching）
每个触发的 `GameplayCue` 都是一个不可靠的 NetMulticast RPC。在我们同时触发多个 `GCs` 的情况下，有一些优化方法可以将它们压缩成一个 RPC，或通过发送更少的数据来节省带宽。

<a name="concepts-gc-batching-manualrpc"></a>
##### 4.8.7.1 手动 RPC（Manual RPC）
假设你有一把射出八颗弹丸的霰弹枪。那就是八个射线检测和撞击 `GameplayCues`。[GASShooter](https://github.com/tranek/GASShooter) 采用了一种简单的方法，通过将所有射线信息存储在 [`EffectContext`](#concepts-ge-ec) 中作为 [`TargetData`](#concepts-targeting-data) 来将它们合并为一个 RPC。虽然这将 RPC 从八个减少到一个，但在那一个 RPC 中仍然通过网络发送了大量数据（约 500 字节）。一种更优化的方法是发送一个包含自定义结构体的 RPC，在其中高效地编码命中位置，或者给它一个随机种子数来在接收端重建/近似撞击位置。客户端随后会解包这个自定义结构体并将其转换为[本地执行的 `GameplayCues`](#concepts-gc-local)。

工作原理：
1. 声明一个 `FScopedGameplayCueSendContext`。这会抑制 `UGameplayCueManager::FlushPendingCues()` 直到它离开作用域，意味着所有 `GameplayCues` 将会排队等待直到 `FScopedGameplayCueSendContext` 离开作用域。
1. 重写 `UGameplayCueManager::FlushPendingCues()` 以根据某些自定义 `GameplayTag` 将可以批处理的 `GameplayCues` 合并到你的自定义结构体中，并通过 RPC 发送给客户端。
1. 客户端接收自定义结构体并将其解包为本地执行的 `GameplayCues`。

当你的 `GameplayCues` 需要 `GameplayCueParameters` 所不提供的特定参数，且你不想将它们添加到 `EffectContext` 中时（如伤害数值、暴击指示器、护盾破碎指示器、致命一击指示器等），也可以使用此方法。

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>
##### 4.8.7.2 一个 GE 上的多个 GC（Multiple GCs on one GE）
一个 `GameplayEffect` 上的所有 `GameplayCues` 已经会在一个 RPC 中发送。默认情况下，`UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()` 会在不可靠的 NetMulticast 中发送整个 `GameplayEffectSpec`（但会转换为 `FGameplayEffectSpecForRPC`），而不管 `ASC` 的同步模式（`Replication Mode`）。这可能会占用大量带宽，取决于 `GameplayEffectSpec` 中包含什么内容。我们可以通过设置控制台变量 `AbilitySystem.AlwaysConvertGESpecToGCParams 1` 来进行优化。这会将 `GameplayEffectSpecs` 转换为 `FGameplayCueParameter` 结构体并通过 RPC 发送，而不是发送整个 `FGameplayEffectSpecForRPC`。这可能会节省带宽，但也会减少信息量，具体取决于 `GESpec` 如何转换为 `GameplayCueParameters` 以及你的 `GCs` 需要知道什么。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gc-events"></a>
#### 4.8.8 游戏提示事件（Gameplay Cue Events）
`GameplayCues` 响应特定的 `EGameplayCueEvents`：

| `EGameplayCueEvent` | 描述                                                                                                                                                                                                                                                                                                                         |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OnActive`          | 当 `GameplayCue` 被激活（添加）时调用。                                                                                                                                                                                                                                                                                   |
| `WhileActive`       | 当 `GameplayCue` 处于激活状态时调用，即使它并非刚刚被应用（中途加入等情况）。这不是 `Tick`！它像 `OnActive` 一样只在 `GameplayCueNotify_Actor` 被添加或变得相关时调用一次。如果你需要 `Tick()`，直接使用 `GameplayCueNotify_Actor` 的 `Tick()` 即可。毕竟它是一个 `AActor`。 |
| `Removed`           | 当 `GameplayCue` 被移除时调用。响应此事件的蓝图 `GameplayCue` 函数是 `OnRemove`。                                                                                                                                                                                                             |
| `Executed`          | 当 `GameplayCue` 被执行时调用：即时效果或周期性 `Tick()`。响应此事件的蓝图 `GameplayCue` 函数是 `OnExecute`。                                                                                                                                                                     |

对于 `GameplayCue` 开始时发生的任何事情，使用 `OnActive`，但后来加入的玩家错过也没关系。对于持续进行的效果，使用 `WhileActive`，你希望后来加入的玩家也能看到。例如，如果你有一个 MOBA 中塔楼建筑爆炸的 `GameplayCue`，你会将初始爆炸粒子系统和爆炸音效放在 `OnActive` 中，而将任何残留的持续火焰粒子或声音放在 `WhileActive` 中。在这种场景中，后来加入的玩家重放 `OnActive` 中的初始爆炸是没有意义的，但你希望他们看到爆炸后地面上持续循环的火焰效果（来自 `WhileActive`）。`OnRemove` 应该清理在 `OnActive` 和 `WhileActive` 中添加的所有内容。每当一个 Actor 进入 `GameplayCueNotify_Actor` 的相关性范围时，`WhileActive` 就会被调用。每当一个 Actor 离开 `GameplayCueNotify_Actor` 的相关性范围时，`OnRemove` 就会被调用。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gc-reliability"></a>
#### 4.8.9 游戏提示可靠性（Gameplay Cue Reliability）

`GameplayCues` 通常应被视为不可靠的，因此不适用于直接影响游戏玩法的任何内容。

**已执行的 `GameplayCues`（Executed）：** 这些 `GameplayCues` 通过不可靠的多播（unreliable multicasts）应用，始终是不可靠的。

**从 `GameplayEffects` 应用的 `GameplayCues`：**
* 自主代理（Autonomous proxy）可靠地接收 `OnActive`、`WhileActive` 和 `OnRemove`
`FActiveGameplayEffectsContainer::NetDeltaSerialize()` 调用 `UAbilitySystemComponent::HandleDeferredGameplayCues()` 来调用 `OnActive` 和 `WhileActive`。`FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()` 调用 `OnRemoved`。
* 模拟代理（Simulated proxies）可靠地接收 `WhileActive` 和 `OnRemove`
`UAbilitySystemComponent::MinimalReplicationGameplayCues` 的同步复制会调用 `WhileActive` 和 `OnRemove`。`OnActive` 事件通过不可靠的多播调用。

**未通过 `GameplayEffect` 应用的 `GameplayCues`：**
* 自主代理（Autonomous proxy）可靠地接收 `OnRemove`
`OnActive` 和 `WhileActive` 事件通过不可靠的多播调用。
* 模拟代理（Simulated proxies）可靠地接收 `WhileActive` 和 `OnRemove`
`UAbilitySystemComponent::MinimalReplicationGameplayCues` 的同步复制会调用 `WhileActive` 和 `OnRemove`。`OnActive` 事件通过不可靠的多播调用。

如果你需要 `GameplayCue` 中的某些内容是"可靠的"，那么通过 `GameplayEffect` 来应用它，并使用 `WhileActive` 添加特效，使用 `OnRemove` 移除特效。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-asg"></a>
### 4.9 技能系统全局设置（Ability System Globals）
[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html) 类保存着关于 GAS 的全局信息。大多数变量可以在 `DefaultGame.ini` 中设置。通常你不需要与这个类交互，但你应该知道它的存在。如果你需要子类化诸如 [`GameplayCueManager`](#concepts-gc-manager) 或 [`GameplayEffectContext`](#concepts-ge-context) 之类的东西，你必须通过 `AbilitySystemGlobals` 来完成。

要子类化 `AbilitySystemGlobals`，请在 `DefaultGame.ini` 中设置类名：
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>
#### 4.9.1 InitGlobalData()
在 UE 4.24 到 5.2 之间，必须调用 `UAbilitySystemGlobals::Get().InitGlobalData()` 才能使用 [`TargetData`](#concepts-targeting-data)，否则你会遇到与 `ScriptStructCache` 相关的错误，客户端也会从服务器断开连接。这个函数在项目中只需要调用一次。Fortnite 从 `UAssetManager::StartInitialLoading()` 中调用它，而 Paragon 从 `UEngine::Init()` 中调用。我发现将它放在 `UAssetManager::StartInitialLoading()` 中是一个好位置，如示例项目所示。我认为这是你应该复制到项目中的样板代码，以避免 `TargetData` 相关的问题。从 5.3 版本开始，它会自动调用。

如果你在使用 `AbilitySystemGlobals` 的 `GlobalAttributeSetDefaultsTableNames` 时遇到崩溃，你可能需要像 Fortnite 那样在 `AssetManager` 或 `GameInstance` 中更晚地调用 `UAbilitySystemGlobals::Get().InitGlobalData()`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-p"></a>
### 4.10 预测（Prediction）
GAS 开箱即用地支持客户端预测；然而，它并不能预测所有内容。GAS 中的客户端预测意味着客户端不必等待服务器的许可就可以激活 `GameplayAbility` 并应用 `GameplayEffects`。它可以"预测"服务器会给予它许可，并预测它将对哪些目标应用 `GameplayEffects`。然后服务器在客户端激活后的网络延迟时间内运行 `GameplayAbility`，并告诉客户端其预测是否正确。如果客户端的任何预测是错误的，它将"回滚"其"错误预测"的更改以匹配服务器。

GAS 相关预测的权威来源是插件源代码中的 `GameplayPrediction.h`。

Epic 的理念是只预测你"能侥幸成功"的内容。例如，Paragon 和 Fortnite 不预测伤害。它们很可能使用 [`ExecutionCalculations`](#concepts-ge-ec) 来计算伤害，而这些本来就无法被预测。这并不是说你不能尝试预测某些内容（如伤害）。当然，如果你做到了并且效果很好，那很棒。

> ……我们也并没有全力投入"预测一切：无缝且自动"的解决方案。我们仍然认为玩家预测最好保持在最低限度（意思是：预测你能侥幸成功的最少量内容）。

*Dave Ratti 来自 Epic 在新的[网络预测插件（Network Prediction Plugin）](#concepts-p-npp)中的评论*

**可预测的内容：**
> * 技能激活（Ability activation）
> * 触发事件（Triggered Events）
> * 游戏效果（GameplayEffect）应用：
>    * 属性修改（Attribute modification）（例外：执行计算（Executions）目前不能预测，只有属性修改器（Attribute Modifiers）可以）
>    * 游戏标签（GameplayTag）修改
> * 游戏提示（Gameplay Cue）事件（无论是来自预测性游戏效果内部还是独立触发的）
> * 蒙太奇（Montages）
> * 移动（Movement）（内置于 UE 的 UCharacterMovement 中）

**不可预测的内容：**
> * 游戏效果（GameplayEffect）移除
> * 游戏效果（GameplayEffect）周期性效果（持续伤害的 tick）

*来自 `GameplayPrediction.h`*

虽然我们可以预测 `GameplayEffect` 的应用，但不能预测 `GameplayEffect` 的移除。我们解决此限制的一种方法是在想要移除 `GameplayEffect` 时预测一个相反的效果。假设我们预测了 40% 的移动速度减缓。我们可以通过应用 40% 的移动速度加成来预测性地移除它。然后同时移除两个 `GameplayEffects`。这并非适用于每种场景，对预测 `GameplayEffect` 移除的支持仍然是需要的。Epic 的 Dave Ratti 已表达希望在 [GAS 的未来迭代](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)中添加此功能。

因为我们不能预测 `GameplayEffects` 的移除，所以我们不能完全预测 `GameplayAbility` 的冷却时间，也没有针对冷却时间的反向 `GameplayEffect` 变通方案。服务器同步复制的冷却 GE（`Cooldown GE`）将存在于客户端上，任何绕过此机制的尝试（例如使用 `Minimal` 同步模式）都会被服务器拒绝。这意味着延迟较高的客户端需要更长时间来告诉服务器进入冷却状态并接收服务器冷却 GE 的移除。这意味着延迟较高的玩家会比延迟较低的玩家有更低的射速，使他们在对抗低延迟玩家时处于劣势。Fortnite 通过使用自定义记录方式而非冷却 GE（`Cooldown GEs`）来避免此问题。

关于预测伤害，我个人不建议这样做，尽管这是大多数人在开始使用 GAS 时最先尝试的事情之一。我尤其不建议尝试预测死亡。虽然你可以预测伤害，但这样做很棘手。如果你错误预测了伤害的应用，玩家会看到敌人的血量突然回弹。如果你尝试预测死亡，这会特别尴尬和令人沮丧。假设你错误预测了一个角色（`Character`）的死亡，它开始播放布娃娃效果，结果在服务器纠正后停止布娃娃动画并继续向你射击。

**注意：** `Instant` `GameplayEffects`（如 `Cost GEs`）改变自身的 `Attributes` 时可以无缝预测，预测对其他角色的 `Instant` `Attribute` 变化会在其 `Attributes` 上显示短暂的异常或"闪烁"。预测的 `Instant` `GameplayEffects` 实际上被当作 `Infinite` `GameplayEffects` 处理，以便在错误预测时可以回滚。当服务器的 `GameplayEffect` 被应用时，可能会同时存在两个相同的 `GameplayEffect`，导致 `Modifier` 在短暂时刻内被应用两次或完全不被应用。它最终会自行修正，但有时这种闪烁对玩家来说是可察觉的。

GAS 预测实现试图解决的问题：
> 1. "我能做这个吗？"预测的基本协议。
> 2. "撤销" 当预测失败时如何撤销副作用。
> 3. "重做" 如何避免重放我们在本地预测过但也从服务器同步过来的副作用。
> 4. "完整性" 如何确保我们真正预测了所有副作用。
> 5. "依赖关系" 如何管理依赖性预测和预测事件链。
> 6. "覆盖" 如何预测性地覆盖原本由服务器同步/拥有的状态。

*来自 `GameplayPrediction.h`*

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-p-key"></a>
#### 4.10.1 预测密钥（Prediction Key）
GAS 的预测（Prediction）机制基于 `Prediction Key`（预测密钥）的概念运作，它是一个整数标识符，由客户端在激活 `GameplayAbility` 时生成。

* 客户端在激活 `GameplayAbility` 时生成一个预测密钥（Prediction Key）。这就是 `Activation Prediction Key`（激活预测密钥）。
* 客户端通过 `CallServerTryActivateAbility()` 将此预测密钥发送给服务器。
* 客户端将此预测密钥添加到它在预测密钥有效期间所施加的所有 `GameplayEffects` 上。
* 客户端的预测密钥超出作用域。同一 `GameplayAbility` 中后续的预测效果需要一个新的[作用域预测窗口（Scoped Prediction Window）](#concepts-p-windows)。


* 服务器从客户端接收到预测密钥。
* 服务器将此预测密钥添加到它所施加的所有 `GameplayEffects` 上。
* 服务器将预测密钥复制回客户端。


* 客户端从服务器接收到带有用于施加它们的预测密钥的已复制 `GameplayEffects`。如果任何已复制的 `GameplayEffects` 与客户端使用相同预测密钥施加的 `GameplayEffects` 匹配，则说明预测正确。在目标上会暂时存在两份 `GameplayEffect` 的副本，直到客户端移除其预测的那一份。
* 客户端从服务器接收回预测密钥。这就是 `Replicated Prediction Key`（已复制预测密钥）。此预测密钥现在被标记为过期。
* 客户端移除它使用现已过期的已复制预测密钥创建的**所有** `GameplayEffects`。由服务器复制过来的 `GameplayEffects` 将继续保留。客户端添加的但没有从服务器收到匹配复制版本的任何 `GameplayEffects` 都是预测错误的。

预测密钥保证在 `GameplayAbilities` 中从 `Activation`（激活）开始的一个原子指令分组"窗口"期间有效，该有效性来自激活预测密钥。你可以将其理解为仅在一帧内有效。来自延迟动作 `AbilityTasks` 的任何回调将不再拥有有效的预测密钥，除非该 `AbilityTask` 内置了同步点（Synch Point），可以生成新的[作用域预测窗口（Scoped Prediction Window）](#concepts-p-windows)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-p-windows"></a>
#### 4.10.2 在技能中创建新的预测窗口（Prediction Windows）
要在 `AbilityTasks` 的回调中预测更多操作，我们需要使用新的作用域预测密钥（Scoped Prediction Key）创建一个新的作用域预测窗口（Scoped Prediction Window）。这有时被称为客户端和服务器之间的同步点（Synch Point）。某些 `AbilityTasks`（如所有与输入相关的任务）内置了创建新作用域预测窗口的功能，这意味着 `AbilityTasks` 回调中的原子代码拥有可使用的有效作用域预测密钥。其他任务（如 `WaitDelay` 任务）没有为其回调创建新作用域预测窗口的内置代码。如果你需要在没有内置创建作用域预测窗口代码的 `AbilityTask`（如 `WaitDelay`）之后预测操作，我们必须使用带有 `OnlyServerWait` 选项的 `WaitNetSync` `AbilityTask` 手动执行此操作。当客户端命中带有 `OnlyServerWait` 的 `WaitNetSync` 时，它会基于 `GameplayAbility` 的激活预测密钥生成一个新的作用域预测密钥，通过 RPC 将其发送到服务器，并将其添加到它所施加的任何新 `GameplayEffects` 上。当服务器命中带有 `OnlyServerWait` 的 `WaitNetSync` 时，它会等待直到从客户端收到新的作用域预测密钥后再继续。此作用域预测密钥执行与激活预测密钥相同的流程——应用于 `GameplayEffects` 并复制回客户端以标记为过期。作用域预测密钥在超出作用域之前一直有效，即作用域预测窗口已关闭。因此，同样的，只有原子操作（非延迟操作）才能使用新的作用域预测密钥。

你可以根据需要创建任意数量的作用域预测窗口。

如果你想将同步点功能添加到你自己的自定义 `AbilityTasks` 中，请参考输入相关的任务是如何在其中注入 `WaitNetSync` `AbilityTask` 代码的。

**注意：** 使用 `WaitNetSync` 时，这会阻止服务器的 `GameplayAbility` 继续执行，直到它收到客户端的消息。这可能会被入侵游戏的恶意用户利用，故意延迟发送他们的新作用域预测密钥。虽然 Epic 谨慎地使用 `WaitNetSync`，但它建议如果你对此有所顾虑，可以考虑构建一个带有延迟的新版本 `AbilityTask`，在没有客户端响应时自动继续。

示例项目在冲刺（Sprint）`GameplayAbility` 中使用 `WaitNetSync` 来在每次施加体力消耗时创建新的作用域预测窗口，以便我们可以预测它。理想情况下，我们希望在施加消耗和冷却时拥有有效的预测密钥。

如果你有一个预测的 `GameplayEffect` 在拥有者客户端上播放了两次，说明你的预测密钥已过期，你正在经历"重做"问题。你通常可以通过在施加 `GameplayEffect` 之前放置一个带有 `OnlyServerWait` 的 `WaitNetSync` `AbilityTask` 来创建新的作用域预测密钥以解决此问题。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-p-spawn"></a>
#### 4.10.3 预测性生成 Actor（Predictively Spawning Actors）
在客户端上预测性地生成 `Actors` 是一个高级主题。GAS 没有提供开箱即用的功能来处理这个问题（`SpawnActor` `AbilityTask` 只在服务器上生成 `Actor`）。核心概念是在客户端和服务器上同时生成一个可复制的 `Actor`。

如果该 `Actor` 只是装饰性的或不具有任何游戏玩法目的，简单的解决方案是重写 `Actor` 的 `IsNetRelevantFor()` 函数，以限制服务器向拥有者客户端复制。拥有者客户端将拥有其本地生成的版本，而服务器和其他客户端将拥有服务器的已复制版本。
```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

如果生成的 `Actor` 影响游戏玩法（如需要预测伤害的投射物），那么你需要超出本文档范围的高级逻辑。请查看 Epic Games 的 GitHub 上 UnrealTournament 是如何预测性地生成投射物的。他们有一个仅在拥有者客户端上生成的虚拟投射物（dummy projectile），它与服务器的已复制投射物进行同步。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-p-future"></a>
#### 4.10.4 GAS 中预测（Prediction）的未来
`GameplayPrediction.h` 指出，在未来他们可能会添加预测 `GameplayEffect` 移除和周期性 `GameplayEffects` 的功能。

来自 Epic 的 Dave Ratti [表达了兴趣](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)来修复用于预测冷却的 `延迟协调（latency reconciliation）` 问题，该问题使延迟较高的玩家相对于延迟较低的玩家处于劣势。

Epic 推出的新 [`网络预测（Network Prediction）` 插件](#concepts-p-npp) 预计将与 GAS 完全互操作，就像 `CharacterMovementComponent` 之前那样。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-p-npp"></a>
#### 4.10.5 网络预测插件（Network Prediction Plugin）
Epic 最近启动了一项计划，用新的 `网络预测（Network Prediction）` 插件来替换 `CharacterMovementComponent`。这个插件仍处于非常早期的阶段，但可以在 Unreal Engine 的 GitHub 上进行非常早期的访问。目前还不清楚它将在哪个未来版本的引擎中首次作为实验性测试版亮相。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-targeting"></a>
### 4.11 目标选取（Targeting）

<a name="concepts-targeting-data"></a>
#### 4.11.1 目标数据（Target Data）
[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html) 是一种通用的目标数据（Targeting Data）结构，用于在网络上传递。`TargetData` 通常持有 `AActor`/`UObject` 引用、`FHitResults` 以及其他通用的位置/方向/原点信息。不过，你可以对其进行子类化，以将基本上任何你想要的内容放入其中，作为一种[在 `GameplayAbilities` 中在客户端和服务器之间传递数据](#concepts-ga-data)的简单方式。基础结构体 `FGameplayAbilityTargetData` 不应该直接使用，而应该被子类化。`GAS` 自带了一些开箱即用的 `FGameplayAbilityTargetData` 子类结构体，位于 `GameplayAbilityTargetTypes.h` 中。

`TargetData` 通常由 [`目标 Actor（Target Actors）`](#concepts-targeting-actors) 产生或**手动创建**，并被 [`AbilityTasks`](#concepts-at) 和 [`GameplayEffects`](#concepts-ge) 通过 [`EffectContext`](#concepts-ge-context) 消费。由于存在于 `EffectContext` 中，[`执行（Executions）`](#concepts-ge-ec)、[`MMCs`](#concepts-ge-mmc)、[`GameplayCues`](#concepts-gc) 以及 [`AttributeSet`](#concepts-as) 后端的函数都可以访问 `TargetData`。

我们通常不直接传递 `FGameplayAbilityTargetData`，而是使用 [`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html)，它内部有一个指向 `FGameplayAbilityTargetData` 的指针的 TArray。这个中间结构体为 `TargetData` 提供了多态性支持。

一个继承自 `FGameplayAbilityTargetData` 的示例：
```c++
USTRUCT(BlueprintType)
struct MYGAME_API FGameplayAbilityTargetData_CustomData : public FGameplayAbilityTargetData
{
    GENERATED_BODY()
public:

    FGameplayAbilityTargetData_CustomData()
    { }

    UPROPERTY()
    FName CoolName = NAME_None;

    UPROPERTY()
    FPredictionKey MyCoolPredictionKey;

    // This is required for all child structs of FGameplayAbilityTargetData
    virtual UScriptStruct* GetScriptStruct() const override
    {
    	return FGameplayAbilityTargetData_CustomData::StaticStruct();
    }

	// This is required for all child structs of FGameplayAbilityTargetData
    bool NetSerialize(FArchive& Ar, class UPackageMap* Map, bool& bOutSuccess)
    {
	    // The engine already defined NetSerialize for FName & FPredictionKey, thanks Epic!
        CoolName.NetSerialize(Ar, Map, bOutSuccess);
        MyCoolPredictionKey.NetSerialize(Ar, Map, bOutSuccess);
        bOutSuccess = true;
        return true;
    }
}

template<>
struct TStructOpsTypeTraits<FGameplayAbilityTargetData_CustomData> : public TStructOpsTypeTraitsBase2<FGameplayAbilityTargetData_CustomData>
{
	enum
	{
        WithNetSerializer = true // This is REQUIRED for FGameplayAbilityTargetDataHandle net serialization to work
	};
};
```
将目标数据添加到句柄的方法：
```c++
UFUNCTION(BlueprintPure)
FGameplayAbilityTargetDataHandle MakeTargetDataFromCustomName(const FName CustomName)
{
	// Create our target data type,
	// Handle's automatically cleanup and delete this data when the handle is destructed,
	// if you don't add this to a handle then be careful because this deals with memory management and memory leaks so its safe to just always add it to a handle at some point in the frame!
	FGameplayAbilityTargetData_CustomData* MyCustomData = new FGameplayAbilityTargetData_CustomData();
	// Setup the struct's information to use the inputted name and any other changes we may want to do
	MyCustomData->CoolName = CustomName;

	// Make our handle wrapper for Blueprint usage
	FGameplayAbilityTargetDataHandle Handle;
	// Add the target data to our handle
	Handle.Add(MyCustomData);
	// Output our handle to Blueprint
	return Handle
}
```

要获取值需要进行类型安全检查，因为从句柄的目标数据获取值的唯一方式是使用通用的 C/C++ 类型转换，这*不是*类型安全的，可能导致对象切片和崩溃。对于类型检查有多种方式（基本上取决于你的偏好），两种常见的方式是：
- 游戏标签（Gameplay Tag(s)）：你可以使用子类层次结构，当你知道某个代码架构的功能发生时，你可以转换为基父类型并获取其游戏标签（Gameplay Tag(s)），然后与之进行比较以转换继承的类。
- 脚本结构体与静态结构体（Script Struct & Static Structs）：你可以直接进行类比较（这可能涉及大量的 IF 语句或编写一些模板函数），下面是一个这样做的示例，但基本上你可以从任何 `FGameplayAbilityTargetData` 获取脚本结构体（这是它作为 `USTRUCT` 并要求任何继承类在 `GetScriptStruct` 中指定结构体类型的一个很好的优势），并比较它是否是你要查找的类型。下面是使用这些函数进行类型检查的示例：
```c++
UFUNCTION(BlueprintPure)
FName GetCoolNameFromTargetData(const FGameplayAbilityTargetDataHandle& Handle, const int Index)
{
    // NOTE, there is two versions of this '::Get(int32 Index)' function;
    // 1) const version that returns 'const FGameplayAbilityTargetData*', good for reading target data values
    // 2) non-const version that returns 'FGameplayAbilityTargetData*', good for modifying target data values
    FGameplayAbilityTargetData* Data = Handle.Get(Index); // This will valid check the index for you

    // Valid check we have something to use, null data means nothing to cast for
    if(Data == nullptr)
    {
       	return NAME_None;
    }
    // This is basically the type checking pass, static_cast does not have type safety, this is why we do this check.
    // If we don't do this then it will object slice the struct and thus we have no way of making sure its that type.
    if(Data->GetScriptStruct() == FGameplayAbilityTargetData_CustomData::StaticStruct())
    {
        // Here is when you would do the cast because we know its the correct type already
        FGameplayAbilityTargetData_CustomData* CustomData = static_cast<FGameplayAbilityTargetData_CustomData*>(Data);
        return CustomData->CoolName;
    }
    return NAME_None;
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-targeting-actors"></a>
#### 4.11.2 目标 Actor（Target Actors）
`GameplayAbilities` 使用 `WaitTargetData` `AbilityTask` 生成 [`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html) 来可视化和捕获来自世界的目标选取信息。`TargetActors` 可以选择性地使用 [`GameplayAbilityWorldReticles`](#concepts-targeting-reticles) 来显示当前目标。确认（Confirmation）后，目标选取信息以 [`TargetData`](#concepts-targeting-data) 的形式返回，然后可以传递给 `GameplayEffects`。

`TargetActors` 基于 `AActor`，因此它们可以拥有任何类型的可见组件来表示它们在**哪里**以及**如何**进行目标选取，例如静态网格体或贴花。静态网格体可用于可视化你的角色将要建造的物体的放置位置。贴花可用于在地面上显示效果范围区域。示例项目使用 [`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html) 在地面上配合贴花来表示流星技能的伤害效果范围区域。它们也可以不显示任何内容。例如，对于一把即时追踪到目标的射线武器来说，显示任何内容都没有意义，如 [GASShooter](https://github.com/tranek/GASShooter) 中所使用的那样。

它们使用基本的射线检测或碰撞重叠来捕获目标选取信息，并根据 `TargetActor` 的实现将结果作为 `FHitResults` 或 `AActor` 数组转换为 `TargetData`。`WaitTargetData` `AbilityTask` 通过其 `TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` 参数来确定何时确认目标。当**不**使用 `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant` 时，`TargetActor` 通常在 `Tick()` 上执行射线检测/重叠并根据其实现将其位置更新为 `FHitResult`。虽然这会在 `Tick()` 上执行射线检测/重叠，但通常并不严重，因为它不会被复制，而且你通常不会同时运行多个（尽管可以有更多）`TargetActor`。只需注意它使用 `Tick()`，一些复杂的 `TargetActors` 可能在其中做很多事情，如 GASShooter 中火箭发射器的副技能。虽然在 `Tick()` 上进行射线检测对客户端的响应非常及时，但如果性能影响太大，你可以考虑降低 `TargetActor` 的 tick 频率。在 `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant` 的情况下，`TargetActor` 立即生成、产生 `TargetData` 并销毁。`Tick()` 永远不会被调用。

| `EGameplayTargetingConfirmation::Type` | 何时确认目标                                                                                                                                                                                                                                                                                                                                     |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`                              | 目标选取立即发生，无需特殊逻辑或用户输入来决定何时"开火"。                                                                                                                                                                                                                                                   |
| `UserConfirmed`                        | 当用户在[技能绑定到 `Confirm` 输入](#concepts-ga-input)时确认目标选取，或通过调用 `UAbilitySystemComponent::TargetConfirm()` 来确认时，目标选取发生。`TargetActor` 也会响应绑定的 `Cancel` 输入或调用 `UAbilitySystemComponent::TargetCancel()` 来取消目标选取。                              |
| `Custom`                               | 游戏目标选取技能（GameplayTargeting Ability）通过调用 `UGameplayAbility::ConfirmTaskByInstanceName()` 来决定目标数据何时就绪。`TargetActor` 也会响应 `UGameplayAbility::CancelTaskByInstanceName()` 来取消目标选取。                                                                                              |
| `CustomMulti`                          | 游戏目标选取技能（GameplayTargeting Ability）通过调用 `UGameplayAbility::ConfirmTaskByInstanceName()` 来决定目标数据何时就绪。`TargetActor` 也会响应 `UGameplayAbility::CancelTaskByInstanceName()` 来取消目标选取。在产生数据时不应结束 `AbilityTask`。                                       |

并非每种 EGameplayTargetingConfirmation::Type 都被每个 `TargetActor` 所支持。例如，`AGameplayAbilityTargetActor_GroundTrace` 不支持 `Instant` 确认（Confirmation）。

`WaitTargetData` `AbilityTask` 接收一个 `AGameplayAbilityTargetActor` 类作为参数，并在每次激活 `AbilityTask` 时生成一个实例，当 `AbilityTask` 结束时销毁 `TargetActor`。`WaitTargetDataUsingActor` `AbilityTask` 接收一个已生成的 `TargetActor`，但在 `AbilityTask` 结束时仍然会销毁它。这两个 `AbilityTasks` 都不够高效，因为它们要么生成，要么需要一个新生成的 `TargetActor` 用于每次使用。它们非常适合原型开发，但在生产环境中，如果你有不断产生 `TargetData` 的情况（如自动步枪），你可能需要探索优化方案。GASShooter 有一个自定义的 [`AGameplayAbilityTargetActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h) 子类和一个从头编写的新 [`WaitTargetDataWithReusableActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) `AbilityTask`，允许你重用 `TargetActor` 而不销毁它。

`TargetActors` 默认不会被复制；但是，如果在你的游戏中向其他玩家显示本地玩家正在瞄准的位置是有意义的，可以使其支持复制。它们包含通过 `WaitTargetData` `AbilityTask` 上的 RPC 与服务器通信的默认功能。如果 `TargetActor` 的 `ShouldProduceTargetDataOnServer` 属性设置为 `false`，那么客户端将在确认时通过 `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` 中的 `CallServerSetReplicatedTargetData()` 将其 `TargetData` 通过 RPC 发送到服务器。如果 `ShouldProduceTargetDataOnServer` 为 `true`，客户端将在 `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` 中发送一个通用确认事件 `EAbilityGenericReplicatedEvent::GenericConfirm` RPC 到服务器，服务器将在收到 RPC 后执行射线检测或重叠检查以在服务器上产生数据。如果客户端取消目标选取，它将在 `UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback` 中发送一个通用取消事件 `EAbilityGenericReplicatedEvent::GenericCancel` RPC 到服务器。如你所见，`TargetActor` 和 `WaitTargetData` `AbilityTask` 上都有大量的委托。`TargetActor` 响应输入以产生和广播 `TargetData` 就绪、确认或取消委托。`WaitTargetData` 监听 `TargetActor` 的 `TargetData` 就绪、确认和取消委托，并将该信息转发回 `GameplayAbility` 和服务器。如果你向服务器发送 `TargetData`，你可能希望在服务器上进行验证，以确保 `TargetData` 看起来合理，以防止作弊。直接在服务器上产生 `TargetData` 可以完全避免此问题，但可能会导致拥有者客户端的预测错误。

根据你使用的 `AGameplayAbilityTargetActor` 的特定子类，不同的 `ExposeOnSpawn` 参数将在 `WaitTargetData` `AbilityTask` 节点上公开。一些常见参数包括：

| 常见 `TargetActor` 参数 | 定义                                                                                                                                                                                                                                                                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Debug（调试）                           | 如果为 `true`，在非发行版本中，每当 `TargetActor` 执行射线检测时，它将绘制调试追踪/重叠信息。请记住，非 `Instant` 的 `TargetActors` 会在 `Tick()` 上执行射线检测，因此这些调试绘制调用也会在 `Tick()` 上发生。                                                        |
| Filter（过滤器）                          | [可选] 一个特殊的结构体，用于在射线检测/重叠发生时过滤掉（移除）目标中的 `Actors`。典型的用例是过滤掉玩家的 `Pawn`、要求目标是特定的类。请参见[目标数据过滤器（Target Data Filters）](#concepts-target-data-filters)了解更高级的用例。 |
| Reticle Class（准星类）                   | [可选] `AGameplayAbilityWorldReticle` 的子类，`TargetActor` 将生成它。                                                                                                                                                                                                                                 |
| Reticle Parameters（准星参数）              | [可选] 配置你的准星（Reticles）。请参见[准星（Reticles）](#concepts-targeting-reticles)。                                                                                                                                                                                                                                        |
| Start Location（起始位置）                  | 一个特殊的结构体，用于指定射线检测应从哪里开始。通常这将是玩家的视角、武器枪口或 `Pawn` 的位置。                                                                                                                                          |

使用默认的 `TargetActor` 类时，`Actors` 只有在直接处于射线检测/重叠范围内时才是有效目标。如果它们离开射线检测/重叠区域（它们移动了或你看向别处），它们就不再是有效目标。如果你想让 `TargetActor` 记住最后的有效目标，你需要将此功能添加到自定义的 `TargetActor` 类中。我将这些称为持久目标（persistent targets），因为它们会一直持续到 `TargetActor` 收到确认或取消、`TargetActor` 在其射线检测/重叠中找到新的有效目标、或目标不再有效（被销毁）。GASShooter 对其火箭发射器副技能的追踪火箭目标选取使用了持久目标。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-target-data-filters"></a>
#### 4.11.3 目标数据过滤器（Target Data Filters）
使用 `Make GameplayTargetDataFilter` 和 `Make Filter Handle` 节点，你可以过滤掉玩家的 `Pawn` 或仅选择特定的类。如果你需要更高级的过滤，你可以子类化 `FGameplayTargetDataFilter` 并重写 `FilterPassesForActor` 函数。
```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** Returns true if the actor passes the filter and will be targeted */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

然而，这不能直接用于 `Wait Target Data` 节点，因为它需要一个 `FGameplayTargetDataFilterHandle`。必须创建一个新的自定义 `Make Filter Handle` 来接受该子类：
```c++
FGameplayTargetDataFilterHandle UGDTargetDataFilterBlueprintLibrary::MakeGDNameFilterHandle(FGDNameTargetDataFilter Filter, AActor* FilterActor)
{
	FGameplayTargetDataFilter* NewFilter = new FGDNameTargetDataFilter(Filter);
	NewFilter->InitializeFilterContext(FilterActor);

	FGameplayTargetDataFilterHandle FilterHandle;
	FilterHandle.Filter = TSharedPtr<FGameplayTargetDataFilter>(NewFilter);
	return FilterHandle;
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>
#### 4.11.4 游戏技能世界准星（Gameplay Ability World Reticles）
[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html)（`准星（Reticles）`）用于在使用非 `Instant` 确认的 [`TargetActors`](#concepts-targeting-actors) 进行目标选取时可视化你正在瞄准**谁**。`TargetActors` 负责所有 `Reticles` 的生成和销毁生命周期。`Reticles` 是 `AActors`，因此它们可以使用任何类型的可视组件来进行表示。如 [GASShooter](https://github.com/tranek/GASShooter) 中所见的常见实现是使用 `WidgetComponent` 在屏幕空间中显示 UMG 控件（始终面向玩家的摄像机）。`Reticles` 不知道它们在哪个 `AActor` 上，但你可以在自定义的 `TargetActor` 中通过子类化添加该功能。`TargetActors` 通常会在每个 `Tick()` 上将 `Reticle` 的位置更新到目标的位置。

GASShooter 使用 `Reticles` 来显示火箭发射器副技能追踪火箭的锁定目标。敌人身上的红色指示器就是 `Reticle`。类似的白色图像是火箭发射器的准星。
![GASShooter 中的准星](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

`Reticles` 附带了一些供设计师使用的 `BlueprintImplementableEvents`（它们旨在在蓝图中开发）：

```c++
/** Called whenever bIsTargetValid changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** Called whenever bIsTargetAnActor changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`Reticles` 可以选择性地使用由 `TargetActor` 提供的 [`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html) 进行配置。默认结构体只提供一个变量 `FVector AOEScale`。虽然你技术上可以子类化此结构体，但 `TargetActor` 只接受基础结构体。不允许在默认 `TargetActors` 中对此进行子类化似乎有些短视。不过，如果你创建自己的自定义 `TargetActor`，你可以提供自己的自定义准星参数结构体，并在生成时手动将其传递给你的 `AGameplayAbilityWorldReticles` 子类。

`Reticles` 默认不会被复制，但如果在你的游戏中向其他玩家显示本地玩家正在瞄准谁是有意义的，可以使其支持复制。

`Reticles` 在默认 `TargetActors` 下只会显示在当前有效目标上。例如，如果你使用 `AGameplayAbilityTargetActor_SingleLineTrace` 来射线检测目标，`Reticle` 只会在敌人直接处于射线路径中时出现。如果你看向别处，敌人就不再是有效目标，`Reticle` 将消失。如果你想让 `Reticle` 停留在最后一个有效目标上，你需要自定义你的 `TargetActor` 来记住最后一个有效目标并将 `Reticle` 保留在其上。我将这些称为持久目标（persistent targets），因为它们会一直持续到 `TargetActor` 收到确认或取消、`TargetActor` 在其射线检测/重叠中找到新的有效目标、或目标不再有效（被销毁）。GASShooter 对其火箭发射器副技能的追踪火箭目标选取使用了持久目标。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-targeting-containers"></a>
#### 4.11.5 游戏效果容器目标选取（Gameplay Effect Containers Targeting）
[`GameplayEffectContainers`](#concepts-ge-containers) 附带了一种可选的、高效的方式来产生 [`TargetData`](#concepts-targeting-data)。这种目标选取在 `EffectContainer` 应用于客户端和服务器时立即执行。它比 [`TargetActors`](#concepts-targeting-actors) 更高效，因为它在目标选取对象的 CDO 上运行（无需生成和销毁 `Actors`），但它缺乏玩家输入、无需确认即立即发生、不能被取消，且不能从客户端向服务器发送数据（在两端都产生数据）。它非常适合即时射线检测和碰撞重叠。Epic 的 [Action RPG 示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) 在其容器中包含了两种示例目标选取类型——选取技能拥有者和从事件中获取 `TargetData`。它还在蓝图中实现了一种，用于在玩家的某个偏移处（由子蓝图类设置）执行即时球体追踪。你可以在 C++ 或蓝图中子类化 `URPGTargetType` 来创建自己的目标选取类型。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae"></a>
## 5. 常用技能与效果实现

<a name="cae-stun"></a>
### 5.1 眩晕（Stun）
通常在实现眩晕时，我们希望取消 `Character` 所有激活的 `GameplayAbilities`，阻止新的 `GameplayAbility` 激活，并在眩晕持续期间阻止移动。示例项目中的流星 `GameplayAbility` 会对命中目标施加眩晕效果。

要取消目标激活的 `GameplayAbilities`，我们在眩晕 [`GameplayTag` 被添加时](#concepts-gt-change) 调用 `AbilitySystemComponent->CancelAbilities()`。

要在眩晕期间阻止新的 `GameplayAbilities` 激活，将眩晕 `GameplayTag` 添加到这些 `GameplayAbilities` 的 [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags) 中。

要在眩晕期间阻止移动，我们重写 `CharacterMovementComponent` 的 `GetMaxSpeed()` 函数，在拥有者拥有眩晕 `GameplayTag` 时返回 0。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-sprint"></a>
### 5.2 冲刺（Sprint）
示例项目提供了一个冲刺的示例——按住 `Left Shift` 时加速奔跑。

更快的移动由 `CharacterMovementComponent` 预测性地处理，通过网络向服务器发送一个标志。详见 `GDCharacterMovementComponent.h/cpp`。

`GA` 负责响应 `Left Shift` 输入，告诉 `CMC` 开始和停止冲刺，并在 `Left Shift` 按下时预测性地消耗耐力。详见 `GA_Sprint_BP`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-ads"></a>
### 5.3 瞄准（Aim Down Sights）
示例项目处理瞄准的方式与冲刺完全相同，只是降低移动速度而非提高。

关于预测性降低移动速度的详细信息，请参阅 `GDCharacterMovementComponent.h/cpp`。

关于输入处理的详细信息，请参阅 `GA_AimDownSight_BP`。瞄准不消耗耐力。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-ls"></a>
### 5.4 生命窃取（Lifesteal）
我在伤害 [`ExecutionCalculation`](#concepts-ge-ec) 中处理生命窃取。`GameplayEffect` 上会带有一个类似 `Effect.CanLifesteal` 的 `GameplayTag`。`ExecutionCalculation` 检查 `GameplayEffectSpec` 是否拥有该 `Effect.CanLifesteal` `GameplayTag`。如果该 `GameplayTag` 存在，`ExecutionCalculation` 会[创建一个动态的 `Instant` `GameplayEffect`](#concepts-ge-dynamic)，将要回复的生命值作为修改器，并将其应用回 `Source` 的 `ASC`。

```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-random"></a>
### 5.5 在客户端和服务器上生成随机数（Generating a Random Number on Client and Server）
有时你需要在 `GameplayAbility` 中为子弹后坐力或散布等功能生成一个"随机"数。客户端和服务器都希望生成相同的随机数。为此，我们必须在 `GameplayAbility` 激活时将 `random seed` 设置为相同的值。你需要在每次激活 `GameplayAbility` 时设置 `random seed`，以防客户端预测激活失败导致其随机数序列与服务器不同步。

| 种子设置方法                                                          | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 使用激活预测密钥（activation prediction key）                                            | `GameplayAbility` 激活预测密钥是一个 int16 类型的值，保证在客户端和服务器的 `Activation()` 中同步且可用。你可以将其作为客户端和服务器上的 `random seed`。此方法的缺点是，预测密钥每次游戏启动时都从零开始，并在生成密钥之间持续递增。这意味着每场比赛都将拥有完全相同的随机数序列。这对你的需求来说可能足够随机，也可能不够。 |
| 在激活 `GameplayAbility` 时通过事件负载发送种子 | 通过事件激活你的 `GameplayAbility`，并通过复制的事件负载将客户端随机生成的种子发送到服务器。这允许更多的随机性，但客户端可以轻易地修改游戏，每次只发送相同的种子值。此外，通过事件激活 `GameplayAbilities` 将阻止它们从输入绑定激活。                                                                                                                                                                     |

如果你的随机偏差很小，大多数玩家不会注意到每局游戏的序列相同，使用激活预测密钥作为 `random seed` 应该能满足你的需求。如果你在做更复杂且需要防作弊的事情，也许使用 `Server Initiated` `GameplayAbility` 会更好，服务器可以创建预测密钥或生成 `random seed` 通过事件负载发送。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-crit"></a>
### 5.6 暴击（Critical Hits）
我在伤害 [`ExecutionCalculation`](#concepts-ge-ec) 中处理暴击。`GameplayEffect` 上会带有一个类似 `Effect.CanCrit` 的 `GameplayTag`。`ExecutionCalculation` 检查 `GameplayEffectSpec` 是否拥有该 `Effect.CanCrit` `GameplayTag`。如果该 `GameplayTag` 存在，`ExecutionCalculation` 会根据暴击率（从 `Source` 捕获的 `Attribute`）生成一个随机数，如果成功则添加暴击伤害（同样是从 `Source` 捕获的 `Attribute`）。由于我不预测伤害，我不需要担心客户端和服务器之间随机数生成器的同步，因为 `ExecutionCalculation` 只会在服务器上运行。如果你尝试使用 `MMC` 预测性地进行伤害计算，你需要从 `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance` 获取 `random seed` 的引用。

参见 [GASShooter](https://github.com/tranek/GASShooter) 如何实现爆头。概念相同，只是它不依赖随机数来决定概率，而是检查 `FHitResult` 的骨骼名称。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 5.7 不堆叠的游戏效果（Gameplay Effects）但只有最大幅度的效果实际影响目标
Paragon 中的减速效果不堆叠。每个减速实例正常应用并跟踪其生命周期，但只有幅度最大的减速效果实际影响 `Character`。GAS 通过 `AggregatorEvaluateMetaData` 开箱即用地提供了此场景的支持。详见 [`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-paused"></a>
### 5.8 在游戏暂停时生成目标数据（Target Data）
如果你需要在等待从 `WaitTargetData` `AbilityTask` 生成 [`TargetData`](#concepts-targeting-data) 时暂停游戏，我建议不要暂停，而是使用 `slomo 0`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>
### 5.9 一键交互系统（One Button Interaction System）
[GASShooter](https://github.com/tranek/GASShooter) 实现了一个一键交互系统，玩家可以按下或按住 'E' 键与可交互对象进行交互，例如复活玩家、打开武器箱以及打开或关闭滑动门。

**[⬆ 返回顶部](#table-of-contents)**

<a name="debugging"></a>
## 6. 调试（Debugging）GAS
在调试 GAS 相关问题时，你通常想知道以下信息：
> * "我的属性（Attributes）值是多少？"
> * "我拥有哪些游戏标签（GameplayTags）？"
> * "我当前拥有哪些游戏效果（GameplayEffects）？"
> * "我被授予了哪些技能（Abilities），哪些正在运行，哪些被阻止激活？"

GAS 提供了两种技术在运行时回答这些问题——[`showdebug abilitysystem`](#debugging-sd) 和 [`GameplayDebugger`](#debugging-gd) 中的钩子。

**提示：** 虚幻引擎倾向于优化（Optimizations）C++ 代码，这使得某些函数难以调试。当你深入跟踪代码时偶尔会遇到这种情况。如果将 Visual Studio 解决方案配置设置为 `DebugGame Editor` 仍然无法跟踪代码或检查变量，你可以通过使用 `UE_DISABLE_OPTIMIZATION` 和 `UE_ENABLE_OPTIMIZATION` 宏或 CoreMiscDefines.h 中定义的发行版变体来包裹被优化的函数以禁用所有优化。除非你从源码重新编译插件，否则这不能用于插件代码。这对内联函数可能有效也可能无效，取决于它们做什么以及它们在哪里。调试完成后务必移除这些宏！

```c++
UE_DISABLE_OPTIMIZATION
void MyClass::MyFunction(int32 MyIntParameter)
{
	// My code
}
UE_ENABLE_OPTIMIZATION
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem
在游戏内控制台中输入 `showdebug abilitysystem`。此功能分为三个"页面"。三个页面都会显示你当前拥有的 `GameplayTags`。在控制台中输入 `AbilitySystem.Debug.NextCategory` 可以在页面之间切换。

第一页显示所有 `Attributes` 的 `CurrentValue`：
![First Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

第二页显示你身上所有 `Duration` 和 `Infinite` 类型的 `GameplayEffects`、它们的堆叠层数、它们赋予的 `GameplayTags` 以及它们提供的 `Modifiers`。
![Second Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

第三页显示所有已授予给你的 `GameplayAbilities`、它们是否正在运行、是否被阻止激活，以及当前运行的 `AbilityTasks` 的状态。
![Third Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

要在目标之间切换（通过 Actor 周围的绿色矩形棱柱表示），使用 `PageUp` 键或 `NextDebugTarget` 控制台命令转到下一个目标，使用 `PageDown` 键或 `PreviousDebugTarget` 控制台命令转到上一个目标。

**注意：** 为了使技能系统信息根据当前选择的调试 Actor 进行更新，你需要在 `AbilitySystemGlobals` 中设置 `bUseDebugTargetFromHud=true`，像这样在 `DefaultGame.ini` 中配置：
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
bUseDebugTargetFromHud=true
```

**注意：** 要使 `showdebug abilitysystem` 正常工作，必须在 GameMode 中选择一个实际的 HUD 类。否则命令将找不到并返回 "Unknown Command"。

**[⬆ 返回顶部](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 游戏调试器（Gameplay Debugger）
GAS 为游戏调试器添加了功能。通过按撇号（'）键访问游戏调试器。按数字键盘上的 3 启用 Abilities 类别。该类别可能因你安装的插件不同而有所不同。如果你的键盘没有数字键盘（如笔记本电脑），你可以在项目设置中更改按键绑定。

当你想查看**其他** `Characters` 上的 `GameplayTags`、`GameplayEffects` 和 `GameplayAbilities` 时，使用游戏调试器。不幸的是，它不显示目标 `Attributes` 的 `CurrentValue`。它会瞄准屏幕中心的任何 `Character`。你可以通过在编辑器的世界大纲视图中选择目标来更改目标，或者看向不同的 `Character` 并再次按撇号（'）键。当前被检查的 `Character` 头顶会有最大的红色圆圈。

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ 返回顶部](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 GAS 日志
GAS 源代码包含大量以不同详细级别输出的日志语句。你最常看到的是 `ABILITY_LOG()` 语句。默认详细级别为 `Display`。任何更高级别的日志默认不会在控制台中显示。

要更改日志类别的��细级别，在控制台中输入：

```
log [category] [verbosity]
```

例如，要开启 `ABILITY_LOG()` 语句，你需要在控制台中输入：
```
log LogAbilitySystem VeryVerbose
```

要重置为默认值，输入：
```
log LogAbilitySystem Display
```

要显示所有日志类别，输入：
```
log list
```

值得注意的 GAS 相关日志类别：

| 日志类别          | 默认详细级别 |
| ------------------------- | ----------------------- |
| LogAbilitySystem          | Display                 |
| LogAbilitySystemComponent | Log                     |
| LogGameplayCueDetails     | Log                     |
| LogGameplayCueTranslator  | Display                 |
| LogGameplayEffectDetails  | Log                     |
| LogGameplayEffects        | Display                 |
| LogGameplayTags           | Log                     |
| LogGameplayTasks          | Log                     |
| VLogAbilitySystem         | Display                 |

更多信息请参阅 [Wiki 上的日志页面](https://unrealcommunity.wiki/logging-lgpidy6i)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="optimizations"></a>
## 7. 优化（Optimizations）

<a name="optimizations-abilitybatching"></a>
### 7.1 技能批处理（Ability Batching）
在一帧内激活、可选地向服务器发送 `TargetData`、并结束的 [`GameplayAbilities`](#concepts-ga) 可以[批处理以将两到三个 RPC 压缩为一个 RPC](#concepts-ga-batching)。这类技能通常用于即时命中（hitscan）武器。

<a name="optimizations-gameplaycuebatching"></a>
### 7.2 游戏提示批处理（Gameplay Cue Batching）
如果你同时发送多个 [`GameplayCues`](#concepts-gc)，考虑[将它们批处理为一个 RPC](#concepts-gc-batching)。目标是减少 RPC 数量（`GameplayCues` 是不可靠的 NetMulticasts）并尽可能少地发送数据。

<a name="optimizations-ascreplicationmode"></a>
### 7.3 AbilitySystemComponent 复制模式（Replication Mode）
默认情况下，[`ASC`](#concepts-asc) 处于[`完全复制模式（Full Replication Mode）`](#concepts-asc-rm)。这会将所有 [`GameplayEffects`](#concepts-ge) 复制到每个客户端（这对单人游戏来说没问题）。在多人游戏中，将玩家拥有的 `ASCs` 设置为 `混合复制模式（Mixed Replication Mode）`，AI 控制的角色设置为 `最小复制模式（Minimal Replication Mode）`。这会使应用在玩家角色上的 `GEs` 仅复制给该角色的拥有者，而应用在 AI 控制角色上的 `GEs` 永远不会将 `GEs` 复制给客户端。[`GameplayTags`](#concepts-gt) 仍然会复制，[`GameplayCues`](#concepts-gc) 仍然会不可靠地 NetMulticast 到所有客户端，不受 `Replication Mode` 影响。这将减少当所有客户端不需要看到 `GEs` 时因复制 `GEs` 产生的网络数据。

<a name="optimizations-attributeproxyreplication"></a>
### 7.4 属性代理复制（Attribute Proxy Replication）
在像《堡垒之夜大逃杀》（Fortnite Battle Royale，FNBR）这样拥有大量玩家的大型游戏中，会有很多 [`ASCs`](#concepts-asc) 存在于始终相关的 `PlayerStates` 上，复制大量 [`Attributes`](#concepts-a)。为了优化这个瓶颈，Fortnite 在 `PlayerState::ReplicateSubobjects()` 中完全禁止了 `ASC` 及其 [`AttributeSets`](#concepts-as) 在**模拟玩家控制代理**上的复制。自主代理和 AI 控制的 `Pawns` 仍然根据其[`复制模式（Replication Mode）`](#concepts-asc-rm)进行完全复制。FNBR 不在始终相关的 `PlayerStates` 上的 `ASC` 中复制 `Attributes`，而是在玩家的 `Pawn` 上使用一个复制的代理结构。当服务器的 `ASC` 上的 `Attributes` 发生变化时，它们也会在代理结构上发生变化。客户端从代理结构接收复制的 `Attributes` 并将更改推送回其本地 `ASC`。这允许 `Attribute` 复制使用 `Pawn` 的相关性和 `NetUpdateFrequency`。这个代理结构还以位掩码形式复制一小组白名单中的 `GameplayTags`。此优化减少了网络上的数据量，并允许我们利用 pawn 相关性。AI 控制的 `Pawns` 的 `ASC` 位于 `Pawn` 上，已经使用��其相关性，因此不需要此优化。

> 我不确定在此后进行的其他服务器端优化（Replication Graph 等）之后是否仍然需要这样做，而且这不是最易维护的模式。

*来自 Epic 的 Dave Ratti 对[社区问题 #3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) 的回答*

<a name="optimizations-asclazyloading"></a>
### 7.5 ASC 延迟加载（Lazy Loading）
《堡垒之夜大逃杀》（FNBR）世界中有大量可破坏的 `AActors`（树木、建筑等），每个都有一个 [`ASC`](#concepts-asc)。这会累积大量内存开销。FNBR 通过仅在需要时（首次受到玩家伤害时）延迟加载 `ASCs` 来优化此问题。这减少了总体内存使用量，因为某些 `AActors` 在一场比赛中可能永远不会被破坏。

**[⬆ 返回顶部](#table-of-contents)**

<a name="qol"></a>
## 8. 生活质量改进建议（Quality of Life Suggestions）

<a name="qol-gameplayeffectcontainers"></a>
### 8.1 游戏效果容器（Gameplay Effect Containers）
[GameplayEffectContainers](#concepts-ge-containers) 将 [`GameplayEffectSpecs`](#concepts-ge-spec)、[`TargetData`](#concepts-targeting-data)、[简单目标选择](#concepts-targeting-containers) 和相关功能组合到易于使用的结构中。这些非常适合将 `GameplayEffectSpecs` 传递给从技能中生成的投射物，投射物在碰撞时在稍后时间应用它们。

<a name="qol-asynctasksascdelegates"></a>
### 8.2 蓝图异步任务绑定 ASC 委托（Blueprint AsyncTasks to Bind to ASC Delegates）
为了提高设计师友好的迭代速度，特别是在设计 UI 的 UMG Widgets 时，创建蓝图异步任务（AsyncTasks，用 C++ 编写）以直接从 UMG 蓝图图表绑定到 `ASC` 上的常见变更委托。唯一的注意事项是它们必须手动销毁（例如当 widget 被销毁时），否则它们将永远存在于内存中。示例项目包含三个蓝图异步任务。

监听 `Attribute` 变化：

![Listen for Attributes Changes BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributeschange.png)

监听冷却时间变化：

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

监听 `GE` 堆叠变化：

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting"></a>
## 9. 故障排除（Troubleshooting）

<a name="troubleshooting-notlocal"></a>
### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`
你需要[在客户端初始化 `ASC`](#concepts-asc-setup)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>
### 9.2 `ScriptStructCache` 错误
你需要调用 [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>
### 9.3 动画蒙太奇（Animation Montages）未复制到客户端
确保你在 [GameplayAbilities](#concepts-ga) 中使用 `PlayMontageAndWait` 蓝图节点而不是 `PlayMontage`。这个 [AbilityTask](#concepts-at) 会通过 `ASC` 自动复制蒙太奇，而 `PlayMontage` 节点不会。

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting-duplicatingblueprintactors"></a>
### 9.4 复制蓝图 Actor 会将 AttributeSets 设置为 nullptr
这是[虚幻引擎中的一个 bug](https://issues.unrealengine.com/issue/UE-81109)，对于从现有蓝图 Actor 类复制的蓝图 Actor 类，会将类上的 `AttributeSet` 指针设置为 nullptr。有几种解决方法。我成功的做法是不在类上创建专用的 `AttributeSet` 指针（.h 中没有指针，构造函数中不调用 `CreateDefaultSubobject`），而是直接在 `PostInitializeComponents()` 中将 `AttributeSets` 添加到 `ASC`（示例项目中未展示）。复制的 `AttributeSets` 仍然会存在于 `ASC` 的 `SpawnedAttributes` 数组中。代码大致如下：

```c++
void AGDPlayerState::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->AddSet<UGDAttributeSetBase>();
		// ... any other AttributeSets that you may have
	}
}
```

在这种情况下，你将使用 `ASC` 上的函数来读取和设置 `AttributeSet` 中的值，而不是[调用通过宏在 `AttributeSet` 上生成的函数](#concepts-as-attributes)。

```c++
/** Returns current (final) value of an attribute */
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;

/** Sets the base value of an attribute. Existing active modifiers are NOT cleared and will act upon the new base value. */
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);
```

因此 `GetHealth()` 大致如下：

```c++
float AGDPlayerState::GetHealth() const
{
	if (AbilitySystemComponent)
	{
		return AbilitySystemComponent->GetNumericAttribute(UGDAttributeSetBase::GetHealthAttribute());
	}

	return 0.0f;
}
```

设置（初始化）生命值 `Attribute` 大致如下：

```c++
const float NewHealth = 100.0f;
if (AbilitySystemComponent)
{
	AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);
}
```

提醒一下，`ASC` 对每个 `AttributeSet` 类最多只期望有一个 `AttributeSet` 对象。

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting-unresolvedexternalsymbolmarkpropertydirty"></a>
### 9.5 未解析的外部符号 UEPushModelPrivate::MarkPropertyDirty(int,int)（Unresolved external symbol）

如果你遇到如下编译错误：

```
error LNK2019: unresolved external symbol "__declspec(dllimport) void __cdecl UEPushModelPrivate::MarkPropertyDirty(int,int)" (__imp_?MarkPropertyDirty@UEPushModelPrivate@@YAXHH@Z) referenced in function "public: void __cdecl FFastArraySerializer::IncrementArrayReplicationKey(void)" (?IncrementArrayReplicationKey@FFastArraySerializer@@QEAAXXZ)
```

这是因为在 `FFastArraySerializer` 上调用了 `MarkItemDirty()`。我在更新 `ActiveGameplayEffect` 时遇到过这个问题，例如更新冷却时间持续时长时。

```c++
ActiveGameplayEffects.MarkItemDirty(*AGE);
```

问题在于 `WITH_PUSH_MODEL` 在多个地方被定义。`PushModelMacros.h` 将其定义为 0，而在多个地方将其定义为 1。`PushModel.h` 看到的是 1，但 `PushModel.cpp` 看到的是 0。

解决方案是在 `Build.cs` 中将 `NetCore` 添加到项目的 `PublicDependencyModuleNames` 中。

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting-enumnamesarenowpathnames"></a>
### 9.6 枚举名称现在由路径名表示（Enum names are now represented by path name）

如果你遇到如下编译警告：

```
warning C4996: 'FGameplayAbilityInputBinds::FGameplayAbilityInputBinds': Enum names are now represented by path names. Please use a version of FGameplayAbilityInputBinds constructor that accepts FTopLevelAssetPath. Please update your code to the new API before upgrading to the next release, otherwise your project will no longer compile.
```

UE 5.1 弃用了在 `BindAbilityActivationToInputComponent()` 构造函数中使用 `FString`。我们必须改为传入 `FTopLevelAssetPath`。

旧的、已弃用的方式：
```c++
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

新方式：
```c++
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

更多信息请参阅 `Engine\Source\Runtime\CoreUObject\Public\UObject\TopLevelAssetPath.h`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="acronyms"></a>
## 10. 常见 GAS 缩写词

| 名称                                                                                                   | 缩写                |
|------------------------------------------------------------------------------------------------------- | ------------------- |
| 技能系统组件（AbilitySystemComponent）                                                                 | ASC                 |
| 技能任务（AbilityTask）                                                                                | AT                  |
| [Epic 的动作RPG示例项目（Action RPG Sample Project）](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) | ARPG, ARPG Sample   |
| 角色移动组件（CharacterMovementComponent）                                                             | CMC                 |
| 游戏技能（GameplayAbility）                                                                            | GA                  |
| 游戏技能系统（GameplayAbilitySystem）                                                                  | GAS                 |
| 游戏提示（GameplayCue）                                                                                | GC                  |
| 游戏效果（GameplayEffect）                                                                             | GE                  |
| 游戏效果执行计算（GameplayEffectExecutionCalculation）                                                 | ExecCalc, Execution |
| 游戏标签（GameplayTag）                                                                                | Tag, GT             |
| 修改器幅度计算（ModifierMagnitudeCalculation）                                                         | ModMagCalc, MMC     |

**[⬆ 返回顶部](#table-of-contents)**

<a name="resources"></a>
## 11. 其他资源
* [官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* 源代码！
   * 特别是 `GameplayPrediction.h`
* [Epic 的 Lyra 示例项目](https://unrealengine.com/marketplace/en-US/learn/lyra)
* [Epic 的动作RPG示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/) 有一个专门讨论 GAS 的文字频道 `#gameplay-ability-system`
   * 查看置顶消息
* [Dan 'Pan' 的 GitHub 资源仓库](https://github.com/Pantong51/GASContent)
* [SabreDartStudios 的 YouTube 视频](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

<a name="resources-daveratti"></a>
### 11.1 与 Epic Games 的 Dave Ratti 的问答

<a name="resources-daveratti-community1"></a>
#### 11.1.1 社区问题 1
[Dave Ratti 在 Unreal Slackers Discord 服务器上回答社区关于 GAS 的问题](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)：

1. 我们如何在 `GameplayAbilities` 之外或不依赖于它的情况下按需创建作用域预测窗口（Scoped Prediction Windows）？例如，一个发射后不管的投射物（Fire and Forget Projectile）在命中敌人时，如何本地预测一个伤害 `GameplayEffect`？

> 预测键（PredictionKey）系统并非真正为此而设计。从根本上说，该系统的工作方式是客户端发起一个预测性动作，通过一个键告知服务器，然后客户端和服务器都运行相同的逻辑，并将预测性副作用与给定的预测键关联起来。例如，"我正在预测性地激活一个技能"或"我已经生成了目标数据，将在 WaitTargetData 任务之后预测性地运行技能图的后续部分"。
>
> 在这种模式下，预测键从服务器"弹回"并通过 UAbilitySystemComponent::ReplicatedPredictionKeyMap（复制属性）返回客户端。一旦键从服务器复制回来，客户端就能撤销所有本地预测的副作用（游戏提示（GameplayCues）、游戏效果（GameplayEffects））：复制的版本*将会存在*，如果不存在则说明是一次错误预测（Misprediction）。准确知道何时撤销预测性副作用在这里至关重要：如果太早，你会看到间隙；如果太晚，你会出现"重复"。（注意这里指的是有状态预测（Stateful Prediction），例如基于持续时间的游戏效果（Gameplay Effect）的循环游戏提示（GameplayCue）。"爆发"游戏提示和即时游戏效果永远不会被"撤销"或回滚。如果它们有关联的预测键，它们只是在客户端被跳过。）
>
> 为了进一步强调这一点：预测性动作必须是服务器自己不会执行的，而只有在客户端告知时才会执行。因此，拥有一个通用的"按需创建一个键并告知服务器以便我可以运行某些东西"的机制是行不通的，除非那个"某些东西"是服务器只有在客户端告知后才会执行的。
>
> 回到最初的问题：类似发射后不管的投射物。Paragon 和 Fortnite 都有使用游戏提示（GameplayCues）的投射物 Actor 类。然而我们不使用预测键系统来做这些。相反，我们有一个非复制游戏提示（Non-Replicated GameplayCues）的概念。游戏提示只在本地触发，完全被服务器跳过。本质上这些都是对 UGameplayCueManager::HandleGameplayCue 的直接调用。它们不通过 UAbilitySystemComponent 路由，因此不会进行预测键检查/提前返回。
>
> 非复制游戏提示的缺点是，它们确实不会被复制。因此投射物类/蓝图需要确保调用这些函数的代码路径在每个客户端上都在运行。我们有用于启动（在 BeginPlay 中调用）、爆炸、撞墙/撞角色等的提示。
>
> 这些类型的事件已经在客户端生成了，所以调用非复制游戏提示并不是什么大问题。复杂的蓝图可能会比较棘手，需要作者确保他们理解什么在哪里运行。

2. 当在本地预测的 `GameplayAbility` 中使用带有 `OnlyServerWait` 的 `WaitNetSync` `AbilityTask` 来创建作用域预测窗口时，玩家是否可能通过延迟发送到服务器的数据包来控制 `GameplayAbility` 的时机，因为服务器正在等待他们带有预测键的 RPC？这在 Paragon 或 Fortnite 中是否曾经是一个问题，如果是，Epic 是如何解决的？

> 是的，这是一个合理的担忧。任何在服务器上运行并等待客户端"信号"的技能蓝图都可能容易受到延迟开关（Lag Switch）类型漏洞的利用。
>
> Paragon 有一个类似于 UAbilityTask_WaitTargetData 的自定义瞄准任务。在这个任务中，对于即时瞄准模式，我们设置了超时或"最大延迟"来等待客户端。如果瞄准模式正在等待用户确认（按键），则会被忽略，因为允许用户花时间。但对于立即确认瞄准的技能，我们只会等待一定时间，然后要么 A）在服务器端生成目标数据，要么 B）取消该技能。
>
> 我们从未为 WaitNetSync 设置过这样的机制，我们使用它也相当少。
>
> 我不认为 Fortnite 使用了类似的东西。Fortnite 中的武器技能是特殊处理的，批量合并到单个 Fortnite 特定的 RPC 中：一个 RPC 用于激活技能、提供目标数据和结束技能。因此武器技能在大逃杀模式中本质上不容易受到此问题的影响。
>
> 我的看法是，这可能是一个可以在系统层面解决的问题，但我预计我们不会很快自己做出改变。针对你提到的情况，对 WaitNetSync 进行局部修复以包含最大延迟可能是一个合理的任务，但同样——我们不太可能在近期在我们这边做这个。


3. Paragon 和 Fortnite 使用了哪种 `EGameplayEffectReplicationMode`，Epic 对何时使用每种模式有什么建议？

> 两款游戏基本上都对玩家控制的角色使用混合模式（Mixed），对 AI 控制的角色使用最小模式（Minimal）（AI 小兵、丛林怪物、AI 外壳等）。这也是我对大多数在多人游戏中使用该系统的人的建议。在项目中越早设置这些越好。
>
> Fortnite 在优化方面更进了几步。它实际上完全不为模拟代理（Simulated Proxies）复制 UAbilitySystemComponent。在拥有该组件的 Fortnite 玩家状态类的 ::ReplicateSubobjects() 中，组件和属性子对象被跳过。我们将技能系统组件中最少量的必要复制数据推送到 Pawn 本身的一个结构体中（基本上是属性值的子集和一个通过位掩码复制的标签白名单子集）。我们称之为"代理（Proxy）"。在接收端，我们获取复制在 Pawn 上的代理数据，并将其推回玩家状态上的技能系统组件中。所以在 FNBR 中，每个玩家确实有一个 ASC，它只是不直接复制：而是通过 Pawn 上的一个最小代理结构体复制数据，然后在接收端路由回 ASC。这样做的好处是 A）数据集更小 B）利用了 Pawn 的相关性（Relevancy）。
>
> 我不确定在后来完成的其他服务器端优化（复制图（Replication Graph）等）之后是否仍然有必要，而且这不是最容易维护的模式。


4. 既然按照 `GameplayPrediction.h` 的说明我们无法预测 `GameplayEffects` 的移除，那么有没有什么策略可以减轻延迟对移除 `GameplayEffects` 的影响？例如，当移除一个移动速度减速效果时，我们目前必须等待服务器复制 `GameplayEffect` 的移除，这会导致玩家角色位置的突然跳变。

> 这是一个棘手的问题，我没有好的答案。我们通常通过容差和平滑来回避这些问题。我完全同意技能系统与角色移动系统的精确同步目前状态不好，这是我们确实想要修复的。
>
> 我曾经搁置了一个允许预测性移除 GE 的方案，但在不得不转向其他工作之前，始终无法解决所有边界情况。不过这也不能解决所有问题，因为角色移动系统仍然有一个内部的已保存移动缓冲区（Saved Move Buffer），它对技能系统和可能的移动速度修改器等一无所知。即使在无法预测 GE 移除的问题之外，仍然可能陷入修正反馈循环。
>
> 如果你认为你有一个确实紧迫的情况，你可以预测性地添加一个 GE 来抑制你的移动速度 GE。我自己从未这样做过，但之前曾理论上思考过。它可能能够帮助解决某一类问题。


5. 我们知道 `AbilitySystemComponent` 在 Paragon 和 Fortnite 中位于 `PlayerState` 上，而在动作RPG示例中位于 `Character` 上。Epic 对于 AbilitySystemComponent 应该放在哪里以及其 `Owner` 应该是什么，有什么内部规则、指南或建议？

> 一般来说，我会说任何不需要重生的东西，其 Owner 和 Avatar Actor 应该是同一个对象。比如 AI 敌人、建筑物、世界道具等。
>
> 任何需要重生的东西，其 Owner 和 Avatar 应该是不同的，这样技能系统组件（Ability System Component）就不需要在重生后被保存/重新创建/恢复。PlayerState 是合理的选择，因为它会复制到所有客户端（而 PlayerController 不会）。缺点是 PlayerState 始终是相关的（Always Relevant），所以在 100 人游戏中可能会遇到问题（参见问题 #3 中关于 FN 做法的说明）。


6. 是否可行拥有多个 `AbilitySystemComponents`，它们具有相同的所有者（Owner）但不同的 Avatar（例如在 Pawn 和武器/物品/投射物上，`Owner` 设置为 `PlayerState`）？

> 我看到的第一个问题是在拥有者 Actor 上实现 IGameplayTagAssetInterface 和 IAbilitySystemInterface。前者可能是可行的：只需聚合所有 ASC 的标签（但要注意——HasAllMatchingGameplayTags 可能只能通过跨 ASC 聚合才能满足。仅仅将调用转发到每个 ASC 并将结果进行 OR 运算是不够的）。但后者更棘手：哪个 ASC 是权威的？如果有人想应用一个 GE——哪个应该接收它？也许你可以解决这些问题，但问题中最困难的部分将是：拥有者下面有多个 ASC。
>
> 在 Pawn 和武器上分别使用独立的 ASC 本身是有意义的。例如，区分描述武器的标签和描述拥有者 Pawn 的标签。也许授予武器的标签也"应用"到拥有者身上而其他不会（例如，属性和 GE 是独立的，但拥有者会像我上面描述的那样聚合拥有的标签），这确实是有道理的。这可以实现，我确信如此。但拥有相同所有者的多个 ASC 可能会变得棘手。


7. 有没有办法阻止服务器覆盖拥有客户端（Owning Client）上本地预测技能的冷却时间？在高延迟场景下，这将让拥有客户端在其本地冷却到期时"尝试"再次激活技能，即使服务器上仍在冷却中。当拥有客户端的激活请求通过网络到达服务器时，服务器可能已经冷却结束，或者服务器可能能够将激活请求排队等待剩余的几毫秒。否则按照现在的情况，延迟较高的客户端在重新激活技能前会比延迟较低的客户端有更长的延迟。这在冷却时间非常短的技能（如基本攻击，冷却时间可能不到一秒）中最为明显。如果没有办法阻止服务器覆盖本地预测技能的冷却时间，Epic 减轻高延迟对重新激活技能影响的策略是什么？换一种基于示例的说法，Epic 是如何设计 Paragon 的基本攻击和其他技能的，使得高延迟玩家能够以与低延迟玩家相同的速度进行攻击或激活，并使用本地预测？

> 简短的回答是没有办法阻止这一点，Paragon 确实有这个问题。更高延迟的连接在基本攻击上会有更低的攻击速率。
>
> 我曾尝试通过添加"GE 调和（GE Reconciliation）"来修复这个问题，在计算 GE 持续时间时考虑延迟。本质上允许服务器消耗一部分 GE 总时间，使得客户端侧 GE 的有效时间在任何延迟量下都 100% 一致（尽管波动仍可能导致问题）。然而我从未将此功能完善到可以发布���状态，项目进展很快，我们一直没有完全解决它。
>
> Fortnite 对武器射速有自己的记录方式：它不使用 GE 作为武器的冷却。如果这对你的游戏是一个关键问题，我建议这样做。


8. Epic 对游戏技能系统（GameplayAbilitySystem）插件的路线图是什么？Epic 计划在 2019 年及以后添加哪些功能？

> 我们觉得整体而言系统在这一点上已经相当稳定，我们没有人在开发重大新功能。偶尔会为 Fortnite 或根据 UDN/拉取请求进行错误修复和小改进，但目前就是这样。
>
> 从长远来看，我认为我们最终会做一个"V2"或一些重大改变。我们从编写这个系统中学到了很多，觉得很多地方做对了，也有很多地方做错了。我很希望有机会纠正那些错误并改进上面指出的一些致命缺陷。
>
> 如果 V2 真的要来，提供升级路径将是最重要的。我们永远不会做一个 V2 然后让 Fortnite 永远留在 V1：会有一些路径或程序尽可能自动迁移，尽管几乎肯定仍需要一些手动重做。
>
> 高优先级修复包括：
> * 与角色移动系统更好的互操作性。统一客户端预测。
> * GE 移除预测（问题 #4）
> * GE 延迟调和（问题 #7）
> * 通用网络优化，如批处理 RPC 和代理结构体。主要是我们为 Fortnite 所做的工作，但找到方法将其分解为更通用的形式，至少使游戏能够更容易地编写自己的游戏特定优化。
>
> 我会考虑做的更一般性的重构类型的改变：
> * 我想从根本上考虑让 GE 不再直接引用电子表格值，而是让它们能够发出参数，这些参数可以由绑定到电子表格值的更高层对象填充。当前模型的问题是，由于 GE 与曲线表行的紧密耦合，GE 变得不可共享。我认为可以编写一个通用的参数化系统，作为 V2 系统的基础。
> * 减少 UGameplayAbility 上的"策略"数量。我会移除 ReplicationPolicy 和 InstancingPolicy。复制在我看来几乎从来不需要，而且会造成混乱。InstancingPolicy 应该被替换为让 FGameplayAbilitySpec 成为一个可被子类化的 UObject。这应该是"非实例化技能对象"，具有事件并且可在蓝图中使用。UGameplayAbility 应该是"每次执行时实例化"的对象。如果你确实需要实例化，它可以是可选的：相反，"非实例化"技能将通过新的 UGameplayAbilitySpec 对象实现。
> * 系统应该提供更多"中间层"构造，例如"过滤的 GE 应用容器"（数据驱动将哪些 GE 应用到哪些 Actor，具有更高层的游戏逻辑）、"重叠体积支持"（基于碰撞体重叠事件应用"过滤的 GE 应用容器"）等。这些是每个项目最终都会以自己的方式实现的构建块。正确实现它们并非易事，所以我认为我们应该更好地提供一些基本实现。
> * 总的来说，减少启动项目所需的样板代码。可能是一个单独的模块"扩展库（Ex Library）"或类似的东西，可以开箱即用地提供被动技能或基本射线武器等功能。这个模块是可选的，但能让你快速启动并运行。
> * 我想将游戏提示（GameplayCues）移到一个与技能系统不耦合的单独模块中。我认为这里可以做很多改进。


> 这只是我的个人意见，不代表任何人的承诺。我认为最现实的行动方案是，随着新的引擎技术计划的推进，技能系统需要更新，届时就是做这类事情的时机。这些计划可能与脚本、网络或物理/角色移动相关。不过这些都是非常长远的展望，所以我无法给出承诺或时间表估计。

**[⬆ 返回顶部](#table-of-contents)**

<a name="resources-daveratti-community2"></a>
#### 11.1.2 社区问题 2
社区成员 [iniside](https://github.com/iniside) 与 Dave Ratti 的问答：

1. 是否计划支持解耦的固定帧率更新（Decoupled Fixed Ticking）？我希望游戏线程是固定的（如 30/60fps），而让渲染线程自由运行。我想问这是否是我们应该在未来期待的，以便对游戏玩法的工作方式做出一些假设。我主要是因为现在物理有了固定的异步更新（Fixed Async Tick），这就提出了系统其余部分未来如何工作的问题。我不隐瞒说，能够在不同时固定引擎其余部分更新频率的情况下拥有固定更新的游戏线程将是非常了不起的。

> 没有计划将渲染帧率和游戏线程更新帧率解耦。我认为由于这些系统的复杂性以及保持与先前引擎版本向后兼容的要求，这件事已经不太可能实现了。
>
> 相反，我们的方向是拥有一个异步"物理线程（Physics Thread）"，它以固定的更新频率运行，独立于游戏线程。需要以固定频率运行的东西可以在这里运行，而游戏线程/渲染可以像以前一样运行。
>
> 值得澄清的是，网络预测（Network Prediction）支持它所称的独立更新（Independent Ticking）和固定更新（Fixed Ticking）模式。我的长期计划是保持独立更新大致像网络预测中现在的样子，即在游戏线程上以可变帧率运行，没有"群组/世界"预测，只是经典的"客户端预测自己的 Pawn 和拥有的 Actor"模型。而固定更新将使用异步物理的内容，允许你预测非客户端控制/拥有的 Actor，如物理对象和其他客户端/Pawn/载具等。


2. 是否有关于网络预测（Network Prediction）如何与技能系统（Ability System）集成的计划？例如，固定帧技能激活（这样服务器获得的是技能被激活和任务被执行的帧，而不是预测键）？

> 是的，计划是重写/移除技能系统的预测键，并用网络预测构造来替换它们。NetworkPredictionExtras 中的 MockAbility 示例展示了这可能如何工作，但它们比 GAS 所需的更"硬编码"。
>
> 主要思路是我们移除 ASC 的 RPC 中明确的客户端->服务器预测键交换。将不再有预测窗口或作用域预测键。相反，一切都将围绕网络预测帧（NetworkPrediction Frames）来锚定。重要的是客户端和服务器就事件发生的时间达成一致。示例包括：
>
> * 技能何时被激活/结束/取消
> * 游戏效果（Gameplay Effects）何时被应用/移除
> * 属性值（属性在帧 X 时的值是什么）
>
> 我认为这可以在技能系统层面以通用方式完成。但实际上让 UGameplayAbility 内部用户定义的逻辑完全可回滚（Rollback-able）仍然需要更多工作。我们最终可能会有一个完全可回滚的 UGameplayAbility 子类，它只能访问更有限的功能集或只能使用标记为回滚友好（Rollback-friendly）的技能任务（Ability Tasks）。类似这样的方案。动画事件和根运动（Root Motion）及其处理方式也有很多影响。
>
> 希望我能有更清晰的答案，但在再次触及 GAS 之前，把基础做好真的很重要。移动和物理必须先稳固，然后才能改变更高层的系统。


3. 是否有计划将网络预测（Network Prediction）的开发移到主分支？不瞒你说，我真的很想查看最新的代码。无论它处于什么状态。

> 我们正在朝这个方向努力。系统工作仍然全部在 NetworkPrediction 中完成（参见 NetworkPhysics.h），底层的异步物理内容应该都是可用的（RewindData.h 等）。但我们在 Fortnite 中也有一些一直在关注的用例，这些显然不能公开。我们正在解决错误、性能优化等问题。
>
> 更多背景：在开发这个系统的早期版本时，我们非常关注事物的"前端"——状态和模拟是如何定义和编写的。我们在那里学到了很多。但随着异步物理内容的上线，我们更多地专注于让真实的东西在这个系统中运行，代价是抛弃了一些我们早期的抽象。目标是在真实的东西运行起来后再回头统一。例如，回到"前端"，在我们现在正在开发的核心技术之上制作最终版本。


4. 有一段时间主分支上有一个用于发送游戏消息（Gameplay Messages）的插件（看起来像事件/消息总线（Event/Message Bus）），但后来被移除了。有计划恢复它吗？有了游戏特性/模块化玩法（Game Features/Modular Gameplay）插件，拥有一个通用的事件总线调度器（Event Bus Dispatcher）将非常有用。

> 我想你指的是 GameplayMessages 插件。这可能会在某个时候回来——API 还没有真正最终确定，作者并不打算让它现在就公开。我同意它对模块化游戏设计应该很有用。但这不是我的领域，所以我没有更多信息。


5. 我最近一直在尝试异步固定物理（Async Fixed Physics），结果很有希望，不过如果未来会有网络预测（NP）更新的话，我可能只是先试验一下然后等待，因为要让它工作我仍然需要让整个引擎进入固定更新，另一方面我又想保持物理在 33ms。如果一切都在 30fps 的话，体验不太好 (:。

我注意到有一些关于异步角色移动组件（Async CharacterMovementComponent）的工作，但不确定这是否会使用网络预测（Network Prediction），还是一项单独的工作？

既然我注意到了，我也尝试以固定更新频率实现了我自己的自定义异步移动，效果还可以，但在此基础上我还需要为插值添加一个单独的更新。设置是在独立的工作线程上以固定 33ms 更新运行模拟更新，进行计算，保存结果，并在游戏线程上对其进行插值以匹配当前帧率。不完美，但完成了工作。

我的问题是，这在未来是否会更容易设置，因为需要编写相当多的样板代码（插值部分），而且逐个对每个移动对象进行插值效率不是特别高。

异步的东西真的很有趣，因为它允许你真正以固定更新频率运行游戏模拟（这将使固定线程变得不必要）并获得更可预测的结果。这是未来的预期方向，还是仅对特定系统有益？据我所记，Actor 的变换（Transform）不是异步更新的，蓝图也不完全是线程安全的。换句话说，这是计划在框架层面支持的东西，还是每个游戏必须自己解决的事情？

> 异步角色移动组件（Async CharacterMovementComponent）
>
> 这基本上是将 CMC 按原样移植到物理线程的��期原型/实验。我还不认为这是 CMC 的未来，但它可能会演变成那样。现在没有网络支持，所以不是我真正建议关注的东西。做这件事的人主要关心的是测量这个系统会增加的输入延迟以及如何减轻它。
>
> 我仍然需要让整个引擎进入固定更新，另一方面我又想保持物理在 33ms。如果一切都在 30fps 的话，体验不太好 (:。
>
> 异步（async）方面的内容非常有趣，因为它可以让你真正以固定更新频率运行游戏模拟（这将使固定线程变得不再必要）
>
> 是的。这里的目标是，启用异步物理（async physics）后，你可以以可变的 tick 频率运行引擎，同时物理和"核心"游戏模拟可以以固定频率运行（例如角色移动、载具、GAS 等）。
>
> 以下是现在需要设置以启用此功能的控制台变量（cvars）：（我想你已经弄清楚了）
> `p.DefaultAsyncDt=0.03333`
> `p.RewindCaptureNumFrames=64`
>
> Chaos 确实为物理状态提供了插值（interpolation）（例如，推送回 UPrimitiveComponent 并对游戏代码可见的变换（transforms））。现在有一个控制台变量 `p.AsyncInterpolationMultiplier`，如果你想查看的话可以用它来控制。你应该可以看到物理体的平滑连续运动，而无需编写任何额外代码。
>
> 如果你想对非物理状态进行插值，目前仍然需要你自己处理。例如，你想在异步物理线程（async physics thread）上更新（tick）一个冷却时间，但在游戏线程（game thread）上看到平滑的连续插值，以便每个渲染帧都更新冷却时间的可视化效果。我们最终会实现这个功能，但目前还没有示例。
>
> 确实需要编写相当多的样板代码（boilerplate code），
>
> 是的，这一直是该系统迄今为止的一个主要问题。我们希望提供一个接口，让有经验的程序员能够最大化性能和安全性（能够编写"开箱即用"的预测性游戏代码，而不会有大量的隐患和那些"可以做但最好不要做"的事情）。所以像角色移动（CharacterMovement）这样的系统可能会做大量自定义工作来最大化其性能——例如，编写模板化代码并进行批量更新、横向扩展、将更新循环分解为不同的阶段等。我们希望为这种用例提供一个良好的"底层"接口来接入异步线程和回滚系统（rollback systems）。在这种情况下——角色移动系统本身仍然可以合理地以自己的方式进行扩展。例如，提供一种方式来用蓝图（Blueprint）创建自定义移动模式，并提供线程安全的蓝图 API。
>
> 但我们认识到，对于不需要自己"系统"的更简单的游戏对象来说，这是不可接受的。需要更贴合虚幻引擎（Unreal）风格的方式。例如，使用反射系统（reflection system），提供通用的蓝图支持等。已经有在其他线程上使用蓝图的示例（参见 BlueprintThreadSafe 关键字以及动画系统一直在努力实现的方向）。所以我认为总有一天会有某种形式的支持。但同样，我们还没有做到。
>
> 我意识到你只是在问关于插值的问题，但这是一般性的回答：目前我们让你手动完成所有事情，如 NetSerialize、ShouldReconcile、Interpolate 等，但最终我们会提供一种方式，类似于"如果你只想使用反射系统，就不必手动编写这些东西"。我们只是不想*强制*所有人使用反射系统，因为这会带来其他限制，而我们认为在系统的最底层不应该承受这些限制。
>
> 最后把这些联系到我之前说的——目前我们真正专注于让几个非常具体的示例能够工作并具有良好的性能，然后我们会将注意力转回到前端，使事物变得易于使用和迭代，减少样板代码等，以便其他人都能使用。

**[⬆ 返回顶部](#table-of-contents)**

<a name="changelog"></a>
## 12. GAS 更新日志（Changelog）

这是一份 GAS 的重要变更列表（修复、变更和新功能），汇编自虚幻引擎官方升级更新日志以及我遇到的未记录变更。如果你发现了此处未列出的内容，请提交 issue 或 pull request。

<a name="changelog-5.3"></a>
### 5.3
* 崩溃修复（Crash Fix）：修复了在无缝旅行（seamless travel）后尝试应用游戏提示（Gameplay Cues）时的崩溃问题。
* 崩溃修复（Crash Fix）：修复了使用实时编码（Live Coding）时 GlobalAbilityTaskCount 导致的崩溃问题。
* 崩溃修复（Crash Fix）：修复了 UAbilityTask::OnDestroy 在递归调用时（如 UAbilityTask_StartAbilityState 的情况）的崩溃问题。
* 错误修复（Bug Fix）：现在可以安全地在子类中调用 `Super::ActivateAbility`。之前，它会调用 `CommitAbility`。
* 错误修复（Bug Fix）：添加了对正确复制不同类型 FGameplayEffectContext 的支持。
* 错误修复（Bug Fix）：FGameplayEffectContextHandle 现在会在获取"Actors"之前检查数据是否有效。
* 错误修复（Bug Fix）：保留游戏技能系统目标数据（Gameplay Ability System Target Data）LocationInfo 的旋转。
* 错误修复（Bug Fix）：游戏技能系统（Gameplay Ability System）现在仅在找到有效的 PC 时才停止搜索 PC。
* 错误修复（Bug Fix）：在 RemoveGameplayCue_Internal 中使用已存在的 GameplayCueParameters 而不是默认参数对象。
* 错误修复（Bug Fix）：GameplayAbilityWorldReticle 现在面向源 Actor 而不是 TargetingActor。
* 错误修复（Bug Fix）：如果在 GiveAbilityAndActivateOnce 中传入了触发事件数据且技能列表被锁定，则缓存触发事件数据。
* 错误修复（Bug Fix）：添加了对 FInheritedGameplayTags 立即更新其 CombinedTags 的支持，而不是等到保存时才更新。
* 错误修复（Bug Fix）：将 ShouldAbilityRespondToEvent 从仅客户端的代码路径移至服务器和客户端共用路径。
* 错误修复（Bug Fix）：修复了 FAttributeSetInitterDiscreteLevels 由于曲线简化（Curve Simplification）在打包构建（Cooked Builds）中不工作的问题。
* 错误修复（Bug Fix）：在 GameplayAbility 中设置 CurrentEventData。
* 错误修复（Bug Fix）：确保在可能执行回调之前正确设置 MinimalReplicationTags。
* 错误修复（Bug Fix）：修复了 ShouldAbilityRespondToEvent 未在实例化的 GameplayAbility 上被调用的问题。
* 错误修复（Bug Fix）：当 gc.PendingKill 被禁用时，在子 Actor 上执行的游戏提示通知 Actor（Gameplay Cue Notify Actors）不再导致内存泄漏。
* 错误修复（Bug Fix）：修复了 GameplayCueManager 中由于哈希碰撞（hash collisions）导致 GameplayCueNotify_Actors 可能"丢失"的问题。
* 错误修复（Bug Fix）：即使 Actor 上没有游戏标签（Gameplay Tags），WaitGameplayTagQuery 现在也会正确遵循其查询条件。
* 错误修复（Bug Fix）：PostAttributeChange 和 AttributeValueChangeDelegates 现在将具有正确的 OldValue。
* 错误修复（Bug Fix）：修复了 FGameplayTagQuery 在由原生代码创建结构体时未显示正确查询描述的问题。
* 错误修复（Bug Fix）：确保在使用技能系统时调用 UAbilitySystemGlobals::InitGlobalData。之前如果用户没有调用它，游戏技能系统将无法正常运行。
* 错误修复（Bug Fix）：修复了从 UGameplayAbility::EndAbility 链接/取消链接动画层（anim layers）时的问题。
* 错误修复（Bug Fix）：更新了技能系统组件（Ability System Component）函数，在使用前检查 Spec 的技能指针。
* 新增（New）：在 FGameplayTagRequirements 中添加了 GameplayTagQuery 字段，以支持指定更复杂的需求。
* 新增（New）：引入了 FGameplayEffectQuery::SourceAggregateTagQuery 以增强 SourceTagQuery。
* 新增（New）：扩展了通过控制台命令执行和取消游戏技能（Gameplay Abilities）和游戏效果（Gameplay Effects）的功能。
* 新增（New）：添加了对游戏技能蓝图（Gameplay Ability Blueprints）执行"审计"（Audit）的功能，可以显示其开发方式和预期用途的信息。
* 变更（Change）：OnAvatarSet 现在在主实例上调用，而不是在每 Actor 实例化的游戏技能的 CDO 上调用。
* 变更（Change）：允许在同一个游戏技能图（Gameplay Ability Graph）中同时使用激活技能（Activate Ability）和从事件激活技能（Activate Ability From Event）。
* 变更（Change）：AnimTask_PlayMontageAndWait 现在有一个开关，允许在混合退出（BlendOut）事件后触发完成（Completed）和中断（Interrupted）。
* 变更（Change）：ModMagnitudeCalc 包装函数已声明为 const。
* 变更（Change）：FGameplayTagQuery::Matches 现在对空查询返回 false。
* 变更（Change）：更新了 FGameplayAttribute::PostSerialize，将包含的属性标记为可搜索名称。
* 变更（Change）：更新了 GetAbilitySystemComponent，将默认参数设为 Self。
* 变更（Change）：在 AbilityTask_WaitTargetData 中将函数标记为 virtual。
* 变更（Change）：移除了未使用的函数 FGameplayAbilityTargetData::AddTargetDataToGameplayCueParameters。
* 变更（Change）：移除了残留的 GameplayAbility::SetMovementSyncPoint。
* 变更（Change）：从游戏任务（Gameplay tasks）和技能系统组件（Ability system components）中移除了未使用的复制标志。
* 变更（Change）：将部分游戏效果功能移至可选组件中。所有现有内容将在 PostCDOCompiled 期间自动更新以使用组件（如有必要）。

https://docs.unrealengine.com/5.3/en-US/unreal-engine-5.3-release-notes/

<a name="changelog-5.2"></a>
### 5.2
* 错误修复（Bug Fix）：修复了 `UAbilitySystemBlueprintLibrary::MakeSpecHandle` 函数中的崩溃问题。
* 错误修复（Bug Fix）：修复了游戏技能系统中的逻辑问题，其中未被控制的 Pawn 会被视为远程的，即使它是在服务器上本地生成的（例如载具）。
* 错误修复（Bug Fix）：正确设置了被服务器拒绝的预测实例化技能的激活信息。
* 错误修复（Bug Fix）：修复了一个导致游戏提示（GameplayCues）在远程实例上卡住的错误。
* 错误修复（Bug Fix）：修复了链式调用 WaitGameplayEvent 时的内存踩踏（memory stomp）问题。
* 错误修复（Bug Fix）：在蓝图中调用技能系统组件（AbilitySystemComponent）的 `GetOwnedGameplayTags()` 函数时，当同一节点多次执行时，不再保留上一次调用的返回值。
* 错误修复（Bug Fix）：修复了 GameplayEffectContext 复制对永远不会被复制的动态对象的引用的问题。
  * 这阻止了 GameplayEffect 调用 `Owner->HandleDeferredGameplayCues(this)`，因为 `bHasMoreUnmappedReferences` 始终为 true。
* 新增（New）：[游戏目标系统（Gameplay Targeting System）](https://docs.unrealengine.com/en-US/gameplay-targeting-system-in-unreal-engine/) 是一种创建数据驱动目标请求的方式。
* 新增（New）：为 GameplayTag 查询添加了自定义序列化支持。
* 新增（New）：添加了对复制派生 FGameplayEffectContext 类型的支持。
* 新增（New）：资源中的游戏属性（Gameplay Attributes）现在在保存时注册为可搜索名称，允许在引用查看器（reference viewer）中查看属性的引用。
* 新增（New）：为技能系统组件（AbilitySystemComponent）添加了一些基本的单元测试。
* 新增（New）：游戏技能系统属性（Gameplay Ability System Attributes）现在支持核心重定向（Core Redirects）。这意味着你现在可以在代码中重命名属性集（Attribute Sets）及其属性，并通过在 DefaultEngine.ini 中添加重定向条目使它们在用旧名称保存的资源中正确加载。
* 变更（Change）：允许从代码更改游戏效果修改器（Gameplay Effect Modifier）的评估通道。
* 变更（Change）：从游戏技能插件（Gameplay Abilities Plugin）中移除了之前未使用的变量 `FGameplayModifierInfo::Magnitude`。
* 变更（Change）：移除了技能系统组件（ability system component）和智能对象（Smart Object）实例标签之间的同步逻辑。

https://docs.unrealengine.com/5.2/en-US/unreal-engine-5.2-release-notes/

<a name="changelog-5.1"></a>
### 5.1
* 错误修复（Bug Fix）：修复了复制的松散游戏标签（loose gameplay tags）未复制给所有者的问题。
* 错误修复（Bug Fix）：修复了技能任务（AbilityTask）中技能可能被阻止及时垃圾回收（garbage-collection）的错误。
* 错误修复（Bug Fix）：修复了一个问题，即监听基于标签激活的游戏技能未能被激活。如果有多个游戏技能监听此标签，且列表中的第一个是无效的或没有权限激活，就会发生这种情况。
* 错误修复（Bug Fix）：修复了使用数据注册表（Data Registries）的游戏效果（GameplayEffects）在加载时错误警告的问题，并改进了警告文本。
* 错误修复（Bug Fix）：移除了 UGameplayAbility 中的代码，该代码错误地只为蓝图调试器的断点注册了最后一个实例化的技能。
* 错误修复（Bug Fix）：修复了在 ApplyGameplayEffectSpecToTarget 内部锁定期间调用 EndAbility 时游戏技能系统技能卡住的问题。
* 新增（New）：添加了对游戏效果（Gameplay Effects）添加阻止技能标签（blocked ability tags）的支持。
* 新增（New）：添加了 WaitGameplayTagQuery 节点。一个基于 UAbilityTask，另一个基于 UAbilityAsync。此节点指定一个 TagQuery，并在查询变为 true 或 false 时根据配置触发其输出引脚。
* 新增（New）：修改了控制台变量中的技能任务调试（AbilityTask debugging），在非发行版构建中默认启用调试记录和日志打印（可根据需要通过热修复开启/关闭）。
* 新增（New）：你现在可以设置 AbilitySystem.AbilityTask.Debug.RecordingEnabled 为 0 禁用，1 在非发行版构建中启用，2 在所有构建（包括发行版）中启用。
* 新增（New）：你可以使用 AbilitySystem.AbilityTask.Debug.AbilityTaskDebugPrintTopNResults 仅在日志中打印前 N 个结果（以避免日志刷屏）。
* 新增（New）：STAT_AbilityTaskDebugRecording 可用于测试这些默认开启的调试变更对性能的影响。
* 新增（New）：添加了过滤游戏提示事件（GameplayCue events）的调试命令。
* 新增（New）：向游戏技能系统添加了新的调试命令 AbilitySystem.DebugAbilityTags、AbilitySystem.DebugBlockedTags 和 AbilitySystem.DebugAttribute。
* 新增（New）：添加了获取游戏属性（Gameplay Attribute）调试字符串表示的蓝图函数。
* 新增（New）：添加了新的游戏任务资源重叠策略（Gameplay Task resource overlap policy），用于取消现有任务。
* 变更（Change）：现在技能任务（Ability Tasks）应确保仅在对 Ability 指针执行所有需要的操作之后才调用 Super::OnDestroy，因为调用后该指针将被置空。
* 变更（Change）：将 FGameplayAbilitySpec/Def::SourceObject 转换为弱引用（weak reference）。
* 变更（Change）：将技能系统组件（Ability System Component）在技能任务（Ability Task）中的引用改为弱指针（weak pointer），以便垃圾回收（Garbage Collection）可以删除它。
* 变更（Change）：移除了冗余的枚举 EWaitGameplayTagQueryAsyncTriggerCondition。
* 变更（Change）：GameplayTasksComponent 和 AbilitySystemComponent 现在支持注册子对象 API（registered subobject API）。
* 变更（Change）：添加了更好的日志记录，以指示游戏技能为何未能激活。
* 变更（Change）：移除了 AbilitySystem.Debug.NextTarget 和 PrevTarget 命令，改用全局 HUD NextDebugTarget 和 PrevDebugTarget 命令。

https://docs.unrealengine.com/5.1/en-US/unreal-engine-5.1-release-notes/

<a name="changelog-5.0"></a>
### 5.0

https://docs.unrealengine.com/5.0/en-US/unreal-engine-5.0-release-notes/

<a name="changelog-4.27"></a>
### 4.27
* 崩溃修复（Crash Fix）：修复了一个根运动源（root motion source）问题，当网络客户端在 Actor 完成执行使用带有力度随时间变化修改器的恒定力根运动任务（constant force root motion task）的技能时可能崩溃。
* 错误修复（Bug Fix）：修复了使用游戏提示（GameplayCues）时编辑器加载时间的性能回退。
* 错误修复（Bug Fix）：GameplayEffectsContainer 的 `SetActiveGameplayEffectLevel` 方法在设置相同的 EffectLevel 时不再标记 FastArray 为脏。
* 错误修复（Bug Fix）：修复了游戏效果混合复制模式（GameplayEffect mixed replication mode）中的一个边界情况，其中未被网络连接显式拥有但通过 `GetNetConnection` 使用该连接的 Actor 将不会收到混合复制更新。
* 错误修复（Bug Fix）：修复了 GameplayAbility 类方法 `EndAbility` 中发生的无限递归，该递归是由从 `K2_OnEndAbility` 再次调用 `EndAbility` 引起的。
* 错误修复（Bug Fix）：游戏标签（GameplayTags）蓝图引脚在标签注册之前加载时不再被静默清除。它们现在的工作方式与 GameplayTag 变量相同，两者的行为都可以通过项目设置中的 ClearInvalidTags 选项更改。
* 错误修复（Bug Fix）：改进了游戏标签（GameplayTag）操作的线程安全性。
* 新增（New）：向 GameplayAbility 的 `K2_CanActivateAbility` 方法公开了 SourceObject。
* 新增（New）：原生游戏标签（Native GameplayTags）。引入了新的 `FNativeGameplayTag`，使得可以创建在模块加载和卸载时正确注册和取消注册的一次性原生标签。
* 新增（New）：更新了 `GiveAbilityAndActivateOnce` 以传入 FGameplayEventData 参数。
* 新增（New）：改进了游戏技能插件中的可缩放浮点数（ScalableFloats），以支持从新的数据注册系统（Data Registry System）动态查找曲线表（curve tables）。添加了 ScalableFloat 头文件，以便在技能插件外更容易重用该通用结构。
* 新增（New）：添加了代码支持，可通过 GameplayTagsEditorModule 在其他编辑器自定义中使用游戏标签 UI。
* 新增（New）：修改了 UGameplayAbility 的 PreActivate 方法，使其可以选择性地接收触发事件数据。
* 新增（New）：添加了更多支持，使用项目特定的过滤器在编辑器中过滤游戏标签。`OnFilterGameplayTag` 提供引用属性和标签来源，因此你可以根据请求标签的资源来过滤标签。
* 新增（New）：添加了在初始化后调用 GameplayEffectSpec 类方法 `SetContext` 时保留原始捕获的 SourceTags 的选项。
* 新增（New）：改进了从特定插件注册游戏标签的 UI。新的标签 UI 现在允许你为新添加的游戏标签源选择磁盘上的插件位置。
* 新增（New）：在 Sequencer 中添加了一个新的轨道，允许在使用游戏技能系统（GameplayAbilitySystem）构建的 Actor 上触发通知状态。与通知类似，GameplayCueTrack 可以使用基于范围的事件或基于触发的事件。
* 变更（Change）：更改了 GameplayCueInterface，通过引用传递 GameplayCueParameters 结构。
* 优化（Optimization）：对加载和重新生成游戏标签表（GameplayTag table）进行了多项性能改进，以优化此选项。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_27/

<a name="changelog-4.26"></a>
### 4.26
* GAS 插件不再标记为测试版（beta）。
* 崩溃修复（Crash Fix）：修复了在没有有效标签源选择的情况下添加游戏标签时的崩溃问题。
* 崩溃修复（Crash Fix）：在 UGameplayCueManager::VerifyNotifyAssetIsInValidPath 中向消息添加了路径字符串参数以修复崩溃。
* 崩溃修复（Crash Fix）：修复了 AbilitySystemComponent_Abilities 中使用未检查指针导致的访问违规崩溃。
* 错误修复（Bug Fix）：修复了堆叠游戏效果（stacking GEs）在应用效果的额外实例时未重置持续时间的错误。
* 错误修复（Bug Fix）：修复了 CancelAllAbilities 仅取消非实例化技能的问题。
* 新增（New）：为游戏技能提交函数添加了可选的标签参数。
* 新增（New）：为 PlayMontageAndWait 技能任务添加了 StartTimeSeconds 并改进了注释。
* 新增（New）：向 FGameplayAbilitySpec 添加了标签容器 "DynamicAbilityTags"。这些是与 spec 一起复制的可选技能标签。它们也会被应用的游戏效果捕获为源标签。
* 新增（New）：GameplayAbility 的 IsLocallyControlled 和 HasAuthority 函数现在可以从蓝图调用。
* 新增（New）：可视化日志记录器（Visual logger）现在仅在当前正在记录可视化日志数据时才收集和存储即时游戏效果（instant GEs）的信息。
* 新增（New）：在蓝图节点中添加了对游戏属性引脚（gameplay attribute pins）重定向器的支持。
* 新增（New）：添加了新功能，当根运动移动相关的技能任务结束时，它们会将移动组件的移动模式恢复到任务开始前的移动模式。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_26/

<a name="changelog-4.25.1"></a>
### 4.25.1
* 已修复！UE-92787 保存带有内联设置属性引脚的获取浮点属性（Get Float Attribute）节点的蓝图时崩溃
* 已修复！UE-92810 生成带有内联更改的实例可编辑游戏标签属性的 Actor 时崩溃

<a name="changelog-4.25"></a>
### 4.25
* 修复了 `RootMotionSource` `AbilityTasks` 的预测
* [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes) 现在额外接收旧的 `Attribute` 值。我们必须将其作为可选参数提供给我们的 `OnRep` 函数。之前，它会尝试读取属性值来获取旧值。然而，如果从复制函数调用，旧值在到达 SetBaseAttributeValueFromReplication 之前已经被丢弃，因此我们会得到新值。
* 向 `UGameplayAbility` 添加了 [`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy)。
* 崩溃修复（Crash Fix）：修复了在没有有效标签源选择的情况下添加游戏标签时的崩溃问题。
* 崩溃修复（Crash Fix）：移除了攻击者通过技能系统使服务器崩溃的几种方式。
* 崩溃修复（Crash Fix）：我们现在在检查标签需求之前确保有游戏效果定义。
* 错误修复（Bug Fix）：修复了游戏标签类别（gameplay tag categories）未应用于蓝图中函数参数的问题（如果它们是函数终止节点的一部分）。
* 错误修复（Bug Fix）：修复了游戏效果标签在多个视口下不被复制的问题。
* 错误修复（Bug Fix）：修复了在循环触发的技能时，InternalTryActivateAbility 函数可能使游戏技能规格（gameplay ability spec）失效的错误。
* 错误修复（Bug Fix）：更改了我们在标签计数容器内更新游戏标签的处理方式。在移除游戏标签时延迟更新父标签时，我们现在会在父标签更新后调用变更相关的委托。这确保了在委托广播时标签表处于一致状态。
* 错误修复（Bug Fix）：在确认目标时，我们现在会在迭代之前复制生成的目标 Actor 数组，因为某些回调可能会修改该数组。
* 错误修复（Bug Fix）：修复了堆叠游戏效果（stacking GameplayEffects）在应用效果的额外实例时未重置持续时间，且使用调用者设置持续时间（set by caller durations）时，只有堆栈上的第一个实例能正确设置持续时间的错误。堆栈中的所有其他 GE 规格的持续时间均为 1 秒。添加了自动化测试来检测此情况。
* 错误修复（Bug Fix）：修复了处理游戏事件委托修改游戏事件委托列表时可能发生的错误。
* 错误修复（Bug Fix）：修复了 GiveAbilityAndActivateOnce 行为不一致的错误。
* 错误修复（Bug Fix）：重新排列了 FGameplayEffectSpec::Initialize 中的一些操作，以处理潜在的顺序依赖问题。
* 新增（New）：UGameplayAbility 现在有一个 OnRemoveAbility 函数。它遵循与 OnGiveAbility 相同的模式，仅在技能的主实例或类默认对象（CDO）上调用。
* 新增（New）：显示阻止的技能标签时，调试文本现在包含阻止标签的总数。
* 新增（New）：将 UAbilitySystemComponent::InternalServerTryActiveAbility 重命名为 UAbilitySystemComponent::InternalServerTryActivateAbility。之前调用 InternalServerTryActiveAbility 的代码现在应该��用 InternalServerTryActivateAbility。
* 新增（New）：在添加或删除标签时继续使用过滤文本显示游戏标签。之前的行为会清除过滤器。
* 新增（New）：在编辑器中添加新标签时不再重置标签源。
* 新增（New）：添加了查询技能系统组件以获取具有指定标签集的所有活跃游戏效果的功能。新函数名为 GetActiveEffectsWithAllTags，可通过代码或蓝图访问。
* 新增（New）：当根运动移动相关的技能任务结束时，它们现在会将移动组件的移动模式恢复到任务开始前的移动模式。
* 新增（New）：将 SpawnedAttributes 设为临时（transient），使其不会保存可能变得陈旧和不正确的数据。添加了空值检查以防止任何当前保存的陈旧数据传播。这可以防止与 SpawnedAttributes 中存储的错误数据相关的问题。
* API 变更（API Change）：AddDefaultSubobjectSet 已被弃用。应改用 AddAttributeSetSubobject。
* 新增（New）：游戏技能现在可以指定在哪个动画实例（Anim Instance）上播放蒙太奇（montage）。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_25/

<a name="changelog-4.24"></a>
### 4.24
* 修复了蓝图节点 `Attribute` 变量在编译时重置为 `None` 的问题。
* 需要调用 [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) 才能使用 [`TargetData`](#concepts-targeting-data)，否则你会收到 `ScriptStructCache` 错误，客户端将与服务器断开连接。我的建议是现在在每个项目中都调用此函数，而在 4.24 之前这是可选的。
* 修复了将 `GameplayTag` 设置器复制到之前未定义该变量的蓝图时的崩溃问题。
* `UGameplayAbility::MontageStop()` 函数现在正确使用 `OverrideBlendOutTime` 参数。
* 修复了组件上的 `GameplayTag` 查询变量在编辑时未被修改的问题。
* 添加了 `GameplayEffectExecutionCalculations` 支持作用域修改器（scoped modifiers）针对"临时变量"的功能，这些临时变量不需要由属性捕获支持。
  * 实现基本上使得可以创建以 `GameplayTag` 标识的聚合器，作为执行公开临时值以供作用域修改器操作的手段；你现在可以构建需要可操作值但不需要从源或目标捕获的公式。
  * 要使用此功能，执行必须向新的成员变量 `ValidTransientAggregatorIdentifiers` 添加标签；这些标签将显示在底部的作用域修改器的计算修改器数组中，标记为临时变量——详细信息自定义也相应更新以支持此功能。
* 添加了受限标签的生活质量改进。移除了受限 `GameplayTag` 源的默认选项。添加受限标签时不再重置源，以便更容易连续添加多个标签。
* `APawn::PossessedBy()` 现在将 `Pawn` 的所有者设置为新的 `Controller`。这很有用，因为[混合复制模式（Mixed Replication Mode）](#concepts-asc-rm)期望 `Pawn` 的所有者是 `Controller`（如果 `ASC` 位于 `Pawn` 上）。
* 修复了 `FAttributeSetInitterDiscreteLevels` 中 POD（Plain Old Data，普通旧数据）的错误。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_24/

**[⬆ 返回顶部](#table-of-contents)**
