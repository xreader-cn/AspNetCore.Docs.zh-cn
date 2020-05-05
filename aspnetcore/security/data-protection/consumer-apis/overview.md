---
title: ASP.NET Core 的使用者 Api 概述
author: rick-anderson
description: 获取 ASP.NET Core 数据保护库中可用的各种使用者 Api 的简要概述。
ms.author: riande
ms.date: 06/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: e840111d702882a45e59cf89d679b87fa537d833
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767852"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>ASP.NET Core 的使用者 Api 概述

`IDataProtectionProvider`和`IDataProtector`接口是使用者使用数据保护系统的基本接口。 它们位于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/)文件包中。

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

提供程序接口表示数据保护系统的根。 它不能直接用于保护或取消保护数据。 相反，使用者必须通过调用`IDataProtector` `IDataProtectionProvider.CreateProtector(purpose)`来获取对的引用，其中目的是描述预期使用者用例的字符串。 有关此参数意向的详细信息以及如何选择适当的值，请参阅[目的字符串](xref:security/data-protection/consumer-apis/purpose-strings)。

## <a name="idataprotector"></a>IDataProtector

通过调用来`CreateProtector`返回保护程序接口，该接口是使用者可用于执行保护和取消保护操作的接口。

若要保护数据片段，请将数据传递给`Protect`方法。 基本接口定义一个方法，该方法可将 byte [] 转换为 byte [] > byte []，但还有一个重载（提供作为扩展方法）来转换字符串 > 字符串。 这两种方法提供的安全性完全相同;开发人员应选择最适合其用例的任何重载。 无论选择哪种重载，protected 方法返回的值现在都受保护（到加密和篡改防），应用程序可以将其发送到不受信任的客户端。

若要取消对以前受保护的数据片段的保护，请将受`Unprotect`保护的数据传递给方法。 （出于开发人员的方便，有基于字节 [] 的和基于字符串的重载。）如果在对此相同`Protect` `IDataProtector`的之前调用生成受保护的负载，则该`Unprotect`方法将返回未受保护的原始有效负载。 如果受保护的负载已被篡改或由不同`IDataProtector`的生成，则该`Unprotect`方法将引发 system.security.cryptography.cryptographicexception。

相同和不同`IDataProtector`的概念将返回到目的概念。 如果两`IDataProtector`个实例是从同一根`IDataProtectionProvider`生成的`IDataProtectionProvider.CreateProtector`，而是通过对的调用中的不同目的字符串生成的，则它们被视为不同的[保护](xref:security/data-protection/consumer-apis/purpose-strings)程序，而一个将无法取消保护由另一个生成的负载。

## <a name="consuming-these-interfaces"></a>使用这些接口

对于支持 DI 的组件，预期的用法是组件在其构造函数中`IDataProtectionProvider`采用参数，而 DI 系统在实例化组件时自动提供此服务。

> [!NOTE]
> 某些应用程序（如控制台应用程序或 ASP.NET 4.x 应用程序）可能不能识别 DI，因此不能使用此处所述的机制。 对于这些方案，请参阅[非 DI 感知方案](xref:security/data-protection/configuration/non-di-scenarios)文档，详细了解如何获取`IDataProtection`提供程序的实例，而无需通过 DI。

下面的示例演示三个概念：

1. [将数据保护系统添加](xref:security/data-protection/configuration/overview)到服务容器中，

2. 使用 DI 接收的实例`IDataProtectionProvider`，然后

3. `IDataProtector`从创建`IDataProtectionProvider` ，并使用它来保护和取消保护数据。

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

包 AspNetCore 包含一个扩展方法`IServiceProvider.GetDataProtector` ，作为开发人员的便利。 它封装为一个操作， `IDataProtectionProvider`从服务提供程序中检索并调用。 `IDataProtectionProvider.CreateProtector` 下面的示例演示了其用法。

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> `IDataProtectionProvider`和`IDataProtector`的实例对于多个调用方是线程安全的。 它的目的是，在组件通过调用获取对的`IDataProtector`引用后`CreateProtector`，它会将该引用用于多次调用`Protect`和。 `Unprotect` 如果无法验证`Unprotect`或解密受保护的有效负载，则对的调用将引发 system.security.cryptography.cryptographicexception。 某些组件可能希望在取消保护操作期间忽略错误;读取身份验证 cookie 的组件可能会处理此错误，并将请求视为根本没有 cookie，而不是完全失败的请求。 需要此行为的组件应专门捕获 System.security.cryptography.cryptographicexception，而不是抑制所有异常。
