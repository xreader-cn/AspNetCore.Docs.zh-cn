---
title: 数据保护 Api 的其他 ASP.NET Core
author: rick-anderson
description: 了解 ASP.NET Core Data Protection ISecret 接口。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: bd772b11300cca8896ed512da1cd12c49e6f104b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060750"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="eced4-103">数据保护 Api 的其他 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eced4-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="eced4-104">实现以下任何接口的类型对于多个调用方应是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="eced4-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="eced4-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="eced4-105">ISecret</span></span>

<span data-ttu-id="eced4-106">`ISecret`接口表示机密值，如加密密钥材料。</span><span class="sxs-lookup"><span data-stu-id="eced4-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="eced4-107">它包含以下 API 图面：</span><span class="sxs-lookup"><span data-stu-id="eced4-107">It contains the following API surface:</span></span>

* <span data-ttu-id="eced4-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="eced4-108">`Length`: `int`</span></span>

* <span data-ttu-id="eced4-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="eced4-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="eced4-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="eced4-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="eced4-111">`WriteSecretIntoBuffer`方法用原始机密值填充所提供的缓冲区。</span><span class="sxs-lookup"><span data-stu-id="eced4-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="eced4-112">此 API 将缓冲区作为参数而不是直接返回，这 `byte[]` 使调用方可以固定缓冲区对象，从而限制托管垃圾回收器的机密公开。</span><span class="sxs-lookup"><span data-stu-id="eced4-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="eced4-113">`Secret`类型是在 `ISecret` 进程内内存中存储机密值的具体实现。</span><span class="sxs-lookup"><span data-stu-id="eced4-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="eced4-114">在 Windows 平台上，通过 [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory)对机密值进行加密。</span><span class="sxs-lookup"><span data-stu-id="eced4-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
