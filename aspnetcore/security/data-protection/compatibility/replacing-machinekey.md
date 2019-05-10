---
title: 替换为在 ASP.NET Core 中的 ASP.NET machineKey
author: rick-anderson
description: 了解如何将 machineKey 在 ASP.NET 中允许使用一个新的和更安全的数据保护系统。
ms.author: riande
ms.date: 04/06/2019
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2317cb50cfe63226baf336ebfc5d681d1cebe5c6
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895334"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>替换为在 ASP.NET Core 中的 ASP.NET machineKey

<a name="compatibility-replacing-machinekey"></a>

实现`<machineKey>`在 ASP.NET 中的元素[是可替换](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)。 这允许对 ASP.NET 加密例程大多数调用，以通过替换数据保护机制，包括新的数据保护系统路由。

## <a name="package-installation"></a>包安装

> [!NOTE]
> 新的数据保护系统只能安装到现有 ASP.NET 应用程序面向.NET 4.5.1 或更高版本。 安装将失败，如果应用程序面向.NET 4.5 或减少。

若要在现有的 ASP.NET 4.5.1+ 项目中安装新的数据保护系统，安装包 Microsoft.AspNetCore.DataProtection.SystemWeb。 这将使用数据保护系统实例化[默认配置](xref:security/data-protection/configuration/default-settings)设置。

安装包时，它将插入到一行*Web.config*这是告诉 ASP.NET，要将其用于[大多数加密操作](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)，包括窗体身份验证、 视图状态和对的调用MachineKey.Protect。 插入的行中读取，如下所示。

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> 您可以告知是否处于活动状态通过检查字段，例如访问新的数据保护系统`__VIEWSTATE`，这应开始使用"CfDJ8"如下面的示例中所示。 "CfDJ8"是标识受数据保护系统的有效负载的魔力"09 F0 第 9 频道 F0"标头的 base64 表示形式。

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>包配置

使用默认值零安装程序配置实例化的数据保护系统。 但是，由于默认情况下密钥保存到本地文件系统，这不适用于场中部署的应用程序。 若要解决此问题，您可以通过创建一种类型的子类 DataProtectionStartup 提供配置和重写其 ConfigureServices 方法。

下面是配置保留密钥的位置和方式静态加密的自定义数据保护启动类型的示例。 它还重写默认应用隔离策略通过提供其自己的应用程序名称。

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
> 此外可以使用`<machineKey applicationName="my-app" ... />`代替 SetApplicationName 显式调用。 这是为了避免强制开发人员创建 DataProtectionStartup 派生类型，如果他们想要配置所有已设置的应用程序名称的便捷机制。

若要启用此自定义配置，请返回到 Web.config，并查找`<appSettings>`包安装到的配置文件添加的元素。 它将类似以下标记：

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

填充的空白值与刚创建的 DataProtectionStartup 派生的类型的程序集限定名称。 如果应用程序的名称为 DataProtectionDemo，这将如下所示如下。

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

新配置数据保护系统现在已准备在应用程序内部使用。
