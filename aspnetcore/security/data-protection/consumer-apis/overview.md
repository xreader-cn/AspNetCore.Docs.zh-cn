---
title: 有关 ASP.NET Core 的使用者 Api 概述
author: rick-anderson
description: 接收各种使用者 Api ASP.NET Core 数据保护库中可用的简要概述。
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: ff9badb55813cae0aa72d3a95dc53792332f109b
ms.sourcegitcommit: 1bb3f3f1905b4e7d4ca1b314f2ce6ee5dd8be75f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66837382"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>有关 ASP.NET Core 的使用者 Api 概述

`IDataProtectionProvider`和`IDataProtector`接口是通过该使用者使用数据保护系统的基本接口。 它们位于[Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/)包。

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

提供程序接口表示的数据保护系统的根目录。 它不能直接用于保护或取消保护数据。 相反，使用者必须获得对的引用`IDataProtector`通过调用`IDataProtectionProvider.CreateProtector(purpose)`，其中，目的是描述预期使用者用例的字符串。 请参阅[目标字符串](xref:security/data-protection/consumer-apis/purpose-strings)着眼于此参数以及如何选择适当的值的更详细信息。

## <a name="idataprotector"></a>IDataProtector

保护程序接口将返回通过调用`CreateProtector`，和它的使用者可以使用来执行此界面保护和取消保护操作。

若要保护的数据片段，将数据传递到`Protect`方法。 基本接口定义的转换为 byte []-> byte [] 的方法，但没有还将字符串转换的重载 （作为扩展方法提供）-> 字符串。 提供两种方法的安全性是完全相同;开发人员应选择任何重载是最方便的其用例。 而不考虑选择，重载保护返回的值方法现在受到保护 （加密和防篡改的），并在应用程序可以将其发送到不受信任的客户端。

若要取消对以前受保护的一段数据的保护，将传递到受保护的数据`Unprotect`方法。 (有 byte []-基于和基于字符串的重载，为开发人员方便起见。)如果受保护的有效负载生成到的早期调用，则`Protect`此同一`IDataProtector`，则`Unprotect`方法将返回原始的未受保护的负载。 如果受保护的有效负载已被篡改或生成由不同`IDataProtector`，则`Unprotect`方法将引发 CryptographicException。

与不同的相同概念`IDataProtector`ties 回用途的概念。 如果两个`IDataProtector`从同一个根生成实例`IDataProtectionProvider`通过不同的用途对的调用中的字符串，但`IDataProtectionProvider.CreateProtector`，则它们被视为[不同的保护程序](xref:security/data-protection/consumer-apis/purpose-strings)，和一个将无法取消保护生成由其他有效负载。

## <a name="consuming-these-interfaces"></a>使用这些接口

有关组件的 DI 感知，预期使用情况是该组件将`IDataProtectionProvider`其构造函数中的参数和实例化组件时，DI 系统可以自动提供此服务。

> [!NOTE]
> 某些应用程序 （例如控制台应用程序或 ASP.NET 4.x 应用程序） 可能不是 DI 感知因此不能使用此处描述的机制。 有关这些方案，请参阅[非 DI 感知方案](xref:security/data-protection/configuration/non-di-scenarios)获取的实例的详细信息的文档`IDataProtection`而无需通过 DI 提供程序。

下面的示例演示了三个概念：

1. [添加数据保护系统](xref:security/data-protection/configuration/overview)到服务容器

2. 使用 DI 来接收实例`IDataProtectionProvider`，和

3. 创建`IDataProtector`从`IDataProtectionProvider`并使用它来保护和取消保护数据。

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

包 Microsoft.AspNetCore.DataProtection.Abstractions 包含扩展方法`IServiceProvider.GetDataProtector`为开发人员方便起见。 它作为单个操作封装这两个检索`IDataProtectionProvider`从服务提供商和调用`IDataProtectionProvider.CreateProtector`。 下面的示例演示其用法。

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> 实例`IDataProtectionProvider`和`IDataProtector`是线程安全的多个调用方。 它可用于的一个组件，获取对的引用后`IDataProtector`通过调用`CreateProtector`，它将该引用用于对多个调用`Protect`和`Unprotect`。 调用`Unprotect`将引发 CryptographicException，如果受保护的有效负载不能验证或中译解出来。 某些组件可能想要忽略错误期间取消保护操作;一个组件，它读取身份验证 cookie 可能处理此错误，并将请求视为如同它在所有具有任何 cookie 而无法完全是请求。 需要此行为的组件应专门捕获 CryptographicException，而不是抑制所有异常。
