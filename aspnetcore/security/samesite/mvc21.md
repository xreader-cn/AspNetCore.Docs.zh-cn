---
title: ASP.NET Core 2.1 MVC SameSite cookie 示例
author: rick-anderson
description: ASP.NET Core 2.1 MVC SameSite cookie 示例
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: security/samesite/mvc21
ms.openlocfilehash: 14f3d3d27d5740519e1ba57529d5694b4cdeb9ec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654990"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="ef1ad-103">ASP.NET Core 2.1 MVC SameSite cookie 示例</span><span class="sxs-lookup"><span data-stu-id="ef1ad-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="ef1ad-104">ASP.NET Core 2.1 内置了对[SameSite](https://www.owasp.org/index.php/SameSite)属性的支持，但它已写入原始标准。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="ef1ad-105">修补后的[行为](https://github.com/dotnet/aspnetcore/issues/8212)更改了 `SameSite.None` 的含义，以发出值为 `None`的 sameSite 属性，而不是根本不发出值。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="ef1ad-106">如果要不发出该值，可以将 cookie 的 `SameSite` 属性设置为-1。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

## <a name="sampleCode"></a><span data-ttu-id="ef1ad-107">编写 SameSite 属性</span><span class="sxs-lookup"><span data-stu-id="ef1ad-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="ef1ad-108">下面是如何在 cookie 上编写 SameSite 属性的示例：</span><span class="sxs-lookup"><span data-stu-id="ef1ad-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="ef1ad-109">设置 Cookie 身份验证和会话状态 cookie</span><span class="sxs-lookup"><span data-stu-id="ef1ad-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="ef1ad-110">Cookie 身份验证、会话状态和[各种其他组件](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)通过 Cookie 选项设置其 sameSite 选项，例如</span><span class="sxs-lookup"><span data-stu-id="ef1ad-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="ef1ad-111">在前面的代码中，cookie 身份验证和会话状态将其 sameSite 属性设置为 `None`，发出具有 `None` 值的属性，同时将 Secure 特性设置为 true。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="ef1ad-112">运行示例</span><span class="sxs-lookup"><span data-stu-id="ef1ad-112">Run the sample</span></span>

<span data-ttu-id="ef1ad-113">如果运行[示例项目](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)，请在初始页面上加载浏览器调试器，并使用它查看站点的 cookie 集合。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="ef1ad-114">若要在 Edge 和 Chrome 中进行此操作，请按 `F12` 选择 "`Application`" 选项卡，然后单击 "`Storage`" 部分中 "`Cookies`" 选项下的 "站点 URL"。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![浏览器调试器 Cookie 列表](BrowserDebugger.png)

<span data-ttu-id="ef1ad-116">您可以从上图中看到，当您单击 "创建 SameSite Cookie" 按钮的 SameSite 属性值为 `Lax`，与在[示例代码](#sampleCode)中设置的值匹配时，示例创建的 cookie 就会看到该示例创建的 cookie。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="interception"></a><span data-ttu-id="ef1ad-117">截获 cookie</span><span class="sxs-lookup"><span data-stu-id="ef1ad-117">Intercepting cookies</span></span>

<span data-ttu-id="ef1ad-118">为了截获 cookie，若要根据用户的浏览器代理中的支持来调整无值，必须使用 `CookiePolicy` 中间件。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="ef1ad-119">这必须放入 http 请求管道中，然后**才能**在 `ConfigureServices()`中写入 cookie 和配置的任何组件。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="ef1ad-120">若要将其插入管道，请使用[Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs)的 `Configure(IApplicationBuilder, IHostingEnvironment)` 方法中的 `app.UseCookiePolicy()`。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="ef1ad-121">例如：</span><span class="sxs-lookup"><span data-stu-id="ef1ad-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="ef1ad-122">然后，在 `ConfigureServices(IServiceCollection services)` 配置 cookie 策略，以便在添加或删除 cookie 时调用帮助器类。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="ef1ad-123">例如：</span><span class="sxs-lookup"><span data-stu-id="ef1ad-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="ef1ad-124">Helper 函数 `CheckSameSite(HttpContext, CookieOptions)`：</span><span class="sxs-lookup"><span data-stu-id="ef1ad-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="ef1ad-125">当 cookie 追加到请求或从请求中删除时调用。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="ef1ad-126">检查 `SameSite` 属性是否设置为 `None`。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="ef1ad-127">如果 `SameSite` 设置为 `None` 并且已知当前用户代理不支持 none 特性值。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="ef1ad-128">使用[SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs)类完成检查：</span><span class="sxs-lookup"><span data-stu-id="ef1ad-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="ef1ad-129">通过将属性设置为，将 `SameSite` 设置为不发出该值 `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="ef1ad-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="ef1ad-130">目标 .NET Framework</span><span class="sxs-lookup"><span data-stu-id="ef1ad-130">Targeting .NET Framework</span></span>

<span data-ttu-id="ef1ad-131">ASP.NET Core 和 System.web （ASP.NET 经典）具有 SameSite 的独立实现。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="ef1ad-132">如果使用 ASP.NET Core 或 SameSite 最低版本要求（.NET 4.7.2）适用于 ASP.NET Core，则不需要 .NET Framework 的 SameSite KB 修补程序。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="ef1ad-133">.NET 上的 ASP.NET Core 要求更新 nuget 程序包依赖项以获取适当的修补程序。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="ef1ad-134">若要获取的 ASP.NET Core 更改 .NET Framework 确保你有对修补的包和版本（2.1.14 或更高版本2.1）的直接引用。</span><span class="sxs-lookup"><span data-stu-id="ef1ad-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="ef1ad-135">更多信息</span><span class="sxs-lookup"><span data-stu-id="ef1ad-135">More Information</span></span>
 
<span data-ttu-id="ef1ad-136">[Chrome 更新](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite 文档](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite 更改公告](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="ef1ad-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>