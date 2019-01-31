---
uid: web-api/overview/security/enabling-cross-origin-requests-in-web-api
title: 启用 ASP.NET Web API 2 中的跨域请求 |Microsoft Docs
author: MikeWasson
description: 演示如何在 ASP.NET Web API 中支持跨域资源共享 (CORS)。
ms.author: riande
ms.date: 01/29/2019
ms.assetid: 9b265a5a-6a70-4a82-adce-2d7c56ae8bdd
msc.legacyurl: /web-api/overview/security/enabling-cross-origin-requests-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 97a0027194b019b09e220493dcb593e682027fe3
ms.sourcegitcommit: d22b3c23c45a076c4f394a70b1c8df2fbcdf656d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55428442"
---
<a name="enable-cross-origin-requests-in-aspnet-web-api-2"></a><span data-ttu-id="3267f-103">启用 ASP.NET Web API 2 中的跨域请求</span><span class="sxs-lookup"><span data-stu-id="3267f-103">Enable cross-origin requests in ASP.NET Web API 2</span></span>
====================
<span data-ttu-id="3267f-104">通过[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="3267f-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

> <span data-ttu-id="3267f-105">浏览器安全策略阻止 Web 页向另一个域发出 AJAX 请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-105">Browser security prevents a web page from making AJAX requests to another domain.</span></span> <span data-ttu-id="3267f-106">此限制称为“同源策略”，可防止恶意站点读取另一个站点中的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="3267f-106">This restriction is called the *same-origin policy*, and prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="3267f-107">但是，有时你可能想要让调用 web API 的其他站点。</span><span class="sxs-lookup"><span data-stu-id="3267f-107">However, sometimes you might want to let other sites call your web API.</span></span>
>
> <span data-ttu-id="3267f-108">[跨域资源共享](http://www.w3.org/TR/cors/)(CORS) 是一种 W3C 标准，允许服务器放宽同源策略。</span><span class="sxs-lookup"><span data-stu-id="3267f-108">[Cross Origin Resource Sharing](http://www.w3.org/TR/cors/) (CORS) is a W3C standard that allows a server to relax the same-origin policy.</span></span> <span data-ttu-id="3267f-109">使用 CORS，服务器可以在显式允许某些跨域请求时拒绝其他跨域请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-109">Using CORS, a server can explicitly allow some cross-origin requests while rejecting others.</span></span> <span data-ttu-id="3267f-110">CORS 比早期技术（例如 [JSONP](http://en.wikipedia.org/wiki/JSONP)）更安全、更灵活。</span><span class="sxs-lookup"><span data-stu-id="3267f-110">CORS is safer and more flexible than earlier techniques such as [JSONP](http://en.wikipedia.org/wiki/JSONP).</span></span> <span data-ttu-id="3267f-111">本教程演示如何在 Web API 应用程序中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3267f-111">This tutorial shows how to enable CORS in your Web API application.</span></span>
>
> ## <a name="software-used-in-the-tutorial"></a><span data-ttu-id="3267f-112">在本教程中使用的软件</span><span class="sxs-lookup"><span data-stu-id="3267f-112">Software used in the tutorial</span></span>
>
> - [<span data-ttu-id="3267f-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3267f-113">Visual Studio</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
> - <span data-ttu-id="3267f-114">Web API 2.2</span><span class="sxs-lookup"><span data-stu-id="3267f-114">Web API 2.2</span></span>

## <a name="introduction"></a><span data-ttu-id="3267f-115">介绍</span><span class="sxs-lookup"><span data-stu-id="3267f-115">Introduction</span></span>

<span data-ttu-id="3267f-116">本教程演示了在 ASP.NET Web API 中的 CORS 支持。</span><span class="sxs-lookup"><span data-stu-id="3267f-116">This tutorial demonstrates CORS support in ASP.NET Web API.</span></span> <span data-ttu-id="3267f-117">我们将首先创建两个 ASP.NET 项目-一个名为"web 服务"，它承载 Web API 控制器，以及其他名为"WebClient"，后者调用 web 服务。</span><span class="sxs-lookup"><span data-stu-id="3267f-117">We'll start by creating two ASP.NET projects – one called "WebService", which hosts a Web API controller, and the other called "WebClient", which calls WebService.</span></span> <span data-ttu-id="3267f-118">由于两个应用程序承载在不同的域中，从 WebClient 对 web 服务的 AJAX 请求是跨域请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-118">Because the two applications are hosted at different domains, an AJAX request from WebClient to WebService is a cross-origin request.</span></span>

![](enabling-cross-origin-requests-in-web-api/_static/image1.png)

### <a name="what-is-same-origin"></a><span data-ttu-id="3267f-119">什么是"相同的原点"？</span><span class="sxs-lookup"><span data-stu-id="3267f-119">What is "same origin"?</span></span>

<span data-ttu-id="3267f-120">如果它们具有相同方案、 主机和端口，两个 Url 将具有相同的原点。</span><span class="sxs-lookup"><span data-stu-id="3267f-120">Two URLs have the same origin if they have identical schemes, hosts, and ports.</span></span> <span data-ttu-id="3267f-121">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span><span class="sxs-lookup"><span data-stu-id="3267f-121">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span></span>

<span data-ttu-id="3267f-122">以下两个 Url 具有相同的原点：</span><span class="sxs-lookup"><span data-stu-id="3267f-122">These two URLs have the same origin:</span></span>

- `http://example.com/foo.html`
- `http://example.com/bar.html`

<span data-ttu-id="3267f-123">这些 Url 有两个相对上一不同的源：</span><span class="sxs-lookup"><span data-stu-id="3267f-123">These URLs have different origins than the previous two:</span></span>

- <span data-ttu-id="3267f-124">`http://example.net` -不同的域</span><span class="sxs-lookup"><span data-stu-id="3267f-124">`http://example.net` - Different domain</span></span>
- <span data-ttu-id="3267f-125">`http://example.com:9000/foo.html` -不同的端口</span><span class="sxs-lookup"><span data-stu-id="3267f-125">`http://example.com:9000/foo.html` - Different port</span></span>
- <span data-ttu-id="3267f-126">`https://example.com/foo.html` -不同的方案</span><span class="sxs-lookup"><span data-stu-id="3267f-126">`https://example.com/foo.html` - Different scheme</span></span>
- <span data-ttu-id="3267f-127">`http://www.example.com/foo.html` -不同的子域</span><span class="sxs-lookup"><span data-stu-id="3267f-127">`http://www.example.com/foo.html` - Different subdomain</span></span>

> [!NOTE]
> <span data-ttu-id="3267f-128">在比较源时，Internet Explorer 不考虑端口。</span><span class="sxs-lookup"><span data-stu-id="3267f-128">Internet Explorer does not consider the port when comparing origins.</span></span>

## <a name="create-the-webservice-project"></a><span data-ttu-id="3267f-129">创建 web 服务项目</span><span class="sxs-lookup"><span data-stu-id="3267f-129">Create the WebService project</span></span>

> [!NOTE]
> <span data-ttu-id="3267f-130">本部分假定您已经知道如何创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="3267f-130">This section assumes you already know how to create Web API projects.</span></span> <span data-ttu-id="3267f-131">如果没有，请参阅[Getting Started with ASP.NET Web API](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md)。</span><span class="sxs-lookup"><span data-stu-id="3267f-131">If not, see [Getting Started with ASP.NET Web API](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).</span></span>

1. <span data-ttu-id="3267f-132">启动 Visual Studio 并创建一个新**ASP.NET Web 应用程序 (.NET Framework)** 项目。</span><span class="sxs-lookup"><span data-stu-id="3267f-132">Start Visual Studio and create a new **ASP.NET Web Application (.NET Framework)** project.</span></span>
2. <span data-ttu-id="3267f-133">在中**新的 ASP.NET Web 应用程序**对话框中，选择**空**项目模板。</span><span class="sxs-lookup"><span data-stu-id="3267f-133">In the **New ASP.NET Web Application** dialog box, select the **Empty** project template.</span></span> <span data-ttu-id="3267f-134">下**添加文件夹和核心引用**，选择**Web API**复选框。</span><span class="sxs-lookup"><span data-stu-id="3267f-134">Under **Add folders and core references for**, select the **Web API** checkbox.</span></span>

   ![Visual Studio 中的新 ASP.NET 项目对话框](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog.png)

3. <span data-ttu-id="3267f-136">添加名为的 Web API 控制器`TestController`使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="3267f-136">Add a Web API controller named `TestController` with the following code:</span></span>

   [!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample1.cs)]

4. <span data-ttu-id="3267f-137">可以在本地运行应用程序或部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="3267f-137">You can run the application locally or deploy to Azure.</span></span> <span data-ttu-id="3267f-138">（对于本教程中的屏幕截图，该应用将部署到 Azure 应用服务 Web 应用。）若要验证的 web API 是否正常工作，请导航到`http://hostname/api/test/`，其中*主机名*是在其中部署应用程序的域。</span><span class="sxs-lookup"><span data-stu-id="3267f-138">(For the screenshots in this tutorial, the app deploys to Azure App Service Web Apps.) To verify that the web API is working, navigate to `http://hostname/api/test/`, where *hostname* is the domain where you deployed the application.</span></span> <span data-ttu-id="3267f-139">应看到响应文本：&quot;获取：测试消息&quot;。</span><span class="sxs-lookup"><span data-stu-id="3267f-139">You should see the response text, &quot;GET: Test Message&quot;.</span></span>

   ![Web 浏览器显示测试消息](enabling-cross-origin-requests-in-web-api/_static/image4.png)

## <a name="create-the-webclient-project"></a><span data-ttu-id="3267f-141">创建 WebClient 项目</span><span class="sxs-lookup"><span data-stu-id="3267f-141">Create the WebClient project</span></span>

1. <span data-ttu-id="3267f-142">创建另一个**ASP.NET Web 应用程序 (.NET Framework)** 项目，然后选择**MVC**项目模板。</span><span class="sxs-lookup"><span data-stu-id="3267f-142">Create another **ASP.NET Web Application (.NET Framework)** project and select the **MVC** project template.</span></span> <span data-ttu-id="3267f-143">（可选） 选择**更改身份验证** > **无身份验证**。</span><span class="sxs-lookup"><span data-stu-id="3267f-143">Optionally, select **Change Authentication** > **No Authentication**.</span></span> <span data-ttu-id="3267f-144">对于本教程中，不需要身份验证。</span><span class="sxs-lookup"><span data-stu-id="3267f-144">You don't need authentication for this tutorial.</span></span>

   ![在 Visual Studio 中新建 ASP.NET 项目对话框中的 MVC 模板](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog-mvc.png)

2. <span data-ttu-id="3267f-146">在中**解决方案资源管理器**，打开文件*Views/Home/Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="3267f-146">In **Solution Explorer**, open the file *Views/Home/Index.cshtml*.</span></span> <span data-ttu-id="3267f-147">此文件中的代码替换为以下：</span><span class="sxs-lookup"><span data-stu-id="3267f-147">Replace the code in this file with the following:</span></span>

   [!code-cshtml[Main](enabling-cross-origin-requests-in-web-api/samples/sample2.cshtml?highlight=13)]

   <span data-ttu-id="3267f-148">有关*serviceUrl*变量，请使用 web 服务应用程序的 URI。</span><span class="sxs-lookup"><span data-stu-id="3267f-148">For the *serviceUrl* variable, use the URI of the WebService app.</span></span>

3. <span data-ttu-id="3267f-149">在本地运行 WebClient 应用或将其发布到另一个网站。</span><span class="sxs-lookup"><span data-stu-id="3267f-149">Run the WebClient app locally or publish it to another website.</span></span>

<span data-ttu-id="3267f-150">当您单击"试用"按钮时，AJAX 请求提交到 web 服务应用程序使用中列出的 HTTP 方法 （GET、 POST 或 PUT） 的下拉列表框。</span><span class="sxs-lookup"><span data-stu-id="3267f-150">When you click the "Try It" button, an AJAX request is submitted to the WebService app using the HTTP method listed in the dropdown box (GET, POST, or PUT).</span></span> <span data-ttu-id="3267f-151">这样可以检查不同跨域请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-151">This lets you examine different cross-origin requests.</span></span> <span data-ttu-id="3267f-152">目前，web 服务应用程序不支持 CORS，因此如果单击该按钮将收到一个错误。</span><span class="sxs-lookup"><span data-stu-id="3267f-152">Currently, the WebService app does not support CORS, so if you click the button you'll get an error.</span></span>

![在浏览器中的 try 错误](enabling-cross-origin-requests-in-web-api/_static/image7.png)

> [!NOTE]
> <span data-ttu-id="3267f-154">如果监视工具中的 HTTP 流量喜欢[Fiddler](https://www.telerik.com/fiddler)，可以看到，在浏览器 does 发送 GET 请求，并请求成功，但 AJAX 调用将返回错误。</span><span class="sxs-lookup"><span data-stu-id="3267f-154">If you watch the HTTP traffic in a tool like [Fiddler](https://www.telerik.com/fiddler), you'll see that the browser does send the GET request, and the request succeeds, but the AJAX call returns an error.</span></span> <span data-ttu-id="3267f-155">务必了解同域策略不会阻止浏览器中的从*发送*请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-155">It's important to understand that same-origin policy does not prevent the browser from *sending* the request.</span></span> <span data-ttu-id="3267f-156">相反，它会阻止应用程序不能看到*响应*。</span><span class="sxs-lookup"><span data-stu-id="3267f-156">Instead, it prevents the application from seeing the *response*.</span></span>

![显示 web 请求的 fiddler web 调试器](enabling-cross-origin-requests-in-web-api/_static/image8.png)

## <a name="enable-cors"></a><span data-ttu-id="3267f-158">启用 CORS</span><span class="sxs-lookup"><span data-stu-id="3267f-158">Enable CORS</span></span>

<span data-ttu-id="3267f-159">现在让我们在 web 服务应用程序中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3267f-159">Now let's enable CORS in the WebService app.</span></span> <span data-ttu-id="3267f-160">首先，添加 CORS NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="3267f-160">First, add the CORS NuGet package.</span></span> <span data-ttu-id="3267f-161">在 Visual Studio 中，从**工具**菜单中，选择**NuGet 包管理器**，然后选择**程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="3267f-161">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="3267f-162">在包管理器控制台窗口中，键入以下命令：</span><span class="sxs-lookup"><span data-stu-id="3267f-162">In the Package Manager Console window, type the following command:</span></span>

[!code-powershell[Main](enabling-cross-origin-requests-in-web-api/samples/sample3.ps1)]

<span data-ttu-id="3267f-163">此命令安装最新的包和更新所有依赖项，其中包括核心 Web API 库。</span><span class="sxs-lookup"><span data-stu-id="3267f-163">This command installs the latest package and updates all dependencies, including the core Web API libraries.</span></span> <span data-ttu-id="3267f-164">使用`-Version`标志以面向特定版本。</span><span class="sxs-lookup"><span data-stu-id="3267f-164">Use the `-Version` flag to target a specific version.</span></span> <span data-ttu-id="3267f-165">CORS 包，需要 Web API 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3267f-165">The CORS package requires Web API 2.0 or later.</span></span>

<span data-ttu-id="3267f-166">打开文件*应用程序\_Start/WebApiConfig.cs*。</span><span class="sxs-lookup"><span data-stu-id="3267f-166">Open the file *App\_Start/WebApiConfig.cs*.</span></span> <span data-ttu-id="3267f-167">将以下代码添加到**WebApiConfig.Register**方法：</span><span class="sxs-lookup"><span data-stu-id="3267f-167">Add the following code to the **WebApiConfig.Register** method:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample4.cs?highlight=9)]

<span data-ttu-id="3267f-168">接下来，添加 **[EnableCors]** 属性为`TestController`类：</span><span class="sxs-lookup"><span data-stu-id="3267f-168">Next, add the **[EnableCors]** attribute to the `TestController` class:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample5.cs?highlight=3,7)]

<span data-ttu-id="3267f-169">有关*来源*参数，请使用 WebClient 应用程序的部署位置的 URI。</span><span class="sxs-lookup"><span data-stu-id="3267f-169">For the *origins* parameter, use the URI where you deployed the WebClient application.</span></span> <span data-ttu-id="3267f-170">这允许跨域请求从 WebClient，同时仍不允许其他所有跨域请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-170">This allows cross-origin requests from WebClient, while still disallowing all other cross-domain requests.</span></span> <span data-ttu-id="3267f-171">更高版本，我将介绍的参数 **[EnableCors]** 中更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="3267f-171">Later, I'll describe the parameters for **[EnableCors]** in more detail.</span></span>

<span data-ttu-id="3267f-172">不包括末尾的正斜杠*来源*URL。</span><span class="sxs-lookup"><span data-stu-id="3267f-172">Do not include a forward slash at the end of the *origins* URL.</span></span>

<span data-ttu-id="3267f-173">重新部署更新的 web 服务应用程序。</span><span class="sxs-lookup"><span data-stu-id="3267f-173">Redeploy the updated WebService application.</span></span> <span data-ttu-id="3267f-174">不需要更新 WebClient。</span><span class="sxs-lookup"><span data-stu-id="3267f-174">You don't need to update WebClient.</span></span> <span data-ttu-id="3267f-175">现在应该成功从 WebClient AJAX 请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-175">Now the AJAX request from WebClient should succeed.</span></span> <span data-ttu-id="3267f-176">所有允许使用 GET、 PUT 和 POST 方法。</span><span class="sxs-lookup"><span data-stu-id="3267f-176">The GET, PUT, and POST methods are all allowed.</span></span>

![Web 浏览器显示成功的测试消息](enabling-cross-origin-requests-in-web-api/_static/image9.png)

## <a name="how-cors-works"></a><span data-ttu-id="3267f-178">CORS 的工作原理</span><span class="sxs-lookup"><span data-stu-id="3267f-178">How CORS Works</span></span>

<span data-ttu-id="3267f-179">本部分介绍在 CORS 请求中，在 HTTP 消息的级别会发生什么情况。</span><span class="sxs-lookup"><span data-stu-id="3267f-179">This section describes what happens in a CORS request, at the level of the HTTP messages.</span></span> <span data-ttu-id="3267f-180">务必要了解 CORS 的工作原理，以便你可以配置 **[EnableCors]** 正确属性，正如您期望的功能不正常工作时进行故障排除。</span><span class="sxs-lookup"><span data-stu-id="3267f-180">It's important to understand how CORS works, so that you can configure the **[EnableCors]** attribute correctly and troubleshoot if things don't work as you expect.</span></span>

<span data-ttu-id="3267f-181">CORS 规范引入了几个新的 HTTP 标头启用跨域请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-181">The CORS specification introduces several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="3267f-182">如果浏览器支持 CORS，则将设置自动跨域请求; 这些标头您不必执行任何特殊的 JavaScript 代码中。</span><span class="sxs-lookup"><span data-stu-id="3267f-182">If a browser supports CORS, it sets these headers automatically for cross-origin requests; you don't need to do anything special in your JavaScript code.</span></span>

<span data-ttu-id="3267f-183">下面是跨域请求的示例。</span><span class="sxs-lookup"><span data-stu-id="3267f-183">Here is an example of a cross-origin request.</span></span> <span data-ttu-id="3267f-184">"源"标头提供发出请求的站点的域。</span><span class="sxs-lookup"><span data-stu-id="3267f-184">The "Origin" header gives the domain of the site that is making the request.</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample6.cmd?highlight=5)]

<span data-ttu-id="3267f-185">如果服务器允许该请求，则将设置访问控制的允许的域标头。</span><span class="sxs-lookup"><span data-stu-id="3267f-185">If the server allows the request, it sets the Access-Control-Allow-Origin header.</span></span> <span data-ttu-id="3267f-186">此标头的值匹配 Origin 标头，或为通配符值"\*"，允许任何源的含义。</span><span class="sxs-lookup"><span data-stu-id="3267f-186">The value of this header either matches the Origin header, or is the wildcard value "\*", meaning that any origin is allowed.</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample7.cmd?highlight=5)]

<span data-ttu-id="3267f-187">如果响应不包括访问控制的允许的域标头，AJAX 请求失败。</span><span class="sxs-lookup"><span data-stu-id="3267f-187">If the response does not include the Access-Control-Allow-Origin header, the AJAX request fails.</span></span> <span data-ttu-id="3267f-188">具体而言，在浏览器不允许该请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-188">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="3267f-189">即使服务器返回成功的响应，请在浏览器不会将响应提供给客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="3267f-189">Even if the server returns a successful response, the browser does not make the response available to the client application.</span></span>

<span data-ttu-id="3267f-190">**预检请求**</span><span class="sxs-lookup"><span data-stu-id="3267f-190">**Preflight Requests**</span></span>

<span data-ttu-id="3267f-191">对于某些 CORS 请求，浏览器发送额外的请求，名为"预检请求，"，再发送实际请求的资源。</span><span class="sxs-lookup"><span data-stu-id="3267f-191">For some CORS requests, the browser sends an additional request, called a "preflight request", before it sends the actual request for the resource.</span></span>

<span data-ttu-id="3267f-192">在浏览器可以跳过预检请求，如果满足以下条件：</span><span class="sxs-lookup"><span data-stu-id="3267f-192">The browser can skip the preflight request if the following conditions are true:</span></span>

- <span data-ttu-id="3267f-193">请求方法是 GET、 HEAD 或 POST，*和*</span><span class="sxs-lookup"><span data-stu-id="3267f-193">The request method is GET, HEAD, or POST, *and*</span></span>
- <span data-ttu-id="3267f-194">应用程序不会设置之外接受，接受语言的内容语言，任何请求标头内容类型或最后一个事件 ID、*和*</span><span class="sxs-lookup"><span data-stu-id="3267f-194">The application does not set any request headers other than Accept, Accept-Language, Content-Language, Content-Type, or Last-Event-ID, *and*</span></span>
- <span data-ttu-id="3267f-195">内容类型标头 (如果设置) 是以下之一：</span><span class="sxs-lookup"><span data-stu-id="3267f-195">The Content-Type header (if set) is one of the following:</span></span>

    - <span data-ttu-id="3267f-196">application/x-www-form-urlencoded</span><span class="sxs-lookup"><span data-stu-id="3267f-196">application/x-www-form-urlencoded</span></span>
    - <span data-ttu-id="3267f-197">multipart/form-data</span><span class="sxs-lookup"><span data-stu-id="3267f-197">multipart/form-data</span></span>
    - <span data-ttu-id="3267f-198">文本/无格式</span><span class="sxs-lookup"><span data-stu-id="3267f-198">text/plain</span></span>

<span data-ttu-id="3267f-199">有关请求标头规则适用于通过调用来设置应用程序的标头**setRequestHeader**上**XMLHttpRequest**对象。</span><span class="sxs-lookup"><span data-stu-id="3267f-199">The rule about request headers applies to headers that the application sets by calling **setRequestHeader** on the **XMLHttpRequest** object.</span></span> <span data-ttu-id="3267f-200">（CORS 规范调用这些"作者请求标头"。）此规则不适用于标头*浏览器*可以设置，例如用户代理、 主机或内容长度。</span><span class="sxs-lookup"><span data-stu-id="3267f-200">(The CORS specification calls these "author request headers".) The rule does not apply to headers the *browser* can set, such as User-Agent, Host, or Content-Length.</span></span>

<span data-ttu-id="3267f-201">下面是预检请求的示例：</span><span class="sxs-lookup"><span data-stu-id="3267f-201">Here is an example of a preflight request:</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample8.cmd?highlight=4-5)]

<span data-ttu-id="3267f-202">预检请求使用 HTTP OPTIONS 方法。</span><span class="sxs-lookup"><span data-stu-id="3267f-202">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="3267f-203">它包括两个特殊的标头：</span><span class="sxs-lookup"><span data-stu-id="3267f-203">It includes two special headers:</span></span>

- <span data-ttu-id="3267f-204">访问控制的请求的方法：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="3267f-204">Access-Control-Request-Method: The HTTP method that will be used for the actual request.</span></span>
- <span data-ttu-id="3267f-205">访问的控件的请求的标头：请求标头的列表，*应用程序*实际请求上设置。</span><span class="sxs-lookup"><span data-stu-id="3267f-205">Access-Control-Request-Headers: A list of request headers that the *application* set on the actual request.</span></span> <span data-ttu-id="3267f-206">（同样，这不包括在浏览器设置的标头。）</span><span class="sxs-lookup"><span data-stu-id="3267f-206">(Again, this does not include headers that the browser sets.)</span></span>

<span data-ttu-id="3267f-207">下面是示例响应中，假定服务器允许请求：</span><span class="sxs-lookup"><span data-stu-id="3267f-207">Here is an example response, assuming that the server allows the request:</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample9.cmd?highlight=6-7)]

<span data-ttu-id="3267f-208">响应包括访问的控制的允许的方法标头，其中列出了允许的方法，和 （可选） 访问的控制的允许的标头标头，其中列出了允许的标头。</span><span class="sxs-lookup"><span data-stu-id="3267f-208">The response includes an Access-Control-Allow-Methods header that lists the allowed methods, and optionally an Access-Control-Allow-Headers header, which lists the allowed headers.</span></span> <span data-ttu-id="3267f-209">如果预检请求成功，则浏览器发送实际请求，如前面所述。</span><span class="sxs-lookup"><span data-stu-id="3267f-209">If the preflight request succeeds, the browser sends the actual request, as described earlier.</span></span>

<span data-ttu-id="3267f-210">常用于测试具有预检 OPTIONS 请求的终结点的工具 (例如， [Fiddler](https://www.telerik.com/fiddler)并[Postman](https://www.getpostman.com/)) 默认情况下不发送所需的选项标头。</span><span class="sxs-lookup"><span data-stu-id="3267f-210">Tools commonly used to test endpoints with preflight OPTIONS requests (for example, [Fiddler](https://www.telerik.com/fiddler) and [Postman](https://www.getpostman.com/)) don't send the required OPTIONS headers by default.</span></span> <span data-ttu-id="3267f-211">确认`Access-Control-Request-Method`和`Access-Control-Request-Headers`与请求一起发送标头和选项标头打开通过 IIS 应用程序。</span><span class="sxs-lookup"><span data-stu-id="3267f-211">Confirm that the `Access-Control-Request-Method` and `Access-Control-Request-Headers` headers are sent with the request and that OPTIONS headers reach the app through IIS.</span></span>

<span data-ttu-id="3267f-212">若要配置 IIS 以允许接收和处理选项请求的 ASP.NET 应用程序，请将以下配置添加到应用程序的*web.config*文件中`<system.webServer><handlers>`部分：</span><span class="sxs-lookup"><span data-stu-id="3267f-212">To configure IIS to allow an ASP.NET app to receive and handle OPTION requests, add the following configuration to the app's *web.config* file in the `<system.webServer><handlers>` section:</span></span>

```xml
<system.webServer>
  <handlers>
    <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
    <remove name="OPTIONSVerbHandler" />
    <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="*" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
  </handlers>
</system.webServer>
```

<span data-ttu-id="3267f-213">删除`OPTIONSVerbHandler`可以防止 IIS 处理选项的请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-213">The removal of `OPTIONSVerbHandler` prevents IIS from handling OPTIONS requests.</span></span> <span data-ttu-id="3267f-214">替换为`ExtensionlessUrlHandler-Integrated-4.0`允许选项的请求来访问应用程序，因为默认模块注册仅允许 GET、 HEAD、 POST 和调试无扩展名 Url 请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-214">The replacement of `ExtensionlessUrlHandler-Integrated-4.0` allows OPTIONS requests to reach the app because the default module registration only allows GET, HEAD, POST, and DEBUG requests with extensionless URLs.</span></span>

## <a name="scope-rules-for-enablecors"></a><span data-ttu-id="3267f-215">[EnableCors] 的范围规则</span><span class="sxs-lookup"><span data-stu-id="3267f-215">Scope Rules for [EnableCors]</span></span>

<span data-ttu-id="3267f-216">可以在应用程序中每个操作，每个控制器，或全局范围内的所有 Web API 控制器启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3267f-216">You can enable CORS per action, per controller, or globally for all Web API controllers in your application.</span></span>

<span data-ttu-id="3267f-217">**每个操作**</span><span class="sxs-lookup"><span data-stu-id="3267f-217">**Per Action**</span></span>

<span data-ttu-id="3267f-218">若要为单个操作中启用 CORS，请设置 **[EnableCors]** 操作方法上的属性。</span><span class="sxs-lookup"><span data-stu-id="3267f-218">To enable CORS for a single action, set the **[EnableCors]** attribute on the action method.</span></span> <span data-ttu-id="3267f-219">下面的示例启用 CORS 的`GetItem`仅限方法。</span><span class="sxs-lookup"><span data-stu-id="3267f-219">The following example enables CORS for the `GetItem` method only.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample10.cs)]

<span data-ttu-id="3267f-220">**每个控制器**</span><span class="sxs-lookup"><span data-stu-id="3267f-220">**Per Controller**</span></span>

<span data-ttu-id="3267f-221">如果您设置 **[EnableCors]** 控制器类，它适用于在控制器上的所有操作。</span><span class="sxs-lookup"><span data-stu-id="3267f-221">If you set **[EnableCors]** on the controller class, it applies to all the actions on the controller.</span></span> <span data-ttu-id="3267f-222">若要操作禁用 CORS，请添加 **[DisableCors]** 属性为该操作。</span><span class="sxs-lookup"><span data-stu-id="3267f-222">To disable CORS for an action, add the **[DisableCors]** attribute to the action.</span></span> <span data-ttu-id="3267f-223">下面的示例为每个方法，但启用 CORS `PutItem`。</span><span class="sxs-lookup"><span data-stu-id="3267f-223">The following example enables CORS for every method except `PutItem`.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample11.cs)]

<span data-ttu-id="3267f-224">**全局**</span><span class="sxs-lookup"><span data-stu-id="3267f-224">**Globally**</span></span>

<span data-ttu-id="3267f-225">若要为应用程序中的所有 Web API 控制器中启用 CORS，请将传递**EnableCorsAttribute**实例向**EnableCors**方法：</span><span class="sxs-lookup"><span data-stu-id="3267f-225">To enable CORS for all Web API controllers in your application, pass an **EnableCorsAttribute** instance to the **EnableCors** method:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample12.cs)]

<span data-ttu-id="3267f-226">如果在多个作用域设置属性，优先顺序是：</span><span class="sxs-lookup"><span data-stu-id="3267f-226">If you set the attribute at more than one scope, the order of precedence is:</span></span>

1. <span data-ttu-id="3267f-227">操作</span><span class="sxs-lookup"><span data-stu-id="3267f-227">Action</span></span>
2. <span data-ttu-id="3267f-228">控制器</span><span class="sxs-lookup"><span data-stu-id="3267f-228">Controller</span></span>
3. <span data-ttu-id="3267f-229">Global</span><span class="sxs-lookup"><span data-stu-id="3267f-229">Global</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="3267f-230">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="3267f-230">Set the allowed origins</span></span>

<span data-ttu-id="3267f-231">*来源*的参数 **[EnableCors]** 属性指定允许哪些域访问资源。</span><span class="sxs-lookup"><span data-stu-id="3267f-231">The *origins* parameter of the **[EnableCors]** attribute specifies which origins are allowed to access the resource.</span></span> <span data-ttu-id="3267f-232">值为逗号分隔的允许的来源列表。</span><span class="sxs-lookup"><span data-stu-id="3267f-232">The value is a comma-separated list of the allowed origins.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample13.cs)]

<span data-ttu-id="3267f-233">此外可以使用通配符值"\*"若要允许来自任何来源的请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-233">You can also use the wildcard value "\*" to allow requests from any origins.</span></span>

<span data-ttu-id="3267f-234">允许任何来源的请求之前，请仔细考虑。</span><span class="sxs-lookup"><span data-stu-id="3267f-234">Consider carefully before allowing requests from any origin.</span></span> <span data-ttu-id="3267f-235">这意味着几乎任何网站可以向你的 web API 的 AJAX 调用。</span><span class="sxs-lookup"><span data-stu-id="3267f-235">It means that literally any website can make AJAX calls to your web API.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample14.cs)]

## <a name="set-the-allowed-http-methods"></a><span data-ttu-id="3267f-236">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="3267f-236">Set the allowed HTTP methods</span></span>

<span data-ttu-id="3267f-237">*方法*的参数 **[EnableCors]** 属性指定的 HTTP 方法允许访问资源。</span><span class="sxs-lookup"><span data-stu-id="3267f-237">The *methods* parameter of the **[EnableCors]** attribute specifies which HTTP methods are allowed to access the resource.</span></span> <span data-ttu-id="3267f-238">若要允许所有方法，使用通配符值"\*"。</span><span class="sxs-lookup"><span data-stu-id="3267f-238">To allow all methods, use the wildcard value "\*".</span></span> <span data-ttu-id="3267f-239">以下示例允许只有 GET 和 POST 的请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-239">The following example allows only GET and POST requests.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample15.cs)]

## <a name="set-the-allowed-request-headers"></a><span data-ttu-id="3267f-240">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="3267f-240">Set the allowed request headers</span></span>

<span data-ttu-id="3267f-241">本文介绍了如何前面预检请求可能包括一个访问控制的请求标头标头，列出应用程序设置的 HTTP 标头 (所谓"author 请求标头")。</span><span class="sxs-lookup"><span data-stu-id="3267f-241">This article described earlier how a preflight request might include an Access-Control-Request-Headers header, listing the HTTP headers set by the application (the so-called "author request headers").</span></span> <span data-ttu-id="3267f-242">*标头*的参数 **[EnableCors]** 属性指定允许哪些作者请求标头。</span><span class="sxs-lookup"><span data-stu-id="3267f-242">The *headers* parameter of the **[EnableCors]** attribute specifies which author request headers are allowed.</span></span> <span data-ttu-id="3267f-243">若要允许任何标头，设置*标头*到"\*"。</span><span class="sxs-lookup"><span data-stu-id="3267f-243">To allow any headers, set *headers* to "\*".</span></span> <span data-ttu-id="3267f-244">到允许列表特定的标头，设置*标头*以逗号分隔列表的允许的标头：</span><span class="sxs-lookup"><span data-stu-id="3267f-244">To whitelist specific headers, set *headers* to a comma-separated list of the allowed headers:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample16.cs)]

<span data-ttu-id="3267f-245">但是，浏览器不是它们如何设置访问控制的请求标头中完全一致的。</span><span class="sxs-lookup"><span data-stu-id="3267f-245">However, browsers are not entirely consistent in how they set Access-Control-Request-Headers.</span></span> <span data-ttu-id="3267f-246">例如，Chrome 当前包括"源"。</span><span class="sxs-lookup"><span data-stu-id="3267f-246">For example, Chrome currently includes "origin".</span></span> <span data-ttu-id="3267f-247">FireFox 不包括标准标头，如"接受"，即使应用程序将它们设置在脚本中。</span><span class="sxs-lookup"><span data-stu-id="3267f-247">FireFox does not include standard headers such as "Accept", even when the application sets them in script.</span></span>

<span data-ttu-id="3267f-248">如果您设置*标头*以外的其他任何内容"\*"，您至少应包含"接受"，"内容类型"，，和"源"，以及你想要支持任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="3267f-248">If you set *headers* to anything other than "\*", you should include at least "accept", "content-type", and "origin", plus any custom headers that you want to support.</span></span>

## <a name="set-the-allowed-response-headers"></a><span data-ttu-id="3267f-249">设置允许的响应标头</span><span class="sxs-lookup"><span data-stu-id="3267f-249">Set the allowed response headers</span></span>

<span data-ttu-id="3267f-250">默认情况下，在浏览器不公开所有向应用程序的响应标头。</span><span class="sxs-lookup"><span data-stu-id="3267f-250">By default, the browser does not expose all of the response headers to the application.</span></span> <span data-ttu-id="3267f-251">默认情况下可用的响应标头是：</span><span class="sxs-lookup"><span data-stu-id="3267f-251">The response headers that are available by default are:</span></span>

- <span data-ttu-id="3267f-252">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="3267f-252">Cache-Control</span></span>
- <span data-ttu-id="3267f-253">内容语言</span><span class="sxs-lookup"><span data-stu-id="3267f-253">Content-Language</span></span>
- <span data-ttu-id="3267f-254">Content-Type</span><span class="sxs-lookup"><span data-stu-id="3267f-254">Content-Type</span></span>
- <span data-ttu-id="3267f-255">过期</span><span class="sxs-lookup"><span data-stu-id="3267f-255">Expires</span></span>
- <span data-ttu-id="3267f-256">Last-Modified</span><span class="sxs-lookup"><span data-stu-id="3267f-256">Last-Modified</span></span>
- <span data-ttu-id="3267f-257">杂注</span><span class="sxs-lookup"><span data-stu-id="3267f-257">Pragma</span></span>

<span data-ttu-id="3267f-258">CORS 规范调用这些[简单响应标头](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="3267f-258">The CORS spec calls these [simple response headers](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header).</span></span> <span data-ttu-id="3267f-259">若要使其他标头可用于应用程序，设置*exposedHeaders*的参数 **[EnableCors]**。</span><span class="sxs-lookup"><span data-stu-id="3267f-259">To make other headers available to the application, set the *exposedHeaders* parameter of **[EnableCors]**.</span></span>

<span data-ttu-id="3267f-260">在以下示例中，控制器的`Get`方法设置名为 X-自定义的标头的自定义标头。</span><span class="sxs-lookup"><span data-stu-id="3267f-260">In the following example, the controller's `Get` method sets a custom header named ‘X-Custom-Header'.</span></span> <span data-ttu-id="3267f-261">默认情况下，在浏览器将公开此标头中的跨域请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-261">By default, the browser will not expose this header in a cross-origin request.</span></span> <span data-ttu-id="3267f-262">若要使标头，包括 X-自定义的标头中*exposedHeaders*。</span><span class="sxs-lookup"><span data-stu-id="3267f-262">To make the header available, include ‘X-Custom-Header' in *exposedHeaders*.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample17.cs)]

## <a name="pass-credentials-in-cross-origin-requests"></a><span data-ttu-id="3267f-263">在跨域请求中传递凭据</span><span class="sxs-lookup"><span data-stu-id="3267f-263">Pass credentials in cross-origin requests</span></span>

<span data-ttu-id="3267f-264">凭据要求特殊处理 CORS 请求中。</span><span class="sxs-lookup"><span data-stu-id="3267f-264">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="3267f-265">默认情况下，在浏览器不发送任何凭据与跨域请求。</span><span class="sxs-lookup"><span data-stu-id="3267f-265">By default, the browser does not send any credentials with a cross-origin request.</span></span> <span data-ttu-id="3267f-266">凭据包含 cookie，以及 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="3267f-266">Credentials include cookies as well as HTTP authentication schemes.</span></span> <span data-ttu-id="3267f-267">若要将发送具有跨源请求的凭据，客户端必须设置**XMLHttpRequest.withCredentials**为 true。</span><span class="sxs-lookup"><span data-stu-id="3267f-267">To send credentials with a cross-origin request, the client must set **XMLHttpRequest.withCredentials** to true.</span></span>

<span data-ttu-id="3267f-268">使用**XMLHttpRequest**直接：</span><span class="sxs-lookup"><span data-stu-id="3267f-268">Using **XMLHttpRequest** directly:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample18.cs)]

<span data-ttu-id="3267f-269">在 jQuery 中：</span><span class="sxs-lookup"><span data-stu-id="3267f-269">In jQuery:</span></span>

[!code-javascript[Main](enabling-cross-origin-requests-in-web-api/samples/sample19.js)]

<span data-ttu-id="3267f-270">此外，服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="3267f-270">In addition, the server must allow the credentials.</span></span> <span data-ttu-id="3267f-271">若要允许 Web API 中的跨域凭据，请设置**SupportsCredentials**属性设置为 true **[EnableCors]** 属性：</span><span class="sxs-lookup"><span data-stu-id="3267f-271">To allow cross-origin credentials in Web API, set the **SupportsCredentials** property to true on the **[EnableCors]** attribute:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample20.cs)]

<span data-ttu-id="3267f-272">如果此属性为 true，HTTP 响应将包括一个访问控制的允许的凭据标头。</span><span class="sxs-lookup"><span data-stu-id="3267f-272">If this property is true, the HTTP response will include an Access-Control-Allow-Credentials header.</span></span> <span data-ttu-id="3267f-273">此标头告知浏览器的服务器允许跨域请求凭据。</span><span class="sxs-lookup"><span data-stu-id="3267f-273">This header tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="3267f-274">如果浏览器发送的凭据，但响应不包含有效的访问控制的允许的凭据标头，在浏览器并不会对该应用程序的响应和 AJAX 请求失败。</span><span class="sxs-lookup"><span data-stu-id="3267f-274">If the browser sends credentials, but the response does not include a valid Access-Control-Allow-Credentials header, the browser will not expose the response to the application, and the AJAX request fails.</span></span>

<span data-ttu-id="3267f-275">请谨慎设置**SupportsCredentials**为 true，因为这意味着在另一个域的网站可以不在用户不知情的情况下对用户的名义，Web API 发送登录的用户的凭据。</span><span class="sxs-lookup"><span data-stu-id="3267f-275">Be careful about setting **SupportsCredentials** to true, because it means a website at another domain can send a logged-in user's credentials to your Web API on the user's behalf, without the user being aware.</span></span> <span data-ttu-id="3267f-276">CORS 规范还会说明该设置*来源*到&quot; \* &quot;无效如果**SupportsCredentials**为 true。</span><span class="sxs-lookup"><span data-stu-id="3267f-276">The CORS spec also states that setting *origins* to &quot;\*&quot; is invalid if **SupportsCredentials** is true.</span></span>

## <a name="custom-cors-policy-providers"></a><span data-ttu-id="3267f-277">自定义 CORS 策略提供程序</span><span class="sxs-lookup"><span data-stu-id="3267f-277">Custom CORS policy providers</span></span>

<span data-ttu-id="3267f-278">**[EnableCors]** 特性实现**ICorsPolicyProvider**接口。</span><span class="sxs-lookup"><span data-stu-id="3267f-278">The **[EnableCors]** attribute implements the **ICorsPolicyProvider** interface.</span></span> <span data-ttu-id="3267f-279">您可以通过创建派生的类提供您自己的实现**特性**并实现**ICorsProlicyProvider**。</span><span class="sxs-lookup"><span data-stu-id="3267f-279">You can provide your own implementation by creating a class that derives from **Attribute** and implements **ICorsProlicyProvider**.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample21.cs)]

<span data-ttu-id="3267f-280">现在可以应用任何位置，可使该属性 **[EnableCors]**。</span><span class="sxs-lookup"><span data-stu-id="3267f-280">Now you can apply the attribute any place that you would put **[EnableCors]**.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample22.cs)]

<span data-ttu-id="3267f-281">例如，自定义 CORS 策略提供程序无法从配置文件读取设置。</span><span class="sxs-lookup"><span data-stu-id="3267f-281">For example, a custom CORS policy provider could read the settings from a configuration file.</span></span>

<span data-ttu-id="3267f-282">作为使用属性的替代方法，可以注册**ICorsPolicyProviderFactory**创建的对象**ICorsPolicyProvider**对象。</span><span class="sxs-lookup"><span data-stu-id="3267f-282">As an alternative to using attributes, you can register an **ICorsPolicyProviderFactory** object that creates **ICorsPolicyProvider** objects.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample23.cs)]

<span data-ttu-id="3267f-283">若要设置**ICorsPolicyProviderFactory**，调用**SetCorsPolicyProviderFactory**扩展方法在启动时，按如下所示：</span><span class="sxs-lookup"><span data-stu-id="3267f-283">To set the **ICorsPolicyProviderFactory**, call the **SetCorsPolicyProviderFactory** extension method at startup, as follows:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample24.cs)]

## <a name="browser-support"></a><span data-ttu-id="3267f-284">浏览器支持</span><span class="sxs-lookup"><span data-stu-id="3267f-284">Browser support</span></span>

<span data-ttu-id="3267f-285">Web API CORS 包是一种服务器端技术。</span><span class="sxs-lookup"><span data-stu-id="3267f-285">The Web API CORS package is a server-side technology.</span></span> <span data-ttu-id="3267f-286">用户的浏览器还需要支持 CORS。</span><span class="sxs-lookup"><span data-stu-id="3267f-286">The user's browser also needs to support CORS.</span></span> <span data-ttu-id="3267f-287">幸运的是，所有主要浏览器的当前版本包括[CORS 的支持](http://caniuse.com/cors)。</span><span class="sxs-lookup"><span data-stu-id="3267f-287">Fortunately, the current versions of all major browsers include [support for CORS](http://caniuse.com/cors).</span></span>
