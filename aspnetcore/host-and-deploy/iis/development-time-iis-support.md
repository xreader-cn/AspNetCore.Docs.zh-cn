---
<span data-ttu-id="ec768-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ec768-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec768-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec768-102">'Blazor'</span></span>
- <span data-ttu-id="ec768-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec768-103">'Identity'</span></span>
- <span data-ttu-id="ec768-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec768-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec768-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec768-105">'Razor'</span></span>
- <span data-ttu-id="ec768-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ec768-106">'SignalR' uid:</span></span> 

---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="ec768-107">Visual Studio 中针对 ASP.NET Core 的开发时 IIS 支持</span><span class="sxs-lookup"><span data-stu-id="ec768-107">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="ec768-108">作者：[Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="ec768-108">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec768-109">本文介绍了对调试在 Windows Server 上与 IIS 一起运行的 ASP.NET Core 应用的 [Visual Studio](https://visualstudio.microsoft.com) 支持。</span><span class="sxs-lookup"><span data-stu-id="ec768-109">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="ec768-110">本主题逐步介绍了如何启用此方案并设置项目。</span><span class="sxs-lookup"><span data-stu-id="ec768-110">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ec768-111">先决条件</span><span class="sxs-lookup"><span data-stu-id="ec768-111">Prerequisites</span></span>

* [<span data-ttu-id="ec768-112">Visual Studio（适用于 Windows）</span><span class="sxs-lookup"><span data-stu-id="ec768-112">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="ec768-113">ASP.NET 和 Web 开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="ec768-113">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="ec768-114">.NET Core 跨平台开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="ec768-114">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="ec768-115">X.509 安全证书（用于 HTTPS 支持）</span><span class="sxs-lookup"><span data-stu-id="ec768-115">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="ec768-116">启用 IIS</span><span class="sxs-lookup"><span data-stu-id="ec768-116">Enable IIS</span></span>

1. <span data-ttu-id="ec768-117">在 Windows 中，导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="ec768-117">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="ec768-118">选中“Internet Information Services”复选框。</span><span class="sxs-lookup"><span data-stu-id="ec768-118">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="ec768-119">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="ec768-119">Select **OK**.</span></span>

<span data-ttu-id="ec768-120">IIS 安装可能需要重启系统。</span><span class="sxs-lookup"><span data-stu-id="ec768-120">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="ec768-121">配置 IIS</span><span class="sxs-lookup"><span data-stu-id="ec768-121">Configure IIS</span></span>

<span data-ttu-id="ec768-122">IIS 必须具有具备以下配置的网站：</span><span class="sxs-lookup"><span data-stu-id="ec768-122">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="ec768-123">主机名：通常情况下，使用“主机名”为“`localhost`”的“默认网站”。</span><span class="sxs-lookup"><span data-stu-id="ec768-123">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="ec768-124">不过，任何具有唯一主机名的有效 IIS 网站也都可行。</span><span class="sxs-lookup"><span data-stu-id="ec768-124">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="ec768-125">**网站绑定**</span><span class="sxs-lookup"><span data-stu-id="ec768-125">**Site Binding**</span></span>
  * <span data-ttu-id="ec768-126">对于要求使用 HTTPS 的应用，请使用证书创建对端口 443 的绑定。</span><span class="sxs-lookup"><span data-stu-id="ec768-126">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="ec768-127">通常使用的是“IIS Express 开发证书”，但任何有效证书也都可行。</span><span class="sxs-lookup"><span data-stu-id="ec768-127">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="ec768-128">对于使用 HTTP 的应用，请确认是否存在对端口 80 的绑定，或为新网站创建对端口 80 的绑定。</span><span class="sxs-lookup"><span data-stu-id="ec768-128">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="ec768-129">对 HTTP 或 HTTPS 使用单一绑定。</span><span class="sxs-lookup"><span data-stu-id="ec768-129">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="ec768-130">不支持同时绑定到 HTTP 和 HTTPS 端口。</span><span class="sxs-lookup"><span data-stu-id="ec768-130">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="ec768-131">在 Visual Studio 中启用开发时 IIS 支持</span><span class="sxs-lookup"><span data-stu-id="ec768-131">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="ec768-132">启动 Visual Studio 安装程序。</span><span class="sxs-lookup"><span data-stu-id="ec768-132">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="ec768-133">对于计划用于 IIS 开发时支持的 Visual Studio 安装，选择“修改”。</span><span class="sxs-lookup"><span data-stu-id="ec768-133">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="ec768-134">对于 ASP.NET 和 Web 开发工作负荷，找到并安装“开发时 IIS 支持”组件。</span><span class="sxs-lookup"><span data-stu-id="ec768-134">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="ec768-135">在工作负荷右侧的“安装详细信息”面板中，“开发时 IIS 支持”下的“可选”部分列出了此组件。</span><span class="sxs-lookup"><span data-stu-id="ec768-135">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="ec768-136">该组件将安装 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，该模块是运行具有 IIS 的 ASP.NET Core 应用所需的本机 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="ec768-136">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="ec768-137">配置项目</span><span class="sxs-lookup"><span data-stu-id="ec768-137">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="ec768-138">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="ec768-138">HTTPS redirection</span></span>

<span data-ttu-id="ec768-139">对于要求使用 HTTPS 的新项目，选中“新建 ASP.NET Core Web 应用”窗口中的“针对 HTTPS 进行配置”复选框。</span><span class="sxs-lookup"><span data-stu-id="ec768-139">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="ec768-140">选中此复选框会向创建后的应用添加 [HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="ec768-140">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="ec768-141">对于要求使用 HTTPS 的现有项目，使用 `Startup.Configure` 中的 HTTPS 重定向和 HSTS 中间件。</span><span class="sxs-lookup"><span data-stu-id="ec768-141">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="ec768-142">有关详细信息，请参阅 <xref:security/enforcing-ssl>。</span><span class="sxs-lookup"><span data-stu-id="ec768-142">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="ec768-143">对于使用 HTTP 的项目，[HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)不会添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="ec768-143">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="ec768-144">无需配置应用。</span><span class="sxs-lookup"><span data-stu-id="ec768-144">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="ec768-145">IIS 启动配置文件</span><span class="sxs-lookup"><span data-stu-id="ec768-145">IIS launch profile</span></span>

<span data-ttu-id="ec768-146">创建新的启动配置文件以添加开发时 IIS 支持：</span><span class="sxs-lookup"><span data-stu-id="ec768-146">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="ec768-147">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="ec768-147">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="ec768-148">选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="ec768-148">Select **Properties**.</span></span> <span data-ttu-id="ec768-149">打开“调试”选项卡。</span><span class="sxs-lookup"><span data-stu-id="ec768-149">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="ec768-150">对于配置文件，请选择“新建”按钮。</span><span class="sxs-lookup"><span data-stu-id="ec768-150">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="ec768-151">在弹出窗口中将该配置文件命名为“IIS”。</span><span class="sxs-lookup"><span data-stu-id="ec768-151">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="ec768-152">选择“确定”创建配置文件。</span><span class="sxs-lookup"><span data-stu-id="ec768-152">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="ec768-153">对于“启动”设置，请从列表中选择“IIS”。</span><span class="sxs-lookup"><span data-stu-id="ec768-153">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="ec768-154">选中“启动浏览器”复选框并提供终结点 URL。</span><span class="sxs-lookup"><span data-stu-id="ec768-154">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="ec768-155">如果应用要求使用 HTTPS，使用 HTTPS 终结点 (`https://`)。</span><span class="sxs-lookup"><span data-stu-id="ec768-155">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="ec768-156">如果应用要求使用 HTTP，使用 HTTP (`http://`) 终结点。</span><span class="sxs-lookup"><span data-stu-id="ec768-156">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="ec768-157">提供[前面指定的 IIS 配置](#configure-iis)使用的相同主机名和端口，通常为 `localhost`。</span><span class="sxs-lookup"><span data-stu-id="ec768-157">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="ec768-158">在 URL 结尾处提供应用名称。</span><span class="sxs-lookup"><span data-stu-id="ec768-158">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="ec768-159">例如，`https://localhost/WebApplication1` (HTTPS) 或 `http://localhost/WebApplication1` (HTTP) 是有效的终结点 URL。</span><span class="sxs-lookup"><span data-stu-id="ec768-159">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="ec768-160">在“环境变量”部分中，选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="ec768-160">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="ec768-161">提供“名称”为“`ASPNETCORE_ENVIRONMENT`”且“值”为“`Development`”的环境变量。</span><span class="sxs-lookup"><span data-stu-id="ec768-161">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="ec768-162">在“Web 服务器设置”区域中，将“应用 URL”设置为用于“启动浏览器”终结点 URL 的相同值。</span><span class="sxs-lookup"><span data-stu-id="ec768-162">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="ec768-163">对于 Visual Studio 2019 或更高版本中的“托管模型”设置，选择“默认”，以使用项目使用的托管模型。</span><span class="sxs-lookup"><span data-stu-id="ec768-163">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="ec768-164">如果项目在自己的项目文件中设置 `<AspNetCoreHostingModel>` 属性，使用的是此属性的值（`InProcess` 或 `OutOfProcess`）。</span><span class="sxs-lookup"><span data-stu-id="ec768-164">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="ec768-165">如果没有设置此属性，使用的是应用的默认（进程内）托管模型。</span><span class="sxs-lookup"><span data-stu-id="ec768-165">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="ec768-166">如果应用需要不同于常规托管模型的显式托管模型，请根据需要将“托管模型”设置为“`In Process`”或“`Out Of Process`”。</span><span class="sxs-lookup"><span data-stu-id="ec768-166">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="ec768-167">保存配置文件。</span><span class="sxs-lookup"><span data-stu-id="ec768-167">Save the profile.</span></span>

<span data-ttu-id="ec768-168">如果未使用 Visual Studio，请将启动配置文件手动添加到“属性”文件夹中的 [launchSettings.json](https://json.schemastore.org/launchsettings) 文件内。</span><span class="sxs-lookup"><span data-stu-id="ec768-168">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="ec768-169">下面的示例展示了如何将配置文件配置为使用 HTTPS 协议：</span><span class="sxs-lookup"><span data-stu-id="ec768-169">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="ec768-170">确认 `applicationUrl` 和 `launchUrl` 终结点是否一致，且是否使用与 IIS 绑定配置相同的协议（HTTP 或 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="ec768-170">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="ec768-171">运行该项目</span><span class="sxs-lookup"><span data-stu-id="ec768-171">Run the project</span></span>

<span data-ttu-id="ec768-172">以管理员身份运行 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="ec768-172">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="ec768-173">确认已将生成配置下拉列表设置为“调试”。</span><span class="sxs-lookup"><span data-stu-id="ec768-173">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="ec768-174">将[“开始调试”按钮](/visualstudio/debugger/debugger-feature-tour)设置为 IIS 配置文件，然后选择此按钮以启动该应用。</span><span class="sxs-lookup"><span data-stu-id="ec768-174">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="ec768-175">如果不以管理员身份运行，Visual Studio 可能会提示重启。</span><span class="sxs-lookup"><span data-stu-id="ec768-175">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="ec768-176">如果出现提示，请重启 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="ec768-176">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="ec768-177">如果使用不受信任的开发证书，则浏览器可能会要求你为不受信任的证书创建异常。</span><span class="sxs-lookup"><span data-stu-id="ec768-177">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="ec768-178">通过[仅我的代码](/visualstudio/debugger/just-my-code)调试“发布”生成配置，从而导致降低编译器优化体验。</span><span class="sxs-lookup"><span data-stu-id="ec768-178">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="ec768-179">例如，不会遇到断点。</span><span class="sxs-lookup"><span data-stu-id="ec768-179">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec768-180">其他资源</span><span class="sxs-lookup"><span data-stu-id="ec768-180">Additional resources</span></span>

* [<span data-ttu-id="ec768-181">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="ec768-181">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec768-182">本文介绍了对调试在 Windows Server 上与 IIS 一起运行的 ASP.NET Core 应用的 [Visual Studio](https://visualstudio.microsoft.com) 支持。</span><span class="sxs-lookup"><span data-stu-id="ec768-182">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="ec768-183">本主题逐步介绍了如何启用此方案并设置项目。</span><span class="sxs-lookup"><span data-stu-id="ec768-183">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ec768-184">先决条件</span><span class="sxs-lookup"><span data-stu-id="ec768-184">Prerequisites</span></span>

* [<span data-ttu-id="ec768-185">Visual Studio（适用于 Windows）</span><span class="sxs-lookup"><span data-stu-id="ec768-185">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="ec768-186">ASP.NET 和 Web 开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="ec768-186">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="ec768-187">.NET Core 跨平台开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="ec768-187">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="ec768-188">X.509 安全证书（用于 HTTPS 支持）</span><span class="sxs-lookup"><span data-stu-id="ec768-188">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="ec768-189">启用 IIS</span><span class="sxs-lookup"><span data-stu-id="ec768-189">Enable IIS</span></span>

1. <span data-ttu-id="ec768-190">在 Windows 中，导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="ec768-190">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="ec768-191">选中“Internet Information Services”复选框。</span><span class="sxs-lookup"><span data-stu-id="ec768-191">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="ec768-192">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="ec768-192">Select **OK**.</span></span>

<span data-ttu-id="ec768-193">IIS 安装可能需要重启系统。</span><span class="sxs-lookup"><span data-stu-id="ec768-193">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="ec768-194">配置 IIS</span><span class="sxs-lookup"><span data-stu-id="ec768-194">Configure IIS</span></span>

<span data-ttu-id="ec768-195">IIS 必须具有具备以下配置的网站：</span><span class="sxs-lookup"><span data-stu-id="ec768-195">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="ec768-196">主机名：通常情况下，使用“主机名”为“`localhost`”的“默认网站”。</span><span class="sxs-lookup"><span data-stu-id="ec768-196">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="ec768-197">不过，任何具有唯一主机名的有效 IIS 网站也都可行。</span><span class="sxs-lookup"><span data-stu-id="ec768-197">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="ec768-198">**网站绑定**</span><span class="sxs-lookup"><span data-stu-id="ec768-198">**Site Binding**</span></span>
  * <span data-ttu-id="ec768-199">对于要求使用 HTTPS 的应用，请使用证书创建对端口 443 的绑定。</span><span class="sxs-lookup"><span data-stu-id="ec768-199">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="ec768-200">通常使用的是“IIS Express 开发证书”，但任何有效证书也都可行。</span><span class="sxs-lookup"><span data-stu-id="ec768-200">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="ec768-201">对于使用 HTTP 的应用，请确认是否存在对端口 80 的绑定，或为新网站创建对端口 80 的绑定。</span><span class="sxs-lookup"><span data-stu-id="ec768-201">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="ec768-202">对 HTTP 或 HTTPS 使用单一绑定。</span><span class="sxs-lookup"><span data-stu-id="ec768-202">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="ec768-203">不支持同时绑定到 HTTP 和 HTTPS 端口。</span><span class="sxs-lookup"><span data-stu-id="ec768-203">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="ec768-204">在 Visual Studio 中启用开发时 IIS 支持</span><span class="sxs-lookup"><span data-stu-id="ec768-204">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="ec768-205">启动 Visual Studio 安装程序。</span><span class="sxs-lookup"><span data-stu-id="ec768-205">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="ec768-206">对于计划用于 IIS 开发时支持的 Visual Studio 安装，选择“修改”。</span><span class="sxs-lookup"><span data-stu-id="ec768-206">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="ec768-207">对于 ASP.NET 和 Web 开发工作负荷，找到并安装“开发时 IIS 支持”组件。</span><span class="sxs-lookup"><span data-stu-id="ec768-207">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="ec768-208">在工作负荷右侧的“安装详细信息”面板中，“开发时 IIS 支持”下的“可选”部分列出了此组件。</span><span class="sxs-lookup"><span data-stu-id="ec768-208">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="ec768-209">该组件将安装 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，该模块是运行具有 IIS 的 ASP.NET Core 应用所需的本机 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="ec768-209">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="ec768-210">配置项目</span><span class="sxs-lookup"><span data-stu-id="ec768-210">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="ec768-211">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="ec768-211">HTTPS redirection</span></span>

<span data-ttu-id="ec768-212">对于要求使用 HTTPS 的新项目，选中“新建 ASP.NET Core Web 应用”窗口中的“针对 HTTPS 进行配置”复选框。</span><span class="sxs-lookup"><span data-stu-id="ec768-212">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="ec768-213">选中此复选框会向创建后的应用添加 [HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="ec768-213">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="ec768-214">对于要求使用 HTTPS 的现有项目，使用 `Startup.Configure` 中的 HTTPS 重定向和 HSTS 中间件。</span><span class="sxs-lookup"><span data-stu-id="ec768-214">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="ec768-215">有关详细信息，请参阅 <xref:security/enforcing-ssl>。</span><span class="sxs-lookup"><span data-stu-id="ec768-215">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="ec768-216">对于使用 HTTP 的项目，[HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)不会添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="ec768-216">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="ec768-217">无需配置应用。</span><span class="sxs-lookup"><span data-stu-id="ec768-217">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="ec768-218">IIS 启动配置文件</span><span class="sxs-lookup"><span data-stu-id="ec768-218">IIS launch profile</span></span>

<span data-ttu-id="ec768-219">创建新的启动配置文件以添加开发时 IIS 支持：</span><span class="sxs-lookup"><span data-stu-id="ec768-219">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="ec768-220">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="ec768-220">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="ec768-221">选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="ec768-221">Select **Properties**.</span></span> <span data-ttu-id="ec768-222">打开“调试”选项卡。</span><span class="sxs-lookup"><span data-stu-id="ec768-222">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="ec768-223">对于配置文件，请选择“新建”按钮。</span><span class="sxs-lookup"><span data-stu-id="ec768-223">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="ec768-224">在弹出窗口中将该配置文件命名为“IIS”。</span><span class="sxs-lookup"><span data-stu-id="ec768-224">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="ec768-225">选择“确定”创建配置文件。</span><span class="sxs-lookup"><span data-stu-id="ec768-225">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="ec768-226">对于“启动”设置，请从列表中选择“IIS”。</span><span class="sxs-lookup"><span data-stu-id="ec768-226">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="ec768-227">选中“启动浏览器”复选框并提供终结点 URL。</span><span class="sxs-lookup"><span data-stu-id="ec768-227">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="ec768-228">如果应用要求使用 HTTPS，使用 HTTPS 终结点 (`https://`)。</span><span class="sxs-lookup"><span data-stu-id="ec768-228">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="ec768-229">如果应用要求使用 HTTP，使用 HTTP (`http://`) 终结点。</span><span class="sxs-lookup"><span data-stu-id="ec768-229">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="ec768-230">提供[前面指定的 IIS 配置](#configure-iis)使用的相同主机名和端口，通常为 `localhost`。</span><span class="sxs-lookup"><span data-stu-id="ec768-230">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="ec768-231">在 URL 结尾处提供应用名称。</span><span class="sxs-lookup"><span data-stu-id="ec768-231">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="ec768-232">例如，`https://localhost/WebApplication1` (HTTPS) 或 `http://localhost/WebApplication1` (HTTP) 是有效的终结点 URL。</span><span class="sxs-lookup"><span data-stu-id="ec768-232">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="ec768-233">在“环境变量”部分中，选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="ec768-233">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="ec768-234">提供“名称”为“`ASPNETCORE_ENVIRONMENT`”且“值”为“`Development`”的环境变量。</span><span class="sxs-lookup"><span data-stu-id="ec768-234">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="ec768-235">在“Web 服务器设置”区域中，将“应用 URL”设置为用于“启动浏览器”终结点 URL 的相同值。</span><span class="sxs-lookup"><span data-stu-id="ec768-235">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="ec768-236">对于 Visual Studio 2019 或更高版本中的“托管模型”设置，选择“默认”，以使用项目使用的托管模型。</span><span class="sxs-lookup"><span data-stu-id="ec768-236">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="ec768-237">如果项目在自己的项目文件中设置 `<AspNetCoreHostingModel>` 属性，使用的是此属性的值（`InProcess` 或 `OutOfProcess`）。</span><span class="sxs-lookup"><span data-stu-id="ec768-237">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="ec768-238">如果没有设置此属性，使用的是应用的默认（进程外）托管模型。</span><span class="sxs-lookup"><span data-stu-id="ec768-238">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="ec768-239">如果应用需要不同于常规托管模型的显式托管模型，请根据需要将“托管模型”设置为“`In Process`”或“`Out Of Process`”。</span><span class="sxs-lookup"><span data-stu-id="ec768-239">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="ec768-240">保存配置文件。</span><span class="sxs-lookup"><span data-stu-id="ec768-240">Save the profile.</span></span>

<span data-ttu-id="ec768-241">如果未使用 Visual Studio，请将启动配置文件手动添加到“属性”文件夹中的 [launchSettings.json](https://json.schemastore.org/launchsettings) 文件内。</span><span class="sxs-lookup"><span data-stu-id="ec768-241">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="ec768-242">下面的示例展示了如何将配置文件配置为使用 HTTPS 协议：</span><span class="sxs-lookup"><span data-stu-id="ec768-242">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="ec768-243">确认 `applicationUrl` 和 `launchUrl` 终结点是否一致，且是否使用与 IIS 绑定配置相同的协议（HTTP 或 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="ec768-243">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="ec768-244">运行该项目</span><span class="sxs-lookup"><span data-stu-id="ec768-244">Run the project</span></span>

<span data-ttu-id="ec768-245">以管理员身份运行 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="ec768-245">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="ec768-246">确认已将生成配置下拉列表设置为“调试”。</span><span class="sxs-lookup"><span data-stu-id="ec768-246">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="ec768-247">将[“开始调试”按钮](/visualstudio/debugger/debugger-feature-tour)设置为 IIS 配置文件，然后选择此按钮以启动该应用。</span><span class="sxs-lookup"><span data-stu-id="ec768-247">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="ec768-248">如果不以管理员身份运行，Visual Studio 可能会提示重启。</span><span class="sxs-lookup"><span data-stu-id="ec768-248">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="ec768-249">如果出现提示，请重启 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="ec768-249">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="ec768-250">如果使用不受信任的开发证书，则浏览器可能会要求你为不受信任的证书创建异常。</span><span class="sxs-lookup"><span data-stu-id="ec768-250">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="ec768-251">通过[仅我的代码](/visualstudio/debugger/just-my-code)调试“发布”生成配置，从而导致降低编译器优化体验。</span><span class="sxs-lookup"><span data-stu-id="ec768-251">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="ec768-252">例如，不会遇到断点。</span><span class="sxs-lookup"><span data-stu-id="ec768-252">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec768-253">其他资源</span><span class="sxs-lookup"><span data-stu-id="ec768-253">Additional resources</span></span>

* [<span data-ttu-id="ec768-254">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="ec768-254">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
