---
title: ASP.NET Core 中的自定义授权策略提供程序
author: mjrousos
description: 了解如何在 ASP.NET Core 应用程序中使用自定义 IAuthorizationPolicyProvider 动态生成授权策略。
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: c104de2903867b3257e5bde5b55dfcb256499746
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755789"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="b83d8-103">在 ASP.NET Core 中使用 IAuthorizationPolicyProvider 的自定义授权策略提供程序</span><span class="sxs-lookup"><span data-stu-id="b83d8-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="b83d8-104">作者：[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="b83d8-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="b83d8-105">通常，在使用[基于策略的授权](xref:security/authorization/policies)时，通过调用 `AuthorizationOptions.AddPolicy` 作为授权服务配置的一部分来注册策略。</span><span class="sxs-lookup"><span data-stu-id="b83d8-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="b83d8-106">在某些情况下，可能无法（或需要）以这种方式注册所有的授权策略。</span><span class="sxs-lookup"><span data-stu-id="b83d8-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="b83d8-107">在这些情况下，可以[使用自定义 `IAuthorizationPolicyProvider` ](#ci)来控制如何提供授权策略。</span><span class="sxs-lookup"><span data-stu-id="b83d8-107">In those cases, you can [use a custom `IAuthorizationPolicyProvider`](#ci) to control how authorization policies are supplied.</span></span>

<span data-ttu-id="b83d8-108">自定义[IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)可能有用的方案示例包括：</span><span class="sxs-lookup"><span data-stu-id="b83d8-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="b83d8-109">使用外部服务提供策略评估。</span><span class="sxs-lookup"><span data-stu-id="b83d8-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="b83d8-110">使用大范围的策略（例如，使用不同的房间号或年龄），因此，无需在调用中添加每个单独的授权策略 `AuthorizationOptions.AddPolicy` 。</span><span class="sxs-lookup"><span data-stu-id="b83d8-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="b83d8-111">根据外部数据源（如数据库）中的信息在运行时创建策略，或通过其他机制动态确定授权要求。</span><span class="sxs-lookup"><span data-stu-id="b83d8-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="b83d8-112">从[AspNetCore GitHub 存储库](https://github.com/dotnet/AspNetCore)[查看或下载示例代码](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)。</span><span class="sxs-lookup"><span data-stu-id="b83d8-112">[View or download sample code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="b83d8-113">下载 dotnet/AspNetCore 存储库 ZIP 文件。</span><span class="sxs-lookup"><span data-stu-id="b83d8-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="b83d8-114">解压缩文件。</span><span class="sxs-lookup"><span data-stu-id="b83d8-114">Unzip the file.</span></span> <span data-ttu-id="b83d8-115">导航到*src/Security/samples/CustomPolicyProvider*项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="b83d8-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="b83d8-116">自定义策略检索</span><span class="sxs-lookup"><span data-stu-id="b83d8-116">Customize policy retrieval</span></span>

<span data-ttu-id="b83d8-117">ASP.NET Core 应用使用接口的实现 `IAuthorizationPolicyProvider` 来检索授权策略。</span><span class="sxs-lookup"><span data-stu-id="b83d8-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="b83d8-118">默认情况下， [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)已注册并使用。</span><span class="sxs-lookup"><span data-stu-id="b83d8-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="b83d8-119">`DefaultAuthorizationPolicyProvider`返回 `AuthorizationOptions` 调用中提供的策略 `IServiceCollection.AddAuthorization` 。</span><span class="sxs-lookup"><span data-stu-id="b83d8-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="b83d8-120">通过 `IAuthorizationPolicyProvider` 在应用程序的[依赖关系注入](xref:fundamentals/dependency-injection)容器中注册不同的实现来自定义此行为。</span><span class="sxs-lookup"><span data-stu-id="b83d8-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="b83d8-121">此 `IAuthorizationPolicyProvider` 接口包含三个 api：</span><span class="sxs-lookup"><span data-stu-id="b83d8-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="b83d8-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)返回给定名称的授权策略。</span><span class="sxs-lookup"><span data-stu-id="b83d8-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="b83d8-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)返回默认授权策略（用于 `[Authorize]` 未指定策略的属性的策略）。</span><span class="sxs-lookup"><span data-stu-id="b83d8-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="b83d8-124">当未指定策略时， [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync)将返回后备授权策略（授权中间件所使用的策略）。</span><span class="sxs-lookup"><span data-stu-id="b83d8-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="b83d8-125">通过实现这些 Api，你可以自定义如何提供授权策略。</span><span class="sxs-lookup"><span data-stu-id="b83d8-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="b83d8-126">参数化授权特性示例</span><span class="sxs-lookup"><span data-stu-id="b83d8-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="b83d8-127">其中一种情况非常 `IAuthorizationPolicyProvider` 有用，即启用要求依赖于参数的自定义 `[Authorize]` 属性。</span><span class="sxs-lookup"><span data-stu-id="b83d8-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="b83d8-128">例如，在[基于策略的授权](xref:security/authorization/policies)文档中，使用基于时期的（"AtLeast21"）策略作为示例。</span><span class="sxs-lookup"><span data-stu-id="b83d8-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="b83d8-129">如果应用中的不同控制器操作应该在*不同*年龄段的用户中可用，则使用许多不同的基于时期的策略可能会很有用。</span><span class="sxs-lookup"><span data-stu-id="b83d8-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="b83d8-130">`AuthorizationOptions`你可以使用自定义动态生成策略，而不是注册应用程序将在中使用的所有基于时期的其他策略 `IAuthorizationPolicyProvider` 。</span><span class="sxs-lookup"><span data-stu-id="b83d8-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="b83d8-131">为了更轻松地使用这些策略，你可以使用自定义授权属性（例如）对操作进行批注 `[MinimumAgeAuthorize(20)]` 。</span><span class="sxs-lookup"><span data-stu-id="b83d8-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="b83d8-132">自定义授权属性</span><span class="sxs-lookup"><span data-stu-id="b83d8-132">Custom Authorization attributes</span></span>

<span data-ttu-id="b83d8-133">授权策略由其名称标识。</span><span class="sxs-lookup"><span data-stu-id="b83d8-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="b83d8-134">`MinimumAgeAuthorizeAttribute`前面所述的自定义需要将参数映射到一个字符串，该字符串可用于检索相应的授权策略。</span><span class="sxs-lookup"><span data-stu-id="b83d8-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="b83d8-135">可以通过从派生 `AuthorizeAttribute` ，并使 `Age` 属性换行属性来实现此目的 `AuthorizeAttribute.Policy` 。</span><span class="sxs-lookup"><span data-stu-id="b83d8-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="b83d8-136">此特性类型具有一个 `Policy` 基于硬编码前缀（）的字符串 `"MinimumAge"` 和一个通过构造函数传入的整数。</span><span class="sxs-lookup"><span data-stu-id="b83d8-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="b83d8-137">可以采用与其他属性相同的方式将其应用于操作， `Authorize` 只不过它采用整数作为参数。</span><span class="sxs-lookup"><span data-stu-id="b83d8-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="b83d8-138">自定义 IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="b83d8-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="b83d8-139">自定义 `MinimumAgeAuthorizeAttribute` 可让你轻松地针对所需的最少年龄请求授权策略。</span><span class="sxs-lookup"><span data-stu-id="b83d8-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="b83d8-140">要解决的下一个问题是确保授权策略可用于所有这些不同年龄段。</span><span class="sxs-lookup"><span data-stu-id="b83d8-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="b83d8-141">这就是 `IAuthorizationPolicyProvider` 有用的。</span><span class="sxs-lookup"><span data-stu-id="b83d8-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="b83d8-142">使用时 `MinimumAgeAuthorizationAttribute` ，授权策略名称将遵循模式 `"MinimumAge" + Age` ，因此自定义 `IAuthorizationPolicyProvider` 应通过以下方式生成授权策略：</span><span class="sxs-lookup"><span data-stu-id="b83d8-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="b83d8-143">正在分析策略名称的期限。</span><span class="sxs-lookup"><span data-stu-id="b83d8-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="b83d8-144">使用 `AuthorizationPolicyBuilder` 创建新的`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="b83d8-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="b83d8-145">在此示例和以下示例中，假定用户通过 cookie 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="b83d8-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="b83d8-146">`AuthorizationPolicyBuilder`应至少用一个授权方案名称构造，或始终会成功。</span><span class="sxs-lookup"><span data-stu-id="b83d8-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="b83d8-147">否则，没有关于如何向用户提供质询的信息，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="b83d8-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="b83d8-148">根据 age 将要求添加到策略 `AuthorizationPolicyBuilder.AddRequirements` 。</span><span class="sxs-lookup"><span data-stu-id="b83d8-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="b83d8-149">在其他情况下，您可以使用 `RequireClaim` 、 `RequireRole` 或 `RequireUserName` 。</span><span class="sxs-lookup"><span data-stu-id="b83d8-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="b83d8-150">多个授权策略提供程序</span><span class="sxs-lookup"><span data-stu-id="b83d8-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="b83d8-151">使用自定义 `IAuthorizationPolicyProvider` 实现时，请记住 ASP.NET Core 仅使用一个实例 `IAuthorizationPolicyProvider` 。</span><span class="sxs-lookup"><span data-stu-id="b83d8-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="b83d8-152">如果自定义提供程序无法为将使用的所有策略名称提供授权策略，则该提供程序应遵从备份提供程序。</span><span class="sxs-lookup"><span data-stu-id="b83d8-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="b83d8-153">例如，假设某个应用程序需要自定义年龄策略和更传统的基于角色的策略检索。</span><span class="sxs-lookup"><span data-stu-id="b83d8-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="b83d8-154">此类应用程序可使用自定义授权策略提供程序，该提供程序：</span><span class="sxs-lookup"><span data-stu-id="b83d8-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="b83d8-155">尝试分析策略名称。</span><span class="sxs-lookup"><span data-stu-id="b83d8-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="b83d8-156">如果策略名称不包含 age，则调用到不同的策略提供程序（如 `DefaultAuthorizationPolicyProvider` ）。</span><span class="sxs-lookup"><span data-stu-id="b83d8-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="b83d8-157">`IAuthorizationPolicyProvider`上面所示的示例实现可以 `DefaultAuthorizationPolicyProvider` 通过在其构造函数中创建备份策略提供程序来进行更新，以便在策略名称与 "MinimumAge" + age 的预期模式不匹配时使用。</span><span class="sxs-lookup"><span data-stu-id="b83d8-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="b83d8-158">然后， `GetPolicyAsync` 可以将方法更新为使用 `BackupPolicyProvider` 而不是返回 null：</span><span class="sxs-lookup"><span data-stu-id="b83d8-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="b83d8-159">默认策略</span><span class="sxs-lookup"><span data-stu-id="b83d8-159">Default policy</span></span>

<span data-ttu-id="b83d8-160">除了提供命名的授权策略外，还需要实现自定义， `IAuthorizationPolicyProvider` `GetDefaultPolicyAsync` 以提供 `[Authorize]` 未指定策略名称的属性的授权策略。</span><span class="sxs-lookup"><span data-stu-id="b83d8-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="b83d8-161">在许多情况下，此授权特性只需要经过身份验证的用户，因此你可以通过调用来进行所需的策略 `RequireAuthenticatedUser` ：</span><span class="sxs-lookup"><span data-stu-id="b83d8-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="b83d8-162">与自定义的所有方面一样 `IAuthorizationPolicyProvider` ，你可以根据需要对其进行自定义。</span><span class="sxs-lookup"><span data-stu-id="b83d8-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="b83d8-163">在某些情况下，可能需要检索回退的默认策略 `IAuthorizationPolicyProvider` 。</span><span class="sxs-lookup"><span data-stu-id="b83d8-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="b83d8-164">回退策略</span><span class="sxs-lookup"><span data-stu-id="b83d8-164">Fallback policy</span></span>

<span data-ttu-id="b83d8-165">自定义 `IAuthorizationPolicyProvider` 可以选择实现 `GetFallbackPolicyAsync` 来提供在[结合策略](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine)时使用的策略，以及未指定策略时使用的策略。</span><span class="sxs-lookup"><span data-stu-id="b83d8-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="b83d8-166">如果 `GetFallbackPolicyAsync` 返回非 null 策略，则在未为请求指定策略时，授权中间件会使用返回的策略。</span><span class="sxs-lookup"><span data-stu-id="b83d8-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="b83d8-167">如果不需要回退策略，则提供程序可以返回 `null` 或延迟到回退提供程序：</span><span class="sxs-lookup"><span data-stu-id="b83d8-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="b83d8-168">使用自定义 IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="b83d8-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="b83d8-169">若要从使用自定义策略 `IAuthorizationPolicyProvider` ，你***必须***：</span><span class="sxs-lookup"><span data-stu-id="b83d8-169">To use custom policies from an `IAuthorizationPolicyProvider`, you ***must***:</span></span>

* <span data-ttu-id="b83d8-170">将适当 `AuthorizationHandler` 的类型注册到依赖关系注入（如[基于策略的授权](xref:security/authorization/policies#authorization-handlers)中所述），与所有基于策略的授权方案相同。</span><span class="sxs-lookup"><span data-stu-id="b83d8-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="b83d8-171">在 `IAuthorizationPolicyProvider` 应用程序的依赖项注入服务集合中注册自定义类型 `Startup.ConfigureServices` ，以替换默认策略提供程序。</span><span class="sxs-lookup"><span data-stu-id="b83d8-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection in `Startup.ConfigureServices` to replace the default policy provider.</span></span>

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

<span data-ttu-id="b83d8-172">`IAuthorizationPolicyProvider` [Dotnet/aspnetcore GitHub 存储库](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)中提供了完整的自定义示例。</span><span class="sxs-lookup"><span data-stu-id="b83d8-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span></span>
