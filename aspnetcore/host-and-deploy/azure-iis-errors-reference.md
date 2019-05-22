---
title: Azure 应用服务和 IIS 上 ASP.NET Core 的常见错误参考
author: guardrex
description: 获取在 Azure 应用服务和 IIS 上托管 ASP.NET Core 应用的常见错误的故障排除建议。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 0191460f8c3dab98e6f977a29eacf0396b6789d8
ms.sourcegitcommit: b4ef2b00f3e1eb287138f8b43c811cb35a100d3e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970076"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="5764c-103">Azure 应用服务和 IIS 上 ASP.NET Core 的常见错误参考</span><span class="sxs-lookup"><span data-stu-id="5764c-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

<span data-ttu-id="5764c-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5764c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5764c-105">本主题提供在 Azure 应用服务和 IIS 上托管 ASP.NET Core 应用的常见错误的故障排除。</span><span class="sxs-lookup"><span data-stu-id="5764c-105">This topic offers troubleshooting advice for common errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="5764c-106">收集以下信息：</span><span class="sxs-lookup"><span data-stu-id="5764c-106">Collect the following information:</span></span>

* <span data-ttu-id="5764c-107">浏览器行为（状态代码和错误消息）</span><span class="sxs-lookup"><span data-stu-id="5764c-107">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="5764c-108">应用程序事件日志条目</span><span class="sxs-lookup"><span data-stu-id="5764c-108">Application Event Log entries</span></span>
  * <span data-ttu-id="5764c-109">Azure 应用服务 &ndash; 请参阅 <xref:host-and-deploy/azure-apps/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="5764c-109">Azure App Service &ndash; See <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
  * <span data-ttu-id="5764c-110">IIS</span><span class="sxs-lookup"><span data-stu-id="5764c-110">IIS</span></span>
    1. <span data-ttu-id="5764c-111">选择 Windows 菜单中的“开始”，键入“事件查看器”，然后按 Enter。</span><span class="sxs-lookup"><span data-stu-id="5764c-111">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="5764c-112">打开“事件查看器”后，展开侧栏中的“Windows 日志” > “应用程序”。</span><span class="sxs-lookup"><span data-stu-id="5764c-112">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="5764c-113">ASP.NET Core 模块 stdout 和 debug日志条目</span><span class="sxs-lookup"><span data-stu-id="5764c-113">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="5764c-114">Azure 应用服务 &ndash; 请参阅 <xref:host-and-deploy/azure-apps/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="5764c-114">Azure App Service &ndash; See <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
  * <span data-ttu-id="5764c-115">IIS &ndash; 按照 ASP.NET Core 模块主题的[日志创建和重定向](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)和[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)部分中的说明进行操作。</span><span class="sxs-lookup"><span data-stu-id="5764c-115">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="5764c-116">将错误信息与以下常见错误进行比较。</span><span class="sxs-lookup"><span data-stu-id="5764c-116">Compare error information to the following common errors.</span></span> <span data-ttu-id="5764c-117">如果找到匹配项，请按照故障排除建议进行操作。</span><span class="sxs-lookup"><span data-stu-id="5764c-117">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="5764c-118">本主题中的错误列表并未包含所有错误。</span><span class="sxs-lookup"><span data-stu-id="5764c-118">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="5764c-119">如果遇到此处未列出的错误，请使用本主题底部的“内容反馈”按钮打开一个新问题，并提供有关如何重现错误的详细说明。</span><span class="sxs-lookup"><span data-stu-id="5764c-119">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a><span data-ttu-id="5764c-120">安装程序无法获取 VC++ Redistributable</span><span class="sxs-lookup"><span data-stu-id="5764c-120">Installer unable to obtain VC++ Redistributable</span></span>

* <span data-ttu-id="5764c-121">**安装程序异常：** 0x80072efd --或者-- 0x80072f76 - 未指定的错误</span><span class="sxs-lookup"><span data-stu-id="5764c-121">**Installer Exception:** 0x80072efd **--OR--** 0x80072f76 - Unspecified error</span></span>

* <span data-ttu-id="5764c-122">**安装程序日志异常&#8224;：** 0x80072efd --或者-- 0x80072f76 错误：无法执行 EXE 包</span><span class="sxs-lookup"><span data-stu-id="5764c-122">**Installer Log Exception&#8224;:** Error 0x80072efd **--OR--** 0x80072f76: Failed to execute EXE package</span></span>

  <span data-ttu-id="5764c-123">&#8224;此日志位于 C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log。</span><span class="sxs-lookup"><span data-stu-id="5764c-123">&#8224;The log is located at *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span></span>

<span data-ttu-id="5764c-124">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-124">Troubleshooting:</span></span>

<span data-ttu-id="5764c-125">如果在[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)时系统无法访问 Internet，则在系统阻止安装程序获取 Microsoft Visual C++ 2015 Redistributable 时会出现此异常。</span><span class="sxs-lookup"><span data-stu-id="5764c-125">If the system doesn't have Internet access while [installing the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), this exception occurs when the installer is prevented from obtaining the *Microsoft Visual C++ 2015 Redistributable*.</span></span> <span data-ttu-id="5764c-126">可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53840)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="5764c-126">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span> <span data-ttu-id="5764c-127">如果安装程序失败，服务器可能不会收到托管[依赖框架的部署 (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd) 所需的 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="5764c-127">If the installer fails, the server may not receive the .NET Core runtime required to host a [framework-dependent deployment (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span> <span data-ttu-id="5764c-128">如果托管 FDD，请在“程序和功能”或“应用和功能”中确认安装了运行时。</span><span class="sxs-lookup"><span data-stu-id="5764c-128">If hosting an FDD, confirm that the runtime is installed in **Programs & Features** or **Apps & features**.</span></span> <span data-ttu-id="5764c-129">如果需要特定的运行时，请从 [.NET 下载存档](https://dotnet.microsoft.com/download/archives)下载运行时并将其安装在系统上。</span><span class="sxs-lookup"><span data-stu-id="5764c-129">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="5764c-130">安装运行时后，重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS。</span><span class="sxs-lookup"><span data-stu-id="5764c-130">After installing the runtime, restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="5764c-131">OS 升级删除了 32 位 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="5764c-131">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="5764c-132">**应用程序日志：** 未能加载模块 DLL C:\WINDOWS\system32\inetsrv\aspnetcore.dll。</span><span class="sxs-lookup"><span data-stu-id="5764c-132">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="5764c-133">该数据是个错误。</span><span class="sxs-lookup"><span data-stu-id="5764c-133">The data is the error.</span></span>

<span data-ttu-id="5764c-134">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-134">Troubleshooting:</span></span>

<span data-ttu-id="5764c-135">OS 升级期间不会保留 C:\Windows\SysWOW64\inetsrv 目录中的非 OS 文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-135">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="5764c-136">如果在 OS 升级前已安装 ASP.NET Core 模块，且随后在 OS 升级后在 32 位模式下运行任何应用池，则会遇到此问题。</span><span class="sxs-lookup"><span data-stu-id="5764c-136">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="5764c-137">在 OS 升级后修复 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="5764c-137">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="5764c-138">请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="5764c-138">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="5764c-139">运行安装程序时，选择“修复”。</span><span class="sxs-lookup"><span data-stu-id="5764c-139">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="5764c-140">缺少站点扩展、安装了 32 位 (x86) 和 64 位 (x64) 站点扩展，或设置了错误的进程位数</span><span class="sxs-lookup"><span data-stu-id="5764c-140">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="5764c-141">适用于 Azure 应用服务托管的应用。</span><span class="sxs-lookup"><span data-stu-id="5764c-141">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="5764c-142">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="5764c-142">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="5764c-143">**应用程序日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="5764c-143">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="5764c-144">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="5764c-144">Could not find inprocess request handler.</span></span> <span data-ttu-id="5764c-145">调用 hostfxr 捕获的输出：无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="5764c-145">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="5764c-146">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="5764c-146">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="5764c-147">未能启动应用程序“/LM/W3SVC/1416782824/ROOT”，ErrorCode“0x8000ffff”。</span><span class="sxs-lookup"><span data-stu-id="5764c-147">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="5764c-148">**ASP.NET Core 模块 stdout 日志：** 无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="5764c-148">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="5764c-149">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="5764c-149">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5764c-150">**ASP.NET Core 模块调试日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="5764c-150">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="5764c-151">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="5764c-151">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="5764c-152">返回失败的 HRESULT：0x8000ffff。</span><span class="sxs-lookup"><span data-stu-id="5764c-152">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="5764c-153">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="5764c-153">Could not find inprocess request handler.</span></span> <span data-ttu-id="5764c-154">无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="5764c-154">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="5764c-155">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="5764c-155">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker-end

<span data-ttu-id="5764c-156">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-156">Troubleshooting:</span></span>

* <span data-ttu-id="5764c-157">如果在预览运行时运行该应用，请安装 32 位 (x86) 或  64位 (x64) 站点扩展，以匹配应用的位数和应用的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="5764c-157">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="5764c-158">请勿同时安装扩展或扩展的多个运行时版本。</span><span class="sxs-lookup"><span data-stu-id="5764c-158">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="5764c-159">ASP.NET Core {RUNTIME VERSION} (x86) 运行时</span><span class="sxs-lookup"><span data-stu-id="5764c-159">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="5764c-160">ASP.NET Core {RUNTIME VERSION} (x64) 运行时</span><span class="sxs-lookup"><span data-stu-id="5764c-160">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="5764c-161">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="5764c-161">Restart the app.</span></span> <span data-ttu-id="5764c-162">等待几秒钟，以便应用重新启动。</span><span class="sxs-lookup"><span data-stu-id="5764c-162">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="5764c-163">如果在预览运行时运行应用，且同时安装了 32 位 (x86) 和 64 位 (x64) [站点扩展](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)，请卸载与应用的位数不匹配的站点扩展。</span><span class="sxs-lookup"><span data-stu-id="5764c-163">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="5764c-164">删除站点扩展之后，重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="5764c-164">After removing the site extension, restart the app.</span></span> <span data-ttu-id="5764c-165">等待几秒钟，以便应用重新启动。</span><span class="sxs-lookup"><span data-stu-id="5764c-165">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="5764c-166">如果在预览运行时运行该应用，并且站点扩展的位数匹配应用的位数，请确认预览站点扩展的运行时版本匹配应用的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="5764c-166">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="5764c-167">确认“应用程序设置”中的应用“平台”与应用的位数匹配。</span><span class="sxs-lookup"><span data-stu-id="5764c-167">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="5764c-168">有关更多信息，请参见<xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>。</span><span class="sxs-lookup"><span data-stu-id="5764c-168">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="5764c-169">已部署 x86 应用，但 32 位应用未启用应用池</span><span class="sxs-lookup"><span data-stu-id="5764c-169">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="5764c-170">**浏览器：** HTTP 错误 500.30 - ANCM 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="5764c-170">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="5764c-171">**应用程序日志：** 物理根路径为 '{PATH}' 的应用程序 '/LM/W3SVC/5/ROOT' 遇到意外的托管异常，异常代码= '0xe0434352'。</span><span class="sxs-lookup"><span data-stu-id="5764c-171">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="5764c-172">请检查 stderr 日志以获取详细信息。</span><span class="sxs-lookup"><span data-stu-id="5764c-172">Please check the stderr logs for more information.</span></span> <span data-ttu-id="5764c-173">物理根路径为 '{PATH}' 的应用程序 '/LM/W3SVC/5/ROOT' 未能加载 clr 和托管应用程序。</span><span class="sxs-lookup"><span data-stu-id="5764c-173">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="5764c-174">CLR 工作线程提前退出</span><span class="sxs-lookup"><span data-stu-id="5764c-174">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="5764c-175">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空。</span><span class="sxs-lookup"><span data-stu-id="5764c-175">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5764c-176">**ASP.NET Core 模块调试日志：** 返回失败的 HRESULT：0x8007023e</span><span class="sxs-lookup"><span data-stu-id="5764c-176">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

<span data-ttu-id="5764c-177">发布自包含应用时，SDK 会捕获此方案。</span><span class="sxs-lookup"><span data-stu-id="5764c-177">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="5764c-178">如果 RID 与平台目标（例如，`win10-x64` RID 与项目文件中的 `<PlatformTarget>x86</PlatformTarget>`）不匹配，则 SDK 会产生错误。</span><span class="sxs-lookup"><span data-stu-id="5764c-178">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="5764c-179">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-179">Troubleshooting:</span></span>

<span data-ttu-id="5764c-180">对于依赖 x86 框架的部署 (`<PlatformTarget>x86</PlatformTarget>`)，请为 32 位应用启用 IIS 应用池。</span><span class="sxs-lookup"><span data-stu-id="5764c-180">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="5764c-181">在 IIS 管理器中，打开应用池的“高级设置”并将“启用 32 位应用程序”设置为 True。</span><span class="sxs-lookup"><span data-stu-id="5764c-181">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="5764c-182">平台与 RID 冲突</span><span class="sxs-lookup"><span data-stu-id="5764c-182">Platform conflicts with RID</span></span>

* <span data-ttu-id="5764c-183">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="5764c-183">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="5764c-184">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' 命令行启动进程，ErrorCode = '0x80004005 : ff。</span><span class="sxs-lookup"><span data-stu-id="5764c-184">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="5764c-185">**ASP.NET Core 模块 stdout 日志：** 未经处理的异常：System.BadImageFormatException：无法加载文件或程序集 '{ASSEMBLY}.dll'。</span><span class="sxs-lookup"><span data-stu-id="5764c-185">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="5764c-186">试图加载的程序的格式不正确。</span><span class="sxs-lookup"><span data-stu-id="5764c-186">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="5764c-187">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-187">Troubleshooting:</span></span>

* <span data-ttu-id="5764c-188">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="5764c-188">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="5764c-189">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="5764c-189">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="5764c-190">有关详细信息，请参阅[疑难解答 (IIS)](xref:host-and-deploy/iis/troubleshoot) 或[疑难解答（Azure 应用服务）](xref:host-and-deploy/azure-apps/troubleshoot)。</span><span class="sxs-lookup"><span data-stu-id="5764c-190">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="5764c-191">如果在升级应用和部署更新的程序集时，Azure 应用部署出现此异常，请从先前的部署中手动删除所有文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-191">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="5764c-192">部署升级的应用时，延迟的不兼容程序集可能造成 `System.BadImageFormatException` 异常。</span><span class="sxs-lookup"><span data-stu-id="5764c-192">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="5764c-193">URI 终结点错误或网站已停止</span><span class="sxs-lookup"><span data-stu-id="5764c-193">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="5764c-194">**浏览器：** ERR_CONNECTION_REFUSED --或者-- 无法连接</span><span class="sxs-lookup"><span data-stu-id="5764c-194">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="5764c-195">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="5764c-195">**Application Log:** No entry</span></span>

* <span data-ttu-id="5764c-196">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-196">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5764c-197">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-197">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5764c-198">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-198">Troubleshooting:</span></span>

* <span data-ttu-id="5764c-199">确认正在使用的应用的 URI 端点是否正确。</span><span class="sxs-lookup"><span data-stu-id="5764c-199">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="5764c-200">检查绑定。</span><span class="sxs-lookup"><span data-stu-id="5764c-200">Check the bindings.</span></span>

* <span data-ttu-id="5764c-201">确认 IIS 网站未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="5764c-201">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="5764c-202">已禁用 CoreWebEngine 或 W3SVC 服务器功能</span><span class="sxs-lookup"><span data-stu-id="5764c-202">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="5764c-203">**OS 异常：** 必须安装 IIS 7.0 CoreWebEngine 和 W3SVC 功能才能使用 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="5764c-203">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="5764c-204">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-204">Troubleshooting:</span></span>

<span data-ttu-id="5764c-205">确认启用了适当的角色和功能。</span><span class="sxs-lookup"><span data-stu-id="5764c-205">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="5764c-206">请参阅 [IIS 配置](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="5764c-206">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="5764c-207">网站物理路径不正确或缺少应用</span><span class="sxs-lookup"><span data-stu-id="5764c-207">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="5764c-208">**浏览器：** 403 禁止访问 - 访问被拒绝 --或者-- 403.14 禁止访问 - Web 服务器被配置为不列出此目录的内容。</span><span class="sxs-lookup"><span data-stu-id="5764c-208">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="5764c-209">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="5764c-209">**Application Log:** No entry</span></span>

* <span data-ttu-id="5764c-210">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-210">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5764c-211">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-211">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5764c-212">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-212">Troubleshooting:</span></span>

<span data-ttu-id="5764c-213">检查 IIS 网站的“基本设置”和物理应用文件夹。</span><span class="sxs-lookup"><span data-stu-id="5764c-213">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="5764c-214">确认应用所在的文件夹位于 IIS 网站的物理路径中。</span><span class="sxs-lookup"><span data-stu-id="5764c-214">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="5764c-215">角色不正确、未安装 ASP.NET Core 模块或权限不正确</span><span class="sxs-lookup"><span data-stu-id="5764c-215">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="5764c-216">**浏览器：** 500.19 内部服务器错误 - 无法访问请求的页面，因为该页面的相关配置数据无效。</span><span class="sxs-lookup"><span data-stu-id="5764c-216">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="5764c-217">--或者-- 无法显示此页面</span><span class="sxs-lookup"><span data-stu-id="5764c-217">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="5764c-218">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="5764c-218">**Application Log:** No entry</span></span>

* <span data-ttu-id="5764c-219">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-219">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5764c-220">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-220">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5764c-221">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-221">Troubleshooting:</span></span>

* <span data-ttu-id="5764c-222">确认启用了适当的角色。</span><span class="sxs-lookup"><span data-stu-id="5764c-222">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="5764c-223">请参阅 [IIS 配置](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="5764c-223">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="5764c-224">打开“程序和功能”或“应用和功能”，然后确认是否安装了 Windows Server Hosting。</span><span class="sxs-lookup"><span data-stu-id="5764c-224">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="5764c-225">如果已安装的程序列表中没有 Windows Server Hosting，请下载并安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="5764c-225">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="5764c-226">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="5764c-226">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="5764c-227">有关详细信息，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="5764c-227">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="5764c-228">请确保将“应用程序池” > “进程模型” > “标识”设置为 ApplicationPoolIdentity，或确保自定义标识具有访问应用部署文件夹的相应权限。</span><span class="sxs-lookup"><span data-stu-id="5764c-228">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="5764c-229">如果卸载了 ASP.NET Core 托管捆绑包并已安装了一个早期版本的托管捆绑包，则 applicationHost.config 文件将不包含 ASP.NET Core 模块分区。</span><span class="sxs-lookup"><span data-stu-id="5764c-229">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="5764c-230">可打开 %windir%/System32/inetsrv/config 处的 applicationHost.config 文件并找到 `<configuration><configSections><sectionGroup name="system.webServer">` 分区组。</span><span class="sxs-lookup"><span data-stu-id="5764c-230">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="5764c-231">如果分区组中缺少 ASP.NET Core 模块分区，请添加以下分区元素：</span><span class="sxs-lookup"><span data-stu-id="5764c-231">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="5764c-232">或者，安装最新版本的 ASP.NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="5764c-232">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="5764c-233">最新版本与受支持的 ASP.NET Core 应用向后兼容。</span><span class="sxs-lookup"><span data-stu-id="5764c-233">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="5764c-234">processPath 不正确、缺少 PATH 变量、未安装托管捆绑包、未重启系统/IIS、未安装 VC++ Redistributable 或 dotnet.exe 访问冲突</span><span class="sxs-lookup"><span data-stu-id="5764c-234">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5764c-235">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="5764c-235">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="5764c-236">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"{...}" ' 命令行启动进程</span><span class="sxs-lookup"><span data-stu-id="5764c-236">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="5764c-237">，ErrorCode = '0x80070002 :0。</span><span class="sxs-lookup"><span data-stu-id="5764c-237">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="5764c-238">应用程序 '{PATH}' 无法启动。</span><span class="sxs-lookup"><span data-stu-id="5764c-238">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="5764c-239">'{PATH}' 中未找到可执行文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-239">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="5764c-240">未能启动应用程序 '/LM/W3SVC/2/ROOT'，ErrorCode '0x8007023e'。</span><span class="sxs-lookup"><span data-stu-id="5764c-240">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="5764c-241">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-241">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="5764c-242">**ASP.NET Core 模块调试日志：** 事件日志：应用程序 '{PATH}' 无法启动。</span><span class="sxs-lookup"><span data-stu-id="5764c-242">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="5764c-243">'{PATH}' 中未找到可执行文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-243">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="5764c-244">返回失败的 HRESULT：0x8007023e</span><span class="sxs-lookup"><span data-stu-id="5764c-244">Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="5764c-245">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="5764c-245">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="5764c-246">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"{...}" ' 命令行启动进程</span><span class="sxs-lookup"><span data-stu-id="5764c-246">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="5764c-247">，ErrorCode = '0x80070002 :0。</span><span class="sxs-lookup"><span data-stu-id="5764c-247">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="5764c-248">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空。</span><span class="sxs-lookup"><span data-stu-id="5764c-248">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="5764c-249">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-249">Troubleshooting:</span></span>

* <span data-ttu-id="5764c-250">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="5764c-250">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="5764c-251">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="5764c-251">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="5764c-252">有关详细信息，请参阅[疑难解答 (IIS)](xref:host-and-deploy/iis/troubleshoot) 或[疑难解答（Azure 应用服务）](xref:host-and-deploy/azure-apps/troubleshoot)。</span><span class="sxs-lookup"><span data-stu-id="5764c-252">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="5764c-253">检查 web.config 中 `<aspNetCore>` 元素的 processPath 属性，对于依赖框架的部署 (FDD)，确保它为 `dotnet`，对于[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)，确保它为 `.\{ASSEMBLY}.exe`。</span><span class="sxs-lookup"><span data-stu-id="5764c-253">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="5764c-254">对于 FDD，可能无法通过路径设置访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="5764c-254">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="5764c-255">确认“系统路径”设置中存在“C:\Program Files\dotnet”\\。</span><span class="sxs-lookup"><span data-stu-id="5764c-255">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="5764c-256">对于 FDD，应用池的用户标识可能无法访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="5764c-256">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="5764c-257">确认应用池用户标识具有访问 C:\Program Files\dotnet 目录的权限。</span><span class="sxs-lookup"><span data-stu-id="5764c-257">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="5764c-258">确认没有为应用池用户标识配置针对 C:\Program Files\dotnet 和应用目录的拒绝规则。</span><span class="sxs-lookup"><span data-stu-id="5764c-258">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="5764c-259">可能已部署 FDD 并安装了 .NET Core，但未重启 IIS。</span><span class="sxs-lookup"><span data-stu-id="5764c-259">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="5764c-260">重启服务器，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS。</span><span class="sxs-lookup"><span data-stu-id="5764c-260">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="5764c-261">可能已部署 FDD，但未在托管系统上安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="5764c-261">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="5764c-262">如果尚未安装 .NET Core 运行时，则运行系统上的 **.NET Core 托管捆绑包安装程序**。</span><span class="sxs-lookup"><span data-stu-id="5764c-262">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="5764c-263">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="5764c-263">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="5764c-264">有关详细信息，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="5764c-264">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="5764c-265">如果需要特定的运行时，请从 [.NET 下载存档](https://dotnet.microsoft.com/download/archives)下载运行时并将其安装在系统上。</span><span class="sxs-lookup"><span data-stu-id="5764c-265">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="5764c-266">重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS，完成安装。</span><span class="sxs-lookup"><span data-stu-id="5764c-266">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="5764c-267">可能已部署 FDD，但系统上未安装 Microsoft Visual C++ 2015 Redistributable (x64)。</span><span class="sxs-lookup"><span data-stu-id="5764c-267">An FDD may have been deployed and the *Microsoft Visual C++ 2015 Redistributable (x64)* isn't installed on the system.</span></span> <span data-ttu-id="5764c-268">可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53840)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="5764c-268">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="5764c-269">\<aspNetCore> 元素的参数不正确</span><span class="sxs-lookup"><span data-stu-id="5764c-269">Incorrect arguments of \<aspNetCore> element</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5764c-270">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="5764c-270">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="5764c-271">**应用程序日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="5764c-271">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="5764c-272">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="5764c-272">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="5764c-273">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="5764c-273">Could not find inprocess request handler.</span></span> <span data-ttu-id="5764c-274">调用 hostfxr 捕获的输出：是否希望运行 dotnet SDK 命令？</span><span class="sxs-lookup"><span data-stu-id="5764c-274">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="5764c-275">请从以下位置安装 dotnet SDK： https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 未能启动应用程序 '/LM/W3SVC/3/ROOT'，ErrorCode '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="5764c-275">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="5764c-276">**ASP.NET Core 模块 stdout 日志：** 是否希望运行 dotnet SDK 命令？</span><span class="sxs-lookup"><span data-stu-id="5764c-276">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="5764c-277">请从以下位置安装 dotnet SDK： https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span><span class="sxs-lookup"><span data-stu-id="5764c-277">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="5764c-278">**ASP.NET Core 模块调试日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="5764c-278">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="5764c-279">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="5764c-279">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="5764c-280">返回失败的 HRESULT：0x8000ffff 找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="5764c-280">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="5764c-281">调用 hostfxr 捕获的输出：是否希望运行 dotnet SDK 命令？</span><span class="sxs-lookup"><span data-stu-id="5764c-281">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="5764c-282">请从以下位置安装 dotnet SDK： https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 返回失败的 HRESULT：0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="5764c-282">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="5764c-283">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="5764c-283">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="5764c-284">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"dotnet" .\{ASSEMBLY}.dll' 命令行启动进程，ErrorCode = '0x80004005 :80008081。</span><span class="sxs-lookup"><span data-stu-id="5764c-284">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="5764c-285">**ASP.NET Core 模块 stdout 日志：** 要执行的应用程序不存在：'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="5764c-285">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

::: moniker-end

<span data-ttu-id="5764c-286">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-286">Troubleshooting:</span></span>

* <span data-ttu-id="5764c-287">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="5764c-287">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="5764c-288">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="5764c-288">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="5764c-289">有关详细信息，请参阅[疑难解答 (IIS)](xref:host-and-deploy/iis/troubleshoot) 或[疑难解答（Azure 应用服务）](xref:host-and-deploy/azure-apps/troubleshoot)。</span><span class="sxs-lookup"><span data-stu-id="5764c-289">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="5764c-290">检查 web.config 中 `<aspNetCore>` 元素的 arguments 属性，确认该属性：(a) 对于依赖框架的部署 (FDD) 为 `.\{ASSEMBLY}.dll`；或 (b) 对于独立部署 (SCD)，则为不存在、为空字符串 (`arguments=""`)，或为应用参数列表 (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`)。</span><span class="sxs-lookup"><span data-stu-id="5764c-290">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="5764c-291">缺少 .NET Core 共享框架</span><span class="sxs-lookup"><span data-stu-id="5764c-291">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="5764c-292">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="5764c-292">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="5764c-293">**应用程序日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="5764c-293">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="5764c-294">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="5764c-294">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="5764c-295">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="5764c-295">Could not find inprocess request handler.</span></span> <span data-ttu-id="5764c-296">调用 hostfxr 捕获的输出：无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="5764c-296">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="5764c-297">找不到指定的框架 'Microsoft.AspNetCore.App'、版本 '{VERSION}'。</span><span class="sxs-lookup"><span data-stu-id="5764c-297">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="5764c-298">未能启动应用程序 '/LM/W3SVC/5/ROOT'，ErrorCode '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="5764c-298">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="5764c-299">**ASP.NET Core 模块 stdout 日志：** 无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="5764c-299">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="5764c-300">找不到指定的框架 'Microsoft.AspNetCore.App'、版本 '{VERSION}'。</span><span class="sxs-lookup"><span data-stu-id="5764c-300">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="5764c-301">**ASP.NET Core 模块调试日志：** 返回失败的 HRESULT：0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="5764c-301">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

<span data-ttu-id="5764c-302">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-302">Troubleshooting:</span></span>

<span data-ttu-id="5764c-303">对于依赖框架的部署 (FDD)，确认已在系统上安装正确的运行时。</span><span class="sxs-lookup"><span data-stu-id="5764c-303">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="5764c-304">应用程序池已停止</span><span class="sxs-lookup"><span data-stu-id="5764c-304">Stopped Application Pool</span></span>

* <span data-ttu-id="5764c-305">**浏览器：** 503 服务不可用</span><span class="sxs-lookup"><span data-stu-id="5764c-305">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="5764c-306">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="5764c-306">**Application Log:** No entry</span></span>

* <span data-ttu-id="5764c-307">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-307">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5764c-308">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-308">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5764c-309">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-309">Troubleshooting:</span></span>

<span data-ttu-id="5764c-310">确认应用程序池未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="5764c-310">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="5764c-311">子应用程序包括 \<handlers> 部分</span><span class="sxs-lookup"><span data-stu-id="5764c-311">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="5764c-312">**浏览器：** HTTP 错误 500.19 - 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="5764c-312">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="5764c-313">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="5764c-313">**Application Log:** No entry</span></span>

* <span data-ttu-id="5764c-314">**ASP.NET Core 模块 stdout 日志：** 创建根应用的日志文件并显示正常操作。</span><span class="sxs-lookup"><span data-stu-id="5764c-314">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="5764c-315">未创建子应用的日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-315">The sub-app's log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5764c-316">**ASP.NET Core 模块调试日志：** 创建根应用的日志文件并显示正常操作。</span><span class="sxs-lookup"><span data-stu-id="5764c-316">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="5764c-317">未创建子应用的日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-317">The sub-app's log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5764c-318">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-318">Troubleshooting:</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5764c-319">确认子应用的 web.config 文件不包含 `<handlers>` 部分，或者子应用未继承父级应用的处理程序。</span><span class="sxs-lookup"><span data-stu-id="5764c-319">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="5764c-320">父级应用的 web.config 的`<system.webServer>` 放在 `<location>` 元素内。</span><span class="sxs-lookup"><span data-stu-id="5764c-320">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="5764c-321">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在父级应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="5764c-321">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="5764c-322">有关更多信息，请参见<xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="5764c-322">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5764c-323">确认子应用的 web.config 文件不包括 `<handlers>` 部分。</span><span class="sxs-lookup"><span data-stu-id="5764c-323">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

::: moniker-end

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="5764c-324">stdout 日志路径不正确</span><span class="sxs-lookup"><span data-stu-id="5764c-324">stdout log path incorrect</span></span>

* <span data-ttu-id="5764c-325">**浏览器：** 应用正常响应。</span><span class="sxs-lookup"><span data-stu-id="5764c-325">**Browser:** The app responds normally.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5764c-326">**应用程序日志：** 无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="5764c-326">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="5764c-327">异常消息：在 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 返回了 HRESULT 0x80070005。</span><span class="sxs-lookup"><span data-stu-id="5764c-327">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="5764c-328">无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中停止 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="5764c-328">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="5764c-329">异常消息：在 {PATH} 返回了 HRESULT 0x80070002。</span><span class="sxs-lookup"><span data-stu-id="5764c-329">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="5764c-330">无法在 {PATH}\aspnetcorev2_inprocess.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="5764c-330">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="5764c-331">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-331">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="5764c-332">**ASP.NET Core 模块调试日志：** 无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="5764c-332">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="5764c-333">异常消息：在 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 返回了 HRESULT 0x80070005。</span><span class="sxs-lookup"><span data-stu-id="5764c-333">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="5764c-334">无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中停止 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="5764c-334">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="5764c-335">异常消息：在 {PATH} 返回了 HRESULT 0x80070002。</span><span class="sxs-lookup"><span data-stu-id="5764c-335">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="5764c-336">无法在 {PATH}\aspnetcorev2_inprocess.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="5764c-336">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="5764c-337">**应用程序日志：** 警告：无法创建 stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log，ErrorCode = -2147024893。</span><span class="sxs-lookup"><span data-stu-id="5764c-337">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="5764c-338">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5764c-338">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="5764c-339">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-339">Troubleshooting:</span></span>

* <span data-ttu-id="5764c-340">web.config 的 `<aspNetCore>` 元素中指定的 `stdoutLogFile` 路径不存在。</span><span class="sxs-lookup"><span data-stu-id="5764c-340">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="5764c-341">有关详细信息，请参阅 [ASP.NET Core 模块：日志创建和重定向](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)。</span><span class="sxs-lookup"><span data-stu-id="5764c-341">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="5764c-342">应用池用户没有 stdout 日志路径的写入权限。</span><span class="sxs-lookup"><span data-stu-id="5764c-342">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="5764c-343">应用程序配置常见问题</span><span class="sxs-lookup"><span data-stu-id="5764c-343">Application configuration general issue</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5764c-344">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败 --或者-- HTTP 错误 500.30 - ANCM 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="5764c-344">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="5764c-345">**应用程序日志：** 变量</span><span class="sxs-lookup"><span data-stu-id="5764c-345">**Application Log:** Variable</span></span>

* <span data-ttu-id="5764c-346">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空或使用常规条目创建，直到应用失败。</span><span class="sxs-lookup"><span data-stu-id="5764c-346">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="5764c-347">**ASP.NET Core 模块调试日志：** 变量</span><span class="sxs-lookup"><span data-stu-id="5764c-347">**ASP.NET Core Module Debug Log:** Variable</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="5764c-348">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="5764c-348">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="5764c-349">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 通过 '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' 命令行创建了进程，但该进程出现故障、未响应或未在给定的端口 '{PORT}' 上侦听，ErrorCode = '{ERROR CODE}'</span><span class="sxs-lookup"><span data-stu-id="5764c-349">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="5764c-350">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空。</span><span class="sxs-lookup"><span data-stu-id="5764c-350">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="5764c-351">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="5764c-351">Troubleshooting:</span></span>

<span data-ttu-id="5764c-352">此进程无法启动，这很可能是由应用配置或编程问题引起的。</span><span class="sxs-lookup"><span data-stu-id="5764c-352">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="5764c-353">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="5764c-353">For more information, see the following topics:</span></span>

* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:test/troubleshoot>
