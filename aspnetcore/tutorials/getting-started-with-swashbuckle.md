---
title: Swashbuckle 和 ASP.NET Core 入门
author: zuckerthoben
description: 了解如何将 Swashbuckle 添加到 ASP.NET Core web API 项目中以集成 Swagger UI。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: da848ef9c5fa85f5186d1b6f0a6111d8c8d069c4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78648234"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="cc0e6-103">Swashbuckle 和 ASP.NET Core 入门</span><span class="sxs-lookup"><span data-stu-id="cc0e6-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="cc0e6-104">作者：[Shayne Boyer](https://twitter.com/spboyer) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="cc0e6-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="cc0e6-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="cc0e6-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cc0e6-106">Swashbuckle 有三个主要组成部分：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="cc0e6-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/)：将 `SwaggerDocument` 对象公开为 JSON 终结点的 Swagger 对象模型和中间件。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="cc0e6-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/)：从路由、控制器和模型直接生成 `SwaggerDocument` 对象的 Swagger 生成器。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="cc0e6-109">它通常与 Swagger 终结点中间件结合，以自动公开 Swagger JSON。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="cc0e6-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/)：Swagger UI 工具的嵌入式版本。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="cc0e6-111">它解释 Swagger JSON 以构建描述 Web API 功能的可自定义的丰富体验。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="cc0e6-112">它包括针对公共方法的内置测试工具。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="cc0e6-113">包安装</span><span class="sxs-lookup"><span data-stu-id="cc0e6-113">Package installation</span></span>

<span data-ttu-id="cc0e6-114">可以使用以下方法来添加 Swashbuckle：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="cc0e6-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc0e6-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cc0e6-116">从“程序包管理器控制台”  窗口：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="cc0e6-117">转到“视图” > “其他窗口” > “程序包管理器控制台”   </span><span class="sxs-lookup"><span data-stu-id="cc0e6-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="cc0e6-118">导航到包含 TodoApi.csproj 文件的目录 </span><span class="sxs-lookup"><span data-stu-id="cc0e6-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="cc0e6-119">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* <span data-ttu-id="cc0e6-120">从“管理 NuGet 程序包”  对话框中：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="cc0e6-121">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目  </span><span class="sxs-lookup"><span data-stu-id="cc0e6-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="cc0e6-122">将“包源”  设置为“nuget.org”</span><span class="sxs-lookup"><span data-stu-id="cc0e6-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="cc0e6-123">确保启用“包括预发行版”选项</span><span class="sxs-lookup"><span data-stu-id="cc0e6-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="cc0e6-124">在搜索框中输入“Swashbuckle.AspNetCore”</span><span class="sxs-lookup"><span data-stu-id="cc0e6-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="cc0e6-125">从“浏览”  选项卡中选择最新的“Swashbuckle.AspNetCore”包，然后单击“安装” </span><span class="sxs-lookup"><span data-stu-id="cc0e6-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc0e6-126">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cc0e6-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cc0e6-127">右键单击“Solution Pad”   > “添加包...”  中的“包”  文件夹</span><span class="sxs-lookup"><span data-stu-id="cc0e6-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="cc0e6-128">将“添加包”  窗口的“源”  下拉列表设置为“nuget.org”</span><span class="sxs-lookup"><span data-stu-id="cc0e6-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="cc0e6-129">确保启用“显示预发行包”选项</span><span class="sxs-lookup"><span data-stu-id="cc0e6-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="cc0e6-130">在搜索框中输入“Swashbuckle.AspNetCore”</span><span class="sxs-lookup"><span data-stu-id="cc0e6-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="cc0e6-131">从结果窗格中选择最新的“Swashbuckle.AspNetCore”包，然后单击“添加包” </span><span class="sxs-lookup"><span data-stu-id="cc0e6-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="cc0e6-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc0e6-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cc0e6-133">从“集成终端”  中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="cc0e6-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cc0e6-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cc0e6-135">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="cc0e6-136">添加并配置 Swagger 中间件</span><span class="sxs-lookup"><span data-stu-id="cc0e6-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="cc0e6-137">在 `Startup` 类中，导入以下命名空间来使用 `OpenApiInfo` 类：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-137">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="cc0e6-138">将 Swagger 生成器添加到 `Startup.ConfigureServices` 方法中的服务集合中：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-138">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

<span data-ttu-id="cc0e6-139">在 `Startup.Configure` 方法中，启用中间件为生成的 JSON 文档和 Swagger UI 提供服务：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-139">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

<span data-ttu-id="cc0e6-140">前面的 `UseSwaggerUI` 方法调用启用了[静态文件中间件](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-140">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="cc0e6-141">如果以 .NET Framework 或 .NET Core 1.x 为目标，请将 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-141">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="cc0e6-142">启动应用，并导航到 `http://localhost:<port>/swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-142">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="cc0e6-143">生成的描述终结点的文档显示在 [Swagger 规范 (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson) 中。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-143">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="cc0e6-144">可在 `http://localhost:<port>/swagger` 找到 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-144">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="cc0e6-145">通过 Swagger UI 浏览 API，并将其合并其他计划中。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-145">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="cc0e6-146">要在应用的根 (`http://localhost:<port>/`) 处提供 Swagger UI，请将 `RoutePrefix` 属性设置为空字符串：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-146">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="cc0e6-147">如果使用目录及 IIS 或反向代理，请使用 `./` 前缀将 Swagger 终结点设置为相对路径。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-147">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="cc0e6-148">例如 `./swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-148">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="cc0e6-149">使用 `/swagger/v1/swagger.json` 指示应用在 URL 的真实根目录中查找 JSON 文件（如果使用，加上路由前缀）。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-149">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="cc0e6-150">例如，请使用 `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` 而不是 `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-150">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="cc0e6-151">自定义和扩展</span><span class="sxs-lookup"><span data-stu-id="cc0e6-151">Customize and extend</span></span>

<span data-ttu-id="cc0e6-152">Swagger 提供了为对象模型进行归档和自定义 UI 以匹配你的主题的选项。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-152">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="cc0e6-153">在 `Startup` 类中，添加以下命名空间：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-153">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="cc0e6-154">API 信息和说明</span><span class="sxs-lookup"><span data-stu-id="cc0e6-154">API info and description</span></span>

<span data-ttu-id="cc0e6-155">传递给 `AddSwaggerGen` 方法的配置操作会添加诸如作者、许可证和说明的信息：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-155">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="cc0e6-156">Swagger UI 显示版本的信息：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-156">The Swagger UI displays the version's information:</span></span>

![包含版本信息的 Swagger UI：说明、作者以及查看更多链接](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="cc0e6-158">XML 注释</span><span class="sxs-lookup"><span data-stu-id="cc0e6-158">XML comments</span></span>

<span data-ttu-id="cc0e6-159">可使用以下方法启用 XML 注释：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-159">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="cc0e6-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc0e6-160">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="cc0e6-161">在“解决方案资源管理器”中右键单击该项目，然后选择“编辑 <project_name>.csproj”   。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-161">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="cc0e6-162">手动将突出显示的行添加到 .csproj 文件  ：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-162">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="cc0e6-163">右键单击“解决方案资源管理器”中的项目，再选择“属性”   。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-163">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="cc0e6-164">选中“生成”选项卡的“输出”部分下的“XML 文档文件”框    。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-164">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc0e6-165">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cc0e6-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="cc0e6-166">在 Solution Pad 中，按 control 并单击项目名称   。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-166">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="cc0e6-167">导航到“工具” > “编辑文件”   。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-167">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="cc0e6-168">手动将突出显示的行添加到 .csproj 文件  ：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-168">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="cc0e6-169">打开“项目选项”对话框 >“生成”>“编译器”   </span><span class="sxs-lookup"><span data-stu-id="cc0e6-169">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="cc0e6-170">查看“常规选项”部分下的“生成 xml 文档”框  </span><span class="sxs-lookup"><span data-stu-id="cc0e6-170">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="cc0e6-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc0e6-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cc0e6-172">手动将突出显示的行添加到 .csproj 文件  ：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-172">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="cc0e6-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cc0e6-173">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cc0e6-174">手动将突出显示的行添加到 .csproj 文件  ：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-174">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="cc0e6-175">启用 XML 注释，为未记录的公共类型和成员提供调试信息。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-175">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="cc0e6-176">警告消息指示未记录的类型和成员。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-176">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="cc0e6-177">例如，以下消息指示违反警告代码 1591：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-177">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="cc0e6-178">要在项目范围内取消警告，请定义要在项目文件中忽略的以分号分隔的警告代码列表。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-178">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="cc0e6-179">将警告代码追加到 `$(NoWarn);` 也会应用 [C# 默认值](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-179">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="cc0e6-180">要仅针对特定成员取消警告，请将代码附入 [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) 预处理程序指令中。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-180">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="cc0e6-181">此方法对于不应通过 API 文档公开的代码非常有用。在以下示例中，将忽略整个 `Program` 类的警告代码 CS1591。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-181">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="cc0e6-182">在类定义结束时还原警告代码的强制执行。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-182">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="cc0e6-183">使用逗号分隔的列表指定多个警告代码。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-183">Specify multiple warning codes with a comma-delimited list.</span></span>

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

<span data-ttu-id="cc0e6-184">将 Swagger 配置为使用按照上述说明生成的 XML 文件。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-184">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="cc0e6-185">对于 Linux 或非 Windows 操作系统，文件名和路径区分大小写。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-185">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="cc0e6-186">例如，“TodoApi.XML”文件在 Windows 上有效，但在 CentOS 上无效  。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-186">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

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

<span data-ttu-id="cc0e6-187">在上述代码中，[反射](/dotnet/csharp/programming-guide/concepts/reflection)用于生成与 Web API 项目相匹配的 XML 文件名。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-187">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="cc0e6-188">[AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*)属性用于构造 XML 文件的路径。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-188">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="cc0e6-189">一些 Swagger 功能（例如，输入参数的架构，或各自属性中的 HTTP 方法和响应代码）无需使用 XML 文档文件即可起作用。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-189">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="cc0e6-190">对于大多数功能（即方法摘要以及参数说明和响应代码说明），必须使用 XML 文件。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-190">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="cc0e6-191">通过向节标题添加说明，将三斜杠注释添加到操作增强了 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-191">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="cc0e6-192">执行 `Delete` 操作前添加 [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) 元素：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-192">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="cc0e6-193">Swagger UI 显示上述代码的 `<summary>` 元素的内部文本：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-193">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![显示 DELETE 方法的 XML 注释“删除特定 TodoItem”](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="cc0e6-196">生成的 JSON 架构驱动 UI：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-196">The UI is driven by the generated JSON schema:</span></span>

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

<span data-ttu-id="cc0e6-197">将 [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) 元素添加到 `Create` 操作方法文档。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-197">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="cc0e6-198">它可以补充 `<summary>` 元素中指定的信息，并提供更可靠的 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-198">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="cc0e6-199">`<remarks>` 元素内容可包含文本、JSON 或 XML。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-199">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="cc0e6-200">请注意带这些附加注释的 UI 增强功能：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-200">Notice the UI enhancements with these additional comments:</span></span>

![显示包含其他注释的 Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="cc0e6-202">数据注释</span><span class="sxs-lookup"><span data-stu-id="cc0e6-202">Data annotations</span></span>

<span data-ttu-id="cc0e6-203">使用 [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 命名空间中的属性来标记模型，以帮助驱动 Swagger UI 组件。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-203">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="cc0e6-204">将 `[Required]` 属性添加到 `TodoItem` 类的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-204">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="cc0e6-205">此属性的状态更改 UI 行为并更改基础 JSON 架构：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-205">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

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

<span data-ttu-id="cc0e6-206">将 `[Produces("application/json")]` 属性添加到 API 控制器。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-206">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="cc0e6-207">这样做的目的是声明控制器的操作支持 application/json 的响应内容类型  ：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-207">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="cc0e6-208">“响应内容类型”  下拉列表选此内容类型作为控制器的默认 GET 操作：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-208">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![包含默认响应内容类型的 Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="cc0e6-210">随着 Web API 中的数据注释的使用越来越多，UI 和 API 帮助页变得更具说明性和更为有用。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-210">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="cc0e6-211">描述响应类型</span><span class="sxs-lookup"><span data-stu-id="cc0e6-211">Describe response types</span></span>

<span data-ttu-id="cc0e6-212">使用 Web API 的开发人员最关心的问题是返回的内容，特别是响应类型和错误代码（如果不标准）。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-212">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="cc0e6-213">在 XML 注释和数据注释中表示响应类型和错误代码。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-213">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="cc0e6-214">`Create` 操作成功后返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-214">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="cc0e6-215">发布的请求正文为 NULL 时，将返回 HTTP 400 状态代码。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-215">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="cc0e6-216">如果 Swagger UI 中没有提供合适的文档，那么使用者会缺少对这些预期结果的了解。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-216">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="cc0e6-217">在以下示例中，通过添加突出显示的行解决此问题：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-217">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="cc0e6-218">Swagger UI 现在清楚地记录预期的 HTTP 响应代码：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-218">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Swagger UI 针对“响应消息”下的状态代码和原因显示 POST 响应类描述“返回新建的待办事项”和“400 - 如果该项为 null”](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="cc0e6-220">在 ASP.NET Core 2.2 或更高版本中，约定可以用作使用 `[ProducesResponseType]` 显式修饰各操作的替代方法。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-220">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="cc0e6-221">有关详细信息，请参阅 <xref:web-api/advanced/conventions>。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-221">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="cc0e6-222">自定义 UI</span><span class="sxs-lookup"><span data-stu-id="cc0e6-222">Customize the UI</span></span>

<span data-ttu-id="cc0e6-223">股票 UI 既实用又可呈现。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-223">The stock UI is both functional and presentable.</span></span> <span data-ttu-id="cc0e6-224">但是，API 文档页应代表品牌或主题。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-224">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="cc0e6-225">将 Swashbuckle 组件标记为需要添加资源以提供静态文件，并构建文件夹结构以托管这些文件。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-225">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="cc0e6-226">如果以 .NET Framework 或 .NET Core 1.x 为目标，请将 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet 包添加到项目：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-226">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="cc0e6-227">如果以 .NET Core 2.x 为目标并使用[元包](xref:fundamentals/metapackage)，则已安装上述 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-227">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="cc0e6-228">启用静态文件中间件：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-228">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="cc0e6-229">从 [Swagger UI GitHub 存储库](https://github.com/swagger-api/swagger-ui/tree/master/dist)中获取 dist  文件夹的内容。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-229">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span></span> <span data-ttu-id="cc0e6-230">此文件夹包含 Swagger UI 页必需的资产。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-230">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="cc0e6-231">创建 wwwroot/swagger/ui 文件夹，然后将 dist 文件夹的内容复制到其中   。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-231">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="cc0e6-232">使用以下 CSS 在 wwwroot/swagger/ui 中创建 custom.css 文件，以自定义页面标题   ：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-232">Create a *custom.css* file, in *wwwroot/swagger/ui*, with the following CSS to customize the page header:</span></span>

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

<span data-ttu-id="cc0e6-233">引用其他 CSS 文件后，引用“ui”文件夹内 index.html 文件中的 custom.css   ：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-233">Reference *custom.css* in the *index.html* file inside ui folder, after any other CSS files:</span></span>

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

<span data-ttu-id="cc0e6-234">浏览到 `http://localhost:<port>/swagger/ui/index.html` 中的 index.html  页。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-234">Browse to the *index.html* page at `http://localhost:<port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="cc0e6-235">在标题文本框中输入 `https://localhost:<port>/swagger/v1/swagger.json`，然后单击“浏览”  按钮。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-235">Enter `https://localhost:<port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="cc0e6-236">生成的页面如下所示：</span><span class="sxs-lookup"><span data-stu-id="cc0e6-236">The resulting page looks as follows:</span></span>

![使用自定义标题的 Swagger UI](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="cc0e6-238">还可以对页面执行更多操作。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-238">There's much more you can do with the page.</span></span> <span data-ttu-id="cc0e6-239">在 [Swagger UI GitHub 存储库](https://github.com/swagger-api/swagger-ui)中查看 UI 资源的完整功能。</span><span class="sxs-lookup"><span data-stu-id="cc0e6-239">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
