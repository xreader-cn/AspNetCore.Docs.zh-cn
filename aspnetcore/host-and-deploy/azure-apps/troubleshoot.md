---
title: 对 Azure 应用服务上的 ASP.NET Core 进行故障排除
author: guardrex
description: 了解如何诊断 ASP.NET Core Azure 应用服务部署问题。
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: d78499c1a82a011239f6b62b546f304a5d5017e2
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313760"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a><span data-ttu-id="6883f-103">对 Azure 应用服务上的 ASP.NET Core 进行故障排除</span><span class="sxs-lookup"><span data-stu-id="6883f-103">Troubleshoot ASP.NET Core on Azure App Service</span></span>

<span data-ttu-id="6883f-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6883f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="6883f-105">本文说明了如何使用 Azure 应用服务的诊断工具诊断 ASP.NET Core 应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="6883f-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue using Azure App Service's diagnostic tools.</span></span> <span data-ttu-id="6883f-106">有关其他故障排除建议，请参阅 Azure 文档中的 [Azure 应用服务诊断概述](/azure/app-service/app-service-diagnostics)和[如何：在 Azure 应用服务中监视应用](/azure/app-service/web-sites-monitor)。</span><span class="sxs-lookup"><span data-stu-id="6883f-106">For additional troubleshooting advice, see [Azure App Service diagnostics overview](/azure/app-service/app-service-diagnostics) and [How to: Monitor Apps in Azure App Service](/azure/app-service/web-sites-monitor) in the Azure documentation.</span></span>

<span data-ttu-id="6883f-107">其他故障排除主题：</span><span class="sxs-lookup"><span data-stu-id="6883f-107">Additional troubleshooting topics:</span></span>

* <span data-ttu-id="6883f-108">IIS 还使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)来托管应用。</span><span class="sxs-lookup"><span data-stu-id="6883f-108">IIS also uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host apps.</span></span> <span data-ttu-id="6883f-109">有关专门针对 IIS 提出的疑难解答建议，请参阅 <xref:host-and-deploy/iis/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="6883f-109">For troubleshooting advice that pertains specifically to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>
* <span data-ttu-id="6883f-110"><xref:fundamentals/error-handling> 介绍了如何在本地系统上进行开发期间处理 ASP.NET Core 应用中的错误。</span><span class="sxs-lookup"><span data-stu-id="6883f-110"><xref:fundamentals/error-handling> covers how to handle errors in ASP.NET Core apps during development on a local system.</span></span>
* <span data-ttu-id="6883f-111">[了解如何使用 Visual Studio 进行调试](/visualstudio/debugger/getting-started-with-the-debugger)介绍了 Visual Studio 调试器的功能。</span><span class="sxs-lookup"><span data-stu-id="6883f-111">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) introduces the features of the Visual Studio debugger.</span></span>
* <span data-ttu-id="6883f-112">[使用 Visual Studio Code 进行调试](https://code.visualstudio.com/docs/editor/debugging)介绍了 Visual Studio Code 中内置的调试支持。</span><span class="sxs-lookup"><span data-stu-id="6883f-112">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) describes the debugging support built into Visual Studio Code.</span></span>

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="6883f-113">解决应用启动错误</span><span class="sxs-lookup"><span data-stu-id="6883f-113">Troubleshoot app startup errors</span></span>

### <a name="application-event-log"></a><span data-ttu-id="6883f-114">应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="6883f-114">Application Event Log</span></span>

<span data-ttu-id="6883f-115">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡  ：</span><span class="sxs-lookup"><span data-stu-id="6883f-115">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="6883f-116">在 Azure 门户中打开“应用服务”中的应用  。</span><span class="sxs-lookup"><span data-stu-id="6883f-116">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="6883f-117">选择“诊断并解决问题”  。</span><span class="sxs-lookup"><span data-stu-id="6883f-117">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="6883f-118">选择“诊断工具”标题  。</span><span class="sxs-lookup"><span data-stu-id="6883f-118">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="6883f-119">在“支持工具”下，选择“应用程序事件”按钮   。</span><span class="sxs-lookup"><span data-stu-id="6883f-119">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="6883f-120">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2 条目提供的最新错误    。</span><span class="sxs-lookup"><span data-stu-id="6883f-120">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="6883f-121">使用“诊断并解决问题”  边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="6883f-121">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="6883f-122">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="6883f-122">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="6883f-123">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="6883f-123">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6883f-124">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6883f-124">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="6883f-125">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="6883f-125">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="6883f-126">打开 LogFiles  文件夹。</span><span class="sxs-lookup"><span data-stu-id="6883f-126">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="6883f-127">选择 eventlog.xml  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="6883f-127">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="6883f-128">检查日志。</span><span class="sxs-lookup"><span data-stu-id="6883f-128">Examine the log.</span></span> <span data-ttu-id="6883f-129">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="6883f-129">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="6883f-130">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="6883f-130">Run the app in the Kudu console</span></span>

<span data-ttu-id="6883f-131">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="6883f-131">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="6883f-132">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="6883f-132">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="6883f-133">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="6883f-133">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="6883f-134">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="6883f-134">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6883f-135">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6883f-135">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="6883f-136">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="6883f-136">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="6883f-137">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="6883f-137">Test a 32-bit (x86) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="6883f-138">当前版本</span><span class="sxs-lookup"><span data-stu-id="6883f-138">Current release</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="6883f-139">运行应用：</span><span class="sxs-lookup"><span data-stu-id="6883f-139">Run the app:</span></span>
   * <span data-ttu-id="6883f-140">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="6883f-140">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="6883f-141">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="6883f-141">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="6883f-142">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6883f-142">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="6883f-143">在预览版上运行的依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="6883f-143">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="6883f-144">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="6883f-144">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="6883f-145">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="6883f-145">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="6883f-146">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="6883f-146">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="6883f-147">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6883f-147">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="6883f-148">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="6883f-148">Test a 64-bit (x64) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="6883f-149">当前版本</span><span class="sxs-lookup"><span data-stu-id="6883f-149">Current release</span></span>

* <span data-ttu-id="6883f-150">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="6883f-150">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="6883f-151">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="6883f-151">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="6883f-152">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="6883f-152">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="6883f-153">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="6883f-153">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="6883f-154">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6883f-154">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="6883f-155">在预览版上运行的依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="6883f-155">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="6883f-156">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="6883f-156">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="6883f-157">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="6883f-157">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="6883f-158">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="6883f-158">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="6883f-159">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6883f-159">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="6883f-160">ASP.NET Core 模块 stdout 日志</span><span class="sxs-lookup"><span data-stu-id="6883f-160">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="6883f-161">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="6883f-161">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="6883f-162">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6883f-162">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="6883f-163">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6883f-163">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="6883f-164">在“选择问题类别”  下，选择“Web 应用关闭”  按钮。</span><span class="sxs-lookup"><span data-stu-id="6883f-164">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="6883f-165">在“建议的解决方案”   >   “启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”  对应的按钮。</span><span class="sxs-lookup"><span data-stu-id="6883f-165">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="6883f-166">在 Kudu 诊断控制台  中，打开路径“站点   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6883f-166">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="6883f-167">向下滚动以在列表底部显示“web.config”  文件。</span><span class="sxs-lookup"><span data-stu-id="6883f-167">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="6883f-168">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="6883f-168">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="6883f-169">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="6883f-169">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="6883f-170">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="6883f-170">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="6883f-171">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="6883f-171">Make a request to the app.</span></span>
1. <span data-ttu-id="6883f-172">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="6883f-172">Return to the Azure portal.</span></span> <span data-ttu-id="6883f-173">选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6883f-173">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="6883f-174">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="6883f-174">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6883f-175">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6883f-175">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="6883f-176">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="6883f-176">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="6883f-177">选择“LogFiles”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="6883f-177">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="6883f-178">检查“已修改”  列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="6883f-178">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="6883f-179">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="6883f-179">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="6883f-180">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="6883f-180">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="6883f-181">在 Kudu 诊断控制台  中，返回到路径“site   > wwwroot  ”以显示 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="6883f-181">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="6883f-182">通过选择铅笔图标再次打开 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="6883f-182">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="6883f-183">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="6883f-183">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="6883f-184">选择“保存”  以保存文件。</span><span class="sxs-lookup"><span data-stu-id="6883f-184">Select **Save** to save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="6883f-185">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="6883f-185">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="6883f-186">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="6883f-186">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="6883f-187">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="6883f-187">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="6883f-188">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="6883f-188">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="6883f-189">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="6883f-189">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a><span data-ttu-id="6883f-190">ASP.NET Core 模块调试日志</span><span class="sxs-lookup"><span data-stu-id="6883f-190">ASP.NET Core Module debug log</span></span>

<span data-ttu-id="6883f-191">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="6883f-191">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="6883f-192">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6883f-192">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="6883f-193">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="6883f-193">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="6883f-194">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="6883f-194">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="6883f-195">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="6883f-195">Redeploy the app.</span></span>
   * <span data-ttu-id="6883f-196">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="6883f-196">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="6883f-197">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="6883f-197">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="6883f-198">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="6883f-198">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6883f-199">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6883f-199">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="6883f-200">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="6883f-200">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="6883f-201">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6883f-201">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="6883f-202">通过选择铅笔按钮编辑 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="6883f-202">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="6883f-203">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="6883f-203">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="6883f-204">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="6883f-204">Select the **Save** button.</span></span>
1. <span data-ttu-id="6883f-205">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="6883f-205">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="6883f-206">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="6883f-206">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6883f-207">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6883f-207">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="6883f-208">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="6883f-208">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="6883f-209">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6883f-209">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="6883f-210">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中  。</span><span class="sxs-lookup"><span data-stu-id="6883f-210">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="6883f-211">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="6883f-211">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="6883f-212">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="6883f-212">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="6883f-213">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="6883f-213">Disable debug logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="6883f-214">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="6883f-214">To disable the enhanced debug log, perform either of the following:</span></span>
   * <span data-ttu-id="6883f-215">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用  。</span><span class="sxs-lookup"><span data-stu-id="6883f-215">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
   * <span data-ttu-id="6883f-216">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分  。</span><span class="sxs-lookup"><span data-stu-id="6883f-216">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="6883f-217">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="6883f-217">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="6883f-218">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="6883f-218">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="6883f-219">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="6883f-219">There's no limit on log file size.</span></span> <span data-ttu-id="6883f-220">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="6883f-220">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="6883f-221">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="6883f-221">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="6883f-222">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="6883f-222">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

## <a name="common-startup-errors"></a><span data-ttu-id="6883f-223">常见启动错误</span><span class="sxs-lookup"><span data-stu-id="6883f-223">Common startup errors</span></span>

<span data-ttu-id="6883f-224">请参阅 <xref:host-and-deploy/azure-iis-errors-reference>。</span><span class="sxs-lookup"><span data-stu-id="6883f-224">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="6883f-225">参考主题介绍了阻止应用启动的大部分常见问题。</span><span class="sxs-lookup"><span data-stu-id="6883f-225">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="slow-or-hanging-app"></a><span data-ttu-id="6883f-226">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="6883f-226">Slow or hanging app</span></span>

<span data-ttu-id="6883f-227">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="6883f-227">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="6883f-228">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="6883f-228">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="6883f-229">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="6883f-229">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a><span data-ttu-id="6883f-230">远程调试</span><span class="sxs-lookup"><span data-stu-id="6883f-230">Remote debugging</span></span>

<span data-ttu-id="6883f-231">请参见下面的主题：</span><span class="sxs-lookup"><span data-stu-id="6883f-231">See the following topics:</span></span>

* <span data-ttu-id="6883f-232">[使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除的远程调试 Web 应用部分](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="6883f-232">[Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (Azure documentation)</span></span>
* <span data-ttu-id="6883f-233">[在 Visual Studio 2017 的 Azure 中的 IIS 上远程调试 ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)（Visual Studio 文档）</span><span class="sxs-lookup"><span data-stu-id="6883f-233">[Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure) (Visual Studio documentation)</span></span>

## <a name="application-insights"></a><span data-ttu-id="6883f-234">Application Insights</span><span class="sxs-lookup"><span data-stu-id="6883f-234">Application Insights</span></span>

<span data-ttu-id="6883f-235">[Application Insights](https://azure.microsoft.com/services/application-insights/) 提供来自 Azure 应用服务中托管的应用的遥测，包括错误日志记录和报告功能。</span><span class="sxs-lookup"><span data-stu-id="6883f-235">[Application Insights](https://azure.microsoft.com/services/application-insights/) provides telemetry from apps hosted in the Azure App Service, including error logging and reporting features.</span></span> <span data-ttu-id="6883f-236">当应用的日志记录功能变得可用时，Application Insights 只能报告应用启动后出现的错误。</span><span class="sxs-lookup"><span data-stu-id="6883f-236">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="6883f-237">有关详细信息，请参阅[用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="6883f-237">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="monitoring-blades"></a><span data-ttu-id="6883f-238">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="6883f-238">Monitoring blades</span></span>

<span data-ttu-id="6883f-239">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="6883f-239">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="6883f-240">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="6883f-240">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="6883f-241">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="6883f-241">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="6883f-242">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="6883f-242">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="6883f-243">在“开发工具”  边栏选项卡部分中，选择“扩展”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6883f-243">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="6883f-244">“ASP.NET Core 扩展”  应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="6883f-244">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="6883f-245">如果未安装扩展，请选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="6883f-245">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="6883f-246">从列表中选择“ASP.NET Core 扩展”  。</span><span class="sxs-lookup"><span data-stu-id="6883f-246">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="6883f-247">选择“确定”  以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="6883f-247">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="6883f-248">选择“添加扩展”  边栏选项卡上的“确定”  。</span><span class="sxs-lookup"><span data-stu-id="6883f-248">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="6883f-249">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="6883f-249">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="6883f-250">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="6883f-250">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="6883f-251">在 Azure 门户中，选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6883f-251">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="6883f-252">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="6883f-252">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6883f-253">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6883f-253">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="6883f-254">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="6883f-254">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="6883f-255">打开路径“site   > wwwroot  ”下的文件夹，然后向下滚动以显示列表底部的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="6883f-255">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="6883f-256">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="6883f-256">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="6883f-257">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="6883f-257">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="6883f-258">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="6883f-258">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="6883f-259">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="6883f-259">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="6883f-260">在 Azure 门户中，选择“诊断日志”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6883f-260">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="6883f-261">选择“应用程序日志记录(文件系统)”  和“详细错误消息”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="6883f-261">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="6883f-262">选择边栏选项卡顶部的“保存”  按钮。</span><span class="sxs-lookup"><span data-stu-id="6883f-262">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="6883f-263">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="6883f-263">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="6883f-264">选择“日志流”  边栏选项卡，将在门户中的“诊断日志”  边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="6883f-264">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="6883f-265">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="6883f-265">Make a request to the app.</span></span>
1. <span data-ttu-id="6883f-266">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="6883f-266">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="6883f-267">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="6883f-267">Be sure to disable stdout logging when troubleshooting is complete.</span></span> <span data-ttu-id="6883f-268">请参阅 [ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)部分中的说明。</span><span class="sxs-lookup"><span data-stu-id="6883f-268">See the instructions in the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) section.</span></span>

<span data-ttu-id="6883f-269">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6883f-269">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="6883f-270">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6883f-270">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="6883f-271">从侧栏的“支持工具”  区域中选择“失败请求跟踪日志”  。</span><span class="sxs-lookup"><span data-stu-id="6883f-271">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="6883f-272">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="6883f-272">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="6883f-273">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="6883f-273">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="6883f-274">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="6883f-274">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="6883f-275">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="6883f-275">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="6883f-276">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="6883f-276">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="6883f-277">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="6883f-277">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6883f-278">其他资源</span><span class="sxs-lookup"><span data-stu-id="6883f-278">Additional resources</span></span>

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="6883f-279">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="6883f-279">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="6883f-280">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="6883f-280">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="6883f-281">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="6883f-281">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="6883f-282">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="6883f-282">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="6883f-283">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="6883f-283">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="6883f-284">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="6883f-284">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
