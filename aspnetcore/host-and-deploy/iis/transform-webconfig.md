---
title: 转换 web.config
author: guardrex
description: 了解如何在发布 ASP.NET Core 应用时转换 web.config 文件。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2019
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 58dee024f5b032d1ef13df02648727b6a07eac1f
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813352"
---
# <a name="transform-webconfig"></a><span data-ttu-id="213b3-103">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="213b3-103">Transform web.config</span></span>

<span data-ttu-id="213b3-104">作者：[Vijay Ramakrishnan](https://github.com/vijayrkn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="213b3-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="213b3-105">基于以下内容发布应用时，可以自动应用对 web.config  文件的转换：</span><span class="sxs-lookup"><span data-stu-id="213b3-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="213b3-106">生成配置</span><span class="sxs-lookup"><span data-stu-id="213b3-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="213b3-107">Profile</span><span class="sxs-lookup"><span data-stu-id="213b3-107">Profile</span></span>](#profile)
* [<span data-ttu-id="213b3-108">环境</span><span class="sxs-lookup"><span data-stu-id="213b3-108">Environment</span></span>](#environment)
* [<span data-ttu-id="213b3-109">自定义</span><span class="sxs-lookup"><span data-stu-id="213b3-109">Custom</span></span>](#custom)

<span data-ttu-id="213b3-110">以下 web.config  生成方案中的任何一个都会发生转换：</span><span class="sxs-lookup"><span data-stu-id="213b3-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="213b3-111">由 `Microsoft.NET.Sdk.Web` SDK 自动生成。</span><span class="sxs-lookup"><span data-stu-id="213b3-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="213b3-112">由开发人员在应用的内容根目录中提供。</span><span class="sxs-lookup"><span data-stu-id="213b3-112">Provided by the developer in the content root of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="213b3-113">生成配置</span><span class="sxs-lookup"><span data-stu-id="213b3-113">Build configuration</span></span>

<span data-ttu-id="213b3-114">首先运行生成配置转换。</span><span class="sxs-lookup"><span data-stu-id="213b3-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="213b3-115">为需要 web.config  转换的每个[生成配置（调试|发布）](/dotnet/core/tools/dotnet-publish#options)添加 web.{CONFIGURATION}.config  文件。</span><span class="sxs-lookup"><span data-stu-id="213b3-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="213b3-116">以下示例在 web.Release.config  中设置特定于配置的环境变量：</span><span class="sxs-lookup"><span data-stu-id="213b3-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

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

<span data-ttu-id="213b3-117">当配置设置为“发布”  时，将应用转换：</span><span class="sxs-lookup"><span data-stu-id="213b3-117">The transform is applied when the configuration is set to *Release*:</span></span>

```console
dotnet publish --configuration Release
```

<span data-ttu-id="213b3-118">配置的 MSBuild 属性为 `$(Configuration)`。</span><span class="sxs-lookup"><span data-stu-id="213b3-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="213b3-119">配置文件</span><span class="sxs-lookup"><span data-stu-id="213b3-119">Profile</span></span>

<span data-ttu-id="213b3-120">在[生成配置](#build-configuration)转换后，第二个运行配置文件转换。</span><span class="sxs-lookup"><span data-stu-id="213b3-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="213b3-121">为需要 web.config  转换的每个配置文件配置添加 web.{PROFILE}.config  文件。</span><span class="sxs-lookup"><span data-stu-id="213b3-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="213b3-122">以下示例在 web.FolderProfile.config  中为文件夹发布配置文件设置特定于配置文件的环境变量：</span><span class="sxs-lookup"><span data-stu-id="213b3-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

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

<span data-ttu-id="213b3-123">当配置文件为 FolderProfile  时，将应用转换：</span><span class="sxs-lookup"><span data-stu-id="213b3-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```console
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="213b3-124">配置文件名称的 MSBuild 属性为 `$(PublishProfile)`。</span><span class="sxs-lookup"><span data-stu-id="213b3-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="213b3-125">如果未传递任何配置文件，则默认配置文件名称为 FileSystem  ，如果该文件存在于应用的内容根目录中，则应用 web.FileSystem.config  。</span><span class="sxs-lookup"><span data-stu-id="213b3-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="213b3-126">环境</span><span class="sxs-lookup"><span data-stu-id="213b3-126">Environment</span></span>

<span data-ttu-id="213b3-127">在[生成配置](#build-configuration)和[配置文件](#profile)转换后，第三个运行环境转换。</span><span class="sxs-lookup"><span data-stu-id="213b3-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="213b3-128">为需要 web.config  转换的每个[环境](xref:fundamentals/environments)添加 web.{ENVIRONMENT}.config  文件。</span><span class="sxs-lookup"><span data-stu-id="213b3-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="213b3-129">以下示例在 web.Production.config  中为生产环境设置特定于环境的环境变量：</span><span class="sxs-lookup"><span data-stu-id="213b3-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

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

<span data-ttu-id="213b3-130">当环境为“生产”  时，将应用转换：</span><span class="sxs-lookup"><span data-stu-id="213b3-130">The transform is applied when the environment is *Production*:</span></span>

```console
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="213b3-131">环境的 MSBuild 属性为 `$(EnvironmentName)`。</span><span class="sxs-lookup"><span data-stu-id="213b3-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="213b3-132">从 Visual Studio 发布并使用发布配置文件时，请参阅 <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>。</span><span class="sxs-lookup"><span data-stu-id="213b3-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="213b3-133">指定环境名称时，`ASPNETCORE_ENVIRONMENT` 环境变量会自动添加到 web.config  文件中。</span><span class="sxs-lookup"><span data-stu-id="213b3-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="213b3-134">自定义</span><span class="sxs-lookup"><span data-stu-id="213b3-134">Custom</span></span>

<span data-ttu-id="213b3-135">在[生成配置](#build-configuration)、[配置文件](#profile)和[环境](#environment)转换后，最后运行自定义转换。</span><span class="sxs-lookup"><span data-stu-id="213b3-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="213b3-136">为需要 web.config  转换的每个自定义配置添加 {CUSTOM_NAME}.transform  文件。</span><span class="sxs-lookup"><span data-stu-id="213b3-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="213b3-137">以下示例在 custom.transform  中设置自定义转换环境变量：</span><span class="sxs-lookup"><span data-stu-id="213b3-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

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

<span data-ttu-id="213b3-138">将 `CustomTransformFileName` 属性传递给 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令时，将应用转换：</span><span class="sxs-lookup"><span data-stu-id="213b3-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```console
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="213b3-139">配置文件名称的 MSBuild 属性为 `$(CustomTransformFileName)`。</span><span class="sxs-lookup"><span data-stu-id="213b3-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="213b3-140">阻止 web.config 转换</span><span class="sxs-lookup"><span data-stu-id="213b3-140">Prevent web.config transformation</span></span>

<span data-ttu-id="213b3-141">若要阻止转换 web.config  文件，请设置 MSBuild 属性 `$(IsWebConfigTransformDisabled)`：</span><span class="sxs-lookup"><span data-stu-id="213b3-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```console
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="213b3-142">其他资源</span><span class="sxs-lookup"><span data-stu-id="213b3-142">Additional resources</span></span>

* [<span data-ttu-id="213b3-143">用于 Web 应用程序项目部署的 Web.config 转换语法</span><span class="sxs-lookup"><span data-stu-id="213b3-143">Web.config Transformation Syntax for Web Application Project Deployment</span></span>](https://go.microsoft.com/fwlink/?LinkId=301874)
* <span data-ttu-id="213b3-144">[用于使用 Visual Studio 的 Web 项目部署的 Web.config 转换语法](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="213b3-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
