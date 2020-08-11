---
title: ASP.NET Core Blazor WebAssembly 与 Azure Active Directory 组和角色
author: guardrex
description: 了解如何配置 Blazor WebAssembly 以使用 Azure Active Directory 组和角色。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 68071be9fb9f7a097c0c3693293bf8295e0173f1
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818802"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="b1b14-103">Azure AD 组、管理角色和用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="b1b14-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="b1b14-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="b1b14-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="b1b14-105">Azure Active Directory (AAD) 提供了多种授权方法，这些方法可与 ASP.NET Core Identity 结合使用：</span><span class="sxs-lookup"><span data-stu-id="b1b14-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="b1b14-106">用户定义的组</span><span class="sxs-lookup"><span data-stu-id="b1b14-106">User-defined groups</span></span>
  * <span data-ttu-id="b1b14-107">安全性</span><span class="sxs-lookup"><span data-stu-id="b1b14-107">Security</span></span>
  * <span data-ttu-id="b1b14-108">O365</span><span class="sxs-lookup"><span data-stu-id="b1b14-108">O365</span></span>
  * <span data-ttu-id="b1b14-109">分布</span><span class="sxs-lookup"><span data-stu-id="b1b14-109">Distribution</span></span>
* <span data-ttu-id="b1b14-110">角色</span><span class="sxs-lookup"><span data-stu-id="b1b14-110">Roles</span></span>
  * <span data-ttu-id="b1b14-111">内置管理角色</span><span class="sxs-lookup"><span data-stu-id="b1b14-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="b1b14-112">用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="b1b14-112">User-defined roles</span></span>

<span data-ttu-id="b1b14-113">本文中的指南适用于以下主题中所述的 Blazor WebAssembly AAD 部署方案：</span><span class="sxs-lookup"><span data-stu-id="b1b14-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="b1b14-114">包含 Microsoft 帐户的独立产品</span><span class="sxs-lookup"><span data-stu-id="b1b14-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="b1b14-115">包含 AAD 的独立产品</span><span class="sxs-lookup"><span data-stu-id="b1b14-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="b1b14-116">由 AAD 托管</span><span class="sxs-lookup"><span data-stu-id="b1b14-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="b1b14-117">Microsoft 图形 API 权限</span><span class="sxs-lookup"><span data-stu-id="b1b14-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="b1b14-118">具有五个以上内置 AAD 管理员角色和安全组成员身份的任何应用用户都需要调用 [Microsoft 图形 API](/graph/use-the-api)。</span><span class="sxs-lookup"><span data-stu-id="b1b14-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five built-in AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="b1b14-119">若要允许调用图形 API，请在 Azure 门户中向托管的 Blazor 解决方案的独立应用或客户端应用授予以下任意[图形 API 权限](/graph/permissions-reference)：</span><span class="sxs-lookup"><span data-stu-id="b1b14-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="b1b14-120">`Directory.Read.All` 是具有最低特权的权限，本文中的示例使用此权限。</span><span class="sxs-lookup"><span data-stu-id="b1b14-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="b1b14-121">用户定义的组和内置管理角色</span><span class="sxs-lookup"><span data-stu-id="b1b14-121">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="b1b14-122">要在 Azure 门户中配置应用以提供 `groups` 成员资格声明，请参阅以下 Azure 文章。</span><span class="sxs-lookup"><span data-stu-id="b1b14-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="b1b14-123">将用户分配到用户定义的 AAD 组和内置管理角色。</span><span class="sxs-lookup"><span data-stu-id="b1b14-123">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="b1b14-124">使用 Azure AD 安全组的角色</span><span class="sxs-lookup"><span data-stu-id="b1b14-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="b1b14-125">`groupMembershipClaims` 属性</span><span class="sxs-lookup"><span data-stu-id="b1b14-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="b1b14-126">以下示例假定用户被分配到 AAD 内置“计费管理员”角色。</span><span class="sxs-lookup"><span data-stu-id="b1b14-126">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="b1b14-127">AAD 发送的单个 `groups` 声明在 JSON 数组中将用户的组和角色作为对象 ID (GUID) 显示。</span><span class="sxs-lookup"><span data-stu-id="b1b14-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="b1b14-128">应用必须将组和角色的 JSON 数组转换为单个 `group` 声明，应用可以针对这些声明生成[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="b1b14-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="b1b14-129">当分配的内置 Azure 管理角色和用户定义组的数量超过五个时，AAD 使用 `true` 值发送 `hasgroups` 声明，而不是发送 `groups` 声明。</span><span class="sxs-lookup"><span data-stu-id="b1b14-129">When the number of assigned built-in Azure Administrative Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="b1b14-130">任何可能为其用户分配了五个以上角色和组的应用都必须单独调用图形 API 才能获取用户的角色和组。</span><span class="sxs-lookup"><span data-stu-id="b1b14-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="b1b14-131">本文中提供的示例实现就是针对这种情况的。</span><span class="sxs-lookup"><span data-stu-id="b1b14-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="b1b14-132">有关详细信息，请参阅 [Microsoft 标识平台访问令牌：负载声明](/azure/active-directory/develop/access-tokens#payload-claims)一文中的 `groups` 和 `hasgroups` 声明信息。</span><span class="sxs-lookup"><span data-stu-id="b1b14-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="b1b14-133">扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 以包括组和角色的数组属性。</span><span class="sxs-lookup"><span data-stu-id="b1b14-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="b1b14-134">为每个属性分配一个空数组，这样以后在 `foreach` 循环中使用这些属性时，就不需要检查 `null` 了。</span><span class="sxs-lookup"><span data-stu-id="b1b14-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="b1b14-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="b1b14-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="b1b14-136">在托管的 Blazor 解决方案的独立应用或客户端应用中，创建一个自定义 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 类。</span><span class="sxs-lookup"><span data-stu-id="b1b14-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="b1b14-137">对获取角色和组信息的图形 API 调用使用正确的作用域（权限）。</span><span class="sxs-lookup"><span data-stu-id="b1b14-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="b1b14-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="b1b14-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="b1b14-139">在 `Program.Main` (`Program.cs`)中，添加 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 实现服务，并添加一个已命名的 <xref:System.Net.Http.HttpClient> 以发出图形 API 请求。</span><span class="sxs-lookup"><span data-stu-id="b1b14-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="b1b14-140">下面的示例将命名客户端 `GraphAPI`：</span><span class="sxs-lookup"><span data-stu-id="b1b14-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="b1b14-141">创建 AAD 目录对象类以接收来自图形 API 调用的 Open Data Protocol (OData) 角色和组。</span><span class="sxs-lookup"><span data-stu-id="b1b14-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="b1b14-142">OData 以 JSON 格式送达，并且对 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 的调用将填充 `DirectoryObjects` 类的实例。</span><span class="sxs-lookup"><span data-stu-id="b1b14-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="b1b14-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="b1b14-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="b1b14-144">创建一个自定义用户工厂来处理角色和组声明。</span><span class="sxs-lookup"><span data-stu-id="b1b14-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="b1b14-145">下面的示例实现还处理 `roles` 声明数组，[用户定义角色](#user-defined-roles)一节中对此进行了说明。</span><span class="sxs-lookup"><span data-stu-id="b1b14-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="b1b14-146">如果存在 `hasgroups` 声明，则使用已命名的 <xref:System.Net.Http.HttpClient> 向提供图形 API 发出授权请求，以获取用户的角色和组。</span><span class="sxs-lookup"><span data-stu-id="b1b14-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="b1b14-147">此实现使用 Microsoft Identity Platform v1.0 终结点 `https://graph.microsoft.com/v1.0/me/memberOf`（[API 文档](/graph/api/user-list-memberof)）。</span><span class="sxs-lookup"><span data-stu-id="b1b14-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="b1b14-148">当 MSAL 包升级到 v2.0 时，本主题的指南将更新到 Identity v2.0。</span><span class="sxs-lookup"><span data-stu-id="b1b14-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="b1b14-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="b1b14-149">`CustomAccountFactory.cs`:</span></span>

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
    private readonly ILogger<CustomUserFactory> _logger;
    private readonly IHttpClientFactory _clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        _clientFactory = clientFactory;
        _logger = logger;
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
                    var client = _clientFactory.CreateClient("GraphAPI");

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
                        _logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    _logger.LogError("Graph API access token failure: {MESSAGE}", 
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

<span data-ttu-id="b1b14-150">无需提供代码来删除原始 `groups` 声明（如果存在），因为框架会自动删除它。</span><span class="sxs-lookup"><span data-stu-id="b1b14-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="b1b14-151">本示例中的方法执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b1b14-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="b1b14-152">添加一个自定义 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 类，将访问令牌附加到传出请求。</span><span class="sxs-lookup"><span data-stu-id="b1b14-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="b1b14-153">添加一个已命名的 <xref:System.Net.Http.HttpClient>，将 Web API 请求发送到安全的外部 Web API 终结点。</span><span class="sxs-lookup"><span data-stu-id="b1b14-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="b1b14-154">使用已命名的 <xref:System.Net.Http.HttpClient> 发出授权请求。</span><span class="sxs-lookup"><span data-stu-id="b1b14-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="b1b14-155">有关此方法的常规覆盖范围，请参阅 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>一文。</span><span class="sxs-lookup"><span data-stu-id="b1b14-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="b1b14-156">在托管的 Blazor 解决方案的独立应用或客户端应用的 `Program.Main` (`Program.cs`) 中注册工厂。</span><span class="sxs-lookup"><span data-stu-id="b1b14-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="b1b14-157">同意将 `Directory.Read.All` 权限范围作为应用的附加范围：</span><span class="sxs-lookup"><span data-stu-id="b1b14-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

```csharp
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

<span data-ttu-id="b1b14-158">为 `Program.Main` 中的每个组或角色创建[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="b1b14-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="b1b14-159">以下示例为 AAD 内置“计费管理员”创建策略角色：</span><span class="sxs-lookup"><span data-stu-id="b1b14-159">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="b1b14-160">有关 AAD 角色对象 ID 的完整列表，请参阅 [AAD 管理角色组 ID](#aad-adminstrative-role-group-ids) 部分。</span><span class="sxs-lookup"><span data-stu-id="b1b14-160">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="b1b14-161">在以下示例中，应用使用前面的策略来授权用户。</span><span class="sxs-lookup"><span data-stu-id="b1b14-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="b1b14-162">[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)适用于以下策略：</span><span class="sxs-lookup"><span data-stu-id="b1b14-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
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

<span data-ttu-id="b1b14-163">对整个组件的访问可以基于使用 [`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) 的策略：</span><span class="sxs-lookup"><span data-stu-id="b1b14-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="b1b14-164">如果用户未登录，则会将他们重定向到 AAD 登录页，然后在他们登录后再返回到组件。</span><span class="sxs-lookup"><span data-stu-id="b1b14-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="b1b14-165">策略检查也可以[在具有过程逻辑的代码中执行](xref:blazor/security/index#procedural-logic)：</span><span class="sxs-lookup"><span data-stu-id="b1b14-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="b1b14-166">用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="b1b14-166">User-defined roles</span></span>

<span data-ttu-id="b1b14-167">还可以将 AAD 注册应用配置为使用用户定义的角色。</span><span class="sxs-lookup"><span data-stu-id="b1b14-167">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="b1b14-168">要在 Azure 门户中配置应用以提供 `roles` 成员资格声明，请参阅 Azure 文档中的[如何：在应用程序中添加应用角色并在令牌中接收它们](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)。</span><span class="sxs-lookup"><span data-stu-id="b1b14-168">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="b1b14-169">以下示例假定应用配置了两个角色：</span><span class="sxs-lookup"><span data-stu-id="b1b14-169">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="b1b14-170">尽管无法将角色分配给没有 Azure AD Premium 帐户的安全组，但你可以将用户分配给角色，并为具有标准 Azure 帐户的用户接收 `roles` 声明。</span><span class="sxs-lookup"><span data-stu-id="b1b14-170">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="b1b14-171">本部分中的指南不需要 Azure AD Premium 帐户。</span><span class="sxs-lookup"><span data-stu-id="b1b14-171">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="b1b14-172">通过为其他每个角色分配重新添加用户，在 Azure 门户中分配多个角色。</span><span class="sxs-lookup"><span data-stu-id="b1b14-172">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="b1b14-173">AAD 发送的单个 `roles` 声明在 JSON 数组中将用户定义的角色作为 `appRoles` 的 `value` 显示。</span><span class="sxs-lookup"><span data-stu-id="b1b14-173">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="b1b14-174">应用必须将 JSON 角色数组转换为单个 `role` 声明。</span><span class="sxs-lookup"><span data-stu-id="b1b14-174">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="b1b14-175">[用户定义组和 AAD 内置管理角色](#user-defined-groups-and-built-in-administrative-roles)部分中显示的 `CustomUserFactory` 设置为对具有 JSON 数组值的 `roles` 声明执行操作。</span><span class="sxs-lookup"><span data-stu-id="b1b14-175">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="b1b14-176">在托管的 Blazor 解决方案的独立应用或客户端应用中添加和注册 `CustomUserFactory`，如[用户定义的组和 AAD 内置管理角色](#user-defined-groups-and-built-in-administrative-roles)部分所示。</span><span class="sxs-lookup"><span data-stu-id="b1b14-176">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="b1b14-177">无需提供代码来删除原始 `roles` 声明，因为框架会自动删除它。</span><span class="sxs-lookup"><span data-stu-id="b1b14-177">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="b1b14-178">在托管的 Blazor 解决方案的独立应用或客户端应用的 `Program.Main` 中，将名为“`role`”的声明指定为角色声明：</span><span class="sxs-lookup"><span data-stu-id="b1b14-178">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="b1b14-179">组件授权方法此时有效。</span><span class="sxs-lookup"><span data-stu-id="b1b14-179">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="b1b14-180">组件中的任何授权机制都可以使用 `admin` 角色来授权用户：</span><span class="sxs-lookup"><span data-stu-id="b1b14-180">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="b1b14-181">[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)（例如 `<AuthorizeView Roles="admin">`）</span><span class="sxs-lookup"><span data-stu-id="b1b14-181">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="b1b14-182">[`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)（例如 `@attribute [Authorize(Roles = "admin")]`）</span><span class="sxs-lookup"><span data-stu-id="b1b14-182">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="b1b14-183">[过程逻辑](xref:blazor/security/index#procedural-logic)（例如 `if (user.IsInRole("admin")) { ... }`）</span><span class="sxs-lookup"><span data-stu-id="b1b14-183">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="b1b14-184">支持多个角色测试：</span><span class="sxs-lookup"><span data-stu-id="b1b14-184">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="b1b14-185">AAD 管理角色组 ID</span><span class="sxs-lookup"><span data-stu-id="b1b14-185">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="b1b14-186">下表中显示的对象 ID 指示用于为 `group` 声明创建[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="b1b14-186">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="b1b14-187">策略允许应用授权用户执行应用中的各种活动。</span><span class="sxs-lookup"><span data-stu-id="b1b14-187">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="b1b14-188">有关详细信息，请参阅[用户定义的组和 AAD 内置管理角色](#user-defined-groups-and-built-in-administrative-roles)部分。</span><span class="sxs-lookup"><span data-stu-id="b1b14-188">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="b1b14-189">AAD 管理角色</span><span class="sxs-lookup"><span data-stu-id="b1b14-189">AAD Administrative Role</span></span> | <span data-ttu-id="b1b14-190">对象 ID</span><span class="sxs-lookup"><span data-stu-id="b1b14-190">Object ID</span></span>
--- | ---
<span data-ttu-id="b1b14-191">应用程序管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-191">Application administrator</span></span> | <span data-ttu-id="b1b14-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="b1b14-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="b1b14-193">应用程序开发人员</span><span class="sxs-lookup"><span data-stu-id="b1b14-193">Application developer</span></span> | <span data-ttu-id="b1b14-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="b1b14-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="b1b14-195">身份验证管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-195">Authentication administrator</span></span> | <span data-ttu-id="b1b14-196">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="b1b14-196">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="b1b14-197">Azure DevOps 管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-197">Azure DevOps administrator</span></span> | <span data-ttu-id="b1b14-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="b1b14-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="b1b14-199">Azure 信息保护管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-199">Azure Information Protection administrator</span></span> | <span data-ttu-id="b1b14-200">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="b1b14-200">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="b1b14-201">B2C IEF 密钥集管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-201">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="b1b14-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="b1b14-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="b1b14-203">B2C IEF 策略管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-203">B2C IEF Policy administrator</span></span> | <span data-ttu-id="b1b14-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="b1b14-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="b1b14-205">B2C 用户流管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-205">B2C user flow administrator</span></span> | <span data-ttu-id="b1b14-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="b1b14-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="b1b14-207">B2C 用户流属性管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-207">B2C user flow attribute administrator</span></span> | <span data-ttu-id="b1b14-208">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="b1b14-208">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="b1b14-209">计费管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-209">Billing administrator</span></span> | <span data-ttu-id="b1b14-210">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="b1b14-210">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="b1b14-211">云应用程序管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-211">Cloud application administrator</span></span> | <span data-ttu-id="b1b14-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="b1b14-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="b1b14-213">云设备管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-213">Cloud device administrator</span></span> | <span data-ttu-id="b1b14-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="b1b14-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="b1b14-215">法规管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-215">Compliance administrator</span></span> | <span data-ttu-id="b1b14-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="b1b14-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="b1b14-217">合规性数据管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-217">Compliance data administrator</span></span> | <span data-ttu-id="b1b14-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="b1b14-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="b1b14-219">条件访问管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-219">Conditional Access administrator</span></span> | <span data-ttu-id="b1b14-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="b1b14-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="b1b14-221">客户密码箱访问审批人</span><span class="sxs-lookup"><span data-stu-id="b1b14-221">Customer LockBox access approver</span></span> | <span data-ttu-id="b1b14-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="b1b14-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="b1b14-223">桌面分析管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-223">Desktop Analytics administrator</span></span> | <span data-ttu-id="b1b14-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="b1b14-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="b1b14-225">目录读者</span><span class="sxs-lookup"><span data-stu-id="b1b14-225">Directory readers</span></span> | <span data-ttu-id="b1b14-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="b1b14-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="b1b14-227">Dynamics 365 管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-227">Dynamics 365 administrator</span></span> | <span data-ttu-id="b1b14-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="b1b14-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="b1b14-229">Exchange 管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-229">Exchange administrator</span></span> | <span data-ttu-id="b1b14-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="b1b14-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="b1b14-231">外部 Identity 提供者管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-231">External Identity Provider administrator</span></span> | <span data-ttu-id="b1b14-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="b1b14-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="b1b14-233">全局管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-233">Global administrator</span></span> | <span data-ttu-id="b1b14-234">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="b1b14-234">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="b1b14-235">全局读取者</span><span class="sxs-lookup"><span data-stu-id="b1b14-235">Global reader</span></span> | <span data-ttu-id="b1b14-236">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="b1b14-236">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="b1b14-237">组管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-237">Groups administrator</span></span> | <span data-ttu-id="b1b14-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="b1b14-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="b1b14-239">来宾邀请者</span><span class="sxs-lookup"><span data-stu-id="b1b14-239">Guest inviter</span></span> | <span data-ttu-id="b1b14-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="b1b14-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="b1b14-241">支持管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-241">Helpdesk administrator</span></span> | <span data-ttu-id="b1b14-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="b1b14-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="b1b14-243">Intune 管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-243">Intune administrator</span></span> | <span data-ttu-id="b1b14-244">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="b1b14-244">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="b1b14-245">Kaizala 管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-245">Kaizala administrator</span></span> | <span data-ttu-id="b1b14-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="b1b14-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="b1b14-247">许可证管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-247">License administrator</span></span> | <span data-ttu-id="b1b14-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="b1b14-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="b1b14-249">消息中心隐私读取者</span><span class="sxs-lookup"><span data-stu-id="b1b14-249">Message center privacy reader</span></span> | <span data-ttu-id="b1b14-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="b1b14-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="b1b14-251">消息中心读取者</span><span class="sxs-lookup"><span data-stu-id="b1b14-251">Message center reader</span></span> | <span data-ttu-id="b1b14-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="b1b14-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="b1b14-253">Office 应用管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-253">Office apps administrator</span></span> | <span data-ttu-id="b1b14-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="b1b14-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="b1b14-255">密码管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-255">Password administrator</span></span> | <span data-ttu-id="b1b14-256">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="b1b14-256">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="b1b14-257">Power BI 管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-257">Power BI administrator</span></span> | <span data-ttu-id="b1b14-258">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="b1b14-258">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="b1b14-259">Power Platform 管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-259">Power platform administrator</span></span> | <span data-ttu-id="b1b14-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="b1b14-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="b1b14-261">特权身份验证管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-261">Privileged authentication administrator</span></span> | <span data-ttu-id="b1b14-262">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="b1b14-262">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="b1b14-263">特权角色管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-263">Privileged role administrator</span></span> | <span data-ttu-id="b1b14-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="b1b14-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="b1b14-265">报告读者</span><span class="sxs-lookup"><span data-stu-id="b1b14-265">Reports reader</span></span> | <span data-ttu-id="b1b14-266">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="b1b14-266">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="b1b14-267">搜索管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-267">Search administrator</span></span> | <span data-ttu-id="b1b14-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="b1b14-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="b1b14-269">搜索编辑员</span><span class="sxs-lookup"><span data-stu-id="b1b14-269">Search editor</span></span> | <span data-ttu-id="b1b14-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="b1b14-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="b1b14-271">安全管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-271">Security administrator</span></span> | <span data-ttu-id="b1b14-272">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="b1b14-272">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="b1b14-273">安全操作员</span><span class="sxs-lookup"><span data-stu-id="b1b14-273">Security operator</span></span> | <span data-ttu-id="b1b14-274">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="b1b14-274">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="b1b14-275">安全读取者</span><span class="sxs-lookup"><span data-stu-id="b1b14-275">Security reader</span></span> | <span data-ttu-id="b1b14-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="b1b14-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="b1b14-277">服务支持管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-277">Service support administrator</span></span> | <span data-ttu-id="b1b14-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="b1b14-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="b1b14-279">SharePoint 管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-279">SharePoint administrator</span></span> | <span data-ttu-id="b1b14-280">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="b1b14-280">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="b1b14-281">Skype for Business 管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-281">Skype for Business administrator</span></span> | <span data-ttu-id="b1b14-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="b1b14-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="b1b14-283">Teams 通信管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-283">Teams Communications Administrator</span></span> | <span data-ttu-id="b1b14-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="b1b14-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="b1b14-285">Teams 通信支持工程师</span><span class="sxs-lookup"><span data-stu-id="b1b14-285">Teams Communications Support Engineer</span></span> | <span data-ttu-id="b1b14-286">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="b1b14-286">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="b1b14-287">Teams 通信专家</span><span class="sxs-lookup"><span data-stu-id="b1b14-287">Teams Communications Specialist</span></span> | <span data-ttu-id="b1b14-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="b1b14-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="b1b14-289">Teams 服务管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-289">Teams Service Administrator</span></span> | <span data-ttu-id="b1b14-290">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="b1b14-290">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="b1b14-291">用户管理员</span><span class="sxs-lookup"><span data-stu-id="b1b14-291">User administrator</span></span> | <span data-ttu-id="b1b14-292">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="b1b14-292">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1b14-293">其他资源</span><span class="sxs-lookup"><span data-stu-id="b1b14-293">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
