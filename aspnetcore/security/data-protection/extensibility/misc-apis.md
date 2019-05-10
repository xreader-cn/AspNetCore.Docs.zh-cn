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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>杂项 ASP.NET Core 数据保护 Api

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> 实现以下接口的任何类型应该是线程安全的多个调用方。

## <a name="isecret"></a>ISecret

`ISecret`接口表示机密值，如加密密钥材料。 它包含以下 API 图面：

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer`方法填充所提供的缓冲区与原始机密值。 此 API 将缓冲区作为参数的原因而不是返回`byte[]`直接是，这使调用方能够固定限制托管的垃圾回收器对机密暴露该缓冲区对象。

`Secret`类型是具体的实现`ISecret`在进程内内存中存储的机密值。 在 Windows 平台上的机密值加密通过[CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)。
