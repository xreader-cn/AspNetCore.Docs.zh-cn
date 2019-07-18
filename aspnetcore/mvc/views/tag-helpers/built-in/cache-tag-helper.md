---
title: ASP.NET Core MVC 中的缓存标记帮助程序
author: pkellner
description: 了解如何使用缓存标记帮助程序。
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: 0273a9805dd5db5450f57dcf3fd4d952308df074
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856210"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="d0507-103">ASP.NET Core MVC 中的缓存标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="d0507-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="d0507-104">作者：[Peter Kellner](https://peterkellner.net) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d0507-104">By [Peter Kellner](https://peterkellner.net) and [Luke Latham](https://github.com/guardrex)</span></span> 

<span data-ttu-id="d0507-105">缓存标记帮助程序通过将其内容缓存到内部 ASP.NET Core 缓存提供程序中，极大地提高了 ASP.NET Core 应用的性能。</span><span class="sxs-lookup"><span data-stu-id="d0507-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="d0507-106">有关标记帮助程序的概述，请参阅 <xref:mvc/views/tag-helpers/intro>。</span><span class="sxs-lookup"><span data-stu-id="d0507-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="d0507-107">以下 Razor 标记缓存当前日期：</span><span class="sxs-lookup"><span data-stu-id="d0507-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="d0507-108">对包含标记帮助程序的页面的第一个请求显示当前日期。</span><span class="sxs-lookup"><span data-stu-id="d0507-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="d0507-109">其他请求将显示已缓存的值，直到缓存过期（默认 20 分钟）或因内存压力而逐出。</span><span class="sxs-lookup"><span data-stu-id="d0507-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="d0507-110">缓存标记帮助程序属性</span><span class="sxs-lookup"><span data-stu-id="d0507-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="d0507-111">enabled</span><span class="sxs-lookup"><span data-stu-id="d0507-111">enabled</span></span>

| <span data-ttu-id="d0507-112">属性类型</span><span class="sxs-lookup"><span data-stu-id="d0507-112">Attribute Type</span></span>  | <span data-ttu-id="d0507-113">示例</span><span class="sxs-lookup"><span data-stu-id="d0507-113">Examples</span></span>        | <span data-ttu-id="d0507-114">默认</span><span class="sxs-lookup"><span data-stu-id="d0507-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="d0507-115">Boolean</span><span class="sxs-lookup"><span data-stu-id="d0507-115">Boolean</span></span>         | <span data-ttu-id="d0507-116">`true`， `false`</span><span class="sxs-lookup"><span data-stu-id="d0507-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="d0507-117">`enabled` 确定是否缓存了缓存标记帮助程序所包含的内容。</span><span class="sxs-lookup"><span data-stu-id="d0507-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="d0507-118">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="d0507-118">The default is `true`.</span></span> <span data-ttu-id="d0507-119">如果设置为 `false`，则不会缓存呈现的输出  。</span><span class="sxs-lookup"><span data-stu-id="d0507-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="d0507-120">示例:</span><span class="sxs-lookup"><span data-stu-id="d0507-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="d0507-121">expires-on</span><span class="sxs-lookup"><span data-stu-id="d0507-121">expires-on</span></span>

| <span data-ttu-id="d0507-122">属性类型</span><span class="sxs-lookup"><span data-stu-id="d0507-122">Attribute Type</span></span>   | <span data-ttu-id="d0507-123">示例</span><span class="sxs-lookup"><span data-stu-id="d0507-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="d0507-124">`expires-on` 为缓存项设置一个绝对到期日期。</span><span class="sxs-lookup"><span data-stu-id="d0507-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="d0507-125">以下示例将在 2025 年 1 月 29 日下午 5:02 之前缓存缓存标记帮助程序的内容：</span><span class="sxs-lookup"><span data-stu-id="d0507-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="d0507-126">expires-after</span><span class="sxs-lookup"><span data-stu-id="d0507-126">expires-after</span></span>

| <span data-ttu-id="d0507-127">属性类型</span><span class="sxs-lookup"><span data-stu-id="d0507-127">Attribute Type</span></span> | <span data-ttu-id="d0507-128">示例</span><span class="sxs-lookup"><span data-stu-id="d0507-128">Example</span></span>                      | <span data-ttu-id="d0507-129">默认</span><span class="sxs-lookup"><span data-stu-id="d0507-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="d0507-130">20 分钟</span><span class="sxs-lookup"><span data-stu-id="d0507-130">20 minutes</span></span> |

<span data-ttu-id="d0507-131">`expires-after` 设置从第一个请求时间到缓存内容的时间长度。</span><span class="sxs-lookup"><span data-stu-id="d0507-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="d0507-132">示例:</span><span class="sxs-lookup"><span data-stu-id="d0507-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="d0507-133">Razor 视图引擎将默认的 `expires-after` 值设置为 20 分钟。</span><span class="sxs-lookup"><span data-stu-id="d0507-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="d0507-134">expires-sliding</span><span class="sxs-lookup"><span data-stu-id="d0507-134">expires-sliding</span></span>

| <span data-ttu-id="d0507-135">属性类型</span><span class="sxs-lookup"><span data-stu-id="d0507-135">Attribute Type</span></span> | <span data-ttu-id="d0507-136">示例</span><span class="sxs-lookup"><span data-stu-id="d0507-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="d0507-137">设置某个缓存项的值未被访问时，该缓存项应被逐出的时间。</span><span class="sxs-lookup"><span data-stu-id="d0507-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="d0507-138">示例:</span><span class="sxs-lookup"><span data-stu-id="d0507-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="d0507-139">vary-by-header</span><span class="sxs-lookup"><span data-stu-id="d0507-139">vary-by-header</span></span>

| <span data-ttu-id="d0507-140">属性类型</span><span class="sxs-lookup"><span data-stu-id="d0507-140">Attribute Type</span></span> | <span data-ttu-id="d0507-141">示例</span><span class="sxs-lookup"><span data-stu-id="d0507-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="d0507-142">String</span><span class="sxs-lookup"><span data-stu-id="d0507-142">String</span></span>         | <span data-ttu-id="d0507-143">`User-Agent`， `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="d0507-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="d0507-144">`vary-by-header` 接受逗号分隔的标头值列表，在标头值发生更改时触发缓存刷新。</span><span class="sxs-lookup"><span data-stu-id="d0507-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="d0507-145">以下示例监视标头值 `User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="d0507-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="d0507-146">该示例将缓存提供给 Web 服务器的每个不同 `User-Agent` 的内容：</span><span class="sxs-lookup"><span data-stu-id="d0507-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="d0507-147">vary-by-query</span><span class="sxs-lookup"><span data-stu-id="d0507-147">vary-by-query</span></span>

| <span data-ttu-id="d0507-148">属性类型</span><span class="sxs-lookup"><span data-stu-id="d0507-148">Attribute Type</span></span> | <span data-ttu-id="d0507-149">示例</span><span class="sxs-lookup"><span data-stu-id="d0507-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="d0507-150">String</span><span class="sxs-lookup"><span data-stu-id="d0507-150">String</span></span>         | <span data-ttu-id="d0507-151">`Make`， `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="d0507-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="d0507-152">`vary-by-query` 接受查询字符串(<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) 中逗号分隔的 <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> 列表，它们在任何列出的键值发生更改时触发缓存刷新。</span><span class="sxs-lookup"><span data-stu-id="d0507-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="d0507-153">以下示例监视 `Make` 和 `Model` 值。</span><span class="sxs-lookup"><span data-stu-id="d0507-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="d0507-154">该示例将缓存提供给 Web 服务器的每个不同 `Make` 和 `Model` 的内容：</span><span class="sxs-lookup"><span data-stu-id="d0507-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="d0507-155">vary-by-route</span><span class="sxs-lookup"><span data-stu-id="d0507-155">vary-by-route</span></span>

| <span data-ttu-id="d0507-156">属性类型</span><span class="sxs-lookup"><span data-stu-id="d0507-156">Attribute Type</span></span> | <span data-ttu-id="d0507-157">示例</span><span class="sxs-lookup"><span data-stu-id="d0507-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="d0507-158">String</span><span class="sxs-lookup"><span data-stu-id="d0507-158">String</span></span>         | <span data-ttu-id="d0507-159">`Make`， `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="d0507-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="d0507-160">`vary-by-route` 接受路由参数名称的逗号分隔列表，用于在路由数据参数值发生更改时触发缓存刷新。</span><span class="sxs-lookup"><span data-stu-id="d0507-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="d0507-161">示例:</span><span class="sxs-lookup"><span data-stu-id="d0507-161">Example:</span></span>

<span data-ttu-id="d0507-162">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="d0507-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="d0507-163">Index.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="d0507-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a><span data-ttu-id="d0507-164">vary-by-cookie</span><span class="sxs-lookup"><span data-stu-id="d0507-164">vary-by-cookie</span></span>

| <span data-ttu-id="d0507-165">属性类型</span><span class="sxs-lookup"><span data-stu-id="d0507-165">Attribute Type</span></span> | <span data-ttu-id="d0507-166">示例</span><span class="sxs-lookup"><span data-stu-id="d0507-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="d0507-167">String</span><span class="sxs-lookup"><span data-stu-id="d0507-167">String</span></span>         | <span data-ttu-id="d0507-168">`.AspNetCore.Identity.Application`， `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="d0507-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="d0507-169">`vary-by-cookie` 接受 Cookie 名称的逗号分隔列表，用于在 Cookie 值发生更改时触发缓存刷新。</span><span class="sxs-lookup"><span data-stu-id="d0507-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="d0507-170">下例监视与 ASP.NET Core 标识相关联的 cookie。</span><span class="sxs-lookup"><span data-stu-id="d0507-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="d0507-171">当用户进行身份验证时，标识 cookie 中的更改就会触发缓存刷新：</span><span class="sxs-lookup"><span data-stu-id="d0507-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="d0507-172">vary-by-user</span><span class="sxs-lookup"><span data-stu-id="d0507-172">vary-by-user</span></span>

| <span data-ttu-id="d0507-173">属性类型</span><span class="sxs-lookup"><span data-stu-id="d0507-173">Attribute Type</span></span>  | <span data-ttu-id="d0507-174">示例</span><span class="sxs-lookup"><span data-stu-id="d0507-174">Examples</span></span>        | <span data-ttu-id="d0507-175">默认</span><span class="sxs-lookup"><span data-stu-id="d0507-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="d0507-176">Boolean</span><span class="sxs-lookup"><span data-stu-id="d0507-176">Boolean</span></span>         | <span data-ttu-id="d0507-177">`true`， `false`</span><span class="sxs-lookup"><span data-stu-id="d0507-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="d0507-178">`vary-by-user` 指定当已登录用户（或上下文主体）发生更改时是否应重置缓存。</span><span class="sxs-lookup"><span data-stu-id="d0507-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="d0507-179">当前用户也称为请求上下文主体，可通过引用 `@User.Identity.Name` 在 Razor 视图中查看。</span><span class="sxs-lookup"><span data-stu-id="d0507-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="d0507-180">下面的示例监视当前登录的用户触发缓存刷新：</span><span class="sxs-lookup"><span data-stu-id="d0507-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="d0507-181">通过登录和注销周期，使用此属性将内容维护在缓存中。</span><span class="sxs-lookup"><span data-stu-id="d0507-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="d0507-182">当值设置为 `true` 时，身份验证周期会使已经过身份验证的用户的缓存失效。</span><span class="sxs-lookup"><span data-stu-id="d0507-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="d0507-183">缓存无效是因为用户进行身份验证时生成了一个新的唯一 cookie 值。</span><span class="sxs-lookup"><span data-stu-id="d0507-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="d0507-184">如果 cookie 不存在或已过期，则会维持缓存以呈现匿名状态。</span><span class="sxs-lookup"><span data-stu-id="d0507-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="d0507-185">如果用户未经过  身份验证，则会维持缓存。</span><span class="sxs-lookup"><span data-stu-id="d0507-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="d0507-186">vary-by</span><span class="sxs-lookup"><span data-stu-id="d0507-186">vary-by</span></span>

| <span data-ttu-id="d0507-187">属性类型</span><span class="sxs-lookup"><span data-stu-id="d0507-187">Attribute Type</span></span> | <span data-ttu-id="d0507-188">示例</span><span class="sxs-lookup"><span data-stu-id="d0507-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="d0507-189">String</span><span class="sxs-lookup"><span data-stu-id="d0507-189">String</span></span>         | `@Model` |

<span data-ttu-id="d0507-190">`vary-by` 允许自定义缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="d0507-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="d0507-191">当属性的字符串值引用的对象发生更改时，会更新缓存标记帮助程序的内容。</span><span class="sxs-lookup"><span data-stu-id="d0507-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="d0507-192">通常将模型值的字符串串联分配给此属性。</span><span class="sxs-lookup"><span data-stu-id="d0507-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="d0507-193">从效果上看，这导致了更新任何已连接的值都会使缓存无效。</span><span class="sxs-lookup"><span data-stu-id="d0507-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="d0507-194">以下示例假定视图的控制器方法将两个路由参数 `myParam1` 和 `myParam2` 的整数值相加，并将其作为单个模型属性返回。</span><span class="sxs-lookup"><span data-stu-id="d0507-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="d0507-195">当此总和更改时，会再次呈现并缓存缓存标记帮助程序的内容。</span><span class="sxs-lookup"><span data-stu-id="d0507-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="d0507-196">操作：</span><span class="sxs-lookup"><span data-stu-id="d0507-196">Action:</span></span>

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

<span data-ttu-id="d0507-197">Index.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="d0507-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="d0507-198">priority</span><span class="sxs-lookup"><span data-stu-id="d0507-198">priority</span></span>

| <span data-ttu-id="d0507-199">属性类型</span><span class="sxs-lookup"><span data-stu-id="d0507-199">Attribute Type</span></span>      | <span data-ttu-id="d0507-200">示例</span><span class="sxs-lookup"><span data-stu-id="d0507-200">Examples</span></span>                               | <span data-ttu-id="d0507-201">默认</span><span class="sxs-lookup"><span data-stu-id="d0507-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="d0507-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="d0507-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="d0507-203">`priority` 为内置缓存提供程序提供缓存逐出指导。</span><span class="sxs-lookup"><span data-stu-id="d0507-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="d0507-204">在内存压力下，Web 服务器将首先逐出 `Low` 缓存项。</span><span class="sxs-lookup"><span data-stu-id="d0507-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="d0507-205">示例:</span><span class="sxs-lookup"><span data-stu-id="d0507-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="d0507-206">`priority` 属性并不能保证特定级别的缓存保留。</span><span class="sxs-lookup"><span data-stu-id="d0507-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="d0507-207">`CacheItemPriority` 仅供参考。</span><span class="sxs-lookup"><span data-stu-id="d0507-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="d0507-208">将此属性设置为 `NeverRemove` 并不能保证缓存项将始终保留。</span><span class="sxs-lookup"><span data-stu-id="d0507-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="d0507-209">请参阅[其他资源](#additional-resources)部分中的相关主题，以获取详细信息。</span><span class="sxs-lookup"><span data-stu-id="d0507-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="d0507-210">缓存标记帮助程序依赖于[内存缓存服务](xref:performance/caching/memory)。</span><span class="sxs-lookup"><span data-stu-id="d0507-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="d0507-211">如果尚未添加该服务，缓存标记帮助程序将为你添加。</span><span class="sxs-lookup"><span data-stu-id="d0507-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0507-212">其他资源</span><span class="sxs-lookup"><span data-stu-id="d0507-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
