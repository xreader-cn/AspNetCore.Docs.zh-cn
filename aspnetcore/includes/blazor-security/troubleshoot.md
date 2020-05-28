## <a name="troubleshoot"></a><span data-ttu-id="c68b7-101">疑难解答</span><span class="sxs-lookup"><span data-stu-id="c68b7-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="c68b7-102">Cookie 和站点数据</span><span class="sxs-lookup"><span data-stu-id="c68b7-102">Cookies and site data</span></span>

<span data-ttu-id="c68b7-103">Cookie 和站点数据可跨应用程序更新保持不变，并干扰测试和故障排除。</span><span class="sxs-lookup"><span data-stu-id="c68b7-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="c68b7-104">更改应用程序代码、更改提供程序的用户帐户更改或提供程序应用配置更改时，请清除以下内容：</span><span class="sxs-lookup"><span data-stu-id="c68b7-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="c68b7-105">用户登录 cookie</span><span class="sxs-lookup"><span data-stu-id="c68b7-105">User sign-in cookies</span></span>
* <span data-ttu-id="c68b7-106">应用 cookie</span><span class="sxs-lookup"><span data-stu-id="c68b7-106">App cookies</span></span>
* <span data-ttu-id="c68b7-107">缓存和存储的站点数据</span><span class="sxs-lookup"><span data-stu-id="c68b7-107">Cached and stored site data</span></span>

<span data-ttu-id="c68b7-108">防止延迟 cookie 和站点数据干扰测试和故障排除的一种方法是：</span><span class="sxs-lookup"><span data-stu-id="c68b7-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="c68b7-109">配置浏览器</span><span class="sxs-lookup"><span data-stu-id="c68b7-109">Configure a browser</span></span>
  * <span data-ttu-id="c68b7-110">使用浏览器进行测试，你可以将其配置为在每次关闭浏览器时删除所有 cookie 和站点数据。</span><span class="sxs-lookup"><span data-stu-id="c68b7-110">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="c68b7-111">请确保在应用程序、测试用户或提供程序配置的任何更改之间手动或通过 IDE 关闭浏览器。</span><span class="sxs-lookup"><span data-stu-id="c68b7-111">Make sure that the browser is closed manually or by the IDE between any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="c68b7-112">在 Visual Studio 中使用自定义命令以 incognito 或 private 模式打开浏览器：</span><span class="sxs-lookup"><span data-stu-id="c68b7-112">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="c68b7-113">从 Visual Studio 的 "**运行**" 按钮打开 "**浏览方式**" 对话框。</span><span class="sxs-lookup"><span data-stu-id="c68b7-113">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="c68b7-114">选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="c68b7-114">Select the **Add** button.</span></span>
  * <span data-ttu-id="c68b7-115">在 "**程序**" 字段中提供浏览器的路径。</span><span class="sxs-lookup"><span data-stu-id="c68b7-115">Provide the path to your browser in the **Program** field.</span></span>
  * <span data-ttu-id="c68b7-116">在 "**参数**" 字段中，提供浏览器用来在 incognito 或专用模式下打开的命令行选项以及应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="c68b7-116">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode and the URL of the app.</span></span> <span data-ttu-id="c68b7-117">例如：</span><span class="sxs-lookup"><span data-stu-id="c68b7-117">For example:</span></span>
    * <span data-ttu-id="c68b7-118">Google Chrome：`--incognito --new-window https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="c68b7-118">Google Chrome: `--incognito --new-window https://localhost:5001`</span></span>
    * <span data-ttu-id="c68b7-119">Mozilla Firefox：`-private -url https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="c68b7-119">Mozilla Firefox: `-private -url https://localhost:5001`</span></span>
  * <span data-ttu-id="c68b7-120">在 "**友好名称**" 字段中提供名称。</span><span class="sxs-lookup"><span data-stu-id="c68b7-120">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="c68b7-121">例如，`Firefox Auth Testing`。</span><span class="sxs-lookup"><span data-stu-id="c68b7-121">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="c68b7-122">选择“确定”  按钮。</span><span class="sxs-lookup"><span data-stu-id="c68b7-122">Select the **OK** button.</span></span>
  * <span data-ttu-id="c68b7-123">若要避免必须为应用的每个测试迭代选择浏览器配置文件，请使用 "**设置为默认值**" 按钮将配置文件设置为默认配置文件。</span><span class="sxs-lookup"><span data-stu-id="c68b7-123">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="c68b7-124">请确保在对应用程序、测试用户或提供程序配置进行任何更改之间的 IDE 关闭浏览器。</span><span class="sxs-lookup"><span data-stu-id="c68b7-124">Make sure that the browser is closed by the IDE between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="c68b7-125">运行服务器应用程序</span><span class="sxs-lookup"><span data-stu-id="c68b7-125">Run the Server app</span></span>

<span data-ttu-id="c68b7-126">在对托管的 Blazor 应用进行测试和故障排除时，请确保从**服务器**项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="c68b7-126">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="c68b7-127">例如，在 Visual Studio 中，确认在用以下任一方法启动应用之前**解决方案资源管理器**中突出显示了服务器项目：</span><span class="sxs-lookup"><span data-stu-id="c68b7-127">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="c68b7-128">选择“运行”按钮。</span><span class="sxs-lookup"><span data-stu-id="c68b7-128">Select the **Run** button.</span></span>
* <span data-ttu-id="c68b7-129">使用菜单中的 "**调试**" "  >  **开始调试**"。</span><span class="sxs-lookup"><span data-stu-id="c68b7-129">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="c68b7-130">按 <kbd>F5</kbd>。</span><span class="sxs-lookup"><span data-stu-id="c68b7-130">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="c68b7-131">检查 JSON Web 令牌的内容（JWT）</span><span class="sxs-lookup"><span data-stu-id="c68b7-131">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="c68b7-132">若要对 JSON Web 令牌（JWT）进行解码，请使用 Microsoft 的[jwt.ms](https://jwt.ms/)工具。</span><span class="sxs-lookup"><span data-stu-id="c68b7-132">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="c68b7-133">UI 中的值永远不会离开浏览器。</span><span class="sxs-lookup"><span data-stu-id="c68b7-133">Values in the UI never leave your browser.</span></span>
