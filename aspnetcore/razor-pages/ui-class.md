---
title: ASP.NET Core 的类库中的可重用 Razor UI
author: Rick-Anderson
description: 说明如何创建可重复使用 Razor UI 在 ASP.NET Core 中的类库中使用分部视图。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/21/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 1b544e208be049f02d01e35daa6eb3bfba94265a
ms.sourcegitcommit: 04ce94b3c1b01d167f30eed60c1c95446dfe759d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/21/2019
ms.locfileid: "71176479"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="a6bf0-103">在 ASP.NET Core 中使用 Razor 类库项目创建可重用的 UI</span><span class="sxs-lookup"><span data-stu-id="a6bf0-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="a6bf0-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a6bf0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6bf0-105">Razor 视图、页、控制器、页模型、 [razor 组件](xref:blazor/class-libraries)、[视图组件](xref:mvc/views/view-components)和数据模型可以内置于 Razor 类库（RCL）中。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="a6bf0-106">RCL 可以打包并重复使用。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="a6bf0-107">应用程序可以包括 RCL，并重写其中包含的视图和页面。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="a6bf0-108">如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="a6bf0-109">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a6bf0-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="a6bf0-110">创建一个包含 Razor UI 的类库</span><span class="sxs-lookup"><span data-stu-id="a6bf0-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a6bf0-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6bf0-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6bf0-112">从 Visual Studio“文件”菜单中，选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-112">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a6bf0-113">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="a6bf0-114">命名库（例如，“RazorClassLib”）>“确定”。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-114">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="a6bf0-115">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="a6bf0-116">验证是否已选中**ASP.NET Core 3.0**或更高版本。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-116">Verify **ASP.NET Core 3.0** or later is selected.</span></span>
* <span data-ttu-id="a6bf0-117">选择 " **Razor 类库** > **"** 。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-117">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="a6bf0-118">默认情况下，Razor 类库（RCL）模板默认为 Razor 组件开发。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-118">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="a6bf0-119">Visual Studio 中的模板选项为页面和视图提供模板支持。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-119">A template option in Visual Studio provides template support for pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a6bf0-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a6bf0-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a6bf0-121">从命令行中，运行 `dotnet new razorclasslib`。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="a6bf0-122">例如:</span><span class="sxs-lookup"><span data-stu-id="a6bf0-122">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="a6bf0-123">默认情况下，Razor 类库（RCL）模板默认为 Razor 组件开发。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-123">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="a6bf0-124">传递选项（`dotnet new razorclasslib -support-pages-and-views`），以提供对页面和视图的支持。 `-support-pages-and-views`</span><span class="sxs-lookup"><span data-stu-id="a6bf0-124">Pass the `-support-pages-and-views` option (`dotnet new razorclasslib -support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="a6bf0-125">有关详细信息，请查看 [dotnet new](/dotnet/core/tools/dotnet-new)。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-125">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="a6bf0-126">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-126">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="a6bf0-127">将 Razor 文件添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-127">Add Razor files to the RCL.</span></span>

<span data-ttu-id="a6bf0-128">ASP.NET Core 模板假定 RCL 内容位于*领域*文件夹。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-128">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="a6bf0-129">请参阅[RCL Pages layout](#rcl-pages-layout) ，以创建在中`~/Pages`而不是`~/Areas/Pages`公开内容的 RCL。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-129">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="a6bf0-130">引用 RCL 内容</span><span class="sxs-lookup"><span data-stu-id="a6bf0-130">Reference RCL content</span></span>

<span data-ttu-id="a6bf0-131">可以通过以下方式引用 RCL：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-131">The RCL can be referenced by:</span></span>

* <span data-ttu-id="a6bf0-132">NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-132">NuGet package.</span></span> <span data-ttu-id="a6bf0-133">请参阅[创建 NuGet 包](/nuget/create-packages/creating-a-package)、[dotnet 添加包](/dotnet/core/tools/dotnet-add-package)和[创建和发布 NuGet 包](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-133">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="a6bf0-134">{ProjectName}.csproj。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-134">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="a6bf0-135">请查看 [dotnet-add 引用](/dotnet/core/tools/dotnet-add-reference)。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-135">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="a6bf0-136">重写视图、分部视图和页面</span><span class="sxs-lookup"><span data-stu-id="a6bf0-136">Override views, partial views, and pages</span></span>

<span data-ttu-id="a6bf0-137">如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-137">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="a6bf0-138">例如，将*WebApp1/Areas/MyFeature/Pages/Page1. cshtml*添加到 WebApp1，WebApp1 中的 page1 将优先于 RCL 中的 page1。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-138">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="a6bf0-139">在示例下载中，将 WebApp1/Areas/MyFeature2 重命名为 WebApp1/Areas/MyFeature 来测试优先级。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-139">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="a6bf0-140">将 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 分部视图复制到 WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-140">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="a6bf0-141">更新标记以指示新的位置。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-141">Update the markup to indicate the new location.</span></span> <span data-ttu-id="a6bf0-142">生成并运行应用，验证使用部分的应用版本。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-142">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="a6bf0-143">RCL 页面布局</span><span class="sxs-lookup"><span data-stu-id="a6bf0-143">RCL Pages layout</span></span>

<span data-ttu-id="a6bf0-144">为引用 RCL 内容就好像它是 web 应用的一部分*页面*文件夹中，创建具有以下文件结构 RCL 项目：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-144">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="a6bf0-145">*RazorUIClassLib/页*</span><span class="sxs-lookup"><span data-stu-id="a6bf0-145">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="a6bf0-146">*RazorUIClassLib/页/Shared*</span><span class="sxs-lookup"><span data-stu-id="a6bf0-146">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="a6bf0-147">假设*RazorUIClassLib/页/共享*包含两个部分的文件： *_Header.cshtml*并 *_Footer.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-147">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="a6bf0-148">`<partial>`标记无法添加到 *_Layout.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-148">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="a6bf0-149">使用静态资产创建 RCL</span><span class="sxs-lookup"><span data-stu-id="a6bf0-149">Create an RCL with static assets</span></span>

<span data-ttu-id="a6bf0-150">RCL 可能需要随附静态资产，这些资产可由 RCL 的使用应用程序引用。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-150">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="a6bf0-151">ASP.NET Core 允许创建包含可用于使用中的应用的静态资产的 RCLs。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-151">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="a6bf0-152">若要将配套资产作为 RCL 的一部分包括在内，请在类库中创建*wwwroot*文件夹，并在该文件夹中包含所有必需的文件。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-152">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="a6bf0-153">当对 RCL 进行打包时， *wwwroot*文件夹中的所有伴随资产将自动包含在包中。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-153">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="a6bf0-154">排除静态资产</span><span class="sxs-lookup"><span data-stu-id="a6bf0-154">Exclude static assets</span></span>

<span data-ttu-id="a6bf0-155">若要排除静态资产，请将所需的排除`$(DefaultItemExcludes)`路径添加到项目文件中的属性组。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-155">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="a6bf0-156">用分号（`;`）分隔条目。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-156">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="a6bf0-157">在下面的示例中， *wwwroot*文件夹中的*lib*样式表不被视为静态资产，而且不包含在已发布的 RCL 中：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-157">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="a6bf0-158">Typescript 集成</span><span class="sxs-lookup"><span data-stu-id="a6bf0-158">Typescript integration</span></span>

<span data-ttu-id="a6bf0-159">若要在 RCL 中包含 TypeScript 文件：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-159">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="a6bf0-160">将 TypeScript 文件（*ts*）置于*wwwroot*文件夹之外。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-160">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="a6bf0-161">例如，将文件放在*客户端*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-161">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="a6bf0-162">为*wwwroot*文件夹配置 TypeScript 生成输出。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-162">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="a6bf0-163">在项目文件中设置的中的属性：`TypescriptOutDir` `PropertyGroup`</span><span class="sxs-lookup"><span data-stu-id="a6bf0-163">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="a6bf0-164">通过`PropertyGroup`在项目文件内的中添加以下`ResolveCurrentProjectStaticWebAssets`目标，将 TypeScript 目标作为目标的依赖项：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-164">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     TypeScriptCompile;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="a6bf0-165">使用引用 RCL 中的内容</span><span class="sxs-lookup"><span data-stu-id="a6bf0-165">Consume content from a referenced RCL</span></span>

<span data-ttu-id="a6bf0-166">RCL 的*wwwroot*文件夹中包含的文件会公开给使用的应用的前缀`_content/{LIBRARY NAME}/`。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-166">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="a6bf0-167">例如，名为*Razor*的库会生成指向中的静态内容`_content/Razor.Class.Lib/`的路径。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-167">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="a6bf0-168">使用应用引用库`<script>`提供的静态资产，以及、 `<style>`、 `<img>`和其他 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-168">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="a6bf0-169">使用的应用必须在中`Startup.Configure`启用[静态文件支持](xref:fundamentals/static-files)：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-169">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="a6bf0-170">从生成输出（`dotnet run`）运行使用中的应用时，默认情况下会在开发环境中启用静态 web 资产。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-170">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="a6bf0-171">若要在从生成输出运行时支持其他环境中的`UseStaticWebAssets`资产，请在*Program.cs*中的主机生成器上调用：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-171">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="a6bf0-172">从`UseStaticWebAssets`已发布的输出（`dotnet publish`）运行应用时，不需要调用。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-172">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="a6bf0-173">多项目开发流程</span><span class="sxs-lookup"><span data-stu-id="a6bf0-173">Multi-project development flow</span></span>

<span data-ttu-id="a6bf0-174">当使用中的应用运行时：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-174">When the consuming app runs:</span></span>

* <span data-ttu-id="a6bf0-175">RCL 中的资产保留在其原始文件夹中。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-175">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="a6bf0-176">资产不会移动到使用的应用。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-176">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="a6bf0-177">重新生成 RCL 后，RCL 的*wwwroot*文件夹内的任何更改都会反映在使用的应用程序中，而无需重新生成使用的应用。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-177">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="a6bf0-178">生成 RCL 时，将生成描述静态 web 资产位置的清单。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-178">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="a6bf0-179">使用的应用程序会在运行时读取清单，以使用来自引用项目和包的资产。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-179">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="a6bf0-180">将新资产添加到 RCL 时，必须重新生成 RCL 以更新其清单，然后使用的应用才能访问新资产。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-180">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="a6bf0-181">发布</span><span class="sxs-lookup"><span data-stu-id="a6bf0-181">Publish</span></span>

<span data-ttu-id="a6bf0-182">发布应用后，所有被引用项目和包中的助理资产将复制到下`_content/{LIBRARY NAME}/`的已发布应用的*wwwroot*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-182">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a6bf0-183">Razor 视图、页、控制器、页模型、 [razor 组件](xref:blazor/class-libraries)、[视图组件](xref:mvc/views/view-components)和数据模型可以内置于 Razor 类库（RCL）中。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-183">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="a6bf0-184">RCL 可以打包并重复使用。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-184">The RCL can be packaged and reused.</span></span> <span data-ttu-id="a6bf0-185">应用程序可以包括 RCL，并重写其中包含的视图和页面。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-185">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="a6bf0-186">如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-186">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="a6bf0-187">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a6bf0-187">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="a6bf0-188">创建一个包含 Razor UI 的类库</span><span class="sxs-lookup"><span data-stu-id="a6bf0-188">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a6bf0-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6bf0-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6bf0-190">从 Visual Studio“文件”菜单中，选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a6bf0-191">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-191">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="a6bf0-192">命名库（例如，“RazorClassLib”）>“确定”。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-192">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="a6bf0-193">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-193">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="a6bf0-194">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-194">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="a6bf0-195">选择 " **Razor 类库** > **"** 。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-195">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="a6bf0-196">RCL 具有以下项目文件：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-196">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a6bf0-197">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a6bf0-197">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a6bf0-198">从命令行中，运行 `dotnet new razorclasslib`。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-198">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="a6bf0-199">例如：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-199">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="a6bf0-200">有关详细信息，请查看 [dotnet new](/dotnet/core/tools/dotnet-new)。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-200">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="a6bf0-201">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-201">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="a6bf0-202">将 Razor 文件添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-202">Add Razor files to the RCL.</span></span>

<span data-ttu-id="a6bf0-203">ASP.NET Core 模板假定 RCL 内容位于*领域*文件夹。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-203">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="a6bf0-204">请参阅[RCL Pages layout](#rcl-pages-layout) ，以创建在中`~/Pages`而不是`~/Areas/Pages`公开内容的 RCL。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-204">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="a6bf0-205">引用 RCL 内容</span><span class="sxs-lookup"><span data-stu-id="a6bf0-205">Reference RCL content</span></span>

<span data-ttu-id="a6bf0-206">可以通过以下方式引用 RCL：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-206">The RCL can be referenced by:</span></span>

* <span data-ttu-id="a6bf0-207">NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-207">NuGet package.</span></span> <span data-ttu-id="a6bf0-208">请参阅[创建 NuGet 包](/nuget/create-packages/creating-a-package)、[dotnet 添加包](/dotnet/core/tools/dotnet-add-package)和[创建和发布 NuGet 包](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-208">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="a6bf0-209">{ProjectName}.csproj。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-209">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="a6bf0-210">请查看 [dotnet-add 引用](/dotnet/core/tools/dotnet-add-reference)。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-210">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="a6bf0-211">演练：创建 RCL 项目并从 Razor Pages 项目使用</span><span class="sxs-lookup"><span data-stu-id="a6bf0-211">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="a6bf0-212">可以下载并测试[完整项目](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)，无需创建项目。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-212">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="a6bf0-213">示例下载包含附加代码和链接，以方便测试项目。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-213">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="a6bf0-214">可以在[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/6098)中留下反馈，评论下载示例和分步说明的对比。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-214">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="a6bf0-215">测试下载应用</span><span class="sxs-lookup"><span data-stu-id="a6bf0-215">Test the download app</span></span>

<span data-ttu-id="a6bf0-216">如果尚未下载已完成的应用，并更愿意创建演练项目，请跳转至[下一节](#create-an-rcl)。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-216">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a6bf0-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6bf0-217">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a6bf0-218">在 Visual Studio 中打开 .sln 文件。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-218">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="a6bf0-219">运行应用。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-219">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a6bf0-220">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a6bf0-220">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a6bf0-221">在 cli 目录中的命令提示符下生成 RCL 和 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-221">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="a6bf0-222">移动至 WebApp1 目录，并运行应用：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-222">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="a6bf0-223">按[“测试 Test WebApp1”](#test-webapp1)中的说明进行操作</span><span class="sxs-lookup"><span data-stu-id="a6bf0-223">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="a6bf0-224">创建 RCL</span><span class="sxs-lookup"><span data-stu-id="a6bf0-224">Create an RCL</span></span>

<span data-ttu-id="a6bf0-225">在本部分中，将创建一个 RCL。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-225">In this section, an RCL is created.</span></span> <span data-ttu-id="a6bf0-226">将 Razor 文件添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-226">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a6bf0-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6bf0-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a6bf0-228">创建 RCL 项目：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-228">Create the RCL project:</span></span>

* <span data-ttu-id="a6bf0-229">从 Visual Studio“文件”菜单中，选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-229">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a6bf0-230">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-230">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="a6bf0-231">将应用命名为 " **RazorUIClassLib** > **"** 。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-231">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="a6bf0-232">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-232">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="a6bf0-233">选择 " **Razor 类库** > **"** 。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-233">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="a6bf0-234">添加一个名为 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 的 Razor 分部视图文件。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-234">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a6bf0-235">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a6bf0-235">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a6bf0-236">从命令行运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-236">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="a6bf0-237">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-237">The preceding commands:</span></span>

* <span data-ttu-id="a6bf0-238">`RazorUIClassLib`创建 RCL。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-238">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="a6bf0-239">创建 Razor _Message 页面，并将其添加至 RCL。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-239">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="a6bf0-240">`-np` 参数创建不含 `PageModel` 的页面。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-240">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="a6bf0-241">创建[_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view)文件，并将其添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-241">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="a6bf0-242">*_ViewStart.cshtml*时需要使用 （其中添加下一节中） 的 Razor 页面项目的布局文件。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-242">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="a6bf0-243">将 Razor 文件和文件夹添加到项目</span><span class="sxs-lookup"><span data-stu-id="a6bf0-243">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="a6bf0-244">使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 中的标记：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-244">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="a6bf0-245">使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml 中的标记：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-245">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="a6bf0-246">使用分步视图 (`<partial name="_Message" />`) 需要 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-246">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="a6bf0-247">可以添加一个 _ViewImports.cshtml 文件，无需包含 `@addTagHelper` 指令。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-247">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="a6bf0-248">例如：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-248">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="a6bf0-249">有关详细信息 *_ViewImports.cshtml*，请参阅[导入共享指令](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="a6bf0-249">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="a6bf0-250">生成类库以验证是否不存在编译器错误：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-250">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="a6bf0-251">生成输出内容包含 RazorUIClassLib.dll 和 RazorUIClassLib.Views.dll。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-251">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="a6bf0-252">RazorUIClassLib.Views.dll 包含已编译的 Razor 内容。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-252">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="a6bf0-253">从 Razor 页面项目使用 Razor UI 库</span><span class="sxs-lookup"><span data-stu-id="a6bf0-253">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a6bf0-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6bf0-254">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a6bf0-255">创建 Razor 页面 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-255">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="a6bf0-256">在**解决方案资源管理器**中，右键单击解决方案 > "**添加** >**新项目**"。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-256">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="a6bf0-257">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-257">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="a6bf0-258">将应用命名为 WebApp1。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-258">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="a6bf0-259">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-259">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="a6bf0-260">选择 " **Web 应用程序** > **"** 。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-260">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="a6bf0-261">在解决方案资源管理器中，右键单击“WebApp1”，然后选择“设为启动项目”。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-261">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="a6bf0-262">在**解决方案资源管理器**中，右键单击**WebApp1** ，然后选择 "**生成依赖** >项" "**项目依赖项**"。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-262">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="a6bf0-263">将 RazorUIClassLib 勾选为 WebApp1 的依赖项。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-263">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="a6bf0-264">在**解决方案资源管理器**中，右键单击**WebApp1** ，然后选择 "**添加** > **引用**"。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-264">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="a6bf0-265">在 "**引用管理器**" 对话框中，选中 " **RazorUIClassLib** > **"** 。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-265">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="a6bf0-266">运行应用。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-266">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a6bf0-267">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a6bf0-267">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a6bf0-268">创建 Razor Pages web 应用和包含 Razor Pages 应用和 RCL 的解决方案文件：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-268">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="a6bf0-269">生成并运行 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-269">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="a6bf0-270">测试 WebApp1</span><span class="sxs-lookup"><span data-stu-id="a6bf0-270">Test WebApp1</span></span>

<span data-ttu-id="a6bf0-271">浏览到`/MyFeature/Page1`以验证 Razor UI 类库是否正在使用中。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-271">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="a6bf0-272">重写视图、分部视图和页面</span><span class="sxs-lookup"><span data-stu-id="a6bf0-272">Override views, partial views, and pages</span></span>

<span data-ttu-id="a6bf0-273">如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-273">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="a6bf0-274">例如，将*WebApp1/Areas/MyFeature/Pages/Page1. cshtml*添加到 WebApp1，WebApp1 中的 page1 将优先于 RCL 中的 page1。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-274">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="a6bf0-275">在示例下载中，将 WebApp1/Areas/MyFeature2 重命名为 WebApp1/Areas/MyFeature 来测试优先级。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-275">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="a6bf0-276">将 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 分部视图复制到 WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-276">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="a6bf0-277">更新标记以指示新的位置。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-277">Update the markup to indicate the new location.</span></span> <span data-ttu-id="a6bf0-278">生成并运行应用，验证使用部分的应用版本。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-278">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="a6bf0-279">RCL 页面布局</span><span class="sxs-lookup"><span data-stu-id="a6bf0-279">RCL Pages layout</span></span>

<span data-ttu-id="a6bf0-280">为引用 RCL 内容就好像它是 web 应用的一部分*页面*文件夹中，创建具有以下文件结构 RCL 项目：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-280">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="a6bf0-281">*RazorUIClassLib/页*</span><span class="sxs-lookup"><span data-stu-id="a6bf0-281">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="a6bf0-282">*RazorUIClassLib/页/Shared*</span><span class="sxs-lookup"><span data-stu-id="a6bf0-282">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="a6bf0-283">假设*RazorUIClassLib/页/共享*包含两个部分的文件： *_Header.cshtml*并 *_Footer.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="a6bf0-283">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="a6bf0-284">`<partial>`标记无法添加到 *_Layout.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="a6bf0-284">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
