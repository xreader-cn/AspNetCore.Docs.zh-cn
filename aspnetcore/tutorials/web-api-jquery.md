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
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a>教程：使用 jQuery 调用 ASP.NET Core web API

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

此教程介绍如何使用 jQuery 调用 ASP.NET Core web API

::: moniker range="< aspnetcore-3.0"

有关 ASP.NET Core 2.2，请参阅 2.2 版本的[使用 jQuery 调用 Web API](xref:tutorials/first-web-api#call-the-api-with-jquery)。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>系统必备

* 完成[教程：创建 web API](xref:tutorials/first-web-api)
* 熟悉 CSS、HTML、JavaScript 和 jQuery

## <a name="call-the-api-with-jquery"></a>使用 jQuery 调用 API

在本部分中，添加了使用 jQuery 调用 Web API 的 HTML 页面。 jQuery 启动请求，并用 API 响应中的详细信息更新页面。

通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[实现默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)  ：

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

在项目目录中创建 wwwroot  文件夹。

将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录   。 用以下标记替代其内容：

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录   。 用以下代码替代其内容：

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：

* 打开 Properties\launchSettings.json  。
* 删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用  。

有多种方式可以获取 jQuery。 在前面的代码片段中，库是从 CDN 中加载的。

此示例调用 API 的所有 CRUD 方法。 以下是 API 调用的说明。

### <a name="get-a-list-of-to-do-items"></a>获取待办事项的列表

jQuery [ajax](https://api.jquery.com/jquery.ajax/) 函数将 `GET` 请求发送至 API，这将返回表示待办事项数组的 JSON。 如果请求成功，则调用 `success` 回调函数。 在该回调中使用待办事项信息更新 DOM。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>添加待办事项

[Ajax](https://api.jquery.com/jquery.ajax/) 函数发送 `POST`，请求正文中包含待办事项。 将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。 待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。 当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>更新待办事项

更新待办事项与添加类似。 `url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>删除待办事项

若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

前进到下一个教程，了解如何生成 API 帮助页：

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end
