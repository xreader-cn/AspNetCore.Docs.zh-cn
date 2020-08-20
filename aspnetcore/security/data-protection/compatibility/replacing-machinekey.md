---
title: 替换 ASP.NET Core 中的 ASP.NET machineKey
author: rick-anderson
description: 了解如何在 ASP.NET 中替换 machineKey，以允许使用新的、更安全的数据保护系统。
ms.author: riande
ms.date: 04/06/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 8cae0b8f1c4582e272061ff87868b32568dfe595
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625565"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="64bad-103">替换 ASP.NET Core 中的 ASP.NET machineKey</span><span class="sxs-lookup"><span data-stu-id="64bad-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="64bad-104">`<machineKey>`ASP.NET 中元素的实现[是可替换](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)的。</span><span class="sxs-lookup"><span data-stu-id="64bad-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="64bad-105">这样，就可以通过一种替代数据保护机制（包括新的数据保护系统）来路由对 ASP.NET 加密例程的大多数调用。</span><span class="sxs-lookup"><span data-stu-id="64bad-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="64bad-106">包安装</span><span class="sxs-lookup"><span data-stu-id="64bad-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="64bad-107">新的数据保护系统只能安装到面向 .NET 4.5.1 或更高版本的现有 ASP.NET 应用程序中。</span><span class="sxs-lookup"><span data-stu-id="64bad-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="64bad-108">如果应用程序面向 .NET 4.5 或更低版本，则安装将失败。</span><span class="sxs-lookup"><span data-stu-id="64bad-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="64bad-109">若要在现有的 ASP.NET 4.5.1 + 项目中安装新的数据保护系统，请 Microsoft.AspNetCore.DataProtection.SystemWeb 安装包。</span><span class="sxs-lookup"><span data-stu-id="64bad-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="64bad-110">这将使用 [默认配置](xref:security/data-protection/configuration/default-settings) 设置来实例化数据保护系统。</span><span class="sxs-lookup"><span data-stu-id="64bad-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="64bad-111">安装包时，它会在 *Web.config* 中插入一行，告诉 ASP.NET 将其用于 [大多数加密操作](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)，包括 forms 身份验证、视图状态和对 MachineKey 的调用。</span><span class="sxs-lookup"><span data-stu-id="64bad-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="64bad-112">插入的行如下所示。</span><span class="sxs-lookup"><span data-stu-id="64bad-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="64bad-113">可以通过检查 `__VIEWSTATE` （如下面的示例中应以 "CfDJ8" 开头）等字段来判断新的数据保护系统是否处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="64bad-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="64bad-114">"CfDJ8" 是用于标识受数据保护系统保护的有效负载的幻 "09 F0 C9 F0" 标头的 base64 表示形式。</span><span class="sxs-lookup"><span data-stu-id="64bad-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="64bad-115">包配置</span><span class="sxs-lookup"><span data-stu-id="64bad-115">Package configuration</span></span>

<span data-ttu-id="64bad-116">数据保护系统使用默认的零安装配置进行实例化。</span><span class="sxs-lookup"><span data-stu-id="64bad-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="64bad-117">但是，由于默认情况下会将密钥保存到本地文件系统，因此，这不能用于在场中部署的应用程序。</span><span class="sxs-lookup"><span data-stu-id="64bad-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="64bad-118">若要解决此问题，可以通过创建子类 DataProtectionStartup 并重写其 ConfigureServices 方法的类型来提供配置。</span><span class="sxs-lookup"><span data-stu-id="64bad-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="64bad-119">下面是一个自定义数据保护启动类型的示例，该类型配置了两个密钥的保存位置和静态加密方式。</span><span class="sxs-lookup"><span data-stu-id="64bad-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="64bad-120">它还通过提供自己的应用程序名称来替代默认应用隔离策略。</span><span class="sxs-lookup"><span data-stu-id="64bad-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="64bad-121">你还可以使用 `<machineKey applicationName="my-app" ... />` 来代替对 SetApplicationName 的显式调用。</span><span class="sxs-lookup"><span data-stu-id="64bad-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="64bad-122">这是一种简便的机制，可避免开发人员在要配置的所有应用程序名称设置的情况下，强制创建 DataProtectionStartup 派生的类型。</span><span class="sxs-lookup"><span data-stu-id="64bad-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="64bad-123">若要启用此自定义配置，请返回 Web.config，并查找 `<appSettings>` 包安装添加到配置文件中的元素。</span><span class="sxs-lookup"><span data-stu-id="64bad-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="64bad-124">它将类似于以下标记：</span><span class="sxs-lookup"><span data-stu-id="64bad-124">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="64bad-125">用刚刚创建的 DataProtectionStartup 派生类型的程序集限定名称填充空白值。</span><span class="sxs-lookup"><span data-stu-id="64bad-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="64bad-126">如果应用程序的名称为 DataProtectionDemo，则如下所示。</span><span class="sxs-lookup"><span data-stu-id="64bad-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="64bad-127">新配置的数据保护系统现在可以在应用程序内部使用。</span><span class="sxs-lookup"><span data-stu-id="64bad-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
