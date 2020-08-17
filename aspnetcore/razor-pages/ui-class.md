---
title: ASP.NET Core 的类库中的可重用 Razor UI
author: Rick-Anderson
description: 说明如何使用 ASP.NET Core 中类库的分部视图创建可重用 Razor UI。
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: 042ef8dcc7135e0c60baec972d405380d85a6a10
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019217"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a><span data-ttu-id="8d0ad-103">使用 ASP.NET Core 中的 Razor 类库项目创建可重用 UI</span><span class="sxs-lookup"><span data-stu-id="8d0ad-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="8d0ad-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8d0ad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d0ad-105">Razor 视图、页面、控制器、页面模型、[Razor组件](xref:blazor/components/class-libraries)、[视图组件](xref:mvc/views/view-components)和数据模型可以构建到 Razor 类库 (RCL) 中。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="8d0ad-106">RCL 可以打包并重复使用。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="8d0ad-107">应用程序可以包括 RCL，并重写其中包含的视图和页面。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="8d0ad-108">如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="8d0ad-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8d0ad-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="8d0ad-110">创建一个包含 Razor UI 的类库</span><span class="sxs-lookup"><span data-stu-id="8d0ad-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d0ad-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d0ad-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8d0ad-112">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="8d0ad-113">依次选择“Razor 类库”>“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="8d0ad-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="8d0ad-114">命名库（例如，“RazorClassLib”）>“创建”。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="8d0ad-115">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="8d0ad-116">如果需要支持视图，请选择“支持页面和视图”。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="8d0ad-117">默认情况下，仅支持 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="8d0ad-118">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-118">Select **Create**.</span></span>

<span data-ttu-id="8d0ad-119">默认情况下，Razor 类库 (RCL) 模板默认为 Razor 组件开发。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="8d0ad-120">“支持页面和视图”选项支持页面和视图。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8d0ad-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8d0ad-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8d0ad-122">从命令行中，运行 `dotnet new razorclasslib`。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="8d0ad-123">例如：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="8d0ad-124">默认情况下，Razor 类库 (RCL) 模板默认为 Razor 组件开发。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="8d0ad-125">传递 `--support-pages-and-views` 选项 (`dotnet new razorclasslib --support-pages-and-views`) 以提供页面和视图支持。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="8d0ad-126">有关详细信息，请查看 [dotnet new](/dotnet/core/tools/dotnet-new)。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="8d0ad-127">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="8d0ad-128">将 Razor 文件添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="8d0ad-129">ASP.NET Core 模板假定 RCL 内容位于 *Areas* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="8d0ad-130">请参阅 [RCL 页面布局](#rcl-pages-layout)，创建公开 `~/Pages` 中内容（而非 `~/Areas/Pages` 中内容）的 RCL。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="8d0ad-131">引用 RCL 内容</span><span class="sxs-lookup"><span data-stu-id="8d0ad-131">Reference RCL content</span></span>

<span data-ttu-id="8d0ad-132">可以通过以下方式引用 RCL：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="8d0ad-133">NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-133">NuGet package.</span></span> <span data-ttu-id="8d0ad-134">请参阅[创建 NuGet 包](/nuget/create-packages/creating-a-package)、[dotnet 添加包](/dotnet/core/tools/dotnet-add-package)和[创建和发布 NuGet 包](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="8d0ad-135">{ProjectName}.csproj。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="8d0ad-136">请查看 [dotnet-add 引用](/dotnet/core/tools/dotnet-add-reference)。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="8d0ad-137">重写视图、分部视图和页面</span><span class="sxs-lookup"><span data-stu-id="8d0ad-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="8d0ad-138">如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="8d0ad-139">例如，将 *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* 添加到 WebApp1，则 WebApp1 中的 Page1 会优先于 RCL 中的 Page1。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="8d0ad-140">在示例下载中，将 WebApp1/Areas/MyFeature2 重命名为 WebApp1/Areas/MyFeature 来测试优先级 。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="8d0ad-141">将 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 分部视图复制到 WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml 。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="8d0ad-142">更新标记以指示新的位置。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="8d0ad-143">生成并运行应用，验证使用部分的应用版本。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="8d0ad-144">RCL 页面布局</span><span class="sxs-lookup"><span data-stu-id="8d0ad-144">RCL Pages layout</span></span>

<span data-ttu-id="8d0ad-145">若要将 RCL 内容作为 Web 应用 *Pages* 文件夹的一部分引用，请使用以下文件结构创建 RCL 项目：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="8d0ad-146">RazorUIClassLib/Pages</span><span class="sxs-lookup"><span data-stu-id="8d0ad-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="8d0ad-147">RazorUIClassLib/Pages/Shared</span><span class="sxs-lookup"><span data-stu-id="8d0ad-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="8d0ad-148">假设 RazorUIClassLib/Pages/Shared 包含两个分部文件：_Header.cshtml 和 _Footer.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="8d0ad-149">可将 `<partial>` 标记添加到 *_Layout.cshtml* 文件：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="8d0ad-150">创建具有静态资产的 RCL</span><span class="sxs-lookup"><span data-stu-id="8d0ad-150">Create an RCL with static assets</span></span>

<span data-ttu-id="8d0ad-151">RCL 可能需要 RCL 或 RCL 的消耗应用可以引用的伴随静态资产。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="8d0ad-152">ASP.NET Core 允许创建 RCL，这些 RCL 包括可供消耗应用使用的静态资产。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="8d0ad-153">若要将伴随资产作为 RCL 的一部分包括在内，请在类库中创建 *wwwroot* 文件夹，并在该文件夹中包括所有必需的文件。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="8d0ad-154">打包 RCL 时，*wwwroot* 文件夹中的所有伴随资产都会自动包括在包中。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="8d0ad-155">使用 `dotnet pack` 命令，而不是 Nuget.exe 版本 `nuget pack`。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-155">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="8d0ad-156">排除静态资产</span><span class="sxs-lookup"><span data-stu-id="8d0ad-156">Exclude static assets</span></span>

<span data-ttu-id="8d0ad-157">若要排除静态资产，请将所需的排除路径添加到项目文件中的 `$(DefaultItemExcludes)` 属性组。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-157">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="8d0ad-158">使用分号 (`;`) 分隔条目。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-158">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="8d0ad-159">在以下示例中，*wwwroot* 文件夹中的 *lib.css* 样式表不被视为静态资产，并且不包括在已发布的 RCL 中：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-159">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="8d0ad-160">TypeScript 集成</span><span class="sxs-lookup"><span data-stu-id="8d0ad-160">Typescript integration</span></span>

<span data-ttu-id="8d0ad-161">若要在 RCL 中包括 TypeScript 文件，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-161">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="8d0ad-162">将 TypeScript 文件 ( *.ts*) 置于 *wwwroot* 文件夹之外。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-162">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="8d0ad-163">例如，将文件置于 *Client* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-163">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="8d0ad-164">为 *wwwroot* 文件夹配置 TypeScript 生成输出。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-164">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="8d0ad-165">在项目文件的 `PropertyGroup` 内设置 `TypescriptOutDir` 属性：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-165">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="8d0ad-166">通过在项目文件的 `PropertyGroup` 内添加以下目标，将 TypeScript 目标作为 `ResolveCurrentProjectStaticWebAssets` 目标的依赖项包括在内：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-166">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="8d0ad-167">使用引用的 RCL 中的内容</span><span class="sxs-lookup"><span data-stu-id="8d0ad-167">Consume content from a referenced RCL</span></span>

<span data-ttu-id="8d0ad-168">RCL 的 *wwwroot* 文件夹中包括的文件使用前缀 `_content/{LIBRARY NAME}/` 向 RCL 或消耗应用公开。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-168">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="8d0ad-169">例如，名为 Razor.Class.Lib 的库会生成指向 `_content/Razor.Class.Lib/` 处的静态内容的路径。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-169">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="8d0ad-170">生成 NuGet 包且程序集名称与包 ID 不同时，请将包 ID 用于 `{LIBRARY NAME}`。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-170">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="8d0ad-171">消耗应用使用 `<script>`、`<style>`、`<img>` 和其他 HTML 标记引用库提供的静态资产。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-171">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="8d0ad-172">消耗应用必须在 `Startup.Configure` 中启用[静态文件支持](xref:fundamentals/static-files)：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-172">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="8d0ad-173">从生成输出 (`dotnet run`) 运行消耗应用时，默认在开发环境中启用静态 Web 资产。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-173">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="8d0ad-174">从生成输出运行时，若要在其他环境中支持资产，请在 *Program.cs* 中的主机生成器上调用 `UseStaticWebAssets`：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-174">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="8d0ad-175">从已发布输出 (`dotnet publish`) 运行应用时，无需调用 `UseStaticWebAssets`。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-175">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="8d0ad-176">多项目开发流程</span><span class="sxs-lookup"><span data-stu-id="8d0ad-176">Multi-project development flow</span></span>

<span data-ttu-id="8d0ad-177">消耗应用运行时：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-177">When the consuming app runs:</span></span>

* <span data-ttu-id="8d0ad-178">RCL 中的资产停留在其原始文件夹中。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-178">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="8d0ad-179">资产不会移至消耗应用。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-179">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="8d0ad-180">重新生成 RCL 后，RCL 的 *wwwroot* 文件夹中的所有更改都会反映在消耗应用中，而无需重新生成消耗应用。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-180">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="8d0ad-181">生成 RCL 时，还会生成描述静态 Web 资产位置的清单。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-181">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="8d0ad-182">消耗应用在运行时读取清单，以使用引用项目和包中的资产。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-182">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="8d0ad-183">将新资产添加到 RCL 后，必须先重新生成 RCL 以更新其清单，然后消耗应用才能访问新资产。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-183">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="8d0ad-184">发布</span><span class="sxs-lookup"><span data-stu-id="8d0ad-184">Publish</span></span>

<span data-ttu-id="8d0ad-185">发布应用后，来自所有引用项目和包的伴随资产都会复制到已发布应用的 *wwwroot* 文件夹中的 `_content/{LIBRARY NAME}/` 下。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-185">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8d0ad-186">Razor 视图、页面、控制器、页面模型、[Razor组件](xref:blazor/components/class-libraries)、[视图组件](xref:mvc/views/view-components)和数据模型可以构建到 Razor 类库 (RCL) 中。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-186">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="8d0ad-187">RCL 可以打包并重复使用。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-187">The RCL can be packaged and reused.</span></span> <span data-ttu-id="8d0ad-188">应用程序可以包括 RCL，并重写其中包含的视图和页面。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-188">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="8d0ad-189">如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-189">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="8d0ad-190">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8d0ad-190">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="8d0ad-191">创建一个包含 Razor UI 的类库</span><span class="sxs-lookup"><span data-stu-id="8d0ad-191">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d0ad-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d0ad-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8d0ad-193">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8d0ad-194">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-194">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="8d0ad-195">命名库（例如，“RazorClassLib”）>“确定”。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-195">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="8d0ad-196">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-196">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="8d0ad-197">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-197">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="8d0ad-198">依次选择“Razor 类库”>“确定”。 </span><span class="sxs-lookup"><span data-stu-id="8d0ad-198">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="8d0ad-199">RCL 具有以下项目文件：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-199">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="8d0ad-200">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8d0ad-200">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8d0ad-201">从命令行中，运行 `dotnet new razorclasslib`。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-201">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="8d0ad-202">例如：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-202">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="8d0ad-203">有关详细信息，请查看 [dotnet new](/dotnet/core/tools/dotnet-new)。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-203">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="8d0ad-204">为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-204">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="8d0ad-205">将 Razor 文件添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-205">Add Razor files to the RCL.</span></span>

<span data-ttu-id="8d0ad-206">ASP.NET Core 模板假定 RCL 内容位于 *Areas* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-206">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="8d0ad-207">请参阅 [RCL 页面布局](#rcl-pages-layout)，创建公开 `~/Pages` 中内容（而非 `~/Areas/Pages` 中内容）的 RCL。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-207">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="8d0ad-208">引用 RCL 内容</span><span class="sxs-lookup"><span data-stu-id="8d0ad-208">Reference RCL content</span></span>

<span data-ttu-id="8d0ad-209">可以通过以下方式引用 RCL：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-209">The RCL can be referenced by:</span></span>

* <span data-ttu-id="8d0ad-210">NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-210">NuGet package.</span></span> <span data-ttu-id="8d0ad-211">请参阅[创建 NuGet 包](/nuget/create-packages/creating-a-package)、[dotnet 添加包](/dotnet/core/tools/dotnet-add-package)和[创建和发布 NuGet 包](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-211">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="8d0ad-212">{ProjectName}.csproj。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-212">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="8d0ad-213">请查看 [dotnet-add 引用](/dotnet/core/tools/dotnet-add-reference)。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-213">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a><span data-ttu-id="8d0ad-214">演练：创建 RCL 项目并通过 Razor 页面项目使用</span><span class="sxs-lookup"><span data-stu-id="8d0ad-214">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="8d0ad-215">可以下载并测试[完整项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)，无需创建项目。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-215">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="8d0ad-216">示例下载包含附加代码和链接，以方便测试项目。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-216">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="8d0ad-217">可以在[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/6098)中留下反馈，评论下载示例和分步说明的对比。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-217">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="8d0ad-218">测试下载应用</span><span class="sxs-lookup"><span data-stu-id="8d0ad-218">Test the download app</span></span>

<span data-ttu-id="8d0ad-219">如果尚未下载已完成的应用，并更愿意创建演练项目，请跳转至[下一节](#create-an-rcl)。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-219">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d0ad-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d0ad-220">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d0ad-221">在 Visual Studio 中打开 .sln 文件。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-221">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="8d0ad-222">运行应用。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-222">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8d0ad-223">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8d0ad-223">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8d0ad-224">在 cli 目录中的命令提示符下生成 RCL 和 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-224">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="8d0ad-225">移动至 WebApp1 目录，并运行应用：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-225">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="8d0ad-226">按[“测试 Test WebApp1”](#test-webapp1)中的说明进行操作</span><span class="sxs-lookup"><span data-stu-id="8d0ad-226">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="8d0ad-227">创建 RCL</span><span class="sxs-lookup"><span data-stu-id="8d0ad-227">Create an RCL</span></span>

<span data-ttu-id="8d0ad-228">本部分会创建 RCL。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-228">In this section, an RCL is created.</span></span> <span data-ttu-id="8d0ad-229">将 Razor 文件添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-229">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d0ad-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d0ad-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d0ad-231">创建 RCL 项目：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-231">Create the RCL project:</span></span>

* <span data-ttu-id="8d0ad-232">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-232">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8d0ad-233">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-233">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="8d0ad-234">将应用命名为“RazorUIClassLib”>“确定” 。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-234">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="8d0ad-235">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-235">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="8d0ad-236">依次选择“Razor 类库”>“确定”。 </span><span class="sxs-lookup"><span data-stu-id="8d0ad-236">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="8d0ad-237">添加一个名为 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 的 Razor 分部视图文件。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-237">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8d0ad-238">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8d0ad-238">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8d0ad-239">从命令行运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-239">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="8d0ad-240">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-240">The preceding commands:</span></span>

* <span data-ttu-id="8d0ad-241">创建 `RazorUIClassLib` RCL。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-241">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="8d0ad-242">创建 Razor _Message 页面，并将其添加至 RCL。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-242">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="8d0ad-243">`-np` 参数创建不含 `PageModel` 的页面。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-243">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="8d0ad-244">创建 [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) 文件并将其添加到 RCL。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-244">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="8d0ad-245">使用 Razor 页面项目的布局需要 _ViewStart.cshtml 文件（在下一部分中添加）。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-245">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a><span data-ttu-id="8d0ad-246">将 Razor 文件和文件夹添加到项目</span><span class="sxs-lookup"><span data-stu-id="8d0ad-246">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="8d0ad-247">使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 中的标记：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="8d0ad-248">使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml 中的标记：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="8d0ad-249">使用分步视图 (`<partial name="_Message" />`) 需要 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="8d0ad-250">可以添加一个 _ViewImports.cshtml 文件，无需包含 `@addTagHelper` 指令。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-250">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="8d0ad-251">例如：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-251">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="8d0ad-252">有关 *_ViewImports.cshtml* 的详细信息，请参阅[导入共享指令](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="8d0ad-252">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="8d0ad-253">生成类库以验证是否不存在编译器错误：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-253">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="8d0ad-254">生成输出内容包含 RazorUIClassLib.dll 和 RazorUIClassLib.Views.dll 。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-254">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="8d0ad-255">RazorUIClassLib.Views.dll 包含已编译的 Razor 内容。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-255">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a><span data-ttu-id="8d0ad-256">从 Razor 页面项目使用 Razor UI 库</span><span class="sxs-lookup"><span data-stu-id="8d0ad-256">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d0ad-257">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d0ad-257">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d0ad-258">创建 Razor 页面 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-258">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="8d0ad-259">在“解决方案资源管理器”中，右键单击解决方案 >“添加”>“新建项目”  。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-259">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="8d0ad-260">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-260">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="8d0ad-261">将应用命名为 WebApp1。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-261">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="8d0ad-262">验证是否已选择 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-262">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="8d0ad-263">选择“Web 应用”>“确定” 。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-263">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="8d0ad-264">在解决方案资源管理器中，右键单击“WebApp1”，然后选择“设为启动项目”  。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-264">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="8d0ad-265">在“解决方案资源管理器”中，右键单击“WebApp1”，然后选择“生成依赖项”>“项目依赖项”   。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-265">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="8d0ad-266">将 RazorUIClassLib 勾选为“WebApp1”的依赖项 。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-266">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="8d0ad-267">在“解决方案资源管理器”中，右键单击“WebApp1”，然后选择“添加”>“引用”   。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-267">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="8d0ad-268">在“引用管理器”对话框中勾选“RazorUIClassLib”>“确定”  。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-268">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="8d0ad-269">运行应用。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-269">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8d0ad-270">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8d0ad-270">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8d0ad-271">创建 Razor 页面 Web 应用和包含 Razor 页面应用及 RCL 的解决方案文件：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-271">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="8d0ad-272">生成并运行 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-272">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="8d0ad-273">测试 WebApp1</span><span class="sxs-lookup"><span data-stu-id="8d0ad-273">Test WebApp1</span></span>

<span data-ttu-id="8d0ad-274">浏览到 `/MyFeature/Page1` 以验证是否正在使用 Razor UI 类库。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-274">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="8d0ad-275">重写视图、分部视图和页面</span><span class="sxs-lookup"><span data-stu-id="8d0ad-275">Override views, partial views, and pages</span></span>

<span data-ttu-id="8d0ad-276">如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-276">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="8d0ad-277">例如，将 *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* 添加到 WebApp1，则 WebApp1 中的 Page1 会优先于 RCL 中的 Page1。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-277">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="8d0ad-278">在示例下载中，将 WebApp1/Areas/MyFeature2 重命名为 WebApp1/Areas/MyFeature 来测试优先级 。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-278">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="8d0ad-279">将 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 分部视图复制到 WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml 。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-279">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="8d0ad-280">更新标记以指示新的位置。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-280">Update the markup to indicate the new location.</span></span> <span data-ttu-id="8d0ad-281">生成并运行应用，验证使用部分的应用版本。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-281">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="8d0ad-282">RCL 页面布局</span><span class="sxs-lookup"><span data-stu-id="8d0ad-282">RCL Pages layout</span></span>

<span data-ttu-id="8d0ad-283">若要将 RCL 内容作为 Web 应用 *Pages* 文件夹的一部分引用，请使用以下文件结构创建 RCL 项目：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-283">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="8d0ad-284">RazorUIClassLib/Pages</span><span class="sxs-lookup"><span data-stu-id="8d0ad-284">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="8d0ad-285">RazorUIClassLib/Pages/Shared</span><span class="sxs-lookup"><span data-stu-id="8d0ad-285">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="8d0ad-286">假设 RazorUIClassLib/Pages/Shared 包含两个分部文件：_Header.cshtml 和 _Footer.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="8d0ad-286">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="8d0ad-287">可将 `<partial>` 标记添加到 *_Layout.cshtml* 文件：</span><span class="sxs-lookup"><span data-stu-id="8d0ad-287">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8d0ad-288">其他资源</span><span class="sxs-lookup"><span data-stu-id="8d0ad-288">Additional resources</span></span>

* <xref:blazor/components/class-libraries>
