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
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="3e20f-103">使用 ASP.NET Core 为本机移动应用创建后端服务</span><span class="sxs-lookup"><span data-stu-id="3e20f-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="3e20f-104">按 [James Montemagno](https://twitter.com/JamesMontemagno)</span><span class="sxs-lookup"><span data-stu-id="3e20f-104">By [James Montemagno](https://twitter.com/JamesMontemagno)</span></span>

<span data-ttu-id="3e20f-105">移动应用可与 ASP.NET Core 后端服务通信。</span><span class="sxs-lookup"><span data-stu-id="3e20f-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="3e20f-106">有关从 iOS 模拟器和 Android 仿真程序连接本地 Web 服务的说明，请参阅[从 iOS 模拟器和 Android 仿真程序连接到本地 Web 服务](/xamarin/cross-platform/deploy-test/connect-to-local-web-services)。</span><span class="sxs-lookup"><span data-stu-id="3e20f-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="3e20f-107">查看或下载后端服务代码示例</span><span class="sxs-lookup"><span data-stu-id="3e20f-107">View or download sample backend services code</span></span>](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="3e20f-108">本机移动应用示例</span><span class="sxs-lookup"><span data-stu-id="3e20f-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="3e20f-109">本教程演示如何使用 ASP.NET Core 创建后端服务以支持本机移动应用。</span><span class="sxs-lookup"><span data-stu-id="3e20f-109">This tutorial demonstrates how to create backend services using ASP.NET Core to support native mobile apps.</span></span> <span data-ttu-id="3e20f-110">它使用 [Xamarin TodoRest 应用](/xamarin/xamarin-forms/data-cloud/consuming/rest) 作为其本机客户端，其中包括适用于 Android、IOS 和 Windows 的单独本机客户端。</span><span class="sxs-lookup"><span data-stu-id="3e20f-110">It uses the [Xamarin.Forms TodoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, and Windows.</span></span> <span data-ttu-id="3e20f-111">你可以遵循链接中的教程来创建本机应用程序（并安装需要的免费 Xamarin 工具），以及下载 Xamarin 示例解决方案。</span><span class="sxs-lookup"><span data-stu-id="3e20f-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="3e20f-112">Xamarin 示例包含一个 ASP.NET Core 的 Web API 服务项目，本文的 ASP.NET Core 应用程序将替换 (，而不需要客户端) 所需的任何更改。</span><span class="sxs-lookup"><span data-stu-id="3e20f-112">The Xamarin sample includes an ASP.NET Core Web API services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![在 Android 智能手机上运行的 ToDoRest 应用程序](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="3e20f-114">功能</span><span class="sxs-lookup"><span data-stu-id="3e20f-114">Features</span></span>

<span data-ttu-id="3e20f-115">[TodoREST 应用](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)支持列出、添加、删除和更新 To-Do 项。</span><span class="sxs-lookup"><span data-stu-id="3e20f-115">The [TodoREST app](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="3e20f-116">每个项都有一个 ID、 Name（名称）、Notes（说明）以及一个指示该项是否已完成的属性 Done。</span><span class="sxs-lookup"><span data-stu-id="3e20f-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="3e20f-117">待办事项的主视图如上所示，列出每个项的名称，并使用复选标记指示它是否已完成。</span><span class="sxs-lookup"><span data-stu-id="3e20f-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="3e20f-118">点击 `+` 图标打开“添加项”对话框：</span><span class="sxs-lookup"><span data-stu-id="3e20f-118">Tapping the `+` icon opens an add item dialog:</span></span>

![“添加项”对话框](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="3e20f-120">点击主列表屏幕上的项将打开一个编辑对话框，在其中可以修改项的名称、 说明以及是否完成，或删除项目：</span><span class="sxs-lookup"><span data-stu-id="3e20f-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![“编辑项”对话框](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="3e20f-122">若要针对在计算机上运行的下一节中创建的 ASP.NET Core 应用对其进行测试，请更新应用的 [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) 常量。</span><span class="sxs-lookup"><span data-stu-id="3e20f-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, update the app's [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) constant.</span></span>

<span data-ttu-id="3e20f-123">Android 仿真程序不会在本地计算机上运行，而是使用环回 IP (10.0.2.2) 与本地计算机通信。</span><span class="sxs-lookup"><span data-stu-id="3e20f-123">Android emulators do not run on the local machine and use a loopback IP (10.0.2.2) to communicate with the local machine.</span></span> <span data-ttu-id="3e20f-124">利用 [Xamarin DeviceInfo](/xamarin/essentials/device-information/) 检测运行系统的操作，以使用正确的 URL。</span><span class="sxs-lookup"><span data-stu-id="3e20f-124">Leverage [Xamarin.Essentials DeviceInfo](/xamarin/essentials/device-information/) to detect what operating the system is running to use the correct URL.</span></span>

<span data-ttu-id="3e20f-125">导航到 [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) 项目并打开 [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) 文件。</span><span class="sxs-lookup"><span data-stu-id="3e20f-125">Navigate to the [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) project and open the [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) file.</span></span> <span data-ttu-id="3e20f-126">*Constants.cs* 文件包含以下配置。</span><span class="sxs-lookup"><span data-stu-id="3e20f-126">The *Constants.cs* file contains the following configuration.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

<span data-ttu-id="3e20f-127">你可以选择将 web 服务部署到 Azure 等云服务，并更新 `RestUrl` 。</span><span class="sxs-lookup"><span data-stu-id="3e20f-127">You can optionally deploy the web service to a cloud service such as Azure and update the `RestUrl`.</span></span>

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="3e20f-128">创建 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="3e20f-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="3e20f-129">在 Visual Studio 中创建一个新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="3e20f-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="3e20f-130">选择 "Web API" 模板。</span><span class="sxs-lookup"><span data-stu-id="3e20f-130">Choose the Web API template.</span></span> <span data-ttu-id="3e20f-131">将项目命名为 *TodoAPI*。</span><span class="sxs-lookup"><span data-stu-id="3e20f-131">Name the project *TodoAPI*.</span></span>

![“新建 ASP.NET Web 应用程序”对话框，其中已选中 Web API 项目模板](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="3e20f-133">应用应该响应对端口5000发出的所有请求，包括我们的移动客户端的明文 http 流量。</span><span class="sxs-lookup"><span data-stu-id="3e20f-133">The app should respond to all requests made to port 5000 including clear-text http traffic for our mobile client.</span></span> <span data-ttu-id="3e20f-134">更新 *Startup.cs* ，因此 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> 不会在开发中运行：</span><span class="sxs-lookup"><span data-stu-id="3e20f-134">Update *Startup.cs* so <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> doesn't run in development:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> <span data-ttu-id="3e20f-135">直接运行应用，而不是在 IIS Express 后面运行。</span><span class="sxs-lookup"><span data-stu-id="3e20f-135">Run the app directly, rather than behind IIS Express.</span></span> <span data-ttu-id="3e20f-136">默认情况下，IIS Express 忽略非本地请求。</span><span class="sxs-lookup"><span data-stu-id="3e20f-136">IIS Express ignores non-local requests by default.</span></span> <span data-ttu-id="3e20f-137">在命令提示符下运行 [dotnet 运行](/dotnet/core/tools/dotnet-run) ，或从 Visual Studio 工具栏中的 "调试目标" 下拉列表中选择应用名称配置文件。</span><span class="sxs-lookup"><span data-stu-id="3e20f-137">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the app name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="3e20f-138">添加一个模型类来表示待办事项。</span><span class="sxs-lookup"><span data-stu-id="3e20f-138">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="3e20f-139">使用 `[Required]` 属性标记必需字段：</span><span class="sxs-lookup"><span data-stu-id="3e20f-139">Mark required fields with the `[Required]` attribute:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

<span data-ttu-id="3e20f-140">API 方法需要通过某种方式处理数据。</span><span class="sxs-lookup"><span data-stu-id="3e20f-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="3e20f-141">使用原始 Xamarin 示例所用的 `ITodoRepository` 接口：</span><span class="sxs-lookup"><span data-stu-id="3e20f-141">Use the same `ITodoRepository` interface the original Xamarin sample uses:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

<span data-ttu-id="3e20f-142">在此示例中，该实现仅使用一个专用项集合：</span><span class="sxs-lookup"><span data-stu-id="3e20f-142">For this sample, the implementation just uses a private collection of items:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

<span data-ttu-id="3e20f-143">在 *Startup.cs* 中配置该实现:</span><span class="sxs-lookup"><span data-stu-id="3e20f-143">Configure the implementation in *Startup.cs*:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a><span data-ttu-id="3e20f-144">创建控制器</span><span class="sxs-lookup"><span data-stu-id="3e20f-144">Creating the Controller</span></span>

<span data-ttu-id="3e20f-145">将新的控制器添加到项目 [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs)。</span><span class="sxs-lookup"><span data-stu-id="3e20f-145">Add a new controller to the project, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span></span> <span data-ttu-id="3e20f-146">它应从继承 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 。</span><span class="sxs-lookup"><span data-stu-id="3e20f-146">It should inherit from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="3e20f-147">添加 `Route` 属性以指示控制器将处理路径以 `api/todoitems` 开始的请求。</span><span class="sxs-lookup"><span data-stu-id="3e20f-147">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="3e20f-148">路由中的 `[controller]` 标记会被控制器的名称代替（省略 `Controller` 后缀），这对全局路由特别有用。</span><span class="sxs-lookup"><span data-stu-id="3e20f-148">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="3e20f-149">详细了解 [路由](../fundamentals/routing.md)。</span><span class="sxs-lookup"><span data-stu-id="3e20f-149">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="3e20f-150">控制器需要 `ITodoRepository` 才能正常运行；通过控制器的构造函数请求该类型的实例。</span><span class="sxs-lookup"><span data-stu-id="3e20f-150">The controller requires an `ITodoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="3e20f-151">在运行时，此实例将使用框架对 [依赖关系注入](../fundamentals/dependency-injection.md) 的支持来提供。</span><span class="sxs-lookup"><span data-stu-id="3e20f-151">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

<span data-ttu-id="3e20f-152">此 API 支持四个不同的 HTTP 谓词来执行对数据源的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="3e20f-152">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="3e20f-153">最简单的是读取操作，它对应于 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="3e20f-153">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="3e20f-154">读取项目</span><span class="sxs-lookup"><span data-stu-id="3e20f-154">Reading Items</span></span>

<span data-ttu-id="3e20f-155">要请求项列表，可对 `List` 方法使用 GET 请求。</span><span class="sxs-lookup"><span data-stu-id="3e20f-155">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="3e20f-156">`[HttpGet]` 方法的 `List` 属性指示此操作应仅处理 GET 请求。</span><span class="sxs-lookup"><span data-stu-id="3e20f-156">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="3e20f-157">此操作的路由是在控制器上指定的路由。</span><span class="sxs-lookup"><span data-stu-id="3e20f-157">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="3e20f-158">你不一定必须将操作名称用作路由的一部分。</span><span class="sxs-lookup"><span data-stu-id="3e20f-158">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="3e20f-159">你只需确保每个操作都有唯一的和明确的路由。</span><span class="sxs-lookup"><span data-stu-id="3e20f-159">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="3e20f-160">路由属性可以分别应用在控制器和方法级别，以此生成特定的路由。</span><span class="sxs-lookup"><span data-stu-id="3e20f-160">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

<span data-ttu-id="3e20f-161">`List`方法返回 200 OK 响应代码和所有 Todo 项，序列化为 JSON。</span><span class="sxs-lookup"><span data-stu-id="3e20f-161">The `List` method returns a 200 OK response code and all of the Todo items, serialized as JSON.</span></span>

<span data-ttu-id="3e20f-162">你可以使用多种工具测试新的 API 方法，如 [Postman](https://www.getpostman.com/docs/)，如此处所示：</span><span class="sxs-lookup"><span data-stu-id="3e20f-162">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Postman 控制台，其中显示一个 todoitem 的 GET 请求，以及显示所返回的三个项目的 JSON 的响应正文](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="3e20f-164">创建项目</span><span class="sxs-lookup"><span data-stu-id="3e20f-164">Creating Items</span></span>

<span data-ttu-id="3e20f-165">按照约定，创建新数据项映射到 HTTP POST 谓词。</span><span class="sxs-lookup"><span data-stu-id="3e20f-165">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="3e20f-166">`Create` 方法具有应用于它的 `[HttpPost]` 属性，并接受 `TodoItem` 实例。</span><span class="sxs-lookup"><span data-stu-id="3e20f-166">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `TodoItem` instance.</span></span> <span data-ttu-id="3e20f-167">由于 `item` 参数在 POST 的正文中传递，因此该参数会指定 `[FromBody]` 属性。</span><span class="sxs-lookup"><span data-stu-id="3e20f-167">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="3e20f-168">在该方法中，会检查项的有效性和之前是否存在于数据存储，并且如果没有任何问题，则使用存储库添加。</span><span class="sxs-lookup"><span data-stu-id="3e20f-168">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="3e20f-169">检查 `ModelState.IsValid` 将执行 [模型验证](../mvc/models/validation.md)，应该在每个接受用户输入的 API 方法中执行此步骤。</span><span class="sxs-lookup"><span data-stu-id="3e20f-169">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

<span data-ttu-id="3e20f-170">该示例使用的 `enum` 包含错误代码将传递到移动客户端：</span><span class="sxs-lookup"><span data-stu-id="3e20f-170">The sample uses an `enum` containing error codes that are passed to the mobile client:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

<span data-ttu-id="3e20f-171">使用 Postman 测试添加新项，选择 POST 谓词并在请求正文中以 JSON 格式提供新对象。</span><span class="sxs-lookup"><span data-stu-id="3e20f-171">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="3e20f-172">你还应添加一个请求标头指定 `Content-Type` 为 `application/json`。</span><span class="sxs-lookup"><span data-stu-id="3e20f-172">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![显示 POST 和响应的 Postman 控制台](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="3e20f-174">该方法返回在响应中新建的项。</span><span class="sxs-lookup"><span data-stu-id="3e20f-174">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="3e20f-175">更新项目</span><span class="sxs-lookup"><span data-stu-id="3e20f-175">Updating Items</span></span>

<span data-ttu-id="3e20f-176">通过使用 HTTP PUT 请求来修改记录。</span><span class="sxs-lookup"><span data-stu-id="3e20f-176">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="3e20f-177">除了此更改之外，`Edit` 方法几乎与 `Create` 完全相同。</span><span class="sxs-lookup"><span data-stu-id="3e20f-177">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="3e20f-178">请注意，如果未找到记录，则 `Edit` 操作将返回 `NotFound`(404) 响应。</span><span class="sxs-lookup"><span data-stu-id="3e20f-178">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

<span data-ttu-id="3e20f-179">若要使用 Postman 进行测试，将谓词更改为 PUT。</span><span class="sxs-lookup"><span data-stu-id="3e20f-179">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="3e20f-180">在请求正文中指定要更新的对象数据。</span><span class="sxs-lookup"><span data-stu-id="3e20f-180">Specify the updated object data in the Body of the request.</span></span>

![显示 PUT 和响应的 Postman 控制台](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="3e20f-182">为了与预先存在的 API 保持一致，此方法在成功时返回 `NoContent` (204) 响应。</span><span class="sxs-lookup"><span data-stu-id="3e20f-182">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="3e20f-183">删除项</span><span class="sxs-lookup"><span data-stu-id="3e20f-183">Deleting Items</span></span>

<span data-ttu-id="3e20f-184">删除记录可以通过向服务发出 DELETE 请求并传递要删除项的 ID 来完成。</span><span class="sxs-lookup"><span data-stu-id="3e20f-184">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="3e20f-185">与更新一样，请求的项不存在时会收到 `NotFound` 响应。</span><span class="sxs-lookup"><span data-stu-id="3e20f-185">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="3e20f-186">请求成功会得到 `NoContent` (204) 响应。</span><span class="sxs-lookup"><span data-stu-id="3e20f-186">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

<span data-ttu-id="3e20f-187">请注意，在测试删除功能时，请求正文中不需要任何内容。</span><span class="sxs-lookup"><span data-stu-id="3e20f-187">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![显示 DELETE 和响应的 Postman 控制台](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a><span data-ttu-id="3e20f-189">防止过度发布</span><span class="sxs-lookup"><span data-stu-id="3e20f-189">Prevent over-posting</span></span>

<span data-ttu-id="3e20f-190">目前，示例应用公开了整个 `TodoItem` 对象。</span><span class="sxs-lookup"><span data-stu-id="3e20f-190">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="3e20f-191">生产应用通常使用模型的子集来限制输入和返回的数据。</span><span class="sxs-lookup"><span data-stu-id="3e20f-191">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="3e20f-192">这背后有多种原因，但安全性是主要原因。</span><span class="sxs-lookup"><span data-stu-id="3e20f-192">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="3e20f-193">模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。</span><span class="sxs-lookup"><span data-stu-id="3e20f-193">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="3e20f-194">本文使用的是 **DTO**。</span><span class="sxs-lookup"><span data-stu-id="3e20f-194">**DTO** is used in this article.</span></span>

<span data-ttu-id="3e20f-195">DTO 可用于：</span><span class="sxs-lookup"><span data-stu-id="3e20f-195">A DTO may be used to:</span></span>

* <span data-ttu-id="3e20f-196">防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="3e20f-196">Prevent over-posting.</span></span>
* <span data-ttu-id="3e20f-197">隐藏客户端不应查看的属性。</span><span class="sxs-lookup"><span data-stu-id="3e20f-197">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="3e20f-198">省略一些属性以缩减有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="3e20f-198">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="3e20f-199">平展包含嵌套对象的对象图。</span><span class="sxs-lookup"><span data-stu-id="3e20f-199">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="3e20f-200">对客户端而言，平展的对象图可能更方便。</span><span class="sxs-lookup"><span data-stu-id="3e20f-200">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="3e20f-201">若要演示 DTO 方法，请参阅 [阻止过账](xref:tutorials/first-web-api#prevent-over-posting)</span><span class="sxs-lookup"><span data-stu-id="3e20f-201">To demonstrate the DTO approach, see [Prevent over-posting](xref:tutorials/first-web-api#prevent-over-posting)</span></span>

## <a name="common-web-api-conventions"></a><span data-ttu-id="3e20f-202">常见的 Web API 约定</span><span class="sxs-lookup"><span data-stu-id="3e20f-202">Common Web API Conventions</span></span>

<span data-ttu-id="3e20f-203">开发应用程序的后端服务时，你将想要使用一组一致的约定或策略来处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="3e20f-203">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="3e20f-204">例如，在上面所示服务中，针对不存在的特定记录的请求会收到 `NotFound` 响应，而不是`BadRequest` 响应。</span><span class="sxs-lookup"><span data-stu-id="3e20f-204">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="3e20f-205">同样，对于此服务，传递模型绑定类型的命令始终检查 `ModelState.IsValid` 并为无效的模型类型返回 `BadRequest`。</span><span class="sxs-lookup"><span data-stu-id="3e20f-205">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="3e20f-206">一旦为 Api 指定通用策略，一般可以将其封装在 [Filter（筛选器）](../mvc/controllers/filters.md)。</span><span class="sxs-lookup"><span data-stu-id="3e20f-206">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="3e20f-207">详细了解 [如何封装 ASP.NET Core MVC 应用程序中的通用 API 策略](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters)。</span><span class="sxs-lookup"><span data-stu-id="3e20f-207">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e20f-208">其他资源</span><span class="sxs-lookup"><span data-stu-id="3e20f-208">Additional resources</span></span>

- [<span data-ttu-id="3e20f-209">Xamarin： Web 服务身份验证</span><span class="sxs-lookup"><span data-stu-id="3e20f-209">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="3e20f-210">Xamarin：使用 RESTful Web 服务</span><span class="sxs-lookup"><span data-stu-id="3e20f-210">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="3e20f-211">Microsoft Learn：在 Xamarin 应用中使用 REST web 服务</span><span class="sxs-lookup"><span data-stu-id="3e20f-211">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="3e20f-212">Microsoft Learn：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="3e20f-212">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
