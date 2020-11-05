---
title: 'ASP.NET Core :::no-loc(Razor)::: 组件类库'
author: guardrex
description: '了解如何从外部组件库将组件包含在 :::no-loc(Blazor)::: 应用中。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/class-libraries
ms.openlocfilehash: f8e36cbe905b5ec2e674123c0f2ab6db99683c7c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056408"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="67b40-103">ASP.NET Core :::no-loc(Razor)::: 组件类库</span><span class="sxs-lookup"><span data-stu-id="67b40-103">ASP.NET Core :::no-loc(Razor)::: components class libraries</span></span>

<span data-ttu-id="67b40-104">作者：[Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="67b40-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="67b40-105">组件可在 [:::no-loc(Razor)::: 类库 (RCL)](xref:razor-pages/ui-class) 中跨项目共享。</span><span class="sxs-lookup"><span data-stu-id="67b40-105">Components can be shared in a [:::no-loc(Razor)::: class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="67b40-106">:::no-loc(Razor)::: 组件类库可以包含在以下各项中：</span><span class="sxs-lookup"><span data-stu-id="67b40-106">A *:::no-loc(Razor)::: components class library* can be included from:</span></span>

* <span data-ttu-id="67b40-107">解决方案中的另一个项目。</span><span class="sxs-lookup"><span data-stu-id="67b40-107">Another project in the solution.</span></span>
* <span data-ttu-id="67b40-108">NuGet 程序包。</span><span class="sxs-lookup"><span data-stu-id="67b40-108">A NuGet package.</span></span>
* <span data-ttu-id="67b40-109">引用的 .NET 库。</span><span class="sxs-lookup"><span data-stu-id="67b40-109">A referenced .NET library.</span></span>

<span data-ttu-id="67b40-110">正如组件是常规的 .NET 类型一样，RCL 提供的组件也是普通的 .NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="67b40-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="67b40-111">创建 RCL</span><span class="sxs-lookup"><span data-stu-id="67b40-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="67b40-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67b40-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="67b40-113">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="67b40-113">Create a new project.</span></span>
1. <span data-ttu-id="67b40-114">选择“:::no-loc(Razor)::: 类库”。</span><span class="sxs-lookup"><span data-stu-id="67b40-114">Select **:::no-loc(Razor)::: Class Library**.</span></span> <span data-ttu-id="67b40-115">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="67b40-115">Select **Next**.</span></span>
1. <span data-ttu-id="67b40-116">在“创建新的 :::no-loc(Razor)::: 类库”对话框中，选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="67b40-116">In the **Create a new :::no-loc(Razor)::: class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="67b40-117">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="67b40-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="67b40-118">本主题中的示例使用项目名称 `ComponentLibrary`。</span><span class="sxs-lookup"><span data-stu-id="67b40-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="67b40-119">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="67b40-119">Select **Create**.</span></span>
1. <span data-ttu-id="67b40-120">将 RCL 添加到一个解决方案：</span><span class="sxs-lookup"><span data-stu-id="67b40-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="67b40-121">右键单击该解决方案。</span><span class="sxs-lookup"><span data-stu-id="67b40-121">Right-click the solution.</span></span> <span data-ttu-id="67b40-122">选择“添加” > “现有项目” 。</span><span class="sxs-lookup"><span data-stu-id="67b40-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="67b40-123">导航到 RCL 的项目文件。</span><span class="sxs-lookup"><span data-stu-id="67b40-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="67b40-124">选择 RCL 的项目文件 (`.csproj`)。</span><span class="sxs-lookup"><span data-stu-id="67b40-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="67b40-125">从应用中添加 RCL 的引用：</span><span class="sxs-lookup"><span data-stu-id="67b40-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="67b40-126">右键单击该应用项目。</span><span class="sxs-lookup"><span data-stu-id="67b40-126">Right-click the app project.</span></span> <span data-ttu-id="67b40-127">选择“添加” > “引用” 。</span><span class="sxs-lookup"><span data-stu-id="67b40-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="67b40-128">选择 RCL 项目。</span><span class="sxs-lookup"><span data-stu-id="67b40-128">Select the RCL project.</span></span> <span data-ttu-id="67b40-129">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="67b40-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="67b40-130">如果在从模板生成 RCL 时选中了“支持页面和视图”复选框，还请通过以下内容，将 `_Imports.razor` 文件添加到所生成项目的根目录中，以启用 :::no-loc(Razor)::: 组件创作：</span><span class="sxs-lookup"><span data-stu-id="67b40-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable :::no-loc(Razor)::: component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="67b40-131">手动将该文件添加到所生成项目的根目录中。</span><span class="sxs-lookup"><span data-stu-id="67b40-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="67b40-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="67b40-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="67b40-133">在命令行界面中通过 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令使用 :::no-loc(Razor)::: 类库模板 (`razorclasslib`)。</span><span class="sxs-lookup"><span data-stu-id="67b40-133">Use the **:::no-loc(Razor)::: Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="67b40-134">下面的示例创建了名为 `ComponentLibrary` 的 RCL。</span><span class="sxs-lookup"><span data-stu-id="67b40-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="67b40-135">执行命令时，自动创建包含 `ComponentLibrary` 的文件夹：</span><span class="sxs-lookup"><span data-stu-id="67b40-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="67b40-136">如果在从模板生成 RCL 时使用 `-s|--support-pages-and-views` 开关，还请通过以下内容将 `_Imports.razor` 文件添加到所生成项目的根目录中，以启用 :::no-loc(Razor)::: 组件创作：</span><span class="sxs-lookup"><span data-stu-id="67b40-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable :::no-loc(Razor)::: component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="67b40-137">手动将该文件添加到所生成项目的根目录中。</span><span class="sxs-lookup"><span data-stu-id="67b40-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="67b40-138">若要将库添加到现有项目中，请在命令行界面中使用 [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) 命令。</span><span class="sxs-lookup"><span data-stu-id="67b40-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="67b40-139">在下面的示例中，将 RCL 添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="67b40-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="67b40-140">使用指向库的路径从应用的项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="67b40-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="67b40-141">使用库组件</span><span class="sxs-lookup"><span data-stu-id="67b40-141">Consume a library component</span></span>

<span data-ttu-id="67b40-142">若要使用另一项目的库中定义的组件，请使用以下任一方法：</span><span class="sxs-lookup"><span data-stu-id="67b40-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="67b40-143">使用命名空间的完整类型名称。</span><span class="sxs-lookup"><span data-stu-id="67b40-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="67b40-144">使用 :::no-loc(Razor)::: 的 [`@using`](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="67b40-144">Use :::no-loc(Razor):::'s [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="67b40-145">单个组件可以按名称添加。</span><span class="sxs-lookup"><span data-stu-id="67b40-145">Individual components can be added by name.</span></span>

<span data-ttu-id="67b40-146">在下面的示例中，`ComponentLibrary` 是一个包含 `Component1` 组件 (`Component1.razor`) 的组件库。</span><span class="sxs-lookup"><span data-stu-id="67b40-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="67b40-147">`Component1` 是库创建成功后，RCL 项目模板会自动添加的示例组件。</span><span class="sxs-lookup"><span data-stu-id="67b40-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="67b40-148">使用 `Component1` 组件的命名空间引用它：</span><span class="sxs-lookup"><span data-stu-id="67b40-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="67b40-149">或者，使用 [`@using`](xref:mvc/views/razor#using) 指令将库纳入范围，并在没有命名空间的情况下使用该组件：</span><span class="sxs-lookup"><span data-stu-id="67b40-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="67b40-150">可以选择在顶级 `_Import.razor` 文件中包含 `@using ComponentLibrary` 指令，使库的组件可用于整个项目。</span><span class="sxs-lookup"><span data-stu-id="67b40-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="67b40-151">将指令添加到任何级别的 `_Import.razor` 文件，将命名空间应用于文件夹中的单个组件或一组组件。</span><span class="sxs-lookup"><span data-stu-id="67b40-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This :::no-loc(Blazor)::: component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="67b40-152">若要提供 `Component1` 的 `my-component` CSS 类，请在应用的 `wwwroot/index.html` 文件 (:::no-loc(Blazor WebAssembly):::) 或 `Pages/_Host.cshtml` 文件 (:::no-loc(Blazor Server):::) 中链接到库的样式表：</span><span class="sxs-lookup"><span data-stu-id="67b40-152">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="67b40-153">创建包括静态资源的 :::no-loc(Razor)::: 组件类库</span><span class="sxs-lookup"><span data-stu-id="67b40-153">Create a :::no-loc(Razor)::: components class library with static assets</span></span>

<span data-ttu-id="67b40-154">RCL 可以包括静态资产。</span><span class="sxs-lookup"><span data-stu-id="67b40-154">An RCL can include static assets.</span></span> <span data-ttu-id="67b40-155">静态资产可用于任何使用该库的应用。</span><span class="sxs-lookup"><span data-stu-id="67b40-155">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="67b40-156">有关详细信息，请参阅 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>。</span><span class="sxs-lookup"><span data-stu-id="67b40-156">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="67b40-157">向多个托管的 :::no-loc(Blazor)::: 应用提供组件和静态资产</span><span class="sxs-lookup"><span data-stu-id="67b40-157">Supply components and static assets to multiple hosted :::no-loc(Blazor)::: apps</span></span>

<span data-ttu-id="67b40-158">有关详细信息，请参阅 <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>。</span><span class="sxs-lookup"><span data-stu-id="67b40-158">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="67b40-159">:::no-loc(Blazor WebAssembly)::: 的浏览器兼容性分析器</span><span class="sxs-lookup"><span data-stu-id="67b40-159">Browser compatibility analyzer for :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="67b40-160">:::no-loc(Blazor WebAssembly)::: 应用面向整个 .NET API 外围应用，但由于浏览器沙盒约束，并非所有 .NET API 在 WebAssembly 上都受支持。</span><span class="sxs-lookup"><span data-stu-id="67b40-160">:::no-loc(Blazor WebAssembly)::: apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="67b40-161">在 WebAssembly 上运行时，不支持的 API 将引发 <xref:System.PlatformNotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="67b40-161">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="67b40-162">当应用使用应用目标平台不支持的 API 时，平台兼容性分析器会向开发人员发出警告。</span><span class="sxs-lookup"><span data-stu-id="67b40-162">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="67b40-163">对于 :::no-loc(Blazor WebAssembly)::: 应用，这意味着需要检查浏览器是否支持这些 API。</span><span class="sxs-lookup"><span data-stu-id="67b40-163">For :::no-loc(Blazor WebAssembly)::: apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="67b40-164">为兼容性分析器注释 .NET Framework API 是一个持续的过程，因此并不是所有的 .NET Framework API 当前都已进行注释。</span><span class="sxs-lookup"><span data-stu-id="67b40-164">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="67b40-165">:::no-loc(Blazor WebAssembly)::: 和 :::no-loc(Razor)::: 类库项目自动启用浏览器兼容性检查，方法是使用 `SupportedPlatform` MSBuild 项将 `browser` 添加为支持的平台。</span><span class="sxs-lookup"><span data-stu-id="67b40-165">:::no-loc(Blazor WebAssembly)::: and :::no-loc(Razor)::: class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="67b40-166">库开发人员可以手动将 `SupportedPlatform` 项添加到库的项目文件以启用该功能：</span><span class="sxs-lookup"><span data-stu-id="67b40-166">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="67b40-167">编写库时，通过将 `browser` 指定为 <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> 来指示浏览器不支持特定的 API：</span><span class="sxs-lookup"><span data-stu-id="67b40-167">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="67b40-168">有关详细信息，请参阅[在特定平台（dotnet/designs GitHub 存储库）上将 API 注释为不受支持](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms)。</span><span class="sxs-lookup"><span data-stu-id="67b40-168">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="67b40-169">:::no-loc(Blazor)::: JavaScript 隔离和对象引用</span><span class="sxs-lookup"><span data-stu-id="67b40-169">:::no-loc(Blazor)::: JavaScript isolation and object references</span></span>

<span data-ttu-id="67b40-170">:::no-loc(Blazor)::: 在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离。</span><span class="sxs-lookup"><span data-stu-id="67b40-170">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="67b40-171">JavaScript 隔离具有以下优势：</span><span class="sxs-lookup"><span data-stu-id="67b40-171">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="67b40-172">导入的 JavaScript 不再污染全局命名空间。</span><span class="sxs-lookup"><span data-stu-id="67b40-172">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="67b40-173">库和组件的使用者不需要手动导入相关的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="67b40-173">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="67b40-174">有关详细信息，请参阅 <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>。</span><span class="sxs-lookup"><span data-stu-id="67b40-174">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="67b40-175">生成并打包库，再将其传送到 NuGet</span><span class="sxs-lookup"><span data-stu-id="67b40-175">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="67b40-176">由于组件库是标准的 .NET 库，因此将它们打包并传送到 NuGet 与将任何库打包并传送到 NuGet 没有什么区别。</span><span class="sxs-lookup"><span data-stu-id="67b40-176">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="67b40-177">在命令行界面中使用 [`dotnet pack`](/dotnet/core/tools/dotnet-pack) 命令，执行打包操作：</span><span class="sxs-lookup"><span data-stu-id="67b40-177">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="67b40-178">在命令行界面中使用 [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) 命令，将包上传到 NuGet。</span><span class="sxs-lookup"><span data-stu-id="67b40-178">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67b40-179">其他资源</span><span class="sxs-lookup"><span data-stu-id="67b40-179">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="67b40-180">向库添加 XML 中间语言 (IL) 裁边器配置文件</span><span class="sxs-lookup"><span data-stu-id="67b40-180">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="67b40-181">向库添加 XML 中间语言 (IL) 链接器配置文件</span><span class="sxs-lookup"><span data-stu-id="67b40-181">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
