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
ms.openlocfilehash: cf20722e8c8669fb17af8db032d4064ca2be2f4c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552456"
---
> [!NOTE]
> 
> **SQLite 限制**
>
> 本教程使用 Entity Framework Core 迁移功能（若可行）。 迁移会更新数据库架构，使其与数据模型中的更改相匹配。 但迁移只执行数据库引擎支持的更改类型，而 SQLite 的架构更改功能受限。 例如，支持添加列，但不支持删除列。 如果已创建迁移以删除列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。 
>
> 要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。 代码将在 `Up` 和 `Down` 方法中用于迁移，并且将涉及以下内容：
>
> * 创建新表。
> * 将旧表中的数据复制到新表中。
> * 放弃旧表。
> * 为新表重命名。
>
> 本教程不涉及编写此类型的特定于数据库的代码。 相反，每当尝试应用迁移失败时，本教程将删除并重新创建数据库。 有关更多信息，请参见以下资源：
>
> * [SQLite EF Core 数据库提供程序限制](/ef/core/providers/sqlite/limitations)
> * [自定义迁移代码](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [数据种子设定](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 语句](https://sqlite.org/lang_altertable.html)