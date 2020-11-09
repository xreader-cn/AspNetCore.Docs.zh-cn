---
title: Microsoft 标识平台和 Azure Active Directory 与 ASP.NET Core
author: rick-anderson
description: 介绍使用 Microsoft 标识平台和 Azure Active Directory 在 ASP.NET Core 对 Web 应用和 API 进行身份验证的相关主题。
ms.author: riande
ms.date: 01/21/2020
ms.custom: mvc, seodec18
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/azure-active-directory/index
ms.openlocfilehash: c8a3d6838b9b93ff58dfaff8423bd6946b5ca743
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061400"
---
# <a name="azure-active-directory-with-aspnet-core"></a><span data-ttu-id="cb782-103">Azure Active Directory 与 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb782-103">Azure Active Directory with ASP.NET Core</span></span>

<span data-ttu-id="cb782-104">这些教程和示例演示了如何使用 Microsoft 标识平台和 Azure Active Directory 在 ASP.NET Core 中进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="cb782-104">These tutorials and samples demonstrate authentication in ASP.NET Core using Microsoft identity platform and Azure Active Directory.</span></span> <span data-ttu-id="cb782-105">有关将 ASP.NET Core 与 Azure AD 结合使用的其他教程和示例，请参阅 [Microsoft 标识平台](/azure/active-directory/develop/)。</span><span class="sxs-lookup"><span data-stu-id="cb782-105">For additional tutorials and samples using ASP.NET Core with Azure AD, see [Microsoft identity platform](/azure/active-directory/develop/).</span></span>

## <a name="application-scenarios"></a><span data-ttu-id="cb782-106">应用程序方案</span><span class="sxs-lookup"><span data-stu-id="cb782-106">Application Scenarios</span></span>

* [<span data-ttu-id="cb782-107">快速入门：将 Microsoft 登录添加到 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="cb782-107">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="cb782-108">登录用户的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="cb782-108">Web app that signs in users</span></span>](/azure/active-directory/develop/scenario-web-app-sign-user-overview?tabs=aspnetcore)
* [<span data-ttu-id="cb782-109">用于调用 Web API 的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="cb782-109">Web app that calls web APIs</span></span>](/azure/active-directory/develop/scenario-web-app-call-api-overview)
* [<span data-ttu-id="cb782-110">受保护的 Web API</span><span class="sxs-lookup"><span data-stu-id="cb782-110">Protected web API</span></span>](/azure/active-directory/develop/scenario-protected-web-api-overview)
* [<span data-ttu-id="cb782-111">调用其他 Web API 的 Web API</span><span class="sxs-lookup"><span data-stu-id="cb782-111">Web API that calls other web APIs</span></span>](/azure/active-directory/develop/scenario-web-api-call-api-overview)
* [<span data-ttu-id="cb782-112">通过 Azure AD B2C 登录用户的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="cb782-112">Web app that signs in users with Azure AD B2C</span></span>](xref:security/authentication/azure-ad-b2c)

## <a name="samples"></a><span data-ttu-id="cb782-113">示例</span><span class="sxs-lookup"><span data-stu-id="cb782-113">Samples</span></span>

* <span data-ttu-id="cb782-114">[使你的 ASP.NET Core 应用能够使用 Azure AD V2 登录用户并调用 Web API](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/)：</span><span class="sxs-lookup"><span data-stu-id="cb782-114">[Enable your ASP.NET Core app to sign-in users and call web APIs using Azure AD V2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span></span> 
  * <span data-ttu-id="cb782-115">请观看[这段相关视频](https://channel9.msdn.com/Events/Build/2018/THR5001)</span><span class="sxs-lookup"><span data-stu-id="cb782-115">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5001)</span></span>
* <span data-ttu-id="cb782-116">[使用 Azure AD V2 从 WPF 应用程序调用 ASP.NET Core 2.0 Web API](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/)：</span><span class="sxs-lookup"><span data-stu-id="cb782-116">[Calling an ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span></span> 
  * <span data-ttu-id="cb782-117">请观看[这段相关视频](https://channel9.msdn.com/Events/Build/2018/THR5000)</span><span class="sxs-lookup"><span data-stu-id="cb782-117">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5000)</span></span>
* [<span data-ttu-id="cb782-118">带有 Azure AD B2C 的 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="cb782-118">An ASP.NET Core web app with Azure AD B2C</span></span>](/samples/azure-samples/active-directory-b2c-dotnetcore-webapp/an-aspnet-core-web-app-with-azure-ad-b2c/)
