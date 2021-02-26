---
no-loc:
- appsettings.json
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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552158"
---
## <a name="share-interop-code-in-a-class-library"></a>在类库中共享互操作代码

可在类库中包含 JS 互操作代码，以便能在 NuGet 包中共享代码。

类库会处理在生成的程序集中嵌入 JavaScript 资源的操作。 JavaScript 文件位于 `wwwroot` 文件夹中。 工具负责在生成库时嵌入资源。

按引用任何其他 NuGet 包的方式在应用的项目文件中引用生成的 NuGet 包。 包还原后，应用代码可如同是 C# 一样调入 JavaScript。

有关详细信息，请参阅 <xref:blazor/components/class-libraries>。
