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
<span data-ttu-id="227a5-101">生成的 Identity 数据库代码需要 [Entity Framework Core 迁移](/ef/core/managing-schemas/migrations/)。</span><span class="sxs-lookup"><span data-stu-id="227a5-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="227a5-102">创建迁移并更新数据库。</span><span class="sxs-lookup"><span data-stu-id="227a5-102">Create a migration and update the database.</span></span> <span data-ttu-id="227a5-103">例如，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="227a5-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="227a5-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="227a5-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="227a5-105">在 Visual Studio **包管理器控制台** 中：</span><span class="sxs-lookup"><span data-stu-id="227a5-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="227a5-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="227a5-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="227a5-107">命令的 "Create Identity Schema" name 参数 `Add-Migration` 是任意的。</span><span class="sxs-lookup"><span data-stu-id="227a5-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="227a5-108">`"CreateIdentitySchema"` 介绍迁移。</span><span class="sxs-lookup"><span data-stu-id="227a5-108">`"CreateIdentitySchema"` describes the migration.</span></span>
