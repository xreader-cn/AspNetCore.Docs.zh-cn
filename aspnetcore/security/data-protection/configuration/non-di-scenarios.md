---
title: 非 DI 感知的情境中 ASP.NET Core 的数据保护
author: rick-anderson
description: 了解如何支持数据保护方案不能或不想要使用提供的依赖关系注入的服务。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 62280a9f911b003383cbe348b9b62942766a2b99
ms.sourcegitcommit: f5762967df3be8b8c868229e679301f2f7954679
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048225"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>非 DI 感知的情境中 ASP.NET Core 的数据保护

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 数据保护系统通常是[添加到服务容器](xref:security/data-protection/consumer-apis/overview)并且供通过依赖关系注入 (DI) 的从属组件。 但是，一些情况下，这不是可行的或所需，尤其是在现有应用程序导入系统。

若要支持这些方案中， [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)包提供了一个具体类型[DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider)，它提供了简单的方法来使用数据保护而不依赖于 DI。 `DataProtectionProvider`类型实现[IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider)。 构造`DataProtectionProvider`只需提供[DirectoryInfo](/dotnet/api/system.io.directoryinfo)实例，以指示应存储提供程序的加密密钥的位置，如下面的代码示例中所示：

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

默认情况下，`DataProtectionProvider`具体类型不会加密原始密钥材料之前将其保存到文件系统。 这是为了支持开发人员将指向网络共享和数据保护系统无法自动推断相应现有静态密钥加密机制的方案。

此外，`DataProtectionProvider`具体类型不会[隔离应用](xref:security/data-protection/configuration/overview#per-application-isolation)默认情况下。 使用相同密钥的目录的所有应用可以都共享负载，只要他们[目的参数](xref:security/data-protection/consumer-apis/purpose-strings)匹配。

[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)构造函数接受的可选配置回调，可用于调整系统的行为。 下面的示例演示使用显式调用还原隔离[SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)。 该示例还演示了将系统配置为自动加密使用 Windows DPAPI 持久化的密钥。 如果目录指向 UNC 共享，你可能想要在所有相关计算机间分发共享的证书并将系统配置为使用基于证书的加密和调用[ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)。

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> 实例`DataProtectionProvider`具体类型是创建开销很大。 如果应用程序将保留此类型的多个实例，并且如果他们正在使用相同的密钥存储目录，可能会降低应用程序性能。 如果使用`DataProtectionProvider`类型，我们建议在一次创建此类型以及它尽可能多地重复使用。 `DataProtectionProvider`类型和所有[IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)从它创建的实例是线程安全的多个调用方。
