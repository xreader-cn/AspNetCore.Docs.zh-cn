---
title: ASP.NET Core 中的密钥管理
author: rick-anderson
description: 了解 ASP.NET Core 的数据保护密钥管理 Api 的实现细节。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: c81e328d8774bfbd1309f854715fcda2152f9eeb
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021206"
---
# <a name="key-management-in-aspnet-core"></a><span data-ttu-id="959d6-103">ASP.NET Core 中的密钥管理</span><span class="sxs-lookup"><span data-stu-id="959d6-103">Key management in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-management"></a>

<span data-ttu-id="959d6-104">数据保护系统自动管理用于保护和取消保护负载的主密钥的生存期。</span><span class="sxs-lookup"><span data-stu-id="959d6-104">The data protection system automatically manages the lifetime of master keys used to protect and unprotect payloads.</span></span> <span data-ttu-id="959d6-105">每个密钥可以有以下四个阶段之一：</span><span class="sxs-lookup"><span data-stu-id="959d6-105">Each key can exist in one of four stages:</span></span>

* <span data-ttu-id="959d6-106">已创建-密钥在密钥环中存在，但尚未激活。</span><span class="sxs-lookup"><span data-stu-id="959d6-106">Created - the key exists in the key ring but has not yet been activated.</span></span> <span data-ttu-id="959d6-107">在经过足够的时间之后，密钥有机会传播到使用此密钥环的所有计算机之前，不应将该密钥用于新的保护操作。</span><span class="sxs-lookup"><span data-stu-id="959d6-107">The key shouldn't be used for new Protect operations until sufficient time has elapsed that the key has had a chance to propagate to all machines that are consuming this key ring.</span></span>

* <span data-ttu-id="959d6-108">活动-密钥在密钥环中存在，并且应该用于所有新的保护操作。</span><span class="sxs-lookup"><span data-stu-id="959d6-108">Active - the key exists in the key ring and should be used for all new Protect operations.</span></span>

* <span data-ttu-id="959d6-109">已过期-密钥已运行其自然生存期，不应再用于新的保护操作。</span><span class="sxs-lookup"><span data-stu-id="959d6-109">Expired - the key has run its natural lifetime and should no longer be used for new Protect operations.</span></span>

* <span data-ttu-id="959d6-110">已吊销-密钥已泄露，不能用于新的保护操作。</span><span class="sxs-lookup"><span data-stu-id="959d6-110">Revoked - the key is compromised and must not be used for new Protect operations.</span></span>

<span data-ttu-id="959d6-111">已创建、活动和过期的密钥均可用于取消保护传入有效负载。</span><span class="sxs-lookup"><span data-stu-id="959d6-111">Created, active, and expired keys may all be used to unprotect incoming payloads.</span></span> <span data-ttu-id="959d6-112">默认情况下，吊销的密钥不能用于取消保护有效负载，但应用程序开发人员可以在必要时[重写此行为](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect)。</span><span class="sxs-lookup"><span data-stu-id="959d6-112">Revoked keys by default may not be used to unprotect payloads, but the application developer can [override this behavior](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) if necessary.</span></span>

>[!WARNING]
> <span data-ttu-id="959d6-113">开发人员可能会尝试从密钥环中删除密钥 (例如，通过从文件系统) 中删除相应的文件。</span><span class="sxs-lookup"><span data-stu-id="959d6-113">The developer might be tempted to delete a key from the key ring (e.g., by deleting the corresponding file from the file system).</span></span> <span data-ttu-id="959d6-114">此时，由该密钥保护的所有数据都将永久 undecipherable，而且不会有任何紧急替代，就像吊销密钥一样。</span><span class="sxs-lookup"><span data-stu-id="959d6-114">At that point, all data protected by the key is permanently undecipherable, and there's no emergency override like there's with revoked keys.</span></span> <span data-ttu-id="959d6-115">删除密钥是真正的破坏性行为，因此数据保护系统不会公开用于执行此操作的第一类 API。</span><span class="sxs-lookup"><span data-stu-id="959d6-115">Deleting a key is truly destructive behavior, and consequently the data protection system exposes no first-class API for performing this operation.</span></span>

## <a name="default-key-selection"></a><span data-ttu-id="959d6-116">默认键选择</span><span class="sxs-lookup"><span data-stu-id="959d6-116">Default key selection</span></span>

<span data-ttu-id="959d6-117">当数据保护系统从后备存储库读取密钥环时，它将尝试从密钥环中查找 "默认" 密钥。</span><span class="sxs-lookup"><span data-stu-id="959d6-117">When the data protection system reads the key ring from the backing repository, it will attempt to locate a "default" key from the key ring.</span></span> <span data-ttu-id="959d6-118">默认密钥用于新的保护操作。</span><span class="sxs-lookup"><span data-stu-id="959d6-118">The default key is used for new Protect operations.</span></span>

<span data-ttu-id="959d6-119">一般试探法是数据保护系统选择最新激活日期为默认密钥的密钥。</span><span class="sxs-lookup"><span data-stu-id="959d6-119">The general heuristic is that the data protection system chooses the key with the most recent activation date as the default key.</span></span> <span data-ttu-id="959d6-120"> (有一个较小的奶油因素，以允许服务器到服务器的时钟歪斜。 ) 如果密钥已过期或已被吊销，并且如果应用程序未禁用自动密钥生成，则会生成一个新密钥，并按以下[密钥过期和滚动](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration)策略立即激活。</span><span class="sxs-lookup"><span data-stu-id="959d6-120">(There's a small fudge factor to allow for server-to-server clock skew.) If the key is expired or revoked, and if the application has not disabled automatic key generation, then a new key will be generated with immediate activation per the [key expiration and rolling](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) policy below.</span></span>

<span data-ttu-id="959d6-121">数据保护系统立即生成新密钥，而不是回退到不同的密钥，这是因为新密钥生成应被视为在新密钥之前激活的所有密钥的隐式过期。</span><span class="sxs-lookup"><span data-stu-id="959d6-121">The reason the data protection system generates a new key immediately rather than falling back to a different key is that new key generation should be treated as an implicit expiration of all keys that were activated prior to the new key.</span></span> <span data-ttu-id="959d6-122">一般情况下，可能已使用不同于旧密钥的算法或静态加密机制配置了新密钥，并且系统应更倾向于回退当前配置。</span><span class="sxs-lookup"><span data-stu-id="959d6-122">The general idea is that new keys may have been configured with different algorithms or encryption-at-rest mechanisms than old keys, and the system should prefer the current configuration over falling back.</span></span>

<span data-ttu-id="959d6-123">出现异常。</span><span class="sxs-lookup"><span data-stu-id="959d6-123">There's an exception.</span></span> <span data-ttu-id="959d6-124">如果应用程序开发人员[禁用了自动密钥生成](xref:security/data-protection/configuration/overview#disableautomatickeygeneration)，则数据保护系统必须选择某些项作为默认密钥。</span><span class="sxs-lookup"><span data-stu-id="959d6-124">If the application developer has [disabled automatic key generation](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), then the data protection system must choose something as the default key.</span></span> <span data-ttu-id="959d6-125">在此回退方案中，系统会选择具有最新激活日期的非吊销密钥，并为有时间传播到群集中的其他计算机的密钥提供首选项。</span><span class="sxs-lookup"><span data-stu-id="959d6-125">In this fallback scenario, the system will choose the non-revoked key with the most recent activation date, with preference given to keys that have had time to propagate to other machines in the cluster.</span></span> <span data-ttu-id="959d6-126">回退系统可能最终会选择过期的默认密钥。</span><span class="sxs-lookup"><span data-stu-id="959d6-126">The fallback system may end up choosing an expired default key as a result.</span></span> <span data-ttu-id="959d6-127">回退系统永远不会选择吊销的密钥作为默认密钥，如果密钥环为空或每个密钥已被吊销，则系统会在初始化时产生错误。</span><span class="sxs-lookup"><span data-stu-id="959d6-127">The fallback system will never choose a revoked key as the default key, and if the key ring is empty or every key has been revoked then the system will produce an error upon initialization.</span></span>

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a><span data-ttu-id="959d6-128">密钥过期和滚动</span><span class="sxs-lookup"><span data-stu-id="959d6-128">Key expiration and rolling</span></span>

<span data-ttu-id="959d6-129">创建密钥后，会自动向其提供一个 {now + 2 days} 的激活日期和过期日期 {now + 90 天}。</span><span class="sxs-lookup"><span data-stu-id="959d6-129">When a key is created, it's automatically given an activation date of { now + 2 days } and an expiration date of { now + 90 days }.</span></span> <span data-ttu-id="959d6-130">激活前的2天延迟提供通过系统传播的关键时间。</span><span class="sxs-lookup"><span data-stu-id="959d6-130">The 2-day delay before activation gives the key time to propagate through the system.</span></span> <span data-ttu-id="959d6-131">也就是说，它允许指向后备存储的其他应用程序在其下一次自动刷新期间观察密钥，从而最大限度地提高密钥环变为活动状态的所有应用程序的可能性。</span><span class="sxs-lookup"><span data-stu-id="959d6-131">That is, it allows other applications pointing at the backing store to observe the key at their next auto-refresh period, thus maximizing the chances that when the key ring does become active it has propagated to all applications that might need to use it.</span></span>

<span data-ttu-id="959d6-132">如果默认密钥将在2天内过期，且密钥环还没有在默认密钥过期后处于活动状态的密钥，则数据保护系统会自动将新密钥保存到密钥环。</span><span class="sxs-lookup"><span data-stu-id="959d6-132">If the default key will expire within 2 days and if the key ring doesn't already have a key that will be active upon expiration of the default key, then the data protection system will automatically persist a new key to the key ring.</span></span> <span data-ttu-id="959d6-133">此新密钥的激活日期为 {默认密钥的过期日期}，到期日期为 {now + 90 天}。</span><span class="sxs-lookup"><span data-stu-id="959d6-133">This new key has an activation date of { default key's expiration date } and an expiration date of { now + 90 days }.</span></span> <span data-ttu-id="959d6-134">这使系统能够定期自动滚动更新密钥，而不会中断服务。</span><span class="sxs-lookup"><span data-stu-id="959d6-134">This allows the system to automatically roll keys on a regular basis with no interruption of service.</span></span>

<span data-ttu-id="959d6-135">在某些情况下，将使用立即激活创建密钥。</span><span class="sxs-lookup"><span data-stu-id="959d6-135">There might be circumstances where a key will be created with immediate activation.</span></span> <span data-ttu-id="959d6-136">例如，当应用程序未运行一段时间并且密钥环中的所有密钥都已过期时。</span><span class="sxs-lookup"><span data-stu-id="959d6-136">One example would be when the application hasn't run for a time and all keys in the key ring are expired.</span></span> <span data-ttu-id="959d6-137">发生这种情况时，会为该密钥提供一个 {now} 的激活日期，而不会出现常规的2天激活延迟。</span><span class="sxs-lookup"><span data-stu-id="959d6-137">When this happens, the key is given an activation date of { now } without the normal 2-day activation delay.</span></span>

<span data-ttu-id="959d6-138">默认密钥生存期为90天，但可以配置这种生存期，如以下示例中所示。</span><span class="sxs-lookup"><span data-stu-id="959d6-138">The default key lifetime is 90 days, though this is configurable as in the following example.</span></span>

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

<span data-ttu-id="959d6-139">管理员还可以更改系统范围内的默认值，尽管对的显式调用 `SetDefaultKeyLifetime` 将覆盖任何系统范围内的策略。</span><span class="sxs-lookup"><span data-stu-id="959d6-139">An administrator can also change the default system-wide, though an explicit call to `SetDefaultKeyLifetime` will override any system-wide policy.</span></span> <span data-ttu-id="959d6-140">默认密钥生存期不能短于7天。</span><span class="sxs-lookup"><span data-stu-id="959d6-140">The default key lifetime cannot be shorter than 7 days.</span></span>

## <a name="automatic-key-ring-refresh"></a><span data-ttu-id="959d6-141">自动密钥环刷新</span><span class="sxs-lookup"><span data-stu-id="959d6-141">Automatic key ring refresh</span></span>

<span data-ttu-id="959d6-142">当数据保护系统初始化时，它将从底层存储库读取密钥环，并将其缓存在内存中。</span><span class="sxs-lookup"><span data-stu-id="959d6-142">When the data protection system initializes, it reads the key ring from the underlying repository and caches it in memory.</span></span> <span data-ttu-id="959d6-143">此缓存允许在不命中后备存储的情况下继续保护和取消保护操作。</span><span class="sxs-lookup"><span data-stu-id="959d6-143">This cache allows Protect and Unprotect operations to proceed without hitting the backing store.</span></span> <span data-ttu-id="959d6-144">系统将每隔24小时自动检查一次后备存储的更改或当前的默认密钥过期时，以先达到的时间为准。</span><span class="sxs-lookup"><span data-stu-id="959d6-144">The system will automatically check the backing store for changes approximately every 24 hours or when the current default key expires, whichever comes first.</span></span>

>[!WARNING]
> <span data-ttu-id="959d6-145">如果) 需要直接使用密钥管理 Api，开发人员非常少 (。</span><span class="sxs-lookup"><span data-stu-id="959d6-145">Developers should very rarely (if ever) need to use the key management APIs directly.</span></span> <span data-ttu-id="959d6-146">数据保护系统将执行上述自动密钥管理。</span><span class="sxs-lookup"><span data-stu-id="959d6-146">The data protection system will perform automatic key management as described above.</span></span>

<span data-ttu-id="959d6-147">数据保护系统公开了一个接口 `IKeyManager` ，该接口可用于检查和更改密钥环。</span><span class="sxs-lookup"><span data-stu-id="959d6-147">The data protection system exposes an interface `IKeyManager` that can be used to inspect and make changes to the key ring.</span></span> <span data-ttu-id="959d6-148">提供实例的 DI 系统 `IDataProtectionProvider` 还可以 `IKeyManager` 为你的消耗提供的实例。</span><span class="sxs-lookup"><span data-stu-id="959d6-148">The DI system that provided the instance of `IDataProtectionProvider` can also provide an instance of `IKeyManager` for your consumption.</span></span> <span data-ttu-id="959d6-149">或者，你可以 `IKeyManager` 直接从中拉取， `IServiceProvider` 如以下示例中所示。</span><span class="sxs-lookup"><span data-stu-id="959d6-149">Alternatively, you can pull the `IKeyManager` straight from the `IServiceProvider` as in the example below.</span></span>

<span data-ttu-id="959d6-150">修改密钥环 (显式创建新密钥或执行吊销) 的任何操作都会使内存中缓存失效。</span><span class="sxs-lookup"><span data-stu-id="959d6-150">Any operation which modifies the key ring (creating a new key explicitly or performing a revocation) will invalidate the in-memory cache.</span></span> <span data-ttu-id="959d6-151">对或的下一个调用 `Protect` `Unprotect` 将导致数据保护系统重新读取键环并重新创建缓存。</span><span class="sxs-lookup"><span data-stu-id="959d6-151">The next call to `Protect` or `Unprotect` will cause the data protection system to reread the key ring and recreate the cache.</span></span>

<span data-ttu-id="959d6-152">下面的示例演示如何使用 `IKeyManager` 接口来检查和操作密钥环，包括吊销现有密钥并手动生成新密钥。</span><span class="sxs-lookup"><span data-stu-id="959d6-152">The sample below demonstrates using the `IKeyManager` interface to inspect and manipulate the key ring, including revoking existing keys and generating a new key manually.</span></span>

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a><span data-ttu-id="959d6-153">密钥存储</span><span class="sxs-lookup"><span data-stu-id="959d6-153">Key storage</span></span>

<span data-ttu-id="959d6-154">数据保护系统具有试探法，它尝试自动推导适当的密钥存储位置和静态加密机制。</span><span class="sxs-lookup"><span data-stu-id="959d6-154">The data protection system has a heuristic whereby it attempts to deduce an appropriate key storage location and encryption-at-rest mechanism automatically.</span></span> <span data-ttu-id="959d6-155">应用程序开发人员也可以配置密钥持久性机制。</span><span class="sxs-lookup"><span data-stu-id="959d6-155">The key persistence mechanism is also configurable by the app developer.</span></span> <span data-ttu-id="959d6-156">以下文档介绍了这些机制的内置实现：</span><span class="sxs-lookup"><span data-stu-id="959d6-156">The following documents discuss the in-box implementations of these mechanisms:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
