---
title: 取消保护已吊销在 ASP.NET Core中键的有效负载
author: rick-anderson
description: 了解如何取消保护数据，使用密钥，因为已吊销，在 ASP.NET Core 应用程序保护。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654594"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="7eb52-103">取消保护已吊销在 ASP.NET Core中键的有效负载</span><span class="sxs-lookup"><span data-stu-id="7eb52-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="7eb52-104">ASP.NET Core 数据保护 Api 主要不用于机密负载的无限期持久性。</span><span class="sxs-lookup"><span data-stu-id="7eb52-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="7eb52-105">其他技术（如[WINDOWS CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx)和[Azure Rights Management](/rights-management/) ）更适用于无限存储的情况，并且具有相应的强大密钥管理功能。</span><span class="sxs-lookup"><span data-stu-id="7eb52-105">Other technologies like [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="7eb52-106">也就是说，无需进行任何开发人员禁止使用 ASP.NET Core 数据保护 Api 进行长期保护的机密数据。</span><span class="sxs-lookup"><span data-stu-id="7eb52-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="7eb52-107">密钥永远不会从密钥环中删除，因此，只要密钥可用且有效，`IDataProtector.Unprotect` 就可以始终恢复现有有效负载。</span><span class="sxs-lookup"><span data-stu-id="7eb52-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="7eb52-108">但是，当开发人员尝试取消保护已被吊销密钥保护的数据时，会出现问题，因为在这种情况下 `IDataProtector.Unprotect` 会引发异常。</span><span class="sxs-lookup"><span data-stu-id="7eb52-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="7eb52-109">这对于短期或暂时性负载（例如身份验证令牌）可能很合适，因为系统可以轻松地重新创建这种类型的负载，并且在最糟糕的情况下，站点访问者可能需要再次登录。</span><span class="sxs-lookup"><span data-stu-id="7eb52-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="7eb52-110">但对于持久化有效负载，具有 `Unprotect` 引发可能导致数据丢失不可接受。</span><span class="sxs-lookup"><span data-stu-id="7eb52-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="7eb52-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="7eb52-111">IPersistedDataProtector</span></span>

<span data-ttu-id="7eb52-112">为支持允许负载不受保护的方案（即使在面对吊销密钥的情况下），数据保护系统包含一个 `IPersistedDataProtector` 类型。</span><span class="sxs-lookup"><span data-stu-id="7eb52-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="7eb52-113">若要获取 `IPersistedDataProtector`的实例，只需以正常方式获取 `IDataProtector` 的实例，然后尝试将 `IDataProtector` 强制转换为 `IPersistedDataProtector`。</span><span class="sxs-lookup"><span data-stu-id="7eb52-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="7eb52-114">并非所有 `IDataProtector` 实例都可以转换为 `IPersistedDataProtector`。</span><span class="sxs-lookup"><span data-stu-id="7eb52-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="7eb52-115">开发人员应使用C# as 运算符或类似的方法来避免由无效强制转换导致的运行时异常，并应准备适当地处理故障情况。</span><span class="sxs-lookup"><span data-stu-id="7eb52-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="7eb52-116">`IPersistedDataProtector` 公开以下 API 图面：</span><span class="sxs-lookup"><span data-stu-id="7eb52-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="7eb52-117">此 API 获取受保护的负载（作为字节数组）并返回未受保护的负载。</span><span class="sxs-lookup"><span data-stu-id="7eb52-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="7eb52-118">没有基于字符串的重载。</span><span class="sxs-lookup"><span data-stu-id="7eb52-118">There's no string-based overload.</span></span> <span data-ttu-id="7eb52-119">这两个 out 参数如下所示。</span><span class="sxs-lookup"><span data-stu-id="7eb52-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="7eb52-120">`requiresMigration`：如果用于保护此有效负载的密钥不再是活动的默认密钥，则将设置为 true，例如，用于保护此有效负载的密钥是旧的，并已发生密钥滚动操作。</span><span class="sxs-lookup"><span data-stu-id="7eb52-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="7eb52-121">调用方可能需要考虑根据其业务需求重新保护负载。</span><span class="sxs-lookup"><span data-stu-id="7eb52-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="7eb52-122">`wasRevoked`：如果用于保护此负载的密钥已被吊销，则设置为 true。</span><span class="sxs-lookup"><span data-stu-id="7eb52-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="7eb52-123">将 `ignoreRevocationErrors: true` 传递到 `DangerousUnprotect` 方法时要格外小心。</span><span class="sxs-lookup"><span data-stu-id="7eb52-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="7eb52-124">如果在调用此方法后，`wasRevoked` 值为 true，则将吊销用于保护此负载的密钥，并且应将有效负载的真实性视为可疑。</span><span class="sxs-lookup"><span data-stu-id="7eb52-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="7eb52-125">在这种情况下，如果有一些单独的保证是可信的，例如它来自安全的数据库，而不是由不受信任的 web 客户端发送，则只能继续对未受保护的有效负载进行操作。</span><span class="sxs-lookup"><span data-stu-id="7eb52-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
