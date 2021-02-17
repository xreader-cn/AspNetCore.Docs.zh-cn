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
### <a name="view-the-identity-database"></a>查看 Identity 数据库

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

* 从 " **视图** " 菜单中选择 " **SQL Server 对象资源管理器** (SSOX) "。
* 导航到 **(localdb) MSSQLLocalDB (SQL Server 13)**。 右键单击 " **dbo"。**  >  **查看数据** AspNetUsers：

![SQL Server 对象资源管理器中的 AspNetUsers 表上的上下文菜单](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

您可以下载许多第三方工具来管理和查看 SQLite 数据库，例如 [DB Browser For sqlite](https://sqlitebrowser.org/)。

---