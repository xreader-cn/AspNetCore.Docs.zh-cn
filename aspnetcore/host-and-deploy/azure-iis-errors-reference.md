---
<span data-ttu-id="0e412-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0e412-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e412-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e412-102">'Blazor'</span></span>
- <span data-ttu-id="0e412-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e412-103">'Identity'</span></span>
- <span data-ttu-id="0e412-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e412-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e412-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e412-105">'Razor'</span></span>
- <span data-ttu-id="0e412-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0e412-106">'SignalR' uid:</span></span> 

---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="0e412-107">Azure 应用服务和 IIS 上 ASP.NET Core 的常见错误参考</span><span class="sxs-lookup"><span data-stu-id="0e412-107">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0e412-108">本主题描述在 Azure 应用服务和 IIS 上托管 ASP.NET Core 应用时的常见错误，并提供特定错误的故障排除建议。</span><span class="sxs-lookup"><span data-stu-id="0e412-108">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="0e412-109">有关一般故障排除指南，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-109">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="0e412-110">收集以下信息：</span><span class="sxs-lookup"><span data-stu-id="0e412-110">Collect the following information:</span></span>

* <span data-ttu-id="0e412-111">浏览器行为（状态代码和错误消息）</span><span class="sxs-lookup"><span data-stu-id="0e412-111">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="0e412-112">应用程序事件日志条目</span><span class="sxs-lookup"><span data-stu-id="0e412-112">Application Event Log entries</span></span>
  * <span data-ttu-id="0e412-113">Azure 应用服务：请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-113">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="0e412-114">IIS</span><span class="sxs-lookup"><span data-stu-id="0e412-114">IIS</span></span>
    1. <span data-ttu-id="0e412-115">选择 Windows 菜单中的“开始”，键入“事件查看器”，然后按 Enter 。</span><span class="sxs-lookup"><span data-stu-id="0e412-115">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="0e412-116">打开事件查看器后，展开侧栏中的“Windows 日志”>“应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="0e412-116">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="0e412-117">ASP.NET Core 模块 stdout 和 debug日志条目</span><span class="sxs-lookup"><span data-stu-id="0e412-117">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="0e412-118">Azure 应用服务：请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-118">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="0e412-119">IIS：按照 ASP.NET Core 模块主题的[日志创建和重定向](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)和[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)部分中的说明进行操作。</span><span class="sxs-lookup"><span data-stu-id="0e412-119">IIS: Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="0e412-120">将错误信息与以下常见错误进行比较。</span><span class="sxs-lookup"><span data-stu-id="0e412-120">Compare error information to the following common errors.</span></span> <span data-ttu-id="0e412-121">如果找到匹配项，请按照故障排除建议进行操作。</span><span class="sxs-lookup"><span data-stu-id="0e412-121">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="0e412-122">本主题中的错误列表并未包含所有错误。</span><span class="sxs-lookup"><span data-stu-id="0e412-122">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="0e412-123">如果遇到此处未列出的错误，请使用本主题底部的“内容反馈”按钮打开一个新问题，并提供有关如何重现错误的详细说明。</span><span class="sxs-lookup"><span data-stu-id="0e412-123">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="0e412-124">OS 升级删除了 32 位 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="0e412-124">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="0e412-125">**应用程序日志：** 未能加载模块 DLL C:\WINDOWS\system32\inetsrv\aspnetcore.dll。</span><span class="sxs-lookup"><span data-stu-id="0e412-125">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="0e412-126">该数据是个错误。</span><span class="sxs-lookup"><span data-stu-id="0e412-126">The data is the error.</span></span>

<span data-ttu-id="0e412-127">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-127">Troubleshooting:</span></span>

<span data-ttu-id="0e412-128">OS 升级期间不会保留 C:\Windows\SysWOW64\inetsrv 目录中的非 OS 文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-128">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="0e412-129">如果在 OS 升级前已安装 ASP.NET Core 模块，且随后在 OS 升级后在 32 位模式下运行任何应用池，则会遇到此问题。</span><span class="sxs-lookup"><span data-stu-id="0e412-129">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="0e412-130">在 OS 升级后修复 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="0e412-130">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="0e412-131">请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="0e412-131">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="0e412-132">运行安装程序时，选择“修复”。</span><span class="sxs-lookup"><span data-stu-id="0e412-132">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="0e412-133">缺少站点扩展、安装了 32 位 (x86) 和 64 位 (x64) 站点扩展，或设置了错误的进程位数</span><span class="sxs-lookup"><span data-stu-id="0e412-133">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="0e412-134">适用于 Azure 应用服务托管的应用。</span><span class="sxs-lookup"><span data-stu-id="0e412-134">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="0e412-135">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="0e412-135">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="0e412-136">**应用程序日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="0e412-136">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="0e412-137">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="0e412-137">Could not find inprocess request handler.</span></span> <span data-ttu-id="0e412-138">调用 hostfxr 捕获的输出：无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-138">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="0e412-139">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="0e412-139">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="0e412-140">未能启动应用程序“/LM/W3SVC/1416782824/ROOT”，ErrorCode“0x8000ffff”。</span><span class="sxs-lookup"><span data-stu-id="0e412-140">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="0e412-141">**ASP.NET Core 模块 stdout 日志：** 无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-141">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="0e412-142">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="0e412-142">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

* <span data-ttu-id="0e412-143">**ASP.NET Core 模块调试日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="0e412-143">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="0e412-144">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="0e412-144">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="0e412-145">返回失败的 HRESULT：0x8000ffff。</span><span class="sxs-lookup"><span data-stu-id="0e412-145">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="0e412-146">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="0e412-146">Could not find inprocess request handler.</span></span> <span data-ttu-id="0e412-147">无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-147">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="0e412-148">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="0e412-148">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

<span data-ttu-id="0e412-149">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-149">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-150">如果在预览运行时运行该应用，请安装 32 位 (x86) 或  64位 (x64) 站点扩展，以匹配应用的位数和应用的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-150">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="0e412-151">请勿同时安装扩展或扩展的多个运行时版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-151">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="0e412-152">ASP.NET Core {RUNTIME VERSION} (x86) 运行时</span><span class="sxs-lookup"><span data-stu-id="0e412-152">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="0e412-153">ASP.NET Core {RUNTIME VERSION} (x64) 运行时</span><span class="sxs-lookup"><span data-stu-id="0e412-153">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="0e412-154">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="0e412-154">Restart the app.</span></span> <span data-ttu-id="0e412-155">等待几秒钟，以便应用重新启动。</span><span class="sxs-lookup"><span data-stu-id="0e412-155">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="0e412-156">如果在预览运行时运行应用，且同时安装了 32 位 (x86) 和 64 位 (x64) [站点扩展](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)，请卸载与应用的位数不匹配的站点扩展。</span><span class="sxs-lookup"><span data-stu-id="0e412-156">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="0e412-157">删除站点扩展之后，重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="0e412-157">After removing the site extension, restart the app.</span></span> <span data-ttu-id="0e412-158">等待几秒钟，以便应用重新启动。</span><span class="sxs-lookup"><span data-stu-id="0e412-158">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="0e412-159">如果在预览运行时运行该应用，并且站点扩展的位数匹配应用的位数，请确认预览站点扩展的运行时版本匹配应用的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-159">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="0e412-160">确认“应用程序设置”中的应用“平台”与应用的位数匹配。</span><span class="sxs-lookup"><span data-stu-id="0e412-160">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="0e412-161">有关详细信息，请参阅 <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>。</span><span class="sxs-lookup"><span data-stu-id="0e412-161">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="0e412-162">已部署 x86 应用，但 32 位应用未启用应用池</span><span class="sxs-lookup"><span data-stu-id="0e412-162">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="0e412-163">**浏览器：** HTTP 错误 500.30 - ANCM 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="0e412-163">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="0e412-164">**应用程序日志：** 物理根路径为 '{PATH}' 的应用程序 '/LM/W3SVC/5/ROOT' 遇到意外的托管异常，异常代码= '0xe0434352'。</span><span class="sxs-lookup"><span data-stu-id="0e412-164">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="0e412-165">请检查 stderr 日志以获取详细信息。</span><span class="sxs-lookup"><span data-stu-id="0e412-165">Please check the stderr logs for more information.</span></span> <span data-ttu-id="0e412-166">物理根路径为 '{PATH}' 的应用程序 '/LM/W3SVC/5/ROOT' 未能加载 clr 和托管应用程序。</span><span class="sxs-lookup"><span data-stu-id="0e412-166">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="0e412-167">CLR 工作线程提前退出</span><span class="sxs-lookup"><span data-stu-id="0e412-167">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="0e412-168">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空。</span><span class="sxs-lookup"><span data-stu-id="0e412-168">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

* <span data-ttu-id="0e412-169">**ASP.NET Core 模块调试日志：** 返回失败的 HRESULT：0x8007023e</span><span class="sxs-lookup"><span data-stu-id="0e412-169">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

<span data-ttu-id="0e412-170">发布自包含应用时，SDK 会捕获此方案。</span><span class="sxs-lookup"><span data-stu-id="0e412-170">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="0e412-171">如果 RID 与平台目标（例如，`win10-x64` RID 与项目文件中的 `<PlatformTarget>x86</PlatformTarget>`）不匹配，则 SDK 会产生错误。</span><span class="sxs-lookup"><span data-stu-id="0e412-171">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="0e412-172">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-172">Troubleshooting:</span></span>

<span data-ttu-id="0e412-173">对于依赖 x86 框架的部署 (`<PlatformTarget>x86</PlatformTarget>`)，请为 32 位应用启用 IIS 应用池。</span><span class="sxs-lookup"><span data-stu-id="0e412-173">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="0e412-174">在 IIS 管理器中，打开应用池的“高级设置”并将“启用 32 位应用程序”设置为 True  。</span><span class="sxs-lookup"><span data-stu-id="0e412-174">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="0e412-175">平台与 RID 冲突</span><span class="sxs-lookup"><span data-stu-id="0e412-175">Platform conflicts with RID</span></span>

* <span data-ttu-id="0e412-176">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="0e412-176">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="0e412-177">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' 命令行启动进程，ErrorCode = '0x80004005 : ff。</span><span class="sxs-lookup"><span data-stu-id="0e412-177">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="0e412-178">**ASP.NET Core 模块 stdout 日志：** 未经处理的异常：System.BadImageFormatException：无法加载文件或程序集 '{ASSEMBLY}.dll'。</span><span class="sxs-lookup"><span data-stu-id="0e412-178">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="0e412-179">试图加载的程序的格式不正确。</span><span class="sxs-lookup"><span data-stu-id="0e412-179">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="0e412-180">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-180">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-181">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="0e412-181">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="0e412-182">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="0e412-182">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="0e412-183">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-183">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="0e412-184">如果在升级应用和部署更新的程序集时，Azure 应用部署出现此异常，请从先前的部署中手动删除所有文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-184">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="0e412-185">部署升级的应用时，延迟的不兼容程序集可能造成 `System.BadImageFormatException` 异常。</span><span class="sxs-lookup"><span data-stu-id="0e412-185">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="0e412-186">URI 终结点错误或网站已停止</span><span class="sxs-lookup"><span data-stu-id="0e412-186">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="0e412-187">**浏览器：** ERR_CONNECTION_REFUSED --或者-- 无法连接</span><span class="sxs-lookup"><span data-stu-id="0e412-187">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="0e412-188">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="0e412-188">**Application Log:** No entry</span></span>

* <span data-ttu-id="0e412-189">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-189">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="0e412-190">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-190">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="0e412-191">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-191">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-192">确认正在使用的应用的 URI 端点是否正确。</span><span class="sxs-lookup"><span data-stu-id="0e412-192">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="0e412-193">检查绑定。</span><span class="sxs-lookup"><span data-stu-id="0e412-193">Check the bindings.</span></span>

* <span data-ttu-id="0e412-194">确认 IIS 网站未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="0e412-194">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="0e412-195">已禁用 CoreWebEngine 或 W3SVC 服务器功能</span><span class="sxs-lookup"><span data-stu-id="0e412-195">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="0e412-196">**OS 异常：** 必须安装 IIS 7.0 CoreWebEngine 和 W3SVC 功能才能使用 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="0e412-196">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="0e412-197">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-197">Troubleshooting:</span></span>

<span data-ttu-id="0e412-198">确认启用了适当的角色和功能。</span><span class="sxs-lookup"><span data-stu-id="0e412-198">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="0e412-199">请参阅 [IIS 配置](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="0e412-199">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="0e412-200">网站物理路径不正确或缺少应用</span><span class="sxs-lookup"><span data-stu-id="0e412-200">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="0e412-201">**浏览器：** 403 禁止访问 - 访问被拒绝 --或者-- 403.14 禁止访问 - Web 服务器被配置为不列出此目录的内容。</span><span class="sxs-lookup"><span data-stu-id="0e412-201">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="0e412-202">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="0e412-202">**Application Log:** No entry</span></span>

* <span data-ttu-id="0e412-203">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-203">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="0e412-204">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-204">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="0e412-205">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-205">Troubleshooting:</span></span>

<span data-ttu-id="0e412-206">检查 IIS 网站的“基本设置”和物理应用文件夹。</span><span class="sxs-lookup"><span data-stu-id="0e412-206">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="0e412-207">确认应用所在的文件夹位于 IIS 网站的物理路径中。</span><span class="sxs-lookup"><span data-stu-id="0e412-207">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="0e412-208">角色不正确、未安装 ASP.NET Core 模块或权限不正确</span><span class="sxs-lookup"><span data-stu-id="0e412-208">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="0e412-209">**浏览器：** 500.19 内部服务器错误 - 无法访问请求的页面，因为该页面的相关配置数据无效。</span><span class="sxs-lookup"><span data-stu-id="0e412-209">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="0e412-210">--或者-- 无法显示此页面</span><span class="sxs-lookup"><span data-stu-id="0e412-210">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="0e412-211">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="0e412-211">**Application Log:** No entry</span></span>

* <span data-ttu-id="0e412-212">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-212">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="0e412-213">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-213">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="0e412-214">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-214">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-215">确认启用了适当的角色。</span><span class="sxs-lookup"><span data-stu-id="0e412-215">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="0e412-216">请参阅 [IIS 配置](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="0e412-216">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="0e412-217">打开“程序和功能”或“应用和功能”，然后确认是否安装了 Windows Server Hosting  。</span><span class="sxs-lookup"><span data-stu-id="0e412-217">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="0e412-218">如果已安装的程序列表中没有 Windows Server Hosting，请下载并安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="0e412-218">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="0e412-219">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="0e412-219">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="0e412-220">有关详细信息，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="0e412-220">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="0e412-221">请确保将“应用程序池”>“进程模型”>“Identity”设置为“ApplicationPoolIdentity”，或确保自定义标识具有访问应用部署文件夹的相应权限   。</span><span class="sxs-lookup"><span data-stu-id="0e412-221">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="0e412-222">如果卸载了 ASP.NET Core 托管捆绑包并已安装了一个早期版本的托管捆绑包，则 applicationHost.config 文件将不包含 ASP.NET Core 模块分区。</span><span class="sxs-lookup"><span data-stu-id="0e412-222">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="0e412-223">可打开 %windir%/System32/inetsrv/config 处的 applicationHost.config 文件并找到 `<configuration><configSections><sectionGroup name="system.webServer">` 分区组 。</span><span class="sxs-lookup"><span data-stu-id="0e412-223">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="0e412-224">如果分区组中缺少 ASP.NET Core 模块分区，请添加以下分区元素：</span><span class="sxs-lookup"><span data-stu-id="0e412-224">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="0e412-225">或者，安装最新版本的 ASP.NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="0e412-225">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="0e412-226">最新版本与受支持的 ASP.NET Core 应用向后兼容。</span><span class="sxs-lookup"><span data-stu-id="0e412-226">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="0e412-227">processPath 不正确、缺少 PATH 变量、未安装托管捆绑包、未重启系统/IIS、未安装 VC++ Redistributable 或 dotnet.exe 访问冲突</span><span class="sxs-lookup"><span data-stu-id="0e412-227">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="0e412-228">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="0e412-228">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="0e412-229">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"{...}" ' 命令行启动进程</span><span class="sxs-lookup"><span data-stu-id="0e412-229">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="0e412-230">，ErrorCode = '0x80070002 :0。</span><span class="sxs-lookup"><span data-stu-id="0e412-230">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="0e412-231">应用程序 '{PATH}' 无法启动。</span><span class="sxs-lookup"><span data-stu-id="0e412-231">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="0e412-232">'{PATH}' 中未找到可执行文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-232">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="0e412-233">未能启动应用程序 '/LM/W3SVC/2/ROOT'，ErrorCode '0x8007023e'。</span><span class="sxs-lookup"><span data-stu-id="0e412-233">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="0e412-234">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-234">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="0e412-235">**ASP.NET Core 模块调试日志：** 事件日志：应用程序 '{PATH}' 无法启动。</span><span class="sxs-lookup"><span data-stu-id="0e412-235">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="0e412-236">'{PATH}' 中未找到可执行文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-236">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="0e412-237">返回失败的 HRESULT：0x8007023e</span><span class="sxs-lookup"><span data-stu-id="0e412-237">Failed HRESULT returned: 0x8007023e</span></span>

<span data-ttu-id="0e412-238">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-238">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-239">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="0e412-239">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="0e412-240">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="0e412-240">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="0e412-241">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-241">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="0e412-242">检查 web.config 中 `<aspNetCore>` 元素的 processPath 属性，对于依赖框架的部署 (FDD)，确保它为 `dotnet`，对于[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)，确保它为 `.\{ASSEMBLY}.exe` 。</span><span class="sxs-lookup"><span data-stu-id="0e412-242">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="0e412-243">对于 FDD，可能无法通过路径设置访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="0e412-243">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="0e412-244">确认“系统路径”设置中存在“C:\Program Files\dotnet”\\。</span><span class="sxs-lookup"><span data-stu-id="0e412-244">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="0e412-245">对于 FDD，应用池的用户标识可能无法访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="0e412-245">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="0e412-246">确认应用池用户标识具有访问 C:\Program Files\dotnet 目录的权限。</span><span class="sxs-lookup"><span data-stu-id="0e412-246">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="0e412-247">确认没有为应用池用户标识配置针对 C:\Program Files\dotnet 和应用目录的拒绝规则。</span><span class="sxs-lookup"><span data-stu-id="0e412-247">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="0e412-248">可能已部署 FDD 并安装了 .NET Core，但未重启 IIS。</span><span class="sxs-lookup"><span data-stu-id="0e412-248">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="0e412-249">重启服务器，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS 。</span><span class="sxs-lookup"><span data-stu-id="0e412-249">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="0e412-250">可能已部署 FDD，但未在托管系统上安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="0e412-250">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="0e412-251">如果尚未安装 .NET Core 运行时，则运行系统上的 **.NET Core 托管捆绑包安装程序**。</span><span class="sxs-lookup"><span data-stu-id="0e412-251">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="0e412-252">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="0e412-252">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="0e412-253">有关详细信息，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="0e412-253">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="0e412-254">如果需要特定的运行时，请从 [.NET 下载存档](https://dotnet.microsoft.com/download/archives)下载运行时并将其安装在系统上。</span><span class="sxs-lookup"><span data-stu-id="0e412-254">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="0e412-255">重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS，完成安装 。</span><span class="sxs-lookup"><span data-stu-id="0e412-255">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="0e412-256">\<aspNetCore> 元素的参数不正确</span><span class="sxs-lookup"><span data-stu-id="0e412-256">Incorrect arguments of \<aspNetCore> element</span></span>

* <span data-ttu-id="0e412-257">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="0e412-257">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="0e412-258">**应用程序日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="0e412-258">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="0e412-259">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="0e412-259">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="0e412-260">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="0e412-260">Could not find inprocess request handler.</span></span> <span data-ttu-id="0e412-261">调用 hostfxr 捕获的输出：是否希望运行 dotnet SDK 命令？</span><span class="sxs-lookup"><span data-stu-id="0e412-261">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="0e412-262">请从以下位置安装 dotnet SDK： https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 未能启动应用程序 '/LM/W3SVC/3/ROOT'，ErrorCode '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="0e412-262">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="0e412-263">**ASP.NET Core 模块 stdout 日志：** 是否希望运行 dotnet SDK 命令？</span><span class="sxs-lookup"><span data-stu-id="0e412-263">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="0e412-264">请从以下位置安装 dotnet SDK： https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span><span class="sxs-lookup"><span data-stu-id="0e412-264">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="0e412-265">**ASP.NET Core 模块调试日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="0e412-265">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="0e412-266">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="0e412-266">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="0e412-267">返回失败的 HRESULT：0x8000ffff 找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="0e412-267">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="0e412-268">调用 hostfxr 捕获的输出：是否希望运行 dotnet SDK 命令？</span><span class="sxs-lookup"><span data-stu-id="0e412-268">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="0e412-269">请从以下位置安装 dotnet SDK： https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 返回失败的 HRESULT：0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="0e412-269">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

<span data-ttu-id="0e412-270">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-270">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-271">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="0e412-271">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="0e412-272">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="0e412-272">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="0e412-273">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-273">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="0e412-274">检查 web.config 中 `<aspNetCore>` 元素的 arguments 属性，确认该属性：(a) 对于依赖框架的部署 (FDD) 为 `.\{ASSEMBLY}.dll`；或 (b) 对于独立部署 (SCD)，则为不存在、为空字符串 (`arguments=""`)，或为应用参数列表 (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) 。</span><span class="sxs-lookup"><span data-stu-id="0e412-274">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="0e412-275">缺少 .NET Core 共享框架</span><span class="sxs-lookup"><span data-stu-id="0e412-275">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="0e412-276">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="0e412-276">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="0e412-277">**应用程序日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="0e412-277">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="0e412-278">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="0e412-278">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="0e412-279">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="0e412-279">Could not find inprocess request handler.</span></span> <span data-ttu-id="0e412-280">调用 hostfxr 捕获的输出：无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-280">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="0e412-281">找不到指定的框架 'Microsoft.AspNetCore.App'、版本 '{VERSION}'。</span><span class="sxs-lookup"><span data-stu-id="0e412-281">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="0e412-282">未能启动应用程序 '/LM/W3SVC/5/ROOT'，ErrorCode '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="0e412-282">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="0e412-283">**ASP.NET Core 模块 stdout 日志：** 无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-283">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="0e412-284">找不到指定的框架 'Microsoft.AspNetCore.App'、版本 '{VERSION}'。</span><span class="sxs-lookup"><span data-stu-id="0e412-284">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="0e412-285">**ASP.NET Core 模块调试日志：** 返回失败的 HRESULT：0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="0e412-285">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

<span data-ttu-id="0e412-286">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-286">Troubleshooting:</span></span>

<span data-ttu-id="0e412-287">对于依赖框架的部署 (FDD)，确认已在系统上安装正确的运行时。</span><span class="sxs-lookup"><span data-stu-id="0e412-287">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="0e412-288">应用程序池已停止</span><span class="sxs-lookup"><span data-stu-id="0e412-288">Stopped Application Pool</span></span>

* <span data-ttu-id="0e412-289">**浏览器：** 503 服务不可用</span><span class="sxs-lookup"><span data-stu-id="0e412-289">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="0e412-290">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="0e412-290">**Application Log:** No entry</span></span>

* <span data-ttu-id="0e412-291">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-291">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="0e412-292">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-292">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="0e412-293">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-293">Troubleshooting:</span></span>

<span data-ttu-id="0e412-294">确认应用程序池未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="0e412-294">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="0e412-295">子应用程序包括 \<handlers> 部分</span><span class="sxs-lookup"><span data-stu-id="0e412-295">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="0e412-296">**浏览器：** HTTP 错误 500.19 - 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="0e412-296">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="0e412-297">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="0e412-297">**Application Log:** No entry</span></span>

* <span data-ttu-id="0e412-298">**ASP.NET Core 模块 stdout 日志：** 创建根应用的日志文件并显示正常操作。</span><span class="sxs-lookup"><span data-stu-id="0e412-298">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="0e412-299">未创建子应用的日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-299">The sub-app's log file isn't created.</span></span>

* <span data-ttu-id="0e412-300">**ASP.NET Core 模块调试日志：** 创建根应用的日志文件并显示正常操作。</span><span class="sxs-lookup"><span data-stu-id="0e412-300">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="0e412-301">未创建子应用的日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-301">The sub-app's log file isn't created.</span></span>

<span data-ttu-id="0e412-302">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-302">Troubleshooting:</span></span>

<span data-ttu-id="0e412-303">确认子应用的 web.config 文件不包含 `<handlers>` 部分，或者子应用未继承父级应用的处理程序。</span><span class="sxs-lookup"><span data-stu-id="0e412-303">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="0e412-304">父级应用的 web.config 的`<system.webServer>` 放在 `<location>` 元素内。</span><span class="sxs-lookup"><span data-stu-id="0e412-304">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="0e412-305">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在父级应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="0e412-305">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="0e412-306">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="0e412-306">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="0e412-307">stdout 日志路径不正确</span><span class="sxs-lookup"><span data-stu-id="0e412-307">stdout log path incorrect</span></span>

* <span data-ttu-id="0e412-308">**浏览器：** 应用正常响应。</span><span class="sxs-lookup"><span data-stu-id="0e412-308">**Browser:** The app responds normally.</span></span>

* <span data-ttu-id="0e412-309">**应用程序日志：** 无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="0e412-309">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="0e412-310">异常消息：在 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 返回了 HRESULT 0x80070005。</span><span class="sxs-lookup"><span data-stu-id="0e412-310">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="0e412-311">无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中停止 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="0e412-311">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="0e412-312">异常消息：在 {PATH} 返回了 HRESULT 0x80070002。</span><span class="sxs-lookup"><span data-stu-id="0e412-312">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="0e412-313">无法在 {PATH}\aspnetcorev2_inprocess.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="0e412-313">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="0e412-314">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-314">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="0e412-315">**ASP.NET Core 模块调试日志：** 无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="0e412-315">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="0e412-316">异常消息：在 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 返回了 HRESULT 0x80070005。</span><span class="sxs-lookup"><span data-stu-id="0e412-316">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="0e412-317">无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中停止 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="0e412-317">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="0e412-318">异常消息：在 {PATH} 返回了 HRESULT 0x80070002。</span><span class="sxs-lookup"><span data-stu-id="0e412-318">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="0e412-319">无法在 {PATH}\aspnetcorev2_inprocess.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="0e412-319">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

<span data-ttu-id="0e412-320">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-320">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-321">web.config 的 `<aspNetCore>` 元素中指定的 `stdoutLogFile` 路径不存在。</span><span class="sxs-lookup"><span data-stu-id="0e412-321">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="0e412-322">有关详细信息，请参阅 [ASP.NET Core 模块：日志创建和重定向](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)。</span><span class="sxs-lookup"><span data-stu-id="0e412-322">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="0e412-323">应用池用户没有 stdout 日志路径的写入权限。</span><span class="sxs-lookup"><span data-stu-id="0e412-323">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="0e412-324">应用程序配置常见问题</span><span class="sxs-lookup"><span data-stu-id="0e412-324">Application configuration general issue</span></span>

* <span data-ttu-id="0e412-325">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败 --或者-- HTTP 错误 500.30 - ANCM 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="0e412-325">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="0e412-326">**应用程序日志：** 变量</span><span class="sxs-lookup"><span data-stu-id="0e412-326">**Application Log:** Variable</span></span>

* <span data-ttu-id="0e412-327">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空或使用常规条目创建，直到应用失败。</span><span class="sxs-lookup"><span data-stu-id="0e412-327">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="0e412-328">**ASP.NET Core 模块调试日志：** 变量</span><span class="sxs-lookup"><span data-stu-id="0e412-328">**ASP.NET Core Module Debug Log:** Variable</span></span>

<span data-ttu-id="0e412-329">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-329">Troubleshooting:</span></span>

<span data-ttu-id="0e412-330">此进程无法启动，这很可能是由应用配置或编程问题引起的。</span><span class="sxs-lookup"><span data-stu-id="0e412-330">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="0e412-331">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="0e412-331">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0e412-332">本主题描述在 Azure 应用服务和 IIS 上托管 ASP.NET Core 应用时的常见错误，并提供特定错误的故障排除建议。</span><span class="sxs-lookup"><span data-stu-id="0e412-332">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="0e412-333">有关一般故障排除指南，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-333">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="0e412-334">收集以下信息：</span><span class="sxs-lookup"><span data-stu-id="0e412-334">Collect the following information:</span></span>

* <span data-ttu-id="0e412-335">浏览器行为（状态代码和错误消息）</span><span class="sxs-lookup"><span data-stu-id="0e412-335">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="0e412-336">应用程序事件日志条目</span><span class="sxs-lookup"><span data-stu-id="0e412-336">Application Event Log entries</span></span>
  * <span data-ttu-id="0e412-337">Azure 应用服务：请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-337">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="0e412-338">IIS</span><span class="sxs-lookup"><span data-stu-id="0e412-338">IIS</span></span>
    1. <span data-ttu-id="0e412-339">选择 Windows 菜单中的“开始”，键入“事件查看器”，然后按 Enter 。</span><span class="sxs-lookup"><span data-stu-id="0e412-339">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="0e412-340">打开事件查看器后，展开侧栏中的“Windows 日志”>“应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="0e412-340">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="0e412-341">ASP.NET Core 模块 stdout 和 debug日志条目</span><span class="sxs-lookup"><span data-stu-id="0e412-341">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="0e412-342">Azure 应用服务：请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-342">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="0e412-343">IIS：按照 ASP.NET Core 模块主题的[日志创建和重定向](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)和[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)部分中的说明进行操作。</span><span class="sxs-lookup"><span data-stu-id="0e412-343">IIS: Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="0e412-344">将错误信息与以下常见错误进行比较。</span><span class="sxs-lookup"><span data-stu-id="0e412-344">Compare error information to the following common errors.</span></span> <span data-ttu-id="0e412-345">如果找到匹配项，请按照故障排除建议进行操作。</span><span class="sxs-lookup"><span data-stu-id="0e412-345">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="0e412-346">本主题中的错误列表并未包含所有错误。</span><span class="sxs-lookup"><span data-stu-id="0e412-346">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="0e412-347">如果遇到此处未列出的错误，请使用本主题底部的“内容反馈”按钮打开一个新问题，并提供有关如何重现错误的详细说明。</span><span class="sxs-lookup"><span data-stu-id="0e412-347">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="0e412-348">OS 升级删除了 32 位 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="0e412-348">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="0e412-349">**应用程序日志：** 未能加载模块 DLL C:\WINDOWS\system32\inetsrv\aspnetcore.dll。</span><span class="sxs-lookup"><span data-stu-id="0e412-349">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="0e412-350">该数据是个错误。</span><span class="sxs-lookup"><span data-stu-id="0e412-350">The data is the error.</span></span>

<span data-ttu-id="0e412-351">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-351">Troubleshooting:</span></span>

<span data-ttu-id="0e412-352">OS 升级期间不会保留 C:\Windows\SysWOW64\inetsrv 目录中的非 OS 文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-352">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="0e412-353">如果在 OS 升级前已安装 ASP.NET Core 模块，且随后在 OS 升级后在 32 位模式下运行任何应用池，则会遇到此问题。</span><span class="sxs-lookup"><span data-stu-id="0e412-353">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="0e412-354">在 OS 升级后修复 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="0e412-354">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="0e412-355">请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="0e412-355">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="0e412-356">运行安装程序时，选择“修复”。</span><span class="sxs-lookup"><span data-stu-id="0e412-356">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="0e412-357">缺少站点扩展、安装了 32 位 (x86) 和 64 位 (x64) 站点扩展，或设置了错误的进程位数</span><span class="sxs-lookup"><span data-stu-id="0e412-357">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="0e412-358">适用于 Azure 应用服务托管的应用。</span><span class="sxs-lookup"><span data-stu-id="0e412-358">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="0e412-359">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="0e412-359">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="0e412-360">**应用程序日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="0e412-360">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="0e412-361">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="0e412-361">Could not find inprocess request handler.</span></span> <span data-ttu-id="0e412-362">调用 hostfxr 捕获的输出：无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-362">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="0e412-363">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="0e412-363">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="0e412-364">未能启动应用程序“/LM/W3SVC/1416782824/ROOT”，ErrorCode“0x8000ffff”。</span><span class="sxs-lookup"><span data-stu-id="0e412-364">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="0e412-365">**ASP.NET Core 模块 stdout 日志：** 无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-365">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="0e412-366">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="0e412-366">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

<span data-ttu-id="0e412-367">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-367">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-368">如果在预览运行时运行该应用，请安装 32 位 (x86) 或  64位 (x64) 站点扩展，以匹配应用的位数和应用的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-368">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="0e412-369">请勿同时安装扩展或扩展的多个运行时版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-369">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="0e412-370">ASP.NET Core {RUNTIME VERSION} (x86) 运行时</span><span class="sxs-lookup"><span data-stu-id="0e412-370">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="0e412-371">ASP.NET Core {RUNTIME VERSION} (x64) 运行时</span><span class="sxs-lookup"><span data-stu-id="0e412-371">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="0e412-372">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="0e412-372">Restart the app.</span></span> <span data-ttu-id="0e412-373">等待几秒钟，以便应用重新启动。</span><span class="sxs-lookup"><span data-stu-id="0e412-373">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="0e412-374">如果在预览运行时运行应用，且同时安装了 32 位 (x86) 和 64 位 (x64) [站点扩展](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)，请卸载与应用的位数不匹配的站点扩展。</span><span class="sxs-lookup"><span data-stu-id="0e412-374">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="0e412-375">删除站点扩展之后，重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="0e412-375">After removing the site extension, restart the app.</span></span> <span data-ttu-id="0e412-376">等待几秒钟，以便应用重新启动。</span><span class="sxs-lookup"><span data-stu-id="0e412-376">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="0e412-377">如果在预览运行时运行该应用，并且站点扩展的位数匹配应用的位数，请确认预览站点扩展的运行时版本匹配应用的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="0e412-377">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="0e412-378">确认“应用程序设置”中的应用“平台”与应用的位数匹配。</span><span class="sxs-lookup"><span data-stu-id="0e412-378">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="0e412-379">有关详细信息，请参阅 <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>。</span><span class="sxs-lookup"><span data-stu-id="0e412-379">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="0e412-380">已部署 x86 应用，但 32 位应用未启用应用池</span><span class="sxs-lookup"><span data-stu-id="0e412-380">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="0e412-381">**浏览器：** HTTP 错误 500.30 - ANCM 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="0e412-381">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="0e412-382">**应用程序日志：** 物理根路径为 '{PATH}' 的应用程序 '/LM/W3SVC/5/ROOT' 遇到意外的托管异常，异常代码= '0xe0434352'。</span><span class="sxs-lookup"><span data-stu-id="0e412-382">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="0e412-383">请检查 stderr 日志以获取详细信息。</span><span class="sxs-lookup"><span data-stu-id="0e412-383">Please check the stderr logs for more information.</span></span> <span data-ttu-id="0e412-384">物理根路径为 '{PATH}' 的应用程序 '/LM/W3SVC/5/ROOT' 未能加载 clr 和托管应用程序。</span><span class="sxs-lookup"><span data-stu-id="0e412-384">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="0e412-385">CLR 工作线程提前退出</span><span class="sxs-lookup"><span data-stu-id="0e412-385">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="0e412-386">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空。</span><span class="sxs-lookup"><span data-stu-id="0e412-386">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="0e412-387">发布自包含应用时，SDK 会捕获此方案。</span><span class="sxs-lookup"><span data-stu-id="0e412-387">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="0e412-388">如果 RID 与平台目标（例如，`win10-x64` RID 与项目文件中的 `<PlatformTarget>x86</PlatformTarget>`）不匹配，则 SDK 会产生错误。</span><span class="sxs-lookup"><span data-stu-id="0e412-388">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="0e412-389">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-389">Troubleshooting:</span></span>

<span data-ttu-id="0e412-390">对于依赖 x86 框架的部署 (`<PlatformTarget>x86</PlatformTarget>`)，请为 32 位应用启用 IIS 应用池。</span><span class="sxs-lookup"><span data-stu-id="0e412-390">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="0e412-391">在 IIS 管理器中，打开应用池的“高级设置”并将“启用 32 位应用程序”设置为 True  。</span><span class="sxs-lookup"><span data-stu-id="0e412-391">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="0e412-392">平台与 RID 冲突</span><span class="sxs-lookup"><span data-stu-id="0e412-392">Platform conflicts with RID</span></span>

* <span data-ttu-id="0e412-393">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="0e412-393">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="0e412-394">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' 命令行启动进程，ErrorCode = '0x80004005 : ff。</span><span class="sxs-lookup"><span data-stu-id="0e412-394">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="0e412-395">**ASP.NET Core 模块 stdout 日志：** 未经处理的异常：System.BadImageFormatException：无法加载文件或程序集 '{ASSEMBLY}.dll'。</span><span class="sxs-lookup"><span data-stu-id="0e412-395">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="0e412-396">试图加载的程序的格式不正确。</span><span class="sxs-lookup"><span data-stu-id="0e412-396">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="0e412-397">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-397">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-398">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="0e412-398">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="0e412-399">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="0e412-399">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="0e412-400">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-400">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="0e412-401">如果在升级应用和部署更新的程序集时，Azure 应用部署出现此异常，请从先前的部署中手动删除所有文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-401">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="0e412-402">部署升级的应用时，延迟的不兼容程序集可能造成 `System.BadImageFormatException` 异常。</span><span class="sxs-lookup"><span data-stu-id="0e412-402">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="0e412-403">URI 终结点错误或网站已停止</span><span class="sxs-lookup"><span data-stu-id="0e412-403">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="0e412-404">**浏览器：** ERR_CONNECTION_REFUSED --或者-- 无法连接</span><span class="sxs-lookup"><span data-stu-id="0e412-404">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="0e412-405">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="0e412-405">**Application Log:** No entry</span></span>

* <span data-ttu-id="0e412-406">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-406">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="0e412-407">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-407">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-408">确认正在使用的应用的 URI 端点是否正确。</span><span class="sxs-lookup"><span data-stu-id="0e412-408">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="0e412-409">检查绑定。</span><span class="sxs-lookup"><span data-stu-id="0e412-409">Check the bindings.</span></span>

* <span data-ttu-id="0e412-410">确认 IIS 网站未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="0e412-410">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="0e412-411">已禁用 CoreWebEngine 或 W3SVC 服务器功能</span><span class="sxs-lookup"><span data-stu-id="0e412-411">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="0e412-412">**OS 异常：** 必须安装 IIS 7.0 CoreWebEngine 和 W3SVC 功能才能使用 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="0e412-412">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="0e412-413">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-413">Troubleshooting:</span></span>

<span data-ttu-id="0e412-414">确认启用了适当的角色和功能。</span><span class="sxs-lookup"><span data-stu-id="0e412-414">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="0e412-415">请参阅 [IIS 配置](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="0e412-415">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="0e412-416">网站物理路径不正确或缺少应用</span><span class="sxs-lookup"><span data-stu-id="0e412-416">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="0e412-417">**浏览器：** 403 禁止访问 - 访问被拒绝 --或者-- 403.14 禁止访问 - Web 服务器被配置为不列出此目录的内容。</span><span class="sxs-lookup"><span data-stu-id="0e412-417">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="0e412-418">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="0e412-418">**Application Log:** No entry</span></span>

* <span data-ttu-id="0e412-419">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-419">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="0e412-420">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-420">Troubleshooting:</span></span>

<span data-ttu-id="0e412-421">检查 IIS 网站的“基本设置”和物理应用文件夹。</span><span class="sxs-lookup"><span data-stu-id="0e412-421">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="0e412-422">确认应用所在的文件夹位于 IIS 网站的物理路径中。</span><span class="sxs-lookup"><span data-stu-id="0e412-422">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="0e412-423">角色不正确、未安装 ASP.NET Core 模块或权限不正确</span><span class="sxs-lookup"><span data-stu-id="0e412-423">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="0e412-424">**浏览器：** 500.19 内部服务器错误 - 无法访问请求的页面，因为该页面的相关配置数据无效。</span><span class="sxs-lookup"><span data-stu-id="0e412-424">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="0e412-425">--或者-- 无法显示此页面</span><span class="sxs-lookup"><span data-stu-id="0e412-425">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="0e412-426">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="0e412-426">**Application Log:** No entry</span></span>

* <span data-ttu-id="0e412-427">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-427">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="0e412-428">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-428">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-429">确认启用了适当的角色。</span><span class="sxs-lookup"><span data-stu-id="0e412-429">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="0e412-430">请参阅 [IIS 配置](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="0e412-430">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="0e412-431">打开“程序和功能”或“应用和功能”，然后确认是否安装了 Windows Server Hosting  。</span><span class="sxs-lookup"><span data-stu-id="0e412-431">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="0e412-432">如果已安装的程序列表中没有 Windows Server Hosting，请下载并安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="0e412-432">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="0e412-433">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="0e412-433">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="0e412-434">有关详细信息，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="0e412-434">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="0e412-435">请确保将“应用程序池”>“进程模型”>“Identity”设置为“ApplicationPoolIdentity”，或确保自定义标识具有访问应用部署文件夹的相应权限   。</span><span class="sxs-lookup"><span data-stu-id="0e412-435">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="0e412-436">如果卸载了 ASP.NET Core 托管捆绑包并已安装了一个早期版本的托管捆绑包，则 applicationHost.config 文件将不包含 ASP.NET Core 模块分区。</span><span class="sxs-lookup"><span data-stu-id="0e412-436">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="0e412-437">可打开 %windir%/System32/inetsrv/config 处的 applicationHost.config 文件并找到 `<configuration><configSections><sectionGroup name="system.webServer">` 分区组 。</span><span class="sxs-lookup"><span data-stu-id="0e412-437">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="0e412-438">如果分区组中缺少 ASP.NET Core 模块分区，请添加以下分区元素：</span><span class="sxs-lookup"><span data-stu-id="0e412-438">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="0e412-439">或者，安装最新版本的 ASP.NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="0e412-439">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="0e412-440">最新版本与受支持的 ASP.NET Core 应用向后兼容。</span><span class="sxs-lookup"><span data-stu-id="0e412-440">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="0e412-441">processPath 不正确、缺少 PATH 变量、未安装托管捆绑包、未重启系统/IIS、未安装 VC++ Redistributable 或 dotnet.exe 访问冲突</span><span class="sxs-lookup"><span data-stu-id="0e412-441">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="0e412-442">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="0e412-442">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="0e412-443">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"{...}" ' 命令行启动进程</span><span class="sxs-lookup"><span data-stu-id="0e412-443">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="0e412-444">，ErrorCode = '0x80070002 :0.</span><span class="sxs-lookup"><span data-stu-id="0e412-444">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="0e412-445">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空。</span><span class="sxs-lookup"><span data-stu-id="0e412-445">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="0e412-446">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-446">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-447">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="0e412-447">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="0e412-448">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="0e412-448">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="0e412-449">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-449">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="0e412-450">检查 web.config 中 `<aspNetCore>` 元素的 processPath 属性，对于依赖框架的部署 (FDD)，确保它为 `dotnet`，对于[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)，确保它为 `.\{ASSEMBLY}.exe` 。</span><span class="sxs-lookup"><span data-stu-id="0e412-450">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="0e412-451">对于 FDD，可能无法通过路径设置访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="0e412-451">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="0e412-452">确认“系统路径”设置中存在“C:\Program Files\dotnet”\\。</span><span class="sxs-lookup"><span data-stu-id="0e412-452">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="0e412-453">对于 FDD，应用池的用户标识可能无法访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="0e412-453">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="0e412-454">确认应用池用户标识具有访问 C:\Program Files\dotnet 目录的权限。</span><span class="sxs-lookup"><span data-stu-id="0e412-454">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="0e412-455">确认没有为应用池用户标识配置针对 C:\Program Files\dotnet 和应用目录的拒绝规则。</span><span class="sxs-lookup"><span data-stu-id="0e412-455">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="0e412-456">可能已部署 FDD 并安装了 .NET Core，但未重启 IIS。</span><span class="sxs-lookup"><span data-stu-id="0e412-456">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="0e412-457">重启服务器，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS 。</span><span class="sxs-lookup"><span data-stu-id="0e412-457">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="0e412-458">可能已部署 FDD，但未在托管系统上安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="0e412-458">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="0e412-459">如果尚未安装 .NET Core 运行时，则运行系统上的 **.NET Core 托管捆绑包安装程序**。</span><span class="sxs-lookup"><span data-stu-id="0e412-459">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="0e412-460">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="0e412-460">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="0e412-461">有关详细信息，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="0e412-461">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="0e412-462">如果需要特定的运行时，请从 [.NET 下载存档](https://dotnet.microsoft.com/download/archives)下载运行时并将其安装在系统上。</span><span class="sxs-lookup"><span data-stu-id="0e412-462">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="0e412-463">重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS，完成安装 。</span><span class="sxs-lookup"><span data-stu-id="0e412-463">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="0e412-464">\<aspNetCore> 元素的参数不正确</span><span class="sxs-lookup"><span data-stu-id="0e412-464">Incorrect arguments of \<aspNetCore> element</span></span>

* <span data-ttu-id="0e412-465">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="0e412-465">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="0e412-466">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"dotnet" .\{ASSEMBLY}.dll' 命令行启动进程，ErrorCode = '0x80004005 :80008081。</span><span class="sxs-lookup"><span data-stu-id="0e412-466">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="0e412-467">**ASP.NET Core 模块 stdout 日志：** 要执行的应用程序不存在：'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="0e412-467">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

<span data-ttu-id="0e412-468">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-468">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-469">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="0e412-469">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="0e412-470">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="0e412-470">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="0e412-471">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="0e412-471">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="0e412-472">检查 web.config 中 `<aspNetCore>` 元素的 arguments 属性，确认该属性：(a) 对于依赖框架的部署 (FDD) 为 `.\{ASSEMBLY}.dll`；或 (b) 对于独立部署 (SCD)，则为不存在、为空字符串 (`arguments=""`)，或为应用参数列表 (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) 。</span><span class="sxs-lookup"><span data-stu-id="0e412-472">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

<span data-ttu-id="0e412-473">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-473">Troubleshooting:</span></span>

<span data-ttu-id="0e412-474">对于依赖框架的部署 (FDD)，确认已在系统上安装正确的运行时。</span><span class="sxs-lookup"><span data-stu-id="0e412-474">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="0e412-475">应用程序池已停止</span><span class="sxs-lookup"><span data-stu-id="0e412-475">Stopped Application Pool</span></span>

* <span data-ttu-id="0e412-476">**浏览器：** 503 服务不可用</span><span class="sxs-lookup"><span data-stu-id="0e412-476">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="0e412-477">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="0e412-477">**Application Log:** No entry</span></span>

* <span data-ttu-id="0e412-478">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-478">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="0e412-479">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-479">Troubleshooting:</span></span>

<span data-ttu-id="0e412-480">确认应用程序池未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="0e412-480">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="0e412-481">子应用程序包括 \<handlers> 部分</span><span class="sxs-lookup"><span data-stu-id="0e412-481">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="0e412-482">**浏览器：** HTTP 错误 500.19 - 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="0e412-482">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="0e412-483">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="0e412-483">**Application Log:** No entry</span></span>

* <span data-ttu-id="0e412-484">**ASP.NET Core 模块 stdout 日志：** 创建根应用的日志文件并显示正常操作。</span><span class="sxs-lookup"><span data-stu-id="0e412-484">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="0e412-485">未创建子应用的日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-485">The sub-app's log file isn't created.</span></span>

<span data-ttu-id="0e412-486">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-486">Troubleshooting:</span></span>

<span data-ttu-id="0e412-487">确认子应用的 web.config 文件不包括 `<handlers>` 部分。</span><span class="sxs-lookup"><span data-stu-id="0e412-487">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="0e412-488">stdout 日志路径不正确</span><span class="sxs-lookup"><span data-stu-id="0e412-488">stdout log path incorrect</span></span>

* <span data-ttu-id="0e412-489">**浏览器：** 应用正常响应。</span><span class="sxs-lookup"><span data-stu-id="0e412-489">**Browser:** The app responds normally.</span></span>

* <span data-ttu-id="0e412-490">**应用程序日志：** 警告：无法创建 stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log，ErrorCode = -2147024893。</span><span class="sxs-lookup"><span data-stu-id="0e412-490">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="0e412-491">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="0e412-491">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="0e412-492">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-492">Troubleshooting:</span></span>

* <span data-ttu-id="0e412-493">web.config 的 `<aspNetCore>` 元素中指定的 `stdoutLogFile` 路径不存在。</span><span class="sxs-lookup"><span data-stu-id="0e412-493">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="0e412-494">有关详细信息，请参阅 [ASP.NET Core 模块：日志创建和重定向](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)。</span><span class="sxs-lookup"><span data-stu-id="0e412-494">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="0e412-495">应用池用户没有 stdout 日志路径的写入权限。</span><span class="sxs-lookup"><span data-stu-id="0e412-495">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="0e412-496">应用程序配置常见问题</span><span class="sxs-lookup"><span data-stu-id="0e412-496">Application configuration general issue</span></span>

* <span data-ttu-id="0e412-497">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="0e412-497">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="0e412-498">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 通过 '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' 命令行创建了进程，但该进程出现故障、未响应或未在给定的端口 '{PORT}' 上侦听，ErrorCode = '{ERROR CODE}'</span><span class="sxs-lookup"><span data-stu-id="0e412-498">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="0e412-499">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空。</span><span class="sxs-lookup"><span data-stu-id="0e412-499">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="0e412-500">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="0e412-500">Troubleshooting:</span></span>

<span data-ttu-id="0e412-501">此进程无法启动，这很可能是由应用配置或编程问题引起的。</span><span class="sxs-lookup"><span data-stu-id="0e412-501">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="0e412-502">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="0e412-502">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
