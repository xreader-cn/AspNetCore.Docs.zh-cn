---
title: ASP.NET Core Blazor WebAssembly 与 Azure Active Directory 组和角色
author: guardrex
description: 了解如何配置 Blazor WebAssembly 以使用 Azure Active Directory 组和角色。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 6e27b062d7b5a1b72804fe5d4ea31ec65358ce45
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402151"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="0f154-103">Azure AD 组、管理角色和用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="0f154-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="0f154-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="0f154-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="0f154-105">Azure Active Directory (AAD) 提供了多种授权方法，这些方法可与 ASP.NET Core Identity 结合使用：</span><span class="sxs-lookup"><span data-stu-id="0f154-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="0f154-106">用户定义的组</span><span class="sxs-lookup"><span data-stu-id="0f154-106">User-defined groups</span></span>
  * <span data-ttu-id="0f154-107">安全性</span><span class="sxs-lookup"><span data-stu-id="0f154-107">Security</span></span>
  * <span data-ttu-id="0f154-108">O365</span><span class="sxs-lookup"><span data-stu-id="0f154-108">O365</span></span>
  * <span data-ttu-id="0f154-109">分布</span><span class="sxs-lookup"><span data-stu-id="0f154-109">Distribution</span></span>
* <span data-ttu-id="0f154-110">角色</span><span class="sxs-lookup"><span data-stu-id="0f154-110">Roles</span></span>
  * <span data-ttu-id="0f154-111">内置管理角色</span><span class="sxs-lookup"><span data-stu-id="0f154-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="0f154-112">用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="0f154-112">User-defined roles</span></span>

<span data-ttu-id="0f154-113">本文中的指南适用于以下主题中所述的 Blazor WebAssembly AAD 部署方案：</span><span class="sxs-lookup"><span data-stu-id="0f154-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="0f154-114">包含 Microsoft 帐户的独立产品</span><span class="sxs-lookup"><span data-stu-id="0f154-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="0f154-115">包含 AAD 的独立产品</span><span class="sxs-lookup"><span data-stu-id="0f154-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="0f154-116">由 AAD 托管</span><span class="sxs-lookup"><span data-stu-id="0f154-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="0f154-117">用户定义的组和内置管理角色</span><span class="sxs-lookup"><span data-stu-id="0f154-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="0f154-118">要在 Azure 门户中配置应用以提供 `groups` 成员资格声明，请参阅以下 Azure 文章。</span><span class="sxs-lookup"><span data-stu-id="0f154-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="0f154-119">将用户分配到用户定义的 AAD 组和内置管理角色。</span><span class="sxs-lookup"><span data-stu-id="0f154-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="0f154-120">使用 Azure AD 安全组的角色</span><span class="sxs-lookup"><span data-stu-id="0f154-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="0f154-121">`groupMembershipClaims` 属性</span><span class="sxs-lookup"><span data-stu-id="0f154-121">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="0f154-122">以下示例假定用户被分配到 AAD 内置“计费管理员”角色。</span><span class="sxs-lookup"><span data-stu-id="0f154-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="0f154-123">AAD 发送的单个 `groups` 声明在 JSON 数组中将用户的组和角色作为对象 ID (GUID) 显示。</span><span class="sxs-lookup"><span data-stu-id="0f154-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="0f154-124">应用必须将组和角色的 JSON 数组转换为单个 `group` 声明，应用可以针对这些声明生成[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="0f154-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="0f154-125">扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 以包括组和角色的数组属性。</span><span class="sxs-lookup"><span data-stu-id="0f154-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="0f154-126">`CustomUserAccount.cs`：</span><span class="sxs-lookup"><span data-stu-id="0f154-126">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

<span data-ttu-id="0f154-127">在托管解决方案的独立应用或客户端应用中创建自定义用户工厂。</span><span class="sxs-lookup"><span data-stu-id="0f154-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="0f154-128">以下工厂还可配置为处理 `roles` 声明数组，此内容在[用户定义的角色](#user-defined-roles)部分中进行了介绍：</span><span class="sxs-lookup"><span data-stu-id="0f154-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
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

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="0f154-129">无需提供代码来删除原始 `groups` 声明，因为框架会自动删除它。</span><span class="sxs-lookup"><span data-stu-id="0f154-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="0f154-130">在托管解决方案的独立应用或客户端应用的 `Program.Main` (`Program.cs`) 中注册工厂：</span><span class="sxs-lookup"><span data-stu-id="0f154-130">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or Client app of a Hosted solution:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="0f154-131">为 `Program.Main` 中的每个组或角色创建[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="0f154-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="0f154-132">以下示例为 AAD 内置“计费管理员”创建策略角色：</span><span class="sxs-lookup"><span data-stu-id="0f154-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="0f154-133">有关 AAD 角色对象 ID 的完整列表，请参阅 [AAD 管理角色组 ID](#aad-adminstrative-role-group-ids) 部分。</span><span class="sxs-lookup"><span data-stu-id="0f154-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="0f154-134">在以下示例中，应用使用前面的策略来授权用户。</span><span class="sxs-lookup"><span data-stu-id="0f154-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="0f154-135">[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)适用于以下策略：</span><span class="sxs-lookup"><span data-stu-id="0f154-135">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="0f154-136">对整个组件的访问可以基于使用 [`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) 的策略：</span><span class="sxs-lookup"><span data-stu-id="0f154-136">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="0f154-137">如果用户未登录，则会将他们重定向到 AAD 登录页，然后在他们登录后再返回到组件。</span><span class="sxs-lookup"><span data-stu-id="0f154-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="0f154-138">策略检查也可以[在具有过程逻辑的代码中执行](xref:blazor/security/index#procedural-logic)：</span><span class="sxs-lookup"><span data-stu-id="0f154-138">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

### <a name="user-defined-roles"></a><span data-ttu-id="0f154-139">用户定义的角色</span><span class="sxs-lookup"><span data-stu-id="0f154-139">User-defined roles</span></span>

<span data-ttu-id="0f154-140">还可以将 AAD 注册应用配置为使用用户定义的角色。</span><span class="sxs-lookup"><span data-stu-id="0f154-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="0f154-141">要在 Azure 门户中配置应用以提供 `roles` 成员资格声明，请参阅 Azure 文档中的[如何：在应用程序中添加应用角色并在令牌中接收它们](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)。</span><span class="sxs-lookup"><span data-stu-id="0f154-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="0f154-142">以下示例假定应用配置了两个角色：</span><span class="sxs-lookup"><span data-stu-id="0f154-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="0f154-143">尽管无法将角色分配给没有 Azure AD Premium 帐户的安全组，但你可以将用户分配给角色，并为具有标准 Azure 帐户的用户接收 `roles` 声明。</span><span class="sxs-lookup"><span data-stu-id="0f154-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="0f154-144">本部分中的指南不需要 Azure AD Premium 帐户。</span><span class="sxs-lookup"><span data-stu-id="0f154-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="0f154-145">通过为其他每个角色分配重新添加用户，在 Azure 门户中分配多个角色。</span><span class="sxs-lookup"><span data-stu-id="0f154-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="0f154-146">AAD 发送的单个 `roles` 声明在 JSON 数组中将用户定义的角色作为 `appRoles` 的 `value` 显示。</span><span class="sxs-lookup"><span data-stu-id="0f154-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="0f154-147">应用必须将 JSON 角色数组转换为单个 `role` 声明。</span><span class="sxs-lookup"><span data-stu-id="0f154-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="0f154-148">[用户定义组和 AAD 内置管理角色](#user-defined-groups-and-built-in-administrative-roles)部分中显示的 `CustomUserFactory` 设置为对具有 JSON 数组值的 `roles` 声明执行操作。</span><span class="sxs-lookup"><span data-stu-id="0f154-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="0f154-149">在托管解决方案的独立应用或客户端应用中添加和注册 `CustomUserFactory`，如[用户定义的组和 AAD 内置管理角色](#user-defined-groups-and-built-in-administrative-roles)部分所示。</span><span class="sxs-lookup"><span data-stu-id="0f154-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="0f154-150">无需提供代码来删除原始 `roles` 声明，因为框架会自动删除它。</span><span class="sxs-lookup"><span data-stu-id="0f154-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="0f154-151">在托管解决方案的独立应用或客户端应用的 `Program.Main` 中，将名为“`role`”的声明指定为角色声明：</span><span class="sxs-lookup"><span data-stu-id="0f154-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="0f154-152">组件授权方法此时有效。</span><span class="sxs-lookup"><span data-stu-id="0f154-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="0f154-153">组件中的任何授权机制都可以使用 `admin` 角色来授权用户：</span><span class="sxs-lookup"><span data-stu-id="0f154-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="0f154-154">[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)（例如 `<AuthorizeView Roles="admin">`）</span><span class="sxs-lookup"><span data-stu-id="0f154-154">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="0f154-155">[`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)（例如 `@attribute [Authorize(Roles = "admin")]`）</span><span class="sxs-lookup"><span data-stu-id="0f154-155">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="0f154-156">[过程逻辑](xref:blazor/security/index#procedural-logic)（例如 `if (user.IsInRole("admin")) { ... }`）</span><span class="sxs-lookup"><span data-stu-id="0f154-156">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="0f154-157">支持多个角色测试：</span><span class="sxs-lookup"><span data-stu-id="0f154-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="0f154-158">AAD 管理角色组 ID</span><span class="sxs-lookup"><span data-stu-id="0f154-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="0f154-159">下表中显示的对象 ID 指示用于为 `group` 声明创建[策略](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="0f154-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="0f154-160">策略允许应用授权用户执行应用中的各种活动。</span><span class="sxs-lookup"><span data-stu-id="0f154-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="0f154-161">有关详细信息，请参阅[用户定义的组和 AAD 内置管理角色](#user-defined-groups-and-built-in-administrative-roles)部分。</span><span class="sxs-lookup"><span data-stu-id="0f154-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="0f154-162">AAD 管理角色</span><span class="sxs-lookup"><span data-stu-id="0f154-162">AAD Administrative Role</span></span> | <span data-ttu-id="0f154-163">对象 ID</span><span class="sxs-lookup"><span data-stu-id="0f154-163">Object ID</span></span>
--- | ---
<span data-ttu-id="0f154-164">应用程序管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-164">Application administrator</span></span> | <span data-ttu-id="0f154-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="0f154-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="0f154-166">应用程序开发人员</span><span class="sxs-lookup"><span data-stu-id="0f154-166">Application developer</span></span> | <span data-ttu-id="0f154-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="0f154-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="0f154-168">身份验证管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-168">Authentication administrator</span></span> | <span data-ttu-id="0f154-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="0f154-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="0f154-170">Azure DevOps 管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-170">Azure DevOps administrator</span></span> | <span data-ttu-id="0f154-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="0f154-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="0f154-172">Azure 信息保护管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="0f154-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="0f154-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="0f154-174">B2C IEF 密钥集管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="0f154-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="0f154-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="0f154-176">B2C IEF 策略管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="0f154-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="0f154-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="0f154-178">B2C 用户流管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-178">B2C user flow administrator</span></span> | <span data-ttu-id="0f154-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="0f154-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="0f154-180">B2C 用户流属性管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="0f154-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="0f154-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="0f154-182">计费管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-182">Billing administrator</span></span> | <span data-ttu-id="0f154-183">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="0f154-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="0f154-184">云应用程序管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-184">Cloud application administrator</span></span> | <span data-ttu-id="0f154-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="0f154-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="0f154-186">云设备管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-186">Cloud device administrator</span></span> | <span data-ttu-id="0f154-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="0f154-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="0f154-188">法规管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-188">Compliance administrator</span></span> | <span data-ttu-id="0f154-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="0f154-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="0f154-190">合规性数据管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-190">Compliance data administrator</span></span> | <span data-ttu-id="0f154-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="0f154-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="0f154-192">条件访问管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-192">Conditional Access administrator</span></span> | <span data-ttu-id="0f154-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="0f154-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="0f154-194">客户密码箱访问审批人</span><span class="sxs-lookup"><span data-stu-id="0f154-194">Customer LockBox access approver</span></span> | <span data-ttu-id="0f154-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="0f154-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="0f154-196">桌面分析管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="0f154-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="0f154-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="0f154-198">目录读者</span><span class="sxs-lookup"><span data-stu-id="0f154-198">Directory readers</span></span> | <span data-ttu-id="0f154-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="0f154-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="0f154-200">Dynamics 365 管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="0f154-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="0f154-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="0f154-202">Exchange 管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-202">Exchange administrator</span></span> | <span data-ttu-id="0f154-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="0f154-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="0f154-204">外部 Identity 提供者管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-204">External Identity Provider administrator</span></span> | <span data-ttu-id="0f154-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="0f154-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="0f154-206">全局管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-206">Global administrator</span></span> | <span data-ttu-id="0f154-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="0f154-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="0f154-208">全局读取者</span><span class="sxs-lookup"><span data-stu-id="0f154-208">Global reader</span></span> | <span data-ttu-id="0f154-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="0f154-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="0f154-210">组管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-210">Groups administrator</span></span> | <span data-ttu-id="0f154-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="0f154-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="0f154-212">来宾邀请者</span><span class="sxs-lookup"><span data-stu-id="0f154-212">Guest inviter</span></span> | <span data-ttu-id="0f154-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="0f154-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="0f154-214">支持管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-214">Helpdesk administrator</span></span> | <span data-ttu-id="0f154-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="0f154-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="0f154-216">Intune 管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-216">Intune administrator</span></span> | <span data-ttu-id="0f154-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="0f154-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="0f154-218">Kaizala 管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-218">Kaizala administrator</span></span> | <span data-ttu-id="0f154-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="0f154-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="0f154-220">许可证管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-220">License administrator</span></span> | <span data-ttu-id="0f154-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="0f154-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="0f154-222">消息中心隐私读取者</span><span class="sxs-lookup"><span data-stu-id="0f154-222">Message center privacy reader</span></span> | <span data-ttu-id="0f154-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="0f154-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="0f154-224">消息中心读取者</span><span class="sxs-lookup"><span data-stu-id="0f154-224">Message center reader</span></span> | <span data-ttu-id="0f154-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="0f154-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="0f154-226">Office 应用管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-226">Office apps administrator</span></span> | <span data-ttu-id="0f154-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="0f154-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="0f154-228">密码管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-228">Password administrator</span></span> | <span data-ttu-id="0f154-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="0f154-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="0f154-230">Power BI 管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-230">Power BI administrator</span></span> | <span data-ttu-id="0f154-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="0f154-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="0f154-232">Power Platform 管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-232">Power platform administrator</span></span> | <span data-ttu-id="0f154-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="0f154-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="0f154-234">特权身份验证管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-234">Privileged authentication administrator</span></span> | <span data-ttu-id="0f154-235">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="0f154-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="0f154-236">特权角色管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-236">Privileged role administrator</span></span> | <span data-ttu-id="0f154-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="0f154-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="0f154-238">报告读者</span><span class="sxs-lookup"><span data-stu-id="0f154-238">Reports reader</span></span> | <span data-ttu-id="0f154-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="0f154-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="0f154-240">搜索管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-240">Search administrator</span></span> | <span data-ttu-id="0f154-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="0f154-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="0f154-242">搜索编辑员</span><span class="sxs-lookup"><span data-stu-id="0f154-242">Search editor</span></span> | <span data-ttu-id="0f154-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="0f154-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="0f154-244">安全管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-244">Security administrator</span></span> | <span data-ttu-id="0f154-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="0f154-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="0f154-246">安全操作员</span><span class="sxs-lookup"><span data-stu-id="0f154-246">Security operator</span></span> | <span data-ttu-id="0f154-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="0f154-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="0f154-248">安全读取者</span><span class="sxs-lookup"><span data-stu-id="0f154-248">Security reader</span></span> | <span data-ttu-id="0f154-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="0f154-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="0f154-250">服务支持管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-250">Service support administrator</span></span> | <span data-ttu-id="0f154-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="0f154-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="0f154-252">SharePoint 管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-252">SharePoint administrator</span></span> | <span data-ttu-id="0f154-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="0f154-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="0f154-254">Skype for Business 管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-254">Skype for Business administrator</span></span> | <span data-ttu-id="0f154-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="0f154-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="0f154-256">Teams 通信管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-256">Teams Communications Administrator</span></span> | <span data-ttu-id="0f154-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="0f154-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="0f154-258">Teams 通信支持工程师</span><span class="sxs-lookup"><span data-stu-id="0f154-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="0f154-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="0f154-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="0f154-260">Teams 通信专家</span><span class="sxs-lookup"><span data-stu-id="0f154-260">Teams Communications Specialist</span></span> | <span data-ttu-id="0f154-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="0f154-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="0f154-262">Teams 服务管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-262">Teams Service Administrator</span></span> | <span data-ttu-id="0f154-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="0f154-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="0f154-264">用户管理员</span><span class="sxs-lookup"><span data-stu-id="0f154-264">User administrator</span></span> | <span data-ttu-id="0f154-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="0f154-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0f154-266">其他资源</span><span class="sxs-lookup"><span data-stu-id="0f154-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
