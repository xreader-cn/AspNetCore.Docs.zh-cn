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
ms.openlocfilehash: 73395ab632f38fef1348b4657770eb52db5778d9
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552692"
---
> [!WARNING]
> <span data-ttu-id="32041-101">如果使用 <xref:System.Text.RegularExpressions> 处理不受信任的输入，则传递一个超时。</span><span class="sxs-lookup"><span data-stu-id="32041-101">When using <xref:System.Text.RegularExpressions> to process untrusted input, pass a timeout.</span></span> <span data-ttu-id="32041-102">恶意用户可能会向 `RegularExpressions` 提供输入，从而导致[拒绝服务攻击](https://www.us-cert.gov/ncas/tips/ST04-015)。</span><span class="sxs-lookup"><span data-stu-id="32041-102">A malicious user can provide input to `RegularExpressions` causing a [Denial-of-Service attack](https://www.us-cert.gov/ncas/tips/ST04-015).</span></span> <span data-ttu-id="32041-103">使用 `RegularExpressions` 的 ASP.NET Core 框架 API 会传递一个超时。</span><span class="sxs-lookup"><span data-stu-id="32041-103">ASP.NET Core framework APIs that use `RegularExpressions` pass a timeout.</span></span>