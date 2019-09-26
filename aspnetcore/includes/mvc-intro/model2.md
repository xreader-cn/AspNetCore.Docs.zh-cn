::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="ba4a9-101">创建一个“Data”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="ba4a9-101">Create a *Data* folder.</span></span>

<span data-ttu-id="ba4a9-102">将以下 `MvcMovieContext` 类添加到“数据”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="ba4a9-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="ba4a9-103">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="ba4a9-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="ba4a9-104">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="ba4a9-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="ba4a9-105">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="ba4a9-105">Add a database connection string</span></span>

<span data-ttu-id="ba4a9-106">将连接字符串添加到 appsettings.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="ba4a9-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="ba4a9-107">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="ba4a9-107">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="ba4a9-108">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="ba4a9-108">Register the database context</span></span>

<span data-ttu-id="ba4a9-109">将以下 `using` 语句添加到 Startup.cs 顶部  ：</span><span class="sxs-lookup"><span data-stu-id="ba4a9-109">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="ba4a9-110">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="ba4a9-110">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="ba4a9-111">生成项目以检查编译器错误。</span><span class="sxs-lookup"><span data-stu-id="ba4a9-111">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba4a9-112">将以下 `MvcMovieContext` 类添加到“模型”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="ba4a9-112">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="ba4a9-113">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="ba4a9-113">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="ba4a9-114">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="ba4a9-114">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="ba4a9-115">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="ba4a9-115">Add a database connection string</span></span>

<span data-ttu-id="ba4a9-116">将连接字符串添加到 appsettings.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="ba4a9-116">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="ba4a9-117">添加所需的 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="ba4a9-117">Add required NuGet packages</span></span>

<span data-ttu-id="ba4a9-118">运行以下 .NET Core CLI 命令，以将 SQLite 和 CodeGeneration.Design 添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="ba4a9-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="ba4a9-119">`Microsoft.VisualStudio.Web.CodeGeneration.Design` 包对基架是必需的。</span><span class="sxs-lookup"><span data-stu-id="ba4a9-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="ba4a9-120">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="ba4a9-120">Register the database context</span></span>

<span data-ttu-id="ba4a9-121">将以下 `using` 语句添加到 Startup.cs 顶部  ：</span><span class="sxs-lookup"><span data-stu-id="ba4a9-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="ba4a9-122">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="ba4a9-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="ba4a9-123">生成项目以检查错误。</span><span class="sxs-lookup"><span data-stu-id="ba4a9-123">Build the project as a check for errors.</span></span>
::: moniker-end