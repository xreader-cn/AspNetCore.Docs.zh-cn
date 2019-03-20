---
title: ASP.NET Core 的类库中的可重用 Razor UI
author: Rick-Anderson
description: 说明如何创建可重复使用 Razor UI 在 ASP.NET Core 中的类库中使用分部视图。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/07/2018
ms.custom: seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: dc7db9483f2d75fe79ed9a9806f944e4f2a05a9b
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265350"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="9953d-103">创建使用 ASP.NET Core 中 Razor 类库项目的可重用 UI</span><span class="sxs-lookup"><span data-stu-id="9953d-103">Create reusable UI using the Razor Class Library project in ASP.NET Core</span></span>

<span data-ttu-id="9953d-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9953d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9953d-105">Razor 视图、页面、控制器、页面模型、[视图组件](xref:mvc/views/view-components)和数据模型可以构建到 Razor 类库 (RCL) 中。</span><span class="sxs-lookup"><span data-stu-id="9953d-105">Razor views, pages, controllers, page models, [View components](xref:mvc/views/view-components), and data models can be built into a Razor Class Library (RCL).</span></span> <span data-ttu-id="9953d-106">RCL 可以打包并重复使用。</span><span class="sxs-lookup"><span data-stu-id="9953d-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="9953d-107">应用程序可以包括 RCL，并重写其中包含的视图和页面。</span><span class="sxs-lookup"><span data-stu-id="9953d-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="9953d-108">如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="9953d-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="9953d-109">此功能要求 [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="9953d-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

<span data-ttu-id="9953d-110">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9953d-110">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="9953d-111">创建一个包含 Razor UI 的类库</span><span class="sxs-lookup"><span data-stu-id="9953d-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9953d-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9953d-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9953d-113">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="9953d-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9953d-114">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="9953d-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="9953d-115">命名库（例如，“RazorClassLib”）>“确定”。</span><span class="sxs-lookup"><span data-stu-id="9953d-115">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="9953d-116">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="9953d-116">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="9953d-117">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="9953d-117">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="9953d-118">选择“Razor 类库” > “确定”。</span><span class="sxs-lookup"><span data-stu-id="9953d-118">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="9953d-119">一个 Razor 类库具有以下项目文件：</span><span class="sxs-lookup"><span data-stu-id="9953d-119">A Razor Class Library has the following project file:</span></span>

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="9953d-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9953d-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9953d-121">从命令行中，运行 `dotnet new razorclasslib`。</span><span class="sxs-lookup"><span data-stu-id="9953d-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="9953d-122">例如：</span><span class="sxs-lookup"><span data-stu-id="9953d-122">For example:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="9953d-123">有关详细信息，请查看 [dotnet new](/dotnet/core/tools/dotnet-new)。</span><span class="sxs-lookup"><span data-stu-id="9953d-123">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="9953d-124">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="9953d-124">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="9953d-125">将 Razor 文件添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="9953d-125">Add Razor files to the RCL.</span></span>

<span data-ttu-id="9953d-126">ASP.NET Core 模板假定 RCL 内容位于*领域*文件夹。</span><span class="sxs-lookup"><span data-stu-id="9953d-126">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="9953d-127">请参阅[RCL 页面布局](#afs)若要创建中的内容公开 RCL`~/Pages`而非`~/Areas/Pages`。</span><span class="sxs-lookup"><span data-stu-id="9953d-127">See [RCL Pages layout](#afs) to create a RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="referencing-razor-class-library-content"></a><span data-ttu-id="9953d-128">引用 Razor 类库内容</span><span class="sxs-lookup"><span data-stu-id="9953d-128">Referencing Razor Class Library content</span></span>

<span data-ttu-id="9953d-129">可以通过以下方式引用 RCL：</span><span class="sxs-lookup"><span data-stu-id="9953d-129">The RCL can be referenced by:</span></span>

* <span data-ttu-id="9953d-130">NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9953d-130">NuGet package.</span></span> <span data-ttu-id="9953d-131">请参阅[创建 NuGet 包](/nuget/create-packages/creating-a-package)、[dotnet 添加包](/dotnet/core/tools/dotnet-add-package)和[创建和发布 NuGet 包](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="9953d-131">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="9953d-132">{ProjectName}.csproj。</span><span class="sxs-lookup"><span data-stu-id="9953d-132">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="9953d-133">请查看 [dotnet-add 引用](/dotnet/core/tools/dotnet-add-reference)。</span><span class="sxs-lookup"><span data-stu-id="9953d-133">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-a-razor-class-library-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="9953d-134">演练：创建一个 Razor 类库项目并使用从 Razor 页面项目</span><span class="sxs-lookup"><span data-stu-id="9953d-134">Walkthrough: Create a Razor Class Library project and use from a Razor Pages project</span></span>

<span data-ttu-id="9953d-135">可以下载并测试[完整项目](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)，无需创建项目。</span><span class="sxs-lookup"><span data-stu-id="9953d-135">You can download the [complete project](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="9953d-136">示例下载包含附加代码和链接，以方便测试项目。</span><span class="sxs-lookup"><span data-stu-id="9953d-136">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="9953d-137">可以在[此 GitHub 问题](https://github.com/aspnet/Docs/issues/6098)中留下反馈，评论下载示例和分步说明的对比。</span><span class="sxs-lookup"><span data-stu-id="9953d-137">You can leave feedback in [this GitHub issue](https://github.com/aspnet/Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="9953d-138">测试下载应用</span><span class="sxs-lookup"><span data-stu-id="9953d-138">Test the download app</span></span>

<span data-ttu-id="9953d-139">如果尚未下载已完成的应用，并更愿意创建演练项目，请跳转至[下一节](#create-a-razor-class-library)。</span><span class="sxs-lookup"><span data-stu-id="9953d-139">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-a-razor-class-library).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9953d-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9953d-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9953d-141">在 Visual Studio 中打开 .sln 文件。</span><span class="sxs-lookup"><span data-stu-id="9953d-141">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="9953d-142">运行应用。</span><span class="sxs-lookup"><span data-stu-id="9953d-142">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="9953d-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9953d-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9953d-144">在 cli 目录中的命令提示符下生成 RCL 和 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="9953d-144">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```console
dotnet build
```

<span data-ttu-id="9953d-145">移动至 WebApp1 目录，并运行应用：</span><span class="sxs-lookup"><span data-stu-id="9953d-145">Move to the *WebApp1* directory and run the app:</span></span>

```console
dotnet run
```

---

<span data-ttu-id="9953d-146">按[“测试 Test WebApp1”](#test)中的说明进行操作</span><span class="sxs-lookup"><span data-stu-id="9953d-146">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-a-razor-class-library"></a><span data-ttu-id="9953d-147">创建 Razor 类库</span><span class="sxs-lookup"><span data-stu-id="9953d-147">Create a Razor Class Library</span></span>

<span data-ttu-id="9953d-148">本部分创建 Razor 类库 (RCL)。</span><span class="sxs-lookup"><span data-stu-id="9953d-148">In this section, a Razor Class Library (RCL) is created.</span></span> <span data-ttu-id="9953d-149">将 Razor 文件添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="9953d-149">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9953d-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9953d-150">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9953d-151">创建 RCL 项目：</span><span class="sxs-lookup"><span data-stu-id="9953d-151">Create the RCL project:</span></span>

* <span data-ttu-id="9953d-152">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="9953d-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9953d-153">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="9953d-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="9953d-154">命名应用程序**RazorUIClassLib** > **确定**。</span><span class="sxs-lookup"><span data-stu-id="9953d-154">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="9953d-155">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="9953d-155">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="9953d-156">选择“Razor 类库” > “确定”。</span><span class="sxs-lookup"><span data-stu-id="9953d-156">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="9953d-157">添加一个名为 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 的 Razor 分部视图文件。</span><span class="sxs-lookup"><span data-stu-id="9953d-157">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="9953d-158">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9953d-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9953d-159">从命令行运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="9953d-159">From the command line, run the following:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="9953d-160">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="9953d-160">The preceding commands:</span></span>

* <span data-ttu-id="9953d-161">创建 `RazorUIClassLib` Razor 类库 (RCL)。</span><span class="sxs-lookup"><span data-stu-id="9953d-161">Creates the `RazorUIClassLib` Razor Class Library (RCL).</span></span>
* <span data-ttu-id="9953d-162">创建 Razor _Message 页面，并将其添加至 RCL。</span><span class="sxs-lookup"><span data-stu-id="9953d-162">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="9953d-163">`-np` 参数创建不含 `PageModel` 的页面。</span><span class="sxs-lookup"><span data-stu-id="9953d-163">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="9953d-164">创建[_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view)文件，并将其添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="9953d-164">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="9953d-165">*_ViewStart.cshtml*时需要使用 （其中添加下一节中） 的 Razor 页面项目的布局文件。</span><span class="sxs-lookup"><span data-stu-id="9953d-165">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="9953d-166">将 Razor 文件和文件夹添加到项目</span><span class="sxs-lookup"><span data-stu-id="9953d-166">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="9953d-167">使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 中的标记：</span><span class="sxs-lookup"><span data-stu-id="9953d-167">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="9953d-168">使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml 中的标记：</span><span class="sxs-lookup"><span data-stu-id="9953d-168">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="9953d-169">使用分步视图 (`<partial name="_Message" />`) 需要 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`。</span><span class="sxs-lookup"><span data-stu-id="9953d-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="9953d-170">可以添加一个 _ViewImports.cshtml 文件，无需包含 `@addTagHelper` 指令。</span><span class="sxs-lookup"><span data-stu-id="9953d-170">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="9953d-171">例如：</span><span class="sxs-lookup"><span data-stu-id="9953d-171">For example:</span></span>

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="9953d-172">有关详细信息 *_ViewImports.cshtml*，请参阅[导入共享指令](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="9953d-172">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="9953d-173">生成类库以验证是否不存在编译器错误：</span><span class="sxs-lookup"><span data-stu-id="9953d-173">Build the class library to verify there are no compiler errors:</span></span>

```console
dotnet build RazorUIClassLib
```

<span data-ttu-id="9953d-174">生成输出内容包含 RazorUIClassLib.dll 和 RazorUIClassLib.Views.dll。</span><span class="sxs-lookup"><span data-stu-id="9953d-174">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="9953d-175">RazorUIClassLib.Views.dll 包含已编译的 Razor 内容。</span><span class="sxs-lookup"><span data-stu-id="9953d-175">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="9953d-176">从 Razor 页面项目使用 Razor UI 库</span><span class="sxs-lookup"><span data-stu-id="9953d-176">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9953d-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9953d-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9953d-178">创建 Razor 页面 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="9953d-178">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="9953d-179">在解决方案资源管理器中，右键单击解决方案 >“添加” >  “新建项目”。</span><span class="sxs-lookup"><span data-stu-id="9953d-179">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="9953d-180">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="9953d-180">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="9953d-181">将应用命名为 WebApp1。</span><span class="sxs-lookup"><span data-stu-id="9953d-181">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="9953d-182">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="9953d-182">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="9953d-183">选择“Web 应用程序” > “确定”。</span><span class="sxs-lookup"><span data-stu-id="9953d-183">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="9953d-184">在解决方案资源管理器中，右键单击“WebApp1”，然后选择“设为启动项目”。</span><span class="sxs-lookup"><span data-stu-id="9953d-184">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="9953d-185">在解决方案资源管理器中，右键单击“WebApp1”，然后选择“生成依赖项” > “项目依赖项”。</span><span class="sxs-lookup"><span data-stu-id="9953d-185">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="9953d-186">将 RazorUIClassLib 勾选为 WebApp1 的依赖项。</span><span class="sxs-lookup"><span data-stu-id="9953d-186">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="9953d-187">在解决方案资源管理器中，右键单击“WebApp1”，选择“添加” > “引用”。</span><span class="sxs-lookup"><span data-stu-id="9953d-187">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="9953d-188">在“引用管理器”对话框中勾选“RazorUIClassLib” > “确定”。</span><span class="sxs-lookup"><span data-stu-id="9953d-188">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="9953d-189">运行应用。</span><span class="sxs-lookup"><span data-stu-id="9953d-189">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="9953d-190">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9953d-190">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9953d-191">创建 Razor 页面 Web 应用和包含 Razor 页面应用和 Razor 类库的解决方案文件：</span><span class="sxs-lookup"><span data-stu-id="9953d-191">Create a Razor Pages web app and a solution file containing the Razor Pages app and the Razor Class Library:</span></span>

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="9953d-192">生成并运行 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="9953d-192">Build and run the web app:</span></span>

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="9953d-193">测试 WebApp1</span><span class="sxs-lookup"><span data-stu-id="9953d-193">Test WebApp1</span></span>

<span data-ttu-id="9953d-194">验证正在使用的 Razor UI 类库。</span><span class="sxs-lookup"><span data-stu-id="9953d-194">Verify the Razor UI class library is being used.</span></span>

* <span data-ttu-id="9953d-195">浏览到 `/MyFeature/Page1`。</span><span class="sxs-lookup"><span data-stu-id="9953d-195">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="9953d-196">重写视图、分部视图和页面</span><span class="sxs-lookup"><span data-stu-id="9953d-196">Override views, partial views, and pages</span></span>

<span data-ttu-id="9953d-197">如果在 Web 应用和 Razor 类库中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="9953d-197">When a view, partial view, or Razor Page is found in both the web app and the Razor Class Library, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="9953d-198">例如，添加*WebApp1/Areas/MyFeature/Pages/Page1.cshtml*到 WebApp1，并在 WebApp1 Page1 将优先于 Razor 类库中的 Page1。</span><span class="sxs-lookup"><span data-stu-id="9953d-198">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the Razor Class Library.</span></span>

<span data-ttu-id="9953d-199">在示例下载中，将 WebApp1/Areas/MyFeature2 重命名为 WebApp1/Areas/MyFeature 来测试优先级。</span><span class="sxs-lookup"><span data-stu-id="9953d-199">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="9953d-200">将 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 分部视图复制到 WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml。</span><span class="sxs-lookup"><span data-stu-id="9953d-200">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="9953d-201">更新标记以指示新的位置。</span><span class="sxs-lookup"><span data-stu-id="9953d-201">Update the markup to indicate the new location.</span></span> <span data-ttu-id="9953d-202">生成并运行应用，验证使用部分的应用版本。</span><span class="sxs-lookup"><span data-stu-id="9953d-202">Build and run the app to verify the app's version of the partial is being used.</span></span>

<a name="afs"></a>

### <a name="rcl-pages-layout"></a><span data-ttu-id="9953d-203">RCL 页面布局</span><span class="sxs-lookup"><span data-stu-id="9953d-203">RCL Pages layout</span></span>

<span data-ttu-id="9953d-204">为引用 RCL 内容就好像它是 web 应用的一部分*页面*文件夹中，创建具有以下文件结构 RCL 项目：</span><span class="sxs-lookup"><span data-stu-id="9953d-204">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="9953d-205">*RazorUIClassLib/页*</span><span class="sxs-lookup"><span data-stu-id="9953d-205">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="9953d-206">*RazorUIClassLib/页/Shared*</span><span class="sxs-lookup"><span data-stu-id="9953d-206">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="9953d-207">假设*RazorUIClassLib/页/共享*包含两个部分的文件： *_Header.cshtml*并 *_Footer.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="9953d-207">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="9953d-208">`<partial>`标记无法添加到 *_Layout.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="9953d-208">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```
