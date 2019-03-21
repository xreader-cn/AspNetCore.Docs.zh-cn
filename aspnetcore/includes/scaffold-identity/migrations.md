---
ms.openlocfilehash: 698a127120f7f52672ceb927ff24eb1b02f91521
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320271"
---
<span data-ttu-id="a87aa-101">生成的标识数据库代码需要[Entity Framework Core 迁移](/ef/core/managing-schemas/migrations/)。</span><span class="sxs-lookup"><span data-stu-id="a87aa-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="a87aa-102">创建迁移并更新数据库。</span><span class="sxs-lookup"><span data-stu-id="a87aa-102">Create a migration and update the database.</span></span> <span data-ttu-id="a87aa-103">例如，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="a87aa-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a87aa-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a87aa-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a87aa-105">在 Visual Studio**程序包管理器控制台**:</span><span class="sxs-lookup"><span data-stu-id="a87aa-105">In the Visual Studio **Package Manager Console**:</span></span>

```PMC
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a87aa-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a87aa-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="a87aa-107">"CreateIdentitySchema"name 参数为`Add-Migration`是任意的命令。</span><span class="sxs-lookup"><span data-stu-id="a87aa-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="a87aa-108">`"CreateIdentitySchema"` 介绍迁移。</span><span class="sxs-lookup"><span data-stu-id="a87aa-108">`"CreateIdentitySchema"` describes the migration.</span></span>
