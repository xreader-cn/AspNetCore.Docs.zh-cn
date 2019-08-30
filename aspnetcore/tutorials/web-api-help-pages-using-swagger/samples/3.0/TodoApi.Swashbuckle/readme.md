---
page_type: sample
description: 了解如何将 Swashbuckle 添加到 ASP.NET Core web API 项目中以集成 Swagger UI。
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: 2b1da1d524eb18f1048314c544c64f82c22761e9
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69988963"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="9d76d-102">Swashbuckle 和 ASP.NET Core 入门</span><span class="sxs-lookup"><span data-stu-id="9d76d-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="9d76d-103">使用 Web API 时，了解其各种方法对开发人员来说可能是一项挑战。</span><span class="sxs-lookup"><span data-stu-id="9d76d-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="9d76d-104">[Swagger](https://swagger.io/) 也称为[OpenAPI](https://www.openapis.org/)，解决了为 Web API 生成有用文档和帮助页的问题。</span><span class="sxs-lookup"><span data-stu-id="9d76d-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="9d76d-105">它具有诸如交互式文档、客户端 SDK 生成和 API 可发现性等优点。</span><span class="sxs-lookup"><span data-stu-id="9d76d-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="9d76d-106">在此示例中，显示了 .NET 实现 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="9d76d-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="9d76d-107">添加并配置 Swagger 中间件</span><span class="sxs-lookup"><span data-stu-id="9d76d-107">Add and configure Swagger middleware</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

<span data-ttu-id="9d76d-108">在 `Startup.Configure` 方法中，启用中间件为生成的 JSON 文档和 Swagger UI 提供服务：</span><span class="sxs-lookup"><span data-stu-id="9d76d-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");In the 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="9d76d-109">前面的 `UseSwaggerUI` 方法调用启用了[静态文件中间件](https://docs.microsoft.com/aspnet/core/fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="9d76d-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="9d76d-110">如果以 .NET Framework 或 .NET Core 1.x 为目标，请将 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="9d76d-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="9d76d-111">启动应用，并导航到 `http://localhost:<port>/swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="9d76d-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="9d76d-112">生成的描述终结点的文档显示在 [Swagger 规范 (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson) 中。</span><span class="sxs-lookup"><span data-stu-id="9d76d-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="9d76d-113">可在 `http://localhost:<port>/swagger` 找到 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="9d76d-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="9d76d-114">通过 Swagger UI 浏览 API，并将其合并其他计划中。</span><span class="sxs-lookup"><span data-stu-id="9d76d-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="9d76d-115">要在应用的根 (`http://localhost:<port>/`) 处提供 Swagger UI，请将 `RoutePrefix` 属性设置为空字符串：</span><span class="sxs-lookup"><span data-stu-id="9d76d-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="9d76d-116">如果使用目录及 IIS 或反向代理，请使用 `./` 前缀将 Swagger 终结点设置为相对路径。</span><span class="sxs-lookup"><span data-stu-id="9d76d-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="9d76d-117">例如 `./swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="9d76d-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="9d76d-118">使用 `/swagger/v1/swagger.json` 指示应用在 URL 的真实根目录中查找 JSON 文件（如果使用，加上路由前缀）。</span><span class="sxs-lookup"><span data-stu-id="9d76d-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="9d76d-119">例如，请使用 `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` 而不是 `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="9d76d-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="9d76d-120">自定义和扩展</span><span class="sxs-lookup"><span data-stu-id="9d76d-120">Customize and extend</span></span>

<span data-ttu-id="9d76d-121">Swagger 提供了为对象模型进行归档和自定义 UI 以匹配你的主题的选项。</span><span class="sxs-lookup"><span data-stu-id="9d76d-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="9d76d-122">在 `Startup` 类中，添加以下命名空间：</span><span class="sxs-lookup"><span data-stu-id="9d76d-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="9d76d-123">API 信息和说明</span><span class="sxs-lookup"><span data-stu-id="9d76d-123">API info and description</span></span>

<span data-ttu-id="9d76d-124">传递给 `AddSwaggerGen` 方法的配置操作会添加诸如作者、许可证和说明的信息：</span><span class="sxs-lookup"><span data-stu-id="9d76d-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

<span data-ttu-id="9d76d-125">Swagger UI 显示版本的信息：</span><span class="sxs-lookup"><span data-stu-id="9d76d-125">The Swagger UI displays the version's information:</span></span>

![包含版本信息的 Swagger UI：说明、作者以及查看更多链接](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="9d76d-127">XML 注释</span><span class="sxs-lookup"><span data-stu-id="9d76d-127">XML comments</span></span>

<span data-ttu-id="9d76d-128">可使用以下方法启用 XML 注释：</span><span class="sxs-lookup"><span data-stu-id="9d76d-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9d76d-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d76d-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9d76d-130">在“解决方案资源管理器”中右键单击该项目，然后选择“编辑 <project_name>.csproj”   。</span><span class="sxs-lookup"><span data-stu-id="9d76d-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="9d76d-131">手动将突出显示的行添加到 .csproj 文件  ：</span><span class="sxs-lookup"><span data-stu-id="9d76d-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9d76d-132">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9d76d-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9d76d-133">在 Solution Pad 中，按 control 并单击项目名称   。</span><span class="sxs-lookup"><span data-stu-id="9d76d-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="9d76d-134">导航到“工具” > “编辑文件”   。</span><span class="sxs-lookup"><span data-stu-id="9d76d-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="9d76d-135">手动将突出显示的行添加到 .csproj 文件  ：</span><span class="sxs-lookup"><span data-stu-id="9d76d-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9d76d-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d76d-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9d76d-137">手动将突出显示的行添加到 .csproj 文件  ：</span><span class="sxs-lookup"><span data-stu-id="9d76d-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="9d76d-138">启用 XML 注释，为未记录的公共类型和成员提供调试信息。</span><span class="sxs-lookup"><span data-stu-id="9d76d-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="9d76d-139">警告消息指示未记录的类型和成员。</span><span class="sxs-lookup"><span data-stu-id="9d76d-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="9d76d-140">例如，以下消息指示违反警告代码 1591：</span><span class="sxs-lookup"><span data-stu-id="9d76d-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="9d76d-141">要在项目范围内取消警告，请定义要在项目文件中忽略的以分号分隔的警告代码列表。</span><span class="sxs-lookup"><span data-stu-id="9d76d-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="9d76d-142">将警告代码追加到 `$(NoWarn);` 也会应用 [C# 默认值](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)。</span><span class="sxs-lookup"><span data-stu-id="9d76d-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="9d76d-143">要仅针对特定成员取消警告，请将代码附入 [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) 预处理程序指令中。</span><span class="sxs-lookup"><span data-stu-id="9d76d-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="9d76d-144">此方法对于不应通过 API 文档公开的代码非常有用。在以下示例中，将忽略整个 `Program` 类的警告代码 CS1591。</span><span class="sxs-lookup"><span data-stu-id="9d76d-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="9d76d-145">在类定义结束时还原警告代码的强制执行。</span><span class="sxs-lookup"><span data-stu-id="9d76d-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="9d76d-146">使用逗号分隔的列表指定多个警告代码。</span><span class="sxs-lookup"><span data-stu-id="9d76d-146">Specify multiple warning codes with a comma-delimited list.</span></span>

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

<span data-ttu-id="9d76d-147">将 Swagger 配置为使用按照上述说明生成的 XML 文件。</span><span class="sxs-lookup"><span data-stu-id="9d76d-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="9d76d-148">对于 Linux 或非 Windows 操作系统，文件名和路径区分大小写。</span><span class="sxs-lookup"><span data-stu-id="9d76d-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="9d76d-149">例如，“TodoApi.XML”文件在 Windows 上有效，但在 CentOS 上无效  。</span><span class="sxs-lookup"><span data-stu-id="9d76d-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

<span data-ttu-id="9d76d-150">在上述代码中，[反射](/dotnet/csharp/programming-guide/concepts/reflection)用于生成与 Web API 项目相匹配的 XML 文件名。</span><span class="sxs-lookup"><span data-stu-id="9d76d-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="9d76d-151">[AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory)属性用于构造 XML 文件的路径。</span><span class="sxs-lookup"><span data-stu-id="9d76d-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="9d76d-152">一些 Swagger 功能（例如，输入参数的架构，或各自属性中的 HTTP 方法和响应代码）无需使用 XML 文档文件即可起作用。</span><span class="sxs-lookup"><span data-stu-id="9d76d-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="9d76d-153">对于大多数功能（即方法摘要以及参数说明和响应代码说明），必须使用 XML 文件。</span><span class="sxs-lookup"><span data-stu-id="9d76d-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="9d76d-154">通过向节标题添加说明，将三斜杠注释添加到操作增强了 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="9d76d-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="9d76d-155">执行 `Delete` 操作前添加 [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) 元素：</span><span class="sxs-lookup"><span data-stu-id="9d76d-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
<span data-ttu-id="9d76d-156">Swagger UI 显示上述代码的 `<summary>` 元素的内部文本：</span><span class="sxs-lookup"><span data-stu-id="9d76d-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![显示 DELETE 方法的 XML 注释“删除特定 TodoItem”](sample_images/triple-slash-comments.png)

<span data-ttu-id="9d76d-159">生成的 JSON 架构驱动 UI：</span><span class="sxs-lookup"><span data-stu-id="9d76d-159">The UI is driven by the generated JSON schema:</span></span>

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
<span data-ttu-id="9d76d-160">将 [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) 元素添加到 `Create` 操作方法文档。</span><span class="sxs-lookup"><span data-stu-id="9d76d-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="9d76d-161">它可以补充 `<summary>` 元素中指定的信息，并提供更可靠的 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="9d76d-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="9d76d-162">`<remarks>` 元素内容可包含文本、JSON 或 XML。</span><span class="sxs-lookup"><span data-stu-id="9d76d-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(201)]
[ProducesResponseType(400)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
<span data-ttu-id="9d76d-163">请注意带这些附加注释的 UI 增强功能：</span><span class="sxs-lookup"><span data-stu-id="9d76d-163">Notice the UI enhancements with these additional comments:</span></span>

![显示包含其他注释的 Swagger UI](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="9d76d-165">数据注释</span><span class="sxs-lookup"><span data-stu-id="9d76d-165">Data annotations</span></span>

<span data-ttu-id="9d76d-166">使用 [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 命名空间中的属性来修饰模型，以帮助驱动 Swagger UI 组件。</span><span class="sxs-lookup"><span data-stu-id="9d76d-166">Decorate the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="9d76d-167">将 `[Required]` 属性添加到 `TodoItem` 类的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="9d76d-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="9d76d-168">此属性的状态更改 UI 行为并更改基础 JSON 架构：</span><span class="sxs-lookup"><span data-stu-id="9d76d-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

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

<span data-ttu-id="9d76d-169">将 `[Produces("application/json")]` 属性添加到 API 控制器。</span><span class="sxs-lookup"><span data-stu-id="9d76d-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="9d76d-170">这样做的目的是声明控制器的操作支持 application/json 的响应内容类型  ：</span><span class="sxs-lookup"><span data-stu-id="9d76d-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="9d76d-171">“响应内容类型”  下拉列表选此内容类型作为控制器的默认 GET 操作：</span><span class="sxs-lookup"><span data-stu-id="9d76d-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![包含默认响应内容类型的 Swagger UI](sample_images/json-response-content-type.png)

<span data-ttu-id="9d76d-173">随着 Web API 中的数据注释的使用越来越多，UI 和 API 帮助页变得更具说明性和更为有用。</span><span class="sxs-lookup"><span data-stu-id="9d76d-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="9d76d-174">描述响应类型</span><span class="sxs-lookup"><span data-stu-id="9d76d-174">Describe response types</span></span>

<span data-ttu-id="9d76d-175">使用 Web API 的开发人员最关心的问题是返回的内容，特别是响应类型和错误代码（如果不标准）。</span><span class="sxs-lookup"><span data-stu-id="9d76d-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="9d76d-176">在 XML 注释和数据注释中表示响应类型和错误代码。</span><span class="sxs-lookup"><span data-stu-id="9d76d-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="9d76d-177">`Create` 操作成功后返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="9d76d-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="9d76d-178">发布的请求正文为 NULL 时，将返回 HTTP 400 状态代码。</span><span class="sxs-lookup"><span data-stu-id="9d76d-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="9d76d-179">如果 Swagger UI 中没有提供合适的文档，那么使用者会缺少对这些预期结果的了解。</span><span class="sxs-lookup"><span data-stu-id="9d76d-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="9d76d-180">在以下示例中，通过添加突出显示的行解决此问题：</span><span class="sxs-lookup"><span data-stu-id="9d76d-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(201)]
[ProducesResponseType(400)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="9d76d-181">Swagger UI 现在清楚地记录预期的 HTTP 响应代码：</span><span class="sxs-lookup"><span data-stu-id="9d76d-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Swagger UI 针对“响应消息”下的状态代码和原因显示 POST 响应类描述“返回新建的待办事项”和“400 - 如果该项为 null”](sample_images/data-annotations-response-types.png)

<span data-ttu-id="9d76d-183">在 ASP.NET Core 2.2 或更高版本中，约定可以用作使用 `[ProducesResponseType]` 显式修饰各操作的替代方法。</span><span class="sxs-lookup"><span data-stu-id="9d76d-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="9d76d-184">有关详细信息，请参阅[使用 Web API 约定](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions)。</span><span class="sxs-lookup"><span data-stu-id="9d76d-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="9d76d-185">有关自定义 UI 的信息，请参阅：[自定义 UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span><span class="sxs-lookup"><span data-stu-id="9d76d-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
