生成的标识数据库代码需要[Entity Framework Core 迁移](/ef/core/managing-schemas/migrations/)。 创建迁移并更新数据库。 例如，运行以下命令：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 Visual Studio**包管理器控制台**中：

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

`Add-Migration` 命令的 "CreateIdentitySchema" name 参数是任意的。 `"CreateIdentitySchema"` 介绍迁移。
