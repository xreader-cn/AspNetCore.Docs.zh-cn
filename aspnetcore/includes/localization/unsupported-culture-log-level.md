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
ms.openlocfilehash: 7caea4089d3624d4c02db4b8adbe9edb73f3d31a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551527"
---
> [!NOTE]
> <span data-ttu-id="4341a-101">在 ASP.NET Core 3.0 之前，如果请求的区域性不受支持，Web 应用将写入每个请求的一个类型为 `LogLevel.Warning` 的日志。</span><span class="sxs-lookup"><span data-stu-id="4341a-101">Prior to ASP.NET Core 3.0 web apps write one log of type `LogLevel.Warning` per request if the requested culture is unsupported.</span></span> <span data-ttu-id="4341a-102">记录每个请求的一个 `LogLevel.Warning` 可以生成包含冗余信息的大型日志文件。</span><span class="sxs-lookup"><span data-stu-id="4341a-102">Logging one `LogLevel.Warning` per request is can make large log files with redundant information.</span></span> <span data-ttu-id="4341a-103">此行为已在 ASP.NET 3.0 中进行了更改。</span><span class="sxs-lookup"><span data-stu-id="4341a-103">This behavior has been changed in ASP.NET 3.0.</span></span> <span data-ttu-id="4341a-104">`RequestLocalizationMiddleware` 写入类型为 `LogLevel.Debug` 的日志，这会减小生产日志的大小。</span><span class="sxs-lookup"><span data-stu-id="4341a-104">The `RequestLocalizationMiddleware` writes a log of type `LogLevel.Debug`, which reduces the size of production logs.</span></span>
