---
title: Swashbuckle 和 ASP.NET Core 入门
author: zuckerthoben
description: 了解如何将 Swashbuckle 添加到 ASP.NET Core web API 项目中以集成 Swagger UI。
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 0a47ed3338ebfbc5361a6082978d407543fb95c5
ms.sourcegitcommit: b06511252f165dd4590ba9b5beca4153fa220779
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2020
ms.locfileid: "85459774"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="0dbdd-103">Swashbuckle 和 ASP.NET Core 入门</span><span class="sxs-lookup"><span data-stu-id="0dbdd-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="0dbdd-104">作者：[Shayne Boyer](https://twitter.com/spboyer) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="0dbdd-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="0dbdd-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0dbdd-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0dbdd-106">Swashbuckle 有三个主要组成部分：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="0dbdd-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/)：将 `SwaggerDocument` 对象公开为 JSON 终结点的 Swagger 对象模型和中间件。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="0dbdd-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/)：从路由、控制器和模型直接生成 `SwaggerDocument` 对象的 Swagger 生成器。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="0dbdd-109">它通常与 Swagger 终结点中间件结合，以自动公开 Swagger JSON。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="0dbdd-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/)：Swagger UI 工具的嵌入式版本。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="0dbdd-111">它解释 Swagger JSON 以构建描述 Web API 功能的可自定义的丰富体验。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="0dbdd-112">它包括针对公共方法的内置测试工具。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="0dbdd-113">包安装</span><span class="sxs-lookup"><span data-stu-id="0dbdd-113">Package installation</span></span>

<span data-ttu-id="0dbdd-114">可以使用以下方法来添加 Swashbuckle：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="0dbdd-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0dbdd-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0dbdd-116">从“程序包管理器控制台”窗口：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="0dbdd-117">转到“视图” > “其他窗口” > “程序包管理器控制台”  </span><span class="sxs-lookup"><span data-stu-id="0dbdd-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="0dbdd-118">导航到包含 TodoApi.csproj 文件的目录</span><span class="sxs-lookup"><span data-stu-id="0dbdd-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="0dbdd-119">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.5.0
    ```

* <span data-ttu-id="0dbdd-120">从“管理 NuGet 程序包”对话框中：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="0dbdd-121">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目 </span><span class="sxs-lookup"><span data-stu-id="0dbdd-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="0dbdd-122">将“包源”设置为“nuget.org”</span><span class="sxs-lookup"><span data-stu-id="0dbdd-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="0dbdd-123">确保启用“包括预发行版”选项</span><span class="sxs-lookup"><span data-stu-id="0dbdd-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="0dbdd-124">在搜索框中输入“Swashbuckle.AspNetCore”</span><span class="sxs-lookup"><span data-stu-id="0dbdd-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="0dbdd-125">从“浏览”选项卡中选择最新的“Swashbuckle.AspNetCore”包，然后单击“安装”</span><span class="sxs-lookup"><span data-stu-id="0dbdd-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0dbdd-126">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0dbdd-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0dbdd-127">右键单击“Solution Pad” > “添加包...”中的“包”文件夹</span><span class="sxs-lookup"><span data-stu-id="0dbdd-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="0dbdd-128">将“添加包”窗口的“源”下拉列表设置为“nuget.org”</span><span class="sxs-lookup"><span data-stu-id="0dbdd-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="0dbdd-129">确保启用“显示预发行包”选项</span><span class="sxs-lookup"><span data-stu-id="0dbdd-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="0dbdd-130">在搜索框中输入“Swashbuckle.AspNetCore”</span><span class="sxs-lookup"><span data-stu-id="0dbdd-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="0dbdd-131">从结果窗格中选择最新的“Swashbuckle.AspNetCore”包，然后单击“添加包”</span><span class="sxs-lookup"><span data-stu-id="0dbdd-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="0dbdd-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0dbdd-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0dbdd-133">从“集成终端”中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="0dbdd-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0dbdd-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0dbdd-135">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="0dbdd-136">添加并配置 Swagger 中间件</span><span class="sxs-lookup"><span data-stu-id="0dbdd-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="0dbdd-137">将 Swagger 生成器添加到 `Startup.ConfigureServices` 方法中的服务集合中：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-137">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

<span data-ttu-id="0dbdd-138">在 `Startup.Configure` 方法中，启用中间件为生成的 JSON 文档和 Swagger UI 提供服务：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-138">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="0dbdd-139">Swashbuckle 依赖于 MVC 的 <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> 来发现路由和终结点。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-139">Swashbuckle relies on MVC's <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> to discover the routes and endpoints.</span></span> <span data-ttu-id="0dbdd-140">如果项目调用 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>，则自动发现路由和终结点。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-140">If the project calls <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>, routes and endpoints are discovered automatically.</span></span> <span data-ttu-id="0dbdd-141">调用 <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A> 时，必须显式调用 <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-141">When calling <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A>, the <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> method must be explicitly called.</span></span> <span data-ttu-id="0dbdd-142">有关详细信息，请参阅 [Swashbuckle、ApiExplorer 和路由](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing)。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-142">For more information, see [Swashbuckle, ApiExplorer, and Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span></span>

<span data-ttu-id="0dbdd-143">前面的 `UseSwaggerUI` 方法调用启用了[静态文件中间件](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-143">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="0dbdd-144">如果以 .NET Framework 或 .NET Core 1.x 为目标，请将 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-144">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="0dbdd-145">启动应用，并导航到 `http://localhost:<port>/swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-145">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="0dbdd-146">生成的描述终结点的文档显示在 [Swagger 规范 (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson) 中。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-146">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="0dbdd-147">可在 `http://localhost:<port>/swagger` 找到 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-147">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="0dbdd-148">通过 Swagger UI 浏览 API，并将其合并其他计划中。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-148">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="0dbdd-149">要在应用的根 (`http://localhost:<port>/`) 处提供 Swagger UI，请将 `RoutePrefix` 属性设置为空字符串：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-149">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="0dbdd-150">如果使用目录及 IIS 或反向代理，请使用 `./` 前缀将 Swagger 终结点设置为相对路径。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-150">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="0dbdd-151">例如 `./swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-151">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="0dbdd-152">使用 `/swagger/v1/swagger.json` 指示应用在 URL 的真实根目录中查找 JSON 文件（如果使用，加上路由前缀）。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-152">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="0dbdd-153">例如，请使用 `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` 而不是 `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-153">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

> [!NOTE]
> <span data-ttu-id="0dbdd-154">默认情况下，Swashbuckle 会在 3.0 版规范（官方称为 OpenAPI 规范）中生成并公开 Swagger JSON。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-154">By default, Swashbuckle generates and exposes Swagger JSON in version 3.0 of the specification&mdash;officially called the OpenAPI Specification.</span></span> <span data-ttu-id="0dbdd-155">为了支持向后兼容性，可以改为选择以 2.0 格式公开 JSON。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-155">To support backwards compatibility, you can opt into exposing JSON in the 2.0 format instead.</span></span> <span data-ttu-id="0dbdd-156">对于当前支持 OpenAPI 版本 2.0 的 Microsoft Power Apps 和 Microsoft Flow 等集成，此 2.0 格式非常重要。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-156">This 2.0 format is important for integrations such as Microsoft Power Apps and Microsoft Flow that currently support OpenAPI version 2.0.</span></span> <span data-ttu-id="0dbdd-157">若要选择采用 2.0 格式，请在 `Startup.Configure` 中设置 `SerializeAsV2` 属性：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-157">To opt into the 2.0 format, set the `SerializeAsV2` property in `Startup.Configure`:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a><span data-ttu-id="0dbdd-158">自定义和扩展</span><span class="sxs-lookup"><span data-stu-id="0dbdd-158">Customize and extend</span></span>

<span data-ttu-id="0dbdd-159">Swagger 提供了为对象模型进行归档和自定义 UI 以匹配你的主题的选项。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-159">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="0dbdd-160">在 `Startup` 类中，添加以下命名空间：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-160">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="0dbdd-161">API 信息和说明</span><span class="sxs-lookup"><span data-stu-id="0dbdd-161">API info and description</span></span>

<span data-ttu-id="0dbdd-162">传递给 `AddSwaggerGen` 方法的配置操作会添加诸如作者、许可证和说明的信息：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-162">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

<span data-ttu-id="0dbdd-163">在 `Startup` 类中，导入以下命名空间来使用 `OpenApiInfo` 类：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-163">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="0dbdd-164">使用 `OpenApiInfo` 类修改 UI 中显示的信息：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-164">Using the `OpenApiInfo` class, modify the information displayed in the UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="0dbdd-165">Swagger UI 显示版本的信息：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-165">The Swagger UI displays the version's information:</span></span>

![包含版本信息的 Swagger UI：说明、作者以及查看更多链接](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="0dbdd-167">XML 注释</span><span class="sxs-lookup"><span data-stu-id="0dbdd-167">XML comments</span></span>

<span data-ttu-id="0dbdd-168">可使用以下方法启用 XML 注释：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-168">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="0dbdd-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0dbdd-169">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="0dbdd-170">在“解决方案资源管理器”中右键单击该项目，然后选择“编辑 <project_name>.csproj” 。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-170">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="0dbdd-171">手动将突出显示的行添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-171">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="0dbdd-172">右键单击“解决方案资源管理器”中的项目，再选择“属性” 。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-172">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="0dbdd-173">选中“生成”选项卡的“输出”部分下的“XML 文档文件”框  。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-173">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0dbdd-174">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0dbdd-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="0dbdd-175">在 Solution Pad 中，按 control 并单击项目名称。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-175">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="0dbdd-176">导航到“工具” > “编辑文件” 。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-176">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="0dbdd-177">手动将突出显示的行添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="0dbdd-178">打开“项目选项”对话框 >“生成”>“编译器”  </span><span class="sxs-lookup"><span data-stu-id="0dbdd-178">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="0dbdd-179">查看“常规选项”部分下的“生成 xml 文档”框 </span><span class="sxs-lookup"><span data-stu-id="0dbdd-179">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="0dbdd-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0dbdd-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0dbdd-181">手动将突出显示的行添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-181">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="0dbdd-182">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0dbdd-182">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0dbdd-183">手动将突出显示的行添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="0dbdd-184">启用 XML 注释，为未记录的公共类型和成员提供调试信息。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-184">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="0dbdd-185">警告消息指示未记录的类型和成员。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-185">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="0dbdd-186">例如，以下消息指示违反警告代码 1591：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-186">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="0dbdd-187">要在项目范围内取消警告，请定义要在项目文件中忽略的以分号分隔的警告代码列表。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-187">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="0dbdd-188">将警告代码追加到 `$(NoWarn);` 也会应用 [C# 默认值](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-188">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="0dbdd-189">要仅针对特定成员取消警告，请将代码附入 [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) 预处理程序指令中。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-189">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="0dbdd-190">此方法对于不应通过 API 文档公开的代码非常有用。在以下示例中，将忽略整个 `Program` 类的警告代码 CS1591。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-190">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="0dbdd-191">在类定义结束时还原警告代码的强制执行。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-191">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="0dbdd-192">使用逗号分隔的列表指定多个警告代码。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-192">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="0dbdd-193">将 Swagger 配置为使用按照上述说明生成的 XML 文件。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-193">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="0dbdd-194">对于 Linux 或非 Windows 操作系统，文件名和路径区分大小写。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-194">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="0dbdd-195">例如，“TodoApi.XML”文件在 Windows 上有效，但在 CentOS 上无效。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-195">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="0dbdd-196">在上述代码中，[反射](/dotnet/csharp/programming-guide/concepts/reflection)用于生成与 Web API 项目相匹配的 XML 文件名。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-196">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="0dbdd-197">[AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*)属性用于构造 XML 文件的路径。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-197">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="0dbdd-198">一些 Swagger 功能（例如，输入参数的架构，或各自属性中的 HTTP 方法和响应代码）无需使用 XML 文档文件即可起作用。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-198">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="0dbdd-199">对于大多数功能（即方法摘要以及参数说明和响应代码说明），必须使用 XML 文件。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-199">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="0dbdd-200">通过向节标题添加说明，将三斜杠注释添加到操作增强了 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-200">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="0dbdd-201">执行 `Delete` 操作前添加 [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) 元素：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-201">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="0dbdd-202">Swagger UI 显示上述代码的 `<summary>` 元素的内部文本：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-202">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![显示 DELETE 方法的 XML 注释“删除特定 TodoItem”](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="0dbdd-205">生成的 JSON 架构驱动 UI：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-205">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="0dbdd-206">将 [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) 元素添加到 `Create` 操作方法文档。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-206">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="0dbdd-207">它可以补充 `<summary>` 元素中指定的信息，并提供更可靠的 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-207">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="0dbdd-208">`<remarks>` 元素内容可包含文本、JSON 或 XML。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-208">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="0dbdd-209">请注意带这些附加注释的 UI 增强功能：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-209">Notice the UI enhancements with these additional comments:</span></span>

![显示包含其他注释的 Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="0dbdd-211">数据注释</span><span class="sxs-lookup"><span data-stu-id="0dbdd-211">Data annotations</span></span>

<span data-ttu-id="0dbdd-212">使用 [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 命名空间中的属性来标记模型，以帮助驱动 Swagger UI 组件。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-212">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="0dbdd-213">将 `[Required]` 属性添加到 `TodoItem` 类的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-213">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="0dbdd-214">此属性的状态更改 UI 行为并更改基础 JSON 架构：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-214">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="0dbdd-215">将 `[Produces("application/json")]` 属性添加到 API 控制器。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-215">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="0dbdd-216">这样做的目的是声明控制器的操作支持 application/json 的响应内容类型：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-216">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="0dbdd-217">“响应内容类型”下拉列表选此内容类型作为控制器的默认 GET 操作：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-217">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![包含默认响应内容类型的 Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="0dbdd-219">随着 Web API 中的数据注释的使用越来越多，UI 和 API 帮助页变得更具说明性和更为有用。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-219">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="0dbdd-220">描述响应类型</span><span class="sxs-lookup"><span data-stu-id="0dbdd-220">Describe response types</span></span>

<span data-ttu-id="0dbdd-221">使用 Web API 的开发人员最关心的问题是返回的内容，特别是响应类型和错误代码（如果不标准）。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-221">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="0dbdd-222">在 XML 注释和数据注释中表示响应类型和错误代码。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-222">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="0dbdd-223">`Create` 操作成功后返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-223">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="0dbdd-224">发布的请求正文为 NULL 时，将返回 HTTP 400 状态代码。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-224">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="0dbdd-225">如果 Swagger UI 中没有提供合适的文档，那么使用者会缺少对这些预期结果的了解。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-225">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="0dbdd-226">在以下示例中，通过添加突出显示的行解决此问题：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-226">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="0dbdd-227">Swagger UI 现在清楚地记录预期的 HTTP 响应代码：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-227">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Swagger UI 针对“响应消息”下的状态代码和原因显示 POST 响应类描述“返回新建的待办事项”和“400 - 如果该项为 null”](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0dbdd-229">在 ASP.NET Core 2.2 或更高版本中，约定可以用作使用 `[ProducesResponseType]` 显式修饰各操作的替代方法。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-229">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="0dbdd-230">有关详细信息，请参阅 <xref:web-api/advanced/conventions>。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-230">For more information, see <xref:web-api/advanced/conventions>.</span></span>

<span data-ttu-id="0dbdd-231">为了支持 `[ProducesResponseType]` 修饰，[Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) 包提供了用于启用和丰富响应、架构和参数元数据的扩展。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-231">To support the `[ProducesResponseType]` decoration, the [Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) package offers extensions to enable and enrich the response, schema, and parameter metadata.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="0dbdd-232">自定义 UI</span><span class="sxs-lookup"><span data-stu-id="0dbdd-232">Customize the UI</span></span>

<span data-ttu-id="0dbdd-233">默认 UI 既实用又可呈现。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-233">The default UI is both functional and presentable.</span></span> <span data-ttu-id="0dbdd-234">但是，API 文档页应代表品牌或主题。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-234">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="0dbdd-235">将 Swashbuckle 组件标记为需要添加资源以提供静态文件，并构建文件夹结构以托管这些文件。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-235">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="0dbdd-236">如果以 .NET Framework 或 .NET Core 1.x 为目标，请将 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet 包添加到项目：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-236">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="0dbdd-237">如果以 .NET Core 2.x 为目标并使用[元包](xref:fundamentals/metapackage)，则已安装上述 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="0dbdd-237">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="0dbdd-238">启用静态文件中间件：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-238">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="0dbdd-239">若要插入其他 CSS 样式表，请将其添加到项目的 wwwroot 文件夹中，并在中间件选项中指定相对路径：</span><span class="sxs-lookup"><span data-stu-id="0dbdd-239">To inject additional CSS stylesheets, add them to the project's *wwwroot* folder and specify the relative path in the middleware options:</span></span>

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```
