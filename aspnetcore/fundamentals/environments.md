---
title: 在 ASP.NET Core 中使用多个环境
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中控制多个环境的应用行为。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/environments
ms.openlocfilehash: 4fc43935aa058efc4497d3d9eb607df6c0899443
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809232"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="8f791-103">在 ASP.NET Core 中使用多个环境</span><span class="sxs-lookup"><span data-stu-id="8f791-103">Use multiple environments in ASP.NET Core</span></span>

<span data-ttu-id="8f791-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8f791-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8f791-105">ASP.NET Core 基于使用环境变量的运行时环境配置应用行为。</span><span class="sxs-lookup"><span data-stu-id="8f791-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="8f791-106">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8f791-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="8f791-107">环境</span><span class="sxs-lookup"><span data-stu-id="8f791-107">Environments</span></span>

<span data-ttu-id="8f791-108">ASP.NET Core 在应用启动时读取环境变量 `ASPNETCORE_ENVIRONMENT`，并将该值存储在 [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname) 中。</span><span class="sxs-lookup"><span data-stu-id="8f791-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname).</span></span> <span data-ttu-id="8f791-109">`ASPNETCORE_ENVIRONMENT` 可设置为任意值，但框架支持[三个值](/dotnet/api/microsoft.aspnetcore.hosting.environmentname)：[Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development)、[Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) 和 [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production)。</span><span class="sxs-lookup"><span data-stu-id="8f791-109">You can set `ASPNETCORE_ENVIRONMENT` to any value, but [three values](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) are supported by the framework: [Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging), and [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production).</span></span> <span data-ttu-id="8f791-110">如果未设置 `ASPNETCORE_ENVIRONMENT`，则默认为 `Production`。</span><span class="sxs-lookup"><span data-stu-id="8f791-110">If `ASPNETCORE_ENVIRONMENT` isn't set, it defaults to `Production`.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="8f791-111">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="8f791-111">The preceding code:</span></span>

* <span data-ttu-id="8f791-112">当 `ASPNETCORE_ENVIRONMENT` 设置为 `Development` 时，调用 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)。</span><span class="sxs-lookup"><span data-stu-id="8f791-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="8f791-113">当 `ASPNETCORE_ENVIRONMENT` 的值设置为下列之一时，调用 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)：</span><span class="sxs-lookup"><span data-stu-id="8f791-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="8f791-114">[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)使用 `IHostingEnvironment.EnvironmentName` 的值来包含或排除元素中的标记：</span><span class="sxs-lookup"><span data-stu-id="8f791-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="8f791-115">在 Windows 和 macOS 上，环境变量和值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="8f791-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="8f791-116">默认情况下，Linux 环境变量和值要区分大小写。</span><span class="sxs-lookup"><span data-stu-id="8f791-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="8f791-117">开发</span><span class="sxs-lookup"><span data-stu-id="8f791-117">Development</span></span>

<span data-ttu-id="8f791-118">开发环境可以启用不应该在生产中公开的功能。</span><span class="sxs-lookup"><span data-stu-id="8f791-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="8f791-119">例如，ASP.NET Core 模板在开发环境中启用了[开发人员异常页](xref:fundamentals/error-handling#developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="8f791-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="8f791-120">本地计算机开发环境可以在项目的 Properties\launchSettings.json 文件中设置。</span><span class="sxs-lookup"><span data-stu-id="8f791-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="8f791-121">在 launchSettings.json 中设置的环境值替代在系统环境中设置的值。</span><span class="sxs-lookup"><span data-stu-id="8f791-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="8f791-122">以下 JSON 显示 launchSettings.json 文件中的三个配置文件：</span><span class="sxs-lookup"><span data-stu-id="8f791-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="8f791-123">launchSettings.json 中的 `applicationUrl` 属性可指定服务器 URL 的列表。</span><span class="sxs-lookup"><span data-stu-id="8f791-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="8f791-124">在列表中的 URL 之间使用分号：</span><span class="sxs-lookup"><span data-stu-id="8f791-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="8f791-125">使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动应用时，使用具有 `"commandName": "Project"` 的第一个配置文件。</span><span class="sxs-lookup"><span data-stu-id="8f791-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="8f791-126">`commandName` 的值指定要启动的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="8f791-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="8f791-127">`commandName` 可为以下任一项：</span><span class="sxs-lookup"><span data-stu-id="8f791-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="8f791-128">`Project`（启动 Kestrel 的项目）</span><span class="sxs-lookup"><span data-stu-id="8f791-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="8f791-129">使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动应用时：</span><span class="sxs-lookup"><span data-stu-id="8f791-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="8f791-130">如果可用，读取 launchSettings.json。</span><span class="sxs-lookup"><span data-stu-id="8f791-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="8f791-131">launchSettings.json 中的 `environmentVariables` 设置会替代环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f791-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="8f791-132">此时显示承载环境。</span><span class="sxs-lookup"><span data-stu-id="8f791-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="8f791-133">以下输出显示了使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动的应用：</span><span class="sxs-lookup"><span data-stu-id="8f791-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="8f791-134">Visual Studio 项目属性“调试”选项卡提供 GUI 来编辑 launchSettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="8f791-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![项目属性设置环境变量](environments/_static/project-properties-debug.png)

<span data-ttu-id="8f791-136">在 Web 服务器重新启动之前，对项目配置文件所做的更改可能不会生效。</span><span class="sxs-lookup"><span data-stu-id="8f791-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="8f791-137">必须重新启动 Kestrel 才能检测到对其环境所做的更改。</span><span class="sxs-lookup"><span data-stu-id="8f791-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="8f791-138">launchSettings.json 不应存储机密。</span><span class="sxs-lookup"><span data-stu-id="8f791-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="8f791-139">[机密管理器工具](xref:security/app-secrets)可用于存储本地开发的机密。</span><span class="sxs-lookup"><span data-stu-id="8f791-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="8f791-140">使用 [Visual Studio Code](https://code.visualstudio.com/) 时，可以在 .vscode/launch.json 文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f791-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="8f791-141">以下示例将环境设置为 `Development`：</span><span class="sxs-lookup"><span data-stu-id="8f791-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="8f791-142">使用与 Properties/launchSettings.json 相同的方法通过 `dotnet run` 启动应用时，不读取项目中的 .vscode/launch.json 文件。</span><span class="sxs-lookup"><span data-stu-id="8f791-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="8f791-143">在没有 launchSettings.json 文件的 Development 环境中启动应用时，需要使用环境变量设置环境或者将命令行参数设为 `dotnet run` 命令。</span><span class="sxs-lookup"><span data-stu-id="8f791-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="8f791-144">生产</span><span class="sxs-lookup"><span data-stu-id="8f791-144">Production</span></span>

<span data-ttu-id="8f791-145">Production 环境应配置为最大限度地提高安全性、性能和应用可靠性。</span><span class="sxs-lookup"><span data-stu-id="8f791-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="8f791-146">不同于开发的一些通用设置包括：</span><span class="sxs-lookup"><span data-stu-id="8f791-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="8f791-147">缓存。</span><span class="sxs-lookup"><span data-stu-id="8f791-147">Caching.</span></span>
* <span data-ttu-id="8f791-148">客户端资源被捆绑和缩小，并可能从 CDN 提供。</span><span class="sxs-lookup"><span data-stu-id="8f791-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="8f791-149">已禁用诊断错误页。</span><span class="sxs-lookup"><span data-stu-id="8f791-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="8f791-150">已启用友好错误页。</span><span class="sxs-lookup"><span data-stu-id="8f791-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="8f791-151">已启用生产记录和监视。</span><span class="sxs-lookup"><span data-stu-id="8f791-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="8f791-152">例如，[Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="8f791-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="8f791-153">设置环境</span><span class="sxs-lookup"><span data-stu-id="8f791-153">Set the environment</span></span>

<span data-ttu-id="8f791-154">为测试设置特定环境通常很有用。</span><span class="sxs-lookup"><span data-stu-id="8f791-154">It's often useful to set a specific environment for testing.</span></span> <span data-ttu-id="8f791-155">如果未设置环境，默认值为 `Production`，这会禁用大多数调试功能。</span><span class="sxs-lookup"><span data-stu-id="8f791-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="8f791-156">设置环境的方法取决于操作系统。</span><span class="sxs-lookup"><span data-stu-id="8f791-156">The method for setting the environment depends on the operating system.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="8f791-157">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="8f791-157">Azure App Service</span></span>

<span data-ttu-id="8f791-158">若要在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中设置环境，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="8f791-158">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="8f791-159">从“应用服务”边栏选项卡中选择应用。</span><span class="sxs-lookup"><span data-stu-id="8f791-159">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="8f791-160">在“设置”组中，选择“应用程序设置”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8f791-160">In the **SETTINGS** group, select the **Application settings** blade.</span></span>
1. <span data-ttu-id="8f791-161">在“应用程序设置”区域中，选择“添加新设置”。</span><span class="sxs-lookup"><span data-stu-id="8f791-161">In the **Application settings** area, select **Add new setting**.</span></span>
1. <span data-ttu-id="8f791-162">在“输入名称”中提供 `ASPNETCORE_ENVIRONMENT`。</span><span class="sxs-lookup"><span data-stu-id="8f791-162">For **Enter a name**, provide `ASPNETCORE_ENVIRONMENT`.</span></span> <span data-ttu-id="8f791-163">在“输入值”中提供环境（例如 `Staging`）。</span><span class="sxs-lookup"><span data-stu-id="8f791-163">For **Enter a value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="8f791-164">交换部署槽位时，如果希望环境设置保持当前槽位，请选中“槽位设置”复选框。</span><span class="sxs-lookup"><span data-stu-id="8f791-164">Select the **Slot Setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="8f791-165">有关详细信息，请参阅 [Azure 文档：交换哪些设置？](/azure/app-service/web-sites-staged-publishing)。</span><span class="sxs-lookup"><span data-stu-id="8f791-165">For more information, see [Azure Documentation: Which settings are swapped?](/azure/app-service/web-sites-staged-publishing).</span></span>
1. <span data-ttu-id="8f791-166">选择边栏选项卡顶部的“保存”。</span><span class="sxs-lookup"><span data-stu-id="8f791-166">Select **Save** at the top of the blade.</span></span>

<span data-ttu-id="8f791-167">在 Azure 门户中添加、更改或删除应用设置（环境变量）后，Azure 应用服务自动重启应用。</span><span class="sxs-lookup"><span data-stu-id="8f791-167">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="8f791-168">Windows</span><span class="sxs-lookup"><span data-stu-id="8f791-168">Windows</span></span>

<span data-ttu-id="8f791-169">若要在使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动该应用时为当前会话设置 `ASPNETCORE_ENVIRONMENT`，则使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="8f791-169">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="8f791-170">**命令提示符**</span><span class="sxs-lookup"><span data-stu-id="8f791-170">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="8f791-171">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="8f791-171">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="8f791-172">这些命令仅对当前窗口有效。</span><span class="sxs-lookup"><span data-stu-id="8f791-172">These commands only take effect for the current window.</span></span> <span data-ttu-id="8f791-173">窗口关闭时，`ASPNETCORE_ENVIRONMENT` 设置将恢复为默认设置或计算机值。</span><span class="sxs-lookup"><span data-stu-id="8f791-173">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="8f791-174">若要在 Windows 中全局设置值，请采用下列两种方法之一：</span><span class="sxs-lookup"><span data-stu-id="8f791-174">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="8f791-175">依次打开“控制面板” > “系统” > “高级系统设置”，再添加或编辑“`ASPNETCORE_ENVIRONMENT`”值：</span><span class="sxs-lookup"><span data-stu-id="8f791-175">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![系统高级属性](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 环境变量](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="8f791-178">打开管理命令提示符并运行 `setx` 命令，或打开管理 PowerShell 命令提示符并运行 `[Environment]::SetEnvironmentVariable`：</span><span class="sxs-lookup"><span data-stu-id="8f791-178">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="8f791-179">**命令提示符**</span><span class="sxs-lookup"><span data-stu-id="8f791-179">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="8f791-180">`/M` 开关指明，在系统一级设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f791-180">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="8f791-181">如果未使用 `/M` 开关，就会为用户帐户设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f791-181">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="8f791-182">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="8f791-182">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="8f791-183">`Machine` 选项值指明，在系统一级设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f791-183">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="8f791-184">如果将选项值更改为 `User`，就会为用户帐户设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f791-184">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="8f791-185">如果全局设置 `ASPNETCORE_ENVIRONMENT` 环境变量，它就会对在值设置后打开的任何命令窗口中对 `dotnet run` 起作用。</span><span class="sxs-lookup"><span data-stu-id="8f791-185">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="8f791-186">**web.config**</span><span class="sxs-lookup"><span data-stu-id="8f791-186">**web.config**</span></span>

<span data-ttu-id="8f791-187">若要使用 web.config 设置 `ASPNETCORE_ENVIRONMENT` 环境变量，请参阅 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>的“设置环境变量”部分。</span><span class="sxs-lookup"><span data-stu-id="8f791-187">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span> <span data-ttu-id="8f791-188">使用 web.config 设置 `ASPNETCORE_ENVIRONMENT` 环境变量后，它的值会替代系统级设置。</span><span class="sxs-lookup"><span data-stu-id="8f791-188">When the `ASPNETCORE_ENVIRONMENT` environment variable is set with *web.config*, its value overrides a setting at the system level.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8f791-189">**项目文件或发布配置文件**</span><span class="sxs-lookup"><span data-stu-id="8f791-189">**Project file or publish profile**</span></span>

<span data-ttu-id="8f791-190">**对于 Windows IIS 部署：** 将 `<EnvironmentName>` 属性包含在发布配置文件（.pubxml）或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="8f791-190">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="8f791-191">此方法在发布项目时设置 web.config 中的环境：</span><span class="sxs-lookup"><span data-stu-id="8f791-191">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

::: moniker-end

<span data-ttu-id="8f791-192">**每个 IIS 应用程序池**</span><span class="sxs-lookup"><span data-stu-id="8f791-192">**Per IIS Application Pool**</span></span>

<span data-ttu-id="8f791-193">若要为在独立应用池中运行的应用设置 `ASPNETCORE_ENVIRONMENT` 环境变量（IIS 10.0 或更高版本支持此操作），请参阅[环境变量 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="8f791-193">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="8f791-194">为应用池设置 `ASPNETCORE_ENVIRONMENT` 环境变量后，它的值会替代系统级设置。</span><span class="sxs-lookup"><span data-stu-id="8f791-194">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8f791-195">在 IIS 中托管应用并添加或更改 `ASPNETCORE_ENVIRONMENT` 环境变量时，请采用下列方法之一，让新值可供应用拾取：</span><span class="sxs-lookup"><span data-stu-id="8f791-195">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="8f791-196">在命令提示符处依次执行 `net stop was /y` 和 `net start w3svc`。</span><span class="sxs-lookup"><span data-stu-id="8f791-196">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="8f791-197">重启服务器。</span><span class="sxs-lookup"><span data-stu-id="8f791-197">Restart the server.</span></span>

### <a name="macos"></a><span data-ttu-id="8f791-198">macOS</span><span class="sxs-lookup"><span data-stu-id="8f791-198">macOS</span></span>

<span data-ttu-id="8f791-199">设置 macOS 的当前环境可在运行应用时完成：</span><span class="sxs-lookup"><span data-stu-id="8f791-199">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="8f791-200">或者，在运行应用前使用 `export` 设置环境：</span><span class="sxs-lookup"><span data-stu-id="8f791-200">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="8f791-201">在 .bashrc 或 .bash_profile 文件中设置计算机级环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f791-201">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="8f791-202">使用任意文本编辑器编辑文件。</span><span class="sxs-lookup"><span data-stu-id="8f791-202">Edit the file using any text editor.</span></span> <span data-ttu-id="8f791-203">添加以下语句：</span><span class="sxs-lookup"><span data-stu-id="8f791-203">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a><span data-ttu-id="8f791-204">Linux</span><span class="sxs-lookup"><span data-stu-id="8f791-204">Linux</span></span>

<span data-ttu-id="8f791-205">对于 Linux 发行版，请在命令提示符中使用 `export` 命令进行基于会话的变量设置，并使用 bash_profile 文件进行计算机级环境设置。</span><span class="sxs-lookup"><span data-stu-id="8f791-205">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="8f791-206">按环境配置</span><span class="sxs-lookup"><span data-stu-id="8f791-206">Configuration by environment</span></span>

<span data-ttu-id="8f791-207">若要按环境加载配置，我们建议：</span><span class="sxs-lookup"><span data-stu-id="8f791-207">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="8f791-208">appsettings 文件 (appsettings.\<Environment>.json)。</span><span class="sxs-lookup"><span data-stu-id="8f791-208">*appsettings* files (*appsettings.\<Environment>.json*).</span></span> <span data-ttu-id="8f791-209">请参阅[配置：文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="8f791-209">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider).</span></span>
* <span data-ttu-id="8f791-210">环境变量（在托管应用的每个系统上进行设置）。</span><span class="sxs-lookup"><span data-stu-id="8f791-210">environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="8f791-211">请参阅[配置：文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)和[开发环境中应用密码的安全存储：环境变量](xref:security/app-secrets#environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="8f791-211">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider) and [Safe storage of app secrets in development: Environment variables](xref:security/app-secrets#environment-variables).</span></span>
* <span data-ttu-id="8f791-212">密码管理器（仅限开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="8f791-212">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="8f791-213">请参阅 <xref:security/app-secrets>。</span><span class="sxs-lookup"><span data-stu-id="8f791-213">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="8f791-214">基于环境的 Startup 类和方法</span><span class="sxs-lookup"><span data-stu-id="8f791-214">Environment-based Startup class and methods</span></span>

### <a name="startup-class-conventions"></a><span data-ttu-id="8f791-215">Startup 类约定</span><span class="sxs-lookup"><span data-stu-id="8f791-215">Startup class conventions</span></span>

<span data-ttu-id="8f791-216">当 ASP.NET Core 应用启动时，[Startup 类](xref:fundamentals/startup)启动应用。</span><span class="sxs-lookup"><span data-stu-id="8f791-216">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="8f791-217">应用可以为不同的环境单独定义 `Startup` 类（例如，`StartupDevelopment`），相应 `Startup` 类会在运行时得到选择。</span><span class="sxs-lookup"><span data-stu-id="8f791-217">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`), and the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="8f791-218">优先考虑名称后缀与当前环境相匹配的类。</span><span class="sxs-lookup"><span data-stu-id="8f791-218">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="8f791-219">如果找不到匹配的 `Startup{EnvironmentName}`，就会使用 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="8f791-219">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span>

<span data-ttu-id="8f791-220">若要实现基于环境的 `Startup` 类，请为使用中的每个环境创建 `Startup{EnvironmentName}` 类，并创建回退 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="8f791-220">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}
```

<span data-ttu-id="8f791-221">使用接受程序集名称的 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 重载：</span><span class="sxs-lookup"><span data-stu-id="8f791-221">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="8f791-222">Startup 方法约定</span><span class="sxs-lookup"><span data-stu-id="8f791-222">Startup method conventions</span></span>

<span data-ttu-id="8f791-223">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 和 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) 支持窗体 `Configure<EnvironmentName>` 和 `Configure<EnvironmentName>Services` 的环境特定版本：</span><span class="sxs-lookup"><span data-stu-id="8f791-223">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`:</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="8f791-224">其他资源</span><span class="sxs-lookup"><span data-stu-id="8f791-224">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="8f791-225">IHostingEnvironment.EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="8f791-225">IHostingEnvironment.EnvironmentName</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname)
