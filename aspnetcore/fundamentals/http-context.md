---
title: 在 ASP.NET Core 中访问 HttpContext
author: coderandhiker
description: 了解如何在 ASP.NET Core 中访问 HttpContext。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: fundamentals/httpcontext
ms.openlocfilehash: 8a7ee180380c42ea745c91b8e6a18c1baa820220
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647010"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="7eb2d-103">在 ASP.NET Core 中访问 HttpContext</span><span class="sxs-lookup"><span data-stu-id="7eb2d-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="7eb2d-104">ASP.NET Core 应用通过 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 接口及其默认实现 <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> 访问 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="7eb2d-105">只有在需要访问服务内的 `HttpContext` 时，才有必要使用 `IHttpContextAccessor`。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="7eb2d-106">通过 Razor Pages 使用 HttpContext</span><span class="sxs-lookup"><span data-stu-id="7eb2d-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="7eb2d-107">Razor 页面 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 公开 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> 属性：</span><span class="sxs-lookup"><span data-stu-id="7eb2d-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="7eb2d-108">通过 Razor 视图使用 HttpContext</span><span class="sxs-lookup"><span data-stu-id="7eb2d-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="7eb2d-109">Razor 视图通过视图上的 [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) 属性直接公开 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="7eb2d-110">下面的示例使用 Windows 身份验证检索 Intranet 应用中的当前用户名：</span><span class="sxs-lookup"><span data-stu-id="7eb2d-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="7eb2d-111">通过控制器使用 HttpContext</span><span class="sxs-lookup"><span data-stu-id="7eb2d-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="7eb2d-112">控制器公开 [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) 属性：</span><span class="sxs-lookup"><span data-stu-id="7eb2d-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="7eb2d-113">通过中间件使用 HttpContext</span><span class="sxs-lookup"><span data-stu-id="7eb2d-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="7eb2d-114">使用自定义中间件组件时，`HttpContext` 传递到 `Invoke` 或 `InvokeAsync` 方法，在中间件配置后可供访问：</span><span class="sxs-lookup"><span data-stu-id="7eb2d-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="7eb2d-115">通过自定义组件使用 HttpContext</span><span class="sxs-lookup"><span data-stu-id="7eb2d-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="7eb2d-116">对于需要访问 `HttpContext` 的其他框架和自定义组件，建议使用内置的[依赖项注入](xref:fundamentals/dependency-injection)容器来注册依赖项。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7eb2d-117">依赖项注入容器向任意类提供 `IHttpContextAccessor`，以供类在自己的构造函数中将它声明为依赖项：</span><span class="sxs-lookup"><span data-stu-id="7eb2d-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

<span data-ttu-id="7eb2d-118">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="7eb2d-118">In the following example:</span></span>

* <span data-ttu-id="7eb2d-119">`UserRepository` 声明自己对 `IHttpContextAccessor` 的依赖。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="7eb2d-120">当依赖项注入容器解析依赖链并创建 `UserRepository` 实例时，就会注入依赖项。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="7eb2d-121">从后台线程访问 HttpContext</span><span class="sxs-lookup"><span data-stu-id="7eb2d-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="7eb2d-122">`HttpContext` 不是线程安全型。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="7eb2d-123">在处理请求之外读取或写入 `HttpContext` 的属性可能会导致 <xref:System.NullReferenceException>。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="7eb2d-124">如果应用生成偶发的 `NullReferenceException` 错误，请评审启动后台处理的部分代码，或者在请求完成后继续处理的部分代码。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="7eb2d-125">查找诸如将控制器方法定义为 `async void` 的错误。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="7eb2d-126">要使用 `HttpContext` 数据安全地执行后台工作，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7eb2d-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="7eb2d-127">在请求处理过程中复制所需的数据。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="7eb2d-128">将复制的数据传递给后台任务。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="7eb2d-129">要避免不安全代码，请勿将 `HttpContext` 传递给执行后台工作的方法。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="7eb2d-130">而是传递所需要的数据。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-130">Pass the required data instead.</span></span> <span data-ttu-id="7eb2d-131">在以下示例中，调用 `SendEmailCore`，开始发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="7eb2d-132">将 `correlationId` 传递到 `SendEmailCore`，而不是 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="7eb2d-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="7eb2d-133">代码执行不会等待 `SendEmailCore` 完成：</span><span class="sxs-lookup"><span data-stu-id="7eb2d-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
