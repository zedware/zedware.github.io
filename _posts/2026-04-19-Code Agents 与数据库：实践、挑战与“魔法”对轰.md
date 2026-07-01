---
layout: post
title: Code Agents 与数据库：实践、挑战与“魔法”对轰
original: https://mp.weixin.qq.com/s/sfa98QfAqUYMXx7EcN6I7g
tags: [Data and AI, Code Agents]
---

*Trust but verify.*

全世界的开发者已经用充分的实践证明，Code Agents（代码智能体）可以胜任各种软件开发任务，数据库系统这一皇冠上的明珠自然也不例外。我们在数据库领域对 Code Agents 进行了全方位的探索，在享受效率红利的同时，也积累了一系列深刻的经验与教训。我们的日常开发栈涵盖了 GitHub Copilot、Claude Code、Gemini CLI/Antigravity、Cursor 及 Codex 等主流工具。

从辅助阅读到内核开发

首先，即便配合推理能力一般的模型，Code Agents 也足以胜任代码阅读、基础功能修改及自动化代码提交。令人惊叹的是，在处理复杂的 C/C++ 算法、SIMD 指令优化等底层任务时，Code Agents 的理解与编写速度甚至超越了很多资深程序员，且准确性更高。对于许多中国程序员而言，Code Agents 撰写的英文注释和 Commit Messages 显然更加地道流畅。

其次，Code Agents 在数据库周边生态——如命令行工具、GUI 管理端及服务化功能的开发中表现突出。这些领域多采用 Java、Go、Rust 或 Python 等现代流行语言，模型训练数据充足，写起来更得心应手。

最后，在数据库内核开发（C/C++、Go、Rust）中，Code Agents 虽能参与，但也存在明显的短板。对于复杂的并发竞争场景、深度的系统级性能优化，或是那些缺少文档的陈旧闭源仓库，Code Agents 往往显得力不从心，难以自动化解决全部问题。

模型能力的“方差”与劣化

除了 Code Agents 框架本身的差异，底层大模型（LLM）的代差也十分显著。例如，GLM 4.x 系列在逻辑推理上明显逊于 5.x；而在代码生成的细腻度上，Google Gemini 很多时候不如 Anthropic 的 Opus 4.x。

受限于算力成本，各大平台的 Coding Plan 普遍存在各种限流措施。此外，一个比较明显的现象是：大模型新版本发布初期往往“惊为天人”，但随着用户量激增，效果便会出现明显的劣化（降智）。这背后或许是平台方为了节省成本，切换了更高程度量化的版本，或是采取了其他的推理剪枝策略。

人的价值：从编码者到定义者

在 Code Agents 与模型之外，人类工程师本身往往是被忽视的关键变量。实践表明，面对同样的任务，不同水平的工程师利用 AI 产出的结果天差地别。如果人类工程师说不清楚问题、没法验证修改结果，那么他在链路中最大的价值，恐怕只剩下作为“第一责任人”去承担系统故障带来的“背锅”重任了。

复刻 SQLite：生机勃勃的“幻觉”

SQLite ( https://github.com/sqlite/sqlite ) 极具代表性：它应用极广、代码精纯，且作为嵌入式数据库，其系统复杂度适中。近年来，围绕 SQLite 的 Fork 或重写项目层出不穷。从 Turso 早期基于 C 的 libSQL ( https://github.com/tursodatabase/libsql ) 改进，到 2024 年决定用 Rust 重写而生的 Limbo ( https://turso.tech/blog/introducing-limbo-a-complete-rewrite-of-sqlite-in-rust )，再到 2026 年在 Vibe Coding 浪潮下、借助 Claude Code 诞生的一系列 Rust 重写项目，如 FrankenSQLite ( https://github.com/Dicklesworthstone/frankensqlite ) 等，这种趋势愈演愈烈。

2026 年 3 月 Claude Code 源码的泄露引发了全球开发者的“重写狂欢”。尽管这些项目在 README 中列举了各种冠冕堂皇的理由，但我们必须清醒地认识到：随着 Code Agents 的普及，开源项目的复刻版本将呈指数级增长，给人一种“生机勃勃的幻觉”。

当前形势下，Code Agents 虽然可以自主编码、撰写测试，但要保证大型系统软件的工业级质量仍极具挑战。测试用例与测试工具正成为数据库领域最核心的资产。 事实上，SQLite、MySQL 等老牌开源软件很早就决定不开源其核心测试用例和测试工具（如 SQLite 核心测试用例是闭源的），这种未雨绸缪的做法在无意间为今天的 Code Agents 完美复刻制造了巨大的门槛。FrankenSQLite 等项目虽初步证明了 Code Agents 构建数据库系统的潜力，但其系统功能与性能仍远逊于打磨了二十载的 SQLite。

魔法对轰魔法：AI Driven Research

从现状看，Code Agents 能够迅速产出大量实现方案，而验证方案的有效性反而成了最大的吞吐瓶颈。

目前的最佳办法是“以魔法对抗魔法”——让 Code Agents 自己来验证有效性。然而，系统性能验证、并发正确性验证及完整的系统功能验证需要耗费巨大的资源和时间，一不小心就会演变成 AI 之间的“魔法对轰”，浪费无数算力。学术界也在研究中尝试 AI 驱动的研究：AI-Driven Research (ADR)。近期 arXiv 上的论文预印稿 AI-Driven Research for Databases (https://arxiv.org/abs/2604.06566) 提出：为了实现 ADR 在数据库系统研究的实际应用，建议通过让“评估器”与“解决方案”协同进化来实现自动化研究。在优化缓冲区管理、查询改写及索引选择的三个案例研究中，这种方法已初步显现其效果。

