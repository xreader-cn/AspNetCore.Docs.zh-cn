---
title: ASP.NET Core 中的自定义授权策略提供程序
author: mjrousos
description: 了解如何在 ASP.NET Core 应用程序中使用自定义 IAuthorizationPolicyProvider 动态生成的授权策略。
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: b11f7f94e1042e65d5f2ff8ab0fe9ea7838bebeb
ms.sourcegitcommit: b1e480e1736b0fe0e4d8dce4a4cf5c8e47fc2101
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71108060"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>在 ASP.NET Core 中使用 IAuthorizationPolicyProvider 的自定义授权策略提供程序 

作者： [Mike Rousos](https://github.com/mjrousos)

通常，在使用[基于策略的授权](xref:security/authorization/policies)时，通过调用`AuthorizationOptions.AddPolicy`作为授权服务配置的一部分来注册策略。 在某些情况下，可能无法（或需要）以这种方式注册所有的授权策略。 在这些情况下，可以使用自定义`IAuthorizationPolicyProvider`来控制如何提供授权策略。

自定义[IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)可能有用的方案示例包括：

* 使用外部服务提供策略评估。
* 使用大范围的策略（例如，使用不同的房间号或年龄），因此，无`AuthorizationOptions.AddPolicy`需在调用中添加每个单独的授权策略。
* 根据外部数据源（如数据库）中的信息在运行时创建策略，或通过其他机制动态确定授权要求。

从[AspNetCore GitHub 存储库](https://github.com/aspnet/AspNetCore)[查看或下载示例代码](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)。 下载 aspnet/AspNetCore 存储库 ZIP 文件。 解压缩文件。 导航到*src/Security/samples/CustomPolicyProvider*项目文件夹。

## <a name="customize-policy-retrieval"></a>自定义策略检索

ASP.NET Core 应用使用的实现`IAuthorizationPolicyProvider`接口以检索授权策略。 默认情况下， [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)已注册并使用。 `DefaultAuthorizationPolicyProvider`返回`AuthorizationOptions` 调用`IServiceCollection.AddAuthorization`中提供的策略。

您可以通过在应用程序的`IAuthorizationPolicyProvider` [依赖关系注入](xref:fundamentals/dependency-injection)容器中注册不同的实现来自定义此行为。 

此`IAuthorizationPolicyProvider`接口包含两个 api：

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)返回给定名称的授权策略。
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)返回默认授权策略（用于`[Authorize]`未指定策略的属性的策略）。 

通过实现这两个 Api，你可以自定义如何提供授权策略。

## <a name="parameterized-authorize-attribute-example"></a>参数化授权特性示例

其中一种`IAuthorizationPolicyProvider`情况非常有用，即`[Authorize]`启用要求依赖于参数的自定义属性。 例如，在[基于策略的授权](xref:security/authorization/policies)文档中，使用基于时期的（"AtLeast21"）策略作为示例。 如果应用中的不同控制器操作应该在*不同*年龄段的用户中可用，则使用许多不同的基于时期的策略可能会很有用。 你可以使用自定义`AuthorizationOptions` `IAuthorizationPolicyProvider`动态生成策略，而不是注册应用程序将在中使用的所有基于时期的其他策略。 为了更轻松地使用这些策略，你可以使用自定义授权属性（ `[MinimumAgeAuthorize(20)]`例如）对操作进行批注。

## <a name="custom-authorization-attributes"></a>自定义授权属性

授权策略由其名称标识。 前面所`MinimumAgeAuthorizeAttribute`述的自定义需要将参数映射到一个字符串，该字符串可用于检索相应的授权策略。 可以通过从`AuthorizeAttribute`派生，并`Age`使属性换行`AuthorizeAttribute.Policy`属性来实现此目的。

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

此特性类型具有一个`Policy`基于硬编码前缀（`"MinimumAge"`）的字符串和一个通过构造函数传入的整数。

可以采用与其他`Authorize`属性相同的方式将其应用于操作，只不过它采用整数作为参数。

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>自定义 IAuthorizationPolicyProvider

自定义`MinimumAgeAuthorizeAttribute`可让你轻松地针对所需的最少年龄请求授权策略。 要解决的下一个问题是确保授权策略可用于所有这些不同年龄段。 这就`IAuthorizationPolicyProvider`是有用的。

使用`MinimumAgeAuthorizationAttribute`时，授权策略名称将遵循模式`"MinimumAge" + Age`，因此自定义`IAuthorizationPolicyProvider`应通过以下方式生成授权策略：

* 正在分析策略名称的期限。
* 使用`AuthorizationPolicyBuilder`创建新的`AuthorizationPolicy`
* 在此示例和以下示例中，假定用户通过 cookie 进行身份验证。 `AuthorizationPolicyBuilder`应至少用一个授权方案名称构造，或始终会成功。 否则，没有关于如何向用户提供质询的信息，将引发异常。
* 根据 age `AuthorizationPolicyBuilder.AddRequirements`将要求添加到策略。 在其他情况下，您可以`RequireClaim`使用`RequireRole`、或`RequireUserName` 。

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

使用自定义时`IAuthorizationPolicyProvider`实现中，请记住，ASP.NET Core 仅使用的一个实例`IAuthorizationPolicyProvider`。 如果自定义提供程序无法为将使用的所有策略名称提供授权策略，则它应回退到备份提供程序。 

例如，假设某个应用程序需要自定义年龄策略和更传统的基于角色的策略检索。 此类应用程序可使用自定义授权策略提供程序，该提供程序：

* 尝试分析策略名称。 
* 如果策略名称不包含 age，则`DefaultAuthorizationPolicyProvider`调用到不同的策略提供程序（如）。

上面所`IAuthorizationPolicyProvider`示的示例实现可以`DefaultAuthorizationPolicyProvider`通过在其构造函数中创建回退策略提供程序来进行更新，以便在策略名称与 "MinimumAge" + age 的预期模式不匹配时使用。

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

然后， `GetPolicyAsync`可以将方法更新为`FallbackPolicyProvider`使用而不是返回 null：

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>默认策略

除了提供命名的授权策略外，还需要`IAuthorizationPolicyProvider`实现`GetDefaultPolicyAsync`自定义，以提供未指定策略`[Authorize]`名称的属性的授权策略。

在许多情况下，此授权特性只需要经过身份验证的用户，因此你可以通过调用来`RequireAuthenticatedUser`进行所需的策略：

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

与自定义`IAuthorizationPolicyProvider`的所有方面一样，你可以根据需要对其进行自定义。 在某些情况下，可能需要检索回退`IAuthorizationPolicyProvider`的默认策略。

## <a name="required-policy"></a>必需的策略

要实现`IAuthorizationPolicyProvider` `GetRequiredPolicyAsync`的自定义需要，还可以提供始终需要的策略。 如果`GetRequiredPolicyAsync`返回非 null 策略，则该策略将与请求的任何其他（命名或默认）策略组合在一起。

如果不需要任何所需的策略，则提供程序可以仅返回 null 或延迟到回退提供程序：

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>使用自定义 IAuthorizationPolicyProvider

若要从`IAuthorizationPolicyProvider`使用自定义策略，你必须：

* 将适当`AuthorizationHandler`的类型注册到依赖关系注入（如[基于策略的授权](xref:security/authorization/policies#authorization-handlers)中所述），与所有基于策略的授权方案相同。
* 在应用程序`IAuthorizationPolicyProvider`的依赖关系注入服务集合（在中`Startup.ConfigureServices`为）中注册自定义类型，以替换默认策略提供程序。

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

`IAuthorizationPolicyProvider` [Aspnet/AuthSamples GitHub 存储库](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)中提供了一个完整的自定义示例。
