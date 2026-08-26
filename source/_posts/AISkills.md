---
title: AISkills
date: 2026-05-18 10:48:57
tags: 大模型
excerpt: 大模型
categories:
    - 大模型
---
# Claude Skills


这条指令确实大有来头。你看到的 `npx uipro-cli init --ai claude `是一个前端设计与 AI 结合的开源工具指令。

以下是它的 GitHub 项目链接、核心作用以及能为你干什么的详细解答：

## 1.GitHub 仓库链接
该指令属于开源项目 UI/UX Pro Max Skill。

GitHub 仓库地址：`nextlevelbuilder/ui-ux-pro-max-skill`

官方技能网站：`ui-ux-pro-max-skill.nextlevelbuilder.io`
## 2.这条指令是干什么的？

简单来说，这条指令是用来给你的 AI 编程助手（如 Claude Code、Cursor 等）“打补丁 / 装插件”的。

在 2026 年，AI 编程工具已经支持通过安装 “技能（Skills）” 或加载特定上下文来扩展能力。
当你运行这行命令时：

`npx uipro-cli init `会在你的本地项目根目录下初始化一个设计系统数据库和配置。

--ai claude 参数表示专门为 Claude 优化。它会把针对 Claude 的专属提示词、UI 规范、最佳实践指南直接注入到你的 AI 运行环境中（例如生成 .claude/skills/ 相关的配置或 CLAUDE.md 文件）。

运行之后，Claude 就会瞬间“附体”变成一位拥有几十年经验的资深 UI/UX 设计师。

# 3.安装之后，它能帮你干什么？
平时让 AI 写前端界面，它常常会写出“直男审美”或者配色诡异、动画生硬的代码。这个工具就是为了解决这个痛点，它内置了：

67 种现代 UI 风格：比如玻璃态（Glassmorphism）、极简主义、野兽派、Bento Grid（便当网格）、AI 原生 UI 等。

161 种行业调色板：针对 SaaS、金融科技、医疗、电商等不同行业提供专业配色。

56 种字体配对、25 种图表推荐 以及 99 条 UX 交互指南。

# 💡 核心使用场景：
自动生成专业设计系统：当你对 Claude 说 “帮我设计一个金融类 App 的仪表盘”，Claude 不再是瞎写，而是会调取这个工具的底层数据库，自动匹配金融行业的高级蓝、无衬线字体，并避开“大红大绿”等设计反模式。

支持 15+ 种前端技术栈：不管是 React、Next.js、Vue、Svelte、Tailwind CSS，还是移动端的 SwiftUI 和 Flutter，它都能让 Claude 吐出完全符合该技术栈规范的、漂亮且具备无障碍（A11y）支持的代码。

UI 审查（Audit）：可以让 Claude 检查你现有的前端代码，指出哪些地方设计不及格（例如：“此处阴影太硬，不符合现代审美，建议修改为...”）。


喂给他
```
请阅读并严格遵守 `.claude/skills/ui-ux-pro-max/SKILL.md` 中的设计规范与 UI/UX 原则。

我的项目是 Vue 3，请帮我编写一个浪漫的婚礼策划落地页组件。具体设计需求如下：

【核心设计需求】
Build a romantic wedding planning landing page with soft UI, portfolio gallery, vendor directory preview, planning tools showcase, and contact form. Use soft pink and gold accents.

【技术与设计细节要求】
1. 风格（Style）：结合技能书中的 Soft UI（软质UI/新拟物微调）风格，整体氛围要优雅、浪漫。
2. 配色（Color）：主色调使用柔和的粉色（Soft Pink）和高贵的金色（Gold Accents）点缀，注意保持文字的可读性和对比度。
3. 组件结构：
   - 导航栏（Navbar）
   - 首屏 Hero 区域（浪漫大气的文案 + 行动召唤按钮）
   - 作品集画廊（Portfolio Gallery）：展示过往婚礼案例
   - 服务商名录预览（Vendor Directory Preview）：卡片式布局
   - 策划工具展示（Planning Tools Showcase）：展示预算表、日程表等工具的 UI 概念
   - 联系表单（Contact Form）：优雅的输入框和提交按钮
4. 代码规范：请直接给出单文件组件（.vue）代码。如果项目中使用了 Tailwind CSS，请优先使用 Tailwind 类名来实现这些高阶审美样式。
```

# Rainbow CSV

这个是 VS Code（或者 Cursor）检测到你打开或者生成了一个叫 charts.csv 的表格数据文件，所以十分贴心地弹出来提醒你：“要不要装个插件来帮你把这个文件变得更好看？”

这个叫 "Rainbow CSV" 的扩展（插件）非常实用，我极其建议你点 【安装】。