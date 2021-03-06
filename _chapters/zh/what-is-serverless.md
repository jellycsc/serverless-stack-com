---
layout: post
title: 什么是无服务器?
date: 2016-12-23 12:00:00
lang: zh
ref: what-is-serverless
description: 无服务器指的是由云服务商完全管理服务器和资源的管理和分配的应用程序。计费则基于这些资源的实际消耗。
comments_id: what-is-serverless/27
---

传统上，我们已经构建并部署了 web 应用程序，对这些应用程序，我们可以对服务器发出的 HTTP 请求进行一定程度的控制。我们的应用程序运行在该服务器上，我们负责为其配置和管理资源。但这会产生一些问题：

1、即使没有处理任何请求，我们也要保持服务器正常运行。

2、我们负责服务器及其所有资源的正常运行及维护。

3、我们还负责对服务器进行适当的安全更新。

4、随着使用量的扩张，我们还需要管理服务器的扩展，结果是，当我们没有太多使用量时，我们要将其减少。

对于较小的公司和个人开发者而言，这可能需要处理很多工作。这使得我们无法专注于我们更重要的工作，构建和维护实际的应用程序。在大型组织中，这是由基础设施团队处理的，并且通常这不是开发者个人的责任。但是，为此所需的过程最终可能减慢开发时间。因为你不能不与基础架构团队合作来帮助你启动和运行而直接继续构建应用程序。作为开发者，我们一直在寻找一种解决这些问题的方法，这就是无服务器的来源。


### 无服务器计算

无服务器计算（或简称 serverless），是一种执行模型，在该模型中，云服务商（AWS，Azure 或 Google Cloud）负责通过动态分配资源来执行一段代码，并且仅收取运行代码所使用资源的费用。该代码通常运行在无状态的容器中，能够被包括 HTTP 请求、数据库事件、队列服务、监控报警、文件上传、调度事件（cron 任务）等各种事件触发。被发送到云服务商执行的代码通常是以函数的形式，因此，无服务器计算有时是指 "函数即服务" 或者 FAAS。以下是主要云服务商提供的 FAAS 产品：

- AWS: [AWS Lambda](https://aws.amazon.com/lambda/)
- Microsoft Azure: [Azure Functions](https://azure.microsoft.com/en-us/services/functions/)
- Google Cloud: [Cloud Functions](https://cloud.google.com/functions/)

尽管无服务器计算对开发人员抽象了底层基础设施，但服务器仍然参与执行我们的函数。由于你的代码将要作为独立的函数执行，我们需要知道以下几点：

#### 微服务

当向无服务器计算的世界过度时，我们面临的最大变化是我们的程序需要被组织成函数的形式。你可能习惯于将你的应用程序部署为单个 Rails 或 Express 整体应用程序。但是在无服务器计算的世界里，你通常需要采用更多基于微服务的架构。你可以通过在单个函数中作为一个整体运行你的整个应用程序并自行处理路由来解决此问题。但不建议这样做，因为减小你的函数的大小更好。我们将在下面讨论。

#### 无状态函数

你的函数通常运行在安全的（几乎是）无状态容器中，这意味着你将无法在一个事件已经完成后长时间执行的应用服务器中运行代码，或者无法使用先前的执行上下文为请求提供服务。你不得不有效地假定你的函数每次都在一个新容器中被调用。 对此有一些微妙之处，我们将会在 [什么是 AWS Lambda]({% link _chapters/what-is-aws-lambda.md %}) 一章中进行讨论。

#### 冷启动

由于你的函数在需要响应事件的容器中运行，因此存在一定的延时。这被称为"冷启动"。当你的函数执行完成后，你的容器可能会保留一段时间。如果另一个事件在此时被触发，则它的响应速度要快得多，这通常被称为"热启动"。

冷启动的持续时间取决于特定云服务商的实现。在 AWS Lambda 上，它的范围从几百毫秒到几秒不等。它可能取决于使用的运行时（或编程语言）、函数（以包的形式）的大小，当然，还取决于所讨论的云服务商。多年以来，随着云服务商在优化时延方面变得越来越出色，冷启动已经大为改善。

除了优化你的函数，你还可以使用一些简单的技巧，例如使用单独的调度函数每隔几分钟来调用你的函数以使其保持运行状态。我们在此教程中使用的 [Serverless Framework](https://serverless.com) 中，有一些插件能够[帮助保持你的函数处于运行状态](https://github.com/FidelLimited/serverless-plugin-warmup)。

既然我们对无服务器计算有了一些了解，让我们更深入地了解什么是 Lambda 函数以及你的代码是如何被执行的。
