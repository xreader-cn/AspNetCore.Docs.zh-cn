---
title: 用于 ASP.NET Core Blazor 的工具
author: guardrex
description: 了解可用于构建 Blazor 应用程序的工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
no-loc:
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
ms.openlocfilehash: d1626fe753782d524bf75c398c11235c3110633a
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762147"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="3a24f-103">用于 ASP.NET Core Blazor 的工具</span><span class="sxs-lookup"><span data-stu-id="3a24f-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="3a24f-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3a24f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="3a24f-105">安装带有“ASP.NET 和 Web 开发”工作负载的最新版本 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="3a24f-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="3a24f-106">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="3a24f-106">Create a new project.</span></span>

1. <span data-ttu-id="3a24f-107">选择“Blazor 应用”。</span><span class="sxs-lookup"><span data-stu-id="3a24f-107">Select **Blazor App**.</span></span> <span data-ttu-id="3a24f-108">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="3a24f-108">Select **Next**.</span></span>

1. <span data-ttu-id="3a24f-109">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="3a24f-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3a24f-110">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="3a24f-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3a24f-111">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="3a24f-111">Select **Create**.</span></span>

1. <span data-ttu-id="3a24f-112">若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="3a24f-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3a24f-113">若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="3a24f-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3a24f-114">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="3a24f-114">Select **Create**.</span></span>

   <span data-ttu-id="3a24f-115">有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。 </span><span class="sxs-lookup"><span data-stu-id="3a24f-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3a24f-116">按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="3a24f-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="3a24f-117">有关信任 ASP.NET Core HTTPS 开发证书的详细信息，请参阅 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>。</span><span class="sxs-lookup"><span data-stu-id="3a24f-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="3a24f-118">安装最新版本的 [.NET Core SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="3a24f-118">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="3a24f-119">如果以前安装了该 SDK，可以通过在命令行界面中执行以下命令来确定已安装的版本：</span><span class="sxs-lookup"><span data-stu-id="3a24f-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="3a24f-120">安装最新版本的 [Visual Studio Code](https://code.visualstudio.com)。</span><span class="sxs-lookup"><span data-stu-id="3a24f-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="3a24f-121">安装最新的 [C# for Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。</span><span class="sxs-lookup"><span data-stu-id="3a24f-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="3a24f-122">若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3a24f-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="3a24f-123">若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3a24f-123">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="3a24f-124">有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。 </span><span class="sxs-lookup"><span data-stu-id="3a24f-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3a24f-125">在 Visual Studio Code 中打开 `WebApplication1` 文件夹，</span><span class="sxs-lookup"><span data-stu-id="3a24f-125">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="3a24f-126">IDE 要求添加资产以用于生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="3a24f-126">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="3a24f-127">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="3a24f-127">Select **Yes**.</span></span>

1. <span data-ttu-id="3a24f-128">按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="3a24f-128">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="3a24f-129">信任开发证书</span><span class="sxs-lookup"><span data-stu-id="3a24f-129">Trust a development certificate</span></span>

<span data-ttu-id="3a24f-130">Linux 上没有用于信任证书的集中途径。</span><span class="sxs-lookup"><span data-stu-id="3a24f-130">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="3a24f-131">通常采用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="3a24f-131">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="3a24f-132">在浏览器的排除列表中排除应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="3a24f-132">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="3a24f-133">信任 `localhost` 的所有自签名证书。</span><span class="sxs-lookup"><span data-stu-id="3a24f-133">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="3a24f-134">将证书添加到浏览器的受信任证书列表。</span><span class="sxs-lookup"><span data-stu-id="3a24f-134">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="3a24f-135">有关详细信息，请参阅浏览器制造商和 Linux 发行版提供的指南。</span><span class="sxs-lookup"><span data-stu-id="3a24f-135">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="3a24f-136">安装 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="3a24f-136">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="3a24f-137">选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。</span><span class="sxs-lookup"><span data-stu-id="3a24f-137">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="3a24f-138">在边栏中，选择“Web 和控制台” > “应用”。 </span><span class="sxs-lookup"><span data-stu-id="3a24f-138">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="3a24f-139">若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="3a24f-139">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3a24f-140">若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="3a24f-140">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3a24f-141">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="3a24f-141">Select **Next**.</span></span>

   <span data-ttu-id="3a24f-142">有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。 </span><span class="sxs-lookup"><span data-stu-id="3a24f-142">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3a24f-143">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="3a24f-143">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="3a24f-144">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="3a24f-144">Select **Next**.</span></span>

1. <span data-ttu-id="3a24f-145">在“项目名称”字段中，将应用命名为 `WebApplication1`。</span><span class="sxs-lookup"><span data-stu-id="3a24f-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="3a24f-146">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="3a24f-146">Select **Create**.</span></span>

1. <span data-ttu-id="3a24f-147">选择“运行” > “启动而不调试”以不使用调试程序运行应用 。</span><span class="sxs-lookup"><span data-stu-id="3a24f-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="3a24f-148">使用“运行” > “开始调试”运行应用，或者使用“运行 (&#9654;)”按钮通过调试程序运行应用。 </span><span class="sxs-lookup"><span data-stu-id="3a24f-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="3a24f-149">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="3a24f-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="3a24f-150">信任证书需要使用用户密码和密钥链密码。</span><span class="sxs-lookup"><span data-stu-id="3a24f-150">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="3a24f-151">有关信任 ASP.NET Core HTTPS 开发证书的详细信息，请参阅 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>。</span><span class="sxs-lookup"><span data-stu-id="3a24f-151">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end
