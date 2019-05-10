---
title: SignalR API 设计时的注意事项
author: anurse
description: 了解如何在您的应用程序版本之间的兼容性设计 SignalR Api。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/api-design
ms.openlocfilehash: 3f17bf055b793e8fc91fbcc15f668928ca261f77
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897804"
---
# <a name="signalr-api-design-considerations"></a>SignalR API 设计时的注意事项

通过[Andrew Stanton-nurse](https://twitter.com/anurse)

本文提供了用于构建基于 SignalR 的 Api 的指南。

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>使用自定义对象参数来确保向后兼容性

将参数添加到 SignalR 集线器方法 （在客户端或服务器） 是*重大更改*。 这意味着较旧的客户端/服务器会在尝试调用不带适当数量的参数的方法时出现错误。 但是，将属性添加到自定义对象参数是**不**一项重大更改。 这可以用于设计兼容的 Api 时可复原的客户端或服务器上的更改。

例如，考虑如下所示的服务器端 API:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

JavaScript 客户端调用此方法使用`invoke`，如下所示：

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

如果以后服务器方法中添加第二个参数，较旧的客户端不会提供此参数值。 例如：

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

当旧客户端尝试调用此方法时，它会收到如下错误：

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

在服务器上，将看到一条日志消息如下：

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

旧客户端仅发送一个参数，但较新版本的服务器 API 所需的两个参数。 使用自定义对象作为参数提供了更大的灵活性。 让我们重新设计要使用自定义对象的原始 API:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

现在，客户端使用对象调用方法：

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

无需添加参数，将属性添加到`TotalLengthRequest`对象：

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

当旧客户端发送一个参数，额外`Param2`属性将保留`null`。 你可以检测到通过检查由较旧的客户端发送的消息`Param2`为`null`并应用默认值。 新的客户端可以发送两个参数。

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

相同的方法适用于在客户端上定义的方法。 可以从服务器端发送自定义对象：

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

在客户端上，访问`Message`属性，而无需使用参数：

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

如果你稍后决定要将消息的发件人添加到负载，请向对象添加属性：

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

较旧的客户端不应为`Sender`值，因此将其忽略。 新的客户端可以通过接受更新，以读取新属性：

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

在这种情况下，新的客户端也是不提供的旧服务器的容错`Sender`值。 由于旧的服务器不会提供`Sender`值时，客户端检查以查看是否存在之前对其进行访问。
