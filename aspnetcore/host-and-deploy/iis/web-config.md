---
title: web.config 文件
author: rick-anderson
description: 了解 web.config 文件中的内容，以及如何配置不同的 ASP.NET Core 模块选项。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: edeef31042547db79fcec98f1236787f78e187a5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057279"
---
# <a name="webconfig-file"></a><span data-ttu-id="9fda7-103">`web.config` 文件</span><span class="sxs-lookup"><span data-stu-id="9fda7-103">`web.config` file</span></span>

<span data-ttu-id="9fda7-104">`web.config` 文件由 IIS 和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)读取，用于使用 IIS 配置已托管的应用。</span><span class="sxs-lookup"><span data-stu-id="9fda7-104">The `web.config` is a file that is read by IIS and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to configure an app hosted with IIS.</span></span>

## <a name="webconfig-file-location"></a><span data-ttu-id="9fda7-105">`web.config` 文件位置</span><span class="sxs-lookup"><span data-stu-id="9fda7-105">`web.config` file location</span></span>

<span data-ttu-id="9fda7-106">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，`web.config` 文件必须存在于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="9fda7-106">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the `web.config` file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="9fda7-107">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="9fda7-107">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="9fda7-108">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 `web.config` 文件。</span><span class="sxs-lookup"><span data-stu-id="9fda7-108">The `web.config` file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="9fda7-109">敏感文件存在于应用的物理路径中，如 `{ASSEMBLY}.runtimeconfig.json`、`{ASSEMBLY}.xml`（XML 文档注释）和 `{ASSEMBLY}.deps.json`，其中 `{ASSEMBLY}` 占位符为程序集名称。</span><span class="sxs-lookup"><span data-stu-id="9fda7-109">Sensitive files exist on the app's physical path, such as `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (XML Documentation comments), and `{ASSEMBLY}.deps.json`, where the placeholder `{ASSEMBLY}` is the assembly name.</span></span> <span data-ttu-id="9fda7-110">如果有 `web.config` 文件且站点正常启动时，IIS 在收到敏感文件请求时不会提供这些敏感文件。</span><span class="sxs-lookup"><span data-stu-id="9fda7-110">When the `web.config` file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="9fda7-111">如果 `web.config` 文件缺失、名字错误或者无法将站点配置为正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="9fda7-111">If the `web.config` file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="9fda7-112">`web.config` 文件必须始终存在于部署中、名称正确以及能够将站点配置为正常启动。切勿从生产部署中删除 `web.config` 文件。</span><span class="sxs-lookup"><span data-stu-id="9fda7-112">**The `web.config` file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the `web.config` file from a production deployment.**</span></span>

<span data-ttu-id="9fda7-113">如果项目中没有 `web.config` 文件，则该文件是使用正确的 `processPath` 和 `arguments`（用于配置 ASP.NET Core 模块）创建的，并且已被移到[发布的输出](xref:host-and-deploy/directory-structure)中。</span><span class="sxs-lookup"><span data-stu-id="9fda7-113">If a `web.config` file isn't present in the project, the file is created with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="9fda7-114">如果项目中没有 `web.config` 文件，则该文件是通过正确的 `processPath` 和 `arguments`（用于配置 ASP.NET Core 模块）转换的，并且已被移到发布的输出中。</span><span class="sxs-lookup"><span data-stu-id="9fda7-114">If a `web.config` file is present in the project, the file is transformed with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="9fda7-115">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="9fda7-115">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="9fda7-116">`web.config` 文件可能会提供控制活动 IIS 模块的额外 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="9fda7-116">The `web.config` file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="9fda7-117">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="9fda7-117">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="9fda7-118">发布项目时，`web.config` 文件的创建、转换和发布是由 MSBuild 目标 (`_TransformWebConfig`) 处理的。</span><span class="sxs-lookup"><span data-stu-id="9fda7-118">Creating, transforming, and publishing the `web.config` file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="9fda7-119">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="9fda7-119">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="9fda7-120">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="9fda7-120">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="9fda7-121">为了防止 Web SDK 转换 `web.config` 文件，请在项目文件中使用 `<IsTransformWebConfigDisabled>` 属性：</span><span class="sxs-lookup"><span data-stu-id="9fda7-121">To prevent the Web SDK from transforming the `web.config` file, use the `<IsTransformWebConfigDisabled>` property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="9fda7-122">禁用 Web SDK 对文件的转换时，`processPath` 和 `arguments` 应由开发人员手动设置。</span><span class="sxs-lookup"><span data-stu-id="9fda7-122">When disabling the Web SDK from transforming the file, the `processPath` and `arguments` should be manually set by the developer.</span></span> <span data-ttu-id="9fda7-123">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="9fda7-123">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a><span data-ttu-id="9fda7-124">使用 `web.config` 配置 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="9fda7-124">Configuration of ASP.NET Core Module with `web.config`</span></span>

<span data-ttu-id="9fda7-125">在站点的 `web.config` 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="9fda7-125">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's `web.config` file.</span></span>

<span data-ttu-id="9fda7-126">以下 `web.config` 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="9fda7-126">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="9fda7-127">以下 `web.config` 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="9fda7-127">The following `web.config` is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="9fda7-128">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> 属性设置为 `false`，表示 [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="9fda7-128">The <xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="9fda7-129">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="9fda7-129">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="9fda7-130">该路径会将 stdout 日志保存到 `LogFiles` 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="9fda7-130">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="9fda7-131">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="9fda7-131">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="9fda7-132">`aspNetCore` 元素的属性</span><span class="sxs-lookup"><span data-stu-id="9fda7-132">Attributes of the `aspNetCore` element</span></span>

| <span data-ttu-id="9fda7-133">属性</span><span class="sxs-lookup"><span data-stu-id="9fda7-133">Attribute</span></span> | <span data-ttu-id="9fda7-134">描述</span><span class="sxs-lookup"><span data-stu-id="9fda7-134">Description</span></span> | <span data-ttu-id="9fda7-135">默认</span><span class="sxs-lookup"><span data-stu-id="9fda7-135">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="9fda7-136">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-136">Optional string attribute.</span></span></p><p><span data-ttu-id="9fda7-137">`processPath` 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="9fda7-137">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="9fda7-138">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-138">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9fda7-139">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 `web.config` 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="9fda7-139">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the `web.config` takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="9fda7-140">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-140">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9fda7-141">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”转发到在 `%ASPNETCORE_PORT%` 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="9fda7-141">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="9fda7-142">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="9fda7-142">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="9fda7-143">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-143">Optional string attribute.</span></span></p><p><span data-ttu-id="9fda7-144">将托管模型指定为进程内 (`InProcess`/`inprocess`) 或进程外 (`OutOfProcess`/`outofprocess`)。</span><span class="sxs-lookup"><span data-stu-id="9fda7-144">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="9fda7-145">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-145">Optional integer attribute.</span></span></p><p><span data-ttu-id="9fda7-146">指定每个应用均可启动的 `processPath` 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="9fda7-146">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="9fda7-147">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="9fda7-147">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="9fda7-148">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="9fda7-148">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="9fda7-149">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-149">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="9fda7-150">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="9fda7-150">Default: `1`</span></span><br><span data-ttu-id="9fda7-151">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="9fda7-151">Min: `1`</span></span><br><span data-ttu-id="9fda7-152">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="9fda7-152">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="9fda7-153">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-153">Required string attribute.</span></span></p><p><span data-ttu-id="9fda7-154">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="9fda7-154">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="9fda7-155">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="9fda7-155">Relative paths are supported.</span></span> <span data-ttu-id="9fda7-156">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="9fda7-156">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="9fda7-157">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-157">Optional integer attribute.</span></span></p><p><span data-ttu-id="9fda7-158">指定允许 `processPath` 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="9fda7-158">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="9fda7-159">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="9fda7-159">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="9fda7-160">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="9fda7-160">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="9fda7-161">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="9fda7-161">Default: `10`</span></span><br><span data-ttu-id="9fda7-162">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="9fda7-162">Min: `0`</span></span><br><span data-ttu-id="9fda7-163">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="9fda7-163">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="9fda7-164">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-164">Optional timespan attribute.</span></span></p><p><span data-ttu-id="9fda7-165">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="9fda7-165">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="9fda7-166">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="9fda7-166">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="9fda7-167">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="9fda7-167">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="9fda7-168">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="9fda7-168">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="9fda7-169">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="9fda7-169">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="9fda7-170">在分钟或秒钟值中使用“`60`”会导致“500 - 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="9fda7-170">Use of `60` in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="9fda7-171">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="9fda7-171">Default: `00:02:00`</span></span><br><span data-ttu-id="9fda7-172">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="9fda7-172">Min: `00:00:00`</span></span><br><span data-ttu-id="9fda7-173">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="9fda7-173">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="9fda7-174">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-174">Optional integer attribute.</span></span></p><p><span data-ttu-id="9fda7-175">检测到 `app_offline.htm` 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="9fda7-175">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="9fda7-176">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="9fda7-176">Default: `10`</span></span><br><span data-ttu-id="9fda7-177">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="9fda7-177">Min: `0`</span></span><br><span data-ttu-id="9fda7-178">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="9fda7-178">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="9fda7-179">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-179">Optional integer attribute.</span></span></p><p><span data-ttu-id="9fda7-180">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="9fda7-180">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="9fda7-181">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="9fda7-181">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="9fda7-182">进程内托管时：不会重新启动该进程，也不会使用 `rapidFailsPerMinute` 设置 。</span><span class="sxs-lookup"><span data-stu-id="9fda7-182">When hosting *in-process* : The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="9fda7-183">进程外托管时：模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 `rapidFailsPerMinute` 次。</span><span class="sxs-lookup"><span data-stu-id="9fda7-183">When hosting *out-of-process* : The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="9fda7-184">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="9fda7-184">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="9fda7-185">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="9fda7-185">Default: `120`</span></span><br><span data-ttu-id="9fda7-186">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="9fda7-186">Min: `0`</span></span><br><span data-ttu-id="9fda7-187">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="9fda7-187">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="9fda7-188">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9fda7-189">如果为 true，`processPath` 中指定的进程的 `stdout` 和 `stderr` 将重定向到 `stdoutLogFile` 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="9fda7-189">If true, `stdout` and `stderr` for the process specified in `processPath` are redirected to the file specified in `stdoutLogFile`.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="9fda7-190">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="9fda7-190">Optional string attribute.</span></span></p><p><span data-ttu-id="9fda7-191">指定在其中记录 `processPath` 中指定进程的 `stdout` 和 `stderr` 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="9fda7-191">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="9fda7-192">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="9fda7-192">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="9fda7-193">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="9fda7-193">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="9fda7-194">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="9fda7-194">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="9fda7-195">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (`.log`) 添加到 `stdoutLogFile` 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="9fda7-195">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="9fda7-196">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 `logs` 文件夹中保存为 `stdout_20180205194132_1934.log`。</span><span class="sxs-lookup"><span data-stu-id="9fda7-196">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="9fda7-197">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="9fda7-197">Set environment variables</span></span>

<span data-ttu-id="9fda7-198">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="9fda7-198">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="9fda7-199">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="9fda7-199">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="9fda7-200">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="9fda7-200">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="9fda7-201">以下示例在 `web.config` 中设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="9fda7-201">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="9fda7-202">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="9fda7-202">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="9fda7-203">开发人员可能会暂时在 `web.config` 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="9fda7-203">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="9fda7-204">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="9fda7-204">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="9fda7-205">直接在 `web.config` 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在[发布配置文件 (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="9fda7-205">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="9fda7-206">此方法在发布项目时设置 `web.config` 中的环境：</span><span class="sxs-lookup"><span data-stu-id="9fda7-206">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="9fda7-207">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="9fda7-207">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="9fda7-208">使用 `web.config` 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="9fda7-208">Configuration of IIS with `web.config`</span></span>

<span data-ttu-id="9fda7-209">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 `web.config` 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="9fda7-209">IIS configuration is influenced by the `<system.webServer>` section of `web.config` for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="9fda7-210">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="9fda7-210">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="9fda7-211">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 `web.config` 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="9fda7-211">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's `web.config` file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="9fda7-212">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="9fda7-212">For more information, see the following topics:</span></span>

* [<span data-ttu-id="9fda7-213">`<system.webServer>` 的配置参考</span><span class="sxs-lookup"><span data-stu-id="9fda7-213">Configuration reference for `<system.webServer>`</span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="9fda7-214">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 `<environmentVariables>`](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“`AppCmd.exe` 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="9fda7-214">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *`AppCmd.exe` command* section of the [Environment Variables `<environmentVariables>`](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="9fda7-215">`web.config` 的配置节</span><span class="sxs-lookup"><span data-stu-id="9fda7-215">Configuration sections of `web.config`</span></span>

<span data-ttu-id="9fda7-216">ASP.NET Core 应用不会使用 `web.config` 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="9fda7-216">Configuration sections of ASP.NET 4.x apps in `web.config` aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="9fda7-217">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="9fda7-217">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="9fda7-218">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="9fda7-218">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="9fda7-219">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="9fda7-219">Transform web.config</span></span>

<span data-ttu-id="9fda7-220">如果在发布时需要转换 `web.config`，请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="9fda7-220">If you need to transform `web.config` on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="9fda7-221">你需要在发布时转换 `web.config`，以根据配置、配置文件或环境来设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="9fda7-221">You might need to transform `web.config` on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9fda7-222">其他资源</span><span class="sxs-lookup"><span data-stu-id="9fda7-222">Additional resources</span></span>

* [<span data-ttu-id="9fda7-223">IIS \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="9fda7-223">IIS \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
