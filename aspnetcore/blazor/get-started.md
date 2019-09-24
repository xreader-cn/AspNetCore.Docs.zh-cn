---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 使用所选工具构建 Blazor 应用，开始使用 Blazor。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/get-started
ms.openlocfilehash: 4c2a8f62b7f6a60815d131756d1e551904d918ad
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207219"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="29214-103">ASP.NET Core Blazor 入门</span><span class="sxs-lookup"><span data-stu-id="29214-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="29214-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="29214-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="29214-105">Blazor 入门：</span><span class="sxs-lookup"><span data-stu-id="29214-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="29214-106">安装最新的[.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)版本。</span><span class="sxs-lookup"><span data-stu-id="29214-106">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="29214-107">在命令 shell 中运行以下命令，安装 Blazor 模板：</span><span class="sxs-lookup"><span data-stu-id="29214-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview9.19465.2
   ```

1. <span data-ttu-id="29214-108">按照所选工具的指导进行操作：</span><span class="sxs-lookup"><span data-stu-id="29214-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="29214-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29214-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="29214-110">1 \。</span><span class="sxs-lookup"><span data-stu-id="29214-110">1\.</span></span> <span data-ttu-id="29214-111">安装最新的[Visual Studio](https://visualstudio.com/vs/) **和 ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="29214-111">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="29214-112">2。</span><span class="sxs-lookup"><span data-stu-id="29214-112">2\.</span></span> <span data-ttu-id="29214-113">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="29214-113">Create a new project.</span></span>

   <span data-ttu-id="29214-114">3。</span><span class="sxs-lookup"><span data-stu-id="29214-114">3\.</span></span> <span data-ttu-id="29214-115">选择**Blazor 应用**。</span><span class="sxs-lookup"><span data-stu-id="29214-115">Select **Blazor App**.</span></span> <span data-ttu-id="29214-116">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="29214-116">Select **Next**.</span></span>

   <span data-ttu-id="29214-117">4 \。</span><span class="sxs-lookup"><span data-stu-id="29214-117">4\.</span></span> <span data-ttu-id="29214-118">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="29214-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="29214-119">确认**位置**项正确或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="29214-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="29214-120">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="29214-120">Select **Create**.</span></span>

   <span data-ttu-id="29214-121">5 \。</span><span class="sxs-lookup"><span data-stu-id="29214-121">5\.</span></span> <span data-ttu-id="29214-122">对于 "Blazor WebAssembly 体验"，请选择 " **Blazor WebAssembly 应用**" 模板。</span><span class="sxs-lookup"><span data-stu-id="29214-122">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="29214-123">对于 Blazor 服务器体验，请选择**Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="29214-123">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="29214-124">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="29214-124">Select **Create**.</span></span> <span data-ttu-id="29214-125">有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息， <xref:blazor/hosting-models>请参阅。</span><span class="sxs-lookup"><span data-stu-id="29214-125">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="29214-126">6 \。</span><span class="sxs-lookup"><span data-stu-id="29214-126">6\.</span></span> <span data-ttu-id="29214-127">按 F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="29214-127">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="29214-128">如果安装了 Blazor Visual Studio extension for the ASP.NET Core Blazor （预览版6或更早版本）的先前预览版本，则可以卸载该扩展。</span><span class="sxs-lookup"><span data-stu-id="29214-128">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="29214-129">现在，在命令外壳中安装 Blazor 模板足以在 Visual Studio 中显示模板。</span><span class="sxs-lookup"><span data-stu-id="29214-129">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="29214-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="29214-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="29214-131">1 \。</span><span class="sxs-lookup"><span data-stu-id="29214-131">1\.</span></span> <span data-ttu-id="29214-132">安装 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="29214-132">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="29214-133">2。</span><span class="sxs-lookup"><span data-stu-id="29214-133">2\.</span></span> <span data-ttu-id="29214-134">安装[ C# Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)的最新版本。</span><span class="sxs-lookup"><span data-stu-id="29214-134">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="29214-135">3。</span><span class="sxs-lookup"><span data-stu-id="29214-135">3\.</span></span> <span data-ttu-id="29214-136">对于 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="29214-136">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="29214-137">对于 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="29214-137">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="29214-138">有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息， <xref:blazor/hosting-models>请参阅。</span><span class="sxs-lookup"><span data-stu-id="29214-138">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="29214-139">4 \。</span><span class="sxs-lookup"><span data-stu-id="29214-139">4\.</span></span> <span data-ttu-id="29214-140">在 Visual Studio Code 中打开 " *WebApplication1* " 文件夹。</span><span class="sxs-lookup"><span data-stu-id="29214-140">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="29214-141">5 \。</span><span class="sxs-lookup"><span data-stu-id="29214-141">5\.</span></span> <span data-ttu-id="29214-142">对于 Blazor 服务器项目，IDE 请求你添加资产以生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="29214-142">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="29214-143">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="29214-143">Select **Yes**.</span></span>

   <span data-ttu-id="29214-144">6 \。</span><span class="sxs-lookup"><span data-stu-id="29214-144">6\.</span></span> <span data-ttu-id="29214-145">如果使用的是 Blazor 服务器应用，请使用 Visual Studio Code 调试程序运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="29214-145">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="29214-146">如果使用 Blazor WebAssembly 应用，请从`dotnet run`应用的项目文件夹执行。</span><span class="sxs-lookup"><span data-stu-id="29214-146">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="29214-147">7 \。</span><span class="sxs-lookup"><span data-stu-id="29214-147">7\.</span></span> <span data-ttu-id="29214-148">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="29214-148">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="29214-149">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="29214-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="29214-150">对于 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="29214-150">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="29214-151">对于 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="29214-151">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="29214-152">有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息， <xref:blazor/hosting-models>请参阅。</span><span class="sxs-lookup"><span data-stu-id="29214-152">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="29214-153">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="29214-153">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="29214-154">边栏中的选项卡上提供了多个页面：</span><span class="sxs-lookup"><span data-stu-id="29214-154">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="29214-155">Home</span><span class="sxs-lookup"><span data-stu-id="29214-155">Home</span></span>
* <span data-ttu-id="29214-156">计数器</span><span class="sxs-lookup"><span data-stu-id="29214-156">Counter</span></span>
* <span data-ttu-id="29214-157">提取数据</span><span class="sxs-lookup"><span data-stu-id="29214-157">Fetch data</span></span>

<span data-ttu-id="29214-158">在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="29214-158">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="29214-159">在网页中递增计数器通常需要编写 JavaScript，但 Razor 组件使用C#可以提供更好的方法。</span><span class="sxs-lookup"><span data-stu-id="29214-159">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="29214-160">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="29214-160">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="29214-161">浏览器`/counter`中`@page`由顶部指令指定的请求会使`Counter`组件呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="29214-161">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="29214-162">组件呈现为呈现树的内存中表示形式，然后可以使用它以一种灵活而高效的方式更新 UI。</span><span class="sxs-lookup"><span data-stu-id="29214-162">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="29214-163">每次选择 "**单击我**" 按钮时：</span><span class="sxs-lookup"><span data-stu-id="29214-163">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="29214-164">触发`onclick`事件。</span><span class="sxs-lookup"><span data-stu-id="29214-164">The `onclick` event is fired.</span></span>
* <span data-ttu-id="29214-165">调用 `IncrementCount` 方法。</span><span class="sxs-lookup"><span data-stu-id="29214-165">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="29214-166">`currentCount`递增。</span><span class="sxs-lookup"><span data-stu-id="29214-166">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="29214-167">再次呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="29214-167">The component is rendered again.</span></span>

<span data-ttu-id="29214-168">运行时将新内容与以前的内容进行比较，并仅将更改的内容应用到文档对象模型（DOM）。</span><span class="sxs-lookup"><span data-stu-id="29214-168">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="29214-169">使用 HTML 语法将组件添加到其他组件。</span><span class="sxs-lookup"><span data-stu-id="29214-169">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="29214-170">例如，通过`Counter` `<Counter />`向`Index`组件添加元素，将该组件添加到应用的主页。</span><span class="sxs-lookup"><span data-stu-id="29214-170">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="29214-171">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="29214-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="29214-172">运行应用。</span><span class="sxs-lookup"><span data-stu-id="29214-172">Run the app.</span></span> <span data-ttu-id="29214-173">主页有由`Counter`组件提供的自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="29214-173">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="29214-174">使用特性或[子内容](xref:blazor/components#child-content)指定组件参数，这些参数允许你设置子组件的属性。</span><span class="sxs-lookup"><span data-stu-id="29214-174">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="29214-175">若要向`Counter`组件添加参数，请更新组件的`@code`块：</span><span class="sxs-lookup"><span data-stu-id="29214-175">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="29214-176">`IncrementAmount`使用特性添加的公共属性。`[Parameter]`</span><span class="sxs-lookup"><span data-stu-id="29214-176">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="29214-177">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="29214-177">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="29214-178">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="29214-178">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="29214-179">使用特性`IncrementAmount`指定`Index`组件的`<Counter>`元素中的。</span><span class="sxs-lookup"><span data-stu-id="29214-179">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="29214-180">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="29214-180">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="29214-181">运行应用。</span><span class="sxs-lookup"><span data-stu-id="29214-181">Run the app.</span></span> <span data-ttu-id="29214-182">每次选择 "**单击我**" 按钮时，组件都有其自己的计数器，每次递增10。`Index`</span><span class="sxs-lookup"><span data-stu-id="29214-182">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="29214-183">中`Counter` 的`/counter`组件（*Counter*）继续递增1。</span><span class="sxs-lookup"><span data-stu-id="29214-183">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="29214-184">后续步骤</span><span class="sxs-lookup"><span data-stu-id="29214-184">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="29214-185">其他资源</span><span class="sxs-lookup"><span data-stu-id="29214-185">Additional resources</span></span>

* <xref:signalr/introduction>
