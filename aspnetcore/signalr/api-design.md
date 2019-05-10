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
# <a name="signalr-api-design-considerations"></a><span data-ttu-id="97679-103">SignalR API 设计时的注意事项</span><span class="sxs-lookup"><span data-stu-id="97679-103">SignalR API design considerations</span></span>

<span data-ttu-id="97679-104">通过[Andrew Stanton-nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="97679-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="97679-105">本文提供了用于构建基于 SignalR 的 Api 的指南。</span><span class="sxs-lookup"><span data-stu-id="97679-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="97679-106">使用自定义对象参数来确保向后兼容性</span><span class="sxs-lookup"><span data-stu-id="97679-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="97679-107">将参数添加到 SignalR 集线器方法 （在客户端或服务器） 是*重大更改*。</span><span class="sxs-lookup"><span data-stu-id="97679-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="97679-108">这意味着较旧的客户端/服务器会在尝试调用不带适当数量的参数的方法时出现错误。</span><span class="sxs-lookup"><span data-stu-id="97679-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="97679-109">但是，将属性添加到自定义对象参数是**不**一项重大更改。</span><span class="sxs-lookup"><span data-stu-id="97679-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="97679-110">这可以用于设计兼容的 Api 时可复原的客户端或服务器上的更改。</span><span class="sxs-lookup"><span data-stu-id="97679-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="97679-111">例如，考虑如下所示的服务器端 API:</span><span class="sxs-lookup"><span data-stu-id="97679-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="97679-112">JavaScript 客户端调用此方法使用`invoke`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="97679-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="97679-113">如果以后服务器方法中添加第二个参数，较旧的客户端不会提供此参数值。</span><span class="sxs-lookup"><span data-stu-id="97679-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="97679-114">例如：</span><span class="sxs-lookup"><span data-stu-id="97679-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="97679-115">当旧客户端尝试调用此方法时，它会收到如下错误：</span><span class="sxs-lookup"><span data-stu-id="97679-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="97679-116">在服务器上，将看到一条日志消息如下：</span><span class="sxs-lookup"><span data-stu-id="97679-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="97679-117">旧客户端仅发送一个参数，但较新版本的服务器 API 所需的两个参数。</span><span class="sxs-lookup"><span data-stu-id="97679-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="97679-118">使用自定义对象作为参数提供了更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="97679-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="97679-119">让我们重新设计要使用自定义对象的原始 API:</span><span class="sxs-lookup"><span data-stu-id="97679-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="97679-120">现在，客户端使用对象调用方法：</span><span class="sxs-lookup"><span data-stu-id="97679-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="97679-121">无需添加参数，将属性添加到`TotalLengthRequest`对象：</span><span class="sxs-lookup"><span data-stu-id="97679-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="97679-122">当旧客户端发送一个参数，额外`Param2`属性将保留`null`。</span><span class="sxs-lookup"><span data-stu-id="97679-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="97679-123">你可以检测到通过检查由较旧的客户端发送的消息`Param2`为`null`并应用默认值。</span><span class="sxs-lookup"><span data-stu-id="97679-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="97679-124">新的客户端可以发送两个参数。</span><span class="sxs-lookup"><span data-stu-id="97679-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="97679-125">相同的方法适用于在客户端上定义的方法。</span><span class="sxs-lookup"><span data-stu-id="97679-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="97679-126">可以从服务器端发送自定义对象：</span><span class="sxs-lookup"><span data-stu-id="97679-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="97679-127">在客户端上，访问`Message`属性，而无需使用参数：</span><span class="sxs-lookup"><span data-stu-id="97679-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="97679-128">如果你稍后决定要将消息的发件人添加到负载，请向对象添加属性：</span><span class="sxs-lookup"><span data-stu-id="97679-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="97679-129">较旧的客户端不应为`Sender`值，因此将其忽略。</span><span class="sxs-lookup"><span data-stu-id="97679-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="97679-130">新的客户端可以通过接受更新，以读取新属性：</span><span class="sxs-lookup"><span data-stu-id="97679-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="97679-131">在这种情况下，新的客户端也是不提供的旧服务器的容错`Sender`值。</span><span class="sxs-lookup"><span data-stu-id="97679-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="97679-132">由于旧的服务器不会提供`Sender`值时，客户端检查以查看是否存在之前对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="97679-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
