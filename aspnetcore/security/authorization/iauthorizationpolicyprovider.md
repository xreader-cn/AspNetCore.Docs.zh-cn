---
title: ASP.NET核心中的自定义授权策略提供程序
author: mjrousos
description: 了解如何在ASP.NET核心应用中使用自定义 I授权策略提供程序动态生成授权策略。
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2c67e25ff73bc8c3a5f3af4730a509b2385fc1cf
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661774"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>自定义授权策略提供程序使用 i 授权策略提供程序ASP.NET核心 

作者：[Mike Rousos](https://github.com/mjrousos)

通常，在使用[基于策略的授权](xref:security/authorization/policies)时，策略是通过调用`AuthorizationOptions.AddPolicy`作为授权服务配置的一部分进行注册的。 在某些情况下，不可能（或不需要）以这种方式注册所有授权策略。 在这些情况下，可以使用自定义来控制`IAuthorizationPolicyProvider`如何提供授权策略。

自定义[I授权策略提供程序](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)可能有用的方案示例包括：

* 使用外部服务提供策略评估。
* 使用大量策略（例如，针对不同的房间号码或年龄），因此使用`AuthorizationOptions.AddPolicy`调用添加每个单独的授权策略没有意义。
* 根据外部数据源（如数据库）中的信息在运行时创建策略，或通过另一种机制动态确定授权要求。

从[AspNetCore GitHub 存储库](https://github.com/dotnet/AspNetCore)[查看或下载示例代码](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)。 下载点网/阿斯普内科存储库 ZIP 文件。 解压缩文件。 导航到*src/安全/示例/自定义策略提供程序*项目文件夹。

## <a name="customize-policy-retrieval"></a>自定义策略检索

ASP.NET核心应用使用接口的`IAuthorizationPolicyProvider`实现来检索授权策略。 默认情况下，[默认授权策略提供程序](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)已注册和使用。 `DefaultAuthorizationPolicyProvider`从`IServiceCollection.AddAuthorization`呼叫中提供`AuthorizationOptions`的策略返回策略。

通过在应用[的依赖项注入](xref:fundamentals/dependency-injection)容器中注册`IAuthorizationPolicyProvider`不同的实现来自定义此行为。 

该`IAuthorizationPolicyProvider`接口包含三个 API：

* [获取策略同步](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)返回给定名称的授权策略。
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)返回默认授权策略（用于`[Authorize]`未指定策略的属性的策略）。 
* [GetBackbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync)返回回退授权策略（未指定策略时授权中间件使用的策略）。 

通过实现这些 API，您可以自定义如何提供授权策略。

## <a name="parameterized-authorize-attribute-example"></a>参数化授权属性示例

一`IAuthorizationPolicyProvider`个有用的方案是启用其要求`[Authorize]`依赖于参数的自定义属性。 例如，[在基于策略的授权](xref:security/authorization/policies)文档中，使用基于年龄（"AtLeast21"）的策略作为示例。 如果应用中的不同控制器操作应提供给*不同*年龄的用户，则使用许多不同的基于年龄的策略可能很有用。 无需注册应用程序将需要的所有`AuthorizationOptions`不同基于年龄的策略，而是可以使用自定义`IAuthorizationPolicyProvider`动态生成策略。 为了简化使用策略，可以使用自定义授权属性（如`[MinimumAgeAuthorize(20)]`）对操作进行分给。

## <a name="custom-authorization-attributes"></a>自定义授权属性

授权策略由其名称标识。 前面描述的`MinimumAgeAuthorizeAttribute`自定义需要将参数映射到可用于检索相应授权策略的字符串中。 可以通过派生和`AuthorizeAttribute`使`Age`属性换行属性`AuthorizeAttribute.Policy`来执行此操作。

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

此属性类型具有一个`Policy`基于硬编码前缀 （`"MinimumAge"`） 的字符串，以及通过构造函数传入的整数。

可以将其应用于操作的方式与其他`Authorize`属性相同，只不过它采用整数作为参数。

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>自定义授权策略提供程序

该自定义`MinimumAgeAuthorizeAttribute`使请求任何所需的最低年龄的授权策略变得容易。 要解决的下一个问题是确保授权策略可用于所有这些不同年龄。 这是有用的`IAuthorizationPolicyProvider`。

使用`MinimumAgeAuthorizationAttribute`时，授权策略名称将遵循模式`"MinimumAge" + Age`，因此自定义`IAuthorizationPolicyProvider`应通过以下方式生成授权策略：

* 从策略名称分析年龄。
* 用于`AuthorizationPolicyBuilder`创建新`AuthorizationPolicy`
* 在此和下面的示例中，假定用户通过 Cookie 进行身份验证。 `AuthorizationPolicyBuilder`应至少使用一个授权方案名称构造 ，或者始终成功。 否则，没有关于如何向用户提供质询的信息，并且将引发异常。
* 根据 具有`AuthorizationPolicyBuilder.AddRequirements`的年龄向策略添加要求。 在其他方案中，您可以使用 ， 或`RequireClaim``RequireUserName`改为`RequireRole`使用 。

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

使用自定义`IAuthorizationPolicyProvider`实现时，请记住，ASP.NET Core 仅使用 的`IAuthorizationPolicyProvider`一个实例。 如果自定义提供程序无法为将使用的所有策略名称提供授权策略，则应将其服从备份提供程序。 

例如，考虑一个既需要自定义年龄策略又需要更传统的基于角色的策略检索的应用程序。 此类应用可以使用自定义授权策略提供程序：

* 尝试分析策略名称。 
* 如果策略名称不包含年龄，则调用`DefaultAuthorizationPolicyProvider`其他策略提供程序（如 ）。

可以通过在其`IAuthorizationPolicyProvider`构造函数中创建备份策略提供程序来更新`DefaultAuthorizationPolicyProvider`上述示例实现以使用 （在策略名称与其预期模式"最小年龄"+ 年龄不匹配时使用）。

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

然后，`GetPolicyAsync`可以更新该方法以使用`BackupPolicyProvider`而不是返回 null：

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>默认策略

除了提供命名授权策略外，还需要`IAuthorizationPolicyProvider`实现`GetDefaultPolicyAsync`为未指定策略名称`[Authorize]`的属性提供授权策略。

在许多情况下，此授权属性只需要经过身份验证的用户，因此可以使用 调用`RequireAuthenticatedUser`：

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

与自定义`IAuthorizationPolicyProvider`的所有方面一样，您可以根据需要自定义此内容。 在某些情况下，可能需要从回退`IAuthorizationPolicyProvider`检索默认策略。

## <a name="fallback-policy"></a>回退策略

自定义`IAuthorizationPolicyProvider`可以选择实现`GetFallbackPolicyAsync`以提供在[组合策略](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine)时和未指定策略时使用的策略。 如果`GetFallbackPolicyAsync`返回非空策略，则当未为请求指定策略时，授权中间件将使用返回的策略。

如果不需要回退策略，提供程序可以返回`null`或延迟回退提供程序：

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>使用自定义 I 授权策略提供程序

要使用 的自定义策略`IAuthorizationPolicyProvider`，必须：

* 使用依赖项`AuthorizationHandler`注入注册适当的类型（在[基于策略的授权](xref:security/authorization/policies#authorization-handlers)中描述），与所有基于策略的授权方案一样。
* 在应用的依赖`IAuthorizationPolicyProvider`项注入服务集合（在 中）`Startup.ConfigureServices`中注册自定义类型以替换默认策略提供程序。

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

完整的自定义`IAuthorizationPolicyProvider`示例在[dotnet/aspnetcore GitHub 存储库](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)中可用。
