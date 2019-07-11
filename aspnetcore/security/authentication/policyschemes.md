---
title: 在 ASP.NET Core 中的策略方案
author: rick-anderson
description: 身份验证策略方案，更便于具有单一逻辑身份验证方案
ms.author: riande
ms.date: 02/28/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: be03f349455c673b0739935ad20e596325c8cb74
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815286"
---
# <a name="policy-schemes-in-aspnet-core"></a>在 ASP.NET Core 中的策略方案

身份验证策略方案更加轻松地有可能使用多个方法的单一逻辑身份验证方案。 例如，策略方案可能使用的挑战，Google 身份验证和 cookie 身份验证的其他所有内容。 身份验证策略方案使其：

* 轻松地将转发到另一个方案的任何身份验证操作。
* 正向动态根据该请求。

使用派生的所有身份验证方案<xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions>和关联[ `AuthenticationHandler<TOptions>` ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):

* 会自动在 ASP.NET Core 2.1 及更高版本的策略方案。
* 可以通过配置方案的选项来启用。

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>示例

下面的示例演示结合了较低级别方案的更高级别方案。 Google 身份验证用于挑战，并为其他所有使用 cookie 身份验证：

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

下面的示例可动态选择基于每个请求的方案。 它是如何混合使用 cookie 和 API 身份验证：

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
