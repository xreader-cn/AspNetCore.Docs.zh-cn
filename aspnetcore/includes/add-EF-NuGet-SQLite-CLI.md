<span data-ttu-id="d7f65-101">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="d7f65-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

<span data-ttu-id="d7f65-102">上述命令添加：</span><span class="sxs-lookup"><span data-stu-id="d7f65-102">The preceding commands add:</span></span>

* <span data-ttu-id="d7f65-103">[aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="d7f65-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="d7f65-104">适用于 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="d7f65-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="d7f65-105">EF Core SQLite 提供程序将 EF Core 包作为依赖项进行安装。</span><span class="sxs-lookup"><span data-stu-id="d7f65-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="d7f65-106">基架需要的包：`Microsoft.VisualStudio.Web.CodeGeneration.Design` 和 `Microsoft.EntityFrameworkCore.SqlServer`。</span><span class="sxs-lookup"><span data-stu-id="d7f65-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="d7f65-107">有关允许应用按环境配置其数据库上下文的多个环境配置指南，请参阅 <xref:fundamentals/environments#environment-based-startup-class-and-methods>。</span><span class="sxs-lookup"><span data-stu-id="d7f65-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]