---
title: 转换 web.config
author: rick-anderson
description: 了解如何在发布 ASP.NET Core 应用时转换 web.config 文件。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 3d0bbfceb6dfedd7234112d33fd99e91a34f5793
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015577"
---
# <a name="transform-webconfig"></a>转换 web.config

作者：[Vijay Ramakrishnan](https://github.com/vijayrkn)

基于以下内容发布应用时，可以自动应用对 web.config 文件的转换：

* [生成配置](#build-configuration)
* [Profile](#profile)
* [环境](#environment)
* [自定义](#custom)

以下 web.config 生成方案中的任何一个都会发生转换：

* 由 `Microsoft.NET.Sdk.Web` SDK 自动生成。
* 由开发人员在应用的[内容根目录](xref:fundamentals/index#content-root)中提供。

## <a name="build-configuration"></a>生成配置

首先运行生成配置转换。

为需要 web.config 转换的每个[生成配置（调试|发布）](/dotnet/core/tools/dotnet-publish#options)添加 web.{CONFIGURATION}.config 文件。

以下示例在 web.Release.config 中设置特定于配置的环境变量：

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

当配置设置为“发布”时，将应用转换：

```dotnetcli
dotnet publish --configuration Release
```

配置的 MSBuild 属性为 `$(Configuration)`。

## <a name="profile"></a>配置文件

在[生成配置](#build-configuration)转换后，第二个运行配置文件转换。

为需要 web.config 转换的每个配置文件配置添加 web.{PROFILE}.config 文件。

以下示例在 web.FolderProfile.config 中为文件夹发布配置文件设置特定于配置文件的环境变量：

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

当配置文件为 FolderProfile 时，将应用转换：

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

配置文件名称的 MSBuild 属性为 `$(PublishProfile)`。

如果未传递任何配置文件，则默认配置文件名称为 FileSystem，如果该文件存在于应用的内容根目录中，则应用 web.FileSystem.config。

## <a name="environment"></a>环境

在[生成配置](#build-configuration)和[配置文件](#profile)转换后，第三个运行环境转换。

为需要 web.config 转换的每个[环境](xref:fundamentals/environments)添加 web.{ENVIRONMENT}.config 文件。

以下示例在 web.Production.config 中为生产环境设置特定于环境的环境变量：

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

当环境为“生产”时，将应用转换：

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

环境的 MSBuild 属性为 `$(EnvironmentName)`。

从 Visual Studio 发布并使用发布配置文件时，请参阅 <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>。

指定环境名称时，`ASPNETCORE_ENVIRONMENT` 环境变量会自动添加到 web.config 文件中。

## <a name="custom"></a>自定义

在[生成配置](#build-configuration)、[配置文件](#profile)和[环境](#environment)转换后，最后运行自定义转换。

为需要 web.config 转换的每个自定义配置添加 {CUSTOM_NAME}.transform 文件。

以下示例在 custom.transform 中设置自定义转换环境变量：

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

将 `CustomTransformFileName` 属性传递给 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令时，将应用转换：

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

配置文件名称的 MSBuild 属性为 `$(CustomTransformFileName)`。

## <a name="prevent-webconfig-transformation"></a>阻止 web.config 转换

若要阻止转换 web.config 文件，请设置 MSBuild 属性 `$(IsWebConfigTransformDisabled)`：

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>其他资源

* [用于 Web 应用程序项目部署的 Web.config 转换语法](/previous-versions/dd465326(v=vs.100))
* [用于使用 Visual Studio 的 Web 项目部署的 Web.config 转换语法](/previous-versions/aspnet/dd465326(v=vs.110))
