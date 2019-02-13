---
title: 开始使用 Blazor
author: guardrex
description: 了解如何通过创建和修改 Blazor 项目来开始使用 Blazor。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: spa/blazor/get-started
ms.openlocfilehash: 8c984bab8a13b4fc2d87fd1a7e0b285dfa25ba09
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159574"
---
# <a name="get-started-with-blazor"></a><span data-ttu-id="a20b8-103">开始使用 Blazor</span><span class="sxs-lookup"><span data-stu-id="a20b8-103">Get started with Blazor</span></span>

<span data-ttu-id="a20b8-104">通过[Daniel Roth](https://github.com/danroth27)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a20b8-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a20b8-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a20b8-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a20b8-106">先决条件：</span><span class="sxs-lookup"><span data-stu-id="a20b8-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="a20b8-107">在 Visual Studio 中创建第一个 Blazor 项目：</span><span class="sxs-lookup"><span data-stu-id="a20b8-107">To create your first Blazor project in Visual Studio:</span></span>

1. <span data-ttu-id="a20b8-108">安装最新[Blazor 语言服务扩展](https://go.microsoft.com/fwlink/?linkid=870389)从 Visual Studio Marketplace。</span><span class="sxs-lookup"><span data-stu-id="a20b8-108">Install the latest [Blazor Language Services extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="a20b8-109">此步骤中使 Blazor 模板可供 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="a20b8-109">This step makes Blazor templates available to Visual Studio.</span></span>
1. <span data-ttu-id="a20b8-110">请 Blazor 模板可用于使用.NET Core CLI 与通过在命令行界面中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="a20b8-110">Make the Blazor templates available for use with the .NET Core CLI by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates
   ```

1. <span data-ttu-id="a20b8-111">选择**文件** > **新建项目** > **Web** > **ASP.NET Core Web 应用程序**。</span><span class="sxs-lookup"><span data-stu-id="a20b8-111">Select **File** > **New Project** > **Web** > **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="a20b8-112">请确保 **.NET Core**并**ASP.NET Core 3.0**顶部选择。</span><span class="sxs-lookup"><span data-stu-id="a20b8-112">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="a20b8-113">选择“Blazor”模板，然后选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="a20b8-113">Choose the **Blazor** template and select **OK**.</span></span>
1. <span data-ttu-id="a20b8-114">按 F5  运行应用。</span><span class="sxs-lookup"><span data-stu-id="a20b8-114">Press **F5** to run the app.</span></span>

<span data-ttu-id="a20b8-115">祝贺你！</span><span class="sxs-lookup"><span data-stu-id="a20b8-115">Congratulations!</span></span> <span data-ttu-id="a20b8-116">只需运行第一个 Blazor 应用 ！</span><span class="sxs-lookup"><span data-stu-id="a20b8-116">You just ran your first Blazor app!</span></span>

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Blazor project in Visual Studio Code:

1. Execute the following command in a command shell:

   ```console
   dotnet new blazor -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Visual Studio code offers to create assets to build and debug the app, which includes the *tasks.json* and *launch.json* files. Select **Yes** to add the assets.

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Blazor app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Blazor project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **Blazor** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Blazor app!
-->

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a20b8-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a20b8-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="a20b8-118">先决条件：</span><span class="sxs-lookup"><span data-stu-id="a20b8-118">Prerequisites:</span></span>

* [<span data-ttu-id="a20b8-119">.NET core SDK 3.0 预览</span><span class="sxs-lookup"><span data-stu-id="a20b8-119">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="a20b8-120">通过在命令行界面中运行以下命令添加 Blazor 模板：</span><span class="sxs-lookup"><span data-stu-id="a20b8-120">Add the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates
   ```

1. <span data-ttu-id="a20b8-121">在命令行界面中创建第一个 Blazor 项目：</span><span class="sxs-lookup"><span data-stu-id="a20b8-121">Create your first Blazor project in a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="a20b8-122">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="a20b8-122">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="a20b8-123">祝贺你！</span><span class="sxs-lookup"><span data-stu-id="a20b8-123">Congratulations!</span></span> <span data-ttu-id="a20b8-124">只需运行第一个 Blazor 应用 ！</span><span class="sxs-lookup"><span data-stu-id="a20b8-124">You just ran your first Blazor app!</span></span>

---

## <a name="blazor-project"></a><span data-ttu-id="a20b8-125">Blazor 项目</span><span class="sxs-lookup"><span data-stu-id="a20b8-125">Blazor project</span></span>

<span data-ttu-id="a20b8-126">当应用运行时，多个页侧栏中的选项卡中有：</span><span class="sxs-lookup"><span data-stu-id="a20b8-126">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="a20b8-127">主页</span><span class="sxs-lookup"><span data-stu-id="a20b8-127">Home</span></span>
* <span data-ttu-id="a20b8-128">计数器</span><span class="sxs-lookup"><span data-stu-id="a20b8-128">Counter</span></span>
* <span data-ttu-id="a20b8-129">提取数据</span><span class="sxs-lookup"><span data-stu-id="a20b8-129">Fetch data</span></span>

<span data-ttu-id="a20b8-130">在计数器页上，选择**Click me**按钮以递增的计数器，而无需刷新页面。</span><span class="sxs-lookup"><span data-stu-id="a20b8-130">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="a20b8-131">使计数器递增一网页中通常需要编写 JavaScript，但 Blazor 提供更好的方法使用C#。</span><span class="sxs-lookup"><span data-stu-id="a20b8-131">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

<span data-ttu-id="a20b8-132">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a20b8-132">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

<span data-ttu-id="a20b8-133">请求`/counter`中指定的浏览器，通过`@page`指令在顶部，会导致计数器组件来呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="a20b8-133">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="a20b8-134">组件将使用灵活且高效的方式更新 UI 的呈现器树的内存中表示形式呈现。</span><span class="sxs-lookup"><span data-stu-id="a20b8-134">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="a20b8-135">每次**Click me**按钮处于选中状态：</span><span class="sxs-lookup"><span data-stu-id="a20b8-135">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="a20b8-136">`onclick`触发事件。</span><span class="sxs-lookup"><span data-stu-id="a20b8-136">The `onclick` event is fired.</span></span>
* <span data-ttu-id="a20b8-137">调用 `IncrementCount` 方法。</span><span class="sxs-lookup"><span data-stu-id="a20b8-137">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="a20b8-138">`currentCount`会递增。</span><span class="sxs-lookup"><span data-stu-id="a20b8-138">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="a20b8-139">再次呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="a20b8-139">The component is rendered again.</span></span>

<span data-ttu-id="a20b8-140">在运行时比较至以前的内容的新内容，并仅将更改的内容应用到文档对象模型 (DOM)。</span><span class="sxs-lookup"><span data-stu-id="a20b8-140">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="a20b8-141">将组件添加到另一个组件使用类似于 HTML 的语法。</span><span class="sxs-lookup"><span data-stu-id="a20b8-141">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="a20b8-142">使用属性或子内容指定组件参数。</span><span class="sxs-lookup"><span data-stu-id="a20b8-142">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="a20b8-143">例如，计数器组件可以已添加到应用程序的主页添加`<Counter />`元素到索引组件。</span><span class="sxs-lookup"><span data-stu-id="a20b8-143">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="a20b8-144">在中*pages/Index.cshtml*，调查提示组件替换为计数器组件：</span><span class="sxs-lookup"><span data-stu-id="a20b8-144">In *Pages/Index.cshtml*, replace the Survey Prompt component with a Counter component:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

<span data-ttu-id="a20b8-145">运行应用。</span><span class="sxs-lookup"><span data-stu-id="a20b8-145">Run the app.</span></span> <span data-ttu-id="a20b8-146">主页都有它自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="a20b8-146">The homepage has its own counter.</span></span>

<span data-ttu-id="a20b8-147">若要将参数添加到计数器组件，更新组件的`@functions`块：</span><span class="sxs-lookup"><span data-stu-id="a20b8-147">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="a20b8-148">添加的属性`IncrementAmount`修饰的`[Parameter]`属性。</span><span class="sxs-lookup"><span data-stu-id="a20b8-148">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="a20b8-149">更改`IncrementCount`方法以使用`IncrementAmount`增加的值时`currentCount`。</span><span class="sxs-lookup"><span data-stu-id="a20b8-149">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="a20b8-150">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a20b8-150">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

<span data-ttu-id="a20b8-151">指定`IncrementAmount`主页组件中的参数`<Counter>`元素使用的属性。</span><span class="sxs-lookup"><span data-stu-id="a20b8-151">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="a20b8-152">Pages/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="a20b8-152">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

<span data-ttu-id="a20b8-153">运行应用。</span><span class="sxs-lookup"><span data-stu-id="a20b8-153">Run the app.</span></span> <span data-ttu-id="a20b8-154">主页具有其自己计数器，它每次按 10 递增**Click me**按钮处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="a20b8-154">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a20b8-155">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a20b8-155">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
