<span data-ttu-id="0fec6-101">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="0fec6-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="0fec6-102">上述命令添加：</span><span class="sxs-lookup"><span data-stu-id="0fec6-102">The preceding commands add:</span></span>

* <span data-ttu-id="0fec6-103">[aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="0fec6-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="0fec6-104">适用于 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="0fec6-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="0fec6-105">EF Core SQLite 提供程序将 EF Core 包作为依赖项进行安装。</span><span class="sxs-lookup"><span data-stu-id="0fec6-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="0fec6-106">基架需要的包：`Microsoft.VisualStudio.Web.CodeGeneration.Design` 和 `Microsoft.EntityFrameworkCore.SqlServer`。</span><span class="sxs-lookup"><span data-stu-id="0fec6-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>
