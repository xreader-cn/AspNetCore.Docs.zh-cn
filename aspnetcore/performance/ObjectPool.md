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
ms.openlocfilehash: f29d15fc1e2d2ad84526598be14638110f08614e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774777"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>在 ASP.NET Core 中使用 ObjectPool 进行对象重用

作者： [Steve Gordon](https://twitter.com/stevejgordon)、 [Ryan Nowak](https://github.com/rynowak)和[Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool>是 ASP.NET Core 基础结构的一部分，它支持在内存中保留一组对象以供重复使用，而不是允许对象被垃圾回收。

如果要管理的对象是，则你可能需要使用对象池：

- 分配/初始化成本高昂。
- 表示某些有限资源。
- 使用可预测和频繁。

例如，在某些位置，ASP.NET Core 框架使用对象池来重复使用<xref:System.Text.StringBuilder>实例。 `StringBuilder`分配并管理自己的用于保存字符数据的缓冲区。 ASP.NET Core 会定期`StringBuilder`使用来实现功能，并重复使用这些功能，从而提高了性能。

对象池并不总是能提高性能：

- 除非对象的初始化开销较高，否则从池中获取该对象的速度通常较慢。
- 在取消分配池之前，不会释放池管理的对象。

仅在使用应用或库的现实方案收集性能数据后，才使用对象池。

**警告： `ObjectPool`没有实现`IDisposable`。建议不要将其与需要处置的类型一起使用。**

**注意： ObjectPool 不会对它将分配的对象数量施加限制，它会限制将保留的对象数。**

## <a name="concepts"></a>概念

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-基本对象池抽象。 用于获取和返回对象。

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-实现此方法可自定义对象的创建方式，以及在返回到池时*重置*对象的方式。 这可以传递到你直接构造的对象池中 .。。或

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>用作创建对象池的工厂。
<!-- REview, there is no ObjectPoolProvider<T> -->

可以通过多种方式在应用中使用 ObjectPool：

* 实例化池。
* 在[依赖关系注入](xref:fundamentals/dependency-injection)（DI）中将池注册为实例。
* `ObjectPoolProvider<>`在 DI 中注册并使用它作为工厂。

## <a name="how-to-use-objectpool"></a>如何使用 ObjectPool

调用<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>以获取对象并<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*>返回对象。  不要求你返回每个对象。 如果不返回对象，将对其进行垃圾回收。

## <a name="objectpool-sample"></a>ObjectPool 示例

下面的代码：

* 添加`ObjectPoolProvider`到[依赖关系注入](xref:fundamentals/dependency-injection)（DI）容器中。
* 向 DI 容器`ObjectPool<StringBuilder>`添加并配置。
* 添加`BirthdayMiddleware`。

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

下面的代码实现`BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
