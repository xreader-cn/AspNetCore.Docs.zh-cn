---
title: 配置 ASP.NET Core Blazor 链接器
author: guardrex
description: 了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: b08ec26fb8d139223c57774600bc3cb19a56ac49
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083298"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>配置 ASP.NET Core Blazor 链接器

作者：[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly 在生成期间执行[中间语言 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) 链接，以从应用的输出程序集中剪裁不必要的 IL。 在调试配置中生成时，将禁用链接器。 应用必须在发布配置中生成才能启用链接器。 部署 Blazor WebAssembly 应用时，建议在发布中生成。 

链接应用可以优化大小，但可能会造成不利影响。 使用反射或相关动态功能的应用可能会在剪裁时中断，因为链接器不知道此动态行为，而且通常无法确定在运行时反射所需的类型。 若要剪裁此类应用，必须通知链接器应用所依赖的代码和包或框架中的反射所需的任何类型。 

若要确保剪裁后的应用在部署后正常工作，请务必在开发时经常对应用的发行版本进行测试。

可以使用以下 MSBuild 功能配置 Blazor 应用的链接：

* 使用 [MSBuild 属性](#control-linking-with-an-msbuild-property)全局配置链接。
* 使用[配置文件](#control-linking-with-a-configuration-file)按程序集控制链接。

## <a name="control-linking-with-an-msbuild-property"></a>使用 MSBuild 属性控制链接

在 `Release` 配置中生成应用时，将启用链接。 若要对此进行更改，请在项目文件中配置 `BlazorWebAssemblyEnableLinking` MSBuild 属性：

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
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

默认情况下，用于 Blazor WebAssembly 应用的 Blazor 链接器配置会去除国际化信息（显式请求的区域设置除外）。 删除这些程序集可最大程度地缩减应用的大小。

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
