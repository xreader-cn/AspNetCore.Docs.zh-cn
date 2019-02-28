---
title: 配置 Blazor 链接器
author: guardrex
description: 了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2019
uid: host-and-deploy/razor-components/configure-linker
ms.openlocfilehash: 7c53e7912ec3b0ae471ea38777f874f55a32487d
ms.sourcegitcommit: 0945078a09c372f17e9b003758ed87e99c2449f4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56647936"
---
# <a name="configure-the-linker-for-blazor"></a>配置 Blazor 链接器

作者：[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Blazor 将在每个版本模式生成期间执行[中间语言 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) 链接，以从输出程序集中删除不必要的 IL。

你可以使用以下任何一种方法控制程序集链接：

* 使用 MSBuild 属性全局禁用链接。
* 使用配置文件在每个程序集基础上控制链接。

## <a name="disable-linking-with-an-msbuild-property"></a>使用 MSBuild 属性禁用链接

在构建应用程序（包括发布）时，默认在发布模式下启用链接。 若要禁用所有程序集链接，请在项目文件中将 `<BlazorLinkOnBuild>` MSBuild 属性设置为 `false`：

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>使用配置文件控制链接

通过提供 XML 配置文件并在项目文件中将该文件指定为 MSBuild 项，可以在每个程序集的基础上控制链接。

以下是使用配置文件示例 (Linker.xml)：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/aspnet/Blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

若要详细了解配置文件的文件格式，请参阅 [IL 链接器：xml 描述符语法](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)。

使用 `BlazorLinkerDescriptor` 项在项目文件中指定配置文件：

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```
