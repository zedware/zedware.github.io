---
layout: post
title: The Vibe Coding Revolution
tags: [Vibe Coding, Claude Code, Google Gemini]
---

本文中除了这段话之外的所有内容都由 Google Gemini 产生；最近写的代码，包括 Tokenizer 等的绝大部份也都是由 Claude Code + LLM 完成。难道人类的文章写作和代码写作都将要成为古董或艺术或自娱自乐？

## The Vibe Coding Revolution: Developing the Tokenizer

This project exemplifies the modern **“vibe coding”** era—where the distance between a concept and a deployed application is bridged by AI agents and seamless cloud infrastructure.

该项目的开发历程代表了范式的转变，进入了一个全新的时代：**“氛围编程”（Vibe Coding）**——即通过 AI 智能体和无缝云基础设施，极大地缩短从构思到上线之间的距离。

---

### 1. The Engine of Creation: Claude Code
### 核心创造引擎：Claude Code

The core of this project’s development was driven by **Claude Code**, Anthropic’s high-agency command-line tool. Unlike traditional chat-based AI assistants, Claude Code operates directly within the developer's terminal. In the case of the tokenizer-webapp, the developer acted as a "Product Manager," describing the desired "vibe"—a fast, clean interface that visualizes how LLMs break down text into tokens—while Claude handled the implementation.

该项目的开发核心是由 **Claude Code** 驱动的。与传统的聊天式 AI 助手不同，Claude Code 直接在开发者的终端中运行。在开发过程中，开发者更像是一位“产品经理”，负责描述预期的“氛围”——即一个简洁、快速、能直观展示大模型如何将文本拆分为 Token 的界面——而 Claude 则负责处理具体的代码实现。

Because the app involves specific libraries like `tiktoken` or Hugging Face’s `transformers`, the development benefited from Claude Code’s ability to "think" in terminal commands—initializing Next.js projects, installing npm packages, and troubleshooting environment quirks. This loop of high-level intent followed by agent execution is the hallmark of the vibe coding movement.

由于该应用涉及特定领域的库（如 `tiktoken` 或 Hugging Face 的 `transformers`），开发过程充分利用了 Claude Code 的终端操作能力。它能自动初始化 Next.js 项目、安装必要的 npm 包，并解决技术挑战。这种由开发者提供意图、智能体负责执行的迭代闭环，正是“氛围编程”的核心特征。

---

### 2. Infrastructure as an Afterthought: Railway
### 被简化到极致的基础设施：Railway

A key bottleneck in vibe coding is the "Now what?" moment—moving code from a local machine to the internet. For this project, **Railway** served as the frictionless bridge to production. By linking the GitHub repository (`zedware/tokenizer_webapp`) to Railway, the deployment was automated.

对于“氛围编程”而言，主要的瓶颈往往在于如何将本地代码发布到互联网上。在这个项目中，**Railway** 成为了通往生产环境的顺畅桥梁。通过将 GitHub 仓库与 Railway 项目关联，部署过程实现了完全自动化。

The developer could even prompt Claude Code to "deploy to Railway," triggering the AI to use the Railway CLI to provision the environment, set up internal networking (like `tokenizer-webapp.railway.internal`), and generate the public domain at `tokenizerwebapp-production.up.railway.app`.

在工作流中，开发者甚至可以直接要求 Claude Code “将其部署到 Railway”，从而触发 AI 调用 Railway CLI 来配置环境、设置内部网络并生成面向公众的域名。

---

### 3. The Result: A Production-Ready Utility
### 最终成果：生产级的实用工具

The resulting application is a functional tool providing immediate value, allowing users to see a real-time breakdown of text into IDs and tokens—a crucial task for LLM developers. It demonstrates that the barriers to entry for creating specialized tools are collapsing.

最终生成的应用是一个具有实际功能的工具，用户访问时可以实时看到文本被拆解为 ID 和 Token 的过程。这证明了创建专业工具的门槛正在迅速降低。

When you combine **Claude Code’s linguistic reasoning** with **Railway’s operational simplicity**, the focus shifts from "How do I build this?" to "What is the best way to solve this problem?" The tokenizer-webapp is a testament to this new era of high-velocity, intent-driven development.

当 **Claude Code 的语言推理能力**与 **Railway 的运维简易性**相结合时，开发的重心便从“我该如何构建它？”转移到了“解决这个问题的最佳方案是什么？”。tokenizer-webapp 正是这一高效、意图驱动的开发新时代的有力见证。
