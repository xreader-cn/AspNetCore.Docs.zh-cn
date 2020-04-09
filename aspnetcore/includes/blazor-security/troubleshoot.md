## <a name="troubleshoot"></a><span data-ttu-id="5331b-101">疑难解答</span><span class="sxs-lookup"><span data-stu-id="5331b-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="5331b-102">Cookie 和网站数据</span><span class="sxs-lookup"><span data-stu-id="5331b-102">Cookies and site data</span></span>

<span data-ttu-id="5331b-103">Cookie 和站点数据可能会在应用更新中保留，并干扰测试和故障排除。</span><span class="sxs-lookup"><span data-stu-id="5331b-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="5331b-104">在更改应用代码、使用提供程序更改用户帐户或提供程序应用配置更改时清除以下内容：</span><span class="sxs-lookup"><span data-stu-id="5331b-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="5331b-105">用户登录 Cookie</span><span class="sxs-lookup"><span data-stu-id="5331b-105">User sign-in cookies</span></span>
* <span data-ttu-id="5331b-106">应用 Cookie</span><span class="sxs-lookup"><span data-stu-id="5331b-106">App cookies</span></span>
* <span data-ttu-id="5331b-107">缓存和存储的站点数据</span><span class="sxs-lookup"><span data-stu-id="5331b-107">Cached and stored site data</span></span>

<span data-ttu-id="5331b-108">防止挥之不去的 Cookie 和站点数据干扰测试和故障排除的一种方法是：</span><span class="sxs-lookup"><span data-stu-id="5331b-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="5331b-109">使用浏览器进行测试，您可以配置该浏览器可在每次关闭浏览器时删除所有 Cookie 和站点数据。</span><span class="sxs-lookup"><span data-stu-id="5331b-109">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
* <span data-ttu-id="5331b-110">关闭对应用、测试用户或提供程序配置的任何更改之间的浏览器。</span><span class="sxs-lookup"><span data-stu-id="5331b-110">Close the browser between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="5331b-111">运行服务器应用</span><span class="sxs-lookup"><span data-stu-id="5331b-111">Run the Server app</span></span>

<span data-ttu-id="5331b-112">测试和排除托管 Blazor 应用时，请确保从 **"服务器"** 项目运行该应用。</span><span class="sxs-lookup"><span data-stu-id="5331b-112">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="5331b-113">例如，在 Visual Studio 中，在使用以下任一方法启动应用之前，请确认服务器项目在**解决方案资源管理器**中突出显示：</span><span class="sxs-lookup"><span data-stu-id="5331b-113">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="5331b-114">选择“运行”按钮。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="5331b-114">Select the **Run** button.</span></span>
* <span data-ttu-id="5331b-115">使用**从** > 菜单**中调试开始调试**。</span><span class="sxs-lookup"><span data-stu-id="5331b-115">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="5331b-116">按 <kbd>F5</kbd>。</span><span class="sxs-lookup"><span data-stu-id="5331b-116">Press <kbd>F5</kbd>.</span></span>
