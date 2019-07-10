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
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>使用 ASP.NET Core 中的 ObjectPool 对象重用

通过[Steve Gordon](https://twitter.com/stevejgordon)， [Ryan Nowak](https://github.com/rynowak)，和[Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool> 是支持将一组对象保存在内存中以供重复使用，而不允许的对象进行垃圾收集的 ASP.NET Core 基础结构的一部分。

您可能想要使用对象池，如果正在管理的对象：

- 成本分配/初始化。
- 表示一些有限的资源。
- 使用以可预测方式和频率。

例如，ASP.NET Core 框架将使用对象池在某些位置重复使用<xref:System.Text.StringBuilder>实例。 `StringBuilder` 分配和管理其自己的缓冲区来存放字符数据。 ASP.NET Core 定期使用`StringBuilder`为了实现功能，并重复使用它们提供性能优势。

对象池始终不会提高性能：

- 对象的初始化成本较高，除非它是通常较慢，可从池中获取的对象。
- 池管理的对象不解除分配，直到该池将被释放。

使用对象池仅在为应用或库使用实际的方案的性能数据收集后。

**警告：`ObjectPool`不会实现`IDisposable`。我们不建议将用于需要可供使用的类型。**

**注意：ObjectPool 不置于将分配的对象数的限制，它将保留的对象数施加限制。**

## <a name="concepts"></a>概念

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -基本对象池抽象。 用于获取和返回对象。

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -实现此接口可以自定义如何创建对象以及如何*重置*时返回到池中。 这可以传递到直接构造的对象池...或

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> 充当用于创建对象池的工厂。
<!-- REview, there is no ObjectPoolProvider<T> -->

ObjectPool 可以采用多种方式应用：

* 实例化一个池。
* 注册中的池[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 为一个实例。
* 注册`ObjectPoolProvider<>`在 DI 并将其用作工厂。

## <a name="how-to-use-objectpool"></a>如何使用 ObjectPool

调用<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>来获取的对象和<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*>来返回的对象。  没有任何要求返回的每个对象。 如果不返回一个对象，它将进行垃圾回收。

## <a name="objectpool-sample"></a>ObjectPool 示例

下面的代码：

* 将添加`ObjectPoolProvider`到[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 容器。
* 添加并配置`ObjectPool<StringBuilder>`到 DI 容器。
* 添加`BirthdayMiddleware`。

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

下面的代码实现 `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
