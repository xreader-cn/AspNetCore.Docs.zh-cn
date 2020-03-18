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
ms.openlocfilehash: 263b85a3213c1da233e4c96095faaf39d0a8e13f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648606"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="6f446-103">配置 ASP.NET Core Blazor 链接器</span><span class="sxs-lookup"><span data-stu-id="6f446-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="6f446-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6f446-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6f446-105">Blazor 在生成期间执行[中间语言 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) 链接，以从应用的输出程序集中删除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="6f446-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="6f446-106">使用以下任何一种方法控制程序集链接：</span><span class="sxs-lookup"><span data-stu-id="6f446-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="6f446-107">使用 [MSBuild 属性](#disable-linking-with-a-msbuild-property)全局禁用链接。</span><span class="sxs-lookup"><span data-stu-id="6f446-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="6f446-108">使用[配置文件](#control-linking-with-a-configuration-file)按程序集控制链接。</span><span class="sxs-lookup"><span data-stu-id="6f446-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="6f446-109">使用 MSBuild 属性禁用链接</span><span class="sxs-lookup"><span data-stu-id="6f446-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="6f446-110">在生成应用（包括发布）时，默认启用链接。</span><span class="sxs-lookup"><span data-stu-id="6f446-110">Linking is enabled by default when an app is built, which includes publishing.</span></span> <span data-ttu-id="6f446-111">若要禁用所有程序集链接，请在项目文件中将 `BlazorLinkOnBuild` MSBuild 属性设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="6f446-111">To disable linking for all assemblies, set the `BlazorLinkOnBuild` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="6f446-112">使用配置文件控制链接</span><span class="sxs-lookup"><span data-stu-id="6f446-112">Control linking with a configuration file</span></span>

<span data-ttu-id="6f446-113">通过提供 XML 配置文件并在项目文件中将该文件指定为 MSBuild 项，按程序集控制链接：</span><span class="sxs-lookup"><span data-stu-id="6f446-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="6f446-114">Linker.xml  ：</span><span class="sxs-lookup"><span data-stu-id="6f446-114">*Linker.xml*:</span></span>

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

<span data-ttu-id="6f446-115">有关详细信息，请参阅 [IL Linker：xml 描述符语法](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)。</span><span class="sxs-lookup"><span data-stu-id="6f446-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="6f446-116">配置链接器以实现国际化</span><span class="sxs-lookup"><span data-stu-id="6f446-116">Configure the linker for internationalization</span></span>

<span data-ttu-id="6f446-117">默认情况下，用于 Blazor WebAssembly 应用的 Blazor 链接器配置会去除国际化信息（显式请求的区域设置除外）。</span><span class="sxs-lookup"><span data-stu-id="6f446-117">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="6f446-118">删除这些程序集可最大程度地缩减应用的大小。</span><span class="sxs-lookup"><span data-stu-id="6f446-118">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="6f446-119">要控制保留哪些国际化程序集，请在项目文件中设置 `<MonoLinkerI18NAssemblies>` MSBuild 属性：</span><span class="sxs-lookup"><span data-stu-id="6f446-119">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="6f446-120">区域值</span><span class="sxs-lookup"><span data-stu-id="6f446-120">Region Value</span></span>     | <span data-ttu-id="6f446-121">Mono 区域程序集</span><span class="sxs-lookup"><span data-stu-id="6f446-121">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="6f446-122">包含的所有程序集</span><span class="sxs-lookup"><span data-stu-id="6f446-122">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="6f446-123">I18N.CJK.dll </span><span class="sxs-lookup"><span data-stu-id="6f446-123">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="6f446-124">I18N.MidEast.dll </span><span class="sxs-lookup"><span data-stu-id="6f446-124">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="6f446-125">`none`（默认值）</span><span class="sxs-lookup"><span data-stu-id="6f446-125">`none` (default)</span></span> | <span data-ttu-id="6f446-126">None</span><span class="sxs-lookup"><span data-stu-id="6f446-126">None</span></span>                    |
| `other`          | <span data-ttu-id="6f446-127">I18N.Other.dll </span><span class="sxs-lookup"><span data-stu-id="6f446-127">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="6f446-128">I18N.Rare.dll </span><span class="sxs-lookup"><span data-stu-id="6f446-128">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="6f446-129">I18N.West.dll </span><span class="sxs-lookup"><span data-stu-id="6f446-129">*I18N.West.dll*</span></span>         |

<span data-ttu-id="6f446-130">各个值之间用逗号分隔（例如：`mideast,west`）。</span><span class="sxs-lookup"><span data-stu-id="6f446-130">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="6f446-131">有关详细信息，请参阅[国际化：Pnetlib 国际化框架库（mono/mono GitHub 存储库）](https://github.com/mono/mono/tree/master/mcs/class/I18N)。</span><span class="sxs-lookup"><span data-stu-id="6f446-131">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
