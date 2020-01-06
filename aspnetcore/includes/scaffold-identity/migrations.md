<span data-ttu-id="ba368-101">生成的标识数据库代码需要[Entity Framework Core 迁移](/ef/core/managing-schemas/migrations/)。</span><span class="sxs-lookup"><span data-stu-id="ba368-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="ba368-102">创建迁移并更新数据库。</span><span class="sxs-lookup"><span data-stu-id="ba368-102">Create a migration and update the database.</span></span> <span data-ttu-id="ba368-103">例如，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ba368-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ba368-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba368-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ba368-105">在 Visual Studio**程序包管理器控制台**:</span><span class="sxs-lookup"><span data-stu-id="ba368-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ba368-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ba368-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="ba368-107">`Add-Migration` 命令的 "CreateIdentitySchema" name 参数是任意的。</span><span class="sxs-lookup"><span data-stu-id="ba368-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="ba368-108">`"CreateIdentitySchema"` 介绍迁移。</span><span class="sxs-lookup"><span data-stu-id="ba368-108">`"CreateIdentitySchema"` describes the migration.</span></span>
