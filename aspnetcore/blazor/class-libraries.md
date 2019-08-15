---
title: ASP.NET Core Razor 组件类库
author: guardrex
description: 了解如何在 Blazor 应用程序中将组件包含在外部组件库中。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/class-libraries
ms.openlocfilehash: 6e93d48bbc684845952c3db8935ccc8b190044b7
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030345"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="b9294-103">ASP.NET Core Razor 组件类库</span><span class="sxs-lookup"><span data-stu-id="b9294-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="b9294-104">作者: [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="b9294-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="b9294-105">可在[Razor 类库 (RCL)](xref:razor-pages/ui-class)中跨项目共享组件。</span><span class="sxs-lookup"><span data-stu-id="b9294-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="b9294-106">*Razor 组件类库*可以包含在其中:</span><span class="sxs-lookup"><span data-stu-id="b9294-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="b9294-107">解决方案中的另一个项目。</span><span class="sxs-lookup"><span data-stu-id="b9294-107">Another project in the solution.</span></span>
* <span data-ttu-id="b9294-108">NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="b9294-108">A NuGet package.</span></span>
* <span data-ttu-id="b9294-109">引用的 .NET 库。</span><span class="sxs-lookup"><span data-stu-id="b9294-109">A referenced .NET library.</span></span>

<span data-ttu-id="b9294-110">正如组件是常规 .NET 类型一样, RCL 提供的组件是普通的 .NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="b9294-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="b9294-111">创建 RCL</span><span class="sxs-lookup"><span data-stu-id="b9294-111">Create an RCL</span></span>

<span data-ttu-id="b9294-112">请按照<xref:blazor/get-started>文章中的指导配置 Blazor 的环境。</span><span class="sxs-lookup"><span data-stu-id="b9294-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b9294-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9294-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b9294-114">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="b9294-114">Create a new project.</span></span>
1. <span data-ttu-id="b9294-115">选择 " **Razor 类库**"。</span><span class="sxs-lookup"><span data-stu-id="b9294-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="b9294-116">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="b9294-116">Select **Next**.</span></span>
1. <span data-ttu-id="b9294-117">在 "**创建新的 Razor 类库**" 对话框中, 选择 "**创建**"。</span><span class="sxs-lookup"><span data-stu-id="b9294-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="b9294-118">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="b9294-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b9294-119">本主题中的示例使用项目名称`MyComponentLib1`。</span><span class="sxs-lookup"><span data-stu-id="b9294-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="b9294-120">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="b9294-120">Select **Create**.</span></span>
1. <span data-ttu-id="b9294-121">将 RCL 添加到解决方案:</span><span class="sxs-lookup"><span data-stu-id="b9294-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="b9294-122">右键单击解决方案。</span><span class="sxs-lookup"><span data-stu-id="b9294-122">Right-click the solution.</span></span> <span data-ttu-id="b9294-123">选择 "**添加** > **现有项目**"。</span><span class="sxs-lookup"><span data-stu-id="b9294-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="b9294-124">导航到 RCL 的项目文件。</span><span class="sxs-lookup"><span data-stu-id="b9294-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="b9294-125">选择 RCL 的项目文件 ( *.csproj*)。</span><span class="sxs-lookup"><span data-stu-id="b9294-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="b9294-126">在应用中添加对 RCL 的引用:</span><span class="sxs-lookup"><span data-stu-id="b9294-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="b9294-127">右键单击应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="b9294-127">Right-click the app project.</span></span> <span data-ttu-id="b9294-128">选择 "**添加** > **引用**"。</span><span class="sxs-lookup"><span data-stu-id="b9294-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="b9294-129">选择 RCL 项目。</span><span class="sxs-lookup"><span data-stu-id="b9294-129">Select the RCL project.</span></span> <span data-ttu-id="b9294-130">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="b9294-130">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b9294-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b9294-131">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="b9294-132">在命令行界面中, 将`razorclasslib` **Razor 类库**模板 () 与[dotnet new](/dotnet/core/tools/dotnet-new)命令一起使用。</span><span class="sxs-lookup"><span data-stu-id="b9294-132">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="b9294-133">在下面的示例中, 创建了一个名`MyComponentLib1`为的 RCL。</span><span class="sxs-lookup"><span data-stu-id="b9294-133">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="b9294-134">执行命令时, `MyComponentLib1`将自动创建包含的文件夹:</span><span class="sxs-lookup"><span data-stu-id="b9294-134">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="b9294-135">若要将库添加到现有项目, 请在命令行界面中使用[dotnet add reference](/dotnet/core/tools/dotnet-add-reference)命令。</span><span class="sxs-lookup"><span data-stu-id="b9294-135">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="b9294-136">在下面的示例中, 将 RCL 添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="b9294-136">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="b9294-137">在应用程序的项目文件夹中执行以下命令, 并在其中包含库的路径:</span><span class="sxs-lookup"><span data-stu-id="b9294-137">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a><span data-ttu-id="b9294-138">客户端应用不支持 RCLs</span><span class="sxs-lookup"><span data-stu-id="b9294-138">RCLs not supported for client-side apps</span></span>

<span data-ttu-id="b9294-139">在当前 ASP.NET Core 3.0 Preview 中, Razor 类库与 Blazor 客户端应用不兼容。</span><span class="sxs-lookup"><span data-stu-id="b9294-139">In the current ASP.NET Core 3.0 Preview, Razor class libraries aren't compatible with Blazor client-side apps.</span></span> <span data-ttu-id="b9294-140">对于 Blazor 客户端应用, 请在命令行界面中使用`blazorlib`模板创建的 Blazor 组件库:</span><span class="sxs-lookup"><span data-stu-id="b9294-140">For Blazor client-side apps, use a Blazor component library created by the `blazorlib` template in a command shell:</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="b9294-141">使用模板的`blazorlib`组件库可以包含静态文件, 如图像、JavaScript 和样式表。</span><span class="sxs-lookup"><span data-stu-id="b9294-141">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="b9294-142">在生成时, 静态文件嵌入到生成的程序集文件 ( *.dll*) 中, 这样就可以使用这些组件, 而无需担心如何包含其资源。</span><span class="sxs-lookup"><span data-stu-id="b9294-142">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="b9294-143">`content`目录中包含的所有文件都将被标记为嵌入的资源。</span><span class="sxs-lookup"><span data-stu-id="b9294-143">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="b9294-144">使用库组件</span><span class="sxs-lookup"><span data-stu-id="b9294-144">Consume a library component</span></span>

<span data-ttu-id="b9294-145">若要使用另一个项目的库中定义的组件, 请使用以下方法之一:</span><span class="sxs-lookup"><span data-stu-id="b9294-145">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="b9294-146">使用带有命名空间的完整类型名称。</span><span class="sxs-lookup"><span data-stu-id="b9294-146">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="b9294-147">使用 Razor 的[ \@using](xref:mvc/views/razor#using)指令。</span><span class="sxs-lookup"><span data-stu-id="b9294-147">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="b9294-148">可以按名称添加单个组件。</span><span class="sxs-lookup"><span data-stu-id="b9294-148">Individual components can be added by name.</span></span>

<span data-ttu-id="b9294-149">在下面的示例中`MyComponentLib1` , 是`SalesReport`包含组件的组件库。</span><span class="sxs-lookup"><span data-stu-id="b9294-149">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="b9294-150">可以`SalesReport`使用命名空间的完整类型名称引用该组件:</span><span class="sxs-lookup"><span data-stu-id="b9294-150">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="b9294-151">如果库与`@using`指令一起使用, 也可以引用组件:</span><span class="sxs-lookup"><span data-stu-id="b9294-151">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="b9294-152">在顶级 *_Import*文件中包含指令,使库的组件可用于整个项目。`@using MyComponentLib1`</span><span class="sxs-lookup"><span data-stu-id="b9294-152">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="b9294-153">将指令添加到任何级别的 *_Import*文件, 以将命名空间应用于文件夹中的单个页面或一组页面。</span><span class="sxs-lookup"><span data-stu-id="b9294-153">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="b9294-154">生成、打包和传送到 NuGet</span><span class="sxs-lookup"><span data-stu-id="b9294-154">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="b9294-155">由于组件库是标准的 .NET 库, 因此将其打包并传送到 NuGet 与将任何库打包到 NuGet 没有什么不同。</span><span class="sxs-lookup"><span data-stu-id="b9294-155">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="b9294-156">在命令行界面中使用[dotnet pack](/dotnet/core/tools/dotnet-pack)命令执行打包:</span><span class="sxs-lookup"><span data-stu-id="b9294-156">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="b9294-157">在命令行界面中使用[dotnet NuGet publish](/dotnet/core/tools/dotnet-nuget-push)命令将包上传到 nuget:</span><span class="sxs-lookup"><span data-stu-id="b9294-157">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command in a command shell:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="b9294-158">使用`blazorlib`模板时, NuGet 包中将包含静态资源。</span><span class="sxs-lookup"><span data-stu-id="b9294-158">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="b9294-159">库使用者会自动接收脚本和样式表, 因此不需要使用者手动安装资源。</span><span class="sxs-lookup"><span data-stu-id="b9294-159">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="b9294-160">使用静态资产创建 Razor 组件类库</span><span class="sxs-lookup"><span data-stu-id="b9294-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="b9294-161">RCL 可以包括静态资产。</span><span class="sxs-lookup"><span data-stu-id="b9294-161">An RCL can include static assets.</span></span> <span data-ttu-id="b9294-162">静态资产可用于使用库的任何应用。</span><span class="sxs-lookup"><span data-stu-id="b9294-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="b9294-163">有关详细信息，请参阅 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets> 。</span><span class="sxs-lookup"><span data-stu-id="b9294-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9294-164">其他资源</span><span class="sxs-lookup"><span data-stu-id="b9294-164">Additional resources</span></span>

* <xref:razor-pages/ui-class>
