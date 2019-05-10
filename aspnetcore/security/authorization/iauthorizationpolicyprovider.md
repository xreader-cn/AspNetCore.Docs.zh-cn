---
title: ASP.NET Core 中的自定义授权策略提供程序
author: mjrousos
description: 了解如何在 ASP.NET Core 应用程序中使用自定义 IAuthorizationPolicyProvider 动态生成的授权策略。
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: e17372bb0ec9091c385a70b1e907eaa3cff24003
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896354"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>在 ASP.NET Core 中使用 IAuthorizationPolicyProvider 的自定义授权策略提供程序 

通过[Mike Rousos](https://github.com/mjrousos)

通常使用时[基于策略的授权](xref:security/authorization/policies)，通过调用注册策略`AuthorizationOptions.AddPolicy`作为授权服务配置的一部分。 在某些情况下，它可能不是可能 （或最好） 若要以这种方式注册所有授权策略。 在这些情况下，你可以使用自定义`IAuthorizationPolicyProvider`来控制如何提供授权策略。

方案的示例在一个自定义[IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)可能会很有用包括：

* 使用外部服务提供的策略评估。
* （适用于不同的房间号或年龄，例如） 使用了大量的策略，因此它不适合将与每个单个授权策略添加`AuthorizationOptions.AddPolicy`调用。
* 在运行时基于外部数据源 （如数据库） 中的信息创建策略或通过其他机制动态确定授权要求。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)从[AspNetCore GitHub 存储库](https://github.com/aspnet/AspNetCore)。 Aspnet/AspNetCore 存储库 ZIP 文件下载。 将文件解压缩。 导航到*src/Security/示例/CustomPolicyProvider*项目文件夹。

## <a name="customize-policy-retrieval"></a>自定义策略检索

ASP.NET Core 应用使用的实现`IAuthorizationPolicyProvider`接口以检索授权策略。 默认情况下[DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)注册和使用。 `DefaultAuthorizationPolicyProvider` 返回从策略`AuthorizationOptions`中提供`IServiceCollection.AddAuthorization`调用。

可以通过注册其他自定义此行为`IAuthorizationPolicyProvider`在应用中的实现[依赖关系注入](xref:fundamentals/dependency-injection)容器。 

`IAuthorizationPolicyProvider`接口包含两个 Api:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)返回具有给定名称的授权策略。
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)返回的默认授权策略 (用于策略`[Authorize]`而无需指定的策略的属性)。 

通过实现这两个 Api，可以自定义提供的授权策略的方式。

## <a name="parameterized-authorize-attribute-example"></a>参数化授权特性的示例

一种情况其中`IAuthorizationPolicyProvider`可启用自定义`[Authorize]`其要求取决于参数的属性。 例如，在[基于策略的授权](xref:security/authorization/policies)文档中，基于年龄的 ("AtLeast21") 作为一个示例所使用的策略。 如果在应用中的不同控制器操作应该对可用的用户*不同*年龄，可能会很有用具有许多不同的基于年龄的策略。 而不是注册所有不同年龄基于策略的应用程序中会用`AuthorizationOptions`，可以生成动态使用自定义策略`IAuthorizationPolicyProvider`。 要使用的策略更轻松，你可以批注具有类似的自定义授权属性操作`[MinimumAgeAuthorize(20)]`。

## <a name="custom-authorization-attributes"></a>自定义授权属性

通过名称标识的授权策略。 自定义`MinimumAgeAuthorizeAttribute`所述之前需要将参数映射到一个字符串，可用于检索相应的授权策略。 您可以执行此操作通过派生自`AuthorizeAttribute`并使`Age`属性包装`AuthorizeAttribute.Policy`属性。

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

此属性类型具有`Policy`字符串基于的硬编码的前缀 (`"MinimumAge"`) 和一个整数，通过构造函数中传递。

可以将其应用于操作中方式不同于其他`Authorize`属性，但前者作为参数的整数。

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Custom IAuthorizationPolicyProvider

自定义`MinimumAgeAuthorizeAttribute`可以方便地对请求授权策略的任何所需的最短期限。 要解决的下一个问题确保为所有这些不同年龄段的授权策略可用。 这就是`IAuthorizationPolicyProvider`非常有用。

使用时`MinimumAgeAuthorizationAttribute`，授权策略名称将遵循的模式`"MinimumAge" + Age`，因此自定义`IAuthorizationPolicyProvider`应生成的授权策略：

* 分析将年龄字段从策略名称。
* 使用`AuthorizationPolicyBuilder`若要创建一个新 `AuthorizationPolicy`
* 向策略添加要求根据与保留`AuthorizationPolicyBuilder.AddRequirements`。 在其他情况下，可能会使用`RequireClaim`， `RequireRole`，或`RequireUserName`相反。

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
            var policy = new AuthorizationPolicyBuilder();
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>多个授权策略提供程序

使用自定义时`IAuthorizationPolicyProvider`实现中，请记住，ASP.NET Core 仅使用的一个实例`IAuthorizationPolicyProvider`。 如果自定义提供程序不能提供的所有策略名称，将使用授权策略，它应回退到备份的提供程序。 

例如，请考虑需要自定义保留时间策略和更传统的基于角色的策略检索的应用程序。 此类应用可以使用自定义授权策略提供程序的：

* 尝试分析策略名称。 
* 调入不同的策略提供程序 (如`DefaultAuthorizationPolicyProvider`) 如果策略名称不包含年龄。

该示例`IAuthorizationPolicyProvider`如上所示的实现可以更新为使用`DefaultAuthorizationPolicyProvider`通过在其构造函数 （若要在策略名称与 MinimumAge + 年龄的其预期的模式不匹配的情况下使用） 中创建一个回退策略提供程序。

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

然后，将`GetPolicyAsync`方法可以更新为使用`FallbackPolicyProvider`而不是返回 null:

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>默认策略

除了提供命名的授权策略，自定义`IAuthorizationPolicyProvider`需要实现`GetDefaultPolicyAsync`提供的授权策略`[Authorize]`属性没有指定策略名称。

在许多情况下，此授权属性仅要求身份验证的用户，这样你就可以通过调用必要的策略`RequireAuthenticatedUser`:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build());
```

自定义的所有方面与`IAuthorizationPolicyProvider`，你可以自定义此操作，请根据需要。 在某些情况下，它可能需要从回退检索默认策略`IAuthorizationPolicyProvider`。

## <a name="required-policy"></a>所需的策略

自定义`IAuthorizationPolicyProvider`需要实现`GetRequiredPolicyAsync`以，（可选） 提供始终是必需的策略。 如果`GetRequiredPolicyAsync`返回非 null 策略，该策略将结合任何其他 （名为或默认值） 请求的策略。

如果需要不必要的策略，则提供程序可以只是返回 null 或推迟到回退提供程序：

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>使用自定义 IAuthorizationPolicyProvider

若要使用自定义策略从`IAuthorizationPolicyProvider`，您必须：

* 注册的相应`AuthorizationHandler`类型使用依赖关系注入 (中所述[基于策略的授权](xref:security/authorization/policies#authorization-handlers))，与所有基于策略的授权方案。
* 注册自定义`IAuthorizationPolicyProvider`应用程序的依赖关系注入服务集合中的类型 (在`Startup.ConfigureServices`) 来替换默认策略提供程序。

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

完成自定义`IAuthorizationPolicyProvider`示例现已推出[aspnet/AuthSamples GitHub 存储库](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)。
