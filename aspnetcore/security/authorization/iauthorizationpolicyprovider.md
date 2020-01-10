---
title: ASP.NET Core 中的自定义授权策略提供程序
author: mjrousos
description: 了解如何在 ASP.NET Core 应用程序中使用自定义 IAuthorizationPolicyProvider 动态生成的授权策略。
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 9f0a0cd5337f7f8d2fc8a4b6902a63b98f6bd702
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828979"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>在 ASP.NET Core 中使用 IAuthorizationPolicyProvider 的自定义授权策略提供程序 

作者：[Mike Rousos](https://github.com/mjrousos)

通常，在使用[基于策略的授权](xref:security/authorization/policies)时，通过调用 `AuthorizationOptions.AddPolicy` 作为授权服务配置的一部分来注册策略。 在某些情况下，可能无法（或需要）以这种方式注册所有的授权策略。 在这些情况下，可以使用自定义 `IAuthorizationPolicyProvider` 来控制如何提供授权策略。

自定义[IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)可能有用的方案示例包括：

* 使用外部服务提供策略评估。
* 使用大范围的策略（例如，使用不同的房间号或年龄），因此，将每个授权策略添加到 `AuthorizationOptions.AddPolicy` 调用并无意义。
* 根据外部数据源（如数据库）中的信息在运行时创建策略，或通过其他机制动态确定授权要求。

从[AspNetCore GitHub 存储库](https://github.com/dotnet/AspNetCore)[查看或下载示例代码](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)。 下载 dotnet/AspNetCore 存储库 ZIP 文件。 解压缩文件。 导航到*src/Security/samples/CustomPolicyProvider*项目文件夹。

## <a name="customize-policy-retrieval"></a>自定义策略检索

ASP.NET Core 应用使用的实现`IAuthorizationPolicyProvider`接口以检索授权策略。 默认情况下， [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)已注册并使用。 `DefaultAuthorizationPolicyProvider` 返回 `IServiceCollection.AddAuthorization` 调用中提供的 `AuthorizationOptions` 的策略。

自定义此行为，方法是在应用程序的[依赖关系注入](xref:fundamentals/dependency-injection)容器中注册不同的 `IAuthorizationPolicyProvider` 实现。 

`IAuthorizationPolicyProvider` 接口包含三个 Api：

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)返回给定名称的授权策略。
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)返回默认授权策略（用于未指定策略的 `[Authorize]` 属性的策略）。 
* 当未指定策略时， [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync)将返回后备授权策略（授权中间件所使用的策略）。 

通过实现这些 Api，你可以自定义如何提供授权策略。

## <a name="parameterized-authorize-attribute-example"></a>参数化授权特性示例

`IAuthorizationPolicyProvider` 很有用的一种情况是，启用其要求依赖于参数的自定义 `[Authorize]` 特性。 例如，在[基于策略的授权](xref:security/authorization/policies)文档中，使用基于时期的（"AtLeast21"）策略作为示例。 如果应用中的不同控制器操作应该在*不同*年龄段的用户中可用，则使用许多不同的基于时期的策略可能会很有用。 您可以使用自定义 `IAuthorizationPolicyProvider`动态生成策略，而不是注册应用程序 `AuthorizationOptions`所需的所有基于时期的不同策略。 若要更轻松地使用策略，可以使用自定义授权属性（如 `[MinimumAgeAuthorize(20)]`）来批注操作。

## <a name="custom-authorization-attributes"></a>自定义授权属性

授权策略由其名称标识。 前面所述的自定义 `MinimumAgeAuthorizeAttribute` 需要将参数映射到一个字符串，该字符串可用于检索相应的授权策略。 为此，可以从 `AuthorizeAttribute` 派生，并使 `Age` 属性包装 `AuthorizeAttribute.Policy` 属性。

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

此属性类型具有基于硬编码前缀（`"MinimumAge"`）的 `Policy` 字符串，以及通过构造函数传入的整数。

可以采用与其他 `Authorize` 特性相同的方式将其应用于操作，只不过它采用整数作为参数。

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>自定义 IAuthorizationPolicyProvider

使用自定义 `MinimumAgeAuthorizeAttribute` 可以轻松地请求所需的最短期限的授权策略。 要解决的下一个问题是确保授权策略可用于所有这些不同年龄段。 这就是 `IAuthorizationPolicyProvider` 有用的地方。

使用 `MinimumAgeAuthorizationAttribute`时，授权策略名称将遵循模式 `"MinimumAge" + Age`，因此自定义 `IAuthorizationPolicyProvider` 应通过以下方式生成授权策略：

* 正在分析策略名称的期限。
* 使用 `AuthorizationPolicyBuilder` 创建新 `AuthorizationPolicy`
* 在此示例和以下示例中，假定用户通过 cookie 进行身份验证。 应至少用一个授权方案名称构造 `AuthorizationPolicyBuilder` 或始终成功。 否则，没有关于如何向用户提供质询的信息，将引发异常。
* 根据 `AuthorizationPolicyBuilder.AddRequirements`的年龄将要求添加到策略。 在其他情况下，你可能会改用 `RequireClaim`、`RequireRole`或 `RequireUserName`。

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>多个授权策略提供程序

使用自定义时`IAuthorizationPolicyProvider`实现中，请记住，ASP.NET Core 仅使用的一个实例`IAuthorizationPolicyProvider`。 如果自定义提供程序无法为将使用的所有策略名称提供授权策略，则该提供程序应遵从备份提供程序。 

例如，假设某个应用程序需要自定义年龄策略和更传统的基于角色的策略检索。 此类应用程序可使用自定义授权策略提供程序，该提供程序：

* 尝试分析策略名称。 
* 如果策略名称不包含 age，则调入到不同的策略提供程序（如 `DefaultAuthorizationPolicyProvider`）。

可以通过在其构造函数中创建备份策略提供程序来更新上面所示的示例 `IAuthorizationPolicyProvider` 实现使用 `DefaultAuthorizationPolicyProvider` （如果策略名称与 "MinimumAge" + age 的预期模式不符，则使用此方法）。

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

然后，可以将 `GetPolicyAsync` 方法更新为使用 `BackupPolicyProvider`，而不是返回 null：

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>默认策略

自定义 `IAuthorizationPolicyProvider` 除了提供命名的授权策略外，还需要实现 `GetDefaultPolicyAsync`，以提供 `[Authorize]` 属性的授权策略，而无需指定策略名称。

在许多情况下，此授权特性只需要经过身份验证的用户，因此，你可以通过调用 `RequireAuthenticatedUser`来执行必要的策略：

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

与自定义 `IAuthorizationPolicyProvider`的所有方面一样，你可以根据需要对其进行自定义。 在某些情况下，可能需要从回退 `IAuthorizationPolicyProvider`检索默认策略。

## <a name="fallback-policy"></a>回退策略

自定义 `IAuthorizationPolicyProvider` 可以选择实现 `GetFallbackPolicyAsync` 来提供在[结合策略](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine)时使用的策略，以及未指定策略时使用的策略。 如果 `GetFallbackPolicyAsync` 返回非 null 策略，则在未为请求指定策略时，授权中间件会使用返回的策略。

如果不需要回退策略，则提供程序可以返回 `null` 或将其推迟到回退提供程序：

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>使用自定义 IAuthorizationPolicyProvider

若要从 `IAuthorizationPolicyProvider`使用自定义策略，你必须：

* 与所有基于策略的授权方案一起，用依赖关系注入注册适当的 `AuthorizationHandler` 类型（如[基于策略的授权](xref:security/authorization/policies#authorization-handlers)中所述）。
* 在应用程序的依赖关系注入服务集合中注册自定义 `IAuthorizationPolicyProvider` 类型（在 `Startup.ConfigureServices`中）以替换默认策略提供程序。

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

[Aspnet/AuthSamples GitHub 存储库](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)中提供了一个完整的自定义 `IAuthorizationPolicyProvider` 示例。
