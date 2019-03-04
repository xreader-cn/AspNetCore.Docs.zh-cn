<a name="dc"></a>

<span data-ttu-id="6997a-101">将以下 `MvcMovieContext` 类添加到“模型”文件夹：</span><span class="sxs-lookup"><span data-stu-id="6997a-101">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]


<span data-ttu-id="6997a-102">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="6997a-102">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="6997a-103">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="6997a-103">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="6997a-104">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="6997a-104">Add a database connection string</span></span>

<span data-ttu-id="6997a-105">将连接字符串添加到 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="6997a-105">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="6997a-106">添加所需的 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="6997a-106">Add required NuGet packages</span></span>

<span data-ttu-id="6997a-107">运行以下 .NET Core CLI 命令，以将 SQLite 和 CodeGeneration.Design 添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="6997a-107">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="6997a-108">`Microsoft.VisualStudio.Web.CodeGeneration.Design` 包对基架是必需的。</span><span class="sxs-lookup"><span data-stu-id="6997a-108">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="6997a-109">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="6997a-109">Register the database context</span></span>

<span data-ttu-id="6997a-110">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="6997a-110">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="6997a-111">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="6997a-111">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="6997a-112">生成项目以检查错误。</span><span class="sxs-lookup"><span data-stu-id="6997a-112">Build the project as a check for errors.</span></span>