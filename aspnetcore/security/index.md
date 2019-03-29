---
title: ASP.NET Core 安全性概述
author: tdykstra
description: 了解 ASP.NET Core 中的身份验证、授权和安全基础知识。
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: security/index
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="993bf-103">ASP.NET Core 安全性概述</span><span class="sxs-lookup"><span data-stu-id="993bf-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="993bf-104">通过 ASP.NET Core，开发者可轻松配置和管理其应用的安全性。</span><span class="sxs-lookup"><span data-stu-id="993bf-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="993bf-105">ASP.NET Core 的功能包括管理身份验证、授权、数据保护、HTTPS 强制、应用机密、请求防伪保护及 CORS 管理。</span><span class="sxs-lookup"><span data-stu-id="993bf-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="993bf-106">通过这些安全功能，可以生成安全可靠的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="993bf-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="993bf-107">ASP.NET Core 安全性功能</span><span class="sxs-lookup"><span data-stu-id="993bf-107">ASP.NET Core security features</span></span>

<span data-ttu-id="993bf-108">ASP.NET Core 提供许多用于保护应用安全的工具和库（包括内置标识提供程序），但也可使用第三方标识服务（如 Facebook、Twitter 或 LinkedIn）。</span><span class="sxs-lookup"><span data-stu-id="993bf-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in Identity providers but you can use 3rd party identity services such as Facebook, Twitter, or LinkedIn.</span></span> <span data-ttu-id="993bf-109">利用 ASP.NET Core 可以轻松管理应用机密，无需将机密信息暴露在代码中就可存储和使用它们。</span><span class="sxs-lookup"><span data-stu-id="993bf-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="993bf-110">身份验证 vs授权</span><span class="sxs-lookup"><span data-stu-id="993bf-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="993bf-111">身份验证是这样一个过程：由用户提供凭据，然后将其与存储在操作系统、数据库、应用或资源中的凭据进行比较。</span><span class="sxs-lookup"><span data-stu-id="993bf-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="993bf-112">在授权过程中，如果凭据匹配，则用户身份验证成功，可执行已向其授权的操作。</span><span class="sxs-lookup"><span data-stu-id="993bf-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="993bf-113">授权指判断允许用户执行的操作的过程。</span><span class="sxs-lookup"><span data-stu-id="993bf-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="993bf-114">对身份验证的另一种理解是将其看作进入某一空间（如服务器、数据库、应用或资源）的方式，而将授权看作用户可对该空间（服务器、数据库或应用）内的对象执行的操作。</span><span class="sxs-lookup"><span data-stu-id="993bf-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="993bf-115">软件中的常见漏洞</span><span class="sxs-lookup"><span data-stu-id="993bf-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="993bf-116">ASP.NET Core 和 EF 提供维护应用安全、预防安全漏洞的功能。</span><span class="sxs-lookup"><span data-stu-id="993bf-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="993bf-117">下表中链接的文档详细介绍了在 Web 应用中避免最常见安全漏洞的技术：</span><span class="sxs-lookup"><span data-stu-id="993bf-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="993bf-118">跨站点脚本攻击</span><span class="sxs-lookup"><span data-stu-id="993bf-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="993bf-119">SQL 注入式攻击</span><span class="sxs-lookup"><span data-stu-id="993bf-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="993bf-120">跨站点请求伪造 (CSRF)</span><span class="sxs-lookup"><span data-stu-id="993bf-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="993bf-121">打开重定向攻击</span><span class="sxs-lookup"><span data-stu-id="993bf-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="993bf-122">还应注意其他漏洞。</span><span class="sxs-lookup"><span data-stu-id="993bf-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="993bf-123">有关详细信息，请参阅目录的“安全性和标识”部分中的其他文章。</span><span class="sxs-lookup"><span data-stu-id="993bf-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
