---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Microsoft 的概述。
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869761"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core Web SDK

### <a name="overview"></a>概述

`Microsoft.NET.Sdk.Web` 是用于生成 ASP.NET Core 应用的[MSBuild 项目 SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) 。 无需此 SDK 即可构建 ASP.NET Core 应用程序，但 Web SDK 如下：

* 旨在提供一流的体验。
* 适用于大多数用户的建议目标。

在项目中使用 Web SDK：

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

使用 Web SDK 启用的功能：

* 面向 .NET Core 3.0 或更高版本的项目隐式引用：

  * [ASP.NET Core 共享框架](xref:fundamentals/metapackage-app)。
  * 设计用于构建 ASP.NET Core 应用程序的[分析器](/visualstudio/extensibility/getting-started-with-roslyn-analyzers)。
* Web SDK 导入 MSBuild 目标，这些目标允许使用发布配置文件并使用 WebDeploy 进行发布。

### <a name="properties"></a>属性

| Property | 描述 |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | 禁用对 `Microsoft.AspNetCore.App` 共享框架的隐式引用。 |
| `DisableImplicitAspNetCoreAnalyzers` | 禁用对 ASP.NET Core 分析器的隐式引用。 |
| `DisableImplicitComponentsAnalyzers` | 生成 Blazor （服务器）应用程序时禁用对 Razor 组件分析器的隐式引用。 |
