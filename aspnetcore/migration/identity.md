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
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>将身份验证和标识迁移到 ASP.NET Core

作者：[Steve Smith](https://ardalis.com/)

在以前的文章中，我们[配置从 ASP.NET MVC 项目迁移到 ASP.NET Core MVC](xref:migration/configuration)。 本文将迁移注册、登录和用户管理功能。

## <a name="configure-identity-and-membership"></a>配置标识和成员身份

在 ASP.NET MVC 中, 使用*Startup.Auth.cs*和*IdentityConfig.cs*中的 ASP.NET Identity 配置身份验证和标识功能, 这些功能位于*App_Start*文件夹中。 在 ASP.NET Core MVC，这些功能在中配置*Startup.cs*。

`Microsoft.AspNetCore.Identity.EntityFrameworkCore`安装和`Microsoft.AspNetCore.Authentication.Cookies` NuGet 包。

然后, 打开*Startup.cs*并更新`Startup.ConfigureServices`方法以使用实体框架和标识服务:

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

此时, 在上面的代码中引用了两个类型, 这些类型尚未从 ASP.NET MVC 项目迁移: `ApplicationDbContext`和。 `ApplicationUser` 创建一个新*模型*文件夹中 ASP.NET Core 项目，并将两个类添加到它对应于这些类型。 你将在 */Models/IdentityModels.cs*中找到这些类的 ASP.NET MVC 版本, 但我们会在迁移的项目中对每个类使用一个文件, 因为这样做更为清晰。

*ApplicationUser.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs*:

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

ASP.NET Core MVC 初学者 Web 项目不包括多的自定义的用户，或`ApplicationDbContext`。 迁移实际应用时, 还需要迁移应用的用户和`DbContext`类的所有自定义属性和方法, 以及应用所使用的任何其他模型类。 例如, 如果`DbContext` `DbSet<Album>`具有, `Album`则需要迁移该类。

将这些文件放入后,可通过更新其`using`语句对 Startup.cs 文件进行编译:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

现在, 我们的应用程序已准备好支持身份验证和标识服务。 只需将这些功能公开给用户。

## <a name="migrate-registration-and-login-logic"></a>迁移注册和登录逻辑

对于使用实体框架和 SQL Server 配置的应用和数据访问配置的标识服务, 我们已准备好添加注册和登录到应用的支持。 回忆一下, 在[迁移过程](xref:migration/mvc#migrate-the-layout-file)中, 我们在 *_Layout*中注释掉对 *_LoginPartial*的引用。 现在可以返回到该代码, 取消注释, 并添加必要的控制器和视图以支持登录功能。

取消注释`@Html.Partial` *_Layout*中的行:

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

现在, 将名为 *_LoginPartial*的新 Razor 视图添加到*Views/Shared*文件夹:

用以下代码更新 *_LoginPartial* (替换其所有内容):

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

此时, 您应该能够在浏览器中刷新站点。

## <a name="summary"></a>总结

ASP.NET Core 引入 ASP.NET 标识功能更改。 在本文中，你看到了如何将 ASP.NET 标识的身份验证和用户管理功能迁移到 ASP.NET Core。
