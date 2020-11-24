---
title: 第 2 部分，将控制器添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: ASP.NET Core MVC 教程系列第 2 部分。
ms.author: riande
ms.date: 11/12/2020
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
ms.openlocfilehash: e51edc15b14a5bdd1d53e547e0b469ad608f46d0
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688395"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="4d6c6-103">第 2 部分，将控制器添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="4d6c6-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="4d6c6-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4d6c6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4d6c6-105">模型-视图-控制器 (MVC) 体系结构模式将应用分成 3 个主要组件：模型 (M)、视图 (V) 和控制器 (C)  。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="4d6c6-106">MVC 模式有助于创建比传统单片应用更易于测试和更新的应用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="4d6c6-107">基于 MVC 的应用包含：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="4d6c6-108">模型 (M)：表示应用数据的类。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="4d6c6-109">模型类使用验证逻辑来对该数据强制实施业务规则。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="4d6c6-110">通常，模型对象检索模型状态并将其存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="4d6c6-111">本教程中，`Movie` 模型将从数据库中检索电影数据，并将其提供给视图或对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="4d6c6-112">更新后的数据将写入到数据库。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-112">Updated data is written to a database.</span></span>

* <span data-ttu-id="4d6c6-113">视图 (V)：视图是显示应用用户界面 (UI) 的组件。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="4d6c6-114">此 UI 通常会显示模型数据。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-114">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="4d6c6-115">控制器 (C)：处理浏览器请求的类。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-115">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="4d6c6-116">它们检索模型数据并调用返回响应的视图模板。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-116">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="4d6c6-117">在 MVC 应用中，视图仅显示信息；控制器处理并响应用户输入和交互。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-117">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="4d6c6-118">例如，控制器处理路由数据和查询字符串值，并将这些值传递给模型。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-118">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="4d6c6-119">该模型可使用这些值查询数据库。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-119">The model might use these values to query the database.</span></span> <span data-ttu-id="4d6c6-120">例如，`https://localhost:5001/Home/Privacy` 具有 `Home`（控制器）的路由数据和 `Privacy`（在 Home 控制器上调用的操作方法）。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-120">For example, `https://localhost:5001/Home/Privacy` has route data of `Home` (the controller) and `Privacy` (the action method to call on the home controller).</span></span> <span data-ttu-id="4d6c6-121">`https://localhost:5001/Movies/Edit/5` 是一个请求，用于通过电影控制器编辑 ID 为 5 的电影。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-121">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="4d6c6-122">本教程的后续部分中将介绍路由数据。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-122">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="4d6c6-123">MVC 模式可帮助创建分隔不同应用特性（输入逻辑、业务逻辑和 UI 逻辑）的应用，同时让这些元素之间实现松散耦合。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-123">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="4d6c6-124">该模式可指定应用中每种逻辑的位置。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-124">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="4d6c6-125">UI 逻辑位于视图中。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-125">The UI logic belongs in the view.</span></span> <span data-ttu-id="4d6c6-126">输入逻辑位于控制器中。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-126">Input logic belongs in the controller.</span></span> <span data-ttu-id="4d6c6-127">业务逻辑位于模型中。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-127">Business logic belongs in the model.</span></span> <span data-ttu-id="4d6c6-128">这种隔离有助于控制构建应用时的复杂程度，因为它可用于一次处理一个实现特性，而不影响其他特性的代码。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-128">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="4d6c6-129">例如，处理视图代码时不必依赖业务逻辑代码。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-129">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="4d6c6-130">本教程系列中介绍了这些概念，并展示了如何使用它们构建电影应用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-130">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="4d6c6-131">MVC 项目包含“控制器”和“视图”文件夹 。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-131">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4d6c6-132">添加控制器</span><span class="sxs-lookup"><span data-stu-id="4d6c6-132">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d6c6-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d6c6-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4d6c6-134">在“解决方案资源管理器”中，右击“控制器 > 添加 > 控制器”
  ![解决方案资源管理器，右击“控制器 > 添加 > 控制器”](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)</span><span class="sxs-lookup"><span data-stu-id="4d6c6-134">In the **Solution Explorer**, right-click **Controllers > Add > Controller**
![Solution Explorer, right click Controllers > Add > Controller](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)</span></span>

* <span data-ttu-id="4d6c6-135">在“添加基架”对话框中，选择“MVC 控制器 - 空” </span><span class="sxs-lookup"><span data-stu-id="4d6c6-135">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![添加 MVC 控制器并为其命名](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

* <span data-ttu-id="4d6c6-137">在“添加新项 - MvcMovie”对话框中，输入 HelloWorldController.cs，然后选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-137">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d6c6-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d6c6-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d6c6-139">选择“EXPLORER”图标，然后按住 Control 并单击（右键单击）“控制器”，选择“新建文件”，然后将新文件命名为 HelloWorldController.cs 。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-139">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![上下文菜单](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4d6c6-141">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4d6c6-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4d6c6-142">在“解决方案资源管理器”中，右键单击“控制器”，选择“添加”>“新文件”。 </span><span class="sxs-lookup"><span data-stu-id="4d6c6-142">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>
<span data-ttu-id="4d6c6-143">![上下文菜单](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span><span class="sxs-lookup"><span data-stu-id="4d6c6-143">![Contextual menu](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span></span>

<span data-ttu-id="4d6c6-144">选择“ASP.NET Core”和“控制器类”。 </span><span class="sxs-lookup"><span data-stu-id="4d6c6-144">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="4d6c6-145">将控制器命名为“HelloWorldController”。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-145">Name the controller **HelloWorldController**.</span></span>

![添加 MVC 控制器并为其命名](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="4d6c6-147">将“Controllers/HelloWorldController.cs”的内容替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-147">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="4d6c6-148">控制器中的每个 `public` 方法均可作为 HTTP 终结点调用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-148">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="4d6c6-149">上述示例中，两种方法均返回一个字符串。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-149">In the sample above, both methods return a string.</span></span> <span data-ttu-id="4d6c6-150">请注意每个方法前面的注释。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-150">Note the comments preceding each method.</span></span>

<span data-ttu-id="4d6c6-151">HTTP 终结点是 Web 应用程序中可定向的 URL（例如 `https://localhost:5001/HelloWorld`），其中结合了所用的协议 `HTTPS`、TCP 端口等 Web 服务器的网络位置 `localhost:5001`，以及目标 URI `HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-151">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="4d6c6-152">第一条注释指出这是一个 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 方法，它通过向基 URL 追加 `/HelloWorld/` 进行调用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-152">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="4d6c6-153">第二条注释指定一个 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 方法，它通过向 URL 追加 `/HelloWorld/Welcome/` 进行调用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-153">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="4d6c6-154">本教程稍后将使用基架引擎生成 `HTTP POST` 方法，用于更新数据。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-154">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="4d6c6-155">在非调试模式下运行应用，并将“HelloWorld”追加到地址栏中的路径。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-155">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="4d6c6-156">`Index` 方法返回一个字符串。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-156">The `Index` method returns a string.</span></span>

![显示“这是我的默认操作”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="4d6c6-158">MVC 根据入站 URL 调用控制器类（及其中的操作方法）。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-158">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="4d6c6-159">MVC 所用的默认 [URL 路由逻辑](xref:mvc/controllers/routing)使用如下格式来确定调用的代码：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-159">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="4d6c6-160">在 Startup.cs 文件的 `Configure` 方法中设置路由格式。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-160">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="4d6c6-161">如果浏览到应用且不提供任何 URL 段，它将默认为上面突出显示的模板行中指定的“Home”控制器和“Index”方法。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-161">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="4d6c6-162">第一个 URL 段决定要运行的控制器类。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-162">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="4d6c6-163">因此 `localhost:{PORT}/HelloWorld` 映射到 HelloWorld 控制器类。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-163">So `localhost:{PORT}/HelloWorld` maps to the **HelloWorld** Controller class.</span></span> <span data-ttu-id="4d6c6-164">该 URL 段的第二部分决定类上的操作方法。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-164">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="4d6c6-165">因此 `localhost:{PORT}/HelloWorld/Index` 将触发 `HelloWorldController` 类的 `Index` 运行。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-165">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="4d6c6-166">请注意，只需浏览到 `localhost:{PORT}/HelloWorld`，而 `Index` 方法默认调用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-166">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="4d6c6-167">原因是 `Index` 是默认方法，如果未显式指定方法名称，则将在控制器上调用它。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-167">That's because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="4d6c6-168">URL 段的第三部分 (`id`) 针对的是路由数据。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-168">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="4d6c6-169">本教程的后续部分中将介绍路由数据。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-169">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="4d6c6-170">浏览到 `https://localhost:{PORT}/HelloWorld/Welcome`。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-170">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="4d6c6-171">`Welcome` 方法将运行并返回字符串 `This is the Welcome action method...`。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-171">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="4d6c6-172">对于此 URL，采用 `HelloWorld` 控制器和 `Welcome` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-172">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="4d6c6-173">目前尚未使用 URL 的 `[Parameters]` 部分。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-173">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![显示“这是 Welcome 操作方法”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="4d6c6-175">修改代码，将一些参数信息从 URL 传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-175">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="4d6c6-176">例如 `/HelloWorld/Welcome?name=Rick&numtimes=4`。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-176">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="4d6c6-177">更改 `Welcome` 方法以包括以下代码中显示的两个参数：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-177">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="4d6c6-178">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-178">The preceding code:</span></span>

* <span data-ttu-id="4d6c6-179">使用 C# 可选参数功能指示，未为 `numTimes` 参数传递值时该参数默认为 1。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-179">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="4d6c6-180">使用 `HtmlEncoder.Default.Encode` 防止恶意输入（即 JavaScript）损害应用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-180">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="4d6c6-181">在 `$"Hello {name}, NumTimes is: {numTimes}"` 中使用[内插字符串](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-181">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="4d6c6-182">运行应用并浏览到：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-182">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="4d6c6-183">（将 `{PORT}` 替换为端口号。）可在 URL 中对 `name` 和 `numtimes` 使用其他值。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-183">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="4d6c6-184">MVC [模型绑定](xref:mvc/models/model-binding)系统可将命名参数从地址栏中的查询字符串自动映射到方法中的参数。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-184">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="4d6c6-185">有关详细信息，请参阅[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-185">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![显示 Hello Rick 的应用程序响应的浏览器窗口，NumTimes 为 \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="4d6c6-187">在上图中，未使用 URL 段 (`Parameters`)，且在[查询字符串](https://wikipedia.org/wiki/Query_string)中传递 `name` 和 `numTimes` 参数。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-187">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="4d6c6-188">上述 URL 中的 `?`（问号）为分隔符，后接查询字符串。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-188">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="4d6c6-189">`&` 字符将字段/值对分隔开。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-189">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="4d6c6-190">将 `Welcome` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-190">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="4d6c6-191">运行应用并输入以下 URL：`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="4d6c6-191">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="4d6c6-192">此时，第三个 URL 段与路由参数 `id` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-192">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="4d6c6-193">`Welcome` 方法包含 `MapControllerRoute` 方法中匹配 URL 模板的参数 `id`。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-193">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span> <span data-ttu-id="4d6c6-194">后面的 `?`（`id?` 中）表示 `id` 参数可选。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-194">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="4d6c6-195">上述示例中，控制器始终执行 MVC 的“VC”部分，即视图和控制器工作 。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-195">In these examples the controller has been doing the "VC" portion of MVC - that is, the **V** iew and the **C** ontroller work.</span></span> <span data-ttu-id="4d6c6-196">控制器将直接返回 HTML。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-196">The controller is returning HTML directly.</span></span> <span data-ttu-id="4d6c6-197">通常不希望控制器直接返回 HTML，因为编码和维护非常繁琐。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-197">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="4d6c6-198">通常，需使用单独的 Razor 视图模板文件来生成 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-198">Instead you typically use a separate Razor view template file to generate the HTML response.</span></span> <span data-ttu-id="4d6c6-199">可在下一教程中执行该操作。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-199">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4d6c6-200">[上一页](start-mvc.md)
> [下一页](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="4d6c6-200">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4d6c6-201">模型-视图-控制器 (MVC) 体系结构模式将应用分成 3 个主要组件：模型 (M)、视图 (V) 和控制器 (C)  。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-201">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="4d6c6-202">MVC 模式有助于创建比传统单片应用更易于测试和更新的应用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-202">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="4d6c6-203">基于 MVC 的应用包含：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-203">MVC-based apps contain:</span></span>

* <span data-ttu-id="4d6c6-204">模型 (M)：表示应用数据的类。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-204">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="4d6c6-205">模型类使用验证逻辑来对该数据强制实施业务规则。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-205">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="4d6c6-206">通常，模型对象检索模型状态并将其存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-206">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="4d6c6-207">本教程中，`Movie` 模型将从数据库中检索电影数据，并将其提供给视图或对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-207">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="4d6c6-208">更新后的数据将写入到数据库。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-208">Updated data is written to a database.</span></span>

* <span data-ttu-id="4d6c6-209">视图 (V)：视图是显示应用用户界面 (UI) 的组件。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-209">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="4d6c6-210">此 UI 通常会显示模型数据。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-210">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="4d6c6-211">控制器 (C)：处理浏览器请求的类。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-211">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="4d6c6-212">它们检索模型数据并调用返回响应的视图模板。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-212">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="4d6c6-213">在 MVC 应用中，视图仅显示信息；控制器处理并响应用户输入和交互。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-213">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="4d6c6-214">例如，控制器处理路由数据和查询字符串值，并将这些值传递给模型。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-214">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="4d6c6-215">该模型可使用这些值查询数据库。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-215">The model might use these values to query the database.</span></span> <span data-ttu-id="4d6c6-216">例如，`https://localhost:5001/Home/About` 具有 `Home`（控制器）的路由数据和 `About`（在 Home 控制器上调用的操作方法）。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-216">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="4d6c6-217">`https://localhost:5001/Movies/Edit/5` 是一个请求，用于通过电影控制器编辑 ID 为 5 的电影。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-217">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="4d6c6-218">本教程的后续部分中将介绍路由数据。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-218">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="4d6c6-219">MVC 模式可帮助创建分隔不同应用特性（输入逻辑、业务逻辑和 UI 逻辑）的应用，同时让这些元素之间实现松散耦合。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-219">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="4d6c6-220">该模式可指定应用中每种逻辑的位置。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-220">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="4d6c6-221">UI 逻辑位于视图中。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-221">The UI logic belongs in the view.</span></span> <span data-ttu-id="4d6c6-222">输入逻辑位于控制器中。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-222">Input logic belongs in the controller.</span></span> <span data-ttu-id="4d6c6-223">业务逻辑位于模型中。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-223">Business logic belongs in the model.</span></span> <span data-ttu-id="4d6c6-224">这种隔离有助于控制构建应用时的复杂程度，因为它可用于一次处理一个实现特性，而不影响其他特性的代码。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-224">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="4d6c6-225">例如，处理视图代码时不必依赖业务逻辑代码。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-225">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="4d6c6-226">本教程系列中介绍了这些概念，并展示了如何使用它们构建电影应用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-226">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="4d6c6-227">MVC 项目包含“控制器”和“视图”文件夹 。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-227">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4d6c6-228">添加控制器</span><span class="sxs-lookup"><span data-stu-id="4d6c6-228">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d6c6-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d6c6-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4d6c6-230">在“解决方案资源管理器”中，右键单击“控制器”，然后单击“添加”>“控制器”
  ![上下文菜单](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png) </span><span class="sxs-lookup"><span data-stu-id="4d6c6-230">In **Solution Explorer**, right-click **Controllers > Add > Controller**
![Contextual menu](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)</span></span>

* <span data-ttu-id="4d6c6-231">在“添加基架”对话框中，选择“MVC 控制器 - 空” </span><span class="sxs-lookup"><span data-stu-id="4d6c6-231">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![添加 MVC 控制器并为其命名](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="4d6c6-233">在“添加空 MVC 控制器”对话框中，输入 HelloWorldController 并选择“ADD”  。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-233">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d6c6-234">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d6c6-234">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4d6c6-235">选择“EXPLORER”图标，然后按住 Control 并单击（右键单击）“控制器”，选择“新建文件”，然后将新文件命名为 HelloWorldController.cs 。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-235">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![上下文菜单](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4d6c6-237">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4d6c6-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4d6c6-238">在“解决方案资源管理器”中，右键单击“控制器”，选择“添加”>“新文件”。 </span><span class="sxs-lookup"><span data-stu-id="4d6c6-238">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>
<span data-ttu-id="4d6c6-239">![上下文菜单](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span><span class="sxs-lookup"><span data-stu-id="4d6c6-239">![Contextual menu](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span></span>

<span data-ttu-id="4d6c6-240">选择“ASP.NET Core”和“MVC 控制器类”。 </span><span class="sxs-lookup"><span data-stu-id="4d6c6-240">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="4d6c6-241">将控制器命名为“HelloWorldController”。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-241">Name the controller **HelloWorldController**.</span></span>

![添加 MVC 控制器并为其命名](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="4d6c6-243">将“Controllers/HelloWorldController.cs”的内容替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-243">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="4d6c6-244">控制器中的每个 `public` 方法均可作为 HTTP 终结点调用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-244">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="4d6c6-245">上述示例中，两种方法均返回一个字符串。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-245">In the sample above, both methods return a string.</span></span> <span data-ttu-id="4d6c6-246">请注意每个方法前面的注释。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-246">Note the comments preceding each method.</span></span>

<span data-ttu-id="4d6c6-247">HTTP 终结点是 Web 应用程序中可定向的 URL（例如 `https://localhost:5001/HelloWorld`），其中结合了所用的协议 `HTTPS`、TCP 端口等 Web 服务器的网络位置 `localhost:5001`，以及目标 URI `HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-247">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="4d6c6-248">第一条注释指出这是一个 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 方法，它通过向基 URL 追加 `/HelloWorld/` 进行调用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-248">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="4d6c6-249">第二条注释指定一个 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 方法，它通过向 URL 追加 `/HelloWorld/Welcome/` 进行调用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-249">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="4d6c6-250">本教程稍后将使用基架引擎生成 `HTTP POST` 方法，用于更新数据。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-250">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="4d6c6-251">在非调试模式下运行应用，并将“HelloWorld”追加到地址栏中的路径。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-251">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="4d6c6-252">`Index` 方法返回一个字符串。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-252">The `Index` method returns a string.</span></span>

![显示“这是我的默认操作”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="4d6c6-254">MVC 根据入站 URL 调用控制器类（及其中的操作方法）。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-254">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="4d6c6-255">MVC 所用的默认 [URL 路由逻辑](xref:mvc/controllers/routing)使用如下格式来确定调用的代码：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-255">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="4d6c6-256">在 Startup.cs 文件的 `Configure` 方法中设置路由格式。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-256">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="4d6c6-257">如果浏览到应用且不提供任何 URL 段，它将默认为上面突出显示的模板行中指定的“Home”控制器和“Index”方法。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-257">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="4d6c6-258">第一个 URL 段决定要运行的控制器类。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-258">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="4d6c6-259">因此 `localhost:{PORT}/HelloWorld` 映射到 `HelloWorldController` 类。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-259">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="4d6c6-260">该 URL 段的第二部分决定类上的操作方法。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-260">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="4d6c6-261">因此 `localhost:{PORT}/HelloWorld/Index` 将触发 `HelloWorldController` 类的 `Index` 运行。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-261">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="4d6c6-262">请注意，只需浏览到 `localhost:{PORT}/HelloWorld`，而 `Index` 方法默认调用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-262">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="4d6c6-263">原因是 `Index` 是默认方法，如果未显式指定方法名称，则将在控制器上调用它。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-263">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="4d6c6-264">URL 段的第三部分 (`id`) 针对的是路由数据。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-264">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="4d6c6-265">本教程的后续部分中将介绍路由数据。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-265">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="4d6c6-266">浏览到 `https://localhost:{PORT}/HelloWorld/Welcome`。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-266">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="4d6c6-267">`Welcome` 方法将运行并返回字符串 `This is the Welcome action method...`。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-267">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="4d6c6-268">对于此 URL，采用 `HelloWorld` 控制器和 `Welcome` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-268">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="4d6c6-269">目前尚未使用 URL 的 `[Parameters]` 部分。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-269">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![显示“这是 Welcome 操作方法”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="4d6c6-271">修改代码，将一些参数信息从 URL 传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-271">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="4d6c6-272">例如 `/HelloWorld/Welcome?name=Rick&numtimes=4`。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-272">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="4d6c6-273">更改 `Welcome` 方法以包括以下代码中显示的两个参数：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-273">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="4d6c6-274">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-274">The preceding code:</span></span>

* <span data-ttu-id="4d6c6-275">使用 C# 可选参数功能指示，未为 `numTimes` 参数传递值时该参数默认为 1。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-275">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="4d6c6-276">使用 `HtmlEncoder.Default.Encode` 防止恶意输入（即 JavaScript）损害应用。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-276">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="4d6c6-277">在 `$"Hello {name}, NumTimes is: {numTimes}"` 中使用[内插字符串](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-277">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="4d6c6-278">运行应用并浏览到：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-278">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="4d6c6-279">（将 `{PORT}` 替换为端口号。）可在 URL 中对 `name` 和 `numtimes` 使用其他值。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-279">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="4d6c6-280">MVC [模型绑定](xref:mvc/models/model-binding)系统可将命名参数从地址栏中的查询字符串自动映射到方法中的参数。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-280">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="4d6c6-281">有关详细信息，请参阅[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-281">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![显示 Hello Rick 的应用程序响应的浏览器窗口，NumTimes 为 \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="4d6c6-283">在上图中，未使用 URL 段 (`Parameters`)，且在[查询字符串](https://wikipedia.org/wiki/Query_string)中传递 `name` 和 `numTimes` 参数。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-283">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="4d6c6-284">上述 URL 中的 `?`（问号）为分隔符，后接查询字符串。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-284">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="4d6c6-285">`&` 字符将字段/值对分隔开。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-285">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="4d6c6-286">将 `Welcome` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4d6c6-286">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="4d6c6-287">运行应用并输入以下 URL：`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="4d6c6-287">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="4d6c6-288">此时，第三个 URL 段与路由参数 `id` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-288">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="4d6c6-289">`Welcome` 方法包含 `MapRoute` 方法中匹配 URL 模板的参数 `id`。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-289">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="4d6c6-290">后面的 `?`（`id?` 中）表示 `id` 参数可选。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-290">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="4d6c6-291">上述示例中，控制器始终执行 MVC 的“VC”部分，即视图和控制器工作。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-291">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="4d6c6-292">控制器将直接返回 HTML。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-292">The controller is returning HTML directly.</span></span> <span data-ttu-id="4d6c6-293">通常不希望控制器直接返回 HTML，因为编码和维护非常繁琐。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-293">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="4d6c6-294">通常，需使用单独的 Razor 视图模板文件来帮助生成 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-294">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="4d6c6-295">可在下一教程中执行该操作。</span><span class="sxs-lookup"><span data-stu-id="4d6c6-295">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4d6c6-296">[上一页](start-mvc.md)
> [下一页](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="4d6c6-296">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end
