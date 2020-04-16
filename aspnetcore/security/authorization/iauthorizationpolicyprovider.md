---
title: ASP.NET核心中的自定义授权策略提供程序
author: mjrousos
description: 了解如何在ASP.NET核心应用中使用自定义 I授权策略提供程序动态生成授权策略。
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2a8b189cc9f17529a962a1f9642c7bb199d5781b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440917"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="7bfca-103">自定义授权策略提供程序使用 i 授权策略提供程序ASP.NET核心</span><span class="sxs-lookup"><span data-stu-id="7bfca-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="7bfca-104">作者：[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="7bfca-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="7bfca-105">通常，在使用[基于策略的授权](xref:security/authorization/policies)时，策略是通过调用`AuthorizationOptions.AddPolicy`作为授权服务配置的一部分进行注册的。</span><span class="sxs-lookup"><span data-stu-id="7bfca-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="7bfca-106">在某些情况下，不可能（或不需要）以这种方式注册所有授权策略。</span><span class="sxs-lookup"><span data-stu-id="7bfca-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="7bfca-107">在这些情况下，可以使用自定义来控制`IAuthorizationPolicyProvider`如何提供授权策略。</span><span class="sxs-lookup"><span data-stu-id="7bfca-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="7bfca-108">自定义[I授权策略提供程序](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)可能有用的方案示例包括：</span><span class="sxs-lookup"><span data-stu-id="7bfca-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="7bfca-109">使用外部服务提供策略评估。</span><span class="sxs-lookup"><span data-stu-id="7bfca-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="7bfca-110">使用大量策略（例如，针对不同的房间号码或年龄），因此使用`AuthorizationOptions.AddPolicy`调用添加每个单独的授权策略没有意义。</span><span class="sxs-lookup"><span data-stu-id="7bfca-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="7bfca-111">根据外部数据源（如数据库）中的信息在运行时创建策略，或通过另一种机制动态确定授权要求。</span><span class="sxs-lookup"><span data-stu-id="7bfca-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="7bfca-112">从[AspNetCore GitHub 存储库](https://github.com/dotnet/AspNetCore)[查看或下载示例代码](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)。</span><span class="sxs-lookup"><span data-stu-id="7bfca-112">[View or download sample code](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="7bfca-113">下载点网/阿斯普内科存储库 ZIP 文件。</span><span class="sxs-lookup"><span data-stu-id="7bfca-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="7bfca-114">解压缩文件。</span><span class="sxs-lookup"><span data-stu-id="7bfca-114">Unzip the file.</span></span> <span data-ttu-id="7bfca-115">导航到*src/安全/示例/自定义策略提供程序*项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="7bfca-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="7bfca-116">自定义策略检索</span><span class="sxs-lookup"><span data-stu-id="7bfca-116">Customize policy retrieval</span></span>

<span data-ttu-id="7bfca-117">ASP.NET核心应用使用接口的`IAuthorizationPolicyProvider`实现来检索授权策略。</span><span class="sxs-lookup"><span data-stu-id="7bfca-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="7bfca-118">默认情况下，[默认授权策略提供程序](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)已注册和使用。</span><span class="sxs-lookup"><span data-stu-id="7bfca-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="7bfca-119">`DefaultAuthorizationPolicyProvider`从`IServiceCollection.AddAuthorization`呼叫中提供`AuthorizationOptions`的策略返回策略。</span><span class="sxs-lookup"><span data-stu-id="7bfca-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="7bfca-120">通过在应用[的依赖项注入](xref:fundamentals/dependency-injection)容器中注册`IAuthorizationPolicyProvider`不同的实现来自定义此行为。</span><span class="sxs-lookup"><span data-stu-id="7bfca-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="7bfca-121">该`IAuthorizationPolicyProvider`接口包含三个 API：</span><span class="sxs-lookup"><span data-stu-id="7bfca-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="7bfca-122">[获取策略同步](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)返回给定名称的授权策略。</span><span class="sxs-lookup"><span data-stu-id="7bfca-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="7bfca-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)返回默认授权策略（用于`[Authorize]`未指定策略的属性的策略）。</span><span class="sxs-lookup"><span data-stu-id="7bfca-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="7bfca-124">[GetBackbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync)返回回退授权策略（未指定策略时授权中间件使用的策略）。</span><span class="sxs-lookup"><span data-stu-id="7bfca-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="7bfca-125">通过实现这些 API，您可以自定义如何提供授权策略。</span><span class="sxs-lookup"><span data-stu-id="7bfca-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="7bfca-126">参数化授权属性示例</span><span class="sxs-lookup"><span data-stu-id="7bfca-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="7bfca-127">一`IAuthorizationPolicyProvider`个有用的方案是启用其要求`[Authorize]`依赖于参数的自定义属性。</span><span class="sxs-lookup"><span data-stu-id="7bfca-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="7bfca-128">例如，[在基于策略的授权](xref:security/authorization/policies)文档中，使用基于年龄（"AtLeast21"）的策略作为示例。</span><span class="sxs-lookup"><span data-stu-id="7bfca-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="7bfca-129">如果应用中的不同控制器操作应提供给*不同*年龄的用户，则使用许多不同的基于年龄的策略可能很有用。</span><span class="sxs-lookup"><span data-stu-id="7bfca-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="7bfca-130">无需注册应用程序将需要的所有`AuthorizationOptions`不同基于年龄的策略，而是可以使用自定义`IAuthorizationPolicyProvider`动态生成策略。</span><span class="sxs-lookup"><span data-stu-id="7bfca-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="7bfca-131">为了简化使用策略，可以使用自定义授权属性（如`[MinimumAgeAuthorize(20)]`）对操作进行分给。</span><span class="sxs-lookup"><span data-stu-id="7bfca-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="7bfca-132">自定义授权属性</span><span class="sxs-lookup"><span data-stu-id="7bfca-132">Custom Authorization attributes</span></span>

<span data-ttu-id="7bfca-133">授权策略由其名称标识。</span><span class="sxs-lookup"><span data-stu-id="7bfca-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="7bfca-134">前面描述的`MinimumAgeAuthorizeAttribute`自定义需要将参数映射到可用于检索相应授权策略的字符串中。</span><span class="sxs-lookup"><span data-stu-id="7bfca-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="7bfca-135">可以通过派生和`AuthorizeAttribute`使`Age`属性换行属性`AuthorizeAttribute.Policy`来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="7bfca-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="7bfca-136">此属性类型具有一个`Policy`基于硬编码前缀 （`"MinimumAge"`） 的字符串，以及通过构造函数传入的整数。</span><span class="sxs-lookup"><span data-stu-id="7bfca-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="7bfca-137">可以将其应用于操作的方式与其他`Authorize`属性相同，只不过它采用整数作为参数。</span><span class="sxs-lookup"><span data-stu-id="7bfca-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="7bfca-138">自定义授权策略提供程序</span><span class="sxs-lookup"><span data-stu-id="7bfca-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="7bfca-139">该自定义`MinimumAgeAuthorizeAttribute`使请求任何所需的最低年龄的授权策略变得容易。</span><span class="sxs-lookup"><span data-stu-id="7bfca-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="7bfca-140">要解决的下一个问题是确保授权策略可用于所有这些不同年龄。</span><span class="sxs-lookup"><span data-stu-id="7bfca-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="7bfca-141">这是有用的`IAuthorizationPolicyProvider`。</span><span class="sxs-lookup"><span data-stu-id="7bfca-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="7bfca-142">使用`MinimumAgeAuthorizationAttribute`时，授权策略名称将遵循模式`"MinimumAge" + Age`，因此自定义`IAuthorizationPolicyProvider`应通过以下方式生成授权策略：</span><span class="sxs-lookup"><span data-stu-id="7bfca-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="7bfca-143">从策略名称分析年龄。</span><span class="sxs-lookup"><span data-stu-id="7bfca-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="7bfca-144">用于`AuthorizationPolicyBuilder`创建新`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="7bfca-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="7bfca-145">在此和下面的示例中，假定用户通过 Cookie 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="7bfca-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="7bfca-146">`AuthorizationPolicyBuilder`应至少使用一个授权方案名称构造 ，或者始终成功。</span><span class="sxs-lookup"><span data-stu-id="7bfca-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="7bfca-147">否则，没有关于如何向用户提供质询的信息，并且将引发异常。</span><span class="sxs-lookup"><span data-stu-id="7bfca-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="7bfca-148">根据 具有`AuthorizationPolicyBuilder.AddRequirements`的年龄向策略添加要求。</span><span class="sxs-lookup"><span data-stu-id="7bfca-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="7bfca-149">在其他方案中，您可以使用 ， 或`RequireClaim``RequireUserName`改为`RequireRole`使用 。</span><span class="sxs-lookup"><span data-stu-id="7bfca-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="7bfca-150">多个授权策略提供程序</span><span class="sxs-lookup"><span data-stu-id="7bfca-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="7bfca-151">使用自定义`IAuthorizationPolicyProvider`实现时，请记住，ASP.NET Core 仅使用 的`IAuthorizationPolicyProvider`一个实例。</span><span class="sxs-lookup"><span data-stu-id="7bfca-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="7bfca-152">如果自定义提供程序无法为将使用的所有策略名称提供授权策略，则应将其服从备份提供程序。</span><span class="sxs-lookup"><span data-stu-id="7bfca-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="7bfca-153">例如，考虑一个既需要自定义年龄策略又需要更传统的基于角色的策略检索的应用程序。</span><span class="sxs-lookup"><span data-stu-id="7bfca-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="7bfca-154">此类应用可以使用自定义授权策略提供程序：</span><span class="sxs-lookup"><span data-stu-id="7bfca-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="7bfca-155">尝试分析策略名称。</span><span class="sxs-lookup"><span data-stu-id="7bfca-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="7bfca-156">如果策略名称不包含年龄，则调用`DefaultAuthorizationPolicyProvider`其他策略提供程序（如 ）。</span><span class="sxs-lookup"><span data-stu-id="7bfca-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="7bfca-157">可以通过在其`IAuthorizationPolicyProvider`构造函数中创建备份策略提供程序来更新`DefaultAuthorizationPolicyProvider`上述示例实现以使用 （在策略名称与其预期模式"最小年龄"+ 年龄不匹配时使用）。</span><span class="sxs-lookup"><span data-stu-id="7bfca-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="7bfca-158">然后，`GetPolicyAsync`可以更新该方法以使用`BackupPolicyProvider`而不是返回 null：</span><span class="sxs-lookup"><span data-stu-id="7bfca-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="7bfca-159">默认策略</span><span class="sxs-lookup"><span data-stu-id="7bfca-159">Default policy</span></span>

<span data-ttu-id="7bfca-160">除了提供命名授权策略外，还需要`IAuthorizationPolicyProvider`实现`GetDefaultPolicyAsync`为未指定策略名称`[Authorize]`的属性提供授权策略。</span><span class="sxs-lookup"><span data-stu-id="7bfca-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="7bfca-161">在许多情况下，此授权属性只需要经过身份验证的用户，因此可以使用 调用`RequireAuthenticatedUser`：</span><span class="sxs-lookup"><span data-stu-id="7bfca-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="7bfca-162">与自定义`IAuthorizationPolicyProvider`的所有方面一样，您可以根据需要自定义此内容。</span><span class="sxs-lookup"><span data-stu-id="7bfca-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="7bfca-163">在某些情况下，可能需要从回退`IAuthorizationPolicyProvider`检索默认策略。</span><span class="sxs-lookup"><span data-stu-id="7bfca-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="7bfca-164">回退策略</span><span class="sxs-lookup"><span data-stu-id="7bfca-164">Fallback policy</span></span>

<span data-ttu-id="7bfca-165">自定义`IAuthorizationPolicyProvider`可以选择实现`GetFallbackPolicyAsync`以提供在[组合策略](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine)时和未指定策略时使用的策略。</span><span class="sxs-lookup"><span data-stu-id="7bfca-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="7bfca-166">如果`GetFallbackPolicyAsync`返回非空策略，则当未为请求指定策略时，授权中间件将使用返回的策略。</span><span class="sxs-lookup"><span data-stu-id="7bfca-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="7bfca-167">如果不需要回退策略，提供程序可以返回`null`或延迟回退提供程序：</span><span class="sxs-lookup"><span data-stu-id="7bfca-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="7bfca-168">使用自定义 I 授权策略提供程序</span><span class="sxs-lookup"><span data-stu-id="7bfca-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="7bfca-169">要使用 的自定义策略`IAuthorizationPolicyProvider`，必须：</span><span class="sxs-lookup"><span data-stu-id="7bfca-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="7bfca-170">使用依赖项`AuthorizationHandler`注入注册适当的类型（在[基于策略的授权](xref:security/authorization/policies#authorization-handlers)中描述），与所有基于策略的授权方案一样。</span><span class="sxs-lookup"><span data-stu-id="7bfca-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="7bfca-171">在应用的依赖`IAuthorizationPolicyProvider`项注入服务集合（在 中）`Startup.ConfigureServices`中注册自定义类型以替换默认策略提供程序。</span><span class="sxs-lookup"><span data-stu-id="7bfca-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="7bfca-172">完整的自定义`IAuthorizationPolicyProvider`示例在[dotnet/aspnetcore GitHub 存储库](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider)中可用。</span><span class="sxs-lookup"><span data-stu-id="7bfca-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).</span></span>
