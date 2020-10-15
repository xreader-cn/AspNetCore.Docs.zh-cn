---
title: ASP.NET Core Blazor WebAssembly 与 Azure Active Directory 组和角色
author: guardrex
description: 了解如何配置 Blazor WebAssembly 以使用 Azure Active Directory 组和角色。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
no-loc:
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
ms.openlocfilehash: ac666a4c7493140d4ae93047e18202c3d8314c7b
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900695"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="bc2ae-103">Azure Active Directory (AAD) 组、管理员角色和用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="bc2ae-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="bc2ae-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="bc2ae-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="bc2ae-105">Azure Active Directory (AAD) 提供了多种授权方法，它们可与 ASP.NET Core Identity 结合使用：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="bc2ae-106">用户定义的组</span><span class="sxs-lookup"><span data-stu-id="bc2ae-106">User-defined groups</span></span>
  * <span data-ttu-id="bc2ae-107">安全性</span><span class="sxs-lookup"><span data-stu-id="bc2ae-107">Security</span></span>
  * <span data-ttu-id="bc2ae-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="bc2ae-108">Microsoft 365</span></span>
  * <span data-ttu-id="bc2ae-109">分布</span><span class="sxs-lookup"><span data-stu-id="bc2ae-109">Distribution</span></span>
* <span data-ttu-id="bc2ae-110">角色</span><span class="sxs-lookup"><span data-stu-id="bc2ae-110">Roles</span></span>
  * <span data-ttu-id="bc2ae-111">AAD 管理员角色</span><span class="sxs-lookup"><span data-stu-id="bc2ae-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="bc2ae-112">用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="bc2ae-112">User-defined roles</span></span>

<span data-ttu-id="bc2ae-113">本文中的指南适用于以下主题中所述的 Blazor WebAssembly AAD 部署方案：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="bc2ae-114">包含 Microsoft 帐户的独立产品</span><span class="sxs-lookup"><span data-stu-id="bc2ae-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="bc2ae-115">包含 AAD 的独立产品</span><span class="sxs-lookup"><span data-stu-id="bc2ae-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="bc2ae-116">由 AAD 托管</span><span class="sxs-lookup"><span data-stu-id="bc2ae-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="bc2ae-117">Microsoft 图形 API 权限</span><span class="sxs-lookup"><span data-stu-id="bc2ae-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="bc2ae-118">具有五个以上 AAD 管理员角色和安全组成员身份的任何应用用户都需要调用 [Microsoft 图形 API](/graph/use-the-api)。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="bc2ae-119">若要允许图形 API 调用，请在 Azure 门户中向托管的 Blazor 解决方案的独立应用或 `Client` 应用授予以下任意[图形 API 权限](/graph/permissions-reference)：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-119">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="bc2ae-120">`Directory.Read.All` 是具有最低特权的权限，本文中的示例使用此权限。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="bc2ae-121">用户定义的组和管理员角色</span><span class="sxs-lookup"><span data-stu-id="bc2ae-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="bc2ae-122">要在 Azure 门户中配置应用以提供 `groups` 成员资格声明，请参阅以下 Azure 文章。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="bc2ae-123">将用户分配到用户定义的 AAD 组和 AAD 管理员角色。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="bc2ae-124">使用 Azure AD 安全组的角色</span><span class="sxs-lookup"><span data-stu-id="bc2ae-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="bc2ae-125">`groupMembershipClaims` 属性</span><span class="sxs-lookup"><span data-stu-id="bc2ae-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="bc2ae-126">以下示例假定用户被分配到 AAD 计费管理员角色。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="bc2ae-127">AAD 发送的单个 `groups` 声明在 JSON 数组中将用户的组和角色作为对象 ID (GUID) 显示。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="bc2ae-128">应用必须将组和角色的 JSON 数组转换为单个 `group` 声明，应用可以针对这些声明生成[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="bc2ae-129">当分配的 AAD 管理员角色和用户定义的组的数量超过五个时，AAD 发送具有 `true` 值的 `hasgroups` 声明，而不是发送 `groups` 声明。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="bc2ae-130">任何可能为其用户分配了五个以上角色和组的应用都必须单独调用图形 API 才能获取用户的角色和组。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="bc2ae-131">本文中提供的示例实现就是针对这种情况的。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="bc2ae-132">有关详细信息，请参阅 [Microsoft 标识平台访问令牌：负载声明](/azure/active-directory/develop/access-tokens#payload-claims)一文中的 `groups` 和 `hasgroups` 声明信息。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="bc2ae-133">扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 以包括组和角色的数组属性。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="bc2ae-134">为每个属性分配一个空数组，这样以后在 `foreach` 循环中使用这些属性时，就不需要检查 `null` 了。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="bc2ae-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="bc2ae-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="bc2ae-136">在托管的 Blazor 解决方案的独立应用或 `Client` 应用中，创建自定义 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 类。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-136">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="bc2ae-137">对获取角色和组信息的图形 API 调用使用正确的作用域（权限）。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="bc2ae-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="bc2ae-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="bc2ae-139">在 `Program.Main` (`Program.cs`)中，添加 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 实现服务，并添加一个已命名的 <xref:System.Net.Http.HttpClient> 以发出图形 API 请求。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="bc2ae-140">下面的示例将命名客户端 `GraphAPI`：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="bc2ae-141">创建 AAD 目录对象类以接收来自图形 API 调用的 Open Data Protocol (OData) 角色和组。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="bc2ae-142">OData 以 JSON 格式送达，并且对 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 的调用将填充 `DirectoryObjects` 类的实例。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="bc2ae-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="bc2ae-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="bc2ae-144">创建一个自定义用户工厂来处理角色和组声明。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="bc2ae-145">下面的示例实现还处理 `roles` 声明数组，[用户定义角色](#user-defined-roles)一节中对此进行了说明。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="bc2ae-146">如果存在 `hasgroups` 声明，则使用已命名的 <xref:System.Net.Http.HttpClient> 向提供图形 API 发出授权请求，以获取用户的角色和组。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="bc2ae-147">此实现使用 Microsoft Identity Platform v1.0 终结点 `https://graph.microsoft.com/v1.0/me/memberOf`（[API 文档](/graph/api/user-list-memberof)）。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="bc2ae-148">当 MSAL 包升级到 v2.0 时，本主题的指南将更新到 Identity v2.0。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="bc2ae-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="bc2ae-149">`CustomAccountFactory.cs`:</span></span>

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
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
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

<span data-ttu-id="bc2ae-150">无需提供代码来删除原始 `groups` 声明（如果存在），因为框架会自动删除它。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="bc2ae-151">本示例中的方法执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="bc2ae-152">添加一个自定义 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 类，将访问令牌附加到传出请求。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="bc2ae-153">添加一个已命名的 <xref:System.Net.Http.HttpClient>，将 Web API 请求发送到安全的外部 Web API 终结点。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="bc2ae-154">使用已命名的 <xref:System.Net.Http.HttpClient> 发出授权请求。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="bc2ae-155">有关此方法的常规覆盖范围，请参阅 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>一文。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="bc2ae-156">在托管的 Blazor 解决方案的独立应用或 `Client` 应用的 `Program.Main` (`Program.cs`) 中注册工厂。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="bc2ae-157">同意将 `Directory.Read.All` 权限范围作为应用的附加范围：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

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

<span data-ttu-id="bc2ae-158">为 `Program.Main` 中的每个组或角色创建[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="bc2ae-159">以下示例为 AAD 计费管理员角色创建策略：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-159">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="bc2ae-160">有关 AAD 角色对象 ID 的完整列表，请参阅 [AAD 管理员角色对象 ID](#aad-administrator-role-object-ids) 部分。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-160">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="bc2ae-161">在以下示例中，应用使用前面的策略来授权用户。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="bc2ae-162">[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)适用于以下策略：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="bc2ae-163">对整个组件的访问可以基于使用 [`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) 的策略：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="bc2ae-164">如果用户未登录，则会将他们重定向到 AAD 登录页，然后在他们登录后再返回到组件。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="bc2ae-165">策略检查也可以[在具有过程逻辑的代码中执行](xref:blazor/security/index#procedural-logic)：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="bc2ae-166">授权用户定义的组和管理员角色访问服务器 API</span><span class="sxs-lookup"><span data-stu-id="bc2ae-166">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="bc2ae-167">除了在客户端 WebAssembly 应用中授权用户访问页面和资源以外，服务器 API 还可以授权用户访问安全 API 终结点。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-167">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="bc2ae-168">在服务器应用验证用户的访问令牌后：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-168">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="bc2ae-169">该应用使用用户来自 JWT (`id_token`) 的不可变[对象标识符声明 (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) 来获取图形 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-169">The app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from the JWT (`id_token`) to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="bc2ae-170">图形 API 调用获取用户的 Azure 用户定义的安全组和管理员角色成员身份。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-170">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships.</span></span>
* <span data-ttu-id="bc2ae-171">成员身份用于建立 `group` 声明。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-171">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="bc2ae-172">[授权策略](xref:security/authorization/policies)可用于限制用户对服务器 API 终结点的访问。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-172">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints.</span></span>

> [!NOTE]
> <span data-ttu-id="bc2ae-173">本指南目前不涵盖根据用户的 [AAD 用户定义的角色](#user-defined-roles)对用户进行授权。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-173">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

### <a name="packages"></a><span data-ttu-id="bc2ae-174">包</span><span class="sxs-lookup"><span data-stu-id="bc2ae-174">Packages</span></span>

<span data-ttu-id="bc2ae-175">将包引用添加到以下包的服务器应用中：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-175">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="bc2ae-176">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="bc2ae-176">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="bc2ae-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="bc2ae-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="bc2ae-178">Azure 配置</span><span class="sxs-lookup"><span data-stu-id="bc2ae-178">Azure configuration</span></span>

* <span data-ttu-id="bc2ae-179">确认为服务器应用注册授予了对图形 API `Directory.Read.All` 权限的 API 访问，这是安全组的最低特权访问级别。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-179">Confirm that the *Server* app registration is given API access to the Graph API permission for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="bc2ae-180">确认在分配权限后，将管理员同意应用于权限。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-180">Confirm that admin consent is applied to the permission after making the permission assignment.</span></span>
* <span data-ttu-id="bc2ae-181">向服务器应用分配新的客户端密码。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-181">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="bc2ae-182">请留意[应用设置](#app-settings)部分中应用配置的密码。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-182">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="bc2ae-183">应用设置</span><span class="sxs-lookup"><span data-stu-id="bc2ae-183">App settings</span></span>

<span data-ttu-id="bc2ae-184">在应用设置文件（`appsettings.json` 或 `appsettings.Production.json`）中，使用服务器应用的客户端密码从 Azure 门户创建 `ClientSecret` 条目：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-184">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="bc2ae-185">例如：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-185">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a><span data-ttu-id="bc2ae-186">授权策略</span><span class="sxs-lookup"><span data-stu-id="bc2ae-186">Authorization policies</span></span>

<span data-ttu-id="bc2ae-187">基于组对象 ID 和 [AAD 管理员角色对象 ID](#aad-administrator-role-object-ids)，为服务器应用的 `Startup.ConfigureServices` (`Startup.cs`) 中的 AAD 安全组和 AAD 管理员角色创建[授权策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-187">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="bc2ae-188">例如，Azure 计费管理员角色策略具有以下配置：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-188">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="bc2ae-189">有关详细信息，请参阅 <xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-189">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="bc2ae-190">控制器访问</span><span class="sxs-lookup"><span data-stu-id="bc2ae-190">Controller access</span></span>

<span data-ttu-id="bc2ae-191">需要服务器应用控制器的策略。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-191">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="bc2ae-192">下面的示例使用名为 `BillingAdmin` 的策略，将对 `BillingDataController` 中计费数据的访问限制为 Azure 计费管理员，如[授权策略](#authorization-policies)部分配置的那样：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-192">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a><span data-ttu-id="bc2ae-193">服务配置</span><span class="sxs-lookup"><span data-stu-id="bc2ae-193">Service configuration</span></span>

<span data-ttu-id="bc2ae-194">在服务器应用的 `Startup.ConfigureServices` 方法中，添加逻辑以进行图形 API 调用，并为用户的安全组和角色建立用户 `group` 声明。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-194">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="bc2ae-195">本节中的示例代码使用基于 Microsoft Identity Platform v1.0 的 Active Directory 身份验证库 (ADAL)。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-195">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span> <span data-ttu-id="bc2ae-196">本主题将针对适用于 .NET 5 的 Identity v2.0 进行更新。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-196">This topic will be updated for Identity v2.0 for .NET 5.</span></span> <span data-ttu-id="bc2ae-197">通过监视 [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503) 可跟踪此项工作的进度。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-197">Track progress on this work by monitoring [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span></span>

<span data-ttu-id="bc2ae-198">服务器应用的 `Startup` 类中的代码需要更多命名空间。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-198">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="bc2ae-199">下面的一组 `using` 语句包含本节下面的代码所需的命名空间：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-199">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

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

<span data-ttu-id="bc2ae-200">配置 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> 时：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-200">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="bc2ae-201">可以选择包括对 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> 的处理。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-201">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="bc2ae-202">例如，应用可以记录失败的身份验证。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-202">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="bc2ae-203">在 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> 中，进行图形 API 调用以获取用户的组和角色。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-203">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="bc2ae-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> 在日志消息中提供个人身份信息 (PII)。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="bc2ae-205">只能用测试用户帐号激活 PII 进行调试。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-205">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="bc2ae-206">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-206">In `Startup.ConfigureServices`:</span></span>

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
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
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

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="bc2ae-207">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-207">In the preceding example:</span></span>

* <span data-ttu-id="bc2ae-208">首先尝试静默令牌获取 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>)，因为访问令牌可能已经存储在 ADAL 令牌缓存中。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-208">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="bc2ae-209">从缓存中获取令牌比请求新令牌更快。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-209">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="bc2ae-210">如果访问令牌不是从缓存中获取的（引发 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> 或 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType>），则使用客户端凭据 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) 生成用户断言 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>)，以代表用户 (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>) 获取令牌。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-210">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="bc2ae-211">接下来，`Microsoft.Graph.GraphServiceClient` 可以继续使用令牌进行图形 API 调用。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-211">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="bc2ae-212">令牌放置在 ADAL 令牌缓存中。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-212">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="bc2ae-213">对于同一用户的未来图形 API 调用，将通过 <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> 以无提示方式从缓存中获取令牌。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-213">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

<span data-ttu-id="bc2ae-214"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> 中的代码不会获得可传递的成员身份。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-214">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="bc2ae-215">若要更改代码以获取直接和可传递的成员身份：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-215">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="bc2ae-216">对于代码行：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-216">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="bc2ae-217">将上面的行替换为：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-217">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="bc2ae-218">对于代码行：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-218">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="bc2ae-219">将上面的行替换为：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-219">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="bc2ae-220">创建声明时，<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> 中的代码不会区分 AAD 安全组和 AAD 管理员角色。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-220">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="bc2ae-221">若要使应用区分组和角色，请在循环访问组和角色时检查 `entry.ODataType`。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-221">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="bc2ae-222">若要创建单独的安全组和角色声明，请使用类似于下面的代码：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-222">To create separate security group and role claims, use code similar to the following:</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="bc2ae-223">用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="bc2ae-223">User-defined roles</span></span>

<span data-ttu-id="bc2ae-224">还可以将 AAD 注册应用配置为使用用户定义的角色。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-224">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="bc2ae-225">要在 Azure 门户中配置应用以提供 `roles` 成员资格声明，请参阅 Azure 文档中的[如何：在应用程序中添加应用角色并在令牌中接收它们](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-225">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="bc2ae-226">以下示例假定应用配置了两个角色：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-226">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="bc2ae-227">尽管无法将角色分配给没有 Azure AD Premium 帐户的安全组，但你可以将用户分配给角色，并为具有标准 Azure 帐户的用户接收 `roles` 声明。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-227">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="bc2ae-228">本部分中的指南不需要 Azure AD Premium 帐户。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-228">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="bc2ae-229">通过为其他每个角色分配重新添加用户，在 Azure 门户中分配多个角色。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-229">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="bc2ae-230">AAD 发送的单个 `roles` 声明在 JSON 数组中将用户定义的角色作为 `appRoles` 的 `value` 显示。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-230">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="bc2ae-231">应用必须将 JSON 角色数组转换为单个 `role` 声明。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-231">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="bc2ae-232">[用户定义的组和 AAD 管理员角色](#user-defined-groups-and-administrator-roles)部分中显示的 `CustomUserFactory` 设置为对具有 JSON 数组值的 `roles` 声明执行操作。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-232">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="bc2ae-233">在托管的 Blazor 解决方案的独立应用或 `Client` 应用中添加和注册 `CustomUserFactory`，如[用户定义的组和 AAD 管理员角色](#user-defined-groups-and-administrator-roles)部分中所述。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-233">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="bc2ae-234">无需提供代码来删除原始 `roles` 声明，因为框架会自动删除它。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-234">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="bc2ae-235">在托管的 Blazor 解决方案的独立应用或 `Client` 应用的 `Program.Main` 中，将名为“`role`”的声明指定为角色声明：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-235">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="bc2ae-236">组件授权方法此时有效。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-236">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="bc2ae-237">组件中的任何授权机制都可以使用 `admin` 角色来授权用户：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-237">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="bc2ae-238">[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)（例如 `<AuthorizeView Roles="admin">`）</span><span class="sxs-lookup"><span data-stu-id="bc2ae-238">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="bc2ae-239">[`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)（例如 `@attribute [Authorize(Roles = "admin")]`）</span><span class="sxs-lookup"><span data-stu-id="bc2ae-239">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="bc2ae-240">[过程逻辑](xref:blazor/security/index#procedural-logic)（例如 `if (user.IsInRole("admin")) { ... }`）</span><span class="sxs-lookup"><span data-stu-id="bc2ae-240">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="bc2ae-241">支持多个角色测试：</span><span class="sxs-lookup"><span data-stu-id="bc2ae-241">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="bc2ae-242">AAD 管理员角色对象 ID</span><span class="sxs-lookup"><span data-stu-id="bc2ae-242">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="bc2ae-243">下表中显示的对象 ID 指示用于为 `group` 声明创建[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-243">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="bc2ae-244">策略允许应用授权用户执行应用中的各种活动。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-244">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="bc2ae-245">有关详细信息，请参阅[用户定义的组和 AAD 管理员角色](#user-defined-groups-and-administrator-roles)部分。</span><span class="sxs-lookup"><span data-stu-id="bc2ae-245">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="bc2ae-246">AAD 管理员角色</span><span class="sxs-lookup"><span data-stu-id="bc2ae-246">AAD Administrator Role</span></span> | <span data-ttu-id="bc2ae-247">对象 ID</span><span class="sxs-lookup"><span data-stu-id="bc2ae-247">Object ID</span></span>
--- | ---
<span data-ttu-id="bc2ae-248">应用程序管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-248">Application administrator</span></span> | <span data-ttu-id="bc2ae-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="bc2ae-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="bc2ae-250">应用程序开发人员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-250">Application developer</span></span> | <span data-ttu-id="bc2ae-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="bc2ae-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="bc2ae-252">身份验证管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-252">Authentication administrator</span></span> | <span data-ttu-id="bc2ae-253">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="bc2ae-253">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="bc2ae-254">Azure DevOps 管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-254">Azure DevOps administrator</span></span> | <span data-ttu-id="bc2ae-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="bc2ae-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="bc2ae-256">Azure 信息保护管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-256">Azure Information Protection administrator</span></span> | <span data-ttu-id="bc2ae-257">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="bc2ae-257">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="bc2ae-258">B2C IEF 密钥集管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-258">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="bc2ae-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="bc2ae-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="bc2ae-260">B2C IEF 策略管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-260">B2C IEF Policy administrator</span></span> | <span data-ttu-id="bc2ae-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="bc2ae-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="bc2ae-262">B2C 用户流管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-262">B2C user flow administrator</span></span> | <span data-ttu-id="bc2ae-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="bc2ae-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="bc2ae-264">B2C 用户流属性管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-264">B2C user flow attribute administrator</span></span> | <span data-ttu-id="bc2ae-265">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="bc2ae-265">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="bc2ae-266">计费管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-266">Billing administrator</span></span> | <span data-ttu-id="bc2ae-267">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="bc2ae-267">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="bc2ae-268">云应用程序管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-268">Cloud application administrator</span></span> | <span data-ttu-id="bc2ae-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="bc2ae-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="bc2ae-270">云设备管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-270">Cloud device administrator</span></span> | <span data-ttu-id="bc2ae-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="bc2ae-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="bc2ae-272">法规管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-272">Compliance administrator</span></span> | <span data-ttu-id="bc2ae-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="bc2ae-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="bc2ae-274">合规性数据管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-274">Compliance data administrator</span></span> | <span data-ttu-id="bc2ae-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="bc2ae-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="bc2ae-276">条件访问管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-276">Conditional Access administrator</span></span> | <span data-ttu-id="bc2ae-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="bc2ae-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="bc2ae-278">客户密码箱访问审批人</span><span class="sxs-lookup"><span data-stu-id="bc2ae-278">Customer LockBox access approver</span></span> | <span data-ttu-id="bc2ae-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="bc2ae-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="bc2ae-280">桌面分析管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-280">Desktop Analytics administrator</span></span> | <span data-ttu-id="bc2ae-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="bc2ae-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="bc2ae-282">目录读者</span><span class="sxs-lookup"><span data-stu-id="bc2ae-282">Directory readers</span></span> | <span data-ttu-id="bc2ae-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="bc2ae-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="bc2ae-284">Dynamics 365 管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-284">Dynamics 365 administrator</span></span> | <span data-ttu-id="bc2ae-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="bc2ae-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="bc2ae-286">Exchange 管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-286">Exchange administrator</span></span> | <span data-ttu-id="bc2ae-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="bc2ae-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="bc2ae-288">外部 Identity 提供者管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-288">External Identity Provider administrator</span></span> | <span data-ttu-id="bc2ae-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="bc2ae-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="bc2ae-290">全局管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-290">Global administrator</span></span> | <span data-ttu-id="bc2ae-291">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="bc2ae-291">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="bc2ae-292">全局读取者</span><span class="sxs-lookup"><span data-stu-id="bc2ae-292">Global reader</span></span> | <span data-ttu-id="bc2ae-293">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="bc2ae-293">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="bc2ae-294">组管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-294">Groups administrator</span></span> | <span data-ttu-id="bc2ae-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="bc2ae-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="bc2ae-296">来宾邀请者</span><span class="sxs-lookup"><span data-stu-id="bc2ae-296">Guest inviter</span></span> | <span data-ttu-id="bc2ae-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="bc2ae-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="bc2ae-298">支持管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-298">Helpdesk administrator</span></span> | <span data-ttu-id="bc2ae-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="bc2ae-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="bc2ae-300">Intune 管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-300">Intune administrator</span></span> | <span data-ttu-id="bc2ae-301">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="bc2ae-301">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="bc2ae-302">Kaizala 管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-302">Kaizala administrator</span></span> | <span data-ttu-id="bc2ae-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="bc2ae-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="bc2ae-304">许可证管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-304">License administrator</span></span> | <span data-ttu-id="bc2ae-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="bc2ae-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="bc2ae-306">消息中心隐私读取者</span><span class="sxs-lookup"><span data-stu-id="bc2ae-306">Message center privacy reader</span></span> | <span data-ttu-id="bc2ae-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="bc2ae-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="bc2ae-308">消息中心读取者</span><span class="sxs-lookup"><span data-stu-id="bc2ae-308">Message center reader</span></span> | <span data-ttu-id="bc2ae-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="bc2ae-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="bc2ae-310">Office 应用管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-310">Office apps administrator</span></span> | <span data-ttu-id="bc2ae-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="bc2ae-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="bc2ae-312">密码管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-312">Password administrator</span></span> | <span data-ttu-id="bc2ae-313">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="bc2ae-313">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="bc2ae-314">Power BI 管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-314">Power BI administrator</span></span> | <span data-ttu-id="bc2ae-315">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="bc2ae-315">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="bc2ae-316">Power Platform 管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-316">Power platform administrator</span></span> | <span data-ttu-id="bc2ae-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="bc2ae-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="bc2ae-318">特权身份验证管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-318">Privileged authentication administrator</span></span> | <span data-ttu-id="bc2ae-319">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="bc2ae-319">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="bc2ae-320">特权角色管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-320">Privileged role administrator</span></span> | <span data-ttu-id="bc2ae-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="bc2ae-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="bc2ae-322">报告读者</span><span class="sxs-lookup"><span data-stu-id="bc2ae-322">Reports reader</span></span> | <span data-ttu-id="bc2ae-323">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="bc2ae-323">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="bc2ae-324">搜索管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-324">Search administrator</span></span> | <span data-ttu-id="bc2ae-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="bc2ae-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="bc2ae-326">搜索编辑员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-326">Search editor</span></span> | <span data-ttu-id="bc2ae-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="bc2ae-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="bc2ae-328">安全管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-328">Security administrator</span></span> | <span data-ttu-id="bc2ae-329">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="bc2ae-329">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="bc2ae-330">安全操作员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-330">Security operator</span></span> | <span data-ttu-id="bc2ae-331">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="bc2ae-331">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="bc2ae-332">安全读取者</span><span class="sxs-lookup"><span data-stu-id="bc2ae-332">Security reader</span></span> | <span data-ttu-id="bc2ae-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="bc2ae-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="bc2ae-334">服务支持管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-334">Service support administrator</span></span> | <span data-ttu-id="bc2ae-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="bc2ae-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="bc2ae-336">SharePoint 管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-336">SharePoint administrator</span></span> | <span data-ttu-id="bc2ae-337">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="bc2ae-337">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="bc2ae-338">Skype for Business 管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-338">Skype for Business administrator</span></span> | <span data-ttu-id="bc2ae-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="bc2ae-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="bc2ae-340">Teams 通信管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-340">Teams Communications Administrator</span></span> | <span data-ttu-id="bc2ae-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="bc2ae-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="bc2ae-342">Teams 通信支持工程师</span><span class="sxs-lookup"><span data-stu-id="bc2ae-342">Teams Communications Support Engineer</span></span> | <span data-ttu-id="bc2ae-343">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="bc2ae-343">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="bc2ae-344">Teams 通信专家</span><span class="sxs-lookup"><span data-stu-id="bc2ae-344">Teams Communications Specialist</span></span> | <span data-ttu-id="bc2ae-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="bc2ae-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="bc2ae-346">Teams 服务管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-346">Teams Service Administrator</span></span> | <span data-ttu-id="bc2ae-347">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="bc2ae-347">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="bc2ae-348">用户管理员</span><span class="sxs-lookup"><span data-stu-id="bc2ae-348">User administrator</span></span> | <span data-ttu-id="bc2ae-349">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="bc2ae-349">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bc2ae-350">其他资源</span><span class="sxs-lookup"><span data-stu-id="bc2ae-350">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
