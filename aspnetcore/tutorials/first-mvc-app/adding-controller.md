---
title: 第 2 部分，将控制器添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: ASP.NET Core MVC 教程系列第 2 部分。
ms.author: riande
ms.date: 08/05/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: 30272900b2ff19879921f1954a1c9b3eba203ca4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400448"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a>第 2 部分，将控制器添加到 ASP.NET Core MVC 应用

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

模型-视图-控制器 (MVC) 体系结构模式将应用分成 3 个主要组件：模型 (M)、视图 (V) 和控制器 (C)  。 MVC 模式有助于创建比传统单片应用更易于测试和更新的应用。 基于 MVC 的应用包含：

* 模型 (M)：表示应用数据的类。 模型类使用验证逻辑来对该数据强制实施业务规则。 通常，模型对象检索模型状态并将其存储在数据库中。 本教程中，`Movie` 模型将从数据库中检索电影数据，并将其提供给视图或对其进行更新。 更新后的数据将写入到数据库。

* 视图 (V)：视图是显示应用用户界面 (UI) 的组件。 此 UI 通常会显示模型数据。

* 控制器 (C)：处理浏览器请求的类。 它们检索模型数据并调用返回响应的视图模板。 在 MVC 应用中，视图仅显示信息；控制器处理并响应用户输入和交互。 例如，控制器处理路由数据和查询字符串值，并将这些值传递给模型。 该模型可使用这些值查询数据库。 例如，`https://localhost:5001/Home/Privacy` 具有 `Home`（控制器）的路由数据和 `Privacy`（在 Home 控制器上调用的操作方法）。 `https://localhost:5001/Movies/Edit/5` 是一个请求，用于通过电影控制器编辑 ID 为 5 的电影。 本教程的后续部分中将介绍路由数据。

MVC 模式可帮助创建分隔不同应用特性（输入逻辑、业务逻辑和 UI 逻辑）的应用，同时让这些元素之间实现松散耦合。 该模式可指定应用中每种逻辑的位置。 UI 逻辑位于视图中。 输入逻辑位于控制器中。 业务逻辑位于模型中。 这种隔离有助于控制构建应用时的复杂程度，因为它可用于一次处理一个实现特性，而不影响其他特性的代码。 例如，处理视图代码时不必依赖业务逻辑代码。

本教程系列中介绍了这些概念，并展示了如何使用它们构建电影应用。 MVC 项目包含“控制器”和“视图”文件夹 。

## <a name="add-a-controller"></a>添加控制器

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在“解决方案资源管理器”中，右键单击“控制器”，然后单击“添加”>“控制器”
  ![上下文菜单](adding-controller/_static/add_controller.png) 

* 在“添加基架”对话框中，选择“MVC 控制器 - 空” 

  ![添加 MVC 控制器并为其命名](adding-controller/_static/ac.png)

* 在“添加空 MVC 控制器”对话框中，输入 HelloWorldController 并选择“ADD”  。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

选择“EXPLORER”图标，然后按住 Control 并单击（右键单击）“控制器”，选择“新建文件”，然后将新文件命名为 HelloWorldController.cs 。

  ![上下文菜单](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

在“解决方案资源管理器”中，右键单击“控制器”，选择“添加”>“新文件”。 
![上下文菜单](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

选择“ASP.NET Core”和“MVC 控制器类”。 

将控制器命名为“HelloWorldController”。

![添加 MVC 控制器并为其命名](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

将“Controllers/HelloWorldController.cs”的内容替换为以下内容：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

控制器中的每个 `public` 方法均可作为 HTTP 终结点调用。 上述示例中，两种方法均返回一个字符串。 请注意每个方法前面的注释。

HTTP 终结点是 Web 应用程序中可定向的 URL（例如 `https://localhost:5001/HelloWorld`），其中结合了所用的协议 `HTTPS`、TCP 端口等 Web 服务器的网络位置 `localhost:5001`，以及目标 URI `HelloWorld`。

第一条注释指出这是一个 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 方法，它通过向基 URL 追加 `/HelloWorld/` 进行调用。 第二条注释指定一个 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 方法，它通过向 URL 追加 `/HelloWorld/Welcome/` 进行调用。 本教程稍后将使用基架引擎生成 `HTTP POST` 方法，用于更新数据。

在非调试模式下运行应用，并将“HelloWorld”追加到地址栏中的路径。 `Index` 方法返回一个字符串。

![显示“这是我的默认操作”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC 根据入站 URL 调用控制器类（及其中的操作方法）。 MVC 所用的默认 [URL 路由逻辑](xref:mvc/controllers/routing)使用如下格式来确定调用的代码：

`/[Controller]/[ActionName]/[Parameters]`

在 Startup.cs 文件的 `Configure` 方法中设置路由格式。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

如果浏览到应用且不提供任何 URL 段，它将默认为上面突出显示的模板行中指定的“Home”控制器和“Index”方法。

第一个 URL 段决定要运行的控制器类。 因此 `localhost:{PORT}/HelloWorld` 映射到 HelloWorld 控制器类。 该 URL 段的第二部分决定类上的操作方法。 因此 `localhost:{PORT}/HelloWorld/Index` 将触发 `HelloWorldController` 类的 `Index` 运行。 请注意，只需浏览到 `localhost:{PORT}/HelloWorld`，而 `Index` 方法默认调用。 原因是 `Index` 是默认方法，如果未显式指定方法名称，则将在控制器上调用它。 URL 段的第三部分 (`id`) 针对的是路由数据。 本教程的后续部分中将介绍路由数据。

浏览到 `https://localhost:{PORT}/HelloWorld/Welcome`。 `Welcome` 方法将运行并返回字符串 `This is the Welcome action method...`。 对于此 URL，采用 `HelloWorld` 控制器和 `Welcome` 操作方法。 目前尚未使用 URL 的 `[Parameters]` 部分。

![显示“这是 Welcome 操作方法”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

修改代码，将一些参数信息从 URL 传递到控制器。 例如 `/HelloWorld/Welcome?name=Rick&numtimes=4`。 更改 `Welcome` 方法以包括以下代码中显示的两个参数：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

前面的代码：

* 使用 C# 可选参数功能指示，未为 `numTimes` 参数传递值时该参数默认为 1。 <!-- remove for simplified -->
* 使用 `HtmlEncoder.Default.Encode` 防止恶意输入（即 JavaScript）损害应用。
* 在 `$"Hello {name}, NumTimes is: {numTimes}"` 中使用[内插字符串](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)。 <!-- remove for simplified -->

运行应用并浏览到：

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

（将 `{PORT}` 替换为端口号。）可在 URL 中对 `name` 和 `numtimes` 使用其他值。 MVC [模型绑定](xref:mvc/models/model-binding)系统可将命名参数从地址栏中的查询字符串自动映射到方法中的参数。 有关详细信息，请参阅[模型绑定](xref:mvc/models/model-binding)。

![显示 Hello Rick 的应用程序响应的浏览器窗口，NumTimes 为 \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

在上图中，未使用 URL 段 (`Parameters`)，且在[查询字符串](https://wikipedia.org/wiki/Query_string)中传递 `name` 和 `numTimes` 参数。 上述 URL 中的 `?`（问号）为分隔符，后接查询字符串。 `&` 字符将字段/值对分隔开。

将 `Welcome` 方法替换为以下代码：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

运行应用并输入以下 URL：`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

此时，第三个 URL 段与路由参数 `id` 相匹配。 `Welcome` 方法包含 `MapControllerRoute` 方法中匹配 URL 模板的参数 `id`。 后面的 `?`（`id?` 中）表示 `id` 参数可选。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

上述示例中，控制器始终执行 MVC 的“VC”部分，即视图和控制器工作 。 控制器将直接返回 HTML。 通常不希望控制器直接返回 HTML，因为编码和维护非常繁琐。 通常，需使用单独的 Razor 视图模板文件来生成 HTML 响应。 可在下一教程中执行该操作。

> [!div class="step-by-step"]
> [上一页](start-mvc.md)
> [下一页](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

模型-视图-控制器 (MVC) 体系结构模式将应用分成 3 个主要组件：模型 (M)、视图 (V) 和控制器 (C)  。 MVC 模式有助于创建比传统单片应用更易于测试和更新的应用。 基于 MVC 的应用包含：

* 模型 (M)：表示应用数据的类。 模型类使用验证逻辑来对该数据强制实施业务规则。 通常，模型对象检索模型状态并将其存储在数据库中。 本教程中，`Movie` 模型将从数据库中检索电影数据，并将其提供给视图或对其进行更新。 更新后的数据将写入到数据库。

* 视图 (V)：视图是显示应用用户界面 (UI) 的组件。 此 UI 通常会显示模型数据。

* 控制器 (C)：处理浏览器请求的类。 它们检索模型数据并调用返回响应的视图模板。 在 MVC 应用中，视图仅显示信息；控制器处理并响应用户输入和交互。 例如，控制器处理路由数据和查询字符串值，并将这些值传递给模型。 该模型可使用这些值查询数据库。 例如，`https://localhost:5001/Home/About` 具有 `Home`（控制器）的路由数据和 `About`（在 Home 控制器上调用的操作方法）。 `https://localhost:5001/Movies/Edit/5` 是一个请求，用于通过电影控制器编辑 ID 为 5 的电影。 本教程的后续部分中将介绍路由数据。

MVC 模式可帮助创建分隔不同应用特性（输入逻辑、业务逻辑和 UI 逻辑）的应用，同时让这些元素之间实现松散耦合。 该模式可指定应用中每种逻辑的位置。 UI 逻辑位于视图中。 输入逻辑位于控制器中。 业务逻辑位于模型中。 这种隔离有助于控制构建应用时的复杂程度，因为它可用于一次处理一个实现特性，而不影响其他特性的代码。 例如，处理视图代码时不必依赖业务逻辑代码。

本教程系列中介绍了这些概念，并展示了如何使用它们构建电影应用。 MVC 项目包含“控制器”和“视图”文件夹 。

## <a name="add-a-controller"></a>添加控制器

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在“解决方案资源管理器”中，右键单击“控制器”，然后单击“添加”>“控制器”
  ![上下文菜单](adding-controller/_static/add_controller.png) 

* 在“添加基架”对话框中，选择“MVC 控制器 - 空” 

  ![添加 MVC 控制器并为其命名](adding-controller/_static/ac.png)

* 在“添加空 MVC 控制器”对话框中，输入 HelloWorldController 并选择“ADD”  。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

选择“EXPLORER”图标，然后按住 Control 并单击（右键单击）“控制器”，选择“新建文件”，然后将新文件命名为 HelloWorldController.cs 。

  ![上下文菜单](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

在“解决方案资源管理器”中，右键单击“控制器”，选择“添加”>“新文件”。 
![上下文菜单](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

选择“ASP.NET Core”和“MVC 控制器类”。 

将控制器命名为“HelloWorldController”。

![添加 MVC 控制器并为其命名](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

将“Controllers/HelloWorldController.cs”的内容替换为以下内容：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

控制器中的每个 `public` 方法均可作为 HTTP 终结点调用。 上述示例中，两种方法均返回一个字符串。 请注意每个方法前面的注释。

HTTP 终结点是 Web 应用程序中可定向的 URL（例如 `https://localhost:5001/HelloWorld`），其中结合了所用的协议 `HTTPS`、TCP 端口等 Web 服务器的网络位置 `localhost:5001`，以及目标 URI `HelloWorld`。

第一条注释指出这是一个 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 方法，它通过向基 URL 追加 `/HelloWorld/` 进行调用。 第二条注释指定一个 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 方法，它通过向 URL 追加 `/HelloWorld/Welcome/` 进行调用。 本教程稍后将使用基架引擎生成 `HTTP POST` 方法，用于更新数据。

在非调试模式下运行应用，并将“HelloWorld”追加到地址栏中的路径。 `Index` 方法返回一个字符串。

![显示“这是我的默认操作”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC 根据入站 URL 调用控制器类（及其中的操作方法）。 MVC 所用的默认 [URL 路由逻辑](xref:mvc/controllers/routing)使用如下格式来确定调用的代码：

`/[Controller]/[ActionName]/[Parameters]`

在 Startup.cs 文件的 `Configure` 方法中设置路由格式。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

如果浏览到应用且不提供任何 URL 段，它将默认为上面突出显示的模板行中指定的“Home”控制器和“Index”方法。

第一个 URL 段决定要运行的控制器类。 因此 `localhost:{PORT}/HelloWorld` 映射到 `HelloWorldController` 类。 该 URL 段的第二部分决定类上的操作方法。 因此 `localhost:{PORT}/HelloWorld/Index` 将触发 `HelloWorldController` 类的 `Index` 运行。 请注意，只需浏览到 `localhost:{PORT}/HelloWorld`，而 `Index` 方法默认调用。 原因是 `Index` 是默认方法，如果未显式指定方法名称，则将在控制器上调用它。 URL 段的第三部分 (`id`) 针对的是路由数据。 本教程的后续部分中将介绍路由数据。

浏览到 `https://localhost:{PORT}/HelloWorld/Welcome`。 `Welcome` 方法将运行并返回字符串 `This is the Welcome action method...`。 对于此 URL，采用 `HelloWorld` 控制器和 `Welcome` 操作方法。 目前尚未使用 URL 的 `[Parameters]` 部分。

![显示“这是 Welcome 操作方法”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

修改代码，将一些参数信息从 URL 传递到控制器。 例如 `/HelloWorld/Welcome?name=Rick&numtimes=4`。 更改 `Welcome` 方法以包括以下代码中显示的两个参数：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

前面的代码：

* 使用 C# 可选参数功能指示，未为 `numTimes` 参数传递值时该参数默认为 1。 <!-- remove for simplified -->
* 使用 `HtmlEncoder.Default.Encode` 防止恶意输入（即 JavaScript）损害应用。
* 在 `$"Hello {name}, NumTimes is: {numTimes}"` 中使用[内插字符串](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)。 <!-- remove for simplified -->

运行应用并浏览到：

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

（将 `{PORT}` 替换为端口号。）可在 URL 中对 `name` 和 `numtimes` 使用其他值。 MVC [模型绑定](xref:mvc/models/model-binding)系统可将命名参数从地址栏中的查询字符串自动映射到方法中的参数。 有关详细信息，请参阅[模型绑定](xref:mvc/models/model-binding)。

![显示 Hello Rick 的应用程序响应的浏览器窗口，NumTimes 为 \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

在上图中，未使用 URL 段 (`Parameters`)，且在[查询字符串](https://wikipedia.org/wiki/Query_string)中传递 `name` 和 `numTimes` 参数。 上述 URL 中的 `?`（问号）为分隔符，后接查询字符串。 `&` 字符将字段/值对分隔开。

将 `Welcome` 方法替换为以下代码：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

运行应用并输入以下 URL：`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

此时，第三个 URL 段与路由参数 `id` 相匹配。 `Welcome` 方法包含 `MapRoute` 方法中匹配 URL 模板的参数 `id`。 后面的 `?`（`id?` 中）表示 `id` 参数可选。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

上述示例中，控制器始终执行 MVC 的“VC”部分，即视图和控制器工作。 控制器将直接返回 HTML。 通常不希望控制器直接返回 HTML，因为编码和维护非常繁琐。 通常，需使用单独的 Razor 视图模板文件来帮助生成 HTML 响应。 可在下一教程中执行该操作。

> [!div class="step-by-step"]
> [上一页](start-mvc.md)
> [下一页](adding-view.md)

::: moniker-end
