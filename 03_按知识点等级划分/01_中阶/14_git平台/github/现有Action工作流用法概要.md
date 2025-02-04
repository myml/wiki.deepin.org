---
title: 现有 Action 工作流用法概要
description: 针对如何使用 GitHub 项目中已配置好的、现有的 GitHub Action 工作流。
published: true
date: 2022-10-25T02:40:16.170Z
tags: 
editor: markdown
dateCreated: 2022-10-10T08:05:35.301Z
---

# 概述

> 如果希望完整的了解 GitHub Action 的用法，请参阅[官方文档](https://docs.github.com/cn/actions/quickstart)。

GitHub Action 是 GitHub 开发流程中提供自动化 CI/CD 的组件之一。一般的，开源项目会为其配置对应的工作流来辅助项目的开发过程，例如配置自动打包构建、自动化测试、代码质量检查等流程。此文档将描述如何使用已配置好的 Action 工作流。

# 查看对应检查 Job 的日志

## 拉取请求（PR）中的 Action 检查

拉取请求中的 PR 状态会被显示在拉取请求的最下方：

![2022-10-10_47304.png](/2022-10-10_47304.png)

每一行对应一个不同的 Job 检查，可以点击对应行右侧的 Details 进入检查的详情页面，查看所对应检查的相关状态，点击会直接跳转到日志内容页面。

## 已推送的提交的 Action 检查

对于已推送的检查，均可以通过点击 commit 旁的对号或错号来以展开相关的详情，同样的，可以通过点击 Details 进入检查的详情页面。

![2022-10-10_34414.png](/2022-10-10_34414.png)

![2022-10-10_2923.png](/2022-10-10_2923.png)

# Action 状态（看 Log，下 Artifacts）

## 日志 (Log) 页面

从上述位置直接点击 Details 会进入对应 Job 的日志页面。

![2022-10-10_90354.png](/2022-10-10_90354.png)

每个 Job 会被分为多个不同的步骤按顺序进行执行，每个步骤会在右侧显示为一组，可以展开查看对应步骤的日志输出。

> 注意：当需要在日志中搜索内容时，**请使用此页面提供的搜索框**，而**不是 <kbd>Ctrl+F</kbd>**。
{.is-warning}

## 概览 (Summary) 页面

在日志页面的左侧上半部分，会看到左侧栏的 Summary 链接，点击即可进入对应 Action 的概览页面。

概览页面会展示工作流的执行状况，汇总信息（如果有），以及工作流所产生的资产（Artifacts，如果有）。

![2022-10-10_66357.png](/2022-10-10_66357.png)

如上截图为包含了资产的 Action 概览页面，在登录状态下，即可下载对应的资产以供使用了。此示例截图中未包含汇总信息，因为此工作流未提供汇总信息。

## Action 总览

可以在仓库的 Actions 标签中查看所有已执行的 Action 工作流。这对于故障排查很有用。对于仓库管理人员，也可以在此进行工作流的手动触发操作。

![2022-10-10_81137.png](/2022-10-10_81137.png)