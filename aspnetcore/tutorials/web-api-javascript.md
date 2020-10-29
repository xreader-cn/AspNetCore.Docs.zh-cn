---
title: 教程：使用 JavaScript 调用 ASP.NET Core Web API
author: rick-anderson
description: 了解如何使用 JavaScript 调用 ASP.NET Core Web API。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/web-api-javascript
ms.openlocfilehash: b41288bd63267a9aa7035e25ebc8d838eed5d93b
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690688"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="21d86-103">教程：使用 JavaScript 调用 ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="21d86-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="21d86-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="21d86-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="21d86-105">此教程介绍如何通过 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 使用 JavaScript 调用 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="21d86-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="21d86-106">有关 ASP.NET Core 2.2，请参阅 2.2 版本的[使用 JavaScript 调用 Web API](xref:tutorials/first-web-api#call-the-web-api-with-javascript)。</span><span class="sxs-lookup"><span data-stu-id="21d86-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="21d86-107">先决条件</span><span class="sxs-lookup"><span data-stu-id="21d86-107">Prerequisites</span></span>

* <span data-ttu-id="21d86-108">完成[教程：创建 web API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="21d86-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="21d86-109">熟悉 CSS、HTML 和 JavaScript</span><span class="sxs-lookup"><span data-stu-id="21d86-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="21d86-110">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="21d86-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="21d86-111">在本部分中，将添加一个 HTML 页面，其中包含用于创建和管理待办事项的窗体。</span><span class="sxs-lookup"><span data-stu-id="21d86-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="21d86-112">事件处理程序会附加到页面上的元素。</span><span class="sxs-lookup"><span data-stu-id="21d86-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="21d86-113">事件处理程序导致对 Web API 的操作方法发出 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="21d86-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="21d86-114">Fetch API 的 `fetch` 函数可启动每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="21d86-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="21d86-115">`fetch` 函数可返回 [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) 对象，该对象包含表示为 `Response` 对象的 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="21d86-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="21d86-116">常见模式是通过调用 `Response` 对象上的 `json` 函数来提取 JSON 响应正文。</span><span class="sxs-lookup"><span data-stu-id="21d86-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="21d86-117">JavaScript 会使用 Web API 响应的详细信息来更新页面。</span><span class="sxs-lookup"><span data-stu-id="21d86-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="21d86-118">最简单的 `fetch` 调用接受一个表示路由的参数。</span><span class="sxs-lookup"><span data-stu-id="21d86-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="21d86-119">第二个参数（称为 `init` 对象）是可选的。</span><span class="sxs-lookup"><span data-stu-id="21d86-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="21d86-120">`init` 用于配置 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="21d86-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="21d86-121">配置应用[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[启用默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)。</span><span class="sxs-lookup"><span data-stu-id="21d86-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="21d86-122">在 Startup.cs 的 `Configure` 方法中需要以下突出显示的代码  ：</span><span class="sxs-lookup"><span data-stu-id="21d86-122">The following highlighted code is needed in the `Configure` method of *Startup.cs* :</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="21d86-123">在项目根中创建 wwwroot 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="21d86-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="21d86-124">在 wwwroot 文件夹中创建一个 js 文件夹   。</span><span class="sxs-lookup"><span data-stu-id="21d86-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="21d86-125">将一个名为 index.html 的 HTML 文件添加到 wwwroot 文件夹   。</span><span class="sxs-lookup"><span data-stu-id="21d86-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="21d86-126">将 index.html 的内容替换为以下标记  ：</span><span class="sxs-lookup"><span data-stu-id="21d86-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="21d86-127">将一个名为 site.js 的 JavaScript 文件添加到 wwwroot/js 文件夹   。</span><span class="sxs-lookup"><span data-stu-id="21d86-127">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="21d86-128">将 site.js 的内容替换为以下代码  ：</span><span class="sxs-lookup"><span data-stu-id="21d86-128">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="21d86-129">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="21d86-129">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="21d86-130">打开 Properties\launchSettings.json  。</span><span class="sxs-lookup"><span data-stu-id="21d86-130">Open *Properties\launchSettings.json* .</span></span>
1. <span data-ttu-id="21d86-131">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用  。</span><span class="sxs-lookup"><span data-stu-id="21d86-131">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="21d86-132">此示例调用 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="21d86-132">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="21d86-133">下面说明 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="21d86-133">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="21d86-134">获取待办事项的列表</span><span class="sxs-lookup"><span data-stu-id="21d86-134">Get a list of to-do items</span></span>

<span data-ttu-id="21d86-135">在以下代码中，会将 HTTP GET 请求发送到 api/TodoItems 路由  ：</span><span class="sxs-lookup"><span data-stu-id="21d86-135">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="21d86-136">当 Web API 返回成功状态的代码时，将调用 `_displayItems` 函数。</span><span class="sxs-lookup"><span data-stu-id="21d86-136">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="21d86-137">将 `_displayItems` 接受的数组参数中的每个待办事项添加到具有“编辑”和“删除”按钮的表中   。</span><span class="sxs-lookup"><span data-stu-id="21d86-137">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="21d86-138">如果 Web API 请求失败，则会将错误记录到浏览器的控制台中。</span><span class="sxs-lookup"><span data-stu-id="21d86-138">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="21d86-139">添加待办事项</span><span class="sxs-lookup"><span data-stu-id="21d86-139">Add a to-do item</span></span>

<span data-ttu-id="21d86-140">在以下代码中：</span><span class="sxs-lookup"><span data-stu-id="21d86-140">In the following code:</span></span>

* <span data-ttu-id="21d86-141">声明 `item` 变量来构造待办事项的对象文字表示形式。</span><span class="sxs-lookup"><span data-stu-id="21d86-141">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="21d86-142">使用以下选项来配置提取请求：</span><span class="sxs-lookup"><span data-stu-id="21d86-142">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="21d86-143">`method`&mdash;指定 POST HTTP 操作谓词。</span><span class="sxs-lookup"><span data-stu-id="21d86-143">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="21d86-144">`body`&mdash;指定请求正文的 JSON 表示形式。</span><span class="sxs-lookup"><span data-stu-id="21d86-144">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="21d86-145">通过将存储在 `item` 中的对象文字传递到 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 函数来生成 JSON。</span><span class="sxs-lookup"><span data-stu-id="21d86-145">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="21d86-146">`headers`&mdash;指定 `Accept` 和 `Content-Type` HTTP 请求标头。</span><span class="sxs-lookup"><span data-stu-id="21d86-146">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="21d86-147">将两个标头都设置为 `application/json`，以便分别指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="21d86-147">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="21d86-148">将 HTTP POST 请求发送到 api/TodoItems 路由  。</span><span class="sxs-lookup"><span data-stu-id="21d86-148">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="21d86-149">当 Web API 返回成功状态的代码时，将调用 `getItems` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="21d86-149">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="21d86-150">如果 Web API 请求失败，则会将错误记录到浏览器的控制台中。</span><span class="sxs-lookup"><span data-stu-id="21d86-150">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="21d86-151">更新待办事项</span><span class="sxs-lookup"><span data-stu-id="21d86-151">Update a to-do item</span></span>

<span data-ttu-id="21d86-152">更新待办事项类似于添加待办事项；但是，二者存在两个重大差异：</span><span class="sxs-lookup"><span data-stu-id="21d86-152">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="21d86-153">路由的后缀为待更新项的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="21d86-153">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="21d86-154">例如，api/TodoItems/1  。</span><span class="sxs-lookup"><span data-stu-id="21d86-154">For example, *api/TodoItems/1* .</span></span>
* <span data-ttu-id="21d86-155">正如 `method` 选项所示，HTTP 操作谓词是 PUT。</span><span class="sxs-lookup"><span data-stu-id="21d86-155">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="21d86-156">删除待办事项</span><span class="sxs-lookup"><span data-stu-id="21d86-156">Delete a to-do item</span></span>

<span data-ttu-id="21d86-157">若要删除待办事项，请将请求的 `method` 选项设置为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="21d86-157">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="21d86-158">前进到下一个教程，了解如何生成 Web API 帮助页：</span><span class="sxs-lookup"><span data-stu-id="21d86-158">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
