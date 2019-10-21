---
title: 在 ASP.NET Core 中访问 HttpContext
author: coderandhiker
description: 了解如何在 ASP.NET Core 中访问 HttpContext。
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: fundamentals/httpcontext
ms.openlocfilehash: 888adf6d61e6968127385952e65f942e86b7eb63
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288979"
---
# <a name="access-httpcontext-in-aspnet-core"></a>在 ASP.NET Core 中访问 HttpContext

ASP.NET Core 应用通过 [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) 接口及其默认实现 [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor) 来访问 `HttpContext`。 只有在需要访问服务内的 `HttpContext` 时，才有必要使用 `IHttpContextAccessor`。

::: moniker range=">= aspnetcore-2.0"

## <a name="use-httpcontext-from-razor-pages"></a>通过 Razor Pages 使用 HttpContext

Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) 公开 [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) 属性：

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

::: moniker-end

## <a name="use-httpcontext-from-a-razor-view"></a>通过 Razor 视图使用 HttpContext

Razor 视图通过视图上的 [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) 属性直接公开 `HttpContext`。 下面的示例使用 Windows 身份验证检索 Intranet 应用中的当前用户名：

```cshtml
@{
    var username = Context.User.Identity.Name;
}
```

## <a name="use-httpcontext-from-a-controller"></a>通过控制器使用 HttpContext

控制器公开 [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) 属性：

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;
        // Do something with the PathBase.

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a>通过中间件使用 HttpContext

使用自定义中间件组件时，`HttpContext` 传递到 `Invoke` 或 `InvokeAsync` 方法，在中间件配置后可供访问：

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        // Middleware initialization optionally using HttpContext
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>通过自定义组件使用 HttpContext

对于需要访问 `HttpContext` 的其他框架和自定义组件，建议使用内置的[依赖项注入](xref:fundamentals/dependency-injection)容器来注册依赖项。 依赖项注入容器向任意类提供 `IHttpContextAccessor`，以供类在自己的构造函数中将它声明为依赖项。

::: moniker range=">= aspnetcore-2.1"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc();
     services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

如下示例中：

* `UserRepository` 声明自己对 `IHttpContextAccessor` 的依赖。
* 当依赖项注入容器解析依赖链并创建 `UserRepository` 实例时，就会注入依赖项。

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

## <a name="httpcontext-access-from-a-background-thread"></a>从后台线程访问 HttpContext

`HttpContext` 不是线程安全。 在处理请求之外读取或写入 `HttpContext` 的属性可能会导致 `NullReferenceException`。

> [!NOTE]
> 在处理请求之外使用 `HttpContext` 通常会导致 `NullReferenceException`。 如果应用生成偶发的 `NullReferenceException`，请评审启动后台处理的部分代码，或者在请求完成后继续处理的部分代码。 查找诸如将控制器方法定义为 `async void` 的错误。

要使用 `HttpContext` 数据安全地执行后台工作，请执行以下操作：

* 在请求处理过程中复制所需的数据。
* 将复制的数据传递给后台任务。

要避免不安全代码，请勿将 `HttpContext` 传递给执行后台工作的方法，而是传递所需要的数据。

```csharp
public class EmailController
{
    public ActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        // Starts sending an email, but doesn't wait for it to complete
        _ = SendEmailCore(correlationId);
        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        // send the email
    }
}
