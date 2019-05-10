---
title: 开始使用 ASP.NET Core 中的数据保护 Api
author: rick-anderson
description: 了解如何使用 ASP.NET Core 数据保护 Api 用于保护和取消保护的应用程序中的数据。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 25bf099a3d9edd7e6e0872725cbc3707750314e6
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087641"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>开始使用 ASP.NET Core 中的数据保护 Api

<a name="security-data-protection-getting-started"></a>

在其最简单、 保护数据包括以下步骤：

1. 从数据保护提供程序创建数据保护程序。

2. 调用`Protect`你想要保护的数据的方法。

3. 调用`Unprotect`想要将返回转换为纯文本格式的数据的方法。

大多数框架和应用模型，如 ASP.NET Core 或 SignalR 中，已配置数据保护系统，并将其添加到服务容器还通过依赖关系注入来访问。 下面的示例演示如何配置依赖关系注入的服务容器和注册数据保护堆栈、 接收通过 DI 的数据保护提供程序、 创建保护程序和数据保护，然后取消。

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

创建一个保护程序时必须提供一个或多个[目标字符串](xref:security/data-protection/consumer-apis/purpose-strings)。 一个字符串，目的提供了使用者之间的隔离。 例如，使用"green"的目的字符串创建的保护程序将无法取消保护数据的"紫色"目的提供的保护程序。

>[!TIP]
> 实例`IDataProtectionProvider`和`IDataProtector`是线程安全的多个调用方。 它可用于的一个组件，获取对的引用后`IDataProtector`通过调用`CreateProtector`，它将该引用用于对多个调用`Protect`和`Unprotect`。
>
>调用`Unprotect`将引发 CryptographicException，如果受保护的有效负载不能验证或中译解出来。 某些组件可能想要忽略错误期间取消保护操作;一个组件，它读取身份验证 cookie 可能处理此错误，并将请求视为如同它在所有具有任何 cookie 而无法完全是请求。 需要此行为的组件应专门捕获 CryptographicException，而不是抑制所有异常。
