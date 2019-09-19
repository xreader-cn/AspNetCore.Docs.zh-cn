---
title: 教程：使用 ASP.NET Core 创建 Web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 生成 Web API。
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 1cc4fffc50978a3a958a96e1eb250cb85a8d2879
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082069"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>教程：使用 ASP.NET Core 创建 Web API

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Mike Wasson](https://github.com/mikewasson)

本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。

::: moniker range=">= aspnetcore-3.0"

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Web API 项目。
> * 添加模型类和数据库上下文。
> * 使用 CRUD 方法构建控制器。
> * 配置路由、URL 路径和返回值。
> * 使用 Postman 调用 Web API。

在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。

## <a name="overview"></a>概述

本教程将创建以下 API：

|API | 说明 | 请求正文 | 响应正文 |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | 获取所有待办事项 | 无 | 待办事项的数组|
|GET /api/TodoItems/{id} | 按 ID 获取项 | 无 | 待办事项|
|POST /api/TodoItems | 添加新项 | 待办事项 | 待办事项 |
|PUT /api/TodoItems/{id} | 更新现有项 &nbsp; | 待办事项 | 无 |
|DELETE /api/TodoItems/{id} &nbsp; &nbsp; | 删除项&nbsp; &nbsp; | 无 | 无|

下图显示了应用的设计。

![右侧的框表示客户端。 它提交请求并从应用程序接收响应，如右侧的框所示。 在应用程序框内，三个框分别代表控制器、模型和数据访问层。 请求进入应用程序的控制器，读/写操作是在控制器和数据访问层之间进行的。 模型被序列化并在响应中被返回给客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>系统必备

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a>创建 Web 项目

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从“文件”菜单中选择“新建”>“项目”    。
* 选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。
* 将项目命名为 TodoApi，然后单击“创建”   。
* 在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.0”    。 选择“API”模板，然后单击“创建”   。 请不要选择“启用 Docker 支持”   。

![VS“新建项目”对话框](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。
* 将目录 (`cd`) 更改为包含项目文件夹的文件夹。
* 运行以下命令：

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* 当对话框询问是否要将所需资产添加到项目时，选择“是”  。

  前面的命令：

  * 创建新的 web API 项目，并在 Visual Studio Code 中打开它。
  * 添加下一部分中所需的 NuGet 包。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 选择“文件”>“新建解决方案”  >  。

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* 选择“.NET Core”>“应用”>“API”>“下一步”     。

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* 在“配置新的 ASP.NET Core Web API”对话框中，将目标框架选择为*“.NET Core 3.0”    。

* 输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

在项目文件夹中打开命令终端并运行以下命令：

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   ```

---

### <a name="test-the-api"></a>测试 API

项目模板会创建 `WeatherForecast` API。 从浏览器调用 `Get` 方法以测试应用。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

按 Ctrl+F5 运行应用。 Visual Studio 启动浏览器并导航到 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是随机选择的端口号。

如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。 在接下来出现的“安全警告”  对话框中，选择“是”  。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

按 Ctrl+F5 运行应用。 在浏览器中，转到以下 URL：[https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

选择“运行”   > “开始调试”  以启动应用。 Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。 将返回 HTTP 404（未找到）错误。 将 `/WeatherForecast` 追加到 URL（将 URL 更改为 `https://localhost:<port>/WeatherForecast`）。

---

返回类似于以下项的 JSON：

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>添加模型类

模型  是一组表示应用管理的数据的类。 此应用的模型是单个 `TodoItem` 类。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在“解决方案资源管理器”  中，右键单击项目。 选择“添加”   > “新建文件夹”  。 将文件夹命名为“Models”  。

* 右键单击“Models”  文件夹，然后选择“添加”   > “类”  。 将类命名为 TodoItem，然后选择“添加”   。

* 将模板代码替换为以下代码：

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 添加名为“Models”的文件夹  。

* 使用以下代码将 `TodoItem` 类添加到 Models  文件夹：

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 右键单击项目。 选择“添加”   > “新建文件夹”  。 将文件夹命名为“Models”  。

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* 右键单击“Models”文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”      。

* 将类命名为“TodoItem”，然后单击“新建”   。

* 将模板代码替换为以下代码：

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

`Id` 属性用作关系数据库中的唯一键。

模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。

## <a name="add-a-database-context"></a>添加数据库上下文

数据库上下文是为数据模型协调 Entity Framework 功能的主类  。 此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>添加 Add Microsoft.EntityFrameworkCore.SqlServer

* 在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包”   。
* 选中“包括预发行版”复选框  。
* 选择“浏览”选项卡，然后在搜索框中输入 Microsoft.EntityFrameworkCore.SqlServer   。
* 在左窗口中选择“Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview”  。
* 选中右窗格中的“项目”复选框，然后选择“安装”   。
* 使用上述说明添加 `Microsoft.EntityFrameworkCore.InMemory` NuGet 包。

![NuGet 程序包管理器](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>添加 TodoContext 数据库上下文

* 右键单击“Models”  文件夹，然后选择“添加”   > “类”  。 将类命名为 TodoContext，然后单击“添加”   。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 将 `TodoContext` 类添加到“Models”  文件夹。

---

* 输入以下代码：

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>注册数据库上下文

在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。 该容器向控制器提供服务。

使用以下突出显示的代码更新 Startup.cs  ：

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

前面的代码：

* 删除未使用的 `using` 声明。
* 将数据库上下文添加到 DI 容器。
* 指定数据库上下文将使用内存中数据库。

## <a name="scaffold-a-controller"></a>构建控制器

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 右键单击 Controllers  文件夹。
* 选择“添加”>“新建构建项”   。
* 选择“其操作使用实体框架的 API 控制器”，然后选择“添加”   。
* 在“添加其操作使用实体框架的 API 控制器”对话框中  ：

  * 在“模型类”中选择“TodoItem (TodoAPI.Models)”   。
  * 在“数据上下文类”中选择“TodoContext (TodoAPI.Models)”   。
  * 选择“添加” 

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

运行以下命令：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

前面的命令：

* 添加构建所需的 NuGet 包。
* 安装构建引擎 (`dotnet-aspnet-codegenerator`)。
* 构建 `TodoItemsController`。

---

生成的代码：

* 定义了没有方法的 API 控制器类。
* 使用 [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性修饰类。 此属性指示控制器响应 Web API 请求。 有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。
* 使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。 数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。

## <a name="examine-the-posttodoitem-create-method"></a>检查 PostTodoItem create 方法

替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

正如 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示，前面的代码是 HTTP POST 方法。 该方法从 HTTP 请求正文获取待办事项的值。

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：

* 如果成功，则返回 HTTP 201 状态代码。 HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。
* 向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。 `Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。 有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。
* 引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。 C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。

### <a name="install-postman"></a>安装 Postman

本教程使用 Postman 测试 Web API。

* 安装 [Postman](https://www.getpostman.com/downloads/)
* 启动 Web 应用。
* 启动 Postman。
* 禁用 **SSL 证书验证**
* 在“文件”>“设置”  （“常规”*  选项卡）中，禁用“SSL 证书验证”  。
    > [!WARNING]
    > 在测试控制器之后重新启用 SSL 证书验证。

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>通过 Postman 测试 PostTodoItem

* 创建新请求。
* 将 HTTP 方法设置为 `POST`。
* 选择“正文”选项卡  。
* 选择“原始”单选按钮  。
* 将类型设置为 JSON (application/json) 
* 在请求正文中，输入待办事项的 JSON：

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* 选择“发送”  。

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>测试位置标头 URI

* 在“响应”  窗格中选择“标头”  选项卡。
* 复制“位置”  标头值：

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* 将方法设置为“GET”。
* 粘贴 URI（例如，`https://localhost:5001/api/TodoItems/1`）
* 选择“发送”  。

## <a name="examine-the-get-methods"></a>检查 GET 方法

这些方法实现两个 GET 终结点：

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

通过从浏览器或 Postman 调用两个终结点来测试应用。 例如:

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

对 `GetTodoItems` 的调用生成类似于以下项的响应：

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>使用 Postman 测试 Get

* 创建新请求。
* 将 HTTP 方法设置为“GET”  。
* 将请求 URL 设置为 `https://localhost:<port>/api/TodoItems`。 例如 `https://localhost:5001/api/TodoItems`。
* 在 Postman 中设置“两窗格视图”  。
* 选择“发送”  。

此应用使用内存中数据库。 如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。 如果未返回任何数据，将数据 [POST](#post) 到应用。

## <a name="routing-and-url-paths"></a>路由和 URL 路径

[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。 每个方法的 URL 路径构造如下所示：

* 在控制器的 `Route` 属性中以模板字符串开头：

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* 将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。 对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”  。 ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。
* 如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。 此示例不使用模板。 有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。

在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。 调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>返回值

`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。 ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。 在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。 未经处理的异常将转换为 5xx 错误。

`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。 例如，`GetTodoItem` 可以返回两个不同的状态值：

* 如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。
* 否则，此方法将返回具有 JSON 响应正文的 200。 返回 `item` 则产生 HTTP 200 响应。

## <a name="the-puttodoitem-method"></a>PutTodoItem 方法

检查 `PutTodoItem` 方法：

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。 根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。 若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。

如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。

### <a name="test-the-puttodoitem-method"></a>测试 PutTodoItem 方法

本示例使用内存数据库，每次启动应用时都必须对其进行初始化。 在进行 PUT 调用之前，数据库中必须有一个项。 调用 GET，以确保在调用 PUT 之前数据库中存在项目。

更新 ID = 1 的待办事项并将其名称设置为“feed fish”：

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

下图显示 Postman 更新：

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>DeleteTodoItem 方法

检查 `DeleteTodoItem` 方法：

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。

### <a name="test-the-deletetodoitem-method"></a>测试 DeleteTodoItem 方法

使用 Postman 删除待办事项：

* 将方法设置为 `DELETE`。
* 设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`
* 选择“Send” 

## <a name="call-the-web-api-with-javascript"></a>使用 JavaScript 调用 Web API

有关分步说明，请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Web API 项目。
> * 添加模型类和数据库上下文。
> * 添加控制器。
> * 添加 CRUD 方法。
> * 配置路由和 URL 路径。
> * 指定返回值。
> * 使用 Postman 调用 Web API。
> * 使用 JavaScript 调用 Web API。

在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。

## <a name="overview"></a>概述

本教程将创建以下 API：

|API | 说明 | 请求正文 | 响应正文 |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | 获取所有待办事项 | 无 | 待办事项的数组|
|GET /api/TodoItems/{id} | 按 ID 获取项 | 无 | 待办事项|
|POST /api/TodoItems | 添加新项 | 待办事项 | 待办事项 |
|PUT /api/TodoItems/{id} | 更新现有项 &nbsp; | 待办事项 | 无 |
|DELETE /api/TodoItems/{id} &nbsp; &nbsp; | 删除项&nbsp; &nbsp; | 无 | 无|

下图显示了应用的设计。

![右侧的框表示客户端。 它提交请求并从应用程序接收响应，如右侧的框所示。 在应用程序框内，三个框分别代表控制器、模型和数据访问层。 请求进入应用程序的控制器，读/写操作是在控制器和数据访问层之间进行的。 模型被序列化并在响应中被返回给客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>系统必备

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>创建 Web 项目

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从“文件”菜单中选择“新建”>“项目”    。
* 选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。
* 将项目命名为 TodoApi，然后单击“创建”   。
* 在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 2.2”    。 选择“API”模板，然后单击“创建”   。 请不要选择“启用 Docker 支持”   。

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。
* 将目录 (`cd`) 更改为包含项目文件夹的文件夹。
* 运行以下命令：

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。

* 当对话框询问是否要将所需资产添加到项目时，选择“是”  。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 选择“文件”>“新建解决方案”  >  。

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* 选择“.NET Core”>“应用”>“API”>“下一步”     。

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* 在“配置新的 ASP.NET Core Web API”  对话框中，接受默认的 *.NET Core 2.2  “目标框架”  。

* 输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>测试 API

项目模板会创建 `values` API。 从浏览器调用 `Get` 方法以测试应用。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

按 Ctrl+F5 运行应用。 Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。

如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。 在接下来出现的“安全警告”  对话框中，选择“是”  。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

按 Ctrl+F5 运行应用。 在浏览器中，转到以下 URL：[https://localhost:5001/api/values](https://localhost:5001/api/values)。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

选择“运行”   > “开始调试”  以启动应用。 Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。 将返回 HTTP 404（未找到）错误。 将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。

---

会返回以下 JSON：

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>添加模型类

模型  是一组表示应用管理的数据的类。 此应用的模型是单个 `TodoItem` 类。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在“解决方案资源管理器”  中，右键单击项目。 选择“添加”   > “新建文件夹”  。 将文件夹命名为“Models”  。

* 右键单击“Models”  文件夹，然后选择“添加”   > “类”  。 将类命名为 TodoItem，然后选择“添加”   。

* 将模板代码替换为以下代码：

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 添加名为“Models”的文件夹  。

* 使用以下代码将 `TodoItem` 类添加到 Models  文件夹：

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 右键单击项目。 选择“添加”   > “新建文件夹”  。 将文件夹命名为“Models”  。

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* 右键单击“Models”文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”      。

* 将类命名为“TodoItem”，然后单击“新建”   。

* 将模板代码替换为以下代码：

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

`Id` 属性用作关系数据库中的唯一键。

模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。

## <a name="add-a-database-context"></a>添加数据库上下文

数据库上下文是为数据模型协调 Entity Framework 功能的主类  。 此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 右键单击“Models”  文件夹，然后选择“添加”   > “类”  。 将类命名为 TodoContext，然后单击“添加”   。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 将 `TodoContext` 类添加到“Models”  文件夹。

---

* 将模板代码替换为以下代码：

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>注册数据库上下文

在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。 该容器向控制器提供服务。

使用以下突出显示的代码更新 Startup.cs  ：

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

前面的代码：

* 删除未使用的 `using` 声明。
* 将数据库上下文添加到 DI 容器。
* 指定数据库上下文将使用内存中数据库。

## <a name="add-a-controller"></a>添加控制器

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 右键单击 Controllers  文件夹。
* 选择“添加”>“新项”   。
* 在“添加新项”对话框中，选择“API 控制器类”模板   。
* 将类命名为 TodoController，然后选择“添加”   。

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 在“控制器”文件夹中，创建名为 `TodoController` 的类  。

---

* 将模板代码替换为以下代码：

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

前面的代码：

* 定义了没有方法的 API 控制器类。
* 使用 [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性修饰类。 此属性指示控制器响应 Web API 请求。 有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。
* 使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。 数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。
* 如果数据库为空，则将名为 `Item1` 的项添加到数据库。 此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。 如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。 因此删除可能看上去不起作用，不过实际上确实有效。

## <a name="add-get-methods"></a>添加 Get 方法

若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

这些方法实现两个 GET 终结点：

* `GET /api/todo`
* `GET /api/todo/{id}`

如果应用仍在运行，请停止它。 然后再次运行它以包括最新更改。

通过从浏览器调用两个终结点来测试应用。 例如:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

以下 HTTP 响应通过调用 `GetTodoItems` 来生成：

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>路由和 URL 路径

[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。 每个方法的 URL 路径构造如下所示：

* 在控制器的 `Route` 属性中以模板字符串开头：

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* 将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。 对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”  。 ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。
* 如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。 此示例不使用模板。 有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。

在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。 调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>返回值

`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。 ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。 在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。 未经处理的异常将转换为 5xx 错误。

`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。 例如，`GetTodoItem` 可以返回两个不同的状态值：

* 如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。
* 否则，此方法将返回具有 JSON 响应正文的 200。 返回 `item` 则产生 HTTP 200 响应。

## <a name="test-the-gettodoitems-method"></a>测试 GetTodoItems 方法

本教程使用 Postman 测试 Web API。

* 安装 [Postman](https://www.getpostman.com/downloads/)
* 启动 Web 应用。
* 启动 Postman。
* 禁用 **SSL 证书验证**

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在“文件”>“设置”（“常规”选项卡）中，禁用“SSL 证书验证”     。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 在“Postman” > “首选项”（“常规”选项卡）中，禁用“SSL 证书验证”     。 或者，选择扳手图标并选择“设置”，然后禁用“SSL 证书验证”  。

---
  
> [!WARNING]
> 在测试控制器之后重新启用 SSL 证书验证。

* 创建新请求。
  * 将 HTTP 方法设置为“GET”  。
  * 将请求 URL 设置为 `https://localhost:<port>/api/todo`。 例如 `https://localhost:5001/api/todo`。
* 在 Postman 中设置“两窗格视图”  。
* 选择“发送”  。

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>添加创建方法

在 Controllers / TodoController.cs 中添加以下 `PostTodoItem` 方法  ： 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

正如 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示，前面的代码是 HTTP POST 方法。 该方法从 HTTP 请求正文获取待办事项的值。

`CreatedAtAction` 方法：

* 如果成功，则返回 HTTP 201 状态代码。 HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。
* 将 `Location` 标头添加到响应。 `Location` 标头指定新建的待办事项的 URI。 有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。
* 引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。 C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>测试 PostTodoItem 方法

* 生成项目。
* 在 Postman 中，将 HTTP 方法设置为 `POST`。
* 选择“正文”选项卡  。
* 选择“原始”单选按钮  。
* 将类型设置为 JSON (application/json) 
* 在请求正文中，输入待办事项的 JSON：

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* 选择“发送”  。

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。

### <a name="test-the-location-header-uri"></a>测试位置标头 URI

* 在“响应”  窗格中选择“标头”  选项卡。
* 复制“位置”  标头值：

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* 将方法设置为“GET”。
* 粘贴 URI（例如，`https://localhost:5001/api/Todo/2`）
* 选择“发送”  。

## <a name="add-a-puttodoitem-method"></a>添加 PutTodoItem 方法

添加以下 `PutTodoItem` 方法：

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。 根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。 若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。

如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。

### <a name="test-the-puttodoitem-method"></a>测试 PutTodoItem 方法

本示例使用内存数据库，每次启动应用时都必须对其进行初始化。 在进行 PUT 调用之前，数据库中必须有一个项。 调用 GET，以确保在调用 PUT 之前数据库中存在项目。

更新 id = 1 的待办事项并将其名称设置为“feed fish”：

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

下图显示 Postman 更新：

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>添加 DeleteTodoItem 方法

添加以下 `DeleteTodoItem` 方法：

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。

### <a name="test-the-deletetodoitem-method"></a>测试 DeleteTodoItem 方法

使用 Postman 删除待办事项：

* 将方法设置为 `DELETE`。
* 设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`
* 选择“Send” 

可通过示例应用删除所有项。 但如果删除最后一项，则在下次调用 API 时，模型类构造函数会创建一个新项。

## <a name="call-the-web-api-with-javascript"></a>使用 JavaScript 调用 Web API

在本部分中，将添加一个 HTML 页面，该页面使用 JavaScript 调用 Web API。 Fetch API 可启动该请求。 JavaScript 会使用 Web API 响应的详细信息来更新页面。

通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[实现默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)  ：

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

在项目目录中创建 wwwroot  文件夹。

将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录   。 用以下标记替代其内容：

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录   。 用以下代码替代其内容：

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：

* 打开 Properties\launchSettings.json  。
* 删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用  。

此示例调用 Web API 的所有 CRUD 方法。 以下是 API 调用的说明。

### <a name="get-a-list-of-to-do-items"></a>获取待办事项的列表

Fetch 将向 Web API 发送 HTTP GET 请求，该 API 返回表示待办事项的数组的 JSON。 如果请求成功，则调用 `success` 回调函数。 在该回调中使用待办事项信息更新 DOM。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>添加待办事项

Fetch 发送 HTTP POST 请求，请求正文中包含待办事项。 将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。 待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。 当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>更新待办事项

更新待办事项与添加类似。 `url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>删除待办事项

若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a>其他资源

[查看或下载本教程的示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。 请参阅[如何下载](xref:index#how-to-download-a-sample)。

有关更多信息，请参见以下资源：

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [本教程的 YouTube 版本](https://www.youtube.com/watch?v=TTkhEyGBfAk)
