---
title: 将控制器添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: 了解如何将控制器添加到简单的 ASP.NET Core MVC 应用。
ms.author: riande
ms.date: 02/28/2017
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: f28dc96b66fce736242d26a2584fea0a23375e23
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815197"
---
# <a name="add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="8e4ef-103">将控制器添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="8e4ef-103">Add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="8e4ef-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8e4ef-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8e4ef-105">模型-视图-控制器 (MVC) 体系结构模式将应用分成 3 个主要组件：模型 (M)、视图 (V) 和控制器 (C)    。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M**odel, **V**iew, and **C**ontroller.</span></span> <span data-ttu-id="8e4ef-106">MVC 模式有助于创建比传统单片应用更易于测试和更新的应用。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="8e4ef-107">基于 MVC 的应用包含：</span><span class="sxs-lookup"><span data-stu-id="8e4ef-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="8e4ef-108">模型 (M)  ：表示应用数据的类。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-108">**M**odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="8e4ef-109">模型类使用验证逻辑来对该数据强制实施业务规则。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="8e4ef-110">通常，模型对象检索模型状态并将其存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="8e4ef-111">本教程中，`Movie` 模型将从数据库中检索电影数据，并将其提供给视图或对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="8e4ef-112">更新后的数据将写入到数据库。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-112">Updated data is written to a database.</span></span>

* <span data-ttu-id="8e4ef-113">视图 (V)  ：视图是显示应用用户界面 (UI) 的组件。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-113">**V**iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="8e4ef-114">此 UI 通常会显示模型数据。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-114">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="8e4ef-115">控制器 (C)  ：处理浏览器请求的类。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-115">**C**ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="8e4ef-116">它们检索模型数据并调用返回响应的视图模板。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-116">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="8e4ef-117">在 MVC 应用中，视图仅显示信息；控制器处理并响应用户输入和交互。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-117">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="8e4ef-118">例如，控制器处理路由数据和查询字符串值，并将这些值传递给模型。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-118">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="8e4ef-119">该模型可使用这些值查询数据库。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-119">The model might use these values to query the database.</span></span> <span data-ttu-id="8e4ef-120">例如，`https://localhost:1234/Home/About` 具有 `Home`（控制器）的路由数据和 `About`（在 Home 控制器上调用的操作方法）。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-120">For example, `https://localhost:1234/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="8e4ef-121">`https://localhost:1234/Movies/Edit/5` 是一个请求，用于通过电影控制器编辑 ID 为 5 的电影。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-121">`https://localhost:1234/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="8e4ef-122">本教程的后续部分中将介绍路由数据。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-122">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="8e4ef-123">MVC 模式可帮助创建分隔不同应用特性（输入逻辑、业务逻辑和 UI 逻辑）的应用，同时让这些元素之间实现松散耦合。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-123">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="8e4ef-124">该模式可指定应用中每种逻辑的位置。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-124">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="8e4ef-125">UI 逻辑位于视图中。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-125">The UI logic belongs in the view.</span></span> <span data-ttu-id="8e4ef-126">输入逻辑位于控制器中。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-126">Input logic belongs in the controller.</span></span> <span data-ttu-id="8e4ef-127">业务逻辑位于模型中。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-127">Business logic belongs in the model.</span></span> <span data-ttu-id="8e4ef-128">这种隔离有助于控制构建应用时的复杂程度，因为它可用于一次处理一个实现特性，而不影响其他特性的代码。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-128">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="8e4ef-129">例如，处理视图代码时不必依赖业务逻辑代码。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-129">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="8e4ef-130">本教程系列中介绍了这些概念，并展示了如何使用它们构建电影应用。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-130">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="8e4ef-131">MVC 项目包含“控制器”和“视图”文件夹   。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-131">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="8e4ef-132">添加控制器</span><span class="sxs-lookup"><span data-stu-id="8e4ef-132">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8e4ef-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e4ef-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8e4ef-134">在“解决方案资源管理器”中，右键单击“控制器”，然后单击“添加”>“控制器”
  ![上下文菜单](adding-controller/_static/add_controller.png)  </span><span class="sxs-lookup"><span data-stu-id="8e4ef-134">In **Solution Explorer**, right-click **Controllers > Add > Controller**
![Contextual menu](adding-controller/_static/add_controller.png)</span></span>

* <span data-ttu-id="8e4ef-135">在“添加基架”对话框中，选择“MVC 控制器 - 空”  </span><span class="sxs-lookup"><span data-stu-id="8e4ef-135">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![添加 MVC 控制器并为其命名](adding-controller/_static/ac.png)

* <span data-ttu-id="8e4ef-137">在“添加空 MVC 控制器”对话框中，输入 HelloWorldController 并选择“ADD”    。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-137">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8e4ef-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8e4ef-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8e4ef-139">选择“EXPLORER”图标，然后按住 Control 并单击（右键单击）“控制器”，选择“新建文件”，然后将新文件命名为 HelloWorldController.cs    。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-139">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![上下文菜单](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8e4ef-141">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8e4ef-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8e4ef-142">在“解决方案资源管理器”中，右键单击“控制器”，选择“添加”>“新文件”。  </span><span class="sxs-lookup"><span data-stu-id="8e4ef-142">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>
<span data-ttu-id="8e4ef-143">![上下文菜单](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span><span class="sxs-lookup"><span data-stu-id="8e4ef-143">![Contextual menu](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span></span>

<span data-ttu-id="8e4ef-144">选择“ASP.NET Core”和“MVC 控制器类”。  </span><span class="sxs-lookup"><span data-stu-id="8e4ef-144">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="8e4ef-145">将控制器命名为“HelloWorldController”。 </span><span class="sxs-lookup"><span data-stu-id="8e4ef-145">Name the controller **HelloWorldController**.</span></span>

![添加 MVC 控制器并为其命名](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="8e4ef-147">将“Controllers/HelloWorldController.cs”的内容替换为以下内容  ：</span><span class="sxs-lookup"><span data-stu-id="8e4ef-147">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="8e4ef-148">控制器中的每个 `public` 方法均可作为 HTTP 终结点调用。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-148">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="8e4ef-149">上述示例中，两种方法均返回一个字符串。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-149">In the sample above, both methods return a string.</span></span> <span data-ttu-id="8e4ef-150">请注意每个方法前面的注释。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-150">Note the comments preceding each method.</span></span>

<span data-ttu-id="8e4ef-151">HTTP 终结点是 Web 应用程序中可定向的 URL（例如 `https://localhost:5001/HelloWorld`），其中结合了所用的协议 `HTTPS`、TCP 端口等 Web 服务器的网络位置 `localhost:5001`，以及目标 URI `HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-151">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="8e4ef-152">第一条注释指出这是一个 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 方法，它通过向基 URL 追加 `/HelloWorld/` 进行调用。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-152">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="8e4ef-153">第二条注释指定一个 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 方法，它通过向 URL 追加 `/HelloWorld/Welcome/` 进行调用。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-153">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="8e4ef-154">本教程稍后将使用基架引擎生成 `HTTP POST` 方法，用于更新数据。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-154">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="8e4ef-155">在非调试模式下运行应用，并将“HelloWorld”追加到地址栏中的路径。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-155">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="8e4ef-156">`Index` 方法返回一个字符串。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-156">The `Index` method returns a string.</span></span>

![显示“这是我的默认操作”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="8e4ef-158">MVC 根据入站 URL 调用控制器类（及其中的操作方法）。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-158">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="8e4ef-159">MVC 所用的默认 [URL 路由逻辑](xref:mvc/controllers/routing)使用如下格式来确定调用的代码：</span><span class="sxs-lookup"><span data-stu-id="8e4ef-159">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="8e4ef-160">在 Startup.cs 文件的 `Configure` 方法中设置路由格式  。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-160">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="8e4ef-161">如果浏览到应用且不提供任何 URL 段，它将默认为上面突出显示的模板行中指定的“Home”控制器和“Index”方法。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-161">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="8e4ef-162">第一个 URL 段决定要运行的控制器类。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-162">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="8e4ef-163">因此 `localhost:xxxx/HelloWorld` 映射到 `HelloWorldController` 类。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-163">So `localhost:xxxx/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="8e4ef-164">该 URL 段的第二部分决定类上的操作方法。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-164">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="8e4ef-165">因此 `localhost:xxxx/HelloWorld/Index` 将触发 `HelloWorldController` 类的 `Index` 运行。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-165">So `localhost:xxxx/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="8e4ef-166">请注意，只需浏览到 `localhost:xxxx/HelloWorld`，而 `Index` 方法默认调用。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-166">Notice that you only had to browse to `localhost:xxxx/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="8e4ef-167">原因是 `Index` 是默认方法，如果未显式指定方法名称，则将在控制器上调用它。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-167">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="8e4ef-168">URL 段的第三部分 (`id`) 针对的是路由数据。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-168">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="8e4ef-169">本教程的后续部分中将介绍路由数据。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-169">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="8e4ef-170">浏览到 `https://localhost:xxxx/HelloWorld/Welcome`。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-170">Browse to `https://localhost:xxxx/HelloWorld/Welcome`.</span></span> <span data-ttu-id="8e4ef-171">`Welcome` 方法将运行并返回字符串 `This is the Welcome action method...`。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-171">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="8e4ef-172">对于此 URL，采用 `HelloWorld` 控制器和 `Welcome` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-172">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="8e4ef-173">目前尚未使用 URL 的 `[Parameters]` 部分。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-173">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![显示“这是 Welcome 操作方法”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="8e4ef-175">修改代码，将一些参数信息从 URL 传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-175">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="8e4ef-176">例如 `/HelloWorld/Welcome?name=Rick&numtimes=4`。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-176">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="8e4ef-177">更改 `Welcome` 方法以包括以下代码中显示的两个参数：</span><span class="sxs-lookup"><span data-stu-id="8e4ef-177">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="8e4ef-178">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="8e4ef-178">The preceding code:</span></span>

* <span data-ttu-id="8e4ef-179">使用 C# 可选参数功能指示，未为 `numTimes` 参数传递值时该参数默认为 1。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-179">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="8e4ef-180">使用 `HtmlEncoder.Default.Encode` 防止恶意输入（即 JavaScript）损害应用。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-180">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="8e4ef-181">在 `$"Hello {name}, NumTimes is: {numTimes}"` 中使用[内插字符串](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-181">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="8e4ef-182">运行应用并浏览到：</span><span class="sxs-lookup"><span data-stu-id="8e4ef-182">Run the app and browse to:</span></span>

   `https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="8e4ef-183">（将 xxxx 替换为端口号。）可在 URL 中对 `name` 和 `numtimes` 使用其他值。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-183">(Replace xxxx with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="8e4ef-184">MVC [模型绑定](xref:mvc/models/model-binding)系统可将命名参数从地址栏中的查询字符串自动映射到方法中的参数。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-184">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="8e4ef-185">有关详细信息，请参阅[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-185">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![显示应用程序响应的浏览器窗口，响应为：你好 Rick，NumTimes 为4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="8e4ef-187">在上图中，未使用 URL 段 (`Parameters`)，且 `name` 和 `numTimes` 参数作为[查询字符串](https://wikipedia.org/wiki/Query_string)进行传递。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-187">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed as [query strings](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="8e4ef-188">上述 URL 中的 `?`（问号）为分隔符，后接查询字符串。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-188">The `?` (question mark) in the above URL is a separator, and the query strings follow.</span></span> <span data-ttu-id="8e4ef-189">`&` 字符用于分隔查询字符串。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-189">The `&` character separates query strings.</span></span>

<span data-ttu-id="8e4ef-190">将 `Welcome` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="8e4ef-190">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="8e4ef-191">运行应用并输入以下 URL：`https://localhost:xxx/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="8e4ef-191">Run the app and enter the following URL: `https://localhost:xxx/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="8e4ef-192">此时，第三个 URL 段与路由参数 `id` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-192">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="8e4ef-193">`Welcome` 方法包含 `MapRoute` 方法中匹配 URL 模板的参数 `id`。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-193">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="8e4ef-194">后面的 `?`（`id?` 中）表示 `id` 参数可选。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-194">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="8e4ef-195">上述示例中，控制器始终执行 MVC 的“VC”部分，即视图和控制器工作。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-195">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="8e4ef-196">控制器将直接返回 HTML。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-196">The controller is returning HTML directly.</span></span> <span data-ttu-id="8e4ef-197">通常不希望控制器直接返回 HTML，因为编码和维护非常繁琐。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-197">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="8e4ef-198">通常，需使用单独的 Razor 视图模板文件来帮助生成 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-198">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="8e4ef-199">可在下一教程中执行该操作。</span><span class="sxs-lookup"><span data-stu-id="8e4ef-199">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8e4ef-200">[上一页](start-mvc.md)
> [下一页](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="8e4ef-200">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>
