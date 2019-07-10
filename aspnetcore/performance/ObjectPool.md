---
title: 使用 ASP.NET Core 中的 ObjectPool 对象重用
author: rick-anderson
description: 增加使用 ObjectPool 的 ASP.NET Core 应用中的性能的技巧。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 4/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 92106d5add7dbda36e451614429baa0db420f0e8
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724831"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="46730-103">使用 ASP.NET Core 中的 ObjectPool 对象重用</span><span class="sxs-lookup"><span data-stu-id="46730-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="46730-104">通过[Steve Gordon](https://twitter.com/stevejgordon)， [Ryan Nowak](https://github.com/rynowak)，和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="46730-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="46730-105"><xref:Microsoft.Extensions.ObjectPool> 是支持将一组对象保存在内存中以供重复使用，而不允许的对象进行垃圾收集的 ASP.NET Core 基础结构的一部分。</span><span class="sxs-lookup"><span data-stu-id="46730-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="46730-106">您可能想要使用对象池，如果正在管理的对象：</span><span class="sxs-lookup"><span data-stu-id="46730-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="46730-107">成本分配/初始化。</span><span class="sxs-lookup"><span data-stu-id="46730-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="46730-108">表示一些有限的资源。</span><span class="sxs-lookup"><span data-stu-id="46730-108">Represent some limited resource.</span></span>
- <span data-ttu-id="46730-109">使用以可预测方式和频率。</span><span class="sxs-lookup"><span data-stu-id="46730-109">Used predictably and frequently.</span></span>

<span data-ttu-id="46730-110">例如，ASP.NET Core 框架将使用对象池在某些位置重复使用<xref:System.Text.StringBuilder>实例。</span><span class="sxs-lookup"><span data-stu-id="46730-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="46730-111">`StringBuilder` 分配和管理其自己的缓冲区来存放字符数据。</span><span class="sxs-lookup"><span data-stu-id="46730-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="46730-112">ASP.NET Core 定期使用`StringBuilder`为了实现功能，并重复使用它们提供性能优势。</span><span class="sxs-lookup"><span data-stu-id="46730-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="46730-113">对象池始终不会提高性能：</span><span class="sxs-lookup"><span data-stu-id="46730-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="46730-114">对象的初始化成本较高，除非它是通常较慢，可从池中获取的对象。</span><span class="sxs-lookup"><span data-stu-id="46730-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="46730-115">池管理的对象不解除分配，直到该池将被释放。</span><span class="sxs-lookup"><span data-stu-id="46730-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="46730-116">使用对象池仅在为应用或库使用实际的方案的性能数据收集后。</span><span class="sxs-lookup"><span data-stu-id="46730-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="46730-117">**警告：`ObjectPool`不会实现`IDisposable`。我们不建议将用于需要可供使用的类型。**</span><span class="sxs-lookup"><span data-stu-id="46730-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="46730-118">**注意：ObjectPool 不置于将分配的对象数的限制，它将保留的对象数施加限制。**</span><span class="sxs-lookup"><span data-stu-id="46730-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="46730-119">概念</span><span class="sxs-lookup"><span data-stu-id="46730-119">Concepts</span></span>

<span data-ttu-id="46730-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -基本对象池抽象。</span><span class="sxs-lookup"><span data-stu-id="46730-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="46730-121">用于获取和返回对象。</span><span class="sxs-lookup"><span data-stu-id="46730-121">Used to get and return objects.</span></span>

<span data-ttu-id="46730-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -实现此接口可以自定义如何创建对象以及如何*重置*时返回到池中。</span><span class="sxs-lookup"><span data-stu-id="46730-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="46730-123">这可以传递到直接构造的对象池...或</span><span class="sxs-lookup"><span data-stu-id="46730-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="46730-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> 充当用于创建对象池的工厂。</span><span class="sxs-lookup"><span data-stu-id="46730-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="46730-125">ObjectPool 可以采用多种方式应用：</span><span class="sxs-lookup"><span data-stu-id="46730-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="46730-126">实例化一个池。</span><span class="sxs-lookup"><span data-stu-id="46730-126">Instantiating a pool.</span></span>
* <span data-ttu-id="46730-127">注册中的池[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 为一个实例。</span><span class="sxs-lookup"><span data-stu-id="46730-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="46730-128">注册`ObjectPoolProvider<>`在 DI 并将其用作工厂。</span><span class="sxs-lookup"><span data-stu-id="46730-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="46730-129">如何使用 ObjectPool</span><span class="sxs-lookup"><span data-stu-id="46730-129">How to use ObjectPool</span></span>

<span data-ttu-id="46730-130">调用<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>来获取的对象和<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*>来返回的对象。</span><span class="sxs-lookup"><span data-stu-id="46730-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="46730-131">没有任何要求返回的每个对象。</span><span class="sxs-lookup"><span data-stu-id="46730-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="46730-132">如果不返回一个对象，它将进行垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="46730-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="46730-133">ObjectPool 示例</span><span class="sxs-lookup"><span data-stu-id="46730-133">ObjectPool sample</span></span>

<span data-ttu-id="46730-134">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="46730-134">The following code:</span></span>

* <span data-ttu-id="46730-135">将添加`ObjectPoolProvider`到[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 容器。</span><span class="sxs-lookup"><span data-stu-id="46730-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="46730-136">添加并配置`ObjectPool<StringBuilder>`到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="46730-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="46730-137">添加`BirthdayMiddleware`。</span><span class="sxs-lookup"><span data-stu-id="46730-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="46730-138">下面的代码实现 `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="46730-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
