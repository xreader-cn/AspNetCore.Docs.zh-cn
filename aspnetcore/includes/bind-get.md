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
ms.openlocfilehash: c6880852f63b1e91789667506f01680608933226
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552636"
---
> [!WARNING]
> <span data-ttu-id="f698c-101">出于安全原因，必须选择绑定 `GET` 请求数据以对模型属性进行分页。</span><span class="sxs-lookup"><span data-stu-id="f698c-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="f698c-102">请在将用户输入映射到属性前对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="f698c-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="f698c-103">当处理依赖查询字符串或路由值的方案时，选择加入 `GET` 绑定非常有用。</span><span class="sxs-lookup"><span data-stu-id="f698c-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="f698c-104">若要将属性绑定在 `GET` 请求上，请将 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 特性的 `SupportsGet` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="f698c-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="f698c-105">有关详细信息，请参阅 [ASP.NET Core Community Standup:Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s)（绑定 GET 讨论）。</span><span class="sxs-lookup"><span data-stu-id="f698c-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
