---
title: 取消保护已吊销在 ASP.NET Core 中键的有效负载
author: rick-anderson
description: 了解如何取消保护数据，使用密钥，因为已吊销，在 ASP.NET Core 应用程序保护。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897104"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="4c8cc-103">取消保护已吊销在 ASP.NET Core 中键的有效负载</span><span class="sxs-lookup"><span data-stu-id="4c8cc-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="4c8cc-104">ASP.NET Core 数据保护 Api 主要不用于机密负载的无限期持久性。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="4c8cc-105">等其他技术[Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx)并[Azure Rights Management](/rights-management/)更适合的无限期存储方案，并且必须相应地强密钥管理功能。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-105">Other technologies like [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="4c8cc-106">也就是说，无需进行任何开发人员禁止使用 ASP.NET Core 数据保护 Api 进行长期保护的机密数据。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="4c8cc-107">永远不会删除密钥从密钥环，因此`IDataProtector.Unprotect`始终可以恢复现有的负载，只要键是可用且有效。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="4c8cc-108">但是，则会产生问题而开发人员尝试取消保护数据作为保护已撤消的密钥，`IDataProtector.Unprotect`这种情况下将引发异常。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="4c8cc-109">这可能是相当不错的短期或临时负载 （如身份验证令牌），如轻松可以通过在系统中，重新创建这些类型的有效负载和最坏的情形站点访问者可能需要重新登录。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="4c8cc-110">但对于持久化有效负载，让`Unprotect`throw 可能导致不可接受的数据丢失。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="4c8cc-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="4c8cc-111">IPersistedDataProtector</span></span>

<span data-ttu-id="4c8cc-112">若要支持允许有效负载为即使在面临吊销密钥时未受保护的方案，数据保护系统包含`IPersistedDataProtector`类型。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="4c8cc-113">若要获取的实例`IPersistedDataProtector`，只需获取的实例`IDataProtector`在正常情况下，请尝试强制转换`IDataProtector`到`IPersistedDataProtector`。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="4c8cc-114">不是所有`IDataProtector`实例可以强制转换为`IPersistedDataProtector`。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="4c8cc-115">开发人员应使用C#运算符或类似引起无效强制转换，以避免运行时异常和它们应准备好适当地处理失败的情况。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="4c8cc-116">`IPersistedDataProtector` 公开了以下 API 图面：</span><span class="sxs-lookup"><span data-stu-id="4c8cc-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="4c8cc-117">此 API 采用受保护的负载 （作为字节数组），并返回未受保护的有效负载。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="4c8cc-118">没有任何基于字符串的重载。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-118">There's no string-based overload.</span></span> <span data-ttu-id="4c8cc-119">这两个输出参数如下所示。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="4c8cc-120">`requiresMigration`： 将设置为 true，如果用于保护此有效负载的密钥不再是活动的默认密钥，例如，用来保护此有效负载的关键是旧密钥滚动操作以来已执行的位置。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="4c8cc-121">调用方可能想要考虑重新保护的有效负载，具体取决于其业务需求。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="4c8cc-122">`wasRevoked`： 将设置为 true，如果用于保护此有效负载的密钥已被吊销。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="4c8cc-123">传递时应格外小心`ignoreRevocationErrors: true`到`DangerousUnprotect`方法。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="4c8cc-124">调用此方法后的，如果`wasRevoked`值为 true，然后用来保护此有效负载的密钥已被吊销，并且有效负载的真实性应将其视为可疑。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="4c8cc-125">在这种情况下，仅继续操作上不受保护的有效负载有一定程度上，它是可信的例如保证单独来自安全的数据库，而不是由不受信任的 web 客户端发送。</span><span class="sxs-lookup"><span data-stu-id="4c8cc-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
