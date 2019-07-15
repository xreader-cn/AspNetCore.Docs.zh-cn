<span data-ttu-id="65c0b-101">生成的标识数据库代码需要[Entity Framework Core 迁移](/ef/core/managing-schemas/migrations/)。</span><span class="sxs-lookup"><span data-stu-id="65c0b-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="65c0b-102">创建迁移并更新数据库。</span><span class="sxs-lookup"><span data-stu-id="65c0b-102">Create a migration and update the database.</span></span> <span data-ttu-id="65c0b-103">例如，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="65c0b-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="65c0b-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65c0b-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65c0b-105">在 Visual Studio**程序包管理器控制台**:</span><span class="sxs-lookup"><span data-stu-id="65c0b-105">In the Visual Studio **Package Manager Console**:</span></span>

```PMC
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="65c0b-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="65c0b-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="65c0b-107">"CreateIdentitySchema"name 参数为`Add-Migration`是任意的命令。</span><span class="sxs-lookup"><span data-stu-id="65c0b-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="65c0b-108">`"CreateIdentitySchema"` 介绍迁移。</span><span class="sxs-lookup"><span data-stu-id="65c0b-108">`"CreateIdentitySchema"` describes the migration.</span></span>
