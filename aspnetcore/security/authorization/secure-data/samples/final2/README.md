---
ms.openlocfilehash: 6246247788eefc8f094ec1a7f156cb36715edb53
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210101"
---
# <a name="how-to-buildrun-secure-user-data-sample"></a><span data-ttu-id="edd9a-101">如何生成/运行安全的用户数据示例</span><span class="sxs-lookup"><span data-stu-id="edd9a-101">How to build/run Secure user data sample</span></span>

* <span data-ttu-id="edd9a-102">使用机密管理器工具设置密码：</span><span class="sxs-lookup"><span data-stu-id="edd9a-102">Set password with the Secret Manager tool:</span></span>

  `dotnet user-secrets set SeedUserPW <pw>`

* <span data-ttu-id="edd9a-103">更新数据库：</span><span class="sxs-lookup"><span data-stu-id="edd9a-103">Update the database:</span></span>

  `dotnet ef database update`

* <span data-ttu-id="edd9a-104">在项目中启用 HTTPS</span><span class="sxs-lookup"><span data-stu-id="edd9a-104">Enable HTTPS in the project</span></span>
