---
title: ASP.NET Core 中的数据保护 Api 入门
author: rick-anderson
description: 了解如何使用 ASP.NET Core 数据保护 Api 在应用中保护和取消保护数据。
ms.author: riande
ms.date: 11/12/2019
no-loc:
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 8c3f3c7fb21434cf335591c41741f0ce868df33e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653832"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>ASP.NET Core 中的数据保护 Api 入门

<a name="security-data-protection-getting-started"></a>

最简单的是保护数据，包括以下步骤：

1. 从数据保护提供程序创建数据保护程序。

2. 用要保护的数据调用 `Protect` 方法。

3. 用要返回到纯文本的数据调用 `Unprotect` 方法。

大多数框架和应用模型（如 ASP.NET Core 或 SignalR）已配置数据保护系统，并将其添加到通过依赖关系注入访问的服务容器。 下面的示例演示如何为依赖关系注入配置服务容器并注册数据保护堆栈，通过 DI 接收数据保护提供程序，创建保护程序并保护然后取消保护数据。

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

创建保护程序时，必须提供一个或多个[目的字符串](xref:security/data-protection/consumer-apis/purpose-strings)。 用途字符串提供使用者之间的隔离。 例如，使用 "绿色" 目的字符串创建的保护程序将无法取消保护由 "紫色" 目的的保护程序提供的数据。

>[!TIP]
> `IDataProtectionProvider` 和 `IDataProtector` 的实例对于多个调用方是线程安全的。 它的目的是，在组件通过调用 `CreateProtector`获取对 `IDataProtector` 的引用时，它会将该引用用于多次调用 `Protect` 和 `Unprotect`。
>
>如果无法验证或解密受保护的有效负载，则对 `Unprotect` 的调用将引发 System.security.cryptography.cryptographicexception。 某些组件可能希望在取消保护操作期间忽略错误;读取身份验证 cookie 的组件可能会处理此错误，并将请求视为根本没有 cookie，而不是完全失败的请求。 需要此行为的组件应专门捕获 System.security.cryptography.cryptographicexception，而不是抑制所有异常。
