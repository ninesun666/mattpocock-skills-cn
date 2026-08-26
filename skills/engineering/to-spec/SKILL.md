---
name: to-spec
description: "Turn the current conversation into a spec and publish it to the project issue tracker: no interview, just synthesis of what you've already discussed."
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a spec. Do NOT interview the user; just synthesize what you already know.

The issue tracker and triage label vocabulary should have been provided to you. If not, tell the user to run `/setup-matt-pocock-skills`.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the spec, and respect any ADRs in the area you're touching.

2. Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one.

Check with the user that these seams match their expectations.

3. Write the spec using the template below, then publish it to the project issue tracker. Apply the `ready-for-agent` triage label - no need for additional triage.

<spec-template>

## 问题陈述

用户正面临的问题,从用户视角描述。

## 解决方案

问题的解决方案,从用户视角描述。

## 用户故事

一份很长、带编号的用户故事列表。每条用户故事的格式为:

1. 作为<角色>,我希望<功能>,以便<价值>

<user-story-example>
1. 作为手机银行客户,我希望看到账户余额,以便更好地决定自己的消费
</user-story-example>

这份用户故事列表应当极其详尽,覆盖功能的各个方面。

## 实现决策

已确定的实现决策列表,可以包括:

- 将要构建/修改的模块
- 这些模块中将被修改的接口
- 来自开发者的技术澄清
- 架构决策
- Schema 变更
- API 契约
- 具体的交互方式

不要包含具体的文件路径或代码片段,它们很快就会过时。

例外:如果原型产出过某个比文字更精确地编码了决策的片段(状态机、reducer、schema、类型形状),将其内联到相关决策中,并简要注明来自原型。裁剪到决策密集的部分,不是可运行的演示,只要关键的 bits。

## 测试决策

已确定的测试决策列表,包括:

- 什么才是好测试的描述(只测外部行为,不测实现细节)
- 哪些模块将被测试
- 测试的先例(即代码库中类似类型的测试)

## 范围外

本规格明确排除在范围之外的内容。

## 补充说明

关于该功能的任何其他说明。

</spec-template>
