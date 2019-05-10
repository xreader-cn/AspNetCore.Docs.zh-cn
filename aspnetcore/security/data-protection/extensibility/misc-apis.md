---
title: 杂项 ASP.NET Core 数据保护 Api
author: rick-anderson
description: 了解有关 ASP.NET Core 数据保护 ISecret 接口。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896614"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="89085-103">杂项 ASP.NET Core 数据保护 Api</span><span class="sxs-lookup"><span data-stu-id="89085-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="89085-104">实现以下接口的任何类型应该是线程安全的多个调用方。</span><span class="sxs-lookup"><span data-stu-id="89085-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="89085-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="89085-105">ISecret</span></span>

<span data-ttu-id="89085-106">`ISecret`接口表示机密值，如加密密钥材料。</span><span class="sxs-lookup"><span data-stu-id="89085-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="89085-107">它包含以下 API 图面：</span><span class="sxs-lookup"><span data-stu-id="89085-107">It contains the following API surface:</span></span>

* <span data-ttu-id="89085-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="89085-108">`Length`: `int`</span></span>

* <span data-ttu-id="89085-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="89085-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="89085-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="89085-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="89085-111">`WriteSecretIntoBuffer`方法填充所提供的缓冲区与原始机密值。</span><span class="sxs-lookup"><span data-stu-id="89085-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="89085-112">此 API 将缓冲区作为参数的原因而不是返回`byte[]`直接是，这使调用方能够固定限制托管的垃圾回收器对机密暴露该缓冲区对象。</span><span class="sxs-lookup"><span data-stu-id="89085-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="89085-113">`Secret`类型是具体的实现`ISecret`在进程内内存中存储的机密值。</span><span class="sxs-lookup"><span data-stu-id="89085-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="89085-114">在 Windows 平台上的机密值加密通过[CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)。</span><span class="sxs-lookup"><span data-stu-id="89085-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
