---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: 44cef0c1491cc609dd9b821910014ec88ecaad81
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552973"
---
::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

创建一个“Data”文件夹。

将以下 `MvcMovieContext` 类添加到“Data”文件夹：  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

前面的代码为实体集创建 `DbSet` 属性。 在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>添加数据库连接字符串

将连接字符串添加到 *appsettings.json* 文件中：

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>添加 NuGet 包和 EF 工具

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>注册数据库上下文

将以下 `using` 语句添加到 Startup.cs 顶部：

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

生成项目以检查编译器错误。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

将以下 `MvcMovieContext` 类添加到“模型”文件夹：  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

前面的代码为实体集创建 `DbSet` 属性。 在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>添加数据库连接字符串

将连接字符串添加到 *appsettings.json* 文件中：

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>添加所需的 NuGet 包

运行以下 .NET Core CLI 命令，以将 SQLite 和 CodeGeneration.Design 添加到项目中：

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

`Microsoft.VisualStudio.Web.CodeGeneration.Design` 包对基架是必需的。

<a name="reg"></a>

### <a name="register-the-database-context"></a>注册数据库上下文

将以下 `using` 语句添加到 Startup.cs 顶部：

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

生成项目以检查错误。
::: moniker-end