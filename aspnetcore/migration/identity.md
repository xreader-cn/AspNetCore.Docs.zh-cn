---
title: 将身份验证和标识迁移到 ASP.NET Core
author: ardalis
description: 了解如何将身份验证和标识从 ASP.NET MVC 项目迁移到 ASP.NET Core MVC 项目。
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: f821930dbd36de18db31104cddf34c563009a506
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022272"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a><span data-ttu-id="a9248-103">将身份验证和标识迁移到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9248-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="a9248-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a9248-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a9248-105">在以前的文章中，我们[配置从 ASP.NET MVC 项目迁移到 ASP.NET Core MVC](xref:migration/configuration)。</span><span class="sxs-lookup"><span data-stu-id="a9248-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="a9248-106">本文将迁移注册、登录和用户管理功能。</span><span class="sxs-lookup"><span data-stu-id="a9248-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-identity-and-membership"></a><span data-ttu-id="a9248-107">配置标识和成员身份</span><span class="sxs-lookup"><span data-stu-id="a9248-107">Configure Identity and Membership</span></span>

<span data-ttu-id="a9248-108">在 ASP.NET MVC 中, 使用*Startup.Auth.cs*和*IdentityConfig.cs*中的 ASP.NET Identity 配置身份验证和标识功能, 这些功能位于*App_Start*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="a9248-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs*, located in the *App_Start* folder.</span></span> <span data-ttu-id="a9248-109">在 ASP.NET Core MVC，这些功能在中配置*Startup.cs*。</span><span class="sxs-lookup"><span data-stu-id="a9248-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="a9248-110">`Microsoft.AspNetCore.Identity.EntityFrameworkCore`安装和`Microsoft.AspNetCore.Authentication.Cookies` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a9248-110">Install the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` and `Microsoft.AspNetCore.Authentication.Cookies` NuGet packages.</span></span>

<span data-ttu-id="a9248-111">然后, 打开*Startup.cs*并更新`Startup.ConfigureServices`方法以使用实体框架和标识服务:</span><span class="sxs-lookup"><span data-stu-id="a9248-111">Then, open *Startup.cs* and update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

<span data-ttu-id="a9248-112">此时, 在上面的代码中引用了两个类型, 这些类型尚未从 ASP.NET MVC 项目迁移: `ApplicationDbContext`和。 `ApplicationUser`</span><span class="sxs-lookup"><span data-stu-id="a9248-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="a9248-113">创建一个新*模型*文件夹中 ASP.NET Core 项目，并将两个类添加到它对应于这些类型。</span><span class="sxs-lookup"><span data-stu-id="a9248-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="a9248-114">你将在 */Models/IdentityModels.cs*中找到这些类的 ASP.NET MVC 版本, 但我们会在迁移的项目中对每个类使用一个文件, 因为这样做更为清晰。</span><span class="sxs-lookup"><span data-stu-id="a9248-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs*, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="a9248-115">*ApplicationUser.cs*:</span><span class="sxs-lookup"><span data-stu-id="a9248-115">*ApplicationUser.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="a9248-116">*ApplicationDbContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="a9248-116">*ApplicationDbContext.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

<span data-ttu-id="a9248-117">ASP.NET Core MVC 初学者 Web 项目不包括多的自定义的用户，或`ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="a9248-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="a9248-118">迁移实际应用时, 还需要迁移应用的用户和`DbContext`类的所有自定义属性和方法, 以及应用所使用的任何其他模型类。</span><span class="sxs-lookup"><span data-stu-id="a9248-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="a9248-119">例如, 如果`DbContext` `DbSet<Album>`具有, `Album`则需要迁移该类。</span><span class="sxs-lookup"><span data-stu-id="a9248-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="a9248-120">将这些文件放入后,可通过更新其`using`语句对 Startup.cs 文件进行编译:</span><span class="sxs-lookup"><span data-stu-id="a9248-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="a9248-121">现在, 我们的应用程序已准备好支持身份验证和标识服务。</span><span class="sxs-lookup"><span data-stu-id="a9248-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="a9248-122">只需将这些功能公开给用户。</span><span class="sxs-lookup"><span data-stu-id="a9248-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="a9248-123">迁移注册和登录逻辑</span><span class="sxs-lookup"><span data-stu-id="a9248-123">Migrate registration and login logic</span></span>

<span data-ttu-id="a9248-124">对于使用实体框架和 SQL Server 配置的应用和数据访问配置的标识服务, 我们已准备好添加注册和登录到应用的支持。</span><span class="sxs-lookup"><span data-stu-id="a9248-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="a9248-125">回忆一下, 在[迁移过程](xref:migration/mvc#migrate-the-layout-file)中, 我们在 *_Layout*中注释掉对 *_LoginPartial*的引用。</span><span class="sxs-lookup"><span data-stu-id="a9248-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml*.</span></span> <span data-ttu-id="a9248-126">现在可以返回到该代码, 取消注释, 并添加必要的控制器和视图以支持登录功能。</span><span class="sxs-lookup"><span data-stu-id="a9248-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="a9248-127">取消注释`@Html.Partial` *_Layout*中的行:</span><span class="sxs-lookup"><span data-stu-id="a9248-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml*:</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="a9248-128">现在, 将名为 *_LoginPartial*的新 Razor 视图添加到*Views/Shared*文件夹:</span><span class="sxs-lookup"><span data-stu-id="a9248-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="a9248-129">用以下代码更新 *_LoginPartial* (替换其所有内容):</span><span class="sxs-lookup"><span data-stu-id="a9248-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

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

<span data-ttu-id="a9248-130">此时, 您应该能够在浏览器中刷新站点。</span><span class="sxs-lookup"><span data-stu-id="a9248-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="a9248-131">总结</span><span class="sxs-lookup"><span data-stu-id="a9248-131">Summary</span></span>

<span data-ttu-id="a9248-132">ASP.NET Core 引入 ASP.NET 标识功能更改。</span><span class="sxs-lookup"><span data-stu-id="a9248-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="a9248-133">在本文中，你看到了如何将 ASP.NET 标识的身份验证和用户管理功能迁移到 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="a9248-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
