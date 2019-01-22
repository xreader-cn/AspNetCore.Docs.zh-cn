---
title: 对 Azure 应用服务上的 ASP.NET Core 进行故障排除
author: guardrex
description: 了解如何诊断 ASP.NET Core Azure 应用服务部署问题。
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: 65a5e355bc15db6de9060331395c441160c8b62d
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341636"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a><span data-ttu-id="98689-103">对 Azure 应用服务上的 ASP.NET Core 进行故障排除</span><span class="sxs-lookup"><span data-stu-id="98689-103">Troubleshoot ASP.NET Core on Azure App Service</span></span>

<span data-ttu-id="98689-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="98689-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="98689-105">本文说明了如何使用 Azure 应用服务的诊断工具诊断 ASP.NET Core 应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="98689-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue using Azure App Service's diagnostic tools.</span></span> <span data-ttu-id="98689-106">有关其他故障排除建议，请参阅 Azure 文档中的 [Azure 应用服务诊断概述](/azure/app-service/app-service-diagnostics)和[如何：在 Azure 应用服务中监视应用](/azure/app-service/web-sites-monitor)。</span><span class="sxs-lookup"><span data-stu-id="98689-106">For additional troubleshooting advice, see [Azure App Service diagnostics overview](/azure/app-service/app-service-diagnostics) and [How to: Monitor Apps in Azure App Service](/azure/app-service/web-sites-monitor) in the Azure documentation.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="98689-107">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="98689-107">App startup errors</span></span>

<span data-ttu-id="98689-108">**502.5 进程故障**</span><span class="sxs-lookup"><span data-stu-id="98689-108">**502.5 Process Failure**</span></span>  
<span data-ttu-id="98689-109">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="98689-109">The worker process fails.</span></span> <span data-ttu-id="98689-110">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="98689-110">The app doesn't start.</span></span>

<span data-ttu-id="98689-111">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="98689-111">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="98689-112">检查应用程序事件日志通常可帮助解决此类型的问题。</span><span class="sxs-lookup"><span data-stu-id="98689-112">Examining the Application Event Log often helps troubleshoot this type of problem.</span></span> <span data-ttu-id="98689-113">[应用程序事件日志](#application-event-log)部分中介绍了访问日志。</span><span class="sxs-lookup"><span data-stu-id="98689-113">Accessing the log is explained in the [Application Event Log](#application-event-log) section.</span></span>

<span data-ttu-id="98689-114">配置错误的应用导致工作进程失败时，将返回“502.5 进程故障”错误页面：</span><span class="sxs-lookup"><span data-stu-id="98689-114">The *502.5 Process Failure* error page is returned when a misconfigured app causes the worker process to fail:</span></span>

![显示“502.5 进程故障”页面的浏览器窗口](troubleshoot/_static/process-failure-page.png)

<span data-ttu-id="98689-116">**500 内部服务器错误**</span><span class="sxs-lookup"><span data-stu-id="98689-116">**500 Internal Server Error**</span></span>  
<span data-ttu-id="98689-117">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="98689-117">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="98689-118">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="98689-118">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="98689-119">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="98689-119">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="98689-120">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="98689-120">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="98689-121">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="98689-121">From the server's perspective, that's correct.</span></span> <span data-ttu-id="98689-122">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="98689-122">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="98689-123">[在 Kudu 控制台中运行应用](#run-the-app-in-the-kudu-console)或[启用 ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)以解决该问题。</span><span class="sxs-lookup"><span data-stu-id="98689-123">[Run the app in the Kudu console](#run-the-app-in-the-kudu-console) or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

<span data-ttu-id="98689-124">**连接重置**</span><span class="sxs-lookup"><span data-stu-id="98689-124">**Connection reset**</span></span>

<span data-ttu-id="98689-125">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="98689-125">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="98689-126">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="98689-126">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="98689-127">此类型的错误在客户端上显示为“连接重置”错误。</span><span class="sxs-lookup"><span data-stu-id="98689-127">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="98689-128">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="98689-128">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="98689-129">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="98689-129">Default startup limits</span></span>

<span data-ttu-id="98689-130">ASP.NET Core 模块的默认“startupTimeLimit”配置为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="98689-130">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="98689-131">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="98689-131">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="98689-132">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="98689-132">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="98689-133">解决应用启动错误</span><span class="sxs-lookup"><span data-stu-id="98689-133">Troubleshoot app startup errors</span></span>

### <a name="application-event-log"></a><span data-ttu-id="98689-134">应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="98689-134">Application Event Log</span></span>

<span data-ttu-id="98689-135">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡：</span><span class="sxs-lookup"><span data-stu-id="98689-135">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="98689-136">在 Azure 门户中打开“应用服务”中的应用。</span><span class="sxs-lookup"><span data-stu-id="98689-136">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="98689-137">选择“诊断并解决问题”。</span><span class="sxs-lookup"><span data-stu-id="98689-137">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="98689-138">选择“诊断工具”标题。</span><span class="sxs-lookup"><span data-stu-id="98689-138">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="98689-139">在“支持工具”下，选择“应用程序事件”按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-139">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="98689-140">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2 条目提供的最新错误。</span><span class="sxs-lookup"><span data-stu-id="98689-140">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="98689-141">使用“诊断并解决问题”边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="98689-141">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="98689-142">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="98689-142">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="98689-143">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-143">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98689-144">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="98689-144">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="98689-145">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="98689-145">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="98689-146">打开 LogFiles 文件夹。</span><span class="sxs-lookup"><span data-stu-id="98689-146">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="98689-147">选择 eventlog.xml 文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="98689-147">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="98689-148">检查日志。</span><span class="sxs-lookup"><span data-stu-id="98689-148">Examine the log.</span></span> <span data-ttu-id="98689-149">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="98689-149">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="98689-150">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="98689-150">Run the app in the Kudu console</span></span>

<span data-ttu-id="98689-151">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="98689-151">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="98689-152">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="98689-152">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="98689-153">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="98689-153">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="98689-154">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-154">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98689-155">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="98689-155">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="98689-156">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="98689-156">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="98689-157">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="98689-157">Open the folders to the path **site** > **wwwroot**.</span></span>
1. <span data-ttu-id="98689-158">在控制台中，通过执行应用的程序集来运行该应用。</span><span class="sxs-lookup"><span data-stu-id="98689-158">In the console, run the app by executing the app's assembly.</span></span>
   * <span data-ttu-id="98689-159">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则使用 dotnet.exe 运行应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="98689-159">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), run the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="98689-160">在以下命令中，替换 `<assembly_name>` 的应用程序集的名称：`dotnet .\<assembly_name>.dll`</span><span class="sxs-lookup"><span data-stu-id="98689-160">In the following command, substitute the name of the app's assembly for `<assembly_name>`: `dotnet .\<assembly_name>.dll`</span></span>
   * <span data-ttu-id="98689-161">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，则运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="98689-161">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), run the app's executable.</span></span> <span data-ttu-id="98689-162">在以下命令中，替换 `<assembly_name>` 的应用程序集的名称：`<assembly_name>.exe`</span><span class="sxs-lookup"><span data-stu-id="98689-162">In the following command, substitute the name of the app's assembly for `<assembly_name>`: `<assembly_name>.exe`</span></span>
1. <span data-ttu-id="98689-163">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="98689-163">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="98689-164">ASP.NET Core 模块 stdout 日志</span><span class="sxs-lookup"><span data-stu-id="98689-164">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="98689-165">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="98689-165">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="98689-166">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="98689-166">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="98689-167">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="98689-167">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="98689-168">在“选择问题类别”下，选择“Web 应用关闭”按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-168">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="98689-169">在“建议的解决方案” > “启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-169">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="98689-170">在 Kudu 诊断控制台中，打开路径“站点 > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="98689-170">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="98689-171">向下滚动以在列表底部显示“web.config”文件。</span><span class="sxs-lookup"><span data-stu-id="98689-171">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="98689-172">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="98689-172">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="98689-173">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="98689-173">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="98689-174">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="98689-174">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="98689-175">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="98689-175">Make a request to the app.</span></span>
1. <span data-ttu-id="98689-176">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="98689-176">Return to the Azure portal.</span></span> <span data-ttu-id="98689-177">选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="98689-177">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="98689-178">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-178">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98689-179">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="98689-179">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="98689-180">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="98689-180">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="98689-181">选择“LogFiles”文件夹。</span><span class="sxs-lookup"><span data-stu-id="98689-181">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="98689-182">检查“已修改”列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="98689-182">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="98689-183">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="98689-183">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="98689-184">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="98689-184">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="98689-185">在 Kudu 诊断控制台中，返回到路径“site > wwwroot”以显示 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="98689-185">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="98689-186">通过选择铅笔图标再次打开 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="98689-186">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="98689-187">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="98689-187">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="98689-188">选择“保存”以保存文件。</span><span class="sxs-lookup"><span data-stu-id="98689-188">Select **Save** to save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="98689-189">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="98689-189">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="98689-190">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="98689-190">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="98689-191">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="98689-191">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="98689-192">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="98689-192">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="98689-193">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="98689-193">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a><span data-ttu-id="98689-194">ASP.NET Core 模块调试日志</span><span class="sxs-lookup"><span data-stu-id="98689-194">ASP.NET Core Module debug log</span></span>

<span data-ttu-id="98689-195">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="98689-195">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="98689-196">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="98689-196">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="98689-197">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="98689-197">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="98689-198">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="98689-198">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="98689-199">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="98689-199">Redeploy the app.</span></span>
   * <span data-ttu-id="98689-200">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中：</span><span class="sxs-lookup"><span data-stu-id="98689-200">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="98689-201">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="98689-201">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="98689-202">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-202">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98689-203">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="98689-203">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="98689-204">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="98689-204">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="98689-205">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="98689-205">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="98689-206">通过选择铅笔按钮编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="98689-206">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="98689-207">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="98689-207">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="98689-208">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-208">Select the **Save** button.</span></span>
1. <span data-ttu-id="98689-209">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="98689-209">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="98689-210">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-210">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98689-211">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="98689-211">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="98689-212">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="98689-212">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="98689-213">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="98689-213">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="98689-214">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="98689-214">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="98689-215">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="98689-215">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="98689-216">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="98689-216">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="98689-217">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="98689-217">Disable debug logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="98689-218">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="98689-218">To disable the enhanced debug log, peform either of the following:</span></span>
   * <span data-ttu-id="98689-219">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用。</span><span class="sxs-lookup"><span data-stu-id="98689-219">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
   * <span data-ttu-id="98689-220">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分。</span><span class="sxs-lookup"><span data-stu-id="98689-220">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="98689-221">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="98689-221">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="98689-222">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="98689-222">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="98689-223">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="98689-223">There's no limit on log file size.</span></span> <span data-ttu-id="98689-224">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="98689-224">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="98689-225">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="98689-225">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="98689-226">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="98689-226">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

## <a name="common-startup-errors"></a><span data-ttu-id="98689-227">常见启动错误</span><span class="sxs-lookup"><span data-stu-id="98689-227">Common startup errors</span></span>

<span data-ttu-id="98689-228">请参阅 <xref:host-and-deploy/azure-iis-errors-reference>。</span><span class="sxs-lookup"><span data-stu-id="98689-228">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="98689-229">参考主题介绍了阻止应用启动的大部分常见问题。</span><span class="sxs-lookup"><span data-stu-id="98689-229">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="slow-or-hanging-app"></a><span data-ttu-id="98689-230">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="98689-230">Slow or hanging app</span></span>

<span data-ttu-id="98689-231">当应用响应缓慢或挂起请求时，请参阅[解决 Azure 应用服务中 Web 应用性能缓慢的问题](/azure/app-service/app-service-web-troubleshoot-performance-degradation)以获取调试指导。</span><span class="sxs-lookup"><span data-stu-id="98689-231">When an app responds slowly or hangs on a request, see [Troubleshoot slow web app performance issues in Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation) for debugging guidance.</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="98689-232">远程调试</span><span class="sxs-lookup"><span data-stu-id="98689-232">Remote debugging</span></span>

<span data-ttu-id="98689-233">请参见下面的主题：</span><span class="sxs-lookup"><span data-stu-id="98689-233">See the following topics:</span></span>

* <span data-ttu-id="98689-234">[使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除的远程调试 Web 应用部分](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="98689-234">[Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (Azure documentation)</span></span>
* <span data-ttu-id="98689-235">[在 Visual Studio 2017 的 Azure 中的 IIS 上远程调试 ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)（Visual Studio 文档）</span><span class="sxs-lookup"><span data-stu-id="98689-235">[Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure) (Visual Studio documentation)</span></span>

## <a name="application-insights"></a><span data-ttu-id="98689-236">Application Insights</span><span class="sxs-lookup"><span data-stu-id="98689-236">Application Insights</span></span>

<span data-ttu-id="98689-237">[Application Insights](https://azure.microsoft.com/services/application-insights/) 提供来自 Azure 应用服务中托管的应用的遥测，包括错误日志记录和报告功能。</span><span class="sxs-lookup"><span data-stu-id="98689-237">[Application Insights](https://azure.microsoft.com/services/application-insights/) provides telemetry from apps hosted in the Azure App Service, including error logging and reporting features.</span></span> <span data-ttu-id="98689-238">当应用的日志记录功能变得可用时，Application Insights 只能报告应用启动后出现的错误。</span><span class="sxs-lookup"><span data-stu-id="98689-238">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="98689-239">有关详细信息，请参阅[用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="98689-239">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="monitoring-blades"></a><span data-ttu-id="98689-240">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="98689-240">Monitoring blades</span></span>

<span data-ttu-id="98689-241">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="98689-241">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="98689-242">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="98689-242">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="98689-243">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="98689-243">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="98689-244">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="98689-244">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="98689-245">在“开发工具”边栏选项卡部分中，选择“扩展”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="98689-245">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="98689-246">“ASP.NET Core 扩展”应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="98689-246">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="98689-247">如果未安装扩展，请选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-247">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="98689-248">从列表中选择“ASP.NET Core 扩展”。</span><span class="sxs-lookup"><span data-stu-id="98689-248">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="98689-249">选择“确定”以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="98689-249">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="98689-250">选择“添加扩展”边栏选项卡上的“确定”。</span><span class="sxs-lookup"><span data-stu-id="98689-250">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="98689-251">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="98689-251">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="98689-252">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="98689-252">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="98689-253">在 Azure 门户中，选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="98689-253">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="98689-254">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-254">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="98689-255">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="98689-255">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="98689-256">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="98689-256">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="98689-257">打开路径“site > wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="98689-257">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="98689-258">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="98689-258">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="98689-259">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="98689-259">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="98689-260">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="98689-260">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="98689-261">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="98689-261">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="98689-262">在 Azure 门户中，选择“诊断日志”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="98689-262">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="98689-263">选择“应用程序日志记录(文件系统)”和“详细错误消息”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="98689-263">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="98689-264">选择边栏选项卡顶部的“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="98689-264">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="98689-265">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="98689-265">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span> 
1. <span data-ttu-id="98689-266">选择“日志流”边栏选项卡，将在门户中的“诊断日志”边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="98689-266">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="98689-267">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="98689-267">Make a request to the app.</span></span>
1. <span data-ttu-id="98689-268">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="98689-268">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="98689-269">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="98689-269">Be sure to disable stdout logging when troubleshooting is complete.</span></span> <span data-ttu-id="98689-270">请参阅 [ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)部分中的说明。</span><span class="sxs-lookup"><span data-stu-id="98689-270">See the instructions in the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) section.</span></span>

<span data-ttu-id="98689-271">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="98689-271">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="98689-272">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="98689-272">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="98689-273">从侧栏的“支持工具”区域中选择“失败请求跟踪日志”。</span><span class="sxs-lookup"><span data-stu-id="98689-273">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="98689-274">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="98689-274">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="98689-275">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="98689-275">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="98689-276">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="98689-276">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="98689-277">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="98689-277">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="98689-278">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="98689-278">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="98689-279">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="98689-279">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98689-280">其他资源</span><span class="sxs-lookup"><span data-stu-id="98689-280">Additional resources</span></span>

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="98689-281">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="98689-281">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="98689-282">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="98689-282">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="98689-283">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="98689-283">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="98689-284">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="98689-284">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="98689-285">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="98689-285">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="98689-286">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="98689-286">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
