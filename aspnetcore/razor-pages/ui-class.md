---
title: ASP.NET Core 的类库中的可重用 Razor UI
author: Rick-Anderson
description: 说明如何创建可重复使用 Razor UI 在 ASP.NET Core 中的类库中使用分部视图。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/22/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 5b83cb44302a5900ec7b2ccc049790b4c1ca57e5
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985382"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="f6d3a-103">在 ASP.NET Core 中使用 Razor 类库项目创建可重用的 UI</span><span class="sxs-lookup"><span data-stu-id="f6d3a-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="f6d3a-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f6d3a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f6d3a-105">Razor 视图、页、控制器、页模型、 [razor 组件](xref:blazor/class-libraries)、[视图组件](xref:mvc/views/view-components)和数据模型可以内置于 Razor 类库 (RCL) 中。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="f6d3a-106">RCL 可以打包并重复使用。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="f6d3a-107">应用程序可以包括 RCL，并重写其中包含的视图和页面。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="f6d3a-108">如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="f6d3a-109">此功能要求 [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="f6d3a-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

<span data-ttu-id="f6d3a-110">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f6d3a-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="f6d3a-111">创建一个包含 Razor UI 的类库</span><span class="sxs-lookup"><span data-stu-id="f6d3a-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f6d3a-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6d3a-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6d3a-113">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f6d3a-114">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="f6d3a-115">命名库（例如，“RazorClassLib”）>“确定”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-115">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="f6d3a-116">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-116">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="f6d3a-117">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-117">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="f6d3a-118">选择“Razor 类库” > “确定”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-118">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="f6d3a-119">RCL 具有以下项目文件:</span><span class="sxs-lookup"><span data-stu-id="f6d3a-119">An RCL has the following project file:</span></span>

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f6d3a-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f6d3a-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f6d3a-121">从命令行中，运行 `dotnet new razorclasslib`。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="f6d3a-122">例如：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-122">For example:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="f6d3a-123">有关详细信息，请查看 [dotnet new](/dotnet/core/tools/dotnet-new)。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-123">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="f6d3a-124">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-124">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="f6d3a-125">将 Razor 文件添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-125">Add Razor files to the RCL.</span></span>

<span data-ttu-id="f6d3a-126">ASP.NET Core 模板假定 RCL 内容位于*领域*文件夹。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-126">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="f6d3a-127">请参阅[RCL Pages layout](#afs) , 以创建在中`~/Pages`而不是`~/Areas/Pages`公开内容的 RCL。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-127">See [RCL Pages layout](#afs) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="referencing-rcl-content"></a><span data-ttu-id="f6d3a-128">引用 RCL 内容</span><span class="sxs-lookup"><span data-stu-id="f6d3a-128">Referencing RCL content</span></span>

<span data-ttu-id="f6d3a-129">可以通过以下方式引用 RCL：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-129">The RCL can be referenced by:</span></span>

* <span data-ttu-id="f6d3a-130">NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-130">NuGet package.</span></span> <span data-ttu-id="f6d3a-131">请参阅[创建 NuGet 包](/nuget/create-packages/creating-a-package)、[dotnet 添加包](/dotnet/core/tools/dotnet-add-package)和[创建和发布 NuGet 包](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-131">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="f6d3a-132">{ProjectName}.csproj。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-132">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="f6d3a-133">请查看 [dotnet-add 引用](/dotnet/core/tools/dotnet-add-reference)。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-133">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="f6d3a-134">演练：创建 RCL 项目并从 Razor Pages 项目使用</span><span class="sxs-lookup"><span data-stu-id="f6d3a-134">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="f6d3a-135">可以下载并测试[完整项目](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)，无需创建项目。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-135">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="f6d3a-136">示例下载包含附加代码和链接，以方便测试项目。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-136">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="f6d3a-137">可以在[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/6098)中留下反馈，评论下载示例和分步说明的对比。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-137">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="f6d3a-138">测试下载应用</span><span class="sxs-lookup"><span data-stu-id="f6d3a-138">Test the download app</span></span>

<span data-ttu-id="f6d3a-139">如果尚未下载已完成的应用，并更愿意创建演练项目，请跳转至[下一节](#create-an-rcl)。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-139">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f6d3a-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6d3a-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6d3a-141">在 Visual Studio 中打开 .sln 文件。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-141">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="f6d3a-142">运行应用。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-142">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f6d3a-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f6d3a-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f6d3a-144">在 cli 目录中的命令提示符下生成 RCL 和 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-144">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```console
dotnet build
```

<span data-ttu-id="f6d3a-145">移动至 WebApp1 目录，并运行应用：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-145">Move to the *WebApp1* directory and run the app:</span></span>

```console
dotnet run
```

---

<span data-ttu-id="f6d3a-146">按[“测试 Test WebApp1”](#test)中的说明进行操作</span><span class="sxs-lookup"><span data-stu-id="f6d3a-146">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="f6d3a-147">创建 RCL</span><span class="sxs-lookup"><span data-stu-id="f6d3a-147">Create an RCL</span></span>

<span data-ttu-id="f6d3a-148">在本部分中, 将创建一个 RCL。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-148">In this section, an RCL is created.</span></span> <span data-ttu-id="f6d3a-149">将 Razor 文件添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-149">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f6d3a-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6d3a-150">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6d3a-151">创建 RCL 项目：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-151">Create the RCL project:</span></span>

* <span data-ttu-id="f6d3a-152">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f6d3a-153">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="f6d3a-154">命名应用程序**RazorUIClassLib** > **确定**。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-154">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="f6d3a-155">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-155">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="f6d3a-156">选择“Razor 类库” > “确定”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-156">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="f6d3a-157">添加一个名为 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 的 Razor 分部视图文件。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-157">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f6d3a-158">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f6d3a-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f6d3a-159">从命令行运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-159">From the command line, run the following:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="f6d3a-160">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-160">The preceding commands:</span></span>

* <span data-ttu-id="f6d3a-161">`RazorUIClassLib`创建 RCL。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-161">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="f6d3a-162">创建 Razor _Message 页面，并将其添加至 RCL。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-162">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="f6d3a-163">`-np` 参数创建不含 `PageModel` 的页面。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-163">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="f6d3a-164">创建[_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view)文件，并将其添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-164">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="f6d3a-165">*_ViewStart.cshtml*时需要使用 （其中添加下一节中） 的 Razor 页面项目的布局文件。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-165">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="f6d3a-166">将 Razor 文件和文件夹添加到项目</span><span class="sxs-lookup"><span data-stu-id="f6d3a-166">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="f6d3a-167">使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 中的标记：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-167">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="f6d3a-168">使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml 中的标记：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-168">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="f6d3a-169">使用分步视图 (`<partial name="_Message" />`) 需要 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="f6d3a-170">可以添加一个 _ViewImports.cshtml 文件，无需包含 `@addTagHelper` 指令。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-170">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f6d3a-171">例如：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-171">For example:</span></span>

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="f6d3a-172">有关详细信息 *_ViewImports.cshtml*，请参阅[导入共享指令](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="f6d3a-172">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="f6d3a-173">生成类库以验证是否不存在编译器错误：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-173">Build the class library to verify there are no compiler errors:</span></span>

```console
dotnet build RazorUIClassLib
```

<span data-ttu-id="f6d3a-174">生成输出内容包含 RazorUIClassLib.dll 和 RazorUIClassLib.Views.dll。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-174">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="f6d3a-175">RazorUIClassLib.Views.dll 包含已编译的 Razor 内容。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-175">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="f6d3a-176">从 Razor 页面项目使用 Razor UI 库</span><span class="sxs-lookup"><span data-stu-id="f6d3a-176">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f6d3a-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6d3a-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6d3a-178">创建 Razor 页面 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-178">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="f6d3a-179">在解决方案资源管理器中，右键单击解决方案 >“添加” >  “新建项目”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-179">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="f6d3a-180">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-180">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="f6d3a-181">将应用命名为 WebApp1。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-181">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="f6d3a-182">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-182">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="f6d3a-183">选择“Web 应用程序” > “确定”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-183">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="f6d3a-184">在解决方案资源管理器中，右键单击“WebApp1”，然后选择“设为启动项目”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-184">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="f6d3a-185">在解决方案资源管理器中，右键单击“WebApp1”，然后选择“生成依赖项” > “项目依赖项”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-185">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="f6d3a-186">将 RazorUIClassLib 勾选为 WebApp1 的依赖项。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-186">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="f6d3a-187">在解决方案资源管理器中，右键单击“WebApp1”，选择“添加” > “引用”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-187">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="f6d3a-188">在“引用管理器”对话框中勾选“RazorUIClassLib” > “确定”。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-188">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="f6d3a-189">运行应用。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-189">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f6d3a-190">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f6d3a-190">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f6d3a-191">创建 Razor Pages web 应用和包含 Razor Pages 应用和 RCL 的解决方案文件:</span><span class="sxs-lookup"><span data-stu-id="f6d3a-191">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="f6d3a-192">生成并运行 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-192">Build and run the web app:</span></span>

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="f6d3a-193">测试 WebApp1</span><span class="sxs-lookup"><span data-stu-id="f6d3a-193">Test WebApp1</span></span>

<span data-ttu-id="f6d3a-194">验证 Razor UI 类库是否正在使用:</span><span class="sxs-lookup"><span data-stu-id="f6d3a-194">Verify the Razor UI class library is in use:</span></span>

* <span data-ttu-id="f6d3a-195">浏览到 `/MyFeature/Page1`。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-195">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="f6d3a-196">重写视图、分部视图和页面</span><span class="sxs-lookup"><span data-stu-id="f6d3a-196">Override views, partial views, and pages</span></span>

<span data-ttu-id="f6d3a-197">如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-197">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="f6d3a-198">例如, 将*WebApp1/Areas/MyFeature/Pages/Page1. cshtml*添加到 WebApp1, WebApp1 中的 page1 将优先于 RCL 中的 page1。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-198">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="f6d3a-199">在示例下载中，将 WebApp1/Areas/MyFeature2 重命名为 WebApp1/Areas/MyFeature 来测试优先级。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-199">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="f6d3a-200">将 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 分部视图复制到 WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-200">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="f6d3a-201">更新标记以指示新的位置。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-201">Update the markup to indicate the new location.</span></span> <span data-ttu-id="f6d3a-202">生成并运行应用，验证使用部分的应用版本。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-202">Build and run the app to verify the app's version of the partial is being used.</span></span>

<a name="afs"></a>

### <a name="rcl-pages-layout"></a><span data-ttu-id="f6d3a-203">RCL 页面布局</span><span class="sxs-lookup"><span data-stu-id="f6d3a-203">RCL Pages layout</span></span>

<span data-ttu-id="f6d3a-204">为引用 RCL 内容就好像它是 web 应用的一部分*页面*文件夹中，创建具有以下文件结构 RCL 项目：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-204">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="f6d3a-205">*RazorUIClassLib/页*</span><span class="sxs-lookup"><span data-stu-id="f6d3a-205">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="f6d3a-206">*RazorUIClassLib/页/Shared*</span><span class="sxs-lookup"><span data-stu-id="f6d3a-206">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="f6d3a-207">假设*RazorUIClassLib/页/共享*包含两个部分的文件： *_Header.cshtml*并 *_Footer.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-207">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="f6d3a-208">`<partial>`标记无法添加到 *_Layout.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="f6d3a-208">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker range=">= aspnetcore-3.0"

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="f6d3a-209">使用静态资产创建 RCL</span><span class="sxs-lookup"><span data-stu-id="f6d3a-209">Create an RCL with static assets</span></span>

<span data-ttu-id="f6d3a-210">RCL 可能需要随附静态资产, 这些资产可由 RCL 的使用应用程序引用。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-210">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="f6d3a-211">ASP.NET Core 允许创建包含可用于使用中的应用的静态资产的 RCLs。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-211">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="f6d3a-212">若要将配套资产作为 RCL 的一部分包括在内, 请在类库中创建*wwwroot*文件夹, 并在该文件夹中包含所有必需的文件。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-212">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="f6d3a-213">当对 RCL 进行打包时, *wwwroot*文件夹中的所有伴随资产将自动包含在包中。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-213">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="f6d3a-214">排除静态资产</span><span class="sxs-lookup"><span data-stu-id="f6d3a-214">Exclude static assets</span></span>

<span data-ttu-id="f6d3a-215">若要排除静态资产, 请将所需的排除`$(DefaultItemExcludes)`路径添加到项目文件中的属性组。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-215">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="f6d3a-216">用分号 (`;`) 分隔条目。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-216">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="f6d3a-217">在下面的示例中, *wwwroot*文件夹中的*lib*样式表不被视为静态资产, 而且不包含在已发布的 RCL 中:</span><span class="sxs-lookup"><span data-stu-id="f6d3a-217">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="f6d3a-218">Typescript 集成</span><span class="sxs-lookup"><span data-stu-id="f6d3a-218">Typescript integration</span></span>

<span data-ttu-id="f6d3a-219">若要在 RCL 中包含 TypeScript 文件:</span><span class="sxs-lookup"><span data-stu-id="f6d3a-219">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="f6d3a-220">将 TypeScript 文件 (*ts*) 置于*wwwroot*文件夹之外。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-220">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="f6d3a-221">例如, 将文件放在*客户端*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-221">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="f6d3a-222">为*wwwroot*文件夹配置 TypeScript 生成输出。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-222">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="f6d3a-223">在项目文件中设置的中的属性:`TypescriptOutDir` `PropertyGroup`</span><span class="sxs-lookup"><span data-stu-id="f6d3a-223">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="f6d3a-224">通过`PropertyGroup`在项目文件内的中添加以下`ResolveCurrentProjectStaticWebAssets`目标, 将 TypeScript 目标作为目标的依赖项:</span><span class="sxs-lookup"><span data-stu-id="f6d3a-224">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     TypeScriptCompile;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="f6d3a-225">使用引用 RCL 中的内容</span><span class="sxs-lookup"><span data-stu-id="f6d3a-225">Consume content from a referenced RCL</span></span>

<span data-ttu-id="f6d3a-226">RCL 的*wwwroot*文件夹中包含的文件会公开给使用的应用的前缀`_content/{LIBRARY NAME}/`。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-226">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="f6d3a-227">例如, 名为*Razor*的库会生成指向中的静态内容`_content/Razor.Class.Lib/`的路径。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-227">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="f6d3a-228">使用应用引用库`<script>`提供的静态资产, 以及、 `<style>`、 `<img>`和其他 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-228">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="f6d3a-229">使用的应用必须在中`Startup.Configure`启用[静态文件支持](xref:fundamentals/static-files):</span><span class="sxs-lookup"><span data-stu-id="f6d3a-229">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="f6d3a-230">从生成输出 (`dotnet run`) 运行使用中的应用时, 默认情况下会在开发环境中启用静态 web 资产。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-230">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="f6d3a-231">若要在从生成输出运行时支持其他环境中的`UseStaticWebAssets`资产, 请在*Program.cs*中的主机生成器上调用:</span><span class="sxs-lookup"><span data-stu-id="f6d3a-231">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="f6d3a-232">从`UseStaticWebAssets`已发布的输出 (`dotnet publish`) 运行应用时, 不需要调用。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-232">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="f6d3a-233">多项目开发流程</span><span class="sxs-lookup"><span data-stu-id="f6d3a-233">Multi-project development flow</span></span>

<span data-ttu-id="f6d3a-234">当使用中的应用运行时:</span><span class="sxs-lookup"><span data-stu-id="f6d3a-234">When the consuming app runs:</span></span>

* <span data-ttu-id="f6d3a-235">RCL 中的资产保留在其原始文件夹中。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-235">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="f6d3a-236">资产不会移动到使用的应用。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-236">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="f6d3a-237">重新生成 RCL 后, RCL 的*wwwroot*文件夹内的任何更改都会反映在使用的应用程序中, 而无需重新生成使用的应用。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-237">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="f6d3a-238">生成 RCL 时, 将生成描述静态 web 资产位置的清单。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-238">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="f6d3a-239">使用的应用程序会在运行时读取清单, 以使用来自引用项目和包的资产。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-239">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="f6d3a-240">将新资产添加到 RCL 时, 必须重新生成 RCL 以更新其清单, 然后使用的应用才能访问新资产。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-240">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="f6d3a-241">发布</span><span class="sxs-lookup"><span data-stu-id="f6d3a-241">Publish</span></span>

<span data-ttu-id="f6d3a-242">发布应用后, 所有被引用项目和包中的助理资产将复制到下`_content/{LIBRARY NAME}/`的已发布应用的*wwwroot*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="f6d3a-242">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end
