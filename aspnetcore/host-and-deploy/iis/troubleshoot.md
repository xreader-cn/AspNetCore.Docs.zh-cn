---
title: 对 IIS 上的 ASP.NET Core 进行故障排除
author: guardrex
description: 了解如何诊断 ASP.NET Core 应用的 Internet Information Services (IIS) 部署的问题。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 4df370dd9b1a5a651bcf767b8b9ace4220bdc345
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313655"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="f8839-103">对 IIS 上的 ASP.NET Core 进行故障排除</span><span class="sxs-lookup"><span data-stu-id="f8839-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="f8839-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f8839-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f8839-105">本文说明了如何在使用 [Internet Information Services (IIS)](/iis) 托管时诊断 ASP.NET Core 应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="f8839-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="f8839-106">本文提供的信息适用于在 Windows Server 和 Windows 桌面上的 IIS 中托管。</span><span class="sxs-lookup"><span data-stu-id="f8839-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

<span data-ttu-id="f8839-107">其他故障排除主题：</span><span class="sxs-lookup"><span data-stu-id="f8839-107">Additional troubleshooting topics:</span></span>

* <span data-ttu-id="f8839-108">Azure 应用服务还使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)和 IIS 来托管应用。</span><span class="sxs-lookup"><span data-stu-id="f8839-108">Azure App Service also uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps.</span></span> <span data-ttu-id="f8839-109">有关专门针对 Azure 应用服务提出的疑难解答建议，请参阅 <xref:host-and-deploy/azure-apps/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="f8839-109">For troubleshooting advice that pertains specifically to Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
* <span data-ttu-id="f8839-110"><xref:fundamentals/error-handling> 介绍了如何在本地系统上进行开发期间处理 ASP.NET Core 应用中的错误。</span><span class="sxs-lookup"><span data-stu-id="f8839-110"><xref:fundamentals/error-handling> covers how to handle errors in ASP.NET Core apps during development on a local system.</span></span>
* <span data-ttu-id="f8839-111">[了解如何使用 Visual Studio 进行调试](/visualstudio/debugger/getting-started-with-the-debugger)介绍了 Visual Studio 调试器的功能。</span><span class="sxs-lookup"><span data-stu-id="f8839-111">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) introduces the features of the Visual Studio debugger.</span></span>
* <span data-ttu-id="f8839-112">[使用 Visual Studio Code 进行调试](https://code.visualstudio.com/docs/editor/debugging)介绍了 Visual Studio Code 中内置的调试支持。</span><span class="sxs-lookup"><span data-stu-id="f8839-112">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) describes the debugging support built into Visual Studio Code.</span></span>

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="f8839-113">解决应用启动错误</span><span class="sxs-lookup"><span data-stu-id="f8839-113">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="f8839-114">启用 ASP.NET Core 模块调试日志</span><span class="sxs-lookup"><span data-stu-id="f8839-114">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="f8839-115">将以下处理程序设置添加到应用的 web.config  文件以启用 ASP.NET Core 模块调试日志：</span><span class="sxs-lookup"><span data-stu-id="f8839-115">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="f8839-116">确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。</span><span class="sxs-lookup"><span data-stu-id="f8839-116">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="f8839-117">应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="f8839-117">Application Event Log</span></span>

<span data-ttu-id="f8839-118">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="f8839-118">Access the Application Event Log:</span></span>

1. <span data-ttu-id="f8839-119">打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。</span><span class="sxs-lookup"><span data-stu-id="f8839-119">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="f8839-120">在“事件查看器”  中，打开“Windows 日志”  节点。</span><span class="sxs-lookup"><span data-stu-id="f8839-120">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="f8839-121">选择“应用程序”  以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="f8839-121">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="f8839-122">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="f8839-122">Search for errors associated with the failing app.</span></span> <span data-ttu-id="f8839-123">错误具有“源”  列中“IIS AspNetCore 模块”  或“IIS Express AspNetCore 模块”  的值。</span><span class="sxs-lookup"><span data-stu-id="f8839-123">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="f8839-124">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="f8839-124">Run the app at a command prompt</span></span>

<span data-ttu-id="f8839-125">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="f8839-125">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="f8839-126">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="f8839-126">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="f8839-127">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="f8839-127">Framework-dependent deployment</span></span>

<span data-ttu-id="f8839-128">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="f8839-128">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="f8839-129">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe  执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="f8839-129">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="f8839-130">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="f8839-130">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="f8839-131">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="f8839-131">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="f8839-132">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="f8839-132">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="f8839-133">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="f8839-133">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="f8839-134">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="f8839-134">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="f8839-135">独立部署</span><span class="sxs-lookup"><span data-stu-id="f8839-135">Self-contained deployment</span></span>

<span data-ttu-id="f8839-136">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="f8839-136">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="f8839-137">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="f8839-137">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="f8839-138">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="f8839-138">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="f8839-139">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="f8839-139">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="f8839-140">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="f8839-140">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="f8839-141">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="f8839-141">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="f8839-142">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="f8839-142">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="f8839-143">ASP.NET Core 模块 stdout 日志</span><span class="sxs-lookup"><span data-stu-id="f8839-143">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="f8839-144">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f8839-144">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="f8839-145">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8839-145">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="f8839-146">如果 logs  文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8839-146">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="f8839-147">有关如何启用 MSBuild 以在部署中自动创建 logs  文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="f8839-147">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="f8839-148">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="f8839-148">Edit the *web.config* file.</span></span> <span data-ttu-id="f8839-149">将“stdoutLogEnabled”  设置为 `true` 并更改“stdoutLogFile”  路径以指向 logs  文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="f8839-149">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="f8839-150">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="f8839-150">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="f8839-151">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="f8839-151">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="f8839-152">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”  。</span><span class="sxs-lookup"><span data-stu-id="f8839-152">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="f8839-153">请确保应用程序池的标识具有对日志文件夹的写入权限  。</span><span class="sxs-lookup"><span data-stu-id="f8839-153">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="f8839-154">保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="f8839-154">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="f8839-155">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="f8839-155">Make a request to the app.</span></span>
1. <span data-ttu-id="f8839-156">导航到 logs  文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8839-156">Navigate to the *logs* folder.</span></span> <span data-ttu-id="f8839-157">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="f8839-157">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="f8839-158">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="f8839-158">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f8839-159">故障排除完成后，禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="f8839-159">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="f8839-160">编辑 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="f8839-160">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="f8839-161">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="f8839-161">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="f8839-162">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="f8839-162">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="f8839-163">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="f8839-163">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="f8839-164">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="f8839-164">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="f8839-165">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="f8839-165">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f8839-166">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="f8839-166">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="f8839-167">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="f8839-167">Enable the Developer Exception Page</span></span>

<span data-ttu-id="f8839-168">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="f8839-168">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="f8839-169">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="f8839-169">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

<span data-ttu-id="f8839-170">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="f8839-170">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="f8839-171">在故障排除后从 web.config  文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="f8839-171">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="f8839-172">有关设置 web.config  中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="f8839-172">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="f8839-173">常见启动错误</span><span class="sxs-lookup"><span data-stu-id="f8839-173">Common startup errors</span></span>

<span data-ttu-id="f8839-174">请参阅 <xref:host-and-deploy/azure-iis-errors-reference>。</span><span class="sxs-lookup"><span data-stu-id="f8839-174">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="f8839-175">参考主题介绍了阻止应用启动的大部分常见问题。</span><span class="sxs-lookup"><span data-stu-id="f8839-175">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="f8839-176">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="f8839-176">Obtain data from an app</span></span>

<span data-ttu-id="f8839-177">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="f8839-177">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="f8839-178">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="f8839-178">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="f8839-179">创建转储</span><span class="sxs-lookup"><span data-stu-id="f8839-179">Create a dump</span></span>

<span data-ttu-id="f8839-180">转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。</span><span class="sxs-lookup"><span data-stu-id="f8839-180">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="f8839-181">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="f8839-181">App crashes or encounters an exception</span></span>

<span data-ttu-id="f8839-182">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="f8839-182">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="f8839-183">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="f8839-183">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="f8839-184">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="f8839-184">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="f8839-185">运行 [EnableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="f8839-185">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="f8839-186">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="f8839-186">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="f8839-187">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="f8839-187">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="f8839-188">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="f8839-188">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="f8839-189">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="f8839-189">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="f8839-190">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="f8839-190">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="f8839-191">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：</span><span class="sxs-lookup"><span data-stu-id="f8839-191">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="f8839-192">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="f8839-192">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="f8839-193">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="f8839-193">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="f8839-194">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="f8839-194">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="f8839-195">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="f8839-195">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="f8839-196">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="f8839-196">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="f8839-197">分析转储</span><span class="sxs-lookup"><span data-stu-id="f8839-197">Analyze the dump</span></span>

<span data-ttu-id="f8839-198">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="f8839-198">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="f8839-199">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="f8839-199">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="f8839-200">远程调试</span><span class="sxs-lookup"><span data-stu-id="f8839-200">Remote debugging</span></span>

<span data-ttu-id="f8839-201">请参阅 Visual Studio 文档中的[在 Visual Studio 2017 中远程调试远程 IIS 计算机上的 ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)。</span><span class="sxs-lookup"><span data-stu-id="f8839-201">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="f8839-202">Application Insights</span><span class="sxs-lookup"><span data-stu-id="f8839-202">Application Insights</span></span>

<span data-ttu-id="f8839-203">[Application Insights](/azure/application-insights/) 提供来自 IIS 托管的应用的遥测，包括错误日志记录和报告功能。</span><span class="sxs-lookup"><span data-stu-id="f8839-203">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="f8839-204">当应用的日志记录功能变得可用时，Application Insights 只能报告应用启动后出现的错误。</span><span class="sxs-lookup"><span data-stu-id="f8839-204">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="f8839-205">有关详细信息，请参阅[用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="f8839-205">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="f8839-206">其他建议</span><span class="sxs-lookup"><span data-stu-id="f8839-206">Additional advice</span></span>

<span data-ttu-id="f8839-207">有时，正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内升级包版本后立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="f8839-207">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="f8839-208">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="f8839-208">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="f8839-209">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="f8839-209">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="f8839-210">删除 bin  和 obj  文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8839-210">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="f8839-211">清除 %UserProfile%\\.nuget\\packages  和 %LocalAppData%\\Nuget\\v3-cache  中的包缓存。</span><span class="sxs-lookup"><span data-stu-id="f8839-211">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="f8839-212">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="f8839-212">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="f8839-213">确认在重新部署应用之前已完全删除服务器上的先前部署。</span><span class="sxs-lookup"><span data-stu-id="f8839-213">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="f8839-214">清除包缓存的简便方法是从命令提示符执行 `dotnet nuget locals all --clear`。</span><span class="sxs-lookup"><span data-stu-id="f8839-214">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="f8839-215">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="f8839-215">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="f8839-216">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="f8839-216">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8839-217">其他资源</span><span class="sxs-lookup"><span data-stu-id="f8839-217">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
