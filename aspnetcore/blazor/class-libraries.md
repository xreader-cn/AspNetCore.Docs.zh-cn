---
title: ASP.NET Core Razor 组件类库
author: guardrex
description: 了解如何在 Blazor 应用程序中将组件包含在外部组件库中。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/class-libraries
ms.openlocfilehash: b5857f2cf22bde801deeeaf227817fdf99862f4a
ms.sourcegitcommit: 4cb0c7e74355f2e87c60e2a196f842b937247a99
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69545781"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="49453-103">ASP.NET Core Razor 组件类库</span><span class="sxs-lookup"><span data-stu-id="49453-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="49453-104">作者: [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="49453-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="49453-105">可在[Razor 类库 (RCL)](xref:razor-pages/ui-class)中跨项目共享组件。</span><span class="sxs-lookup"><span data-stu-id="49453-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="49453-106">*Razor 组件类库*可以包含在其中:</span><span class="sxs-lookup"><span data-stu-id="49453-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="49453-107">解决方案中的另一个项目。</span><span class="sxs-lookup"><span data-stu-id="49453-107">Another project in the solution.</span></span>
* <span data-ttu-id="49453-108">NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="49453-108">A NuGet package.</span></span>
* <span data-ttu-id="49453-109">引用的 .NET 库。</span><span class="sxs-lookup"><span data-stu-id="49453-109">A referenced .NET library.</span></span>

<span data-ttu-id="49453-110">正如组件是常规 .NET 类型一样, RCL 提供的组件是普通的 .NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="49453-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="49453-111">创建 RCL</span><span class="sxs-lookup"><span data-stu-id="49453-111">Create an RCL</span></span>

<span data-ttu-id="49453-112">请按照<xref:blazor/get-started>文章中的指导配置 Blazor 的环境。</span><span class="sxs-lookup"><span data-stu-id="49453-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="49453-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49453-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="49453-114">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="49453-114">Create a new project.</span></span>
1. <span data-ttu-id="49453-115">选择 " **Razor 类库**"。</span><span class="sxs-lookup"><span data-stu-id="49453-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="49453-116">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="49453-116">Select **Next**.</span></span>
1. <span data-ttu-id="49453-117">在 "**创建新的 Razor 类库**" 对话框中, 选择 "**创建**"。</span><span class="sxs-lookup"><span data-stu-id="49453-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="49453-118">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="49453-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="49453-119">本主题中的示例使用项目名称`MyComponentLib1`。</span><span class="sxs-lookup"><span data-stu-id="49453-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="49453-120">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="49453-120">Select **Create**.</span></span>
1. <span data-ttu-id="49453-121">将 RCL 添加到解决方案:</span><span class="sxs-lookup"><span data-stu-id="49453-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="49453-122">右键单击解决方案。</span><span class="sxs-lookup"><span data-stu-id="49453-122">Right-click the solution.</span></span> <span data-ttu-id="49453-123">选择 "**添加** > **现有项目**"。</span><span class="sxs-lookup"><span data-stu-id="49453-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="49453-124">导航到 RCL 的项目文件。</span><span class="sxs-lookup"><span data-stu-id="49453-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="49453-125">选择 RCL 的项目文件 ( *.csproj*)。</span><span class="sxs-lookup"><span data-stu-id="49453-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="49453-126">在应用中添加对 RCL 的引用:</span><span class="sxs-lookup"><span data-stu-id="49453-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="49453-127">右键单击应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="49453-127">Right-click the app project.</span></span> <span data-ttu-id="49453-128">选择 "**添加** > **引用**"。</span><span class="sxs-lookup"><span data-stu-id="49453-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="49453-129">选择 RCL 项目。</span><span class="sxs-lookup"><span data-stu-id="49453-129">Select the RCL project.</span></span> <span data-ttu-id="49453-130">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="49453-130">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="49453-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="49453-131">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="49453-132">在命令行界面中, 将`razorclasslib` **Razor 类库**模板 () 与[dotnet new](/dotnet/core/tools/dotnet-new)命令一起使用。</span><span class="sxs-lookup"><span data-stu-id="49453-132">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="49453-133">在下面的示例中, 创建了一个名`MyComponentLib1`为的 RCL。</span><span class="sxs-lookup"><span data-stu-id="49453-133">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="49453-134">执行命令时, `MyComponentLib1`将自动创建包含的文件夹:</span><span class="sxs-lookup"><span data-stu-id="49453-134">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="49453-135">若要将库添加到现有项目, 请在命令行界面中使用[dotnet add reference](/dotnet/core/tools/dotnet-add-reference)命令。</span><span class="sxs-lookup"><span data-stu-id="49453-135">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="49453-136">在下面的示例中, 将 RCL 添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="49453-136">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="49453-137">在应用程序的项目文件夹中执行以下命令, 并在其中包含库的路径:</span><span class="sxs-lookup"><span data-stu-id="49453-137">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="49453-138">使用库组件</span><span class="sxs-lookup"><span data-stu-id="49453-138">Consume a library component</span></span>

<span data-ttu-id="49453-139">若要使用另一个项目的库中定义的组件, 请使用以下方法之一:</span><span class="sxs-lookup"><span data-stu-id="49453-139">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="49453-140">使用带有命名空间的完整类型名称。</span><span class="sxs-lookup"><span data-stu-id="49453-140">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="49453-141">使用 Razor 的[ \@using](xref:mvc/views/razor#using)指令。</span><span class="sxs-lookup"><span data-stu-id="49453-141">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="49453-142">可以按名称添加单个组件。</span><span class="sxs-lookup"><span data-stu-id="49453-142">Individual components can be added by name.</span></span>

<span data-ttu-id="49453-143">在下面的示例中`MyComponentLib1` , 是`SalesReport`包含组件的组件库。</span><span class="sxs-lookup"><span data-stu-id="49453-143">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="49453-144">可以`SalesReport`使用命名空间的完整类型名称引用该组件:</span><span class="sxs-lookup"><span data-stu-id="49453-144">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="49453-145">如果库与`@using`指令一起使用, 也可以引用组件:</span><span class="sxs-lookup"><span data-stu-id="49453-145">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="49453-146">在顶级 *_Import*文件中包含指令,使库的组件可用于整个项目。`@using MyComponentLib1`</span><span class="sxs-lookup"><span data-stu-id="49453-146">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="49453-147">将指令添加到任何级别的 *_Import*文件, 以将命名空间应用于文件夹中的单个页面或一组页面。</span><span class="sxs-lookup"><span data-stu-id="49453-147">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="49453-148">生成、打包和传送到 NuGet</span><span class="sxs-lookup"><span data-stu-id="49453-148">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="49453-149">由于组件库是标准的 .NET 库, 因此将其打包并传送到 NuGet 与将任何库打包到 NuGet 没有什么不同。</span><span class="sxs-lookup"><span data-stu-id="49453-149">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="49453-150">在命令行界面中使用[dotnet pack](/dotnet/core/tools/dotnet-pack)命令执行打包:</span><span class="sxs-lookup"><span data-stu-id="49453-150">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="49453-151">在命令行界面中使用[dotnet NuGet publish](/dotnet/core/tools/dotnet-nuget-push)命令将包上传到 nuget:</span><span class="sxs-lookup"><span data-stu-id="49453-151">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command in a command shell:</span></span>

```console
dotnet nuget publish
```

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="49453-152">使用静态资产创建 Razor 组件类库</span><span class="sxs-lookup"><span data-stu-id="49453-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="49453-153">RCL 可以包括静态资产。</span><span class="sxs-lookup"><span data-stu-id="49453-153">An RCL can include static assets.</span></span> <span data-ttu-id="49453-154">静态资产可用于使用库的任何应用。</span><span class="sxs-lookup"><span data-stu-id="49453-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="49453-155">有关详细信息，请参阅 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets> 。</span><span class="sxs-lookup"><span data-stu-id="49453-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49453-156">其他资源</span><span class="sxs-lookup"><span data-stu-id="49453-156">Additional resources</span></span>

* <xref:razor-pages/ui-class>
