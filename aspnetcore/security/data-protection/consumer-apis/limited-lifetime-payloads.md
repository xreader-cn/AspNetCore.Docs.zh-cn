---
title: 限制在 ASP.NET Core 的受保护负载的生存期
author: rick-anderson
description: 了解如何限制使用 ASP.NET Core 数据保护 Api 的受保护负载的生存期。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 8dc3b856ec67477ec8ae777749c9bf3107eb4eda
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651438"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>限制在 ASP.NET Core 的受保护负载的生存期

在某些情况下，应用程序开发人员需要创建在设定的时间段之后过期的受保护负载。 例如，受保护的负载可能表示密码重置令牌，该令牌仅应在一小时内有效。 当然，开发人员也可以创建自己的负载格式，其中包含一个嵌入的过期日期，而高级开发人员可能希望这样做，但对于大多数管理这些过期的开发人员来说，这种情况都很繁琐。

为了使我们的开发人员受众更容易，包[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)包含用于创建在设定的时间段后自动过期的有效负载的实用工具 api。 这些 Api 挂起 `ITimeLimitedDataProtector` 类型。

## <a name="api-usage"></a>API 使用情况

`ITimeLimitedDataProtector` 接口是用于保护和取消保护时间限制/自过期负载的核心接口。 若要创建 `ITimeLimitedDataProtector`的实例，首先需要使用特定用途构造的常规[IDataProtector](xref:security/data-protection/consumer-apis/overview)的实例。 `IDataProtector` 实例可用后，调用 `IDataProtector.ToTimeLimitedDataProtector` 扩展方法以获取具有内置过期功能的保护程序。

`ITimeLimitedDataProtector` 公开以下 API surface 和 extension 方法：

* CreateProtector （string 目的）： ITimeLimitedDataProtector-此 API 类似于现有 `IDataProtectionProvider.CreateProtector`，因为它可用于从根时间限制的保护程序创建[用途链](xref:security/data-protection/consumer-apis/purpose-strings)。

* 保护（byte [] 纯文本、DateTimeOffset 过期）： byte []

* 保护（byte [] 纯文本，TimeSpan 生存期）： byte []

* 保护（byte [] 纯文本）： byte []

* 保护（字符串纯文本、DateTimeOffset 过期）：字符串

* 保护（字符串纯文本、TimeSpan 生存期）：字符串

* 保护（字符串纯文本）：字符串

除了使用纯文本的核心 `Protect` 方法之外，还有一些新的重载，可用于指定有效负载的到期日期。 可以将到期日期指定为绝对日期（通过 `DateTimeOffset`）或指定为相对时间（从当前系统时间起，通过 `TimeSpan`）。 如果调用不带过期的重载，则假定负载永不过期。

* 取消保护（byte [] protectedData，out DateTimeOffset 过期）： byte []

* Unprotect(byte[] protectedData) : byte[]

* 取消保护（string protectedData，out DateTimeOffset 过期）：字符串

* 取消保护（string protectedData）：字符串

`Unprotect` 方法返回未受保护的原始数据。 如果负载尚未过期，则将以可选的 out 参数形式返回绝对过期，并将其作为不受保护的原始数据。 如果有效负载已过期，则解除保护方法的所有重载都将引发 System.security.cryptography.cryptographicexception。

>[!WARNING]
> 不建议使用这些 Api 来保护需要长期或无限持久性的负载。 "我可以承受受保护的负载在一个月后永久无法恢复吗？" 可以作为最佳经验法则;如果答案不是，开发人员应考虑其他 Api。

下面的示例使用[非 DI 代码路径](xref:security/data-protection/configuration/non-di-scenarios)来实例化数据保护系统。 若要运行此示例，请确保首先添加了对 AspNetCore 包的引用。

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
