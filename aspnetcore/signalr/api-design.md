---
title: 'SignalR API 设计注意事项'
author: anurse
description: '了解如何 SignalR 在应用版本间设计 api 以实现兼容性。'
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/api-design
ms.openlocfilehash: 87665a7950edbc70b664230d2f078598e9dbc0aa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059645"
---
# <a name="no-locsignalr-api-design-considerations"></a><span data-ttu-id="6b81b-103">SignalR API 设计注意事项</span><span class="sxs-lookup"><span data-stu-id="6b81b-103">SignalR API design considerations</span></span>

<span data-ttu-id="6b81b-104">作者： [Andrew Stanton](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="6b81b-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="6b81b-105">本文提供了基于生成的 SignalR api 的指南。</span><span class="sxs-lookup"><span data-stu-id="6b81b-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="6b81b-106">使用自定义对象参数以确保向后兼容性</span><span class="sxs-lookup"><span data-stu-id="6b81b-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="6b81b-107">将参数添加到 SignalR 客户端或服务器上的 (集线器方法) 是一项 *重大更改* 。</span><span class="sxs-lookup"><span data-stu-id="6b81b-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change* .</span></span> <span data-ttu-id="6b81b-108">这意味着，较旧的客户端/服务器在尝试调用方法时，如果没有适当数量的参数，将会出现错误。</span><span class="sxs-lookup"><span data-stu-id="6b81b-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="6b81b-109">但是，将属性添加到自定义对象参数 **不** 是一项重大更改。</span><span class="sxs-lookup"><span data-stu-id="6b81b-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="6b81b-110">这可用于设计可对客户端或服务器上的更改复原的兼容 Api。</span><span class="sxs-lookup"><span data-stu-id="6b81b-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="6b81b-111">例如，请考虑如下所示的服务器端 API：</span><span class="sxs-lookup"><span data-stu-id="6b81b-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="6b81b-112">JavaScript 客户端使用调用此方法 `invoke` ，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6b81b-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="6b81b-113">如果以后将第二个参数添加到服务器方法，较旧的客户端将不提供此参数值。</span><span class="sxs-lookup"><span data-stu-id="6b81b-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="6b81b-114">例如：</span><span class="sxs-lookup"><span data-stu-id="6b81b-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="6b81b-115">旧客户端尝试调用此方法时，会收到类似于下面的错误：</span><span class="sxs-lookup"><span data-stu-id="6b81b-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="6b81b-116">在服务器上，你会看到如下所示的日志消息：</span><span class="sxs-lookup"><span data-stu-id="6b81b-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="6b81b-117">旧客户端只发送一个参数，但更新的服务器 API 需要两个参数。</span><span class="sxs-lookup"><span data-stu-id="6b81b-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="6b81b-118">使用自定义对象作为参数可提供更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="6b81b-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="6b81b-119">让我们重新设计原始 API，以使用自定义对象：</span><span class="sxs-lookup"><span data-stu-id="6b81b-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="6b81b-120">现在，客户端使用对象调用方法：</span><span class="sxs-lookup"><span data-stu-id="6b81b-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="6b81b-121">将属性添加到对象，而不是添加参数 `TotalLengthRequest` ：</span><span class="sxs-lookup"><span data-stu-id="6b81b-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="6b81b-122">旧客户端发送单个参数时， `Param2` 会保留额外的属性 `null` 。</span><span class="sxs-lookup"><span data-stu-id="6b81b-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="6b81b-123">通过选中 `Param2` `null` 并应用默认值，可以检测由较旧客户端发送的消息。</span><span class="sxs-lookup"><span data-stu-id="6b81b-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="6b81b-124">新的客户端可以发送这两个参数。</span><span class="sxs-lookup"><span data-stu-id="6b81b-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="6b81b-125">相同的技术适用于在客户端上定义的方法。</span><span class="sxs-lookup"><span data-stu-id="6b81b-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="6b81b-126">你可以从服务器端发送自定义对象：</span><span class="sxs-lookup"><span data-stu-id="6b81b-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="6b81b-127">在客户端，访问 `Message` 属性，而不是使用参数：</span><span class="sxs-lookup"><span data-stu-id="6b81b-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="6b81b-128">如果以后决定将消息的发件人添加到有效负载，请将属性添加到对象：</span><span class="sxs-lookup"><span data-stu-id="6b81b-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="6b81b-129">较旧的客户端不需要 `Sender` 该值，因此它们将忽略该值。</span><span class="sxs-lookup"><span data-stu-id="6b81b-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="6b81b-130">新的客户端可以通过更新读取新属性来接受它：</span><span class="sxs-lookup"><span data-stu-id="6b81b-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="6b81b-131">在这种情况下，新客户端也可以容忍不提供值的旧服务器 `Sender` 。</span><span class="sxs-lookup"><span data-stu-id="6b81b-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="6b81b-132">由于旧服务器不会提供 `Sender` 值，因此客户端会检查其是否存在，然后再访问它。</span><span class="sxs-lookup"><span data-stu-id="6b81b-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
