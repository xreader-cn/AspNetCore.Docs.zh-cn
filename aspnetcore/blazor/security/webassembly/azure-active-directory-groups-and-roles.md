---
title: ASP.NET Core Blazor WebAssembly 与 Azure Active Directory 组和角色
author: guardrex
description: 了解如何配置 Blazor WebAssembly 以使用 Azure Active Directory 组和角色。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
ms.openlocfilehash: d1c75d85283b583d8bfd885fcd6552b69c2528c7
ms.sourcegitcommit: d4836f9b7c508f51c6c4ee6d0cc719b38c1729c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758270"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="b4a99-103">Azure Active Directory (AAD) 组、管理员角色和应用角色</span><span class="sxs-lookup"><span data-stu-id="b4a99-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="b4a99-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="b4a99-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="b4a99-105">Azure Active Directory (AAD) 提供了多种授权方法，它们可与 ASP.NET Core Identity 结合使用：</span><span class="sxs-lookup"><span data-stu-id="b4a99-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="b4a99-106">组</span><span class="sxs-lookup"><span data-stu-id="b4a99-106">Groups</span></span>
  * <span data-ttu-id="b4a99-107">安全性</span><span class="sxs-lookup"><span data-stu-id="b4a99-107">Security</span></span>
  * <span data-ttu-id="b4a99-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="b4a99-108">Microsoft 365</span></span>
  * <span data-ttu-id="b4a99-109">分布</span><span class="sxs-lookup"><span data-stu-id="b4a99-109">Distribution</span></span>
* <span data-ttu-id="b4a99-110">角色</span><span class="sxs-lookup"><span data-stu-id="b4a99-110">Roles</span></span>
  * <span data-ttu-id="b4a99-111">AAD 管理员角色</span><span class="sxs-lookup"><span data-stu-id="b4a99-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="b4a99-112">应用角色</span><span class="sxs-lookup"><span data-stu-id="b4a99-112">App Roles</span></span>

<span data-ttu-id="b4a99-113">本文中的指南适用于以下主题中所述的 Blazor WebAssembly AAD 部署方案：</span><span class="sxs-lookup"><span data-stu-id="b4a99-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="b4a99-114">包含 Microsoft 帐户的独立产品</span><span class="sxs-lookup"><span data-stu-id="b4a99-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="b4a99-115">包含 AAD 的独立产品</span><span class="sxs-lookup"><span data-stu-id="b4a99-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="b4a99-116">由 AAD 托管</span><span class="sxs-lookup"><span data-stu-id="b4a99-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="b4a99-117">本文的指南提供了有关客户端和服务器应用的说明：</span><span class="sxs-lookup"><span data-stu-id="b4a99-117">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="b4a99-118">CLIENT：独立 Blazor WebAssembly 应用或托管 Blazor 解决方案的 `Client` 应用。</span><span class="sxs-lookup"><span data-stu-id="b4a99-118">**CLIENT**: Standalone Blazor WebAssembly apps or the *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="b4a99-119">SERVER：独立 ASP.NET Core 服务器 API/Web API 应用或托管 Blazor 解决方案的 `Server` 应用。</span><span class="sxs-lookup"><span data-stu-id="b4a99-119">**SERVER**: Standalone ASP.NET Core server API/web API apps or the *`Server`* app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="b4a99-120">作用域</span><span class="sxs-lookup"><span data-stu-id="b4a99-120">Scopes</span></span>

<span data-ttu-id="b4a99-121">若要允许 [Microsoft 图像 API](/graph/use-the-api) 调用用户配置文件、角色分配和组成员身份数据，需要在 Azure 门户中为 CLIENT 配置 (`https://graph.microsoft.com/User.Read`) [图像 API 权限（作用域）](/graph/permissions-reference)。</span><span class="sxs-lookup"><span data-stu-id="b4a99-121">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="b4a99-122">在 Azure 门户中，向为角色和组成员身份数据调用图形 API 的 SERVER 应用授予 `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [图形 API 权限（作用域）](/graph/permissions-reference)。</span><span class="sxs-lookup"><span data-stu-id="b4a99-122">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="b4a99-123">除了本文第一节中列出的主题所述的 AAD 部署方案中所需的作用域之外，还需要这些作用域。</span><span class="sxs-lookup"><span data-stu-id="b4a99-123">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="b4a99-124">“权限”和“作用域”术语在 Azure 门户和各种 Microsoft 和外部文档集中均可互换使用。</span><span class="sxs-lookup"><span data-stu-id="b4a99-124">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="b4a99-125">本文为针对在 Azure 门户中分配给应用的权限通篇使用“作用域”一词。</span><span class="sxs-lookup"><span data-stu-id="b4a99-125">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="b4a99-126">组成员身份声明属性</span><span class="sxs-lookup"><span data-stu-id="b4a99-126">Group Membership Claims attribute</span></span>

<span data-ttu-id="b4a99-127">在 Azure 门户的 CLIENT 和 SERVER 应用的应用清单中，将 [`groupMembershipClaims` 属性](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)设置为 `All`。</span><span class="sxs-lookup"><span data-stu-id="b4a99-127">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="b4a99-128">如果值为 `All`，将获得已登录用户所属的所有安全组、通讯组和角色。</span><span class="sxs-lookup"><span data-stu-id="b4a99-128">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="b4a99-129">打开应用的 Azure 门户注册。</span><span class="sxs-lookup"><span data-stu-id="b4a99-129">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="b4a99-130">在边栏中选择“管理” > “清单”。</span><span class="sxs-lookup"><span data-stu-id="b4a99-130">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="b4a99-131">查找 `groupMembershipClaims` 属性。</span><span class="sxs-lookup"><span data-stu-id="b4a99-131">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="b4a99-132">将值设置为 `All`。</span><span class="sxs-lookup"><span data-stu-id="b4a99-132">Set the value to `All`.</span></span>
1. <span data-ttu-id="b4a99-133">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="b4a99-133">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="b4a99-134">自定义用户帐户</span><span class="sxs-lookup"><span data-stu-id="b4a99-134">Custom user account</span></span>

<span data-ttu-id="b4a99-135">将用户分配到 Azure 门户中的 AAD 安全组和 AAD 管理员角色。</span><span class="sxs-lookup"><span data-stu-id="b4a99-135">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="b4a99-136">本文中的示例：</span><span class="sxs-lookup"><span data-stu-id="b4a99-136">The examples in this article:</span></span>

* <span data-ttu-id="b4a99-137">假设将用户分配到 Azure 门户 AAD 租户中的 AAD 计费管理员角色，以授权访问服务器 API 数据。</span><span class="sxs-lookup"><span data-stu-id="b4a99-137">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="b4a99-138">使用[授权策略](xref:security/authorization/policies)在 CLIENT 和 SERVER 应用中控制访问。</span><span class="sxs-lookup"><span data-stu-id="b4a99-138">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="b4a99-139">在 CLIENT 应用中，将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 扩展为包含以下属性：</span><span class="sxs-lookup"><span data-stu-id="b4a99-139">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="b4a99-140">`Roles`：AAD 应用角色数组（包含在[应用角色](#app-roles)部分）</span><span class="sxs-lookup"><span data-stu-id="b4a99-140">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="b4a99-141">`Wids`：[已知 ID 声明 (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims) 中的 AAD 管理员角色</span><span class="sxs-lookup"><span data-stu-id="b4a99-141">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="b4a99-142">`Oid`：不可变[对象标识符声明 (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims)（唯一标识租户内和跨租户的用户）</span><span class="sxs-lookup"><span data-stu-id="b4a99-142">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="b4a99-143">为每个数组属性分配一个空数组，这样在 `foreach` 循环中使用这些属性时，无需再检查 `null`。</span><span class="sxs-lookup"><span data-stu-id="b4a99-143">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="b4a99-144">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="b4a99-144">`CustomUserAccount.cs`:</span></span>

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

<span data-ttu-id="b4a99-145">向 [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) 的 CLIENT 应用项目文件添加包引用。</span><span class="sxs-lookup"><span data-stu-id="b4a99-145">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="b4a99-146">添加 <xref:blazor/security/webassembly/graph-api#graph-sdk> 一文中“Graph SDK”部分的 Graph SDK 实用工具类和配置。</span><span class="sxs-lookup"><span data-stu-id="b4a99-146">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="b4a99-147">在 `GraphClientExtensions` 类中，在 `AuthenticateRequestAsync` 方法中指定访问令牌的 `User.Read` 作用域：</span><span class="sxs-lookup"><span data-stu-id="b4a99-147">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="b4a99-148">将以下自定义用户帐户工厂添加到 CLIENT 应用。</span><span class="sxs-lookup"><span data-stu-id="b4a99-148">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="b4a99-149">自定义用户工厂用于建立以下内容：</span><span class="sxs-lookup"><span data-stu-id="b4a99-149">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="b4a99-150">应用角色数组 (`appRole`)（包含在[应用角色](#app-roles)部分）</span><span class="sxs-lookup"><span data-stu-id="b4a99-150">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="b4a99-151">AAD 管理员角色声明 (`directoryRole`)</span><span class="sxs-lookup"><span data-stu-id="b4a99-151">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="b4a99-152">用户移动电话号码 (`mobilePhone`) 的用户配置文件数据声明示例</span><span class="sxs-lookup"><span data-stu-id="b4a99-152">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="b4a99-153">AAD 组声明 (`directoryGroup`)</span><span class="sxs-lookup"><span data-stu-id="b4a99-153">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="b4a99-154">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="b4a99-154">`CustomAccountFactory.cs`:</span></span>

```csharp
using System;
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
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="b4a99-155">上面的代码不包含可传递的成员身份。</span><span class="sxs-lookup"><span data-stu-id="b4a99-155">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="b4a99-156">如果应用需要直接且可传递的组成员身份声明，请将 `MemberOf` 属性 (`IUserMemberOfCollectionWithReferencesRequestBuilder`) 替换为 `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`)。</span><span class="sxs-lookup"><span data-stu-id="b4a99-156">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="b4a99-157">前面的代码将忽略作为 AAD 管理员角色（`#microsoft.graph.directoryRole` 类型）的组成员身份声明 (`groups`)，因为 Microsoft Identity Platform 2.0 返回的 GUID 值为 AAD 管理员角色实体 ID 而不是[角色模板 ID](/azure/active-directory/roles/permissions-reference#role-template-ids)。</span><span class="sxs-lookup"><span data-stu-id="b4a99-157">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="b4a99-158">实体 ID 在 Microsoft Identity Platform 2.0 的租户中不稳定，因此不应用于为应用中的用户创建授权策略。</span><span class="sxs-lookup"><span data-stu-id="b4a99-158">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="b4a99-159">始终使用 `wids` 声明提供的 AAD 管理员角色的角色模板 ID。</span><span class="sxs-lookup"><span data-stu-id="b4a99-159">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="b4a99-160">在 CLIENT 应用的 `Program.Main` 中，将 MSAL 身份验证配置为使用自定义用户帐户工厂。</span><span class="sxs-lookup"><span data-stu-id="b4a99-160">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="b4a99-161">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="b4a99-161">`Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a><span data-ttu-id="b4a99-162">授权配置</span><span class="sxs-lookup"><span data-stu-id="b4a99-162">Authorization configuration</span></span>

<span data-ttu-id="b4a99-163">在 CLIENT 应用中，为每个[应用角色](#app-roles)、AAD 管理员角色或 `Program.Main` 中的安全组创建[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="b4a99-163">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="b4a99-164">以下示例为 AAD 计费管理员角色创建策略：</span><span class="sxs-lookup"><span data-stu-id="b4a99-164">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="b4a99-165">有关 AAD 管理员角色 ID 的完整列表，请参阅 Azure 文档中的[角色模板 ID](/azure/active-directory/roles/permissions-reference#role-template-ids)。</span><span class="sxs-lookup"><span data-stu-id="b4a99-165">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="b4a99-166">有关授权策略的详细信息，请参阅 <xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="b4a99-166">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="b4a99-167">在以下示例中，CLIENT 应用使用前面的策略来授权用户。</span><span class="sxs-lookup"><span data-stu-id="b4a99-167">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="b4a99-168">[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)适用于以下策略：</span><span class="sxs-lookup"><span data-stu-id="b4a99-168">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="b4a99-169">对整个组件的访问可以基于使用 [`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) 的策略：</span><span class="sxs-lookup"><span data-stu-id="b4a99-169">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="b4a99-170">如果用户未登录，则会将他们重定向到 AAD 登录页，然后在他们登录后再返回到组件。</span><span class="sxs-lookup"><span data-stu-id="b4a99-170">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="b4a99-171">策略检查也可以[在具有过程逻辑的代码中执行](xref:blazor/security/index#procedural-logic)。</span><span class="sxs-lookup"><span data-stu-id="b4a99-171">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="b4a99-172">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="b4a99-172">`Pages/CheckPolicy.razor`:</span></span>

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

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="b4a99-173">授权服务器 API/web API 访问</span><span class="sxs-lookup"><span data-stu-id="b4a99-173">Authorize server API/web API access</span></span>

<span data-ttu-id="b4a99-174">当访问令牌包含 `groups`、`wids` 和 `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` 声明时，SERVER API 应用可以通过[授权策略](xref:security/authorization/policies)授权用户访问安全组、AAD 管理员角色和应用角色的安全 API 终结点。</span><span class="sxs-lookup"><span data-stu-id="b4a99-174">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="b4a99-175">下面的示例使用 `wids`（已知 ID/角色模板 ID）声明，在 `Startup.ConfigureServices` 中为 AAD 计费管理员角色创建策略：</span><span class="sxs-lookup"><span data-stu-id="b4a99-175">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="b4a99-176">有关 AAD 管理员角色 ID 的完整列表，请参阅 Azure 文档中的[角色模板 ID](/azure/active-directory/roles/permissions-reference#role-template-ids)。</span><span class="sxs-lookup"><span data-stu-id="b4a99-176">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="b4a99-177">有关授权策略的详细信息，请参阅 <xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="b4a99-177">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="b4a99-178">对 SERVER 应用中的控制器的访问可以基于将 [`[Authorize]` 属性](xref:security/authorization/simple) 与策略名称（API 文档：<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>）结合使用。</span><span class="sxs-lookup"><span data-stu-id="b4a99-178">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="b4a99-179">下面的示例使用名为 `BillingAdministrator` 的策略，对 `BillingDataController` 中计费数据的访问限制为 Azure 计费管理员：</span><span class="sxs-lookup"><span data-stu-id="b4a99-179">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

<span data-ttu-id="b4a99-180">有关详细信息，请参阅 <xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="b4a99-180">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="b4a99-181">应用角色</span><span class="sxs-lookup"><span data-stu-id="b4a99-181">App Roles</span></span>

<span data-ttu-id="b4a99-182">要在 Azure 门户中配置应用以提供应用角色成员资格声明，请参阅[如何：在应用程序中添加应用角色并在令牌中接收它们](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)。</span><span class="sxs-lookup"><span data-stu-id="b4a99-182">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="b4a99-183">下面的示例假定 CLIENT 和 SERVER 应用配置了两个角色，并将角色分配给测试用户：</span><span class="sxs-lookup"><span data-stu-id="b4a99-183">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="b4a99-184">当开发托管 Blazor WebAssembly 应用或独立应用的客户端服务器对（独立 Blazor WebAssembly 应用和独立 ASP.NET Core 服务器 API/Web API 应用）时，客户端和服务器 Azure 门户应用注册的 `appRoles` 清单属性必须包含相同的已配置角色。</span><span class="sxs-lookup"><span data-stu-id="b4a99-184">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="b4a99-185">在客户端应用清单中建立角色后，请将它们全部复制到服务器应用清单中。</span><span class="sxs-lookup"><span data-stu-id="b4a99-185">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="b4a99-186">如果未在客户端和服务器应用注册之间映射清单 `appRoles`，则不会为服务器 API/Web API 的经过身份验证的用户建立角色声明，即使其访问令牌具有正确的角色声明也是如此。</span><span class="sxs-lookup"><span data-stu-id="b4a99-186">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="b4a99-187">尽管无法将角色分配给没有 Azure AD Premium 帐户的组，但你可以将角色分配给用户，并为具有标准 Azure 帐户的用户接收角色声明。</span><span class="sxs-lookup"><span data-stu-id="b4a99-187">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="b4a99-188">本部分中的指南不需要 AAD Premium 帐户。</span><span class="sxs-lookup"><span data-stu-id="b4a99-188">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="b4a99-189">通过为其他每个角色分配重新添加用户，在 Azure 门户中分配多个角色。</span><span class="sxs-lookup"><span data-stu-id="b4a99-189">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="b4a99-190">[自定义用户帐户](#custom-user-account)部分中显示的 `CustomAccountFactory` 设置为使用 JSON 数组值应对 `roles` 声明。</span><span class="sxs-lookup"><span data-stu-id="b4a99-190">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="b4a99-191">在 CLIENT 应用中添加并注册 `CustomAccountFactory`，如[自定义用户帐户](#custom-user-account)部分所示。</span><span class="sxs-lookup"><span data-stu-id="b4a99-191">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="b4a99-192">无需提供代码来删除原始 `roles` 声明，因为框架会自动删除它。</span><span class="sxs-lookup"><span data-stu-id="b4a99-192">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="b4a99-193">在 CLIENT 应用的 `Program.Main` 中，指定名为“`appRole`”的声明作为 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> 检查的角色声明：</span><span class="sxs-lookup"><span data-stu-id="b4a99-193">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="b4a99-194">如果希望使用 `directoryRoles` 声明（ADD 管理员角色），请将“`directoryRoles`”分配给 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="b4a99-194">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="b4a99-195">在 SERVER 应用的 `Startup.ConfigureServices` 中，指定名为“`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`”的声明作为 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> 检查的角色声明：</span><span class="sxs-lookup"><span data-stu-id="b4a99-195">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> <span data-ttu-id="b4a99-196">如果希望使用 `wids` 声明（ADD 管理员角色），请将“`wids`”分配给 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="b4a99-196">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="b4a99-197">组件授权方法此时有效。</span><span class="sxs-lookup"><span data-stu-id="b4a99-197">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="b4a99-198">CLIENT 应用的组件中的任何授权机制都可以使用 `admin` 角色来授权用户：</span><span class="sxs-lookup"><span data-stu-id="b4a99-198">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="b4a99-199">`AuthorizeView` 组件</span><span class="sxs-lookup"><span data-stu-id="b4a99-199">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="b4a99-200">[`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="b4a99-200">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="b4a99-201">过程逻辑</span><span class="sxs-lookup"><span data-stu-id="b4a99-201">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="b4a99-202">支持多个角色测试：</span><span class="sxs-lookup"><span data-stu-id="b4a99-202">Multiple role tests are supported:</span></span>

* <span data-ttu-id="b4a99-203">要求用户为 `admin` 或 `developer` 角色，并具有 `AuthorizeView` 组件：</span><span class="sxs-lookup"><span data-stu-id="b4a99-203">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="b4a99-204">要求用户同时为 `admin` 和 `developer` 角色，并具有 `AuthorizeView` 组件：</span><span class="sxs-lookup"><span data-stu-id="b4a99-204">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="b4a99-205">要求用户为 `admin` 或 `developer` 角色，并具有 `[Authorize]` 属性：</span><span class="sxs-lookup"><span data-stu-id="b4a99-205">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="b4a99-206">要求用户同时为 `admin` 和 `developer` 角色，并具有 `[Authorize]` 属性：</span><span class="sxs-lookup"><span data-stu-id="b4a99-206">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="b4a99-207">要求用户为 `admin` 或 `developer` 角色，并具有过程代码：</span><span class="sxs-lookup"><span data-stu-id="b4a99-207">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* <span data-ttu-id="b4a99-208">要求用户同时为 `admin` 或 `developer` 角色，并具有过程代码，方法是将 [条件 OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) 更改为上述示例中的 [条件 AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)：</span><span class="sxs-lookup"><span data-stu-id="b4a99-208">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="b4a99-209">SERVER 应用的控制器中的任何授权机制都可以使用 `admin` 角色来授权用户：</span><span class="sxs-lookup"><span data-stu-id="b4a99-209">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="b4a99-210">[`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="b4a99-210">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="b4a99-211">过程逻辑</span><span class="sxs-lookup"><span data-stu-id="b4a99-211">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="b4a99-212">支持多个角色测试：</span><span class="sxs-lookup"><span data-stu-id="b4a99-212">Multiple role tests are supported:</span></span>

* <span data-ttu-id="b4a99-213">要求用户为 `admin` 或 `developer` 角色，并具有 `[Authorize]` 属性：</span><span class="sxs-lookup"><span data-stu-id="b4a99-213">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="b4a99-214">要求用户同时为 `admin` 和 `developer` 角色，并具有 `[Authorize]` 属性：</span><span class="sxs-lookup"><span data-stu-id="b4a99-214">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="b4a99-215">要求用户为 `admin` 或 `developer` 角色，并具有过程代码：</span><span class="sxs-lookup"><span data-stu-id="b4a99-215">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* <span data-ttu-id="b4a99-216">要求用户同时为 `admin` 或 `developer` 角色，并具有过程代码，方法是将 [条件 OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) 更改为上述示例中的 [条件 AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)：</span><span class="sxs-lookup"><span data-stu-id="b4a99-216">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="b4a99-217">其他资源</span><span class="sxs-lookup"><span data-stu-id="b4a99-217">Additional resources</span></span>

* [<span data-ttu-id="b4a99-218">角色模板 ID（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="b4a99-218">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="b4a99-219">`groupMembershipClaims` 属性（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="b4a99-219">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="b4a99-220">如何：在应用程序中添加应用角色并在令牌中接收它们（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="b4a99-220">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="b4a99-221">应用程序角色（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="b4a99-221">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
