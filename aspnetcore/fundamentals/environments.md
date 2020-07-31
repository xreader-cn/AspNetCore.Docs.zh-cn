---
title: 在 ASP.NET Core 中使用多个环境
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中控制多个环境的应用行为。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330604"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="a9257-103">在 ASP.NET Core 中使用多个环境</span><span class="sxs-lookup"><span data-stu-id="a9257-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9257-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="a9257-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="a9257-105">ASP.NET Core 基于使用环境变量的运行时环境配置应用行为。</span><span class="sxs-lookup"><span data-stu-id="a9257-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="a9257-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a9257-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="a9257-107">环境</span><span class="sxs-lookup"><span data-stu-id="a9257-107">Environments</span></span>

<span data-ttu-id="a9257-108">为了确定运行时环境，ASP.NET Core 从以下环境变量中读取信息：</span><span class="sxs-lookup"><span data-stu-id="a9257-108">To determine the runtime environment, ASP.NET Core reads from the following environment variables:</span></span>

1. [<span data-ttu-id="a9257-109">DOTNET_ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="a9257-109">DOTNET_ENVIRONMENT</span></span>](xref:fundamentals/configuration/index#default-host-configuration)
1. <span data-ttu-id="a9257-110">`ASPNETCORE_ENVIRONMENT`（当调用 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 时）。</span><span class="sxs-lookup"><span data-stu-id="a9257-110">`ASPNETCORE_ENVIRONMENT` when <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> is called.</span></span> <span data-ttu-id="a9257-111">默认 ASP.NET Core Web 应用模板调用 `ConfigureWebHostDefaults`。</span><span class="sxs-lookup"><span data-stu-id="a9257-111">The default ASP.NET Core web app templates call `ConfigureWebHostDefaults`.</span></span> <span data-ttu-id="a9257-112">`ASPNETCORE_ENVIRONMENT` 值替代 `DOTNET_ENVIRONMENT`。</span><span class="sxs-lookup"><span data-stu-id="a9257-112">The `ASPNETCORE_ENVIRONMENT` value overrides `DOTNET_ENVIRONMENT`.</span></span>

<span data-ttu-id="a9257-113">`IHostEnvironment.EnvironmentName` 可以设置为任意值，但是框架提供了下列值：</span><span class="sxs-lookup"><span data-stu-id="a9257-113">`IHostEnvironment.EnvironmentName` can be set to any value, but the following values are provided by the framework:</span></span>

* <span data-ttu-id="a9257-114"><xref:Microsoft.Extensions.Hosting.Environments.Development>：[launchSettings.json](#lsj) 文件将本地计算机上的 `ASPNETCORE_ENVIRONMENT` 设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="a9257-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : The [launchSettings.json](#lsj) file sets `ASPNETCORE_ENVIRONMENT` to `Development` on the local machine.</span></span>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="a9257-115"><xref:Microsoft.Extensions.Hosting.Environments.Production>：没有设置 `DOTNET_ENVIRONMENT` 和 `ASPNETCORE_ENVIRONMENT` 时的默认值。</span><span class="sxs-lookup"><span data-stu-id="a9257-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : The default if `DOTNET_ENVIRONMENT` and `ASPNETCORE_ENVIRONMENT` have not been set.</span></span>

<span data-ttu-id="a9257-116">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="a9257-116">The following code:</span></span>

* <span data-ttu-id="a9257-117">当 `ASPNETCORE_ENVIRONMENT` 设置为 `Development` 时，调用 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)。</span><span class="sxs-lookup"><span data-stu-id="a9257-117">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="a9257-118">当 `ASPNETCORE_ENVIRONMENT` 的值设置为 `Staging`、`Production` 或 `Staging_2` 时，调用 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)。</span><span class="sxs-lookup"><span data-stu-id="a9257-118">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set to `Staging`, `Production`, or  `Staging_2`.</span></span>
* <span data-ttu-id="a9257-119">将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 `Startup.Configure` 中。</span><span class="sxs-lookup"><span data-stu-id="a9257-119">Injects <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="a9257-120">当应用仅需为几个代码差异最小的环境调整 `Startup.Configure` 时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="a9257-120">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>
* <span data-ttu-id="a9257-121">类似于由 ASP.NET Core 模板生成的代码。</span><span class="sxs-lookup"><span data-stu-id="a9257-121">Is similar to the code generated by the ASP.NET Core templates.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="a9257-122">[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)使用 [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) 的值在元素中包含或排除标记：</span><span class="sxs-lookup"><span data-stu-id="a9257-122">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="a9257-123">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)中的[“关于”页](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml)包括前面的标记，并显示 `IWebHostEnvironment.EnvironmentName` 的值。</span><span class="sxs-lookup"><span data-stu-id="a9257-123">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) includes the preceding markup and displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

<span data-ttu-id="a9257-124">在 Windows 和 macOS 上，环境变量和值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="a9257-124">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="a9257-125">默认情况下，Linux 环境变量和值区分大小写。</span><span class="sxs-lookup"><span data-stu-id="a9257-125">Linux environment variables and values are **case-sensitive** by default.</span></span>

### <a name="create-environmentssample"></a><span data-ttu-id="a9257-126">创建 EnvironmentsSample</span><span class="sxs-lookup"><span data-stu-id="a9257-126">Create EnvironmentsSample</span></span>

<span data-ttu-id="a9257-127">本文档中使用的[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)基于名为“EnvironmentsSample”的 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="a9257-127">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) used in this document is based on a Razor Pages project named *EnvironmentsSample*.</span></span>

<span data-ttu-id="a9257-128">以下代码创建并运行名为“EnvironmentsSample”的 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="a9257-128">The following code creates and runs a web app named *EnvironmentsSample*:</span></span>

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

<span data-ttu-id="a9257-129">当应用运行时，它会显示下面的部分输出内容：</span><span class="sxs-lookup"><span data-stu-id="a9257-129">When the app runs, it displays some of the following output:</span></span>

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a><span data-ttu-id="a9257-130">开发和 launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="a9257-130">Development and launchSettings.json</span></span>

<span data-ttu-id="a9257-131">开发环境可以启用不应该在生产中公开的功能。</span><span class="sxs-lookup"><span data-stu-id="a9257-131">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="a9257-132">例如，ASP.NET Core 模板在开发环境中启用了[开发人员异常页](xref:fundamentals/error-handling#developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="a9257-132">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="a9257-133">本地计算机开发环境可以在项目的 Properties\launchSettings.json 文件中设置。</span><span class="sxs-lookup"><span data-stu-id="a9257-133">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="a9257-134">在 launchSettings.json 中设置的环境值替代在系统环境中设置的值。</span><span class="sxs-lookup"><span data-stu-id="a9257-134">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="a9257-135">launchSettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="a9257-135">The *launchSettings.json* file:</span></span>

* <span data-ttu-id="a9257-136">仅在本地开发计算机上使用。</span><span class="sxs-lookup"><span data-stu-id="a9257-136">Is only used on the local development machine.</span></span>
* <span data-ttu-id="a9257-137">未部署。</span><span class="sxs-lookup"><span data-stu-id="a9257-137">Is not deployed.</span></span>
* <span data-ttu-id="a9257-138">包含配置文件设置。</span><span class="sxs-lookup"><span data-stu-id="a9257-138">contains profile settings.</span></span>

<span data-ttu-id="a9257-139">下面的 JSON 显示名为“EnvironmentsSample”的 ASP.NET Core Web 项目的 launchSettings.json 文件，此项目是通过 Visual Studio 或 `dotnet new` 创建的：</span><span class="sxs-lookup"><span data-stu-id="a9257-139">The following JSON shows the *launchSettings.json* file for an ASP.NET Core web projected named *EnvironmentsSample* created with Visual Studio or `dotnet new`:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

<span data-ttu-id="a9257-140">前面的标记包含两个配置文件：</span><span class="sxs-lookup"><span data-stu-id="a9257-140">The preceding markup contains two profiles:</span></span>

* <span data-ttu-id="a9257-141">`IIS Express`：在 Visual Studio 中启动应用时使用的默认配置文件。</span><span class="sxs-lookup"><span data-stu-id="a9257-141">`IIS Express`: The default profile used when launching the app from Visual Studio.</span></span> <span data-ttu-id="a9257-142">由于 `"commandName"` 键的值为 `"IISExpress"`，因此 [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) 是 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="a9257-142">The `"commandName"` key has the value `"IISExpress"`, therefore, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) is the web server.</span></span> <span data-ttu-id="a9257-143">可以将启动配置文件设置为此项目或所包含的其他任何配置文件。</span><span class="sxs-lookup"><span data-stu-id="a9257-143">You can set the launch profile to the project or any other profile included.</span></span> <span data-ttu-id="a9257-144">例如，在下图中，选择项目名称会启动 [Kestrel Web 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="a9257-144">For example, in the image below, selecting the project name launches the [Kestrel web server](xref:fundamentals/servers/kestrel).</span></span>

  ![使用菜单启动 IIS Express](environments/_static/iisx2.png)
* <span data-ttu-id="a9257-146">`EnvironmentsSample`：配置文件名称是项目名称。</span><span class="sxs-lookup"><span data-stu-id="a9257-146">`EnvironmentsSample`: The profile name is the project name.</span></span> <span data-ttu-id="a9257-147">当使用 `dotnet run` 启动应用时，默认使用此配置文件。</span><span class="sxs-lookup"><span data-stu-id="a9257-147">This profile is used by default when launching the app with `dotnet run`.</span></span>  <span data-ttu-id="a9257-148">由于 `"commandName"` 键的值为 `"Project"`，因此启动的是 [Kestrel Web 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="a9257-148">The `"commandName"` key has the value `"Project"`, therefore, the [Kestrel web server](xref:fundamentals/servers/kestrel) is launched.</span></span>

<span data-ttu-id="a9257-149">`commandName` 的值可指定要启动的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="a9257-149">The value of `commandName` can specify the web server to launch.</span></span> <span data-ttu-id="a9257-150">`commandName` 可为以下任一项：</span><span class="sxs-lookup"><span data-stu-id="a9257-150">`commandName` can be any one of the following:</span></span>

* <span data-ttu-id="a9257-151">`IISExpress`：启动 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="a9257-151">`IISExpress` : Launches IIS Express.</span></span>
* <span data-ttu-id="a9257-152">`IIS`：不启动任何 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="a9257-152">`IIS` : No web server launched.</span></span> <span data-ttu-id="a9257-153">IIS 预计可用。</span><span class="sxs-lookup"><span data-stu-id="a9257-153">IIS is expected to be available.</span></span>
* <span data-ttu-id="a9257-154">`Project`：启动 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="a9257-154">`Project` : Launches Kestrel.</span></span>

<span data-ttu-id="a9257-155">Visual Studio 项目属性“调试”选项卡提供了 GUI 来编辑 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="a9257-155">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file.</span></span> <span data-ttu-id="a9257-156">在 Web 服务器重新启动之前，对项目配置文件所做的更改可能不会生效。</span><span class="sxs-lookup"><span data-stu-id="a9257-156">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="a9257-157">必须重新启动 Kestrel 才能检测到对其环境所做的更改。</span><span class="sxs-lookup"><span data-stu-id="a9257-157">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

![项目属性设置环境变量](environments/_static/project-properties-debug.png)

<span data-ttu-id="a9257-159">以下 launchSettings.json 文件包含多个配置文件：</span><span class="sxs-lookup"><span data-stu-id="a9257-159">The following *launchSettings.json* file contains multiple profiles:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

<span data-ttu-id="a9257-160">可通过以下方式来选择配置文件：</span><span class="sxs-lookup"><span data-stu-id="a9257-160">Profiles can be selected:</span></span>

* <span data-ttu-id="a9257-161">在 Visual Studio UI 中。</span><span class="sxs-lookup"><span data-stu-id="a9257-161">From the Visual Studio UI.</span></span>
* <span data-ttu-id="a9257-162">在命令行界面中使用 [`dotnet run`](/dotnet/core/tools/dotnet-run) 命令，并将 `--launch-profile` 选项设置为配置文件名称。</span><span class="sxs-lookup"><span data-stu-id="a9257-162">Using the [`dotnet run`](/dotnet/core/tools/dotnet-run) command in a command shell with the `--launch-profile` option set to the profile's name.</span></span> <span data-ttu-id="a9257-163">这种方法只支持 Kestrel 配置文件。</span><span class="sxs-lookup"><span data-stu-id="a9257-163">*This approach only supports Kestrel profiles.*</span></span>

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> <span data-ttu-id="a9257-164">launchSettings.json 不应存储机密。</span><span class="sxs-lookup"><span data-stu-id="a9257-164">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="a9257-165">[机密管理器工具](xref:security/app-secrets)可用于存储本地开发的机密。</span><span class="sxs-lookup"><span data-stu-id="a9257-165">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="a9257-166">使用 [Visual Studio Code](https://code.visualstudio.com/) 时，可以在 .vscode/launch.json 文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a9257-166">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="a9257-167">下面的示例设置了多个[主机配置值环境变量](xref:fundamentals/host/web-host#host-configuration-values)：</span><span class="sxs-lookup"><span data-stu-id="a9257-167">The following example sets several [Host configuration values environment variables](xref:fundamentals/host/web-host#host-configuration-values):</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

<span data-ttu-id="a9257-168">.vscode/launch.json 文件仅由 Visual Studio Code 使用。</span><span class="sxs-lookup"><span data-stu-id="a9257-168">The *.vscode/launch.json* file is only used by Visual Studio Code.</span></span>

### <a name="production"></a><span data-ttu-id="a9257-169">生产</span><span class="sxs-lookup"><span data-stu-id="a9257-169">Production</span></span>

<span data-ttu-id="a9257-170">生产环境应配置为最大限度地提高安全性、[性能](xref:performance/performance-best-practices)和应用可靠性。</span><span class="sxs-lookup"><span data-stu-id="a9257-170">The production environment should be configured to maximize security, [performance](xref:performance/performance-best-practices), and application robustness.</span></span> <span data-ttu-id="a9257-171">不同于开发的一些通用设置包括：</span><span class="sxs-lookup"><span data-stu-id="a9257-171">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="a9257-172">[缓存](xref:performance/caching/memory)。</span><span class="sxs-lookup"><span data-stu-id="a9257-172">[Caching](xref:performance/caching/memory).</span></span>
* <span data-ttu-id="a9257-173">客户端资源被捆绑和缩小，并可能从 CDN 提供。</span><span class="sxs-lookup"><span data-stu-id="a9257-173">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="a9257-174">已禁用诊断错误页。</span><span class="sxs-lookup"><span data-stu-id="a9257-174">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="a9257-175">已启用友好错误页。</span><span class="sxs-lookup"><span data-stu-id="a9257-175">Friendly error pages enabled.</span></span>
* <span data-ttu-id="a9257-176">已启用生产[记录](xref:fundamentals/logging/index)和监视。</span><span class="sxs-lookup"><span data-stu-id="a9257-176">Production [logging](xref:fundamentals/logging/index) and monitoring enabled.</span></span> <span data-ttu-id="a9257-177">例如，使用 [Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="a9257-177">For example, using [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="a9257-178">设置环境</span><span class="sxs-lookup"><span data-stu-id="a9257-178">Set the environment</span></span>

<span data-ttu-id="a9257-179">通常，可以使用环境变量或平台设置来设置用于测试的特定环境。</span><span class="sxs-lookup"><span data-stu-id="a9257-179">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="a9257-180">如果未设置环境，默认值为 `Production`，这会禁用大多数调试功能。</span><span class="sxs-lookup"><span data-stu-id="a9257-180">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="a9257-181">设置环境的方法取决于操作系统。</span><span class="sxs-lookup"><span data-stu-id="a9257-181">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="a9257-182">构建主机时，应用读取的最后一个环境设置将决定应用的环境。</span><span class="sxs-lookup"><span data-stu-id="a9257-182">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="a9257-183">应用运行时无法更改应用的环境。</span><span class="sxs-lookup"><span data-stu-id="a9257-183">The app's environment can't be changed while the app is running.</span></span>

<span data-ttu-id="a9257-184">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)中的[“关于”页](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml)显示 `IWebHostEnvironment.EnvironmentName` 的值。</span><span class="sxs-lookup"><span data-stu-id="a9257-184">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="a9257-185">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="a9257-185">Azure App Service</span></span>

<span data-ttu-id="a9257-186">若要在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中设置环境，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="a9257-186">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="a9257-187">从“应用服务”边栏选项卡中选择应用。</span><span class="sxs-lookup"><span data-stu-id="a9257-187">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="a9257-188">在“设置”组中，选择“配置”边栏选项卡 。</span><span class="sxs-lookup"><span data-stu-id="a9257-188">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="a9257-189">在“应用程序设置”选项卡中，选择“新建应用程序设置”。 </span><span class="sxs-lookup"><span data-stu-id="a9257-189">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="a9257-190">在“添加/编辑应用程序设置”窗口中，在“名称”中提供 `ASPNETCORE_ENVIRONMENT`。</span><span class="sxs-lookup"><span data-stu-id="a9257-190">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="a9257-191">在“值”中提供环境（例如 `Staging`）。</span><span class="sxs-lookup"><span data-stu-id="a9257-191">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="a9257-192">交换部署槽位时，如果希望环境设置保持当前槽位，请选中“部署槽位设置”复选框。</span><span class="sxs-lookup"><span data-stu-id="a9257-192">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="a9257-193">有关详细信息，请参阅 Azure 文档中的[在 Azure 应用服务中设置过渡环境](/azure/app-service/web-sites-staged-publishing)。</span><span class="sxs-lookup"><span data-stu-id="a9257-193">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="a9257-194">选择“确定”以关闭“添加/编辑应用程序设置”窗口。 </span><span class="sxs-lookup"><span data-stu-id="a9257-194">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="a9257-195">选择“配置”边栏选项卡顶部的“保存” 。</span><span class="sxs-lookup"><span data-stu-id="a9257-195">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="a9257-196">在 Azure 门户中添加、更改或删除应用设置后，Azure 应用服务自动重启应用。</span><span class="sxs-lookup"><span data-stu-id="a9257-196">Azure App Service automatically restarts the app after an app setting is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="a9257-197">Windows</span><span class="sxs-lookup"><span data-stu-id="a9257-197">Windows</span></span>

<span data-ttu-id="a9257-198">launchSettings.json 中的环境值替代在系统环境中设置的值。</span><span class="sxs-lookup"><span data-stu-id="a9257-198">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="a9257-199">若要在使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动该应用时为当前会话设置 `ASPNETCORE_ENVIRONMENT`，则使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="a9257-199">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="a9257-200">**命令提示符**</span><span class="sxs-lookup"><span data-stu-id="a9257-200">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

<span data-ttu-id="a9257-201">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="a9257-201">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

<span data-ttu-id="a9257-202">前面的命令只为通过此命令窗口启动的进程设置 `ASPNETCORE_ENVIRONMENT`。</span><span class="sxs-lookup"><span data-stu-id="a9257-202">The preceding command sets `ASPNETCORE_ENVIRONMENT` only for processes launched from that command window.</span></span>

<span data-ttu-id="a9257-203">若要在 Windows 中全局设置值，请采用下列两种方法之一：</span><span class="sxs-lookup"><span data-stu-id="a9257-203">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="a9257-204">依次打开“控制面板”>“系统”>“高级系统设置”，再添加或编辑“`ASPNETCORE_ENVIRONMENT`”值：</span><span class="sxs-lookup"><span data-stu-id="a9257-204">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![系统高级属性](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 环境变量](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="a9257-207">打开管理命令提示符并运行 `setx` 命令，或打开管理 PowerShell 命令提示符并运行 `[Environment]::SetEnvironmentVariable`：</span><span class="sxs-lookup"><span data-stu-id="a9257-207">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="a9257-208">**命令提示符**</span><span class="sxs-lookup"><span data-stu-id="a9257-208">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  <span data-ttu-id="a9257-209">`/M` 开关指明，在系统一级设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a9257-209">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="a9257-210">如果未使用 `/M` 开关，就会为用户帐户设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a9257-210">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="a9257-211">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="a9257-211">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  <span data-ttu-id="a9257-212">`Machine` 选项值指明，在系统一级设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a9257-212">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="a9257-213">如果将选项值更改为 `User`，就会为用户帐户设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a9257-213">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="a9257-214">如果全局设置 `ASPNETCORE_ENVIRONMENT` 环境变量，它就会对在值设置后打开的任何命令窗口中对 `dotnet run` 起作用。</span><span class="sxs-lookup"><span data-stu-id="a9257-214">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span> <span data-ttu-id="a9257-215">launchSettings.json 中的环境值替代在系统环境中设置的值。</span><span class="sxs-lookup"><span data-stu-id="a9257-215">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="a9257-216">**web.config**</span><span class="sxs-lookup"><span data-stu-id="a9257-216">**web.config**</span></span>

<span data-ttu-id="a9257-217">若要使用 web.config 设置 `ASPNETCORE_ENVIRONMENT` 环境变量，请参阅 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>的“设置环境变量”部分。</span><span class="sxs-lookup"><span data-stu-id="a9257-217">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="a9257-218">**项目文件或发布配置文件**</span><span class="sxs-lookup"><span data-stu-id="a9257-218">**Project file or publish profile**</span></span>

<span data-ttu-id="a9257-219">**对于 Windows IIS 部署：** 将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="a9257-219">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="a9257-220">此方法在发布项目时设置 web.config 中的环境：</span><span class="sxs-lookup"><span data-stu-id="a9257-220">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="a9257-221">**每个 IIS 应用程序池**</span><span class="sxs-lookup"><span data-stu-id="a9257-221">**Per IIS Application Pool**</span></span>

<span data-ttu-id="a9257-222">若要为在独立应用池中运行的应用设置 `ASPNETCORE_ENVIRONMENT` 环境变量（IIS 10.0 或更高版本支持此操作），请参阅[环境变量 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="a9257-222">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="a9257-223">为应用池设置 `ASPNETCORE_ENVIRONMENT` 环境变量后，它的值会替代系统级设置。</span><span class="sxs-lookup"><span data-stu-id="a9257-223">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

<span data-ttu-id="a9257-224">在 IIS 中托管应用并添加或更改 `ASPNETCORE_ENVIRONMENT` 环境变量时，请采用下列方法之一，让新值可供应用拾取：</span><span class="sxs-lookup"><span data-stu-id="a9257-224">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>

* <span data-ttu-id="a9257-225">在命令提示符处依次执行 `net stop was /y` 和 `net start w3svc`。</span><span class="sxs-lookup"><span data-stu-id="a9257-225">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
* <span data-ttu-id="a9257-226">重新启动服务器。</span><span class="sxs-lookup"><span data-stu-id="a9257-226">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="a9257-227">macOS</span><span class="sxs-lookup"><span data-stu-id="a9257-227">macOS</span></span>

<span data-ttu-id="a9257-228">设置 macOS 的当前环境可在运行应用时完成：</span><span class="sxs-lookup"><span data-stu-id="a9257-228">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

<span data-ttu-id="a9257-229">或者，在运行应用前使用 `export` 设置环境：</span><span class="sxs-lookup"><span data-stu-id="a9257-229">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

<span data-ttu-id="a9257-230">在 .bashrc 或 .bash_profile 文件中设置计算机级环境变量 。</span><span class="sxs-lookup"><span data-stu-id="a9257-230">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="a9257-231">使用任意文本编辑器编辑文件。</span><span class="sxs-lookup"><span data-stu-id="a9257-231">Edit the file using any text editor.</span></span> <span data-ttu-id="a9257-232">添加以下语句：</span><span class="sxs-lookup"><span data-stu-id="a9257-232">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a><span data-ttu-id="a9257-233">Linux</span><span class="sxs-lookup"><span data-stu-id="a9257-233">Linux</span></span>

<span data-ttu-id="a9257-234">对于 Linux 发行版，在命令提示符处对基于会话的变量设置使用 `export` 命令，并对计算机级别环境设置使用 bash_profile 文件。</span><span class="sxs-lookup"><span data-stu-id="a9257-234">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="a9257-235">使用代码设置环境</span><span class="sxs-lookup"><span data-stu-id="a9257-235">Set the environment in code</span></span>

<span data-ttu-id="a9257-236">生成主机时，调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*>。</span><span class="sxs-lookup"><span data-stu-id="a9257-236">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="a9257-237">请参阅 <xref:fundamentals/host/generic-host#environmentname>。</span><span class="sxs-lookup"><span data-stu-id="a9257-237">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="a9257-238">按环境配置</span><span class="sxs-lookup"><span data-stu-id="a9257-238">Configuration by environment</span></span>

<span data-ttu-id="a9257-239">若要按环境加载配置，请参阅 <xref:fundamentals/configuration/index#json-configuration-provider>。</span><span class="sxs-lookup"><span data-stu-id="a9257-239">To load configuration by environment, see <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="a9257-240">基于环境的 Startup 类和方法</span><span class="sxs-lookup"><span data-stu-id="a9257-240">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="a9257-241">将 IWebHostEnvironment 注入 Startup 类</span><span class="sxs-lookup"><span data-stu-id="a9257-241">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="a9257-242">将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入 `Startup` 构造函数。</span><span class="sxs-lookup"><span data-stu-id="a9257-242">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="a9257-243">当应用仅需为几个代码差异最小的环境配置 `Startup` 时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="a9257-243">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="a9257-244">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="a9257-244">In the following example:</span></span>

* <span data-ttu-id="a9257-245">环境保存在 `_env` 字段中。</span><span class="sxs-lookup"><span data-stu-id="a9257-245">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="a9257-246">`_env` 在 `ConfigureServices` 和 `Configure` 中用于根据应用的环境应用启动配置。</span><span class="sxs-lookup"><span data-stu-id="a9257-246">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a><span data-ttu-id="a9257-247">Startup 类约定</span><span class="sxs-lookup"><span data-stu-id="a9257-247">Startup class conventions</span></span>

<span data-ttu-id="a9257-248">当 ASP.NET Core 应用启动时，[Startup 类](xref:fundamentals/startup)启动应用。</span><span class="sxs-lookup"><span data-stu-id="a9257-248">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="a9257-249">应用可以为不同的环境定义多个 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="a9257-249">The app can define multiple `Startup` classes for different environments.</span></span> <span data-ttu-id="a9257-250">在运行时选择适当的 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="a9257-250">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="a9257-251">优先考虑名称后缀与当前环境相匹配的类。</span><span class="sxs-lookup"><span data-stu-id="a9257-251">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="a9257-252">如果找不到匹配的 `Startup{EnvironmentName}`，就会使用 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="a9257-252">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="a9257-253">当应用需要为各环境之间存在许多代码差异的多个环境配置启动时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="a9257-253">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span> <span data-ttu-id="a9257-254">典型的应用不需要这种方法。</span><span class="sxs-lookup"><span data-stu-id="a9257-254">Typical apps will not need this approach.</span></span>

<span data-ttu-id="a9257-255">若要实现基于环境的 `Startup` 类，请创建 `Startup{EnvironmentName}` 类和回退 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="a9257-255">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` classes and a fallback `Startup` class:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

<span data-ttu-id="a9257-256">使用接受程序集名称的 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 重载：</span><span class="sxs-lookup"><span data-stu-id="a9257-256">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a><span data-ttu-id="a9257-257">Startup 方法约定</span><span class="sxs-lookup"><span data-stu-id="a9257-257">Startup method conventions</span></span>

<span data-ttu-id="a9257-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 和 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) 支持窗体 `Configure<EnvironmentName>` 和 `Configure<EnvironmentName>Services` 的环境特定版本。</span><span class="sxs-lookup"><span data-stu-id="a9257-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="a9257-259">当应用需要为多个环境（每个环境有许多代码差异）配置启动时，这种方法就非常有用：</span><span class="sxs-lookup"><span data-stu-id="a9257-259">This approach is useful when the app requires configuring startup for several environments with many code differences per environment:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="a9257-260">其他资源</span><span class="sxs-lookup"><span data-stu-id="a9257-260">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9257-261">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9257-261">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a9257-262">ASP.NET Core 基于使用环境变量的运行时环境配置应用行为。</span><span class="sxs-lookup"><span data-stu-id="a9257-262">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="a9257-263">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a9257-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="a9257-264">环境</span><span class="sxs-lookup"><span data-stu-id="a9257-264">Environments</span></span>

<span data-ttu-id="a9257-265">ASP.NET Core 在应用启动时读取环境变量 `ASPNETCORE_ENVIRONMENT`，并将该值存储在 [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName) 中。</span><span class="sxs-lookup"><span data-stu-id="a9257-265">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="a9257-266">`ASPNETCORE_ENVIRONMENT` 可设置为任意值，但框架提供三个值：</span><span class="sxs-lookup"><span data-stu-id="a9257-266">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="a9257-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>（默认值）</span><span class="sxs-lookup"><span data-stu-id="a9257-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="a9257-268">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="a9257-268">The preceding code:</span></span>

* <span data-ttu-id="a9257-269">当 `ASPNETCORE_ENVIRONMENT` 设置为 `Development` 时，调用 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)。</span><span class="sxs-lookup"><span data-stu-id="a9257-269">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="a9257-270">当 `ASPNETCORE_ENVIRONMENT` 的值设置为下列之一时，调用 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)：</span><span class="sxs-lookup"><span data-stu-id="a9257-270">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="a9257-271">[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)使用 `IHostingEnvironment.EnvironmentName` 的值来包含或排除元素中的标记：</span><span class="sxs-lookup"><span data-stu-id="a9257-271">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="a9257-272">在 Windows 和 macOS 上，环境变量和值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="a9257-272">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="a9257-273">默认情况下，Linux 环境变量和值区分大小写。</span><span class="sxs-lookup"><span data-stu-id="a9257-273">Linux environment variables and values are case-sensitive by default.</span></span>

### <a name="development"></a><span data-ttu-id="a9257-274">开发</span><span class="sxs-lookup"><span data-stu-id="a9257-274">Development</span></span>

<span data-ttu-id="a9257-275">开发环境可以启用不应该在生产中公开的功能。</span><span class="sxs-lookup"><span data-stu-id="a9257-275">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="a9257-276">例如，ASP.NET Core 模板在开发环境中启用了[开发人员异常页](xref:fundamentals/error-handling#developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="a9257-276">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="a9257-277">本地计算机开发环境可以在项目的 Properties\launchSettings.json 文件中设置。</span><span class="sxs-lookup"><span data-stu-id="a9257-277">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="a9257-278">在 launchSettings.json 中设置的环境值替代在系统环境中设置的值。</span><span class="sxs-lookup"><span data-stu-id="a9257-278">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="a9257-279">以下 JSON 显示 launchSettings.json 文件中的三个配置文件：</span><span class="sxs-lookup"><span data-stu-id="a9257-279">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="a9257-280">launchSettings.json 中的 `applicationUrl` 属性可指定服务器 URL 的列表。</span><span class="sxs-lookup"><span data-stu-id="a9257-280">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="a9257-281">在列表中的 URL 之间使用分号：</span><span class="sxs-lookup"><span data-stu-id="a9257-281">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="a9257-282">使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动应用时，使用具有 `"commandName": "Project"` 的第一个配置文件。</span><span class="sxs-lookup"><span data-stu-id="a9257-282">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="a9257-283">`commandName` 的值指定要启动的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="a9257-283">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="a9257-284">`commandName` 可为以下任一项：</span><span class="sxs-lookup"><span data-stu-id="a9257-284">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="a9257-285">`Project`（启动 Kestrel 的项目）</span><span class="sxs-lookup"><span data-stu-id="a9257-285">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="a9257-286">使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动应用时：</span><span class="sxs-lookup"><span data-stu-id="a9257-286">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="a9257-287">如果可用，读取 launchSettings.json。</span><span class="sxs-lookup"><span data-stu-id="a9257-287">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="a9257-288">launchSettings.json 中的 `environmentVariables` 设置会替代环境变量。</span><span class="sxs-lookup"><span data-stu-id="a9257-288">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="a9257-289">此时显示承载环境。</span><span class="sxs-lookup"><span data-stu-id="a9257-289">The hosting environment is displayed.</span></span>

<span data-ttu-id="a9257-290">以下输出显示了使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动的应用：</span><span class="sxs-lookup"><span data-stu-id="a9257-290">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="a9257-291">Visual Studio 项目属性“调试”选项卡提供 GUI 来编辑 launchSettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="a9257-291">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![项目属性设置环境变量](environments/_static/project-properties-debug.png)

<span data-ttu-id="a9257-293">在 Web 服务器重新启动之前，对项目配置文件所做的更改可能不会生效。</span><span class="sxs-lookup"><span data-stu-id="a9257-293">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="a9257-294">必须重新启动 Kestrel 才能检测到对其环境所做的更改。</span><span class="sxs-lookup"><span data-stu-id="a9257-294">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="a9257-295">launchSettings.json 不应存储机密。</span><span class="sxs-lookup"><span data-stu-id="a9257-295">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="a9257-296">[机密管理器工具](xref:security/app-secrets)可用于存储本地开发的机密。</span><span class="sxs-lookup"><span data-stu-id="a9257-296">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="a9257-297">使用 [Visual Studio Code](https://code.visualstudio.com/) 时，可以在 .vscode/launch.json 文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a9257-297">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="a9257-298">以下示例将环境设置为 `Development`：</span><span class="sxs-lookup"><span data-stu-id="a9257-298">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="a9257-299">使用与 Properties/launchSettings.json 相同的方法通过 `dotnet run` 启动应用时，不读取项目中的 .vscode/launch.json 文件 。</span><span class="sxs-lookup"><span data-stu-id="a9257-299">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="a9257-300">在没有 launchSettings.json 文件的 Development 环境中启动应用时，需要使用环境变量设置环境或者将命令行参数设为 `dotnet run` 命令。</span><span class="sxs-lookup"><span data-stu-id="a9257-300">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="a9257-301">生产</span><span class="sxs-lookup"><span data-stu-id="a9257-301">Production</span></span>

<span data-ttu-id="a9257-302">Production 环境应配置为最大限度地提高安全性、性能和应用可靠性。</span><span class="sxs-lookup"><span data-stu-id="a9257-302">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="a9257-303">不同于开发的一些通用设置包括：</span><span class="sxs-lookup"><span data-stu-id="a9257-303">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="a9257-304">缓存。</span><span class="sxs-lookup"><span data-stu-id="a9257-304">Caching.</span></span>
* <span data-ttu-id="a9257-305">客户端资源被捆绑和缩小，并可能从 CDN 提供。</span><span class="sxs-lookup"><span data-stu-id="a9257-305">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="a9257-306">已禁用诊断错误页。</span><span class="sxs-lookup"><span data-stu-id="a9257-306">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="a9257-307">已启用友好错误页。</span><span class="sxs-lookup"><span data-stu-id="a9257-307">Friendly error pages enabled.</span></span>
* <span data-ttu-id="a9257-308">已启用生产记录和监视。</span><span class="sxs-lookup"><span data-stu-id="a9257-308">Production logging and monitoring enabled.</span></span> <span data-ttu-id="a9257-309">例如，[Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="a9257-309">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="a9257-310">设置环境</span><span class="sxs-lookup"><span data-stu-id="a9257-310">Set the environment</span></span>

<span data-ttu-id="a9257-311">通常，可以使用环境变量或平台设置来设置用于测试的特定环境。</span><span class="sxs-lookup"><span data-stu-id="a9257-311">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="a9257-312">如果未设置环境，默认值为 `Production`，这会禁用大多数调试功能。</span><span class="sxs-lookup"><span data-stu-id="a9257-312">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="a9257-313">设置环境的方法取决于操作系统。</span><span class="sxs-lookup"><span data-stu-id="a9257-313">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="a9257-314">构建主机时，应用读取的最后一个环境设置将决定应用的环境。</span><span class="sxs-lookup"><span data-stu-id="a9257-314">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="a9257-315">应用运行时无法更改应用的环境。</span><span class="sxs-lookup"><span data-stu-id="a9257-315">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="a9257-316">环境变量或平台设置</span><span class="sxs-lookup"><span data-stu-id="a9257-316">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="a9257-317">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="a9257-317">Azure App Service</span></span>

<span data-ttu-id="a9257-318">若要在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中设置环境，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="a9257-318">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="a9257-319">从“应用服务”边栏选项卡中选择应用。</span><span class="sxs-lookup"><span data-stu-id="a9257-319">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="a9257-320">在“设置”组中，选择“配置”边栏选项卡 。</span><span class="sxs-lookup"><span data-stu-id="a9257-320">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="a9257-321">在“应用程序设置”选项卡中，选择“新建应用程序设置”。 </span><span class="sxs-lookup"><span data-stu-id="a9257-321">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="a9257-322">在“添加/编辑应用程序设置”窗口中，在“名称”中提供 `ASPNETCORE_ENVIRONMENT`。</span><span class="sxs-lookup"><span data-stu-id="a9257-322">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="a9257-323">在“值”中提供环境（例如 `Staging`）。</span><span class="sxs-lookup"><span data-stu-id="a9257-323">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="a9257-324">交换部署槽位时，如果希望环境设置保持当前槽位，请选中“部署槽位设置”复选框。</span><span class="sxs-lookup"><span data-stu-id="a9257-324">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="a9257-325">有关详细信息，请参阅 Azure 文档中的[在 Azure 应用服务中设置过渡环境](/azure/app-service/web-sites-staged-publishing)。</span><span class="sxs-lookup"><span data-stu-id="a9257-325">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="a9257-326">选择“确定”以关闭“添加/编辑应用程序设置”窗口。 </span><span class="sxs-lookup"><span data-stu-id="a9257-326">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="a9257-327">选择“配置”边栏选项卡顶部的“保存” 。</span><span class="sxs-lookup"><span data-stu-id="a9257-327">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="a9257-328">在 Azure 门户中添加、更改或删除应用设置（环境变量）后，Azure 应用服务自动重启应用。</span><span class="sxs-lookup"><span data-stu-id="a9257-328">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="a9257-329">Windows</span><span class="sxs-lookup"><span data-stu-id="a9257-329">Windows</span></span>

<span data-ttu-id="a9257-330">若要在使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动该应用时为当前会话设置 `ASPNETCORE_ENVIRONMENT`，则使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="a9257-330">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="a9257-331">**命令提示符**</span><span class="sxs-lookup"><span data-stu-id="a9257-331">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="a9257-332">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="a9257-332">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="a9257-333">这些命令仅对当前窗口有效。</span><span class="sxs-lookup"><span data-stu-id="a9257-333">These commands only take effect for the current window.</span></span> <span data-ttu-id="a9257-334">窗口关闭时，`ASPNETCORE_ENVIRONMENT` 设置将恢复为默认设置或计算机值。</span><span class="sxs-lookup"><span data-stu-id="a9257-334">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="a9257-335">若要在 Windows 中全局设置值，请采用下列两种方法之一：</span><span class="sxs-lookup"><span data-stu-id="a9257-335">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="a9257-336">依次打开“控制面板”>“系统”>“高级系统设置”，再添加或编辑“`ASPNETCORE_ENVIRONMENT`”值：</span><span class="sxs-lookup"><span data-stu-id="a9257-336">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![系统高级属性](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 环境变量](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="a9257-339">打开管理命令提示符并运行 `setx` 命令，或打开管理 PowerShell 命令提示符并运行 `[Environment]::SetEnvironmentVariable`：</span><span class="sxs-lookup"><span data-stu-id="a9257-339">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="a9257-340">**命令提示符**</span><span class="sxs-lookup"><span data-stu-id="a9257-340">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="a9257-341">`/M` 开关指明，在系统一级设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a9257-341">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="a9257-342">如果未使用 `/M` 开关，就会为用户帐户设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a9257-342">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="a9257-343">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="a9257-343">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="a9257-344">`Machine` 选项值指明，在系统一级设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a9257-344">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="a9257-345">如果将选项值更改为 `User`，就会为用户帐户设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a9257-345">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="a9257-346">如果全局设置 `ASPNETCORE_ENVIRONMENT` 环境变量，它就会对在值设置后打开的任何命令窗口中对 `dotnet run` 起作用。</span><span class="sxs-lookup"><span data-stu-id="a9257-346">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="a9257-347">**web.config**</span><span class="sxs-lookup"><span data-stu-id="a9257-347">**web.config**</span></span>

<span data-ttu-id="a9257-348">若要使用 web.config 设置 `ASPNETCORE_ENVIRONMENT` 环境变量，请参阅 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>的“设置环境变量”部分。</span><span class="sxs-lookup"><span data-stu-id="a9257-348">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="a9257-349">**项目文件或发布配置文件**</span><span class="sxs-lookup"><span data-stu-id="a9257-349">**Project file or publish profile**</span></span>

<span data-ttu-id="a9257-350">**对于 Windows IIS 部署：** 将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="a9257-350">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="a9257-351">此方法在发布项目时设置 web.config 中的环境：</span><span class="sxs-lookup"><span data-stu-id="a9257-351">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="a9257-352">**每个 IIS 应用程序池**</span><span class="sxs-lookup"><span data-stu-id="a9257-352">**Per IIS Application Pool**</span></span>

<span data-ttu-id="a9257-353">若要为在独立应用池中运行的应用设置 `ASPNETCORE_ENVIRONMENT` 环境变量（IIS 10.0 或更高版本支持此操作），请参阅[环境变量 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="a9257-353">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="a9257-354">为应用池设置 `ASPNETCORE_ENVIRONMENT` 环境变量后，它的值会替代系统级设置。</span><span class="sxs-lookup"><span data-stu-id="a9257-354">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a9257-355">在 IIS 中托管应用并添加或更改 `ASPNETCORE_ENVIRONMENT` 环境变量时，请采用下列方法之一，让新值可供应用拾取：</span><span class="sxs-lookup"><span data-stu-id="a9257-355">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="a9257-356">在命令提示符处依次执行 `net stop was /y` 和 `net start w3svc`。</span><span class="sxs-lookup"><span data-stu-id="a9257-356">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="a9257-357">重新启动服务器。</span><span class="sxs-lookup"><span data-stu-id="a9257-357">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="a9257-358">macOS</span><span class="sxs-lookup"><span data-stu-id="a9257-358">macOS</span></span>

<span data-ttu-id="a9257-359">设置 macOS 的当前环境可在运行应用时完成：</span><span class="sxs-lookup"><span data-stu-id="a9257-359">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="a9257-360">或者，在运行应用前使用 `export` 设置环境：</span><span class="sxs-lookup"><span data-stu-id="a9257-360">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="a9257-361">在 .bashrc 或 .bash_profile 文件中设置计算机级环境变量 。</span><span class="sxs-lookup"><span data-stu-id="a9257-361">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="a9257-362">使用任意文本编辑器编辑文件。</span><span class="sxs-lookup"><span data-stu-id="a9257-362">Edit the file using any text editor.</span></span> <span data-ttu-id="a9257-363">添加以下语句：</span><span class="sxs-lookup"><span data-stu-id="a9257-363">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="a9257-364">Linux</span><span class="sxs-lookup"><span data-stu-id="a9257-364">Linux</span></span>

<span data-ttu-id="a9257-365">对于 Linux 发行版，在命令提示符处对基于会话的变量设置使用 `export` 命令，并对计算机级别环境设置使用 bash_profile 文件。</span><span class="sxs-lookup"><span data-stu-id="a9257-365">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="a9257-366">使用代码设置环境</span><span class="sxs-lookup"><span data-stu-id="a9257-366">Set the environment in code</span></span>

<span data-ttu-id="a9257-367">生成主机时，调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*>。</span><span class="sxs-lookup"><span data-stu-id="a9257-367">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="a9257-368">请参阅 <xref:fundamentals/host/web-host#environment>。</span><span class="sxs-lookup"><span data-stu-id="a9257-368">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="a9257-369">按环境配置</span><span class="sxs-lookup"><span data-stu-id="a9257-369">Configuration by environment</span></span>

<span data-ttu-id="a9257-370">若要按环境加载配置，我们建议：</span><span class="sxs-lookup"><span data-stu-id="a9257-370">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="a9257-371">appsettings 文件 (appsettings.Environment>.json) 。</span><span class="sxs-lookup"><span data-stu-id="a9257-371">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="a9257-372">请参阅 <xref:fundamentals/configuration/index#json-configuration-provider>。</span><span class="sxs-lookup"><span data-stu-id="a9257-372">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="a9257-373">环境变量（在托管应用的每个系统上进行设置）。</span><span class="sxs-lookup"><span data-stu-id="a9257-373">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="a9257-374">请参见 <xref:fundamentals/host/web-host#environment> 和 <xref:security/app-secrets#environment-variables>。</span><span class="sxs-lookup"><span data-stu-id="a9257-374">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="a9257-375">密码管理器（仅限开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="a9257-375">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="a9257-376">请参阅 <xref:security/app-secrets>。</span><span class="sxs-lookup"><span data-stu-id="a9257-376">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="a9257-377">基于环境的 Startup 类和方法</span><span class="sxs-lookup"><span data-stu-id="a9257-377">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="a9257-378">将 IHostingEnvironment 注入 Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="a9257-378">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="a9257-379">将 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> 注入 `Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="a9257-379">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="a9257-380">当应用仅需为几个代码差异最小的环境配置 `Startup.Configure` 时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="a9257-380">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="a9257-381">将 IHostingEnvironment 注入 Startup 类</span><span class="sxs-lookup"><span data-stu-id="a9257-381">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="a9257-382">将 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> 注入 `Startup` 构造函数，并将服务分配给一个字段，以便在整个 `Startup` 类中使用。</span><span class="sxs-lookup"><span data-stu-id="a9257-382">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="a9257-383">当应用需为几个代码差异最小的环境配置启动时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="a9257-383">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="a9257-384">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="a9257-384">In the following example:</span></span>

* <span data-ttu-id="a9257-385">环境保存在 `_env` 字段中。</span><span class="sxs-lookup"><span data-stu-id="a9257-385">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="a9257-386">`_env` 在 `ConfigureServices` 和 `Configure` 中用于根据应用的环境应用启动配置。</span><span class="sxs-lookup"><span data-stu-id="a9257-386">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="a9257-387">Startup 类约定</span><span class="sxs-lookup"><span data-stu-id="a9257-387">Startup class conventions</span></span>

<span data-ttu-id="a9257-388">当 ASP.NET Core 应用启动时，[Startup 类](xref:fundamentals/startup)启动应用。</span><span class="sxs-lookup"><span data-stu-id="a9257-388">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="a9257-389">应用可以为不同的环境定义单独的 `Startup` 类（例如 `StartupDevelopment`）。</span><span class="sxs-lookup"><span data-stu-id="a9257-389">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="a9257-390">在运行时选择适当的 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="a9257-390">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="a9257-391">优先考虑名称后缀与当前环境相匹配的类。</span><span class="sxs-lookup"><span data-stu-id="a9257-391">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="a9257-392">如果找不到匹配的 `Startup{EnvironmentName}`，就会使用 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="a9257-392">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="a9257-393">当应用需要为各环境之间存在许多代码差异的多个环境配置启动时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="a9257-393">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="a9257-394">若要实现基于环境的 `Startup` 类，请为使用中的每个环境创建 `Startup{EnvironmentName}` 类，并创建回退 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="a9257-394">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="a9257-395">使用接受程序集名称的 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 重载：</span><span class="sxs-lookup"><span data-stu-id="a9257-395">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="a9257-396">Startup 方法约定</span><span class="sxs-lookup"><span data-stu-id="a9257-396">Startup method conventions</span></span>

<span data-ttu-id="a9257-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 和 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) 支持窗体 `Configure<EnvironmentName>` 和 `Configure<EnvironmentName>Services` 的环境特定版本。</span><span class="sxs-lookup"><span data-stu-id="a9257-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="a9257-398">当应用需要为各环境之间存在许多代码差异的多个环境配置启动时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="a9257-398">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="a9257-399">其他资源</span><span class="sxs-lookup"><span data-stu-id="a9257-399">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
