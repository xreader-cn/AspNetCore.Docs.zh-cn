---
title: ASP.NET Core Razor 组件类库
author: guardrex
description: 了解如何从外部组件库将组件包含在 Blazor 应用中。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218761"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="1850c-103">ASP.NET Core Razor 组件类库</span><span class="sxs-lookup"><span data-stu-id="1850c-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="1850c-104">作者：[Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="1850c-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="1850c-105">组件可在 [Razor 类库 (RCL)](xref:razor-pages/ui-class) 中跨项目共享。</span><span class="sxs-lookup"><span data-stu-id="1850c-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="1850c-106">“Razor 组件类库”可以包含在以下各项中  ：</span><span class="sxs-lookup"><span data-stu-id="1850c-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="1850c-107">解决方案中的另一个项目。</span><span class="sxs-lookup"><span data-stu-id="1850c-107">Another project in the solution.</span></span>
* <span data-ttu-id="1850c-108">NuGet 程序包。</span><span class="sxs-lookup"><span data-stu-id="1850c-108">A NuGet package.</span></span>
* <span data-ttu-id="1850c-109">引用的 .NET 库。</span><span class="sxs-lookup"><span data-stu-id="1850c-109">A referenced .NET library.</span></span>

<span data-ttu-id="1850c-110">正如组件是常规的 .NET 类型一样，RCL 提供的组件也是普通的 .NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="1850c-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="1850c-111">创建 RCL</span><span class="sxs-lookup"><span data-stu-id="1850c-111">Create an RCL</span></span>

<span data-ttu-id="1850c-112">按照 <xref:blazor/get-started> 文章中的指导，为 Blazor 配置环境。</span><span class="sxs-lookup"><span data-stu-id="1850c-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1850c-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1850c-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1850c-114">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="1850c-114">Create a new project.</span></span>
1. <span data-ttu-id="1850c-115">选择“Razor 类库”  。</span><span class="sxs-lookup"><span data-stu-id="1850c-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="1850c-116">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="1850c-116">Select **Next**.</span></span>
1. <span data-ttu-id="1850c-117">在“创建新的 Razor 类库”对话框中，选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="1850c-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="1850c-118">在“项目名称”字段提供项目名称，或接受默认项目名称  。</span><span class="sxs-lookup"><span data-stu-id="1850c-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="1850c-119">本主题中的示例使用项目名称 `MyComponentLib1`。</span><span class="sxs-lookup"><span data-stu-id="1850c-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="1850c-120">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="1850c-120">Select **Create**.</span></span>
1. <span data-ttu-id="1850c-121">将 RCL 添加到一个解决方案：</span><span class="sxs-lookup"><span data-stu-id="1850c-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="1850c-122">右键单击该解决方案。</span><span class="sxs-lookup"><span data-stu-id="1850c-122">Right-click the solution.</span></span> <span data-ttu-id="1850c-123">选择“添加” > “现有项目”   。</span><span class="sxs-lookup"><span data-stu-id="1850c-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="1850c-124">导航到 RCL 的项目文件。</span><span class="sxs-lookup"><span data-stu-id="1850c-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="1850c-125">选择 RCL 的项目文件 (.csproj)  。</span><span class="sxs-lookup"><span data-stu-id="1850c-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="1850c-126">从应用中添加 RCL 的引用：</span><span class="sxs-lookup"><span data-stu-id="1850c-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="1850c-127">右键单击该应用项目。</span><span class="sxs-lookup"><span data-stu-id="1850c-127">Right-click the app project.</span></span> <span data-ttu-id="1850c-128">选择“添加” > “引用”   。</span><span class="sxs-lookup"><span data-stu-id="1850c-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="1850c-129">选择 RCL 项目。</span><span class="sxs-lookup"><span data-stu-id="1850c-129">Select the RCL project.</span></span> <span data-ttu-id="1850c-130">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="1850c-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="1850c-131">如果在从模板生成 RCL 时选中了“支持页和视图”复选框，还请通过以下内容，将“Imports.razor”文件添加到所生成项目的根目录中，以启用 Razor 组件创作   ：</span><span class="sxs-lookup"><span data-stu-id="1850c-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="1850c-132">手动将该文件添加到所生成项目的根目录中。</span><span class="sxs-lookup"><span data-stu-id="1850c-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1850c-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1850c-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="1850c-134">在命令行界面中，将 Razor 类库模板 (`razorclasslib`) 与 [dotnet new](/dotnet/core/tools/dotnet-new) 命令一起使用  。</span><span class="sxs-lookup"><span data-stu-id="1850c-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="1850c-135">下面的示例创建了名为 `MyComponentLib1` 的 RCL。</span><span class="sxs-lookup"><span data-stu-id="1850c-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="1850c-136">执行命令时，自动创建包含 `MyComponentLib1` 的文件夹：</span><span class="sxs-lookup"><span data-stu-id="1850c-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="1850c-137">如果在从模板生成 RCL 时使用 `-s|--support-pages-and-views` 开关，还请通过以下内容将“Imports.razor”文件添加到所生成项目的根目录中，以启用 Razor 组件创作  ：</span><span class="sxs-lookup"><span data-stu-id="1850c-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="1850c-138">手动将该文件添加到所生成项目的根目录中。</span><span class="sxs-lookup"><span data-stu-id="1850c-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="1850c-139">若要将库添加到现有项目中，请在命令行界面中使用 [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) 命令。</span><span class="sxs-lookup"><span data-stu-id="1850c-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="1850c-140">在下面的示例中，将 RCL 添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="1850c-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="1850c-141">使用指向库的路径从应用的项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1850c-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="1850c-142">使用库组件</span><span class="sxs-lookup"><span data-stu-id="1850c-142">Consume a library component</span></span>

<span data-ttu-id="1850c-143">若要使用另一项目的库中定义的组件，请使用以下任一方法：</span><span class="sxs-lookup"><span data-stu-id="1850c-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="1850c-144">使用命名空间的完整类型名称。</span><span class="sxs-lookup"><span data-stu-id="1850c-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="1850c-145">使用 Razor 的 [\@using](xref:mvc/views/razor#using) 指令。</span><span class="sxs-lookup"><span data-stu-id="1850c-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="1850c-146">单个组件可以按名称添加。</span><span class="sxs-lookup"><span data-stu-id="1850c-146">Individual components can be added by name.</span></span>

<span data-ttu-id="1850c-147">在下面的示例中，`MyComponentLib1` 是一个包含 `SalesReport` 组件的组件库。</span><span class="sxs-lookup"><span data-stu-id="1850c-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="1850c-148">`SalesReport` 组件可通过其命名空间的完整类型名称进行引用：</span><span class="sxs-lookup"><span data-stu-id="1850c-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="1850c-149">如果将库纳入作用域内，该组件也可通过 `@using` 指令进行引用：</span><span class="sxs-lookup"><span data-stu-id="1850c-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="1850c-150">在顶级 _Import.razor 文件中包含 `@using MyComponentLib1` 指令，使库的组件可用于整个项目  。</span><span class="sxs-lookup"><span data-stu-id="1850c-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="1850c-151">将指令添加到任何级别的 _Import.razor 文件，将命名空间应用于文件夹中的单个页面或一组页面  。</span><span class="sxs-lookup"><span data-stu-id="1850c-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="1850c-152">创建包括静态资源的 Razor 组件类库</span><span class="sxs-lookup"><span data-stu-id="1850c-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="1850c-153">RCL 可以包括静态资产。</span><span class="sxs-lookup"><span data-stu-id="1850c-153">An RCL can include static assets.</span></span> <span data-ttu-id="1850c-154">静态资产可用于任何使用该库的应用。</span><span class="sxs-lookup"><span data-stu-id="1850c-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="1850c-155">有关详细信息，请参阅 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>。</span><span class="sxs-lookup"><span data-stu-id="1850c-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="1850c-156">生成并打包库，再将其传送到 NuGet</span><span class="sxs-lookup"><span data-stu-id="1850c-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="1850c-157">由于组件库是标准的 .NET 库，因此将它们打包并传送到 NuGet 与将任何库打包并传送到 NuGet 没有什么区别。</span><span class="sxs-lookup"><span data-stu-id="1850c-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="1850c-158">在命令行界面中使用 [dotnet pack](/dotnet/core/tools/dotnet-pack) 命令，执行打包操作：</span><span class="sxs-lookup"><span data-stu-id="1850c-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="1850c-159">在命令行界面中使用 [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) 命令，将包上传到 NuGet。</span><span class="sxs-lookup"><span data-stu-id="1850c-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1850c-160">其他资源</span><span class="sxs-lookup"><span data-stu-id="1850c-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="1850c-161">将 XML 链接器配置文件添加到库</span><span class="sxs-lookup"><span data-stu-id="1850c-161">Add an XML linker configuration file to a library</span></span>](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
