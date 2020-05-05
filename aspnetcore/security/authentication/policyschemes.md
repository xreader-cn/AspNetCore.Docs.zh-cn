---
title: ASP.NET Core 中的策略方案
author: rick-anderson
description: 使用身份验证策略方案，可以更轻松地创建一个逻辑身份验证方案
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: ddedf62c5e8363bd93c9948fd2d3418abc566539
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767312"
---
# <a name="policy-schemes-in-aspnet-core"></a>ASP.NET Core 中的策略方案

使用身份验证策略方案，可以更方便地使用多种方法。 例如，策略方案可能使用 Google 身份验证，并对其他所有内容使用 cookie 身份验证。 身份验证策略方案：

* 可以轻松地将任何身份验证操作转发到另一个方案。
* 根据请求动态转发。

使用派生<xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions>的所有身份验证方案和关联[的\<AuthenticationHandler TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)：

* 是 ASP.NET Core 2.1 及更高版本中自动的策略方案。
* 可以通过配置方案的选项来启用。

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>示例

下面的示例演示了结合较低级别方案的更高级别的方案。 Google 身份验证用于质询，cookie 身份验证用于所有其他操作：

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

下面的示例基于每个请求启用动态选择方案。 也就是说，如何混合使用 cookie 和 API 身份验证：

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
