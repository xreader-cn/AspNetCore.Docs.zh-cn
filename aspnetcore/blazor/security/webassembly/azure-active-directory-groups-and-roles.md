---
title: ASP.NET Core Blazor WebAssembly 与 Azure Active Directory 组和角色
author: guardrex
description: 了解如何配置 Blazor WebAssembly 以使用 Azure Active Directory 组和角色。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: ded70f028b3021574ba260838837d9b23abd72f1
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981877"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="c855f-103">Azure Active Directory (AAD) 组、管理员角色和用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="c855f-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="c855f-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="c855f-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="c855f-105">本文适用于带有 Microsoft Identity v1.0 的 Blazor ASP.NET Core 应用 3.1 版，并计划更新为带有 Identity v2.0 的 5.0。</span><span class="sxs-lookup"><span data-stu-id="c855f-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity v1.0 and is scheduled for update to 5.0 with Identity v2.0.</span></span> <span data-ttu-id="c855f-106">有关详细信息，请参阅[具有 AAD/B2C 组和角色的 Blazor WASM (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)。</span><span class="sxs-lookup"><span data-stu-id="c855f-106">For more information, see [Blazor WASM with AAD/B2C groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683).</span></span>

<span data-ttu-id="c855f-107">Azure Active Directory (AAD) 提供了多种授权方法，它们可与 ASP.NET Core Identity 结合使用：</span><span class="sxs-lookup"><span data-stu-id="c855f-107">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="c855f-108">用户定义的组</span><span class="sxs-lookup"><span data-stu-id="c855f-108">User-defined groups</span></span>
  * <span data-ttu-id="c855f-109">安全性</span><span class="sxs-lookup"><span data-stu-id="c855f-109">Security</span></span>
  * <span data-ttu-id="c855f-110">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="c855f-110">Microsoft 365</span></span>
  * <span data-ttu-id="c855f-111">分布</span><span class="sxs-lookup"><span data-stu-id="c855f-111">Distribution</span></span>
* <span data-ttu-id="c855f-112">角色</span><span class="sxs-lookup"><span data-stu-id="c855f-112">Roles</span></span>
  * <span data-ttu-id="c855f-113">AAD 管理员角色</span><span class="sxs-lookup"><span data-stu-id="c855f-113">AAD Administrator Roles</span></span>
  * <span data-ttu-id="c855f-114">用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="c855f-114">User-defined roles</span></span>

<span data-ttu-id="c855f-115">本文中的指南适用于以下主题中所述的 Blazor WebAssembly AAD 部署方案：</span><span class="sxs-lookup"><span data-stu-id="c855f-115">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="c855f-116">包含 Microsoft 帐户的独立产品</span><span class="sxs-lookup"><span data-stu-id="c855f-116">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="c855f-117">包含 AAD 的独立产品</span><span class="sxs-lookup"><span data-stu-id="c855f-117">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="c855f-118">由 AAD 托管</span><span class="sxs-lookup"><span data-stu-id="c855f-118">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="c855f-119">作用域</span><span class="sxs-lookup"><span data-stu-id="c855f-119">Scopes</span></span>

<span data-ttu-id="c855f-120">具有五个以上 AAD 管理员角色和安全组成员身份的任何应用用户都需要调用 [Microsoft 图形 API](/graph/use-the-api)。</span><span class="sxs-lookup"><span data-stu-id="c855f-120">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="c855f-121">若要允许 Graph API 调用，请在 Azure 门户中向托管 Blazor 解决方案的独立应用或 `Client` 应用授予以下任意 [Graph API 权限（范围）](/graph/permissions-reference)：</span><span class="sxs-lookup"><span data-stu-id="c855f-121">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="c855f-122">`Directory.Read.All` 是具有最低特权的范围，本文中的示例使用此范围。</span><span class="sxs-lookup"><span data-stu-id="c855f-122">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="c855f-123">用户定义的组和管理员角色</span><span class="sxs-lookup"><span data-stu-id="c855f-123">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="c855f-124">要在 Azure 门户中配置应用以提供 `groups` 成员资格声明，请参阅以下 Azure 文章。</span><span class="sxs-lookup"><span data-stu-id="c855f-124">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="c855f-125">将用户分配到用户定义的 AAD 组和 AAD 管理员角色。</span><span class="sxs-lookup"><span data-stu-id="c855f-125">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="c855f-126">使用 Azure AD 安全组的角色</span><span class="sxs-lookup"><span data-stu-id="c855f-126">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="c855f-127">`groupMembershipClaims` 属性</span><span class="sxs-lookup"><span data-stu-id="c855f-127">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="c855f-128">以下示例假定用户被分配到 AAD 计费管理员角色。</span><span class="sxs-lookup"><span data-stu-id="c855f-128">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="c855f-129">AAD 发送的单个 `groups` 声明在 JSON 数组中将用户的组和角色作为对象 ID (GUID) 显示。</span><span class="sxs-lookup"><span data-stu-id="c855f-129">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="c855f-130">应用必须将组和角色的 JSON 数组转换为单个 `group` 声明，应用可以针对这些声明生成[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="c855f-130">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="c855f-131">当分配的 AAD 管理员角色和用户定义的组的数量超过五个时，AAD 发送具有 `true` 值的 `hasgroups` 声明，而不是发送 `groups` 声明。</span><span class="sxs-lookup"><span data-stu-id="c855f-131">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="c855f-132">任何可能为其用户分配了五个以上角色和组的应用都必须单独调用图形 API 才能获取用户的角色和组。</span><span class="sxs-lookup"><span data-stu-id="c855f-132">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="c855f-133">本文中提供的示例实现就是针对这种情况的。</span><span class="sxs-lookup"><span data-stu-id="c855f-133">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="c855f-134">有关详细信息，请参阅 [Microsoft 标识平台访问令牌：负载声明](/azure/active-directory/develop/access-tokens#payload-claims)一文中的 `groups` 和 `hasgroups` 声明信息。</span><span class="sxs-lookup"><span data-stu-id="c855f-134">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="c855f-135">扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 以包括组和角色的数组属性。</span><span class="sxs-lookup"><span data-stu-id="c855f-135">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="c855f-136">为每个属性分配一个空数组，这样以后在 `foreach` 循环中使用这些属性时，就不需要检查 `null` 了。</span><span class="sxs-lookup"><span data-stu-id="c855f-136">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="c855f-137">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="c855f-137">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c855f-138">通过以下任意一种方法来为 AAD 组和角色创建声明：</span><span class="sxs-lookup"><span data-stu-id="c855f-138">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="c855f-139">使用 Graph SDK</span><span class="sxs-lookup"><span data-stu-id="c855f-139">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="c855f-140">使用命名 `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="c855f-140">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="c855f-141">使用 Graph SDK</span><span class="sxs-lookup"><span data-stu-id="c855f-141">Use the Graph SDK</span></span>

<span data-ttu-id="c855f-142">将包引用添加到 [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) 托管 Blazor 解决方案的独立应用或 `Client` 应用。</span><span class="sxs-lookup"><span data-stu-id="c855f-142">Add a package reference to the standalone app or *`Client`* app of a hosted Blazor solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="c855f-143">添加 <xref:blazor/security/webassembly/graph-api#graph-sdk> 一文中“Graph SDK”部分的 Graph SDK 实用工具类和配置。</span><span class="sxs-lookup"><span data-stu-id="c855f-143">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="c855f-144">将下面的自定义用户帐户工厂添加到托管 Blazor 解决方案的独立应用或 `Client` 应用 (`CustomAccountFactory.cs`)。</span><span class="sxs-lookup"><span data-stu-id="c855f-144">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted Blazor solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="c855f-145">使用自定义用户工厂来处理角色和组声明。</span><span class="sxs-lookup"><span data-stu-id="c855f-145">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="c855f-146">`roles` 声明数组在[用户定义的角色](#user-defined-roles)部分介绍。</span><span class="sxs-lookup"><span data-stu-id="c855f-146">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="c855f-147">如果存在 `hasgroups` 声明，则使用 Graph SDK 向 Graph API 发出授权请求，以获取用户的角色和组：</span><span class="sxs-lookup"><span data-stu-id="c855f-147">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="c855f-148">上面的代码不包含可传递的成员身份。</span><span class="sxs-lookup"><span data-stu-id="c855f-148">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="c855f-149">如果应用需要直接和可传递的组成员身份声明，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c855f-149">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="c855f-150">将 `groupsAndAzureRoles` 的 `IUserMemberOfCollectionWithReferencesPage` 类型更改为 `IUserTransitiveMemberOfCollectionWithReferencesPage`。</span><span class="sxs-lookup"><span data-stu-id="c855f-150">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="c855f-151">请求用户的组和角色时，请将 `MemberOf` 属性替换为 `TransitiveMemberOf`。</span><span class="sxs-lookup"><span data-stu-id="c855f-151">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="c855f-152">在 `Program.Main` (`Program.cs`) 中，将 MSAL 身份验证配置为使用自定义用户帐户工厂：如果应用使用扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类，请使用以下代码将自定义用户帐户类替换为 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>：</span><span class="sxs-lookup"><span data-stu-id="c855f-152">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

### <a name="use-a-named-httpclient"></a><span data-ttu-id="c855f-153">使用命名 `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="c855f-153">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="c855f-154">在托管的 Blazor 解决方案的独立应用或 `Client` 应用中，创建自定义 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 类。</span><span class="sxs-lookup"><span data-stu-id="c855f-154">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="c855f-155">对获取角色和组信息的 Graph API 调用使用正确的范围。</span><span class="sxs-lookup"><span data-stu-id="c855f-155">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="c855f-156">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="c855f-156">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

<span data-ttu-id="c855f-157">在 `Program.Main` (`Program.cs`)中，添加 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 实现服务，并添加一个已命名的 <xref:System.Net.Http.HttpClient> 以发出图形 API 请求。</span><span class="sxs-lookup"><span data-stu-id="c855f-157">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="c855f-158">下面的示例将命名客户端 `GraphAPI`：</span><span class="sxs-lookup"><span data-stu-id="c855f-158">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="c855f-159">创建 AAD 目录对象类以接收来自图形 API 调用的 Open Data Protocol (OData) 角色和组。</span><span class="sxs-lookup"><span data-stu-id="c855f-159">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="c855f-160">OData 以 JSON 格式送达，并且对 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 的调用将填充 `DirectoryObjects` 类的实例。</span><span class="sxs-lookup"><span data-stu-id="c855f-160">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="c855f-161">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="c855f-161">`DirectoryObjects.cs`:</span></span>

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

<span data-ttu-id="c855f-162">创建一个自定义用户工厂来处理角色和组声明。</span><span class="sxs-lookup"><span data-stu-id="c855f-162">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="c855f-163">下面的示例实现还处理 `roles` 声明数组，[用户定义角色](#user-defined-roles)一节中对此进行了说明。</span><span class="sxs-lookup"><span data-stu-id="c855f-163">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="c855f-164">如果存在 `hasgroups` 声明，则使用已命名的 <xref:System.Net.Http.HttpClient> 向提供图形 API 发出授权请求，以获取用户的角色和组。</span><span class="sxs-lookup"><span data-stu-id="c855f-164">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="c855f-165">此实现使用 Microsoft Identity Platform v1.0 终结点 `https://graph.microsoft.com/v1.0/me/memberOf`（[API 文档](/graph/api/user-list-memberof)）。</span><span class="sxs-lookup"><span data-stu-id="c855f-165">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="c855f-166">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="c855f-166">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {Message}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="c855f-167">无需提供代码来删除原始 `groups` 声明（如果存在），因为框架会自动删除它。</span><span class="sxs-lookup"><span data-stu-id="c855f-167">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="c855f-168">本示例中的方法执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c855f-168">The approach in this example:</span></span>
>
> * <span data-ttu-id="c855f-169">添加一个自定义 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 类，将访问令牌附加到传出请求。</span><span class="sxs-lookup"><span data-stu-id="c855f-169">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="c855f-170">添加一个已命名的 <xref:System.Net.Http.HttpClient>，将 Web API 请求发送到安全的外部 Web API 终结点。</span><span class="sxs-lookup"><span data-stu-id="c855f-170">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="c855f-171">使用已命名的 <xref:System.Net.Http.HttpClient> 发出授权请求。</span><span class="sxs-lookup"><span data-stu-id="c855f-171">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="c855f-172">有关此方法的常规覆盖范围，请参阅 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>一文。</span><span class="sxs-lookup"><span data-stu-id="c855f-172">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="c855f-173">在托管的 Blazor 解决方案的独立应用或 `Client` 应用的 `Program.Main` (`Program.cs`) 中注册工厂。</span><span class="sxs-lookup"><span data-stu-id="c855f-173">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="c855f-174">同意将 `Directory.Read.All` 范围作为应用的附加范围：</span><span class="sxs-lookup"><span data-stu-id="c855f-174">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

## <a name="authorization-configuration"></a><span data-ttu-id="c855f-175">授权配置</span><span class="sxs-lookup"><span data-stu-id="c855f-175">Authorization configuration</span></span>

<span data-ttu-id="c855f-176">为 `Program.Main` 中的每个组或角色创建[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="c855f-176">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="c855f-177">以下示例为 AAD 计费管理员角色创建策略：</span><span class="sxs-lookup"><span data-stu-id="c855f-177">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="c855f-178">有关 AAD 角色对象 ID 的完整列表，请参阅 [AAD 管理员角色对象 ID](#aad-administrator-role-object-ids) 部分。</span><span class="sxs-lookup"><span data-stu-id="c855f-178">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="c855f-179">在以下示例中，应用使用前面的策略来授权用户。</span><span class="sxs-lookup"><span data-stu-id="c855f-179">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="c855f-180">[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)适用于以下策略：</span><span class="sxs-lookup"><span data-stu-id="c855f-180">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="c855f-181">对整个组件的访问可以基于使用 [`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) 的策略：</span><span class="sxs-lookup"><span data-stu-id="c855f-181">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="c855f-182">如果用户未登录，则会将他们重定向到 AAD 登录页，然后在他们登录后再返回到组件。</span><span class="sxs-lookup"><span data-stu-id="c855f-182">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="c855f-183">策略检查也可以[在具有过程逻辑的代码中执行](xref:blazor/security/index#procedural-logic)：</span><span class="sxs-lookup"><span data-stu-id="c855f-183">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="c855f-184">授权用户定义的组和管理员角色访问服务器 API</span><span class="sxs-lookup"><span data-stu-id="c855f-184">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="c855f-185">除了在客户端 WebAssembly 应用中授权用户访问页面和资源以外，服务器 API 还可以授权用户访问安全 API 终结点。</span><span class="sxs-lookup"><span data-stu-id="c855f-185">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="c855f-186">在服务器应用验证用户的访问令牌后：</span><span class="sxs-lookup"><span data-stu-id="c855f-186">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="c855f-187">服务器 API 应用使用用户的不可变[对象标识符声明 (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims)（来自访问令牌）来获取 Graph API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c855f-187">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="c855f-188">Graph API 调用通过调用用户的 [`memberOf`](/graph/api/user-list-memberof) 来获取用户的 Azure 用户定义安全组和管理员角色成员身份。</span><span class="sxs-lookup"><span data-stu-id="c855f-188">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="c855f-189">成员身份用于建立 `group` 声明。</span><span class="sxs-lookup"><span data-stu-id="c855f-189">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="c855f-190">[授权策略](xref:security/authorization/policies)可用于限制用户在整个应用中对服务器 API 终结点的访问。</span><span class="sxs-lookup"><span data-stu-id="c855f-190">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="c855f-191">本指南目前不涵盖根据用户的 [AAD 用户定义的角色](#user-defined-roles)对用户进行授权。</span><span class="sxs-lookup"><span data-stu-id="c855f-191">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="c855f-192">本部分的指南将服务器 API 应用配置为适用于 Microsoft Graph API 调用的[守护程序应用](/azure/active-directory/develop/scenario-daemon-overview)。</span><span class="sxs-lookup"><span data-stu-id="c855f-192">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="c855f-193">此方法不：</span><span class="sxs-lookup"><span data-stu-id="c855f-193">This approach does **not**:</span></span>

* <span data-ttu-id="c855f-194">需要 `access_as_user` 范围。</span><span class="sxs-lookup"><span data-stu-id="c855f-194">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="c855f-195">代表发出 API 请求的用户/客户端访问 Graph API。</span><span class="sxs-lookup"><span data-stu-id="c855f-195">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="c855f-196">服务器 API 应用对 Graph API 的调用只需要服务器 API 应用在 Azure 门户中拥有 `Directory.Read.All` 的应用程序 Graph API 范围。</span><span class="sxs-lookup"><span data-stu-id="c855f-196">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="c855f-197">此方法可确保客户端应用无法访问服务器 API 不显式允许的目录数据。</span><span class="sxs-lookup"><span data-stu-id="c855f-197">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="c855f-198">客户端应用只能访问服务器 API 应用的控制器终结点。</span><span class="sxs-lookup"><span data-stu-id="c855f-198">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="c855f-199">Azure 配置</span><span class="sxs-lookup"><span data-stu-id="c855f-199">Azure configuration</span></span>

* <span data-ttu-id="c855f-200">确认为服务器应用注册授予了 `Directory.Read.All` 的应用程序（非委派）Graph API 范围，这是安全组的最低特权访问级别 。</span><span class="sxs-lookup"><span data-stu-id="c855f-200">Confirm that the *Server* app registration is given **Application** (not **Delegated**) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="c855f-201">确认在分配范围后，将管理员同意应用于该范围。</span><span class="sxs-lookup"><span data-stu-id="c855f-201">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="c855f-202">向服务器应用分配新的客户端密码。</span><span class="sxs-lookup"><span data-stu-id="c855f-202">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="c855f-203">请留意[应用设置](#app-settings)部分中应用配置的密码。</span><span class="sxs-lookup"><span data-stu-id="c855f-203">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="c855f-204">应用设置</span><span class="sxs-lookup"><span data-stu-id="c855f-204">App settings</span></span>

<span data-ttu-id="c855f-205">在应用设置文件（`appsettings.json` 或 `appsettings.Production.json`）中，使用服务器应用的客户端密码从 Azure 门户创建 `ClientSecret` 条目：</span><span class="sxs-lookup"><span data-stu-id="c855f-205">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="c855f-206">例如：</span><span class="sxs-lookup"><span data-stu-id="c855f-206">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="c855f-207">如果未验证租户发布者域，则用户/客户端访问的服务器 API 范围将使用基于 `https://` 的 URI。</span><span class="sxs-lookup"><span data-stu-id="c855f-207">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="c855f-208">在这种情况下，服务器 API 应用需要在 `appsettings.json` 文件中配置 `Audience`。</span><span class="sxs-lookup"><span data-stu-id="c855f-208">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="c855f-209">在下面的配置中，`Audience` 值的末尾不包含默认范围 `/{DEFAULT SCOPE}`，其中占位符 `{DEFAULT SCOPE}` 是默认范围：</span><span class="sxs-lookup"><span data-stu-id="c855f-209">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> <span data-ttu-id="c855f-210">在上面的示例配置中：</span><span class="sxs-lookup"><span data-stu-id="c855f-210">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="c855f-211">租户域为 `contoso.onmicrosoft.com`。</span><span class="sxs-lookup"><span data-stu-id="c855f-211">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="c855f-212">服务器 API 应用 `ClientId` 为 `41451fa7-82d9-4673-8fa5-69eff5a761fd`。</span><span class="sxs-lookup"><span data-stu-id="c855f-212">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="c855f-213">如果应用已验证发布者域并且具有基于 `api://` 的 API 范围，则通常不需要显式配置 `Audience`。</span><span class="sxs-lookup"><span data-stu-id="c855f-213">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="c855f-214">有关详细信息，请参阅 <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>。</span><span class="sxs-lookup"><span data-stu-id="c855f-214">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="c855f-215">授权策略</span><span class="sxs-lookup"><span data-stu-id="c855f-215">Authorization policies</span></span>

<span data-ttu-id="c855f-216">基于组对象 ID 和 [AAD 管理员角色对象 ID](#aad-administrator-role-object-ids)，为服务器应用的 `Startup.ConfigureServices` (`Startup.cs`) 中的 AAD 安全组和 AAD 管理员角色创建[授权策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="c855f-216">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="c855f-217">例如，Azure 计费管理员角色策略具有以下配置：</span><span class="sxs-lookup"><span data-stu-id="c855f-217">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="c855f-218">有关详细信息，请参阅 <xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="c855f-218">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="c855f-219">控制器访问</span><span class="sxs-lookup"><span data-stu-id="c855f-219">Controller access</span></span>

<span data-ttu-id="c855f-220">需要服务器应用控制器的策略。</span><span class="sxs-lookup"><span data-stu-id="c855f-220">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="c855f-221">下面的示例使用名为 `BillingAdmin` 的策略，将对 `BillingDataController` 中计费数据的访问限制为 Azure 计费管理员，如[授权策略](#authorization-policies)部分配置的那样：</span><span class="sxs-lookup"><span data-stu-id="c855f-221">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="c855f-222">包</span><span class="sxs-lookup"><span data-stu-id="c855f-222">Packages</span></span>

<span data-ttu-id="c855f-223">将包引用添加到以下包的服务器应用中：</span><span class="sxs-lookup"><span data-stu-id="c855f-223">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="c855f-224">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="c855f-224">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="c855f-225">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="c855f-225">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span></span>

### <a name="services"></a><span data-ttu-id="c855f-226">服务</span><span class="sxs-lookup"><span data-stu-id="c855f-226">Services</span></span>

<span data-ttu-id="c855f-227">在服务器应用的 `Startup.ConfigureServices` 方法中，服务器应用的 `Startup` 类中的代码需要更多命名空间 。</span><span class="sxs-lookup"><span data-stu-id="c855f-227">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="c855f-228">将以下命名空间添加到 `Startup.cs`：</span><span class="sxs-lookup"><span data-stu-id="c855f-228">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="c855f-229">配置 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> 时：</span><span class="sxs-lookup"><span data-stu-id="c855f-229">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="c855f-230">可以选择包括对 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> 的处理。</span><span class="sxs-lookup"><span data-stu-id="c855f-230">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c855f-231">例如，应用可以记录失败的身份验证。</span><span class="sxs-lookup"><span data-stu-id="c855f-231">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="c855f-232">在 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> 中，进行图形 API 调用以获取用户的组和角色。</span><span class="sxs-lookup"><span data-stu-id="c855f-232">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="c855f-233"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> 在日志消息中提供个人身份信息 (PII)。</span><span class="sxs-lookup"><span data-stu-id="c855f-233"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="c855f-234">只能用测试用户帐号激活 PII 进行调试。</span><span class="sxs-lookup"><span data-stu-id="c855f-234">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="c855f-235">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="c855f-235">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

<span data-ttu-id="c855f-236">在上面的代码中，可选择处理以下令牌错误：</span><span class="sxs-lookup"><span data-stu-id="c855f-236">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="c855f-237">`MsalUiRequiredException`：应用没有足够的权限（范围）。</span><span class="sxs-lookup"><span data-stu-id="c855f-237">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="c855f-238">确定 Azure 门户中的服务器 API 应用范围是否包括 `Directory.Read.All` 的应用程序权限。</span><span class="sxs-lookup"><span data-stu-id="c855f-238">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="c855f-239">确认已向租户管理员授予应用权限。</span><span class="sxs-lookup"><span data-stu-id="c855f-239">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="c855f-240">`MsalServiceException` (`AADSTS70011`)：确认范围为 `https://graph.microsoft.com/.default`。</span><span class="sxs-lookup"><span data-stu-id="c855f-240">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="c855f-241">包</span><span class="sxs-lookup"><span data-stu-id="c855f-241">Packages</span></span>

<span data-ttu-id="c855f-242">将包引用添加到以下包的服务器应用中：</span><span class="sxs-lookup"><span data-stu-id="c855f-242">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="c855f-243">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="c855f-243">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="c855f-244">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="c855f-244">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="c855f-245">服务配置</span><span class="sxs-lookup"><span data-stu-id="c855f-245">Service configuration</span></span>

<span data-ttu-id="c855f-246">在服务器应用的 `Startup.ConfigureServices` 方法中，添加逻辑以进行图形 API 调用，并为用户的安全组和角色建立用户 `group` 声明。</span><span class="sxs-lookup"><span data-stu-id="c855f-246">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="c855f-247">本节中的示例代码使用基于 Microsoft Identity Platform v1.0 的 Active Directory 身份验证库 (ADAL)。</span><span class="sxs-lookup"><span data-stu-id="c855f-247">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span>

<span data-ttu-id="c855f-248">服务器应用的 `Startup` 类中的代码需要更多命名空间。</span><span class="sxs-lookup"><span data-stu-id="c855f-248">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="c855f-249">下面的一组 `using` 语句包含本节下面的代码所需的命名空间：</span><span class="sxs-lookup"><span data-stu-id="c855f-249">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="c855f-250">配置 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> 时：</span><span class="sxs-lookup"><span data-stu-id="c855f-250">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="c855f-251">可以选择包括对 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> 的处理。</span><span class="sxs-lookup"><span data-stu-id="c855f-251">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c855f-252">例如，应用可以记录失败的身份验证。</span><span class="sxs-lookup"><span data-stu-id="c855f-252">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="c855f-253">在 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> 中，进行图形 API 调用以获取用户的组和角色。</span><span class="sxs-lookup"><span data-stu-id="c855f-253">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="c855f-254"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> 在日志消息中提供个人身份信息 (PII)。</span><span class="sxs-lookup"><span data-stu-id="c855f-254"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="c855f-255">只能用测试用户帐号激活 PII 进行调试。</span><span class="sxs-lookup"><span data-stu-id="c855f-255">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="c855f-256">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="c855f-256">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="c855f-257">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="c855f-257">In the preceding example:</span></span>

* <span data-ttu-id="c855f-258">首先尝试静默令牌获取 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>)，因为访问令牌可能已经存储在 ADAL 令牌缓存中。</span><span class="sxs-lookup"><span data-stu-id="c855f-258">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="c855f-259">从缓存中获取令牌比请求新令牌更快。</span><span class="sxs-lookup"><span data-stu-id="c855f-259">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="c855f-260">如果访问令牌不是从缓存中获取的（引发 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> 或 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType>），则使用客户端凭据 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) 生成用户断言 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>)，以代表用户 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>) 获取令牌。</span><span class="sxs-lookup"><span data-stu-id="c855f-260">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="c855f-261">接下来，`Microsoft.Graph.GraphServiceClient` 可以继续使用令牌进行图形 API 调用。</span><span class="sxs-lookup"><span data-stu-id="c855f-261">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="c855f-262">令牌放置在 ADAL 令牌缓存中。</span><span class="sxs-lookup"><span data-stu-id="c855f-262">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="c855f-263">对于同一用户的未来图形 API 调用，将通过 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> 以无提示方式从缓存中获取令牌。</span><span class="sxs-lookup"><span data-stu-id="c855f-263">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="c855f-264"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> 中的代码不会获得可传递的成员身份。</span><span class="sxs-lookup"><span data-stu-id="c855f-264">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="c855f-265">若要更改代码以获取直接和可传递的成员身份：</span><span class="sxs-lookup"><span data-stu-id="c855f-265">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="c855f-266">对于代码行：</span><span class="sxs-lookup"><span data-stu-id="c855f-266">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="c855f-267">将上面的行替换为：</span><span class="sxs-lookup"><span data-stu-id="c855f-267">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="c855f-268">对于代码行：</span><span class="sxs-lookup"><span data-stu-id="c855f-268">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="c855f-269">将上面的行替换为：</span><span class="sxs-lookup"><span data-stu-id="c855f-269">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="c855f-270">创建声明时，<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> 中的代码不会区分 AAD 安全组和 AAD 管理员角色。</span><span class="sxs-lookup"><span data-stu-id="c855f-270">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="c855f-271">若要使应用区分组和角色，请在循环访问组和角色时检查 `entry.ODataType`。</span><span class="sxs-lookup"><span data-stu-id="c855f-271">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="c855f-272">若要创建单独的安全组和角色声明，请使用类似于下面的代码：</span><span class="sxs-lookup"><span data-stu-id="c855f-272">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="c855f-273">用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="c855f-273">User-defined roles</span></span>

<span data-ttu-id="c855f-274">还可以将 AAD 注册应用配置为使用用户定义的角色。</span><span class="sxs-lookup"><span data-stu-id="c855f-274">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="c855f-275">要在 Azure 门户中配置应用以提供 `roles` 成员资格声明，请参阅 Azure 文档中的[如何：在应用程序中添加应用角色并在令牌中接收它们](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)。</span><span class="sxs-lookup"><span data-stu-id="c855f-275">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="c855f-276">以下示例假定应用配置了两个角色：</span><span class="sxs-lookup"><span data-stu-id="c855f-276">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="c855f-277">尽管无法将角色分配给没有 Azure AD Premium 帐户的安全组，但你可以将用户分配给角色，并为具有标准 Azure 帐户的用户接收 `roles` 声明。</span><span class="sxs-lookup"><span data-stu-id="c855f-277">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="c855f-278">本部分中的指南不需要 Azure AD Premium 帐户。</span><span class="sxs-lookup"><span data-stu-id="c855f-278">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="c855f-279">通过为其他每个角色分配重新添加用户，在 Azure 门户中分配多个角色。</span><span class="sxs-lookup"><span data-stu-id="c855f-279">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="c855f-280">AAD 发送的单个 `roles` 声明在 JSON 数组中将用户定义的角色作为 `appRoles` 的 `value` 显示。</span><span class="sxs-lookup"><span data-stu-id="c855f-280">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="c855f-281">应用必须将 JSON 角色数组转换为单个 `role` 声明。</span><span class="sxs-lookup"><span data-stu-id="c855f-281">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="c855f-282">[用户定义的组和 AAD 管理员角色](#user-defined-groups-and-administrator-roles)部分中显示的 `CustomUserFactory` 设置为对具有 JSON 数组值的 `roles` 声明执行操作。</span><span class="sxs-lookup"><span data-stu-id="c855f-282">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="c855f-283">在托管的 Blazor 解决方案的独立应用或 `Client` 应用中添加和注册 `CustomUserFactory`，如[用户定义的组和 AAD 管理员角色](#user-defined-groups-and-administrator-roles)部分中所述。</span><span class="sxs-lookup"><span data-stu-id="c855f-283">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="c855f-284">无需提供代码来删除原始 `roles` 声明，因为框架会自动删除它。</span><span class="sxs-lookup"><span data-stu-id="c855f-284">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="c855f-285">在托管的 Blazor 解决方案的独立应用或 `Client` 应用的 `Program.Main` 中，将名为“`role`”的声明指定为角色声明：</span><span class="sxs-lookup"><span data-stu-id="c855f-285">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="c855f-286">组件授权方法此时有效。</span><span class="sxs-lookup"><span data-stu-id="c855f-286">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="c855f-287">组件中的任何授权机制都可以使用 `admin` 角色来授权用户：</span><span class="sxs-lookup"><span data-stu-id="c855f-287">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="c855f-288">[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)（例如 `<AuthorizeView Roles="admin">`）</span><span class="sxs-lookup"><span data-stu-id="c855f-288">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="c855f-289">[`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)（例如 `@attribute [Authorize(Roles = "admin")]`）</span><span class="sxs-lookup"><span data-stu-id="c855f-289">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="c855f-290">[过程逻辑](xref:blazor/security/index#procedural-logic)（例如 `if (user.IsInRole("admin")) { ... }`）</span><span class="sxs-lookup"><span data-stu-id="c855f-290">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="c855f-291">支持多个角色测试：</span><span class="sxs-lookup"><span data-stu-id="c855f-291">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="c855f-292">AAD 管理员角色对象 ID</span><span class="sxs-lookup"><span data-stu-id="c855f-292">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="c855f-293">下表中显示的对象 ID 指示用于为 `group` 声明创建[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="c855f-293">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="c855f-294">策略允许应用授权用户执行应用中的各种活动。</span><span class="sxs-lookup"><span data-stu-id="c855f-294">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="c855f-295">有关详细信息，请参阅[用户定义的组和 AAD 管理员角色](#user-defined-groups-and-administrator-roles)部分。</span><span class="sxs-lookup"><span data-stu-id="c855f-295">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="c855f-296">AAD 管理员角色</span><span class="sxs-lookup"><span data-stu-id="c855f-296">AAD Administrator Role</span></span> | <span data-ttu-id="c855f-297">对象 ID</span><span class="sxs-lookup"><span data-stu-id="c855f-297">Object ID</span></span>
--- | ---
<span data-ttu-id="c855f-298">应用程序管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-298">Application administrator</span></span> | <span data-ttu-id="c855f-299">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="c855f-299">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="c855f-300">应用程序开发人员</span><span class="sxs-lookup"><span data-stu-id="c855f-300">Application developer</span></span> | <span data-ttu-id="c855f-301">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="c855f-301">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="c855f-302">身份验证管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-302">Authentication administrator</span></span> | <span data-ttu-id="c855f-303">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="c855f-303">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="c855f-304">Azure DevOps 管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-304">Azure DevOps administrator</span></span> | <span data-ttu-id="c855f-305">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="c855f-305">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="c855f-306">Azure 信息保护管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-306">Azure Information Protection administrator</span></span> | <span data-ttu-id="c855f-307">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="c855f-307">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="c855f-308">B2C IEF 密钥集管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-308">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="c855f-309">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="c855f-309">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="c855f-310">B2C IEF 策略管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-310">B2C IEF Policy administrator</span></span> | <span data-ttu-id="c855f-311">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="c855f-311">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="c855f-312">B2C 用户流管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-312">B2C user flow administrator</span></span> | <span data-ttu-id="c855f-313">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="c855f-313">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="c855f-314">B2C 用户流属性管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-314">B2C user flow attribute administrator</span></span> | <span data-ttu-id="c855f-315">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="c855f-315">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="c855f-316">计费管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-316">Billing administrator</span></span> | <span data-ttu-id="c855f-317">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="c855f-317">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="c855f-318">云应用程序管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-318">Cloud application administrator</span></span> | <span data-ttu-id="c855f-319">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="c855f-319">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="c855f-320">云设备管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-320">Cloud device administrator</span></span> | <span data-ttu-id="c855f-321">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="c855f-321">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="c855f-322">法规管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-322">Compliance administrator</span></span> | <span data-ttu-id="c855f-323">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="c855f-323">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="c855f-324">合规性数据管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-324">Compliance data administrator</span></span> | <span data-ttu-id="c855f-325">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="c855f-325">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="c855f-326">条件访问管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-326">Conditional Access administrator</span></span> | <span data-ttu-id="c855f-327">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="c855f-327">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="c855f-328">客户密码箱访问审批人</span><span class="sxs-lookup"><span data-stu-id="c855f-328">Customer LockBox access approver</span></span> | <span data-ttu-id="c855f-329">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="c855f-329">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="c855f-330">桌面分析管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-330">Desktop Analytics administrator</span></span> | <span data-ttu-id="c855f-331">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="c855f-331">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="c855f-332">目录读者</span><span class="sxs-lookup"><span data-stu-id="c855f-332">Directory readers</span></span> | <span data-ttu-id="c855f-333">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="c855f-333">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="c855f-334">Dynamics 365 管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-334">Dynamics 365 administrator</span></span> | <span data-ttu-id="c855f-335">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="c855f-335">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="c855f-336">Exchange 管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-336">Exchange administrator</span></span> | <span data-ttu-id="c855f-337">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="c855f-337">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="c855f-338">外部 Identity 提供者管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-338">External Identity Provider administrator</span></span> | <span data-ttu-id="c855f-339">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="c855f-339">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="c855f-340">全局管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-340">Global administrator</span></span> | <span data-ttu-id="c855f-341">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="c855f-341">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="c855f-342">全局读取者</span><span class="sxs-lookup"><span data-stu-id="c855f-342">Global reader</span></span> | <span data-ttu-id="c855f-343">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="c855f-343">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="c855f-344">组管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-344">Groups administrator</span></span> | <span data-ttu-id="c855f-345">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="c855f-345">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="c855f-346">来宾邀请者</span><span class="sxs-lookup"><span data-stu-id="c855f-346">Guest inviter</span></span> | <span data-ttu-id="c855f-347">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="c855f-347">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="c855f-348">支持管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-348">Helpdesk administrator</span></span> | <span data-ttu-id="c855f-349">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="c855f-349">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="c855f-350">Intune 管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-350">Intune administrator</span></span> | <span data-ttu-id="c855f-351">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="c855f-351">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="c855f-352">Kaizala 管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-352">Kaizala administrator</span></span> | <span data-ttu-id="c855f-353">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="c855f-353">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="c855f-354">许可证管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-354">License administrator</span></span> | <span data-ttu-id="c855f-355">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="c855f-355">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="c855f-356">消息中心隐私读取者</span><span class="sxs-lookup"><span data-stu-id="c855f-356">Message center privacy reader</span></span> | <span data-ttu-id="c855f-357">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="c855f-357">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="c855f-358">消息中心读取者</span><span class="sxs-lookup"><span data-stu-id="c855f-358">Message center reader</span></span> | <span data-ttu-id="c855f-359">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="c855f-359">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="c855f-360">Office 应用管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-360">Office apps administrator</span></span> | <span data-ttu-id="c855f-361">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="c855f-361">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="c855f-362">密码管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-362">Password administrator</span></span> | <span data-ttu-id="c855f-363">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="c855f-363">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="c855f-364">Power BI 管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-364">Power BI administrator</span></span> | <span data-ttu-id="c855f-365">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="c855f-365">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="c855f-366">Power Platform 管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-366">Power platform administrator</span></span> | <span data-ttu-id="c855f-367">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="c855f-367">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="c855f-368">特权身份验证管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-368">Privileged authentication administrator</span></span> | <span data-ttu-id="c855f-369">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="c855f-369">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="c855f-370">特权角色管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-370">Privileged role administrator</span></span> | <span data-ttu-id="c855f-371">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="c855f-371">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="c855f-372">报告读者</span><span class="sxs-lookup"><span data-stu-id="c855f-372">Reports reader</span></span> | <span data-ttu-id="c855f-373">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="c855f-373">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="c855f-374">搜索管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-374">Search administrator</span></span> | <span data-ttu-id="c855f-375">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="c855f-375">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="c855f-376">搜索编辑员</span><span class="sxs-lookup"><span data-stu-id="c855f-376">Search editor</span></span> | <span data-ttu-id="c855f-377">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="c855f-377">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="c855f-378">安全管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-378">Security administrator</span></span> | <span data-ttu-id="c855f-379">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="c855f-379">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="c855f-380">安全操作员</span><span class="sxs-lookup"><span data-stu-id="c855f-380">Security operator</span></span> | <span data-ttu-id="c855f-381">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="c855f-381">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="c855f-382">安全读取者</span><span class="sxs-lookup"><span data-stu-id="c855f-382">Security reader</span></span> | <span data-ttu-id="c855f-383">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="c855f-383">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="c855f-384">服务支持管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-384">Service support administrator</span></span> | <span data-ttu-id="c855f-385">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="c855f-385">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="c855f-386">SharePoint 管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-386">SharePoint administrator</span></span> | <span data-ttu-id="c855f-387">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="c855f-387">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="c855f-388">Skype for Business 管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-388">Skype for Business administrator</span></span> | <span data-ttu-id="c855f-389">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="c855f-389">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="c855f-390">Teams 通信管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-390">Teams Communications Administrator</span></span> | <span data-ttu-id="c855f-391">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="c855f-391">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="c855f-392">Teams 通信支持工程师</span><span class="sxs-lookup"><span data-stu-id="c855f-392">Teams Communications Support Engineer</span></span> | <span data-ttu-id="c855f-393">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="c855f-393">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="c855f-394">Teams 通信专家</span><span class="sxs-lookup"><span data-stu-id="c855f-394">Teams Communications Specialist</span></span> | <span data-ttu-id="c855f-395">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="c855f-395">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="c855f-396">Teams 服务管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-396">Teams Service Administrator</span></span> | <span data-ttu-id="c855f-397">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="c855f-397">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="c855f-398">用户管理员</span><span class="sxs-lookup"><span data-stu-id="c855f-398">User administrator</span></span> | <span data-ttu-id="c855f-399">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="c855f-399">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c855f-400">其他资源</span><span class="sxs-lookup"><span data-stu-id="c855f-400">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
