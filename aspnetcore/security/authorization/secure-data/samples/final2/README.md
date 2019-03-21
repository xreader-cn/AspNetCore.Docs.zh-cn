---
ms.openlocfilehash: 6246247788eefc8f094ec1a7f156cb36715edb53
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210101"
---
# <a name="how-to-buildrun-secure-user-data-sample"></a>如何生成/运行安全的用户数据示例

* 使用机密管理器工具设置密码：

  `dotnet user-secrets set SeedUserPW <pw>`

* 更新数据库：

  `dotnet ef database update`

* 在项目中启用 HTTPS
