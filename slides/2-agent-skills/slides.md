---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Agent Skills
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
author: Utopia
# open graph
# seoMeta:
#  ogImage: https://cover.sli.dev
routerMode: hash
---

# 关于 Agent Skills 的分享

<div class="pt-10 mx-auto text-center leading-36px">
  <div class="inline-block text-left">
    <div>
      分享：吴展华
    </div>
    <div>
      部门：用户体验设计部
    </div>
    <div>
      岗位：软件开发（前端）
    </div>
  </div>
</div>
<img src="https://utopia1994.oss-cn-shanghai.aliyuncs.com/img-bed/202307022058660.png" class="w-160px fixed top-4 right-4" />

<style>
h1 {
  color: #fff;
  background-color: none;
  background-image: none;
}
</style>

---

# 简单了解 Agent Skills

如果你经常使用AI助手，一定遇到过这样的困扰：每次开始新对话，都要重复输入相同的工作流程说明；好不容易把 AI调教到满意的状态，换个对话窗口又要从头来过。
Skills 正是为解决这个痛点而生的。

2025年10月16日，Anthropic 发布了 Claude Code 的一项重大更新 Agent Skills，它允许用户将专业知识、脚本和资源打包成模块化的“技能文件夹”（Skill folders），让 AI 能在特定工作场景中更专业地执行任务。

它把专业知识、工作流程和最佳实践打包起来，让 Claude Code 能够自动调用。

Skills 类似于为 AI 助手提供的"专业培训"。通过 Skill，您可以：

- **封装专业知识**：将特定领域的最佳实践和操作流程封装成可复用的技能
- **提供工作流模板**：定义标准化的任务处理流程，提高工作效率
- **扩展 AI 能力**：让 AI 助手能够处理更专业、更复杂的任务
- **团队协作共享**：项目级 Skills 可以在团队成员间共享专业知识

---

# Agent Skills 现状

<div />

2025-12-18，Anthropic 已为 Agent Skills 定义了[开放标准](https://agentskills.io/home)，托管在 [agentskills.io](https://agentskills.io/home) 上。类似 MCP 的标准化路径，这意味着其他 AI coding agent 也可以使用 Agent Skills，并且社区贡献，复用会更加方便。

目前，cursor(beta)， gemini cli， vs code copilot， codex， CodeBuddy(腾讯)， opencode 已跟进支持。 相信国内的 AI coding agent 后期也会像支持 MCP 一样跟进支持 Agent Skills。

如果你目前使用的 AI coding agent 暂不支持 Skills，通过安装 [OpenSkills](https://github.com/numman-ali/openskills) 也可以使用 Agent Skills。

---

# 为什么需要 Agent Skills？

---

# 如何创建 Agent Skills？

---

## 目录结构

```
my-skill/
├── SKILL.md          # 必需: 元数据 + 指令
├── scripts/          # 可选: 可执行代码，AI 直接运行的固定脚本程序（脚本代码本身不进 Context Window，只有脚本运行完成后的输出结果会进 Agent 的 Context。）
├── references/       # 可选: 参考文档，给 AI 看的技术规范，API 文档，专业指南
└── assets/           # 可选: 素材资源，拿来用的，会被复制，修改，如图片，excel 模板
```

---

# 示例：pdf 文件处理

<div />

**SKILL.md 文件内容**

```md
---
name: pdf-processing
description: Extract text, fill forms, merge PDFs. Use when working with PDF files, forms, or document extraction.
---

## Quick start

Extract text:

import pdfplumber
with pdfplumber.open("doc.pdf") as pdf:
    text = pdf.pages[0].extract_text()


For form filling, see [FORMS.md](references/FORMS.md).
For detailed API reference, see [REFERENCE.md](references/REFERENCE.md).

## Requirements

Packages must be installed in your environment:

pip install pypdf pdfplumber

```

---

**Frontmatter 字段**

| 字段            | 必填 | 说明                            | 示例                          |
| :-------------- | :--- | :------------------------------ | :---------------------------- |
| `name`          | 是   | Skill 名称,未指定时使用目录名   | `pdf`                         |
| `description`   | 是   | Skill 描述,帮助 AI 理解何时使用，要求简短明了 | `PDF 文档处理专家` |

<div class="mt-10" />

**建议使用 skill-creator skill 创建技能**

[skill-creator skill](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md) 可以指导你创建结构清晰的技能。它会提出一些澄清问题，建议改进描述，并帮助您正确地格式化说明。

该工具可在 GitHub 上的技能代码库中找到，对于您最初创建的几个技能尤其有用。

---

# 渐进式披露机制

为了提高 Claude 的效率，Agent Skills 采用[渐进式披露机制 ](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)。在处理任务时，Claude 首先扫描 Skills 的元数据（描述和摘要）以识别相关匹配项。如果找到匹配的技能，Claude 会加载完整的指令（SKILL.md）。最后，如果技能包含可执行代码或引用文件，则仅在需要时才加载这些文件。

```md
Level 1: SKILL.md 元数据（name + description）

         ↓ 提示词匹配上 description

Level 2: SKILL.md 完整内容（指令）

         ↓ 当 Agent 认为需要加载额外资源时

Level 3: reference, assets, scripts 中的具体文件（按需读取）
```

---

# 核心优势

<div>
<!-- 占位 div, 不可删除 -->
</div>

**Token 效率**

Skills 的渐进式披露架构带来了极高的 token 效率。由于每个技能的元数据仅占上百个 token，你可以同时启用几十甚至上百个 Skills，完整技能按需加载，不会浪费富贵的上下文空间。

**可组合性**

多个 Skills 可以自动协同工作。当你提出一个复杂任务时，AI Agent 会智能识别需要调用哪些 SkillS，并协调它们一起完成任务。比如你说"根据这份销售数据生成季度报告"，AI Agent 可能会：

1. 调用数据分析 Skill 处理原始数据
2. 调用图表生成 Skill 创建可视化
3. 调用文档 Skill 生成最终报告

整个过程你不需要手动指定使用哪个 Skill，AI Agent 会根据任务需求自动选择和组合。

**可复用性**

1. skills 本质还是提示词工程，你可以将特定领域的最佳实践和操作流程封装成可复用的技能，减少重复提示词工程时间。
2. 你可以在团队中共享 Skills，减少重复劳动，提高团队效率。
3. 因为 skills 是标准化的，所以可以被其他 AI Agent 复用。

---

# 在前端开发中的应用 - 快速开发列表页面

---

![表格页面](https://utopia1994.oss-cn-shanghai.aliyuncs.com/img-bed/202601121426060.png)

---

作为前端开发工程师，经常需要开发列表页面。这是一个难度不高，但非常繁琐的任务，模板代码多，对接后端字段也耗时。所以我把这任务的最佳实践（自认为）定义成了一个 skills: table-page-dev, 大大提高了效率。

下面是我开发一个代理商列表页时的提示词：

```md
@src/views/agency-page 在这个目录下完成代理商列表页面的开发，表格的 UI 图片已上传

## 接口定义
import { postOpsAgencyPageList, deleteOpsAgency, updateOpsAgencyStatus } from '@vben/api/operation/opsAgencyController.ts'

列表接口：postOpsAgencyPageList
删除接口： deleteOpsAgency
更新状态接口： updateOpsAgencyStatus

## 表格列定义
操作列：编辑， 删除， 启用/禁用
rechargeNeedApprove 列: 枚举映射：Y: 需求, N: 不需要， 使用 ElTag 自定义 ui

## 搜索表单
除了分页参数，把列表接口的所有请求参数都配置到搜索表单上

```

Agent 会根据提示词中的关键信息『列表页面的开发』自动使用 skills - table-page-dev, 完成页面的开发和后端接口对接。后期我只需要 review 代码，对瑕疵微调就可以交付代码了。

---

> Q:  为什么不让 ai 直接生成代码，让他自由发挥？
>
> A:  AI 生成的代码质量和风格不稳定，让 AI 参考 skill 来完成任务，可以尽量的保证代码质量，同时生成的代码也是你熟悉的风格，方便后期的维护

---

# 一些主流的 Agent Skills

anthropics: https://github.com/anthropics/skills

openai: https://github.com/openai/skills

智普：https://github.com/AnswerZhao/agent-skills 并没有开源，[如何获取](https://mp.weixin.qq.com/s/bCPxerWFH_VUchqGXTK1tg)

---

# Anthropic Skills

<div />

**文档处理 Skills**

| 技能 | 描述 |
| :--- | :--- |
| DOCX | 全面的 Word 文档创建、编辑和分析 |
| PDF | PDF 文本提取、表单填写和文档转换 |
| PPTX | PowerPoint 演示文稿创建和设计 |
| XLSX | 电子表格操作和公式重新计算 |

---

**开发与技术 Skills**

| 技能 | 描述 |
| :--- | :--- |
| MCP Builder | 创建高质量 MCP (Model Context Protocol) 服务器的指南 |
| Webapp Testing | 使用 Playwright 进行本地 Web 应用程序测试 |
| Skill Creator | 用于创建有效技能的元技能 |

---

**创意与设计 Skills**

| 技能 | 描述 |
| :--- | :--- |
| Algorithmic Art | 使用 p5.js 创建生成式艺术 |
| Slack GIF Creator | 针对 Slack 限制的动画优化 |
| Theme Factory | 为工件应用专业主题 |
| Canvas Design | 画布式视觉设计，如海报、艺术品 |
| Frontend Design | 创建具有独特性、生产级且美观的前端界面 |
| Brand Guidelines | 品牌指南应用，一致的视觉标识实现 |

---

# 一些疑问

1. **Skills 与 Prompt：何时使用**

在以下情况下使用 Prompt：当你需要给出一次性指令、提供即时背景信息或进行对话式交流时。Prompt 是 reactive 的，也是短暂的。

在以下情况下使用 Skills：当你需要反复运用某些流程或专业知识时。Skills 可以大幅减少重复提示词工程时间。Skills 具有前瞻性—— Agent 知道何时按需运用它们——并且贯穿于整个对话过程中。

将它们结合起来使用：提示和技能相辅相成。先用技能奠定基础，再用提示为每个任务提供具体背景和细化指导。

---

2. **Skills vs MCP (Model Context Protocol)**

这是最重要的对比，也是社区讨论最多的话题。

**架构定位：**

MCP：连接外部系统的"桥梁"， 定义 Agent 如何以统一方式调用外部的工具、数据和服务，本身不定义任务逻辑或执行流程。（What - 提供什么数据/能力）

Skills：使用这些能力的"说明书"，教 Agent 如何完整处理特定工作（How - 如何使用）

**形象比喻：**

MCP = 给 Claude 配备工具箱（扳手、螺丝刀、锯子）

Skills = 教 Claude 如何使用这些工具（安装步骤、注意事项）

> 社区观点："Skills 可能比 MCP 更重要。MCP 存在 token 消耗过度的问题，而 Skills 优雅地避免了这一点。"

---

# 常见问题

Q: Skill 不被触发怎么办？
> A: 检查 description 是否包含相关关键词，尝试更明确地描述使用场景。

Q: Token 消耗太高？
> A: 将详细内容移到单独的参考文件，使用渐进式披露。