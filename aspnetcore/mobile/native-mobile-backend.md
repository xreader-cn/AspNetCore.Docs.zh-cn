---
title: 使用 ASP.NET Core 为本机移动应用创建后端服务
author: rick-anderson
description: 了解如何使用 ASP.NET Core MVC 创建后端服务，以支持本机移动应用。
ms.author: riande
ms.date: 2/12/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mobile/native-mobile-backend
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564023"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>使用 ASP.NET Core 为本机移动应用创建后端服务

按 [James Montemagno](https://twitter.com/JamesMontemagno)

移动应用可与 ASP.NET Core 后端服务通信。 有关从 iOS 模拟器和 Android 仿真程序连接本地 Web 服务的说明，请参阅[从 iOS 模拟器和 Android 仿真程序连接到本地 Web 服务](/xamarin/cross-platform/deploy-test/connect-to-local-web-services)。

[查看或下载后端服务代码示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a>本机移动应用示例

本教程演示如何使用 ASP.NET Core 创建后端服务以支持本机移动应用。 它使用 [Xamarin TodoRest 应用](/xamarin/xamarin-forms/data-cloud/consuming/rest) 作为其本机客户端，其中包括适用于 Android、IOS 和 Windows 的单独本机客户端。 你可以遵循链接中的教程来创建本机应用程序（并安装需要的免费 Xamarin 工具），以及下载 Xamarin 示例解决方案。 Xamarin 示例包含一个 ASP.NET Core 的 Web API 服务项目，本文的 ASP.NET Core 应用程序将替换 (，而不需要客户端) 所需的任何更改。

![在 Android 智能手机上运行的 ToDoRest 应用程序](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>功能

[TodoREST 应用](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)支持列出、添加、删除和更新 To-Do 项。 每个项都有一个 ID、 Name（名称）、Notes（说明）以及一个指示该项是否已完成的属性 Done。

待办事项的主视图如上所示，列出每个项的名称，并使用复选标记指示它是否已完成。

点击 `+` 图标打开“添加项”对话框：

![“添加项”对话框](native-mobile-backend/_static/todo-android-new-item.png)

点击主列表屏幕上的项将打开一个编辑对话框，在其中可以修改项的名称、 说明以及是否完成，或删除项目：

![“编辑项”对话框](native-mobile-backend/_static/todo-android-edit-item.png)

若要针对在计算机上运行的下一节中创建的 ASP.NET Core 应用对其进行测试，请更新应用的 [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) 常量。

Android 仿真程序不会在本地计算机上运行，而是使用环回 IP (10.0.2.2) 与本地计算机通信。 利用 [Xamarin DeviceInfo](/xamarin/essentials/device-information/) 检测运行系统的操作，以使用正确的 URL。

导航到 [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) 项目并打开 [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) 文件。 *Constants.cs* 文件包含以下配置。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

你可以选择将 web 服务部署到 Azure 等云服务，并更新 `RestUrl` 。

## <a name="creating-the-aspnet-core-project"></a>创建 ASP.NET Core 项目

在 Visual Studio 中创建一个新的 ASP.NET Core Web 应用程序。 选择 "Web API" 模板。 将项目命名为 *TodoAPI*。

![“新建 ASP.NET Web 应用程序”对话框，其中已选中 Web API 项目模板](native-mobile-backend/_static/web-api-template.png)

应用应该响应对端口5000发出的所有请求，包括我们的移动客户端的明文 http 流量。 更新 *Startup.cs* ，因此 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> 不会在开发中运行：

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> 直接运行应用，而不是在 IIS Express 后面运行。 默认情况下，IIS Express 忽略非本地请求。 在命令提示符下运行 [dotnet 运行](/dotnet/core/tools/dotnet-run) ，或从 Visual Studio 工具栏中的 "调试目标" 下拉列表中选择应用名称配置文件。

添加一个模型类来表示待办事项。 使用 `[Required]` 属性标记必需字段：

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

API 方法需要通过某种方式处理数据。 使用原始 Xamarin 示例所用的 `ITodoRepository` 接口：

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

在此示例中，该实现仅使用一个专用项集合：

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

在 *Startup.cs* 中配置该实现:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a>创建控制器

将新的控制器添加到项目 [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs)。 它应从继承 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 。 添加 `Route` 属性以指示控制器将处理路径以 `api/todoitems` 开始的请求。 路由中的 `[controller]` 标记会被控制器的名称代替（省略 `Controller` 后缀），这对全局路由特别有用。 详细了解 [路由](../fundamentals/routing.md)。

控制器需要 `ITodoRepository` 才能正常运行；通过控制器的构造函数请求该类型的实例。 在运行时，此实例将使用框架对 [依赖关系注入](../fundamentals/dependency-injection.md) 的支持来提供。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

此 API 支持四个不同的 HTTP 谓词来执行对数据源的 CRUD（创建、读取、更新、删除）操作。 最简单的是读取操作，它对应于 HTTP GET 请求。

### <a name="reading-items"></a>读取项目

要请求项列表，可对 `List` 方法使用 GET 请求。 `[HttpGet]` 方法的 `List` 属性指示此操作应仅处理 GET 请求。 此操作的路由是在控制器上指定的路由。 你不一定必须将操作名称用作路由的一部分。 你只需确保每个操作都有唯一的和明确的路由。 路由属性可以分别应用在控制器和方法级别，以此生成特定的路由。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

`List`方法返回 200 OK 响应代码和所有 Todo 项，序列化为 JSON。

你可以使用多种工具测试新的 API 方法，如 [Postman](https://www.getpostman.com/docs/)，如此处所示：

![Postman 控制台，其中显示一个 todoitem 的 GET 请求，以及显示所返回的三个项目的 JSON 的响应正文](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>创建项目

按照约定，创建新数据项映射到 HTTP POST 谓词。 `Create` 方法具有应用于它的 `[HttpPost]` 属性，并接受 `TodoItem` 实例。 由于 `item` 参数在 POST 的正文中传递，因此该参数会指定 `[FromBody]` 属性。

在该方法中，会检查项的有效性和之前是否存在于数据存储，并且如果没有任何问题，则使用存储库添加。 检查 `ModelState.IsValid` 将执行 [模型验证](../mvc/models/validation.md)，应该在每个接受用户输入的 API 方法中执行此步骤。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

该示例使用的 `enum` 包含错误代码将传递到移动客户端：

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

使用 Postman 测试添加新项，选择 POST 谓词并在请求正文中以 JSON 格式提供新对象。 你还应添加一个请求标头指定 `Content-Type` 为 `application/json`。

![显示 POST 和响应的 Postman 控制台](native-mobile-backend/_static/postman-post.png)

该方法返回在响应中新建的项。

### <a name="updating-items"></a>更新项目

通过使用 HTTP PUT 请求来修改记录。 除了此更改之外，`Edit` 方法几乎与 `Create` 完全相同。 请注意，如果未找到记录，则 `Edit` 操作将返回 `NotFound`(404) 响应。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

若要使用 Postman 进行测试，将谓词更改为 PUT。 在请求正文中指定要更新的对象数据。

![显示 PUT 和响应的 Postman 控制台](native-mobile-backend/_static/postman-put.png)

为了与预先存在的 API 保持一致，此方法在成功时返回 `NoContent` (204) 响应。

### <a name="deleting-items"></a>删除项

删除记录可以通过向服务发出 DELETE 请求并传递要删除项的 ID 来完成。 与更新一样，请求的项不存在时会收到 `NotFound` 响应。 请求成功会得到 `NoContent` (204) 响应。

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

请注意，在测试删除功能时，请求正文中不需要任何内容。

![显示 DELETE 和响应的 Postman 控制台](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a>防止过度发布

目前，示例应用公开了整个 `TodoItem` 对象。 生产应用通常使用模型的子集来限制输入和返回的数据。 这背后有多种原因，但安全性是主要原因。 模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。 本文使用的是 **DTO**。

DTO 可用于：

* 防止过度发布。
* 隐藏客户端不应查看的属性。
* 省略一些属性以缩减有效负载大小。
* 平展包含嵌套对象的对象图。 对客户端而言，平展的对象图可能更方便。

若要演示 DTO 方法，请参阅 [阻止过账](xref:tutorials/first-web-api#prevent-over-posting)

## <a name="common-web-api-conventions"></a>常见的 Web API 约定

开发应用程序的后端服务时，你将想要使用一组一致的约定或策略来处理横切关注点。 例如，在上面所示服务中，针对不存在的特定记录的请求会收到 `NotFound` 响应，而不是`BadRequest` 响应。 同样，对于此服务，传递模型绑定类型的命令始终检查 `ModelState.IsValid` 并为无效的模型类型返回 `BadRequest`。

一旦为 Api 指定通用策略，一般可以将其封装在 [Filter（筛选器）](../mvc/controllers/filters.md)。 详细了解 [如何封装 ASP.NET Core MVC 应用程序中的通用 API 策略](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters)。

## <a name="additional-resources"></a>其他资源

- [Xamarin： Web 服务身份验证](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin：使用 RESTful Web 服务](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn：在 Xamarin 应用中使用 REST web 服务](/learn/modules/consume-rest-services/)
- [Microsoft Learn：使用 ASP.NET Core 创建 Web API](/learn/modules/build-web-api-aspnet-core/)
