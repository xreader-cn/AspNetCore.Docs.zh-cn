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
ms.openlocfilehash: ddc6e2abf60577644a38b0b6ad0c74a734205ef5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552396"
---
转接头中间件应在其他中间件之前运行。 此顺序可确保依赖于转接头信息的中间件可以使用标头值进行处理。 若要在诊断和错误处理中间件后运行转接头中间件，请参阅[转接头中间件顺序](xref:host-and-deploy/proxy-load-balancer#fhmo)。