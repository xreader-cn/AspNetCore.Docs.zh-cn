## <a name="troubleshoot"></a><span data-ttu-id="61755-101">疑难解答</span><span class="sxs-lookup"><span data-stu-id="61755-101">Troubleshoot</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a><span data-ttu-id="61755-102">常见错误</span><span class="sxs-lookup"><span data-stu-id="61755-102">Common errors</span></span>

* <span data-ttu-id="61755-103">AAD 的客户端未获得授权</span><span class="sxs-lookup"><span data-stu-id="61755-103">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="61755-104">信息：Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] 授权失败。</span><span class="sxs-lookup"><span data-stu-id="61755-104">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="61755-105">不符合以下要求：DenyAnonymousAuthorizationRequirement：要求用户经过身份验证。</span><span class="sxs-lookup"><span data-stu-id="61755-105">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="61755-106">AAD 返回的登录回叫错误：</span><span class="sxs-lookup"><span data-stu-id="61755-106">Login callback error from AAD:</span></span>

  * <span data-ttu-id="61755-107">错误：`unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="61755-107">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="61755-108">说明：`AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="61755-108">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="61755-109">若要解决该错误：</span><span class="sxs-lookup"><span data-stu-id="61755-109">To resolve the error:</span></span>

  1. <span data-ttu-id="61755-110">在 Azure 门户中访问[应用的清单](/azure/active-directory/develop/reference-app-manifest)。</span><span class="sxs-lookup"><span data-stu-id="61755-110">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="61755-111">将 [`allowPublicClient`](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) 属性设置为 `null` 或 `true`。</span><span class="sxs-lookup"><span data-stu-id="61755-111">Set the [`allowPublicClient`](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) attribute to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="61755-112">Cookie 和站点数据</span><span class="sxs-lookup"><span data-stu-id="61755-112">Cookies and site data</span></span>

<span data-ttu-id="61755-113">Cookie 和站点数据在经过应用更新后仍可保持不变，并且会干扰测试和故障排除。</span><span class="sxs-lookup"><span data-stu-id="61755-113">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="61755-114">在更改应用代码、更改提供程序的用户帐户或更改提供程序的应用配置时，请清除以下内容：</span><span class="sxs-lookup"><span data-stu-id="61755-114">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="61755-115">用户登录 Cookie</span><span class="sxs-lookup"><span data-stu-id="61755-115">User sign-in cookies</span></span>
* <span data-ttu-id="61755-116">应用 Cookie</span><span class="sxs-lookup"><span data-stu-id="61755-116">App cookies</span></span>
* <span data-ttu-id="61755-117">缓存和存储的站点数据</span><span class="sxs-lookup"><span data-stu-id="61755-117">Cached and stored site data</span></span>

<span data-ttu-id="61755-118">防止存留的 Cookie 和站点数据干扰测试和故障排除的一种方法是：</span><span class="sxs-lookup"><span data-stu-id="61755-118">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="61755-119">配置浏览器</span><span class="sxs-lookup"><span data-stu-id="61755-119">Configure a browser</span></span>
  * <span data-ttu-id="61755-120">使用浏览器测试是否可以配置为在每次关闭浏览器时删除所有 Cookie 和站点数据。</span><span class="sxs-lookup"><span data-stu-id="61755-120">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="61755-121">对于应用、测试用户或提供程序配置的任何更改，请确保浏览器是手动关闭的或由 IDE 关闭的。</span><span class="sxs-lookup"><span data-stu-id="61755-121">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="61755-122">在 Visual Studio 中使用自定义命令以 incognito 或 private 模式打开浏览器：</span><span class="sxs-lookup"><span data-stu-id="61755-122">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="61755-123">通过 Visual Studio 的“运行”按钮打开“浏览工具”对话框 。</span><span class="sxs-lookup"><span data-stu-id="61755-123">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="61755-124">选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="61755-124">Select the **Add** button.</span></span>
  * <span data-ttu-id="61755-125">在“程序”字段中提供浏览器的路径。</span><span class="sxs-lookup"><span data-stu-id="61755-125">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="61755-126">以下可执行路径是适用于 Windows 10 的典型安装位置。</span><span class="sxs-lookup"><span data-stu-id="61755-126">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="61755-127">如果浏览器安装在其他位置，或者未使用 Windows 10，请提供浏览器可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="61755-127">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="61755-128">Microsoft Edge：`C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="61755-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="61755-129">Google Chrome：`C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="61755-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="61755-130">Mozilla Firefox：`C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="61755-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="61755-131">在“参数”字段中，提供浏览器用来在 incognito 或 private 模式下执行打开操作的命令行选项。</span><span class="sxs-lookup"><span data-stu-id="61755-131">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="61755-132">某些浏览器需要应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="61755-132">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="61755-133">Microsoft Edge：请使用 `-inprivate`。</span><span class="sxs-lookup"><span data-stu-id="61755-133">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="61755-134">Google Chrome：使用 `--incognito --new-window {URL}`，其中占位符 `{URL}` 是要打开的 URL（例如，`https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="61755-134">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="61755-135">Mozilla Firefox：使用 `-private -url {URL}`，其中占位符 `{URL}` 是要打开的 URL（例如，`https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="61755-135">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="61755-136">在“友好名称”字段中提供名称。</span><span class="sxs-lookup"><span data-stu-id="61755-136">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="61755-137">例如 `Firefox Auth Testing`。</span><span class="sxs-lookup"><span data-stu-id="61755-137">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="61755-138">选择“确定”按钮。</span><span class="sxs-lookup"><span data-stu-id="61755-138">Select the **OK** button.</span></span>
  * <span data-ttu-id="61755-139">若要避免在每次迭代使用应用进行测试时必须选择浏览器配置文件，请使用“设置为默认值”按钮将配置文件设置为默认值。</span><span class="sxs-lookup"><span data-stu-id="61755-139">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="61755-140">对于应用、测试用户或提供程序配置的任何更改，请确保浏览器是由 IDE 关闭的。</span><span class="sxs-lookup"><span data-stu-id="61755-140">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="61755-141">运行 Server 应用</span><span class="sxs-lookup"><span data-stu-id="61755-141">Run the Server app</span></span>

<span data-ttu-id="61755-142">在对托管的 Blazor 应用进行测试和故障排除时，请确保从 `Server` 项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="61755-142">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="61755-143">例如，在 Visual Studio 中，确认在用以下任一方法启动应用之前，解决方案资源管理器中突出显示了 Server 项目：</span><span class="sxs-lookup"><span data-stu-id="61755-143">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="61755-144">选择“运行”按钮。</span><span class="sxs-lookup"><span data-stu-id="61755-144">Select the **Run** button.</span></span>
* <span data-ttu-id="61755-145">从菜单栏中，依次使用“调试” > “开始调试” 。</span><span class="sxs-lookup"><span data-stu-id="61755-145">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="61755-146">按 F5 <kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="61755-146">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="61755-147">检查 JSON Web 令牌 (JWT) 的内容</span><span class="sxs-lookup"><span data-stu-id="61755-147">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="61755-148">若要对 JSON Web 令牌 (JWT) 进行解码，请使用 Microsoft 的 [jwt.ms](https://jwt.ms/) 工具。</span><span class="sxs-lookup"><span data-stu-id="61755-148">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="61755-149">UI 中的值永远不会离开浏览器。</span><span class="sxs-lookup"><span data-stu-id="61755-149">Values in the UI never leave your browser.</span></span>
