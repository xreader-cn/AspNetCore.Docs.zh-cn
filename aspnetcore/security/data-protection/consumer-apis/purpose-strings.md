---
title: 在 ASP.NET Core 目的字符串
author: rick-anderson
description: 了解如何在 ASP.NET Core 数据保护 Api 中使用目的字符串。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 4c85423f8de7e4b784ae1bb304a884541df251b6
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087528"
---
# <a name="purpose-strings-in-aspnet-core"></a>在 ASP.NET Core 目的字符串

<a name="data-protection-consumer-apis-purposes"></a>

使用的组件`IDataProtectionProvider`必须通过唯一*目的*参数`CreateProtector`方法。 目的*参数*是固有的数据保护系统，安全性，因为它提供了加密的使用者之间的隔离，即使根加密密钥也相同。

当使用者指定用途时，目的字符串使用根加密密钥以及到该使用者派生唯一加密子项。 这将隔离所有其他加密使用者应用程序中使用者： 没有其他组件可以读取其负载，和它无法读取任何组件的负载。 这种隔离还会呈现不可行的整个类别的针对组件的攻击。

![用途图示例](purpose-strings/_static/purposes.png)

在关系图中更高版本，`IDataProtector`实例 A 和 B**不能**读取彼此的负载，只有他们自己。

目的字符串不一定要机密。 它只应是唯一的意义上说，没有其他正常运转的组件将不断提供相同用途的字符串。

>[!TIP]
> 使用使用数据保护 Api 的命名空间和类型名称是组件的一个很好的经验，如下所示此信息将不会发生冲突的做法。
>
>Contoso 编写的组件，它负责 minting 持有者令牌可能会使用 Contoso.Security.BearerToken 作为其用途字符串。 或者-甚至更好地-它可能会使用 Contoso.Security.BearerToken.v1 作为其用途字符串。 追加版本号允许 Contoso.Security.BearerToken.v2 用作其用途的未来版本，并且不同版本之间彼此完全隔离负载到位。

目的参数以来`CreateProtector`是一个字符串数组，上述可能已改为指定作为`[ "Contoso.Security.BearerToken", "v1" ]`。 这将允许建立目的的层次结构并打开与数据保护系统的多租户方案的可能性。

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> 组件不应允许不受信任的用户输入为目的链输入的唯一来源。
>
>例如，考虑 Contoso.Messaging.SecureMessage 负责存储安全消息的组件。 如果调用安全消息传送组件`CreateProtector([ username ])`，则恶意用户可能会创建一个包含帐户用户名"Contoso.Security.BearerToken"中，尝试获取要调用的组件`CreateProtector([ "Contoso.Security.BearerToken" ])`，从而会无意中造成安全消息传送系统可能会被视为身份验证令牌的 mint 负载。
>
>将消息传送组件的更好用途链`CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`，其中提供了适当的隔离。

提供的隔离和行为`IDataProtectionProvider`， `IDataProtector`，和目的如下所示：

* 为给定`IDataProtectionProvider`对象，`CreateProtector`方法将创建`IDataProtector`对象唯一绑定到同时`IDataProtectionProvider`对象创建它，并使用目的参数传递到方法。

* 用途参数不能为 null。 （如果目的指定为一个数组，这意味着不能长度为零的数组和数组的所有元素必须都为非 null。）空字符串目的从技术上讲允许但不建议这样做。

* 两个用途自变量是等效的当且仅当它们包含相同的字符串 （使用序号比较器） 中的顺序相同。 单一用途参数等效于相应的单个元素目的数组。

* 两个`IDataProtector`对象是否等效，当且仅当它们等效项从创建`IDataProtectionProvider`具有等效目的参数的对象。

* 为给定`IDataProtector`对象，调用`Unprotect(protectedData)`将返回原始`unprotectedData`当且仅当`protectedData := Protect(unprotectedData)`等效`IDataProtector`对象。

> [!NOTE]
> 我们不考虑的如果某些组件有意选择目的字符串是一种以与另一个组件发生冲突。 此类组件实质上是认为是恶意的行为，并且此系统并不旨在提供安全保证，在的内部工作进程已运行恶意代码。
