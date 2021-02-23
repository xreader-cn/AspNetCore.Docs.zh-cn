---
title: 第 2 部分，将控制器添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: ASP.NET Core MVC 教程系列第 2 部分。
ms.author: riande
ms.date: 01/23/2021
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
uid: tutorials/first-mvc-app/adding-controller
ms.custom: contperf-fy21q3
ms.openlocfilehash: 47bb9b96bd5565a3a67f3cbdf9a4b6bc1f987447
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975256"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="e9d14-103">第 2 部分，将控制器添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="e9d14-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="e9d14-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e9d14-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e9d14-105">模型-视图-控制器 (MVC) 体系结构模式将应用分成 3 个主要组件：模型 (M)、视图 (V) 和控制器 (C)  。</span><span class="sxs-lookup"><span data-stu-id="e9d14-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="e9d14-106">MVC 模式有助于创建比传统单片应用更易于测试和更新的应用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span>

<span data-ttu-id="e9d14-107">基于 MVC 的应用包含：</span><span class="sxs-lookup"><span data-stu-id="e9d14-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="e9d14-108">模型 (M)：表示应用数据的类。</span><span class="sxs-lookup"><span data-stu-id="e9d14-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="e9d14-109">模型类使用验证逻辑来对该数据强制实施业务规则。</span><span class="sxs-lookup"><span data-stu-id="e9d14-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="e9d14-110">通常，模型对象检索模型状态并将其存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="e9d14-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="e9d14-111">本教程中，`Movie` 模型将从数据库中检索电影数据，并将其提供给视图或对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="e9d14-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="e9d14-112">更新后的数据将写入到数据库。</span><span class="sxs-lookup"><span data-stu-id="e9d14-112">Updated data is written to a database.</span></span>
* <span data-ttu-id="e9d14-113">视图 (V)：视图是显示应用用户界面 (UI) 的组件。</span><span class="sxs-lookup"><span data-stu-id="e9d14-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="e9d14-114">此 UI 通常会显示模型数据。</span><span class="sxs-lookup"><span data-stu-id="e9d14-114">Generally, this UI displays the model data.</span></span>
* <span data-ttu-id="e9d14-115">控制器：可执行以下操作的类：</span><span class="sxs-lookup"><span data-stu-id="e9d14-115">**C** ontrollers: Classes that:</span></span>
  * <span data-ttu-id="e9d14-116">处理浏览器请求。</span><span class="sxs-lookup"><span data-stu-id="e9d14-116">Handle browser requests.</span></span>
  * <span data-ttu-id="e9d14-117">检索模型数据。</span><span class="sxs-lookup"><span data-stu-id="e9d14-117">Retrieve model data.</span></span>
  * <span data-ttu-id="e9d14-118">调用返回响应的视图模板。</span><span class="sxs-lookup"><span data-stu-id="e9d14-118">Call view templates that return a response.</span></span>

<span data-ttu-id="e9d14-119">在 MVC 应用中，视图仅显示信息。</span><span class="sxs-lookup"><span data-stu-id="e9d14-119">In an MVC app, the view only displays information.</span></span> <span data-ttu-id="e9d14-120">控制器处理用户输入和交互并对其进行响应。</span><span class="sxs-lookup"><span data-stu-id="e9d14-120">The controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="e9d14-121">例如，控制器处理 URL 段和查询字符串值，并将这些值传递给模型。</span><span class="sxs-lookup"><span data-stu-id="e9d14-121">For example, the controller handles URL segments and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="e9d14-122">该模型可使用这些值查询数据库。</span><span class="sxs-lookup"><span data-stu-id="e9d14-122">The model might use these values to query the database.</span></span> <span data-ttu-id="e9d14-123">例如：</span><span class="sxs-lookup"><span data-stu-id="e9d14-123">For example:</span></span>

* <span data-ttu-id="e9d14-124">`Https://localhost:5001/Home/Privacy`：指定 `Home` 控制器和 `Privacy` 操作。</span><span class="sxs-lookup"><span data-stu-id="e9d14-124">`Https://localhost:5001/Home/Privacy`: specifies the `Home`  controller  and the `Privacy` action.</span></span>
* <span data-ttu-id="e9d14-125">`Https://localhost:5001/Movies/Edit/5`：是使用 `Movies` 控制器和 `Edit` 操作编辑 ID=5 的电影的请求，本教程稍后将对此进行详细介绍。</span><span class="sxs-lookup"><span data-stu-id="e9d14-125">`Https://localhost:5001/Movies/Edit/5`: is a request to edit the movie with ID=5 using the `Movies` controller and the `Edit` action, which are detailed later in the tutorial.</span></span>

<span data-ttu-id="e9d14-126">本教程的后续部分中将介绍路由数据。</span><span class="sxs-lookup"><span data-stu-id="e9d14-126">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="e9d14-127">MVC 体系结构模式将应用分成 3 组主要组件：模型、视图和控制器。</span><span class="sxs-lookup"><span data-stu-id="e9d14-127">The MVC architectural pattern separates an app into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="e9d14-128">此模式有助于实现关注点分离：UI 逻辑位于视图中。</span><span class="sxs-lookup"><span data-stu-id="e9d14-128">This pattern helps to achieve separation of concerns: The UI logic belongs in the view.</span></span> <span data-ttu-id="e9d14-129">输入逻辑位于控制器中。</span><span class="sxs-lookup"><span data-stu-id="e9d14-129">Input logic belongs in the controller.</span></span> <span data-ttu-id="e9d14-130">业务逻辑位于模型中。</span><span class="sxs-lookup"><span data-stu-id="e9d14-130">Business logic belongs in the model.</span></span> <span data-ttu-id="e9d14-131">这种隔离有助于控制构建应用时的复杂程度，因为它可用于一次处理一个实现特性，而不影响其他特性的代码。</span><span class="sxs-lookup"><span data-stu-id="e9d14-131">This separation helps manage complexity when building an app, because it enables work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="e9d14-132">例如，处理视图代码时不必依赖业务逻辑代码。</span><span class="sxs-lookup"><span data-stu-id="e9d14-132">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="e9d14-133">本教程系列介绍并演示了这些概念，同时生成一个电影应用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-133">These concepts are introduced and demonstrated in this tutorial series while building a movie app.</span></span> <span data-ttu-id="e9d14-134">MVC 项目包含“控制器”和“视图”文件夹 。</span><span class="sxs-lookup"><span data-stu-id="e9d14-134">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="e9d14-135">添加控制器</span><span class="sxs-lookup"><span data-stu-id="e9d14-135">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9d14-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9d14-136">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e9d14-137">在“解决方案资源管理器”中，右键单击“控制器”>“添加”>“控制器” 。</span><span class="sxs-lookup"><span data-stu-id="e9d14-137">In the **Solution Explorer**, right-click **Controllers > Add > Controller**.</span></span>

![在“解决方案资源管理器”中，右键单击“控制器”>“添加”>“控制器”](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

<span data-ttu-id="e9d14-139">在“添加基架”对话框中，选择“MVC 控制器 - 空” 。</span><span class="sxs-lookup"><span data-stu-id="e9d14-139">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**.</span></span>

![添加 MVC 控制器并为其命名](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

<span data-ttu-id="e9d14-141">在“添加新项 - MvcMovie”对话框中，输入 HelloWorldController.cs，然后选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="e9d14-141">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9d14-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9d14-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e9d14-143">选择“EXPLORER”图标，然后按住 Control 并单击（右键单击）“控制器”，选择“新建文件”，然后将新文件命名为 HelloWorldController.cs 。</span><span class="sxs-lookup"><span data-stu-id="e9d14-143">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

![上下文菜单](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e9d14-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e9d14-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e9d14-146">在“解决方案资源管理器”中，右键单击“控制器”，选择“添加”>“新文件”。 </span><span class="sxs-lookup"><span data-stu-id="e9d14-146">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![上下文菜单](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="e9d14-148">选择“ASP.NET Core”和“控制器类”。 </span><span class="sxs-lookup"><span data-stu-id="e9d14-148">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="e9d14-149">将控制器命名为“HelloWorldController”。</span><span class="sxs-lookup"><span data-stu-id="e9d14-149">Name the controller **HelloWorldController**.</span></span>

![添加 MVC 控制器并为其命名](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="e9d14-151">将“Controllers/HelloWorldController.cs”的内容替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="e9d14-151">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="e9d14-152">控制器中的每个 `public` 方法均可作为 HTTP 终结点调用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-152">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="e9d14-153">上述示例中，两种方法均返回一个字符串。</span><span class="sxs-lookup"><span data-stu-id="e9d14-153">In the sample above, both methods return a string.</span></span> <span data-ttu-id="e9d14-154">请注意每个方法前面的注释。</span><span class="sxs-lookup"><span data-stu-id="e9d14-154">Note the comments preceding each method.</span></span>

<span data-ttu-id="e9d14-155">一个 HTTP 终结点：</span><span class="sxs-lookup"><span data-stu-id="e9d14-155">An HTTP endpoint:</span></span>

* <span data-ttu-id="e9d14-156">是 Web 应用程序中可定向的 URL，如 `https://localhost:5001/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-156">Is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`.</span></span>
* <span data-ttu-id="e9d14-157">结合以下内容：</span><span class="sxs-lookup"><span data-stu-id="e9d14-157">Combines:</span></span>
  * <span data-ttu-id="e9d14-158">所用的协议：`HTTPS`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-158">The protocol used: `HTTPS`.</span></span>
  * <span data-ttu-id="e9d14-159">Web 服务器的网络位置，包括 TCP 端口：`localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-159">The network location of the web server, including the TCP port: `localhost:5001`.</span></span>
  * <span data-ttu-id="e9d14-160">目标 URI：`HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-160">The target URI: `HelloWorld`.</span></span>

<span data-ttu-id="e9d14-161">第一条注释指出这是一个 [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) 方法，它通过向基 URL 追加 `/HelloWorld/` 进行调用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-161">The first comment states this is an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span>

<span data-ttu-id="e9d14-162">第二条注释指定一个 [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) 方法，它通过向 URL 追加 `/HelloWorld/Welcome/` 进行调用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-162">The second comment specifies an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="e9d14-163">本教程稍后将使用基架引擎生成 `HTTP POST` 方法，用于更新数据。</span><span class="sxs-lookup"><span data-stu-id="e9d14-163">Later on in the tutorial, the scaffolding engine is used to generate `HTTP POST` methods, which update data.</span></span>

<span data-ttu-id="e9d14-164">在不使用调试程序的情况下运行应用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-164">Run the app without the debugger.</span></span>

<span data-ttu-id="e9d14-165">将“HelloWorld”追加到地址栏中的路径。</span><span class="sxs-lookup"><span data-stu-id="e9d14-165">Append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="e9d14-166">`Index` 方法返回一个字符串。</span><span class="sxs-lookup"><span data-stu-id="e9d14-166">The `Index` method returns a string.</span></span>

![显示“这是我的默认操作”应用响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="e9d14-168">MVC 根据入站 URL 调用控制器类以及其中的操作方法。</span><span class="sxs-lookup"><span data-stu-id="e9d14-168">MVC invokes controller classes, and the action methods within them, depending on the incoming URL.</span></span> <span data-ttu-id="e9d14-169">MVC 所用的默认 [URL 路由逻辑](xref:mvc/controllers/routing)使用如下格式来确定调用的代码：</span><span class="sxs-lookup"><span data-stu-id="e9d14-169">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC, uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="e9d14-170">在 Startup.cs 文件的 `Configure` 方法中设置路由格式。</span><span class="sxs-lookup"><span data-stu-id="e9d14-170">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="e9d14-171">如果浏览到应用且不提供任何 URL 段，它将默认为上面突出显示的模板行中指定的“Home”控制器和“Index”方法。</span><span class="sxs-lookup"><span data-stu-id="e9d14-171">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>  <span data-ttu-id="e9d14-172">在前面的 URL 段中：</span><span class="sxs-lookup"><span data-stu-id="e9d14-172">In the preceding URL segments:</span></span>

* <span data-ttu-id="e9d14-173">第一个 URL 段决定要运行的控制器类。</span><span class="sxs-lookup"><span data-stu-id="e9d14-173">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="e9d14-174">因此 `localhost:5001/HelloWorld` 映射到 HelloWorld 控制器类。</span><span class="sxs-lookup"><span data-stu-id="e9d14-174">So `localhost:5001/HelloWorld` maps to the **HelloWorld** Controller class.</span></span>
* <span data-ttu-id="e9d14-175">该 URL 段的第二部分决定类上的操作方法。</span><span class="sxs-lookup"><span data-stu-id="e9d14-175">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="e9d14-176">因此 `localhost:5001/HelloWorld/Index` 触发 `HelloWorldController` 类的 `Index` 方法来运行。</span><span class="sxs-lookup"><span data-stu-id="e9d14-176">So `localhost:5001/HelloWorld/Index` causes the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="e9d14-177">请注意，只需浏览到 `localhost:5001/HelloWorld`，而 `Index` 方法默认调用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-177">Notice that you only had to browse to `localhost:5001/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="e9d14-178">`Index` 是默认方法，如果未显式指定方法名称，则将在控制器上调用它。</span><span class="sxs-lookup"><span data-stu-id="e9d14-178">`Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span>
* <span data-ttu-id="e9d14-179">URL 段的第三部分 (`id`) 针对的是路由数据。</span><span class="sxs-lookup"><span data-stu-id="e9d14-179">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="e9d14-180">本教程的后续部分中将介绍路由数据。</span><span class="sxs-lookup"><span data-stu-id="e9d14-180">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="e9d14-181">浏览到 `https://localhost:{PORT}/HelloWorld/Welcome`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-181">Browse to: `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="e9d14-182">将 `{PORT}` 替换为端口号。</span><span class="sxs-lookup"><span data-stu-id="e9d14-182">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="e9d14-183">`Welcome` 方法将运行并返回字符串 `This is the Welcome action method...`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-183">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="e9d14-184">对于此 URL，采用 `HelloWorld` 控制器和 `Welcome` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="e9d14-184">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="e9d14-185">目前尚未使用 URL 的 `[Parameters]` 部分。</span><span class="sxs-lookup"><span data-stu-id="e9d14-185">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![显示“这是 Welcome 操作方法”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="e9d14-187">修改代码，将一些参数信息从 URL 传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="e9d14-187">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="e9d14-188">例如 `/HelloWorld/Welcome?name=Rick&numtimes=4`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-188">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span>

<span data-ttu-id="e9d14-189">更改 `Welcome` 方法以包括以下代码中显示的两个参数：</span><span class="sxs-lookup"><span data-stu-id="e9d14-189">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="e9d14-190">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e9d14-190">The preceding code:</span></span>

* <span data-ttu-id="e9d14-191">使用 C# 可选参数功能指示，未为 `numTimes` 参数传递值时该参数默认为 1。</span><span class="sxs-lookup"><span data-stu-id="e9d14-191">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span>
* <span data-ttu-id="e9d14-192">使用 `HtmlEncoder.Default.Encode` 防止恶意输入（例如通过 JavaScript）损害应用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-192">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input, such as through JavaScript.</span></span>
* <span data-ttu-id="e9d14-193">在 `$"Hello {name}, NumTimes is: {numTimes}"` 中使用[内插字符串](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)。</span><span class="sxs-lookup"><span data-stu-id="e9d14-193">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span>

<span data-ttu-id="e9d14-194">运行应用并浏览到 `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-194">Run the app and browse to: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="e9d14-195">将 `{PORT}` 替换为端口号。</span><span class="sxs-lookup"><span data-stu-id="e9d14-195">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="e9d14-196">在 URL 中对 `name` 和 `numtimes` 使用其他值。</span><span class="sxs-lookup"><span data-stu-id="e9d14-196">Try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="e9d14-197">MVC [模型绑定](xref:mvc/models/model-binding)系统可将命名参数从查询字符串自动映射到方法中的参数。</span><span class="sxs-lookup"><span data-stu-id="e9d14-197">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string to parameters in the method.</span></span> <span data-ttu-id="e9d14-198">有关详细信息，请参阅[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="e9d14-198">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![显示 Hello Rick 的应用程序响应的浏览器窗口，NumTimes 为 \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="e9d14-200">在上图中：</span><span class="sxs-lookup"><span data-stu-id="e9d14-200">In the previous image:</span></span>

* <span data-ttu-id="e9d14-201">未使用 `Parameters` URL 段。</span><span class="sxs-lookup"><span data-stu-id="e9d14-201">The URL segment `Parameters` isn't used.</span></span>
* <span data-ttu-id="e9d14-202">在[查询字符串](https://wikipedia.org/wiki/Query_string)中传递 `name` 和 `numTimes` 参数。</span><span class="sxs-lookup"><span data-stu-id="e9d14-202">The `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span>
* <span data-ttu-id="e9d14-203">上述 URL 中的 `?`（问号）为分隔符，后接查询字符串。</span><span class="sxs-lookup"><span data-stu-id="e9d14-203">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span>
* <span data-ttu-id="e9d14-204">`&` 字符将字段/值对分隔开。</span><span class="sxs-lookup"><span data-stu-id="e9d14-204">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="e9d14-205">将 `Welcome` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="e9d14-205">Replace the `Welcome` method with the following code:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="e9d14-206">运行应用并输入以下 URL：`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="e9d14-206">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="e9d14-207">在上述 URL 中：</span><span class="sxs-lookup"><span data-stu-id="e9d14-207">In the preceding URL:</span></span>

* <span data-ttu-id="e9d14-208">第三个 URL 段与路由参数 `id` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="e9d14-208">The third URL segment matched the route parameter `id`.</span></span> 
* <span data-ttu-id="e9d14-209">`Welcome` 方法包含 `MapControllerRoute` 方法中匹配 URL 模板的参数 `id`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-209">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="e9d14-210">后面的 `?`（`id?` 中）表示 `id` 参数可选。</span><span class="sxs-lookup"><span data-stu-id="e9d14-210">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

<span data-ttu-id="e9d14-211">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="e9d14-211">In the preceding example:</span></span>

* <span data-ttu-id="e9d14-212">第三个 URL 段与路由参数 `id` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="e9d14-212">The third URL segment matched the route parameter `id`.</span></span>
* <span data-ttu-id="e9d14-213">`Welcome` 方法包含 `MapControllerRoute` 方法中匹配 URL 模板的参数 `id`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-213">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="e9d14-214">后面的 `?`（`id?` 中）表示 `id` 参数可选。</span><span class="sxs-lookup"><span data-stu-id="e9d14-214">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e9d14-215">[上一步：入门](start-mvc.md)
> [下一步：添加视图](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="e9d14-215">[Previous: Get Started](start-mvc.md)
[Next: Add a View](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e9d14-216">模型-视图-控制器 (MVC) 体系结构模式将应用分成 3 个主要组件：模型 (M)、视图 (V) 和控制器 (C)  。</span><span class="sxs-lookup"><span data-stu-id="e9d14-216">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="e9d14-217">MVC 模式有助于创建比传统单片应用更易于测试和更新的应用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-217">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="e9d14-218">基于 MVC 的应用包含：</span><span class="sxs-lookup"><span data-stu-id="e9d14-218">MVC-based apps contain:</span></span>

* <span data-ttu-id="e9d14-219">模型 (M)：表示应用数据的类。</span><span class="sxs-lookup"><span data-stu-id="e9d14-219">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="e9d14-220">模型类使用验证逻辑来对该数据强制实施业务规则。</span><span class="sxs-lookup"><span data-stu-id="e9d14-220">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="e9d14-221">通常，模型对象检索模型状态并将其存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="e9d14-221">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="e9d14-222">本教程中，`Movie` 模型将从数据库中检索电影数据，并将其提供给视图或对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="e9d14-222">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="e9d14-223">更新后的数据将写入到数据库。</span><span class="sxs-lookup"><span data-stu-id="e9d14-223">Updated data is written to a database.</span></span>

* <span data-ttu-id="e9d14-224">视图 (V)：视图是显示应用用户界面 (UI) 的组件。</span><span class="sxs-lookup"><span data-stu-id="e9d14-224">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="e9d14-225">此 UI 通常会显示模型数据。</span><span class="sxs-lookup"><span data-stu-id="e9d14-225">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="e9d14-226">控制器 (C)：处理浏览器请求的类。</span><span class="sxs-lookup"><span data-stu-id="e9d14-226">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="e9d14-227">它们检索模型数据并调用返回响应的视图模板。</span><span class="sxs-lookup"><span data-stu-id="e9d14-227">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="e9d14-228">在 MVC 应用中，视图仅显示信息；控制器处理并响应用户输入和交互。</span><span class="sxs-lookup"><span data-stu-id="e9d14-228">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="e9d14-229">例如，控制器处理路由数据和查询字符串值，并将这些值传递给模型。</span><span class="sxs-lookup"><span data-stu-id="e9d14-229">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="e9d14-230">该模型可使用这些值查询数据库。</span><span class="sxs-lookup"><span data-stu-id="e9d14-230">The model might use these values to query the database.</span></span> <span data-ttu-id="e9d14-231">例如，`https://localhost:5001/Home/About` 具有 `Home`（控制器）的路由数据和 `About`（在 Home 控制器上调用的操作方法）。</span><span class="sxs-lookup"><span data-stu-id="e9d14-231">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="e9d14-232">`https://localhost:5001/Movies/Edit/5` 是一个请求，用于通过电影控制器编辑 ID 为 5 的电影。</span><span class="sxs-lookup"><span data-stu-id="e9d14-232">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="e9d14-233">本教程的后续部分中将介绍路由数据。</span><span class="sxs-lookup"><span data-stu-id="e9d14-233">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="e9d14-234">MVC 模式可帮助创建分隔不同应用特性（输入逻辑、业务逻辑和 UI 逻辑）的应用，同时让这些元素之间实现松散耦合。</span><span class="sxs-lookup"><span data-stu-id="e9d14-234">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="e9d14-235">该模式可指定应用中每种逻辑的位置。</span><span class="sxs-lookup"><span data-stu-id="e9d14-235">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="e9d14-236">UI 逻辑位于视图中。</span><span class="sxs-lookup"><span data-stu-id="e9d14-236">The UI logic belongs in the view.</span></span> <span data-ttu-id="e9d14-237">输入逻辑位于控制器中。</span><span class="sxs-lookup"><span data-stu-id="e9d14-237">Input logic belongs in the controller.</span></span> <span data-ttu-id="e9d14-238">业务逻辑位于模型中。</span><span class="sxs-lookup"><span data-stu-id="e9d14-238">Business logic belongs in the model.</span></span> <span data-ttu-id="e9d14-239">这种隔离有助于控制构建应用时的复杂程度，因为它可用于一次处理一个实现特性，而不影响其他特性的代码。</span><span class="sxs-lookup"><span data-stu-id="e9d14-239">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="e9d14-240">例如，处理视图代码时不必依赖业务逻辑代码。</span><span class="sxs-lookup"><span data-stu-id="e9d14-240">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="e9d14-241">本教程系列中介绍了这些概念，并展示了如何使用它们构建电影应用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-241">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="e9d14-242">MVC 项目包含“控制器”和“视图”文件夹 。</span><span class="sxs-lookup"><span data-stu-id="e9d14-242">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="e9d14-243">添加控制器</span><span class="sxs-lookup"><span data-stu-id="e9d14-243">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9d14-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9d14-244">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9d14-245">在“解决方案资源管理器”中，右键单击“控制器”>“添加”>“控制器” </span><span class="sxs-lookup"><span data-stu-id="e9d14-245">In **Solution Explorer**, right-click **Controllers > Add > Controller**</span></span>

  ![上下文菜单](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* <span data-ttu-id="e9d14-247">在“添加基架”对话框中，选择“MVC 控制器 - 空” </span><span class="sxs-lookup"><span data-stu-id="e9d14-247">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![添加 MVC 控制器并为其命名](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="e9d14-249">在“添加空 MVC 控制器”对话框中，输入 HelloWorldController 并选择“ADD”  。</span><span class="sxs-lookup"><span data-stu-id="e9d14-249">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9d14-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9d14-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e9d14-251">选择“EXPLORER”图标，然后按住 Control 并单击（右键单击）“控制器”，选择“新建文件”，然后将新文件命名为 HelloWorldController.cs 。</span><span class="sxs-lookup"><span data-stu-id="e9d14-251">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![上下文菜单](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e9d14-253">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e9d14-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e9d14-254">在“解决方案资源管理器”中，右键单击“控制器”，选择“添加”>“新文件”。 </span><span class="sxs-lookup"><span data-stu-id="e9d14-254">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![上下文菜单](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="e9d14-256">选择“ASP.NET Core”和“MVC 控制器类”。 </span><span class="sxs-lookup"><span data-stu-id="e9d14-256">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="e9d14-257">将控制器命名为“HelloWorldController”。</span><span class="sxs-lookup"><span data-stu-id="e9d14-257">Name the controller **HelloWorldController**.</span></span>

![添加 MVC 控制器并为其命名](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="e9d14-259">将“Controllers/HelloWorldController.cs”的内容替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="e9d14-259">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="e9d14-260">控制器中的每个 `public` 方法均可作为 HTTP 终结点调用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-260">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="e9d14-261">上述示例中，两种方法均返回一个字符串。</span><span class="sxs-lookup"><span data-stu-id="e9d14-261">In the sample above, both methods return a string.</span></span> <span data-ttu-id="e9d14-262">请注意每个方法前面的注释。</span><span class="sxs-lookup"><span data-stu-id="e9d14-262">Note the comments preceding each method.</span></span>

<span data-ttu-id="e9d14-263">HTTP 终结点是 Web 应用程序中可定向的 URL（例如 `https://localhost:5001/HelloWorld`），其中结合了所用的协议 `HTTPS`、TCP 端口等 Web 服务器的网络位置 `localhost:5001`，以及目标 URI `HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-263">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="e9d14-264">第一条注释指出这是一个 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 方法，它通过向基 URL 追加 `/HelloWorld/` 进行调用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-264">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="e9d14-265">第二条注释指定一个 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 方法，它通过向 URL 追加 `/HelloWorld/Welcome/` 进行调用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-265">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="e9d14-266">本教程稍后将使用基架引擎生成 `HTTP POST` 方法，用于更新数据。</span><span class="sxs-lookup"><span data-stu-id="e9d14-266">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="e9d14-267">在非调试模式下运行应用，并将“HelloWorld”追加到地址栏中的路径。</span><span class="sxs-lookup"><span data-stu-id="e9d14-267">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="e9d14-268">`Index` 方法返回一个字符串。</span><span class="sxs-lookup"><span data-stu-id="e9d14-268">The `Index` method returns a string.</span></span>

![显示“这是我的默认操作”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="e9d14-270">MVC 根据入站 URL 调用控制器类（及其中的操作方法）。</span><span class="sxs-lookup"><span data-stu-id="e9d14-270">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="e9d14-271">MVC 所用的默认 [URL 路由逻辑](xref:mvc/controllers/routing)使用如下格式来确定调用的代码：</span><span class="sxs-lookup"><span data-stu-id="e9d14-271">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="e9d14-272">在 Startup.cs 文件的 `Configure` 方法中设置路由格式。</span><span class="sxs-lookup"><span data-stu-id="e9d14-272">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="e9d14-273">如果浏览到应用且不提供任何 URL 段，它将默认为上面突出显示的模板行中指定的“Home”控制器和“Index”方法。</span><span class="sxs-lookup"><span data-stu-id="e9d14-273">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="e9d14-274">第一个 URL 段决定要运行的控制器类。</span><span class="sxs-lookup"><span data-stu-id="e9d14-274">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="e9d14-275">因此 `localhost:{PORT}/HelloWorld` 映射到 `HelloWorldController` 类。</span><span class="sxs-lookup"><span data-stu-id="e9d14-275">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="e9d14-276">该 URL 段的第二部分决定类上的操作方法。</span><span class="sxs-lookup"><span data-stu-id="e9d14-276">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="e9d14-277">因此 `localhost:{PORT}/HelloWorld/Index` 将触发 `HelloWorldController` 类的 `Index` 运行。</span><span class="sxs-lookup"><span data-stu-id="e9d14-277">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="e9d14-278">请注意，只需浏览到 `localhost:{PORT}/HelloWorld`，而 `Index` 方法默认调用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-278">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="e9d14-279">原因是 `Index` 是默认方法，如果未显式指定方法名称，则将在控制器上调用它。</span><span class="sxs-lookup"><span data-stu-id="e9d14-279">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="e9d14-280">URL 段的第三部分 (`id`) 针对的是路由数据。</span><span class="sxs-lookup"><span data-stu-id="e9d14-280">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="e9d14-281">本教程的后续部分中将介绍路由数据。</span><span class="sxs-lookup"><span data-stu-id="e9d14-281">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="e9d14-282">浏览到 `https://localhost:{PORT}/HelloWorld/Welcome`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-282">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="e9d14-283">`Welcome` 方法将运行并返回字符串 `This is the Welcome action method...`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-283">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="e9d14-284">对于此 URL，采用 `HelloWorld` 控制器和 `Welcome` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="e9d14-284">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="e9d14-285">目前尚未使用 URL 的 `[Parameters]` 部分。</span><span class="sxs-lookup"><span data-stu-id="e9d14-285">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![显示“这是 Welcome 操作方法”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="e9d14-287">修改代码，将一些参数信息从 URL 传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="e9d14-287">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="e9d14-288">例如 `/HelloWorld/Welcome?name=Rick&numtimes=4`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-288">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="e9d14-289">更改 `Welcome` 方法以包括以下代码中显示的两个参数：</span><span class="sxs-lookup"><span data-stu-id="e9d14-289">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="e9d14-290">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e9d14-290">The preceding code:</span></span>

* <span data-ttu-id="e9d14-291">使用 C# 可选参数功能指示，未为 `numTimes` 参数传递值时该参数默认为 1。</span><span class="sxs-lookup"><span data-stu-id="e9d14-291">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="e9d14-292">使用 `HtmlEncoder.Default.Encode` 防止恶意输入（即 JavaScript）损害应用。</span><span class="sxs-lookup"><span data-stu-id="e9d14-292">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="e9d14-293">在 `$"Hello {name}, NumTimes is: {numTimes}"` 中使用[内插字符串](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)。</span><span class="sxs-lookup"><span data-stu-id="e9d14-293">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="e9d14-294">运行应用并浏览到：</span><span class="sxs-lookup"><span data-stu-id="e9d14-294">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="e9d14-295">（将 `{PORT}` 替换为端口号。）可在 URL 中对 `name` 和 `numtimes` 使用其他值。</span><span class="sxs-lookup"><span data-stu-id="e9d14-295">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="e9d14-296">MVC [模型绑定](xref:mvc/models/model-binding)系统可将命名参数从地址栏中的查询字符串自动映射到方法中的参数。</span><span class="sxs-lookup"><span data-stu-id="e9d14-296">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="e9d14-297">有关详细信息，请参阅[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="e9d14-297">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![显示 Hello Rick 的应用程序响应的浏览器窗口，NumTimes 为 \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="e9d14-299">在上图中，未使用 URL 段 (`Parameters`)，且在[查询字符串](https://wikipedia.org/wiki/Query_string)中传递 `name` 和 `numTimes` 参数。</span><span class="sxs-lookup"><span data-stu-id="e9d14-299">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="e9d14-300">上述 URL 中的 `?`（问号）为分隔符，后接查询字符串。</span><span class="sxs-lookup"><span data-stu-id="e9d14-300">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="e9d14-301">`&` 字符将字段/值对分隔开。</span><span class="sxs-lookup"><span data-stu-id="e9d14-301">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="e9d14-302">将 `Welcome` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="e9d14-302">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="e9d14-303">运行应用并输入以下 URL：`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="e9d14-303">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="e9d14-304">此时，第三个 URL 段与路由参数 `id` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="e9d14-304">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="e9d14-305">`Welcome` 方法包含 `MapRoute` 方法中匹配 URL 模板的参数 `id`。</span><span class="sxs-lookup"><span data-stu-id="e9d14-305">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="e9d14-306">后面的 `?`（`id?` 中）表示 `id` 参数可选。</span><span class="sxs-lookup"><span data-stu-id="e9d14-306">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="e9d14-307">上述示例中，控制器始终执行 MVC 的“VC”部分，即视图和控制器工作。</span><span class="sxs-lookup"><span data-stu-id="e9d14-307">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="e9d14-308">控制器将直接返回 HTML。</span><span class="sxs-lookup"><span data-stu-id="e9d14-308">The controller is returning HTML directly.</span></span> <span data-ttu-id="e9d14-309">通常不希望控制器直接返回 HTML，因为编码和维护非常繁琐。</span><span class="sxs-lookup"><span data-stu-id="e9d14-309">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="e9d14-310">通常，需使用单独的 Razor 视图模板文件来帮助生成 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="e9d14-310">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="e9d14-311">可在下一教程中执行该操作。</span><span class="sxs-lookup"><span data-stu-id="e9d14-311">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e9d14-312">[上一页](start-mvc.md)
> [下一页](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="e9d14-312">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end
