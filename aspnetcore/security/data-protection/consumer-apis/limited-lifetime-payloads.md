---
title: 限制在 ASP.NET Core 的受保护负载的生存期
author: rick-anderson
description: 了解如何限制使用 ASP.NET Core 数据保护 Api 的受保护负载的生存期。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 8dc3b856ec67477ec8ae777749c9bf3107eb4eda
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897114"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>限制在 ASP.NET Core 的受保护负载的生存期

提供了应用程序开发人员希望创建将在一段时间后过期的受保护的负载的方案。 例如，受保护的有效负载可能表示应仅在有效一小时内的密码重置令牌。 当然可以供开发人员创建他们自己有效负载格式，它包含嵌入的到期日期，和高级开发人员可能想要执行此操作，但对于大多数开发人员管理这些过期时间可能变得枯燥乏味。

若要简化此过程对于我们的开发人员受众，该包[Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)实用工具 Api 包含用于创建在一段时间后自动过期的有效负载。 这些 Api 的挂起的中断`ITimeLimitedDataProtector`类型。

## <a name="api-usage"></a>API 使用情况

`ITimeLimitedDataProtector`接口是用于保护和取消保护的限时 / 自即将到期的有效负载的核心接口。 若要创建的实例`ITimeLimitedDataProtector`，首先需要的正则表达式实例[IDataProtector](xref:security/data-protection/consumer-apis/overview)构造有特定的用途。 一次`IDataProtector`实例是否可用，请调用`IDataProtector.ToTimeLimitedDataProtector`扩展方法，以使用内置过期功能得到保护程序。

`ITimeLimitedDataProtector` 公开了以下 API 图面和扩展方法：

* CreateProtector （字符串用途）：ITimeLimitedDataProtector-此 API 是类似于现有`IDataProtectionProvider.CreateProtector`在于它可用于创建[用途链](xref:security/data-protection/consumer-apis/purpose-strings)从根的限时保护程序。

* Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]

* Protect(byte[] plaintext, TimeSpan lifetime) : byte[]

* Protect(byte[] plaintext) : byte[]

* 保护 （字符串纯文本，DateTimeOffset 过期）： 字符串

* 保护 （纯文本字符串，时间跨度生存期）： 字符串

* 保护 （纯文本字符串）： 字符串

除了核心`Protect`这需要仅纯文本，方法有新重载允许指定有效负载的到期日期。 到期日期可以指定为绝对日期 (通过`DateTimeOffset`) 或相对时间 (从当前系统时间，通过`TimeSpan`)。 如果调用不会过期时间的重载，则假定负载永远不会为过期。

* Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]

* Unprotect(byte[] protectedData) : byte[]

* 取消保护 (DateTimeOffset 过期出字符串 protectedData): 字符串

* 取消保护 (字符串 protectedData): 字符串

`Unprotect`方法返回原始的未受保护的数据。 如果尚未超过有效负载，绝对到期则返回作为可选输出参数以及原始的未受保护数据。 如果有效负载已过期，Unprotect 方法的所有重载将都引发 CryptographicException。

>[!WARNING]
> 不建议使用这些 Api 来保护需要永久保留长期或无限期的负载。 "我能否在一个月后为永久性不可恢复的受保护负载的？" 可作为一个良好经验;如果答案是没有然后开发人员应考虑备用 Api。

下面的示例使用[非 DI 代码路径](xref:security/data-protection/configuration/non-di-scenarios)用于实例化的数据保护系统。 若要运行此示例，请确保首先添加对 Microsoft.AspNetCore.DataProtection.Extensions 包的引用。

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
