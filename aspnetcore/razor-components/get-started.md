---
title: 开始使用 Razor 组件
author: guardrex
description: 了解如何开始使用 Razor 组件创建和修改 Razor 组件项目。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/get-started
ms.openlocfilehash: 151e58497b0f22fa7c5a9bde1f665eeb73fd5dc3
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515416"
---
# <a name="get-started-with-razor-components"></a><span data-ttu-id="a82f8-103">开始使用 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="a82f8-103">Get started with Razor Components</span></span>

<span data-ttu-id="a82f8-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a82f8-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="a82f8-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a82f8-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a82f8-106">先决条件：</span><span class="sxs-lookup"><span data-stu-id="a82f8-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="a82f8-107">在 Visual Studio 中创建第一个 Razor 组件项目：</span><span class="sxs-lookup"><span data-stu-id="a82f8-107">To create your first Razor Components project in Visual Studio:</span></span>

1. <span data-ttu-id="a82f8-108">安装最新[.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)发布。</span><span class="sxs-lookup"><span data-stu-id="a82f8-108">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>
1. <span data-ttu-id="a82f8-109">启用 Visual Studio 以使用预览版 Sdk:</span><span class="sxs-lookup"><span data-stu-id="a82f8-109">Enable Visual Studio to use preview SDKs:</span></span>
   1. <span data-ttu-id="a82f8-110">打开**工具** > **选项**菜单栏中。</span><span class="sxs-lookup"><span data-stu-id="a82f8-110">Open **Tools** > **Options** in the menu bar.</span></span>
   1. <span data-ttu-id="a82f8-111">打开**项目和解决方案**节点。</span><span class="sxs-lookup"><span data-stu-id="a82f8-111">Open the **Projects and Solutions** node.</span></span> <span data-ttu-id="a82f8-112">打开 **.NET Core**选项卡。</span><span class="sxs-lookup"><span data-stu-id="a82f8-112">Open the **.NET Core** tab.</span></span>
   1. <span data-ttu-id="a82f8-113">选中的复选框**使用的.NET Core SDK 预览**。</span><span class="sxs-lookup"><span data-stu-id="a82f8-113">Check the box for **Use previews of the .NET Core SDK**.</span></span> <span data-ttu-id="a82f8-114">选择 **确定**。</span><span class="sxs-lookup"><span data-stu-id="a82f8-114">Select **OK**.</span></span>
1. <span data-ttu-id="a82f8-115">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="a82f8-115">Create a new project.</span></span>
1. <span data-ttu-id="a82f8-116">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="a82f8-116">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a82f8-117">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="a82f8-117">Select **Next**.</span></span>
1. <span data-ttu-id="a82f8-118">中提供名称**项目名称**字段。</span><span class="sxs-lookup"><span data-stu-id="a82f8-118">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="a82f8-119">确认**位置**条目是否正确，或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="a82f8-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a82f8-120">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="a82f8-120">Select **Create**.</span></span>
1. <span data-ttu-id="a82f8-121">请确保 **.NET Core**并**ASP.NET Core 3.0**顶部选择。</span><span class="sxs-lookup"><span data-stu-id="a82f8-121">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="a82f8-122">选择**Razor 组件**模板，然后选择**创建**。</span><span class="sxs-lookup"><span data-stu-id="a82f8-122">Choose the **Razor Components** template and select **Create**.</span></span>
1. <span data-ttu-id="a82f8-123">按 F5  运行应用。</span><span class="sxs-lookup"><span data-stu-id="a82f8-123">Press **F5** to run the app.</span></span>

<span data-ttu-id="a82f8-124">祝贺你！</span><span class="sxs-lookup"><span data-stu-id="a82f8-124">Congratulations!</span></span> <span data-ttu-id="a82f8-125">只需运行第一个 Razor 组件应用 ！</span><span class="sxs-lookup"><span data-stu-id="a82f8-125">You just ran your first Razor Components app!</span></span>

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Razor Components project in Visual Studio Code:

1. Execute the following command from a command shell:

   ```console
   dotnet new razorcomponents -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Add a *.vscode* folder.

1. Add a *tasks.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/tasks.json)]

1. Add a *launch.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/launch.json)]

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Razor Components app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Razor Components project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **ASP.NET Core Razor Components** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Razor Components app!
-->

# [<a name="net-core-cli"></a><span data-ttu-id="a82f8-126">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a82f8-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="a82f8-127">先决条件：</span><span class="sxs-lookup"><span data-stu-id="a82f8-127">Prerequisites:</span></span>

* [<span data-ttu-id="a82f8-128">.NET core SDK 3.0 预览</span><span class="sxs-lookup"><span data-stu-id="a82f8-128">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="a82f8-129">若要从命令行界面创建第一个 Razor 组件项目：</span><span class="sxs-lookup"><span data-stu-id="a82f8-129">To create your first Razor Components project from a command shell:</span></span>

   ```console
   dotnet new razorcomponents -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="a82f8-130">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="a82f8-130">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="a82f8-131">祝贺你！</span><span class="sxs-lookup"><span data-stu-id="a82f8-131">Congratulations!</span></span> <span data-ttu-id="a82f8-132">只需运行第一个 Razor 组件应用 ！</span><span class="sxs-lookup"><span data-stu-id="a82f8-132">You just ran your first Razor Components app!</span></span>

---

## <a name="razor-components-project"></a><span data-ttu-id="a82f8-133">Razor 组件项目</span><span class="sxs-lookup"><span data-stu-id="a82f8-133">Razor Components project</span></span>

<span data-ttu-id="a82f8-134">Razor 组件使用 Razor 语法编写的但不同于 Razor 页面和 MVC 视图编译。</span><span class="sxs-lookup"><span data-stu-id="a82f8-134">Razor Components are authored using Razor syntax but are compiled differently than Razor Pages and MVC views.</span></span> <span data-ttu-id="a82f8-135">*.Razor*文件扩展名用于指定 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="a82f8-135">The *.razor* file extension is used to specify a Razor Component.</span></span> <span data-ttu-id="a82f8-136">Razor 页面和 MVC 视图继续使用 *.cshtml*文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="a82f8-136">Razor Pages and MVC views continue to use the *.cshtml* file extension.</span></span>

> [!NOTE]
> <span data-ttu-id="a82f8-137">可以使用创作 razor 组件 *.cshtml*文件扩展名，只要这些文件被标识为 Razor 组件文件使用`_RazorComponentInclude`MSBuild 属性。</span><span class="sxs-lookup"><span data-stu-id="a82f8-137">Razor Components can be authored using the *.cshtml* file extension as long as those files are identified as Razor Component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="a82f8-138">例如，使用 Razor 组件模板创建的应用指定的所有 *.cshtml*文件下*组件*文件夹应视为 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="a82f8-138">For example, an app created using the Razor Component template specifies that all *.cshtml* files under the *Components* folder should be treated as Razor Components:</span></span>
>
> ```xml
> <_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
> ```

<span data-ttu-id="a82f8-139">当应用运行时，多个页侧栏中的选项卡中有：</span><span class="sxs-lookup"><span data-stu-id="a82f8-139">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="a82f8-140">主页</span><span class="sxs-lookup"><span data-stu-id="a82f8-140">Home</span></span>
* <span data-ttu-id="a82f8-141">计数器</span><span class="sxs-lookup"><span data-stu-id="a82f8-141">Counter</span></span>
* <span data-ttu-id="a82f8-142">提取数据</span><span class="sxs-lookup"><span data-stu-id="a82f8-142">Fetch data</span></span>

<span data-ttu-id="a82f8-143">在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="a82f8-143">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="a82f8-144">增加网页中的计数器值通常需要编写 JavaScript，但 Razor 组件使用 C# 提供了更好的方法。</span><span class="sxs-lookup"><span data-stu-id="a82f8-144">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

<span data-ttu-id="a82f8-145">*WebApplication1/Components/Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a82f8-145">*WebApplication1/Components/Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor)]

<span data-ttu-id="a82f8-146">请求`/counter`中指定的浏览器，通过`@page`指令在顶部，会导致计数器组件来呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="a82f8-146">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="a82f8-147">组件将使用灵活且高效的方式更新 UI 的呈现器树的内存中表示形式呈现。</span><span class="sxs-lookup"><span data-stu-id="a82f8-147">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="a82f8-148">每次**Click me**按钮处于选中状态：</span><span class="sxs-lookup"><span data-stu-id="a82f8-148">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="a82f8-149">`onclick`触发事件。</span><span class="sxs-lookup"><span data-stu-id="a82f8-149">The `onclick` event is fired.</span></span>
* <span data-ttu-id="a82f8-150">调用 `IncrementCount` 方法。</span><span class="sxs-lookup"><span data-stu-id="a82f8-150">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="a82f8-151">`currentCount`会递增。</span><span class="sxs-lookup"><span data-stu-id="a82f8-151">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="a82f8-152">再次呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="a82f8-152">The component is rendered again.</span></span>

<span data-ttu-id="a82f8-153">在运行时比较至以前的内容的新内容，并仅将更改的内容应用到文档对象模型 (DOM)。</span><span class="sxs-lookup"><span data-stu-id="a82f8-153">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="a82f8-154">将组件添加到另一个组件使用类似于 HTML 的语法。</span><span class="sxs-lookup"><span data-stu-id="a82f8-154">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="a82f8-155">使用属性或子内容指定组件参数。</span><span class="sxs-lookup"><span data-stu-id="a82f8-155">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="a82f8-156">例如，计数器组件可以已添加到应用程序的主页添加`<Counter />`元素到索引组件。</span><span class="sxs-lookup"><span data-stu-id="a82f8-156">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="a82f8-157">*WebApplication1/Components/Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="a82f8-157">*WebApplication1/Components/Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="a82f8-158">运行应用。</span><span class="sxs-lookup"><span data-stu-id="a82f8-158">Run the app.</span></span> <span data-ttu-id="a82f8-159">主页都有它自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="a82f8-159">The homepage has its own counter.</span></span>

<span data-ttu-id="a82f8-160">若要将参数添加到计数器组件，更新组件的`@functions`块：</span><span class="sxs-lookup"><span data-stu-id="a82f8-160">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="a82f8-161">添加的属性`IncrementAmount`修饰的`[Parameter]`属性。</span><span class="sxs-lookup"><span data-stu-id="a82f8-161">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="a82f8-162">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="a82f8-162">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="a82f8-163">*WebApplication1/Components/Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a82f8-163">*WebApplication1/Components/Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=4,8)]

<span data-ttu-id="a82f8-164">使用属性在 Home 组件的 `<Counter>` 元素中指定 `IncrementAmount` 参数。</span><span class="sxs-lookup"><span data-stu-id="a82f8-164">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="a82f8-165">*WebApplication1/Components/Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="a82f8-165">*WebApplication1/Components/Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor)]

<span data-ttu-id="a82f8-166">运行应用。</span><span class="sxs-lookup"><span data-stu-id="a82f8-166">Run the app.</span></span> <span data-ttu-id="a82f8-167">主页具有其自己计数器，它每次按 10 递增**Click me**按钮处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="a82f8-167">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a82f8-168">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a82f8-168">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
