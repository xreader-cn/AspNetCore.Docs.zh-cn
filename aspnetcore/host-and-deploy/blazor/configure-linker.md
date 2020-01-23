---
title: 配置 ASP.NET Core Blazor 链接器
author: guardrex
description: 了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: cdcd62915b8f1bae26773ed91e55973527e158f6
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160270"
---
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a>配置 ASP.NET Core Blazor 链接器

作者：[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 在生成期间执行[中间语言 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) 链接以从应用的输出程序集中删除不必要的 IL。

使用以下任何一种方法控制程序集链接：

* 使用 [MSBuild 属性](#disable-linking-with-a-msbuild-property)全局禁用链接。
* 使用[配置文件](#control-linking-with-a-configuration-file)按程序集控制链接。

## <a name="disable-linking-with-a-msbuild-property"></a>使用 MSBuild 属性禁用链接

在生成应用（包括发布）时，默认启用链接。 若要禁用所有程序集链接，请在项目文件中将 `BlazorLinkOnBuild` MSBuild 属性设置为 `false`：

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>使用配置文件控制链接

通过提供 XML 配置文件并在项目文件中将该文件指定为 MSBuild 项，按程序集控制链接：

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

Linker.xml  ：

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
      Fixes: https://github.com/dotnet/blazor/issues/239
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

有关详细信息，请参阅 [IL Linker：xml 描述符语法](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)。

### <a name="configure-the-linker-for-internationalization"></a>配置链接器以实现国际化

默认情况下，Blazor 对于 Blazor WebAssembly 应用的链接器配置会去除国际化信息（显式请求的区域设置除外）。 删除这些程序集可最大程度地缩减应用的大小。

要控制保留哪些国际化程序集，请在项目文件中设置 `<MonoLinkerI18NAssemblies>` MSBuild 属性：

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| 区域值     | Mono 区域程序集    |
| ---------------- | ----------------------- |
| `all`            | 包含的所有程序集 |
| `cjk`            | I18N.CJK.dll           |
| `mideast`        | I18N.MidEast.dll       |
| `none`（默认值） | None                    |
| `other`          | I18N.Other.dll         |
| `rare`           | I18N.Rare.dll          |
| `west`           | I18N.West.dll          |

各个值之间用逗号分隔（例如：`mideast,west`）。

有关详细信息，请参阅[国际化：Pnetlib 国际化框架库（mono/mono GitHub 存储库）](https://github.com/mono/mono/tree/master/mcs/class/I18N)。
