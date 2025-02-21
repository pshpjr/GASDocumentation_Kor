﻿# 들어가기 전에
번역기를 번역한 것
 
# GASDocumentation
My understanding of Unreal Engine 5's GameplayAbilitySystem plugin (GAS) with a simple multiplayer sample project. This is not official documentation and neither this project nor myself are affiliated with Epic Games. I make no guarantee for the accuracy of this information.

The goal of this documentation is to explain the major concepts and classes in GAS and provide some additional commentary based on my experience with it. There is a lot of 'tribal knowledge' of GAS among users in the community and I aim to share all of mine here.

The Sample Project and documentation are current with **Unreal Engine 5.0**. There are branches of this documentation for older versions of Unreal Engine, but they are no longer supported and are liable to have bugs or out of date information.

[GASShooter](https://github.com/tranek/GASShooter) is a sister Sample Project demonstrating advanced techniques with GAS for a multiplayer FPS/TPS.

The best documentation will always be the plugin source code.

<a name="table-of-contents"></a>
## Table of Contents

> 1. [GameplayAbilitySystem Plugin 소개](#intro)
> 1. [Sample Project](#sp)
> 1. [Setting Up a Project Using GAS](#setup)
> 1. [Concepts](#concepts)  
>    4.1 [Ability System Component](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [Replication Mode](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [설정 및 초기화](#concepts-asc-setup)  
>    4.2 [Gameplay Tags](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [Responding to Changes in Gameplay Tags](#concepts-gt-change)  
>    4.3 [Attributes](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [Attribute Definition](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [BaseValue vs CurrentValue](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [Meta Attributes](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [Responding to Attribute Changes](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [Derived Attributes](#concepts-a-derived)  
>    4.4 [Attribute Set](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [Attribute Set Definition](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [Attribute Set Design](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [Subcomponents with Individual Attributes](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [Adding and Removing AttributeSets at Runtime](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [Item Attributes (Weapon Ammo)](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [Plain Floats on the Item](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [`AttributeSet` on the Item](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [`ASC` on the Item](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [Defining Attributes](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [Initializing Attributes](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [Gameplay Effects](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [Gameplay Effect Definition](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [Applying Gameplay Effects](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [Removing Gameplay Effects](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [Gameplay Effect Modifiers](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [Multiply and Divide Modifiers](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [Gameplay Tags on Modifiers](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [Stacking Gameplay Effects](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [Granted Abilities](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [Gameplay Effect Tags](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [Immunity](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [Modifier Magnitude Calculation](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [Gameplay Effect Execution Calculation](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [Sending Data to Execution Calculations](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [Backing Data Attribute Calculation Modifier](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [Backing Data Temporary Variable Calculation Modifier](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [Gameplay Effect Context](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [Custom Application Requirement](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [Cost Gameplay Effect](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [Cooldown Gameplay Effect](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [Get the Cooldown Gameplay Effect's Remaining Time](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [Listening for Cooldown Begin and End](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [Predicting Cooldowns](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [Changing Active Gameplay Effect Duration](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [Creating Dynamic Gameplay Effects at Runtime](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [Gameplay Effect Containers](#concepts-ge-containers)  
>    4.6 [Gameplay Abilities](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [Gameplay Ability Definition](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [Replication Policy](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [Server Respects Remote Ability Cancellation](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [Replicate Input Directly](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [Binding Input to the ASC](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [Binding to Input without Activating Abilities](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [Granting Abilities](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [Activating Abilities](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [Passive Abilities](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [Canceling Abilities](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [Getting Active Abilities](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [Instancing Policy](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [Net Execution Policy](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [Ability Tags](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [Gameplay Ability Spec](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [Passing Data to Abilities](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [Ability Cost and Cooldown](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [Leveling Up Abilities](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [Ability Sets](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [Ability Batching](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [Net Security Policy](#concepts-ga-netsecuritypolicy)  
>    4.7 [Ability Tasks](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [Ability Task Definition](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [Custom Ability Tasks](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [Using Ability Tasks](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [Root Motion Source Ability Tasks](#concepts-at-rms)  
>    4.8 [Gameplay Cues](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [Gameplay Cue Definition](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [Triggering Gameplay Cues](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [Local Gameplay Cues](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [Gameplay Cue Parameters](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [Gameplay Cue Manager](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [Prevent Gameplay Cues from Firing](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [Gameplay Cue Batching](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [Manual RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [Multiple GCs on one GE](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [Gameplay Cue Events](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [Gameplay Cue Reliability](#concepts-gc-reliability)  
>    4.9 [Ability System Globals](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    4.10 [Prediction](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [Prediction Key](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [Creating New Prediction Windows in Abilities](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [Predictively Spawning Actors](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [Future of Prediction in GAS](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [Network Prediction Plugin](#concepts-p-npp)  
>    4.11 [Targeting](#concepts-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [Target Data](#concepts-targeting-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [Target Actors](#concepts-targeting-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [Target Data Filters](#concepts-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [Gameplay Ability World Reticles](#concepts-targeting-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [Gameplay Effect Containers Targeting](#concepts-targeting-containers)  
> 1. [Commonly Implemented Abilities and Effects](#cae)  
>    5.1 [Stun](#cae-stun)  
>    5.2 [Sprint](#cae-sprint)  
>    5.3 [Aim Down Sights](#cae-ads)  
>    5.4 [Lifesteal](#cae-ls)  
>    5.5 [Generating a Random Number on Client and Server](#cae-random)  
>    5.6 [Critical Hits](#cae-crit)  
>    5.7 [Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target](#cae-nonstackingge)  
>    5.8 [Generate Target Data While Game is Paused](#cae-paused)  
>    5.9 [One Button Interaction System](#cae-onebuttoninteractionsystem)  
> 1. [Debugging GAS](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [Gameplay Debugger](#debugging-gd)  
>    6.3 [GAS Logging](#debugging-log)  
> 1. [Optimizations](#optimizations)  
>    7.1 [Ability Batching](#optimizations-abilitybatching)  
>    7.2 [Gameplay Cue Batching](#optimizations-gameplaycuebatching)  
>    7.3 [AbilitySystemComponent Replication Mode](#optimizations-ascreplicationmode)  
>    7.4 [Attribute Proxy Replication](#optimizations-attributeproxyreplication)  
>    7.5 [ASC Lazy Loading](#optimizations-asclazyloading)  
> 1. [Quality of Life Suggestions](#qol)  
>    8.1 [Gameplay Effect Containers](#qol-gameplayeffectcontainers)  
>    8.2 [Blueprint AsyncTasks to Bind to ASC Delegates](#qol-asynctasksascdelegates)  
> 1. [Troubleshooting](#troubleshooting)  
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)  
>    9.2 [`ScriptStructCache` errors](#troubleshooting-scriptstructcache)  
>    9.3 [Animation Montages are not replicating to clients](#troubleshooting-replicatinganimmontages)  
>    9.4 [Duplicating Blueprint Actors is setting AttributeSets to nullptr](#troubleshooting-duplicatingblueprintactors)  
>    9.5 [unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)](#troubleshooting-unresolvedexternalsymbolmarkpropertydirty)  
> 1. [Common GAS Acronyms](#acronyms)
> 1. [Other Resources](#resources)  
>    11.1 [Q&A With Epic Game's Dave Ratti](#resources-daveratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [Community Questions 1](#resources-daveratti-community1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [Community Questions 2](#resources-daveratti-community2)  
> 1. [GAS Changelog](#changelog)  
>    * [4.27](#changelog-4.27)  
>    * [4.26](#changelog-4.26)  
>    * [4.25.1](#changelog-4.25.1)  
>    * [4.25](#changelog-4.25)  
>    * [4.24](#changelog-4.24)
         
<a name="intro"></a>
## 1. Intro to the GameplayAbilitySystem Plugin
From the [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html):
>게임플레이 능력 시스템은 RPG나 MOBA 타이틀에서 볼 수 있는 유형의 능력과 속성을 구축하기 위한 매우 유연한 프레임워크입니다. 게임의 캐릭터가 사용할 액션이나 패시브 능력, 이러한 액션의 결과로 다양한 속성을 생성하거나 약화시킬 수 있는 상태 효과(디버프), 이러한 액션의 사용을 규제하기 위해 "쿨다운" 타이머 또는 리소스 비용(마나)을 구현할 수 있습니다. 각 레벨에서 능력과 그 효과의 수준을 변경하고(스킬 레벨업) 입자 또는 음향 효과를 활성화하는 등의 작업을 수행합니다. 간단히 말해서, 이 시스템은 현대 RPG 또는 MOBA 타이틀에 설정된 좋아하는 캐릭터의 능력만큼 복잡하거나 점프처럼 단순한 게임 내 능력을 설계, 구현 및 효율적으로 네트워크화하는 데 도움이 될 수 있습니다.

GameplayAbilitySystem 플러그인은 Epic Games에서 개발했으며 Unreal Engine 5(UE5)와 함께 제공됩니다. Paragon 및 Fortnite와 같은 AAA 상용 게임에서 전투 테스트를 거쳤습니다.

플러그인은 싱글 및 멀티플레이어 게임에서 다음과 같은 즉시 사용 가능한 솔루션을 제공합니다.:
* 옵션 비용 및 재사용 대기 시간으로 레벨 기반 캐릭터 능력 또는 기술 구현 ([GameplayAbilities](#concepts-ga))
* 액터에 속하는 `Attributes` 조작 ([Attributes](#concepts-a))
* 액터에 상태 효과 적용 ([GameplayEffects](#concepts-ge))
* 액터에 `GameplayTags` 적용 ([GameplayTags](#concepts-gt))
* 시각 효과 또는 음향 효과 생
성s ([GameplayCues](#concepts-gc))
* 위에서 언급한 모든 것의 Replication

멀티플레이어 게임에서 GAS는 다음[client-side prediction](#concepts-p)을 지원합니다:
* 능력 활성화
* 애니메이션 몽타주 재생
* `Attributes` 변경
* `GameplayTags` 적용
* Spawning `GameplayCues`
* Movement via `RootMotionSource` functions connected to the `CharacterMovementComponent`.

**GAS 는 C++로 설정해야 하지만**, `GameplayAbilities`와 `GameplayEffects`는 디자이너가 Blueprint에서 만들 수 있습니다.


GAS의 현재 문제:
* `GameplayEffect` 대기 시간 조정(능력 재사용 대기시간을 예측할 수 없음으로 인해 대기 시간이 긴 플레이어는 대기 시간이 짧은 플레이어에 비해 낮은 재사용 대기시간 능력에 대한 발사 속도가 낮아집니다.)(쿨타임 짧은 능력을 자주 쓸 수 없다)
* Cannot predict the removal of `GameplayEffects`. We can however predict adding `GameplayEffects` with the inverse effects, effectively removing them. This is not always appropriate or feasible and still remains an issue.
* 표준 템플릿, 멀티플레이어 예제 및 문서가 부족합니다. 바라건대 이것이 도움이됩니다!


**[⬆ Back to Top](#table-of-contents)**

<a name="sp"></a>
## 2. Sample Project
이 문서에는 GameplayAbilitySystem 플러그인을 처음 사용하지만 Unreal Engine 5는 사용해본 사람들을 대상으로 하는 멀티플레이어 3인칭 슈팅 샘플 프로젝트가 포함되어 있습니다. 사용자는 UE5의 C++, 블루프린트, UMG, 복제 및 기타 중간 주제에 대해 알고 있어야 합니다.  This project provides an example of how to set up a basic third person shooter multiplayer-ready project with the `AbilitySystemComponent` (`ASC`) on the `PlayerState` class for player/AI controlled heroes and the `ASC` on the `Character` class for AI controlled minions.

The goal is to keep this project simple while showing the GAS basics and demonstrating some commonly requested abilities with well-commented code. Because of its beginner focus, the project does not show advanced topics like [predicting projectiles](#concepts-p-spawn).

Concepts demonstrated:
* `ASC` on `PlayerState` vs `Character`
* Replicated `Attributes`
* Replicated animation montages
* `GameplayTags`
* Applying and removing `GameplayEffects` inside of and externally from `GameplayAbilities`
* Applying damage mitigated by armor to change health of a character
* `GameplayEffectExecutionCalculations`
* Stun effect
* Death and respawn
* Spawning actors (projectiles) from an ability on the server
* Predictively changing the local player's speed with aim down sights and sprinting
* Constantly draining stamina to sprint
* Using mana to cast abilities
* Passive abilities
* Stacking `GameplayEffects`
* Targeting actors
* `GameplayAbilities` created in Blueprint
* `GameplayAbilities` created in C++
* Instanced per `Actor` `GameplayAbilities`
* Non-Instanced `GameplayAbilities` (Jump)
* Static `GameplayCues` (FireGun projectile impact particle effect)
* Actor `GameplayCues` (Sprint and Stun particle effects)

시연된 개념:
* `PlayerState` 대 `Character`의 `ASC`
* 복제된 '속성'
* 복제된 애니메이션 몽타주
* `게임플레이 태그`
* `GameplayAbilities` 내부 및 외부에서 `GameplayEffects` 적용 및 제거
* 캐릭터의 건강을 변경하기 위해 갑옷에 의해 완화된 피해를 적용
* `GameplayEffectExecutionCalculations`
* 기절 효과
* 죽음과 부활
* 서버의 능력에서 액터(발사체) 생성
* 조준점 및 전력 질주로 로컬 플레이어의 속도를 예측적으로 변경
* 스프린트를 위해 지속적으로 스태미나 소모
* 능력을 시전하기 위해 마나 사용
* 패시브 능력
* 'GameplayEffects' 스태킹
* 대상 배우
* 블루프린트에서 생성된 `GameplayAbilities`
* C++에서 생성된 `GameplayAbilities`
* `Actor` `GameplayAbilities`별로 인스턴스화됨
* 인스턴스화되지 않은 'GameplayAbilities'(점프)
* 정적 `GameplayCues`(FireGun 발사체 충격 입자 효과)
* Actor `GameplayCues`(스프린트 및 스턴 파티클 효과)

hero class는 다음 능력을 가지고 있습니다. :

| Ability                    | Input Bind          | Predicted  | C++ / Blueprint | Description                                                                                                                                                                  |
| -------------------------- | ------------------- | ---------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Jump                       | Space Bar           | Yes        | C++             | Makes the hero jump.                                                                                                                                                         |
| Gun                        | Left Mouse Button   | No         | C++             | 투사체를 발사함. 애니메이션은 예측 가능하지만 투사체는 아님.                                                                                |
| Aim Down Sights            | Right Mouse Button  | Yes        | Blueprint       | 버튼을 누르고 있는 동안 영웅은 느리게 걷고 카메라는 총으로 더 정확한 샷을 할 수 있도록 확대됩니다.                                                    |
| Sprint                     | Left Shift          | Yes        | Blueprint       | 버튼을 누르고 있는 동안 영웅은 스테미너가 더 빨리 소모되는 달리기를 실행합니다.                                                                                                        |
| Forward Dash               | Q                   | Yes        | Blueprint       | 영웅은 스테미너 희생하여 앞으로 돌진합니다                                                                                                                              |
| Passive Armor Stacks       | Passive             | No         | Blueprint       | 4초마다 영웅은 최대 4중첩까지 방어구를 얻습니다. 피해를 입으면 방어구 1중첩이 제거됩니다.                                                    |
| Meteor                     | R                   | No         | Blueprint       | 플레이어는 위치를 지정해 적에게 피해를 입히고 기절시킬 유성을 떨어트립니다. The targeting is predicted while spawning the meteor is not.                    |

`GameplayAbilities`가 C++이든 블루프린트든 상관 없습니다. 혼합 사용하는 예시가 사용되었습니다. 

Minions do not come with any predefined `GameplayAbilities`. The Red Minions have more health regen while the Blue Minions have higher starting health.

For `GameplayAbility` naming, I used the suffix `_BP` to denote the `GameplayAbility's` logic was created in Blueprint. The lack of suffix means the logic was created in C++.

**Blueprint Asset Naming Prefixes**

| Prefix      | Asset Type          |
| ----------- | ------------------- |
| GA_         | GameplayAbility     |
| GC_         | GameplayCue         |
| GE_         | GameplayEffect      |

**[⬆ Back to Top](#table-of-contents)**

<a name="setup"></a>
## 3. Setting Up a Project Using GAS
Basic steps to set up a project using GAS:
1. 에디터에서 GameplayAbilitySystem plugin 활성화
1. `YourProjectName.Build.cs`를 편집해 `"GameplayAbilities", "GameplayTags", "GameplayTasks"`를`PrivateDependencyModuleNames`에 추가
1. Refresh/Regenerate your Visual Studio project files
1. Starting with 4.24, it is now mandatory to call `UAbilitySystemGlobals::Get().InitGlobalData()` to use [`TargetData`](#concepts-targeting-data). The Sample Project does this in `UAssetManager::StartInitialLoading()`. See [`InitGlobalData()`](#concepts-asg-initglobaldata) for more information.

That's all that you have to do to enable GAS. From here, add an [`ASC`](#concepts-asc) and [`AttributeSet`](#concepts-as) to your `Character` or `PlayerState` and start making [`GameplayAbilities`](#concepts-ga) and [`GameplayEffects`](#concepts-ge)!

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts"></a>
## 4. GAS Concepts

#### Sections

> 4.1 [Ability System Component](#concepts-asc)  
> 4.2 [Gameplay Tags](#concepts-gt)  
> 4.3 [Attributes](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [Gameplay Effects](#concepts-ge)  
> 4.6 [Gameplay Abilities](#concepts-ga)  
> 4.7 [Ability Tasks](#concepts-at)  
> 4.8 [Gameplay Cues](#concepts-gc)  
> 4.9 [Ability System Globals](#concepts-asg)  
> 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 Ability System Component
The `AbilitySystemComponent` (`ASC`) 가 가장 중요하다. 시스템과 모든 상호작용을 처리하는 `UActorComponent` ([`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)) 입니다. 어떤 `Actor`든[`GameplayAbilities`](#concepts-ga)을 쓰고, [`Attributes`](#concepts-a)를 가지고, [`GameplayEffects`](#concepts-ge)를 받으려면 한 개의 `ASC`가 attached 되야 합니다. 모든 객체는(`Attributes`는 예외로 [`AttributeSet`](#concepts-as)에서 replicate) `ASC`안에서 관리되고 replicate 됩니다 . Developers are expected but not required to subclass this.

 `ASC`가 부착된`Actor`는 `ASC`의`OwnerActor`라고 합니다. The physical representation `Actor` of the `ASC` is called the `AvatarActor`. The `OwnerActor` and the `AvatarActor` can be the same `Actor` as in the case of a simple AI minion in a MOBA game. They can also be different `Actors` as in the case of a player controlled hero in a MOBA game where the `OwnerActor` is the `PlayerState` and the `AvatarActor` is the hero's `Character` class. Most `Actors` will have the `ASC` on themselves. If your `Actor` will respawn and need persistence of `Attributes` or `GameplayEffects` between spawns (like a hero in a MOBA), then the ideal location for the `ASC` is on the `PlayerState`.

`ASC`가 부착된`Actor`는 `ASC`의`OwnerActor`라고 합니다. 'ASC'의 물리적 표현인 '액터'를 '아바타 액터'라고 합니다. 'OwnerActor'와 'AvatarActor'는 MOBA 게임에서 단순한 AI 미니언의 경우와 같은 'Actor'일 수 있습니다. 그들은 또한 'OwnerActor'가 'PlayerState'이고 'AvatarActor'가 영웅의 'Character' 클래스인 MOBA 게임에서 플레이어가 제어하는 영웅의 경우와 같이 다른 'Actors'일 수 있습니다. 대부분의 '액터'에는 'ASC'가 있습니다. '액터'가 리스폰되고 스폰 사이에 '속성' 또는 '게임플레이 효과'의 지속성이 필요한 경우(MOBA의 영웅처럼) 'ASC'의 이상적인 위치는 'PlayerState'입니다.


( 외부에서 입력을 받거나 죽고 부활할 때 기존 상태를 가지고 있어야 하는(아이템 같은 거) 애들은 ASC가 PlayerState에 있고 캐릭터는 따로 두는 것 같음)

**Note:** If your `ASC` is on your `PlayerState`, then you will need to increase the `NetUpdateFrequency` of your `PlayerState`. It defaults to a very low value on the `PlayerState` and can cause delays or perceived lag before changes to things like `Attributes` and `GameplayTags` happen on the clients. Be sure to enable [`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency), Fortnite uses it.

**참고:** `ASC`가 `PlayerState`에 있는 경우 `PlayerState`의 `NetUpdateFrequency`를 늘려야 합니다. 기본적으로 `PlayerState`의 값이 매우 낮고 `Attributes` 및 `GameplayTags`와 같은 변경 사항이 클라이언트에서 발생하기 전에 지연 또는 인지 지연이 발생할 수 있습니다. '적응형 네트워크 업데이트 빈도'를 활성화해야 합니다. Fortnite는 이를 사용합니다.

Both, the `OwnerActor` and the `AvatarActor` if different `Actors`, should implement the `IAbilitySystemInterface`. This interface has one function that must be overriden, `UAbilitySystemComponent* GetAbilitySystemComponent() const`, which returns a pointer to its `ASC`. `ASCs` interact with each other internally to the system by looking for this interface function.

'OwnerActor'와 'AvatarActor'가 다른 'Actors'인 경우 'IAbilitySystemInterface'를 구현해야 합니다. 이 인터페이스에는 'ASC'에 대한 포인터를 반환하는 'UAbilitySystemComponent* GetAbilitySystemComponent() const'라는 재정의해야 하는 함수가 하나 있습니다. 'ASC'는 이 인터페이스 기능을 찾아 시스템 내부에서 서로 상호 작용합니다.

The `ASC` holds its current active `GameplayEffects` in `FActiveGameplayEffectsContainer ActiveGameplayEffects`.

'ASC'는 'FACiveGameplayEffectsContainer ActiveGameplayEffects'에 현재 활성인 'GameplayEffects'를 보유합니다.

The `ASC` holds its granted `Gameplay Abilities` in `FGameplayAbilitySpecContainer ActivatableAbilities`. Any time that you plan to iterate over `ActivatableAbilities.Items`, be sure to add `ABILITYLIST_SCOPE_LOCK();` above your loop to lock the list from changing (due to removing an ability). Every `ABILITYLIST_SCOPE_LOCK();` in scope increments `AbilityScopeLockCount` and then decrements when it falls out of scope. Do not try to remove an ability inside the scope of `ABILITYLIST_SCOPE_LOCK();` (the clear ability functions check `AbilityScopeLockCount` internally to prevent removing abilities if the list is locked).

'ASC'는 'FGameplayAbilitySpecContainer ActivatableAbilities'에 부여된 'Gameplay Abilities'를 보유합니다. 'ActivatableAbilities.Items'를 반복할 계획이라면 언제든지 루프 위에 'ABILITYLIST_SCOPE_LOCK();'을 추가하여 (능력 제거로 인해) 목록이 변경되지 않도록 잠급니다. 범위의 모든 `ABILITYLIST_SCOPE_LOCK();`은 `AbilityScopeLockCount`를 증가시킨 다음 범위를 벗어날 때 감소합니다. `ABILITYLIST_SCOPE_LOCK();` 범위 내에서 능력을 제거하려고 하지 마십시오(목록이 잠겨 있는 경우 능력 제거를 방지하기 위해 능력 지우기 기능은 내부적으로 `AbilityScopeLockCount`를 확인합니다).

<a name="concepts-asc-rm"></a>
### 4.1.1 Replication Mode
The `ASC` defines three different replication modes for replicating `GameplayEffects`, `GameplayTags`, and `GameplayCues` - `Full`, `Mixed`, and `Minimal`. `Attributes` are replicated by their `AttributeSet`.

'ASC'는 'GameplayEffects', 'GameplayTags' 및 'GameplayCues'를 복제하기 위한 3가지 복제 모드('Full', 'Mixed' 및 'Minimal')를 정의합니다. '속성'은 '속성 집합'에 의해 복제됩니다.

| Replication Mode   | 사용대상                            | Description                                                                                                                    |
| ------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Full`             | Single Player                           | Every `GameplayEffect` is replicated to every client.                                                                          |
| `Mixed`            | Multiplayer, player controlled `Actors` | `GameplayEffects`는 소유자에게만 replicate됨. Only `GameplayTags` and `GameplayCues`만 전부 보임 |
| `Minimal`          | Multiplayer, AI controlled `Actors`     | `GameplayEffects` are never replicated to anyone. Only `GameplayTags` and `GameplayCues` are replicated to everyone.           |

**Note:** `Mixed` replication mode expects the `OwnerActor's` `Owner` to be the `Controller`. `PlayerState's` `Owner` is the `Controller` by default but `Character's` is not. If using `Mixed` replication mode with the `OwnerActor` not the `PlayerState`, then you need to call `SetOwner()` on the `OwnerActor` with a valid `Controller`.

**참고:** `혼합` 복제 모드에서는 `OwnerActor`의 `Owner`가 `Controller`가 될 것으로 예상합니다. `PlayerState`의 `Owner`는 기본적으로 `Controller`이지만 `Character's`는 그렇지 않습니다. `PlayerState`가 아닌 `OwnerActor`와 함께 `Mixed` 복제 모드를 사용하는 경우 유효한 `Controller`로 `OwnerActor`에서 `SetOwner()`를 호출해야 합니다.

(Mixed는 OwnerActor의 Owner가 Controller일 것을 기대. PlayerState의 Owner는 보통 Controller지만 Character는 아니기 때문.플레이어 상태가 아닌 OwnerActor에서 혼합  모드를 사용하는 경우 유효한 컨트롤러가 있는 OwnerActor에서만 SetOwner()를 호출해야 합니다.
Starting with 4.24, `PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 Setup and Initialization
`ASCs` are typically constructed in the `OwnerActor's` constructor and explicitly marked replicated. **This must be done in C++**.

'ASC'는 일반적으로 'OwnerActor' 생성자에서 생성되며 명시적으로 복제된 것으로 표시됩니다. **이 작업은 C++에서 수행해야 합니다**.


(C++로 ASC를 쓰고, replicate 된다고 밝혀야 함. 
> PlayerState설명

 - 모든 접속된 클라이언트는 현재 클라이언트의 정보를 포함하고 있는 한개의 PlayerState 객체를 갖는다
 - PlayerState 객체는 모든 클라이언트에게 Replicated 되므로 어떤 클라이언트에서 다른 클라이언트의 정보를 접할 수가 있다
 - 현재 클라이언트에서 다른 클라이언트의 PlayerState 객체에 접근하는 쉬운 방법은 GameState::getPlayerArray 를 이용하는 것이다
 - PlayerName, Score 등 다른 클라이언트에게 제공해야 하는 다양한 정보(커스텀 변수)를 이 객체에 저장하여 다른 클라이언트에게 전달할 수 있다
 - PlayerPawn이 Destroy 되더라도 PlayerState는 유지된다)
 
```c++
AGDPlayerState::AGDPlayerState()
{
	// Create ability system component, and set it to be explicitly replicated
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

The `ASC` needs to be initialized with its `OwnerActor` and `AvatarActor` on both the server and the client. You want to initialize after the `Pawn's` `Controller` has been set (after possession). Single player games only need to worry about the server path.

'ASC'는 서버와 클라이언트 모두에서 'OwnerActor'와 'AvatarActor'로 초기화해야 합니다. (소유 후) `Pawn`의 `Controller`가 설정된 후 초기화하고 싶습니다. 싱글 플레이어 게임은 서버 경로만 걱정하면 됩니다.

(ASC는 서버, 클라 양쪽에서 OwnerActor, AvatarActor로 초기화 되어야 한다.)

For player controlled characters where the `ASC` lives on the `Pawn`, I typically initialize on the server in the `Pawn's` `PossessedBy()` function and initialize on the client in the `PlayerController's` `AcknowledgePossession()` function.

'ASC'가 'Pawn'에 있는 플레이어 제어 캐릭터의 경우 일반적으로 'Pawn'의 'PossessedBy()' 함수로 서버에서 초기화하고 'PlayerController'의 'AcknowledgePossession()' 함수에서 클라이언트에서 초기화합니다.

(ASC가 폰에 있는(플레이어가 조작하는 캐릭터)는 서버에서 Pawn의 PossessedBy() 함수로 초기화하고 클라에서는 PlayerController의 AcknowledgePossession() 함수를 쓴다. [possession이 뭔지 링크](https://docs.unrealengine.com/4.27/ko/InteractiveExperiences/HowTo/PossessPawns/Blueprints/)
아래 방법은 글 작성자의 권장사항)

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

For player controlled characters where the `ASC` lives on the `PlayerState`, I typically initialize the server in the `Pawn's` `PossessedBy()` function and initialize on the client in the `Pawn's` `OnRep_PlayerState()` function. This ensures that the `PlayerState` exists on the client.

'ASC'가 'PlayerState'에 있는 플레이어 제어 캐릭터의 경우 일반적으로 'Pawn'의 'PossessedBy()' 함수에서 서버를 초기화하고 'Pawn'의 'OnRep_PlayerState()' 함수에서 클라이언트를 초기화합니다. 이렇게 하면 'PlayerState'가 클라이언트에 존재합니다.

(ASC가 PlayerState에 있을 땐 서버에서 Pawn의 PossessedBy() 함수를, 클라에선 Pawn의 OnRep_PlayerState() 함수를 쓴다. 이렇게 하면 PlayerState가 클라에 존재하게 된다. 
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

If you get the error message `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!` then you did not initialize your `ASC` on the client.

'LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted 능력 %s when not local!' 오류 메시지가 표시되면 클라이언트에서 'ASC'를 초기화하지 않은 것입니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt"></a>
### 4.2 Gameplay Tags
[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html) are hierarchical names in the form of `Parent.Child.Grandchild...` that are registered with the `GameplayTagManager`. These tags are incredibly useful for classifying and describing the state of an object. For example, if a character is stunned, we could give it a `State.Debuff.Stun` `GameplayTag` for the duration of the stun.

'FGameplayTags'는 'GameplayTagManager'에 등록된 'Parent.Child.Grandchild...' 형식의 계층적 이름입니다. 이러한 태그는 개체의 상태를 분류하고 설명하는 데 매우 유용합니다. 예를 들어 캐릭터가 기절하면 기절 기간 동안 'State.Debuff.Stun' 'GameplayTag'를 부여할 수 있습니다.

(GameplayTagManager에 태그를 추가할 수 있다. 개층적 구조로 객체의 상태를 묘사하거나 분류하는데 큰 도움이 된다. State.Debuff.Stun이라는 태그는 스턴 상태이며 스턴은 디버프라는 것을 알 수 있다. )

You will find yourself replacing things that you used to handle with booleans or enums with `GameplayTags` and doing boolean logic on whether or not objects have certain `GameplayTags`.

부울 또는 열거형으로 처리하던 것을 `GameplayTags`로 바꾸고 객체에 특정 `GameplayTags`가 있는지 여부에 대해 부울 논리를 수행하는 자신을 발견하게 될 것입니다.

(어떤 객체가 해당 태그를 가지고 있는지 확인도 가능하다)


When giving tags to an object, we typically add them to its `ASC` if it has one so that GAS can interact with them. `UAbilitySystemComponent` implements the `IGameplayTagAssetInterface` giving functions to access its owned `GameplayTags`.

객체에 태그를 부여할 때 일반적으로 GAS가 객체와 상호 작용할 수 있도록 객체에 태그가 있는 경우 'ASC'에 추가합니다. 'UAbilitySystemComponent'는 'IGameplayTagAssetInterface'를 구현하여 소유한 'GameplayTags'에 액세스하는 기능을 제공합니다.


Multiple `GameplayTags` can be stored in an `FGameplayTagContainer`. It is preferable to use a `GameplayTagContainer` over a `TArray<FGameplayTag>` since the `GameplayTagContainers` add some efficiency magic. While tags are standard `FNames`, they can be efficiently packed together in `FGameplayTagContainers` for replication if `Fast Replication` is enabled in the project settings. `Fast Replication` requires that the server and the clients have the same list of `GameplayTags`. This generally shouldn't be a problem so you should enable this option. `GameplayTagContainers` can also return a `TArray<FGameplayTag>` for iteration.

여러 개의 `GameplayTag`를 `FGameplayTagContainer`에 저장할 수 있습니다. `GameplayTagContainers`가 효율성 마법을 추가하기 때문에 `TArray<FGameplayTag>`보다 `GameplayTagContainer`를 사용하는 것이 좋습니다. 태그는 표준 'FNames'이지만 프로젝트 설정에서 'Fast Replication'이 활성화되어 있으면 리플리케이션을 위해 'FGameplayTagContainers'에 효율적으로 함께 묶을 수 있습니다. '빠른 복제'를 사용하려면 서버와 클라이언트에 'GameplayTags' 목록이 동일해야 합니다. 이것은 일반적으로 문제가 되지 않으므로 이 옵션을 활성화해야 합니다. `GameplayTagContainers`는 반복을 위해 `TArray<FGameplayTag>`를 반환할 수도 있습니다.

(여러 GameplayTags는 FGameplayTagContainer에 저장된다. 배열 쓰는 것 보다 이게 더 좋다. FGameplayTagContainer를 쓰고 Fast Replication 옵션을 프로젝트 세팅에서 켜라. 이 옵션은 서버와 클라가 같은 GameplayTags를 가질 것을 요구한다. 반복을 위한 TArray도 반환할 수 있다.)

`GameplayTags` stored in `FGameplayTagCountContainer` have a `TagMap` that stores the number of instances of that `GameplayTag`. A `FGameplayTagCountContainer` may still have the `GameplayTag` in it but its `TagMapCount` is zero. You may encounter this while debugging if an `ASC` still has a `GameplayTag`. Any of the `HasTag()` or `HasMatchingTag()` or similar functions will check the `TagMapCount` and return false if the `GameplayTag` is not present or its `TagMapCount` is zero.

'FGameplayTagCountContainer'에 저장된 'GameplayTags'에는 해당 'GameplayTag'의 인스턴스 수를 저장하는 'TagMap'이 있습니다. 'FGameplayTagCountContainer'에는 여전히 'GameplayTag'가 있을 수 있지만 'TagMapCount'는 0입니다. `ASC`에 여전히 `GameplayTag`가 있는 경우 디버깅하는 동안 이 문제가 발생할 수 있습니다. 'HasTag()' 또는 'HasMatchingTag()' 또는 이와 유사한 함수는 'TagMapCount'를 확인하고 'GameplayTag'가 없거나 해당 'TagMapCount'가 0이면 false를 반환합니다.

(FGameplayTagCountContainer에 저장된 태그는 태그맵을 통해 몇 개의 인스턴스가 태그를 가지고 있는지 저장한다. 이걸 바탕으로 HasTag()같은 함수들이 작동한다. 디버깅중 ASC에 
GameplayTag가 있을 경우 FGameplayTagCountContainer에 GameplayTag가 있는데도 TagMapCount가 0으로 나올 수 있다)


`GameplayTags` must be defined ahead of time in the `DefaultGameplayTags.ini`. The UE5 Editor provides an interface in the project settings to let developers manage `GameplayTags` without needing to manually edit the `DefaultGameplayTags.ini`. The `GameplayTag` editor can create, rename, search for references, and delete `GameplayTags`.

`GameplayTags`는 `DefaultGameplayTags.ini`에서 미리 정의되어야 합니다. UE5 에디터는 개발자가 'DefaultGameplayTags.ini'를 수동으로 편집할 필요 없이 'GameplayTags'를 관리할 수 있도록 프로젝트 설정에 인터페이스를 제공합니다. `GameplayTag` 편집기는 `GameplayTags`를 생성, 이름 변경, 참조 검색 및 삭제할 수 있습니다.

![GameplayTag Editor in Project Settings](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

Searching for `GameplayTag` references will bring up the familiar `Reference Viewer` graph in the Editor showing all the assets that reference the `GameplayTag`. This will not however show any C++ classes that reference the `GameplayTag`.

'GameplayTag' 참조를 검색하면 'GameplayTag'를 참조하는 모든 자산을 보여주는 익숙한 'Reference Viewer' 그래프가 에디터에 표시됩니다. 그러나 'GameplayTag'를 참조하는 C++ 클래스는 표시되지 않습니다.

Renaming `GameplayTags` creates a redirect so that assets still referencing the original `GameplayTag` can redirect to the new `GameplayTag`. I prefer if possible to instead create a new `GameplayTag`, update all the references manually to the new `GameplayTag`, and then delete the old `GameplayTag` to avoid creating a redirect.


'GameplayTags'의 이름을 바꾸면 여전히 원래 'GameplayTag'를 참조하는 자산이 새 'GameplayTag'로 리디렉션될 수 있도록 리디렉션이 생성됩니다. 대신 가능하면 새 `GameplayTag`를 만들고 모든 참조를 새 `GameplayTag`로 수동으로 업데이트한 다음 이전 `GameplayTag`를 삭제하여 리디렉션 생성을 방지하는 것을 선호합니다.

In addition to `Fast Replication`, the `GameplayTag` editor has an option to fill in commonly replicated `GameplayTags` to optimize them further.

`빠른 복제` 외에도 `GameplayTag` 편집기에는 일반적으로 복제되는 `GameplayTags`를 채워 추가로 최적화하는 옵션이 있습니다.

`GameplayTags` are replicated if they're added from a `GameplayEffect`. The `ASC` allows you to add `LooseGameplayTags` that are not replicated and must be managed manually. The Sample Project uses a `LooseGameplayTag` for `State.Dead` so that the owning clients can immediately respond to when their health drops to zero. Respawning manually sets the `TagMapCount` back to zero. Only manually adjust the `TagMapCount` when working with `LooseGameplayTags`. It is preferable to use the `UAbilitySystemComponent::AddLooseGameplayTag()` and `UAbilitySystemComponent::RemoveLooseGameplayTag()` functions than manually adjusting the `TagMapCount`.

'GameplayTags'는 'GameplayEffect'에서 추가된 경우 복제됩니다. `ASC`를 사용하면 복제되지 않고 수동으로 관리해야 하는 `LooseGameplayTags`를 추가할 수 있습니다. 샘플 프로젝트는 'State.Dead'에 'LooseGameplayTag'를 사용하므로 소유 클라이언트는 건강이 0으로 떨어질 때 즉시 응답할 수 있습니다. Respawning은 수동으로 `TagMapCount`를 다시 0으로 설정합니다. `LooseGameplayTags`로 작업할 때만 `TagMapCount`를 수동으로 조정하십시오. 'TagMapCount'를 수동으로 조정하는 것보다 'UAbilitySystemComponent::AddLooseGameplayTag()' 및 'UAbilitySystemComponent::RemoveLooseGameplayTag()' 함수를 사용하는 것이 좋습니다.

태그의 레퍼런스 얻기 

Getting a reference to a `GameplayTag` in C++:
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

For advanced `GameplayTag` manipulation like getting the parent or children `GameplayTags`, look at the functions offered by the `GameplayTagManager`. To access the `GameplayTagManager`, include `GameplayTagManager.h` and call it with `UGameplayTagManager::Get().FunctionName`. The `GameplayTagManager` actually stores the `GameplayTags` as relational nodes (parent, child, etc) for faster processing than constant string manipulation and comparisons.

상위 또는 하위 `GameplayTags` 가져오기와 같은 고급 `GameplayTag` 조작의 경우 `GameplayTagManager`에서 제공하는 기능을 살펴보세요. `GameplayTagManager`에 액세스하려면 `GameplayTagManager.h`를 포함하고 `UGameplayTagManager::Get().FunctionName`으로 호출합니다. 'GameplayTagManager'는 실제로 'GameplayTags'를 관계형 노드(부모, 자식 등)로 저장하여 지속적인 문자열 조작 및 비교보다 빠른 처리를 제공합니다.

`GameplayTags` and `GameplayTagContainers` can have the optional `UPROPERTY` specifier `Meta = (Categories = "GameplayCue")` that filters the tags in the Blueprint to show only `GameplayTags` that have the parent tag of `GameplayCue`. This is useful when you know the `GameplayTag` or `GameplayTagContainer` variable should only be used for `GameplayCues`.

`GameplayTags` 및 `GameplayTagContainers`는 선택적 `UPROPERTY` 지정자 `Meta = (Categories = "GameplayCue")`를 가질 수 있으며, 이 지정자는 `GameplayCue`의 상위 태그가 있는 `GameplayTags`만 표시하도록 블루프린트의 태그를 필터링합니다. 이는 `GameplayTag` 또는 `GameplayTagContainer` 변수가 `GameplayCues`에만 사용해야 한다는 것을 알고 있을 때 유용합니다.

Alternatively, there's a separate structure called `FGameplayCueTag` that encapsulates a `FGameplayTag` and also automatically filters `GameplayTags` in Blueprint to only show those tags with the parent tag of `GameplayCue`.

또는 'FGameplayTag'를 캡슐화하고 'GameplayCue'의 부모 태그가 있는 태그만 표시하도록 블루프린트에서 'GameplayTags'를 자동으로 필터링하는 'FGameplayCueTag'라는 별도의 구조가 있습니다.

If you want to filter a `GameplayTag` parameter in a function, use the `UFUNCTION` specifier `Meta = (GameplayTagFilter = "GameplayCue")`. `GameplayTagContainer` parameters in functions can not be filtered. If you would like to edit your engine to allow this, look at how `SGameplayTagGraphPin::ParseDefaultValueData()` from `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp` calls `FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);` and passes `FilterString` to `SGameplayTagWidget` in `SGameplayTagGraphPin::GetListContent()`. The `GameplayTagContainer` version of these functions in `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp` do not check for the meta field properties and pass along the filter.
 
함수에서 `GameplayTag` 매개변수를 필터링하려면 `UFUNCTION` 지정자 `Meta = (GameplayTagFilter = "GameplayCue")`를 사용하세요. 함수의 'GameplayTagContainer' 매개변수는 필터링할 수 없습니다. 이를 허용하도록 엔진을 편집하려면 `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp`의 `SGameplayTagGraphPin::ParseDefaultValueData()`가 `FilterString = UGameplayTagsManager:: Get().GetCategoriesMetaFromField(PinStructType);` 및 `SGameplayTagGraphPin::GetListContent()`의 `SGameplayTagWidget`에 `FilterString`을 전달합니다. 이 함수의 `GameplayTagContainer` 버전은 `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin`에 있습니다.

The Sample Project extensively uses `GameplayTags`.

샘플 프로젝트는 `GameplayTags`를 광범위하게 사용합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt-change"></a>
### 4.2.1 Responding to Changes in Gameplay Tags
The `ASC` provides a delegate for when `GameplayTags` are added or removed. It takes in a `EGameplayTagEventType` that can specify only to fire when the `GameplayTag` is added/removed or for any change in the `GameplayTag's` `TagMapCount`.

'ASC'는 'GameplayTags'가 추가되거나 제거될 때 대리자를 제공합니다. `GameplayTag`가 추가/제거되거나 `GameplayTag`의 `TagMapCount`가 변경될 때만 실행되도록 지정할 수 있는 `EGameplayTagEventType`을 받습니다.

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

The callback function has a parameter for the `GameplayTag` and the new `TagCount`. 

(델리게이트에 집어넣을 콜백들은 인자 2 개 받음)
```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 Attributes

<a name="concepts-a-definition"></a>
#### 4.3.1 Attribute Definition
`Attributes` are float values defined by the struct [`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html). These can represent anything from the amount of health a character has to the character's level to the number of charges that a potion has. If it is a gameplay-related numerical value belonging to an `Actor`, you should consider using an `Attribute` for it. `Attributes` should generally only be modified by [`GameplayEffects`](#concepts-ge) so that the ASC can [predict](#concepts-p) the changes.

'속성'은 'FGameplayAttributeData' 구조체에 의해 정의된 부동 소수점 값입니다. 이는 캐릭터의 체력에서 캐릭터의 레벨, 물약의 충전 횟수까지 무엇이든 나타낼 수 있습니다. '액터'에 속하는 게임플레이 관련 수치라면 '속성'을 사용하는 것을 고려해야 합니다. '속성'은 일반적으로 'GameplayEffects' 변경 사항에 의해서만 수정되어야 합니다. 

`Attributes` are defined by and live in an [`AttributeSet`](#concepts-as). The `AttributeSet` is responsible for replicating `Attributes` that are marked for replication. See the section on [`AttributeSets`](#concepts-as) for how to define `Attributes`.

(Attributes는 AttributeSet에서 정의함. AttributeSet은 replicate도 한다. 자세한건 [`AttributeSets`](#concepts-as) )

**Tip:** If you don't want an `Attribute` to show up in the Editor's list of `Attributes`, you can use the `Meta = (HideInDetailsView)` `property specifier`.

**팁:** 편집기의 '속성' 목록에 '속성'이 표시되지 않도록 하려면 '메타 = (HideInDetailsView)' '속성 지정자'를 사용할 수 있습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 BaseValue vs CurrentValue
An `Attribute` is composed of two values - a `BaseValue` and a `CurrentValue`. The `BaseValue` is the permanent value of the `Attribute` whereas the `CurrentValue` is the `BaseValue` plus temporary modifications from `GameplayEffects`. For example, your `Character` may have a movespeed `Attribute` with a `BaseValue` of 600 units/second. Since there are no `GameplayEffects` modifying the movespeed yet, the `CurrentValue` is also 600 u/s. If she gets a temporary 50 u/s movespeed buff, the `BaseValue` stays the same at 600 u/s while the `CurrentValue` is now 600 + 50 for a total of 650 u/s. When the movespeed buff expires, the `CurrentValue` reverts back to the `BaseValue` of 600 u/s.

'속성'은 'BaseValue'와 'CurrentValue'의 두 가지 값으로 구성됩니다. 'BaseValue'는 'Attribute'의 영구 값인 반면, 'CurrentValue'는 'BaseValue'에 'GameplayEffects'의 임시 수정을 더한 것입니다. 예를 들어, `Character`는 `BaseValue`가 600units/second인 movespeed `Attribute`를 가질 수 있습니다. 아직 movespeed를 수정하는 `GameplayEffects`가 없기 때문에 `CurrentValue`도 600u/s입니다. 그녀가 일시적인 50u/s 이동 속도 버프를 받으면 `BaseValue`는 600u/s에서 동일하게 유지되는 반면 `CurrentValue`는 이제 600 + 50이 되어 총 650u/s가 됩니다. movespeed 버프가 만료되면 'CurrentValue'가 600u/s의 'BaseValue'로 되돌아갑니다.

Often beginners to GAS will confuse `BaseValue` with a maximum value for an `Attribute` and try to treat it as such. This is an incorrect approach. Maximum values for `Attributes` that can change or are referenced in abilities or UI should be treated as separate `Attributes`. For hardcoded maximum and minimum values, there is a way to define a `DataTable` with `FAttributeMetaData` that can set maximum and minimum values, but Epic's comment above the struct calls it a "work in progress". See `AttributeSet.h` for more information. To prevent confusion, I recommend that maximum values that can be referenced in abilities or UI be made as separate `Attributes` and hardcoded maximum and minimum values that are only used for clamping `Attributes` be defined as hardcoded floats in the `AttributeSet`. Clamping of `Attributes` is discussed in [PreAttributeChange()](#concepts-as-preattributechange) for changes to the `CurrentValue` and [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute) for changes to the `BaseValue` from `GameplayEffects`.

GAS를 처음 접하는 사람들은 종종 `BaseValue`를 `Attribute`의 최대값과 혼동하여 그렇게 취급하려고 합니다. 이것은 잘못된 접근 방식입니다. 능력이나 UI에서 변경될 수 있거나 참조되는 '속성'의 최대값은 별도의 '속성'으로 처리해야 합니다. 하드코딩된 최대값과 최소값의 경우 최대값과 최소값을 설정할 수 있는 `FAttributeMetaData`로 `DataTable`을 정의하는 방법이 있지만 구조체 위에 있는 Epic의 주석에서는 이를 "작업 진행 중"이라고 부릅니다. 자세한 내용은 `AttributeSet.h`를 참조하세요. 혼동을 방지하기 위해 어빌리티나 UI에서 참조할 수 있는 최대값은 별도의 'Attributes'로 지정하고, 'Attributes' 클램핑에만 사용되는 하드코딩된 최대값과 최소값은 'AttributeSet'에서 하드코딩된 부동 소수점으로 정의하는 것이 좋습니다.


(BaseValue를 어떤 Attribute의 최댓값으로 잘못 사용하기도 한다. 최댓값은 또 다른 Attributes 값으로 지정해야 한다. 혼동을 막기 위해 UI, ability에서 참조하는 최대값은 Attribute로 만들고 `클램핑` 하는데만 사용하는 하드코딩된 최대 최소 값은 AttributeSet에 적어라. 클램핑 관련 설명 [PreAttributeChange()](#concepts-as-preattributechange).  `CurrentValue` 바꾸는 얘기는 여기[PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute), `BaseValue` 바꾸는 얘기는 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)) 여기. )

Permanent changes to the `BaseValue` come from `Instant` `GameplayEffects` whereas `Duration` and `Infinite` `GameplayEffects` change the `CurrentValue`. Periodic `GameplayEffects` are treated like instant `GameplayEffects` and change the `BaseValue`.

'BaseValue'에 대한 영구적인 변경은 'Instant' 'GameplayEffects'에서 오는 반면, 'Duration' 및 'Infinite' 'GameplayEffects'는 'CurrentValue'를 변경합니다. 주기적 'GameplayEffects'는 인스턴트 'GameplayEffects'처럼 취급되며 'BaseValue'를 변경합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 4.3.3 Meta Attributes
Some `Attributes` are treated as placeholders for temporary values that are intended to interact with `Attributes`. These are called `Meta Attributes`. For example, we commonly define damage as a `Meta Attribute`. Instead of a `GameplayEffect` directly changing our health `Attribute`, we use a `Meta Attribute` called damage as a placeholder. This way the damage value can be modified with buffs and debuffs in an [`GameplayEffectExecutionCalculation`](#concepts-ge-ec) and can be further manipulated in the `AttributeSet`, for example subtracting the damage from a current shield `Attribute`, before finally subtracting the remainder from the health `Attribute`. The damage `Meta Attribute` has no persistence between `GameplayEffects` and is overriden by every one. `Meta Attributes` are not typically replicated.

일부 '속성'은 '속성'과 상호 작용하기 위한 임시 값의 자리 표시자로 처리됩니다. 이를 '메타 속성'이라고 합니다. 예를 들어 우리는 일반적으로 데미지를 '메타 속성'으로 정의합니다. 'GameplayEffect'가 건강 '속성'을 직접 변경하는 대신, 데미지라는 '메타 속성'을 자리 표시자로 사용합니다. 이런 식으로 데미지 값은 `GameplayEffectExecutionCalculation`에서 버프와 디버프로 수정될 수 있고 `AttributeSet`에서 추가로 조작될 수 있습니다. 예를 들어 현재 방패 `Attribute`에서 데미지를 빼고 마지막으로 건강 `에서 나머지를 빼기 전에 조작할 수 있습니다. 속성`. 피해 '메타 속성'은 'GameplayEffects' 간에 지속성이 없으며 모든 것에 의해 재정의됩니다. '메타 속성'은 일반적으로 복제되지 않습니다.

(다른 Attributes를 위한 임시 Attributes도 존재하는데, `Meta Attributes`라 한다. 데미지와 관련된 예시가 있다. 피해를 받을 때는 GameplayEffect를 이용해 체력을 직접 깎기 보다는 데미지라는 변수를 이용하는 것이 좋다. [`GameplayEffectExecutionCalculation`](#concepts-ge-ec) 내부에 있는 버프, 디버프나 AttributeSet 내부에 있는 Attribute에 의해 최종적으로 체력이 깎이는 양은 다를 수 있다. 100의 피해를 받을 때 10%의 피해를 줄이는 장비가 있다면 체력은 90만 줄어들 것이다. `Meta Attributes`는 `GameplayEffects`와 지속적인 관계가 없기에 대부분 replicate 되지 않는다. )

`Meta Attributes` provide a good logical separation for things like damage and healing between "How much damage did we do?" and "What do we do with this damage?". This logical separation means our `Gameplay Effects` and `Execution Calculations` don't need to know how the Target handles the damage. Continuing our damage example, the `Gameplay Effect` determines how much damage and then the `AttributeSet` decides what to do with that damage. Not all characters may have the same `Attributes`, especially if you use subclassed `AttributeSets`. The base `AttributeSet` class may only have a health `Attribute`, but a subclassed `AttributeSet` may add a shield `Attribute`. The subclassed `AttributeSet` with the shield `Attribute` would distribute the damage received differently than the base `AttributeSet` class.

'메타 속성'은 "얼마나 많은 피해를 입혔습니까?" 및 "이 손상으로 무엇을 할 수 있습니까?". 이 논리적 분리는 `Gameplay Effects`와 `Execution Calculations`가 대상이 피해를 처리하는 방법을 알 필요가 없음을 의미합니다. 피해 사례를 계속하면 'Gameplay Effect'가 피해량을 결정한 다음 'AttributeSet'이 해당 피해로 무엇을 할지 결정합니다. 특히 서브클래싱된 'AttributeSets'를 사용하는 경우 모든 문자가 동일한 '속성'을 가질 수는 없습니다. 기본 `AttributeSet` 클래스는 상태 `Attribute`만 가질 수 있지만 서브클래싱된 `AttributeSet`은 방패 `Attribute`를 추가할 수 있습니다. 방패 `Attribute`가 있는 서브클래싱된 `AttributeSet`은 기본 `AttributeSet` 클래스와 다르게 받는 피해를 분산합니다.

(`Meta Attributes`는 "얼마의 피해를 받았는가?"와 "피해를 어떻게 적용해야 하는가?" 사이를 논리적으로 분리시킨다. 이 말은 `Gameplay Effects`와 `Execution Calculations`가 대상이 피해를 어떻게 처리하든 알 필요 없다는 것이다. 앞의 예시를 계속 얘기하자면, `Gameplay Effect`는 100의 피해를 줘야 한다고 알리고 `AttributeSet`은 알아서 10% 차감한 90의 체력을 깎는다. )

While `Meta Attributes` are a good design pattern, they are not mandatory. If you only ever have one `Execution Calculation` used for all instances of damage and one `Attribute Set` class shared by all characters, then you may be fine doing the damage distribution to health, shields, etc. inside of the `Execution Calculation` and directly modifying those `Attributes`. You'll only be sacrificing flexibility, but that may be okay for you.

(이 방법은 좋지만 의무는 아니다. 모든 캐릭터들이 동일한 `Attribute Set`을 가지고, 같은 피해량 공식을 쓴다면 굳이 안 써도 된다. )
**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 Responding to Attribute Changes
To listen for when an `Attribute` changes to update the UI or other gameplay, use `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`. This function returns a delegate that you can bind to that will be automatically called whenever an `Attribute` changes. The delegate provides a `FOnAttributeChangeData` parameter with the `NewValue`, `OldValue`, and `FGameplayEffectModCallbackData`. **Note:** The `FGameplayEffectModCallbackData` will only be set on the server.

(Attribute가 변경될 때 알고 싶다면 `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`를 써라. 얘가 리턴하는 델리게이트는 값이 변경될 때 실행된다. 콜백은 FOnAttributeChangeData을 인자로 받는데 이 안에는 `NewValue`, `OldValue`, and `FGameplayEffectModCallbackData`가 있다. )
```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

The Sample Project binds to the `Attribute` value changed delegates on the `GDPlayerState` to update the HUD and to respond to player death when health reaches zero.

(샘플 프로젝트는 GDPlayerState의 `Attribute` 변경을 델리게이트 해 HUD에 표시하고, 체력이 0이 되면 죽인다.)

A custom Blueprint node that wraps this into an `ASyncTask` is included in the Sample Project. It is used in the `UI_HUD` UMG Widget to update the health, mana, and stamina values. This `AsyncTask` will live forever until manually called `EndTask()`, which we do in the UMG Widget's `Destruct` event. See `AsyncTaskAttributeChanged.h/cpp`.

(이를 `Attribute`로 래핑한 커스텀 블루프린트가 샘플 프로젝트에 있다. UI_HUD UMG가 체력, 마나, 스테미너를 업데이트 한다. 위젯이 소멸되어 EndTask()가 호출되기 전까지 계속 루프를 돈다. `AsyncTaskAttributeChanged.h/cpp` 참고)

![Listen for Attribute Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-derived"></a>
#### 4.3.5 Derived Attributes
To make an `Attribute` that has some or all of its value derived from one or more other `Attributes`, use an `Infinite` `GameplayEffect` with one or more `Attribute Based` or [`MMC`](#concepts-ge-mmc) [`Modifiers`](#concepts-ge-mods). The `Derived Attribute` will update automatically when an `Attribute` that it depends on is updated.

하나 이상의 다른 '속성'에서 파생된 값의 일부 또는 전부를 갖는 '속성'을 만들려면 하나 이상의 '속성 기반' 또는 'MMC'와 함께 '무한' 'GameplayEffect'를 사용하세요. '파생 속성'은 의존하는 '속성'이 업데이트되면 자동으로 업데이트됩니다.

The final formula for all the `Modifiers` on a `Derived Attribute` is the same formula for `Modifier Aggregators`. If you need calculations to happen in a certain order, do it all inside of an `MMC`.

'파생 속성'의 모든 '수정자'에 대한 최종 공식은 '수정자 집계자'에 대한 공식과 동일합니다. 특정 순서로 계산을 수행해야 하는 경우 'MMC' 내부에서 모든 작업을 수행합니다.

(하나 이상의 다른 `Attributes` 에서 파생된 값의 일부 또는 전부를 갖는 `Attributes`를 만들기 위해, 한 개 이상의 `Attribute Based` 또는 [`MMC`](#concepts-ge-mmc) [`Modifiers`](#concepts-ge-mods)와 함께 `Infinite` `GameplayEffect`를 써라)

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

**Note:** If playing with multiple clients in PIE, you need to disable `Run Under One Process` in the Editor Preferences otherwise the `Derived Attributes` will not update when their independent `Attributes` update on clients other than the first.

(에디터에서 플레이 할 경우 `Run Under One Process`(한 개 프로세스에서 실행) 옵션을 꺼라. `Derived Attributes`가 갱신 안 된다. 

In this example, we have an `Infinite` `GameplayEffect` that derives the value of `TestAttrA` from the `Attributes`, `TestAttrB` and `TestAttrC`, in the formula `TestAttrA = (TestAttrA + TestAttrB) * ( 2 * TestAttrC)`. `TestAttrA` recalculates its value automatically whenever any of the `Attributes` update their values.

(`Derived Attributes`의 예시가 이런 거다. 다른 값들이 바뀌면 자동으로 변해야 하는 값들)

![Derived Attribute Example](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 Attribute Set

<a name="concepts-as-definition"></a>
#### 4.4.1 Attribute Set Definition
The `AttributeSet` defines, holds, and manages changes to `Attributes`. Developers should subclass from [`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html). Creating an `AttributeSet` in an `OwnerActor's` constructor automatically registers it with its `ASC`. **This must be done in C++**.

(`AttributeSet`은 `Attributes`를 정의, 관리한다. [`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html)의 서브 클래스여야 한다. 주인 액터의 생성자는 ASC에 `AttributeSet`을 자동으로 등록한다. **꼭  C++에서 이 과정 진행해야 함**

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 Attribute Set Design
An `ASC` may have one or many `AttributeSets`. AttributeSets have negligible memory overhead so how many `AttributeSets` to use is an organizational decision left up to the developer.

('ASC'는 하나 이상의 `AttributeSets`을 가질 수 있습니다. `AttributeSets`의 메모리 오버헤드는 무시할 수 있으므로 `AttributeSets` 을 몇 개나 쓸지는 조직에서 결정해라.)

It is acceptable to have one large monolithic `AttributeSet` shared by every `Actor` in your game and only use attributes if needed while ignoring unused attributes.

(모든 액터들이 쓰는 커다란 단일 `AttributeSet` 사용도 가능하며, 사용되지 않는 속성은 무시하고 필요한 경우에만 사용한다.)

Alternatively, you may choose to have more than one `AttributeSet` representing groupings of `Attributes` that you selectively add to your `Actors` as needed. For example, you could have an `AttributeSet` for health related `Attributes`, an `AttributeSet` for mana related `Attributes`, and so on. In a MOBA game, heroes might need mana but minions might not. Therefore the heroes would get the mana `AttributeSet` and minions would not.

(필요하다면 여러 `AttributeSet` 사용도 가능하다. MOBA 게임에서 영웅은 마나와 체력이 필요하다. 하지만 미니언은 체력만 있으면 된다. 영웅에는 마나가 있는 `AttributeSet`을 적용하고 미니언은 없는 세트를 추가한다. )

Additionally, `AttributeSets` can be subclassed as another means of selectively choosing which `Attributes` an `Actor` has. `Attributes` are internally referred to as `AttributeSetClassName.AttributeName`. When you subclass an `AttributeSet`, all of the `Attributes` from the parent class will still have the parent class's name as the prefix.

(또한 `AttributeSets`은 어떤 `Attributes`가 `Actor`에 있는지 선택하는 수단으로 불 수도 있다. `적클래스.폭탄병`의 경우 폭탄병은 '적' 클래스의 모든 속성이 있을 것이다. )

While you can have more than one `AttributeSet`, you should not have more than one `AttributeSet` of the same class on an `ASC`. If you have more than one `AttributeSet` from the same class, it won't know which `AttributeSet` to use and will just pick one.

(`AttributeSet`은 두 개 이상 가질 수 있지만 같은 `ASC`인 `AttributeSet`은 한 개만 가질 수 있다. ) 

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 Subcomponents with Individual Attributes
In the scenario where you have multiple damageable components on a `Pawn` like individually damageable armor pieces, I recommend that if you know the maximum number of damageable components that a `Pawn` could have to make that many health `Attributes` on one `AttributeSet` - DamageableCompHealth0, DamageableCompHealth1, etc. to represent logical 'slots' for those damageable components. In your damageable component class instance, assign the slot number `Attribute` that can be read by `GameplayAbilities` or [`Executions`](#concepts-ge-ec) to know which `Attribute` to apply damage to. `Pawns` that have less than the maximum number or zero of damageable components are fine. Just because a `AttributeSet` has an `Attribute`, doesn't mean that you have to use it. Unused `Attributes` take up trivial amount of memory.

(신발, 갑옷 등 서로 다른 부위의 내구도가 있는 장비를 장착하고 있고, 장착 가능한 최대 부위 개수를 안다면, 각각을 `AttributeSet`의 `Attributes`으로 만들어 논리적으로 구별하라. 장비 객체에 `Attribute`를 각각 부여해  `GameplayAbilities` or [`Executions`](#concepts-ge-ec)에서 어떤 `Attribute`에 피해를 줘야 하는지 알 수 있다. 메모리에 영향이 별로 없으므로 `Attribute`를 만들어두고 안 써도 된다. 

If your subcomponents need many `Attributes` each, there's potentially an unbounded number of subcomponents, the subcomponents can detach and be used by other players (e.g. weapons), or for any other reason this approach doesn't work for you, I'd recommend switching away from `Attributes` and instead store plain old floats on the components. See [Item Attributes](#concepts-as-design-itemattributes).

(하위 요소가 많은 `Attributes`를 필요로 하는 경우, 하위 요소의 수가 무한한 경우, 무기처럼 하위 요소를 장착 해제하고 다른 사람이 쓸 수 있는 경우(주워서) 등등은 쓸모가 경우,`Attributes` 대신 flotat 값을 저장하는 게 낫다. See [Item Attributes](#concepts-as-design-itemattributes).)

<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 Adding and Removing AttributeSets at Runtime
`AttributeSets` can be added and removed from an `ASC` at runtime; however, removing `AttributeSets` can be dangerous. For example, if an `AttributeSet` is removed on a client before the server and an `Attribute` value change is replicated to client, the `Attribute` won't find its `AttributeSet` and crash the game.

(`AttributeSets`을 런타임에서 `ASC`에 추가 제거 할 수 있지만, `AttributeSets`를 제거하는 것은 위험하다. `AttributeSet`이 서버보다 먼저 클라에서 제거되고, `Attribute`값이 변경되어 클라이언트로 replicate 되면 게임이 크래시 난다. 

On weapon add to inventory:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

On weapon remove from inventory:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```
<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 Item Attributes (Weapon Ammo)
There's a few ways to implement equippable items with `Attributes` (weapon ammo, armor durability, etc). All of these approaches store values directly on the item. This is necessary for items that can be equipped by more than one player over its lifetime.

(`Attributes` (weapon ammo, armor durability, etc) 를 이용한 구현 방법이 몇가지 있다. 이런 방법을은 아이템에 값을 바로 저장한다. 아이템을 다수의 인원이 장착할 수 있을 때 사용된다. )

> 1. Use plain floats on the item (**Recommended**)
> 1. Separate `AttributeSet` on the item
> 1. Separate `ASC` on the item


(아이템에 하드코딩하기, `AttributeSet`을 아이템에서 분리하기 `ASC`를 분리하기


<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 Plain Floats on the Item
Instead of `Attributes`, store plain float values on the item class instance. Fortnite and [GASShooter](https://github.com/tranek/GASShooter) handle gun ammo this way. For a gun, store the max clip size, current ammo in clip, reserve ammo, etc directly as replicated floats (`COND_OwnerOnly`) on the gun instance. If weapons share reserve ammo, you would move the reserve ammo onto the character as an `Attribute` in a shared ammo `AttributeSet` (reload abilities can use a `Cost GE` to pull from reserve ammo into the gun's float clip ammo). Since you're not using `Attributes` for current clip ammo, you will need to override some functions in `UGameplayAbility` to check and apply cost against the floats on the gun. Making the gun the `SourceObject` in the [`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec) when granting the ability means you'll have access to the gun that granted the ability inside the ability.

'속성' 대신 항목 클래스 인스턴스에 일반 부동 소수점 값을 저장합니다. Fortnite와 GASShooter는 총기 탄약을 이런 식으로 처리합니다. 총의 경우 최대 클립 크기, 클립의 현재 탄약, 예비 탄약 등을 총 인스턴스에 복제된 부동 소수점(`COND_OwnerOnly`)으로 직접 저장합니다. 무기가 예비 탄약을 공유하는 경우 공유 탄약 'AttributeSet'의 '속성'으로 예비 탄약을 캐릭터에 옮깁니다(재장전 능력은 예비 탄약에서 총의 플로트 클립 탄약으로 끌어오기 위해 '비용 GE'를 사용할 수 있습니다). 현재 탄약에 '속성'을 사용하지 않기 때문에 'UGameplayAbility'의 일부 기능을 재정의하여 총의 부유물에 대한 비용을 확인하고 적용해야 합니다. 능력을 부여할 때 'GameplayAbilitySpec'에서 총을 'SourceObject'로 만드는 것은 당신을 의미합니다'

To prevent the gun from replicating back the ammo amount and clobbering the local ammo amount during automatic fire, disable replication while the player has a `IsFiring` `GameplayTag` in `PreReplication()`. You're essentially doing your own local prediction here.

자동 발사 중에 총이 탄약 양을 복제하고 로컬 탄약 양을 방해하는 것을 방지하려면 플레이어가 `PreReplication()`에 `IsFiring` `GameplayTag`가 있는 동안 복제를 비활성화하십시오. 당신은 본질적으로 여기에서 당신 자신의 지역 예측을 하고 있습니다.

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

Benefits:
1. Avoids limitations of using `AttributeSets` (see below)

Limitations:
1. Can not use existing `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Requires work to override key functions on `UGameplayAbility` to check and apply ammo costs against the gun's floats

이익:
1. 'AttributeSets' 사용의 제한을 피합니다(아래 참조).

제한 사항:
1. 기존 'GameplayEffect' 워크플로를 사용할 수 없습니다(탄약 사용을 위한 '비용 GE' 등).
1. 'UGameplayAbility'의 주요 기능을 재정의하는 작업이 필요하여 총의 부유물에 대한 탄약 비용을 확인하고 적용해야 합니다.


<a name="concepts-as-design-itemattributes-attributeset"></a>
###### 4.4.2.3.2 `AttributeSet` on the Item
Using a separate `AttributeSet` on the item that gets [added to the player's `ASC` on adding it to the player's inventory](#concepts-as-design-addremoveruntime) can work, but it has some major limitations. I had this working in early versions of [GASShooter](https://github.com/tranek/GASShooter) for the weapon ammo. The weapon stores its `Attributes` such as max clip size, current ammo in clip, reserve ammo, etc in an `AttributeSet` that lives on the weapon class. If weapons share reserve ammo, you would move the reserve ammo onto the character in a shared ammo `AttributeSet`. When a weapon is added to the player's inventory on the server, the weapon would add its `AttributeSet` to the player's `ASC::SpawnedAttributes`. The server would then replicate this down to the client. If the weapon is removed from the inventory, it would remove its `AttributeSet` from the `ASC::SpawnedAttributes`.

플레이어의 인벤토리에 추가할 때 플레이어의 'ASC'에 추가되는 항목에 별도의 'AttributeSet'을 사용하면 작동할 수 있지만 몇 가지 주요 제한 사항이 있습니다. 나는 무기 탄약을 위한 GASShooter의 초기 버전에서 이것을 작동시켰습니다. 무기는 최대 클립 크기, 클립의 현재 탄약, 예비 탄약 등과 같은 '속성'을 무기 클래스에 있는 'AttributeSet'에 저장합니다. 무기가 예비 탄약을 공유하는 경우 예비 탄약을 공유 탄약 'AttributeSet'의 캐릭터로 이동합니다. 무기가 서버의 플레이어 인벤토리에 추가되면 무기는 플레이어의 'ASC::SpawnedAttributes'에 'AttributeSet'을 추가합니다. 그런 다음 서버는 이를 클라이언트에 복제합니다. 무기가 인벤토리에서 제거되면 `ASC::SpawnedAttributes`에서 `AttributeSet`이 제거됩니다.


( 아이템이 [인벤토리에 들어갈 때 플레이어의 `ASC`에 추가](#concepts-as-design-addremoveruntime)할 수 있다면 별도의 `AttributeSet`을 사용하는것이 좋지만, 제한이 있다. 총알 부분에서 계속 이 작업을 했다. 무기에 있는 `AttributeSet`에 장탄량, 탄약 수 등을 `Attributes`로 저장했다. 무기가 예비 탄약을 공유할 경우 예비 탄약을 공유 탄약 '속성 세트'에 담아 캐릭터로 이동시킵니다. 무기가 서버의 플레이어 인벤토리에 추가 되면 무기는 플레이어의 {ASC ::SpawnedAttributes}에 {AttributeSet}을 추가합니다. 그런 다음 서버는 이를 클라이언트에 복제합니다. 무기가 인벤토리에서 제거되면 {ASC ::SpawnedAttributes}에서 {AttributeSet}을 제거합니다. )

When the `AttributeSet` lives on something other than the `OwnerActor` (say a weapon), you'll initially get some compilation errors in the `AttributeSet`. The fix is to construct the `AttributeSet` in `BeginPlay()` instead of in the constructor and to implement `IAbilitySystemInterface` (set the pointer to the `ASC` when you add the weapon to the player inventory) on the weapon.

`AttributeSet`이 `OwnerActor`(예: 무기)가 아닌 다른 항목에 있는 경우 처음에는 `AttributeSet`에 일부 컴파일 오류가 발생합니다. 수정 사항은 생성자 대신 'BeginPlay()'에서 'AttributeSet'을 구성하고 무기에 'IAbilitySystemInterface'를 구현하는 것입니다(무기를 플레이어 인벤토리에 추가할 때 포인터를 'ASC'로 설정).

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

You can see it in practice by checking out this [older version of GASShooter](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735).

Benefits:
1. Can use existing `GameplayAbility` and `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Simple to setup for a very small set of items

1. 기존의 'GameplayAbility' 및 'GameplayEffect' 워크플로를 사용할 수 있습니다(탄약 사용을 위한 '비용 GE' 등).
1. 매우 작은 항목 세트에 대한 설정이 간단합니다.


Limitations:
1. You have to make a new `AttributeSet` class for every weapon type. `ASCs` can only functionally have one `AttributeSet` instance of a class since changes to an `Attribute` look for the first instance of their `AttributeSet` class in the `ASCs` `SpawnedAttributes` array. Additional instances of the same `AttributeSet` class are ignored.
1. You can only have one of each type of weapon in the player's inventory due to previous reason of one `AttributeSet` instance per `AttributeSet` class.
1. Removing an `AttributeSet` is dangerous. In GASShooter if the player killed himself from a rocket, the player would immediately remove the rocket launcher from his inventory (including its `AttributeSet` from the `ASC`). When the server replicated that the rocket launcher's ammo `Attribute` changed, the `AttributeSet` no longer existed on the client's `ASC` and the game crashed.

1. 모든 무기 유형에 대해 새로운 'AttributeSet' 클래스를 만들어야 합니다. `ASCs`는 `Attribute`에 대한 변경이 `ASCs` `SpawnedAttributes` 배열에서 `AttributeSet` 클래스의 첫 번째 인스턴스를 찾기 때문에 기능적으로 클래스의 `AttributeSet` 인스턴스를 하나만 가질 수 있습니다. 동일한 `AttributeSet` 클래스의 추가 인스턴스는 무시됩니다.
1. 이전의 이유로 `AttributeSet` 클래스당 하나의 `AttributeSet` 인스턴스가 있었기 때문에 플레이어의 인벤토리에는 각 무기 유형 중 하나만 가질 수 있습니다.
1. 'AttributeSet'을 제거하는 것은 위험합니다. GASShooter에서 플레이어가 로켓에서 자살한 경우 플레이어는 인벤토리에서 로켓 발사기를 즉시 제거합니다('ASC'의 'AttributeSet' 포함). 서버가 로켓 발사기의 탄약 '속성'이 변경되었음을 복제했을 때 클라이언트의 'ASC'에 '속성 집합'이 더 이상 존재하지 않고 게임이 충돌했습니다.

1. 모든 무기 유형에 대해 새로운 'AttributeSet' 클래스를 만들어야 합니다. `ASCs`는 `Attribute`에 대한 변경이 `ASCs` `SpawnedAttributes` 배열에서 `AttributeSet` 클래스의 첫 번째 인스턴스를 찾기 때문에 기능적으로 클래스의 `AttributeSet` 인스턴스를 하나만 가질 수 있습니다. 동일한 `AttributeSet` 클래스의 추가 인스턴스는 무시됩니다.
1. 이전의 이유로 `AttributeSet` 클래스당 하나의 `AttributeSet` 인스턴스가 있었기 때문에 플레이어의 인벤토리에는 각 무기 유형 중 하나만 가질 수 있습니다.
1. 'AttributeSet'을 제거하는 것은 위험합니다. GASShooter에서 플레이어가 로켓에서 자살한 경우 플레이어는 인벤토리에서 로켓 발사기를 즉시 제거합니다('ASC'의 'AttributeSet' 포함). 서버가 로켓 발사기의 탄약 '속성'이 변경되었음을 복제했을 때 클라이언트의 'ASC'에 '속성 집합'이 더 이상 존재하지 않고 게임이 충돌했습니다.


<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 `ASC` on the Item
Putting a whole `AbilitySystemComponent` on each item is an extreme approach. I have not personally done this nor have I seen it in the wild. It would take a lot of engineering to make it work.

각 항목에 전체 'AbilitySystemComponent'를 넣는 것은 극단적인 접근 방식입니다. 나는 이것을 개인적으로 한 적이 없으며 야생에서 본 적도 없습니다. 그것을 작동시키려면 많은 엔지니어링이 필요할 것입니다.

> Is it viable to have several AbilitySystemComponents which have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with Owner set to PlayerState)?
> 
> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning actor. The former may be possible: just aggregate the tags from all all ASCs (but watch out -HasAllMatchingGameplayTags may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE -which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will multiple ASCs beneath them.
> 
> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags the describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (E.g, attributes and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.

> 소유자는 같지만 아바타가 다른 여러 능력 시스템 구성 요소를 가질 수 있습니까(예: 소유자가 PlayerState로 설정된 폰 및 무기/아이템/발사체)?
>
> 내가 보는 첫 번째 문제는 소유 액터에서 IGameplayTagAssetInterface 및 IAbilitySystemInterface를 구현하는 것입니다. 전자가 가능할 수 있습니다. 모든 ASC에서 태그를 집계하기만 하면 됩니다(그러나 -HasAllMatchingGameplayTags는 교차 ASC 집계를 통해서만 충족될 수 있습니다. 해당 호출을 각 ASC에 전달하고 결과를 함께 OR하는 것만으로는 충분하지 않습니다). 그러나 후자는 더 까다롭습니다. 어떤 ASC가 권위 있는 것입니까? 누군가가 GE를 신청하고 싶다면 어떤 사람이 받아야 할까요? 아마도 당신은 이것을 해결할 수 있지만 문제의 이 면은 가장 어려울 것입니다. 소유자는 그 아래에 여러 ASC를 가질 것입니다.
>
> 폰과 무기에 ASC를 분리하는 것은 그 자체로 의미가 있을 수 있습니다. 예를 들어, 무기를 설명하는 태그와 소유 폰을 설명하는 태그를 구별합니다. 아마도 무기에 부여된 태그가 소유자에게 "적용"되고 다른 어떤 것도 적용되지 않는다는 것이 합리적일 수 있습니다(예: 속성과 GE는 독립적이지만 소유자는 위에서 설명한 것처럼 소유 태그를 집계합니다). 이것은 효과가 있을 수 있다고 확신합니다. 그러나 동일한 소유자에게 여러 ASC를 보유하는 것은 위험할 수 있습니다.


*Dave Ratti from Epic's answer to [community questions #6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

Benefits:
1. Can use existing `GameplayAbility` and `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Can reuse `AttributeSet` classes (one on each weapon's ASC)

Limitations:
1. Unknown engineering cost
1. Is it even possible?

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 Defining Attributes
**`Attributes` can only be defined in C++** in the `AttributeSet's` header file. It is recommended to add this block of macros to the top of every `AttributeSet` header file. It will automatically generate getter and setter functions for your `Attributes`.

**`Attributes`는 `AttributeSet's` 헤더 파일의 C++** 에서만 정의할 수 있습니다. 이 매크로 블록을 모든 `AttributeSet` 헤더 파일의 맨 위에 추가하는 것이 좋습니다. '속성'에 대한 getter 및 setter 함수를 자동으로 생성합니다.

```c++
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

A replicated health attribute would be defined like this:

복제된 상태 속성은 다음과 같이 정의됩니다
```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

Also define the `OnRep` function in the header:
```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

The .cpp file for the `AttributeSet` should fill in the `OnRep` function with the `GAMEPLAYATTRIBUTE_REPNOTIFY` macro used by the prediction system:
```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

Finally, the `Attribute` needs to be added to `GetLifetimeReplicatedProps`:
```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always` tells the `OnRep` function to trigger if the local value is already equal to the value being repped down from the Server (due to prediction). By default it won't trigger the `OnRep` function if the local value is the same as the value being repped down from the Server.

If the `Attribute` is not replicated like a `Meta Attribute`, then the `OnRep` and `GetLifetimeReplicatedProps` steps can be skipped.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 4.4.4 Initializing Attributes
There are multiple ways to initialize `Attributes` (set their `BaseValue` and consequently their `CurrentValue` to some initial value). Epic recommends using an instant `GameplayEffect`. This is the method used in the Sample Project too.

'속성'을 초기화하는 방법에는 여러 가지가 있습니다(해당 'BaseValue'를 설정하고 결과적으로 'CurrentValue'를 초기 값으로 설정). Epic은 인스턴트 'GameplayEffect'를 사용할 것을 권장합니다. 이것은 샘플 프로젝트에서도 사용되는 방법입니다.

See `GE_HeroAttributes` Blueprint in the Sample Project for how to make an instant `GameplayEffect` to initialize `Attributes`. Application of this `GameplayEffect` happens in C++.

'속성'을 초기화하기 위해 인스턴트 'GameplayEffect'를 만드는 방법은 샘플 프로젝트의 'GE_HeroAttributes' 블루프린트를 참조하세요. 이 `GameplayEffect`의 적용은 C++에서 발생합니다.

If you used the `ATTRIBUTE_ACCESSORS` macro when you defined your `Attributes`, an initialization function will automatically be generated on the `AttributeSet` for each `Attribute` that you can call at your leisure in C++.

'속성'을 정의할 때 'ATTRIBUTE_ACCESSORS' 매크로를 사용한 경우 C++에서 여가 시간에 호출할 수 있는 각 '속성'에 대해 '속성 집합'에 초기화 함수가 자동으로 생성됩니다.

```c++
// InitHealth(float InitialValue) is an automatically generated function for an Attribute 'Health' defined with the `ATTRIBUTE_ACCESSORS` macro
AttributeSet->InitHealth(100.0f);
```

See `AttributeSet.h` for more ways to initialize `Attributes`.

**Note:** Prior to 4.24, `FAttributeSetInitterDiscreteLevels` did not work with `FGameplayAttributeData`. It was created when `Attributes` were raw floats and will complain about `FGameplayAttributeData` not being `Plain Old Data` (`POD`). This is fixed in 4.24 https://issues.unrealengine.com/issue/UE-76557.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()
`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)` is one of the main functions in the `AttributeSet` to respond to changes to an `Attribute's` `CurrentValue` before the change happens. It is the ideal place to clamp incoming changes to `CurrentValue` via the reference parameter `NewValue`.

`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)`는 변경이 일어나기 전에 `Attribute'의 `CurrentValue`에 대한 변경에 응답하기 위한 `AttributeSet`의 주요 기능 중 하나입니다. 참조 매개변수 'NewValue'를 통해 들어오는 변경 사항을 'CurrentValue'로 고정하는 이상적인 위치입니다.

For example to clamp movespeed modifiers the Sample Project does it like so:
예를 들어 샘플 프로젝트에서 movespeed 수정자를 고정하려면 다음과 같이 합니다.
```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// Cannot slow less than 150 units/s and cannot boost more than 1000 units/s
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```
The `GetMoveSpeedAttribute()` function is created by the macro block that we added to the `AttributeSet.h` ([Defining Attributes](#concepts-as-attributes)).

`GetMoveSpeedAttribute()` 함수는 `AttributeSet.h`(속성 정의하기)에 추가한 매크로 블록에 의해 생성됩니다.

This is triggered from any changes to `Attributes`, whether using `Attribute` setters (defined by the macro block in `AttributeSet.h` ([Defining Attributes](#concepts-as-attributes))) or using [`GameplayEffects`](#concepts-ge).

이것은 `AttributeSet.h`의 매크로 블록에 의해 정의된 `Attribute` setter로 트리거링

**Note:** Any clamping that happens here does not permanently change the modifier on the `ASC`. It only changes the value returned from querying the modifier. This means anything that recalculates the `CurrentValue` from all of the modifiers like [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) and [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) need to implement clamping again.

**참고:** 여기에서 발생하는 모든 클램핑은 'ASC'의 수정자를 영구적으로 변경하지 않습니다. 수정자를 쿼리하여 반환된 값만 변경합니다. 즉, `GameplayEffectExecutionCalculations`와 같은 모든 수정자에서 `CurrentValue`를 다시 계산하려면 클램핑을 다시 구현해야 합니다.

**Note:** Epic's comments for `PreAttributeChange()` say not to use it for gameplay events and instead use it mainly for clamping. The recommended place for gameplay events on `Attribute` change is `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)` ([Responding to Attribute Changes](#concepts-a-changes)).

**참고:** `PreAttributeChange()`에 대한 에픽의 설명에 따르면 게임플레이 이벤트에는 사용하지 말고 주로 클램핑에 사용합니다. '속성' 변경 시 게임플레이 이벤트에 권장되는 위치는 'UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)'(속성 변경에 응답합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()
`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)` only triggers after changes to the `BaseValue` of an `Attribute` from an instant [`GameplayEffect`](#concepts-ge). This is a valid place to do more `Attribute` manipulation when they change from a `GameplayEffect`.

`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)`는 인스턴트 `GameplayEffect`에서 `Attribute`의 `BaseValue`가 변경된 후에만 트리거됩니다. 이것은 `GameplayEffect`에서 변경될 때 더 많은 `Attribute` 조작을 수행할 수 있는 유효한 장소입니다.

For example, in the Sample Project we subtract the final damage `Meta Attribute` from the health `Attribute` here. If there was a shield `Attribute`, we would subtract the damage from it first before subtracting the remainder from health. The Sample Project also uses this location to apply hit react animations, show floating Damage Numbers, and assign experience and gold bounties to the killer. By design, the damage `Meta Attribute` will always come through an instant `GameplayEffect` and never the `Attribute` setter.

예를 들어 샘플 프로젝트에서는 여기에서 건강 '속성'에서 최종 데미지 '메타 속성'을 뺍니다. 방패 '속성'이 있는 경우 체력에서 나머지를 빼기 전에 먼저 피해를 뺍니다. 샘플 프로젝트는 또한 이 위치를 사용하여 적중 반응 애니메이션을 적용하고, 부동 피해 수치를 표시하고, 킬러에게 경험치와 골드 현상금을 할당합니다. 설계상 '메타 속성' 피해는 항상 '속성' 설정자가 아닌 즉각적인 'GameplayEffect'를 통해 발생합니다.

Other `Attributes` that will only have their `BaseValue` changed from instant `GameplayEffects` like mana and stamina can also be clamped to their maximum value counterpart `Attributes` here.

마나 및 스태미너와 같이 인스턴트 'GameplayEffects'에서 'BaseValue'만 변경되는 다른 '속성'도 여기에서 최대값에 해당하는 '속성'으로 고정할 수 있습니다.

**Note:** When `PostGameplayEffectExecute()` is called, changes to the `Attribute` have already happened, but they have not replicated back to clients yet so clamping values here will not cause two network updates to clients. Clients will only receive the update after clamping.

**참고:** `PostGameplayEffectExecute()`가 호출되면 `속성`에 대한 변경 사항이 이미 발생했지만 아직 클라이언트에 다시 복제되지 않았으므로 여기에서 값을 클램핑해도 클라이언트에 대한 두 개의 네트워크 업데이트가 발생하지 않습니다. 클라이언트는 클램핑 후에만 업데이트를 받습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()
`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)` triggers when an `Aggregator` is created for an `Attribute` in this set. It allows custom setup of [`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html). `AggregatorEvaluateMetaData` is used by the `Aggregator` in evaluating the `CurrentValue` of an `Attribute` based on all the [`Modifiers`](#concepts-ge-mods) applied to it. By default, `AggregatorEvaluateMetaData` is only used by the `Aggregator` to determine which `Modifiers` qualify with the example of `MostNegativeMod_AllPositiveMods` which allows all positive `Modifiers` but restricts negative `Modifiers` to only the most negative one. This was used by Paragon to only allow the most negative move speed slow effect to apply to a player regardless of how many slow effects where on them at any one time while applying all positive move speed buffs. `Modifiers` that don't qualify still exist on the `ASC`, they just aren't aggregated into the final `CurrentValue`. They can potentially qualify later once conditions change, like in the case if the most negative `Modifier` expires, the next most negative `Modifier` (if one exists) then qualifies.

'OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)'는 이 세트의 '속성'에 대해 '집계'가 생성될 때 트리거됩니다. 그것은 `FAggregatorEvaluateMetaData`의 사용자 정의 설정을 허용합니다. 'AggregatorEvaluateMetaData'는 모든 'Modifiers'를 기반으로 '속성'의 'CurrentValue'를 평가할 때 'Aggregator'가 사용합니다. 기본적으로 'AggregatorEvaluateMetaData'는 모든 양의 'Modifier'를 허용하지만 부정적인 'Modifier'를 가장 부정적인 것으로 제한하는 'Most NegativeMod_All PositiveMods'의 예제에 적합한 'Modifier'를 결정하기 위해 'Agregator'에서만 사용된다. 이것은 파라곤이 모든 양의 이동 속도 버프를 적용하면서 한 번에 얼마나 많은 느린 효과를 주는지에 상관없이 가장 부정적인 이동 속도 느린 효과만 플레이어에게 적용하도록 하기 위해 사용되었습니다. ASC에는 아직 수식어가 존재하지만 최종 Current Value에는 집계되지 않는다. 조건이 바뀌면 나중에 자격을 얻을 수 있다. 예를 들어 가장 부정적인 '수정자'가 만료되면 그 다음으로 가장 부정적인 '수정자'가 자격을 얻는다.

To use AggregatorEvaluateMetaData in the example of only allowing the most negative `Modifier` and all positive `Modifiers`:
가장 부정적인 'Modifier'와 모든 긍정적인 'Modifiers'만 허용하는 예에서 AggregatorEvaluateMetaData를 사용하려면:
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

Your custom `AggregatorEvaluateMetaData` for qualifiers should be added to `FAggregatorEvaluateMetaDataLibrary` as static variables.
한정자에 대한 사용자 지정 `AggregatorEvaluateMetaData`는 정적 변수로 `FAggregatorEvaluateMetaDataLibrary`에 추가되어야 합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 Gameplay Effects

<a name="concepts-ge-definition"></a>
#### 4.5.1 Gameplay Effect Definition
[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html) (`GE`) are the vessels through which abilities change [`Attributes`](#concepts-a) and [`GameplayTags`](#concepts-gt) on themselves and others. They can cause immediate `Attribute` changes like damage or healing or apply long term status buff/debuffs like a movespeed boost or stunning. The `UGameplayEffect` class is a meant to be a **data-only** class that defines a single gameplay effect. No additional logic should be added to `GameplayEffects`. Typically designers will create many Blueprint child classes of `UGameplayEffect`.

`GameplayEffects`(`GE`)는 능력이 자신과 다른 사람의 `속성`을 변경하는 혈관입니다. 그들은 피해 또는 치유와 같은 즉각적인 '속성' 변경을 일으키거나 이동 속도 부스트 또는 기절과 같은 장기적인 상태 버프/디버프를 적용할 수 있습니다. 'UGameplayEffect' 클래스는 단일 게임플레이 효과를 정의하는 **데이터 전용** 클래스입니다. 'GameplayEffects'에 추가 로직을 추가해서는 안 됩니다. 일반적으로 디자이너는 'UGameplayEffect'의 블루프린트 자식 클래스를 많이 만듭니다.

`GameplayEffects` change `Attributes` through [`Modifiers`](#concepts-ge-mods) and [`Executions` (`GameplayEffectExecutionCalculation`)](#concepts-ge-ec).

'GameplayEffects'는 'Modifiers'를 통해 'Attributes'를 변경합니다.

`GameplayEffects` have three types of duration: `Instant`, `Duration`, and `Infinite`.

'GameplayEffects'에는 'Instant', 'Duration' 및 'Infinite'의 세 가지 지속 시간 유형이 있습니다.

Additionally, `GameplayEffects` can add/execute [`GameplayCues`](#concepts-gc). An `Instant` `GameplayEffect` will call `Execute` on the `GameplayCue` `GameplayTags` whereas a `Duration` or `Infinite` `GameplayEffect` will call `Add` and `Remove` on the `GameplayCue` `GameplayTags`.

추가적으로, `GameplayEffects`는 `GameplayCues`를 추가/실행할 수 있습니다. 'Instant' 'GameplayEffect'는 'GameplayCue', 'GameplayTags'에서 'Execute'를 호출하는 반면, 'Duration' 또는 'Infinite' 'GameplayEffect'는 'GameplayCue' 'GameplayTags'에서 'Add' 및 'Remove'를 호출합니다.

| Duration Type | GameplayCue Event | When to use                                                                                                                                                                                                                                |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Instant`     | Execute           | '속성'의 'BaseValue'에 대한 즉각적인 영구 변경의 경우. 'GameplayTags'는 프레임에도 적용되지 않습니다.                                                                                                                    |
| `Duration`    | Add & Remove      | `속성`의 `CurrentValue`에 대한 임시 변경 및 `GameplayEffect`가 만료되거나 수동으로 제거될 때 제거될 `GameplayTags`를 적용하기 위한 것입니다. 지속 시간은 `UGameplayEffect` 클래스/블루프린트에 지정됩니다.       |
| `Infinite`    | Add & Remove      | `속성`의 `CurrentValue`를 일시적으로 변경하고 `GameplayEffect`가 제거될 때 제거될 `GameplayTags`를 적용합니다. 이것들은 저절로 만료되지 않으며 능력이나 'ASC'에 의해 수동으로 제거되어야 합니다.  |

`Duration` and `Infinite` `GameplayEffects` have the option of applying `Periodic Effects` that apply its `Modifiers` and `Executions` every `X` seconds as defined by its `Period`. `Periodic Effects` are treated as `Instant` `GameplayEffects` when it comes to changing the `Attribute's` `BaseValue` and `Executing` `GameplayCues`. These are useful for damage over time (DOT) type effects. **Note:** `Periodic Effects` cannot be [predicted](#concepts-p).

`Duration` 및 `Infinite` `GameplayEffects`에는 `Period`에 정의된 대로 `X`초마다 `Modifiers` 및 `Executions`를 적용하는 `Periodic Effects`를 적용하는 옵션이 있습니다. `Periodic Effects`는 `Attribute'의 `BaseValue` 및 `Executing` `GameplayCues`를 변경할 때 `Instant` `GameplayEffects`로 처리됩니다. 이는 DOT(시간 경과에 따른 손상) 유형 효과에 유용합니다. **참고:** '주기적 영향'은 예측할 수 없습니다.

`Duration` and `Infinite` `GameplayEffects` can be temporarily turned off and on after application if their `Ongoing Tag Requirements` are not met/met ([Gameplay Effect Tags](#concepts-ge-tags)). Turning off a `GameplayEffect` removes the effects of its `Modifiers` and applied `GameplayTags` but does not remove the `GameplayEffect`. Turning the `GameplayEffect` back on reapplies its `Modifiers` and `GameplayTags`.

'Duration' 및 'Infinite' 'GameplayEffects'는 '진행 중인 태그 요구 사항'이 충족/충족되지 않는 경우 적용 후 일시적으로 껐다가 켤 수 있습니다(게임 플레이 효과 태그. 'GameplayEffect'를 끄면 'Modifiers'의 효과가 제거됩니다. 'GameplayTags'를 적용했지만 'GameplayEffect'는 제거하지 않았습니다. 'GameplayEffect'를 다시 켜면 'Modifiers'와 'GameplayTags'가 다시 적용됩니다.

If you need to manually recalculate the `Modifiers` of a `Duration` or `Infinite` `GameplayEffect` (say you have an `MMC` that uses data that doesn't come from `Attributes`), you can call `UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)` with the same level that it already has using `UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()`. `Modifiers` that are based on backing `Attributes` automatically update when those backing `Attributes` update. The key functions of `SetActiveGameplayEffectLevel()` to update the `Modifiers` are:

'Duration' 또는 'Infinite' 'GameplayEffect'의 'Modifiers'를 수동으로 다시 계산해야 하는 경우('Attributes'에서 가져오지 않은 데이터를 사용하는 'MMC'가 있다고 가정해 봅시다), 'UAbilitySystemComponent'를 호출할 수 있습니다. :ActiveGameplayEffects.SetActiveGameplayEffectLevel(FACiveGameplayEffectHandle ActiveHandle, int32 NewLevel)`은 이미 `UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()`을 사용하고 있는 것과 동일한 수준입니다. '속성'을 지원하는 '수정자'는 '속성'을 지원하는 '수정자'가 업데이트될 때 자동으로 업데이트됩니다. `Modifiers`를 업데이트하기 위한 `SetActiveGameplayEffectLevel()`의 주요 기능은 다음과 같습니다.

```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// Private function otherwise we'd call these three functions without needing to set the level to what it already is
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffects` are not typically instantiated. When an ability or `ASC` wants to apply a `GameplayEffect`, it creates a [`GameplayEffectSpec`](#concepts-ge-spec) from the `GameplayEffect's` `ClassDefaultObject`. Successfully applied `GameplayEffectSpecs` are then added to a new struct called `FActiveGameplayEffect` which is what the `ASC` keeps track of in a special container struct called `ActiveGameplayEffects`.

'GameplayEffects'는 일반적으로 인스턴스화되지 않습니다. 능력 또는 `ASC`가 `GameplayEffect`를 적용하려는 경우 `GameplayEffect`의 `ClassDefaultObject`에서 `GameplayEffectSpec`을 생성합니다. 성공적으로 적용된 `GameplayEffectSpecs`는 `FACiveGameplayEffect`라는 새 구조체에 추가되며, 이는 `ASC`가 `ActiveGameplayEffects`라는 특수 컨테이너 구조체에서 추적하는 것입니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-applying"></a>
#### 4.5.2 Applying Gameplay Effects
`GameplayEffects` can be applied in many ways from functions on [`GameplayAbilities`](#concepts-ga) and functions on the `ASC` and usually take the form of `ApplyGameplayEffectTo`. The different functions are essentially convenience functions that will eventually call `UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()` on the `Target`.
`GameplayEffects`는 `Target`의 `GameplayAbilities``에 있는 함수에서 여러 가지 방법으로 적용할 수 있습니다.

To apply `GameplayEffects` outside of a `GameplayAbility` for example from a projectile, you need to get the `Target's` `ASC` and use one of its functions to `ApplyGameplayEffectToSelf`.

예를 들어 발사체에서 'GameplayAbility' 외부에 'GameplayEffects'를 적용하려면 '대상'의 'ASC'를 가져와서 해당 기능 중 하나를 'ApplyGameplayEffectToSelf'에 사용해야 합니다.

You can listen for when any `Duration` or `Infinite` `GameplayEffects` are applied to an `ASC` by binding to its delegate:

델리게이트에 바인딩하여 'Duration' 또는 'Infinite' 'GameplayEffects'가 'ASC'에 적용될 때 수신할 수 있습니다.
```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
The callback function:
```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

The server will always call this function regardless of replication mode. The autonomous proxy will only call this for replicated `GameplayEffects` in `Full` and `Mixed` replication modes. Simulated proxies will only call this in `Full` [replication mode](#concepts-asc-rm).

서버는 복제 모드에 관계없이 항상 이 함수를 호출합니다. 자율 프록시는 '전체' 및 '혼합' 복제 모드에서 복제된 'GameplayEffects'에 대해서만 이것을 호출합니다. 시뮬레이션된 프록시는 '전체' 복제 모드에서만 이것을 호출합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-removing"></a>
#### 4.5.3 Removing Gameplay Effects
`GameplayEffects` can be removed in many ways from functions on [`GameplayAbilities`](#concepts-ga) and functions on the `ASC` and usually take the form of `RemoveActiveGameplayEffect`. The different functions are essentially convenience functions that will eventually call `FActiveGameplayEffectsContainer::RemoveActiveEffects()` on the `Target`.

`GameplayEffects`는 `Target`의 `GameplayAbilities`에 있는 기능에서 여러 가지 방법으로 제거할 수 있습니다.`GameplayAbility` 외부에서 `GameplayEffects`를 제거하려면 `Target`의 `ASC`를 가져와서 해당 기능 중 하나를 `RemoveActiveGameplayEffect`에 사용해야 합니다.

To remove `GameplayEffects` outside of a `GameplayAbility`, you need to get the `Target's` `ASC` and use one of its functions to `RemoveActiveGameplayEffect`.

`GameplayAbility` 외부에서 `GameplayEffects`를 제거하려면 `Target'의 `ASC`를 가져와서 해당 기능 중 하나를 `RemoveActiveGameplayEffect`에 사용해야 합니다.

You can listen for when any `Duration` or `Infinite` `GameplayEffects` are removed from an `ASC` by binding to its delegate:
델리게이트에 바인딩하여 'ASC'에서 'Duration' 또는 'Infinite' 'GameplayEffects'가 제거될 때 수신할 수 있습니다.

```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```
The callback function:
```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

The server will always call this function regardless of replication mode. The autonomous proxy will only call this for replicated `GameplayEffects` in `Full` and `Mixed` replication modes. Simulated proxies will only call this in `Full` [replication mode](#concepts-asc-rm).

서버는 복제 모드에 관계없이 항상 이 함수를 호출합니다. 자율 프록시는 '전체' 및 '혼합' 복제 모드에서 복제된 'GameplayEffects'에 대해서만 이것을 호출합니다. 시뮬레이션된 프록시는 '전체' 복제 모드에서만 이것을 호출합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 4.5.4 Gameplay Effect Modifiers
`Modifiers` change an `Attribute` and are the only way to [predictively](#concepts-p) change an `Attribute`. A `GameplayEffect` can have zero or many `Modifiers`. Each `Modifier` is responsible for changing only one `Attribute` via a specified operation.

'수정자'는 '속성'을 변경하며 '속성'을 예측적으로 변경하는 유일한 방법입니다. 'GameplayEffect'는 0개 또는 많은 'Modifiers'를 가질 수 있습니다. 각 `Modifier`는 지정된 작업을 통해 하나의 `Attribute`만 변경할 책임이 있습니다.

| Operation  | Description                                                                                                         |
| ---------- | ------------------------------------------------------------------------------------------------------------------- |
| `Add`      | Adds the result to the `Modifier's` specified `Attribute`. Use a negative value for subtraction.                    |
| `Multiply` | Multiplies the result to the `Modifier's` specified `Attribute`.                                                    |
| `Divide`   | Divides the result against the `Modifier's` specified `Attribute`.                                                  |
| `Override` | Overrides the `Modifier's` specified `Attribute` with the result.                                                   |

`Attribute`의 `CurrentValue`는 `BaseValue`에 추가된 모든 `Modifiers`의 집계 결과입니다. 'Modifiers'가 집계되는 방식에 대한 공식은 'GameplayEffectAggregator.cpp'의 'FAggregatorModChannel::EvaluateWithBase'에 다음과 같이 정의되어 있습니다.:
```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

Any `Override` `Modifiers` will override the final value with the last applied `Modifier` taking precedence.

모든 '재정의' '수정자'는 마지막으로 적용된 '수정자'가 우선적으로 적용되는 최종 값을 재정의합니다.

**Note:** For percentage based changes, make sure to use the `Multiply` operation so that it happens after addition.

**Note:** [Prediction](#concepts-p) has trouble with percentage changes.

**참고:** 백분율 기반 변경의 경우 '곱하기' 작업을 사용하여 더하기 후에 발생하도록 하세요.

**참고:** 예측은 백분율 변경에 문제가 있습니다.


There are four types of `Modifiers`: Scalable Float, Attribute Based, Custom Calculation Class, and Set By Caller. They all generate some float value that is then used to change the specified `Attribute` of the `Modifier` based on its operation.

'수정자'에는 확장 가능한 부동 소수점, 속성 기반, 사용자 정의 계산 클래스 및 호출자 설정의 네 가지 유형이 있습니다. 그것들은 모두 연산에 따라 `Modifier`의 지정된 `Attribute`를 변경하는 데 사용되는 부동 소수점 값을 생성합니다.

| `Modifier` Type            | Description                                                                                                                                                                                                                                             |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scalable Float`           | `FScalableFloats` are a structure that can point to a Data Table that has the variables as rows and levels as columns. The Scalable Floats will automatically read the value of the specified table row at the ability's current level (or different level if overriden on the [`GameplayEffectSpec`](#concepts-ge-spec)). This value can further be manipulated by a coefficient. If no Data Table/Row is specified, it treats the value as a 1 so the coefficient can be used to hard code in a single value at all levels. ![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png) |                                                                                                                                                                                                                                                                                                                                                                                                                                                      
| `Attribute Based`          | `Attribute Based` `Modifiers` take the `CurrentValue` or `BaseValue` of a backing `Attribute` on the `Source` (who created the `GameplayEffectSpec`) or `Target` (who received the `GameplayEffectSpec`) and further modifies it with a coefficient and pre and post coefficient additions. `Snapshotting` means the backing `Attribute` is captured when the `GameplayEffectSpec` is created whereas no snapshotting means the `Attribute` is captured when the `GameplayEffectSpec` is applied. |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
| `Custom Calculation Class` | `Custom Calculation Class` provides the most flexibility for complex `Modifiers`. This `Modifier` takes a [`ModifierMagnitudeCalculation`](#concepts-ge-mmc) class and can further manipulate the resulting float value with a coefficient and pre and post coefficient additions.  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  
| `Set By Caller`            | `SetByCaller` `Modifiers` are values that are set outside of the `GameplayEffect` at runtime by the ability or whoever made the `GameplayEffectSpec` on the `GameplayEffectSpec`. For example, you would use a `SetByCaller` if you want to set the damage to be based on how long the player held down a button to charge the ability. `SetByCallers` are essentially `TMap<FGameplayTag, float>` that live on the `GameplayEffectSpec`. The `Modifier` is just telling the `Aggregator` to look for a `SetByCaller` value associated with the supplied `GameplayTag`. The `SetByCallers` used by `Modifiers` can only use the `GameplayTag` version of the concept. The `FName` version is disabled here. If the `Modifier` is set to `SetByCaller` but a `SetByCaller` with the correct `GameplayTag` does not exist on the `GameplayEffectSpec`, the game will throw a runtime error and return a value of 0. This might cause issues in the case of a `Divide` operation. See [`SetByCallers`](#concepts-ge-spec-setbycaller) for more information on how to use `SetByCallers`. |



| `수정자` 유형 | 설명 | 
| -------------------------- |------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `확장 가능한 플로트` | 'FScalableFloats'는 변수를 행으로, 수준을 열로 하는 데이터 테이블을 가리킬 수 있는 구조입니다. Scalable Floats는 능력의 현재 레벨에서 지정된 테이블 행의 값을 자동으로 읽습니다. 값을 1로 처리하므로 계수를 사용하여 모든 수준에서 단일 값으로 하드 코딩할 수 있습니다.
| `속성 기반` | '속성 기반' '수정자'는 '소스'('GameplayEffectSpec'을 생성한 사람) 또는 '대상'('GameplayEffectSpec'을 수신한 사람)에 대한 지원 '속성'의 'CurrentValue' 또는 'BaseValue'를 가져와서 추가로 수정합니다. 계수와 사전 및 사후 계수 추가가 있습니다. '스냅샷'은 'GameplayEffectSpec'이 생성될 때 지원되는 '속성'이 캡처됨을 의미하는 반면 스냅샷이 없는 것은 'GameplayEffectSpec'이 적용될 때 '속성'이 캡처됨을 의미합니다. |
| `사용자 정의 계산 클래스` | `Custom Calculation Class`는 복잡한 `Modifiers`에 가장 유연성을 제공합니다. 이 'Modifier'는 'ModifierMagnitudeCalculation' 클래스를 사용하며 계수 및 사전 및 사후 계수 추가로 결과 부동 소수점 값을 추가로 조작할 수 있습니다. |
| `발신자 설정` | 'SetByCaller' 'Modifiers'는 런타임 시 'GameplayEffect' 외부에 설정되는 값입니다. 능력이나 'GameplayEffectSpec'에서 'GameplayEffectSpec'을 만든 사람이 누구든지 설정합니다. 예를 들어 플레이어가 능력을 충전하기 위해 버튼을 누르고 있는 시간을 기반으로 피해를 설정하려면 'SetByCaller'를 사용합니다. `SetByCallers`는 본질적으로 `GameplayEffectSpec`에 있는 `TMap<FGameplayTag, float>`입니다. 'Modifier'는 제공된 'GameplayTag'와 연결된 'SetByCaller' 값을 찾도록 'Aggregator'에 지시합니다. 'Modifiers'가 사용하는 'SetByCallers'는 개념의 'GameplayTag' 버전만 사용할 수 있습니다. 'FName' 버전은 여기에서 비활성화됩니다. 'Modifier'가 'SetByCaller'로 설정되어 있지만 'GameplayTag'가 올바른 'SetByCaller'가 'GameplayEffectSpec'에 존재하지 않는 경우, 게임은 런타임 오류를 발생시키고 값 0을 반환합니다. 이는 '나누기' 작업의 경우 문제를 일으킬 수 있습니다. 'SetByCallers' 사용 방법에 대한 자세한 내용은 'SetByCallers'를 참조하세요. |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>
##### 4.5.4.1 Multiply and Divide Modifiers
By default, all `Multiply` and `Divide` `Modifiers` are added together before multiplying or dividing them into the `Attribute`'s `BaseValue`.
기본적으로 모든 `Multiply` 및 `Divide` `Modifiers`는 `Attribute`의 `BaseValue`로 곱하거나 나누기 전에 함께 추가됩니다.
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
*from `GameplayEffectAggregator.cpp`*

Both `Multiply` and `Divide` `Modifiers` have a `Bias` value of `1` in this formula (`Addition` has a `Bias` of `0`). So it would look something like:
'곱하기'와 '나누기' '수정자' 모두 이 공식에서 '편향' 값이 '1'입니다('더하기'의 '편향'은 '0'임). 따라서 다음과 같이 보일 것입니다.
```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

This formula leads to some unexpected results. Firstly, this formula adds all the modifiers together before multiplying or dividing them into the `BaseValue`. Most people would expect it to multiply or divide them together. For example, if you have two `Multiply` modifiers of `1.5`, most people would expect the `BaseValue` to be multiplied by `1.5 x 1.5 = 2.25`. Instead, this adds the `1.5`s together to multiply the `BaseValue` by `2` (`50% increase + another 50% increase = 100% increase`). This was for the example from `GameplayPrediction.h` of a `10%` speed buff on `500` base speed would be `550`. Add another `10%` speed buff and it will be `600`.

이 공식은 예상치 못한 결과를 초래합니다. 첫째, 이 공식은 'BaseValue'로 곱하거나 나누기 전에 모든 수정자를 함께 추가합니다. 대부분의 사람들은 그것을 함께 곱하거나 나눌 것으로 예상합니다. 예를 들어 `1.5`의 `곱하기` 수정자가 두 개 있는 경우 대부분의 사람들은 `BaseValue`에 `1.5 x 1.5 = 2.25`를 곱할 것으로 예상합니다. 대신 `1.5`를 더하여 `BaseValue`에 `2`를 곱합니다(`50% 증가 + 또 다른 50% 증가 = 100% 증가`). 이것은 `500` 기본 속도에 대한 `10%` 속도 버프의 `GameplayPrediction.h`에서 `550`의 예를 위한 것입니다. 다른 '10%' 속도 버프를 추가하면 '600'이 됩니다.

Secondly, this formula has some undocumented rules about what values can be used as it was designed with Paragon in mind.

둘째, 이 공식에는 Paragon을 염두에 두고 설계된 대로 사용할 수 있는 값에 대한 문서화되지 않은 규칙이 있습니다.

Rules for `Multiply` and `Divide` multiplication addition formula:
* `(No more than one value < 1) AND (Any number of values [1, 2))`
* `OR (One value >= 2)`

곱하기' 및 '나누기' 곱셈 덧셈 공식에 대한 규칙:
* `(하나 이상의 값 < 1) AND (값 1, 2))`
* `또는 (하나의 값 >= 2)`

The `Bias` in the formula basically subtracts out the integer digit of numbers in the range `[1, 2)`. The first `Modifier`'s `Bias` subtracts out from the starting `Sum` value (set to the `Bias` before the loop) which is why any value by itself works and why one value `< 1` will work with the numbers in the range `[1, 2)`.

수식의 '바이어스'는 기본적으로 '1, 2)' 범위의 정수 숫자를 뺍니다. 첫 번째 `Modifier`의 `Bias`는 시작 `Sum` 값(루프 전에 `Bias`로 설정됨)에서 빼므로 값 자체가 작동하는 이유와 하나의 값 `< 1`이 '1, 2)' 범위의 숫자.

Some examples with `Multiply`:  
Multipliers: `0.5`  
`1 + (0.5 - 1) = 0.5`, correct

Multipliers: `0.5, 0.5`  
`1 + (0.5 - 1) + (0.5 - 1) = 0`, incorrect expected `1`? Multiple values less than `1` don't make sense for adding multipliers. Paragon was designed to only use the [greatest negative value for `Multiply` `Modifiers`](#cae-nonstackingge) so there would only ever be at most one value less than `1` multiplying into the `BaseValue`.

`1 + (0.5 - 1) + (0.5 - 1) = 0`, 예상된 `1`이 잘못되었습니까? '1'보다 작은 여러 값은 승수를 추가하는 데 적합하지 않습니다. Paragon은 'Multiply' 'Modifiers'에 가장 큰 음수 값만 사용하도록 설계되었으므로 'BaseValue'에 곱하는 '1'보다 작은 값은 최대 하나뿐입니다.

Multipliers: `1.1, 0.5`  
`1 + (0.5 - 1) + (1.1 - 1) = 0.6`, correct

Multipliers: `5, 5`  
`1 + (5 - 1) + (5 - 1) = 9`, incorrect expected `10`. Will always be the `sum of the Modifiers - number of Modifiers + 1`.

Many games will want their `Multiply` and `Divide` `Modifiers` to multiply and divide together before applying to the `BaseValue`. To achieve this, you will need to **change the engine code** for `FAggregatorModChannel::EvaluateWithBase()`.

많은 게임은 `BaseValue`에 적용하기 전에 `Multiply` 및 `Divide` `Modifiers`가 함께 곱하고 나누기를 원할 것입니다. 이를 위해서는 `FAggregatorModChannel::EvaluateWithBase()`에 대한 **엔진 코드를 변경**해야 합니다.

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
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

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>
##### 4.5.4.2 Gameplay Tags on Modifiers

`SourceTags` and `TargetTags` can be set for each [Modifier](#concepts-ge-mods). They work the same like the [`Application Tag requirements`](#concepts-ge-tags) of a `GameplayEffect`. So the tags are considered only when the effect is applied. I.e. when having a periodic, infinite effect, they are only taken into consideration on the first application of the effect but *not* on each periodic execution.

`SourceTags` 및 `TargetTags`는 `GameplayEffect`의 각 Modifier에 대해 설정할 수 있습니다. 따라서 태그는 효과가 적용된 경우에만 고려됩니다. 즉, 주기적이고 무한한 효과가 있는 경우 효과를 처음 적용할 때만 고려되지만 각 주기적 실행에서는 고려되지 *않습니다*.

`Attribute Based` Modifiers can also set `SourceTagFilter` and `TargetTagFilter`. When determining the magnitude of the attribute which is the source of the `Attribute Based` Modifier, these filters are used to exclude certain Modifiers to that attribute. Modifiers which source or target didn't have all of the tags of the filter are excluded.

'속성 기반' 수정자는 'SourceTagFilter' 및 'TargetTagFilter'도 설정할 수 있습니다. '속성 기반' 수정자의 소스인 속성의 크기를 결정할 때 이러한 필터는 해당 속성에 대한 특정 수정자를 제외하는 데 사용됩니다. 소스 또는 대상에 필터의 일부 태그가 없는 수정자는 제외됩니다.

This means in detail: The tags of the source ASC and the target ASC are captured by `GameplayEffects`. The source ASC tags are captured, when the `GameplayEffectSpec` is created, the target ASC tags are captured on execution of the effect. When determining, if a Modifier of an infinite or duration effect "qualifies" to be applied (i.e. its Aggregator qualifies) and those filters are set, the captured tags are compared against the filters.

즉, 소스 ASC와 대상 ASC의 태그가 `GameplayEffects`에 의해 캡처됩니다. 소스 ASC 태그가 캡처되고, `GameplayEffectSpec`이 생성되면 효과 실행 시 대상 ASC 태그가 캡처됩니다. 결정할 때 무한 또는 지속 시간 효과의 수정자가 적용할 "자격"이 있고(즉, 집계자가 자격이 있음) 해당 필터가 설정되어 있으면 캡처된 태그가 필터와 비교됩니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-stacking"></a>
#### 4.5.5 Stacking Gameplay Effects
`GameplayEffects` by default will apply new instances of the `GameplayEffectSpec` that don't know or care about previously existing instances of the `GameplayEffectSpec` on application. `GameplayEffects` can be set to stack where instead of a new instance of the `GameplayEffectSpec` is added, the currently existing `GameplayEffectSpec's` stack count is changed. Stacking only works for `Duration` and `Infinite` `GameplayEffects`.

기본적으로 `GameplayEffects`는 애플리케이션에 있는 `GameplayEffectSpec`의 기존 인스턴스를 알지 못하거나 신경 쓰지 않는 `GameplayEffectSpec`의 새 인스턴스를 적용합니다. 'GameplayEffects'는 'GameplayEffectSpec'의 새 인스턴스가 추가되는 대신 현재 존재하는 'GameplayEffectSpec'의 스택 수가 변경되는 스택으로 설정할 수 있습니다. 스태킹은 'Duration' 및 'Infinite' 'GameplayEffects'에서만 작동합니다.

There are two types of stacking: Aggregate by Source and Aggregate by Target.

스태킹에는 소스별 집계 및 대상별 집계의 두 가지 유형이 있습니다.

| Stacking Type       | Description                                                                                                                          
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ 
| Aggregate by Source | There is a separate instance of stacks per Source `ASC` on the Target. Each Source can apply X amount of stacks.                     |
| Aggregate by Target | There is only one instance of stacks on the Target regardless of Source. Each Source can apply a stack up to the shared stack limit. |

| 스태킹 유형 | 설명 |
-------------------| -------------------------------------------------- -------------------------------------------------- -------------------------------- 
| 소스별 집계 | 대상의 소스 'ASC'마다 별도의 스택 인스턴스가 있습니다. 각 소스는 X만큼의 스택을 적용할 수 있습니다. |
| 대상별 집계 | 소스에 관계없이 대상에는 스택 인스턴스가 하나만 있습니다. 각 소스는 공유 스택 제한까지 스택을 적용할 수 있습니다. |


Stacks also have policies for expiration, duration refresh, and period reset. They have helpful hover tooltips in the `GameplayEffect` Blueprint.

스택에는 만료, 기간 새로 고침 및 기간 재설정에 대한 정책도 있습니다. 'GameplayEffect' 블루프린트에 유용한 호버 툴팁이 있습니다.

The Sample Project includes a custom Blueprint node that listens for `GameplayEffect` stack changes. The HUD UMG Widget uses it to update the amount of passive armor stacks that the player has. This `AsyncTask` will live forever until manually called `EndTask()`, which we do in the UMG Widget's `Destruct` event. See `AsyncTaskEffectStackChanged.h/cpp`.

샘플 프로젝트에는 'GameplayEffect' 스택 변경을 수신 대기하는 커스텀 블루프린트 노드가 포함되어 있습니다. HUD UMG 위젯은 이를 사용하여 플레이어가 가지고 있는 패시브 아머 스택의 양을 업데이트합니다. 이 'AsyncTask'는 UMG 위젯의 'Destruct' 이벤트에서 수동으로 'EndTask()'를 호출할 때까지 영원히 지속됩니다. 'AsyncTaskEffectStackChanged.h/cpp'를 참조하세요.

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 4.5.6 Granted Abilities
`GameplayEffects` can grant new [`GameplayAbilities`](#concepts-ga) to `ASCs`. Only `Duration` and `Infinite` `GameplayEffects` can grant abilities.

'GameplayEffects'는 'ASC'에 새로운 'GameplayAbilities'를 부여할 수 있습니다. 'Duration' 및 'Infinite' 'GameplayEffects'만 능력을 부여할 수 있습니다.

A common usecase for this is when you want to force another player to do something like moving them from a knockback or pull. You would apply a `GameplayEffect` to them that grants them an automatically activating ability (see [Passive Abilities](#concepts-ga-activating-passive) for how to automatically activate an ability when it is granted) that does the desired action to them.

이에 대한 일반적인 사용 사례는 다른 플레이어가 밀쳐내거나 당기는 것과 같은 작업을 강제로 수행하려는 경우입니다. 자동 활성화 능력을 부여하는 'GameplayEffect'를 적용할 수 있습니다(원하는 동작을 수행하는 수동 능력 참조).

Designers can choose which abilities a `GameplayEffect` grants, what level to grant them at, what [input to bind](#concepts-ga-input) them at and the removal policy for the granted ability.

디자이너는 'GameplayEffect'가 부여하는 능력, 부여할 레벨, 바인딩할 입력 및 부여된 능력에 대한 제거 정책을 선택할 수 있습니다.

| Removal Policy             | Description                                                                                                                                                                     |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cancel Ability Immediately | The granted ability is canceled and removed immediately when the `GameplayEffect` that granted it is removed from the Target.                                                   |
| Remove Ability on End      | The granted ability is allowed to finish and then is removed from the Target.                                                                                                   |
| Do Nothing                 | The granted ability is not affected by the removal of the granting `GameplayEffect` from the Target. The Target has the ability permanently until it is manually removed later. |

| 제거 정책 | 설명 |
 -------------------------- | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------- 
| 즉시 능력 취소 | 부여된 능력은 부여된 'GameplayEffect'가 대상에서 제거되면 즉시 취소되고 제거됩니다. |
| 종료 시 능력 제거 | 부여된 능력은 완료될 수 있으며 대상에서 제거됩니다. |
| 아무것도 하지 마세요 | 부여된 능력은 대상에서 부여한 'GameplayEffect'를 제거해도 영향을 받지 않습니다. 대상은 나중에 수동으로 제거될 때까지 영구적으로 능력을 가집니다. |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 
Gameplay Effect Tags
`GameplayEffects` carry multiple [`GameplayTagContainers`](#concepts-gt). Designers will edit the `Added` and `Removed` `GameplayTagContainers` for each category and the result will show up in the `Combined` `GameplayTagContainer` on compilation. `Added` tags are new tags that this `GameplayEffect` adds that its parents did not previously have. `Removed` tags are tags that parent classes have but this subclass does not have.

`GameplayEffects`는 여러 `GameplayTagContainers`를 전달합니다. 디자이너는 각 카테고리에 대해 `Added` 및 `Removed` `GameplayTagContainers`를 편집하고 결과는 컴파일 시 `Combined` `GameplayTagContainer`에 표시됩니다. 'Added' 태그는 이 'GameplayEffect'가 부모에게 이전에 없었던 새로운 태그입니다. '제거됨' 태그는 상위 클래스에는 있지만 이 하위 클래스에는 없는 태그입니다.

| Category                          | Description                                                                                                                                                                                                                                                                                                                                                                        |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Gameplay Effect Asset Tags        | Tags that the `GameplayEffect` has. They do not do any function on their own and serve only the purpose of describing the `GameplayEffect`.                                                                                                                                                                                                                                        |
| Granted Tags                      | Tags that live on the `GameplayEffect` but are also given to the `ASC` that the `GameplayEffect` is applied to. They are removed from the `ASC` when the `GameplayEffect` is removed. This only works for `Duration` and `Infinite` `GameplayEffects`.                                                                                                                             |
| Ongoing Tag Requirements          | Once applied, these tags determine whether the `GameplayEffect` is on or off. A `GameplayEffect` can be off and still be applied. If a `GameplayEffect` is off due to failing the Ongoing Tag Requirements, but the requirements are then met, the `GameplayEffect` will turn on again and reapply its modifiers. This only works for `Duration` and `Infinite` `GameplayEffects`. |
| Application Tag Requirements      | Tags on the Target that determine if a `GameplayEffect` can be applied to the Target. If these requirements are not met, the `GameplayEffect` is not applied.                                                                                                                                                                                                                      |
| Remove Gameplay Effects with Tags | `GameplayEffects` on the Target that have any of these tags in their `Asset Tags` or `Granted Tags` will be removed from the Target when this `GameplayEffect` is successfully applied.                                                                                                                                                                                            |


| 카테고리                          | 설명                                                                                                                                                                                                                                                                                                                                                                        |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 
| Gameplay Effect Asset Tags        | `GameplayEffect`에 있는 태그입니다. 자체적으로 어떤 기능도 수행하지 않으며 'GameplayEffect'를 설명하는 목적으로만 제공됩니다.                                                                                                                                                                                                                                        |
| Granted Tags                      | 'GameplayEffect'에 있지만 'GameplayEffect'가 적용되는 'ASC'에도 부여되는 태그입니다. `GameplayEffect`가 제거되면 `ASC`에서 제거됩니다. 이것은 'Duration' 및 'Infinite' 'GameplayEffects'에서만 작동합니다.                                                                                                                             |
| Ongoing Tag Requirements          | 일단 적용되면 이 태그는 'GameplayEffect'가 켜져 있는지 여부를 결정합니다. 'GameplayEffect'는 꺼져 있어도 계속 적용할 수 있습니다. 진행 중인 태그 요구 사항에 실패하여 'GameplayEffect'가 꺼져 있지만 요구 사항이 충족되면 'GameplayEffect'가 다시 켜지고 수정자를 다시 적용합니다. 이것은 'Duration' 및 'Infinite' 'GameplayEffects'에서만 작동합니다. |
| Application Tag Requirements      | 대상에 'GameplayEffect'를 적용할 수 있는지 여부를 결정하는 대상의 태그입니다. 이러한 요구 사항이 충족되지 않으면 'GameplayEffect'가 적용되지 않습니다.                                                                                                                                                                                                                      |
| Remove Gameplay Effects with Tags | 'Asset Tags' 또는 'Granted Tags'에 이러한 태그가 있는 Target의 'GameplayEffects'는 이 'GameplayEffect'가 성공적으로 적용되면 대상에서 제거됩니다.                                                                                                                                                                                            |



**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 Immunity
`GameplayEffects` can grant immunity, effectively blocking the application of other `GameplayEffects`, based on [`GameplayTags`](#concepts-gt). While immunity can be effectively achieved through other means like `Application Tag Requirements`, using this system provides a delegate for when `GameplayEffects` are blocked due to immunity `UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`.

`GameplayEffects`는 면역을 부여하여 `GameplayTags`를 기반으로 다른 `GameplayEffects`의 적용을 효과적으로 차단할 수 있습니다. 면역은 '애플리케이션 태그 요구 사항'과 같은 다른 수단을 통해 효과적으로 달성할 수 있지만, 이 시스템을 사용하면 면역 'UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate'로 인해 'GameplayEffects'가 차단될 때 대리자를 제공합니다.

`GrantedApplicationImmunityTags` checks if the Source `ASC` (including tags from the Source ability's `AbilityTags` if there was one) has any of the specified tags. This is a way to provide immunity from all `GameplayEffects` from certain characters or sources based on their tags.

`GrantedApplicationImmunityTags`는 소스 `ASC`(소스 기능의 `AbilityTags`가 있는 경우 태그 포함)에 지정된 태그가 있는지 확인합니다. 이것은 태그를 기반으로 특정 캐릭터 또는 소스의 모든 'GameplayEffects'에 대한 면역성을 제공하는 방법입니다.

`Granted Application Immunity Query` checks the incoming `GameplayEffectSpec` if it matches any of the queries to block or allow its application.

'Granted Application Immunity Query'는 들어오는 'GameplayEffectSpec'이 애플리케이션을 차단하거나 허용하는 쿼리와 일치하는지 확인합니다.

The queries have helpful hover tooltips in the `GameplayEffect` Blueprint.

쿼리에는 `GameplayEffect` 블루프린트에 유용한 호버 툴팁이 있습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 4.5.9 Gameplay Effect Spec
The [`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html) (`GESpec`) can be thought of as the instantiations of `GameplayEffects`. They hold a reference to the `GameplayEffect` class that they represent, what level it was created at, and who created it. These can be freely created and modified at runtime before application unlike `GameplayEffects` which should be created by designers prior to runtime. When applying a `GameplayEffect`, a `GameplayEffectSpec` is created from the `GameplayEffect` and that is actually what is applied to the Target.

`GameplayEffectSpec`(`GESpec`)은 `GameplayEffects`의 인스턴스화로 생각할 수 있습니다. 그것들은 그들이 나타내는 `GameplayEffect` 클래스에 대한 참조, 생성된 레벨 및 생성한 사람에 대한 참조를 보유합니다. 디자이너가 런타임 전에 생성해야 하는 'GameplayEffects'와 달리 애플리케이션 실행 전에 런타임에 자유롭게 생성 및 수정할 수 있습니다. 'GameplayEffect'를 적용할 때 'GameplayEffect'에서 'GameplayEffectSpec'이 생성되고 이것이 실제로 Target에 적용되는 것입니다.

`GameplayEffectSpecs` are created from `GameplayEffects` using `UAbilitySystemComponent::MakeOutgoingSpec()` which is `BlueprintCallable`. `GameplayEffectSpecs` do not have to be immediately applied. It is common to pass a `GameplayEffectSpec` to a projectile created from an ability that the projectile can apply to the target it hits later. When `GameplayEffectSpecs` are successfully applied, they return a new struct called `FActiveGameplayEffect`.

'GameplayEffectSpecs'는 'BlueprintCallable'인 'UAbilitySystemComponent::MakeOutgoingSpec()'을 사용하여 'GameplayEffects'에서 생성됩니다. 'GameplayEffectSpecs'는 즉시 적용할 필요가 없습니다. 발사체가 나중에 명중하는 대상에 적용할 수 있는 능력에서 생성된 발사체에 'GameplayEffectSpec'을 전달하는 것이 일반적입니다. `GameplayEffectSpecs`가 성공적으로 적용되면 `FAactiveGameplayEffect`라는 새 구조체를 반환합니다.

Notable `GameplayEffectSpec` Contents:
* The `GameplayEffect` class that this `GameplayEffect` was created from.
* The level of this `GameplayEffectSpec`. Usually the same as the level of the ability that created the `GameplayEffectSpec` but can be different.
* The duration of the `GameplayEffectSpec`. Defaults to the duration of the `GameplayEffect` but can be different.
* The period of the `GameplayEffectSpec` for periodic effects. Defaults to the period of the `GameplayEffect` but can be different.
* The current stack count of this `GameplayEffectSpec`. The stack limit is on the `GameplayEffect`.
* The [`GameplayEffectContextHandle`](#concepts-ge-context) tells us who created this `GameplayEffectSpec`.
* `Attributes` that were captured at the time of the `GameplayEffectSpec`'s creation due to snapshotting.
* `DynamicGrantedTags` that the `GameplayEffectSpec` grants to the Target in addition to the `GameplayTags` that the `GameplayEffect` grants.
* `DynamicAssetTags` that the `GameplayEffectSpec` has in addition to the `AssetTags` that the `GameplayEffect` has.
* `SetByCaller` `TMaps`.

주목할만한 `GameplayEffectSpec` 콘텐츠:
* 이 `GameplayEffect`가 생성된 `GameplayEffect` 클래스.
* 이 `GameplayEffectSpec`의 레벨입니다. 일반적으로 'GameplayEffectSpec'을 생성한 능력 수준과 동일하지만 다를 수 있습니다.
* 'GameplayEffectSpec'의 지속 시간. 기본값은 'GameplayEffect'의 지속 시간이지만 다를 수 있습니다.
* 주기적인 효과에 대한 'GameplayEffectSpec'의 기간입니다. 기본값은 'GameplayEffect' 기간이지만 다를 수 있습니다.
* 이 `GameplayEffectSpec`의 현재 스택 수입니다. 스택 제한은 'GameplayEffect'에 있습니다.
* `GameplayEffectContextHandle`은 이 `GameplayEffectSpec`을 만든 사람을 알려줍니다.
* 스냅샷으로 인해 `GameplayEffectSpec` 생성 시 캡처된 `속성`.
* 'GameplayEffect'가 부여하는 'GameplayTags' 외에 'GameplayEffectSpec'이 Target에 부여하는 'DynamicGrantedTags'.
* `GameplayEffect`가 가지고 있는 `AssetTags` 외에 `GameplayEffectSpec`이 가지고 있는 `DynamicAssetTags`.
* `SetByCaller` `TMaps`.


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 SetByCallers
`SetByCallers` allow the `GameplayEffectSpec` to carry float values associated with a `GameplayTag` or `FName` around. They are stored in their respective `TMaps`: `TMap<FGameplayTag, float>` and `TMap<FName, float>` on the `GameplayEffectSpec`. These can be used to as `Modifiers` on the `GameplayEffect` or as generic means of ferrying floats around. It is common to pass numerical data generated inside of an ability to [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) or [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) via `SetByCallers`.

'SetByCallers'를 사용하면 'GameplayEffectSpec'이 'GameplayTag' 또는 'FName'과 관련된 부동 소수점 값을 전달할 수 있습니다. 그것들은 각각의 `TMaps`에 저장됩니다: `TMap<FGameplayTag, float>` 및 `TMap<FName, float>` on the `GameplayEffectSpec`. 이것들은 'GameplayEffect'의 'Modifiers'로 사용하거나 부유물을 나르는 일반적인 수단으로 사용할 수 있습니다. 기능 내부에서 생성된 수치 데이터를 'SetByCallers'를 통해 'GameplayEffectExecutionCalculations'에 전달하는 것이 일반적입니다.

| `SetByCaller` Use | Notes                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 
| `Modifiers`       | Must be defined ahead of time in the `GameplayEffect` class. Can only use the `GameplayTag` version. If one is defined on the `GameplayEffect` class but the `GameplayEffectSpec` does not have the corresponding tag and float value pair, the game will have a runtime error on application of the `GameplayEffectSpec` and return 0. This is a potential problem for a `Divide` operation. See [`Modifiers`](#concepts-ge-mods). |
| Elsewhere         | Does not need to be defined ahead of time anywhere. Reading a `SetByCaller` that does not exist on a `GameplayEffectSpec` can return a developer defined default value with optional warnings.                                                                                                                                                                                                                                      |

| `SetByCaller` 사용 | 메모 
| ------------------ | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- ------------------- 
| `수정자` | 'GameplayEffect' 클래스에서 미리 정의해야 합니다. 'GameplayTag' 버전만 사용할 수 있습니다. 'GameplayEffect' 클래스에 정의되어 있지만 'GameplayEffectSpec'에 해당 태그와 부동 소수점 값 쌍이 없으면 게임은 'GameplayEffectSpec' 적용 시 런타임 오류가 발생하고 0을 반환합니다. 이것은 잠재적인 문제입니다. '나누기' 작업입니다. '수정자'를 참조하세요. |
| 다른 곳 | 어디에서나 미리 정의할 필요가 없습니다. `GameplayEffectSpec`에 존재하지 않는 `SetByCaller`를 읽으면 선택적 경고와 함께 개발자가 정의한 기본값을 반환할 수 있습니다. |


To assign `SetByCaller` values in Blueprint, use the Blueprint node for the version that you need (`GameplayTag` or `FName`):

블루프린트에서 `SetByCaller` 값을 할당하려면 필요한 버전(`GameplayTag` 또는 `FName`)에 대한 블루프린트 노드를 사용하십시오:



![Assigning SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

To read a `SetByCaller` value in Blueprint, you will need to make custom nodes in your Blueprint Library.

블루프린트에서 'SetByCaller' 값을 읽으려면 블루프린트 라이브러리에서 커스텀 노드를 만들어야 합니다.

To assign `SetByCaller` values in C++, use the version of the function that you need (`GameplayTag` or `FName`):

C++에서 `SetByCaller` 값을 할당하려면 필요한 함수 버전(`GameplayTag` 또는 `FName`)을 사용하세요.

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

To read a `SetByCaller` value in C++, use the version of the function that you need (`GameplayTag` or `FName`):

C++에서 `SetByCaller` 값을 읽으려면 필요한 함수 버전(`GameplayTag` 또는 `FName`)을 사용하세요.

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

I recommend using the `GameplayTag` version over the `FName` version. This can prevent spelling errors in Blueprint and `GameplayTags` are more efficient to send over the network when the `GameplayEffectSpec` replicates than `FNames` since the `TMaps` replicate too.

'FName' 버전보다 'GameplayTag' 버전을 사용하는 것이 좋습니다. 이렇게 하면 블루프린트의 철자 오류를 방지할 수 있으며 `TMaps`도 복제하므로 `GameplayEffectSpec`이 `FNames`보다 복제될 때 `GameplayTags`가 네트워크를 통해 전송하는 것이 더 효율적입니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 4.5.10 Gameplay Effect Context
The [`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html) structure holds information about a `GameplayEffectSpec's` instigator and [`TargetData`](#concepts-targeting-data). This is also a good structure to subclass to pass arbitrary data around between places like [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) / [`GameplayEffectExecutionCalculations`](#concepts-ge-ec), [`AttributeSets`](#concepts-as), and [`GameplayCues`](#concepts-gc).

'GameplayEffectContext' 구조는 'GameplayEffectSpec'의 인스티게이터 및 'TargetData'에 대한 정보를 보유합니다.

To subclass the `GameplayEffectContext`:

1. Subclass `FGameplayEffectContext`
1. Override `FGameplayEffectContext::GetScriptStruct()`
1. Override `FGameplayEffectContext::Duplicate()`
1. Override `FGameplayEffectContext::NetSerialize()` if your new data needs to be replicated
1. Implement `TStructOpsTypeTraits` for your subclass, like the parent struct `FGameplayEffectContext` has
1. Override `AllocGameplayEffectContext()` in your [`AbilitySystemGlobals`](#concepts-asg) class to return a new object of your subclass

`GameplayEffectContext`를 서브클래싱하려면:

1. 서브클래스 `FGameplayEffectContext`
1. `FGameplayEffectContext::GetScriptStruct()` 재정의
1. `FGameplayEffectContext::Duplicate()` 재정의
1. 새 데이터를 복제해야 하는 경우 `FGameplayEffectContext::NetSerialize()`를 재정의합니다.
1. 부모 구조체 `FGameplayEffectContext`가 가지고 있는 것처럼 서브클래스에 `TStructOpsTypeTraits`를 구현합니다.
1. `AbilitySystemGlobals` 클래스에서 `AllocGameplayEffectContext()`를 재정의하여 하위 클래스의 새 개체를 반환합니다.


[GASShooter](https://github.com/tranek/GASShooter) uses a subclassed `GameplayEffectContext` to add `TargetData` which can be accessed in `GameplayCues`, specifically for the shotgun since it can hit more than one enemy.

GASShooter는 서브클래싱된 `GameplayEffectContext`를 사용하여 `GameplayCues`에서 액세스할 수 있는 `TargetData`를 추가합니다. 특히 샷건은 둘 이상의 적을 공격할 수 있기 때문입니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 Modifier Magnitude Calculation
[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html) (`ModMagCalc` or `MMC`) are powerful classes used as [`Modifiers`](#concepts-ge-mods) in `GameplayEffects`. They function similarly to [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) but are less powerful and most importantly they can be [predicted](#concepts-p). Their sole purpose is to return a float value from `CalculateBaseMagnitude_Implementation()`. You can subclass and override this function in Blueprint and C++.

`ModifierMagnitudeCalculations`(`ModMagCalc` 또는 `MMC`)는 `Modifiers`로 사용되는 강력한 클래스입니다. Blueprint 및 C++에서 이 함수를 서브클래스화하고 재정의할 수 있습니다.

`MMCs` can be used in any duration of `GameplayEffects` - `Instant`, `Duration`, `Infinite`, or `Periodic`.

'MMC'는 'GameplayEffects'의 'Instant', 'Duration', 'Infinite' 또는 'Periodic' 기간 동안 사용할 수 있습니다.

`MMCs` strength lies in their capability to capture the value of any number of `Attributes` on the `Source` or the `Target` of `GameplayEffect` with full access to the `GameplayEffectSpec` to read `GameplayTags` and `SetByCallers`. `Attributes` can either be snapshotted or not. Snapshotted `Attributes` are captured when the `GameplayEffectSpec` is created whereas non snapshotted `Attributes` are captured when the `GameplayEffectSpec` is applied and automatically update when the `Attribute` changes for `Infinite` and `Duration` `GameplayEffects`. Capturing `Attributes` recalculates their `CurrentValue` from existing mods on the `ASC`. This recalculation will **not** run [`PreAttributeChange()`](#concepts-as-preattributechange) in the `AbilitySet` so any clamping must be done here again.

'MMC'의 강점은 'GameplayEffectSpec'에 대한 전체 액세스 권한으로 'GameplayTags' 및 'SetByCallers'를 읽을 수 있는 'Source' 또는 'Target'의 'Attributes' 값을 캡처하는 기능에 있습니다. '속성'은 스냅샷을 생성하거나 생성하지 않을 수 있습니다. 스냅샷된 '속성'은 'GameplayEffectSpec'이 생성될 때 캡처되는 반면 스냅샷되지 않은 '속성'은 'GameplayEffectSpec'이 적용될 때 캡처되며 'Infinite' 및 'Duration' 'GameplayEffects'에 대한 '속성'이 변경되면 자동으로 업데이트됩니다. '속성'을 캡처하면 'ASC'의 기존 모드에서 'CurrentValue'가 다시 계산됩니다. 이 재계산은 `AbilitySet`에서 `PreAttributeChange()`를 실행하지 **않으므로** 모든 클램핑을 여기서 다시 수행해야 합니다.

| Snapshot | Source or Target | Captured on `GameplayEffectSpec` | `Infinite` 또는 `Duration` `GE`에 대해 `Attribute`가 변경되면 자동으로 업데이트 |
| -------- | ---------------- | -------------------------------- | -------------------------------------------------------------------------------- |
| Yes      | Source           | Creation                         | No                                                                               |
| Yes      | Target           | Application                      | No                                                                               |
| No       | Source           | Application                      | Yes                                                                              |
| No       | Target           | Application                      | Yes                                                                              |

The resultant float from an `MMC` can further be modified in the `GameplayEffect's` `Modifier` by a coefficient and a pre and post coefficient addition.

'MMC'의 결과 float는 'GameplayEffect'의 'Modifier'에서 계수와 사전 및 사후 계수 추가로 추가로 수정할 수 있습니다.

An example `MMC` that captures the `Target's` mana `Attribute` reduces it from a poison effect where the amount reduced changes depending on how much mana the `Target` has and a tag that the `Target` might have:

`Target`의 마나 `Attribute`를 캡처하는 `MMC`의 예는 `Target`이 가지고 있는 마나의 양과 `Target`이 가질 수 있는 태그에 따라 감소량이 변경되는 독 효과에서 이를 감소시킵니다.

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

If you don't add the `FGameplayEffectAttributeCaptureDefinition` to `RelevantAttributesToCapture` in the `MMC's` constructor and try to capture `Attributes`, you will get an error about a missing Spec while capturing. If you don't need to capture `Attributes`, then you don't have to add anything to `RelevantAttributesToCapture`.

`MMC` 생성자의 `RelevantAttributesToCapture`에 `FGameplayEffectAttributeCaptureDefinition`을 추가하지 않고 `Attributes`를 캡처하려고 하면 캡처하는 동안 누락된 사양에 대한 오류가 발생합니다. `Attributes`를 캡처할 필요가 없다면 `RelevantAttributesToCapture`에 아무것도 추가할 필요가 없습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 4.5.12 Gameplay Effect Execution Calculation
[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html) (`ExecutionCalculation`, `Execution` (you will often see this term in the plugin's source code), or `ExecCalc`) are the most powerful way for `GameplayEffects` to make changes to an `ASC`. Like [`ModifierMagnitudeCalculations`](#concepts-ge-mmc), these can capture `Attributes` and optionally snapshot them. Unlike `MMCs`, these can change more than one `Attribute` and essentially do anything else that the programmer wants. The downside to this power and flexibility is that they can not be [predicted](#concepts-p) and they must be implemented in C++.

`GameplayEffectExecutionCalculation`(`ExecutionCalculation`, `Execution`(플러그인의 소스 코드에서 종종 이 용어를 볼 수 있음) 또는 `ExecCalc`)은 `GameplayEffects`가 `ASC`를 변경하는 가장 강력한 방법입니다. 'ModifierMagnitudeCalculations'와 같이 C++로 구현해야 합니다.

`ExecutionCalculations` can only be used with `Instant` and `Periodic` `GameplayEffects`. Anything with the word 'Execute' in it typically refers to these two types of `GameplayEffects`.

`ExecutionCalculations`는 `Instant` 및 `Periodic` `GameplayEffects`에서만 사용할 수 있습니다. 'Execute'라는 단어가 있는 것은 일반적으로 이 두 가지 유형의 'GameplayEffects'를 나타냅니다.

Snapshotting captures the `Attribute` when the `GameplayEffectSpec` is created whereas not snapshotting captures the `Attribute` when the `GameplayEffectSpec` is applied. Capturing `Attributes` recalculates their `CurrentValue` from existing mods on the `ASC`. This recalculation will **not** run [`PreAttributeChange()`](#concepts-as-preattributechange) in the `AbilitySet` so any clamping must be done here again.

Snapshotting은 `GameplayEffectSpec`이 생성될 때 `Attribute`를 캡처하는 반면 스냅샷을 생성하지 않으면 `GameplayEffectSpec`이 적용될 때 `Attribute`를 캡처합니다. '속성'을 캡처하면 'ASC'의 기존 모드에서 'CurrentValue'가 다시 계산됩니다. 이 재계산은 `AbilitySet`에서 `PreAttributeChange()`를 실행하지 **않으므로** 모든 클램핑을 여기서 다시 수행해야 합니다.

| Snapshot | Source or Target | Captured on `GameplayEffectSpec` |
| -------- | ---------------- | -------------------------------- |
| Yes      | Source           | Creation                         |
| Yes      | Target           | Application                      |
| No       | Source           | Application                      |
| No       | Target           | Application                      |

To set up `Attribute` capture, we follow a pattern set by Epic's ActionRPG Sample Project by defining a struct holding and defining how we capture the `Attributes` and creating one copy of it in the struct's constructor. You will have a struct like this for every `ExecCalc`. **Note:** Each struct needs a unique name as they share the same namespace. Using the same name for the structs will cause incorrect behavior in capturing your `Attributes` (mostly capturing the values of the wrong `Attributes`).

'속성' 캡처를 설정하기 위해, '속성'을 캡처하는 방법을 정의하고 구조체의 생성자에서 복사본 하나를 생성하여 구조체를 정의하고 에픽의 ActionRPG 샘플 프로젝트에서 설정한 패턴을 따릅니다. 모든 `ExecCalc`에 대해 이와 같은 구조체를 갖게 됩니다. **참고:** 각 구조체는 동일한 네임스페이스를 공유하므로 고유한 이름이 필요합니다. 구조체에 같은 이름을 사용하면 `특성`을 캡처할 때 잘못된 동작이 발생합니다(대부분 잘못된 `특성` 값 캡처).

For `Local Predicted`, `Server Only`, and `Server Initiated` [`GameplayAbilities`](#concepts-ga), the `ExecCalc` only calls on the Server.

`Local Predicted`, `Server Only` 및 `Server Initiated` `GameplayAbilities`의 경우 `ExecCalc`는 서버에서만 호출합니다.

Calculating damage received based on a complex formula reading from many attributes on the `Source` and the `Target` is the most common example of an `ExecCalc`. The included Sample Project has a simple `ExecCalc` for calculating damage that reads the value of damage from the `GameplayEffectSpec's` [`SetByCaller`](#concepts-ge-spec-setbycaller) and then mitigates that value based on the armor `Attribute` captured from the `Target`. See `GDDamageExecCalculation.cpp/.h`.

'Source'와 'Target'의 많은 속성을 읽는 복잡한 공식을 기반으로 받은 피해를 계산하는 것이 'ExecCalc'의 가장 일반적인 예입니다. 포함된 샘플 프로젝트에는 'GameplayEffectSpec'의 'SetByCaller'에서 피해 값을 읽은 다음 'Target'에서 캡처한 갑옷 'Attribute'를 기반으로 해당 값을 완화하는 피해 계산을 위한 간단한 'ExecCalc'가 있습니다. `GDamageExecCalculation.cpp/.h`를 참조하세요.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 Sending Data to Execution Calculations
There are a few ways to send data to an `ExecutionCalculation` in addition to capturing `Attributes`.

'속성'을 캡처하는 것 외에도 'ExecutionCalculation'에 데이터를 보내는 몇 가지 방법이 있습니다.

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller
Any [`SetByCallers` set on the `GameplayEffectSpec`](#concepts-ge-spec-setbycaller) can be directly read in the `ExecutionCalculation`.

`GameplayEffectSpec`에 설정된 모든 `SetByCallers`는 `ExecutionCalculation`에서 직접 읽을 수 있습니다.

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 Backing Data Attribute Calculation Modifier
If you want to hardcode values to a `GameplayEffect`, you can pass them in using a `CalculationModifier` that uses one of the captured `Attributes` as the backing data.

값을 'GameplayEffect'에 하드코딩하려면 캡처된 '속성' 중 하나를 지원 데이터로 사용하는 'CalculationModifier'를 사용하여 값을 전달할 수 있습니다.

In this screenshot example, we're adding 50 to the captured Damage `Attribute`. You could also set this to `Override` to just take in only the hardcoded value.

이 스크린샷 예에서는 캡처된 피해 '속성'에 50을 추가합니다. 또한 이것을 '재정의'로 설정하여 하드코딩된 값만 받을 수도 있습니다.

![Backing Data Attribute Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

The `ExecutionCalculation` reads this value in when it captures the `Attribute`.

`ExecutionCalculation`은 `Attribute`를 캡처할 때 이 값을 읽습니다.

```c++
float Damage = 0.0f;
// Capture optional damage value set on the damage GE as a CalculationModifier under the ExecutionCalculation
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>
###### 4.5.12.1.3 Backing Data Temporary Variable Calculation Modifier
If you want to hardcode values to a `GameplayEffect`, you can pass them in using a `CalculationModifier` that uses a `Temporary Variable` or `Transient Aggregator` as it's called in C++. The `Temporary Variable` is associated with a `GameplayTag`.

값을 'GameplayEffect'에 하드코딩하려면 C++에서 호출되는 'Temporary Variable' 또는 'Transient Aggregator'를 사용하는 'CalculationModifier'를 사용하여 값을 전달할 수 있습니다. '임시 변수'는 'GameplayTag'와 연결됩니다.

In this screenshot example, we're adding 50 to a `Temporary Variable` using the `Data.Damage` `GameplayTag`.

이 스크린샷 예시에서는 `Data.Damage` `GameplayTag`를 사용하여 `임시 변수`에 50을 추가합니다.

![Backing Data Temporary Variable Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

Add backing `Temporary Variables` to your `ExecutionCalculation`'s constructor:

`ExecutionCalculation`의 생성자에 백업 `Temporary Variables`를 추가합니다.

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

The `ExecutionCalculation` reads this value in using special capture functions similar to the `Attribute` capture functions.

`ExecutionCalculation`은 `Attribute` 캡처 기능과 유사한 특수 캡처 기능을 사용하여 이 값을 읽습니다.

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 Gameplay Effect Context
You can send data to the `ExecutionCalculation` via a custom [`GameplayEffectContext` on the `GameplayEffectSpec`](#concepts-ge-context).

`GameplayEffectSpec`의 커스텀 `GameplayEffectContext`를 통해 `ExecutionCalculation`에 데이터를 보낼 수 있습니다.

In the `ExecutionCalculation` you can access the `EffectContext` from the `FGameplayEffectCustomExecutionParameters`.

`ExecutionCalculation`에서 `FGameplayEffectCustomExecutionParameters`의 `EffectContext`에 액세스할 수 있습니다.
```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

If you need change something on the `GameplayEffectSpec` or the `EffectContext`:
`GameplayEffectSpec` 또는 `EffectContext`에서 무언가를 변경해야 하는 경우:

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

Use caution if modifying the `GameplayEffectSpec` in the `ExecutionCalculation`. See the comment for `GetOwningSpecForPreExecuteMod()`.
'ExecutionCalculation'에서 'GameplayEffectSpec'을 수정할 때는 주의하세요. `GetOwningSpecForPreExecuteMod()`에 대한 주석을 참조하십시오.

```c++
/** Non const access. Be careful with this, especially when modifying a spec after attribute capture. */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 4.5.13 Custom Application Requirement
[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html) (`CAR`) classes give the designers advanced control over whether a `GameplayEffect` can be applied versus the simple `GameplayTag` checks on the `GameplayEffect`. These can be implemented in Blueprint by overriding `CanApplyGameplayEffect()` and in C++ by overriding `CanApplyGameplayEffect_Implementation()`.

`CustomApplicationRequirement`(`CAR`) 클래스는 `GameplayEffect`에 대한 단순한 `GameplayTag` 검사에 비해 `GameplayEffect`를 적용할 수 있는지 여부에 대한 고급 제어 기능을 디자이너에게 제공합니다. 이는 'CanApplyGameplayEffect()'를 재정의하여 블루프린트에서 구현하고, 'CanApplyGameplayEffect_Implementation()'을 재정의하여 C++에서 구현할 수 있습니다.

Examples of when to use `CARs`:
* `Target` needs to have a certain amount of an `Attribute`
* `Target` needs to have a certain number of stacks of a `GameplayEffect`

'CAR'을 사용하는 경우의 예:
* `Target`에는 일정량의 `Attribute`가 있어야 합니다.
* `Target`에는 `GameplayEffect`의 특정 수의 스택이 있어야 합니다.


`CARs` can also do more advanced things like checking if an instance of this `GameplayEffect` is already on the `Target` and [changing the duration](#concepts-ge-duration) of the existing instance instead of applying a new instance (return false for `CanApplyGameplayEffect()`).

`CAR`은 이 `GameplayEffect`의 인스턴스가 이미 `Target`에 있는지 확인하고 지속 시간을 변경하는 것과 같은 고급 작업을 수행할 수도 있습니다.
**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 Cost Gameplay Effect
[`GameplayAbilities`](#concepts-ga) have an optional `GameplayEffect` specifically designed to use as the cost of the ability. Costs are how much of an `Attribute` an `ASC` needs to have to be able to activate the `GameplayAbility`. If a `GA` cannot afford the `Cost GE`, then they will not be able to activate. This `Cost GE` should be an `Instant` `GameplayEffect` with one or more `Modifiers` that subtract from `Attributes`. By default, `Cost GEs` are meant to be predicted and it is recommended to maintain that capability meaning do not use `ExecutionCalculations`. `MMCs` are perfectly acceptable and encouraged for complex cost calculations.

`GameplayAbilities`에는 능력의 비용으로 사용하도록 특별히 설계된 선택적 `GameplayEffect`가 있습니다. 비용은 'GameplayAbility'를 활성화하기 위해 'ASC'가 필요로 하는 '속성'의 양입니다. 'GA'가 '비용 GE'를 감당할 수 없으면 활성화할 수 없습니다. 이 `Cost GE`는 `Attributes`에서 빼는 하나 이상의 `Modifiers`가 있는 `Instant` `GameplayEffect`여야 합니다. 기본적으로 '비용 GE'는 예측을 의미하며 '실행 계산'을 사용하지 않는 기능 의미를 유지하는 것이 좋습니다. 'MMC'는 복잡한 비용 계산에 완벽하게 허용되며 권장됩니다.

When starting out, you will most likely have one unique `Cost GE` per `GA` that has a cost. A more advanced technique is to reuse one `Cost GE` for multiple `GAs` and just modify the `GameplayEffectSpec` created from the `Cost GE` with the `GA`-specific data (the cost value is defined on the `GA`). **This only works for `Instanced` abilities.**

시작할 때 비용이 있는 `GA`당 하나의 고유한 `비용 GE`가 있을 가능성이 큽니다. 더 발전된 기술은 여러 `GA`에 대해 하나의 `Cost GE`를 재사용하고 `Cost GE`에서 생성된 `GameplayEffectSpec`을 `GA` 관련 데이터로 수정하는 것입니다(비용 값은 `GA` ). **이 기능은 '인스턴스' 기능에만 적용됩니다.**

Two techniques for reusing the `Cost GE`:
'Cost GE'를 재사용하는 두 가지 기술:

1. **Use an `MMC`.** This is the easiest method. Create an [`MMC`](#concepts-ge-mmc) that reads the cost value from the `GameplayAbility` instance which you can get from the `GameplayEffectSpec`.
1. **'MMC'를 사용합니다.** 가장 쉬운 방법입니다. `GameplayEffectSpec`에서 얻을 수 있는 `GameplayAbility` 인스턴스에서 비용 값을 읽는 `MMC`를 만듭니다.
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

In this example the cost value is an `FScalableFloat` on the `GameplayAbility` child class that I added to it.
이 예제에서 비용 값은 내가 추가한 `GameplayAbility` 자식 클래스의 `FScalableFloat`입니다

2. **'UGameplayAbility::GetCostGameplayEffect()'를 재정의합니다.** 이 함수를 재정의하고 런타임에 'GameplayAbility'의 비용 값을 읽는 'GameplayEffect'를 만듭니다.
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **Override `UGameplayAbility::GetCostGameplayEffect()`.** Override this function and [create a `GameplayEffect` at runtime](#concepts-ge-dynamic) that reads the cost value on the `GameplayAbility`.


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 Cooldown Gameplay Effect
[`GameplayAbilities`](#concepts-ga) have an optional `GameplayEffect` specifically designed to use as the cooldown of the ability. Cooldowns determine how long after activation the ability can be activated again. If a `GA` is still on cooldown, it cannot activate. This `Cooldown GE` should be a `Duration` `GameplayEffect` with no `Modifiers` and a unique `GameplayTag` per `GameplayAbility` or per ability slot (if your game has interchangeable abilities assigned to slots that share a cooldown) in the `GameplayEffect's` `GrantedTags` ("`Cooldown Tag`"). The `GA` actually checks for the presence for the `Cooldown Tag` instead of the presence of the `Cooldown GE`. By default, `Cooldown GEs` are meant to be predicted and it is recommended to maintain that capability meaning do not use `ExecutionCalculations`. `MMCs` are perfectly acceptable and encouraged for complex cooldown calculations.

'게임 플레이 능력'. 'GA'는 실제로 'Cooldown GE' 대신 'Cooldown Tag'가 있는지 확인합니다. 기본적으로 'Cooldown GEs'는 예측을 위한 것이며 'ExecutionCalculations'를 사용하지 않음을 의미하는 기능을 유지하는 것이 좋습니다. 'MMC'는 완벽하게 허용되며 복잡한 재사용 대기시간 계산에 권장됩니다.

When starting out, you will most likely have one unique `Cooldown GE` per `GA` that has a cooldown. A more advanced technique is to reuse one `Cooldown GE` for multiple `GAs` and just modify the `GameplayEffectSpec` created from the `Cooldown GE` with the `GA`-specific data (the cooldown duration and the `Cooldown Tag` are defined on the `GA`). **This only works for `Instanced` abilities.**

시작할 때 쿨다운이 있는 'GA'당 하나의 고유한 '쿨다운 GE'가 있을 가능성이 큽니다. 더 발전된 기술은 하나의 `Cooldown GE`를 여러 `GA`에 재사용하고 `Cooldown GE`에서 생성된 `GameplayEffectSpec`을 `GA` 관련 데이터로 수정하는 것입니다(쿨다운 지속 시간 및 `Cooldown Tag`는 'GA'에 정의됨). **이 기능은 '인스턴스' 기능에만 적용됩니다.**

Two techniques for reusing the `Cooldown GE`:
'쿨다운 GE' 재사용을 위한 두 가지 기술:

1. **Use a [`SetByCaller`](#concepts-ge-spec-setbycaller).** This is the easiest method. Set the duration of your shared `Cooldown GE` to `SetByCaller` with a `GameplayTag`. On your `GameplayAbility` subclass, define a float / `FScalableFloat` for the duration, a `FGameplayTagContainer` for the unique `Cooldown Tag`, and a temporary `FGameplayTagContainer` that we will use as the return pointer of the union of our `Cooldown Tag` and the `Cooldown GE's` tags.

1. **'SetByCaller'를 사용합니다.** 가장 쉬운 방법입니다. 공유된 `Cooldown GE`의 지속 시간을 `GameplayTag`를 사용하여 `SetByCaller`로 설정합니다. `GameplayAbility` 서브클래스에서 지속 시간에 대한 float / `FScalableFloat`, 고유한 `Cooldown Tag`에 대한 `FGameplayTagContainer`, 그리고 우리의 통합의 반환 포인터로 사용할 임시 `FGameplayTagContainer`를 정의합니다. `Cooldown Tag` 및 `Cooldown GE's` 태그.

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

Then override `UGameplayAbility::GetCooldownTags()` to return the union of our `Cooldown Tags` and any existing `Cooldown GE's` tags.
그런 다음 `UGameplayAbility::GetCooldownTags()`를 재정의하여 `Cooldown Tags`와 기존 `Cooldown GE` 태그의 합집합을 반환합니다.
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

Finally, override `UGameplayAbility::ApplyCooldown()` to inject our `Cooldown Tags` and to add the `SetByCaller` to the cooldown `GameplayEffectSpec`.
마지막으로 `UGameplayAbility::ApplyCooldown()`을 재정의하여 `쿨다운 태그`를 삽입하고 `SetByCaller`를 쿨다운 `GameplayEffectSpec`에 추가합니다.
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

In this picture, the cooldown's duration `Modifier` is set to `SetByCaller` with a `Data Tag` of `Data.Cooldown`. `Data.Cooldown` would be `OurSetByCallerTag` in the code above.

이 그림에서 쿨다운의 지속 시간 'Modifier'는 'Data.Cooldown'의 'Data Tag'와 함께 'SetByCaller'로 설정되어 있습니다. 위 코드에서 `Data.Cooldown`은 `OurSetByCallerTag`입니다.
![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **Use an [`MMC`](#concepts-ge-mmc).** This has the same setup as above except for setting the `SetByCaller` as the duration on the `Cooldown GE` and in `ApplyCooldown`. Instead, set the duration to be a `Custom Calculation Class` and point to the new `MMC` that we will make.
 
2. **`MMC`를 사용합니다.** `SetByCaller`를 `Cooldown GE` 및 `ApplyCooldown`에서 지속 시간으로 설정하는 것을 제외하고 위와 동일한 설정입니다. 대신 기간을 'Custom Calculation Class'로 설정하고 우리가 만들 새 'MMC'를 가리킵니다.

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

Then override `UGameplayAbility::GetCooldownTags()` to return the union of our `Cooldown Tags` and any existing `Cooldown GE's` tags.
그런 다음 `UGameplayAbility::GetCooldownTags()`를 재정의하여 `Cooldown Tags`와 기존 `Cooldown GE` 태그의 합집합을 반환합니다.

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

Finally, override `UGameplayAbility::ApplyCooldown()` to inject our `Cooldown Tags` into the cooldown `GameplayEffectSpec`.
마지막으로 `UGameplayAbility::ApplyCooldown()`을 재정의하여 `쿨다운 태그`를 쿨다운 `GameplayEffectSpec`에 삽입합니다.

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

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 Get the Cooldown Gameplay Effect's Remaining Time
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

**Note:** Querying the cooldown's time remaining on clients requires that they can receive replicated `GameplayEffects`. This will depend on their `ASC's` [replication mode](#concepts-asc-rm).

**참고:** 클라이언트에 남은 재사용 대기시간을 쿼리하려면 복제된 'GameplayEffects'를 받을 수 있어야 합니다. 이것은 'ASC'의 복제 모드에 따라 달라집니다.

<a name="concepts-ge-cooldown-listen"></a>
##### 4.5.15.2 Listening for Cooldown Begin and End
To listen for when a cooldown begins, you can either respond to when the `Cooldown GE` is applied by binding to `AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf` or when the `Cooldown Tag` is added by binding to `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`. I recommend listening for when the `Cooldown GE` is added because you also have access to the `GameplayEffectSpec` that applied it. From this you can determine if the `Cooldown GE` is the locally predicted one or the Server's correcting one.

쿨다운이 시작될 때 수신 대기하려면 'AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf'에 바인딩하여 'Cooldown GE'가 적용되거나 'AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)'. 'Cooldown GE'가 추가되면 이를 적용한 'GameplayEffectSpec'에도 액세스할 수 있으므로 듣는 것이 좋습니다. 이를 통해 `Cooldown GE`가 로컬에서 예측한 것인지 서버에서 수정한 것인지 결정할 수 있습니다.

To listen for when a cooldown ends, you can either respond to when the `Cooldown GE` is removed by binding to `AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()` or when the `Cooldown Tag` is removed by binding to `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`. I recommend listening for when the `Cooldown Tag` is removed because when the Server's corrected `Cooldown GE` comes in, it will remove our locally predicted one causing the `OnAnyGameplayEffectRemovedDelegate()` to fire even though we're still on cooldown. The `Cooldown Tag` will not change during the removal of the predicted `Cooldown GE` and the application of the Server's corrected `Cooldown GE`.

쿨다운이 언제 종료되는지 들으려면 `AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()`에 바인딩하여 `Cooldown GE`가 제거되거나 `AbilitySystemComponent->RegisterGameplayTagEvent( CooldownTag, EGameplayTagEventType::NewOrRemoved)`. 서버의 수정된 'Cooldown GE'가 들어오면 로컬에서 예측한 태그가 제거되어 아직 쿨다운 중에도 'OnAnyGameplayEffectRemovedDelegate()'가 실행되기 때문에 `Cooldown Tag`가 제거될 때 듣는 것이 좋습니다. '쿨다운 태그'는 예측된 '쿨다운 GE'를 제거하고 서버의 수정된 '쿨다운 GE'를 적용하는 동안 변경되지 않습니다.

**Note:** Listening for a `GameplayEffect` to be added or removed on clients requires that they can receive replicated `GameplayEffects`. This will depend on their `ASC's` [replication mode](#concepts-asc-rm).

**참고:** 클라이언트에서 추가 또는 제거할 `GameplayEffect`를 수신하려면 복제된 `GameplayEffect`를 수신할 수 있어야 합니다. 이것은 'ASC'의 복제 모드에 따라 달라집니다.

The Sample Project includes a custom Blueprint node that listens for cooldowns beginning and ending. The HUD UMG Widget uses it to update the amount of time remaining on the Meteor's cooldown. This `AsyncTask` will live forever until manually called `EndTask()`, which we do in the UMG Widget's `Destruct` event. See [`AsyncTaskCooldownChanged.h/cpp`](Source/GASDocumentation/Private/Characters/Abilities/AsyncTaskCooldownChanged.cpp).

샘플 프로젝트에는 쿨다운 시작과 끝을 수신 대기하는 커스텀 블루프린트 노드가 포함되어 있습니다. HUD UMG 위젯은 이를 사용하여 Meteor의 재사용 대기시간에 남은 시간을 업데이트합니다. 이 'AsyncTask'는 UMG 위젯의 'Destruct' 이벤트에서 수동으로 'EndTask()'를 호출할 때까지 영원히 지속됩니다. 'AsyncTaskCooldownChanged.h/cpp'를 참조하세요.

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

<a name="concepts-ge-cooldown-prediction"></a>
##### 4.5.15.3 Predicting Cooldowns
Cooldowns cannot really be predicted currently. We can start UI cooldown timer's when the locally predicted `Cooldown GE` is applied but the `GameplayAbility's` actual cooldown is tied to the server's cooldown's time remaining. Depending on the player's latency, the locally predicted cooldown could expire but the `GameplayAbility` would still be on cooldown on the server and this would prevent the `GameplayAbility's` immediate re-activation until the server's cooldown expires.

현재로서는 쿨다운을 예측할 수 없습니다. 로컬에서 예측한 'Cooldown GE'가 적용될 때 UI 쿨다운 타이머를 시작할 수 있지만 'GameplayAbility'의 실제 쿨다운은 서버의 남은 쿨다운 시간과 연결되어 있습니다. 플레이어의 대기 시간에 따라 로컬에서 예측한 쿨다운이 만료될 수 있지만 'GameplayAbility'는 여전히 서버에서 쿨다운 상태이므로 서버의 쿨다운이 만료될 때까지 'GameplayAbility'의 즉각적인 재활성화가 방지됩니다.

The Sample Project handles this by graying out the Meteor ability's UI icon when the locally predicted cooldown begins and then starting the cooldown timer once the server's corrected `Cooldown GE` comes in.

샘플 프로젝트는 로컬 예측 쿨다운이 시작될 때 유성 능력의 UI 아이콘을 회색으로 처리한 다음 서버의 수정된 '쿨다운 GE'가 들어오면 쿨다운 타이머를 시작하여 이 문제를 처리합니다.

A gameplay consequence of this is that players with high latencies have a lower rate of fire on short cooldown abilities than players with lower latencies putting them at a disadvantage. Fortnite avoids this by their weapons having custom bookkeeping that do not use cooldown `GameplayEffects`.

이것의 게임 플레이 결과는 지연 시간이 긴 플레이어가 짧은 쿨다운 능력에 대한 발사 속도가 지연 시간이 짧은 플레이어보다 낮아 불리하게 작용한다는 것입니다. Fortnite는 쿨다운 'GameplayEffects'를 사용하지 않는 맞춤형 저장 방식이 무기에 있으므로 이를 방지합니다.

Allowing for true predicted cooldowns (player could activate a `GameplayAbility` when the local cooldown expires but the server is still on cooldown) is something that Epic would like to implement someday in a [future iteration of GAS](#concepts-p-future).

진정한 예측 쿨다운을 허용하는 것(로컬 쿨다운이 만료되었지만 서버는 여전히 쿨다운 상태일 때 플레이어는 'GameplayAbility'를 활성화할 수 있음)은 에픽이 향후 GAS 반복에서 구현하고자 하는 것입니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 4.5.16 Changing Active Gameplay Effect Duration
To change the time remaining for a `Cooldown GE` or any `Duration` `GameplayEffect`, we need to change the `GameplayEffectSpec's` `Duration`, update its `StartServerWorldTime`, update its `CachedStartServerWorldTime`, update its `StartWorldTime`, and rerun the check on the duration with `CheckDuration()`. Doing this on the server and marking the `FActiveGameplayEffect` dirty will replicate the changes to clients.

`Cooldown GE` 또는 `Duration` `GameplayEffect`의 남은 시간을 변경하려면 `GameplayEffectSpec`의 `Duration`을 변경하고, `StartServerWorldTime`을 업데이트하고, `CachedStartServerWorldTime`을 업데이트하고, `StartWorldTime`을 업데이트해야 합니다. 그리고 `CheckDuration()`을 사용하여 지속 시간을 다시 확인하십시오. 서버에서 이 작업을 수행하고 `FACiveGameplayEffect`를 더티로 표시하면 변경 사항이 클라이언트에 복제됩니다.

**Note:** This does involve a `const_cast` and may not be Epic's intended way of changing durations, but it seems to work well so far.

**참고:** 여기에는 `const_cast`가 포함되며 Epic이 의도한 지속 시간 변경 방법이 아닐 수도 있지만 지금까지는 잘 작동하는 것 같습니다.


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

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 Creating Dynamic Gameplay Effects at Runtime
Creating Dynamic `GameplayEffects` at runtime is an advanced topic. You shouldn't have to do this too often.

런타임 시 동적 'GameplayEffects' 생성은 고급 주제입니다. 너무 자주 할 필요는 없습니다.

Only `Instant` `GameplayEffects` can be created at runtime from scratch in C++. `Duration` and `Infinite` `GameplayEffects` cannot be created dynamically at runtime because when they replicate they look for the `GameplayEffect` class definition that does not exist. To achieve this functionality, you should instead make an archetype `GameplayEffect` class like you would normally do in the Editor. Then customize the `GameplayEffectSpec` instance with what you need at runtime.

C++에서는 런타임에 `Instant` `GameplayEffects`만 처음부터 생성할 수 있습니다. `Duration` 및 `Infinite` `GameplayEffects`는 복제할 때 존재하지 않는 `GameplayEffect` 클래스 정의를 찾기 때문에 런타임에 동적으로 생성할 수 없습니다. 이 기능을 사용하려면 일반적으로 에디터에서 하는 것처럼 아키타입 'GameplayEffect' 클래스를 만들어야 합니다. 그런 다음 런타임에 필요한 항목으로 `GameplayEffectSpec` 인스턴스를 사용자 지정합니다.

`Instant` `GameplayEffects` created at runtime can also be called from within a [local predicted](#concepts-p) `GameplayAbility`. However, it is unknown yet if the dynamic creation can have side effects.

런타임에 생성된 `Instant` `GameplayEffects`는 로컬 예측된 `GameplayAbility` 내에서도 호출할 수 있습니다. 그러나 동적 생성이 부작용을 일으킬 수 있는지 여부는 아직 알려지지 않았습니다.

##### Examples

The Sample Project creates one to send the gold and experience points back to the killer of a character when it takes the killing blow in its `AttributeSet`.

샘플 프로젝트는 캐릭터의 'AttributeSet'에서 킬링 타격을 가할 때 캐릭터의 킬러에게 골드와 경험치를 되돌려 보내기 위해 하나를 생성합니다.

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

A second example shows a runtime `GameplayEffect` created within a local predicted `GameplayAbility`. Use at your own risk (see comments in code)!

두 번째 예는 로컬 예측 'GameplayAbility' 내에서 생성된 런타임 'GameplayEffect'를 보여줍니다. 자신의 책임하에 사용하십시오(코드의 주석 참조)!

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
		
		// GE 클래스 기본 객체에서 GESpec을 생성하는 ASC의 동작을 피하기 위해 여기에 GESpec을 생성합니다.
		// 여기에 동적 GE가 있으므로 기본 GameplayEffect 클래스를 사용하여 GESpec을 생성하므로
		// 수정자를 잃게 됩니다. 주의: 여기에서 수행된 이 "해킹"이 단점을 가질 수 있는지 여부는 알 수 없습니다!
		// 스펙은 GE가 스펙의 UPROPERTY이기 때문에 GarbageCollector가 GE 객체를 수집하는 것을 방지합니다.

		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // "new", since lifetime is managed by a shared ptr within the handle
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 4.5.18 Gameplay Effect Containers
Epic's [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) implements a structure called `FGameplayEffectContainer`. These are not in vanilla GAS but are extremely handy for containing `GameplayEffects` and [`TargetData`](#concepts-targeting-data). It automates a some of the effort like creating `GameplayEffectSpecs` from `GameplayEffects` and setting default values in its `GameplayEffectContext`. Making a `GameplayEffectContainer` in a `GameplayAbility` and passing it to spawned projectiles is very easy and straightforward. I opted not to implement the `GameplayEffectContainers` in the included Sample Project to show how you would work without them in vanilla GAS, but I highly recommend looking into them and considering adding them to your project.

Epic의 액션 RPG 샘플 프로젝트는 'FGameplayEffectContainer'라는 구조를 구현합니다. 이것은 바닐라 GAS에 없지만 `GameplayEffects` 및 `TargetData`를 포함하는 데 매우 편리합니다. `GameplayEffects`에서 `GameplayEffectSpecs`를 생성하고 `GameplayEffectContext`에서 기본값을 설정하는 것과 같은 작업을 자동화합니다. `GameplayAbility`에서 `GameplayEffectContainer`를 만들고 생성된 발사체에 전달하는 것은 매우 쉽고 간단합니다. 나는 바닐라 GAS에서 그것들 없이 어떻게 작동하는지 보여주기 위해 포함된 샘플 프로젝트에서 `GameplayEffectContainers`를 구현하지 않기로 선택했지만, 그것들을 조사하고 프로젝트에 추가하는 것을 고려하는 것이 좋습니다.

To access the `GESpecs` inside of the `GameplayEffectContainers` to do things like adding `SetByCallers`, break the `FGameplayEffectContainer` and access the `GESpec` reference by its index in the array of `GESpecs`. This requires that you know the index ahead of time of the `GESpec` that you want to access.

`GameplayEffectContainers` 내부의 `GESpecs`에 액세스하여 `SetByCallers` 추가와 같은 작업을 수행하려면 `FGameplayEffectContainer`를 중단하고 `GESpecs` 배열의 인덱스로 `GESpec` 참조에 액세스합니다. 이를 위해서는 액세스하려는 `GESpec`의 인덱스를 미리 알고 있어야 합니다.

![SetByCaller with a GameplayEffectContainer](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainers` also contain an optional efficient means of [targeting](#concepts-targeting-containers).

'GameplayEffectContainers'에는 선택적인 효율적인 타겟팅 수단도 포함되어 있습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga"></a>
### 4.6 Gameplay Abilities

<a name="concepts-ga-definition"></a>
#### 4.6.1 Gameplay Ability Definition
[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html) (`GA`) are any actions or skills that an `Actor` can do in the game. More than one `GameplayAbility` can be active at one time for example sprinting and shooting a gun. These can be made in Blueprint or C++.

'GameplayAbilities'('GA')는 '액터'가 게임에서 할 수 있는 모든 작업이나 기술입니다. 예를 들어 전력 질주 및 총 쏘는 것과 같이 한 번에 둘 이상의 'GameplayAbility'를 활성화할 수 있습니다. 블루프린트나 C++로 만들 수 있습니다.

Examples of `GameplayAbilities`:
* Jumping
* Sprinting
* Shooting a gun
* Passively blocking an attack every X number of seconds
* Using a potion
* Opening a door
* Collecting a resource
* Constructing a building

'GameplayAbilities'의 예:
* 점프
* 전력 질주
* 총을 쏘다
* X초마다 공격을 수동적으로 차단
* 물약 사용
* 문을 여는 것
* 자원 수집
* 건물 건설

Things that should not be implemented with `GameplayAbilities`:
* Basic movement input
* Some interactions with UIs - Don't use a `GameplayAbility` to purchase an item from a store.

`GameplayAbilities`로 구현하면 안 되는 것들:
* 기본 이동 입력
* UI와의 일부 상호 작용 - 상점에서 아이템을 구매하기 위해 'GameplayAbility'를 사용하지 마십시오.

These are not rules, just my recommendations. Your design and implementations may vary.

이것은 규칙이 아니라 제 권장 사항일 뿐입니다. 디자인과 구현은 다를 수 있습니다.

`GameplayAbilities` come with default functionality to have a level to modify the amount of change to attributes or to change the `GameplayAbility's` functionality.

'GameplayAbilities'에는 속성 변경 정도를 수정하거나 'GameplayAbility' 기능을 변경하는 수준이 있는 기본 기능이 함께 제공됩니다.

`GameplayAbilities` run on the owning client and/or the server depending on the [`Net Execution Policy`](#concepts-ga-net) but not simulated proxies. The `Net Execution Policy` determines if a `GameplayAbility` will be locally [predicted](#concepts-p). They include default behavior for [optional cost and cooldown `GameplayEffects`](#concepts-ga-commit). `GameplayAbilities` use [`AbilityTasks`](#concepts-at) for actions that happen over time like waiting for an event, waiting for an attribute change, waiting for players to choose a target, or moving a `Character` with `Root Motion Source`. **Simulated clients will not run `GameplayAbilities`**. Instead, when the server runs the ability, anything that visually needs to play on the simulated proxies (like animation montages) will be replicated or RPC'd through `AbilityTasks` or [`GameplayCues`](#concepts-gc) for cosmetic things like sounds and particles.

`GameplayAbilities`는 `Net Execution Policy`에 따라 소유 클라이언트 및/또는 서버에서 실행되지만 시뮬레이션된 프록시는 실행되지 않습니다. 'Net Execution Policy'는 'GameplayAbility'가 로컬에서 예측되는지 여부를 결정합니다. 여기에는 선택적 비용 및 쿨다운 `GameplayEffects`에 대한 기본 동작이 포함됩니다. 'GameplayAbilities'는 이벤트를 기다리거나, 속성 변경을 기다리거나, 플레이어가 대상을 선택하기를 기다리거나, 'Root Motion Source'로 '캐릭터'를 이동하는 것과 같이 시간이 지남에 따라 발생하는 작업에 대해 'AbilityTasks'를 사용합니다. **시뮬레이트된 클라이언트는 `GameplayAbilities`를 실행하지 않습니다**. 그 대신, 서버가 능력을 실행할 때 시뮬레이션된 프록시에서 시각적으로 재생해야 하는 모든 것(애니메이션 몽타주 등)은 'AbilityTasks' 또는 'GameplayCues'를 통해 사운드 및 파티클과 같은 외형적인 것들이 복제되거나 RPC'됩니다

All `GameplayAbilities` will have their `ActivateAbility()` function overriden with your gameplay logic. Additional logic can be added to `EndAbility()` that runs when the `GameplayAbility` completes or is canceled.

 모든 'GameplayAbilities'에는 'ActivateAbility()' 기능이 게임플레이 로직으로 재정의됩니다. `GameplayAbility`가 완료되거나 취소될 때 실행되는 `EndAbility()`에 로직을 추가할 수 있습니다.

Flowchart of a simple `GameplayAbility`:

![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)


Flowchart of a more complex `GameplayAbility`:
![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

Complex abilities can be implemented using multiple `GameplayAbilities` that interact (activate, cancel, etc) with each other.



<a name="concepts-ga-definition-reppolicy"></a>
##### 4.6.1.1 Replication Policy
Don't use this option. The name is misleading and you don't need it. [`GameplayAbilitySpecs`](#concepts-ga-spec) are replicated from the server to the owning client by default. As mentioned above, **`GameplayAbilities` don't run on simulated proxies**. They use `AbilityTasks` and `GameplayCues` to replicate or RPC visual changes to the simulated proxies. Dave Ratti from Epic has stated his desire to [remove this option in the future](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89).

이 옵션을 사용하지 마십시오. 이름은 오해의 소지가 있으며 필요하지 않습니다. 'GameplayAbilitySpecs'는 기본적으로 서버에서 소유 클라이언트로 복제됩니다. 위에서 언급했듯이 **`GameplayAbilities`는 시뮬레이션된 프록시에서 실행되지 않습니다**. 그들은 `AbilityTasks` 및 `GameplayCues`를 사용하여 시뮬레이션된 프록시에 시각적 변경 사항을 복제하거나 RPC합니다. Epic의 Dave Ratti는 앞으로 이 옵션을 제거하고 싶다고 말했습니다.

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 Server Respects Remote Ability Cancellation
This option causes trouble more often than not. It means if the client's `GameplayAbility` ends either due to cancellation or natural completion, it will force the server's version to end whether it completed or not. The latter issue is the important one, especially for locally predicted `GameplayAbilities` used by players with high latencies. Generally you will want to disable this option.

이 옵션은 더 자주 문제를 일으킵니다. 클라이언트의 'GameplayAbility'가 취소 또는 자연 완료로 인해 종료되면 완료 여부에 관계없이 서버 버전을 강제 종료한다는 의미입니다. 후자의 문제는 특히 지연 시간이 긴 플레이어가 사용하는 로컬 예측 'GameplayAbilities'의 경우 중요한 문제입니다. 일반적으로 이 옵션을 비활성화할 수 있습니다.

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 Replicate Input Directly
Setting this option will always replicate input press and release events to the server. Epic recommends not using this and instead relying on the `Generic Replicated Events` that are built into the existing input related [`AbilityTasks`](#concepts-at) if you have your [input bound to your `ASC`](#concepts-ga-input).

이 옵션을 설정하면 입력 프레스 및 릴리스 이벤트가 항상 서버에 복제됩니다. Epic은 이것을 사용하지 않고 대신 기존 입력 관련 'AbilityTasks'에 내장된 'Generic Replicated Events'에 의존할 것을 권장합니다.

Epic's comment:
```c++
/** Direct Input state replication. These will be called if bReplicateInputDirectly is true on the ability and is generally not a good thing to use. (Instead, prefer to use Generic Replicated Events). */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 Binding Input to the ASC
The `ASC` allows you to directly bind input actions to it and assign those inputs to `GameplayAbilities` when you grant them. Input actions assigned to `GameplayAbilities` automatically activate those `GameplayAbilities` when pressed if the `GameplayTag` requirements are met. Assigned input actions are required to use the built-in `AbilityTasks` that respond to input.

'ASC'를 사용하면 입력 작업을 직접 바인딩하고 부여할 때 해당 입력을 'GameplayAbilities'에 할당할 수 있습니다. 'GameplayAbilities'에 할당된 입력 작업은 'GameplayTag' 요구 사항이 충족되는 경우 눌렀을 때 해당 'GameplayAbilities'를 자동으로 활성화합니다. 입력에 응답하는 내장 'AbilityTasks'를 사용하려면 할당된 입력 작업이 필요합니다.

In addition to input actions assigned to activate `GameplayAbilities`, the `ASC` also accepts generic `Confirm` and `Cancel` inputs. These special inputs are used by `AbilityTasks` for confirming things like [`Target Actors`](#concepts-targeting-actors) or canceling them.

'GameplayAbilities'를 활성화하기 위해 할당된 입력 작업 외에도 'ASC'는 일반 '확인' 및 '취소' 입력도 허용합니다. 이러한 특수 입력은 '대상 액터'와 같은 것을 확인하거나 취소하기 위해 'AbilityTasks'에서 사용됩니다.

To bind input to an `ASC`, you must first create an enum that translates the input action name to a byte. The enum name must match exactly to the name used for the input action in the project settings. The `DisplayName` does not matter.

입력을 'ASC'에 바인딩하려면 먼저 입력 작업 이름을 바이트로 변환하는 열거형을 만들어야 합니다. 열거형 이름은 프로젝트 설정에서 입력 작업에 사용된 이름과 정확히 일치해야 합니다. 'DisplayName'은 중요하지 않습니다.


From the Sample Project:
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

If your `ASC` lives on the `Character`, then in `SetupPlayerInputComponent()` include the function for binding to the `ASC`:
`ASC`가 `Character`에 있는 경우 `SetupPlayerInputComponent()`에 `ASC`에 바인딩하는 함수를 포함합니다.

```c++
// Bind to AbilitySystemComponent
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"), FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

If your `ASC` lives on the `PlayerState`, there is a potential race condition inside of `SetupPlayerInputComponent()` where the `PlayerState` may not have replicated to the client yet. Therefore, I recommend attempting to bind to input in `SetupPlayerInputComponent()` and `OnRep_PlayerState()`. `OnRep_PlayerState()` is not sufficient by itself because there could be a case where the `Actor's` `InputComponent` could be null when `PlayerState` replicates before the `PlayerController` tells the client to call `ClientRestart()` which creates the `InputComponent`. The Sample Project demonstrates attempting to bind in both locations with a boolean gating the process so it only actually binds the input once.

`ASC`가 `PlayerState`에 있는 경우 `PlayerState`가 아직 클라이언트에 복제되지 않았을 수 있는 `SetupPlayerInputComponent()` 내부에 잠재적인 경쟁 조건이 있습니다. 따라서 `SetupPlayerInputComponent()` 및 `OnRep_PlayerState()`에서 입력에 바인딩을 시도하는 것이 좋습니다. 'PlayerController'가 클라이언트에게 '입력 구성 요소'. 샘플 프로젝트는 부울 게이팅 프로세스를 사용하여 두 위치 모두에서 바인딩을 시도하여 실제로 입력을 한 번만 바인딩하는 것을 보여줍니다.

**Note:** In the Sample Project `Confirm` and `Cancel` in the enum don't match the input action names in the project settings (`ConfirmTarget` and `CancelTarget`), but we supply the mapping between them in `BindAbilityActivationToInputComponent()`. These are special since we supply the mapping and they don't have to match, but they can match. All other inputs in the enum must match the input action names in the project settings.

**참고:** 샘플 프로젝트에서 열거형의 'Confirm' 및 'Cancel'은 프로젝트 설정('ConfirmTarget' 및 'CancelTarget')의 입력 작업 이름과 일치하지 않지만, `BindAbilityActivationToInputComponent()`. 이것은 매핑을 제공하고 일치할 필요는 없지만 일치할 수 있기 때문에 특별합니다. 열거형의 다른 모든 입력은 프로젝트 설정의 입력 작업 이름과 일치해야 합니다.

For `GameplayAbilities` that will only ever be activated by one input (they will always exist in the same "slot" like a MOBA), I prefer to add a variable to my `UGameplayAbility` subclass where I can define their input. I can then read this from the `ClassDefaultObject` when granting the ability.

하나의 입력으로만 활성화되는 `GameplayAbilities`의 경우(MOBA와 같은 동일한 "슬롯"에 항상 존재함) 입력을 정의할 수 있는 `UGameplayAbility` 서브클래스에 변수를 추가하는 것을 선호합니다. 그런 다음 능력을 부여할 때 `ClassDefaultObject`에서 이것을 읽을 수 있습니다.

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 Binding to Input without Activating Abilities
If you don't want your `GameplayAbilities` to automatically activate when an input is pressed but still bind them to input to use with `AbilityTasks`, you can add a new bool variable to your `UGameplayAbility` subclass, `bActivateOnInput`, that defaults to `true` and override `UAbilitySystemComponent::AbilityLocalInputPressed()`.

입력을 눌렀을 때 `GameplayAbilities`가 자동으로 활성화되는 것을 원하지 않지만 `AbilityTasks`와 함께 사용할 입력에 바인딩하려면 `UGameplayAbility` 서브클래스인 `bActivateOnInput`에 새 bool 변수를 추가하면 됩니다. 기본값은 'true'이고 'UAbilitySystemComponent::AbilityLocalInputPressed()'를 재정의합니다.

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

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 4.6.3 Granting Abilities
Granting a `GameplayAbility` to an `ASC` adds it to the `ASC's` list of `ActivatableAbilities` allowing it to activate the `GameplayAbility` at will if it meets the [`GameplayTag` requirements](#concepts-ga-tags).

'GameplayAbility'를 'ASC'에 부여하면 'ActivatableAbilities'의 'ASC' 목록에 추가되어 'GameplayTag' 요구 사항을 충족하는 경우 'GameplayAbility'를 마음대로 활성화할 수 있습니다.

We grant `GameplayAbilities` on the server which then automatically replicates the [`GameplayAbilitySpec`](#concepts-ga-spec) to the owning client. Other clients / simulated proxies do not receive the `GameplayAbilitySpec`.

서버에 `GameplayAbilities`를 부여하면 자동으로 `GameplayAbilitySpec`을 소유 클라이언트에 복제합니다. 다른 클라이언트/시뮬레이트된 프록시는 'GameplayAbilitySpec'을 수신하지 않습니다.

The Sample Project stores a `TArray<TSubclassOf<UGDGameplayAbility>>` on the `Character` class that it reads from and grants when the game starts:
샘플 프로젝트는 게임이 시작될 때 읽고 부여하는 `Character` 클래스에 `TArray<TSubclassOf<UGDGameplayAbility>>`를 저장합니다.
```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// Grant abilities, but only on the server	
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->CharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->CharacterAbilitiesGiven = true;
}
```

When granting these `GameplayAbilities`, we're creating `GameplayAbilitySpecs` with the `UGameplayAbility` class, the ability level, the input that it is bound to, and the `SourceObject` or who gave this `GameplayAbility` to this `ASC`.

이 `GameplayAbilities`를 부여할 때 `UGameplayAbility` 클래스, 능력 수준, 바인딩된 입력, `SourceObject` 또는 이 `GameplayAbility`를 이 `ASC`에 준 사람으로 `GameplayAbilitySpecs`를 생성합니다. 

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 Activating Abilities
If a `GameplayAbility` is assigned an input action, it will be automatically activated if the input is pressed and it meets its `GameplayTag` requirements. This may not always be the desirable way to activate a `GameplayAbility`. The `ASC` provides four other methods of activating `GameplayAbilities`: by `GameplayTag`, `GameplayAbility` class, `GameplayAbilitySpec` handle, and by an event. Activating a `GameplayAbility` by event allows you to [pass in a payload of data with the event](#concepts-ga-data).

'GameplayAbility'에 입력 작업이 할당된 경우 입력을 누르고 'GameplayTag' 요구 사항을 충족하면 자동으로 활성화됩니다. 이것이 'GameplayAbility'를 활성화하는 데 항상 바람직한 방법은 아닙니다. `ASC`는 `GameplayAbilities`를 활성화하는 네 가지 다른 방법을 제공합니다: `GameplayTag`, `GameplayAbility` 클래스, `GameplayAbilitySpec` 핸들 및 이벤트. 이벤트로 'GameplayAbility'를 활성화하면 이벤트와 함께 데이터 페이로드를 전달할 수 있습니다.

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec);
```
To activate a `GameplayAbility` by event, the `GameplayAbility` must have its `Triggers` set up in the `GameplayAbility`. Assign a `GameplayTag` and pick an option for `GameplayEvent`. To send the event, use the function `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`. Activating a `GameplayAbility` by event allows you to pass in a payload with data.

이벤트별로 'GameplayAbility'를 활성화하려면 'GameplayAbility'의 'Triggers'가 'GameplayAbility'에 설정되어 있어야 합니다. 'GameplayTag'를 할당하고 'GameplayEvent'에 대한 옵션을 선택합니다. 이벤트를 보내려면 'UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)' 함수를 사용하세요. 이벤트로 'GameplayAbility'를 활성화하면 데이터가 포함된 페이로드를 전달할 수 있습니다.

`GameplayAbility` `Triggers` also allow you to activate the `GameplayAbility` when a `GameplayTag` is added or removed.

`GameplayAbility` `Triggers`를 사용하면 `GameplayTag`가 추가되거나 제거될 때 `GameplayAbility`를 활성화할 수도 있습니다.

**Note:** When activating a `GameplayAbility` from event in Blueprint, you must use the `ActivateAbilityFromEvent` node and the standard `ActivateAbility` node **cannot exist** in your graph. If the `ActivateAbility` node exists, it will always be called over the `ActivateAbilityFromEvent` node.

**참고:** 블루프린트의 이벤트에서 'GameplayAbility'를 활성화할 때 그래프에 'ActivateAbilityFromEvent' 노드와 표준 'ActivateAbility' 노드를 **존재할 수 없습니다**. 'ActivateAbility' 노드가 있으면 항상 'ActivateAbilityFromEvent' 노드를 통해 호출됩니다.

**Note:** Don't forget to call `EndAbility()` when the `GameplayAbility` should terminate unless you have a `GameplayAbility` that will always run like a passive ability.

**참고:** 항상 패시브 능력처럼 실행되는 `GameplayAbility`가 없는 한 `GameplayAbility`가 종료되어야 할 때 `EndAbility()`를 호출하는 것을 잊지 마십시오.

Activation sequence for **locally predicted** `GameplayAbilities`:
1. **Owning client** calls `TryActivateAbility()`
1. Calls `InternalTryActivateAbility()`
1. Calls `CanActivateAbility()` and returns whether `GameplayTag` requirements are met, if the `ASC` can afford the cost, if the `GameplayAbility` is not on cooldown, and if no other instances are currently active
1. Calls `CallServerTryActivateAbility()` and passes it the `Prediction Key` that it generates
1. Calls `CallActivateAbility()`
1. Calls `PreActivate()` Epic refers to this as "boilerplate init stuff"
1. Calls `ActivateAbility()` finally activating the ability

**로컬 예측** `GameplayAbilities`의 활성화 순서:
1. **소유 클라이언트**가 `TryActivateAbility()`를 호출합니다.
1. `InternalTryActivateAbility()` 호출
1. 'CanActivateAbility()'를 호출하고 'GameplayTag' 요구 사항이 충족되었는지, 'ASC'가 비용을 감당할 수 있는지, 'GameplayAbility'가 쿨다운 상태가 아니며, 현재 활성화된 다른 인스턴스가 없는 경우를 반환합니다.
1. 'CallServerTryActivateAbility()'를 호출하고 생성한 '예측 키'를 전달합니다.
1. 'CallActivateAbility()' 호출
1. `PreActivate()` 호출 Epic은 이것을 "boilerplate init stuff"라고 부릅니다.
1. 'ActivateAbility()'를 호출하여 마침내 능력을 활성화합니다.


**Server** receives `CallServerTryActivateAbility()`
1. Calls `ServerTryActivateAbility()`
1. Calls `InternalServerTryActivateAbility()` 
1. Calls `InternalTryActivateAbility()`
1. Calls `CanActivateAbility()` and returns whether `GameplayTag` requirements are met, if the `ASC` can afford the cost, if the `GameplayAbility` is not on cooldown, and if no other instances are currently active
1. Calls `ClientActivateAbilitySucceed()` if successful telling it to update its `ActivationInfo` that its activation was confirmed by the server and broadcasting the `OnConfirmDelegate` delegate. This is not the same as input confirmation.
1. Calls `CallActivateAbility()`
1. Calls `PreActivate()` Epic refers to this as "boilerplate init stuff"
1. Calls `ActivateAbility()` finally activating the ability

**서버**는 `CallServerTryActivateAbility()`를 수신합니다.
1. `ServerTryActivateAbility()` 호출
1. `InternalServerTryActivateAbility()` 호출
1. `InternalTryActivateAbility()` 호출
1. 'CanActivateAbility()'를 호출하고 'GameplayTag' 요구 사항이 충족되었는지, 'ASC'가 비용을 감당할 수 있는지, 'GameplayAbility'가 쿨다운 상태가 아니며, 현재 활성화된 다른 인스턴스가 없는 경우를 반환합니다.
1. 서버에서 활성화가 확인되었음을 알리고 `OnConfirmDelegate` 대리자를 브로드캐스트하는 `ActivationInfo` 업데이트에 성공하면 `ClientActivateAbilitySucceed()`를 호출합니다. 이것은 입력 확인과 동일하지 않습니다.
1. 'CallActivateAbility()' 호출
1. `PreActivate()` 호출 Epic은 이것을 "boilerplate init stuff"라고 부릅니다.
1. 'ActivateAbility()'를 호출하여 마침내 능력을 활성화합니다.


If at any time the server fails to activate, it will call `ClientActivateAbilityFailed()`, immediately terminating the client's `GameplayAbility` and undoing any predicted changes.

언제든지 서버가 활성화에 실패하면 'ClientActivateAbilityFailed()'를 호출하여 클라이언트의 'GameplayAbility'를 즉시 종료하고 예측된 변경 사항을 취소합니다.
<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 Passive Abilities
To implement passive `GameplayAbilities` that automatically activate and run continuously, override `UGameplayAbility::OnAvatarSet()` which is automatically called when a `GameplayAbility` is granted and the `AvatarActor` is set and call `TryActivateAbility()`.

자동으로 활성화되어 지속적으로 실행되는 패시브 `GameplayAbilities`를 구현하려면 `GameplayAbility`가 부여되고 `AvatarActor`가 설정될 때 자동으로 호출되는 `UGameplayAbility::OnAvatarSet()`를 재정의하고 `TryActivateAbility()`를 호출합니다.

I recommend adding a `bool` to your custom `UGameplayAbility` class specifying if the `GameplayAbility` should be activated when granted. The Sample Project does this for its passive armor stacking ability.

부여될 때 'GameplayAbility'가 활성화되어야 하는지 여부를 지정하는 커스텀 'UGameplayAbility' 클래스에 'bool'을 추가하는 것이 좋습니다. 샘플 프로젝트는 패시브 아머 스택 기능을 위해 이 작업을 수행합니다.

패시브 `GameplayAbilities`에는 일반적으로 `서버 전용`의 `Net Execution Policy`가 있습니다.
Passive `GameplayAbilities` will typically have a [`Net Execution Policy`](#concepts-ga-net) of `Server Only`.

패시브 `GameplayAbilities`에는 일반적으로 `서버 전용`의 `Net Execution Policy`가 있습니다.

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (ActivateAbilityOnGranted)
	{
		bool ActivatedAbility = ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

Epic describes this function as the correct place to initiate passive abilities and to do `BeginPlay` type things.

Epic은 이 기능을 패시브 능력을 시작하고 'BeginPlay' 유형의 작업을 수행하는 올바른 위치라고 설명합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>
#### 4.6.5 Canceling Abilities
To cancel a `GameplayAbility` from within, you call `CancelAbility()`. This will call `EndAbility()` and set its `WasCancelled` parameter to true.

내부에서 `GameplayAbility`를 취소하려면 `CancelAbility()`를 호출합니다. 이것은 `EndAbility()`를 호출하고 `WasCancelled` 매개변수를 true로 설정합니다.

To cancel a `GameplayAbility` externally, the `ASC` provides a few functions:

'GameplayAbility'를 외부에서 취소하기 위해 'ASC'는 다음과 같은 몇 가지 기능을 제공합니다.

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

**Note:** I have found that `CancelAllAbilities` doesn't seem to work right if you have a `Non-Instanced` `GameplayAbilities`. It seems to hit the `Non-Instanced` `GameplayAbility` and give up. `CancelAbilities` can handle `Non-Instanced` `GameplayAbilities` better and that is what the Sample Project uses (Jump is a non-instanced `GameplayAbility`). Your mileage may vary.

**참고:** `Non-Instanced` `GameplayAbilities`가 있는 경우 `CancelAllAbilities`가 제대로 작동하지 않는 것으로 나타났습니다. 'Non-Instanced' 'GameplayAbility'를 치고 포기하는 것 같습니다. `CancelAbilities`는 `Non-Instanced` `GameplayAbilities`를 더 잘 처리할 수 있으며 이것이 샘플 프로젝트가 사용하는 것입니다(Jump는 인스턴스가 아닌 `GameplayAbility`임). 귀하의 마일리지가 다를 수 있습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 Getting Active Abilities
Beginners often ask "How can I get the active ability?" perhaps to set variables on it or to cancel it. More than one `GameplayAbility` can be active at a time so there is no one "active ability". Instead, you must search through an `ASC's` list of `ActivatableAbilities` (granted `GameplayAbilities` that the `ASC` owns) and find the one matching the [`Asset` or `Granted` `GameplayTag`](#concepts-ga-tags) that you are looking for.

초보자들은 종종 "액티브 능력을 어떻게 얻을 수 있습니까?"라고 묻습니다. 변수를 설정하거나 취소할 수 있습니다. 한 번에 둘 이상의 'GameplayAbility'가 활성화될 수 있으므로 하나의 "활성화된 능력"이 없습니다. 대신 `ActivatableAbilities`의 `ASC` 목록(`ASC`이 소유한 부여된 `GameplayAbilities`)을 검색하여 찾고 있는 `Asset` 또는 `Granted` `GameplayTag`와 일치하는 항목을 찾아야 합니다.

`UAbilitySystemComponent::GetActivatableAbilities()` returns a `TArray<FGameplayAbilitySpec>` for you to iterate over.

`UAbilitySystemComponent::GetActivatableAbilities()`는 반복할 `TArray<FGameplayAbilitySpec>`을 반환합니다.

The `ASC` also has another helper function that takes in a `GameplayTagContainer` as a parameter to assist in searching instead of manually iterating over the list of `GameplayAbilitySpecs`. The `bOnlyAbilitiesThatSatisfyTagRequirements` parameter will only return `GameplayAbilitySpecs` that satisfy their `GameplayTag` requirements and could be activated right now. For example, you could have two basic attack `GameplayAbilities`, one with a weapon and one with bare fists, and the correct one activates depending on if a weapon is equipped setting the `GameplayTag` requirement. See Epic's comment on the function for more information.

'ASC'에는 'GameplayAbilitySpecs' 목록을 수동으로 반복하는 대신 검색을 지원하는 매개변수로 'GameplayTagContainer'를 사용하는 또 다른 도우미 기능도 있습니다. `bOnlyAbilitiesThatSatisfyTagRequirements` 매개변수는 `GameplayTag` 요구 사항을 충족하고 지금 활성화할 수 있는 `GameplayAbilitySpecs`만 반환합니다. 예를 들어, 두 가지 기본 공격 'GameplayAbilities'가 있을 수 있습니다. 하나는 무기를 들고 다른 하나는 맨주먹으로, 올바른 공격은 무기 장착 여부에 따라 'GameplayTag' 요구 사항을 설정하여 활성화됩니다. 자세한 내용은 해당 기능에 대한 에픽의 설명을 참조하세요.

```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

Once you get the `FGameplayAbilitySpec` that you are looking for, you can call `IsActive()` on it.

찾고 있는 `FGameplayAbilitySpec`을 얻으면 `IsActive()`를 호출할 수 있습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 4.6.7 Instancing Policy
A `GameplayAbility's` `Instancing Policy` determines if and how the `GameplayAbility` is instanced when activated.

'GameplayAbility'의 '인스턴싱 정책'은 'GameplayAbility'가 활성화될 때 인스턴스화되는지 여부와 방법을 결정합니다.

| `Instancing Policy`     | Description                                                                                      | Example of when to use                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Instanced Per Actor     | Each `ASC` only has one instance of the `GameplayAbility` that is reused between activations.    | This will probably be the `Instancing Policy` that you use the most. You can use it for any ability and provides persistence between activations. The designer is responsible for manually resetting any variables between activations that need it.                                                                                                                                                               |
| Instanced Per Execution | Every time a `GameplayAbility` is activated, a new instance of the `GameplayAbility` is created. | The benefit of these `GameplayAbilities` is that the variables are reset everytime you activate. These provide worse performance than `Instanced Per Actor` since they will spawn new `GameplayAbilities` every time they activate. The Sample Project does not use any of these.                                                                                                                                 |
| Non-Instanced           | The `GameplayAbility` operates on its `ClassDefaultObject`. No instances are created.            | This has the best performance of the three but is the most restrictive in what can be done with it. `Non-Instanced` `GameplayAbilities` cannot store state, meaning no dynamic variables and no binding to `AbilityTask` delegates. The best place to use them is for frequently used simple abilities like minion basic attacks in a MOBA or RTS. The Sample Project's Jump `GameplayAbility` is `Non-Instanced`. |

| `인스턴싱 정책` | 설명 | 사용 예 |
| ----------------------- | -------------------------------------------------- ---------------------------------------------- | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -- |
| 액터당 인스턴스화 | 각 'ASC'에는 활성화 간에 재사용되는 'GameplayAbility' 인스턴스가 하나만 있습니다. | 이것은 아마도 가장 많이 사용하는 '인스턴싱 정책'일 것입니다. 모든 기능에 사용할 수 있으며 활성화 간에 지속성을 제공합니다. 디자이너는 필요한 활성화 사이에 모든 변수를 수동으로 재설정할 책임이 있습니다. |
| 실행당 인스턴스화 | 'GameplayAbility'가 활성화될 때마다 'GameplayAbility'의 새 인스턴스가 생성됩니다. | 이러한 'GameplayAbilities'의 이점은 활성화할 때마다 변수가 재설정된다는 것입니다. 이들은 활성화할 때마다 새로운 'GameplayAbilities'를 생성하기 때문에 '인스턴싱된 액터'보다 성능이 떨어집니다. 샘플 프로젝트는 이들 중 어느 것도 사용하지 않습니다. |
| 인스턴스화되지 않음 | 'GameplayAbility'는 'ClassDefaultObject'에서 작동합니다. 인스턴스가 생성되지 않습니다. | 이것은 세 가지 중 최고의 성능을 제공하지만 수행할 수 있는 작업에서 가장 제한적입니다. `Non-Instanced` `GameplayAbilities`는 상태를 저장할 수 없습니다. 즉, 동적 변수가 없고 `AbilityTask` 대리자에 대한 바인딩이 없습니다. 그것들을 사용하는 가장 좋은 장소는 MOBA 또는 RTS의 미니언 기본 공격과 같이 자주 사용되는 간단한 능력입니다. 샘플 프로젝트의 점프 'GameplayAbility'는 'Non-Instanced'입니다. |



**[⬆ Back to Top](#table-of-contents)**



<a name="concepts-ga-net"></a>
#### 4.6.8 Net Execution Policy
A `GameplayAbility's` `Net Execution Policy` determines who runs the `GameplayAbility` and in what order.

| `Net Execution Policy` | Description                                                                                                                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Local Only`           | The `GameplayAbility` is only run on the owning client. This could be useful for abilities that only make local cosmetic changes. Single player games should use `Server Only`.                                     |
| `Local Predicted`      | `Local Predicted` `GameplayAbilities` activate first on the owning client and then on the server. The server's version will correct anything that the client predicted incorrectly. See [Prediction](#concepts-p). |
| `Server Only`          | The `GameplayAbility` is only run on the server. Passive `GameplayAbilities` will typically be `Server Only`. Single player games should use this.                                                                  |
| `Server Initiated`     | `Server Initiated` `GameplayAbilities` activate first on the server and then on the owning client. I personally haven't used these much if any.                                                                     | 

| `순 실행 정책` | 설명 |
| ---------------------- | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- ----------- |
| `로컬 전용` | 'GameplayAbility'는 소유 클라이언트에서만 실행됩니다. 이것은 국부적으로 외형을 변경하는 능력에만 유용할 수 있습니다. 싱글 플레이어 게임은 '서버 전용'을 사용해야 합니다. |
| `로컬 예측` | `Local Predicted` `GameplayAbilities`는 먼저 소유 클라이언트에서 활성화된 다음 서버에서 활성화됩니다. 서버 버전은 클라이언트가 잘못 예측한 모든 것을 수정합니다. 예측을 참조하십시오. |
| `서버 전용` | 'GameplayAbility'는 서버에서만 실행됩니다. 패시브 `GameplayAbilities`는 일반적으로 `서버 전용`입니다. 싱글 플레이어 게임은 이것을 사용해야 합니다. |
| `서버 시작` | `Server Initiated` `GameplayAbilities`는 먼저 서버에서 활성화된 다음 소유 클라이언트에서 활성화됩니다. 나는 개인적으로 이것을 많이 사용하지 않았습니다. |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 Ability Tags
`GameplayAbilities` come with `GameplayTagContainers` with built-in logic. None of these `GameplayTags` are replicated.

'GameplayAbilities'는 로직이 내장된 'GameplayTagContainers'와 함께 제공됩니다. 이러한 'GameplayTags'는 복제되지 않습니다.

| `GameplayTag Container`     | Description                                                                                                                                                                                   |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Ability Tags`              | `GameplayTags` that the `GameplayAbility` owns. These are just `GameplayTags` to describe the `GameplayAbility`.                                                                              |
| `Cancel Abilities with Tag` | Other `GameplayAbilities` that have these `GameplayTags` in their `Ability Tags` will be canceled when this `GameplayAbility` is activated.                                                   |
| `Block Abilities with Tag`  | Other `GameplayAbilities` that have these `GameplayTags` in their `Ability Tags` are blocked from activating while this `GameplayAbility` is active.                                          |
| `Activation Owned Tags`     | These `GameplayTags` are given to the `GameplayAbility's` owner while this `GameplayAbility` is active. Remember these are not replicated.                                                    |
| `Activation Required Tags`  | This `GameplayAbility` can only be activated if the owner has **all** of these `GameplayTags`.                                                                                                |
| `Activation Blocked Tags`   | This `GameplayAbility` cannot be activated if the owner has **any** of these `GameplayTags`.                                                                                                  |
| `Source Required Tags`      | This `GameplayAbility` can only be activated if the `Source` has **all** of these `GameplayTags`. The `Source` `GameplayTags` are only set if the `GameplayAbility` is triggered by an event. |
| `Source Blocked Tags`       | This `GameplayAbility` cannot be activated if the `Source` has **any** of these `GameplayTags`. The `Source` `GameplayTags` are only set if the `GameplayAbility` is triggered by an event.   |
| `Target Required Tags`      | This `GameplayAbility` can only be activated if the `Target` has **all** of these `GameplayTags`. The `Target` `GameplayTags` are only set if the `GameplayAbility` is triggered by an event. |
| `Target Blocked Tags`       | This `GameplayAbility` cannot be activated if the `Target` has **any** of these `GameplayTags`. The `Target` `GameplayTags` are only set if the `GameplayAbility` is triggered by an event.   |

| `GameplayTag 컨테이너` | 설명 |
| --------------------------- | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- --------------------------------------- |
| `능력 태그` | `GameplayAbility`가 소유한 `GameplayTags`. 이는 'GameplayAbility'를 설명하는 'GameplayTags'일 뿐입니다. |
| `태그로 기능 취소` | 이 'GameplayAbility'가 활성화되면 'Ability Tags'에 이러한 'GameplayTags'가 있는 다른 'GameplayAbilities'가 취소됩니다. |
| `태그로 능력 차단` | '능력 태그'에 이러한 'GameplayTags'가 있는 다른 'GameplayAbilities'는 이 'GameplayAbility'가 활성화되어 있는 동안 활성화되지 않습니다. |
| `활성화 소유 태그` | 이 'GameplayTags'는 'GameplayAbility'가 활성화되어 있는 동안 'GameplayAbility' 소유자에게 제공됩니다. 이것들은 복제되지 않는다는 것을 기억하십시오. |
| `활성화 필수 태그` | 이 `GameplayAbility`는 소유자가 이 `GameplayTags`를 **모두** 가지고 있는 경우에만 활성화할 수 있습니다. |
| `활성화 차단 태그` | 소유자에게 이러한 'GameplayTags'가 **하나라도** 있으면 이 'GameplayAbility'를 활성화할 수 없습니다. |
| `소스 필수 태그` | 이 'GameplayAbility'는 '소스'에 이러한 'GameplayTags'가 **모두** 있는 경우에만 활성화할 수 있습니다. `Source` `GameplayTags`는 `GameplayAbility`가 이벤트에 의해 트리거된 경우에만 설정됩니다. |
| `소스 차단 태그` | '소스'에 이러한 'GameplayTags'가 **하나라도** 있으면 이 'GameplayAbility'를 활성화할 수 없습니다. `Source` `GameplayTags`는 `GameplayAbility`가 이벤트에 의해 트리거된 경우에만 설정됩니다. |
| `대상 필수 태그` | 이 `GameplayAbility`는 `Target`에 이러한 `GameplayTags`가 **모두** 있는 경우에만 활성화할 수 있습니다. `Target` `GameplayTags`는 `GameplayAbility`가 이벤트에 의해 트리거되는 경우에만 설정됩니다. |
| `대상 차단 태그` | 이 `GameplayAbility`는 `Target`에 이러한 `GameplayTags`가 **하나라도** 있으면 활성화할 수 없습니다. `Target` `GameplayTags`는 `GameplayAbility`가 이벤트에 의해 트리거되는 경우에만 설정됩니다. |



**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 Gameplay Ability Spec
A `GameplayAbilitySpec` exists on the `ASC` after a `GameplayAbility` is granted and defines the activatable `GameplayAbility` - `GameplayAbility` class, level, input bindings, and runtime state that must be kept separate from the `GameplayAbility` class.

'GameplayAbility'가 부여된 후 'ASC'에 'GameplayAbilitySpec'이 존재하며 활성화 가능한 'GameplayAbility' - 'GameplayAbility' 클래스, 레벨, 입력 바인딩, 런타임 상태를 정의하며 이는 'GameplayAbility' 클래스와 별도로 유지되어야 합니다.

When a `GameplayAbility` is granted on the server, the server replicates the `GameplayAbilitySpec` to the owning client so that she may activate it.

'GameplayAbility'가 서버에 부여되면 서버는 'GameplayAbilitySpec'을 소유 클라이언트에 복제하여 클라이언트가 활성화할 수 있도록 합니다.

Activating a `GameplayAbilitySpec` will create an instance (or not for `Non-Instanced` `GameplayAbilities`) of the `GameplayAbility` depending on its `Instancing Policy`.

'GameplayAbilitySpec'을 활성화하면 '인스턴싱 정책'에 따라 'GameplayAbility'의 인스턴스('Non-Instanced' 'GameplayAbilities'가 아닌 인스턴스)가 생성됩니다.
**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 Passing Data to Abilities
The general paradigm for `GameplayAbilities` is `Activate->Generate Data->Apply->End`. Sometimes you need to act on existing data. GAS provides a few options for getting external data into your `GameplayAbilities`:

'GameplayAbilities'의 일반적인 패러다임은 '활성화->데이터 생성->적용->종료'입니다. 때로는 기존 데이터에 대해 조치를 취해야 합니다. GAS는 외부 데이터를 `GameplayAbilities`로 가져오기 위한 몇 가지 옵션을 제공합니다.

| Method                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Activate `GameplayAbility` by Event             | Activate a `GameplayAbility` with an event containing a payload of data. The event's payload is replicated from client to server for local predicted `GameplayAbilities`. Use the two `Optional Object` or the [`TargetData`](#concepts-targeting-data) variables for arbitrary data that does not fit any of the existing variables. The downside to this is that it prevents you from activating the ability with an input bind. To activate a `GameplayAbility` by event, the `GameplayAbility` must have its `Triggers` set up in the `GameplayAbility`. Assign a `GameplayTag` and pick an option for `GameplayEvent`. To send the event, use the function `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`. |
| Use `WaitGameplayEvent` `AbilityTask`           | Use the `WaitGameplayEvent` `AbilityTask` to tell the `GameplayAbility` to listen for an event with payload data after it activates. The event payload and the process to send it is the same as activating `GameplayAbilities` by event. The downside to this is that events are not replicated by the `AbilityTask` and should only be used for `Local Only` and `Server Only` `GameplayAbilities`. You potentially could write your own `AbilityTask` that will replicate the event payload.                                                                                                                                                                                                                                                                                               |
| Use `TargetData`                                | A custom `TargetData` struct is a good way to pass arbitrary data between the client and server.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Store Data on the `OwnerActor` or `AvatarActor` | Use replicated variables stored on the `OwnerActor`, `AvatarActor`, or any other object that you can get a reference to. This method is the most flexible and will work with `GameplayAbilities` activated by input binds. However, it does not guarantee the data will be synchronized from replication at the time of use. You must ensure that ahead of time - meaning if you set a replicated variable and then immediately activate a `GameplayAbility` there is no guarantee the order that will happen on the receiver due to potential packet loss.                                                                                                                                                                                                                                   |

| 방법 | 설명 |
| ----------------------------------------------------------- | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- --------------- |
| 이벤트로 `GameplayAbility` 활성화 | 데이터 페이로드가 포함된 이벤트로 'GameplayAbility'를 활성화합니다. 이벤트의 페이로드는 로컬 예측 'GameplayAbilities'에 대해 클라이언트에서 서버로 복제됩니다. 두 개의 `Optional Object` 또는 `TargetData`를 사용하십시오. |
| `WaitGameplayEvent` `AbilityTask` 사용 | 'WaitGameplayEvent' 'AbilityTask'를 사용하여 'GameplayAbility'에 이벤트가 활성화된 후 페이로드 데이터가 있는 이벤트를 수신하도록 지시합니다. 이벤트 페이로드와 이를 보내는 과정은 이벤트로 'GameplayAbilities'를 활성화하는 것과 같습니다. 이것의 단점은 이벤트가 'AbilityTask'에 의해 복제되지 않고 'Local Only' 및 'Server Only' 'GameplayAbilities'에만 사용해야 한다는 것입니다. 이벤트 페이로드를 복제하는 고유한 'AbilityTask'를 작성할 수 있습니다. |
| `TargetData` 사용 | 사용자 정의 `TargetData` 구조체는 클라이언트와 서버 간에 임의의 데이터를 전달하는 좋은 방법입니다. |
| `OwnerActor` 또는 `AvatarActor`에 데이터 저장 | `OwnerActor`, `AvatarActor` 또는 참조할 수 있는 다른 개체에 저장된 복제된 변수를 사용합니다. 이 방법은 가장 유연하며 입력 바인딩에 의해 활성화된 'GameplayAbilities'와 함께 작동합니다. 그러나 사용 시 복제에서 데이터가 동기화된다는 보장은 없습니다. 사전에 확인해야 합니다. 즉, 복제된 변수를 설정한 다음 즉시 'GameplayAbility'를 활성화하면 잠재적인 패킷 손실로 인해 수신기에서 발생할 순서가 없다는 의미입니다. |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-commit"></a>
#### 4.6.12 Ability Cost and Cooldown
`GameplayAbilities` come with functionality for optional costs and cooldowns. Costs are predefined amounts of `Attributes` that the `ASC` must have in order to activate the `GameplayAbility` implemented with an `Instant` `GameplayEffect` ([`Cost GE`](#concepts-ge-cost)). Cooldowns are timers that prevent the reactivation of a `GameplayAbility` until it expires and is implemented with a `Duration` `GameplayEffect` ([`Cooldown GE`](#concepts-ge-cooldown)).

'GameplayAbilities'는 선택적 비용 및 재사용 대기시간을 위한 기능과 함께 제공됩니다. 비용은 `Instant` `GameplayEffect`(`비용 GE`)로 구현된 `GameplayAbility`를 활성화하기 위해 `ASC`가 가져야 하는 사전 정의된 `Attributes`의 양입니다. 재사용 대기시간은 만료될 때까지 `GameplayAbility`의 재활성화를 방지하는 타이머이며 `Duration` `GameplayEffect`(`Cooldown GE`)로 구현됩니다.

Before a `GameplayAbility` calls `UGameplayAbility::Activate()`, it calls `UGameplayAbility::CanActivateAbility()`. This function checks if the owning `ASC` can afford the cost (`UGameplayAbility::CheckCost()`) and ensures that the `GameplayAbility` is not on cooldown (`UGameplayAbility::CheckCooldown()`).

`GameplayAbility`가 `UGameplayAbility::Activate()`를 호출하기 전에 `UGameplayAbility::CanActivateAbility()`를 호출합니다. 이 함수는 소유한 `ASC`가 비용을 감당할 수 있는지 확인하고(`UGameplayAbility::CheckCost()`) `GameplayAbility`가 쿨다운 상태가 아닌지 확인합니다(`UGameplayAbility::CheckCooldown()`).

After a `GameplayAbility` calls `Activate()`, it can optionally commit the cost and cooldown at any time using `UGameplayAbility::CommitAbility()` which calls `UGameplayAbility::CommitCost()` and `UGameplayAbility::CommitCooldown()`. The designer may choose to call `CommitCost()` or `CommitCooldown()` separately if they shouldn't be committed at the same time. Committing cost and cooldown calls `CheckCost()` and `CheckCooldown()` one more time and is the last chance for the `GameplayAbility` to fail related to them. The owning `ASC's` `Attributes` could potentially change after a `GameplayAbility` is activated, failing to meet the cost at time of commit. Committing the cost and cooldown can be [locally predicted](#concepts-p) if the [prediction key](#concepts-p-key) is valid at the time of commit.

`GameplayAbility`가 `Activate()`를 호출한 후, 선택적으로 `UGameplayAbility::CommitCost()` 및 `UGameplayAbility::CommitCooldown()을 호출하는 `UGameplayAbility::CommitAbility()`를 사용하여 언제든지 비용과 재사용 대기시간을 커밋할 수 있습니다. `. 디자이너는 'CommitCost()' 또는 'CommitCooldown()'을 동시에 커밋하지 않아야 하는 경우 별도로 호출하도록 선택할 수 있습니다. 커밋 비용과 쿨다운은 `CheckCost()`와 `CheckCooldown()`을 한 번 더 호출하며, 이와 관련된 `GameplayAbility`가 실패할 마지막 기회입니다. 소유 'ASC'의 '속성'은 'GameplayAbility'가 활성화된 후 잠재적으로 변경되어 커밋 시 비용을 충족하지 못할 수 있습니다. 비용 및 재사용 대기 시간을 로컬에서 예측할 수 있는 커밋은 커밋 시점에 유효합니다.

See [`CostGE`](#concepts-ge-cost) and [`CooldownGE`](#concepts-ge-cooldown) for implementation details.

구현 세부정보는 'CostGE'를 참조하세요.
**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-leveling"></a>
#### 4.6.13 Leveling Up Abilities
There are two common methods for leveling up an ability:

능력을 레벨업하는 두 가지 일반적인 방법이 있습니다.

| Level Up Method                            | Description                                                                                                                                                                                                      |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ungrant and Regrant at the New Level       | Ungrant (remove) the `GameplayAbility` from the `ASC` and regrant it back at the next level on the server. This terminates the `GameplayAbility` if it was active at the time.                                   |
| Increase the `GameplayAbilitySpec's` Level | On the server, find the `GameplayAbilitySpec`, increase its level, and mark it dirty so that replicates to the owning client. This method does not terminate the `GameplayAbility` if it was active at the time. |

| 레벨업 방법 | 설명 |
| ------------------------------------------ | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------- |
| 새로운 차원의 Ungrant 및 Regrant | 'ASC'에서 'GameplayAbility'를 부여 취소(제거)하고 서버의 다음 수준에서 다시 부여합니다. 'GameplayAbility'가 당시 활성화되어 있었다면 종료됩니다. |
| 'GameplayAbilitySpec's' 레벨 올리기 | 서버에서 `GameplayAbilitySpec`을 찾아 레벨을 높이고 더티 표시하여 소유 클라이언트에 복제되도록 합니다. 이 메서드는 'GameplayAbility'가 당시 활성화되어 있었다면 종료하지 않습니다. |

The main difference between the two methods is if you want active `GameplayAbilities` to be canceled at the time of level up. You will most likely use both methods depending on your `GameplayAbilities`. I recommend adding a `bool` to your `UGameplayAbility` subclass specifying which method to use.

두 방법의 주요 차이점은 레벨 업 시 활성 'GameplayAbilities'를 취소하려는 경우입니다. 'GameplayAbilities'에 따라 두 가지 방법을 모두 사용할 가능성이 큽니다. 사용할 메소드를 지정하는 `UGameplayAbility` 서브클래스에 `bool`을 추가하는 것이 좋습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-sets"></a>
#### 4.6.14 Ability Sets
`GameplayAbilitySets` are convenience `UDataAsset` classes for holding input bindings and lists of startup `GameplayAbilities` for Characters with logic to grant the `GameplayAbilities`. Subclasses can also include extra logic or properties. Paragon had a `GameplayAbilitySet` per hero that included all of their given `GameplayAbilities`.

`GameplayAbilitySets`는 입력 바인딩을 보유하기 위한 편리한 `UDataAsset` 클래스와 `GameplayAbilities`를 부여하는 논리가 있는 캐릭터에 대한 시작 `GameplayAbilities` 목록입니다. 하위 클래스에는 추가 논리 또는 속성이 포함될 수도 있습니다. Paragon에는 주어진 'GameplayAbilities'를 모두 포함하는 영웅별로 'GameplayAbilitySet'이 있습니다.

I find this class to be unnecessary at least given what I've seen of it so far. The Sample Project handles all of the functionality of `GameplayAbilitySets` inside of the `GDCharacterBase` and its subclasses.

나는 이 수업이 적어도 내가 지금까지 본 것을 감안할 때 불필요하다고 생각합니다. 샘플 프로젝트는 'GDCharacterBase' 및 그 하위 클래스 내에서 'GameplayAbilitySets'의 모든 기능을 처리합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-batching"></a>
#### 4.6.15 Ability Batching
Traditional `Gameplay Ability` lifecycle involves a minimum of two or three RPCs from the client to the server.

기존의 '게임 플레이 능력' 수명 주기에는 클라이언트에서 서버까지 최소 2~3개의 RPC가 포함됩니다.

1. `CallServerTryActivateAbility()`
1. `ServerSetReplicatedTargetData()` (Optional)
1. `ServerEndAbility()`

If a `GameplayAbility` performs all of these actions in one atomic grouping in a frame, we can optimize this workflow to batch (combine) all two or three RPCs into one RPC. `GAS` refers to this RPC optimization as `Ability Batching`. The common example of when to use `Ability Batching` is for hitscan guns. Hitscan guns activate, do a line trace, send the [`TargetData`](#concepts-targeting-data) to the server, and end the ability all in one atomic group in one frame. The [GASShooter](https://github.com/tranek/GASShooter) sample project demonstrates this technique for its hitscan guns.

'GameplayAbility'가 프레임의 하나의 원자 그룹화에서 이러한 모든 작업을 수행하는 경우 이 워크플로를 최적화하여 2개 또는 3개의 RPC를 하나의 RPC로 일괄 처리(결합)할 수 있습니다. 'GAS'는 이 RPC 최적화를 'Ability Batching'이라고 합니다. 'Ability Batching'을 사용하는 일반적인 예는 히트 스캔 총입니다. Hitscan 총은 활성화되고, 라인 추적을 수행하고, 'TargetData'를 서버로 보내고, 한 프레임의 하나의 원자 그룹에서 모든 기능을 종료합니다. GASShooter 샘플 프로젝트는 히트스캔 건에 대해 이 기술을 보여줍니다.

Semi-Automatic guns are the best case scenario and batch the `CallServerTryActivateAbility()`, `ServerSetReplicatedTargetData()` (the bullet hit result), and `ServerEndAbility()` into one RPC instead of three RPCs.

반자동 총은 최상의 시나리오이며 `CallServerTryActivateAbility()`, `ServerSetReplicatedTargetData()`(총알 명중 결과) 및 `ServerEndAbility()`를 3개의 RPC 대신 하나의 RPC로 일괄 처리합니다.

Full-Automatic/Burst guns batch `CallServerTryActivateAbility()` and `ServerSetReplicatedTargetData()` for the first bullet into one RPC instead of two RPCs. Each subsequent bullet is its own `ServerSetReplicatedTargetData()` RPC. Finally, `ServerEndAbility()` is sent as a separate RPC when the gun stops firing. This is a worst case scenario where we only save one RPC on the first bullet instead of two. This scenario could have also been implemented with activating the ability via a [`Gameplay Event`](#concepts-ga-data) which would send the bullet's `TargetData` in with the `EventPayload` to the server from the client. The downside of the latter approach is that the `TargetData` would have to be generated externally to the ability whereas the batching approach generates the `TargetData` inside of the ability.

완전 자동/버스트 건은 첫 번째 총알에 대해 `CallServerTryActivateAbility()` 및 `ServerSetReplicatedTargetData()`를 2개의 RPC 대신 하나의 RPC로 일괄 처리합니다. 각 후속 글머리 기호는 자체 `ServerSetReplicatedTargetData()` RPC입니다. 마지막으로 총 발사가 중지되면 `ServerEndAbility()`가 별도의 RPC로 전송됩니다. 이것은 두 개가 아닌 첫 번째 글머리 기호에 하나의 RPC만 저장하는 최악의 시나리오입니다. 이 시나리오는 클라이언트에서 서버로 'EventPayload'와 함께 총알의 'TargetData'를 보내는 'Gameplay Event'를 통해 기능을 활성화하여 구현할 수도 있습니다. 후자의 접근 방식의 단점은 'TargetData'가 기능 외부에서 생성되어야 하는 반면 일괄 처리 방식은 기능 내부에서 'TargetData'를 생성한다는 것입니다.

`Ability Batching` is disabled by default on the [`ASC`](#concepts-asc). To enable `Ability Batching`, override `ShouldDoServerAbilityRPCBatch()` to return true:

'Ability Batching'은 기본적으로 'ASC''에서 비활성화되어 true를 반환합니다.

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

Now that `Ability Batching` is enabled, before activating abilities that you want batched, you must create a `FScopedServerAbilityRPCBatcher` struct beforehand. This special struct will try to batch any abilities following it within its scope. Once the `FScopedServerAbilityRPCBatcher` falls out of scope, any abilities activated will not try to batch. `FScopedServerAbilityRPCBatcher` works by having special code in each of the functions that can be batched that intercepts the call from sending the RPC and instead packs the message into a batch struct. When `FScopedServerAbilityRPCBatcher` falls out of scope, it automatically RPCs this batch struct to the server in `UAbilitySystemComponent::EndServerAbilityRPCBatch()`. The server receives the batch RPC in `UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)`. The `BatchInfo` parameter will contain flags for if the ability should end and if input was pressed at the time of activation and the `TargetData` if that was included. This is a good function to put a breakpoint on to confirm that your batching is working properly. Alternatively, use the cvar `AbilitySystem.ServerRPCBatching.Log 1` to enable special ability batching logging.

이제 `Ability Batching`이 활성화되었으므로 배치하려는 능력을 활성화하기 전에 `FScopedServerAbilityRPCBatcher` 구조체를 미리 생성해야 합니다. 이 특별한 구조체는 범위 내에서 뒤에 오는 모든 능력을 일괄 처리하려고 시도합니다. 일단 `FScopedServerAbilityRPCBatcher`가 범위를 벗어나면 활성화된 기능은 일괄 처리를 시도하지 않습니다. 'FScopedServerAbilityRPCBatcher'는 RPC를 보내는 호출을 가로채고 대신 메시지를 일괄 처리 구조로 압축하는 일괄 처리될 수 있는 각 함수에 특수 코드를 가짐으로써 작동합니다. `FScopedServerAbilityRPCBatcher`가 범위를 벗어나면 `UAbilitySystemComponent::EndServerAbilityRPCBatch()`의 서버에 이 배치 구조체를 자동으로 RPC합니다. 서버는 `UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& 배치 정보)`. 'BatchInfo' 매개변수에는 능력이 종료되어야 하는지와 활성화 시 입력이 눌려졌는지에 대한 플래그가 포함되며 포함된 경우 'TargetData'가 포함됩니다. 이것은 일괄 처리가 제대로 작동하는지 확인하기 위해 중단점을 설정하는 좋은 기능입니다. 또는 cvar `AbilitySystem.ServerRPCBatching.Log 1`을 사용하여 특수 기능 일괄 로깅을 활성화합니다.

This mechanism can only be done in C++ and can only activate abilities by their `FGameplayAbilitySpecHandle`.

이 메커니즘은 C++에서만 수행할 수 있으며 'FGameplayAbilitySpecHandle'로만 능력을 활성화할 수 있습니다.

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

GASShooter reuses the same batched `GameplayAbility` for semi-automatic and full-automatic guns which never directly call `EndAbility()` (it is handled outside of the ability by a local-only ability that manages player input and the call to the batched ability based on the current firemode). Since all of the RPCs must happen within the scope of the `FScopedServerAbilityRPCBatcher`, I provide the `EndAbilityImmediately` parameter so that the controlling/managing local-only can specify whether this ability should batch the `EndAbility()` call (semi-automatic), or not batch the `EndAbility()` call (full-automatic) and the `EndAbility()` call will happen sometime later in its own RPC.

GASShooter는 'EndAbility()'를 직접 호출하지 않는 반자동 및 완전 자동 총에 대해 동일한 일괄 처리된 'GameplayAbility'를 재사용합니다. 현재 발사 모드에 기반한 능력). 모든 RPC는 `FScopedServerAbilityRPCBatcher`의 범위 내에서 발생해야 하므로 로컬 전용 제어/관리가 이 기능이 `EndAbility()` 호출(반자동 ) 또는 `EndAbility()` 호출(완전 자동)을 일괄 처리하지 않고 `EndAbility()` 호출은 나중에 자체 RPC에서 발생합니다.

GASShooter exposes a Blueprint node to allow batching abilities which the aforementioned local-only ability uses to trigger the batched ability.

GASShooter는 앞에서 언급한 로컬 전용 능력이 일괄 처리 능력을 트리거하는 데 사용하는 일괄 처리 능력을 허용하기 위해 블루프린트 노드를 노출합니다.

![Activate Batched Ability](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.16 Net Security Policy
A `GameplayAbility`'s `NetSecurityPolicy` determines where should an ability execute on the network. It provides protection from clients attempting to execute restricted abilities.

| `NetSecurityPolicy`     | Description                                                                                                                                        |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ClientOrServer`        | No security requirements. Client or server can trigger execution and termination of this ability freely.                                           |
| `ServerOnlyExecution`   | A client requesting execution of this ability will be ignored by the server. Clients can still request that the server cancel or end this ability. |
| `ServerOnlyTermination` | A client requesting cancellation or ending of this ability will be ignored by the server. Clients can still request execution of the ability.      |
| `ServerOnly`            | Server controls both execution and termination of this ability. A client making any requests will be ignored.                                      |


| `NetSecurityPolicy` | 설명 |
| ----------------------- | -------------------------------------------------- -------------------------------------------------- ---------------------------------------------- |
| `클라이언트 또는 서버` | 보안 요구 사항이 없습니다. 클라이언트 또는 서버는 이 능력의 실행 및 종료를 자유롭게 트리거할 수 있습니다. |
| `서버 전용 실행` | 이 기능의 실행을 요청하는 클라이언트는 서버에서 무시됩니다. 클라이언트는 여전히 서버에 이 기능을 취소하거나 종료하도록 요청할 수 있습니다. |
| `서버만 종료` | 이 기능의 취소 또는 종료를 요청하는 클라이언트는 서버에서 무시됩니다. 클라이언트는 여전히 기능 실행을 요청할 수 있습니다. |
| `서버 전용` | 서버는 이 기능의 실행과 종료를 모두 제어합니다. 요청하는 클라이언트는 무시됩니다. |


**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at"></a>
### 4.7 Ability Tasks

<a name="concepts-at-definition"></a>
### 4.7.1 Ability Task Definition
`GameplayAbilities` only execute in one frame. This does not allow for much flexibility on its own. To do actions that happen over time or require responding to delegates fired at some point later in time we use latent actions called `AbilityTasks`.

'GameplayAbilities'는 한 프레임에서만 실행됩니다. 이것은 자체적으로 많은 유연성을 허용하지 않습니다. 시간이 지남에 따라 발생하거나 나중에 특정 시점에서 실행된 대리자에 응답해야 하는 작업을 수행하려면 'AbilityTasks'라는 잠재적인 작업을 사용합니다.

GAS comes with many `AbilityTasks` out of the box:
* Tasks for moving Characters with `RootMotionSource`
* A task for playing animation montages
* Tasks for responding to `Attribute` changes
* Tasks for responding to `GameplayEffect` changes
* Tasks for responding to player input
* and more

GAS는 기본적으로 많은 'AbilityTasks'와 함께 제공됩니다.
* `RootMotionSource`로 캐릭터 이동 작업
* 애니메이션 몽타주 재생 작업
* '속성' 변경에 대응하기 위한 작업
* 'GameplayEffect' 변경에 대응하기 위한 태스크
* 플레이어 입력에 응답하기 위한 작업
* 그리고 더

The `UAbilityTask` constructor enforces a hardcoded game-wide maximum of 1000 concurrent `AbilityTasks` running at the same time. Keep this in mind when designing `GameplayAbilities` for games that can have hundreds of characters in the world at the same time like RTS games.

'UAbilityTask' 생성자는 하드코딩된 게임 전체에서 동시에 실행되는 최대 1000개의 동시 'AbilityTasks'를 적용합니다. RTS 게임처럼 세계에 수백 명의 캐릭터를 동시에 가질 수 있는 게임을 위한 'GameplayAbilities'를 설계할 때 이 점을 염두에 두십시오.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-definition"></a>
### 4.7.2 Custom Ability Tasks
Often you will be creating your own custom `AbilityTasks` (in C++). The Sample Project comes with two custom `AbilityTasks`:
1. `PlayMontageAndWaitForEvent` is a combination of the default `PlayMontageAndWait` and `WaitGameplayEvent` `AbilityTasks`. This allows animation montages to send gameplay events from `AnimNotifies` back to the `GameplayAbility` that started them. Use this to trigger actions at specific times during animation montages.
1. `WaitReceiveDamage` listens for the `OwnerActor` to receive damage. The passive armor stacks `GameplayAbility` removes a stack of armor when the hero receives an instance of damage.

종종 여러분은 자신만의 커스텀 `AbilityTasks`(C++에서)를 만들 것입니다. 샘플 프로젝트에는 두 가지 사용자 정의 'AbilityTasks'가 제공됩니다.
1. `PlayMontageAndWaitForEvent`는 기본 `PlayMontageAndWait`와 `WaitGameplayEvent` `AbilityTasks`의 조합입니다. 이를 통해 애니메이션 몽타주는 'AnimNotify'에서 게임플레이 이벤트를 시작한 'GameplayAbility'로 다시 보낼 수 있습니다. 애니메이션 몽타주 중 특정 시간에 동작을 트리거하려면 이것을 사용하십시오.
1. `WaitReceiveDamage`는 `OwnerActor`가 피해를 입는 것을 수신 대기합니다. 패시브 아머 스택 'GameplayAbility'는 영웅이 피해를 입었을 때 아머 스택을 제거합니다.


`AbilityTasks` are composed of:
* A static function that creates new instances of the `AbilityTask`
* Delegates that are broadcasted on when the `AbilityTask` completes its purpose
* An `Activate()` function to start its main job, bind to external delegates, etc.
* An `OnDestroy()` function for cleanup, including external delegates that it bound to
* Callback functions for any external delegates that it bound to
* Member variables and any internal helper functions

'AbilityTasks'는 다음으로 구성됩니다.
* 'AbilityTask'의 새 인스턴스를 생성하는 정적 함수
* 'AbilityTask'가 목적을 완료했을 때 브로드캐스트되는 델리게이트
* 주요 작업을 시작하고 외부 대리자에 바인딩하는 등의 `Activate()` 함수
* 바인딩된 외부 대리자를 포함하여 정리를 위한 `OnDestroy()` 함수
* 바인딩된 외부 대리자에 대한 콜백 함수
* 멤버 변수 및 내부 도우미 함수


**Note:** `AbilityTasks` can only declare one type of output delegate. All of your output delegates must be of this type, regardless if they use the parameters or not. Pass default values for unused delegate parameters.

**참고:** `AbilityTasks`는 한 가지 유형의 출력 대리자만 선언할 수 있습니다. 매개변수를 사용하는지 여부에 관계없이 모든 출력 대리자는 이 유형이어야 합니다. 사용하지 않는 대리자 매개변수에 대한 기본값을 전달합니다.

`AbilityTasks` only run on the Client or Server that is running the owning `GameplayAbility`; however, `AbilityTasks` can be set to run on simulated clients by setting `bSimulatedTask = true;` in the `AbilityTask` constructor, overriding `virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`, and setting any member variables to be replicated. This is only useful in rare situations like movement `AbilityTasks` where you don't want to replicate every movement change but instead simulate the entire movement `AbilityTask`. All of the `RootMotionSource` `AbilityTasks` do this. See `AbilityTask_MoveToLocation.h/.cpp` as an example.

`AbilityTasks`는 소유 `GameplayAbility`를 실행하는 클라이언트 또는 서버에서만 실행됩니다. 그러나 `AbilityTask` 생성자에서 `bSimulatedTask = true;`를 설정하고 `virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`를 재정의하고 복제할 멤버 변수를 설정하여 시뮬레이션된 클라이언트에서 실행되도록 `AbilityTasks`를 설정할 수 있습니다. 이것은 모든 움직임 변화를 복제하고 싶지 않고 대신 전체 움직임 'AbilityTask'를 시뮬레이션하는 'AbilityTasks' 움직임과 같은 드문 상황에서만 유용합니다. 모든 `RootMotionSource` `AbilityTasks`가 이 작업을 수행합니다. 예를 들어 `AbilityTask_MoveToLocation.h/.cpp`를 참조하십시오.

`AbilityTasks` can `Tick` if you set `bTickingTask = true;` in the `AbilityTask` constructor and override `virtual void TickTask(float DeltaTime);`. This is useful when you need to lerp values smoothly across frames. See `AbilityTask_MoveToLocation.h/.cpp` as an example.

`AbilityTask` 생성자에서 `bTickingTask = true;`를 설정하고 `virtual void TickTask(float DeltaTime);`를 재정의하면 `AbilityTasks`가 `Tick`할 수 있습니다. 이것은 프레임 간에 부드럽게 값을 lerp해야 할 때 유용합니다. 예를 들어 `AbilityTask_MoveToLocation.h/.cpp`를 참조하십시오.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-using"></a>
### 4.7.3 Using Ability Tasks
To create and activate an `AbilityTask` in C++ (From `GDGA_FireGun.cpp`):
```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

In Blueprint, we just use the Blueprint node that we create for the `AbilityTask`. We don't have to call `ReadyForActivate()`. That is automatically called by `Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`. `K2Node_LatentGameplayTaskCall` also automatically calls `BeginSpawningActor()` and `FinishSpawningActor()` if they exist in your `AbilityTask` class (see `AbilityTask_WaitTargetData`). To reiterate, `K2Node_LatentGameplayTaskCall` only does automagic sorcery for Blueprint. In C++, we have to manually call `ReadyForActivation()`, `BeginSpawningActor()`, and `FinishSpawningActor()`.

블루프린트에서는 'AbilityTask'용으로 생성한 블루프린트 노드만 사용합니다. 우리는 `ReadyForActivate()`를 호출할 필요가 없습니다. 그것은 `Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`에 의해 자동으로 호출됩니다. `K2Node_LatentGameplayTaskCall`은 `BeginSpawningActor()` 및 `FinishSpawningActor()`가 `AbilityTask` 클래스에 있는 경우 자동으로 호출하기도 합니다(`AbilityTask_WaitTargetData` 참조). 다시 말하지만 `K2Node_LatentGameplayTaskCall`은 블루프린트에 대한 자동 마법만 수행합니다. C++에서는 `ReadyForActivation()`, `BeginSpawningActor()`, `FinishSpawningActor()`를 수동으로 호출해야 합니다.

![Blueprint WaitTargetData AbilityTask](https://github.com/tranek/GASDocumentation/raw/master/Images/abilitytask.png)

To manually cancel an `AbilityTask`, just call `EndTask()` on the `AbilityTask` object in Blueprint (called `Async Task Proxy`) or in C++.

`AbilityTask`를 수동으로 취소하려면 Blueprint(`Async Task Proxy`라고 함) 또는 C++의 `AbilityTask` 객체에서 `EndTask()`를 호출하면 됩니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-rms"></a>
### 4.7.4 Root Motion Source Ability Tasks
GAS comes with `AbilityTasks` for moving `Characters` over time for things like knockbacks, complex jumps, pulls, and dashes using `Root Motion Sources` hooked into the `CharacterMovementComponent`.

GAS에는 'CharacterMovementComponent'에 연결된 'Root Motion Sources'를 사용하여 넉백, 복잡한 점프, 당기기 및 대시와 같은 항목에 대해 시간이 지남에 따라 'Characters'를 이동하는 'AbilityTasks'가 함께 제공됩니다.

**Note:** Predicting `RootMotionSource` `AbilityTasks` works up to engine version 4.19 and 4.25+. Prediction is bugged for engine versions 4.20-4.24; however, the `AbilityTasks` still perform their function in multiplayer with minor net corrections and work perfectly in single player. It is possible to cherry pick the [prediction fix](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c) from 4.25 into a custom 4.20-4.24 engine.

**참고:** 예측 `RootMotionSource` `AbilityTasks`는 엔진 버전 4.19 및 4.25+까지 작동합니다. 엔진 버전 4.20-4.24에 대한 예측이 버그가 있습니다. 그러나 'AbilityTasks'는 약간의 네트워크 수정으로 멀티플레이어에서 여전히 기능을 수행하고 싱글 플레이어에서 완벽하게 작동합니다. 4.25에서 사용자 지정 4.20-4.24 엔진으로 예측 수정 사항을 선택하는 것이 가능합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 Gameplay Cues

<a name="concepts-gc-definition"></a>
#### 4.8.1 Gameplay Cue Definition
`GameplayCues` (`GC`) execute non-gameplay related things like sound effects, particle effects, camera shakes, etc. `GameplayCues` are typically replicated (unless explicitly `Executed`, `Added`, or `Removed` locally) and predicted.

`GameplayCues`(`GC`)는 사운드 효과, 파티클 효과, 카메라 흔들림 등과 같은 게임플레이와 관련되지 않은 것을 실행합니다. `GameplayCues`는 일반적으로 복제됩니다(명시적으로 로컬에서 `Executed`, `Added` 또는 `Removed`가 아닌 경우). 예측했다.

We trigger `GameplayCues` by sending a corresponding `GameplayTag` with the **mandatory parent name of `GameplayCue.`** and an event type (`Execute`, `Add`, or `Remove`) to the `GameplayCueManager` via the `ASC`. `GameplayCueNotify` objects and other `Actors` that implement the `IGameplayCueInterface` can subscribe to these events based on the `GameplayCue's` `GameplayTag` (`GameplayCueTag`).

**필수 상위 이름인 `GameplayCue.`**와 이벤트 유형(`Execute`, `Add` 또는 `Remove`)이 있는 해당 `GameplayTag`를 다음을 통해 `GameplayCueManager`에 전송하여 `GameplayCues`를 트리거합니다. 'ASC'. 'GameplayCueNotify' 객체 및 'IGameplayCueInterface'를 구현하는 기타 '액터'는 'GameplayCue'의 'GameplayTag'('GameplayCueTag')를 기반으로 이러한 이벤트를 구독할 수 있습니다.

**Note:** Just to reiterate, `GameplayCue` `GameplayTags` need to start with the parent `GameplayTag` of `GameplayCue`. So for example, a valid `GameplayCue` `GameplayTag` might be `GameplayCue.A.B.C`.

**참고:** 다시 말하지만 `GameplayCue` `GameplayTags`는 `GameplayCue`의 상위 `GameplayTag`로 시작해야 합니다. 예를 들어 유효한 `GameplayCue` `GameplayTag`는 `GameplayCue.ABC`일 수 있습니다.

There are two classes of `GameplayCueNotifies`, `Static` and `Actor`. They respond to different events and different types of `GameplayEffects` can trigger them. Override the corresponding event with your logic.

'GameplayCueNotify', 'Static' 및 'Actor'의 두 가지 클래스가 있습니다. 그들은 다양한 이벤트에 반응하고 다양한 유형의 'GameplayEffects'가 이를 트리거할 수 있습니다. 논리로 해당 이벤트를 재정의합니다.

| `GameplayCue` Class                                                                                                                  | Event             | `GameplayEffect` Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant` or `Periodic`  | Static `GameplayCueNotifies` operate on the `ClassDefaultObject` (meaning no instances) and are perfect for one-off effects like hit impacts.                                                                                                                                                                                                                                                                                                                                                                        |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` or `Remove` | `Duration` or `Infinite` | Actor `GameplayCueNotifies` spawn a new instance when `Added`. Because these are instanced, they can do actions over time until they are `Removed`. These are good for looping sounds and particle effects that will be removed when the backing `Duration` or `Infinite` `GameplayEffect` is removed or by manually calling remove. These also come with options to manage how many are allowed to be `Added` at the same so that multiple applications of the same effect only start the sounds or particles once. |


| `GameplayCue` 클래스 | 이벤트 | `GameplayEffect` 유형 | 설명 |
| -------------------------------------------------- -------------------------------------------------- -------------------------------- | ------------------ | ------------------------ | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- |
| `GameplayCueNotify_Static` | `실행` | `즉시` 또는 `정기` | 정적 'GameplayCueNotify'는 'ClassDefaultObject'(인스턴스가 없음을 의미)에서 작동하며 적중 충격과 같은 일회성 효과에 적합합니다. |
| `GameplayCueNotify_Actor` | '추가' 또는 '제거' | '기간' 또는 '무한' | 액터 'GameplayCueNotify'는 'Added' 시 새 인스턴스를 생성합니다. 이들은 인스턴스화되기 때문에 '제거'될 때까지 시간이 지남에 따라 작업을 수행할 수 있습니다. 이는 뒷받침하는 `Duration` 또는 `Infinite` `GameplayEffect`가 제거되거나 수동으로 제거를 호출하여 제거될 반복 사운드 및 입자 효과에 좋습니다. 여기에는 동일한 효과의 여러 응용 프로그램이 사운드 또는 입자를 한 번만 시작하도록 동시에 '추가'할 수 있는 수를 관리하는 옵션도 함께 제공됩니다. |



`GameplayCueNotifies` technically can respond to any of the events but this is typically how we use them.

**Note:** When using `GameplayCueNotify_Actor`, check `Auto Destroy on Remove` otherwise subsequent calls to `Add` that `GameplayCueTag` won't work.

'GameplayCueNotify'는 기술적으로 모든 이벤트에 응답할 수 있지만 일반적으로 이것이 사용하는 방법입니다.

When using an `ASC` [Replication Mode](#concepts-asc-rm) other than `Full`, `Add` and `Remove` `GC` events will fire twice on Server players (listen server) - once for applying the `GE` and again from the "Minimal" `NetMultiCast` to the clients. However, `WhileActive` events will still only fire once. All events will only fire once on clients.

`ASC` 복제 모드를 사용할 때 - 한 번은 `GE`를 적용하고 다시 "Minimal" `NetMultiCast`에서 클라이언트로. 그러나 'WhileActive' 이벤트는 여전히 한 번만 실행됩니다. 모든 이벤트는 클라이언트에서 한 번만 실행됩니다.

The Sample Project includes a `GameplayCueNotify_Actor` for stun and sprint effects. It also has a `GameplayCueNotify_Static` for the FireGun's projectile impact. These `GCs` can be optimized further by [triggering them locally](#concepts-gc-local) instead of replicating them through a `GE`. I opted for showing the beginner way of using them in the Sample Project.

샘플 프로젝트에는 기절 및 질주 효과를 위한 'GameplayCueNotify_Actor'가 포함되어 있습니다. FireGun의 발사체 충격에 대한 'GameplayCueNotify_Static'도 있습니다. 이러한 'GC'는 'GE'를 통해 복제하는 대신 로컬에서 트리거하여 추가로 최적화할 수 있습니다. 나는 샘플 프로젝트에서 그것들을 사용하는 초보자 방법을 보여주기로 선택했습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 Triggering Gameplay Cues

From inside of a `GameplayEffect` when it is successfully applied (not blocked by tags or immunity), fill in the `GameplayTags` of all the `GameplayCues` that should be triggered.

성공적으로 적용되었을 때 `GameplayEffect` 내부에서(태그나 면역에 의해 차단되지 않음), 트리거되어야 하는 모든 `GameplayCues`의 `GameplayTags`를 채우십시오.

![GameplayCue Triggered from a GameplayEffect](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility` offers Blueprint nodes to `Execute`, `Add`, or `Remove` `GameplayCues`.

'UGameplayAbility'는 'GameplayCues'를 '실행', '추가' 또는 '제거'할 수 있는 블루프린트 노드를 제공합니다.

![GameplayCue Triggered from a GameplayAbility](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

In C++, you can call functions directly on the `ASC` (or expose them to Blueprint in your `ASC` subclass):

C++에서는 `ASC`에서 직접 함수를 호출할 수 있습니다(또는 `ASC` 하위 클래스의 Blueprint에 노출):

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

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 4.8.3 Local Gameplay Cues
The exposed functions for firing `GameplayCues` from `GameplayAbilities` and the `ASC` are replicated by default. Each `GameplayCue` event is a multicast RPC. This can cause a lot of RPCs. GAS also enforces a maximum of two of the same `GameplayCue` RPCs per net update. We avoid this by using local `GameplayCues` where we can. Local `GameplayCues` only `Execute`, `Add`, or `Remove` on the individual client.

`GameplayAbilities` 및 `ASC`에서 `GameplayCues`를 실행하기 위해 노출된 함수는 기본적으로 복제됩니다. 각 'GameplayCue' 이벤트는 멀티캐스트 RPC입니다. 이로 인해 많은 RPC가 발생할 수 있습니다. GAS는 또한 네트 업데이트당 동일한 'GameplayCue' RPC를 최대 2개까지 적용합니다. 가능한 한 로컬 'GameplayCues'를 사용하여 이를 방지합니다. 로컬 'GameplayCues'는 개별 클라이언트의 '실행', '추가' 또는 '제거'만 가능합니다.

Scenarios where we can use local `GameplayCues`:
* Projectile impacts
* Melee collision impacts
* `GameplayCues` fired from animation montages

로컬 `GameplayCues`를 사용할 수 있는 시나리오:
* 발사체 충돌
* 근접 충돌 충격
* 애니메이션 몽타주에서 실행된 `GameplayCues`


Local `GameplayCue` functions that you should add to your `ASC` subclass:
`ASC` 서브클래스에 추가해야 하는 로컬 `GameplayCue` 함수:
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

If a `GameplayCue` was `Added` locally, it should be `Removed` locally. If it was `Added` via replication, it should be `Removed` via replication.

'GameplayCue'가 로컬에서 '추가'된 경우 로컬에서 '제거'되어야 합니다. 복제를 통해 '추가'된 경우 복제를 통해 '제거'되어야 합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 4.8.4 Gameplay Cue Parameters
`GameplayCues` receive a `FGameplayCueParameters` structure containing extra information for the `GameplayCue` as a parameter. If you manually trigger the `GameplayCue` from a function on the `GameplayAbility` or the `ASC`, then you must manually fill in the `GameplayCueParameters` structure that is passed to the `GameplayCue`. If the `GameplayCue` is triggered by a `GameplayEffect`, then the following variables are automatically filled in on the `GameplayCueParameters` 
structure:

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude (if the `GameplayEffect` has an `Attribute` for magnitude selected in the dropdown above the `GameplayCue` tag container and a corresponding `Modifier` that affects that `Attribute`)


`GameplayCues`는 `GameplayCue`에 대한 추가 정보가 포함된 `FGameplayCueParameters` 구조를 매개변수로 받습니다. 'GameplayAbility' 또는 'ASC'의 함수에서 'GameplayCue'를 수동으로 트리거하는 경우 'GameplayCue'에 전달되는 'GameplayCueParameters' 구조를 수동으로 채워야 합니다. 'GameplayCue'가 'GameplayEffect'에 의해 트리거되면 다음 변수가 'GameplayCueParameters' 구조에 자동으로 채워집니다.:

* 집계 소스 태그
* AggregatedTargetTags
* 게임 플레이 효과 레벨
* 능력레벨
* 효과 컨텍스트
* 크기('GameplayEffect'에 'GameplayCue' 태그 컨테이너 위의 드롭다운에서 선택한 크기에 대한 '속성'과 해당 '속성'에 영향을 주는 해당 '수정자'가 있는 경우)


The `SourceObject` variable in the `GameplayCueParameters` structure is potentially a good place to pass arbitrary data to the `GameplayCue` when triggering the `GameplayCue` manually.

`GameplayCueParameters` 구조의 `SourceObject` 변수는 잠재적으로 `GameplayCue`를 수동으로 트리거할 때 `GameplayCue`에 임의의 데이터를 전달할 수 있는 좋은 위치입니다.

**Note:** Some of the variables in the parameters structure like `Instigator` might already exist in the `EffectContext`. The `EffectContext` can also contain a `FHitResult` for location of where to spawn the `GameplayCue` in the world. Subclassing `EffectContext` is potentially a good way to pass more data to `GameplayCues`, especially those triggered by a `GameplayEffect`.

**참고:** `Instigator`와 같은 매개변수 구조의 일부 변수는 이미 `EffectContext`에 존재할 수 있습니다. 'EffectContext'에는 월드에서 'GameplayCue'를 스폰할 위치에 대한 'FHitResult'도 포함될 수 있습니다. `EffectContext`를 서브클래싱하는 것은 잠재적으로 `GameplayCues`에 더 많은 데이터, 특히 `GameplayEffect`에 의해 트리거되는 데이터를 전달하는 좋은 방법입니다.

See the 3 functions in [`UAbilitySystemGlobals`](#concepts-asg) that populate the `GameplayCueParameters` structure for more information. They are virtual so you can override them to autopopulate more information.

자세한 내용은 `GameplayCueParameters` 구조를 채우는 `UAbilitySystemGlobals`의 3가지 함수를 참조하세요. 그것들은 가상이므로 더 많은 정보를 자동으로 채우도록 재정의할 수 있습니다.

```c++
/** Initialize GameplayCue Parameters */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 4.8.5 Gameplay Cue Manager
By default, the `GameplayCueManager` will scan the entire game directory for `GameplayCueNotifies` and load them into memory on play. We can change the path where the `GameplayCueManager` scans by setting it in the `DefaultGame.ini`.

기본적으로 `GameplayCueManager`는 `GameplayCueNotify`에 대한 전체 게임 디렉토리를 스캔하고 플레이 시 메모리에 로드합니다. `DefaultGame.ini`에서 설정하여 `GameplayCueManager`가 스캔하는 경로를 변경할 수 있습니다.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

We do want the `GameplayCueManager` to scan and find all of the `GameplayCueNotifies`; however, we don't want it to async load every single one on play. This will put every `GameplayCueNotify` and all of their referenced sounds and particles into memory regardless if they're even used in a level. In a large game like Paragon, this can be hundreds of megabytes of unneeded assets in memory and cause hitching and game freezes on startup.

우리는 `GameplayCueManager`가 모든 `GameplayCueNotify`를 스캔하고 찾기를 원합니다. 그러나 우리는 그것이 플레이에 있는 모든 것을 비동기적으로 로드하는 것을 원하지 않습니다. 이렇게 하면 모든 'GameplayCueNotify'와 참조된 모든 사운드 및 파티클이 레벨에서 사용되는지 여부에 관계없이 메모리에 저장됩니다. Paragon과 같은 대형 게임에서는 메모리에 수백 메가바이트의 불필요한 자산이 있을 수 있으며 시작 시 끊김 및 게임 정지가 발생할 수 있습니다.

An alternative to async loading every `GameplayCue` on startup is to only async load `GameplayCues` as they're triggered in-game. This mitigates the unnecessary memory usage and potential game hard freezes while async loading every `GameplayCue` in exchange for potentially delayed effects for the first time that a specific `GameplayCue` is triggered during play. This potential delay is nonexistent for SSDs. I have not tested on a HDD. If using this option in the UE Editor, there may be slight hitches or freezes during the first load of GameplayCues if the Editor needs to compile particle systems. This is not an issue in builds as the particle systems will already be compiled.

시작 시 모든 'GameplayCue'를 비동기식으로 로드하는 대신 게임 내에서 트리거될 때 'GameplayCues'만 비동기식으로 로드합니다. 이렇게 하면 재생 중에 특정 'GameplayCue'가 처음으로 트리거되는 잠재적 지연 효과에 대한 대가로 모든 'GameplayCue'를 비동기적으로 로드하는 동안 불필요한 메모리 사용량과 잠재적인 게임 하드 정지가 완화됩니다. 이 잠재적 지연은 SSD에 존재하지 않습니다. 나는 HDD에서 테스트하지 않았습니다. UE 에디터에서 이 옵션을 사용하는 경우 에디터가 파티클 시스템을 컴파일해야 하는 경우 GameplayCues의 첫 번째 로드 중에 약간의 끊김이나 정지가 있을 수 있습니다. 파티클 시스템은 이미 컴파일되어 있으므로 빌드에서는 문제가 되지 않습니다.

First we must subclass `UGameplayCueManager` and tell the `AbilitySystemGlobals` class to use our `UGameplayCueManager` subclass in `DefaultGame.ini`.

먼저 `UGameplayCueManager`를 서브클래싱하고 `DefaultGame.ini`에서 `UGameplayCueManager` 서브클래스를 사용하도록 `AbilitySystemGlobals` 클래스에 지시해야 합니다.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

In our `UGameplayCueManager` subclass, override `ShouldAsyncLoadRuntimeObjectLibraries()`.

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 4.8.6 Prevent Gameplay Cues from Firing
Sometimes we don't want `GameplayCues` to fire. For example if we block an attack, we may not want to play the hit impact attached to the damage `GameplayEffect` or play a custom one instead. We can do this inside of [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) by calling `OutExecutionOutput.MarkGameplayCuesHandledManually()` and then manually sending our `GameplayCue` event to the `Target` or `Source's` `ASC`.

때때로 우리는 `GameplayCues`가 실행되는 것을 원하지 않습니다. 예를 들어 공격을 차단하는 경우 'GameplayEffect' 손상에 연결된 타격 충격을 재생하거나 대신 사용자 지정 충격을 재생하고 싶지 않을 수 있습니다. `GameplayEffectExecutionCalculations`` 내부에서 이 작업을 수행한 다음 `GameplayCue` 이벤트를 `Target` 또는 `Source'의 `ASC`에 수동으로 보낼 수 있습니다.

If you never want any `GameplayCues` to fire on a specific `ASC`, you can set `AbilitySystemComponent->bSuppressGameplayCues = true;`.

특정 `ASC`에서 `GameplayCues`가 실행되는 것을 원하지 않으면 `AbilitySystemComponent->bSuppressGameplayCues = true;`로 설정할 수 있습니다.



**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-batching"></a>
#### 4.8.7 Gameplay Cue Batching
Each `GameplayCue` triggered is an unreliable NetMulticast RPC. In situations where we fire multiple `GCs` at the same time, there are a few optimization methods to condense them down into one RPC or save bandwidth by sending less data.

트리거된 각 'GameplayCue'는 신뢰할 수 없는 NetMulticast RPC입니다. 여러 `GC`를 동시에 실행하는 상황에서 하나의 RPC로 압축하거나 더 적은 데이터를 전송하여 대역폭을 절약하는 몇 가지 최적화 방법이 있습니다.

<a name="concepts-gc-batching-manualrpc"></a>
##### 4.8.7.1 Manual RPC
Say you have a shotgun that shoots eight pellets. That's eight trace and impact `GameplayCues`. [GASShooter](https://github.com/tranek/GASShooter) takes the lazy approach of combining them into one RPC by stashing all of the trace information into the [`EffectContext`](#concepts-ge-ec) as [`TargetData`](#concepts-targeting-data). While this reduces the RPCs from eight to one, it still sends a lot of data over the network in that one RPC (~500 bytes). A more optimized approach is to send an RPC with a custom struct where you efficiently encode the hit locations or maybe you give it a random seed number to recreate/approximate the impact locations on the receiving side. The clients would then unpack this custom struct and turn back into [locally executed `GameplayCues`](#concepts-gc-local).

8개의 알약을 쏘는 산탄총이 있다고 가정해 보겠습니다. 8개의 추적 및 영향 'GameplayCues'입니다. GASShooter는 모든 추적 정보를 `TargetData`로 `EffectContext`에 숨김으로써 하나의 RPC로 결합하는 게으른 접근 방식을 취합니다. 이렇게 하면 RPC가 8개에서 1개로 줄어들지만 여전히 네트워크를 통해 많은 데이터를 전송합니다. 하나의 RPC(~500바이트). 보다 최적화된 접근 방식은 적중 위치를 효율적으로 인코딩하거나 수신 측의 영향 위치를 재창조/근사화하기 위해 임의의 시드 번호를 제공하는 사용자 지정 구조체와 함께 RPC를 보내는 것입니다. 그런 다음 클라이언트는 이 커스텀 구조체의 압축을 풀고 로컬에서 실행되는 `GameplayCues`로 돌아갑니다.

How this works:
1. Declare a `FScopedGameplayCueSendContext`. This suppresses `UGameplayCueManager::FlushPendingCues()` until it falls out of scope, meaning all `GameplayCues` will be queued up until the `FScopedGameplayCueSendContext` falls out of scope.
1. Override `UGameplayCueManager::FlushPendingCues()` to merge `GameplayCues` that can be batched together based on some custom `GameplayTag` into your custom struct and RPC it to clients.
1. Clients receive the custom struct and unpack it into locally executed `GameplayCues`.


작동 방식:
1. 'FScopedGameplayCueSendContext'를 선언합니다. 이렇게 하면 범위를 벗어날 때까지 `UGameplayCueManager::FlushPendingCues()`가 억제됩니다. 즉, 모든 `GameplayCues`는 `FScopedGameplayCueSendContext`가 범위를 벗어날 때까지 대기열에 추가됩니다.
1. `UGameplayCueManager::FlushPendingCues()`를 재정의하여 일부 사용자 정의 `GameplayTag`를 기반으로 일괄 처리할 수 있는 `GameplayCues`를 사용자 정의 구조체에 병합하고 이를 클라이언트에 RPC합니다.
1. 클라이언트는 커스텀 구조체를 받아 로컬에서 실행되는 `GameplayCues`에 압축을 풉니다.


This method can also be used when you need specific parameters for your `GameplayCues` that don't fit with what `GameplayCueParameters` offer and you don't want to add them to the `EffectContext` like damage numbers, crit indicator, broken shield indicator, was fatal hit indicator, etc.

이 방법은 `GameplayCueParameters`가 제공하는 것과 맞지 않는 `GameplayCues`에 대한 특정 매개변수가 필요하고 피해 수치, 치명타 표시기, 깨진 방패와 같이 `EffectContext`에 추가하고 싶지 않은 경우에도 사용할 수 있습니다. 표시기, 치명타 표시기 등이었습니다.

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>
##### 4.8.7.2 Multiple GCs on one GE
All of the `GameplayCues` on a `GameplayEffect` are sent in one RPC already. By default, `UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()` will send the whole `GameplayEffectSpec` (but converted to `FGameplayEffectSpecForRPC`) in the unreliable NetMulticast regardless of the `ASC`'s `Replication Mode`. This could potentially be a lot of bandwidth depending on what is in the `GameplayEffectSpec`. We can potentially optimize this by setting the cvar `AbilitySystem.AlwaysConvertGESpecToGCParams 1`. This will convert `GameplayEffectSpecs` to `FGameplayCueParameter` structures and RPC those instead of the whole `FGameplayEffectSpecForRPC`. This potentially saves bandwidth but also has less information, depending on how the `GESpec` is converted to `GameplayCueParameters` and what your `GCs` need to know.

'GameplayEffect'의 모든 'GameplayCues'는 이미 하나의 RPC로 전송됩니다. 기본적으로 `UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()`은 `ASC`의 `복제 모드`에 관계없이 신뢰할 수 없는 NetMulticast에서 전체 `GameplayEffectSpec`(그러나 `FGameplayEffectSpecForRPC`로 변환됨)을 보냅니다. 이것은 `GameplayEffectSpec`에 무엇이 있는지에 따라 잠재적으로 많은 대역폭이 될 수 있습니다. cvar `AbilitySystem.AlwaysConvertGESpecToGCParams 1`을 설정하여 이를 잠재적으로 최적화할 수 있습니다. 이것은 `GameplayEffectSpecs`를 `FGameplayCueParameter` 구조로 변환하고 전체 `FGameplayEffectSpecForRPC` 대신 RPC를 RPC합니다. 이렇게 하면 잠재적으로 대역폭을 절약할 수 있지만 `GESpec`이 `GameplayCueParameters`로 변환되는 방식과 `GC`가 알아야 할 사항에 따라 정보가 더 적습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-events"></a>
#### 4.8.8 Gameplay Cue Events
`GameplayCues` respond to specific `EGameplayCueEvents`:

| `EGameplayCueEvent` | Description                                                                                                                                                                                                                                                                                                                         |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OnActive`          | Called when a `GameplayCue` is activated (added).                                                                                                                                                                                                                                                                                   |
| `WhileActive`       | Called when `GameplayCue` is active, even if it wasn't actually just applied (Join in progress, etc). This is not `Tick`! It's called once just like `OnActive` when a `GameplayCueNotify_Actor` is added or becomes relevant. If you need `Tick()`, just use the `GameplayCueNotify_Actor`'s `Tick()`. It's an `AActor` after all. |
| `Removed`           | Called when a `GameplayCue` is removed. The Blueprint `GameplayCue` function that responds to this event is `OnRemove`.                                                                                                                                                                                                             |
| `Executed`          | Called when a `GameplayCue` is executed: instant effects or periodic `Tick()`. The Blueprint `GameplayCue` function that responds to this event is `OnExecute`.                                                                                                                                                                     |

`GameplayCues`는 특정 `EGameplayCueEvents`에 응답합니다.

| `EGameplayCueEvent` | 설명 |
| ------------------- | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- ----------------------- |
| `온액티브` | 'GameplayCue'가 활성화(추가)되면 호출됩니다. |
| `활성 상태에서` | 'GameplayCue'가 활성화될 때 호출됩니다. 실제로 방금 적용되지 않았더라도(Join in progress 등). 이것은 '틱'이 아닙니다! 'GameplayCueNotify_Actor'가 추가되거나 관련될 때 'OnActive'처럼 한 번 호출됩니다. `Tick()`이 필요한 경우 `GameplayCueNotify_Actor`의 `Tick()`을 사용하세요. 결국 'AActor'다. |
| `제거됨` | 'GameplayCue'가 제거될 때 호출됩니다. 이 이벤트에 응답하는 블루프린트 'GameplayCue' 함수는 'OnRemove'입니다. |
| `실행` | 'GameplayCue'가 실행될 때 호출됩니다: 인스턴트 효과 또는 주기적 'Tick()'. 이 이벤트에 응답하는 블루프린트 'GameplayCue' 함수는 'OnExecute'입니다. |


Use `OnActive` for anything in your `GameplayCue` that happen at the start of the `GameplayCue` but is okay if late joiners miss. Use `WhileActive` for ongoing effects in the `GameplayCue` that you would want late joiners to see. For example, if you have a `GameplayCue` for a tower structure in a MOBA exploding, you would put the initial explosion particle system and explosion sound in `OnActive` and you would put any residual ongoing fire particles or sounds in the `WhileActive`. In this scenario, it wouldn't make sense for late joiners to replay the initial explosion from `OnActive`, but you would want them to see the persistent, looping fire effects on the ground after the explosion happened from `WhileActive`. `OnRemove` should clean up anything added in `OnActive` and `WhileActive`. `WhileActive` will be called every time an Actor enters the relevancy range of a `GameplayCueNotify_Actor`. `OnRemove` will be called every time an Actor leaves relevancy range of a `GameplayCueNotify_Actor`.

'Gameplay Cue'가 시작될 때 발생하는 'Gameplay Cue'의 모든 항목에 대해 'OnActive'를 사용하지만 늦게 참여하는 사람이 놓치면 괜찮습니다. 늦게 합류하는 사람들이 보고 싶은 `GameplayCue`의 지속적인 효과를 보려면 `WhileActive`를 사용하십시오. 예를 들어, MOBA 폭발의 타워 구조에 대한 'GameplayCue'가 있는 경우 초기 폭발 입자 시스템과 폭발 사운드를 'OnActive'에 넣고 진행 중인 잔여 화재 입자나 소리를 'WhileActive'에 넣습니다. . 이 시나리오에서 후기 합류자가 'OnActive'의 초기 폭발을 재생하는 것은 이치에 맞지 않지만, 'WhileActive'에서 폭발이 발생한 후 지상에서 지속적이고 반복되는 화재 효과를 보기를 원할 것입니다. `OnRemove`는 `OnActive` 및 `WhileActive`에 추가된 모든 것을 정리해야 합니다. 액터가 'GameplayCueNotify_Actor'의 관련성 범위에 들어갈 때마다 'WhileActive'가 호출됩니다. 액터가 'GameplayCueNotify_Actor'의 관련성 범위를 벗어날 때마다 'OnRemove'가 호출됩니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-reliability"></a>
#### 4.8.9 Gameplay Cue Reliability

`GameplayCues` in general should be considered unreliable and thus unsuited for anything that directly affects gameplay.

일반적으로 `GameplayCues`는 신뢰할 수 없는 것으로 간주되어야 하므로 게임 플레이에 직접적인 영향을 미치는 모든 것에 적합하지 않습니다.

**Executed `GameplayCues`:** These `GameplayCues` are applied via unreliable multicasts and are always unreliable.

**실행된 `GameplayCues`:** 이 `GameplayCues`는 신뢰할 수 없는 멀티캐스트를 통해 적용되며 항상 신뢰할 수 없습니다.

**`GameplayCues` applied from `GameplayEffects`:**
* Autonomous proxy reliably receives `OnActive`, `WhileActive`, and `OnRemove`  
`FActiveGameplayEffectsContainer::NetDeltaSerialize()` calls `UAbilitySystemComponent::HandleDeferredGameplayCues()` to call `OnActive` and `WhileActive`. `FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()` makes the call to `OnRemoved`.
* Simulated proxies reliably receive `WhileActive` and `OnRemove`  
`UAbilitySystemComponent::MinimalReplicationGameplayCues`'s replication calls `WhileActive` and `OnRemove`. The `OnActive` event is called by an unreliable multicast.

**`GameplayEffects`에서 적용된 `GameplayCues`:**
* 자율 프록시는 'OnActive', 'WhileActive' 및 'OnRemove'를 안정적으로 수신합니다.
`FACiveGameplayEffectsContainer::NetDeltaSerialize()`는 `UAbilitySystemComponent::HandleDeferredGameplayCues()`를 호출하여 `OnActive` 및 `WhileActive`를 호출합니다. `FACiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()`는 `OnRemoved`를 호출합니다.
* 시뮬레이션된 프록시는 'WhileActive' 및 'OnRemove'를 안정적으로 수신합니다.
`UAbilitySystemComponent::MinimalReplicationGameplayCues`의 복제는 `WhileActive` 및 `OnRemove`를 호출합니다. 'OnActive' 이벤트가 신뢰할 수 없는 멀티캐스트에 의해 호출되었습니다.


**`GameplayCues` applied without a `GameplayEffect`:**
* Autonomous proxy reliably receives `OnRemove`  
The `OnActive` and `WhileActive` events are called by an unreliable multicast.
* Simulated proxies reliably receive `WhileActive` and `OnRemove`  
`UAbilitySystemComponent::MinimalReplicationGameplayCues`'s replication calls `WhileActive` and `OnRemove`. The `OnActive` event is called by an unreliable multicast.

**`GameplayEffect` 없이 적용된 `GameplayCues`:**
* 자율 프록시는 'OnRemove'를 안정적으로 수신합니다.
'OnActive' 및 'WhileActive' 이벤트는 신뢰할 수 없는 멀티캐스트에 의해 호출됩니다.
* 시뮬레이션된 프록시는 'WhileActive' 및 'OnRemove'를 안정적으로 수신합니다.
`UAbilitySystemComponent::MinimalReplicationGameplayCues`의 복제는 `WhileActive` 및 `OnRemove`를 호출합니다. 'OnActive' 이벤트가 신뢰할 수 없는 멀티캐스트에 의해 호출되었습니다.


If you need something in a `GameplayCue` to be 'reliable', then apply it from a `GameplayEffect` and use `WhileActive` to add the FX and `OnRemove` to remove the FX.

`GameplayCue`에서 '신뢰할 수 있는' 것이 필요한 경우 `GameplayEffect`에서 적용하고 `WhileActive`를 사용하여 FX를 추가하고 `OnRemove`를 사용하여 FX를 제거합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asg"></a>
### 4.9 Ability System Globals
The [`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html) class holds global information about GAS. Most of the variables can be set from the `DefaultGame.ini`. Generally you won't have to interact with this class, but you should be aware of its existence. If you need to subclass things like the [`GameplayCueManager`](#concepts-gc-manager) or the [`GameplayEffectContext`](#concepts-ge-context), you have to do that through the `AbilitySystemGlobals`.

'AbilitySystemGlobals' 클래스는 GAS에 대한 전역 정보를 보유합니다. 대부분의 변수는 'DefaultGame.ini'에서 설정할 수 있습니다. 일반적으로 이 클래스와 상호 작용할 필요는 없지만 그 존재를 알고 있어야 합니다. 'GameplayCueManager'와 같은 것을 서브클래스화해야 하는 경우 'AbilitySystemGlobals'를 통해 이를 수행해야 합니다.

To subclass `AbilitySystemGlobals`, set the class name in the `DefaultGame.ini`:
`AbilitySystemGlobals`를 하위 클래스로 지정하려면 `DefaultGame.ini`에서 클래스 이름을 설정합니다.:
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>
#### 4.9.1 InitGlobalData()
Starting in UE 4.24, it is now necessary to call `UAbilitySystemGlobals::Get().InitGlobalData()` to use [`TargetData`](#concepts-targeting-data), otherwise you will get errors related to `ScriptStructCache` and clients will be disconnected from the server. This function only needs to be called once in a project. Fortnite calls it from `UAssetManager::StartInitialLoading()` and Paragon called it from `UEngine::Init()`. I find that putting it in `UAssetManager::StartInitialLoading()` is a good place as shown in the Sample Project. I would consider this boilerplate code that you should copy into your project to avoid issues with `TargetData`.

UE 4.24 부터는 `TargetData`를 사용하기 위해 `UAbilitySystemGlobals::Get().InitGlobalData()`를 호출해야 합니다. 샘플 프로젝트에서 볼 수 있듯이 좋은 곳입니다. 'TargetData' 문제를 피하기 위해 프로젝트에 복사해야 하는 이 상용구 코드를 고려할 것입니다.

If you run into a crash while using the `AbilitySystemGlobals` `GlobalAttributeSetDefaultsTableNames`, you may need to call `UAbilitySystemGlobals::Get().InitGlobalData()` later like Fortnite in the `AssetManager` or in the `GameInstance`.

`AbilitySystemGlobals` `GlobalAttributeSetDefaultsTableNames`를 사용하는 동안 충돌이 발생하면 나중에 `AssetManager` 또는 `GameInstance`에서 Fortnite처럼 `UAbilitySystemGlobals::Get().InitGlobalData()`를 호출해야 할 수도 있습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p"></a>
### 4.10 Prediction
GAS comes out of the box with support for client-side prediction; however, it does not predict everything. Client-side prediction in GAS means that the client does not have to wait for the server's permission to activate a `GameplayAbility` and apply `GameplayEffects`. It can "predict" the server giving it permission to do this and predict the targets that it would apply `GameplayEffects` to. The server then runs the `GameplayAbility` network latency-time after the client activates and tells the client if he was correct or not in his predictions. If the client was wrong in any of his predictions, he will "roll back" his changes from his "mispredictions" to match the server.

GAS는 클라이언트 측 예측을 지원하는 즉시 사용 가능합니다. 그러나 모든 것을 예측하지는 않습니다. GAS의 클라이언트 측 예측은 클라이언트가 'GameplayAbility'를 활성화하고 'GameplayEffects'를 적용하기 위해 서버의 권한을 기다릴 필요가 없음을 의미합니다. 이 작업을 수행할 수 있는 권한을 부여하는 서버를 "예측"하고 `GameplayEffects`를 적용할 대상을 예측할 수 있습니다. 그런 다음 서버는 클라이언트가 활성화된 후 'GameplayAbility' 네트워크 대기 시간을 실행하고 클라이언트가 예측에서 맞았는지 아닌지 알려줍니다. 클라이언트가 그의 예측 중 하나라도 틀렸다면, 그는 서버와 일치시키기 위해 자신의 "잘못 예측"에서 변경 사항을 "롤백"할 것입니다.

The definitive source for GAS-related prediction is `GameplayPrediction.h` in the plugin source code.

GAS 관련 예측의 최종 소스는 플러그인 소스 코드의 'GameplayPrediction.h'입니다.

Epic's mindset is to only predict what you "can get away with". For example, Paragon and Fortnite do not predict damage. Most likely they use [`ExecutionCalculations`](#concepts-ge-ec) for their damage which cannot be predicted anyway. This is not to say that you can't try to predict certain things like damage. By all means if you do it and it works well for you then that's great.

Epic의 사고 방식은 "도망가질 수 있는 것"만 예측하는 것입니다. 예를 들어 Paragon과 Fortnite는 피해를 예측하지 않습니다. 그들은 어쨌든 예측할 수 없는 피해를 위해 'ExecutionCalculations'를 사용했을 가능성이 큽니다. 이것은 손상과 같은 특정 사항을 예측할 수 없다는 말은 아닙니다. 어쨌든 당신이 그것을하고 그것이 당신을 위해 잘 작동한다면 그것은 훌륭합니다.

> ... we are also not all in on a "predict everything: seamlessly and automatically" solution. We still feel player prediction is best kept to a minimum (meaning: predict the minimum amount of stuff you can get away with).

> ... 또한 "모든 것을 완벽하게 자동으로 예측"하는 솔루션에 모두 참여하지 않습니다. 우리는 여전히 플레이어 예측이 최소한으로 유지되는 것이 가장 좋다고 생각합니다(즉, 피할 수 있는 최소한의 양을 예측).

*Dave Ratti from Epic's comment from the new [Network Prediction Plugin](#concepts-p-npp)*

**What is predicted:**
> * Ability activation
> *	Triggered Events
> *	GameplayEffect application:
>    * Attribute modification (EXCEPTIONS: Executions do not currently predict, only attribute modifiers)
>    * GameplayTag modification
> * Gameplay Cue events (both from within predictive gameplay effect and on their own)
> * Montages
> * Movement (built into UE5 UCharacterMovement)

**What is not predicted:**
> * GameplayEffect removal
> * GameplayEffect periodic effects (dots ticking)

*From `GameplayPrediction.h`*

*새로운 네트워크 예측 플러그인에 대한 에픽의 댓글 Dave Ratti*

**예상되는 내용:**
> * 능력 활성화
> *	트리거된 이벤트
> *	GameplayEffect 응용 프로그램:
> * 속성 수정(예외: 실행은 현재 예측하지 않고 속성 수정자만)
> * GameplayTag 수정
> * 게임 플레이 큐 이벤트(예측 게임 플레이 효과 내에서 및 자체적으로 모두)
> * 몽타주
> * 움직임 (UE5 UCharacterMovement에 내장)

**예상되지 않은 사항:**
> * GameplayEffect 제거
> * GameplayEffect 주기적인 효과(점 똑딱거림)

*'GameplayPrediction.h'에서 제공*




While we can predict `GameplayEffect` application, we cannot predict `GameplayEffect` removal. One way that we can work around this limitation is to predict the inverse effect when we want to remove a `GameplayEffect`. Say we predict a movement speed slow of 40%. We can predictively remove it by applying a movement speed buff of 40%. Then remove both `GameplayEffects` at the same time. This is not appropriate for every scenario and support for predicting `GameplayEffect` removal is still needed. Dave Ratti from Epic has expressed desire to add it to a [future iteration of GAS](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89).

'GameplayEffect' 애플리케이션은 예측할 수 있지만 'GameplayEffect' 제거는 예측할 수 없습니다. 이 제한을 해결할 수 있는 한 가지 방법은 'GameplayEffect'를 제거하려고 할 때 역효과를 예측하는 것입니다. 이동 속도가 40% 느려질 것으로 예측한다고 가정해 보겠습니다. 40%의 이동 속도 버프를 적용하여 예측적으로 제거할 수 있습니다. 그런 다음 두 'GameplayEffects'를 동시에 제거합니다. 이는 모든 시나리오에 적합하지 않으며 'GameplayEffect' 제거 예측에 대한 지원이 여전히 필요합니다. Epic의 Dave Ratti는 GAS의 향후 버전에 이를 추가하고 싶다고 밝혔습니다.

Because we cannot predict the removal of `GameplayEffects`, we cannot fully predict `GameplayAbility` cooldowns and there is no inverse `GameplayEffect` workaround for them. The server's replicated `Cooldown GE` will exist on the client and any attempts to bypass this (with `Minimal` replication mode for example) will be rejected by the server. This means clients with higher latencies take longer to tell the server to go on cooldown and to receive the removal of the server's `Cooldown GE`. This means players with higher latencies will have a lower rate of fire than players with lower latencies, giving them a disadvantage against lower latency players. Fortnite avoids this issue by using custom bookkeeping instead of `Cooldown GEs`.

'GameplayEffects'의 제거를 예측할 수 없기 때문에 'GameplayAbility' 쿨다운을 완전히 예측할 수 없으며 이에 대한 역 'GameplayEffect' 해결 방법도 없습니다. 서버의 복제된 '쿨다운 GE'가 클라이언트에 존재하며 이를 우회하려는 모든 시도(예: '최소' 복제 모드 사용)는 서버에서 거부됩니다. 이는 대기 시간이 더 긴 클라이언트가 서버에 쿨다운을 시작하고 서버의 '쿨다운 GE' 제거를 수신하도록 지시하는 데 더 오래 걸린다는 것을 의미합니다. 즉, 지연 시간이 긴 플레이어는 지연 시간이 짧은 플레이어보다 발사 속도가 낮아 지연 시간이 짧은 플레이어에게 불리합니다. Fortnite는 `Cooldown GEs` 대신 사용자 지정 부기를 사용하여 이 문제를 방지합니다.


Regarding predicting damage, I personally do not recommend it despite it being one of the first things that most people try when starting with GAS. I especially do not recommend trying to predict death. While you can predict damage, doing so is tricky. If you mispredict applying damage, the player will see the enemy's health jump back up. This can be especially awkward and frustrating if you try to predict death. Say you mispredict a `Character's` death and it starts ragdolling only to stop ragdolling and continue shooting at you when the server corrects it.

피해 예측에 관해서는 대부분의 사람들이 GAS를 시작할 때 가장 먼저 시도하는 것 중 하나임에도 불구하고 개인적으로 권장하지 않습니다. 나는 특히 죽음을 예측하려고 시도하지 않는 것이 좋습니다. 피해를 예측할 수는 있지만 그렇게 하는 것은 까다롭습니다. 피해 적용을 잘못 예측하면 플레이어는 적의 체력이 다시 상승하는 것을 볼 수 있습니다. 죽음을 예측하려고 하면 특히 어색하고 답답할 수 있습니다. '캐릭터'의 죽음을 잘못 예측했는데 서버가 수정하면 래그돌링을 멈추고 계속해서 당신에게 총을 쏘기 위해 랙돌링을 시작한다고 가정해 보겠습니다.

**Note:** `Instant`	`GameplayEffects` (like `Cost GEs`) that change `Attributes` can be predicted on yourself seamlessly, predicting `Instant` `Attribute` changes to other characters will show a brief anomaly or "blip" in their `Attributes`. Predicted `Instant` `GameplayEffects` are actually treated like `Infinite` `GameplayEffects` so that they can be rolled back if mispredicted. When the server's `GameplayEffect` is applied, there potentially exists two of the same `GameplayEffect's` causing the `Modifier` to be applied twice or not at all for a brief moment. It will eventually correct itself but sometimes the blip is noticeable to players.

**참고:** `인스턴트`	'속성'을 변경하는 'GameplayEffects'(예: '비용 GE')는 자신에 대해 원활하게 예측할 수 있습니다.'속성'에 간단한 이상 또는 "점점"을 표시합니다. 예측된 `Instant` `GameplayEffects`는 실제로 `Infinite` `GameplayEffects`로 처리되어 잘못 예측된 경우 롤백할 수 있습니다. 서버의 'GameplayEffect'가 적용될 때, 잠재적으로 동일한 'GameplayEffect'가 2개 존재하여 'Modifier'가 두 번 적용되거나 잠시 동안 전혀 적용되지 않을 수 있습니다. 결국 자체적으로 수정되지만 때때로 플레이어에게 오류가 눈에 띄게 됩니다.

Problems that GAS's prediction implementation is trying to solve:
> 1. "Can I do this?" Basic protocol for prediction.
> 2. "Undo" How to undo side effects when a prediction fails.
> 3. "Redo" How to avoid replaying side effects that we predicted locally but that also get replicated from the server.
> 4. "Completeness" How to be sure we /really/ predicted all side effects.
> 5. "Dependencies" How to manage dependent prediction and chains of predicted events.
> 6. "Override" How to override state predictively that is otherwise replicated/owned by the server.

GAS의 예측 구현이 해결하려는 문제:
> 1. "이걸 해도 될까요?" 예측을 위한 기본 프로토콜.
> 2. "실행 취소" 예측 실패 시 부작용을 실행 취소하는 방법입니다.
> 3. "다시 실행" 로컬에서 예측했지만 서버에서도 복제되는 부작용을 다시 재생하지 않는 방법.
> 4. "완전성" 모든 부작용을 /실제로/예측했는지 확인하는 방법.
> 5. "종속성" 종속 예측 및 예측 이벤트 체인을 관리하는 방법.
> 6. "Override" 서버가 복제/소유하는 상태를 예측적으로 재정의하는 방법.


*From `GameplayPrediction.h`*

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-key"></a>
#### 4.10.1 Prediction Key
GAS's prediction works on the concept of a `Prediction Key` which is an integer identifier that the client generates when he activates a `GameplayAbility`.

* Client generates a prediction key when it activates a `GameplayAbility. This is the `Activation Prediction Key`.
* Client sends this prediction key to the server with `CallServerTryActivateAbility()`.
* Client adds this prediction key to all `GameplayEffects` that it applies while the prediction key is valid.
* Client's prediction key falls out of scope. Further predicted effects in the same `GameplayAbility` need a new [Scoped Prediction Window](#concepts-p-windows).


* Server receives the prediction key from the client.
* Server adds this prediction key to all `GameplayEffects` that it applies.
* Server replicates the prediction key back to the client.


* Client receives replicated `GameplayEffects` from the server with the prediction key used to apply them. If any of the replicated `GameplayEffects` match the `GameplayEffects` that the client applied with the same prediction key, they were predicted correctly. There will temporarily be two copies of the `GameplayEffect` on the target until the client removes its predicted one.
* Client receives the prediction key back from the server. This is the `Replicated Prediction Key`. This prediction key is now marked stale.
* Client removes **all** `GameplayEffects` that it created with the now stale replicated prediction key. `GameplayEffects` replicated by the server will persist. Any `GameplayEffects` that the client added and didn't receive a matching replicated version from the server were mispredicted.


GAS의 예측은 클라이언트가 'GameplayAbility'를 활성화할 때 생성하는 정수 식별자인 '예측 키'의 개념에서 작동합니다.

* 클라이언트는 'GameplayAbility'를 활성화할 때 예측 키를 생성합니다. '활성화 예측 키'입니다.
* 클라이언트는 'CallServerTryActivateAbility()'를 사용하여 이 예측 키를 서버에 보냅니다.
* 클라이언트는 예측 키가 유효한 동안 적용되는 모든 'GameplayEffects'에 이 예측 키를 추가합니다.
* 클라이언트의 예측 키는 범위를 벗어납니다. 동일한 'GameplayAbility'의 추가 예측 효과에는 새로운 범위 예측 창이 필요합니다.


* 서버는 클라이언트로부터 예측 키를 받습니다.
* 서버는 이 예측 키를 적용하는 모든 'GameplayEffects'에 추가합니다.
* 서버는 예측 키를 클라이언트에 다시 복제합니다.


* 클라이언트는 서버에서 복제된 'GameplayEffects'를 적용하는 데 사용된 예측 키와 함께 받습니다. 복제된 'GameplayEffects' 중 클라이언트가 동일한 예측 키로 적용한 'GameplayEffects'와 일치하는 것이 있으면 올바르게 예측된 것입니다. 클라이언트가 예측한 것을 제거할 때까지 대상에 일시적으로 두 개의 'GameplayEffect' 복사본이 있습니다.
* 클라이언트는 서버로부터 예측 키를 다시 받습니다. '복제된 예측 키'입니다. 이 예측 키는 이제 오래된 것으로 표시됩니다.
* 클라이언트는 이제 부실한 복제 예측 키로 생성한 **모든** `GameplayEffects`를 제거합니다. 서버에 의해 복제된 `GameplayEffects`는 지속됩니다. 클라이언트가 추가했지만 서버에서 일치하는 복제 버전을 받지 못한 모든 'GameplayEffects'가 잘못 예측되었습니다.


Prediction keys are guaranteed to be valid during an atomic grouping of instructions "window" in `GameplayAbilities` starting with `Activation` from the activation prediction key. You can think of this as being only valid during one frame. Any callbacks from latent action `AbilityTasks` will no longer have a valid prediction key unless the `AbilityTask` has a built-in Synch Point which generates a new [Scoped Prediction Window](#concepts-p-windows).

예측 키는 활성화 예측 키의 '활성화'로 시작하는 'GameplayAbilities'의 명령어 "창"을 원자적으로 그룹화하는 동안 유효하도록 보장됩니다. 이것은 한 프레임 동안만 유효하다고 생각할 수 있습니다. 잠재 작업 'AbilityTasks'의 모든 콜백은 'AbilityTask'에 새 범위 예측 창을 생성하는 내장 동기화 포인트가 없는 한 더 이상 유효한 예측 키를 갖지 않습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-windows"></a>
#### 4.10.2 Creating New Prediction Windows in Abilities
To predict more actions in callbacks from `AbilityTasks`, we need to create a new Scoped Prediction Window with a new Scoped Prediction Key. This is sometimes referred to as a Synch Point between the client and server. Some `AbilityTasks` like all of the input related ones come with built-in functionality to create a new scoped prediction window, meaning atomic code in the `AbilityTasks'` callbacks have a valid scoped prediction key to use. Other tasks like the `WaitDelay` task do not have built-in code to create a new scoped prediction window for its callback. If you need to predict actions after an `AbilityTask` that does not have built-in code to create a scoped prediction window like `WaitDelay`, we must manually do that using the `WaitNetSync` `AbilityTask` with the option `OnlyServerWait`. When the client hits a `WaitNetSync` with `OnlyServerWait`, it generates a new scoped prediction key based on the `GameplayAbility's` activation prediction key, RPCs it to the server, and adds it to any new `GameplayEffects` that it applies. When the server hits a `WaitNetSync` with `OnlyServerWait`, it waits until it receives the new scoped prediction key from the client before continuing. This scoped prediction key does the same dance as activation prediction keys - applied to `GameplayEffects` and replicated back to clients to be marked stale. The scoped prediction key is valid until it falls out of scope, meaning the scoped prediction window has closed. So again, only atomic operations, nothing latent, can use the new scoped prediction key.

'AbilityTasks'의 콜백에서 더 많은 작업을 예측하려면 새 범위 예측 키로 새 범위 예측 창을 만들어야 합니다. 이것은 때때로 클라이언트와 서버 간의 동기화 지점이라고 합니다. 모든 입력 관련 항목과 같은 일부 'AbilityTasks'에는 새로운 범위의 예측 창을 생성하는 기능이 내장되어 있습니다. 이는 'AbilityTasks' 콜백의 원자 코드에 사용할 유효한 범위의 예측 키가 있음을 의미합니다. 'WaitDelay' 작업과 같은 다른 작업에는 콜백에 대한 새 범위 예측 창을 만드는 내장 코드가 없습니다. 'WaitDelay'와 같은 범위가 지정된 예측 창을 생성하기 위해 내장 코드가 없는 'AbilityTask' 이후에 작업을 예측해야 하는 경우, 'OnlyServerWait' 옵션과 함께 'WaitNetSync' 'AbilityTask'를 사용하여 수동으로 수행해야 합니다. 클라이언트가 'OnlyServerWait'를 사용하여 'WaitNetSync'에 도달하면 'GameplayAbility' 활성화 예측 키를 기반으로 새로운 범위의 예측 키를 생성하고, 이를 서버에 RPC하고, 적용하는 새 'GameplayEffects'에 추가합니다. 서버가 'OnlyServerWait'로 'WaitNetSync'에 도달하면 계속하기 전에 클라이언트로부터 새로운 범위의 예측 키를 받을 때까지 기다립니다. 이 범위가 지정된 예측 키는 활성화 예측 키와 동일한 춤을 추며 'GameplayEffects'에 적용되고 클라이언트에 다시 복제되어 오래된 것으로 표시됩니다. 범위가 지정된 예측 키는 범위를 벗어날 때까지 유효합니다. 즉, 범위가 지정된 예측 창이 닫힙니다. 다시 말하지만, 잠재성이 없는 원자적 연산만 새로운 범위의 예측 키를 사용할 수 있습니다.


You can create as many scoped prediction windows as you need.

필요한 만큼 범위가 지정된 예측 기간을 만들 수 있습니다.

If you would like to add the synch point functionality to your own custom `AbilityTasks`, look at how the input ones essentially inject the `WaitNetSync` `AbilityTask` code into them.

자신의 커스텀 'AbilityTasks'에 동기화 지점 기능을 추가하려면 입력 항목이 본질적으로 'WaitNetSync' 'AbilityTask' 코드를 입력하는 방법을 살펴보세요.

**Note:** When using `WaitNetSync`, this does block the server's `GameplayAbility` from continuing execution until it hears from the client. This could potentially be abused by malicious users who hack the game and intentionally delay sending their new scoped prediction key. While Epic uses the `WaitNetSync` sparingly, it recommends potentially building a new version of the `AbilityTask` with a delay that automatically continues without the client if this is a concern for you.

**참고:** `WaitNetSync`를 사용할 때 클라이언트에서 들을 때까지 서버의 `GameplayAbility`가 계속 실행되는 것을 차단합니다. 이것은 게임을 해킹하고 의도적으로 새로운 범위의 예측 키 전송을 지연하는 악의적인 사용자에 의해 악용될 수 있습니다. Epic은 'WaitNetSync'를 드물게 사용하지만, 이것이 우려되는 경우 클라이언트 없이 자동으로 계속되는 지연을 사용하여 'AbilityTask'의 새 버전을 잠재적으로 빌드할 것을 권장합니다.

The Sample Project uses `WaitNetSync` in the Sprint `GameplayAbility` to create a new scoped prediction window every time we apply the stamina cost so that we can predict it. Ideally we want a valid prediction key when applying costs and cooldowns.

샘플 프로젝트는 스프린트 'GameplayAbility'의 'WaitNetSync'를 사용하여 예측할 수 있도록 스태미나 비용을 적용할 때마다 새로운 범위의 예측 창을 만듭니다. 이상적으로는 비용과 재사용 대기시간을 적용할 때 유효한 예측 키가 필요합니다.

If you have a predicted `GameplayEffect` that is playing twice on the owning client, your prediction key is stale and you're experiencing the "redo" problem. You can usually solve this by putting a `WaitNetSync` `AbilityTask` with `OnlyServerWait` right before you apply the `GameplayEffect` to create a new scoped prediction key.

소유 클라이언트에서 두 번 재생되는 예측된 'GameplayEffect'가 있는 경우 예측 키가 오래되어 "다시 실행" 문제가 발생합니다. 일반적으로 `GameplayEffect`를 적용하여 새로운 범위의 예측 키를 생성하기 직전에 `WaitNetSync` `AbilityTask`를 `OnlyServerWait`과 함께 넣으면 이 문제를 해결할 수 있습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-spawn"></a>
#### 4.10.3 Predictively Spawning Actors
Spawning `Actors` predictively on clients is an advanced topic. GAS does not provide functionality to handle this out of the box (the `SpawnActor` `AbilityTask` only spawns the `Actor` on the server). The key concept is to spawn a replicated `Actor` on both the client and the server.

클라이언트에 예측적으로 '액터'를 생성하는 것은 고급 주제입니다. GAS는 이것을 즉시 처리하는 기능을 제공하지 않습니다(`SpawnActor` `AbilityTask`는 서버에서 `Actor`만 생성합니다). 핵심 개념은 클라이언트와 서버 모두에 복제된 '액터'를 생성하는 것입니다.

If the `Actor` is just cosmetic or doesn't serve any gameplay purpose, the simple solution is to override the `Actor's` `IsNetRelevantFor()` function to restrict the server from replicating to the owning client. The owning client would have his locally spawned version and the server and other clients would have the server's replicated version.

'액터'가 단지 장식용이거나 게임플레이 목적을 제공하지 않는 경우 간단한 해결책은 '액터'의 'IsNetRelevantFor()' 함수를 재정의하여 서버가 소유 클라이언트로 복제하는 것을 제한하는 것입니다. 소유 클라이언트는 로컬에서 생성된 버전을 갖고 서버와 다른 클라이언트는 서버의 복제된 버전을 갖게 됩니다.
```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

If the spawned `Actor` affects gameplay like a projectile that needs to predict damage, then you need advanced logic that is outside of the scope of this documentation. Look at how UnrealTournament predictively spawns projectiles on Epic Games' GitHub. They have a dummy projectile spawned only on the owning client that synchs up with the server's replicated projectile.

생성된 '액터'가 손상을 예측해야 하는 발사체처럼 게임플레이에 영향을 미치는 경우 이 문서의 범위를 벗어나는 고급 로직이 필요합니다. UnrealTournament가 Epic Games의 GitHub에서 어떻게 발사체를 예측적으로 생성하는지 살펴보세요. 그들은 서버의 복제된 발사체와 동기화되는 소유 클라이언트에서만 생성된 더미 발사체가 있습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-future"></a>
#### 4.10.4 Future of Prediction in GAS
`GameplayPrediction.h` states in the future they could potentially add functionality for predicting `GameplayEffect` removal and periodic `GameplayEffects`.

`GameplayPrediction.h`는 향후 `GameplayEffect` 제거 및 주기적인 `GameplayEffects`를 예측하는 기능을 추가할 수 있다고 말합니다.

Dave Ratti from Epic has [expressed interest](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) in fixing the `latency reconciliation` problem for predicting cooldowns, disadvantaging players with higher latencies versus players with lower latencies.

Epic의 Dave Ratti는 대기 시간을 예측하는 '대기 시간 조정' 문제를 수정하는 데 관심을 표명하여 대기 시간이 짧은 플레이어에 비해 대기 시간이 긴 플레이어에게 불리합니다.

The new [`Network Prediction` plugin](#concepts-p-npp) by Epic is expected to be fully interoperable with the GAS like the `CharacterMovementComponent` *was* before it.

Epic의 새로운 `Network Prediction` 플러그인은 이전의 `CharacterMovementComponent`와 같이 GAS와 완전히 상호 운용될 것으로 예상됩니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-npp"></a>
#### 4.10.5 Network Prediction Plugin
Epic recently started an initiative to replace the `CharacterMovementComponent` with a new `Network Prediction` plugin. This plugin is still in its very early stages but is available to very early access on the Unreal Engine GitHub. It's too soon to tell which future version of the Engine that it will make its experimental beta debut in.

Epic은 최근 `CharacterMovementComponent`를 새로운 `Network Prediction` 플러그인으로 교체하기 위한 계획을 시작했습니다. 이 플러그인은 아직 초기 단계에 있지만 Unreal Engine GitHub에서 매우 조기에 액세스할 수 있습니다. 어떤 엔진의 미래 버전이 실험적 베타 데뷔를 할 것인지 말하기는 너무 이릅니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting"></a>
### 4.11 Targeting

<a name="concepts-targeting-data"></a>
#### 4.11.1 Target Data
[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html) is a generic structure for targeting data meant to be passed across the network. `TargetData` will typically hold `AActor`/`UObject` references, `FHitResults`, and other generic location/direction/origin information. However, you can subclass it to put essentially anything that you want inside of them as a simple means to [pass data between the client and server in `GameplayAbilities`](#concepts-ga-data). The base struct `FGameplayAbilityTargetData` is not meant to be used directly but instead subclassed. `GAS` comes with a few subclassed `FGameplayAbilityTargetData` structs out of the box located in `GameplayAbilityTargetTypes.h`.

'FGameplayAbilityTargetData'는 네트워크를 통해 전달되는 대상 데이터를 위한 일반 구조입니다. `TargetData`는 일반적으로 `AActor`/`UObject` 참조, `FHitResults` 및 기타 일반 위치/방향/원점 정보를 보유합니다. 그러나 'GameplayAbilities'에서 클라이언트와 서버 간에 데이터를 전달하는 간단한 수단으로 기본적으로 원하는 모든 것을 내부에 넣도록 하위 클래스화할 수 있습니다. 기본 구조체 'FGameplayAbilityTargetData'는 직접 사용하기 위한 것이 아니라 서브클래싱된 것입니다. `GAS`는 `GameplayAbilityTargetTypes.h`에 있는 몇 가지 서브클래싱된 `FGameplayAbilityTargetData` 구조체와 함께 제공됩니다.

`TargetData` is typically produced by [`Target Actors`](#concepts-targeting-actors) or **created manually** and consumed by [`AbilityTasks`](#concepts-at) and [`GameplayEffects`](#concepts-ge) via the [`EffectContext`](#concepts-ge-context). As a result of being in the `EffectContext`, [`Executions`](#concepts-ge-ec), [`MMCs`](#concepts-ge-mmc), [`GameplayCues`](#concepts-gc), and the functions on the backend of the [`AttributeSet`](#concepts-as) can access the `TargetData`.

'TargetData'는 일반적으로 'Target Actors'에 의해 생성되며 'TargetData'에 액세스할 수 있습니다.

We don't typically pass around the `FGameplayAbilityTargetData` directly, instead we use a [`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html) which has an internal TArray of pointers to `FGameplayAbilityTargetData`. This intermediate struct provides support for polymorphism of the `TargetData`.

일반적으로 `FGameplayAbilityTargetData`를 직접 전달하지 않고 대신 `FGameplayAbilityTargetData`에 대한 포인터의 내부 TArray가 있는 `FGameplayAbilityTargetDataHandle`을 사용합니다. 이 중간 구조체는 `TargetData`의 다형성을 지원합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-actors"></a>
#### 4.11.2 Target Actors
`GameplayAbilities` spawn [`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html) with the `WaitTargetData` `AbilityTask` to visualize and capture targeting information from the world. `TargetActors` may optionally use [`GameplayAbilityWorldReticles`](#concepts-targeting-reticles) to display current targets. Upon confirmation, the targeting information is returned as [`TargetData`](#concepts-targeting-data) which can then be passed into `GameplayEffects`.
 
 'GameplayAbilities'는 'WaitTargetData' 'AbilityTask'와 함께 'TargetActors'를 생성하여 세계의 타겟팅 정보를 시각화하고 캡처합니다. 'TargetActors'는 선택적으로 'GameplayAbilityWorldReticles'를 사용하여 'GameplayEffects'에 전달할 수 있습니다.
 
`TargetActors` are based on `AActor` so they can have any kind of visible component to represent **where** and **how** they are targeting such as static meshes or decals. Static meshes may be used to visualize placement of an object that your character will build. Decals may be used to show an area of effect on the ground. The Sample Project uses [`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html) with a decal on the ground to represent the damage area of effect for the Meteor ability. They also don't need to display anything either. For example it wouldn't make sense to display anything for a hitscan gun that instantly traces a line to its target as used in [GASShooter](https://github.com/tranek/GASShooter).

'TargetActors'는 'AActor'를 기반으로 하므로 정적 메시나 데칼과 같이 대상이 대상인 **위치**와 **방법**을 나타내는 모든 종류의 가시적 구성요소를 가질 수 있습니다. 스태틱 메시는 캐릭터가 만들 개체의 배치를 시각화하는 데 사용할 수 있습니다. 데칼은 지면의 효과 영역을 표시하는 데 사용할 수 있습니다. 샘플 프로젝트는 지면에 데칼이 있는 'AGameplayAbilityTargetActor_GroundTrace'를 사용하여 Meteor 능력의 피해 범위를 나타냅니다. 또한 아무것도 표시할 필요가 없습니다. 예를 들어 GASShooter 에서 사용되는 것처럼 표적에 대한 선을 즉시 추적하는 히트스캔 총에 대해 아무 것도 표시하지 않는 것은 이치에 맞지 않습니다.

They capture targeting information using basic traces or collision overlaps and convert the results as `FHitResults` or `AActor` arrays to `TargetData` depending on the `TargetActor` implementation. The `WaitTargetData` `AbilityTask` determines when the targets are confirmed through its `TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` parameter. When **not** using `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`, the `TargetActor` typically performs the trace/overlap on `Tick()` and updates its location to the `FHitResult` depending on its implementation. While this performs a trace/overlap on `Tick()`, it's generally not terrible since it's not replicated and you typically don't have more than one (although you could have more) `TargetActor` running at a time. Just be aware that it uses `Tick()` and some complex `TargetActors` might do a lot on it like the rocket launcher's secondary ability in GASShooter. While tracing on `Tick()` is very responsive to the client, you may consider lowering the tick rate on the `TargetActor` if the performance hit is too much. In the case of `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`, the `TargetActor` immediately spawns, produces `TargetData`, and destroys. `Tick()` is never called. 

기본 추적 또는 충돌 중첩을 사용하여 타겟팅 정보를 캡처하고 `TargetActor` 구현에 따라 `FHitResults` 또는 `AActor` 배열로 결과를 `TargetData`로 변환합니다. `WaitTargetData` `AbilityTask`는 `TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` 매개변수를 통해 대상이 확인되는 시점을 결정합니다. `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`를 사용 **하지 않을 때**, `TargetActor`는 일반적으로 `Tick()`에서 추적/중첩을 수행하고 구현에 따라 위치를 `FHitResult`로 업데이트합니다. 이것이 `Tick()`에 대한 추적/중복을 수행하는 동안 일반적으로 복제되지 않고 일반적으로 한 번에 실행되는 `TargetActor`가 두 개 이상(더 많이 가질 수 있지만) 없기 때문에 일반적으로 끔찍하지 않습니다. 그것은 `Tick()`을 사용하고 일부 복잡한 `TargetActors`는 GASShooter에서 로켓 발사기의 보조 능력처럼 많은 일을 할 수 있다는 점에 유의하십시오. `Tick()`에 대한 추적은 클라이언트에 매우 민감하지만 성능 적중이 너무 많은 경우 `TargetActor`의 틱 비율을 낮추는 것을 고려할 수 있습니다. `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`의 경우 `TargetActor`가 즉시 생성되어 `TargetData`를 생성하고 소멸합니다. 'Tick()'은 호출되지 않습니다. 

| `EGameplayTargetingConfirmation::Type` | When targets are confirmed                                                                                                                                                                                                                                                                                                                                     |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`                              | The targeting happens instantly without special logic or user input deciding when to 'fire'.                                                                                                                                                                                                                                                                   |
| `UserConfirmed`                        | The targeting happens when the user confirms the targeting when the [ability is bound to a `Confirm` input](#concepts-ga-input) or by calling `UAbilitySystemComponent::TargetConfirm()`. The `TargetActor` will also respond to a bound `Cancel` input or call to `UAbilitySystemComponent::TargetCancel()` to cancel targeting.                              |
| `Custom`                               | The GameplayTargeting Ability is responsible for deciding when the targeting data is ready by calling `UGameplayAbility::ConfirmTaskByInstanceName()`. The `TargetActor` will also respond to `UGameplayAbility::CancelTaskByInstanceName()` to cancel targeting.                                                                                              |
| `CustomMulti`                          | The GameplayTargeting Ability is responsible for deciding when the targeting data is ready by calling `UGameplayAbility::ConfirmTaskByInstanceName()`. The `TargetActor` will also respond to `UGameplayAbility::CancelTaskByInstanceName()` to cancel targeting. Should not end the `AbilityTask` upon data production.                                       |

| `EGameplayTargetingConfirmation::Type` | 대상이 확정되면 |
| -------------------------------------- | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- |
| '인스턴트' | 타겟팅은 '발사' 시점을 결정하는 특별한 논리나 사용자 입력 없이 즉시 발생합니다. |
| `사용자 확인` | 타겟팅을 취소하는 '확인' 입력에 기능이 바인딩되어 있을 때 사용자가 타겟팅을 확인하면 타겟팅이 발생합니다. |
| `커스텀` | GameplayTargeting 능력은 `UGameplayAbility::ConfirmTaskByInstanceName()`을 호출하여 타겟팅 데이터가 준비되는 시점을 결정하는 역할을 합니다. 또한 `TargetActor`는 `UGameplayAbility::CancelTaskByInstanceName()`에 응답하여 타겟팅을 취소합니다. |
| `커스텀멀티` | GameplayTargeting 능력은 `UGameplayAbility::ConfirmTaskByInstanceName()`을 호출하여 타겟팅 데이터가 준비되는 시점을 결정하는 역할을 합니다. 또한 `TargetActor`는 `UGameplayAbility::CancelTaskByInstanceName()`에 응답하여 타겟팅을 취소합니다. 데이터 생성 시 'AbilityTask'를 종료해서는 안 됩니다. |


Not every EGameplayTargetingConfirmation::Type is supported by every `TargetActor`. For example, `AGameplayAbilityTargetActor_GroundTrace` does not support `Instant` confirmation.

모든 EGameplayTargetingConfirmation::Type이 모든 `TargetActor`에서 지원되는 것은 아닙니다. 예를 들어 'AGameplayAbilityTargetActor_GroundTrace'는 'Instant' 확인을 지원하지 않습니다.

The `WaitTargetData` `AbilityTask` takes in a `AGameplayAbilityTargetActor` class as a parameter and will spawn an instance on each activation of the `AbilityTask` and will destroy the `TargetActor` when the `AbilityTask` ends. The `WaitTargetDataUsingActor` `AbilityTask` takes in an already spawned `TargetActor`, but still destroys it when the `AbilityTask` ends. Both of these `AbilityTasks` are inefficient in that they either spawn or require a newly spawned `TargetActor` for each use. They're great for prototyping, but in production you might explore optimizing it if you have cases where you are constantly producing `TargetData` like in the case of an automatic rifle. GASShooter has a custom subclass of [`AGameplayAbilityTargetActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h) and a new [`WaitTargetDataWithReusableActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) `AbilityTask` written from scratch that allows you to reuse a `TargetActor` without destroying it.

'WaitTargetData' 'AbilityTask'는 'AGameplayAbilityTargetActor' 클래스를 매개변수로 받아 'AbilityTask'가 활성화될 때마다 인스턴스를 생성하고 'AbilityTask'가 종료되면 'TargetActor'를 소멸시킵니다. `WaitTargetDataUsingActor` `AbilityTask`는 이미 생성된 `TargetActor`를 가져오지만 `AbilityTask`가 종료되면 여전히 파괴합니다. 이 두 'AbilityTasks'는 사용할 때마다 새로 스폰된 'TargetActor'를 생성하거나 필요로 한다는 점에서 비효율적입니다. 프로토타이핑에 적합하지만 자동 소총의 경우와 같이 'TargetData'를 지속적으로 생성하는 경우가 있는 경우 프로덕션 환경에서 최적화를 탐색할 수 있습니다.

`TargetActors` are not replicated by default; however, they can be made to replicate if that makes sense in your game to show other players where the local player is targeting. They do include default functionality to communicate with the server via RPCs on the `WaitTargetData` `AbilityTask`. If the `TargetActor`'s `ShouldProduceTargetDataOnServer` property is set to `false`, then the client will RPC its `TargetData` to the server on confirmation via `CallServerSetReplicatedTargetData()` in `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`. If `ShouldProduceTargetDataOnServer` is `true`, the client will send a generic confirm event, `EAbilityGenericReplicatedEvent::GenericConfirm`, RPC to the server in `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` and the server will do the trace or overlap check upon receiving the RPC to produce data on the server. If the client cancels the targeting, it will send a generic cancel event, `EAbilityGenericReplicatedEvent::GenericCancel`, RPC to the server in `UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback`. As you can see, there are a lot of delegates on both the `TargetActor` and the `WaitTargetData` `AbilityTask`. The `TargetActor` responds to inputs to produce and broadcast `TargetData` ready, confirm, or cancel delegates. `WaitTargetData` listens to the `TargetActor`'s `TargetData` ready, confirm, and cancel delegates and relays that information back to the `GameplayAbility` and to the server. If you send `TargetData` to the server, you may want to do validation on the server to make sure the `TargetData` looks reasonable to prevent cheating. Producing the `TargetData` directly on the server avoids this issue entirely, but will potentially lead to mispredictions for the owning client.

'TargetActors'는 기본적으로 복제되지 않습니다. 그러나 로컬 플레이어가 타겟팅하는 위치를 다른 플레이어에게 표시하기 위해 게임에서 의미가 있는 경우 복제하도록 만들 수 있습니다. 여기에는 `WaitTargetData` `AbilityTask`의 RPC를 통해 서버와 통신하는 기본 기능이 포함되어 있습니다. `TargetActor`의 `ShouldProduceTargetDataOnServer` 속성이 `false`로 설정되면 클라이언트는 `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`의 `CallServerSetReplicatedTargetData()`를 통해 확인 시 서버에 `TargetData`를 RPC합니다. `ShouldProduceTargetDataOnServer`가 `true`이면 클라이언트는 `UAbilityTask_WaitTargetData:: OnTargetDataReadyCallback()` 및 서버는 서버에서 데이터를 생성하기 위해 RPC를 수신할 때 추적 또는 중복 검사를 수행합니다. 클라이언트가 타겟팅을 취소하면 일반 취소 이벤트인 `EAbilityGenericReplicatedEvent::GenericCancel`, RPC를 `UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback`에서 서버로 보냅니다. 보시다시피 'TargetActor'와 'WaitTargetData', 'AbilityTask' 모두에 많은 델리게이트가 있습니다. `TargetActor`는 입력에 응답하여 `TargetData` 준비, 확인 또는 취소 대리자를 생성 및 브로드캐스트합니다. `WaitTargetData`는 `TargetActor`의 `TargetData` 준비, 확인 및 취소를 수신 대기하고 대리자를 확인하고 해당 정보를 다시 `GameplayAbility` 및 서버에 전달합니다. 'TargetData'를 서버에 보내면, 부정 행위를 방지하기 위해 `TargetData`가 합당한지 확인하기 위해 서버에서 유효성 검사를 수행할 수 있습니다. 서버에서 직접 'TargetData'를 생성하면 이 문제를 완전히 피할 수 있지만 잠재적으로 소유 클라이언트에 대한 잘못된 예측으로 이어질 수 있습니다.

Depending on the particular subclass of `AGameplayAbilityTargetActor` that you use, different `ExposeOnSpawn` parameters will be exposed on the `WaitTargetData` `AbilityTask` node. Some common parameters include:

사용하는 `AGameplayAbilityTargetActor`의 특정 서브클래스에 따라 다른 `ExposeOnSpawn` 매개변수가 `WaitTargetData` `AbilityTask` 노드에 노출됩니다. 몇 가지 일반적인 매개변수는 다음과 같습니다.

| Common `TargetActor` Parameters | Definition                                                                                                                                                                                                                                                                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Debug                           | If `true`, it will draw debug tracing/overlapping information whenever the `TargetActor` performs a trace in non-shipping builds. Remember, non-`Instant` `TargetActors` will perform a trace on `Tick()` so these debug draw calls will also happen on `Tick()`.                                                        |
| Filter                          | [Optional] A special struct for filtering out (removing) `Actors` from the targets when the trace/overlap happens. Typical use cases are to filter out the player's `Pawn`, require targets be of a specific class. See [Target Data Filters](#concepts-target-data-filters) for more advanced use cases. |
| Reticle Class                   | [Optional] Subclass of `AGameplayAbilityWorldReticle` that the `TargetActor` will spawn.                                                                                                                                                                                                                                 |
| Reticle Parameters              | [Optional] Configure your Reticles. See [Reticles](#concepts-targeting-reticles).                                                                                                                                                                                                                                        |
| Start Location                  | A special struct for where tracing should start from. Typically this will be the player's viewpoint, a weapon muzzle, or the `Pawn`'s location.                                                                                                                                                                          |

| 공통 `TargetActor` 매개변수 | 정의 |
| ------------------------------- | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- ------------ |
| 디버그 | 'true'인 경우 'TargetActor'가 비배송 빌드에서 추적을 수행할 때마다 디버그 추적/중첩 정보를 그립니다. 비`Instant` `TargetActors`는 `Tick()`에서 추적을 수행하므로 이러한 디버그 그리기 호출은 `Tick()`에서도 발생합니다. |
| 필터 | 선택 사항 추적/중복이 발생할 때 대상에서 '액터'를 필터링(제거)하기 위한 특수 구조체입니다. 일반적인 사용 사례는 플레이어의 '폰'을 필터링하는 것입니다. 대상은 특정 클래스여야 합니다. 고급 사용 사례는 대상 데이터 필터를 참조하세요. |
| 레티클 클래스 | `TargetActor`가 스폰할 `AGameplayAbilityWorldReticle`의 선택적 서브클래스입니다. |
| 레티클 매개변수 | 선택 사항 레티클을 구성합니다. 레티클을 참조하십시오. |
| 시작 위치 | 추적을 시작해야 하는 위치에 대한 특수 구조체입니다. 일반적으로 이것은 플레이어의 시점, 무기 총구 또는 '폰'의 위치입니다. |


With the default `TargetActor` classes, `Actors` are only valid targets when they are directly in the trace/overlap. If they leave the trace/overlap (they move or you look away), they are no longer valid. If you want the `TargetActor` to remember the last valid target(s), you will need to add this functionality to a custom `TargetActor` class. I refer to these as persistent targets as they will persist until the `TargetActor` receives confirmation or cancellation, the `TargetActor` finds a new valid target in its trace/overlap, or the target is no longer valid (destroyed). GASShooter uses persistent targets for its rocket launcher's secondary ability's homing rockets targeting.

기본 `TargetActor` 클래스를 사용하면 `Actors`는 추적/중첩에 직접 있을 때만 유효한 대상입니다. 추적/중첩을 떠나면(이동하거나 시선을 돌림) 더 이상 유효하지 않습니다. `TargetActor`가 마지막 유효한 대상을 기억하게 하려면 이 기능을 사용자 정의 `TargetActor` 클래스에 추가해야 합니다. 'TargetActor'가 확인 또는 취소를 수신하거나, 'TargetActor'가 추적/중첩에서 유효한 새 대상을 찾거나, 대상이 더 이상 유효하지 않을 때까지(파괴될 때까지) 지속 대상을 영구 대상이라고 합니다. GASShooter는 로켓 발사기의 보조 능력의 유도 로켓 표적화를 위해 영구 표적을 사용합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-target-data-filters"></a>
#### 4.11.3 Target Data Filters
Using both the `Make GameplayTargetDataFilter` and `Make Filter Handle` nodes, you can filter out the player's `Pawn` or select only a specific class. If you need more advanced filtering, you can subclass `FGameplayTargetDataFilter` and override the `FilterPassesForActor` function. 

'Make GameplayTargetDataFilter' 및 'Make Filter Handle' 노드를 모두 사용하여 플레이어의 'Pawn'을 필터링하거나 특정 클래스만 선택할 수 있습니다. 더 고급 필터링이 필요한 경우 'FGameplayTargetDataFilter'를 서브클래싱하고 'FilterPassesForActor' 함수를 재정의할 수 있습니다.

```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** Returns true if the actor passes the filter and will be targeted */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

However, this will not work directly into the `Wait Target Data` node as it requires a `FGameplayTargetDataFilterHandle`. A new custom `Make Filter Handle` must be made to accept the subclass:

그러나 이것은 `FGameplayTargetDataFilterHandle`이 필요하기 때문에 `Wait Target Data` 노드에 직접 작동하지 않습니다. 하위 클래스를 허용하려면 새로운 사용자 정의 'Make Filter Handle'을 만들어야 합니다.

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

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>
#### 4.11.4 Gameplay Ability World Reticles
[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html) (`Reticles`) visualize **who** you are targeting when targeting with non-`Instant` confirmed [`TargetActors`](#concepts-targeting-actors). `TargetActors` are responsible for the spawn and destroy lifetimes for all `Reticles`. `Reticles` are `AActors` so they can use any kind of visual component for representation. A common implementation as seen in [GASShooter](https://github.com/tranek/GASShooter) is to use a `WidgetComponent` to display a UMG Widget in screen space (always facing the player's camera). `Reticles` do not know which `AActor` that they're on, but you could subclass in that functionality on a custom `TargetActor`. `TargetActors` will typically update the `Reticle`'s location to the target's location on every `Tick()`.

`AGameplayAbilityWorldReticles`(`Reticles`)는 `Instant`가 아닌 확인된 `TargetActors`로 타겟팅할 때 타겟팅하는 **누구**를 시각화합니다. 'TargetActors'는 모든 'Reticles'에 대한 스폰 및 소멸 수명을 담당합니다. '레티클'은 'AActor'이므로 표현을 위해 모든 종류의 시각적 구성 요소를 사용할 수 있습니다. GASShooter에서 볼 수 있는 일반적인 구현은 'WidgetComponent'를 사용하여 화면 공간에 UMG 위젯을 표시하는 것입니다(항상 플레이어의 카메라를 향함). '레티클'은 어떤 'AActor'가 켜져 있는지 알지 못하지만 사용자 정의 'TargetActor'에서 해당 기능을 서브클래싱할 수 있습니다. `TargetActors`는 일반적으로 `Tick()`마다 `Reticle`의 위치를 대상의 위치로 업데이트합니다.

GASShooter uses `Reticles` to show locked-on targets for the rocket launcher's secondary ability's homing rockets. The red indicator on the enemy is the `Reticle`. The similar white image is the rocket launcher's crosshair.

GASShooter는 'Reticles'를 사용하여 로켓 발사기의 보조 능력의 유도 로켓에 대해 고정된 목표를 표시합니다. 적의 빨간 표시는 '레티클'입니다. 비슷한 흰색 이미지는 로켓 발사기의 십자선입니다.

![Reticles in GASShooter](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

GASShooter는 'Reticles'를 사용하여 로켓 발사기의 보조 능력의 유도 로켓에 대해 고정된 목표를 표시합니다. 적의 빨간 표시는 '레티클'입니다. 비슷한 흰색 이미지는 로켓 발사기의 십자선입니다.

`Reticles` come with a handful of `BlueprintImplementableEvents` for designers (they're intended to be developed in Blueprints):

'레티클'은 디자이너를 위한 소수의 'BlueprintImplementableEvents'와 함께 제공됩니다(블루프린트에서 개발하기 위한 것임):

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

`Reticles` can optionally use [`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html) provided by the `TargetActor` for configuration. The default struct only provides one variable `FVector AOEScale`. While you can technically subclass this struct, the `TargetActor` will only accept the base struct. It seems a little short-sighted to not allow this to be subclassed with default `TargetActors`. However, if you make your own custom `TargetActor`, you can provide your own custom reticle parameters struct and manually pass it to your subclass of `AGameplayAbilityWorldReticles` when you spawn them.

'레티클'은 구성을 위해 'TargetActor'에서 제공하는 'FWorldReticleParameters'를 선택적으로 사용할 수 있습니다. 기본 구조체는 'FVector AOEScale' 변수 하나만 제공합니다. 기술적으로 이 구조체를 서브클래싱할 수 있지만 `TargetActor`는 기본 구조체만 허용합니다. 이것을 기본 `TargetActors`로 서브클래싱하는 것을 허용하지 않는 것은 약간 근시안적인 것 같습니다. 그러나 사용자 지정 `TargetActor`를 만드는 경우 사용자 지정 레티클 매개변수 구조체를 제공하고 스폰할 때 `AGameplayAbilityWorldReticles`의 하위 클래스에 수동으로 전달할 수 있습니다.

`Reticles` are not replicated by default, but can be made replicated if it makes sense for your game to show other players who the local player is targeting.

'레티클'은 기본적으로 복제되지 않지만 게임에서 로컬 플레이어가 대상으로 하는 다른 플레이어를 표시하는 것이 타당하다면 복제할 수 있습니다.

`Reticles` will only display on the current valid target with the default `TargetActors`. For example, if you're using a `AGameplayAbilityTargetActor_SingleLineTrace` to trace for a target, the `Reticle` will only appear when the enemy is directly in the trace path. If you look away, the enemy is no longer a valid target and the `Reticle` will disappear. If you want the `Reticle` to stay on the last valid target, you will want to customize your `TargetActor` to remember the last valid target and keep the `Reticle` on them. I refer to these as persistent targets as they will persist until the `TargetActor` receives confirmation or cancellation, the `TargetActor` finds a new valid target in its trace/overlap, or the target is no longer valid (destroyed).  GASShooter uses persistent targets for its rocket launcher's secondary ability's homing rockets targeting.

'레티클'은 기본 'TargetActors'를 사용하여 현재 유효한 대상에만 표시됩니다. 예를 들어 `AGameplayAbilityTargetActor_SingleLineTrace`를 사용하여 대상을 추적하는 경우 `Reticle`은 적이 추적 경로에 직접 있을 때만 나타납니다. 시선을 돌리면 적이 더 이상 유효한 대상이 아니며 '레티클'이 사라집니다. '레티클'이 마지막 유효한 타겟에 유지되도록 하려면 'TargetActor'를 사용자 지정하여 마지막 유효한 타겟을 기억하고 '레티클'을 유지해야 합니다. 'TargetActor'가 확인 또는 취소를 수신하거나, 'TargetActor'가 추적/중첩에서 유효한 새 대상을 찾거나, 대상이 더 이상 유효하지 않을 때까지(파괴될 때까지) 지속 대상을 영구 대상이라고 합니다. GASShooter는 로켓 발사기의 보조 능력을 위해 영구 목표물을 사용합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-containers"></a>
#### 4.11.5 Gameplay Effect Containers Targeting
[`GameplayEffectContainers`](#concepts-ge-containers) come with an optional, efficient means of producing [`TargetData`](#concepts-targeting-data). This targeting takes places instantly when the `EffectContainer` is applied on the client and the server. It's more efficient than [`TargetActors`](#concepts-targeting-actors) because it runs on the CDO of the targeting object (no spawning and destroying of `Actors`), but it lacks player input, happens instantly without needing confirmation, cannot be canceled, and cannot send data from the client to the server (produces data on both). It works well for instant traces and collision overlaps. Epic's [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) includes two example types of targeting with its containers - target the ability owner and pull `TargetData` from an event. It also implements one in Blueprint to do instant sphere traces at some offset (set by child Blueprint classes) from the player. You can subclass `URPGTargetType` in C++ or Blueprint to make your own targeting types.

`GameplayEffectContainers` come with an optional, efficient means of producing `TargetData`. This targeting takes places instantly when the `EffectContainer` is applied on the client and the server. It's more efficient than `TargetActors` because it runs on the CDO of the targeting object (no spawning and destroying of `Actors`), but it lacks player input, happens instantly without needing confirmation, cannot be canceled, and cannot send data from the client to the server (produces data on both). It works well for instant traces and collision overlaps. Epic's Action RPG Sample Project includes two example types of targeting with its containers - target the ability owner and pull `TargetData` from an event. It also implements one in Blueprint to do instant sphere traces at some offset (set by child Blueprint classes) from the player. You can subclass `URPGTargetType` in C++ or Blueprint to make your own targeting types.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae"></a>
## 5. Commonly Implemented Abilities and Effects

<a name="cae-stun"></a>
### 5.1 Stun
Typically with stuns, we want to cancel all of a `Character's` active `GameplayAbilities`, prevent new `GameplayAbility` activations, and prevent movement throughout the duration of the stun. The Sample Project's Meteor `GameplayAbility` applies a stun on hit targets.

일반적으로 기절의 경우 '캐릭터'의 활성 'GameplayAbilities'를 모두 취소하고, 새로운 'GameplayAbility' 활성화를 방지하고, 기절 기간 동안 움직임을 방지하려고 합니다. 샘플 프로젝트의 Meteor 'GameplayAbility'는 적중 대상에 기절을 적용합니다.

To cancel the target's active `GameplayAbilities`, we call `AbilitySystemComponent->CancelAbilities()` when the stun [`GameplayTag` is added](#concepts-gt-change).

대상의 활성 'GameplayAbilities'를 취소하려면 스턴 'GameplayTag'가 추가될 때 'AbilitySystemComponent->CancelAbilities()'를 호출합니다.

To prevent new `GameplayAbilities` from activating while stunned, the `GameplayAbilities` are given the stun `GameplayTag` in their [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags).

기절 상태에서 새로운 'GameplayAbilities'가 활성화되는 것을 방지하기 위해 'GameplayAbilities'에는 'Activation Blocked Tags' 'GameplayTagContainer'에서 기절된 'GameplayTag'가 지정됩니다.

To prevent movement while stunned, we override the `CharacterMovementComponent's` `GetMaxSpeed()` function to return 0 when the owner has the stun `GameplayTag`.

기절 상태에서 움직임을 방지하기 위해 소유자가 기절한 'GameplayTag'가 있을 때 0을 반환하도록 'CharacterMovementComponent'의 'GetMaxSpeed()' 함수를 재정의합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-sprint"></a>
### 5.2 Sprint
The Sample Project provides an example of how to sprint - run faster while `Left Shift` is held down.

샘플 프로젝트는 'Left Shift' 키를 누르고 있는 동안 더 빠르게 달리는 방법의 예를 제공합니다.

The faster movement is handled predictively by the `CharacterMovementComponent` by sending a flag over the network to the server. See `GDCharacterMovementComponent.h/cpp` for details.

더 빠른 움직임은 'CharacterMovementComponent'가 네트워크를 통해 서버로 플래그를 전송하여 예측적으로 처리됩니다. 자세한 내용은 `GDCharacterMovementComponent.h/cpp`를 참조하세요.

The `GA` handles responding to the `Left Shift` input, tells the `CMC` to begin and stop sprinting, and to predictively charge stamina while `Left Shift` is pressed. See `GA_Sprint_BP` for details.

'GA'는 'Left Shift' 입력에 대한 응답을 처리하고, 'CMC'에 전력 질주를 시작 및 중지하고, 'Left Shift'를 누르고 있는 동안 체력을 예측적으로 충전하도록 지시합니다. 자세한 내용은 `GA_Sprint_BP`를 참조하세요.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ads"></a>
### 5.3 Aim Down Sights
The Sample Project handles this the exact same way as sprinting but decreasing the movement speed instead of increasing it.

샘플 프로젝트는 이것을 전력 질주와 똑같은 방식으로 처리하지만 이동 속도를 높이는 대신 감소시킵니다.

See `GDCharacterMovementComponent.h/cpp` for details on predictively decreasing the movement speed.

이동 속도를 예측적으로 줄이는 방법에 대한 자세한 내용은 `GDCharacterMovementComponent.h/cpp`를 참조하세요.

See `GA_AimDownSight_BP` for details on handling the input. There is no stamina cost for aiming down sights.

입력 처리에 대한 자세한 내용은 `GA_AimDownSight_BP`를 참조하세요. 조준경 조준에는 스태미나 비용이 없습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ls"></a>
### 5.4 Lifesteal
I handle lifesteal inside of the damage [`ExecutionCalculation`](#concepts-ge-ec). The `GameplayEffect` will have a `GameplayTag` on it like `Effect.CanLifesteal`. The `ExecutionCalculation` checks if the `GameplayEffectSpec` has that `Effect.CanLifesteal` `GameplayTag`. If the `GameplayTag` exists, the `ExecutionCalculation` [creates a dynamic `Instant` `GameplayEffect`](#concepts-ge-dynamic) with the amount of health to give as the modifier and applies it back to the `Source's` `ASC`.

저는 데미지 'ExecutionCalculation' 안에서 흡혈을 처리합니다. 'GameplayEffect'에는 'Effect.CanLifesteal'과 같은 'GameplayTag'가 있습니다. 'ExecutionCalculation'은 'GameplayEffectSpec'에 'Effect.CanLifesteal' 'GameplayTag'가 있는지 확인합니다. 'GameplayTag'가 존재하는 경우, 'ExecutionCalculation'은 제공할 체력의 양을 수정자로 하여 동적 'Instant' 'GameplayEffect'를 생성하고 이를 'Source'의 'ASC'에 다시 적용합니다.

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

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-random"></a>
### 5.5 Generating a Random Number on Client and Server
Sometimes you need to generate a "random" number inside of a `GameplayAbility` for things like bullet recoil or spread. The client and the server will both want to generate the same random numbers. To do this, we must set the `random seed` to be the same at the time of `GameplayAbility` activation. You will want to set the `random seed` each time you activate the `GameplayAbility` in case the client mispredicts activation and its random number sequence becomes out of synch with the server's.

때때로 총알 반동이나 확산과 같은 것을 위해 `GameplayAbility` 내부에 "임의" 숫자를 생성해야 합니다. 클라이언트와 서버는 모두 동일한 난수를 생성하기를 원할 것입니다. 이렇게 하려면 `GameplayAbility` 활성화 시 `random seed`를 동일하게 설정해야 합니다. 클라이언트가 활성화를 잘못 예측하고 난수 시퀀스가 서버의 것과 동기화되지 않는 경우를 대비하여 `GameplayAbility`를 활성화할 때마다 `random seed`를 설정하고 싶을 것입니다.

| Seed Setting Method                                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Use the activation prediction key                                            | The `GameplayAbility` activation prediction key is an int16 guaranteed to be synchronized and available in both the client and server in the `Activation()`. You can set this as the `random seed` on both the client and the server. The downside to this method is that the prediction key always starts at zero each time the game starts and consistently increments the value to use between generating keys. This means each match will have the exact same random number sequence. This may or may not be random enough for your needs. |
| Send a seed through an event payload when you activate the `GameplayAbility` | Activate your `GameplayAbility` by event and send the randomly generated seed from the client to the server via the replicated event payload. This allows for more randomness but the client could easily hack their game to only send the same seed value every time. Also activating `GameplayAbilities` by event will prevent them from activating from the input bind.                                                                                                                                                                     |
| 종자 설정 방법 | 설명 |
| -------------------------------------------------- -------------------------- | -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------------------------------- -------------------------- |
| 활성화 예측 키 사용 | `GameplayAbility` 활성화 예측 키는 `Activation()`에서 클라이언트와 서버 모두에서 동기화되고 사용 가능하도록 보장된 int16입니다. 클라이언트와 서버 모두에서 '랜덤 시드'로 설정할 수 있습니다. 이 방법의 단점은 게임이 시작될 때마다 예측 키가 항상 0에서 시작하고 키 생성 사이에 사용할 값을 지속적으로 증가시킨다는 것입니다. 이것은 각 일치가 정확히 동일한 난수 시퀀스를 가짐을 의미합니다. 이것은 필요에 따라 충분히 무작위적일 수도 있고 아닐 수도 있습니다. |
| `GameplayAbility`를 활성화할 때 이벤트 페이로드를 통해 시드 보내기 | 이벤트별로 'GameplayAbility'를 활성화하고 복제된 이벤트 페이로드를 통해 클라이언트에서 서버로 무작위로 생성된 시드를 보냅니다. 이것은 더 많은 임의성을 허용하지만 클라이언트는 게임을 쉽게 해킹하여 매번 동일한 시드 값만 보낼 수 있습니다. 또한 이벤트로 'GameplayAbilities'를 활성화하면 입력 바인딩에서 활성화되지 않습니다. |

If your random deviation is small, most players won't notice that the sequence is the same every game and using the activation prediction key as the `random seed` should work for you. If you're doing something more complex that needs to be hacker proof, perhaps using a `Server Initiated` `GameplayAbility` would work better where the server can create the prediction key or generate the `random seed` to send via an event payload.

무작위 편차가 작은 경우 대부분의 플레이어는 시퀀스가 모든 게임에서 동일하다는 것을 알지 못하며 '임의 시드'로 활성화 예측 키를 사용하면 효과가 있습니다. 해커 증거가 필요한 더 복잡한 작업을 수행하는 경우 'Server Initiated' 'GameplayAbility'를 사용하는 것이 서버가 예측 키를 생성하거나 이벤트 페이로드를 통해 보낼 '랜덤 시드'를 생성할 수 있는 곳에서 더 잘 작동합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-crit"></a>
### 5.6 Critical Hits
I handle critical hits inside of the damage [`ExecutionCalculation`](#concepts-ge-ec). The `GameplayEffect` will have a `GameplayTag` on it like `Effect.CanCrit`. The `ExecutionCalculation` checks if the `GameplayEffectSpec` has that `Effect.CanCrit` `GameplayTag`. If the `GameplayTag` exists, the `ExecutionCalculation` generates a random number corresponding to the critical hit chance (`Attribute` captured from the `Source`) and adds the critical hit damage (also an `Attribute` captured from the `Source`) if it succeeded. Since I don't predict damage, I don't have to worry about synchronizing the random number generators on the client and server since the `ExecutionCalculation` will only run on the server. If you tried to do this predictively using an `MMC` to do your damage calculation, you would have to get a reference to the `random seed` from the `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance`.

성공하면 데미지 'ExecutionCalculation' 내에서 치명타를 처리합니다. 나는 손상을 예측하지 않기 때문에 'ExecutionCalculation'이 서버에서만 실행되기 때문에 클라이언트와 서버에서 난수 생성기를 동기화하는 것에 대해 걱정할 필요가 없습니다. 피해 계산을 위해 `MMC`를 사용하여 예측적으로 이 작업을 수행하려고 하면 `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance`에서 `random seed`에 대한 참조를 가져와야 합니다.

See how [GASShooter](https://github.com/tranek/GASShooter) does headshots. It's the same concept except that it does not rely on a random number for chance and instead checks the `FHitResult` bone name.

GASShooter가 헤드샷을 수행하는 방법을 확인하십시오. 무작위 숫자에 의존하지 않고 대신 'FHitResult' 본 이름을 확인한다는 점을 제외하면 동일한 개념입니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 5.7 Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target
Slow effects in Paragon did not stack. Each slow instance applied and kept track of their lifetimes as normal, but only the greatest magnitude slow effect actually affected the `Character`. GAS provides for this scenario out of the box with `AggregatorEvaluateMetaData`. See [`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated) for details and implementation.

Paragon의 슬로우 효과가 중첩되지 않았습니다. 각각의 느린 인스턴스가 적용되고 수명을 정상적으로 추적했지만 실제로 가장 큰 규모의 느린 효과만 '캐릭터'에 영향을 미쳤습니다. GAS는 'AggregatorEvaluateMetaData'를 사용하여 이 시나리오를 즉시 제공합니다. 자세한 내용과 구현은 `AggregatorEvaluateMetaData()`를 참조하세요.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-paused"></a>
### 5.8 Generate Target Data While Game is Paused
If you need to pause the game while waiting to generate [`TargetData`](#concepts-targeting-data) from a `WaitTargetData` `AbilityTask` from your player, I suggest instead of pausing to use `slomo 0`.

플레이어의 `WaitTargetData` `AbilityTask`에서 `TargetData`를 생성하기 위해 기다리는 동안 게임을 일시 중지해야 하는 경우 일시 중지하는 대신 `slomo 0`을 사용하는 것이 좋습니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>
### 5.9 One Button Interaction System
[GASShooter](https://github.com/tranek/GASShooter) implements a one button interaction system where the player can press or hold 'E' to interact with interactable objects like reviving a player, opening a weapon chest, and opening or closing a sliding door.

GASShooter는 플레이어가 'E'를 누르거나 길게 눌러 플레이어 소생, 무기 상자 열기, 슬라이딩 도어 열기 또는 닫기와 같은 상호 작용 가능한 개체와 상호 작용할 수 있는 원 버튼 상호 작용 시스템을 구현합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging"></a>
## 6. Debugging GAS
Often when debugging GAS related issues, you want to know things like:
> * "What are the values of my attributes?"
> * "What gameplay tags do I have?"
> * "What gameplay effects do I currently have?"
> * "What abilities do I have granted, which ones are running, and which ones are blocked from activating?".

종종 GAS 관련 문제를 디버깅할 때 다음과 같은 사항을 알고 싶어합니다.
> * "내 속성의 값은 무엇입니까?"
> * "나는 어떤 게임플레이 태그를 가지고 있나요?"
> * "현재 내가 가지고 있는 게임 플레이 효과는 무엇입니까?"
> * "내가 부여한 능력은 무엇이며 어떤 능력이 실행되고 어떤 능력이 활성화되지 않습니까?".


GAS comes with two techniques for answering these questions at runtime - [`showdebug abilitysystem`](#debugging-sd) and hooks in the [`GameplayDebugger`](#debugging-gd).

GAS는 런타임에 이러한 질문에 답하기 위한 두 가지 기술인 `showdebug 능력 시스템`을 제공합니다.

**Tip:** UE5 likes to optimize C++ code which makes it hard to debug some functions. You will encounter this rarely when tracing deep into your code. If setting your Visual Studio solution configuration to `DebugGame Editor` still prevents tracing code or inspecting variables, you can disable all optimizations by wrapping the optimized function with the `PRAGMA_DISABLE_OPTIMIZATION_ACTUAL` and `PRAGMA_ENABLE_OPTIMIZATION_ACTUAL` macros. This cannot be used on the plugin code unless you rebuild the plugin from source. This may or may not work on inline functions depending on what they do and where they are. Be sure to remove the macros when you're done debugging!

**팁:** UE5는 C++ 코드 최적화를 좋아하므로 일부 기능을 디버그하기 어렵습니다. 코드를 깊숙이 추적할 때 이 문제가 거의 발생하지 않습니다. Visual Studio 솔루션 구성을 `DebugGame Editor`로 설정해도 여전히 코드 추적 또는 변수 검사가 방지되는 경우 최적화된 함수를 `PRAGMA_DISABLE_OPTIMIZATION_ACTUAL` 및 `PRAGMA_ENABLE_OPTIMIZATION_ACTUAL` 매크로로 래핑하여 모든 최적화를 비활성화할 수 있습니다. 소스에서 플러그인을 다시 빌드하지 않는 한 플러그인 코드에서 사용할 수 없습니다. 이것은 인라인 함수가 수행하는 작업과 위치에 따라 작동하거나 작동하지 않을 수 있습니다. 디버깅이 끝나면 매크로를 제거하십시오!

```c++
PRAGMA_DISABLE_OPTIMIZATION_ACTUAL
void MyClass::MyFunction(int32 MyIntParameter)
{
	// My code
}
PRAGMA_ENABLE_OPTIMIZATION_ACTUAL
```

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem
Type `showdebug abilitysystem` in the in-game console. This feature is split into three "pages". All three pages will show the `GameplayTags` that you currently have. Type `AbilitySystem.Debug.NextCategory` into the console to cycle between the pages.

게임 내 콘솔에 `showdebugabilitysystem`을 입력합니다. 이 기능은 3개의 "페이지"로 나뉩니다. 세 페이지 모두 현재 가지고 있는 'GameplayTags'를 표시합니다. 콘솔에 `AbilitySystem.Debug.NextCategory`를 입력하여 페이지 사이를 순환합니다.

The first page shows the `CurrentValue` of all of your `Attributes`:
![First Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

첫 번째 페이지는 모든 '속성'의 'CurrentValue'를 보여줍니다.
!showdebug 능력 시스템의 첫 페이지


The second page shows all of the `Duration` and `Infinite` `GameplayEffects` on you, their number of stacks, what `GameplayTags` they give, and what `Modifiers` they give.
![Second Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

두 번째 페이지는 모든 `Duration` 및 `Infinite` `GameplayEffects`, 스택 수, 제공하는 `GameplayTags` 및 제공하는 `Modifiers`를 보여줍니다.

The third page shows all of the `GameplayAbilities` that have been granted to you, whether they are currently running, whether they are blocked from activating, and the status of currently running `AbilityTasks`.
![Third Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

세 번째 페이지에는 귀하에게 부여된 모든 'GameplayAbilities', 현재 실행 중인지, 활성화가 차단되었는지 여부, 현재 실행 중인 'AbilityTasks' 상태가 표시됩니다.

While you can cycle between targets with `PageUp` and `PageDown`, the pages will only show data for the `ASC` on your locally controlled `Character`. However, using `AbilitySystem.Debug.NextTarget` and `AbilitySystem.Debug.PrevTarget` will show data for other `ASCs`, but it will not update the top half of the debug information nor will it update the green targeting rectangular prism so there is no way to know which `ASC` is currently being targeted. This bug has been reported https://issues.unrealengine.com/issue/UE-90437.

'PageUp' 및 'PageDown'을 사용하여 대상 간에 순환할 수 있지만 페이지는 로컬로 제어되는 'Character'의 'ASC'에 대한 데이터만 표시합니다. 그러나 `AbilitySystem.Debug.NextTarget` 및 `AbilitySystem.Debug.PrevTarget`을 사용하면 다른 `ASC`에 대한 데이터가 표시되지만 디버그 정보의 위쪽 절반은 업데이트되지 않으며 녹색 타겟팅 직사각형 프리즘도 업데이트되지 않으므로 현재 어떤 'ASC'가 대상이 되는지 알 수 있는 방법이 없습니다. 이 버그는 https://issues.unrealengine.com/issue/UE-90437에 보고되었습니다.

**Note:** For `showdebug abilitysystem` to work an actual HUD class must be selected in the GameMode. Otherwise the command is not found and "Unknown Command" is returned.

**참고:** 실제 HUD 클래스가 작동하려면 `showdebug 능력 시스템`이 게임 모드에서 선택되어야 합니다. 그렇지 않으면 명령을 찾을 수 없고 "알 수 없는 명령"이 반환됩니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 Gameplay Debugger
GAS adds functionality to the Gameplay Debugger. Access the Gameplay Debugger with the Apostrophe (') key. Enable the Abilities category by pressing 3 on your numpad. The category may be different depending on what plugins you have. If your keyboard doesn't have a numpad like a laptop, then you can change the keybindings in the project settings.

GAS는 게임 플레이 디버거에 기능을 추가합니다. 아포스트로피(') 키를 사용하여 게임 플레이 디버거에 액세스합니다. 숫자 키패드에서 3을 눌러 능력 범주를 활성화합니다. 카테고리는 보유하고 있는 플러그인에 따라 다를 수 있습니다. 키보드에 노트북과 같은 숫자 키패드가 없는 경우 프로젝트 설정에서 키 바인딩을 변경할 수 있습니다.

Use the Gameplay Debugger when you want to see the `GameplayTags`, `GameplayEffects`, and `GameplayAbilities` on **other** `Characters`. Unfortunately it does not show the `CurrentValue` of the target's `Attributes`. It will target whatever `Character` is in the center of your screen. You can change targets by selecting them in the World Outliner in the Editor or by looking at a different `Character` and press Apostrophe (') again. The currently inspected `Character` has the largest red circle above it.

**other** `Characters`에서 `GameplayTags`, `GameplayEffects` 및 `GameplayAbilities`를 보려면 게임플레이 디버거를 사용하세요. 불행히도 대상의 '속성'의 'CurrentValue'는 표시되지 않습니다. 화면 중앙에 있는 'Character'를 대상으로 합니다. Editor의 World Outliner에서 대상을 선택하거나 다른 'Character'를 보고 아포스트로피(')를 다시 눌러 대상을 변경할 수 있습니다. 현재 검사된 'Character'는 그 위에 가장 큰 빨간색 원이 있습니다.

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 GAS Logging
The GAS source code contains a lot of logging statements produced at varying verbosity levels. You will most likely see these as `ABILITY_LOG()` statements. The default verbosity level is `Display`. Anything higher will not be displayed in the console by default.

GAS 소스 코드에는 다양한 상세 수준에서 생성된 많은 로깅 문이 포함되어 있습니다. 이것을 `ABILITY_LOG()` 문으로 볼 가능성이 큽니다. 기본 상세 수준은 '디스플레이'입니다. 더 높은 값은 기본적으로 콘솔에 표시되지 않습니다.

To change the verbosity level of a log category, type into your console:

로그 범주의 상세 수준을 변경하려면 콘솔에 다음을 입력합니다.

```
log [category] [verbosity]
```

For example, to turn on `ABILITY_LOG()` statements, you would type into your console:
예를 들어 `ABILITY_LOG()` 문을 켜려면 콘솔에 다음을 입력합니다.:
```
log LogAbilitySystem VeryVerbose
```

To reset it back to default, type:
다시 기본값으로 재설정하려면 다음을 입력하십시오.:
```
log LogAbilitySystem Display
```

To display all log categories, type:
모든 로그 범주를 표시하려면 다음을 입력하십시오.:
```
log list
```

Notable GAS related logging categories:

| Logging Category          | Default Verbosity Level |
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

주목할만한 GAS 관련 로깅 카테고리:

| 로깅 카테고리 | 기본 상세 수준 |
| -------------------------- | ----------------------- |
| LogAbilitySystem | 디스플레이 |
| LogAbilitySystemComponent | 로그 |
| LogGameplayCue자세히 | 로그 |
| LogGameplayCue번역기 | 디스플레이 |
| LogGameplayEffectDetails | 로그 |
| LogGameplayEffects | 디스플레이 |
| LogGameplay태그 | 로그 |
| LogGameplayTasks | 로그 |
| VLogAbilitySystem | 디스플레이 |


See the [Wiki on Logging](https://unrealcommunity.wiki/logging-lgpidy6i) for more information.

**[⬆ Back to Top](#table-of-contents)**

<a name="optimizations"></a>
## 7. Optimizations

<a name="optimizations-abilitybatching"></a>
### 7.1 Ability Batching
[`GameplayAbilities`](#concepts-ga) that activate, optionally send `TargetData` to the server, and end all in one frame can be [batched to condense two-three RPCs into one RPC](#concepts-ga-batching). These types of abilities are commonly used for hitscan guns.

활성화하고 선택적으로 서버에 `TargetData`를 보내고 한 프레임에서 모두 끝나는 `GameplayAbilities`를 일괄 처리하여 2-3개의 RPC를 하나의 RPC로 압축할 수 있습니다. 이러한 유형의 능력은 일반적으로 히트스캔 총에 사용됩니다.

<a name="optimizations-gameplaycuebatching"></a>
### 7.2 Gameplay Cue Batching
If you're sending many [`GameplayCues`](#concepts-gc) at the same time, consider [batching them into one RPC](#concepts-gc-batching). The goal is to reduce the number of RPCs (`GameplayCues` are unreliable NetMulticasts) and send as little data as possible.

동시에 많은 `GameplayCues`를 보내는 경우 하나의 RPC로 일괄 처리하는 것을 고려하십시오. 목표는 RPC 수를 줄이고(`GameplayCues`는 NetMulticasts 신뢰할 수 없음) 가능한 한 적은 데이터를 보내는 것입니다.

<a name="optimizations-ascreplicationmode"></a>
### 7.3 AbilitySystemComponent Replication Mode
By default, the [`ASC`](#concepts-asc) is in [`Full Replication Mode`](#concepts-asc-rm). This will replicate all [`GameplayEffects`](#concepts-ge) to every client (which is fine for a single player game). In a multiplayer game, set the player owned `ASCs` to `Mixed Replication Mode` and AI controlled characters to `Minimal Replication Mode`. This will replicate `GEs` applied on a player character to only replicate to the owner of that character and `GEs` applied on AI controlled characters will never replicate `GEs` to clients. [`GameplayTags`](#concepts-gt) will still replicate and [`GameplayCues`](#concepts-gc) will still unreliable NetMulticast to all clients, regardless of the `Replication Mode`. This will cut down on network data from `GEs` being replicated when all clients don't need to see them.

<a name="optimizations-attributeproxyreplication"></a>
### 7.4 Attribute Proxy Replication
In large games with many players like Fortnite Battle Royale (FNBR), there will be a lot of [`ASCs`](#concepts-asc) living on always-relevant `PlayerStates` replicating a lot of [`Attributes`](#concepts-a). To optimize this bottleneck, Fortnite disables the `ASC` and its [`AttributeSets`](#concepts-as) from replicating altogether on **simulated player-controlled proxies** in the `PlayerState::ReplicateSubobjects()`. Autonomous proxies and AI controlled `Pawns` still fully replicate according to their [`Replication Mode`](#concepts-asc-rm). Instead of replicating `Attributes` on the `ASC` on the always-relevant `PlayerStates`, FNBR uses a replicated proxy structure on the player's `Pawn`. When `Attributes` change on the server's `ASC`, they are changed on the proxy struct too. The client receives the replicated `Attributes` from the proxy struct and pushes the changes back into its local `ASC`. This allows `Attribute` replication to use the `Pawn`'s relevancy and `NetUpdateFrequency`. This proxy struct also replicates a small white-listed set of `GameplayTags` in a bitmask. This optimization reduces the amount of data over the network and allows us to take advantage of pawn relevancy. AI controlled `Pawns` have their `ASC` on the `Pawn` which already uses its relevancy so this optimization is not needed for them.

FNBR(Fortnite Battle Royale)과 같이 플레이어가 많은 대형 게임에서는 'ASC'가 많이 있을 것입니다. 항상 관련이 있는 `PlayerStates`의 `ASC`에 `Attributes`를 복제하는 대신 FNBR은 플레이어의 `Pawn`에 복제된 프록시 구조를 사용합니다. '속성'이 서버의 'ASC'에서 변경되면 프록시 구조에서도 변경됩니다. 클라이언트는 프록시 구조체에서 복제된 '속성'을 수신하고 변경 사항을 로컬 'ASC'로 다시 푸시합니다. 이를 통해 `Attribute` 복제가 `Pawn`의 관련성과 `NetUpdateFrequency`를 사용할 수 있습니다. 이 프록시 구조체는 또한 비트마스크에 화이트리스트에 있는 작은 'GameplayTags' 세트를 복제합니다. 이 최적화는 네트워크를 통한 데이터의 양을 줄이고 폰 관련성을 활용할 수 있도록 합니다.

> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.

> 그 이후로 수행된 다른 서버 측 최적화(복제 그래프 등)에 여전히 필요한지 확실하지 않으며 가장 유지 관리 가능한 패턴이 아닙니다.

*Dave Ratti from Epic's answer to [community questions #3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

<a name="optimizations-asclazyloading"></a>
### 7.5 ASC Lazy Loading
Fortnite Battle Royale (FNBR) has a lot of damageable `AActors` (trees, buildings, etc) in the world, each with an [`ASC`](#concepts-asc). This can add up in memory cost. FNBR optimizes this by lazily loading `ASCs` only when they're needed (when they first take damage by a player). This reduces overall memory usage since some `AActors` may never be damaged in a match.

Fortnite Battle Royale(FNBR)에는 전 세계에 피해를 줄 수 있는 'AActor'(나무, 건물 등)가 많이 있으며 각각 'ASC'가 있습니다. 일부 'AActor'는 경기에서 절대 손상되지 않을 수 있으므로 전체 메모리 사용량을 줄입니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="qol"></a>
## 8. Quality of Life Suggestions

<a name="qol-gameplayeffectcontainers"></a>
### 8.1 Gameplay Effect Containers
[GameplayEffectContainers](#concepts-ge-containers) combine [`GameplayEffectSpecs`](#concepts-ge-spec), [`TargetData`](#concepts-targeting-data), [simple targeting](#concepts-targeting-containers), and related functionality into easy to use structures. These are great for transfering `GameplayEffectSpecs` to projectiles spawned from an ability that will then apply them on collision at a later time.

GameplayEffectContainers 및 관련 기능을 사용하기 쉬운 구조로. 이는 'GameplayEffectSpecs'를 능력에서 생성된 발사체로 전송하는 데 유용하며, 이 발사체는 나중에 충돌 시 적용됩니다.

<a name="qol-asynctasksascdelegates"></a>
### 8.2 Blueprint AsyncTasks to Bind to ASC Delegates
To increase designer-friendly iteration times, especially when designing UMG Widgets for UI, create Blueprint AsyncTasks (in C++) to bind to the common change delegates on the `ASC` directly from your UMG Blueprint graphs. The only caveat is that they must be manually destroyed (like when the widget is destroyed) otherwise they will live in memory forever. The Sample Project includes three Blueprint AsyncTasks.

특히 UI용 UMG 위젯을 디자인할 때 디자이너에게 친숙한 반복 시간을 늘리려면 Blueprint AsyncTasks(C++에서)를 만들어 UMG 블루프린트 그래프에서 직접 'ASC'의 공통 변경 대리자에 바인딩합니다. 유일한 주의 사항은 위젯이 파괴될 때와 같이 수동으로 파괴되어야 한다는 것입니다. 그렇지 않으면 영원히 메모리에 남게 됩니다. 샘플 프로젝트에는 3개의 Blueprint AsyncTask가 포함되어 있습니다.

Listen for `Attribute` changes:

![Listen for Attributes Changes BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributeschange.png)

Listen for cooldown changes:

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

Listen for `GE` stack changes:

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting"></a>
## 9. Troubleshooting

<a name="troubleshooting-notlocal"></a>
### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`
You need to [initialize the `ASC` on the client](#concepts-asc-setup).

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>
### 9.2 `ScriptStructCache` errors
You need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata).

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>
### 9.3 Animation Montages are not replicating to clients
Make sure that you're using the `PlayMontageAndWait` Blueprint node instead of `PlayMontage` in your [GameplayAbilities](#concepts-ga). This [AbilityTask](#concepts-at) replicates the montage through the `ASC` automatically whereas the `PlayMontage` node does not.

GameplayAbilities에서 'PlayMontage' 대신 'PlayMontageAndWait' 블루프린트 노드를 사용하고 있는지 확인하십시오. 'PlayMontage' 노드는 그렇지 않은 반면, 'ASC'는 몽타주를 자동으로 복제합니다.

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-duplicatingblueprintactors"></a>
### 9.4 Duplicating Blueprint Actors is setting AttributeSets to nullptr
There is a [bug in Unreal Engine](https://issues.unrealengine.com/issue/UE-81109) that will set `AttributeSet` pointers on your classes to nullptr for Blueprint Actor classes that are duplicated from existing Blueprint Actor classes. There are a few workarounds for this. I've had success not creating bespoke `AttributeSet` pointers on my classes (no pointer in the .h, not calling `CreateDefaultSubobject` in the constructor) and instead just directly adding `AttributeSets` to the `ASC` in `PostInitializeComponents()` (not shown in the Sample Project). The replicated `AttributeSets` will still live in the `ASC's` `SpawnedAttributes` array. It would look something like this:

Unreal Engine에는 기존 Blueprint Actor 클래스에서 복제된 Blueprint Actor 클래스에 대해 클래스의 'AttributeSet' 포인터를 nullptr로 설정하는 버그가 있습니다. 이에 대한 몇 가지 해결 방법이 있습니다. 내 클래스에 맞춤형 `AttributeSet` 포인터를 생성하지 않고(.h에 포인터가 없고 생성자에서 `CreateDefaultSubobject`를 호출하지 않음) 대신 `PostInitializeComponents()`의 `ASC`에 `AttributeSets`를 직접 추가하는 데 성공했습니다. ` (샘플 프로젝트에는 표시되지 않음). 복제된 `AttributeSets`는 여전히 `ASC'의 `SpawnedAttributes` 배열에 있습니다. 다음과 같이 보일 것입니다.

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

In this scenario, you would read and set the values in the `AttributeSet` using the functions on the `ASC` instead of [calling functions on the `AttributeSet` made from the macros](#concepts-as-attributes).

이 시나리오에서는 매크로에서 만든 'AttributeSet'의 함수를 호출하는 대신 'ASC'의 함수를 사용하여 'AttributeSet'의 값을 읽고 설정합니다.

```c++
/** Returns current (final) value of an attribute */
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;

/** Sets the base value of an attribute. Existing active modifiers are NOT cleared and will act upon the new base value. */
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);
```

So the `GetHealth()` would look something like:

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

Setting (initializing) the health `Attribute` would look something like:

```c++
const float NewHealth = 100.0f;
if (AbilitySystemComponent)
{
	AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);
}
```

As a reminder, the `ASC` only ever expects at most one `AttributeSet` object per `AttributeSet` class.

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-unresolvedexternalsymbolmarkpropertydirty"></a>
### 9.5 unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)

If you get a compiler error like:

```
error LNK2019: unresolved external symbol "__declspec(dllimport) void __cdecl UEPushModelPrivate::MarkPropertyDirty(int,int)" (__imp_?MarkPropertyDirty@UEPushModelPrivate@@YAXHH@Z) referenced in function "public: void __cdecl FFastArraySerializer::IncrementArrayReplicationKey(void)" (?IncrementArrayReplicationKey@FFastArraySerializer@@QEAAXXZ)
```

This is from trying to call `MarkItemDirty()` on a `FFastArraySerializer`. I've encountered this from updating an `ActiveGameplayEffect` such as when updating the cooldown duration.

```c++
ActiveGameplayEffects.MarkItemDirty(*AGE);
```

What's happening is that `WITH_PUSH_MODEL` is getting defined in more than one place. `PushModelMacros.h` is defining it as 0 while it's defined as 1 in multiple places. `PushModel.h` is seeing it as 1 but `PushModel.cpp` is seeing it as 0.

The solution is to add `NetCore` to your project's `PublicDependencyModuleNames` in the `Build.cs`.

**[⬆ Back to Top](#table-of-contents)**

<a name="acronyms"></a>
## 10. Common GAS Acronyms

| Name                                                                                                   | Acronyms            |
|------------------------------------------------------------------------------------------------------- | ------------------- |
| AbilitySystemComponent                                                                                 | ASC                 |
| AbilityTask                                                                                            | AT                  |
| [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) | ARPG, ARPG Sample   |
| CharacterMovementComponent                                                                             | CMC                 |
| GameplayAbility                                                                                        | GA                  |
| GameplayAbilitySystem                                                                                  | GAS                 |
| GameplayCue                                                                                            | GC                  |
| GameplayEffect                                                                                         | GE                  |
| GameplayEffectExecutionCalculation                                                                     | ExecCalc, Execution |
| GameplayTag                                                                                            | Tag, GT             |
| ModifierMagnitudeCalculation                                                                           | ModMagCalc, MMC     |

**[⬆ Back to Top](#table-of-contents)**

<a name="resources"></a>
## 11. Other Resources
* [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* Source Code!
   * Especially `GameplayPrediction.h`
* [Lyra Sample Project by Epic](https://unrealengine.com/marketplace/en-US/learn/lyra)
* [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/) has a text channel dedicated to GAS `#gameplay-ability-system`
   * Check pinned messages
* [GitHub repository of resources by Dan 'Pan'](https://github.com/Pantong51/GASContent)
* [YouTube Videos by SabreDartStudios](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

<a name="resources-daveratti"></a>
### 11.1 Q&A With Epic Game's Dave Ratti

<a name="resources-daveratti-community1"></a>
#### 11.1.1 Community Questions 1
[Dave Ratti responses to the Unreal Slackers Discord Server community questions about GAS](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89):

1. How can we create scoped prediction windows on demand outside or irrespective of GameplayAbilities? For example, how can a fire and forget projectile locally predict a damage GameplayEffectwhen it hits an enemy?

> The PredictionKey system is not really meant to do this. Fundamentally this systems works by a client initiating a predictive action, telling the server about it with a key, and then both client and server running the same thing and associating predictive side effects with the given prediction key. For example, “I am predictively activating an ability” or “I have produced target data and am going to predictively run the part of the ability graph after the WaitTargetData task”.
>
> With this pattern, the PredictionKey “bounces” off the server and comes back to the client via UAbilitySystemComponent::ReplicatedPredictionKeyMap (replicated property). Once the key is replicated back from the server, the client is able to undo all of the locally predictive side effects (GameplayCues, GameplayEffects): the replicated versions *will be there* and if they aren’t then it was a misprediction. Knowing exactly when to undo the predictive side effects is crucial here: if you are too early you will see gaps, if you are too late you will have “double”. (Note this is referring to stateful prediction, like a looping GameplayCue of a duration based Gameplay Effect. “Burst” GameplayCues and instant Gameplay Effects are never “undone” or rolled back. They are just skipped on the client if there is a prediction key associated with them).
>
> To further hit home the point: it’s crucial that predictive action is something the server does not do on their own, but only does so when the client tells them too. So having a generic “Create a key on demand and tell the server so I can run something” does not work unless that “something” is something the server will only do once told to by the client.
>
> Backing up to the original question: something like a fire and forget projectile. Both Paragon and Fornite have projectile actor classes that use GameplayCues. However we do not use the Prediction Key system to do these. Instead we have a concept on Non-Replicated GameplayCues. GameplayCues that just fire off locally and are skipped by the server completely.  Essentially all these are, are direct calls to UGameplayCueManager::HandleGameplayCue. They do not route through the UAbilitySystemComponent so no prediction key checks / early returns are made.
>
> The downside with non replicated GameplayCues is that, well, they are not replicated. So its up to the projectile class/blueprint to make sure the code paths that call these functions are running on everyone. We have for cues startup (called in BeginPlay), explosion, hit wall/character, etc.
>
> These type ofevents are already generated client side, so calling into a non replicated gameplay cue was no big deal. Complicated blueprints can be tricky, and are up to the author to make sure they understand what is running where.


2. When using a WaitNetSyncAbilityTaskwith OnlyServerWaitto create a scoped prediction window in a locally predicted GameplayAbility, could players potentially cheat by delaying their packets to the Server to control GameplayAbilitytiming since the Server is waiting for their RPC withtheir prediction key? Was this ever an issue in Paragon or Fortnite, and if so, what did Epic do to remedy it?

> Yes, this is a valid concern. Any ability blueprint running on the server that is waiting for a client “signal” is potentially vulnerable to lag switch type exploits.
>
> Paragon had a custom targeting task similar to UAbilityTask_WaitTargetData. In this task we had timeouts, or a “max delay” that we would wait on the client for instantaneous targeting modes. If the targeting mode was waiting for user confirmation (button press) then it would be ignored since the user is allowed to take his time. But for abilities that instantly confirmed targeting we would only wait a certain amount of time before either A) generating the target data server side of B) canceling the ability.
>
> We never had such mechanisms for WaitNetSync, which we used pretty sparingly.
>
> I don’t believe Fortnite makes use of anything like this though. The weapon abilities in Fortnite are special cased batched to a single fortnite-specific RPC: one RPC to activate the ability, provide target data, and end the ability. So weapon abilities are intrinsically not vulnerable to this in Battle Royale.
>
> My take is that this is something that could probably be solved system wide but I don’t see us making the change ourselves anytime soon. Spot fixing WaitNetSync to include a max delay for the case you mention is probably a reasonable task, but again - unlikely we will do this on our end in the immediate future.


3. Which EGameplayEffectReplicationModedid Paragon and Fortnite use and what are Epic’s recommendations for when to use each?

> Both games essentially use Mixed mode for their player controlled characters and Minimal for AI controlled (AI minions, jungle creeps, AIHusks, etc). This is what I would recommend most people using the system in a multiplayer game. The sooner into your project you set these, the better.
>
> Fortnite goes a few steps further with its optimizations. It actually does not replicate the UAbilitySystemComponent at all for simulated proxies. The component and attribute subobjects are skipped inside ::ReplicateSubobjects() on the owning fortnite player state class. We do push the bare minimum replicated data from the ability system component to a structure on the pawn itself (basically, a subset of attribute values and a white list subset of tags that we replicate down in a bitmask). We call this a “proxy”. On the receiving side we take the proxy data, replicated on the pawn, and push it back into ability system component on the player state. So you do have an ASC for each player in FNBR, it just doesn’t directly replicate: instead it replicates data via a minimal proxy struct on the pawn and then routes back to the ASC on receiving side. This is advantage since its A) a more minimal set of data B) takes advantage of pawn relevancy.
>
> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.


4. Since we cannot predict the removal of GameplayEffectsas per GameplayPrediction.h, are there any strategies for mitigating the effects of latency on removing GameplayEffects? For example, when removing a movement speed slow, we currently have to wait for the Server to replicate the GameplayEffectremoval resulting in a snap of the player’s character position.

> This is a tough one and I don’t have a good answer. We generally skirted around these problems with tolerances and smoothing. I totally agree that ability system and precise synchronization with the character movement system is not in a good place and something we do want to fix.
>
> I had a shelf of allowing predictive removal of GEs but could never work out all edge cases before having to move on. This doesn’t solve everything though since character movement still has an internal saved move buffer that does not know anything about the ability system and possible movement speed modifiers, etc. It is still possible to get into correction feedback loops even outside of not being able to predict the removal of GEs.
>
> If you think you have a case that is truly desperate, you are able to predictively add a GE that would inhibit your movement speed GEs. I’ve never done this myself but have theorized about it before. It may be able to help with a certain class of problem.


5. We know that the AbilitySystemComponentlives on the PlayerStatein Paragon and Fortnite and on the Characterin the Action RPG Sample. What are Epic’s internal rules, guidelines, or recommendations for where the AbilitySystemComponentshould live --what should its Ownerbe?

> In general I would say anything that does not need to respawn should have the Owner andAvatar actor be the same thing. Anything like AI enemies, buildings, world props, etc.
>
> Anything that does respawn should have the Owner and Avatar be different so that the Ability System Component does not need to be saved off / recreated / restored after a respawn.. PlayerState is the logical choice it is replicated to all clients (where as PlayerController is not). The downside is PlayerStates are always relevant so you can run into problems in 100 player games. (See notes on what FN did in question #3).


6. Is it viable to have several AbilitySystemComponentswhich have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with Ownerset to PlayerState)?

> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning actor. The former may be possible: just aggregate the tags from all all ASCs (but watch out - HasAlMatchingGameplayTags may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE - which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will multiple ASCs beneath them.
>
> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags the describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (E.g, attributes and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.


7. Is there a way to stop the Server from overwriting the cooldown duration of locally predicted abilities on the Owning Client? In scenarios of high latency, this would let theOwning Client "try" to activate the ability again when its local cooldown expires but it is still on cooldown on the Server. By the time the Owning Client's activation request reaches the Server over the network, the Server may be off cooldown or the Server might be able to queue the activation request for the remaining milliseconds that it has left. Otherwise as is, clients with higher latency have a longer delay before when they can reactivate an ability versus those with less latency. This is most apparent with very low cooldown abilities like a basic attack that can be less than one second of cooldown. If there isn't a way to stop the Server from overwriting the cooldown duration of locally predicted abilities, what is Epic's strategy for mitigating theeffects of high latency on reactivating abilities? To word it another example-based way, how did Epic design Paragon's basic attacks and other abilities so that high latency players could attack or activate at the same speed as low latency players with local prediction?

> The short answer there is not a way to prevent this and Paragon definitely had the problem. Higher latency connections would have a lower ROF with basic attacks.
>
> I attempted to fix this by adding “GE reconciliation” where latency was taken into account when calculating GE duration. Essentially allowing the server to eat some of the total GE time so that the effective time of the GE client side would be 100% consistent with any amount of latency (though fluctuations could still cause issues). However I never got this working in a state that could ship and the project moved fast and we just never fully addressed it.
>
> Fortnite does its own bookkeeping for weapon firing rates: it does not use GEs for cooldowns on weapons. I would recommend this if this is a critical problem for your game.


8. What is Epic’s roadmap for the GameplayAbilitySystem plugin? Which features does Epic plan to add in 2019 and beyond?

> We feel that overall the system is pretty stable at this point and we don’t have anyone working on major new features. Bug fixes and small improvements occasionally are made for Fortnite or from UDN/pull requests, but that is it right now.
>
> Longer term, I think we will eventually do a “V2” or some big changes. We learned a lot from writing this system and feel we got a lot right and a lot wrong. I would love a chance to correct those mistakes and improve some of the fatal flaws that were pointed out above.
>
> If a V2 was to ever come, providing an upgrade path would be of utmost importance. We would never make a V2 and leave Fortnite on V1 forever: there would be some path or procedures that would automatically migrate as much as possible, though there would still almost certainly be some manual remaking required.
>
> The high priority fixes would be:
> * Better interoperability with the character movement system. Unifying client prediction.
> * GE removal prediction (question #4)
> * GE latency reconciliation (question #8)
> * Generalized network optimizations such as batching RPCs and proxy structures. Mostly the stuff that we’ve done for Fortnite but find ways to break it down into more generalized form, at least so that games can write their own game specific optimizations more easily.
>
> The more general refactor type of changes I would consider making:
> * I would like to look at fundamentally moving away from having GEs reference spreadsheet values directly, instead they would be able to emit parameters and those parameters could be filled by some higher level object that is bound to spreadsheet values. The problem with the current model is that GEs become unsharable due to their tight coupling with the curve table rows. I think a generalized system for parameterization could be written and be the underpinning of a V2 system.
> * Reduce number of “policies” on UGameplayAbility. I would remove ReplicationPolicy InstancingPolicy. Replication is, imo, almost never actually needed and causes confusion. InstancingPolicy should be replaced instead by making FGameplayAbilitySpec a UObject that can be subclassed. This should have been the “non instantiated ability object” that has events and is blueprintable. The UGameplayAbility should be the “instanced per execution” object. It could be optional if you need to actually instantiate: instead “non instanced” abilities would be implemented via the new UGameplayAbilitySpec object. 
> * The system should provide more “middle level” constructs such as “filtered GE application container” (data drive what GEs to apply to which actors with higher level gameplay logic), “Overlapping volume support” (apply the “Filtered GE application container” based on collision primitive overlap events), etc.These are building blocks that every project ends up implementing in their own way. Getting them right is non trivial so I think we should do a better job providing some basic implementations. 
> * In general, reducing boilerplate needed to get your project up and running. Possibly a separate module “Ex library” or whatever that could provide things like passive abilities or basichitscan weapons out of the box. This module would be optional but would get you up and running quickly.
> * I would like to move GameplayCues to a separate module that is not coupled with the ability system. I think there are a lot of improvements that could be made here.


> This is only my personal opinion and not a commitment from anyone. I think the most realistic course of action will be as new engine tech initiatives come through, the ability system will need to be updated and that will be a time to do this sort of thing. These initiatives could be related to scripting, networking, or physics/character movement. This is all very far looking ahead though so I cannot give commitments or estimates on timelines.

**[⬆ Back to Top](#table-of-contents)**

<a name="resources-daveratti-community2"></a>
#### 11.1.2 Community Questions 2
Community member [iniside](https://github.com/iniside)'s Q&A with Dave Ratti:

1. Is the support for decoupled fixed ticking planned ? I'd like to
have Game Thread be fixed (like 30/60fps) and let the rendering thread
run wild. I ask if this is something we should expect in future or
not, to make some assumptions about how gameplay should work.
I ask mainly because there is now a fixed async tick for physics and
this poses a question how the rest of the system might work in the
future. I do not hide that having the ability to have fixed tick game
thread without also fixing tick rate of the rest of the engine would
be beyond awesome.

> There are no plans to decouple rendering frame rate and game thread tick frame rate. I think the ship has sailed on this ever happening due to the complexity of these systems and the requirement to preserve backwards compatibility with previous engine versions.
>
> Instead, the direction we've gone is to have an asynchronous "Physics Thread" which runs at a fixed tick rate, independent of the game thread. Things that need to run at a fixed rate can run here and the game thread / rendering can operate how they always have.
>
> It's worth clarifying that Network Prediction supports what it calls Independent Ticking and Fixed Ticking modes. My long term plan is to keep Independent Ticking roughly how it is today in Network Prediction where it runs on the game thread at variable frame rate and there is no "group/world" prediction, it's just the classic "clients predict their own pawn and owned actors" model. And Fixed Ticking would be what uses the async physics stuff and allows you to predict non client controlled/owned actors like physics objects and other clients/pawns/vehicles/etc.


2. Is there any plan on how the integration of Network Prediction will
look with the Ability System ? Like for example, fixed frame ability
activation (so the server gets frames in which abilities were
activated and tasks executed instead of prediction keys) ?

> Yes, the plan is to rewrite/remove the Ability System's prediction keys and replace them with Network Prediction constructs. The MockAbility examples in NetworkPredictionExtras show how this might work but they are more "hard coded" than what GAS will require. 
>
> The main idea would be that we remove the explicit client->server Prediction Key exchange in the ASC's RPCs. There would no longer be prediction windows or scoped prediction keys. Instead everything would be anchored around NetworkPrediction frames. The important thing is that client and server agree on when things happen. Examples would be:
>
> * When abilities were activated/ended/cancelled
> * When Gameplay Effects were applied/removed
> * Attribute values (what an attributes value was at frame X)
>
> I think this could be done generically at the ability system level. But actually making the user-defined logic inside a UGameplayAbility completely rollback-able would still take more work. We may end up having a subclass of UGameplayAbility that is fully rollbackable and has access to a more limited set of functionality or only Ability Tasks that are marked as rollback-friendly. Something like that. There are also many implications to animation events and root motion and how those are processed.
>
> Wish I had a more clear answer but it's really important we get the foundation right before touching GAS again. Movement and physics have to be solid before the higher level systems can be changed.


3. Is there a plan to move Network Prediction development toward the
main branch ? Not gonna lie, I'd really like to check the latest code.
Regardless of it's state.

> We are working towards it. The system work is still all being done in NetworkPrediction (see NetworkPhysics.h) and the underlying async physics stuff should be all available (RewindData.h etc). But we also have use cases in Fortnite that we have been focused on that obviously can't be made public. We are working through bugs, performance optimizations, etc.
>
> For more context: when working on the early versions of this system, we were very focused on the "front end" of things - how state and simulations were defined and written. We learned a lot there. But as the async physics stuff has come online, we've been much more focused on just getting something real to work in this system, at the expense of throwing out some of our early abstractions. The goal here is to circle back when the real thing is working and reunifying things. E.g, get back to the "front end" and make the final version of that on top of the core pieces of tech we are working on now.


4. For some time on Main there was a plugin for sending Gameplay
Messages (Looked like Event/Message Bus), but it was removed. Any
plans to restore it ? With the Game Features/Modular Gameplay plugins,
having a generic Event Bus Dispatcher would be extremely useful.

> I think you are referring to the GameplayMessages plugin. This will probably come back at some point - the API isn't really finalized yet and the author didn't mean for it to be public yet. I agree it should be useful for modular gameplay design. But it's not really my area so I don't have much more information. 


5. I've been playing recently with async fixed physics and the results
are promising, though if there is going to be NP update in the future
I will probably just play around and wait, since to get it working I
still need to get entire engine into fixed tick and on the other hand
I try to keep physics at 33ms. Which does not make for a good
experience if everything is at 30 fps (:.

I have noticed there was some work on Async
CharacterMovementComponent, but not sure if this will be using Network
Prediction, or it is a separate effort ?

Since I noticed it, I also went ahead and tried to implement my custom
async movement at fixed tick rate, which worked okay, but on top of it
I also needed to add a separate update for interpolation. The setup
was to run simulation tick on separate worker threads at fixed 33ms
update, do calculations, save result, and interpolate it at the game
thread to match current frame rate. Not perfect, but it got the job
done.

My question is, if this is something that might be easier to set up in
the future, as there is just quite a bit of boilerplate code to write,
(the interpolation part) and it's not particularly efficient to
interpolate each moving object individually.

The async stuff is really interesting, because it would allow you to
really run game simulation at fixed update rate (which would make
fixed thread unneeded) and have more predictable results. Is this
something that is intended going forward, or more of a benefit to
select systems ? As far as I remember actor transforms are not updated
async and blueprints are not entirely thread safe. In other words is
it something that is planned to be supported at more of a framework
level or something that each game has to solve on it's own ?

> Async CharacterMovementComponent
>
> This is basically an early prototype/experiment of porting CMC as it is to the physics thread. I don't view it as the future of CMC yet, but it could evolve into that. Right now there is no networking support so it's not something I would really follow. The people doing it are mostly concerned with measuring input latency that this system would add and how that could be mitigated.
>
> I still need to get entire engine into fixed tick and on the other hand I try to keep physics at 33ms. Which does not make for a good experience if everything is at 30 fps (:.
>
> The async stuff is really interesting, because it would allow you to really run game simulation at fixed update rate (which would make fixed thread unneeded) 
>
> Yes. The goal here is that with async physics enabled, you can run the engine at variable tick rate while the physics and "core" gameplay simulations can run at the fixed rate (such as character movement, vehicles, GAS, etc).
>
> These are the cvars that need to be set to enable this now: (I think you've figured this out)  
> `p.DefaultAsyncDt=0.03333`  
> `p.RewindCaptureNumFrames=64`
>
> Chaos does provide interpolation for the physics state (E.g, the transforms that get pushed back to the UPrimitiveComponent and are visible to the game code). There is a cvar now, `p.AsyncInterpolationMultiplier`, which controls that if you want to look at it. You should see smooth continuous motion of physics bodies without having to write any extra code. 
>
> If you want to interpolate non physics state, it is still up to you to do that right now. The example would be like a cool-down that you want to update (tick) on the async physics thread but see smooth continuous interpolation on the game thread so that every render frame the cool down visualization is updated. We will get to this eventually but don't have examples yet.
>
> there is just quite a bit of boilerplate code to write,
>
> Yeah, so that has been a big general problem with the system up until now. We want to provide an interface that experienced programmers can use to maximize performance and safety (the ability to write gameplay code that "just works" predictively without tons of hazards and things you could-do-but-better-not). So something like CharacterMoverment might do a bunch of custom stuff to maximize its performance - e.g, writing templated code and doing batch updating, going wide, breaking the update loop into distinct phases etc. We want to provide a good "low level" interface into the async thread and rollback systems for this use case. And in this case too - it's still reasonable that the character movement system itself is extendable in its own way. For example providing a way to blueprint a custom movement mode and providing a blueprint API that is thread safe.
>
> But we recognize this is not acceptable for simpler gameplay objects that don't really need their own "system". Something more inline with Unreal is what is needed. E.g, using the reflection system, having general blueprint support, etc. There are examples of blueprints being used on other threads (see BlueprintThreadSafe keyword and what the animation system has been working towards). So I think there will be some form of this one day. But again, we aren't there yet.
>
> I realize you were just asking about interpolation but that is the general answer: right now we have you do everything manually like NetSerialize, ShouldReconcile, Interpolate, etc but eventually we'll have a way that is like "if you want to just use the reflection system, you don't have to manually write this stuff". We just don't want to *force* everyone to use the reflection system since that imposes other limitations that we think we don't want to take on the lowest levels of the system. 
>
> And then just to tie this back to what I said earlier - right now we are really focused on getting a few very specific examples working and performant and then we will turn attention back to the front end and making things friendly to use and iterate on, reducing boilerplate, etc for everybody else to use. 


GAS에 대한 Unreal Slackers Discord Server 커뮤니티 질문에 대한 Dave Ratti의 답변:

1. GameplayAbilities 외부에 있거나 관계 없이 요청 시 범위가 지정된 예측 창을 어떻게 만들 수 있습니까? 예를 들어 Fire and Forget 발사체가 적을 공격할 때 어떻게 손상을 로컬로 예측할 수 있습니까?

> PredictionKey 시스템은 실제로 이를 수행하기 위한 것이 아닙니다. 기본적으로 이 시스템은 클라이언트가 예측 작업을 시작하고 키로 이에 대해 서버에 알리고 클라이언트와 서버 모두 동일한 작업을 실행하고 예측 부작용을 주어진 예측 키와 연관시키는 방식으로 작동합니다. 예를 들어, "나는 능력을 예측적으로 활성화하고 있습니다" 또는 "목표 데이터를 생성했으며 WaitTargetData 작업 후에 능력 그래프의 일부를 예측적으로 실행할 예정입니다".
>
> 이 패턴을 사용하면 PredictionKey가 서버에서 "바운스"되고 UAbilitySystemComponent::ReplicatedPredictionKeyMap(복제된 속성)을 통해 클라이언트로 돌아옵니다. 키가 서버에서 다시 복제되면 클라이언트는 모든 로컬 예측 부작용(GameplayCues, GameplayEffects)을 실행 취소할 수 있습니다. 복제된 버전은 *거기에 있을 것이고* 없으면 잘못된 예측입니다. 예측 부작용을 취소할 때를 정확히 아는 것은 여기에서 매우 중요합니다. 너무 일찍 시작하면 격차가 발생하고 너무 늦으면 "이중"이 발생합니다. (이것은 지속 시간 기반 게임 플레이 효과의 반복되는 GameplayCue와 같은 상태 기반 예측을 나타냅니다. "버스트" GameplayCues 및 인스턴트 게임 플레이 효과는 "실행 취소"되거나 롤백되지 않습니다. 예측 키가 있는 경우 클라이언트에서 건너뜁니다. 그들과 관련된).
>
> 요점을 더 정확히 파악하려면 예측 조치가 서버가 자체적으로 수행하는 것이 아니라 클라이언트가 지시할 때만 수행하는 것이 중요합니다. 따라서 "무언가"가 서버가 클라이언트에게 한 번만 지시한 경우가 아니면 "요청 시 키를 생성하고 서버에 지시하여 무언가를 실행할 수 있도록 하십시오"라는 일반 사항을 갖는 것은 작동하지 않습니다.
>
> 원래 질문으로 돌아가서: 발사체를 불에 태우고 잊어버리는 것과 같은 것입니다. Paragon과 Fornite에는 모두 GameplayCues를 사용하는 발사체 액터 클래스가 있습니다. 그러나 우리는 이를 수행하기 위해 예측 키 시스템을 사용하지 않습니다. 대신 Non-Replication된 GameplayCues에 대한 개념이 있습니다. 로컬에서만 실행되고 서버에서 완전히 건너뛰는 GameplayCues. 본질적으로 이것들은 모두 UGameplayCueManager::HandleGameplayCue에 대한 직접 호출입니다. UAbilitySystemComponent를 통해 라우팅하지 않으므로 예측 키 확인/조기 반환이 이루어지지 않습니다.
>
> 복제되지 않은 GameplayCues의 단점은 복제되지 않는다는 것입니다. 따라서 이러한 함수를 호출하는 코드 경로가 모든 사람에서 실행되고 있는지 확인하는 것은 발사체 클래스/청사진에 달려 있습니다. 우리는 시작(BeginPlay에서 호출), 폭발, 벽에 부딪힘/캐릭터 등을 위한 큐가 있습니다.
>
> 이러한 유형의 이벤트는 이미 클라이언트 측에서 생성되었으므로 복제되지 않은 게임 플레이 큐를 호출하는 것은 큰 문제가 되지 않았습니다. 복잡한 청사진은 까다로울 수 있으며 어디에서 실행되고 있는지 이해하는 것은 작성자의 몫입니다.


2. OnlyServerWait와 함께 WaitNetSyncAbilityTask를 사용하여 로컬로 예측된 GameplayAbility에서 범위가 지정된 예측 창을 만들 때 서버가 예측 키로 RPC를 기다리고 있기 때문에 플레이어가 GameplayAbilitytiming을 제어하기 위해 서버에 대한 패킷을 지연시켜 잠재적으로 부정 행위를 할 수 있습니까? 이것이 Paragon이나 Fortnite에서 문제가 된 적이 있습니까? 그렇다면 Epic은 이를 해결하기 위해 무엇을 했습니까?

> 예, 이것은 유효한 우려입니다. 클라이언트 "신호"를 기다리는 서버에서 실행되는 모든 능력 청사진은 잠재적으로 지연 스위치 유형 악용에 취약합니다.
>
> Paragon에는 UAbilityTask_WaitTargetData와 유사한 사용자 지정 타겟팅 작업이 있습니다. 이 작업에서 우리는 즉각적인 타겟팅 모드를 위해 클라이언트에서 기다리는 시간 초과 또는 "최대 지연"이 있었습니다. 타겟팅 모드가 사용자 확인(버튼 누름)을 기다리는 경우 사용자가 시간을 할애할 수 있으므로 무시됩니다. 그러나 즉시 대상 지정을 확인하는 능력의 경우 A) 대상 데이터 서버 측 생성 B) 능력 취소 전에 특정 시간만 기다릴 것입니다.
>
> 우리는 거의 사용하지 않는 WaitNetSync에 대해 그러한 메커니즘을 갖고 있지 않았습니다.
>
> 나는 Fortnite가 이와 같은 것을 사용한다고 생각하지 않습니다. Fortnite의 무기 능력은 단일 포트나이트 전용 RPC로 특수한 경우 일괄 처리됩니다. 하나의 RPC는 능력을 활성화하고, 대상 데이터를 제공하고, 능력을 종료합니다. 따라서 무기 능력은 본질적으로 Battle Royale에서 이에 취약하지 않습니다.
>
> 제 생각에는 이것이 아마도 시스템 전체에서 해결될 수 있는 문제이지만 조만간 우리 스스로 변경 작업을 수행할 것으로 보이지는 않습니다. 언급한 경우에 대한 최대 지연을 포함하도록 WaitNetSync를 스팟 수정하는 것은 아마도 합리적인 작업이지만 다시 - 가까운 시일 내에 이 작업을 완료하지 않을 것입니다.


3. 어떤 EGameplayEffectReplicationModedid Paragon과 Fortnite를 사용하며 각각을 언제 사용해야 하는지에 대한 Epic의 권장 사항은 무엇입니까?

> 두 게임 모두 기본적으로 플레이어 제어 캐릭터에는 혼합 모드를 사용하고 AI 제어에는 최소 모드를 사용합니다(AI 미니언, 정글 크립, AIHusk 등). 이것이 내가 멀티플레이어 게임에서 시스템을 사용하는 대부분의 사람들에게 추천하고 싶은 것입니다. 이러한 설정은 프로젝트에 빠를수록 좋습니다.
>
> Fortnite는 최적화를 통해 몇 단계 더 나아갑니다. 실제로 시뮬레이션된 프록시에 대해 UAbilitySystemComponent를 전혀 복제하지 않습니다. 구성 요소 및 속성 하위 개체는 소유 포트나이트 플레이어 상태 클래스의 ::ReplicateSubobjects() 내부에서 건너뜁니다. 우리는 최소한의 복제 데이터를 능력 시스템 구성 요소에서 폰 자체의 구조로 푸시합니다(기본적으로 속성 값의 하위 집합과 비트마스크에서 복제하는 태그의 화이트리스트 하위 집합). 우리는 이것을 "프록시"라고 부릅니다. 수신 측에서는 폰에 복제된 프록시 데이터를 가져와서 플레이어 상태의 능력 시스템 구성 요소로 다시 푸시합니다. 따라서 FNBR의 각 플레이어에 대한 ASC가 있지만 직접 복제하지는 않습니다. 대신 폰의 최소 프록시 구조체를 통해 데이터를 복제한 다음 수신 측의 ASC로 다시 라우팅합니다. 이것은 A) 더 최소한의 데이터 세트 B) 폰 관련성을 활용하기 때문에 이점입니다.
>
> 그 이후로 수행된 다른 서버 측 최적화(복제 그래프 등)에 여전히 필요한지 확실하지 않으며 가장 유지 관리 가능한 패턴이 아닙니다.


4. GameplayPrediction.h에 따라 GameplayEffects의 제거를 예측할 수 없으므로 GameplayEffects 제거에 대한 대기 시간의 영향을 완화하기 위한 전략이 있습니까? 예를 들어 느린 이동 속도를 제거할 때 현재 서버가 GameplayEffectremoval을 복제하여 플레이어의 캐릭터 위치를 스냅할 때까지 기다려야 합니다.

> 어려운 일이고 좋은 답이 없습니다. 우리는 일반적으로 허용오차와 평활화로 이러한 문제를 우회했습니다. 능력 시스템과 캐릭터 이동 시스템과의 정확한 동기화가 좋지 않고 우리가 수정하고 싶은 부분에 전적으로 동의합니다.
>
> GE를 예측적으로 제거할 수 있는 선반이 있었지만 계속 진행하기 전에 모든 극단적인 경우를 해결할 수는 없었습니다. 캐릭터 이동에는 능력 시스템 및 가능한 이동 속도 수정자 등에 대해 아무것도 모르는 내부 저장된 이동 버퍼가 있기 때문에 이것이 모든 것을 해결하지는 못합니다. GE의 제거를 예측합니다.
>
> 정말 절박한 경우가 있다고 생각되면 이동 속도 GE를 억제하는 GE를 예측적으로 추가할 수 있습니다. 나는 이것을 직접 한 적이 없지만 이전에 그것에 대해 이론화한 적이 있습니다. 특정 유형의 문제를 해결하는 데 도움이 될 수 있습니다.


5. 우리는 AbilitySystemComponent가 Paragon과 Fortnite의 PlayerState와 Action RPG 샘플의 캐릭터에 있다는 것을 알고 있습니다. 능력 시스템 구성 요소가 있어야 하는 위치에 대한 에픽의 내부 규칙, 지침 또는 권장 사항은 무엇입니까? 소유자는 무엇이어야 합니까?

> 일반적으로 리스폰할 필요가 없는 것은 소유자와 아바타 액터가 같아야 합니다. AI 적, 건물, 세계 소품 등과 같은 모든 것.
>
> 재생성되는 모든 항목은 소유자와 아바타가 달라야 재생성 후 능력 시스템 구성 요소를 저장/재생성/복원할 필요가 없습니다. PlayerState는 모든 클라이언트에 복제되는 논리적 선택입니다(여기서 PlayerController는 아니다). 단점은 PlayerStates가 항상 관련이 있으므로 100명의 플레이어 게임에서 문제가 발생할 수 있다는 것입니다. (3번 질문에서 FN이 한 일에 대한 메모 참조).


6. 소유자는 같지만 아바타가 다른 여러 능력 시스템 구성 요소를 가질 수 있습니까(예: 소유자가 PlayerState로 설정된 폰 및 무기/아이템/발사체)?

> 내가 보는 첫 번째 문제는 소유 액터에서 IGameplayTagAssetInterface 및 IAbilitySystemInterface를 구현하는 것입니다. 전자가 가능할 수도 있습니다. 모든 ASC에서 태그를 집계하기만 하면 됩니다(단, HasAlMatchingGameplayTags는 교차 ASC 집계를 통해서만 충족될 수 있습니다. 각 ASC에 대한 호출을 전달하고 결과를 함께 OR하는 것만으로는 충분하지 않습니다.) 그러나 후자는 더 까다롭습니다. 어떤 ASC가 권위 있는 것입니까? 누군가가 GE를 신청하고 싶다면 어떤 사람이 받아야 합니까? 아마도 당신은 이것을 해결할 수 있지만 문제의 이 면은 가장 어려울 것입니다. 소유자는 그 아래에 여러 ASC를 가질 것입니다.
>
> 폰과 무기에 ASC를 분리하는 것은 그 자체로 의미가 있을 수 있습니다. 예를 들어, 무기를 설명하는 태그와 소유 폰을 설명하는 태그를 구별합니다. 아마도 무기에 부여된 태그가 소유자에게 "적용"되고 다른 어떤 것도 적용되지 않는다는 것이 합리적일 수 있습니다(예: 속성과 GE는 독립적이지만 소유자는 위에서 설명한 것처럼 소유 태그를 집계합니다). 이것은 효과가 있을 수 있다고 확신합니다. 그러나 동일한 소유자에게 여러 ASC를 보유하는 것은 위험할 수 있습니다.


7. 서버가 소유 클라이언트에서 로컬로 예측된 능력의 재사용 대기시간을 덮어쓰지 않도록 하는 방법이 있습니까? 대기 시간이 긴 시나리오에서 이를 통해 소유 클라이언트는 로컬 쿨다운이 만료되었지만 여전히 서버에서 쿨다운 상태에 있을 때 기능을 다시 활성화하려고 "시도"합니다. 소유 클라이언트의 활성화 요청이 네트워크를 통해 서버에 도달할 때까지 서버는 휴지 상태가 아니거나 서버가 남은 밀리초 동안 활성화 요청을 대기열에 넣을 수 있습니다. 그렇지 않으면 대기 시간이 더 긴 클라이언트는 대기 시간이 더 짧은 클라이언트에 비해 기능을 다시 활성화할 수 있을 때까지 더 긴 지연 시간을 갖습니다. 이것은 쿨다운이 1초 미만일 수 있는 기본 공격과 같은 매우 낮은 쿨다운 능력에서 가장 분명합니다. 없으면' 서버가 로컬에서 예측한 능력의 재사용 대기시간을 덮어쓰는 것을 막는 방법, 능력 재활성화에 대한 긴 대기 시간의 영향을 완화하기 위한 Epic의 전략은 무엇입니까? 다른 예시 기반 방식으로 표현하자면 Epic은 지연 시간이 긴 플레이어가 로컬 예측을 통해 지연 시간이 짧은 플레이어와 동일한 속도로 공격하거나 활성화할 수 있도록 Paragon의 기본 공격 및 기타 능력을 어떻게 설계했습니까?

> 짧은 대답은 이것을 막을 방법이 없으며 Paragon은 확실히 문제가 있었습니다. 대기 시간이 더 긴 연결은 기본 공격에서 더 낮은 ROF를 갖습니다.
>
> GE 지속 시간을 계산할 때 대기 시간을 고려한 "GE 조정"을 추가하여 이 문제를 해결하려고 했습니다. 기본적으로 서버가 총 GE 시간의 일부를 사용하도록 허용하여 GE 클라이언트 측의 유효 시간이 모든 대기 시간과 100% 일치하도록 합니다(변동이 여전히 문제를 일으킬 수 있음). 그러나 배송 가능한 상태에서 이 작업을 수행한 적이 없고 프로젝트가 빠르게 진행되었으며 완전히 해결하지 못했습니다.
>
> Fortnite는 무기 발사 속도에 대한 자체 부기를 수행합니다. 무기의 재사용 대기시간에 GE를 사용하지 않습니다. 이것이 귀하의 게임에 중요한 문제라면 이것을 추천합니다.


8. GameplayAbilitySystem 플러그인에 대한 에픽의 로드맵은 무엇입니까? 에픽은 2019년 이후에 어떤 기능을 추가할 계획인가요?

> 현재로서는 전반적으로 시스템이 매우 안정적이며 주요 새 기능을 작업하는 사람이 아무도 없다고 생각합니다. Fortnite 또는 UDN/pull 요청에서 버그 수정 및 약간의 개선이 때때로 이루어지지만 지금은 그게 전부입니다.
>
> 장기적으로 우리는 결국 "V2"나 큰 변화를 일으킬 것이라고 생각합니다. 우리는 이 시스템을 작성하면서 많은 것을 배웠고 우리가 많이 맞았고 많이 틀렸다고 느낍니다. 이러한 실수를 수정하고 위에서 지적한 치명적인 결함을 개선할 수 있는 기회를 갖고 싶습니다.
>
> V2가 나온다면 업그레이드 경로를 제공하는 것이 가장 중요할 것입니다. 우리는 V2를 만들지 않고 Fortnite를 V1에 영원히 남겨두지 않을 것입니다. 여전히 거의 확실히 수동으로 다시 작성해야 하지만, 가능한 한 많이 자동으로 마이그레이션되는 몇 가지 경로나 절차가 있을 것입니다.
>
> 우선 순위가 높은 수정 사항은 다음과 같습니다.
> * 캐릭터 이동 시스템과의 상호 운용성이 향상되었습니다. 클라이언트 예측 통합.
> * GE 제거 예측(질문 #4)
> * GE 대기 시간 조정(질문 #8)
> * 일괄 처리 RPC 및 프록시 구조와 같은 일반화된 네트워크 최적화. 대부분 우리가 Fortnite에 대해 수행했지만, 최소한 게임이 고유한 게임별 최적화를 보다 쉽게 작성할 수 있도록 보다 일반화된 형태로 분해하는 방법을 찾습니다.
>
> 내가 고려하는 더 일반적인 리팩터 유형 변경:
> * GE가 스프레드시트 값을 직접 참조하는 것에서 근본적으로 벗어나고 싶습니다. 대신 매개변수를 내보낼 수 있고 이러한 매개변수는 스프레드시트 값에 바인딩된 더 높은 수준의 개체로 채워질 수 있습니다. 현재 모델의 문제는 GE가 곡선 테이블 행과의 긴밀한 결합으로 인해 공유할 수 없게 된다는 것입니다. 매개변수화를 위한 일반화된 시스템이 작성될 수 있고 V2 시스템의 토대가 될 수 있다고 생각합니다.
> * UGameplayAbility에서 "정책"의 수를 줄입니다. ReplicationPolicy InstancingPolicy를 제거하겠습니다. 복제는 실제로 거의 필요하지 않으며 혼란을 야기합니다. 대신 FGameplayAbilitySpec 을 서브클래싱할 수 있는 UObject 로 만들어 InstancingPolicy 를 대체해야 합니다. 이것은 이벤트가 있고 청사진이 가능한 "인스턴스화되지 않은 능력 개체"여야 합니다. UGameplayAbility는 "실행당 인스턴스" 개체여야 합니다. 실제로 인스턴스화해야 하는 경우 선택 사항일 수 있습니다. 대신 "인스턴스되지 않은" 능력이 새 UGameplayAbilitySpec 개체를 통해 구현됩니다.
> * 시스템은 "필터링된 GE 응용 프로그램 컨테이너"(더 높은 수준의 게임 플레이 로직을 가진 행위자에게 적용할 GE 데이터 드라이브), "중복 볼륨 지원"("필터링된 GE 응용 프로그램 컨테이너 적용 " 충돌 프리미티브 중첩 이벤트를 기반으로 함) 등이 있습니다. 이는 모든 프로젝트가 고유한 방식으로 구현하게 되는 빌딩 블록입니다. 그것들을 바로 잡는 것은 사소한 일이 아니므로 몇 가지 기본 구현을 제공하는 데 더 나은 일을 해야 한다고 생각합니다.
> * 일반적으로 프로젝트를 시작하고 실행하는 데 필요한 상용구를 줄입니다. 별도의 모듈 "Ex 라이브러리" 또는 기본적으로 기본 히트스캔 무기나 패시브 능력과 같은 것을 제공할 수 있는 모듈일 수 있습니다. 이 모듈은 선택 사항이지만 신속하게 시작하고 실행할 수 있습니다.
> * GameplayCues를 능력 시스템과 연결되지 않은 별도의 모듈로 옮기고 싶습니다. 여기에서 개선할 수 있는 부분이 많다고 생각합니다.


> 이것은 제 개인적인 생각일 뿐 누구의 공약도 아닙니다. 가장 현실적인 조치는 새로운 엔진 기술 이니셔티브가 진행되고 능력 시스템이 업데이트되어야 하고 그 때가 이런 종류의 작업을 수행할 때일 것입니다. 이러한 이니셔티브는 스크립팅, 네트워킹 또는 물리학/캐릭터 이동과 관련될 수 있습니다. 이것은 모두 매우 먼 미래를 내다보고 있기 때문에 일정에 대한 약속이나 추정을 할 수 없습니다.

**⬆ 맨 위로 돌아가기**

<a name="resources-daveratti-community2"></a>
#### 11.1.2 커뮤니티 질문 2
Dave Ratti와의 커뮤니티 회원 iniside의 Q&A:

1. 분리된 고정 틱에 대한 지원이 계획되어 있습니까? 나는하고 싶어요
게임 스레드를 고정하고(예: 30/60fps) 렌더링 스레드를
미친듯이 달려라. 나는 이것이 우리가 미래에 기대해야 하는 것인지 아니면
게임 플레이가 어떻게 작동해야 하는지에 대한 몇 가지 가정을 하기 위해 아닙니다.
물리학에 대한 고정된 비동기 틱이 있기 때문에 주로 묻습니다.
이것은 시스템의 나머지 부분이 어떻게 작동하는지에 대한 질문을 제기합니다.
미래. 나는 고정 틱 게임을 할 수있는 능력이 있다는 것을 숨기지 않습니다
엔진의 나머지 부분의 틱 속도도 고정하지 않은 스레드는
이상해.

> 렌더링 프레임 속도와 게임 스레드 틱 프레임 속도를 분리할 계획은 없습니다. 이러한 시스템의 복잡성과 이전 엔진 버전과의 역호환성을 유지해야 하는 요구 사항으로 인해 우주선이 계속해서 발생하고 있다고 생각합니다.
>
> 대신, 우리가 가고 있는 방향은 게임 스레드와 독립적으로 고정된 틱 속도로 실행되는 비동기식 "물리 스레드"를 사용하는 것입니다. 고정된 속도로 실행되어야 하는 것들이 여기에서 실행될 수 있으며 게임 스레드/렌더링은 항상 있는 방식으로 작동할 수 있습니다.
>
> Network Prediction이 Independent Ticking 및 Fixed Ticking 모드라고 하는 것을 지원한다는 점을 명확히 하는 것이 좋습니다. 내 장기 계획은 Independent Ticking을 현재 네트워크 예측에서 대략적으로 유지하는 것입니다. 여기에서는 게임 스레드에서 가변 프레임 속도로 실행되고 "그룹/세계" 예측이 없으며 고전적인 "클라이언트가 자신의 폰을 예측하고 소유 배우" 모델. 그리고 Fixed Ticking은 비동기 물리를 사용하는 것으로 물리 개체 및 기타 클라이언트/폰/차량/등과 같은 클라이언트 제어/소유가 아닌 액터를 예측할 수 있습니다.


2. Network Prediction의 통합에 대한 계획이 있습니까?
능력 시스템으로 봐? 예를 들어 고정 프레임 기능
활성화(그래서 서버는 능력이 있었던 프레임을 얻습니다.
활성화되고 예측 키 대신 실행되는 작업) ?

> 예, 계획은 능력 시스템의 예측 키를 다시 작성/제거하고 네트워크 예측 구성으로 교체하는 것입니다. NetworkPredictionExtras의 MockAbility 예제는 이것이 어떻게 작동하는지 보여주지만 GAS에서 요구하는 것보다 더 "하드 코딩"됩니다.
>
> 주요 아이디어는 ASC의 RPC에서 명시적 클라이언트->서버 예측 키 교환을 제거하는 것입니다. 더 이상 예측 창이나 범위가 지정된 예측 키가 없습니다. 대신 모든 것이 NetworkPrediction 프레임 주위에 고정됩니다. 중요한 것은 클라이언트와 서버가 일이 발생할 때 동의한다는 것입니다. 예는 다음과 같습니다.
>
> * 능력이 활성화/종료/취소되었을 때
> * 게임 플레이 효과 적용/제거 시
> * 속성 값(속성 값이 프레임 X에 있었던 것)
>
> 일반적으로 능력 시스템 수준에서 수행할 수 있다고 생각합니다. 그러나 실제로 UGameplayAbility 내부의 사용자 정의 로직을 완전히 롤백 가능하게 만드는 것은 여전히 더 많은 작업이 필요합니다. 완전히 롤백 가능하고 보다 제한된 기능 세트에 액세스하거나 롤백 친화적으로 표시된 능력 작업에만 액세스할 수 있는 UGameplayAbility 서브클래스를 갖게 될 수 있습니다. 그런 것. 또한 애니메이션 이벤트와 루트 모션 및 이러한 이벤트가 처리되는 방식에도 많은 영향이 있습니다.
>
> 좀 더 명확한 답변을 드리고 싶지만 GAS를 다시 만지기 직전에 기초를 다지는 것이 정말 중요합니다. 더 높은 수준의 시스템이 변경되기 전에 움직임과 물리학이 견고해야 합니다.


3. Network Prediction 개발을
본점? 거짓말이 아니라 최신 코드를 확인하고 싶습니다.
그 상태에 관계없이.

> 우리는 그것을 위해 노력하고 있습니다. 시스템 작업은 여전히 모두 NetworkPrediction에서 수행되고(NetworkPhysics.h 참조) 기본 비동기 물리 항목은 모두 사용 가능해야 합니다(RewindData.h 등). 그러나 Fortnite에는 분명히 공개할 수 없는 사용 사례가 있습니다. 버그, 성능 최적화 등을 통해 작업하고 있습니다.
>
> 자세한 내용: 이 시스템의 초기 버전에서 작업할 때 우리는 사물의 "프론트 엔드", 즉 상태와 시뮬레이션이 정의되고 작성되는 방식에 매우 집중했습니다. 우리는 그곳에서 많은 것을 배웠습니다. 그러나 비동기 물리학이 온라인에 등장하면서 우리는 초기 추상화 중 일부를 버리는 대신 이 시스템에서 실제로 작동하도록 하는 데 훨씬 더 집중했습니다. 여기의 목표는 진짜가 작동하고 사물을 통일할 때 다시 원을 그리는 것입니다. 예를 들어 "프론트 엔드"로 돌아가서 현재 작업 중인 기술의 핵심 부분 위에 최종 버전을 만듭니다.


4. 한동안 메인에 게임플레이를 보내기 위한 플러그인이 있었습니다.
메시지(이벤트/메시지 버스처럼 보임)가 제거되었습니다. 어느
복원할 계획입니까? 게임 기능/모듈식 게임 플레이 플러그인을 사용하면
일반 이벤트 버스 디스패처가 있으면 매우 유용합니다.

> GameplayMessages 플러그인을 말씀하시는 것 같습니다. 이것은 아마도 어느 시점에 돌아올 것입니다. API는 아직 확정되지 않았으며 작성자는 아직 공개할 의도가 없었습니다. 모듈식 게임플레이 디자인에 유용해야 한다는 데 동의합니다. 하지만 제 영역이 아니기 때문에 더 많은 정보가 없습니다.


5. 최근에 비동기 고정 물리학과 결과를 가지고 놀았습니다.
장래에 NP 업데이트가 있을 예정이라면
나는 아마 그냥 놀고 기다릴 것입니다.
여전히 전체 엔진을 고정 틱으로 전환해야 하고 다른 한편으로는
나는 물리학을 33ms로 유지하려고 노력합니다. 좋은 것을 만들지 않는 것
모든 것이 30fps(:.

Async에 대한 작업이 있음을 확인했습니다.
CharacterMovementComponent, 그러나 이것이 네트워크를 사용할 것인지 확실하지 않음
예측인가, 아니면 별도의 노력인가?

그걸 눈치채고 나도 가서 내 커스텀을 구현해 봤다.
고정된 틱 속도로 비동기 이동, 제대로 작동했지만 그 위에
또한 보간을 위해 별도의 업데이트를 추가해야 했습니다. 설정
고정 33ms에서 별도의 작업자 스레드에서 시뮬레이션 틱을 실행하는 것이었습니다.
업데이트, 계산 수행, 결과 저장 및 게임에서 보간
스레드가 현재 프레임 속도와 일치하도록 합니다. 완벽하지는 않지만 작업을 수행했습니다.
완료.

내 질문은 이것이 설정하기 더 쉬운 것이라면
앞으로 작성해야 할 상용구 코드가 상당히 많기 때문에
(보간 부분) 특히 효율적이지 않습니다.
각 움직이는 개체를 개별적으로 보간합니다.

비동기 항목은 정말 흥미롭습니다.
실제로 고정 업데이트 속도로 게임 시뮬레이션을 실행합니다.
고정 스레드가 필요하지 않음) 더 예측 가능한 결과를 얻을 수 있습니다. 이거야?
앞으로 의도된 것, 또는 더 많은 이점이 있는 것
시스템 선택 ? 내가 기억하는 한 액터 변환은 업데이트되지 않습니다.
async 및 블루프린트는 완전히 스레드로부터 안전하지 않습니다. 다시 말해
더 많은 프레임워크에서 지원될 계획입니다.
레벨 또는 각 게임이 자체적으로 해결해야 하는 것?

> 비동기 CharacterMovementComponent
>
> 이것은 기본적으로 CMC를 물리 스레드에 그대로 이식하는 초기 프로토타입/실험입니다. 아직 CMC의 미래로 보지는 않지만 그렇게 발전할 수 있습니다. 지금은 네트워킹 지원이 없기 때문에 내가 정말로 따르고 싶은 것이 아닙니다. 이를 수행하는 사람들은 대부분 이 시스템이 추가할 입력 대기 시간과 이를 완화할 수 있는 방법을 측정하는 데 관심이 있습니다.
>
> 여전히 전체 엔진을 고정된 틱으로 전환해야 하며 다른 한편으로는 물리학을 33ms로 유지하려고 합니다. 모든 것이 30fps(:.
>
> 비동기 항목은 정말 흥미롭습니다. 고정된 업데이트 속도로 게임 시뮬레이션을 실행할 수 있기 때문입니다(고정 스레드가 필요하지 않음)
>
> 네. 여기서 목표는 비동기 물리를 활성화하면 엔진을 가변 틱 속도로 실행할 수 있는 반면 물리 및 "코어" 게임플레이 시뮬레이션은 고정 속도로 실행할 수 있다는 것입니다(예: 캐릭터 이동, 차량, GAS 등).
>
> 다음은 지금 활성화하기 위해 설정해야 하는 cvar입니다.
> `p.DefaultAsyncDt=0.03333`
> `p.RewindCaptureNumFrames=64`
>
> Chaos는 물리 상태에 대한 보간을 제공합니다(예: UPrimitiveComponent로 다시 푸시되고 게임 코드에서 볼 수 있는 변환). 이제 cvar인 `p.AsyncInterpolationMultiplier`가 있으며, 보고 싶은 경우 이를 제어합니다. 추가 코드를 작성하지 않고도 물리 바디의 부드럽고 연속적인 움직임을 볼 수 있습니다.
>
> 비물리적 상태를 보간하려면 지금 당장 수행해야 합니다. 예는 비동기 물리 스레드에서 업데이트(체크)하고 싶지만 게임 스레드에서 부드러운 연속 보간을 확인하여 모든 렌더 프레임이 쿨다운 시각화가 업데이트되도록 하려는 쿨다운과 같습니다. 우리는 결국 이것에 도달할 것이지만 아직 예가 없습니다.
>
> 작성할 상용구 코드가 상당히 많습니다.
>
> 네, 그래서 지금까지 시스템의 큰 문제였습니다. 우리는 숙련된 프로그래머가 성능과 안전성을 극대화하는 데 사용할 수 있는 인터페이스를 제공하고자 합니다. 따라서 CharacterMoverment와 같은 것은 성능을 최대화하기 위해 많은 사용자 지정 작업을 수행할 수 있습니다. 예를 들어 템플릿 코드 작성 및 일괄 업데이트 수행, 확장, 업데이트 루프를 개별 단계로 나누기 등입니다. 우리는 좋은 "낮은 수준" 인터페이스를 이 사용 사례에 대한 비동기 스레드 및 롤백 시스템. 그리고 이 경우에도 - 캐릭터 이동 시스템 자체가 자체 방식으로 확장 가능한 것은 여전히 합리적입니다.
>
> 하지만 자체 "시스템"이 실제로 필요하지 않은 단순한 게임플레이 개체에는 이것이 허용되지 않는다는 것을 알고 있습니다. Unreal과 더 관련이 있는 것이 필요한 것입니다. 예를 들어 리플렉션 시스템 사용, 일반 청사진 지원 등. 다른 스레드에서 청사진이 사용되는 예가 있습니다(BlueprintThreadSafe 키워드 및 애니메이션 시스템이 작업 중인 대상 참조). 그래서 언젠가는 이런 형태가 있을 것이라고 생각합니다. 그러나 다시, 우리는 아직 거기에 있지 않습니다.
>
> 보간법에 대해 질문하신 것으로 알고 있지만 이것이 일반적인 답변입니다. 지금은 NetSerialize, ShouldReconcile, Interpolate 등과 같은 모든 작업을 수동으로 수행해야 하지만 결국 "만약 당신이 그냥 사용하고 싶다면 리플렉션 시스템을 사용하면 이 항목을 수동으로 작성할 필요가 없습니다." 모든 사람이 리플렉션 시스템을 사용하도록 *강요*하고 싶지는 않습니다. 리플렉션 시스템이 시스템의 가장 낮은 수준을 사용하고 싶지 않다고 생각하는 다른 제한 사항을 부과하기 때문입니다.
>
> 그리고 이것을 내가 앞서 말한 것과 다시 연결하기 위해 - 지금 우리는 몇 가지 매우 구체적인 예제가 작동하고 성능을 발휘하도록 하는 데 집중하고 있습니다. 그런 다음 다시 프런트 엔드로 관심을 돌려 사용하기 쉽고 반복할 수 있도록 만들 것입니다. , 다른 모든 사람들이 사용할 수 있도록 상용구 줄이기 등.


**[⬆ Back to Top](#table-of-contents)**

<a name="changelog"></a>
## 12. GAS Changelog

This is a list of notable changes (fixes, changes, and new features) to GAS compiled from the official Unreal Engine upgrade changelog and from undocumented changes that I've encountered. If you've found something that isn't listed here, please make an issue or pull request.

<a name="changelog-4.27"></a>
### 4.27
* Crash Fix: Fixed a root motion source issue where a networked client could crash when an Actor finishes executing an ability that uses a constant force root motion task with a strength-over-time modifier.
* Bug Fix: Fixed a regression in Editor loading time when using GameplayCues.
* Bug Fix: GameplayEffectsContainer's `SetActiveGameplayEffectLevel` method will no longer dirty FastArray if setting the same EffectLevel.
* Bug Fix: Fixed an edge case in GameplayEffect mixed replication mode where Actors not explicitly owned by the net connection but who utilize that connection from `GetNetConnection` will not received mixed replication updates.
* Bug Fix: Fixed an endless recursion occuring in GameplayAbility's class method `EndAbility` which was called by calling `EndAbility` again from `K2_OnEndAbility`.
* Bug Fix: GameplayTags Blueprint pins will no longer be silently cleared if they are loaded before tags are registered. They now work the same as GameplayTag variables, and the behavior for both can be changed with the ClearInvalidTags option in the Project Settings.
* Bug Fix: Improved thread safety of GameplayTag operations.
* New: Exposed SourceObject to GameplayAbility's `K2_CanActivateAbility` method.
* New: Native GameplayTags. Introducing a new `FNativeGameplayTag`, these make it possible to do one off native tags that are correctly registered and unregistered when the module is loaded and unloaded.
* New: Added new method `GrantAndActivateAbilityOnSelfWithParams` which allows Designers to pass in FGameplayEventData when granting and then activating an ability from Blueprint.
* New: Improved ScalableFloats in the GameplayAbilities plugin to support dynamic lookup of curve tables from the new Data Registry System. Added a ScalableFloat header for easier reuse of the generic struct outside the abilities plugin.
* New: Added code support for using the GameplayTag UI in other Editor customizations via GameplayTagsEditorModule.
* New: Modified UGameplayAbility's PreActivate method to optionally take in trigger event data.
* New: Added more support to filter GameplayTags in the Editor using a project-specific filter. `OnFilterGameplayTag` supplies the referencing property and the tag source, so you can filter tags based on what asset is requesting the tag.
* New: Added option to preserve the original captured SourceTags when GameplayEffectSpec's class method `SetContext` is called after initialization.
* New: Improved UI for registering GameplayTags from specific plugins. The new tag UI now lets you select a plugin location on disk for newly added GameplayTag sources.
* New: A new track has been added to Sequencer to allow for triggering notify states on Actors built using the GameplayAbiltiySystem. Like notifies, the GameplayCueTrack can utilize range-based events or trigger-based events.
* Change: Changed the GameplayCueInterface to pass GameplayCueParameters struct by reference.
* Optimization: Made several performance improvements to loading and regenerating the GameplayTag table were implemented so that this option would be optimized.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_27/

<a name="changelog-4.26"></a>
### 4.26
* GAS plugin is no longer flagged as beta.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Added the path string arg to a message to fix a crash in UGameplayCueManager::VerifyNotifyAssetIsInValidPath.
* Crash Fix: Fixed an access violation crash in AbilitySystemComponent_Abilities when using a ptr without checking it.
* Bug Fix: Fixed a bug where stacking GEs that did not reset the duration on additional instances of the effect being applied.
* Bug Fix: Fixed an issue that caused CancelAllAbilities to only cancel non-instanced abilities.
* New: Added optional tag parameters to gameplay ability commit functions.
* New: Added StartTimeSeconds to PlayMontageAndWait ability task and improved comments.
* New: Added tag container "DynamicAbilityTags" to FGameplayAbilitySpec. These are optional ability tags that are replicated with the spec. They are also captured as source tags by applied gameplay effects.
* New: GameplayAbility IsLocallyControlled and HasAuthority functions are now callable from Blueprint.
* New: Visual logger will now only collect and store info about instant GEs if we're currently recording visual logging data.
* New: Added support for redirectors on gameplay attribute pins in blueprint nodes.
* New: Added new functionality for when root motion movement related ability tasks end they will return the movement component's movement mode to the movement mode it was in before the task started.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_26/

<a name="changelog-4.25.1"></a>
### 4.25.1
* Fixed! UE-92787 Crash saving blueprint with a Get Float Attribute node and the attribute pin is set inline
* Fixed! UE-92810 Crash spawning actor with instance editable gameplay tag property that was changed inline

<a name="changelog-4.25"></a>
### 4.25
* Fixed prediction of `RootMotionSource` `AbilityTasks`
* [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes) now additionally takes in the old `Attribute` value. We must supply that as the optional parameter to our `OnRep` functions. Previously, it was reading the attribute value to try to get the old value. However, if called from a replication function, the old value had already been discarded before reaching SetBaseAttributeValueFromReplication so we'd get the new value instead.
* Added [`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy) to `UGameplayAbility`.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Removed a few ways for attackers to crash a server through the ability system.
* Crash Fix: We now make sure we have a GameplayEffect definition before checking tag requirements.
* Bug Fix: Fixed an issue with gameplay tag categories not applying to function parameters in Blueprints if they were part of a function terminator node.
* Bug Fix: Fixed an issue with gameplay effects' tags not being replicated with multiple viewports.
* Bug Fix: Fixed a bug where a gameplay ability spec could be invalidated by the InternalTryActivateAbility function while looping through triggered abilities.
* Bug Fix: Changed how we handle updating gameplay tags inside of tag count containers. When deferring the update of parent tags while removing gameplay tags, we will now call the change-related delegates after the parent tags have updated. This ensures that the tag table is in a consistent state when the delegates broadcast.
* Bug Fix: We now make a copy of the spawned target actor array before iterating over it inside when confirming targets because some callbacks may modify the array.
* Bug Fix: Fixed a bug where stacking GameplayEffects that did not reset the duration on additional instances of the effect being applied and with set by caller durations would only have the duration correctly set for the first instance on the stack. All other GE specs in the stack would have a duration of 1 second. Added automation tests to detect this case.
* Bug Fix: Fixed a bug that could occur if handling gameplay event delegates modified the list of gameplay event delegates.
* Bug Fix: Fixed a bug causing GiveAbilityAndActivateOnce to behave inconsistently.
* Bug Fix: Reordered some operations inside FGameplayEffectSpec::Initialize to deal with a potential ordering dependency.
* New: UGameplayAbility now has an OnRemoveAbility function. It follows the same pattern as OnGiveAbility and is only called on the primary instance of the ability or the class default object.
* New: When displaying blocked ability tags, the debug text now includes the total number of blocked tags.
* New: Renamed UAbilitySystemComponent::InternalServerTryActiveAbility to UAbilitySystemComponent::InternalServerTryActivateAbility.Code that was calling InternalServerTryActiveAbility should now call InternalServerTryActivateAbility.
* New: Continue to use the filter text for displaying gameplay tags when a tag is added or deleted. The previous behavior cleared the filter.
* New: Don't reset the tag source when we add a new tag in the editor.
* New: Added the ability to query an ability system component for all active gameplay effects that have a specified set of tags. The new function is called GetActiveEffectsWithAllTags and can be accessed through code or blueprints.
* New: When root motion movement related ability tasks end they now return the movement component's movement mode to the movement mode it was in before the task started.
* New: Made SpawnedAttributes transient so it won't save data that can become stale and incorrect. Added null checks to prevent any currently saved stale data from propagating. This prevents problems related to bad data getting stored in SpawnedAttributes.
* API Change: AddDefaultSubobjectSet has been deprecated. AddAttributeSetSubobject should be used instead.
* New: Gameplay Abilities can now specify the Anim Instance on which to play a montage.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_25/

<a name="changelog-4.24"></a>
### 4.24
* Fixed blueprint node `Attribute` variables resetting to `None` on compile.
* Need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) to use [`TargetData`](#concepts-targeting-data) otherwise you will get `ScriptStructCache` errors and clients will be disconnected from the server. My advice is to always call this in every project now whereas before 4.24 it was optional.
* Fixed crash when copying a `GameplayTag` setter to a blueprint that didn't have the variable previously defined.
* `UGameplayAbility::MontageStop()` function now properly uses the `OverrideBlendOutTime` parameter.
* Fixed `GameplayTag` query variables on components not being modified when edited.
* Added the ability for `GameplayEffectExecutionCalculations` to support scoped modifiers against "temporary variables" that aren't required to be backed by an attribute capture.
   * Implementation basically enables `GameplayTag`-identified aggregators to be created as a means for an execution to expose a temporary value to be manipulated with scoped modifiers; you can now build formulas that want manipulatable values that don't need to be captured from a source or target.
   * To use, an execution has to add a tag to the new member variable `ValidTransientAggregatorIdentifiers`; those tags will show up in the calculation modifier array of scoped mods at the bottom, marked as temporary variables—with updated details customizations accordingly to support feature
* Added restricted tag quality-of-life improvements. Removed the default option for restricted `GameplayTag` source. We no longer reset the source when adding restricted tags to make it easier to add several in a row. 
* `APawn::PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`. Useful because [Mixed Replication Mode](#concepts-asc-rm) expects the owner of the `Pawn` to be the `Controller` if the `ASC` lives on the `Pawn`.
* Fixed bug with POD (Plain Old Data) in `FAttributeSetInitterDiscreteLevels`.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_24/

**[⬆ Back to Top](#table-of-contents)**
