---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
title: Getting Started with ASP.NET 4.7 Web 窗体和 Visual Studio 2017 |Microsoft Docs
author: Erikre
description: 此分步教程系列将指导您学习生成使用 ASP.NET 4.7 和 Microsoft Visual Studio 的 ASP.NET Web 窗体应用程序的基础知识
ms.author: riande
ms.date: 01/09/2019
ms.assetid: 9b96eaa1-8ef0-4338-a2e8-e0f970bfaf68
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
msc.type: authoredcontent
ms.openlocfilehash: fb41ce72e9454d8d670a0b95234d2bc3f909f0ee
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341545"
---
<a name="getting-started-with-aspnet-45-web-forms-and-visual-studio-2017"></a><span data-ttu-id="9a9b8-103">开始使用 ASP.NET 4.5 Web 窗体和 Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="9a9b8-103">Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2017</span></span>
====================

<span data-ttu-id="9a9b8-104">[下载 Wingtip Toys 示例项目 (C#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409)或[下载电子书 (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)</span><span class="sxs-lookup"><span data-stu-id="9a9b8-104">[Download Wingtip Toys Sample Project (C#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) or [Download E-book (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)</span></span>

<span data-ttu-id="9a9b8-105">本系列教程演示如何构建具有 ASP.NET 4.5 和 Microsoft Visual Studio 2017 的 ASP.NET Web 窗体应用程序。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-105">This tutorial series shows you how to build an ASP.NET Web Forms application with ASP.NET 4.5 and Microsoft Visual Studio 2017.</span></span> 

## <a name="introduction"></a><span data-ttu-id="9a9b8-106">介绍</span><span class="sxs-lookup"><span data-stu-id="9a9b8-106">Introduction</span></span>

<span data-ttu-id="9a9b8-107">本教程系列将指导您创建使用 Visual Studio 2017 和 ASP.NET 4.5 的 ASP.NET Web 窗体应用程序。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-107">This tutorial series guides you through creating an ASP.NET Web Forms application using Visual Studio 2017 and ASP.NET 4.5.</span></span> <span data-ttu-id="9a9b8-108">你将创建名为应用程序**Wingtip Toys** -销售联机项简化店面 web 站点。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-108">You'll create an application named **Wingtip Toys** - a simplified storefront web site selling items online.</span></span> <span data-ttu-id="9a9b8-109">系列中，突出显示新的 ASP.NET 4.5 功能。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-109">During the series, new ASP.NET 4.5 features are highlighted.</span></span>

### <a name="target-audience"></a><span data-ttu-id="9a9b8-110">目标受众</span><span class="sxs-lookup"><span data-stu-id="9a9b8-110">Target audience</span></span>

<span data-ttu-id="9a9b8-111">本系列教程的目标受众是开发人员熟悉 ASP.NET Web 窗体。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-111">Developers new to ASP.NET Web Forms are the target audience for this tutorial series.</span></span>

<span data-ttu-id="9a9b8-112">您应该对一些了解以下几个方面：</span><span class="sxs-lookup"><span data-stu-id="9a9b8-112">You should have some knowledge in the following areas:</span></span>

- <span data-ttu-id="9a9b8-113">面向对象的编程 (OOP) 和语言</span><span class="sxs-lookup"><span data-stu-id="9a9b8-113">Object-oriented programming (OOP) and languages</span></span>
- <span data-ttu-id="9a9b8-114">Web 开发 (HTML、 CSS、 JavaScript)</span><span class="sxs-lookup"><span data-stu-id="9a9b8-114">Web development (HTML, CSS, JavaScript)</span></span>
- <span data-ttu-id="9a9b8-115">关系数据库</span><span class="sxs-lookup"><span data-stu-id="9a9b8-115">Relational databases</span></span>
- <span data-ttu-id="9a9b8-116">N 层体系结构</span><span class="sxs-lookup"><span data-stu-id="9a9b8-116">N-tier architecture</span></span>

<span data-ttu-id="9a9b8-117">若要查看这些区域，请考虑学习以下内容：</span><span class="sxs-lookup"><span data-stu-id="9a9b8-117">To review these areas, consider studying the following content:</span></span>

- [<span data-ttu-id="9a9b8-118">Visual C# 入门</span><span class="sxs-lookup"><span data-stu-id="9a9b8-118">Getting Started with Visual C#</span></span>](https://msdn.microsoft.com/library/a72418yk.aspx)
- <span data-ttu-id="9a9b8-119">[Web 开发](https://msdn.microsoft.com/beginner/bb308760.aspx)， [HTML、 CSS、 JavaScript、 SQL、 PHP、 JQuery](http://w3schools.com/)</span><span class="sxs-lookup"><span data-stu-id="9a9b8-119">[Web Development](https://msdn.microsoft.com/beginner/bb308760.aspx), [HTML, CSS, JavaScript, SQL, PHP, JQuery](http://w3schools.com/)</span></span>
- [<span data-ttu-id="9a9b8-120">关系数据库</span><span class="sxs-lookup"><span data-stu-id="9a9b8-120">Relational database</span></span>](http://en.wikipedia.org/wiki/Relational_database)
- [<span data-ttu-id="9a9b8-121">多层体系结构</span><span class="sxs-lookup"><span data-stu-id="9a9b8-121">Multitier architecture</span></span>](http://en.wikipedia.org/wiki/Multitier_architecture)

### <a name="application-features"></a><span data-ttu-id="9a9b8-122">应用程序功能</span><span class="sxs-lookup"><span data-stu-id="9a9b8-122">Application features</span></span>

<span data-ttu-id="9a9b8-123">本系列中显示的 ASP.NET Web 窗体功能包括：</span><span class="sxs-lookup"><span data-stu-id="9a9b8-123">The ASP.NET Web Form features presented in this series include:</span></span>

- <span data-ttu-id="9a9b8-124">Web 应用程序项目 （而不是网站项目）</span><span class="sxs-lookup"><span data-stu-id="9a9b8-124">The Web Application Project (not Web Site Project)</span></span>
- <span data-ttu-id="9a9b8-125">Web Forms — Web 窗体</span><span class="sxs-lookup"><span data-stu-id="9a9b8-125">Web Forms</span></span>
- <span data-ttu-id="9a9b8-126">母版页配置</span><span class="sxs-lookup"><span data-stu-id="9a9b8-126">Master Pages, Configuration</span></span>
- <span data-ttu-id="9a9b8-127">Bootstrap</span><span class="sxs-lookup"><span data-stu-id="9a9b8-127">Bootstrap</span></span>
- <span data-ttu-id="9a9b8-128">实体框架代码优先，LocalDB</span><span class="sxs-lookup"><span data-stu-id="9a9b8-128">Entity Framework Code First, LocalDB</span></span>
- <span data-ttu-id="9a9b8-129">请求验证</span><span class="sxs-lookup"><span data-stu-id="9a9b8-129">Request Validation</span></span>
- <span data-ttu-id="9a9b8-130">强类型数据控件</span><span class="sxs-lookup"><span data-stu-id="9a9b8-130">Strongly-typed Data Controls</span></span>
- <span data-ttu-id="9a9b8-131">模型绑定</span><span class="sxs-lookup"><span data-stu-id="9a9b8-131">Model Binding</span></span>
- <span data-ttu-id="9a9b8-132">数据注释</span><span class="sxs-lookup"><span data-stu-id="9a9b8-132">Data Annotations</span></span>
- <span data-ttu-id="9a9b8-133">值提供程序</span><span class="sxs-lookup"><span data-stu-id="9a9b8-133">Value Providers</span></span>
- <span data-ttu-id="9a9b8-134">SSL 和 OAuth</span><span class="sxs-lookup"><span data-stu-id="9a9b8-134">SSL and OAuth</span></span>
- <span data-ttu-id="9a9b8-135">ASP.NET 标识、 配置和授权</span><span class="sxs-lookup"><span data-stu-id="9a9b8-135">ASP.NET Identity, Configuration, and Authorization</span></span>
- <span data-ttu-id="9a9b8-136">非介入式验证</span><span class="sxs-lookup"><span data-stu-id="9a9b8-136">Unobtrusive Validation</span></span>
- <span data-ttu-id="9a9b8-137">路由</span><span class="sxs-lookup"><span data-stu-id="9a9b8-137">Routing</span></span>
- <span data-ttu-id="9a9b8-138">ASP.NET 错误处理</span><span class="sxs-lookup"><span data-stu-id="9a9b8-138">ASP.NET Error Handling</span></span>

### <a name="application-scenarios-and-tasks"></a><span data-ttu-id="9a9b8-139">应用程序方案和任务</span><span class="sxs-lookup"><span data-stu-id="9a9b8-139">Application scenarios and tasks</span></span>

<span data-ttu-id="9a9b8-140">教程系列的任务包括：</span><span class="sxs-lookup"><span data-stu-id="9a9b8-140">Tutorial series tasks include:</span></span>

- <span data-ttu-id="9a9b8-141">创建、 检查，并运行新的项目</span><span class="sxs-lookup"><span data-stu-id="9a9b8-141">Creating, reviewing, and running a new project</span></span>
- <span data-ttu-id="9a9b8-142">创建的数据库结构</span><span class="sxs-lookup"><span data-stu-id="9a9b8-142">Creating a database structure</span></span>
- <span data-ttu-id="9a9b8-143">初始化和数据库进行种子设定</span><span class="sxs-lookup"><span data-stu-id="9a9b8-143">Initializing and seeding a database</span></span>
- <span data-ttu-id="9a9b8-144">自定义 UI 样式、 图形和母版页</span><span class="sxs-lookup"><span data-stu-id="9a9b8-144">Customizing the UI with styles, graphics, and a master page</span></span>
- <span data-ttu-id="9a9b8-145">添加页面和导航</span><span class="sxs-lookup"><span data-stu-id="9a9b8-145">Adding pages and navigation</span></span>
- <span data-ttu-id="9a9b8-146">显示菜单的详细信息和产品数据</span><span class="sxs-lookup"><span data-stu-id="9a9b8-146">Displaying menu details and product data</span></span>
- <span data-ttu-id="9a9b8-147">创建购物车</span><span class="sxs-lookup"><span data-stu-id="9a9b8-147">Creating a shopping cart</span></span>
- <span data-ttu-id="9a9b8-148">添加 SSL 和 OAuth 支持</span><span class="sxs-lookup"><span data-stu-id="9a9b8-148">Adding SSL and OAuth support</span></span>
- <span data-ttu-id="9a9b8-149">添加付款方式</span><span class="sxs-lookup"><span data-stu-id="9a9b8-149">Adding a payment method</span></span>
- <span data-ttu-id="9a9b8-150">包括一个管理员角色和用户访问应用程序</span><span class="sxs-lookup"><span data-stu-id="9a9b8-150">Including an administrator role and a user to the application</span></span>
- <span data-ttu-id="9a9b8-151">限制对特定页面和文件夹的访问</span><span class="sxs-lookup"><span data-stu-id="9a9b8-151">Restricting access to specific pages and folder</span></span>
- <span data-ttu-id="9a9b8-152">将文件上载到 web 应用程序</span><span class="sxs-lookup"><span data-stu-id="9a9b8-152">Uploading a file to the web application</span></span>
- <span data-ttu-id="9a9b8-153">实施输入的验证</span><span class="sxs-lookup"><span data-stu-id="9a9b8-153">Implementing input validation</span></span>
- <span data-ttu-id="9a9b8-154">注册 web 应用程序路由</span><span class="sxs-lookup"><span data-stu-id="9a9b8-154">Registering routes for the web application</span></span>
- <span data-ttu-id="9a9b8-155">实现错误处理和错误日志记录</span><span class="sxs-lookup"><span data-stu-id="9a9b8-155">Implementing error handling and error logging</span></span>

## <a name="overview"></a><span data-ttu-id="9a9b8-156">概述</span><span class="sxs-lookup"><span data-stu-id="9a9b8-156">Overview</span></span>

<span data-ttu-id="9a9b8-157">本教程系列是用于人熟悉的编程概念，但熟悉 ASP.NET Web 窗体。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-157">This tutorial series is intended for someone familiar with programming concepts, but new to ASP.NET Web Forms.</span></span> <span data-ttu-id="9a9b8-158">如果您已经熟悉 ASP.NET Web 窗体，这一系列仍然可以帮助您了解新的 ASP.NET 4.5 功能。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-158">If you're already familiar with ASP.NET Web Forms, this series can still help you learn about new ASP.NET 4.5 features.</span></span> <span data-ttu-id="9a9b8-159">有关读者熟悉编程概念和 ASP.NET Web 窗体，请参阅中提供的其他 Web 窗体教程[Getting Started](../../../index.md) ASP.NET 网站上的部分。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-159">For readers unfamiliar with programming concepts and ASP.NET Web Forms, see the additional Web Forms tutorials provided in the [Getting Started](../../../index.md) section on the ASP.NET Web site.</span></span>

<span data-ttu-id="9a9b8-160">在本系列教程中提供在 ASP.NET 4.5 包括以下功能：</span><span class="sxs-lookup"><span data-stu-id="9a9b8-160">The ASP.NET 4.5 provided in this tutorial series includes the following features:</span></span>

- <span data-ttu-id="9a9b8-161">提供了用于创建项目的简单 UI[对许多 ASP.NET 框架的支持](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add)（Web 窗体、 MVC 和 Web API）。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-161">A simple UI for creating projects that offers [support for many ASP.NET frameworks](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (Web Forms, MVC, and Web API).</span></span>
- <span data-ttu-id="9a9b8-162">[Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap)，布局、 主题和响应式设计框架。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-162">[Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), a layout, theming, and responsive design framework.</span></span>
- <span data-ttu-id="9a9b8-163">[ASP.NET 标识](../../../../identity/index.md)，新的 ASP.NET 成员资格系统的与 web 托管 IIS 之外的软件的工作方式在所有 ASP.NET 框架和工作原理相同。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-163">[ASP.NET Identity](../../../../identity/index.md), a new ASP.NET membership system that works the same in all ASP.NET frameworks and works with web hosting software other than IIS.</span></span>
- [<span data-ttu-id="9a9b8-164">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="9a9b8-164">Entity Framework 6</span></span>](https://msdn.microsoft.com/data/ef.aspx)

  <span data-ttu-id="9a9b8-165">实体框架使您能够更新：</span><span class="sxs-lookup"><span data-stu-id="9a9b8-165">An update to the Entity Framework enabling you to:</span></span>
  - <span data-ttu-id="9a9b8-166">检索和操作数据作为强类型化对象</span><span class="sxs-lookup"><span data-stu-id="9a9b8-166">Retrieve and manipulate data as strongly-typed objects</span></span>
  - <span data-ttu-id="9a9b8-167">以异步方式访问数据</span><span class="sxs-lookup"><span data-stu-id="9a9b8-167">Access data asynchronously</span></span>
  - <span data-ttu-id="9a9b8-168">处理暂时性连接故障</span><span class="sxs-lookup"><span data-stu-id="9a9b8-168">Handle transient connection faults</span></span>
  - <span data-ttu-id="9a9b8-169">日志的 SQL 语句</span><span class="sxs-lookup"><span data-stu-id="9a9b8-169">Log SQL statements</span></span>

<span data-ttu-id="9a9b8-170">有关完整的 ASP.NET 4.5 功能列表，请参阅[ASP.NET 和 Web Tools for Visual Studio 2013 发行说明](../../../../visual-studio/overview/2013/release-notes.md)。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-170">For a complete ASP.NET 4.5 feature list, see [ASP.NET and Web Tools for Visual Studio 2013 Release Notes](../../../../visual-studio/overview/2013/release-notes.md).</span></span>

### <a name="the-wingtip-toys-sample-application"></a><span data-ttu-id="9a9b8-171">Wingtip Toys 示例应用程序</span><span class="sxs-lookup"><span data-stu-id="9a9b8-171">The Wingtip Toys sample application</span></span>

<span data-ttu-id="9a9b8-172">下面的屏幕截图取自您在本系列教程中创建的 ASP.NET Web 窗体应用程序。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-172">The following screenshots are from the ASP.NET Web Forms application that you create in this tutorial series.</span></span> <span data-ttu-id="9a9b8-173">在 Visual Studio 中运行应用程序时，将显示以下 web 主页。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-173">When you run the application in Visual Studio, the following web Home page appears.</span></span>

![Wingtip Toys 的默认页](introduction-and-overview/_static/image1.png)

<span data-ttu-id="9a9b8-175">可以将注册为新用户，或以现有用户身份登录。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-175">You can register as a new user, or sign in as an existing user.</span></span> <span data-ttu-id="9a9b8-176">顶部导航栏上的链接从数据库产品类别以及他们的产品。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-176">The top navigation has links to product categories and their products from the database.</span></span>

<span data-ttu-id="9a9b8-177">如果选择**产品**，显示所有可用产品。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-177">If you select **Products**, all available products are displayed.</span></span> 

![Wingtip Toys 的产品](introduction-and-overview/_static/image2.png)

<span data-ttu-id="9a9b8-179">如果选择特定产品时，将显示产品详细信息。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-179">If you select a specific product, product details are displayed.</span></span>


![Wingtip Toys 的产品详细信息](introduction-and-overview/_static/image3.png)

<span data-ttu-id="9a9b8-181">作为用户，可以注册和登录 Web 窗体模板的默认功能。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-181">As a user, you can register and sign in with Web Forms template default functionality.</span></span> <span data-ttu-id="9a9b8-182">本教程还介绍如何使用现有的 Gmail 帐户登录。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-182">This tutorial also explains how to sign in using an existing Gmail account.</span></span> <span data-ttu-id="9a9b8-183">此外，你可以以管理员身份登录来添加和从数据库中删除产品。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-183">Additionally, you can sign in as the administrator to add and remove products from the database.</span></span>

![Wingtip Toys-登录](introduction-and-overview/_static/image4.png)

<span data-ttu-id="9a9b8-185">你已以用户身份登录，你可以将产品添加到购物车并使用 PayPal 结帐。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-185">Once you've signed in as a user, you can add products to the shopping cart and checkout with PayPal.</span></span> <span data-ttu-id="9a9b8-186">示例应用程序用于处理 PayPal 的开发人员沙箱中。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-186">The sample application is designed to work in PayPal's developer sandbox.</span></span> <span data-ttu-id="9a9b8-187">没有实际资金事务会发生。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-187">No actual money transaction takes place.</span></span>

![Wingtip Toys 的购物车](introduction-and-overview/_static/image5.png)

<span data-ttu-id="9a9b8-189">PayPal 确认你的帐户、 订单和付款信息。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-189">PayPal confirms your account, order, and payment information.</span></span>

![Wingtip Toys - PayPal](introduction-and-overview/_static/image6.png)

<span data-ttu-id="9a9b8-191">返回从 PayPal 后, 可以查看并完成你的订单。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-191">After returning from PayPal, you can review and complete your order.</span></span>

![Wingtip Toys 的顺序查看](introduction-and-overview/_static/image7.png)

## <a name="prerequisites"></a><span data-ttu-id="9a9b8-193">系统必备</span><span class="sxs-lookup"><span data-stu-id="9a9b8-193">Prerequisites</span></span>

<span data-ttu-id="9a9b8-194">在开始之前，请确保您的计算机上安装以下软件：</span><span class="sxs-lookup"><span data-stu-id="9a9b8-194">Before you start, make sure the following software is installed on your computer:</span></span>

- <span data-ttu-id="9a9b8-195">[Microsoft Visual Studio 2017 或 Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-195">[Microsoft Visual Studio 2017 or Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/).</span></span>

<span data-ttu-id="9a9b8-196">会自动安装.NET Framework。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-196">The .NET Framework is installed automatically.</span></span>

<span data-ttu-id="9a9b8-197">本系列教程使用 Microsoft Visual Studio Community 2017。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-197">This tutorial series uses Microsoft Visual Studio Community 2017.</span></span> <span data-ttu-id="9a9b8-198">可以使用任一，或 Microsoft Visual Studio 2017，若要完成本系列教程。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-198">You can use either that or Microsoft Visual Studio 2017 to complete this tutorial series.</span></span>

<span data-ttu-id="9a9b8-199">请注意以下有关 Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9a9b8-199">Note the following about Visual Studio:</span></span>

* <span data-ttu-id="9a9b8-200">Microsoft Visual Studio 2017和Microsoft Visual Studio社区2017在本教程系列中称为*Visual Studio*。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-200">Microsoft Visual Studio 2017 and Microsoft Visual Studio Community 2017 are referred to as *Visual Studio* throughout this tutorial series.</span></span>

* <span data-ttu-id="9a9b8-201">Visual Studio 2017 安装已安装任何较旧版本旁边。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-201">Visual Studio 2017 is installed next to any older versions already installed.</span></span> <span data-ttu-id="9a9b8-202">在早期版本中创建的站点可以在 Visual Studio 2017 中打开，并继续在以前的版本中打开。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-202">Sites created in earlier versions can be opened in Visual Studio 2017 and continue to open in previous versions.</span></span>

* <span data-ttu-id="9a9b8-203">首次启动 Visual Studio 时，假定所选*Web 开发*设置。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-203">The first time you started Visual Studio, it is assumed you selected the *Web Development* settings.</span></span> <span data-ttu-id="9a9b8-204">有关详细信息，请参阅[如何：选择 Web 开发环境设置](https://msdn.microsoft.com/library/ff521558.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-204">For more information, see [How to: Select Web Development Environment Settings](https://msdn.microsoft.com/library/ff521558.aspx).</span></span>

<span data-ttu-id="9a9b8-205">在安装后系统必备组件，现在即可开始创建在本系列教程中提供的 Web 项目。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-205">After installing the prerequisites, you're ready to begin creating the Web project presented in this tutorial series.</span></span>

## <a name="download-the-sample-application"></a><span data-ttu-id="9a9b8-206">下载示例应用程序</span><span class="sxs-lookup"><span data-stu-id="9a9b8-206">Download the sample application</span></span>

 <span data-ttu-id="9a9b8-207">您可以从 MSDN 示例网站随时下载在已完成的示例 applicatiion:</span><span class="sxs-lookup"><span data-stu-id="9a9b8-207">You can download the completed sample applicatiion at anytime from the MSDN Samples site:</span></span>

<span data-ttu-id="9a9b8-208">[开始使用 ASP.NET 4.5 Web 窗体和 Visual Studio 2013-Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#)</span><span class="sxs-lookup"><span data-stu-id="9a9b8-208">[Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#)</span></span> 

 <span data-ttu-id="9a9b8-209">此下载具有以下各项：</span><span class="sxs-lookup"><span data-stu-id="9a9b8-209">This download has the following items:</span></span>

- <span data-ttu-id="9a9b8-210">中的示例应用程序*WingtipToys*文件夹。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-210">The sample application in the *WingtipToys* folder.</span></span>
- <span data-ttu-id="9a9b8-211">若要创建示例应用程序所使用的资源*WingtipToys 资产*中的文件夹*WingtipToys*文件夹。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-211">The resources used to create the sample application in the *WingtipToys-Assets* folder in the *WingtipToys* folder.</span></span>

<span data-ttu-id="9a9b8-212">在下载 *.zip*文件。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-212">The download is a *.zip* file.</span></span> <span data-ttu-id="9a9b8-213">若要查看已完成本教程系列中创建的项目，找到并选择*C#* 文件夹的.zip 文件。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-213">To see the completed project that this tutorial series creates, find and select the *C#* folder in the .zip file.</span></span> <span data-ttu-id="9a9b8-214">保存C#使用用于 Visual Studio 项目的文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-214">Save the C# folder to the folder you use to work with Visual Studio projects.</span></span> <span data-ttu-id="9a9b8-215">默认情况下，Visual Studio 2017 项目文件夹为：</span><span class="sxs-lookup"><span data-stu-id="9a9b8-215">By default, the Visual Studio 2017 projects folder is:</span></span>

<span data-ttu-id="9a9b8-216"><strong>C:\Users&#92;</strong><strong><em>&lt;username&gt;</em></strong><strong>\source\repos</strong></span><span class="sxs-lookup"><span data-stu-id="9a9b8-216"><strong>C:\Users&#92;</strong><strong><em>&lt;username&gt;</em></strong><strong>\source\repos</strong></span></span>

<span data-ttu-id="9a9b8-217">重命名***C#*** 向文件夹***WingtipToys***。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-217">Rename the ***C#*** folder to ***WingtipToys***.</span></span>

> [!NOTE]
> <span data-ttu-id="9a9b8-218">如果已有一个名为文件夹*WingtipToys*在项目文件夹中，暂时重命名该现有文件夹重命名前*C#* 文件夹*WingtipToys*。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-218">If you already have a folder named *WingtipToys* in your Projects folder, temporarily rename that existing folder before renaming the *C#* folder to *WingtipToys*.</span></span>

<span data-ttu-id="9a9b8-219">若要运行已完成的项目，打开*WingtipToys*文件夹，然后双击*WingtipToys.sln*文件。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-219">To run the completed project, open the *WingtipToys* folder and double-click the *WingtipToys.sln* file.</span></span> <span data-ttu-id="9a9b8-220">Visual Studio 2017 打开项目。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-220">Visual Studio 2017 opens the project.</span></span> <span data-ttu-id="9a9b8-221">接下来，右键单击*Default.aspx*中的文件**解决方案资源管理器**，然后选择**中浏览器中查看**。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-221">Next, right-click the *Default.aspx* file in **Solution Explorer** and select **View In Browser**.</span></span>

## <a name="take-a-aspnet-web-forms-quiz-to-review-content"></a><span data-ttu-id="9a9b8-222">参加的 ASP.NET Web 窗体测验，若要查看内容</span><span class="sxs-lookup"><span data-stu-id="9a9b8-222">Take a ASP.NET Web Forms quiz to review content</span></span>

<span data-ttu-id="9a9b8-223">完成后的系列教程，请测试您的知识并加强关键概念小测验。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-223">After completing the tutorial series, take a quiz to test your knowledge and reinforce key concepts.</span></span> <span data-ttu-id="9a9b8-224">每个问题提供的说明和链接的其他指导。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-224">Each question provides an explanation and links to additional guidance.</span></span>

 * [<span data-ttu-id="9a9b8-225">ASP.NET Web 窗体测验</span><span class="sxs-lookup"><span data-stu-id="9a9b8-225">ASP.NET Web Forms Quiz</span></span>](https://blogs.msdn.microsoft.com/erikreitan/2016/01/08/asp-net-web-forms-quiz/) 

## <a name="tutorial-support-and-comments"></a><span data-ttu-id="9a9b8-226">教程支持和提出的意见</span><span class="sxs-lookup"><span data-stu-id="9a9b8-226">Tutorial support and comments</span></span>

<span data-ttu-id="9a9b8-227">有关的问题和意见，使用包含的问题与解答部分[开始使用 ASP.NET 4.5 Web 窗体和 Visual Studio 2013-Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) 示例页面。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-227">For questions and comments, use the Q and A section included on the [Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) sample page.</span></span>

<span data-ttu-id="9a9b8-228">在本教程系列中的注释是欢迎使用。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-228">Comments on this tutorial series are welcome.</span></span> <span data-ttu-id="9a9b8-229">更新本系列教程后，每项工作是进行更正或改进建议，请考虑。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-229">When this tutorial series is updated, every effort is made to consider corrections or suggestions for improvements.</span></span>

<span data-ttu-id="9a9b8-230">如果发生错误，相应的错误消息可能是令人困惑，与不合理的解释如何修复此错误。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-230">If an error occurs, the corresponding error messages could be confusing, with no good explanation on how to fix it.</span></span> <span data-ttu-id="9a9b8-231">如需帮助，可以查看[ASP.NET 论坛](https://forums.asp.net/)。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-231">For help, you can check the [ASP.NET forums](https://forums.asp.net/).</span></span> <span data-ttu-id="9a9b8-232">另一个很好来源是中的问题与解答部分[开始使用 ASP.NET 4.5 Web 窗体和 Visual Studio 2013-Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) 示例页面。</span><span class="sxs-lookup"><span data-stu-id="9a9b8-232">Another good source is the Q and A section in the [Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) sample page.</span></span> 

> [!div class="step-by-step"]
> [<span data-ttu-id="9a9b8-233">下一页</span><span class="sxs-lookup"><span data-stu-id="9a9b8-233">Next</span></span>](create-the-project.md)
