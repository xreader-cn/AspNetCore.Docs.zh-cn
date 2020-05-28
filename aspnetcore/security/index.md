---
<span data-ttu-id="88709-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="88709-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="88709-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="88709-102">'Blazor'</span></span>
- <span data-ttu-id="88709-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="88709-103">'Identity'</span></span>
- <span data-ttu-id="88709-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="88709-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="88709-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="88709-105">'Razor'</span></span>
- <span data-ttu-id="88709-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="88709-106">'SignalR' uid:</span></span> 

---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="88709-107">ASP.NET Core 安全性概述</span><span class="sxs-lookup"><span data-stu-id="88709-107">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="88709-108">通过 ASP.NET Core，开发者可轻松配置和管理其应用的安全性。</span><span class="sxs-lookup"><span data-stu-id="88709-108">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="88709-109">ASP.NET Core 的功能包括管理身份验证、授权、数据保护、HTTPS 强制、应用机密、请求防伪保护及 CORS 管理。</span><span class="sxs-lookup"><span data-stu-id="88709-109">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="88709-110">通过这些安全功能，可以生成安全可靠的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="88709-110">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="88709-111">ASP.NET Core 安全性功能</span><span class="sxs-lookup"><span data-stu-id="88709-111">ASP.NET Core security features</span></span>

<span data-ttu-id="88709-112">ASP.NET Core 提供许多用于保护应用安全的工具和库（包括内置的标识提供者），但你也可以使用第三方标识服务（如 Facebook、Twitter 和 LinkedIn）。</span><span class="sxs-lookup"><span data-stu-id="88709-112">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="88709-113">利用 ASP.NET Core 可以轻松管理应用机密，无需将机密信息暴露在代码中就可存储和使用它们。</span><span class="sxs-lookup"><span data-stu-id="88709-113">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="88709-114">身份验证 vs授权</span><span class="sxs-lookup"><span data-stu-id="88709-114">Authentication vs. Authorization</span></span>

<span data-ttu-id="88709-115">身份验证是这样一个过程：由用户提供凭据，然后将其与存储在操作系统、数据库、应用或资源中的凭据进行比较。</span><span class="sxs-lookup"><span data-stu-id="88709-115">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="88709-116">在授权过程中，如果凭据匹配，则用户身份验证成功，可执行已向其授权的操作。</span><span class="sxs-lookup"><span data-stu-id="88709-116">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="88709-117">授权指判断允许用户执行的操作的过程。</span><span class="sxs-lookup"><span data-stu-id="88709-117">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="88709-118">也可以将身份验证理解为进入空间（例如服务器、数据库、应用或资源）的一种方式，而授权是用户可以对该空间（服务器、数据库或应用）内的哪些对象执行哪些操作。</span><span class="sxs-lookup"><span data-stu-id="88709-118">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="88709-119">软件中的常见漏洞</span><span class="sxs-lookup"><span data-stu-id="88709-119">Common Vulnerabilities in software</span></span>

<span data-ttu-id="88709-120">ASP.NET Core 和 EF 提供维护应用安全、预防安全漏洞的功能。</span><span class="sxs-lookup"><span data-stu-id="88709-120">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="88709-121">下表中链接的文档详细介绍了在 Web 应用中避免最常见安全漏洞的技术：</span><span class="sxs-lookup"><span data-stu-id="88709-121">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="88709-122">跨站点脚本攻击</span><span class="sxs-lookup"><span data-stu-id="88709-122">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="88709-123">SQL 注入式攻击</span><span class="sxs-lookup"><span data-stu-id="88709-123">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="88709-124">跨站点请求伪造 (CSRF)</span><span class="sxs-lookup"><span data-stu-id="88709-124">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="88709-125">打开重定向攻击</span><span class="sxs-lookup"><span data-stu-id="88709-125">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="88709-126">还应注意其他漏洞。</span><span class="sxs-lookup"><span data-stu-id="88709-126">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="88709-127">有关详细信息，请参阅目录的“安全性和 Identity”部分中的其他文章。</span><span class="sxs-lookup"><span data-stu-id="88709-127">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
