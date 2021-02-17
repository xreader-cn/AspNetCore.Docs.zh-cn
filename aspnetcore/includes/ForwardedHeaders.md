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
<span data-ttu-id="c5569-101">转接头中间件应在其他中间件之前运行。</span><span class="sxs-lookup"><span data-stu-id="c5569-101">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="c5569-102">此顺序可确保依赖于转接头信息的中间件可以使用标头值进行处理。</span><span class="sxs-lookup"><span data-stu-id="c5569-102">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="c5569-103">若要在诊断和错误处理中间件后运行转接头中间件，请参阅[转接头中间件顺序](xref:host-and-deploy/proxy-load-balancer#fhmo)。</span><span class="sxs-lookup"><span data-stu-id="c5569-103">To run Forwarded Headers Middleware after diagnostics and error handling middleware, see [Forwarded Headers Middleware order](xref:host-and-deploy/proxy-load-balancer#fhmo).</span></span>