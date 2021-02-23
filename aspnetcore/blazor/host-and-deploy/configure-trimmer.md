---
title: 配置适用于 ASP.NET Core Blazor 的裁边器
author: guardrex
description: 了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器（裁边器）。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975205"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a>配置适用于 ASP.NET Core Blazor 的裁边器

Blazor WebAssembly 执行[中间语言 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) 剪裁以减小发布输出的大小。 默认情况下，在发布应用时进行剪裁。

剪裁可能会造成不利影响。 在使用反射的应用中，剪裁器通常无法确定在运行时反射所需的类型。 若要剪裁使用反射的应用，必须通知剪裁器应用所依赖的应用代码和包或框架中的反射所需的类型。 剪裁器也无法在运行时对应用的动态行为作出响应。 若要确保剪裁后的应用在部署后正常工作，请在开发时经常对已发布的输出进行测试。

若要配置剪裁器，请参阅 .NET 基础知识文档中的[剪裁选项](/dotnet/core/deploying/trimming-options)一文，其中包括有关以下主题的指导：

* 通过项目文件中的 `<PublishTrimmed>` 属性对整个应用禁用剪裁。
* 控制剪裁器如何放弃未充分利用的 IL。
* 阻止剪裁器剪裁特定程序集。
* 要剪裁的“根”程序集。
* 通过在项目文件中将 `<SuppressTrimAnalysisWarnings>` 属性设置为 `false` 来显示关于反射的类型的警告。
* 控制符号剪裁和调试程序支持。
* 设置剪裁器功能以剪裁框架库功能。

## <a name="additional-resources"></a>其他资源

* [裁剪自包含部署和可执行文件](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
