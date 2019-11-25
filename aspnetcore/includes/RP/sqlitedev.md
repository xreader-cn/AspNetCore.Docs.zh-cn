### <a name="use-sqlite-for-development-sql-server-for-production"></a>将 SQLite 用于开发，将 SQL Server 用于生产

选择 SQLite 后，模板生成的代码便可用于开发。 下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。 注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]
