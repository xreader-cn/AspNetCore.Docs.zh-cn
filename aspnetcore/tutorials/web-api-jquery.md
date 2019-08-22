---
title: 教程：使用 jQuery 调用 ASP.NET Core web API
author: rick-anderson
description: 了解如何使用 jQuery 调用 ASP.NET Core web API。
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: a319e4b4ce09e9b09afeaff065d5740276deb115
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022560"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a><span data-ttu-id="462da-103">教程：使用 jQuery 调用 ASP.NET Core web API</span><span class="sxs-lookup"><span data-stu-id="462da-103">Tutorial: Call an ASP.NET Core web API with jQuery</span></span>

<span data-ttu-id="462da-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="462da-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="462da-105">此教程介绍如何使用 jQuery 调用 ASP.NET Core web API</span><span class="sxs-lookup"><span data-stu-id="462da-105">This tutorial shows how to call an ASP.NET Core web API with jQuery</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="462da-106">有关 ASP.NET Core 2.2，请参阅 2.2 版本的[使用 jQuery 调用 Web API](xref:tutorials/first-web-api#call-the-api-with-jquery)。</span><span class="sxs-lookup"><span data-stu-id="462da-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the Web API with jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="462da-107">系统必备</span><span class="sxs-lookup"><span data-stu-id="462da-107">Prerequisites</span></span>

* <span data-ttu-id="462da-108">完成[教程：创建 web API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="462da-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="462da-109">熟悉 CSS、HTML、JavaScript 和 jQuery</span><span class="sxs-lookup"><span data-stu-id="462da-109">Familiarity with CSS, HTML, JavaScript, and jQuery</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="462da-110">使用 jQuery 调用 API</span><span class="sxs-lookup"><span data-stu-id="462da-110">Call the API with jQuery</span></span>

<span data-ttu-id="462da-111">在本部分中，添加了使用 jQuery 调用 Web API 的 HTML 页面。</span><span class="sxs-lookup"><span data-stu-id="462da-111">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="462da-112">jQuery 启动请求，并用 API 响应中的详细信息更新页面。</span><span class="sxs-lookup"><span data-stu-id="462da-112">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="462da-113">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[实现默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)  ：</span><span class="sxs-lookup"><span data-stu-id="462da-113">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

<span data-ttu-id="462da-114">在项目目录中创建 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="462da-114">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="462da-115">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="462da-115">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="462da-116">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="462da-116">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

<span data-ttu-id="462da-117">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="462da-117">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="462da-118">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="462da-118">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="462da-119">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="462da-119">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="462da-120">打开 Properties\launchSettings.json  。</span><span class="sxs-lookup"><span data-stu-id="462da-120">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="462da-121">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用  。</span><span class="sxs-lookup"><span data-stu-id="462da-121">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="462da-122">有多种方式可以获取 jQuery。</span><span class="sxs-lookup"><span data-stu-id="462da-122">There are several ways to get jQuery.</span></span> <span data-ttu-id="462da-123">在前面的代码片段中，库是从 CDN 中加载的。</span><span class="sxs-lookup"><span data-stu-id="462da-123">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="462da-124">此示例调用 API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="462da-124">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="462da-125">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="462da-125">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="462da-126">获取待办事项的列表</span><span class="sxs-lookup"><span data-stu-id="462da-126">Get a list of to-do items</span></span>

<span data-ttu-id="462da-127">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 函数将 `GET` 请求发送至 API，这将返回表示待办事项数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="462da-127">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="462da-128">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="462da-128">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="462da-129">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="462da-129">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="462da-130">添加待办事项</span><span class="sxs-lookup"><span data-stu-id="462da-130">Add a to-do item</span></span>

<span data-ttu-id="462da-131">[Ajax](https://api.jquery.com/jquery.ajax/) 函数发送 `POST`，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="462da-131">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="462da-132">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="462da-132">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="462da-133">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="462da-133">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="462da-134">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="462da-134">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="462da-135">更新待办事项</span><span class="sxs-lookup"><span data-stu-id="462da-135">Update a to-do item</span></span>

<span data-ttu-id="462da-136">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="462da-136">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="462da-137">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="462da-137">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="462da-138">删除待办事项</span><span class="sxs-lookup"><span data-stu-id="462da-138">Delete a to-do item</span></span>

<span data-ttu-id="462da-139">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="462da-139">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

<span data-ttu-id="462da-140">前进到下一个教程，了解如何生成 API 帮助页：</span><span class="sxs-lookup"><span data-stu-id="462da-140">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end
