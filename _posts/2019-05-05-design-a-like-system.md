---
layout: post
title:  设计点赞系统
date:   2019-05-05 23:50:00 +0800
categories: design-a-system
---

> 

## 1. 要点
+ 以队列方式，定期批量实现分布式计数
+ client 假处理（可选），提高用户体验

## 2. 原理
+ client 拉取目标的点赞信息
+ 当用户点击 like/unlike 时，client 向 server 发送请求；同时更新本地的点赞信息（可选）
+ server 把请求放入队列，按照目标分组，定期批量更新的点赞信息
+ client 定期拉取目标的点赞信息，如果计数时间新于本地计数时间，那么就更新本地的点赞信息

## A. 名词解释
+ 点赞信息：包括 like/unlike 计数、计数时间、此用户对目标的 like/unlike 状态