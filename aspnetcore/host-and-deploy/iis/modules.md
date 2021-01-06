---
title: IIS 模块与 ASP.NET Core
author: rick-anderson
description: 了解适用于 ASP.NET Core 应用的活动和非活动 IIS 模块以及如何管理 IIS 模块。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 47ba04f199f9b77cf6032de9f80f2410f5c69424
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057396"
---
# <a name="iis-modules-with-aspnet-core"></a><span data-ttu-id="8bb56-103">IIS 模块与 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8bb56-103">IIS modules with ASP.NET Core</span></span>

<span data-ttu-id="8bb56-104">某些本机 IIS 模块和所有 IIS 托管模块无法处理 ASP.NET Core 应用的请求。</span><span class="sxs-lookup"><span data-stu-id="8bb56-104">Some of the native IIS modules and all of the IIS managed modules aren't able to process requests for ASP.NET Core apps.</span></span> <span data-ttu-id="8bb56-105">在许多情况下，ASP.NET Core 提供了 IIS 本机和托管模块解决的方案的替代方案。</span><span class="sxs-lookup"><span data-stu-id="8bb56-105">In many cases, ASP.NET Core offers an alternative to the scenarios addressed by IIS native and managed modules.</span></span>

## <a name="native-modules"></a><span data-ttu-id="8bb56-106">本机模块</span><span class="sxs-lookup"><span data-stu-id="8bb56-106">Native modules</span></span>

<span data-ttu-id="8bb56-107">该表指示在使用 ASP.NET Core 应用和 ASP.NET Core 模块时正常工作的本机 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-107">The table indicates native IIS modules that are functional with ASP.NET Core apps and the ASP.NET Core Module.</span></span>

| <span data-ttu-id="8bb56-108">模块</span><span class="sxs-lookup"><span data-stu-id="8bb56-108">Module</span></span> | <span data-ttu-id="8bb56-109">在 ASP.NET Core 应用内可用</span><span class="sxs-lookup"><span data-stu-id="8bb56-109">Functional with ASP.NET Core apps</span></span> | <span data-ttu-id="8bb56-110">ASP.NET Core 选项</span><span class="sxs-lookup"><span data-stu-id="8bb56-110">ASP.NET Core Option</span></span> |
| --- | :---: | --- |
| <span data-ttu-id="8bb56-111">**匿名身份验证**</span><span class="sxs-lookup"><span data-stu-id="8bb56-111">**Anonymous Authentication**</span></span><br>`AnonymousAuthenticationModule`                                  | <span data-ttu-id="8bb56-112">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-112">Yes</span></span> | |
| <span data-ttu-id="8bb56-113">**基本身份验证**</span><span class="sxs-lookup"><span data-stu-id="8bb56-113">**Basic Authentication**</span></span><br>`BasicAuthenticationModule`                                          | <span data-ttu-id="8bb56-114">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-114">Yes</span></span> | |
| <span data-ttu-id="8bb56-115">**客户端证书映射身份验证**</span><span class="sxs-lookup"><span data-stu-id="8bb56-115">**Client Certification Mapping Authentication**</span></span><br>`CertificateMappingAuthenticationModule`      | <span data-ttu-id="8bb56-116">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-116">Yes</span></span> | |
| <span data-ttu-id="8bb56-117">**CGI**</span><span class="sxs-lookup"><span data-stu-id="8bb56-117">**CGI**</span></span><br>`CgiModule`                                                                           | <span data-ttu-id="8bb56-118">否</span><span class="sxs-lookup"><span data-stu-id="8bb56-118">No</span></span>  | |
| <span data-ttu-id="8bb56-119">**配置验证**</span><span class="sxs-lookup"><span data-stu-id="8bb56-119">**Configuration Validation**</span></span><br>`ConfigurationValidationModule`                                  | <span data-ttu-id="8bb56-120">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-120">Yes</span></span> | |
| <span data-ttu-id="8bb56-121">**HTTP 错误**</span><span class="sxs-lookup"><span data-stu-id="8bb56-121">**HTTP Errors**</span></span><br>`CustomErrorModule`                                                           | <span data-ttu-id="8bb56-122">否</span><span class="sxs-lookup"><span data-stu-id="8bb56-122">No</span></span>  | [<span data-ttu-id="8bb56-123">状态代码页中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-123">Status Code Pages Middleware</span></span>](xref:fundamentals/error-handling#usestatuscodepages) |
| <span data-ttu-id="8bb56-124">**自定义日志记录**</span><span class="sxs-lookup"><span data-stu-id="8bb56-124">**Custom Logging**</span></span><br>`CustomLoggingModule`                                                      | <span data-ttu-id="8bb56-125">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-125">Yes</span></span> | |
| <span data-ttu-id="8bb56-126">**默认文档**</span><span class="sxs-lookup"><span data-stu-id="8bb56-126">**Default Document**</span></span><br>`DefaultDocumentModule`                                                  | <span data-ttu-id="8bb56-127">否</span><span class="sxs-lookup"><span data-stu-id="8bb56-127">No</span></span>  | [<span data-ttu-id="8bb56-128">默认文件中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-128">Default Files Middleware</span></span>](xref:fundamentals/static-files#serve-a-default-document) |
| <span data-ttu-id="8bb56-129">**摘要式身份验证**</span><span class="sxs-lookup"><span data-stu-id="8bb56-129">**Digest Authentication**</span></span><br>`DigestAuthenticationModule`                                        | <span data-ttu-id="8bb56-130">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-130">Yes</span></span> | |
| <span data-ttu-id="8bb56-131">**目录浏览**</span><span class="sxs-lookup"><span data-stu-id="8bb56-131">**Directory Browsing**</span></span><br>`DirectoryListingModule`                                               | <span data-ttu-id="8bb56-132">否</span><span class="sxs-lookup"><span data-stu-id="8bb56-132">No</span></span>  | [<span data-ttu-id="8bb56-133">目录浏览中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-133">Directory Browsing Middleware</span></span>](xref:fundamentals/static-files#enable-directory-browsing) |
| <span data-ttu-id="8bb56-134">**动态压缩**</span><span class="sxs-lookup"><span data-stu-id="8bb56-134">**Dynamic Compression**</span></span><br>`DynamicCompressionModule`                                            | <span data-ttu-id="8bb56-135">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-135">Yes</span></span> | [<span data-ttu-id="8bb56-136">响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-136">Response Compression Middleware</span></span>](xref:performance/response-compression) |
| <span data-ttu-id="8bb56-137">**请求跟踪失败**</span><span class="sxs-lookup"><span data-stu-id="8bb56-137">**Failed Requests Tracing**</span></span><br>`FailedRequestsTracingModule`                                     | <span data-ttu-id="8bb56-138">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-138">Yes</span></span> | [<span data-ttu-id="8bb56-139">ASP.NET Core 日志记录</span><span class="sxs-lookup"><span data-stu-id="8bb56-139">ASP.NET Core Logging</span></span>](xref:fundamentals/logging/index#tracesource-provider) |
| <span data-ttu-id="8bb56-140">**文件缓存**</span><span class="sxs-lookup"><span data-stu-id="8bb56-140">**File Caching**</span></span><br>`FileCacheModule`                                                            | <span data-ttu-id="8bb56-141">否</span><span class="sxs-lookup"><span data-stu-id="8bb56-141">No</span></span>  | [<span data-ttu-id="8bb56-142">响应缓存中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-142">Response Caching Middleware</span></span>](xref:performance/caching/middleware) |
| <span data-ttu-id="8bb56-143">**HTTP 缓存**</span><span class="sxs-lookup"><span data-stu-id="8bb56-143">**HTTP Caching**</span></span><br>`HttpCacheModule`                                                            | <span data-ttu-id="8bb56-144">否</span><span class="sxs-lookup"><span data-stu-id="8bb56-144">No</span></span>  | [<span data-ttu-id="8bb56-145">响应缓存中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-145">Response Caching Middleware</span></span>](xref:performance/caching/middleware) |
| <span data-ttu-id="8bb56-146">**HTTP 日志记录**</span><span class="sxs-lookup"><span data-stu-id="8bb56-146">**HTTP Logging**</span></span><br>`HttpLoggingModule`                                                          | <span data-ttu-id="8bb56-147">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-147">Yes</span></span> | [<span data-ttu-id="8bb56-148">ASP.NET Core 日志记录</span><span class="sxs-lookup"><span data-stu-id="8bb56-148">ASP.NET Core Logging</span></span>](xref:fundamentals/logging/index) |
| <span data-ttu-id="8bb56-149">**HTTP 重定向**</span><span class="sxs-lookup"><span data-stu-id="8bb56-149">**HTTP Redirection**</span></span><br>`HttpRedirectionModule`                                                  | <span data-ttu-id="8bb56-150">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-150">Yes</span></span> | [<span data-ttu-id="8bb56-151">URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-151">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) |
| <span data-ttu-id="8bb56-152">**HTTP 跟踪**</span><span class="sxs-lookup"><span data-stu-id="8bb56-152">**HTTP Tracing**</span></span><br>`TracingModule`                                                              | <span data-ttu-id="8bb56-153">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-153">Yes</span></span> | |
| <span data-ttu-id="8bb56-154">**IIS 客户端证书映射身份验证**</span><span class="sxs-lookup"><span data-stu-id="8bb56-154">**IIS Client Certificate Mapping Authentication**</span></span><br>`IISCertificateMappingAuthenticationModule` | <span data-ttu-id="8bb56-155">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-155">Yes</span></span> | |
| <span data-ttu-id="8bb56-156">**IP 和域限制**</span><span class="sxs-lookup"><span data-stu-id="8bb56-156">**IP and Domain Restrictions**</span></span><br>`IpRestrictionModule`                                          | <span data-ttu-id="8bb56-157">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-157">Yes</span></span> | |
| <span data-ttu-id="8bb56-158">**ISAPI 筛选器**</span><span class="sxs-lookup"><span data-stu-id="8bb56-158">**ISAPI Filters**</span></span><br>`IsapiFilterModule`                                                         | <span data-ttu-id="8bb56-159">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-159">Yes</span></span> | [<span data-ttu-id="8bb56-160">中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-160">Middleware</span></span>](xref:fundamentals/middleware/index) |
| <span data-ttu-id="8bb56-161">**ISAPI**</span><span class="sxs-lookup"><span data-stu-id="8bb56-161">**ISAPI**</span></span><br>`IsapiModule`                                                                       | <span data-ttu-id="8bb56-162">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-162">Yes</span></span> | [<span data-ttu-id="8bb56-163">中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-163">Middleware</span></span>](xref:fundamentals/middleware/index) |
| <span data-ttu-id="8bb56-164">**协议支持**</span><span class="sxs-lookup"><span data-stu-id="8bb56-164">**Protocol Support**</span></span><br>`ProtocolSupportModule`                                                  | <span data-ttu-id="8bb56-165">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-165">Yes</span></span> | |
| <span data-ttu-id="8bb56-166">**请求筛选**</span><span class="sxs-lookup"><span data-stu-id="8bb56-166">**Request Filtering**</span></span><br>`RequestFilteringModule`                                                | <span data-ttu-id="8bb56-167">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-167">Yes</span></span> | [<span data-ttu-id="8bb56-168">URL 重写中间件`IRule`</span><span class="sxs-lookup"><span data-stu-id="8bb56-168">URL Rewriting Middleware `IRule`</span></span>](xref:fundamentals/url-rewriting#irule-based-rule) |
| <span data-ttu-id="8bb56-169">**请求监视器**</span><span class="sxs-lookup"><span data-stu-id="8bb56-169">**Request Monitor**</span></span><br>`RequestMonitorModule`                                                    | <span data-ttu-id="8bb56-170">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-170">Yes</span></span> | |
| <span data-ttu-id="8bb56-171">**URL 重写**&#8224；</span><span class="sxs-lookup"><span data-stu-id="8bb56-171">**URL Rewriting**&#8224;</span></span><br>`RewriteModule`                                                      | <span data-ttu-id="8bb56-172">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-172">Yes</span></span> | [<span data-ttu-id="8bb56-173">URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-173">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) |
| <span data-ttu-id="8bb56-174">**服务器端包括**</span><span class="sxs-lookup"><span data-stu-id="8bb56-174">**Server-Side Includes**</span></span><br>`ServerSideIncludeModule`                                            | <span data-ttu-id="8bb56-175">否</span><span class="sxs-lookup"><span data-stu-id="8bb56-175">No</span></span>  | |
| <span data-ttu-id="8bb56-176">**静态压缩**</span><span class="sxs-lookup"><span data-stu-id="8bb56-176">**Static Compression**</span></span><br>`StaticCompressionModule`                                              | <span data-ttu-id="8bb56-177">否</span><span class="sxs-lookup"><span data-stu-id="8bb56-177">No</span></span>  | [<span data-ttu-id="8bb56-178">响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-178">Response Compression Middleware</span></span>](xref:performance/response-compression) |
| <span data-ttu-id="8bb56-179">**静态内容**</span><span class="sxs-lookup"><span data-stu-id="8bb56-179">**Static Content**</span></span><br>`StaticFileModule`                                                         | <span data-ttu-id="8bb56-180">否</span><span class="sxs-lookup"><span data-stu-id="8bb56-180">No</span></span>  | [<span data-ttu-id="8bb56-181">静态文件中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-181">Static File Middleware</span></span>](xref:fundamentals/static-files) |
| <span data-ttu-id="8bb56-182">**令牌缓存**</span><span class="sxs-lookup"><span data-stu-id="8bb56-182">**Token Caching**</span></span><br>`TokenCacheModule`                                                          | <span data-ttu-id="8bb56-183">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-183">Yes</span></span> | |
| <span data-ttu-id="8bb56-184">**URI 缓存**</span><span class="sxs-lookup"><span data-stu-id="8bb56-184">**URI Caching**</span></span><br>`UriCacheModule`                                                              | <span data-ttu-id="8bb56-185">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-185">Yes</span></span> | |
| <span data-ttu-id="8bb56-186">**URL 授权**</span><span class="sxs-lookup"><span data-stu-id="8bb56-186">**URL Authorization**</span></span><br>`UrlAuthorizationModule`                                                | <span data-ttu-id="8bb56-187">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-187">Yes</span></span> | [ASP.NET Core Identity](xref:security/authentication/identity) |
| <span data-ttu-id="8bb56-188">**Windows 身份验证**</span><span class="sxs-lookup"><span data-stu-id="8bb56-188">**Windows Authentication**</span></span><br>`WindowsAuthenticationModule`                                      | <span data-ttu-id="8bb56-189">是</span><span class="sxs-lookup"><span data-stu-id="8bb56-189">Yes</span></span> | |

<span data-ttu-id="8bb56-190">&#8224;由于[目录结构](xref:host-and-deploy/directory-structure)中的更改，URL 重写模块的 `isFile` 和 `isDirectory` 匹配类型不适用于 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="8bb56-190">&#8224;The URL Rewrite Module's `isFile` and `isDirectory` match types don't work with ASP.NET Core apps due to the changes in [directory structure](xref:host-and-deploy/directory-structure).</span></span>

## <a name="managed-modules"></a><span data-ttu-id="8bb56-191">托管模块</span><span class="sxs-lookup"><span data-stu-id="8bb56-191">Managed modules</span></span>

<span data-ttu-id="8bb56-192">当应用池的 .NET CLR 版本设置为“无托管代码”时，托管 ASP.NET Core 应用的托管模块无法使用。</span><span class="sxs-lookup"><span data-stu-id="8bb56-192">Managed modules are *not* functional with hosted ASP.NET Core apps when the app pool's .NET CLR version is set to **No Managed Code**.</span></span> <span data-ttu-id="8bb56-193">ASP.NET Core 在几种情况下提供了中间件替代方案。</span><span class="sxs-lookup"><span data-stu-id="8bb56-193">ASP.NET Core offers middleware alternatives in several cases.</span></span>

| <span data-ttu-id="8bb56-194">模块</span><span class="sxs-lookup"><span data-stu-id="8bb56-194">Module</span></span>                  | <span data-ttu-id="8bb56-195">ASP.NET Core 选项</span><span class="sxs-lookup"><span data-stu-id="8bb56-195">ASP.NET Core Option</span></span> |
| ----------------------- | ------------------- |
| <span data-ttu-id="8bb56-196">AnonymousIdentification</span><span class="sxs-lookup"><span data-stu-id="8bb56-196">AnonymousIdentification</span></span> | |
| <span data-ttu-id="8bb56-197">DefaultAuthentication</span><span class="sxs-lookup"><span data-stu-id="8bb56-197">DefaultAuthentication</span></span>   | |
| <span data-ttu-id="8bb56-198">FileAuthorization</span><span class="sxs-lookup"><span data-stu-id="8bb56-198">FileAuthorization</span></span>       | |
| <span data-ttu-id="8bb56-199">FormsAuthentication</span><span class="sxs-lookup"><span data-stu-id="8bb56-199">FormsAuthentication</span></span>     | <span data-ttu-id="8bb56-200">[Cookie 身份验证中间件](xref:security/authentication/cookie)</span><span class="sxs-lookup"><span data-stu-id="8bb56-200">[Cookie Authentication Middleware](xref:security/authentication/cookie)</span></span> |
| <span data-ttu-id="8bb56-201">OutputCache</span><span class="sxs-lookup"><span data-stu-id="8bb56-201">OutputCache</span></span>             | [<span data-ttu-id="8bb56-202">响应缓存中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-202">Response Caching Middleware</span></span>](xref:performance/caching/middleware) |
| <span data-ttu-id="8bb56-203">配置文件</span><span class="sxs-lookup"><span data-stu-id="8bb56-203">Profile</span></span>                 | |
| <span data-ttu-id="8bb56-204">RoleManager</span><span class="sxs-lookup"><span data-stu-id="8bb56-204">RoleManager</span></span>             | |
| <span data-ttu-id="8bb56-205">ScriptModule-4.0</span><span class="sxs-lookup"><span data-stu-id="8bb56-205">ScriptModule-4.0</span></span>        | |
| <span data-ttu-id="8bb56-206">会话</span><span class="sxs-lookup"><span data-stu-id="8bb56-206">Session</span></span>                 | [<span data-ttu-id="8bb56-207">会话中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-207">Session Middleware</span></span>](xref:fundamentals/app-state) |
| <span data-ttu-id="8bb56-208">UrlAuthorization</span><span class="sxs-lookup"><span data-stu-id="8bb56-208">UrlAuthorization</span></span>        | |
| <span data-ttu-id="8bb56-209">UrlMappingsModule</span><span class="sxs-lookup"><span data-stu-id="8bb56-209">UrlMappingsModule</span></span>       | [<span data-ttu-id="8bb56-210">URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="8bb56-210">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) |
| <span data-ttu-id="8bb56-211">UrlRoutingModule-4.0</span><span class="sxs-lookup"><span data-stu-id="8bb56-211">UrlRoutingModule-4.0</span></span>    | [ASP.NET Core Identity](xref:security/authentication/identity) |
| <span data-ttu-id="8bb56-212">WindowsAuthentication</span><span class="sxs-lookup"><span data-stu-id="8bb56-212">WindowsAuthentication</span></span>   | |

## <a name="iis-manager-application-changes"></a><span data-ttu-id="8bb56-213">IIS 管理器应用程序更改</span><span class="sxs-lookup"><span data-stu-id="8bb56-213">IIS Manager application changes</span></span>

<span data-ttu-id="8bb56-214">使用 IIS 管理器配置设置时，应用的 web.config 文件已更改。</span><span class="sxs-lookup"><span data-stu-id="8bb56-214">When using IIS Manager to configure settings, the *web.config* file of the app is changed.</span></span> <span data-ttu-id="8bb56-215">如果部署应用并包括 web.config，则使用 IIS 管理器所做的任何更改都会被部署的 web.config 文件覆盖。</span><span class="sxs-lookup"><span data-stu-id="8bb56-215">If deploying an app and including *web.config*, any changes made with IIS Manager are overwritten by the deployed *web.config* file.</span></span> <span data-ttu-id="8bb56-216">如果更改服务器的 web.config 文件，请立即将服务器上已更新的 web.config 文件复制到本地项目。</span><span class="sxs-lookup"><span data-stu-id="8bb56-216">If changes are made to the server's *web.config* file, copy the updated *web.config* file on the server to the local project immediately.</span></span>

## <a name="disabling-iis-modules"></a><span data-ttu-id="8bb56-217">禁用 IIS 模块</span><span class="sxs-lookup"><span data-stu-id="8bb56-217">Disabling IIS modules</span></span>

<span data-ttu-id="8bb56-218">如果在服务器级别配置了必须为应用禁用的 IIS 模块，则应用 web.config 文件还可以禁用该模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-218">If an IIS module is configured at the server level that must be disabled for an app, an addition to the app's *web.config* file can disable the module.</span></span> <span data-ttu-id="8bb56-219">将模块留在原位并使用配置设置（如果可用）将其停用或从应用中移除模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-219">Either leave the module in place and deactivate it using a configuration setting (if available) or remove the module from the app.</span></span>

### <a name="module-deactivation"></a><span data-ttu-id="8bb56-220">模块停用</span><span class="sxs-lookup"><span data-stu-id="8bb56-220">Module deactivation</span></span>

<span data-ttu-id="8bb56-221">许多模块提供一个配置设置，允许在不从应用中删除模块的情况下禁用它们。</span><span class="sxs-lookup"><span data-stu-id="8bb56-221">Many modules offer a configuration setting that allows them to be disabled without removing the module from the app.</span></span> <span data-ttu-id="8bb56-222">这是停用模块最简单、快捷的方式。</span><span class="sxs-lookup"><span data-stu-id="8bb56-222">This is the simplest and quickest way to deactivate a module.</span></span> <span data-ttu-id="8bb56-223">例如，可使用 web.config 中的 `<httpRedirect>` 元素禁用 HTTP 重定向模块：</span><span class="sxs-lookup"><span data-stu-id="8bb56-223">For example, the HTTP Redirection Module can be disabled with the `<httpRedirect>` element in *web.config*:</span></span>

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="8bb56-224">有关通过配置设置禁用模块的详细信息，请按照 [IIS \<system.webServer>](/iis/configuration/system.webServer/) 的“子元素”部分中的链接进行操作。</span><span class="sxs-lookup"><span data-stu-id="8bb56-224">For more information on disabling modules with configuration settings, follow the links in the *Child Elements* section of [IIS \<system.webServer>](/iis/configuration/system.webServer/).</span></span>

### <a name="module-removal"></a><span data-ttu-id="8bb56-225">模块删除</span><span class="sxs-lookup"><span data-stu-id="8bb56-225">Module removal</span></span>

<span data-ttu-id="8bb56-226">如果选择使用 web.config 中的设置删除模块，请先解锁此模块和 web.config 的 `<modules>` 部分 ：</span><span class="sxs-lookup"><span data-stu-id="8bb56-226">If opting to remove a module with a setting in *web.config*, unlock the module and unlock the `<modules>` section of *web.config* first:</span></span>

1. <span data-ttu-id="8bb56-227">解锁服务器级别的模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-227">Unlock the module at the server level.</span></span> <span data-ttu-id="8bb56-228">在 IIS 管理器“连接”边栏中选择 IIS 服务器。</span><span class="sxs-lookup"><span data-stu-id="8bb56-228">Select the IIS server in the IIS Manager **Connections** sidebar.</span></span> <span data-ttu-id="8bb56-229">打开 IIS 区域中的模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-229">Open the **Modules** in the **IIS** area.</span></span> <span data-ttu-id="8bb56-230">在列表中选择该模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-230">Select the module in the list.</span></span> <span data-ttu-id="8bb56-231">在右侧的“操作”边栏中，选择“解锁”。</span><span class="sxs-lookup"><span data-stu-id="8bb56-231">In the **Actions** sidebar on the right, select **Unlock**.</span></span> <span data-ttu-id="8bb56-232">如果该模块的操作条目显示为“锁定”，则该模块已被解锁，且无需任何操作。</span><span class="sxs-lookup"><span data-stu-id="8bb56-232">If the action entry for the module appears as **Lock**, the module is already unlocked, and no action is required.</span></span> <span data-ttu-id="8bb56-233">解锁尽可能多稍后计划从 web.config 中删除的模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-233">Unlock as many modules as you plan to remove from *web.config* later.</span></span>

2. <span data-ttu-id="8bb56-234">在不使用 web.config 的 `<modules>` 部分的情况下部署应用。如果使用包含 `<modules>` 部分的 web.config 部署应用，但未先在 IIS 管理器中解锁该部分，则配置管理器会在尝试解锁该部分时引发异常。</span><span class="sxs-lookup"><span data-stu-id="8bb56-234">Deploy the app without a `<modules>` section in *web.config*. If an app is deployed with a *web.config* containing the `<modules>` section without having unlocked the section first in the IIS Manager, the Configuration Manager throws an exception when attempting to unlock the section.</span></span> <span data-ttu-id="8bb56-235">因此，请在不使用 `<modules>` 部分的情况下部署该应用。</span><span class="sxs-lookup"><span data-stu-id="8bb56-235">Therefore, deploy the app without a `<modules>` section.</span></span>

3. <span data-ttu-id="8bb56-236">解锁 web.config 的 `<modules>` 部分。在“连接”边栏中，选择“站点”中的网站。</span><span class="sxs-lookup"><span data-stu-id="8bb56-236">Unlock the `<modules>` section of *web.config*. In the **Connections** sidebar, select the website in **Sites**.</span></span> <span data-ttu-id="8bb56-237">在“管理”区域中，打开“配置编辑器”。</span><span class="sxs-lookup"><span data-stu-id="8bb56-237">In the **Management** area, open the **Configuration Editor**.</span></span> <span data-ttu-id="8bb56-238">使用导航控件选择 `system.webServer/modules` 部分。</span><span class="sxs-lookup"><span data-stu-id="8bb56-238">Use the navigation controls to select the `system.webServer/modules` section.</span></span> <span data-ttu-id="8bb56-239">在右侧的“操作”边栏中，选择“解锁”该部分。</span><span class="sxs-lookup"><span data-stu-id="8bb56-239">In the **Actions** sidebar on the right, select to **Unlock** the section.</span></span> <span data-ttu-id="8bb56-240">如果该模块的操作条目显示为“锁定部分”，则该模块已被解锁，且无需任何操作。</span><span class="sxs-lookup"><span data-stu-id="8bb56-240">If the action entry for the module section appears as **Lock Section**, the module section is already unlocked, and no action is required.</span></span>

4. <span data-ttu-id="8bb56-241">可使用 `<remove>` 元素将 `<modules>` 部分添加到应用的本地 web.config 文件，从应用中删除模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-241">Add a `<modules>` section to the app's local *web.config* file with a `<remove>` element to remove the module from the app.</span></span> <span data-ttu-id="8bb56-242">可添加多个 `<remove>` 元素来删除多个模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-242">Add multiple `<remove>` elements to remove multiple modules.</span></span> <span data-ttu-id="8bb56-243">如果在服务器上更改 web.config，请立即在本地对项目的 web.config 文件执行相同的更改。</span><span class="sxs-lookup"><span data-stu-id="8bb56-243">If *web.config* changes are made on the server, immediately make the same changes to the project's *web.config* file locally.</span></span> <span data-ttu-id="8bb56-244">以这种方式删除模块不会影响模块的使用与服务器上的其他应用。</span><span class="sxs-lookup"><span data-stu-id="8bb56-244">Removing a module using this approach doesn't affect the use of the module with other apps on the server.</span></span>

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

<span data-ttu-id="8bb56-245">要使用 web.config 为 IIS Express 添加或删除模块，请修改 applicationHost.config 以解锁 `<modules>` 部分 ：</span><span class="sxs-lookup"><span data-stu-id="8bb56-245">In order to add or remove modules for IIS Express using *web.config*, modify *applicationHost.config* to unlock the `<modules>` section:</span></span>

1. <span data-ttu-id="8bb56-246">打开 {APPLICATION ROOT}\\.vs\config\applicationhost.config。</span><span class="sxs-lookup"><span data-stu-id="8bb56-246">Open *{APPLICATION ROOT}\\.vs\config\applicationhost.config*.</span></span>

1. <span data-ttu-id="8bb56-247">找到 IIS 模块的 `<section>` 元素，并将 `overrideModeDefault` 从 `Deny` 更改为 `Allow`：</span><span class="sxs-lookup"><span data-stu-id="8bb56-247">Locate the `<section>` element for IIS modules and change `overrideModeDefault` from `Deny` to `Allow`:</span></span>

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. <span data-ttu-id="8bb56-248">找到 `<location path="" overrideMode="Allow"><system.webServer><modules>` 部分。</span><span class="sxs-lookup"><span data-stu-id="8bb56-248">Locate the `<location path="" overrideMode="Allow"><system.webServer><modules>` section.</span></span> <span data-ttu-id="8bb56-249">对于任何要删除的模块，请将 `lockItem` 从 `true` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="8bb56-249">For any modules that you wish to remove, set `lockItem` from `true` to `false`.</span></span> <span data-ttu-id="8bb56-250">在以下示例中，已解锁 CGI 模块：</span><span class="sxs-lookup"><span data-stu-id="8bb56-250">In the following example, the CGI Module is unlocked:</span></span>

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. <span data-ttu-id="8bb56-251">解锁 `<modules>` 部分和单个模块后，可使用应用的 web.config 文件添加或删除 IIS 模块，以便在 IIS Express 上运行应用。</span><span class="sxs-lookup"><span data-stu-id="8bb56-251">After the `<modules>` section and individual modules are unlocked, you're free to add or remove IIS modules using the app's *web.config* file for running the app on IIS Express.</span></span>

<span data-ttu-id="8bb56-252">IIS 模块还可以使用 Appcmd.exe 删除。</span><span class="sxs-lookup"><span data-stu-id="8bb56-252">An IIS module can also be removed with *Appcmd.exe*.</span></span> <span data-ttu-id="8bb56-253">该命令中提供 `MODULE_NAME` 和 `APPLICATION_NAME`：</span><span class="sxs-lookup"><span data-stu-id="8bb56-253">Provide the `MODULE_NAME` and `APPLICATION_NAME` in the command:</span></span>

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

<span data-ttu-id="8bb56-254">例如，从默认网站中删除 `DynamicCompressionModule`：</span><span class="sxs-lookup"><span data-stu-id="8bb56-254">For example, remove the `DynamicCompressionModule` from the Default Web Site:</span></span>

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a><span data-ttu-id="8bb56-255">最小模块配置</span><span class="sxs-lookup"><span data-stu-id="8bb56-255">Minimum module configuration</span></span>

<span data-ttu-id="8bb56-256">要求运行 ASP.NET Core 应用的模块只有匿名身份验证模块和 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-256">The only modules required to run an ASP.NET Core app are the Anonymous Authentication Module and the ASP.NET Core Module.</span></span>

<span data-ttu-id="8bb56-257">URI 缓存模块 (`UriCacheModule`) 允许 IIS 在 URL 级别缓存网站配置。</span><span class="sxs-lookup"><span data-stu-id="8bb56-257">The URI Caching Module (`UriCacheModule`) allows IIS to cache website configuration at the URL level.</span></span> <span data-ttu-id="8bb56-258">不使用此模块的话，即使重复请求相同的 URL，IIS 也必须读取并分析每个请求的配置。</span><span class="sxs-lookup"><span data-stu-id="8bb56-258">Without this module, IIS must read and parse configuration on every request, even when the same URL is repeatedly requested.</span></span> <span data-ttu-id="8bb56-259">分析每个请求的配置会导致严重的性能损失。</span><span class="sxs-lookup"><span data-stu-id="8bb56-259">Parsing the configuration every request results in a significant performance penalty.</span></span> <span data-ttu-id="8bb56-260">虽然托管的 ASP.NET Core 应用并非严格要求运行 URI 缓存模块，但我们建议为所有 ASP.NET Core 部署启用 URI 缓存模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-260">*Although the URI Caching Module isn't strictly required for a hosted ASP.NET Core app to run, we recommend that the URI Caching Module be enabled for all ASP.NET Core deployments.*</span></span>

<span data-ttu-id="8bb56-261">HTTP 缓存模块 (`HttpCacheModule`) 实现 IIS 输出缓存以及用于在 HTTP.sys 缓存中缓存项目的逻辑。</span><span class="sxs-lookup"><span data-stu-id="8bb56-261">The HTTP Caching Module (`HttpCacheModule`) implements the IIS output cache and also the logic for caching items in the HTTP.sys cache.</span></span> <span data-ttu-id="8bb56-262">如果不使用此模块，内容不再以内核模式缓存，并且缓存配置文件将被忽略。</span><span class="sxs-lookup"><span data-stu-id="8bb56-262">Without this module, content is no longer cached in kernel mode, and cache profiles are ignored.</span></span> <span data-ttu-id="8bb56-263">删除 HTTP 缓存模块通常会对性能和资源使用情况产生不利影响。</span><span class="sxs-lookup"><span data-stu-id="8bb56-263">Removing the HTTP Caching Module usually has adverse effects on performance and resource usage.</span></span> <span data-ttu-id="8bb56-264">虽然托管的 ASP.NET Core 应用程序并非严格要求运行 HTTP 缓存模块，但我们建议为所有 ASP.NET Core 部署启用 HTTP 缓存模块。</span><span class="sxs-lookup"><span data-stu-id="8bb56-264">*Although the HTTP Caching Module isn't strictly required for a hosted ASP.NET Core app to run, we recommend that the HTTP Caching Module be enabled for all ASP.NET Core deployments.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8bb56-265">其他资源</span><span class="sxs-lookup"><span data-stu-id="8bb56-265">Additional resources</span></span>

* [<span data-ttu-id="8bb56-266">IIS 体系结构简介：IIS 中的模块</span><span class="sxs-lookup"><span data-stu-id="8bb56-266">Introduction to IIS Architectures: Modules in IIS</span></span>](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [<span data-ttu-id="8bb56-267">IIS 模块概述</span><span class="sxs-lookup"><span data-stu-id="8bb56-267">IIS Modules Overview</span></span>](/iis/get-started/introduction-to-iis/iis-modules-overview)
* <span data-ttu-id="8bb56-268">[自定义 IIS 7.0 角色和模块](/previous-versions/tn-archive/cc627313(v=technet.10))</span><span class="sxs-lookup"><span data-stu-id="8bb56-268">[Customizing IIS 7.0 Roles and Modules](/previous-versions/tn-archive/cc627313(v=technet.10))</span></span>
* [<span data-ttu-id="8bb56-269">IIS \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="8bb56-269">IIS \<system.webServer></span></span>](/iis/configuration/system.webServer/)