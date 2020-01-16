---
title: Azure Active Directory 与 ASP.NET Core
author: rick-anderson
description: 发现与在 ASP.NET Core 中使用 Azure Active Directory 进行身份验证相关的主题。
ms.author: riande
ms.date: 01/14/2020
ms.custom: mvc, seodec18
uid: security/authentication/azure-active-directory/index
ms.openlocfilehash: a856643216d423c791d3df47bd2206f9121b543f
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75951909"
---
# <a name="azure-active-directory-with-aspnet-core"></a><span data-ttu-id="9ad05-103">Azure Active Directory 与 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ad05-103">Azure Active Directory with ASP.NET Core</span></span>

## <a name="azure-ad-v1-samples"></a><span data-ttu-id="9ad05-104">Azure AD V1 示例</span><span class="sxs-lookup"><span data-stu-id="9ad05-104">Azure AD V1 samples</span></span>

<span data-ttu-id="9ad05-105">下面的示例演示如何集成 Azure AD V1，使用户能够使用工作和学校帐户登录：</span><span class="sxs-lookup"><span data-stu-id="9ad05-105">The following samples show how to integrate Azure AD V1, enabling users to sign-in with a work and school account:</span></span>
* <span data-ttu-id="9ad05-106">[将 Azure AD 集成到 ASP.NET Core Web 应用中](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore/tree/master)（已存档且不受支持）</span><span class="sxs-lookup"><span data-stu-id="9ad05-106">[Integrating Azure AD Into an ASP.NET Core Web App](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore/tree/master) (archived and unsupported)</span></span>
* <span data-ttu-id="9ad05-107">[Calling a ASP.NET Core Web API From a WPF Application Using Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore)（从使用 Azure AD 的 WPF 应用程序调用 ASP.NET Core Web API）</span><span class="sxs-lookup"><span data-stu-id="9ad05-107">[Calling a ASP.NET Core Web API From a WPF Application Using Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore)</span></span>
* <span data-ttu-id="9ad05-108">[Calling a Web API in an ASP.NET Core Web Application Using Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore/)（在使用 Azure AD 的 ASP.NET Core Web 应用程序中调用 Web API）</span><span class="sxs-lookup"><span data-stu-id="9ad05-108">[Calling a Web API in an ASP.NET Core Web Application Using Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore/)</span></span>

## <a name="azure-ad-v2-samples"></a><span data-ttu-id="9ad05-109">Azure AD V2 示例</span><span class="sxs-lookup"><span data-stu-id="9ad05-109">Azure AD V2 samples</span></span>

<span data-ttu-id="9ad05-110">下面的示例演示如何集成 Azure AD V2，使用户能够使用工作和学校帐户或者 Microsoft 个人帐户（以前称为 Live 帐户）登录：</span><span class="sxs-lookup"><span data-stu-id="9ad05-110">The following samples show how to integrate Azure AD V2, enabling users to sign-in with a work and school account or a Microsoft personal account (formerly Live account):</span></span>
* <span data-ttu-id="9ad05-111">[Integrating Azure AD V2 into an ASP.NET Core 2.0 web app](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)（将 Azure AD V2 集成到 ASP.NET Core 2.0 Web 应用中）：</span><span class="sxs-lookup"><span data-stu-id="9ad05-111">[Integrating Azure AD V2 into an ASP.NET Core 2.0 web app](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2):</span></span> 
  * <span data-ttu-id="9ad05-112">请观看[这段相关视频](https://channel9.msdn.com/Events/Build/2018/THR5001)</span><span class="sxs-lookup"><span data-stu-id="9ad05-112">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5001)</span></span> 

* <span data-ttu-id="9ad05-113">[Calling a ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](https://github.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)（使用 Azure AD V2 从 WPF 应用程序调用 ASP.NET Core 2.0 Web API）：</span><span class="sxs-lookup"><span data-stu-id="9ad05-113">[Calling a ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](https://github.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2):</span></span> 
  * <span data-ttu-id="9ad05-114">请观看[这段相关视频](https://channel9.msdn.com/Events/Build/2018/THR5000)</span><span class="sxs-lookup"><span data-stu-id="9ad05-114">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5000)</span></span>

## <a name="azure-ad-b2c-sample"></a><span data-ttu-id="9ad05-115">Azure AD B2C 示例</span><span class="sxs-lookup"><span data-stu-id="9ad05-115">Azure AD B2C sample</span></span>

<span data-ttu-id="9ad05-116">此示例演示如何集成 Azure AD B2C，使用户能够使用社交网站身份（如 Facebook、Google...）登录</span><span class="sxs-lookup"><span data-stu-id="9ad05-116">This sample shows how to integrate Azure AD B2C, enabling users to sign-in with social identities (like Facebook, Google, ...)</span></span>
* [<span data-ttu-id="9ad05-117">带有 Azure AD B2C 的 ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="9ad05-117">An ASP.NET Core web API with Azure AD B2C</span></span>](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/)
