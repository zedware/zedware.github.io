---
layout: post
title: State Machine and Event Processing
original: https://blog.csdn.net/zedware/article/details/7251583
---

列了个提纲，一直没有写完。
1. Introduction
1.1 What's State Machine
1.2 What's Event Processing

2. Implementation of State Machine
1.1 C style switch/case
1.2 GoF Design Pattern
    GoF's book.
1.3 State Machine Design Pattern with Event
    http://angry-architect.blogspot.com/2006/08/problems-with-state-pattern.html
1.4 Java Design Pattern

3. Details
1.1 Special States
Ready, End, Abort
1.2 States for error processing
1.3 Special Events
Abort, Timeout
1.4 Concurrency
Caused by special events.
Caused by underlying APIs.
Concurreny in a state - intra-state concurrency
Concurrent among states - inter-state concurrency
1.5 Coding style
Wrapper for underlying APIs
Loops in a state
Loops among states
            
