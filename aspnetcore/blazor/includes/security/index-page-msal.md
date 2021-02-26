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
ms.openlocfilehash: ce0a993093812c9a477d85d363827988ae9bd536
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551917"
---
<span data-ttu-id="89796-101">索引页 (`wwwroot/index.html`) 包含一个脚本，用于在 JavaScript 中定义 `AuthenticationService`。</span><span class="sxs-lookup"><span data-stu-id="89796-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="89796-102">`AuthenticationService` 处理 OIDC 协议的低级别详细信息。</span><span class="sxs-lookup"><span data-stu-id="89796-102">`AuthenticationService` handles the low-level details of the OIDC protocol.</span></span> <span data-ttu-id="89796-103">应用从内部调用脚本中定义的方法以执行身份验证操作。</span><span class="sxs-lookup"><span data-stu-id="89796-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
