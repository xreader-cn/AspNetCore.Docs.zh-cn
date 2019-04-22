---
title: 替换为在 ASP.NET Core 中的 ASP.NET machineKey
author: rick-anderson
description: 了解如何将 machineKey 在 ASP.NET 中允许使用一个新的和更安全的数据保护系统。
ms.author: riande
ms.date: 04/06/2019
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2317cb50cfe63226baf336ebfc5d681d1cebe5c6
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705544"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="50c77-103">替换为在 ASP.NET Core 中的 ASP.NET machineKey</span><span class="sxs-lookup"><span data-stu-id="50c77-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="50c77-104">实现`<machineKey>`在 ASP.NET 中的元素[是可替换](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)。</span><span class="sxs-lookup"><span data-stu-id="50c77-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="50c77-105">这允许对 ASP.NET 加密例程大多数调用，以通过替换数据保护机制，包括新的数据保护系统路由。</span><span class="sxs-lookup"><span data-stu-id="50c77-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="50c77-106">包安装</span><span class="sxs-lookup"><span data-stu-id="50c77-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="50c77-107">新的数据保护系统只能安装到现有 ASP.NET 应用程序面向.NET 4.5.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="50c77-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="50c77-108">安装将失败，如果应用程序面向.NET 4.5 或减少。</span><span class="sxs-lookup"><span data-stu-id="50c77-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="50c77-109">若要在现有的 ASP.NET 4.5.1+ 项目中安装新的数据保护系统，安装包 Microsoft.AspNetCore.DataProtection.SystemWeb。</span><span class="sxs-lookup"><span data-stu-id="50c77-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="50c77-110">这将使用数据保护系统实例化[默认配置](xref:security/data-protection/configuration/default-settings)设置。</span><span class="sxs-lookup"><span data-stu-id="50c77-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="50c77-111">安装包时，它将插入到一行*Web.config*这是告诉 ASP.NET，要将其用于[大多数加密操作](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)，包括窗体身份验证、 视图状态和对的调用MachineKey.Protect。</span><span class="sxs-lookup"><span data-stu-id="50c77-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="50c77-112">插入的行中读取，如下所示。</span><span class="sxs-lookup"><span data-stu-id="50c77-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="50c77-113">您可以告知是否处于活动状态通过检查字段，例如访问新的数据保护系统`__VIEWSTATE`，这应开始使用"CfDJ8"如下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="50c77-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="50c77-114">"CfDJ8"是标识受数据保护系统的有效负载的魔力"09 F0 第 9 频道 F0"标头的 base64 表示形式。</span><span class="sxs-lookup"><span data-stu-id="50c77-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="50c77-115">包配置</span><span class="sxs-lookup"><span data-stu-id="50c77-115">Package configuration</span></span>

<span data-ttu-id="50c77-116">使用默认值零安装程序配置实例化的数据保护系统。</span><span class="sxs-lookup"><span data-stu-id="50c77-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="50c77-117">但是，由于默认情况下密钥保存到本地文件系统，这不适用于场中部署的应用程序。</span><span class="sxs-lookup"><span data-stu-id="50c77-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="50c77-118">若要解决此问题，您可以通过创建一种类型的子类 DataProtectionStartup 提供配置和重写其 ConfigureServices 方法。</span><span class="sxs-lookup"><span data-stu-id="50c77-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="50c77-119">下面是配置保留密钥的位置和方式静态加密的自定义数据保护启动类型的示例。</span><span class="sxs-lookup"><span data-stu-id="50c77-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="50c77-120">它还重写默认应用隔离策略通过提供其自己的应用程序名称。</span><span class="sxs-lookup"><span data-stu-id="50c77-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

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
> <span data-ttu-id="50c77-121">此外可以使用`<machineKey applicationName="my-app" ... />`代替 SetApplicationName 显式调用。</span><span class="sxs-lookup"><span data-stu-id="50c77-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="50c77-122">这是为了避免强制开发人员创建 DataProtectionStartup 派生类型，如果他们想要配置所有已设置的应用程序名称的便捷机制。</span><span class="sxs-lookup"><span data-stu-id="50c77-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="50c77-123">若要启用此自定义配置，请返回到 Web.config，并查找`<appSettings>`包安装到的配置文件添加的元素。</span><span class="sxs-lookup"><span data-stu-id="50c77-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="50c77-124">它将类似以下标记：</span><span class="sxs-lookup"><span data-stu-id="50c77-124">It will look like the following markup:</span></span>

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

<span data-ttu-id="50c77-125">填充的空白值与刚创建的 DataProtectionStartup 派生的类型的程序集限定名称。</span><span class="sxs-lookup"><span data-stu-id="50c77-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="50c77-126">如果应用程序的名称为 DataProtectionDemo，这将如下所示如下。</span><span class="sxs-lookup"><span data-stu-id="50c77-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="50c77-127">新配置数据保护系统现在已准备在应用程序内部使用。</span><span class="sxs-lookup"><span data-stu-id="50c77-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
