---
title: 'ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: 示例'
author: rick-anderson
description: 'ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: 示例'
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/samesite/mvc21
ms.openlocfilehash: 61878af0f9af72284b43ffd46cca42b0cf043326
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051546"
---
# <a name="aspnet-core-21-mvc-samesite-no-loccookie-sample"></a><span data-ttu-id="6aa07-103">ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: 示例</span><span class="sxs-lookup"><span data-stu-id="6aa07-103">ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: sample</span></span>

<span data-ttu-id="6aa07-104">ASP.NET Core 2.1 内置了对 [SameSite](https://www.owasp.org/index.php/SameSite) 属性的支持，但它已写入原始标准。</span><span class="sxs-lookup"><span data-stu-id="6aa07-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="6aa07-105">修补后的 [行为](https://github.com/dotnet/aspnetcore/issues/8212) 更改了的含义 `SameSite.None` ，以发出值为的 sameSite 特性 `None` ，而不是根本不发出值。</span><span class="sxs-lookup"><span data-stu-id="6aa07-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="6aa07-106">如果您不希望发出该值，则可以将属性设置 `SameSite` :::no-loc(cookie)::: 为-1。</span><span class="sxs-lookup"><span data-stu-id="6aa07-106">If you want to not emit the value you can set the `SameSite` property on a :::no-loc(cookie)::: to -1.</span></span>

[!INCLUDE[](~/includes/SameSite:::no-loc(Identity):::.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="6aa07-107">编写 SameSite 属性</span><span class="sxs-lookup"><span data-stu-id="6aa07-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="6aa07-108">下面是如何在上编写 SameSite 属性的示例 :::no-loc(cookie)::: ：</span><span class="sxs-lookup"><span data-stu-id="6aa07-108">Following is an example of how to write a SameSite attribute on a :::no-loc(cookie)::::</span></span>

```c#
var :::no-loc(cookie):::Options = new :::no-loc(Cookie):::Options
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the :::no-loc(cookie)::: to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the :::no-loc(cookie)::: to the response :::no-loc(cookie)::: collection
Response.:::no-loc(Cookie):::s.Append(:::no-loc(Cookie):::Name, ":::no-loc(cookie):::Value", :::no-loc(cookie):::Options);
```

## <a name="setting-no-loccookie-authentication-and-session-state-no-loccookies"></a><span data-ttu-id="6aa07-109">设置 :::no-loc(Cookie)::: 身份验证和会话状态 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="6aa07-109">Setting :::no-loc(Cookie)::: Authentication and Session State :::no-loc(cookie):::s</span></span>

<span data-ttu-id="6aa07-110">:::no-loc(Cookie)::: 身份验证、会话状态和 [各种其他组件](../samesite.md?view=aspnetcore-2.1) 通过选项设置其 sameSite 选项 :::no-loc(Cookie)::: ，例如</span><span class="sxs-lookup"><span data-stu-id="6aa07-110">:::no-loc(Cookie)::: authentication, session state and [various other components](../samesite.md?view=aspnetcore-2.1) set their sameSite options via :::no-loc(Cookie)::: options, for example</span></span>

```c#
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
        options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
        options.:::no-loc(Cookie):::.IsEssential = true;
    });

services.AddSession(options =>
{
    options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
    options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
    options.:::no-loc(Cookie):::.IsEssential = true;
});
```

<span data-ttu-id="6aa07-111">在前面的代码中， :::no-loc(cookie)::: 身份验证和会话状态将其 sameSite 属性设置为 `None` ，发出具有值的属性， `None` 同时将 Secure 特性设置为 true。</span><span class="sxs-lookup"><span data-stu-id="6aa07-111">In the preceding code, both :::no-loc(cookie)::: authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="6aa07-112">运行示例</span><span class="sxs-lookup"><span data-stu-id="6aa07-112">Run the sample</span></span>

<span data-ttu-id="6aa07-113">如果运行 [示例项目](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)，请在初始页面上加载浏览器调试器，并使用它查看站点的 :::no-loc(cookie)::: 集合。</span><span class="sxs-lookup"><span data-stu-id="6aa07-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the :::no-loc(cookie)::: collection for the site.</span></span> <span data-ttu-id="6aa07-114">若要在 Edge 和 Chrome 中进行此操作，请按， `F12` 选择 `Application` 选项卡，然后单击节中的选项下的 "站点 URL" `:::no-loc(Cookie):::s` `Storage` 。</span><span class="sxs-lookup"><span data-stu-id="6aa07-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `:::no-loc(Cookie):::s` option in the `Storage` section.</span></span>

![Browser 调试器：：： no (Cookie) ：：： List](BrowserDebugger.png)

<span data-ttu-id="6aa07-116">:::no-loc(cookie):::当你单击 "创建 SameSite" 按钮的 SameSite 属性值为时，你可以从上图中看到该示例所创建的，这与 :::no-loc(Cookie)::: `Lax` [示例代码](#sampleCode)中设置的值匹配。</span><span class="sxs-lookup"><span data-stu-id="6aa07-116">You can see from the image above that the :::no-loc(cookie)::: created by the sample when you click the "Create SameSite :::no-loc(Cookie):::" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-no-loccookies"></a><a name="interception"></a><span data-ttu-id="6aa07-117">截获 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="6aa07-117">Intercepting :::no-loc(cookie):::s</span></span>

<span data-ttu-id="6aa07-118">为了截获 :::no-loc(cookie)::: ，若要根据用户的浏览器代理中的支持来调整无值，必须使用 `:::no-loc(Cookie):::Policy` 中间件。</span><span class="sxs-lookup"><span data-stu-id="6aa07-118">In order to intercept :::no-loc(cookie):::s, to adjust the none value according to its support in the user's browser agent you must use the `:::no-loc(Cookie):::Policy` middleware.</span></span> <span data-ttu-id="6aa07-119">在中写入和配置的任何组件 **之前** ，必须将其放入 http 请求管道 :::no-loc(cookie)::: `ConfigureServices()` 。</span><span class="sxs-lookup"><span data-stu-id="6aa07-119">This must be placed into the http request pipeline **before** any components that write :::no-loc(cookie):::s and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="6aa07-120">若要将其插入管道中，请在 Startup.cs 的方法中使用 `app.Use:::no-loc(Cookie):::Policy()` `Configure(IApplicationBuilder, IHostingEnvironment)` 。 [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs)</span><span class="sxs-lookup"><span data-stu-id="6aa07-120">To insert it into the pipeline use `app.Use:::no-loc(Cookie):::Policy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="6aa07-121">例如： 。</span><span class="sxs-lookup"><span data-stu-id="6aa07-121">For example:</span></span>

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
    app.Use:::no-loc(Cookie):::Policy();
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

<span data-ttu-id="6aa07-122">然后在中，将 `ConfigureServices(IServiceCollection services)` :::no-loc(cookie)::: 策略配置为在 :::no-loc(cookie)::: 附加或删除时调用帮助器类。</span><span class="sxs-lookup"><span data-stu-id="6aa07-122">Then in the `ConfigureServices(IServiceCollection services)` configure the :::no-loc(cookie)::: policy to call out to a helper class when :::no-loc(cookie):::s are appended or deleted.</span></span> <span data-ttu-id="6aa07-123">例如： 。</span><span class="sxs-lookup"><span data-stu-id="6aa07-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<:::no-loc(Cookie):::PolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppend:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
        options.OnDelete:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
    });
}

private void CheckSameSite(HttpContext httpContext, :::no-loc(Cookie):::Options options)
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

<span data-ttu-id="6aa07-124">Helper 函数 `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)` ：</span><span class="sxs-lookup"><span data-stu-id="6aa07-124">The helper function `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)`:</span></span>

* <span data-ttu-id="6aa07-125">当 :::no-loc(cookie)::: 向请求追加或从请求中删除时，将调用。</span><span class="sxs-lookup"><span data-stu-id="6aa07-125">Is called when :::no-loc(cookie):::s are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="6aa07-126">检查 `SameSite` 属性是否设置为 `None` 。</span><span class="sxs-lookup"><span data-stu-id="6aa07-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="6aa07-127">如果将 `SameSite` 设置为 `None` ，并且已知当前用户代理不支持 none 特性值，则为。</span><span class="sxs-lookup"><span data-stu-id="6aa07-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="6aa07-128">使用 [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) 类完成检查：</span><span class="sxs-lookup"><span data-stu-id="6aa07-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="6aa07-129">设置 `SameSite` 为不通过将属性设置为来发出该值 `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="6aa07-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="6aa07-130">目标 .NET Framework</span><span class="sxs-lookup"><span data-stu-id="6aa07-130">Targeting .NET Framework</span></span>

<span data-ttu-id="6aa07-131">ASP.NET Core 和 System.web (ASP.NET 经典) 具有独立的 SameSite 实现。</span><span class="sxs-lookup"><span data-stu-id="6aa07-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="6aa07-132">如果使用 ASP.NET Core 或 ( .NET 4.7.2) 适用于 ASP.NET Core，则不需要 .NET Framework 的 SameSite KB 修补程序。</span><span class="sxs-lookup"><span data-stu-id="6aa07-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="6aa07-133">.NET 上的 ASP.NET Core 要求更新 nuget 程序包依赖项以获取适当的修补程序。</span><span class="sxs-lookup"><span data-stu-id="6aa07-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="6aa07-134">若要获取的 ASP.NET Core 更改 .NET Framework 确保你对修补的包以及版本 (2.1.14 或更高版本2.1 版本) 直接引用。</span><span class="sxs-lookup"><span data-stu-id="6aa07-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.:::no-loc(Cookie):::Policy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="6aa07-135">更多信息</span><span class="sxs-lookup"><span data-stu-id="6aa07-135">More Information</span></span>
 
<span data-ttu-id="6aa07-136">[Chrome 更新](https://www.chromium.org/updates/same-site) 
[ASP.NET Core SameSite 文档](../samesite.md?view=aspnetcore-2.1) 
[ASP.NET Core 2.1 SameSite 更改公告](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="6aa07-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](../samesite.md?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>