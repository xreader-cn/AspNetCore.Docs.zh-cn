---
title: Razor 组件类库
author: guardrex
description: 发现如何组件可以包含在外部组件库中的 Razor 组件应用程序。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/14/2019
uid: razor-components/class-libraries
ms.openlocfilehash: 1064ad60d90af15af483ba9bca5ed85fb63c2924
ms.sourcegitcommit: 10e14b85490f064395e9b2f423d21e3c2d39ed8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "59515353"
---
# <a name="razor-components-class-libraries"></a><span data-ttu-id="8b3ee-103">Razor 组件类库</span><span class="sxs-lookup"><span data-stu-id="8b3ee-103">Razor Components Class Libraries</span></span>

<span data-ttu-id="8b3ee-104">通过[Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="8b3ee-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="8b3ee-105">跨项目，可以在 Razor 类库中共享组件。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-105">Components can be shared in Razor class libraries across projects.</span></span> <span data-ttu-id="8b3ee-106">组件可以从包含：</span><span class="sxs-lookup"><span data-stu-id="8b3ee-106">Components can be included from:</span></span>

* <span data-ttu-id="8b3ee-107">在解决方案中的另一个项目。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-107">Another project in the solution.</span></span>
* <span data-ttu-id="8b3ee-108">NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-108">A NuGet package.</span></span>
* <span data-ttu-id="8b3ee-109">引用的.NET 库。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-109">A referenced .NET library.</span></span>

<span data-ttu-id="8b3ee-110">正如组件是常规的.NET 类型，由 Razor 类库提供的组件是普通的.NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-110">Just as components are regular .NET types, components provided by Razor class libraries are normal .NET assemblies.</span></span>

<span data-ttu-id="8b3ee-111">使用`razorclasslib`与 （Razor 类库） 模板[dotnet 新](/dotnet/core/tools/dotnet-new)命令：</span><span class="sxs-lookup"><span data-stu-id="8b3ee-111">Use the `razorclasslib` (Razor class library) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command:</span></span>

```console
dotnet new razorclasslib -o MyComponentLib1
```

<span data-ttu-id="8b3ee-112">添加 Razor 组件文件 (*.razor*) 对 Razor 类库。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-112">Add Razor Component files (*.razor*) to the Razor class library.</span></span>

<span data-ttu-id="8b3ee-113">若要将库添加到现有项目，请使用[dotnet sln](/dotnet/core/tools/dotnet-sln)命令：</span><span class="sxs-lookup"><span data-stu-id="8b3ee-113">To add the library to an existing project, use the [dotnet sln](/dotnet/core/tools/dotnet-sln) command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b3ee-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b3ee-114">Visual Studio</span></span>](#tab/visual-studio)

```console
dotnet sln add .\MyComponentLib1
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8b3ee-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b3ee-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet add WebApplication1 reference MyComponentLib1
```

---

> [!NOTE]
> <span data-ttu-id="8b3ee-116">Razor 类库不兼容 ASP.NET Core 预览版 3 中的 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-116">Razor class libraries aren't compatible with Blazor apps in ASP.NET Core Preview 3.</span></span>
>
> <span data-ttu-id="8b3ee-117">若要创建可与 Blazor 和 Razor 组件应用程序共享的库组件，使用创建的 Blazor 类库`blazorlib`模板。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-117">To create components in a library that can be shared with Blazor and Razor Components apps, use a Blazor class library created by the `blazorlib` template.</span></span>
>
> <span data-ttu-id="8b3ee-118">Razor 类库不支持在 ASP.NET Core 预览版 3 中的静态资产。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-118">Razor class libraries don't support static assets in ASP.NET Core Preview 3.</span></span> <span data-ttu-id="8b3ee-119">使用的组件库`blazorlib`模板可以包括静态文件，如图像、 JavaScript 和样式表。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-119">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="8b3ee-120">在生成时，静态文件嵌入到生成的程序集文件 (*.dll*)，而无需担心如何包含其资源允许使用的组件。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-120">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="8b3ee-121">中包括的任何文件`content`目录标记为嵌入的资源。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-121">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="8b3ee-122">使用的库组件</span><span class="sxs-lookup"><span data-stu-id="8b3ee-122">Consume a library component</span></span>

<span data-ttu-id="8b3ee-123">为了使用在另一个项目中，库中定义的组件[ @addTagHelper ](xref:mvc/views/tag-helpers/intro#add-helper-label)必须使用指令。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-123">In order to consume components defined in a library in another project, the [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) directive must be used.</span></span> <span data-ttu-id="8b3ee-124">单个组件可能添加的名称。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-124">Individual components may be added by name.</span></span>

<span data-ttu-id="8b3ee-125">指令的常规格式为：</span><span class="sxs-lookup"><span data-stu-id="8b3ee-125">The general format of the directive is:</span></span>

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="8b3ee-126">例如，添加以下指令`Component1`的`MyComponentLib1`:</span><span class="sxs-lookup"><span data-stu-id="8b3ee-126">For example, the following directive adds `Component1` of `MyComponentLib1`:</span></span>

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1
```

<span data-ttu-id="8b3ee-127">但是，很常见，以包含所有中使用通配符的程序集的组件 (`*`):</span><span class="sxs-lookup"><span data-stu-id="8b3ee-127">However, it's common to include all of the components from an assembly using a wildcard (`*`):</span></span>

```cshtml
@addTagHelper *, MyComponentLib1
```

<span data-ttu-id="8b3ee-128">`@addTagHelper`指令将其纳入 *_ViewImport.cshtml*若要使组件可用于整个项目或已应用到单个页面或组的文件夹中的页面。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-128">The `@addTagHelper` directive can be included in *_ViewImport.cshtml* to make the components available for an entire project or applied to a single page or set of pages within a folder.</span></span> <span data-ttu-id="8b3ee-129">使用`@addTagHelper`指令后，组件库的组件可以使用像与应用位于同一程序集中。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-129">With the `@addTagHelper` directive in place, the components of the component library can be consumed as if they were in the same assembly as the app.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="8b3ee-130">生成、 包和发送到 NuGet</span><span class="sxs-lookup"><span data-stu-id="8b3ee-130">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="8b3ee-131">由于组件库是标准.NET 库，打包并传送到 NuGet 没有任何差别打包和传送到 NuGet 的任何库。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-131">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="8b3ee-132">使用执行打包[dotnet pack](/dotnet/core/tools/dotnet-pack)命令：</span><span class="sxs-lookup"><span data-stu-id="8b3ee-132">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command:</span></span>

```console
dotnet pack
```

<span data-ttu-id="8b3ee-133">将包上载到使用 NuGet [dotnet nuget 发布](/dotnet/core/tools/dotnet-nuget-push)命令：</span><span class="sxs-lookup"><span data-stu-id="8b3ee-133">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="8b3ee-134">当使用`blazorlib`模板，NuGet 包中包含静态资源。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-134">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="8b3ee-135">库的使用者自动接收脚本和样式表，因此使用者无需手动安装的资源。</span><span class="sxs-lookup"><span data-stu-id="8b3ee-135">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>
