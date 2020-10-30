---
title: '迁移身份验证和 :::no-loc(Identity)::: ASP.NET Core'
author: ardalis
description: 了解如何从 ASP.NET MVC 项目将身份验证和标识迁移到 ASP.NET Core MVC 项目。
ms.author: riande
ms.date: 3/22/2020
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
uid: migration/identity
ms.openlocfilehash: 8ceff0596c069d815c38b9bb526477a9d1430951
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060646"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a><span data-ttu-id="37739-103">迁移身份验证和 :::no-loc(Identity)::: ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37739-103">Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core</span></span>

<span data-ttu-id="37739-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="37739-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="37739-105">在前面的文章中，我们 [已将配置从 ASP.NET mvc 项目迁移到 ASP.NET CORE mvc](xref:migration/configuration)。</span><span class="sxs-lookup"><span data-stu-id="37739-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="37739-106">本文将迁移注册、登录和用户管理功能。</span><span class="sxs-lookup"><span data-stu-id="37739-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-no-locidentity-and-membership"></a><span data-ttu-id="37739-107">配置 :::no-loc(Identity)::: 和成员资格</span><span class="sxs-lookup"><span data-stu-id="37739-107">Configure :::no-loc(Identity)::: and Membership</span></span>

<span data-ttu-id="37739-108">在 ASP.NET MVC 中，身份验证和标识功能是使用 :::no-loc(Identity)::: *Startup.Auth.cs* 和 *:::no-loc(Identity)::: Config.cs* 中的 ASP.NET 配置的，位于 *App_Start* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="37739-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET :::no-loc(Identity)::: in *Startup.Auth.cs* and *:::no-loc(Identity):::Config.cs* , located in the *App_Start* folder.</span></span> <span data-ttu-id="37739-109">在 ASP.NET Core MVC 中，这些功能在 *Startup.cs* 中进行配置。</span><span class="sxs-lookup"><span data-stu-id="37739-109">In ASP.NET Core MVC, these features are configured in *Startup.cs* .</span></span>

<span data-ttu-id="37739-110">安装以下 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="37739-110">Install the following NuGet packages:</span></span>

* `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s`
* `Microsoft.EntityFrameworkCore.SqlServer`

<span data-ttu-id="37739-111">在 *Startup.cs* 中，更新 `Startup.ConfigureServices` 方法以使用实体框架和 :::no-loc(Identity)::: 服务：</span><span class="sxs-lookup"><span data-stu-id="37739-111">In *Startup.cs* , update the `Startup.ConfigureServices` method to use Entity Framework and :::no-loc(Identity)::: services:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

<span data-ttu-id="37739-112">此时，在上面的代码中引用了两个类型，这些类型尚未从 ASP.NET MVC 项目迁移： `ApplicationDbContext` 和 `ApplicationUser` 。</span><span class="sxs-lookup"><span data-stu-id="37739-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="37739-113">在 ASP.NET Core 项目中创建新的 " *模型* " 文件夹，并将两个与这些类型对应的类添加到其中。</span><span class="sxs-lookup"><span data-stu-id="37739-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="37739-114">你将在 */Models/ :::no-loc(Identity)::: Models.cs* 中找到这些类的 ASP.NET MVC 版本，但我们会在迁移的项目中对每个类使用一个文件，因为这样做更为清晰。</span><span class="sxs-lookup"><span data-stu-id="37739-114">You will find the ASP.NET MVC versions of these classes in */Models/:::no-loc(Identity):::Models.cs* , but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="37739-115">*ApplicationUser.cs* ：</span><span class="sxs-lookup"><span data-stu-id="37739-115">*ApplicationUser.cs* :</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : :::no-loc(Identity):::User
  {
  }
}
```

<span data-ttu-id="37739-116">*ApplicationDbContext.cs* ：</span><span class="sxs-lookup"><span data-stu-id="37739-116">*ApplicationDbContext.cs* :</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the :::no-loc(ASP.NET Core Identity)::: model and override the defaults if needed.
            // For example, you can rename the :::no-loc(ASP.NET Core Identity)::: table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

<span data-ttu-id="37739-117">ASP.NET Core MVC 初学者 Web 项目不包括很多自定义的用户或 `ApplicationDbContext` 。</span><span class="sxs-lookup"><span data-stu-id="37739-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="37739-118">迁移实际应用时，还需要迁移应用的用户和类的所有自定义属性和方法，以及应用所使用的 `DbContext` 任何其他模型类。</span><span class="sxs-lookup"><span data-stu-id="37739-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="37739-119">例如，如果 `DbContext` 具有 `DbSet<Album>` ，则需要迁移 `Album` 该类。</span><span class="sxs-lookup"><span data-stu-id="37739-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="37739-120">将这些文件放入后，可通过更新其语句对 *Startup.cs* 文件进行编译 `using` ：</span><span class="sxs-lookup"><span data-stu-id="37739-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.:::no-loc(Identity):::;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="37739-121">现在，我们的应用程序已准备好支持身份验证和 :::no-loc(Identity)::: 服务。</span><span class="sxs-lookup"><span data-stu-id="37739-121">Our app is now ready to support authentication and :::no-loc(Identity)::: services.</span></span> <span data-ttu-id="37739-122">只需将这些功能公开给用户。</span><span class="sxs-lookup"><span data-stu-id="37739-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="37739-123">迁移注册和登录逻辑</span><span class="sxs-lookup"><span data-stu-id="37739-123">Migrate registration and login logic</span></span>

<span data-ttu-id="37739-124">通过为 :::no-loc(Identity)::: 应用配置的服务和使用实体框架和 SQL Server 配置的数据访问，我们已准备好添加注册和登录到应用的支持。</span><span class="sxs-lookup"><span data-stu-id="37739-124">With :::no-loc(Identity)::: services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="37739-125">请记住，在 [迁移过程](xref:migration/mvc#migrate-the-layout-file)中，我们已注释掉对 *_Layout* 中 *_LoginPartial* 的引用。</span><span class="sxs-lookup"><span data-stu-id="37739-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml* .</span></span> <span data-ttu-id="37739-126">现在可以返回到该代码，取消注释，并添加必要的控制器和视图以支持登录功能。</span><span class="sxs-lookup"><span data-stu-id="37739-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="37739-127">取消注释 `@Html.Partial` *_Layout* 中的行：</span><span class="sxs-lookup"><span data-stu-id="37739-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml* :</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="37739-128">现在，将 :::no-loc(Razor)::: 名为 *_LoginPartial* 的新视图添加到 *Views/Shared* 文件夹中：</span><span class="sxs-lookup"><span data-stu-id="37739-128">Now, add a new :::no-loc(Razor)::: view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="37739-129">用下面的代码更新 *_LoginPartial* ， () 替换其所有内容：</span><span class="sxs-lookup"><span data-stu-id="37739-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

<span data-ttu-id="37739-130">此时，您应该能够在浏览器中刷新站点。</span><span class="sxs-lookup"><span data-stu-id="37739-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="37739-131">摘要</span><span class="sxs-lookup"><span data-stu-id="37739-131">Summary</span></span>

<span data-ttu-id="37739-132">ASP.NET Core 引入了对 ASP.NET 功能的更改 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="37739-132">ASP.NET Core introduces changes to the ASP.NET :::no-loc(Identity)::: features.</span></span> <span data-ttu-id="37739-133">本文介绍了如何将 ASP.NET 的身份验证和用户管理功能迁移 :::no-loc(Identity)::: 到 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="37739-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET :::no-loc(Identity)::: to ASP.NET Core.</span></span>
