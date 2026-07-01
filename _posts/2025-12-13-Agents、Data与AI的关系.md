---
layout: post
title: Agents、Data 与 AI 的关系
original: https://mp.weixin.qq.com/s/OoiOZgayzhq5Qo_-envz9A
tags: [Data and AI, Agents]
---

谁说站在光里的才算英雄？——— 孤勇者

在号称 Agent 元年的今天，不谈 Agents 未免有些 Out 了。最近的 AWS 年度大会 re:Invent 2025 上，其 CTO，著名的大个头荷兰人， Werner Hans Peter Vogels 都宣布这是他最后一次大会演讲，将把机会让给年轻人，并在最后宣布“Werner Out”！Agents 在最近确实很火，也在某些领域获得一些应用。不过，估计要像传统 MIS 应用、Web 应用、移动 App 那么成熟还为时过早。这也给大家做大模型、应用框架、应用工具，甚至数据基础设施的公司和团队带来了新的机会。

无论是从 Google 还是 AWS 发布的 Agents 系列服务来看，围绕 Agents 的核心组件包括：SDK、Framework、Memory、Runtime，当然也少不了 MCP Server、Browser Tool 等。还有一个容易被忽视但极端重要的基础设施，就是 Agent 的全栈监控以及对应的问题诊断分析（Observability）。Agent Ops 背后对应的是可观测数据库及分析系统，现有的可观测性数据系统包括 InfluxDB、Datadog 等，它们也需要为 Agents 改进。从数据系统的角度看，记忆系统（Memory System）显得比较突出和重要，但实际上其背后存放数据的仍然大多是已有的各种数据库系统。如果要更好的服务于 Agent 应用，避免操作一大堆不同的数据库系统，Databricks Neon 等可能是潜在的更好选择。

其实，如果只是做个 Demo 系统，选择的方式可能是哪个容易上手、怎么方便怎么来，别说 Postgres，就算是 SQLite，甚至 DuckDB 都可能够用。当然也有些另类系统，如 Turso，它是用 Rust 写的兼容 SQLite 的小型数据库系统。正经的线上系统还是需要选择成熟的云服务。

此外，MCP 后面可以对接现有的各种数据库系统。所以，从封面或类似的图中看起来，数据库都成了幕后的基础设施。那么，在 Agents 应用时代，数据系统是否也 Out 了呢？如果我们稍为研究一下历史，就会发现以数据库系统为代表的数据系统从来就没有被替代，只不过它们不在聚光灯下而已。

