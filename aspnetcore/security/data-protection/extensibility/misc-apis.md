---
title: 杂项 ASP.NET Core 数据保护 Api
author: rick-anderson
description: 了解有关 ASP.NET Core 数据保护 ISecret 接口。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654354"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>杂项 ASP.NET Core 数据保护 Api

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> 实现以下接口的任何类型应该是线程安全的多个调用方。

## <a name="isecret"></a>ISecret

`ISecret` 接口表示机密值，如加密密钥材料。 它包含以下 API 图面：

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer` 方法用原始机密值填充所提供的缓冲区。 此 API 使用缓冲区作为参数，而不是直接返回 `byte[]`，这使调用方可以固定缓冲区对象，从而限制托管垃圾回收器的机密公开。

`Secret` 类型是 `ISecret` 的具体实现，其中的密钥值存储在进程内内存中。 在 Windows 平台上，通过[CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)对机密值进行加密。
