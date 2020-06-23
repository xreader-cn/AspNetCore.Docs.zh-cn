---
title: 在 ASP.NET Core 中使用 ObjectPool 进行对象重用
author: rick-anderson
description: 使用 ObjectPool 提高 ASP.NET Core 应用中性能的提示。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 004ca5724517bf3fbf6512c0b9653793f4e0f702
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85241011"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="e3a9a-103">在 ASP.NET Core 中使用 ObjectPool 进行对象重用</span><span class="sxs-lookup"><span data-stu-id="e3a9a-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="e3a9a-104">作者： [Steve Gordon](https://twitter.com/stevejgordon)、 [Ryan Nowak](https://github.com/rynowak)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e3a9a-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e3a9a-105"><xref:Microsoft.Extensions.ObjectPool>是 ASP.NET Core 基础结构的一部分，它支持在内存中保留一组对象以供重复使用，而不是允许对象被垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="e3a9a-106">如果要管理的对象是，则你可能需要使用对象池：</span><span class="sxs-lookup"><span data-stu-id="e3a9a-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="e3a9a-107">分配/初始化成本高昂。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="e3a9a-108">表示某些有限资源。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-108">Represent some limited resource.</span></span>
- <span data-ttu-id="e3a9a-109">使用可预测和频繁。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-109">Used predictably and frequently.</span></span>

<span data-ttu-id="e3a9a-110">例如，在某些位置，ASP.NET Core 框架使用对象池来重复使用 <xref:System.Text.StringBuilder> 实例。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="e3a9a-111">`StringBuilder`分配并管理自己的用于保存字符数据的缓冲区。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="e3a9a-112">ASP.NET Core 会定期使用 `StringBuilder` 来实现功能，并重复使用这些功能，从而提高了性能。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="e3a9a-113">对象池并不总是能提高性能：</span><span class="sxs-lookup"><span data-stu-id="e3a9a-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="e3a9a-114">除非对象的初始化开销较高，否则从池中获取该对象的速度通常较慢。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="e3a9a-115">在取消分配池之前，不会释放池管理的对象。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="e3a9a-116">仅在使用应用或库的现实方案收集性能数据后，才使用对象池。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="e3a9a-117">**警告： `ObjectPool` 没有实现 `IDisposable` 。建议不要将其与需要处置的类型一起使用。**</span><span class="sxs-lookup"><span data-stu-id="e3a9a-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="e3a9a-118">**注意： ObjectPool 不会对它将分配的对象数量施加限制，它会限制将保留的对象数。**</span><span class="sxs-lookup"><span data-stu-id="e3a9a-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="e3a9a-119">概念</span><span class="sxs-lookup"><span data-stu-id="e3a9a-119">Concepts</span></span>

<span data-ttu-id="e3a9a-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-基本对象池抽象。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="e3a9a-121">用于获取和返回对象。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-121">Used to get and return objects.</span></span>

<span data-ttu-id="e3a9a-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-实现此方法可自定义对象的创建方式，以及在返回到池时*重置*对象的方式。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="e3a9a-123">这可以传递到你直接构造的对象池中 .。。或</span><span class="sxs-lookup"><span data-stu-id="e3a9a-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="e3a9a-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>用作创建对象池的工厂。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="e3a9a-125">可以通过多种方式在应用中使用 ObjectPool：</span><span class="sxs-lookup"><span data-stu-id="e3a9a-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="e3a9a-126">实例化池。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-126">Instantiating a pool.</span></span>
* <span data-ttu-id="e3a9a-127">在[依赖关系注入](xref:fundamentals/dependency-injection)（DI）中将池注册为实例。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="e3a9a-128">`ObjectPoolProvider<>`在 DI 中注册并使用它作为工厂。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="e3a9a-129">如何使用 ObjectPool</span><span class="sxs-lookup"><span data-stu-id="e3a9a-129">How to use ObjectPool</span></span>

<span data-ttu-id="e3a9a-130">调用 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> 以获取对象并 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> 返回对象。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="e3a9a-131">不要求你返回每个对象。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="e3a9a-132">如果不返回对象，将对其进行垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="e3a9a-133">ObjectPool 示例</span><span class="sxs-lookup"><span data-stu-id="e3a9a-133">ObjectPool sample</span></span>

<span data-ttu-id="e3a9a-134">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="e3a9a-134">The following code:</span></span>

* <span data-ttu-id="e3a9a-135">添加 `ObjectPoolProvider` 到[依赖关系注入](xref:fundamentals/dependency-injection)（DI）容器中。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="e3a9a-136">向 DI 容器添加并配置 `ObjectPool<StringBuilder>` 。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="e3a9a-137">添加 `BirthdayMiddleware` 。</span><span class="sxs-lookup"><span data-stu-id="e3a9a-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="e3a9a-138">下面的代码实现`BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="e3a9a-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
