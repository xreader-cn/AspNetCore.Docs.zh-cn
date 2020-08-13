---
title: Visual Studio 中针对 ASP.NET Core 的开发时 IIS 支持
author: rick-anderson
description: 发现对调试在 Windows Server 上与 IIS 一起运行的 ASP.NET Core 应用的支持。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: ba4d47e0500d7f3287e6a57d10036d774c4b19f4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015660"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="3da10-103">Visual Studio 中针对 ASP.NET Core 的开发时 IIS 支持</span><span class="sxs-lookup"><span data-stu-id="3da10-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="3da10-104">作者：[Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="3da10-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3da10-105">本文介绍了对调试在 Windows Server 上与 IIS 一起运行的 ASP.NET Core 应用的 [Visual Studio](https://visualstudio.microsoft.com) 支持。</span><span class="sxs-lookup"><span data-stu-id="3da10-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="3da10-106">本主题逐步介绍了如何启用此方案并设置项目。</span><span class="sxs-lookup"><span data-stu-id="3da10-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3da10-107">先决条件</span><span class="sxs-lookup"><span data-stu-id="3da10-107">Prerequisites</span></span>

* [<span data-ttu-id="3da10-108">Visual Studio（适用于 Windows）</span><span class="sxs-lookup"><span data-stu-id="3da10-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="3da10-109">ASP.NET 和 Web 开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="3da10-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="3da10-110">.NET Core 跨平台开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="3da10-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="3da10-111">X.509 安全证书（用于 HTTPS 支持）</span><span class="sxs-lookup"><span data-stu-id="3da10-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="3da10-112">启用 IIS</span><span class="sxs-lookup"><span data-stu-id="3da10-112">Enable IIS</span></span>

1. <span data-ttu-id="3da10-113">在 Windows 中，导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="3da10-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="3da10-114">选中“Internet Information Services”复选框。</span><span class="sxs-lookup"><span data-stu-id="3da10-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="3da10-115">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="3da10-115">Select **OK**.</span></span>

<span data-ttu-id="3da10-116">IIS 安装可能需要重启系统。</span><span class="sxs-lookup"><span data-stu-id="3da10-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="3da10-117">配置 IIS</span><span class="sxs-lookup"><span data-stu-id="3da10-117">Configure IIS</span></span>

<span data-ttu-id="3da10-118">IIS 必须具有具备以下配置的网站：</span><span class="sxs-lookup"><span data-stu-id="3da10-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="3da10-119">主机名：通常情况下，使用“主机名”为“`localhost`”的“默认网站”。</span><span class="sxs-lookup"><span data-stu-id="3da10-119">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="3da10-120">不过，任何具有唯一主机名的有效 IIS 网站也都可行。</span><span class="sxs-lookup"><span data-stu-id="3da10-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="3da10-121">**网站绑定**</span><span class="sxs-lookup"><span data-stu-id="3da10-121">**Site Binding**</span></span>
  * <span data-ttu-id="3da10-122">对于要求使用 HTTPS 的应用，请使用证书创建对端口 443 的绑定。</span><span class="sxs-lookup"><span data-stu-id="3da10-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="3da10-123">通常使用的是“IIS Express 开发证书”，但任何有效证书也都可行。</span><span class="sxs-lookup"><span data-stu-id="3da10-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="3da10-124">对于使用 HTTP 的应用，请确认是否存在对端口 80 的绑定，或为新网站创建对端口 80 的绑定。</span><span class="sxs-lookup"><span data-stu-id="3da10-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="3da10-125">对 HTTP 或 HTTPS 使用单一绑定。</span><span class="sxs-lookup"><span data-stu-id="3da10-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="3da10-126">不支持同时绑定到 HTTP 和 HTTPS 端口。</span><span class="sxs-lookup"><span data-stu-id="3da10-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="3da10-127">在 Visual Studio 中启用开发时 IIS 支持</span><span class="sxs-lookup"><span data-stu-id="3da10-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="3da10-128">启动 Visual Studio 安装程序。</span><span class="sxs-lookup"><span data-stu-id="3da10-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="3da10-129">对于计划用于 IIS 开发时支持的 Visual Studio 安装，选择“修改”。</span><span class="sxs-lookup"><span data-stu-id="3da10-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="3da10-130">对于 ASP.NET 和 Web 开发工作负荷，找到并安装“开发时 IIS 支持”组件。</span><span class="sxs-lookup"><span data-stu-id="3da10-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="3da10-131">在工作负荷右侧的“安装详细信息”面板中，“开发时 IIS 支持”下的“可选”部分列出了此组件。</span><span class="sxs-lookup"><span data-stu-id="3da10-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="3da10-132">该组件将安装 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，该模块是运行具有 IIS 的 ASP.NET Core 应用所需的本机 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="3da10-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="3da10-133">配置项目</span><span class="sxs-lookup"><span data-stu-id="3da10-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="3da10-134">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="3da10-134">HTTPS redirection</span></span>

<span data-ttu-id="3da10-135">对于要求使用 HTTPS 的新项目，选中“新建 ASP.NET Core Web 应用”窗口中的“针对 HTTPS 进行配置”复选框。</span><span class="sxs-lookup"><span data-stu-id="3da10-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="3da10-136">选中此复选框会向创建后的应用添加 [HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="3da10-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="3da10-137">对于要求使用 HTTPS 的现有项目，使用 `Startup.Configure` 中的 HTTPS 重定向和 HSTS 中间件。</span><span class="sxs-lookup"><span data-stu-id="3da10-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="3da10-138">有关详细信息，请参阅 <xref:security/enforcing-ssl>。</span><span class="sxs-lookup"><span data-stu-id="3da10-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="3da10-139">对于使用 HTTP 的项目，[HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)不会添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="3da10-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="3da10-140">无需配置应用。</span><span class="sxs-lookup"><span data-stu-id="3da10-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="3da10-141">IIS 启动配置文件</span><span class="sxs-lookup"><span data-stu-id="3da10-141">IIS launch profile</span></span>

<span data-ttu-id="3da10-142">创建新的启动配置文件以添加开发时 IIS 支持：</span><span class="sxs-lookup"><span data-stu-id="3da10-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="3da10-143">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="3da10-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="3da10-144">选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="3da10-144">Select **Properties**.</span></span> <span data-ttu-id="3da10-145">打开“调试”选项卡。</span><span class="sxs-lookup"><span data-stu-id="3da10-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="3da10-146">对于配置文件，请选择“新建”按钮。</span><span class="sxs-lookup"><span data-stu-id="3da10-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="3da10-147">在弹出窗口中将该配置文件命名为“IIS”。</span><span class="sxs-lookup"><span data-stu-id="3da10-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="3da10-148">选择“确定”创建配置文件。</span><span class="sxs-lookup"><span data-stu-id="3da10-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="3da10-149">对于“启动”设置，请从列表中选择“IIS”。</span><span class="sxs-lookup"><span data-stu-id="3da10-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="3da10-150">选中“启动浏览器”复选框并提供终结点 URL。</span><span class="sxs-lookup"><span data-stu-id="3da10-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="3da10-151">如果应用要求使用 HTTPS，使用 HTTPS 终结点 (`https://`)。</span><span class="sxs-lookup"><span data-stu-id="3da10-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="3da10-152">如果应用要求使用 HTTP，使用 HTTP (`http://`) 终结点。</span><span class="sxs-lookup"><span data-stu-id="3da10-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="3da10-153">提供[前面指定的 IIS 配置](#configure-iis)使用的相同主机名和端口，通常为 `localhost`。</span><span class="sxs-lookup"><span data-stu-id="3da10-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="3da10-154">在 URL 结尾处提供应用名称。</span><span class="sxs-lookup"><span data-stu-id="3da10-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="3da10-155">例如，`https://localhost/WebApplication1` (HTTPS) 或 `http://localhost/WebApplication1` (HTTP) 是有效的终结点 URL。</span><span class="sxs-lookup"><span data-stu-id="3da10-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="3da10-156">在“环境变量”部分中，选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="3da10-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="3da10-157">提供“名称”为“`ASPNETCORE_ENVIRONMENT`”且“值”为“`Development`”的环境变量。</span><span class="sxs-lookup"><span data-stu-id="3da10-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="3da10-158">在“Web 服务器设置”区域中，将“应用 URL”设置为用于“启动浏览器”终结点 URL 的相同值。</span><span class="sxs-lookup"><span data-stu-id="3da10-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="3da10-159">对于 Visual Studio 2019 或更高版本中的“托管模型”设置，选择“默认”，以使用项目使用的托管模型。</span><span class="sxs-lookup"><span data-stu-id="3da10-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="3da10-160">如果项目在自己的项目文件中设置 `<AspNetCoreHostingModel>` 属性，使用的是此属性的值（`InProcess` 或 `OutOfProcess`）。</span><span class="sxs-lookup"><span data-stu-id="3da10-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="3da10-161">如果没有设置此属性，使用的是应用的默认（进程内）托管模型。</span><span class="sxs-lookup"><span data-stu-id="3da10-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="3da10-162">如果应用需要不同于常规托管模型的显式托管模型，请根据需要将“托管模型”设置为“`In Process`”或“`Out Of Process`”。</span><span class="sxs-lookup"><span data-stu-id="3da10-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="3da10-163">保存配置文件。</span><span class="sxs-lookup"><span data-stu-id="3da10-163">Save the profile.</span></span>

<span data-ttu-id="3da10-164">如果未使用 Visual Studio，请将启动配置文件手动添加到“属性”文件夹中的 [launchSettings.json](https://json.schemastore.org/launchsettings) 文件内。</span><span class="sxs-lookup"><span data-stu-id="3da10-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="3da10-165">下面的示例展示了如何将配置文件配置为使用 HTTPS 协议：</span><span class="sxs-lookup"><span data-stu-id="3da10-165">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="3da10-166">确认 `applicationUrl` 和 `launchUrl` 终结点是否一致，且是否使用与 IIS 绑定配置相同的协议（HTTP 或 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="3da10-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="3da10-167">运行该项目</span><span class="sxs-lookup"><span data-stu-id="3da10-167">Run the project</span></span>

<span data-ttu-id="3da10-168">以管理员身份运行 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="3da10-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="3da10-169">确认已将生成配置下拉列表设置为“调试”。</span><span class="sxs-lookup"><span data-stu-id="3da10-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="3da10-170">将[“开始调试”按钮](/visualstudio/debugger/debugger-feature-tour)设置为 IIS 配置文件，然后选择此按钮以启动该应用。</span><span class="sxs-lookup"><span data-stu-id="3da10-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="3da10-171">如果不以管理员身份运行，Visual Studio 可能会提示重启。</span><span class="sxs-lookup"><span data-stu-id="3da10-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="3da10-172">如果出现提示，请重启 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="3da10-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="3da10-173">如果使用不受信任的开发证书，则浏览器可能会要求你为不受信任的证书创建异常。</span><span class="sxs-lookup"><span data-stu-id="3da10-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="3da10-174">通过[仅我的代码](/visualstudio/debugger/just-my-code)调试“发布”生成配置，从而导致降低编译器优化体验。</span><span class="sxs-lookup"><span data-stu-id="3da10-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="3da10-175">例如，不会遇到断点。</span><span class="sxs-lookup"><span data-stu-id="3da10-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3da10-176">其他资源</span><span class="sxs-lookup"><span data-stu-id="3da10-176">Additional resources</span></span>

* [<span data-ttu-id="3da10-177">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="3da10-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3da10-178">本文介绍了对调试在 Windows Server 上与 IIS 一起运行的 ASP.NET Core 应用的 [Visual Studio](https://visualstudio.microsoft.com) 支持。</span><span class="sxs-lookup"><span data-stu-id="3da10-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="3da10-179">本主题逐步介绍了如何启用此方案并设置项目。</span><span class="sxs-lookup"><span data-stu-id="3da10-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3da10-180">先决条件</span><span class="sxs-lookup"><span data-stu-id="3da10-180">Prerequisites</span></span>

* [<span data-ttu-id="3da10-181">Visual Studio（适用于 Windows）</span><span class="sxs-lookup"><span data-stu-id="3da10-181">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="3da10-182">ASP.NET 和 Web 开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="3da10-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="3da10-183">.NET Core 跨平台开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="3da10-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="3da10-184">X.509 安全证书（用于 HTTPS 支持）</span><span class="sxs-lookup"><span data-stu-id="3da10-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="3da10-185">启用 IIS</span><span class="sxs-lookup"><span data-stu-id="3da10-185">Enable IIS</span></span>

1. <span data-ttu-id="3da10-186">在 Windows 中，导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="3da10-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="3da10-187">选中“Internet Information Services”复选框。</span><span class="sxs-lookup"><span data-stu-id="3da10-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="3da10-188">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="3da10-188">Select **OK**.</span></span>

<span data-ttu-id="3da10-189">IIS 安装可能需要重启系统。</span><span class="sxs-lookup"><span data-stu-id="3da10-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="3da10-190">配置 IIS</span><span class="sxs-lookup"><span data-stu-id="3da10-190">Configure IIS</span></span>

<span data-ttu-id="3da10-191">IIS 必须具有具备以下配置的网站：</span><span class="sxs-lookup"><span data-stu-id="3da10-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="3da10-192">主机名：通常情况下，使用“主机名”为“`localhost`”的“默认网站”。</span><span class="sxs-lookup"><span data-stu-id="3da10-192">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="3da10-193">不过，任何具有唯一主机名的有效 IIS 网站也都可行。</span><span class="sxs-lookup"><span data-stu-id="3da10-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="3da10-194">**网站绑定**</span><span class="sxs-lookup"><span data-stu-id="3da10-194">**Site Binding**</span></span>
  * <span data-ttu-id="3da10-195">对于要求使用 HTTPS 的应用，请使用证书创建对端口 443 的绑定。</span><span class="sxs-lookup"><span data-stu-id="3da10-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="3da10-196">通常使用的是“IIS Express 开发证书”，但任何有效证书也都可行。</span><span class="sxs-lookup"><span data-stu-id="3da10-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="3da10-197">对于使用 HTTP 的应用，请确认是否存在对端口 80 的绑定，或为新网站创建对端口 80 的绑定。</span><span class="sxs-lookup"><span data-stu-id="3da10-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="3da10-198">对 HTTP 或 HTTPS 使用单一绑定。</span><span class="sxs-lookup"><span data-stu-id="3da10-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="3da10-199">不支持同时绑定到 HTTP 和 HTTPS 端口。</span><span class="sxs-lookup"><span data-stu-id="3da10-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="3da10-200">在 Visual Studio 中启用开发时 IIS 支持</span><span class="sxs-lookup"><span data-stu-id="3da10-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="3da10-201">启动 Visual Studio 安装程序。</span><span class="sxs-lookup"><span data-stu-id="3da10-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="3da10-202">对于计划用于 IIS 开发时支持的 Visual Studio 安装，选择“修改”。</span><span class="sxs-lookup"><span data-stu-id="3da10-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="3da10-203">对于 ASP.NET 和 Web 开发工作负荷，找到并安装“开发时 IIS 支持”组件。</span><span class="sxs-lookup"><span data-stu-id="3da10-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="3da10-204">在工作负荷右侧的“安装详细信息”面板中，“开发时 IIS 支持”下的“可选”部分列出了此组件。</span><span class="sxs-lookup"><span data-stu-id="3da10-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="3da10-205">该组件将安装 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，该模块是运行具有 IIS 的 ASP.NET Core 应用所需的本机 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="3da10-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="3da10-206">配置项目</span><span class="sxs-lookup"><span data-stu-id="3da10-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="3da10-207">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="3da10-207">HTTPS redirection</span></span>

<span data-ttu-id="3da10-208">对于要求使用 HTTPS 的新项目，选中“新建 ASP.NET Core Web 应用”窗口中的“针对 HTTPS 进行配置”复选框。</span><span class="sxs-lookup"><span data-stu-id="3da10-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="3da10-209">选中此复选框会向创建后的应用添加 [HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="3da10-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="3da10-210">对于要求使用 HTTPS 的现有项目，使用 `Startup.Configure` 中的 HTTPS 重定向和 HSTS 中间件。</span><span class="sxs-lookup"><span data-stu-id="3da10-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="3da10-211">有关详细信息，请参阅 <xref:security/enforcing-ssl>。</span><span class="sxs-lookup"><span data-stu-id="3da10-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="3da10-212">对于使用 HTTP 的项目，[HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)不会添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="3da10-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="3da10-213">无需配置应用。</span><span class="sxs-lookup"><span data-stu-id="3da10-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="3da10-214">IIS 启动配置文件</span><span class="sxs-lookup"><span data-stu-id="3da10-214">IIS launch profile</span></span>

<span data-ttu-id="3da10-215">创建新的启动配置文件以添加开发时 IIS 支持：</span><span class="sxs-lookup"><span data-stu-id="3da10-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="3da10-216">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="3da10-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="3da10-217">选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="3da10-217">Select **Properties**.</span></span> <span data-ttu-id="3da10-218">打开“调试”选项卡。</span><span class="sxs-lookup"><span data-stu-id="3da10-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="3da10-219">对于配置文件，请选择“新建”按钮。</span><span class="sxs-lookup"><span data-stu-id="3da10-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="3da10-220">在弹出窗口中将该配置文件命名为“IIS”。</span><span class="sxs-lookup"><span data-stu-id="3da10-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="3da10-221">选择“确定”创建配置文件。</span><span class="sxs-lookup"><span data-stu-id="3da10-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="3da10-222">对于“启动”设置，请从列表中选择“IIS”。</span><span class="sxs-lookup"><span data-stu-id="3da10-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="3da10-223">选中“启动浏览器”复选框并提供终结点 URL。</span><span class="sxs-lookup"><span data-stu-id="3da10-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="3da10-224">如果应用要求使用 HTTPS，使用 HTTPS 终结点 (`https://`)。</span><span class="sxs-lookup"><span data-stu-id="3da10-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="3da10-225">如果应用要求使用 HTTP，使用 HTTP (`http://`) 终结点。</span><span class="sxs-lookup"><span data-stu-id="3da10-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="3da10-226">提供[前面指定的 IIS 配置](#configure-iis)使用的相同主机名和端口，通常为 `localhost`。</span><span class="sxs-lookup"><span data-stu-id="3da10-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="3da10-227">在 URL 结尾处提供应用名称。</span><span class="sxs-lookup"><span data-stu-id="3da10-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="3da10-228">例如，`https://localhost/WebApplication1` (HTTPS) 或 `http://localhost/WebApplication1` (HTTP) 是有效的终结点 URL。</span><span class="sxs-lookup"><span data-stu-id="3da10-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="3da10-229">在“环境变量”部分中，选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="3da10-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="3da10-230">提供“名称”为“`ASPNETCORE_ENVIRONMENT`”且“值”为“`Development`”的环境变量。</span><span class="sxs-lookup"><span data-stu-id="3da10-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="3da10-231">在“Web 服务器设置”区域中，将“应用 URL”设置为用于“启动浏览器”终结点 URL 的相同值。</span><span class="sxs-lookup"><span data-stu-id="3da10-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="3da10-232">对于 Visual Studio 2019 或更高版本中的“托管模型”设置，选择“默认”，以使用项目使用的托管模型。</span><span class="sxs-lookup"><span data-stu-id="3da10-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="3da10-233">如果项目在自己的项目文件中设置 `<AspNetCoreHostingModel>` 属性，使用的是此属性的值（`InProcess` 或 `OutOfProcess`）。</span><span class="sxs-lookup"><span data-stu-id="3da10-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="3da10-234">如果没有设置此属性，使用的是应用的默认（进程外）托管模型。</span><span class="sxs-lookup"><span data-stu-id="3da10-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="3da10-235">如果应用需要不同于常规托管模型的显式托管模型，请根据需要将“托管模型”设置为“`In Process`”或“`Out Of Process`”。</span><span class="sxs-lookup"><span data-stu-id="3da10-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="3da10-236">保存配置文件。</span><span class="sxs-lookup"><span data-stu-id="3da10-236">Save the profile.</span></span>

<span data-ttu-id="3da10-237">如果未使用 Visual Studio，请将启动配置文件手动添加到“属性”文件夹中的 [launchSettings.json](https://json.schemastore.org/launchsettings) 文件内。</span><span class="sxs-lookup"><span data-stu-id="3da10-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="3da10-238">下面的示例展示了如何将配置文件配置为使用 HTTPS 协议：</span><span class="sxs-lookup"><span data-stu-id="3da10-238">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="3da10-239">确认 `applicationUrl` 和 `launchUrl` 终结点是否一致，且是否使用与 IIS 绑定配置相同的协议（HTTP 或 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="3da10-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="3da10-240">运行该项目</span><span class="sxs-lookup"><span data-stu-id="3da10-240">Run the project</span></span>

<span data-ttu-id="3da10-241">以管理员身份运行 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="3da10-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="3da10-242">确认已将生成配置下拉列表设置为“调试”。</span><span class="sxs-lookup"><span data-stu-id="3da10-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="3da10-243">将[“开始调试”按钮](/visualstudio/debugger/debugger-feature-tour)设置为 IIS 配置文件，然后选择此按钮以启动该应用。</span><span class="sxs-lookup"><span data-stu-id="3da10-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="3da10-244">如果不以管理员身份运行，Visual Studio 可能会提示重启。</span><span class="sxs-lookup"><span data-stu-id="3da10-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="3da10-245">如果出现提示，请重启 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="3da10-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="3da10-246">如果使用不受信任的开发证书，则浏览器可能会要求你为不受信任的证书创建异常。</span><span class="sxs-lookup"><span data-stu-id="3da10-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="3da10-247">通过[仅我的代码](/visualstudio/debugger/just-my-code)调试“发布”生成配置，从而导致降低编译器优化体验。</span><span class="sxs-lookup"><span data-stu-id="3da10-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="3da10-248">例如，不会遇到断点。</span><span class="sxs-lookup"><span data-stu-id="3da10-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3da10-249">其他资源</span><span class="sxs-lookup"><span data-stu-id="3da10-249">Additional resources</span></span>

* [<span data-ttu-id="3da10-250">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="3da10-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
