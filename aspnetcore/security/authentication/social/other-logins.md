---
title: 外部 OAuth 身份验证提供程序
author: rick-anderson
description: 发现适用于 ASP.NET Core 应用的外部 OAuth 身份验证提供程序。
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/otherlogins
ms.openlocfilehash: c61bbef26017f14df09f8ca6f494d29f79903b6b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776456"
---
# <a name="external-oauth-authentication-providers"></a><span data-ttu-id="3a3f0-103">外部 OAuth 身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="3a3f0-103">External OAuth authentication providers</span></span>

<span data-ttu-id="3a3f0-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Pranav Rastogi 撰写](https://github.com/rustd)和[Valeriy Novytskyy](https://github.com/01binary)</span><span class="sxs-lookup"><span data-stu-id="3a3f0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), and [Valeriy Novytskyy](https://github.com/01binary)</span></span>

<span data-ttu-id="3a3f0-105">下面的列表包含与 ASP.NET Core 应用一起使用的常见外部 OAuth 身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="3a3f0-105">The following list includes common external OAuth authentication providers that work with ASP.NET Core apps.</span></span> <span data-ttu-id="3a3f0-106">第三方 NuGet 包（如由[contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth)维护的包）可用于对由 ASP.NET Core 团队实现的身份验证提供程序进行补充。</span><span class="sxs-lookup"><span data-stu-id="3a3f0-106">Third-party NuGet packages, such as the ones maintained by [aspnet-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), can be used to complement the authentication providers implemented by the ASP.NET Core team.</span></span>

* <span data-ttu-id="3a3f0-107">[LinkedIn](https://www.linkedin.com/developer/apps) （[说明](https://developer.linkedin.com/docs/oauth2)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([Instructions](https://developer.linkedin.com/docs/oauth2))</span></span>

* <span data-ttu-id="3a3f0-108">[Instagram](https://www.instagram.com/developer/register/) （[说明](https://www.instagram.com/developer/authentication/)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-108">[Instagram](https://www.instagram.com/developer/register/) ([Instructions](https://www.instagram.com/developer/authentication/))</span></span>

* <span data-ttu-id="3a3f0-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) （[说明](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([Instructions](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span></span>

* <span data-ttu-id="3a3f0-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) （[说明](https://developer.github.com/v3/oauth/)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([Instructions](https://developer.github.com/v3/oauth/))</span></span>

* <span data-ttu-id="3a3f0-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) （[说明](https://developer.yahoo.com/bbauth/user.html)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([Instructions](https://developer.yahoo.com/bbauth/user.html))</span></span>

* <span data-ttu-id="3a3f0-112">[Tumblr](https://www.tumblr.com/oauth/apps) （[说明](https://www.tumblr.com/docs/api/v2#auth)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([Instructions](https://www.tumblr.com/docs/api/v2#auth))</span></span>

* <span data-ttu-id="3a3f0-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) （[说明](https://developers.pinterest.com/docs/api/overview/?)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([Instructions](https://developers.pinterest.com/docs/api/overview/?))</span></span>

* <span data-ttu-id="3a3f0-114">[Pocket](https://getpocket.com/developer/apps/new) （[说明](https://getpocket.com/developer/docs/authentication)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-114">[Pocket](https://getpocket.com/developer/apps/new) ([Instructions](https://getpocket.com/developer/docs/authentication))</span></span>

* <span data-ttu-id="3a3f0-115">[Flickr](https://www.flickr.com/services/apps/create) （[说明](https://www.flickr.com/services/api/auth.oauth.html)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-115">[Flickr](https://www.flickr.com/services/apps/create) ([Instructions](https://www.flickr.com/services/api/auth.oauth.html))</span></span>

* <span data-ttu-id="3a3f0-116">[Dribble](https://dribbble.com/signup) （[说明](https://developer.dribbble.com/v1/oauth/)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-116">[Dribble](https://dribbble.com/signup) ([Instructions](https://developer.dribbble.com/v1/oauth/))</span></span>

* <span data-ttu-id="3a3f0-117">[Vimeo](https://vimeo.com/join) （[说明](https://developer.vimeo.com/api/authentication)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-117">[Vimeo](https://vimeo.com/join) ([Instructions](https://developer.vimeo.com/api/authentication))</span></span>

* <span data-ttu-id="3a3f0-118">[SoundCloud](https://soundcloud.com/you/apps/new) （[说明](https://developers.soundcloud.com/blog/we-love-oauth-2)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([Instructions](https://developers.soundcloud.com/blog/we-love-oauth-2))</span></span>

* <span data-ttu-id="3a3f0-119">[VK](https://vk.com/apps?act=manage) （[说明](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites)）</span><span class="sxs-lookup"><span data-stu-id="3a3f0-119">[VK](https://vk.com/apps?act=manage) ([Instructions](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span></span>

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
