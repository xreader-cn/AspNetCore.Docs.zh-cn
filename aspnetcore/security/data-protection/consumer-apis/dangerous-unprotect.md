---
title: 取消保护已吊销在 ASP.NET Core 中键的有效负载
author: rick-anderson
description: 了解如何取消保护数据，使用密钥，因为已吊销，在 ASP.NET Core 应用程序保护。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208026"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>取消保护已吊销在 ASP.NET Core 中键的有效负载

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

ASP.NET Core 数据保护 Api 主要不用于机密负载的无限期持久性。 等其他技术[Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx)并[Azure Rights Management](/rights-management/)更适合的无限期存储方案，并且必须相应地强密钥管理功能。 也就是说，无需进行任何开发人员禁止使用 ASP.NET Core 数据保护 Api 进行长期保护的机密数据。 永远不会删除密钥从密钥环，因此`IDataProtector.Unprotect`始终可以恢复现有的负载，只要键是可用且有效。

但是，则会产生问题而开发人员尝试取消保护数据作为保护已撤消的密钥，`IDataProtector.Unprotect`这种情况下将引发异常。 这可能是相当不错的短期或临时负载 （如身份验证令牌），如轻松可以通过在系统中，重新创建这些类型的有效负载和最坏的情形站点访问者可能需要重新登录。 但对于持久化有效负载，让`Unprotect`throw 可能导致不可接受的数据丢失。

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

若要支持允许有效负载为即使在面临吊销密钥时未受保护的方案，数据保护系统包含`IPersistedDataProtector`类型。 若要获取的实例`IPersistedDataProtector`，只需获取的实例`IDataProtector`在正常情况下，请尝试强制转换`IDataProtector`到`IPersistedDataProtector`。

> [!NOTE]
> 不是所有`IDataProtector`实例可以强制转换为`IPersistedDataProtector`。 开发人员应使用C#运算符或类似引起无效强制转换，以避免运行时异常和它们应准备好适当地处理失败的情况。

`IPersistedDataProtector` 公开了以下 API 图面：

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

此 API 采用受保护的负载 （作为字节数组），并返回未受保护的有效负载。 没有任何基于字符串的重载。 这两个输出参数如下所示。

* `requiresMigration`： 将设置为 true，如果用于保护此有效负载的密钥不再是活动的默认密钥，例如，用来保护此有效负载的关键是旧密钥滚动操作以来已执行的位置。 调用方可能想要考虑重新保护的有效负载，具体取决于其业务需求。

* `wasRevoked`： 将设置为 true，如果用于保护此有效负载的密钥已被吊销。

>[!WARNING]
> 传递时应格外小心`ignoreRevocationErrors: true`到`DangerousUnprotect`方法。 调用此方法后的，如果`wasRevoked`值为 true，然后用来保护此有效负载的密钥已被吊销，并且有效负载的真实性应将其视为可疑。 在这种情况下，仅继续操作上不受保护的有效负载有一定程度上，它是可信的例如保证单独来自安全的数据库，而不是由不受信任的 web 客户端发送。

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
