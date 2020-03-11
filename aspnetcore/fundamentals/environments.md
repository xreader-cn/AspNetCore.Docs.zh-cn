---
title: 在 ASP.NET Core 中使用多个环境
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中控制多个环境的应用行为。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78645354"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="84789-103">在 ASP.NET Core 中使用多个环境</span><span class="sxs-lookup"><span data-stu-id="84789-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84789-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="84789-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="84789-105">ASP.NET Core 基于使用环境变量的运行时环境配置应用行为。</span><span class="sxs-lookup"><span data-stu-id="84789-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="84789-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="84789-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="84789-107">环境</span><span class="sxs-lookup"><span data-stu-id="84789-107">Environments</span></span>

<span data-ttu-id="84789-108">ASP.NET Core 在应用启动时读取环境变量 `ASPNETCORE_ENVIRONMENT`，并将该值存储在 [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) 中。</span><span class="sxs-lookup"><span data-stu-id="84789-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="84789-109">`ASPNETCORE_ENVIRONMENT` 可设置为任意值，但框架提供三个值：</span><span class="sxs-lookup"><span data-stu-id="84789-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="84789-110"><xref:Microsoft.Extensions.Hosting.Environments.Production>（默认值）</span><span class="sxs-lookup"><span data-stu-id="84789-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="84789-111">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="84789-111">The preceding code:</span></span>

* <span data-ttu-id="84789-112">当 `ASPNETCORE_ENVIRONMENT` 设置为 `Development` 时，调用 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)。</span><span class="sxs-lookup"><span data-stu-id="84789-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="84789-113">当 `ASPNETCORE_ENVIRONMENT` 的值设置为下列之一时，调用 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)：</span><span class="sxs-lookup"><span data-stu-id="84789-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="84789-114">[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)使用 `IHostingEnvironment.EnvironmentName` 的值来包含或排除元素中的标记：</span><span class="sxs-lookup"><span data-stu-id="84789-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="84789-115">在 Windows 和 macOS 上，环境变量和值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="84789-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="84789-116">默认情况下，Linux 环境变量和值要区分大小写  。</span><span class="sxs-lookup"><span data-stu-id="84789-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="84789-117">开发</span><span class="sxs-lookup"><span data-stu-id="84789-117">Development</span></span>

<span data-ttu-id="84789-118">开发环境可以启用不应该在生产中公开的功能。</span><span class="sxs-lookup"><span data-stu-id="84789-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="84789-119">例如，ASP.NET Core 模板在开发环境中启用了[开发人员异常页](xref:fundamentals/error-handling#developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="84789-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="84789-120">本地计算机开发环境可以在项目的 Properties\launchSettings.json 文件中设置  。</span><span class="sxs-lookup"><span data-stu-id="84789-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="84789-121">在 launchSettings.json 中设置的环境值替代在系统环境中设置的值  。</span><span class="sxs-lookup"><span data-stu-id="84789-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="84789-122">以下 JSON 显示 launchSettings.json 文件中的三个配置文件  ：</span><span class="sxs-lookup"><span data-stu-id="84789-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="84789-123">launchSettings.json 中的 `applicationUrl` 属性可指定服务器 URL 的列表  。</span><span class="sxs-lookup"><span data-stu-id="84789-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="84789-124">在列表中的 URL 之间使用分号：</span><span class="sxs-lookup"><span data-stu-id="84789-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="84789-125">使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动应用时，使用具有 `"commandName": "Project"` 的第一个配置文件。</span><span class="sxs-lookup"><span data-stu-id="84789-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="84789-126">`commandName` 的值指定要启动的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="84789-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="84789-127">`commandName` 可为以下任一项：</span><span class="sxs-lookup"><span data-stu-id="84789-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="84789-128">`Project`（启动 Kestrel 的项目）</span><span class="sxs-lookup"><span data-stu-id="84789-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="84789-129">使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动应用时：</span><span class="sxs-lookup"><span data-stu-id="84789-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="84789-130">如果可用，读取 launchSettings.json  。</span><span class="sxs-lookup"><span data-stu-id="84789-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="84789-131">launchSettings.json 中的 `environmentVariables` 设置会替代环境变量  。</span><span class="sxs-lookup"><span data-stu-id="84789-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="84789-132">此时显示承载环境。</span><span class="sxs-lookup"><span data-stu-id="84789-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="84789-133">以下输出显示了使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动的应用：</span><span class="sxs-lookup"><span data-stu-id="84789-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="84789-134">Visual Studio 项目属性“调试”选项卡提供 GUI 来编辑 launchSettings.json 文件   ：</span><span class="sxs-lookup"><span data-stu-id="84789-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![项目属性设置环境变量](environments/_static/project-properties-debug.png)

<span data-ttu-id="84789-136">在 Web 服务器重新启动之前，对项目配置文件所做的更改可能不会生效。</span><span class="sxs-lookup"><span data-stu-id="84789-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="84789-137">必须重新启动 Kestrel 才能检测到对其环境所做的更改。</span><span class="sxs-lookup"><span data-stu-id="84789-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="84789-138">launchSettings.json 不应存储机密  。</span><span class="sxs-lookup"><span data-stu-id="84789-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="84789-139">[机密管理器工具](xref:security/app-secrets)可用于存储本地开发的机密。</span><span class="sxs-lookup"><span data-stu-id="84789-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="84789-140">使用 [Visual Studio Code](https://code.visualstudio.com/) 时，可以在 .vscode/launch.json 文件中设置环境变量  。</span><span class="sxs-lookup"><span data-stu-id="84789-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="84789-141">以下示例将环境设置为 `Development`：</span><span class="sxs-lookup"><span data-stu-id="84789-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="84789-142">使用与 Properties/launchSettings.json 相同的方法通过 `dotnet run` 启动应用时，不读取项目中的 .vscode/launch.json 文件   。</span><span class="sxs-lookup"><span data-stu-id="84789-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="84789-143">在没有 launchSettings.json 文件的 Development 环境中启动应用时，需要使用环境变量设置环境或者将命令行参数设为 `dotnet run` 命令  。</span><span class="sxs-lookup"><span data-stu-id="84789-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="84789-144">生产</span><span class="sxs-lookup"><span data-stu-id="84789-144">Production</span></span>

<span data-ttu-id="84789-145">Production 环境应配置为最大限度地提高安全性、性能和应用可靠性。</span><span class="sxs-lookup"><span data-stu-id="84789-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="84789-146">不同于开发的一些通用设置包括：</span><span class="sxs-lookup"><span data-stu-id="84789-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="84789-147">缓存。</span><span class="sxs-lookup"><span data-stu-id="84789-147">Caching.</span></span>
* <span data-ttu-id="84789-148">客户端资源被捆绑和缩小，并可能从 CDN 提供。</span><span class="sxs-lookup"><span data-stu-id="84789-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="84789-149">已禁用诊断错误页。</span><span class="sxs-lookup"><span data-stu-id="84789-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="84789-150">已启用友好错误页。</span><span class="sxs-lookup"><span data-stu-id="84789-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="84789-151">已启用生产记录和监视。</span><span class="sxs-lookup"><span data-stu-id="84789-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="84789-152">例如，[Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="84789-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="84789-153">设置环境</span><span class="sxs-lookup"><span data-stu-id="84789-153">Set the environment</span></span>

<span data-ttu-id="84789-154">通常，可以使用环境变量或平台设置来设置用于测试的特定环境。</span><span class="sxs-lookup"><span data-stu-id="84789-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="84789-155">如果未设置环境，默认值为 `Production`，这会禁用大多数调试功能。</span><span class="sxs-lookup"><span data-stu-id="84789-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="84789-156">设置环境的方法取决于操作系统。</span><span class="sxs-lookup"><span data-stu-id="84789-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="84789-157">构建主机时，应用读取的最后一个环境设置将决定应用的环境。</span><span class="sxs-lookup"><span data-stu-id="84789-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="84789-158">应用运行时无法更改应用的环境。</span><span class="sxs-lookup"><span data-stu-id="84789-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="84789-159">环境变量或平台设置</span><span class="sxs-lookup"><span data-stu-id="84789-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="84789-160">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="84789-160">Azure App Service</span></span>

<span data-ttu-id="84789-161">若要在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中设置环境，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="84789-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="84789-162">从“应用服务”边栏选项卡中选择应用  。</span><span class="sxs-lookup"><span data-stu-id="84789-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="84789-163">在“设置”组中，选择“配置”边栏选项卡   。</span><span class="sxs-lookup"><span data-stu-id="84789-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="84789-164">在“应用程序设置”选项卡中，选择“新建应用程序设置”。  </span><span class="sxs-lookup"><span data-stu-id="84789-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="84789-165">在“添加/编辑应用程序设置”窗口中，在“名称”中提供  `ASPNETCORE_ENVIRONMENT`  。</span><span class="sxs-lookup"><span data-stu-id="84789-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="84789-166">在“值”中提供环境（例如 `Staging`）  。</span><span class="sxs-lookup"><span data-stu-id="84789-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="84789-167">交换部署槽位时，如果希望环境设置保持当前槽位，请选中“部署槽位设置”复选框  。</span><span class="sxs-lookup"><span data-stu-id="84789-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="84789-168">有关详细信息，请参阅 Azure 文档中的[在 Azure 应用服务中设置过渡环境](/azure/app-service/web-sites-staged-publishing)。</span><span class="sxs-lookup"><span data-stu-id="84789-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="84789-169">选择“确定”以关闭“添加/编辑应用程序设置”窗口。  </span><span class="sxs-lookup"><span data-stu-id="84789-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="84789-170">选择“配置”边栏选项卡顶部的“保存”   。</span><span class="sxs-lookup"><span data-stu-id="84789-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="84789-171">在 Azure 门户中添加、更改或删除应用设置（环境变量）后，Azure 应用服务自动重启应用。</span><span class="sxs-lookup"><span data-stu-id="84789-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="84789-172">Windows</span><span class="sxs-lookup"><span data-stu-id="84789-172">Windows</span></span>

<span data-ttu-id="84789-173">若要在使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动该应用时为当前会话设置 `ASPNETCORE_ENVIRONMENT`，则使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="84789-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="84789-174">**命令提示符**</span><span class="sxs-lookup"><span data-stu-id="84789-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="84789-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="84789-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="84789-176">这些命令仅对当前窗口有效。</span><span class="sxs-lookup"><span data-stu-id="84789-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="84789-177">窗口关闭时，`ASPNETCORE_ENVIRONMENT` 设置将恢复为默认设置或计算机值。</span><span class="sxs-lookup"><span data-stu-id="84789-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="84789-178">若要在 Windows 中全局设置值，请采用下列两种方法之一：</span><span class="sxs-lookup"><span data-stu-id="84789-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="84789-179">依次打开“控制面板”  >“系统”  >“高级系统设置”  ，再添加或编辑“`ASPNETCORE_ENVIRONMENT`”值：</span><span class="sxs-lookup"><span data-stu-id="84789-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![系统高级属性](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 环境变量](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="84789-182">打开管理命令提示符并运行 `setx` 命令，或打开管理 PowerShell 命令提示符并运行 `[Environment]::SetEnvironmentVariable`：</span><span class="sxs-lookup"><span data-stu-id="84789-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="84789-183">**命令提示符**</span><span class="sxs-lookup"><span data-stu-id="84789-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="84789-184">`/M` 开关指明，在系统一级设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="84789-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="84789-185">如果未使用 `/M` 开关，就会为用户帐户设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="84789-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="84789-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="84789-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="84789-187">`Machine` 选项值指明，在系统一级设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="84789-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="84789-188">如果将选项值更改为 `User`，就会为用户帐户设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="84789-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="84789-189">如果全局设置 `ASPNETCORE_ENVIRONMENT` 环境变量，它就会对在值设置后打开的任何命令窗口中对 `dotnet run` 起作用。</span><span class="sxs-lookup"><span data-stu-id="84789-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="84789-190">**web.config**</span><span class="sxs-lookup"><span data-stu-id="84789-190">**web.config**</span></span>

<span data-ttu-id="84789-191">若要使用 web.config  设置 `ASPNETCORE_ENVIRONMENT` 环境变量，请参阅 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>的“设置环境变量”  部分。</span><span class="sxs-lookup"><span data-stu-id="84789-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="84789-192">**项目文件或发布配置文件**</span><span class="sxs-lookup"><span data-stu-id="84789-192">**Project file or publish profile**</span></span>

<span data-ttu-id="84789-193">**对于 Windows IIS 部署：** 将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="84789-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="84789-194">此方法在发布项目时设置 web.config  中的环境：</span><span class="sxs-lookup"><span data-stu-id="84789-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="84789-195">**每个 IIS 应用程序池**</span><span class="sxs-lookup"><span data-stu-id="84789-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="84789-196">若要为在独立应用池中运行的应用设置 `ASPNETCORE_ENVIRONMENT` 环境变量（IIS 10.0 或更高版本支持此操作），请参阅[环境变量 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”  部分。</span><span class="sxs-lookup"><span data-stu-id="84789-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="84789-197">为应用池设置 `ASPNETCORE_ENVIRONMENT` 环境变量后，它的值会替代系统级设置。</span><span class="sxs-lookup"><span data-stu-id="84789-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="84789-198">在 IIS 中托管应用并添加或更改 `ASPNETCORE_ENVIRONMENT` 环境变量时，请采用下列方法之一，让新值可供应用拾取：</span><span class="sxs-lookup"><span data-stu-id="84789-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="84789-199">在命令提示符处依次执行 `net stop was /y` 和 `net start w3svc`。</span><span class="sxs-lookup"><span data-stu-id="84789-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="84789-200">重新启动服务器。</span><span class="sxs-lookup"><span data-stu-id="84789-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="84789-201">macOS</span><span class="sxs-lookup"><span data-stu-id="84789-201">macOS</span></span>

<span data-ttu-id="84789-202">设置 macOS 的当前环境可在运行应用时完成：</span><span class="sxs-lookup"><span data-stu-id="84789-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="84789-203">或者，在运行应用前使用 `export` 设置环境：</span><span class="sxs-lookup"><span data-stu-id="84789-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="84789-204">在 .bashrc 或 .bash_profile 文件中设置计算机级环境变量   。</span><span class="sxs-lookup"><span data-stu-id="84789-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="84789-205">使用任意文本编辑器编辑文件。</span><span class="sxs-lookup"><span data-stu-id="84789-205">Edit the file using any text editor.</span></span> <span data-ttu-id="84789-206">添加以下语句：</span><span class="sxs-lookup"><span data-stu-id="84789-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="84789-207">Linux</span><span class="sxs-lookup"><span data-stu-id="84789-207">Linux</span></span>

<span data-ttu-id="84789-208">对于 Linux 发行版，请在命令提示符中使用 `export` 命令进行基于会话的变量设置，并使用 bash_profile 文件进行计算机级环境设置  。</span><span class="sxs-lookup"><span data-stu-id="84789-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="84789-209">使用代码设置环境</span><span class="sxs-lookup"><span data-stu-id="84789-209">Set the environment in code</span></span>

<span data-ttu-id="84789-210">生成主机时，调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*>。</span><span class="sxs-lookup"><span data-stu-id="84789-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="84789-211">请参阅 <xref:fundamentals/host/generic-host#environmentname>。</span><span class="sxs-lookup"><span data-stu-id="84789-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="84789-212">按环境配置</span><span class="sxs-lookup"><span data-stu-id="84789-212">Configuration by environment</span></span>

<span data-ttu-id="84789-213">若要按环境加载配置，我们建议：</span><span class="sxs-lookup"><span data-stu-id="84789-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="84789-214">appsettings 文件 (appsettings.Environment>.json)   。</span><span class="sxs-lookup"><span data-stu-id="84789-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="84789-215">请参阅 <xref:fundamentals/configuration/index#json-configuration-provider>。</span><span class="sxs-lookup"><span data-stu-id="84789-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="84789-216">环境变量（在托管应用的每个系统上进行设置）。</span><span class="sxs-lookup"><span data-stu-id="84789-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="84789-217">请参见 <xref:fundamentals/host/generic-host#environmentname> 和 <xref:security/app-secrets#environment-variables>。</span><span class="sxs-lookup"><span data-stu-id="84789-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="84789-218">密码管理器（仅限开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="84789-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="84789-219">请参阅 <xref:security/app-secrets>。</span><span class="sxs-lookup"><span data-stu-id="84789-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="84789-220">基于环境的 Startup 类和方法</span><span class="sxs-lookup"><span data-stu-id="84789-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="84789-221">将 IWebHostEnvironment 注入 Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="84789-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="84789-222">将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入 `Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="84789-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="84789-223">当应用仅需为几个代码差异最小的环境调整 `Startup.Configure` 时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="84789-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="84789-224">将 IWebHostEnvironment 注入 Startup 类</span><span class="sxs-lookup"><span data-stu-id="84789-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="84789-225">将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入 `Startup` 构造函数。</span><span class="sxs-lookup"><span data-stu-id="84789-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="84789-226">当应用仅需为几个代码差异最小的环境配置 `Startup` 时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="84789-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="84789-227">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="84789-227">In the following example:</span></span>

* <span data-ttu-id="84789-228">环境保存在 `_env` 字段中。</span><span class="sxs-lookup"><span data-stu-id="84789-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="84789-229">`_env` 在 `ConfigureServices` 和 `Configure` 中用于根据应用的环境应用启动配置。</span><span class="sxs-lookup"><span data-stu-id="84789-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
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
### <a name="startup-class-conventions"></a><span data-ttu-id="84789-230">Startup 类约定</span><span class="sxs-lookup"><span data-stu-id="84789-230">Startup class conventions</span></span>

<span data-ttu-id="84789-231">当 ASP.NET Core 应用启动时，[Startup 类](xref:fundamentals/startup)启动应用。</span><span class="sxs-lookup"><span data-stu-id="84789-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="84789-232">应用可以为不同的环境定义单独的 `Startup` 类（例如 `StartupDevelopment`）。</span><span class="sxs-lookup"><span data-stu-id="84789-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="84789-233">在运行时选择适当的 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="84789-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="84789-234">优先考虑名称后缀与当前环境相匹配的类。</span><span class="sxs-lookup"><span data-stu-id="84789-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="84789-235">如果找不到匹配的 `Startup{EnvironmentName}`，就会使用 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="84789-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="84789-236">当应用需要为各环境之间存在许多代码差异的多个环境配置启动时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="84789-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="84789-237">若要实现基于环境的 `Startup` 类，请为使用中的每个环境创建 `Startup{EnvironmentName}` 类，并创建回退 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="84789-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="84789-238">使用接受程序集名称的 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 重载：</span><span class="sxs-lookup"><span data-stu-id="84789-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="84789-239">Startup 方法约定</span><span class="sxs-lookup"><span data-stu-id="84789-239">Startup method conventions</span></span>

<span data-ttu-id="84789-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 和 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) 支持窗体 `Configure<EnvironmentName>` 和 `Configure<EnvironmentName>Services` 的环境特定版本。</span><span class="sxs-lookup"><span data-stu-id="84789-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="84789-241">当应用需要为各环境之间存在许多代码差异的多个环境配置启动时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="84789-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="84789-242">其他资源</span><span class="sxs-lookup"><span data-stu-id="84789-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="84789-243">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="84789-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="84789-244">ASP.NET Core 基于使用环境变量的运行时环境配置应用行为。</span><span class="sxs-lookup"><span data-stu-id="84789-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="84789-245">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="84789-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="84789-246">环境</span><span class="sxs-lookup"><span data-stu-id="84789-246">Environments</span></span>

<span data-ttu-id="84789-247">ASP.NET Core 在应用启动时读取环境变量 `ASPNETCORE_ENVIRONMENT`，并将该值存储在 [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName) 中。</span><span class="sxs-lookup"><span data-stu-id="84789-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="84789-248">`ASPNETCORE_ENVIRONMENT` 可设置为任意值，但框架提供三个值：</span><span class="sxs-lookup"><span data-stu-id="84789-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="84789-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>（默认值）</span><span class="sxs-lookup"><span data-stu-id="84789-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="84789-250">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="84789-250">The preceding code:</span></span>

* <span data-ttu-id="84789-251">当 `ASPNETCORE_ENVIRONMENT` 设置为 `Development` 时，调用 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)。</span><span class="sxs-lookup"><span data-stu-id="84789-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="84789-252">当 `ASPNETCORE_ENVIRONMENT` 的值设置为下列之一时，调用 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)：</span><span class="sxs-lookup"><span data-stu-id="84789-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="84789-253">[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)使用 `IHostingEnvironment.EnvironmentName` 的值来包含或排除元素中的标记：</span><span class="sxs-lookup"><span data-stu-id="84789-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="84789-254">在 Windows 和 macOS 上，环境变量和值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="84789-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="84789-255">默认情况下，Linux 环境变量和值要区分大小写  。</span><span class="sxs-lookup"><span data-stu-id="84789-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="84789-256">开发</span><span class="sxs-lookup"><span data-stu-id="84789-256">Development</span></span>

<span data-ttu-id="84789-257">开发环境可以启用不应该在生产中公开的功能。</span><span class="sxs-lookup"><span data-stu-id="84789-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="84789-258">例如，ASP.NET Core 模板在开发环境中启用了[开发人员异常页](xref:fundamentals/error-handling#developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="84789-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="84789-259">本地计算机开发环境可以在项目的 Properties\launchSettings.json 文件中设置  。</span><span class="sxs-lookup"><span data-stu-id="84789-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="84789-260">在 launchSettings.json 中设置的环境值替代在系统环境中设置的值  。</span><span class="sxs-lookup"><span data-stu-id="84789-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="84789-261">以下 JSON 显示 launchSettings.json 文件中的三个配置文件  ：</span><span class="sxs-lookup"><span data-stu-id="84789-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="84789-262">launchSettings.json 中的 `applicationUrl` 属性可指定服务器 URL 的列表  。</span><span class="sxs-lookup"><span data-stu-id="84789-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="84789-263">在列表中的 URL 之间使用分号：</span><span class="sxs-lookup"><span data-stu-id="84789-263">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="84789-264">使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动应用时，使用具有 `"commandName": "Project"` 的第一个配置文件。</span><span class="sxs-lookup"><span data-stu-id="84789-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="84789-265">`commandName` 的值指定要启动的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="84789-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="84789-266">`commandName` 可为以下任一项：</span><span class="sxs-lookup"><span data-stu-id="84789-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="84789-267">`Project`（启动 Kestrel 的项目）</span><span class="sxs-lookup"><span data-stu-id="84789-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="84789-268">使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动应用时：</span><span class="sxs-lookup"><span data-stu-id="84789-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="84789-269">如果可用，读取 launchSettings.json  。</span><span class="sxs-lookup"><span data-stu-id="84789-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="84789-270">launchSettings.json 中的 `environmentVariables` 设置会替代环境变量  。</span><span class="sxs-lookup"><span data-stu-id="84789-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="84789-271">此时显示承载环境。</span><span class="sxs-lookup"><span data-stu-id="84789-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="84789-272">以下输出显示了使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动的应用：</span><span class="sxs-lookup"><span data-stu-id="84789-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="84789-273">Visual Studio 项目属性“调试”选项卡提供 GUI 来编辑 launchSettings.json 文件   ：</span><span class="sxs-lookup"><span data-stu-id="84789-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![项目属性设置环境变量](environments/_static/project-properties-debug.png)

<span data-ttu-id="84789-275">在 Web 服务器重新启动之前，对项目配置文件所做的更改可能不会生效。</span><span class="sxs-lookup"><span data-stu-id="84789-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="84789-276">必须重新启动 Kestrel 才能检测到对其环境所做的更改。</span><span class="sxs-lookup"><span data-stu-id="84789-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="84789-277">launchSettings.json 不应存储机密  。</span><span class="sxs-lookup"><span data-stu-id="84789-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="84789-278">[机密管理器工具](xref:security/app-secrets)可用于存储本地开发的机密。</span><span class="sxs-lookup"><span data-stu-id="84789-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="84789-279">使用 [Visual Studio Code](https://code.visualstudio.com/) 时，可以在 .vscode/launch.json 文件中设置环境变量  。</span><span class="sxs-lookup"><span data-stu-id="84789-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="84789-280">以下示例将环境设置为 `Development`：</span><span class="sxs-lookup"><span data-stu-id="84789-280">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="84789-281">使用与 Properties/launchSettings.json 相同的方法通过 `dotnet run` 启动应用时，不读取项目中的 .vscode/launch.json 文件   。</span><span class="sxs-lookup"><span data-stu-id="84789-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="84789-282">在没有 launchSettings.json 文件的 Development 环境中启动应用时，需要使用环境变量设置环境或者将命令行参数设为 `dotnet run` 命令  。</span><span class="sxs-lookup"><span data-stu-id="84789-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="84789-283">生产</span><span class="sxs-lookup"><span data-stu-id="84789-283">Production</span></span>

<span data-ttu-id="84789-284">Production 环境应配置为最大限度地提高安全性、性能和应用可靠性。</span><span class="sxs-lookup"><span data-stu-id="84789-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="84789-285">不同于开发的一些通用设置包括：</span><span class="sxs-lookup"><span data-stu-id="84789-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="84789-286">缓存。</span><span class="sxs-lookup"><span data-stu-id="84789-286">Caching.</span></span>
* <span data-ttu-id="84789-287">客户端资源被捆绑和缩小，并可能从 CDN 提供。</span><span class="sxs-lookup"><span data-stu-id="84789-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="84789-288">已禁用诊断错误页。</span><span class="sxs-lookup"><span data-stu-id="84789-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="84789-289">已启用友好错误页。</span><span class="sxs-lookup"><span data-stu-id="84789-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="84789-290">已启用生产记录和监视。</span><span class="sxs-lookup"><span data-stu-id="84789-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="84789-291">例如，[Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="84789-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="84789-292">设置环境</span><span class="sxs-lookup"><span data-stu-id="84789-292">Set the environment</span></span>

<span data-ttu-id="84789-293">通常，可以使用环境变量或平台设置来设置用于测试的特定环境。</span><span class="sxs-lookup"><span data-stu-id="84789-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="84789-294">如果未设置环境，默认值为 `Production`，这会禁用大多数调试功能。</span><span class="sxs-lookup"><span data-stu-id="84789-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="84789-295">设置环境的方法取决于操作系统。</span><span class="sxs-lookup"><span data-stu-id="84789-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="84789-296">构建主机时，应用读取的最后一个环境设置将决定应用的环境。</span><span class="sxs-lookup"><span data-stu-id="84789-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="84789-297">应用运行时无法更改应用的环境。</span><span class="sxs-lookup"><span data-stu-id="84789-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="84789-298">环境变量或平台设置</span><span class="sxs-lookup"><span data-stu-id="84789-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="84789-299">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="84789-299">Azure App Service</span></span>

<span data-ttu-id="84789-300">若要在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中设置环境，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="84789-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="84789-301">从“应用服务”边栏选项卡中选择应用  。</span><span class="sxs-lookup"><span data-stu-id="84789-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="84789-302">在“设置”组中，选择“配置”边栏选项卡   。</span><span class="sxs-lookup"><span data-stu-id="84789-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="84789-303">在“应用程序设置”选项卡中，选择“新建应用程序设置”。  </span><span class="sxs-lookup"><span data-stu-id="84789-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="84789-304">在“添加/编辑应用程序设置”窗口中，在“名称”中提供  `ASPNETCORE_ENVIRONMENT`  。</span><span class="sxs-lookup"><span data-stu-id="84789-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="84789-305">在“值”中提供环境（例如 `Staging`）  。</span><span class="sxs-lookup"><span data-stu-id="84789-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="84789-306">交换部署槽位时，如果希望环境设置保持当前槽位，请选中“部署槽位设置”复选框  。</span><span class="sxs-lookup"><span data-stu-id="84789-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="84789-307">有关详细信息，请参阅 Azure 文档中的[在 Azure 应用服务中设置过渡环境](/azure/app-service/web-sites-staged-publishing)。</span><span class="sxs-lookup"><span data-stu-id="84789-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="84789-308">选择“确定”以关闭“添加/编辑应用程序设置”窗口。  </span><span class="sxs-lookup"><span data-stu-id="84789-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="84789-309">选择“配置”边栏选项卡顶部的“保存”   。</span><span class="sxs-lookup"><span data-stu-id="84789-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="84789-310">在 Azure 门户中添加、更改或删除应用设置（环境变量）后，Azure 应用服务自动重启应用。</span><span class="sxs-lookup"><span data-stu-id="84789-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="84789-311">Windows</span><span class="sxs-lookup"><span data-stu-id="84789-311">Windows</span></span>

<span data-ttu-id="84789-312">若要在使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动该应用时为当前会话设置 `ASPNETCORE_ENVIRONMENT`，则使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="84789-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="84789-313">**命令提示符**</span><span class="sxs-lookup"><span data-stu-id="84789-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="84789-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="84789-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="84789-315">这些命令仅对当前窗口有效。</span><span class="sxs-lookup"><span data-stu-id="84789-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="84789-316">窗口关闭时，`ASPNETCORE_ENVIRONMENT` 设置将恢复为默认设置或计算机值。</span><span class="sxs-lookup"><span data-stu-id="84789-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="84789-317">若要在 Windows 中全局设置值，请采用下列两种方法之一：</span><span class="sxs-lookup"><span data-stu-id="84789-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="84789-318">依次打开“控制面板”  >“系统”  >“高级系统设置”  ，再添加或编辑“`ASPNETCORE_ENVIRONMENT`”值：</span><span class="sxs-lookup"><span data-stu-id="84789-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![系统高级属性](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 环境变量](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="84789-321">打开管理命令提示符并运行 `setx` 命令，或打开管理 PowerShell 命令提示符并运行 `[Environment]::SetEnvironmentVariable`：</span><span class="sxs-lookup"><span data-stu-id="84789-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="84789-322">**命令提示符**</span><span class="sxs-lookup"><span data-stu-id="84789-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="84789-323">`/M` 开关指明，在系统一级设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="84789-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="84789-324">如果未使用 `/M` 开关，就会为用户帐户设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="84789-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="84789-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="84789-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="84789-326">`Machine` 选项值指明，在系统一级设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="84789-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="84789-327">如果将选项值更改为 `User`，就会为用户帐户设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="84789-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="84789-328">如果全局设置 `ASPNETCORE_ENVIRONMENT` 环境变量，它就会对在值设置后打开的任何命令窗口中对 `dotnet run` 起作用。</span><span class="sxs-lookup"><span data-stu-id="84789-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="84789-329">**web.config**</span><span class="sxs-lookup"><span data-stu-id="84789-329">**web.config**</span></span>

<span data-ttu-id="84789-330">若要使用 web.config  设置 `ASPNETCORE_ENVIRONMENT` 环境变量，请参阅 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>的“设置环境变量”  部分。</span><span class="sxs-lookup"><span data-stu-id="84789-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="84789-331">**项目文件或发布配置文件**</span><span class="sxs-lookup"><span data-stu-id="84789-331">**Project file or publish profile**</span></span>

<span data-ttu-id="84789-332">**对于 Windows IIS 部署：** 将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="84789-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="84789-333">此方法在发布项目时设置 web.config  中的环境：</span><span class="sxs-lookup"><span data-stu-id="84789-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="84789-334">**每个 IIS 应用程序池**</span><span class="sxs-lookup"><span data-stu-id="84789-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="84789-335">若要为在独立应用池中运行的应用设置 `ASPNETCORE_ENVIRONMENT` 环境变量（IIS 10.0 或更高版本支持此操作），请参阅[环境变量 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”  部分。</span><span class="sxs-lookup"><span data-stu-id="84789-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="84789-336">为应用池设置 `ASPNETCORE_ENVIRONMENT` 环境变量后，它的值会替代系统级设置。</span><span class="sxs-lookup"><span data-stu-id="84789-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="84789-337">在 IIS 中托管应用并添加或更改 `ASPNETCORE_ENVIRONMENT` 环境变量时，请采用下列方法之一，让新值可供应用拾取：</span><span class="sxs-lookup"><span data-stu-id="84789-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="84789-338">在命令提示符处依次执行 `net stop was /y` 和 `net start w3svc`。</span><span class="sxs-lookup"><span data-stu-id="84789-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="84789-339">重新启动服务器。</span><span class="sxs-lookup"><span data-stu-id="84789-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="84789-340">macOS</span><span class="sxs-lookup"><span data-stu-id="84789-340">macOS</span></span>

<span data-ttu-id="84789-341">设置 macOS 的当前环境可在运行应用时完成：</span><span class="sxs-lookup"><span data-stu-id="84789-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="84789-342">或者，在运行应用前使用 `export` 设置环境：</span><span class="sxs-lookup"><span data-stu-id="84789-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="84789-343">在 .bashrc 或 .bash_profile 文件中设置计算机级环境变量   。</span><span class="sxs-lookup"><span data-stu-id="84789-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="84789-344">使用任意文本编辑器编辑文件。</span><span class="sxs-lookup"><span data-stu-id="84789-344">Edit the file using any text editor.</span></span> <span data-ttu-id="84789-345">添加以下语句：</span><span class="sxs-lookup"><span data-stu-id="84789-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="84789-346">Linux</span><span class="sxs-lookup"><span data-stu-id="84789-346">Linux</span></span>

<span data-ttu-id="84789-347">对于 Linux 发行版，请在命令提示符中使用 `export` 命令进行基于会话的变量设置，并使用 bash_profile 文件进行计算机级环境设置  。</span><span class="sxs-lookup"><span data-stu-id="84789-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="84789-348">使用代码设置环境</span><span class="sxs-lookup"><span data-stu-id="84789-348">Set the environment in code</span></span>

<span data-ttu-id="84789-349">生成主机时，调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*>。</span><span class="sxs-lookup"><span data-stu-id="84789-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="84789-350">请参阅 <xref:fundamentals/host/web-host#environment>。</span><span class="sxs-lookup"><span data-stu-id="84789-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="84789-351">按环境配置</span><span class="sxs-lookup"><span data-stu-id="84789-351">Configuration by environment</span></span>

<span data-ttu-id="84789-352">若要按环境加载配置，我们建议：</span><span class="sxs-lookup"><span data-stu-id="84789-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="84789-353">appsettings 文件 (appsettings.Environment>.json)   。</span><span class="sxs-lookup"><span data-stu-id="84789-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="84789-354">请参阅 <xref:fundamentals/configuration/index#json-configuration-provider>。</span><span class="sxs-lookup"><span data-stu-id="84789-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="84789-355">环境变量（在托管应用的每个系统上进行设置）。</span><span class="sxs-lookup"><span data-stu-id="84789-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="84789-356">请参见 <xref:fundamentals/host/web-host#environment> 和 <xref:security/app-secrets#environment-variables>。</span><span class="sxs-lookup"><span data-stu-id="84789-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="84789-357">密码管理器（仅限开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="84789-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="84789-358">请参阅 <xref:security/app-secrets>。</span><span class="sxs-lookup"><span data-stu-id="84789-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="84789-359">基于环境的 Startup 类和方法</span><span class="sxs-lookup"><span data-stu-id="84789-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="84789-360">将 IHostingEnvironment 注入 Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="84789-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="84789-361">将 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> 注入 `Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="84789-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="84789-362">当应用仅需为几个代码差异最小的环境配置 `Startup.Configure` 时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="84789-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="84789-363">将 IHostingEnvironment 注入 Startup 类</span><span class="sxs-lookup"><span data-stu-id="84789-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="84789-364">将 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> 注入 `Startup` 构造函数，并将服务分配给一个字段，以便在整个 `Startup` 类中使用。</span><span class="sxs-lookup"><span data-stu-id="84789-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="84789-365">当应用需为几个代码差异最小的环境配置启动时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="84789-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="84789-366">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="84789-366">In the following example:</span></span>

* <span data-ttu-id="84789-367">环境保存在 `_env` 字段中。</span><span class="sxs-lookup"><span data-stu-id="84789-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="84789-368">`_env` 在 `ConfigureServices` 和 `Configure` 中用于根据应用的环境应用启动配置。</span><span class="sxs-lookup"><span data-stu-id="84789-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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

### <a name="startup-class-conventions"></a><span data-ttu-id="84789-369">Startup 类约定</span><span class="sxs-lookup"><span data-stu-id="84789-369">Startup class conventions</span></span>

<span data-ttu-id="84789-370">当 ASP.NET Core 应用启动时，[Startup 类](xref:fundamentals/startup)启动应用。</span><span class="sxs-lookup"><span data-stu-id="84789-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="84789-371">应用可以为不同的环境定义单独的 `Startup` 类（例如 `StartupDevelopment`）。</span><span class="sxs-lookup"><span data-stu-id="84789-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="84789-372">在运行时选择适当的 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="84789-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="84789-373">优先考虑名称后缀与当前环境相匹配的类。</span><span class="sxs-lookup"><span data-stu-id="84789-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="84789-374">如果找不到匹配的 `Startup{EnvironmentName}`，就会使用 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="84789-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="84789-375">当应用需要为各环境之间存在许多代码差异的多个环境配置启动时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="84789-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="84789-376">若要实现基于环境的 `Startup` 类，请为使用中的每个环境创建 `Startup{EnvironmentName}` 类，并创建回退 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="84789-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="84789-377">使用接受程序集名称的 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 重载：</span><span class="sxs-lookup"><span data-stu-id="84789-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="84789-378">Startup 方法约定</span><span class="sxs-lookup"><span data-stu-id="84789-378">Startup method conventions</span></span>

<span data-ttu-id="84789-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 和 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) 支持窗体 `Configure<EnvironmentName>` 和 `Configure<EnvironmentName>Services` 的环境特定版本。</span><span class="sxs-lookup"><span data-stu-id="84789-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="84789-380">当应用需要为各环境之间存在许多代码差异的多个环境配置启动时，这种方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="84789-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="84789-381">其他资源</span><span class="sxs-lookup"><span data-stu-id="84789-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
