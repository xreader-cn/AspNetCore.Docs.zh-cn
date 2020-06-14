---
title: ASP.NET Core Blazor 具有 Azure Active Directory 组和角色的 WebAssembly
author: guardrex
description: 了解如何配置 Blazor WebAssembly 以使用 Azure Active Directory 组和角色。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/aad-groups-roles
ms.openlocfilehash: 87cdf02a6f6babc869d90658e6a7cd54db73bb68
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756023"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Azure AD 组、管理角色和用户定义的角色

作者： [Luke Latham](https://github.com/guardrex)和[Javier Calvarro 使用](https://github.com/javiercn)

Azure Active Directory （AAD）提供多种授权方法，这些方法可与 ASP.NET Core 组合 Identity ：

* 用户定义的组
  * 安全性
  * O365
  * 分布
* 角色
  * 内置管理角色
  * 用户定义的角色

本文中的指导适用于 Blazor 以下主题中所述的 WEBASSEMBLY AAD 部署方案：

* [包含 Microsoft 帐户的独立产品](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [包含 AAD 的独立产品](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [由 AAD 托管](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>用户定义的组和内置管理角色

若要在 Azure 门户中配置应用以提供 `groups` 成员身份声明，请参阅以下 Azure 文章。 将用户分配到用户定义的 AAD 组和内置管理角色。

* [使用 Azure AD 安全组的角色](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [groupMembershipClaims 属性](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

以下示例假定已将用户分配给 AAD 内置*计费管理员*角色。

AAD 发送的单个声明会将 `groups` 用户的组和角色显示为 JSON 数组中的对象 id （guid）。 应用必须将组和角色的 JSON 数组转换为 `group` 应用可针对其生成[策略](xref:security/authorization/policies)的单个声明。

扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 以包括组和角色的数组属性。

*CustomUserAccount.cs*：

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

在托管解决方案的独立应用程序或客户端应用程序中创建自定义用户工厂。 以下工厂还配置为处理 `roles` 声明数组，这些声明数组包含在[用户定义的角色](#user-defined-roles)部分中：

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

不需要提供删除原始声明的代码， `groups` 因为框架会自动删除它。

注册 `Program.Main` 托管解决方案的独立应用或客户端应用的工厂（*Program.cs*）：

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

为中的每个组或角色创建一个[策略](xref:security/authorization/policies) `Program.Main` 。 以下示例为 AAD 内置*计费管理员*角色创建策略：

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

有关 AAD 角色对象 Id 的完整列表，请参阅[Aad 管理角色组 id](#aad-adminstrative-role-group-ids)部分。

在下面的示例中，应用程序使用上述策略来对用户进行授权。

[AuthorizeView 组件](xref:security/blazor/index#authorizeview-component)适用于策略：

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

可以通过使用[ `[Authorize]` 属性指令](xref:security/blazor/index#authorize-attribute)（）对整个组件进行访问 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ：

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

如果用户未登录，则会将其重定向到 AAD 登录页，并在用户登录后返回到该组件。

还可以[在代码中使用过程逻辑执行](xref:security/blazor/index#procedural-logic)策略检查：

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

### <a name="user-defined-roles"></a>用户定义的角色

还可以将 AAD 注册的应用配置为使用用户定义的角色。

若要在 Azure 门户中配置应用以提供 `roles` 成员身份声明，请参阅[如何：在应用程序中添加应用程序角色和在 Azure 文档中的令牌中接收这些角色](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)。

下面的示例假定应用程序配置了两个角色：

* `admin`
* `developer`

> [!NOTE]
> 尽管不能将角色分配给没有 Azure AD Premium 帐户的安全组，但你可以将用户分配到角色，并接收 `roles` 具有标准 Azure 帐户的用户的声明。 本部分中的指导不需要 Azure AD Premium 帐户。
>
> 通过为每个附加角色分配**_重新添加用户_**，在 Azure 门户中分配多个角色。

`roles`AAD 发送的单个声明会将用户定义的角色显示为 `appRoles` `value` JSON 数组中的。 应用必须将角色的 JSON 数组转换为单独 `role` 的声明。

`CustomUserFactory`[用户定义组和 AAD 内置管理角色](#user-defined-groups-and-built-in-administrative-roles)部分中显示的设置为对 `roles` 具有 JSON 数组值的声明执行操作。 `CustomUserFactory`在托管解决方案的独立应用或客户端应用中添加和注册，如[用户定义的组和 AAD 内置管理角色](#user-defined-groups-and-built-in-administrative-roles)部分中所示。 不需要提供删除原始声明的代码， `roles` 因为框架会自动删除它。

在 `Program.Main` 托管解决方案的独立应用程序或客户端应用程序中，将名为 "" 的声明指定 `role` 为角色声明：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

此时，组件授权方法会起作用。 组件中的任何授权机制都可以使用 `admin` 角色来授权用户：

* [AuthorizeView 组件](xref:security/blazor/index#authorizeview-component)（示例： `<AuthorizeView Roles="admin">` ）
* [ `[Authorize]` attribute 指令](xref:security/blazor/index#authorize-attribute)（ <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ）（示例： `@attribute [Authorize(Roles = "admin")]` ）
* [过程逻辑](xref:security/blazor/index#procedural-logic)（示例： `if (user.IsInRole("admin")) { ... }` ）

  支持多个角色测试：

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>AAD 管理角色组 Id

下表中提供的对象 Id 用于创建声明[策略](xref:security/authorization/policies) `group` 。 策略允许应用向用户授权应用中的各种活动。 有关详细信息，请参阅[用户定义的组和 AAD 内置管理角色](#user-defined-groups-and-built-in-administrative-roles)部分。

AAD 管理角色 | 对象 ID
--- | ---
应用程序管理员 | fa11557b-4f15-4ddd-85d5-313c7cd74047
应用程序开发人员 | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
身份验证管理员 | 02d110a1-96b1-419e-af87-746461b60ed7
Azure DevOps 管理员 | a5311ace-ca41-44cd-b833-8d22caa0b34f
Azure 信息保护管理员 | 18632dce-f9b5-4f01-abb5-37051f06860e
B2C IEF 键集管理员 | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
B2C IEF 策略管理员 | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
B2C 用户流管理员 | baa531b7-8cf0-44ad-8f98-eded88dae827
B2C 用户流属性管理员 | dd0baca0-a535-48c1-b871-8431abe16452
计费管理员 | 69ff516a-b57d-4697-a429-9de4af7b5609
云应用程序管理员 | 250b5fe3-b553-458d-9a53-b782c13c34bf
云设备管理员 | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
法规管理员 | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
合规性数据管理员 | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
条件访问管理员 | 8f71a611-137d-49af-87ad-e97f1fd5da76
客户密码箱访问审批人 | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
桌面分析管理员 | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
目录读者 | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Dynamics 365 管理员 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Exchange 管理员 | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
外部 Identity 提供商管理员 | febfaeb4-e478-407a-b4b3-f4d9716618a2
全局管理员 | a45ba61b-44db-462c-924b-3b2719152588
全局读取者 | f6903b21-6aba-4124-b44c-76671796b9d5
组管理员 | 158b3e5a-d89d-460b-92b5-3b34985f0197
来宾邀请者 | 4c730a1d-cc22-44af-8f9f-4eec635c7502
支持管理员 | 108678c8-6628-44e1-8d01-caf598a6a5f5
Intune 管理员 | 79950741-23fa-4189-b2cb-46640601c497
Kaizala 管理员 | d6322af2-48e7-42e0-8c68-0bbe31af3412
许可证管理员 | 3355458a-e423-44bf-8b98-4ac5e572cea5
消息中心隐私读取者 | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
消息中心读取者 | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Office 应用管理员 | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
密码管理员 | 466e48b7-5d66-4ae5-8911-1a118de74941
Power BI 管理员 | 984e83b8-8337-4255-91a1-acb663175ab4
Power Platform 管理员 | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
特权身份验证管理员 | 0829f731-b46d-419f-9742-aeb122367d11
特权角色管理员 | f20a725a-d1c8-4107-83ea-1171c97d00c7
报告读者 | 54635450-e8ed-4f2d-9632-07db2517b4de
搜索管理员 | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
搜索编辑员 | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
安全管理员 | 20fa50e3-6531-44d8-bd39-b251420568ad
安全操作员 | 43aae017-8e51-4188-91ab-e6debd572800
安全读取者 | 45035cd3-fd97-4250-8197-3a53d3562d9b
服务支持管理员 | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
SharePoint 管理员 | e1c32229-875e-461d-ae24-3cb99116e86c
Skype for Business 管理员 | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Teams 通信管理员 | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Teams 通信支持工程师 | 802dd94e-d717-46f6-af98-b9167071e9fc
团队通信专家 | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Teams 服务管理员 | 8846a0be-197b-443a-b13c-11192691fa24
用户管理员 | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
