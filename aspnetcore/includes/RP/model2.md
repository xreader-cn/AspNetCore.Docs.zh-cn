<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="42f24-101">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="42f24-101">Add a database context class</span></span>

<span data-ttu-id="42f24-102">将以下 `RazorPagesMovieContext` 类添加到“数据”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="42f24-102">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="42f24-103">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="42f24-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="42f24-104">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="42f24-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="42f24-105">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="42f24-105">Add a database connection string</span></span>

<span data-ttu-id="42f24-106">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示： </span><span class="sxs-lookup"><span data-stu-id="42f24-106">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="42f24-107">添加所需的 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="42f24-107">Add required NuGet packages</span></span>

<span data-ttu-id="42f24-108">运行以下 .NET Core CLI 命令，以将 SQLite 和 CodeGeneration.Design 添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="42f24-108">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design

```

<span data-ttu-id="42f24-109">`Microsoft.VisualStudio.Web.CodeGeneration.Design` 包对基架是必需的。</span><span class="sxs-lookup"><span data-stu-id="42f24-109">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="42f24-110">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="42f24-110">Register the database context</span></span>

<span data-ttu-id="42f24-111">将以下 `using` 语句添加到 Startup.cs 顶部  ：</span><span class="sxs-lookup"><span data-stu-id="42f24-111">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="42f24-112">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="42f24-112">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="42f24-113">生成项目以检查错误。</span><span class="sxs-lookup"><span data-stu-id="42f24-113">Build the project as a check for errors.</span></span>
