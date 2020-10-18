---
title: 自定义 AuthorizationMiddleware 的行为
author: pranavkm
ms.author: prkrishn
description: 本文介绍如何自定义 AuthorizationMiddleware 的结果处理。
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156765"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="ab509-103">自定义 AuthorizationMiddleware 的行为</span><span class="sxs-lookup"><span data-stu-id="ab509-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="ab509-104">应用程序可以注册 `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` ，以自定义中间件处理授权结果的方式。</span><span class="sxs-lookup"><span data-stu-id="ab509-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="ab509-105">应用程序可以使用自定义中间件来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="ab509-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="ab509-106">返回自定义响应。</span><span class="sxs-lookup"><span data-stu-id="ab509-106">Return customized responses.</span></span>
* <span data-ttu-id="ab509-107">提高默认质询或禁止响应。</span><span class="sxs-lookup"><span data-stu-id="ab509-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="ab509-108">下面的代码显示了授权处理程序的示例，该示例为某些类型的授权失败返回自定义响应：</span><span class="sxs-lookup"><span data-stu-id="ab509-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="ab509-109">注册 `MyAuthorizationMiddlewareResultHandler` 到 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="ab509-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->