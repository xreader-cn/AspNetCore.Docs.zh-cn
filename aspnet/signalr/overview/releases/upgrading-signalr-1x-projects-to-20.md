---
uid: signalr/overview/releases/upgrading-signalr-1x-projects-to-20
title: 将 SignalR 1.x 项目升级到版本 2 |Microsoft Docs
author: bradygaster
description: 本主题介绍如何升级现有的 SignalR 1.x 项目到 SignalR 2.x 中，以及如何解决在升级过程中可能出现的问题...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: adcfef99-9bc5-489d-a91b-9b7c2bc35e04
msc.legacyurl: /signalr/overview/releases/upgrading-signalr-1x-projects-to-20
msc.type: authoredcontent
ms.openlocfilehash: a1cb4903f3cdeef70ffd0f624a3a2170f641a395
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837333"
---
<a name="upgrading-signalr-1x-projects-to-version-2"></a><span data-ttu-id="f487b-103">将 SignalR 1.x 项目升级到版本 2</span><span class="sxs-lookup"><span data-stu-id="f487b-103">Upgrading SignalR 1.x Projects to version 2</span></span>
====================
<span data-ttu-id="f487b-104">通过[Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="f487b-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="f487b-105">本主题介绍如何升级现有的 SignalR 1.x 项目到 SignalR 2.x 中，以及如何解决在升级过程中可能出现的问题。</span><span class="sxs-lookup"><span data-stu-id="f487b-105">This topic describes how to upgrade an existing SignalR 1.x project to SignalR 2.x, and how to troubleshoot issues that may arise during the upgrade process.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="f487b-106">在本教程中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="f487b-106">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="f487b-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="f487b-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="f487b-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="f487b-108">.NET 4.5</span></span>
> - <span data-ttu-id="f487b-109">SignalR 版本 1 和 2</span><span class="sxs-lookup"><span data-stu-id="f487b-109">SignalR versions 1 and 2</span></span>
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="f487b-110">本教程使用 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="f487b-110">Using Visual Studio 2012 with this tutorial</span></span>
>
>
> <span data-ttu-id="f487b-111">若要学习本教程使用 Visual Studio 2012，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f487b-111">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
>
> - <span data-ttu-id="f487b-112">更新你[程序包管理器](http://docs.nuget.org/docs/start-here/installing-nuget)到最新版本。</span><span class="sxs-lookup"><span data-stu-id="f487b-112">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="f487b-113">安装[Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)。</span><span class="sxs-lookup"><span data-stu-id="f487b-113">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="f487b-114">在 Web 平台安装程序中，搜索并安装**ASP.NET 和 Web 工具 2013.1 适用于 Visual Studio 2012**。</span><span class="sxs-lookup"><span data-stu-id="f487b-114">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="f487b-115">这将安装 Visual Studio 模板的 SignalR 类，如**中心**。</span><span class="sxs-lookup"><span data-stu-id="f487b-115">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="f487b-116">某些模板 (如**OWIN 启动类**) 将不可用; 对于这些数据，改为使用的类文件。</span><span class="sxs-lookup"><span data-stu-id="f487b-116">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="f487b-117">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="f487b-117">Questions and comments</span></span>
>
> <span data-ttu-id="f487b-118">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="f487b-118">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="f487b-119">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="f487b-119">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="f487b-120">SignalR 2，在使用的服务器平台提供一致的开发体验[OWIN](http://owin.org)。</span><span class="sxs-lookup"><span data-stu-id="f487b-120">SignalR 2 offers a consistent development experience across server platforms using [OWIN](http://owin.org).</span></span> <span data-ttu-id="f487b-121">本文介绍的几个步骤所需更新到版本 2 的 SignalR 1.x 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f487b-121">This article describes the few steps that are needed to update a SignalR 1.x application to version 2.</span></span>

<span data-ttu-id="f487b-122">虽然建议升级应用程序到 SignalR 2，SignalR 1.x 仍受支持。</span><span class="sxs-lookup"><span data-stu-id="f487b-122">While it is encouraged to upgrade applications to SignalR 2, SignalR 1.x will still be supported.</span></span>

<span data-ttu-id="f487b-123">本教程介绍如何升级到 SignalR 2 web 承载的应用程序。</span><span class="sxs-lookup"><span data-stu-id="f487b-123">This tutorial describes how to upgrade a web-hosted application to SignalR 2.</span></span> <span data-ttu-id="f487b-124">SignalR 2 下现在支持自承载应用程序 （这些托管在一个控制台应用程序、 Windows 服务或其他进程服务器）。</span><span class="sxs-lookup"><span data-stu-id="f487b-124">Self-hosted applications (those that host a server in a console application, Windows service, or other process) are now supported under SignalR 2.</span></span> <span data-ttu-id="f487b-125">有关如何开始使用 SignalR 2 创建自承载的应用程序的信息，请参阅[教程：自承载 SignalR](../deployment/tutorial-signalr-self-host.md)。</span><span class="sxs-lookup"><span data-stu-id="f487b-125">For information on how to get started creating a self-hosted application with SignalR 2, see [Tutorial: SignalR Self-Host](../deployment/tutorial-signalr-self-host.md).</span></span>

## <a name="contents"></a><span data-ttu-id="f487b-126">内容</span><span class="sxs-lookup"><span data-stu-id="f487b-126">Contents</span></span>

<span data-ttu-id="f487b-127">以下部分介绍与升级 SignalR 项目，以及如何解决可能出现的问题涉及的任务。</span><span class="sxs-lookup"><span data-stu-id="f487b-127">The following sections describe tasks involved with upgrading SignalR projects, and how to troubleshoot issues that may arise.</span></span>

- [<span data-ttu-id="f487b-128">示例：升级到 SignalR 2 入门教程</span><span class="sxs-lookup"><span data-stu-id="f487b-128">Example: Upgrading the Getting Started tutorial to SignalR 2</span></span>](#example)
- [<span data-ttu-id="f487b-129">在升级过程中遇到的错误疑难解答</span><span class="sxs-lookup"><span data-stu-id="f487b-129">Troubleshooting errors encountered during upgrading</span></span>](#troubleshooting)

<a id="example"></a>

## <a name="example-upgrading-the-getting-started-tutorial-application-to-signalr-2"></a><span data-ttu-id="f487b-130">示例:入门教程应用程序升级到 SignalR 2</span><span class="sxs-lookup"><span data-stu-id="f487b-130">Example: Upgrading the Getting Started tutorial application to SignalR 2</span></span>

<span data-ttu-id="f487b-131">在本部分中，你将更新中创建的应用程序[SignalR 1.x 版的入门教程](../older-versions/index.md)使用 SignalR 2。</span><span class="sxs-lookup"><span data-stu-id="f487b-131">In this section, you'll update the application created in the [SignalR 1.x version of the Getting Started Tutorial](../older-versions/index.md) to use SignalR 2.</span></span>

1. <span data-ttu-id="f487b-132">完成入门教程后，右键单击项目，然后选择**属性**。</span><span class="sxs-lookup"><span data-stu-id="f487b-132">Once you've finished the Getting Started tutorial, right-click on the project, and select **Properties**.</span></span> <span data-ttu-id="f487b-133">确认**目标框架**设置为 **.NET Framework 4.5。**</span><span class="sxs-lookup"><span data-stu-id="f487b-133">Verify that the **Target framework** is set to **.NET Framework 4.5.**</span></span>
2. <span data-ttu-id="f487b-134">打开包管理器控制台。</span><span class="sxs-lookup"><span data-stu-id="f487b-134">Open the Package Manager Console.</span></span> <span data-ttu-id="f487b-135">删除 SignalR 1.x 从项目使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="f487b-135">Remove SignalR 1.x from the project using the following command:</span></span>

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample1.ps1)]
3. <span data-ttu-id="f487b-136">安装 SignalR 2： 使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="f487b-136">Install SignalR 2 using the following command:</span></span>

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample2.ps1)]
4. <span data-ttu-id="f487b-137">在 HTML 页中，更新 SignalR 以匹配的版本现在包含在项目中的脚本的脚本引用。</span><span class="sxs-lookup"><span data-stu-id="f487b-137">In the HTML page, update the script reference for SignalR to match the version of the script now included in the project.</span></span>

    [!code-html[Main](upgrading-signalr-1x-projects-to-20/samples/sample3.html)]
5. <span data-ttu-id="f487b-138">在全局应用程序类中，删除对 MapHubs 的调用。</span><span class="sxs-lookup"><span data-stu-id="f487b-138">In the global application class, remove the call to MapHubs.</span></span>

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample4.cs)]
6. <span data-ttu-id="f487b-139">右键单击解决方案，然后选择**外**，**新项...**.在对话框中，选择**Owin 启动类**。</span><span class="sxs-lookup"><span data-stu-id="f487b-139">Right-click the solution, and select **Add**, **New Item...**. In the dialog, select **Owin Startup Class**.</span></span> <span data-ttu-id="f487b-140">将新类命名**Startup.cs**。</span><span class="sxs-lookup"><span data-stu-id="f487b-140">Name the new class **Startup.cs**.</span></span>

    ![](upgrading-signalr-1x-projects-to-20/_static/image1.png)
7. <span data-ttu-id="f487b-141">Startup.cs 的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="f487b-141">Replace the contents of Startup.cs with the following code:</span></span>

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample5.cs)]

    <span data-ttu-id="f487b-142">程序集特性将类添加到 Owin 的启动过程中，后者执行`Configuration`Owin 启动时的方法。</span><span class="sxs-lookup"><span data-stu-id="f487b-142">The assembly attribute adds the class to Owin's startup process, which executes the `Configuration` method when Owin starts up.</span></span> <span data-ttu-id="f487b-143">此方法又调用`MapSignalR`方法，用于在应用程序中创建的所有 SignalR 集线器路由。</span><span class="sxs-lookup"><span data-stu-id="f487b-143">This in turn calls the `MapSignalR` method, which creates routes for all SignalR hubs in the application.</span></span>
8. <span data-ttu-id="f487b-144">运行该项目，并主页的 URL 复制到另一个浏览器或浏览器窗格中的，前面所述。</span><span class="sxs-lookup"><span data-stu-id="f487b-144">Run the project, and copy the URL of the main page into another browser or browser pane, as before.</span></span> <span data-ttu-id="f487b-145">每个页面会要求提供用户名，并从每个页面发送的消息应在这两个浏览器窗格中可见。</span><span class="sxs-lookup"><span data-stu-id="f487b-145">Each page will ask for a username, and messages sent from each page should be visible in both browser panes.</span></span>

<a id="troubleshooting"></a>

## <a name="troubleshooting-errors-encountered-during-upgrading"></a><span data-ttu-id="f487b-146">在升级过程中遇到的错误疑难解答</span><span class="sxs-lookup"><span data-stu-id="f487b-146">Troubleshooting errors encountered during upgrading</span></span>

<span data-ttu-id="f487b-147">本部分介绍在升级过程中可能出现的问题。</span><span class="sxs-lookup"><span data-stu-id="f487b-147">This section describes issues that may arise during upgrading.</span></span> <span data-ttu-id="f487b-148">有关更全面的 SignalR 应用程序可能发生的错误和问题列表，请参阅[SignalR 疑难解答](../testing-and-debugging/troubleshooting.md)。</span><span class="sxs-lookup"><span data-stu-id="f487b-148">For a more comprehensive list of errors and issues that may occur with a SignalR application, see [SignalR Troubleshooting](../testing-and-debugging/troubleshooting.md).</span></span>

### <a name="the-call-is-ambiguous-between-the-following-methods-or-properties"></a><span data-ttu-id="f487b-149">调用之间不明确的以下方法或属性</span><span class="sxs-lookup"><span data-stu-id="f487b-149">'The call is ambiguous between the following methods or properties'</span></span>

<span data-ttu-id="f487b-150">如果对的引用，将发生此错误`Microsoft.AspNet.SignalR.Owin`不会删除。</span><span class="sxs-lookup"><span data-stu-id="f487b-150">This error will occur if a reference to `Microsoft.AspNet.SignalR.Owin` is not removed.</span></span> <span data-ttu-id="f487b-151">此包已弃用;必须删除的引用，必须卸载 SelfHost 包的 1.x 版本。</span><span class="sxs-lookup"><span data-stu-id="f487b-151">This package is deprecated; the reference must be removed and the 1.x version of the SelfHost package must be uninstalled.</span></span>

### <a name="hub-methods-fail-silently"></a><span data-ttu-id="f487b-152">中心方法将以静默方式失败</span><span class="sxs-lookup"><span data-stu-id="f487b-152">Hub methods fail silently</span></span>

<span data-ttu-id="f487b-153">验证你的客户端中的脚本引用是否最新，并的`OwinStartup`属性 (attribute) Startup 类具有正确的类和项目的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="f487b-153">Verify that the script references in your client are up to date, and that the `OwinStartup` attribute for your Startup class has the correct class and assembly names for your project.</span></span> <span data-ttu-id="f487b-154">此外，请试着打开浏览器; 中的中心地址 （/signalr/中心）出现任何错误将提供有关什么问题的详细信息。</span><span class="sxs-lookup"><span data-stu-id="f487b-154">Also, try opening up the hubs address (/signalr/hubs) in your browser; any error that appears will offer more information about what's going wrong.</span></span>
