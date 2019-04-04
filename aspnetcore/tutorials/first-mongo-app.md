---
title: 使用 ASP.NET Core 和 MongoDB 构建 Web API
author: prkhandelwal
description: 本教程演示如何使用 MongoDB NoSQL 数据库构建 ASP.NET Core Web API。
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 01/31/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 5b8a0c963940d65545579b7120edac3571e4ad2a
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750692"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>使用 ASP.NET Core 和 MongoDB 创建 Web API

作者 [Pratik Khandelwal](https://twitter.com/K2Prk) 和 [Scott Addie](https://twitter.com/Scott_Addie)

本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。

在本教程中，你将了解：

> [!div class="checklist"]
> * 配置 MongoDB
> * 创建 MongoDB 数据库
> * 定义 MongoDB 集合和架构
> * 从 Web API 执行 MongoDB CRUD 操作

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>系统必备

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 2.2 或更高版本](https://www.microsoft.com/net/download/all)
* 已安装“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2017 版本 15.9 或更高版本](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 2.2 或更高版本](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [用于 Visual Studio Code 的 C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [.NET Core SDK 2.2 或更高版本](https://www.microsoft.com/net/download/all)
* [Visual Studio for Mac 版本 7.7 或更高版本](https://www.visualstudio.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>配置 MongoDB

如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB 中。 将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin 添加到 `Path` 环境变量中。 通过此更改可以从开发计算机上的任意位置访问 MongoDB。

使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。 有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。

1. 选择开发计算机上用于存储数据的目录。 例如，Windows 上的 C:\\BooksData。 创建目录（如果不存在）。 mongo Shell 不会创建新目录。
1. 打开命令行界面。 运行以下命令以连接到默认端口 27017 上的 MongoDB。 请记得将 `<data_directory_path>` 替换为上一步中选择的目录。

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. 打开另一个命令行界面实例。 通过运行以下命令来连接到默认测试数据库：

    ```console
    mongo
    ```

1. 在命令行界面中运行下面的命令：

    ```console
    use BookstoreDb
    ```

    如果该命令尚不存在，则将创建名为 BookstoreDb 的数据库。 如果该数据库存在，则将为事务打开其连接。

1. 使用以下命令创建 `Books` 集合：

    ```console
    db.createCollection('Books')
    ```

    显示以下结果：

    ```console
    { "ok" : 1 }
    ```

1. 使用以下命令定义 `Books` 集合的架构并插入两个文档：

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    显示以下结果：

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. 使用以下命令查看数据库中的文档：

    ```console
    db.Books.find({}).pretty()
    ```

    显示以下结果：

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。

数据库可供使用了。 你可以开始创建 ASP.NET Core Web API。

## <a name="create-the-aspnet-core-web-api-project"></a>创建 ASP.NET Core Web API 项目

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 转到“文件” > “新建” > “项目”。
1. 选择“ASP.NET Core Web 应用程序”，将项目命名为“BooksApi”，然后单击“确定”。
1. 选择“.NET Core”目标框架和“ASP.NET Core 2.2”。 选择“API”项目模板，然后单击“确定”：
1. 访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。 在“包管理器控制台”窗口中，导航到项目根。 运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 在命令行界面中运行以下命令：

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    将在 Visual Studio Code 中生成并打开以 .NET Core 为目标的新 ASP.NET Core Web API 项目。

1. 显示“'BooksApi' 中缺少进行生成和调试所需的资产。是否添加它们?”通知时，单击“是”。
1. 访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。 打开“集成终端”并导航到项目根。 运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 转到“文件” > “新建解决方案” > “.NET Core” > “应用”。
1. 选择“ASP.NET Core Web API”C# 项目模板，然后单击“下一步”。
1. 从“目标框架”下拉列表中选择“.NET Core 2.2”，然后单击“下一步”。
1. 输入“BooksApi”作为“项目名称”，然后单击“创建”。
1. 在“解决方案”面板中，右键单击项目的“依赖项”节点，然后选择“添加包”。
1. 在搜索框中输入“MongoDB.Driver”，选择“MongoDB.Driver”包，然后单击“添加包”。
1. 在“许可证接受”对话框中单击“接受”按钮。

---

## <a name="add-a-model"></a>添加模型

1. 将 Models 目录添加到项目根。
1. 使用以下代码将 `Book` 类添加到 Models 目录：

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

在前面的类中，`Id`属性：

* 需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。
* 使用 `[BsonId]` 进行批注，以将此属性指定为文档的主键。
* 使用 `[BsonRepresentation(BsonType.ObjectId)]` 进行批注，以允许将参数作为类型 `string` 而非 `ObjectId` 传递。 Mongo 处理从 `string` 到 `ObjectId` 的转换。

类中的其他属性使用 `[BsonElement]` 属性进行批注。 该属性的值表示 MongoDB 集合中的属性名称。

## <a name="add-a-crud-operations-class"></a>添加 CRUD 操作类

1. 将 Services 目录添加到项目根。
1. 使用以下代码将 `BookService` 类添加到 Services 目录：

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. 将 MongoDB 连接字符串添加到 appsettings.json：

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    将在 `BookService` 类构造函数中访问前面的 `BookstoreDb` 属性。

1. 在 `Startup.ConfigureServices` 中，向依赖关系注入系统注册 `BookService` 类：

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    必须执行前面的服务注册才能支持消费类中的构造函数注入。

`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：

* `MongoClient` &ndash; 读取执行数据库操作的服务器实例。 此类的构造函数提供了 MongoDB 连接字符串：

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* `IMongoDatabase` &ndash; 表示用于执行操作的 Mongo 数据库。 本教程在界面上使用泛型 `GetCollection<T>(collection)` 方法来获取对特定集合中的数据的访问。 调用此方法后，可以对集合执行 CRUD 操作。 在 `GetCollection<T>(collection)` 方法调用中：
  * `collection` 表示集合名称。
  * `T` 表示存储在集合中的 CLR 对象类型。

`GetCollection<T>(collection)` 返回 `MongoCollection` 对象，该对象表示集合。 在本教程中，对集合调用以下方法：

* `Find<T>` &ndash; 返回集合中与提供的搜索条件匹配的所有文档。
* `InsertOne` &ndash; 插入提供的对象作为集合中的新文档。
* `ReplaceOne` &ndash; 将与提供的搜索条件匹配的单个文档替换为提供的对象。
* `DeleteOne` &ndash; 删除与提供的搜索条件匹配的单个文档。

## <a name="add-a-controller"></a>添加控制器

1. 使用以下代码将 `BooksController` 类添加到 Controllers 目录：

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    前面的 Web API 控制器：

    * 使用 `BookService` 类执行 CRUD 操作。
    * 包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。
    * <xref:System.Web.Http.ApiController.CreatedAtRoute*> 方法返回 201 响应，这是在服务器上创建新资源的 HTTP POST 方法的标准响应。 `CreatedAtRoute` 还会向响应添加位置标头。 位置标头指定新建的待办事项的 URI。 请参阅 [10.2.2 201 已创建](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。
1. 生成并运行应用。
1. 在浏览器中导航到 `http://localhost:<port>/api/books`。 将显示下面的 JSON 响应：

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

## <a name="next-steps"></a>后续步骤

有关构建 ASP.NET Core Web API 的详细信息，请参阅以下资源：

* <xref:web-api/index>
* <xref:web-api/action-return-types>
