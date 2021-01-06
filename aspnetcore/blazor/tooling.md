---
title: 用于 ASP.NET Core Blazor 的工具
author: guardrex
description: 了解可用于构建 Blazor 应用程序的工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 4813668f5278473fbaae36d572e69700b3fe771a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97764232"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="50df3-103">用于 ASP.NET Core Blazor 的工具</span><span class="sxs-lookup"><span data-stu-id="50df3-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="50df3-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="50df3-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="50df3-105">安装带有“ASP.NET 和 Web 开发”工作负载的最新版本 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="50df3-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="50df3-106">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="50df3-106">Create a new project.</span></span>

1. <span data-ttu-id="50df3-107">选择“Blazor 应用”。</span><span class="sxs-lookup"><span data-stu-id="50df3-107">Select **Blazor App**.</span></span> <span data-ttu-id="50df3-108">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="50df3-108">Select **Next**.</span></span>

1. <span data-ttu-id="50df3-109">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="50df3-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="50df3-110">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="50df3-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="50df3-111">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="50df3-111">Select **Create**.</span></span>

1. <span data-ttu-id="50df3-112">若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="50df3-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="50df3-113">若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="50df3-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="50df3-114">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="50df3-114">Select **Create**.</span></span>

   <span data-ttu-id="50df3-115">对于托管的 Blazor WebAssembly 体验，选择“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="50df3-115">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="50df3-116">若要了解 Blazor WebAssembly（独立和托管）和 Blazor Server这两个 Blazor 托管模型，请参阅 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="50df3-116">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="50df3-117">按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="50df3-117">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="50df3-118">有关信任 ASP.NET Core HTTPS 开发证书的详细信息，请参阅 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>。</span><span class="sxs-lookup"><span data-stu-id="50df3-118">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="50df3-119">安装最新版本的 [.NET Core SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="50df3-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="50df3-120">如果以前安装了该 SDK，可以通过在命令行界面中执行以下命令来确定已安装的版本：</span><span class="sxs-lookup"><span data-stu-id="50df3-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="50df3-121">安装最新版本的 [Visual Studio Code](https://code.visualstudio.com)。</span><span class="sxs-lookup"><span data-stu-id="50df3-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="50df3-122">安装最新的 [C# for Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。</span><span class="sxs-lookup"><span data-stu-id="50df3-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="50df3-123">若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="50df3-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="50df3-124">对于托管的 Blazor WebAssembly 体验，请向命令添加托管选项 (`-ho` 或 `--hosted`) 选项：</span><span class="sxs-lookup"><span data-stu-id="50df3-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="50df3-125">若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="50df3-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="50df3-126">若要了解 Blazor WebAssembly（独立和托管）和 Blazor Server这两个 Blazor 托管模型，请参阅 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="50df3-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="50df3-127">在 Visual Studio Code 中打开 `WebApplication1` 文件夹，</span><span class="sxs-lookup"><span data-stu-id="50df3-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="50df3-128">IDE 要求添加资产以用于生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="50df3-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="50df3-129">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="50df3-129">Select **Yes**.</span></span>

1. <span data-ttu-id="50df3-130">按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="50df3-130">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="50df3-131">信任开发证书</span><span class="sxs-lookup"><span data-stu-id="50df3-131">Trust a development certificate</span></span>

<span data-ttu-id="50df3-132">Linux 上没有用于信任证书的集中途径。</span><span class="sxs-lookup"><span data-stu-id="50df3-132">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="50df3-133">通常采用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="50df3-133">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="50df3-134">在浏览器的排除列表中排除应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="50df3-134">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="50df3-135">信任 `localhost` 的所有自签名证书。</span><span class="sxs-lookup"><span data-stu-id="50df3-135">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="50df3-136">将证书添加到浏览器的受信任证书列表。</span><span class="sxs-lookup"><span data-stu-id="50df3-136">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="50df3-137">有关详细信息，请参阅浏览器制造商和 Linux 发行版提供的指南。</span><span class="sxs-lookup"><span data-stu-id="50df3-137">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="50df3-138">安装 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="50df3-138">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="50df3-139">选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。</span><span class="sxs-lookup"><span data-stu-id="50df3-139">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="50df3-140">在边栏中，选择“Web 和控制台” > “应用”。 </span><span class="sxs-lookup"><span data-stu-id="50df3-140">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="50df3-141">若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="50df3-141">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="50df3-142">若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="50df3-142">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="50df3-143">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="50df3-143">Select **Next**.</span></span>

   <span data-ttu-id="50df3-144">若要了解 Blazor WebAssembly（独立和托管）和 Blazor Server这两个 Blazor 托管模型，请参阅 <xref:blazor/hosting-models> 。</span><span class="sxs-lookup"><span data-stu-id="50df3-144">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="50df3-145">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="50df3-145">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="50df3-146">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="50df3-146">Select **Next**.</span></span>

1. <span data-ttu-id="50df3-147">对于托管的 Blazor WebAssembly 体验，选择“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="50df3-147">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="50df3-148">在“项目名称”字段中，将应用命名为 `WebApplication1`。</span><span class="sxs-lookup"><span data-stu-id="50df3-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="50df3-149">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="50df3-149">Select **Create**.</span></span>

1. <span data-ttu-id="50df3-150">选择“运行” > “启动而不调试”以不使用调试程序运行应用 。</span><span class="sxs-lookup"><span data-stu-id="50df3-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="50df3-151">使用“运行” > “开始调试”运行应用，或者使用“运行 (&#9654;)”按钮通过调试程序运行应用。 </span><span class="sxs-lookup"><span data-stu-id="50df3-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="50df3-152">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="50df3-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="50df3-153">信任证书需要使用用户密码和密钥链密码。</span><span class="sxs-lookup"><span data-stu-id="50df3-153">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="50df3-154">有关信任 ASP.NET Core HTTPS 开发证书的详细信息，请参阅 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>。</span><span class="sxs-lookup"><span data-stu-id="50df3-154">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a><span data-ttu-id="50df3-155">使用适用于跨平台 Blazor 开发的 Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="50df3-155">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="50df3-156">[Visual Studio Code](https://code.visualstudio.com/) 是一个开源的跨平台集成开发环境 (IDE)，可用于开发 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="50df3-156">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="50df3-157">使用 .NET CLI 创建新的 Blazor 应用，来使用 Visual Studio Code 进行开发。</span><span class="sxs-lookup"><span data-stu-id="50df3-157">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="50df3-158">有关详细信息，请选择[本文的 Linux 版本](/aspnet/core/blazor/tooling?pivots=linux)。</span><span class="sxs-lookup"><span data-stu-id="50df3-158">For more information, see the [Linux version of this article](/aspnet/core/blazor/tooling?pivots=linux).</span></span>

## <a name="no-locblazor-template-options"></a><span data-ttu-id="50df3-159">Blazor 模板选项</span><span class="sxs-lookup"><span data-stu-id="50df3-159">Blazor template options</span></span>

<span data-ttu-id="50df3-160">Blazor 框架提供了一些模板，用于为每个 Blazor 托管模型（共两个）创建新应用。</span><span class="sxs-lookup"><span data-stu-id="50df3-160">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="50df3-161">模板用于创建新的 Blazor 项目和解决方案，而不考虑您选择用于 Blazor 开发的工具（Visual Studio、Visual Studio for Mac、Visual Studio Code 还是 .NET CLI）：</span><span class="sxs-lookup"><span data-stu-id="50df3-161">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="50df3-162">Blazor WebAssembly 项目模板：`blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="50df3-162">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="50df3-163">Blazor Server项目模板：`blazorserver`</span><span class="sxs-lookup"><span data-stu-id="50df3-163">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="50df3-164">有关 Blazor 的托管模型的详细信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="50df3-164">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="50df3-165">通过将 help 选项（`-h` 或 `--help`）传递给命令行界面中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI 命令，可使用模板选项：</span><span class="sxs-lookup"><span data-stu-id="50df3-165">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
