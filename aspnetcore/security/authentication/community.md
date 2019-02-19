---
title: 用于 ASP.NET Core 社区 OSS 身份验证选项
author: rick-anderson
description: 发现 ASP.NET Core 的开源身份验证的选项。
ms.author: riande
ms.date: 02/15/2019
uid: security/authentication/community
ms.openlocfilehash: e25df794bdff8f904382e7a299755ae4c23b892e
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410279"
---
# <a name="community-oss-authentication-options-for-aspnet-core"></a><span data-ttu-id="e1bf9-103">用于 ASP.NET Core 社区 OSS 身份验证选项</span><span class="sxs-lookup"><span data-stu-id="e1bf9-103">Community OSS authentication options for ASP.NET Core</span></span>

<span data-ttu-id="e1bf9-104">此页包含用于 ASP.NET Core 提供社区，开放源代码身份验证选项。</span><span class="sxs-lookup"><span data-stu-id="e1bf9-104">This page contains community-provided, open source authentication options for ASP.NET Core.</span></span> <span data-ttu-id="e1bf9-105">此页面会定期更新为新的提供程序变得可用。</span><span class="sxs-lookup"><span data-stu-id="e1bf9-105">This page is periodically updated as new providers become available.</span></span>

## <a name="oss-authentication-providers"></a><span data-ttu-id="e1bf9-106">OSS 身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="e1bf9-106">OSS authentication providers</span></span>

<span data-ttu-id="e1bf9-107">下面的列表是按字母顺序排序。</span><span class="sxs-lookup"><span data-stu-id="e1bf9-107">The list below is sorted alphabetically.</span></span>

| <span data-ttu-id="e1bf9-108">name</span><span class="sxs-lookup"><span data-stu-id="e1bf9-108">Name</span></span> | <span data-ttu-id="e1bf9-109">描述</span><span class="sxs-lookup"><span data-stu-id="e1bf9-109">Description</span></span> |
| ---- | ----------- |
| [<span data-ttu-id="e1bf9-110">AspNet.Security.OpenIdConnect.Server (ASOS)</span><span class="sxs-lookup"><span data-stu-id="e1bf9-110">AspNet.Security.OpenIdConnect.Server (ASOS)</span></span>](https://github.com/aspnet-contrib/AspNet.Security.OpenIdConnect.Server) | <span data-ttu-id="e1bf9-111">ASOS 是一个低级别，协议首先 OpenID Connect 服务器框架为 ASP.NET Core 和 OWIN/Katana。</span><span class="sxs-lookup"><span data-stu-id="e1bf9-111">ASOS is a low-level, protocol-first OpenID Connect server framework for ASP.NET Core and OWIN/Katana.</span></span> |
| [<span data-ttu-id="e1bf9-112">Cierge</span><span class="sxs-lookup"><span data-stu-id="e1bf9-112">Cierge</span></span>](https://github.com/pwdless/Cierge) | <span data-ttu-id="e1bf9-113">Cierge 是处理用户注册、 登录、 配置文件、 管理和社交登录名的 OpenID Connect 的服务器。</span><span class="sxs-lookup"><span data-stu-id="e1bf9-113">Cierge is an OpenID Connect server that handles user signup, login, profiles, management, and social logins.</span></span> |
| [<span data-ttu-id="e1bf9-114">Gluu 服务器</span><span class="sxs-lookup"><span data-stu-id="e1bf9-114">Gluu Server</span></span>](https://gluu.org/) | <span data-ttu-id="e1bf9-115">企业准备就绪后，打开源软件，用于标识、 访问管理 (IAM) 和单一登录 (SSO)。</span><span class="sxs-lookup"><span data-stu-id="e1bf9-115">Enterprise ready, open source software for identity, access management (IAM), and single sign-on (SSO).</span></span> <span data-ttu-id="e1bf9-116">有关详细信息，请参阅[Gluu 产品文档](https://gluu.org/docs/)。</span><span class="sxs-lookup"><span data-stu-id="e1bf9-116">For more information, see the [Gluu Product Documentation](https://gluu.org/docs/).</span></span> |
| [<span data-ttu-id="e1bf9-117">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="e1bf9-117">IdentityServer</span></span>](https://identityserver.io/) | <span data-ttu-id="e1bf9-118">IdentityServer 是 OpenID Connect 和 OAuth 2.0 的框架，用于 ASP.NET Core，正式认证 OpenID foundation 和.NET Foundation 监管下。</span><span class="sxs-lookup"><span data-stu-id="e1bf9-118">IdentityServer is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core, officially certified by the OpenID Foundation and under governance of the .NET Foundation.</span></span> <span data-ttu-id="e1bf9-119">有关详细信息，请参阅[欢迎使用 IdentityServer4 （文档）](https://identityserver4.readthedocs.io/en/latest/)。</span><span class="sxs-lookup"><span data-stu-id="e1bf9-119">For more information, see [Welcome to IdentityServer4 (Documentation)](https://identityserver4.readthedocs.io/en/latest/).</span></span> |
| [<span data-ttu-id="e1bf9-120">OpenIddict</span><span class="sxs-lookup"><span data-stu-id="e1bf9-120">OpenIddict</span></span>](https://github.com/openiddict/openiddict-core) | <span data-ttu-id="e1bf9-121">OpenIddict 是能够轻松使用 OpenID Connect 服务器 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="e1bf9-121">OpenIddict is an easy-to-use OpenID Connect server for ASP.NET Core.</span></span> |

<span data-ttu-id="e1bf9-122">若要添加提供程序，[编辑此页面](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Faspnet%2FDocs%2Fedit%2Fmaster%2Faspnetcore%2Fsecurity%2Fauthentication%2Fcommunity.md)。</span><span class="sxs-lookup"><span data-stu-id="e1bf9-122">To add a provider, [edit this page](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Faspnet%2FDocs%2Fedit%2Fmaster%2Faspnetcore%2Fsecurity%2Fauthentication%2Fcommunity.md).</span></span>
