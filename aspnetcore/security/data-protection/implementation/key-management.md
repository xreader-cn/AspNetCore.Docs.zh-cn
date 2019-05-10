---
title: ASP.NET Core中的密钥管理
author: rick-anderson
description: 了解管理 ASP.NET Core 数据保护 Api 的实现详细信息。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: 431bdf2d3076c83279b78f327ddb647f69e6e584
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892294"
---
# <a name="key-management-in-aspnet-core"></a><span data-ttu-id="fb656-103">ASP.NET Core 中的密钥管理</span><span class="sxs-lookup"><span data-stu-id="fb656-103">Key management in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-management"></a>

<span data-ttu-id="fb656-104">数据保护系统自动管理主密钥用于保护和取消保护负载的生存期。</span><span class="sxs-lookup"><span data-stu-id="fb656-104">The data protection system automatically manages the lifetime of master keys used to protect and unprotect payloads.</span></span> <span data-ttu-id="fb656-105">每个键可以存在于四个阶段之一：</span><span class="sxs-lookup"><span data-stu-id="fb656-105">Each key can exist in one of four stages:</span></span>

* <span data-ttu-id="fb656-106">此键创建的密钥环中存在但尚未激活。</span><span class="sxs-lookup"><span data-stu-id="fb656-106">Created - the key exists in the key ring but has not yet been activated.</span></span> <span data-ttu-id="fb656-107">密钥不应用于新的保护操作，直到有足够的时间已过该密钥没有机会将传播到所有计算机正在使用此密钥环。</span><span class="sxs-lookup"><span data-stu-id="fb656-107">The key shouldn't be used for new Protect operations until sufficient time has elapsed that the key has had a chance to propagate to all machines that are consuming this key ring.</span></span>

* <span data-ttu-id="fb656-108">活动-密钥密钥环中存在，应使用对所有新的保护操作。</span><span class="sxs-lookup"><span data-stu-id="fb656-108">Active - the key exists in the key ring and should be used for all new Protect operations.</span></span>

* <span data-ttu-id="fb656-109">过期的密钥已经运行了其自然的生存期内，不再应该用于新的保护操作。</span><span class="sxs-lookup"><span data-stu-id="fb656-109">Expired - the key has run its natural lifetime and should no longer be used for new Protect operations.</span></span>

* <span data-ttu-id="fb656-110">撤消-密钥遭到破坏，并且不能对新的保护操作。</span><span class="sxs-lookup"><span data-stu-id="fb656-110">Revoked - the key is compromised and must not be used for new Protect operations.</span></span>

<span data-ttu-id="fb656-111">创建、 活动和已过期的密钥可能都可用来取消保护传入的负载。</span><span class="sxs-lookup"><span data-stu-id="fb656-111">Created, active, and expired keys may all be used to unprotect incoming payloads.</span></span> <span data-ttu-id="fb656-112">默认情况下已吊销的密钥不可能用于取消保护有效负载，但应用程序开发人员可以[重写此行为](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect)如有必要。</span><span class="sxs-lookup"><span data-stu-id="fb656-112">Revoked keys by default may not be used to unprotect payloads, but the application developer can [override this behavior](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) if necessary.</span></span>

>[!WARNING]
> <span data-ttu-id="fb656-113">开发人员可能想要删除密钥从密钥环 （例如，通过从文件系统中删除相应的文件）。</span><span class="sxs-lookup"><span data-stu-id="fb656-113">The developer might be tempted to delete a key from the key ring (e.g., by deleting the corresponding file from the file system).</span></span> <span data-ttu-id="fb656-114">此时，密钥保护的所有数据都都将永久无法被破解，并且都没有不紧急的重写一样也使用已吊销的密钥。</span><span class="sxs-lookup"><span data-stu-id="fb656-114">At that point, all data protected by the key is permanently undecipherable, and there's no emergency override like there's with revoked keys.</span></span> <span data-ttu-id="fb656-115">正在删除密钥是真正破坏性的行为，并因此数据保护系统公开没有第一类 API 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="fb656-115">Deleting a key is truly destructive behavior, and consequently the data protection system exposes no first-class API for performing this operation.</span></span>

## <a name="default-key-selection"></a><span data-ttu-id="fb656-116">默认密钥选择</span><span class="sxs-lookup"><span data-stu-id="fb656-116">Default key selection</span></span>

<span data-ttu-id="fb656-117">当数据保护系统从后备存储库读取密钥环时，它将尝试查找从密钥环中的"默认"密钥。</span><span class="sxs-lookup"><span data-stu-id="fb656-117">When the data protection system reads the key ring from the backing repository, it will attempt to locate a "default" key from the key ring.</span></span> <span data-ttu-id="fb656-118">默认密钥用于保护的新操作。</span><span class="sxs-lookup"><span data-stu-id="fb656-118">The default key is used for new Protect operations.</span></span>

<span data-ttu-id="fb656-119">常规的启发式方法是，数据保护系统会选择最新的激活日期与默认密钥的密钥。</span><span class="sxs-lookup"><span data-stu-id="fb656-119">The general heuristic is that the data protection system chooses the key with the most recent activation date as the default key.</span></span> <span data-ttu-id="fb656-120">（没有小种附加因素，以允许服务器到服务器时钟偏差。）如果密钥已过期或吊销，并且如果应用程序禁用自动密钥生成，则将立即激活每个与生成新密钥[密钥过期和滚动](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration)下面的策略。</span><span class="sxs-lookup"><span data-stu-id="fb656-120">(There's a small fudge factor to allow for server-to-server clock skew.) If the key is expired or revoked, and if the application has not disabled automatic key generation, then a new key will be generated with immediate activation per the [key expiration and rolling](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) policy below.</span></span>

<span data-ttu-id="fb656-121">数据保护系统的原因会立即生成新密钥，而不是回退到不同的密钥是生成新密钥应视为隐式在新密钥之前激活的所有密钥的过期时间。</span><span class="sxs-lookup"><span data-stu-id="fb656-121">The reason the data protection system generates a new key immediately rather than falling back to a different key is that new key generation should be treated as an implicit expiration of all keys that were activated prior to the new key.</span></span> <span data-ttu-id="fb656-122">一般理念是新的密钥可能已使用不同的算法或静态加密机制比旧密钥配置和系统应首选而回退的当前配置。</span><span class="sxs-lookup"><span data-stu-id="fb656-122">The general idea is that new keys may have been configured with different algorithms or encryption-at-rest mechanisms than old keys, and the system should prefer the current configuration over falling back.</span></span>

<span data-ttu-id="fb656-123">没有异常。</span><span class="sxs-lookup"><span data-stu-id="fb656-123">There's an exception.</span></span> <span data-ttu-id="fb656-124">如果应用程序开发人员具有[禁用自动密钥生成](xref:security/data-protection/configuration/overview#disableautomatickeygeneration)，则数据保护系统必须选择用作默认键。</span><span class="sxs-lookup"><span data-stu-id="fb656-124">If the application developer has [disabled automatic key generation](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), then the data protection system must choose something as the default key.</span></span> <span data-ttu-id="fb656-125">在此回退方案中，系统将使用首选项提供给有时间传播到群集中的其他计算机的密钥选择的最新的激活日期，非撤消键。</span><span class="sxs-lookup"><span data-stu-id="fb656-125">In this fallback scenario, the system will choose the non-revoked key with the most recent activation date, with preference given to keys that have had time to propagate to other machines in the cluster.</span></span> <span data-ttu-id="fb656-126">回退系统最终可能因此选择的已过期的默认密钥。</span><span class="sxs-lookup"><span data-stu-id="fb656-126">The fallback system may end up choosing an expired default key as a result.</span></span> <span data-ttu-id="fb656-127">回退系统永远不会选择用作默认键已撤消的键，如果密钥环为空或已被吊销的每个键，然后系统将发出初始化时出错。</span><span class="sxs-lookup"><span data-stu-id="fb656-127">The fallback system will never choose a revoked key as the default key, and if the key ring is empty or every key has been revoked then the system will produce an error upon initialization.</span></span>

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a><span data-ttu-id="fb656-128">密钥的过期和滚动</span><span class="sxs-lookup"><span data-stu-id="fb656-128">Key expiration and rolling</span></span>

<span data-ttu-id="fb656-129">创建密钥时，它自动具有提供的激活日期为 {now + 2 天} 和 {now + 90 天} 的到期日期。</span><span class="sxs-lookup"><span data-stu-id="fb656-129">When a key is created, it's automatically given an activation date of { now + 2 days } and an expiration date of { now + 90 days }.</span></span> <span data-ttu-id="fb656-130">激活前 2 天的延迟提供关键的时间才能传遍整个系统。</span><span class="sxs-lookup"><span data-stu-id="fb656-130">The 2-day delay before activation gives the key time to propagate through the system.</span></span> <span data-ttu-id="fb656-131">也就是说，它允许在后备存储指向其他应用程序以观察该密钥在其下一步的自动刷新周期，从而最大化，密钥环会成为的活动已传播到所有应用程序，可能需要使用它的可能性。</span><span class="sxs-lookup"><span data-stu-id="fb656-131">That is, it allows other applications pointing at the backing store to observe the key at their next auto-refresh period, thus maximizing the chances that when the key ring does become active it has propagated to all applications that might need to use it.</span></span>

<span data-ttu-id="fb656-132">如果默认密钥将在 2 天内过期，并且密钥环尚不具有一个键，它将处于活动状态的默认密钥到期后，数据保护系统将自动保留密钥环的新键。</span><span class="sxs-lookup"><span data-stu-id="fb656-132">If the default key will expire within 2 days and if the key ring doesn't already have a key that will be active upon expiration of the default key, then the data protection system will automatically persist a new key to the key ring.</span></span> <span data-ttu-id="fb656-133">此新的密钥都有的激活日期为 {默认密钥到期日期} 和 {now + 90 天} 的到期日期。</span><span class="sxs-lookup"><span data-stu-id="fb656-133">This new key has an activation date of { default key's expiration date } and an expiration date of { now + 90 days }.</span></span> <span data-ttu-id="fb656-134">这允许系统以自动服务不会中断定期轮转密钥。</span><span class="sxs-lookup"><span data-stu-id="fb656-134">This allows the system to automatically roll keys on a regular basis with no interruption of service.</span></span>

<span data-ttu-id="fb656-135">可能会有情况，在其中立即激活与创建密钥。</span><span class="sxs-lookup"><span data-stu-id="fb656-135">There might be circumstances where a key will be created with immediate activation.</span></span> <span data-ttu-id="fb656-136">一个示例是当应用程序未运行的时间和过期密钥环中的所有键。</span><span class="sxs-lookup"><span data-stu-id="fb656-136">One example would be when the application hasn't run for a time and all keys in the key ring are expired.</span></span> <span data-ttu-id="fb656-137">在此情况下，该密钥有 {现在} 的不正常 2 天的激活延迟激活日期。</span><span class="sxs-lookup"><span data-stu-id="fb656-137">When this happens, the key is given an activation date of { now } without the normal 2-day activation delay.</span></span>

<span data-ttu-id="fb656-138">默认密钥生存期为 90 天，但这是可配置，如以下示例所示。</span><span class="sxs-lookup"><span data-stu-id="fb656-138">The default key lifetime is 90 days, though this is configurable as in the following example.</span></span>

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

<span data-ttu-id="fb656-139">管理员还可以更改默认系统范围内，但显式调用`SetDefaultKeyLifetime`将覆盖任何系统范围的策略。</span><span class="sxs-lookup"><span data-stu-id="fb656-139">An administrator can also change the default system-wide, though an explicit call to `SetDefaultKeyLifetime` will override any system-wide policy.</span></span> <span data-ttu-id="fb656-140">默认密钥生存期不能为短于 7 天。</span><span class="sxs-lookup"><span data-stu-id="fb656-140">The default key lifetime cannot be shorter than 7 days.</span></span>

## <a name="automatic-key-ring-refresh"></a><span data-ttu-id="fb656-141">自动密钥环刷新</span><span class="sxs-lookup"><span data-stu-id="fb656-141">Automatic key ring refresh</span></span>

<span data-ttu-id="fb656-142">数据保护系统初始化时，它从基础存储库中读取密钥环，并将其缓存在内存中。</span><span class="sxs-lookup"><span data-stu-id="fb656-142">When the data protection system initializes, it reads the key ring from the underlying repository and caches it in memory.</span></span> <span data-ttu-id="fb656-143">此缓存，保护和取消保护操作继续进行而不会达到后备存储。</span><span class="sxs-lookup"><span data-stu-id="fb656-143">This cache allows Protect and Unprotect operations to proceed without hitting the backing store.</span></span> <span data-ttu-id="fb656-144">大约每隔 24 小时或当前的默认密钥过期，具体取决于第一个，系统将自动检查更改的后备存储。</span><span class="sxs-lookup"><span data-stu-id="fb656-144">The system will automatically check the backing store for changes approximately every 24 hours or when the current default key expires, whichever comes first.</span></span>

>[!WARNING]
> <span data-ttu-id="fb656-145">开发人员应很少 （如果有） 需要直接使用密钥管理 Api。</span><span class="sxs-lookup"><span data-stu-id="fb656-145">Developers should very rarely (if ever) need to use the key management APIs directly.</span></span> <span data-ttu-id="fb656-146">数据保护系统将执行自动密钥管理，如上文所述。</span><span class="sxs-lookup"><span data-stu-id="fb656-146">The data protection system will perform automatic key management as described above.</span></span>

<span data-ttu-id="fb656-147">数据保护系统公开某接口`IKeyManager`，可以用来检查和更改密钥环。</span><span class="sxs-lookup"><span data-stu-id="fb656-147">The data protection system exposes an interface `IKeyManager` that can be used to inspect and make changes to the key ring.</span></span> <span data-ttu-id="fb656-148">提供的实例的 DI 系统`IDataProtectionProvider`还可以提供的一个实例`IKeyManager`使用费。</span><span class="sxs-lookup"><span data-stu-id="fb656-148">The DI system that provided the instance of `IDataProtectionProvider` can also provide an instance of `IKeyManager` for your consumption.</span></span> <span data-ttu-id="fb656-149">或者，你可以拉取`IKeyManager`直接从`IServiceProvider`如下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="fb656-149">Alternatively, you can pull the `IKeyManager` straight from the `IServiceProvider` as in the example below.</span></span>

<span data-ttu-id="fb656-150">这会修改密钥环 （显式创建的新项或执行吊销） 的任何操作将使内存中缓存失效。</span><span class="sxs-lookup"><span data-stu-id="fb656-150">Any operation which modifies the key ring (creating a new key explicitly or performing a revocation) will invalidate the in-memory cache.</span></span> <span data-ttu-id="fb656-151">下次调用`Protect`或`Unprotect`将导致重新读取密钥环，并重新创建缓存的数据保护系统。</span><span class="sxs-lookup"><span data-stu-id="fb656-151">The next call to `Protect` or `Unprotect` will cause the data protection system to reread the key ring and recreate the cache.</span></span>

<span data-ttu-id="fb656-152">下面的示例演示了如何使用`IKeyManager`界面检查和操作密钥环，包括撤销的现有密钥和手动生成新的密钥。</span><span class="sxs-lookup"><span data-stu-id="fb656-152">The sample below demonstrates using the `IKeyManager` interface to inspect and manipulate the key ring, including revoking existing keys and generating a new key manually.</span></span>

[!code-csharp[](key-management/samples/key-management.cs)]

## <a name="key-storage"></a><span data-ttu-id="fb656-153">密钥存储</span><span class="sxs-lookup"><span data-stu-id="fb656-153">Key storage</span></span>

<span data-ttu-id="fb656-154">数据保护系统具有启发式方法，它会尝试自动推断相应的密钥存储位置和静态加密机制。</span><span class="sxs-lookup"><span data-stu-id="fb656-154">The data protection system has a heuristic whereby it attempts to deduce an appropriate key storage location and encryption-at-rest mechanism automatically.</span></span> <span data-ttu-id="fb656-155">密钥持久性机制也是由应用开发人员可配置的。</span><span class="sxs-lookup"><span data-stu-id="fb656-155">The key persistence mechanism is also configurable by the app developer.</span></span> <span data-ttu-id="fb656-156">以下文档讨论这些机制的内置实现：</span><span class="sxs-lookup"><span data-stu-id="fb656-156">The following documents discuss the in-box implementations of these mechanisms:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
