---
title: 具有截止时间和取消功能的可靠的 gRPC 服务
author: jamesnk
description: 了解如何在 .NET 中创建具有截止时间和取消功能的可靠的 gRPC 服务。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
no-loc:
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: 59b737a032ea37a554ad5ddd0f4d44e4e1602d88
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594363"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="11755-103">具有截止时间和取消功能的可靠的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="11755-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="11755-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="11755-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="11755-105">截止时间和取消功能是 gRPC 客户端用来中止进行中调用的功能。</span><span class="sxs-lookup"><span data-stu-id="11755-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="11755-106">本文介绍截止时间和取消功能非常重要的原因，以及如何在 .NET gRPC 应用中使用它们。</span><span class="sxs-lookup"><span data-stu-id="11755-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="11755-107">截止时间</span><span class="sxs-lookup"><span data-stu-id="11755-107">Deadlines</span></span>

<span data-ttu-id="11755-108">截止时间功能让 gRPC 客户端可以指定等待调用完成的时间。</span><span class="sxs-lookup"><span data-stu-id="11755-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="11755-109">超过截止时间时，将取消调用。</span><span class="sxs-lookup"><span data-stu-id="11755-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="11755-110">设定一个截止时间非常重要，因为它将提供调用可运行的最长时间。</span><span class="sxs-lookup"><span data-stu-id="11755-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="11755-111">它能阻止异常运行的服务持续运行并耗尽服务器资源。</span><span class="sxs-lookup"><span data-stu-id="11755-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="11755-112">截止时间对于构建可靠应用非常有效，应该进行配置。</span><span class="sxs-lookup"><span data-stu-id="11755-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="11755-113">截止时间配置：</span><span class="sxs-lookup"><span data-stu-id="11755-113">Deadline configuration:</span></span>

* <span data-ttu-id="11755-114">在进行调用时，使用 `CallOptions.Deadline` 配置截止时间。</span><span class="sxs-lookup"><span data-stu-id="11755-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="11755-115">没有截止时间默认值。</span><span class="sxs-lookup"><span data-stu-id="11755-115">There is no default deadline value.</span></span> <span data-ttu-id="11755-116">gRPC 调用没有时间限制，除非指定了截止时间。</span><span class="sxs-lookup"><span data-stu-id="11755-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="11755-117">截止时间指的是超过截止时间的 UTC 时间。</span><span class="sxs-lookup"><span data-stu-id="11755-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="11755-118">例如，`DateTime.UtcNow.AddSeconds(5)` 是从现在起 5 秒的截止时间。</span><span class="sxs-lookup"><span data-stu-id="11755-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="11755-119">如果使用的是过去或当前的时间，则调用将立即超过截止时间。</span><span class="sxs-lookup"><span data-stu-id="11755-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="11755-120">截止时间随 gRPC 调用发送到服务，并由客户端和服务独立跟踪。</span><span class="sxs-lookup"><span data-stu-id="11755-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="11755-121">gRPC 调用可能在一台计算机上完成，但当响应返回给客户端时，已超过了截止时间。</span><span class="sxs-lookup"><span data-stu-id="11755-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="11755-122">如果超过了截止时间，客户端和服务将有不同的行为：</span><span class="sxs-lookup"><span data-stu-id="11755-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="11755-123">客户端将立即中止基础的 HTTP 请求并引发 `DeadlineExceeded` 错误。</span><span class="sxs-lookup"><span data-stu-id="11755-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="11755-124">客户端应用可以选择捕获错误并向用户显示超时消息。</span><span class="sxs-lookup"><span data-stu-id="11755-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="11755-125">在服务器上，将中止正在执行的 HTTP 请求，并引发 [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken)。</span><span class="sxs-lookup"><span data-stu-id="11755-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="11755-126">尽管中止了 HTTP 请求，gRPC 调用仍将继续在服务器上运行，直到方法完成。</span><span class="sxs-lookup"><span data-stu-id="11755-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="11755-127">将取消令牌传递给异步方法，使其随调用一同被取消，这非常重要。</span><span class="sxs-lookup"><span data-stu-id="11755-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="11755-128">例如，向异步数据库查询和 HTTP 请求传递取消令牌。</span><span class="sxs-lookup"><span data-stu-id="11755-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="11755-129">传递取消令牌让取消的调用可以在服务器上快速完成，并为其他调用释放资源。</span><span class="sxs-lookup"><span data-stu-id="11755-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="11755-130">配置 `CallOptions.Deadline` 以设置 gRPC 调用的截止时间：</span><span class="sxs-lookup"><span data-stu-id="11755-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="11755-131">在 gRPC 服务中使用 `ServerCallContext.CancellationToken`：</span><span class="sxs-lookup"><span data-stu-id="11755-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="11755-132">传播截止时间</span><span class="sxs-lookup"><span data-stu-id="11755-132">Propagating deadlines</span></span>

<span data-ttu-id="11755-133">从正在执行的 gRPC 服务进行 gRPC 调用时，应传播截止时间。</span><span class="sxs-lookup"><span data-stu-id="11755-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="11755-134">例如：</span><span class="sxs-lookup"><span data-stu-id="11755-134">For example:</span></span>

1. <span data-ttu-id="11755-135">客户端应用调用带有截止时间的 `FrontendService.GetUser`。</span><span class="sxs-lookup"><span data-stu-id="11755-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="11755-136">`FrontendService` 调用 `UserService.GetUser`。</span><span class="sxs-lookup"><span data-stu-id="11755-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="11755-137">客户端指定的截止时间应随新的 gRPC 调用进行指定。</span><span class="sxs-lookup"><span data-stu-id="11755-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="11755-138">`UserService.GetUser` 接收截止时间。</span><span class="sxs-lookup"><span data-stu-id="11755-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="11755-139">如果超过了客户端应用的截止时间，将正确超时。</span><span class="sxs-lookup"><span data-stu-id="11755-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="11755-140">调用上下文将使用 `ServerCallContext.Deadline` 提供截止时间：</span><span class="sxs-lookup"><span data-stu-id="11755-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="11755-141">手动传播截止时间可能会很繁琐。</span><span class="sxs-lookup"><span data-stu-id="11755-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="11755-142">截止时间需要传递给每个调用，很容易不小心错过。</span><span class="sxs-lookup"><span data-stu-id="11755-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="11755-143">gRPC 客户端工厂提供自动解决方案。</span><span class="sxs-lookup"><span data-stu-id="11755-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="11755-144">指定 `EnableCallContextPropagation`：</span><span class="sxs-lookup"><span data-stu-id="11755-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="11755-145">自动将截止时间和取消令牌传播到子调用。</span><span class="sxs-lookup"><span data-stu-id="11755-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="11755-146">这是确保复杂的嵌套 gRPC 场景始终传播截止时间和取消的一种极佳方式。</span><span class="sxs-lookup"><span data-stu-id="11755-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="11755-147">有关详细信息，请参阅 <xref:grpc/clientfactory#deadline-and-cancellation-propagation>。</span><span class="sxs-lookup"><span data-stu-id="11755-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="11755-148">取消</span><span class="sxs-lookup"><span data-stu-id="11755-148">Cancellation</span></span>

<span data-ttu-id="11755-149">取消功能让 gRPC 客户端可以取消不再需要的长期运行的调用。</span><span class="sxs-lookup"><span data-stu-id="11755-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="11755-150">例如，当用户访问网站上的页面时，将启动流实时更新的 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="11755-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="11755-151">当用户离开页面时，应取消流。</span><span class="sxs-lookup"><span data-stu-id="11755-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="11755-152">通过传递带有 [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) 的取消令牌，或通过调用 `Dispose`，可以在客户端中取消 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="11755-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="11755-153">可以取消的 gRPC 服务应：</span><span class="sxs-lookup"><span data-stu-id="11755-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="11755-154">将 `ServerCallContext.CancellationToken` 传递给异步方法。</span><span class="sxs-lookup"><span data-stu-id="11755-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="11755-155">取消异步方法可以使服务器上的调用快速完成。</span><span class="sxs-lookup"><span data-stu-id="11755-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="11755-156">将取消令牌传播给子调用。</span><span class="sxs-lookup"><span data-stu-id="11755-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="11755-157">传播取消令牌可确保子调用与其父级一起取消。</span><span class="sxs-lookup"><span data-stu-id="11755-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="11755-158">[gRPC 客户端工厂](xref:grpc/clientfactory)和 `EnableCallContextPropagation()` 自动传播取消令牌。</span><span class="sxs-lookup"><span data-stu-id="11755-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="11755-159">其他资源</span><span class="sxs-lookup"><span data-stu-id="11755-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
