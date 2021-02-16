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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536271"
---
<span data-ttu-id="07dda-101">所有平台上的环境变量分层键都不支持 `:` 分隔符。</span><span class="sxs-lookup"><span data-stu-id="07dda-101">The `:` separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="07dda-102">`__`（双下划线）：</span><span class="sxs-lookup"><span data-stu-id="07dda-102">`__`, the double underscore, is:</span></span>

* <span data-ttu-id="07dda-103">受所有平台支持。</span><span class="sxs-lookup"><span data-stu-id="07dda-103">Supported by all platforms.</span></span> <span data-ttu-id="07dda-104">例如，[Bash](https://linuxhint.com/bash-environment-variables/) 不支持 `:` 分隔符，但支持 `__`。</span><span class="sxs-lookup"><span data-stu-id="07dda-104">For example, the `:` separator is not supported by [Bash](https://linuxhint.com/bash-environment-variables/), but `__` is.</span></span>
* <span data-ttu-id="07dda-105">自动替换为 `:`</span><span class="sxs-lookup"><span data-stu-id="07dda-105">Automatically replaced by a `:`</span></span>