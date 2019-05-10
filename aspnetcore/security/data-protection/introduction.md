---
title: ASP.NET Core 数据保护
author: rick-anderson
description: 了解有关数据保护的概念和 ASP.NET Core 数据保护 Api 的设计原则。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/introduction
ms.openlocfilehash: 37f170a3e8a46ef2215b0999358d46dd402636df
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897984"
---
# <a name="aspnet-core-data-protection"></a>ASP.NET Core 数据保护

Web 应用程序通常需要将安全敏感数据存储。 Windows 桌面应用程序提供 DPAPI，但这并不适合 web 应用程序。 ASP.NET Core 数据保护堆栈提供一个简单、 易于使用的加密 API，开发人员可以使用来保护数据，包括密钥管理和旋转。

ASP.NET Core 数据保护堆栈旨在用作的长期替代&lt;machineKey&gt;在 ASP.NET 中的元素 1.x-4.x。 它旨在解决许多旧加密堆栈的不足之处同时为大多数现代应用程序可能会遇到的使用情况下提供开箱解决方案。

## <a name="problem-statement"></a>问题陈述

总体问题语句可以用于简单地在单个句子中所述：我需要保留以供将来检索受信任的信息，但我不信任持久性机制。 在 web 术语中，这可能会根据写入"我需要为往返通过不受信任的客户端的受信任状态。"

此规范的示例是身份验证 cookie 或持有者令牌。 服务器会生成"我是 Groot，具有 xyz 的权限"令牌并将它传递到客户端。 在将来某天的客户端将显示该令牌发送回服务器，但该服务器需要某种类型的客户端尚未伪造令牌的保障。 因此第一个要求： 真实性 （也称为 完整性、 防篡改攻击）。

持久化的状态由服务器信任的因为我们预计这种状态可能包含特定于操作环境的信息。 这可能是在文件路径、 权限、 句柄或其他间接引用的窗体或其他一些特定于服务器的数据。 此类信息通常不应公开到不受信任的客户端。 因此第二个要求： 保密性。

最后，现代应用程序的组件化，因为我们已了解是各个组件将想要充分利用此系统而不考虑其他组件的系统中。 例如，如果持有者令牌组件使用的此堆栈，它应运行不受干扰地从一种反 CSRF 机制，也可能使用相同的堆栈。 因此最后一项要求： 隔离。

我们可以进一步约束以缩小我们的要求的范围。 我们假定加密系统内的所有服务都是同等信任和数据无需生成或在我们的直接控制服务外部使用。 此外，我们需要的操作是尽可能快，因为 web 服务的每个请求可能会经过加密系统的一个或多个时间。 这使得对称加密非常适合用于我们的方案，和我们可以折扣此类具有所需的时间之前的非对称加密。

## <a name="design-philosophy"></a>设计理念

我们首先使用现有的堆栈识别问题。 一旦我们有的我们的调查了现有解决方案布局而得出的结论是，没有现有的解决方案没有我们查找的功能。 然后，我们设计基于多个指导原则的解决方案。

* 系统应提供简单的配置。 理想情况下系统将为零配置，并且开发人员可以快速开始使用。 在开发人员需要配置一个特定方面 （如密钥存储库） 的情况下，应考虑到使这些特定的配置更简单。

* 提供一个简单的面向消费者的 API。 Api 应易于正确使用且难以使用不正确。

* 开发人员不应了解密钥管理原则。 系统应处理算法选择和开发人员的代表的密钥生存期。 理想情况下，开发人员甚至不应具有访问原始密钥材料。

* 应尽可能的静态保护密钥。 系统应找出相应的默认保护机制，并将其自动应用。

记住这些原则与我们开发了一个简单[易于使用](xref:security/data-protection/using-data-protection)数据保护堆栈。

ASP.NET Core 数据保护 Api 主要不用于机密负载的无限期持久性。 等其他技术[Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx)并[Azure Rights Management](/rights-management/)更适合的无限期存储方案，并且必须相应地强密钥管理功能。 也就是说，无需进行任何开发人员禁止使用 ASP.NET Core 数据保护 Api 进行长期保护的机密数据。

## <a name="audience"></a>读者

数据保护系统分为五个主要的软件包。 这些 Api 的各个方面定位三个主要受众;

1. [使用者 Api 概述](xref:security/data-protection/consumer-apis/overview)目标应用程序和框架开发人员。

   "我不想要了解有关在堆栈的运行方式或配置方式。 我只是想要执行某项操作中的最简单的方式尽可能高概率为成功使用 Api。"

2. [配置 Api](xref:security/data-protection/configuration/overview)目标应用程序开发人员和系统管理员。

   "我需要告诉我的环境需要非默认路径或设置数据保护系统。"

3. 扩展性 Api 目标，开发人员负责实现自定义策略。 这些 Api 的使用情况将限于极少数情况下并遇到安全注意开发人员。

   "我需要替换的整个系统内组件，因为我有真正独特的行为要求。 我愿意以了解不常见使用的 API 图面部分，若要生成可满足我的要求的插件。"

## <a name="package-layout"></a>包布局

数据保护堆栈包含五个包。

* [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/)包含<xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider>和<xref:Microsoft.AspNetCore.DataProtection.IDataProtector>接口，用于创建数据保护服务。 它还包含用于处理这些类型的有用的扩展方法 (例如， [IDataProtector.Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*))。 如果数据保护系统进行实例化其他位置和要使用 API，引用`Microsoft.AspNetCore.DataProtection.Abstractions`。

* [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/)包含数据保护系统，包括核心加密操作、 密钥管理、 配置和可扩展性的核心实现。 若要实例化的数据保护系统 (例如，将其添加到<xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>) 或修改或扩展其行为，引用`Microsoft.AspNetCore.DataProtection`。

* [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)包含其他一些 Api，开发人员可能会发现很有用，但这不属于核心包中。 例如，此包包含工厂方法来实例化数据保护系统，而无需依赖关系注入在文件系统上存储在一个位置的密钥 (请参阅<xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>)。 它还包含用于限制受保护负载的生存期的扩展方法 (请参阅<xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector>)。

* [Microsoft.AspNetCore.DataProtection.SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/)可以安装到现有 ASP.NET 4.x 应用重定向其`<machineKey>`操作，使用新的 ASP.NET Core 数据保护堆栈。 有关详细信息，请参阅 <xref:security/data-protection/compatibility/replacing-machinekey>。

* [Microsoft.AspNetCore.Cryptography.KeyDerivation](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/)提供 PBKDF2 密码哈希例程的实现，并可以由必须安全地处理用户密码的系统。 有关详细信息，请参阅 <xref:security/data-protection/consumer-apis/password-hashing>。

## <a name="additional-resources"></a>其他资源

<xref:host-and-deploy/web-farm>
