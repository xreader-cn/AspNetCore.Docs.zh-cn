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
# <a name="key-management-in-aspnet-core"></a>ASP.NET Core 中的密钥管理

<a name="data-protection-implementation-key-management"></a>

数据保护系统自动管理主密钥用于保护和取消保护负载的生存期。 每个键可以存在于四个阶段之一：

* 此键创建的密钥环中存在但尚未激活。 密钥不应用于新的保护操作，直到有足够的时间已过该密钥没有机会将传播到所有计算机正在使用此密钥环。

* 活动-密钥密钥环中存在，应使用对所有新的保护操作。

* 过期的密钥已经运行了其自然的生存期内，不再应该用于新的保护操作。

* 撤消-密钥遭到破坏，并且不能对新的保护操作。

创建、 活动和已过期的密钥可能都可用来取消保护传入的负载。 默认情况下已吊销的密钥不可能用于取消保护有效负载，但应用程序开发人员可以[重写此行为](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect)如有必要。

>[!WARNING]
> 开发人员可能想要删除密钥从密钥环 （例如，通过从文件系统中删除相应的文件）。 此时，密钥保护的所有数据都都将永久无法被破解，并且都没有不紧急的重写一样也使用已吊销的密钥。 正在删除密钥是真正破坏性的行为，并因此数据保护系统公开没有第一类 API 执行此操作。

## <a name="default-key-selection"></a>默认密钥选择

当数据保护系统从后备存储库读取密钥环时，它将尝试查找从密钥环中的"默认"密钥。 默认密钥用于保护的新操作。

常规的启发式方法是，数据保护系统会选择最新的激活日期与默认密钥的密钥。 （没有小种附加因素，以允许服务器到服务器时钟偏差。）如果密钥已过期或吊销，并且如果应用程序禁用自动密钥生成，则将立即激活每个与生成新密钥[密钥过期和滚动](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration)下面的策略。

数据保护系统的原因会立即生成新密钥，而不是回退到不同的密钥是生成新密钥应视为隐式在新密钥之前激活的所有密钥的过期时间。 一般理念是新的密钥可能已使用不同的算法或静态加密机制比旧密钥配置和系统应首选而回退的当前配置。

没有异常。 如果应用程序开发人员具有[禁用自动密钥生成](xref:security/data-protection/configuration/overview#disableautomatickeygeneration)，则数据保护系统必须选择用作默认键。 在此回退方案中，系统将使用首选项提供给有时间传播到群集中的其他计算机的密钥选择的最新的激活日期，非撤消键。 回退系统最终可能因此选择的已过期的默认密钥。 回退系统永远不会选择用作默认键已撤消的键，如果密钥环为空或已被吊销的每个键，然后系统将发出初始化时出错。

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>密钥的过期和滚动

创建密钥时，它自动具有提供的激活日期为 {now + 2 天} 和 {now + 90 天} 的到期日期。 激活前 2 天的延迟提供关键的时间才能传遍整个系统。 也就是说，它允许在后备存储指向其他应用程序以观察该密钥在其下一步的自动刷新周期，从而最大化，密钥环会成为的活动已传播到所有应用程序，可能需要使用它的可能性。

如果默认密钥将在 2 天内过期，并且密钥环尚不具有一个键，它将处于活动状态的默认密钥到期后，数据保护系统将自动保留密钥环的新键。 此新的密钥都有的激活日期为 {默认密钥到期日期} 和 {now + 90 天} 的到期日期。 这允许系统以自动服务不会中断定期轮转密钥。

可能会有情况，在其中立即激活与创建密钥。 一个示例是当应用程序未运行的时间和过期密钥环中的所有键。 在此情况下，该密钥有 {现在} 的不正常 2 天的激活延迟激活日期。

默认密钥生存期为 90 天，但这是可配置，如以下示例所示。

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

管理员还可以更改默认系统范围内，但显式调用`SetDefaultKeyLifetime`将覆盖任何系统范围的策略。 默认密钥生存期不能为短于 7 天。

## <a name="automatic-key-ring-refresh"></a>自动密钥环刷新

数据保护系统初始化时，它从基础存储库中读取密钥环，并将其缓存在内存中。 此缓存，保护和取消保护操作继续进行而不会达到后备存储。 大约每隔 24 小时或当前的默认密钥过期，具体取决于第一个，系统将自动检查更改的后备存储。

>[!WARNING]
> 开发人员应很少 （如果有） 需要直接使用密钥管理 Api。 数据保护系统将执行自动密钥管理，如上文所述。

数据保护系统公开某接口`IKeyManager`，可以用来检查和更改密钥环。 提供的实例的 DI 系统`IDataProtectionProvider`还可以提供的一个实例`IKeyManager`使用费。 或者，你可以拉取`IKeyManager`直接从`IServiceProvider`如下面的示例中所示。

这会修改密钥环 （显式创建的新项或执行吊销） 的任何操作将使内存中缓存失效。 下次调用`Protect`或`Unprotect`将导致重新读取密钥环，并重新创建缓存的数据保护系统。

下面的示例演示了如何使用`IKeyManager`界面检查和操作密钥环，包括撤销的现有密钥和手动生成新的密钥。

[!code-csharp[](key-management/samples/key-management.cs)]

## <a name="key-storage"></a>密钥存储

数据保护系统具有启发式方法，它会尝试自动推断相应的密钥存储位置和静态加密机制。 密钥持久性机制也是由应用开发人员可配置的。 以下文档讨论这些机制的内置实现：

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
