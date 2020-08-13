---
title: NSwag 和 ASP.NET Core 入门
author: zuckerthoben
description: 了解如何使用 NSwag 为 ASP.NET Core Web API 生成文档和帮助页面。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 8fd42c7d31edd20c2aae7577c5a490b54ab8129c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022259"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="d5032-103">NSwag 和 ASP.NET Core 入门</span><span class="sxs-lookup"><span data-stu-id="d5032-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="d5032-104">作者：[Christoph Nienaber](https://twitter.com/zuckerthoben)、[Rico Suter](https://rsuter.com) 和 [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="d5032-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="d5032-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d5032-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="d5032-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d5032-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="d5032-107">NSwag 提供了下列功能：</span><span class="sxs-lookup"><span data-stu-id="d5032-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="d5032-108">能够使用 Swagger UI 和 Swagger 生成器。</span><span class="sxs-lookup"><span data-stu-id="d5032-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="d5032-109">灵活的代码生成功能。</span><span class="sxs-lookup"><span data-stu-id="d5032-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="d5032-110">借助 NSwag，无需使用现有 API。也就是说，可使用包含 Swagger 的第三方 API，并生成客户端实现。</span><span class="sxs-lookup"><span data-stu-id="d5032-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="d5032-111">使用 NSwag，可以加快开发周期，并轻松适应 API 更改。</span><span class="sxs-lookup"><span data-stu-id="d5032-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="d5032-112">注册 NSwag 中间件</span><span class="sxs-lookup"><span data-stu-id="d5032-112">Register the NSwag middleware</span></span>

<span data-ttu-id="d5032-113">注册 NSwag 中间件即可：</span><span class="sxs-lookup"><span data-stu-id="d5032-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="d5032-114">生成已实现的 Web API 的 Swagger 规范。</span><span class="sxs-lookup"><span data-stu-id="d5032-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="d5032-115">为 Swagger UI 提供服务以浏览和测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="d5032-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="d5032-116">若要使用 [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core 中间件，请安装 [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d5032-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="d5032-117">此包内的中间件可用于生成并提供Swagger 规范、Swagger UI（v2 和 v3）和 [ReDoc UI](https://github.com/Rebilly/ReDoc)。</span><span class="sxs-lookup"><span data-stu-id="d5032-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="d5032-118">若要安装 NSwag NuGet 包，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="d5032-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5032-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5032-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5032-120">从“程序包管理器控制台”窗口：</span><span class="sxs-lookup"><span data-stu-id="d5032-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="d5032-121">转到“视图” > “其他窗口” > “程序包管理器控制台”  </span><span class="sxs-lookup"><span data-stu-id="d5032-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="d5032-122">导航到包含 TodoApi.csproj 文件的目录</span><span class="sxs-lookup"><span data-stu-id="d5032-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="d5032-123">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="d5032-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="d5032-124">从“管理 NuGet 程序包”对话框中：</span><span class="sxs-lookup"><span data-stu-id="d5032-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="d5032-125">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目 </span><span class="sxs-lookup"><span data-stu-id="d5032-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="d5032-126">将“包源”设置为“nuget.org”</span><span class="sxs-lookup"><span data-stu-id="d5032-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="d5032-127">在搜索框中输入“NSwag.AspNetCore”</span><span class="sxs-lookup"><span data-stu-id="d5032-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="d5032-128">从“浏览”选项卡中选择“NSwag.AspNetCore”包，然后单击“安装” </span><span class="sxs-lookup"><span data-stu-id="d5032-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5032-129">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d5032-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d5032-130">右键单击“Solution Pad” > “添加包...”中的“包”文件夹</span><span class="sxs-lookup"><span data-stu-id="d5032-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="d5032-131">将“添加包”窗口的“源”下拉列表设置为“nuget.org”</span><span class="sxs-lookup"><span data-stu-id="d5032-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="d5032-132">在搜索框中输入“NSwag.AspNetCore”</span><span class="sxs-lookup"><span data-stu-id="d5032-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="d5032-133">从结果窗格中选择“NSwag.AspNetCore”包，然后单击“添加包”</span><span class="sxs-lookup"><span data-stu-id="d5032-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d5032-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d5032-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d5032-135">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="d5032-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="d5032-136">添加并配置 Swagger 中间件</span><span class="sxs-lookup"><span data-stu-id="d5032-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="d5032-137">通过执行以下步骤，在 ASP.NET Core 应用中添加和配置 Swagger：</span><span class="sxs-lookup"><span data-stu-id="d5032-137">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="d5032-138">在 `Startup.ConfigureServices` 方法中，注册所需的 Swagger 服务：</span><span class="sxs-lookup"><span data-stu-id="d5032-138">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="d5032-139">在 `Startup.Configure` 方法中，启用中间件为生成的 Swagger 规范和 Swagger UI 提供服务：</span><span class="sxs-lookup"><span data-stu-id="d5032-139">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="d5032-140">启动应用。</span><span class="sxs-lookup"><span data-stu-id="d5032-140">Launch the app.</span></span> <span data-ttu-id="d5032-141">转到：</span><span class="sxs-lookup"><span data-stu-id="d5032-141">Navigate to:</span></span>
  * <span data-ttu-id="d5032-142">`http://localhost:<port>/swagger`，以查看 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="d5032-142">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="d5032-143">`http://localhost:<port>/swagger/v1/swagger.json`，以查看 Swagger 规范。</span><span class="sxs-lookup"><span data-stu-id="d5032-143">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="d5032-144">代码生成</span><span class="sxs-lookup"><span data-stu-id="d5032-144">Code generation</span></span>

<span data-ttu-id="d5032-145">若要利用 NSwag 的代码生成功能，可选择以下选项之一：</span><span class="sxs-lookup"><span data-stu-id="d5032-145">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="d5032-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio)：一款 Windows 桌面应用，用于在 C# 或 TypeScript 中生成 API 客户端代码。</span><span class="sxs-lookup"><span data-stu-id="d5032-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): A Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="d5032-147">[NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) 或 [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet 包 - 用于在项目中生成代码。</span><span class="sxs-lookup"><span data-stu-id="d5032-147">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="d5032-148">通过[命令行](https://github.com/RicoSuter/NSwag/wiki/CommandLine)使用 NSwag。</span><span class="sxs-lookup"><span data-stu-id="d5032-148">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="d5032-149">[NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d5032-149">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet package.</span></span>
* <span data-ttu-id="d5032-150">[Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice)（Unchase OpenAPI (Swagger) 连接服务）：一种 Visual Studio 连接服务，用于在 C# 或 TypeScript 中生成 API 客户端代码。</span><span class="sxs-lookup"><span data-stu-id="d5032-150">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): A Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="d5032-151">还可以使用 NSwag 为 OpenAPI 服务生成 C# 控制器。</span><span class="sxs-lookup"><span data-stu-id="d5032-151">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="d5032-152">使用 NSwagStudio 生成代码</span><span class="sxs-lookup"><span data-stu-id="d5032-152">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="d5032-153">按照 [NSwagStudio GitHub 存储库](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio)中的说明操作，以安装 NSwagStudio。</span><span class="sxs-lookup"><span data-stu-id="d5032-153">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span> <span data-ttu-id="d5032-154">在 NSwag 发布页面上，可以下载无需安装和管理员权限即可启动的 xcopy 版本。</span><span class="sxs-lookup"><span data-stu-id="d5032-154">On the NSwag release page you can download an xcopy version which can be started without installation and admin privileges.</span></span>
* <span data-ttu-id="d5032-155">启动 NSwagStudio，并在“Swagger 规范 URL”文本框中输入 swagger.json 文件 URL。</span><span class="sxs-lookup"><span data-stu-id="d5032-155">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="d5032-156">例如， *http://localhost:44354/swagger/v1/swagger.json* 。</span><span class="sxs-lookup"><span data-stu-id="d5032-156">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="d5032-157">单击“创建本地副本”按钮，以生成 Swagger 规范的 JSON 表示形式。</span><span class="sxs-lookup"><span data-stu-id="d5032-157">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![创建 Swagger 规范的本地副本](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="d5032-159">在“输出”区域中，单击选中“C# 客户端”复选框。</span><span class="sxs-lookup"><span data-stu-id="d5032-159">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="d5032-160">也可以选中“TypeScript 客户端”或“C# Web API 控制器”，具体视项目而定。</span><span class="sxs-lookup"><span data-stu-id="d5032-160">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="d5032-161">如果选中“C# Web API 控制器”，服务规范会重新生成服务，起到反向生成的作用。</span><span class="sxs-lookup"><span data-stu-id="d5032-161">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="d5032-162">单击“生成输出”，以生成 TodoApi.NSwag 项目的完整 C# 客户端实现。</span><span class="sxs-lookup"><span data-stu-id="d5032-162">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="d5032-163">若要查看生成的客户端代码，请单击“C# 客户端”选项卡：</span><span class="sxs-lookup"><span data-stu-id="d5032-163">To see the generated client code, click the **CSharp Client** tab:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> <span data-ttu-id="d5032-164">C# 客户端代码的生成依据是，“设置”选项卡中的选择。修改设置以执行任务，例如默认命名空间重命名和同步方法生成。</span><span class="sxs-lookup"><span data-stu-id="d5032-164">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="d5032-165">将生成的 C# 代码复制到使用 API 的客户端项目内的文件中。</span><span class="sxs-lookup"><span data-stu-id="d5032-165">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="d5032-166">开始使用 Web API：</span><span class="sxs-lookup"><span data-stu-id="d5032-166">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="d5032-167">自定义 API 文档</span><span class="sxs-lookup"><span data-stu-id="d5032-167">Customize API documentation</span></span>

<span data-ttu-id="d5032-168">Swagger 提供用于记录对象模型以便于使用 Web API 的选项。</span><span class="sxs-lookup"><span data-stu-id="d5032-168">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="d5032-169">API 信息和说明</span><span class="sxs-lookup"><span data-stu-id="d5032-169">API info and description</span></span>

<span data-ttu-id="d5032-170">在 `Startup.ConfigureServices` 方法中，传递给 `AddSwaggerDocument` 方法的配置操作会添加诸如作者、许可证和说明的信息：</span><span class="sxs-lookup"><span data-stu-id="d5032-170">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="d5032-171">Swagger UI 显示版本的信息：</span><span class="sxs-lookup"><span data-stu-id="d5032-171">The Swagger UI displays the version's information:</span></span>

![带有版本信息的 Swagger UI](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="d5032-173">XML 注释</span><span class="sxs-lookup"><span data-stu-id="d5032-173">XML comments</span></span>

<span data-ttu-id="d5032-174">若要启用 XML 注释，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="d5032-174">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d5032-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5032-175">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="d5032-176">在“解决方案资源管理器”中右键单击该项目，然后选择“编辑 <project_name>.csproj” 。</span><span class="sxs-lookup"><span data-stu-id="d5032-176">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="d5032-177">手动将突出显示的行添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="d5032-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="d5032-178">右键单击“解决方案资源管理器”中的项目，然后选择“属性”</span><span class="sxs-lookup"><span data-stu-id="d5032-178">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="d5032-179">查看“生成”选项卡的“输出”部分下的“XML 文档文件”框  </span><span class="sxs-lookup"><span data-stu-id="d5032-179">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d5032-180">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d5032-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="d5032-181">在 Solution Pad 中，按 control 并单击项目名称。</span><span class="sxs-lookup"><span data-stu-id="d5032-181">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="d5032-182">导航到“工具” > “编辑文件” 。</span><span class="sxs-lookup"><span data-stu-id="d5032-182">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="d5032-183">手动将突出显示的行添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="d5032-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="d5032-184">打开“项目选项”对话框 >“生成”>“编译器”  </span><span class="sxs-lookup"><span data-stu-id="d5032-184">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="d5032-185">查看“常规选项”部分下的“生成 xml 文档”框 </span><span class="sxs-lookup"><span data-stu-id="d5032-185">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="net-core-cli"></a>[<span data-ttu-id="d5032-186">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d5032-186">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d5032-187">手动将突出显示的行添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="d5032-187">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="d5032-188">数据注释</span><span class="sxs-lookup"><span data-stu-id="d5032-188">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="d5032-189">由于 NSwag 使用[反射](/dotnet/csharp/programming-guide/concepts/reflection)，且建议的 Web API 操作返回类型为 [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult)，因此无法推断正在执行的操作和返回的内容。</span><span class="sxs-lookup"><span data-stu-id="d5032-189">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="d5032-190">请看下面的示例：</span><span class="sxs-lookup"><span data-stu-id="d5032-190">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="d5032-191">上述操作返回 `IActionResult`，但在操作内部返回 [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) 或 [BadRequest](xref:System.Web.Http.ApiController.BadRequest*)。</span><span class="sxs-lookup"><span data-stu-id="d5032-191">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="d5032-192">使用数据注释告知客户端，已知此操作会返回哪些 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="d5032-192">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="d5032-193">使用以下属性标记该操作：</span><span class="sxs-lookup"><span data-stu-id="d5032-193">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="d5032-194">由于 NSwag 使用[反射](/dotnet/csharp/programming-guide/concepts/reflection)，且建议的 Web API 操作返回类型为 [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601)，因此只能推断 `T` 定义的返回类型。</span><span class="sxs-lookup"><span data-stu-id="d5032-194">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="d5032-195">无法自动推断其他可能的返回类型。</span><span class="sxs-lookup"><span data-stu-id="d5032-195">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="d5032-196">请看下面的示例：</span><span class="sxs-lookup"><span data-stu-id="d5032-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="d5032-197">上述操作将返回 `ActionResult<T>`。</span><span class="sxs-lookup"><span data-stu-id="d5032-197">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="d5032-198">在操作中，它将返回 [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*)。</span><span class="sxs-lookup"><span data-stu-id="d5032-198">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="d5032-199">由于控制器具有 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性，所以也可能出现 [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) 响应。</span><span class="sxs-lookup"><span data-stu-id="d5032-199">Since the controller has the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="d5032-200">有关详细信息，请参阅[自动 HTTP 400 响应](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="d5032-200">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="d5032-201">使用数据注释告知客户端，已知此操作会返回哪些 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="d5032-201">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="d5032-202">使用以下属性标记该操作：</span><span class="sxs-lookup"><span data-stu-id="d5032-202">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="d5032-203">在 ASP.NET Core 2.2 或更高版本中，可使用约定，而不是使用 `[ProducesResponseType]` 显式修饰各操作。</span><span class="sxs-lookup"><span data-stu-id="d5032-203">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="d5032-204">有关详细信息，请参阅 <xref:web-api/advanced/conventions>。</span><span class="sxs-lookup"><span data-stu-id="d5032-204">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="d5032-205">Swagger 生成器现在可准确地描述此操作，且生成的客户端知道调用终结点时收到的内容。</span><span class="sxs-lookup"><span data-stu-id="d5032-205">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="d5032-206">建议使用这些属性来标记所有操作。</span><span class="sxs-lookup"><span data-stu-id="d5032-206">As a recommendation, mark all actions with these attributes.</span></span>

<span data-ttu-id="d5032-207">有关 API 操作应返回的 HTTP 响应的指导原则，请参阅 [RFC 7231 规范](https://tools.ietf.org/html/rfc7231#section-4.3)。</span><span class="sxs-lookup"><span data-stu-id="d5032-207">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
