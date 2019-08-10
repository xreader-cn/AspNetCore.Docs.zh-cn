---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 使用所选工具构建 Blazor 应用, 开始使用 Blazor。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
uid: blazor/get-started
ms.openlocfilehash: b4609858be43acf9d1b2d8be5eff4879fd56f49f
ms.sourcegitcommit: 051f068c78931432e030b60094c38376d64d013e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68948327"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="a4739-103">ASP.NET Core Blazor 入门</span><span class="sxs-lookup"><span data-stu-id="a4739-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="a4739-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a4739-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a4739-105">Blazor 入门:</span><span class="sxs-lookup"><span data-stu-id="a4739-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="a4739-106">安装最新的[.Net Core 3.0 预览版 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)版本。</span><span class="sxs-lookup"><span data-stu-id="a4739-106">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="a4739-107">在命令 shell 中运行以下命令, 安装 Blazor 模板:</span><span class="sxs-lookup"><span data-stu-id="a4739-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview7.19365.7
   ```

1. <span data-ttu-id="a4739-108">按照所选工具的指导进行操作:</span><span class="sxs-lookup"><span data-stu-id="a4739-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a4739-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4739-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="a4739-110">1 \。</span><span class="sxs-lookup"><span data-stu-id="a4739-110">1\.</span></span> <span data-ttu-id="a4739-111">安装**ASP.NET 和 web 开发**工作负荷的最新[Visual Studio 预览版](https://visualstudio.com/vs/preview)。</span><span class="sxs-lookup"><span data-stu-id="a4739-111">Install the latest [Visual Studio preview](https://visualstudio.com/vs/preview) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="a4739-112">2。</span><span class="sxs-lookup"><span data-stu-id="a4739-112">2\.</span></span> <span data-ttu-id="a4739-113">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="a4739-113">Create a new project.</span></span>

   <span data-ttu-id="a4739-114">3。</span><span class="sxs-lookup"><span data-stu-id="a4739-114">3\.</span></span> <span data-ttu-id="a4739-115">选择**Blazor 应用**。</span><span class="sxs-lookup"><span data-stu-id="a4739-115">Select **Blazor App**.</span></span> <span data-ttu-id="a4739-116">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="a4739-116">Select **Next**.</span></span>

   <span data-ttu-id="a4739-117">4 \。</span><span class="sxs-lookup"><span data-stu-id="a4739-117">4\.</span></span> <span data-ttu-id="a4739-118">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="a4739-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a4739-119">确认**位置**项正确或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="a4739-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a4739-120">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="a4739-120">Select **Create**.</span></span>

   <span data-ttu-id="a4739-121">5 \。</span><span class="sxs-lookup"><span data-stu-id="a4739-121">5\.</span></span> <span data-ttu-id="a4739-122">对于 Blazor 客户端体验, 请选择 " **Blazor (客户端)** " 模板。</span><span class="sxs-lookup"><span data-stu-id="a4739-122">For a Blazor client-side experience, choose the **Blazor (client-side)** template.</span></span> <span data-ttu-id="a4739-123">对于 Blazor 服务器端体验, 请选择**Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="a4739-123">For a Blazor server-side experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a4739-124">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="a4739-124">Select **Create**.</span></span> <span data-ttu-id="a4739-125">有关这两个 Blazor 托管模型、服务器端和客户端的信息, 请参阅<xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="a4739-125">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="a4739-126">6 \。</span><span class="sxs-lookup"><span data-stu-id="a4739-126">6\.</span></span> <span data-ttu-id="a4739-127">按 F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="a4739-127">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="a4739-128">如果安装了 Blazor Visual Studio extension for the ASP.NET Core Blazor (预览版6或更早版本) 的先前预览版本, 则可以在预览版7中卸载该扩展。</span><span class="sxs-lookup"><span data-stu-id="a4739-128">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension at Preview 7.</span></span> <span data-ttu-id="a4739-129">现在, 在命令外壳中安装 Blazor 模板足以在 Visual Studio 中显示模板。</span><span class="sxs-lookup"><span data-stu-id="a4739-129">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a4739-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4739-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="a4739-131">1 \。</span><span class="sxs-lookup"><span data-stu-id="a4739-131">1\.</span></span> <span data-ttu-id="a4739-132">安装 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="a4739-132">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="a4739-133">2。</span><span class="sxs-lookup"><span data-stu-id="a4739-133">2\.</span></span> <span data-ttu-id="a4739-134">安装[ C# Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)的最新版本。</span><span class="sxs-lookup"><span data-stu-id="a4739-134">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="a4739-135">3。</span><span class="sxs-lookup"><span data-stu-id="a4739-135">3\.</span></span> <span data-ttu-id="a4739-136">对于 Blazor 客户端体验, 请在命令行界面中执行以下命令:</span><span class="sxs-lookup"><span data-stu-id="a4739-136">For a Blazor client-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazor -o WebApplication1
      ```

      <span data-ttu-id="a4739-137">对于 Blazor 服务器端体验, 请在命令行界面中执行以下命令:</span><span class="sxs-lookup"><span data-stu-id="a4739-137">For a Blazor server-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorserverside -o WebApplication1
      ```

      <span data-ttu-id="a4739-138">有关这两个 Blazor 托管模型、服务器端和客户端的信息, 请参阅<xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="a4739-138">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="a4739-139">4 \。</span><span class="sxs-lookup"><span data-stu-id="a4739-139">4\.</span></span> <span data-ttu-id="a4739-140">在 Visual Studio Code 中打开 " *WebApplication1* " 文件夹。</span><span class="sxs-lookup"><span data-stu-id="a4739-140">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="a4739-141">5 \。</span><span class="sxs-lookup"><span data-stu-id="a4739-141">5\.</span></span> <span data-ttu-id="a4739-142">对于 Blazor 服务器端项目, IDE 请求你添加资产以生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="a4739-142">For a Blazor server-side project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="a4739-143">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="a4739-143">Select **Yes**.</span></span>

   <span data-ttu-id="a4739-144">6 \。</span><span class="sxs-lookup"><span data-stu-id="a4739-144">6\.</span></span> <span data-ttu-id="a4739-145">如果使用的是 Blazor 服务器端应用, 请使用 Visual Studio Code 调试程序运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="a4739-145">If using a Blazor server-side app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="a4739-146">如果使用 Blazor 客户端应用, 则从应用`dotnet run`的项目文件夹执行。</span><span class="sxs-lookup"><span data-stu-id="a4739-146">If using a Blazor client-side app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="a4739-147">7 \。</span><span class="sxs-lookup"><span data-stu-id="a4739-147">7\.</span></span> <span data-ttu-id="a4739-148">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="a4739-148">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor server-side experience, select the **ASP.NET Core Blazor Server App** template. For a Blazor client-side experience, select the **ASP.NET Core Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a4739-149">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a4739-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="a4739-150">对于 Blazor 客户端体验, 请在命令行界面中执行以下命令:</span><span class="sxs-lookup"><span data-stu-id="a4739-150">For a Blazor client-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="a4739-151">对于 Blazor 服务器端体验, 请在命令行界面中执行以下命令:</span><span class="sxs-lookup"><span data-stu-id="a4739-151">For a Blazor server-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorserverside -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="a4739-152">有关这两个 Blazor 托管模型、服务器端和客户端的信息, 请参阅<xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="a4739-152">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="a4739-153">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="a4739-153">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="a4739-154">边栏中的选项卡上提供了多个页面:</span><span class="sxs-lookup"><span data-stu-id="a4739-154">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="a4739-155">Home</span><span class="sxs-lookup"><span data-stu-id="a4739-155">Home</span></span>
* <span data-ttu-id="a4739-156">计数器</span><span class="sxs-lookup"><span data-stu-id="a4739-156">Counter</span></span>
* <span data-ttu-id="a4739-157">提取数据</span><span class="sxs-lookup"><span data-stu-id="a4739-157">Fetch data</span></span>

<span data-ttu-id="a4739-158">在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="a4739-158">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="a4739-159">在网页中递增计数器通常需要编写 JavaScript, 但 Razor 组件使用C#可以提供更好的方法。</span><span class="sxs-lookup"><span data-stu-id="a4739-159">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="a4739-160">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="a4739-160">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="a4739-161">浏览器`/counter`中`@page`由顶部指令指定的请求会使`Counter`组件呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="a4739-161">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="a4739-162">组件呈现为呈现树的内存中表示形式, 然后可以使用它以一种灵活而高效的方式更新 UI。</span><span class="sxs-lookup"><span data-stu-id="a4739-162">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="a4739-163">每次选择 "**单击我**" 按钮时:</span><span class="sxs-lookup"><span data-stu-id="a4739-163">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="a4739-164">触发`onclick`事件。</span><span class="sxs-lookup"><span data-stu-id="a4739-164">The `onclick` event is fired.</span></span>
* <span data-ttu-id="a4739-165">调用 `IncrementCount` 方法。</span><span class="sxs-lookup"><span data-stu-id="a4739-165">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="a4739-166">`currentCount`递增。</span><span class="sxs-lookup"><span data-stu-id="a4739-166">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="a4739-167">再次呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="a4739-167">The component is rendered again.</span></span>

<span data-ttu-id="a4739-168">运行时将新内容与以前的内容进行比较, 并仅将更改的内容应用到文档对象模型 (DOM)。</span><span class="sxs-lookup"><span data-stu-id="a4739-168">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="a4739-169">使用 HTML 语法将组件添加到其他组件。</span><span class="sxs-lookup"><span data-stu-id="a4739-169">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="a4739-170">例如, 通过`Counter` `<Counter />`向`Index`组件添加元素, 将该组件添加到应用的主页。</span><span class="sxs-lookup"><span data-stu-id="a4739-170">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="a4739-171">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="a4739-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="a4739-172">运行应用。</span><span class="sxs-lookup"><span data-stu-id="a4739-172">Run the app.</span></span> <span data-ttu-id="a4739-173">主页有由`Counter`组件提供的自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="a4739-173">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="a4739-174">使用特性或[子内容](xref:blazor/components#child-content)指定组件参数, 这些参数允许你设置子组件的属性。</span><span class="sxs-lookup"><span data-stu-id="a4739-174">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="a4739-175">若要向`Counter`组件添加参数, 请更新组件的`@code`块:</span><span class="sxs-lookup"><span data-stu-id="a4739-175">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="a4739-176">`IncrementAmount`使用属性添加的属性。`[Parameter]`</span><span class="sxs-lookup"><span data-stu-id="a4739-176">Add a property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="a4739-177">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="a4739-177">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="a4739-178">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="a4739-178">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="a4739-179">使用特性`IncrementAmount`指定`Index`组件的`<Counter>`元素中的。</span><span class="sxs-lookup"><span data-stu-id="a4739-179">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="a4739-180">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="a4739-180">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="a4739-181">运行应用。</span><span class="sxs-lookup"><span data-stu-id="a4739-181">Run the app.</span></span> <span data-ttu-id="a4739-182">每次选择 "**单击我**" 按钮时,组件都有其自己的计数器,每次递增10。`Index`</span><span class="sxs-lookup"><span data-stu-id="a4739-182">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="a4739-183">中`Counter` 的`/counter`组件 (*Counter*) 继续递增1。</span><span class="sxs-lookup"><span data-stu-id="a4739-183">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a4739-184">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a4739-184">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="a4739-185">其他资源</span><span class="sxs-lookup"><span data-stu-id="a4739-185">Additional resources</span></span>

* <xref:signalr/introduction>
