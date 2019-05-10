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
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="37b34-103">在 ASP.NET Core 中使用 IAuthorizationPolicyProvider 的自定义授权策略提供程序</span><span class="sxs-lookup"><span data-stu-id="37b34-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="37b34-104">通过[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="37b34-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="37b34-105">通常使用时[基于策略的授权](xref:security/authorization/policies)，通过调用注册策略`AuthorizationOptions.AddPolicy`作为授权服务配置的一部分。</span><span class="sxs-lookup"><span data-stu-id="37b34-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="37b34-106">在某些情况下，它可能不是可能 （或最好） 若要以这种方式注册所有授权策略。</span><span class="sxs-lookup"><span data-stu-id="37b34-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="37b34-107">在这些情况下，你可以使用自定义`IAuthorizationPolicyProvider`来控制如何提供授权策略。</span><span class="sxs-lookup"><span data-stu-id="37b34-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="37b34-108">方案的示例在一个自定义[IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)可能会很有用包括：</span><span class="sxs-lookup"><span data-stu-id="37b34-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="37b34-109">使用外部服务提供的策略评估。</span><span class="sxs-lookup"><span data-stu-id="37b34-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="37b34-110">（适用于不同的房间号或年龄，例如） 使用了大量的策略，因此它不适合将与每个单个授权策略添加`AuthorizationOptions.AddPolicy`调用。</span><span class="sxs-lookup"><span data-stu-id="37b34-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn’t make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="37b34-111">在运行时基于外部数据源 （如数据库） 中的信息创建策略或通过其他机制动态确定授权要求。</span><span class="sxs-lookup"><span data-stu-id="37b34-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="37b34-112">[查看或下载示例代码](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)从[AspNetCore GitHub 存储库](https://github.com/aspnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="37b34-112">[View or download sample code](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="37b34-113">Aspnet/AspNetCore 存储库 ZIP 文件下载。</span><span class="sxs-lookup"><span data-stu-id="37b34-113">Download the aspnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="37b34-114">将文件解压缩。</span><span class="sxs-lookup"><span data-stu-id="37b34-114">Unzip the file.</span></span> <span data-ttu-id="37b34-115">导航到*src/Security/示例/CustomPolicyProvider*项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="37b34-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="37b34-116">自定义策略检索</span><span class="sxs-lookup"><span data-stu-id="37b34-116">Customize policy retrieval</span></span>

<span data-ttu-id="37b34-117">ASP.NET Core 应用使用的实现`IAuthorizationPolicyProvider`接口以检索授权策略。</span><span class="sxs-lookup"><span data-stu-id="37b34-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="37b34-118">默认情况下[DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)注册和使用。</span><span class="sxs-lookup"><span data-stu-id="37b34-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="37b34-119">`DefaultAuthorizationPolicyProvider` 返回从策略`AuthorizationOptions`中提供`IServiceCollection.AddAuthorization`调用。</span><span class="sxs-lookup"><span data-stu-id="37b34-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="37b34-120">可以通过注册其他自定义此行为`IAuthorizationPolicyProvider`在应用中的实现[依赖关系注入](xref:fundamentals/dependency-injection)容器。</span><span class="sxs-lookup"><span data-stu-id="37b34-120">You can customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app’s [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="37b34-121">`IAuthorizationPolicyProvider`接口包含两个 Api:</span><span class="sxs-lookup"><span data-stu-id="37b34-121">The `IAuthorizationPolicyProvider` interface contains two APIs:</span></span>

* <span data-ttu-id="37b34-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)返回具有给定名称的授权策略。</span><span class="sxs-lookup"><span data-stu-id="37b34-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="37b34-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)返回的默认授权策略 (用于策略`[Authorize]`而无需指定的策略的属性)。</span><span class="sxs-lookup"><span data-stu-id="37b34-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 

<span data-ttu-id="37b34-124">通过实现这两个 Api，可以自定义提供的授权策略的方式。</span><span class="sxs-lookup"><span data-stu-id="37b34-124">By implementing these two APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="37b34-125">参数化授权特性的示例</span><span class="sxs-lookup"><span data-stu-id="37b34-125">Parameterized authorize attribute example</span></span>

<span data-ttu-id="37b34-126">一种情况其中`IAuthorizationPolicyProvider`可启用自定义`[Authorize]`其要求取决于参数的属性。</span><span class="sxs-lookup"><span data-stu-id="37b34-126">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="37b34-127">例如，在[基于策略的授权](xref:security/authorization/policies)文档中，基于年龄的 ("AtLeast21") 作为一个示例所使用的策略。</span><span class="sxs-lookup"><span data-stu-id="37b34-127">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="37b34-128">如果在应用中的不同控制器操作应该对可用的用户*不同*年龄，可能会很有用具有许多不同的基于年龄的策略。</span><span class="sxs-lookup"><span data-stu-id="37b34-128">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="37b34-129">而不是注册所有不同年龄基于策略的应用程序中会用`AuthorizationOptions`，可以生成动态使用自定义策略`IAuthorizationPolicyProvider`。</span><span class="sxs-lookup"><span data-stu-id="37b34-129">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="37b34-130">要使用的策略更轻松，你可以批注具有类似的自定义授权属性操作`[MinimumAgeAuthorize(20)]`。</span><span class="sxs-lookup"><span data-stu-id="37b34-130">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="37b34-131">自定义授权属性</span><span class="sxs-lookup"><span data-stu-id="37b34-131">Custom Authorization attributes</span></span>

<span data-ttu-id="37b34-132">通过名称标识的授权策略。</span><span class="sxs-lookup"><span data-stu-id="37b34-132">Authorization policies are identified by their names.</span></span> <span data-ttu-id="37b34-133">自定义`MinimumAgeAuthorizeAttribute`所述之前需要将参数映射到一个字符串，可用于检索相应的授权策略。</span><span class="sxs-lookup"><span data-stu-id="37b34-133">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="37b34-134">您可以执行此操作通过派生自`AuthorizeAttribute`并使`Age`属性包装`AuthorizeAttribute.Policy`属性。</span><span class="sxs-lookup"><span data-stu-id="37b34-134">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="37b34-135">此属性类型具有`Policy`字符串基于的硬编码的前缀 (`"MinimumAge"`) 和一个整数，通过构造函数中传递。</span><span class="sxs-lookup"><span data-stu-id="37b34-135">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="37b34-136">可以将其应用于操作中方式不同于其他`Authorize`属性，但前者作为参数的整数。</span><span class="sxs-lookup"><span data-stu-id="37b34-136">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="37b34-137">Custom IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="37b34-137">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="37b34-138">自定义`MinimumAgeAuthorizeAttribute`可以方便地对请求授权策略的任何所需的最短期限。</span><span class="sxs-lookup"><span data-stu-id="37b34-138">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="37b34-139">要解决的下一个问题确保为所有这些不同年龄段的授权策略可用。</span><span class="sxs-lookup"><span data-stu-id="37b34-139">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="37b34-140">这就是`IAuthorizationPolicyProvider`非常有用。</span><span class="sxs-lookup"><span data-stu-id="37b34-140">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="37b34-141">使用时`MinimumAgeAuthorizationAttribute`，授权策略名称将遵循的模式`"MinimumAge" + Age`，因此自定义`IAuthorizationPolicyProvider`应生成的授权策略：</span><span class="sxs-lookup"><span data-stu-id="37b34-141">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="37b34-142">分析将年龄字段从策略名称。</span><span class="sxs-lookup"><span data-stu-id="37b34-142">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="37b34-143">使用`AuthorizationPolicyBuilder`若要创建一个新 `AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="37b34-143">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="37b34-144">向策略添加要求根据与保留`AuthorizationPolicyBuilder.AddRequirements`。</span><span class="sxs-lookup"><span data-stu-id="37b34-144">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="37b34-145">在其他情况下，可能会使用`RequireClaim`， `RequireRole`，或`RequireUserName`相反。</span><span class="sxs-lookup"><span data-stu-id="37b34-145">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="37b34-146">多个授权策略提供程序</span><span class="sxs-lookup"><span data-stu-id="37b34-146">Multiple authorization policy providers</span></span>

<span data-ttu-id="37b34-147">使用自定义时`IAuthorizationPolicyProvider`实现中，请记住，ASP.NET Core 仅使用的一个实例`IAuthorizationPolicyProvider`。</span><span class="sxs-lookup"><span data-stu-id="37b34-147">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="37b34-148">如果自定义提供程序不能提供的所有策略名称，将使用授权策略，它应回退到备份的提供程序。</span><span class="sxs-lookup"><span data-stu-id="37b34-148">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should fall back to a backup provider.</span></span> 

<span data-ttu-id="37b34-149">例如，请考虑需要自定义保留时间策略和更传统的基于角色的策略检索的应用程序。</span><span class="sxs-lookup"><span data-stu-id="37b34-149">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="37b34-150">此类应用可以使用自定义授权策略提供程序的：</span><span class="sxs-lookup"><span data-stu-id="37b34-150">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="37b34-151">尝试分析策略名称。</span><span class="sxs-lookup"><span data-stu-id="37b34-151">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="37b34-152">调入不同的策略提供程序 (如`DefaultAuthorizationPolicyProvider`) 如果策略名称不包含年龄。</span><span class="sxs-lookup"><span data-stu-id="37b34-152">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="37b34-153">该示例`IAuthorizationPolicyProvider`如上所示的实现可以更新为使用`DefaultAuthorizationPolicyProvider`通过在其构造函数 （若要在策略名称与 MinimumAge + 年龄的其预期的模式不匹配的情况下使用） 中创建一个回退策略提供程序。</span><span class="sxs-lookup"><span data-stu-id="37b34-153">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a fallback policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="37b34-154">然后，将`GetPolicyAsync`方法可以更新为使用`FallbackPolicyProvider`而不是返回 null:</span><span class="sxs-lookup"><span data-stu-id="37b34-154">Then, the `GetPolicyAsync` method can be updated to use the `FallbackPolicyProvider` instead of returning null:</span></span>

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="37b34-155">默认策略</span><span class="sxs-lookup"><span data-stu-id="37b34-155">Default policy</span></span>

<span data-ttu-id="37b34-156">除了提供命名的授权策略，自定义`IAuthorizationPolicyProvider`需要实现`GetDefaultPolicyAsync`提供的授权策略`[Authorize]`属性没有指定策略名称。</span><span class="sxs-lookup"><span data-stu-id="37b34-156">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="37b34-157">在许多情况下，此授权属性仅要求身份验证的用户，这样你就可以通过调用必要的策略`RequireAuthenticatedUser`:</span><span class="sxs-lookup"><span data-stu-id="37b34-157">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build());
```

<span data-ttu-id="37b34-158">自定义的所有方面与`IAuthorizationPolicyProvider`，你可以自定义此操作，请根据需要。</span><span class="sxs-lookup"><span data-stu-id="37b34-158">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="37b34-159">在某些情况下，它可能需要从回退检索默认策略`IAuthorizationPolicyProvider`。</span><span class="sxs-lookup"><span data-stu-id="37b34-159">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="required-policy"></a><span data-ttu-id="37b34-160">所需的策略</span><span class="sxs-lookup"><span data-stu-id="37b34-160">Required policy</span></span>

<span data-ttu-id="37b34-161">自定义`IAuthorizationPolicyProvider`需要实现`GetRequiredPolicyAsync`以，（可选） 提供始终是必需的策略。</span><span class="sxs-lookup"><span data-stu-id="37b34-161">A custom `IAuthorizationPolicyProvider` needs to implement `GetRequiredPolicyAsync` to, optionally, provide a policy that is always required.</span></span> <span data-ttu-id="37b34-162">如果`GetRequiredPolicyAsync`返回非 null 策略，该策略将结合任何其他 （名为或默认值） 请求的策略。</span><span class="sxs-lookup"><span data-stu-id="37b34-162">If `GetRequiredPolicyAsync` returns a non-null policy, that policy will be combined with any other (named or default) policy that is requested.</span></span>

<span data-ttu-id="37b34-163">如果需要不必要的策略，则提供程序可以只是返回 null 或推迟到回退提供程序：</span><span class="sxs-lookup"><span data-stu-id="37b34-163">If no required policy is needed, the provider can just return null or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="37b34-164">使用自定义 IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="37b34-164">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="37b34-165">若要使用自定义策略从`IAuthorizationPolicyProvider`，您必须：</span><span class="sxs-lookup"><span data-stu-id="37b34-165">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="37b34-166">注册的相应`AuthorizationHandler`类型使用依赖关系注入 (中所述[基于策略的授权](xref:security/authorization/policies#authorization-handlers))，与所有基于策略的授权方案。</span><span class="sxs-lookup"><span data-stu-id="37b34-166">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="37b34-167">注册自定义`IAuthorizationPolicyProvider`应用程序的依赖关系注入服务集合中的类型 (在`Startup.ConfigureServices`) 来替换默认策略提供程序。</span><span class="sxs-lookup"><span data-stu-id="37b34-167">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="37b34-168">完成自定义`IAuthorizationPolicyProvider`示例现已推出[aspnet/AuthSamples GitHub 存储库](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)。</span><span class="sxs-lookup"><span data-stu-id="37b34-168">A complete custom `IAuthorizationPolicyProvider` sample is available in the [aspnet/AuthSamples GitHub repository](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span></span>
