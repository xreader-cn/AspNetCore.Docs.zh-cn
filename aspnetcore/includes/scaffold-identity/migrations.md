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
ms.openlocfilehash: 1700adafb58cad57ea1becbf53cad45edd047962
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551790"
---
生成的 Identity 数据库代码需要 [Entity Framework Core 迁移](/ef/core/managing-schemas/migrations/)。 创建迁移并更新数据库。 例如，运行以下命令：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 Visual Studio **包管理器控制台** 中：

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

命令的 "Create Identity Schema" name 参数 `Add-Migration` 是任意的。 `"CreateIdentitySchema"` 介绍迁移。
