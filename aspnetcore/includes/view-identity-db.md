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
ms.openlocfilehash: ff0502f68546213d76fe33f45574b5d8654b522b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552856"
---
### <a name="view-the-identity-database"></a><span data-ttu-id="da9c9-101">查看 Identity 数据库</span><span class="sxs-lookup"><span data-stu-id="da9c9-101">View the Identity database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="da9c9-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da9c9-102">Visual Studio</span></span>](#tab/visual-studio) 

* <span data-ttu-id="da9c9-103">从 " **视图** " 菜单中选择 " **SQL Server 对象资源管理器** (SSOX) "。</span><span class="sxs-lookup"><span data-stu-id="da9c9-103">From the **View** menu, select **SQL Server Object Explorer** (SSOX).</span></span>
* <span data-ttu-id="da9c9-104">导航到 **(localdb) MSSQLLocalDB (SQL Server 13)**。</span><span class="sxs-lookup"><span data-stu-id="da9c9-104">Navigate to **(localdb)MSSQLLocalDB(SQL Server 13)**.</span></span> <span data-ttu-id="da9c9-105">右键单击 " **dbo"。**  >  **查看数据** AspNetUsers：</span><span class="sxs-lookup"><span data-stu-id="da9c9-105">Right-click on **dbo.AspNetUsers** > **View Data**:</span></span>

![SQL Server 对象资源管理器中的 AspNetUsers 表上的上下文菜单](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[<span data-ttu-id="da9c9-107">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="da9c9-107">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="da9c9-108">您可以下载许多第三方工具来管理和查看 SQLite 数据库，例如 [DB Browser For sqlite](https://sqlitebrowser.org/)。</span><span class="sxs-lookup"><span data-stu-id="da9c9-108">There are many third party tools you can download to manage and view a SQLite database, for example [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

---