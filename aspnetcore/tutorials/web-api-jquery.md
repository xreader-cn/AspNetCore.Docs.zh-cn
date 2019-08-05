---
title: 教程：使用 jQuery 调用 ASP.NET Core web API
author: rick-anderson
description: 了解如何使用 jQuery 调用 ASP.NET Core web API。
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: eb8b2453fd037170a49f531fea4c3ef1c056292d
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602566"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a><span data-ttu-id="9fed4-103">教程：使用 jQuery 调用 ASP.NET Core web API</span><span class="sxs-lookup"><span data-stu-id="9fed4-103">Tutorial: Call an ASP.NET Core web API with jQuery</span></span>

<span data-ttu-id="9fed4-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9fed4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9fed4-105">此教程介绍如何使用 jQuery 调用 ASP.NET Core web API</span><span class="sxs-lookup"><span data-stu-id="9fed4-105">This tutorial shows how to call an ASP.NET Core web API with jQuery</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9fed4-106">有关 ASP.NET Core 2.2，请参阅 2.2 版本的[使用 jQuery 调用 Web API](xref:tutorials/first-web-api#call-the-api-with-jquery)。</span><span class="sxs-lookup"><span data-stu-id="9fed4-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the Web API with jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="9fed4-107">系统必备</span><span class="sxs-lookup"><span data-stu-id="9fed4-107">Prerequisites</span></span>

<span data-ttu-id="9fed4-108">完成[教程：创建 web API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="9fed4-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="9fed4-109">使用 jQuery 调用 API</span><span class="sxs-lookup"><span data-stu-id="9fed4-109">Call the API with jQuery</span></span>

<span data-ttu-id="9fed4-110">在本部分中，添加了使用 jQuery 调用 Web API 的 HTML 页面。</span><span class="sxs-lookup"><span data-stu-id="9fed4-110">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="9fed4-111">jQuery 启动请求，并用 API 响应中的详细信息更新页面。</span><span class="sxs-lookup"><span data-stu-id="9fed4-111">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="9fed4-112">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[实现默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)  ：</span><span class="sxs-lookup"><span data-stu-id="9fed4-112">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

<span data-ttu-id="9fed4-113">在项目目录中创建 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="9fed4-113">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="9fed4-114">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="9fed4-114">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="9fed4-115">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="9fed4-115">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

<span data-ttu-id="9fed4-116">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="9fed4-116">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="9fed4-117">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="9fed4-117">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="9fed4-118">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="9fed4-118">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="9fed4-119">打开 Properties\launchSettings.json  。</span><span class="sxs-lookup"><span data-stu-id="9fed4-119">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="9fed4-120">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用  。</span><span class="sxs-lookup"><span data-stu-id="9fed4-120">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="9fed4-121">有多种方式可以获取 jQuery。</span><span class="sxs-lookup"><span data-stu-id="9fed4-121">There are several ways to get jQuery.</span></span> <span data-ttu-id="9fed4-122">在前面的代码片段中，库是从 CDN 中加载的。</span><span class="sxs-lookup"><span data-stu-id="9fed4-122">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="9fed4-123">此示例调用 API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="9fed4-123">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="9fed4-124">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="9fed4-124">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="9fed4-125">获取待办事项的列表</span><span class="sxs-lookup"><span data-stu-id="9fed4-125">Get a list of to-do items</span></span>

<span data-ttu-id="9fed4-126">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 函数将 `GET` 请求发送至 API，这将返回表示待办事项数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="9fed4-126">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="9fed4-127">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="9fed4-127">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="9fed4-128">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="9fed4-128">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="9fed4-129">添加待办事项</span><span class="sxs-lookup"><span data-stu-id="9fed4-129">Add a to-do item</span></span>

<span data-ttu-id="9fed4-130">[Ajax](https://api.jquery.com/jquery.ajax/) 函数发送 `POST`，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="9fed4-130">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="9fed4-131">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="9fed4-131">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="9fed4-132">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="9fed4-132">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="9fed4-133">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="9fed4-133">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="9fed4-134">更新待办事项</span><span class="sxs-lookup"><span data-stu-id="9fed4-134">Update a to-do item</span></span>

<span data-ttu-id="9fed4-135">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="9fed4-135">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="9fed4-136">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="9fed4-136">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="9fed4-137">删除待办事项</span><span class="sxs-lookup"><span data-stu-id="9fed4-137">Delete a to-do item</span></span>

<span data-ttu-id="9fed4-138">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="9fed4-138">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

<span data-ttu-id="9fed4-139">前进到下一个教程，了解如何生成 API 帮助页：</span><span class="sxs-lookup"><span data-stu-id="9fed4-139">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end