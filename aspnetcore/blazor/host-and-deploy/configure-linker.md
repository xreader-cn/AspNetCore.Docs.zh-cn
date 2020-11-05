---
title: '配置 ASP.NET Core :::no-loc(Blazor)::: 链接器'
author: guardrex
description: '了解在构建 :::no-loc(Blazor)::: 应用时如何控制中间语言 (IL) 链接器。'
monikerRange: '>= aspnetcore-3.1 < aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: 0c99056053356133e901d6cf468fec8034dfb845
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055823"
---
# <a name="configure-the-linker-for-aspnet-core-no-locblazor"></a><span data-ttu-id="0c8bd-103">配置 ASP.NET Core :::no-loc(Blazor)::: 链接器</span><span class="sxs-lookup"><span data-stu-id="0c8bd-103">Configure the Linker for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="0c8bd-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0c8bd-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0c8bd-105">:::no-loc(Blazor WebAssembly)::: 在生成期间执行[中间语言 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) 链接，以从应用的输出程序集中剪裁不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-105">:::no-loc(Blazor WebAssembly)::: performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="0c8bd-106">在调试配置中生成时，将禁用链接器。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="0c8bd-107">应用必须在发布配置中生成才能启用链接器。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="0c8bd-108">部署 :::no-loc(Blazor WebAssembly)::: 应用时，建议在发布中生成。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-108">We recommend building in Release when deploying your :::no-loc(Blazor WebAssembly)::: apps.</span></span> 

<span data-ttu-id="0c8bd-109">链接应用可以优化大小，但可能会造成不利影响。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="0c8bd-110">使用反射或相关动态功能的应用可能会在剪裁时中断，因为链接器不知道此动态行为，而且通常无法确定在运行时反射所需的类型。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="0c8bd-111">若要剪裁此类应用，必须通知链接器应用所依赖的代码和包或框架中的反射所需的任何类型。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="0c8bd-112">若要确保剪裁后的应用在部署后正常工作，请务必在开发时经常对应用的发行版本进行测试。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="0c8bd-113">可以使用以下 MSBuild 功能配置 :::no-loc(Blazor)::: 应用的链接：</span><span class="sxs-lookup"><span data-stu-id="0c8bd-113">Linking for :::no-loc(Blazor)::: apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="0c8bd-114">使用 [MSBuild 属性](#control-linking-with-an-msbuild-property)全局配置链接。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="0c8bd-115">使用[配置文件](#control-linking-with-a-configuration-file)按程序集控制链接。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="0c8bd-116">使用 MSBuild 属性控制链接</span><span class="sxs-lookup"><span data-stu-id="0c8bd-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="0c8bd-117">在 `Release` 配置中生成应用时，将启用链接。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="0c8bd-118">若要对此进行更改，请在项目文件中配置 `:::no-loc(Blazor):::WebAssemblyEnableLinking` MSBuild 属性：</span><span class="sxs-lookup"><span data-stu-id="0c8bd-118">To change this, configure the `:::no-loc(Blazor):::WebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyEnableLinking>false</:::no-loc(Blazor):::WebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="0c8bd-119">使用配置文件控制链接</span><span class="sxs-lookup"><span data-stu-id="0c8bd-119">Control linking with a configuration file</span></span>

<span data-ttu-id="0c8bd-120">通过提供 XML 配置文件并在项目文件中将该文件指定为 MSBuild 项，按程序集控制链接：</span><span class="sxs-lookup"><span data-stu-id="0c8bd-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <:::no-loc(Blazor):::LinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="0c8bd-121">`LinkerConfig.xml`：</span><span class="sxs-lookup"><span data-stu-id="0c8bd-121">`LinkerConfig.xml`:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or :::no-loc(Blazor)::: packages must not be
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
  <assembly fullname="MyCool:::no-loc(Blazor):::App" />
</linker>
```

<span data-ttu-id="0c8bd-122">有关详细信息和示例，请参阅[数据格式（mono/链接器 GitHub 存储库）](https://github.com/mono/linker/blob/master/docs/data-formats.md)。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-122">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="0c8bd-123">将 XML 链接器配置文件添加到库</span><span class="sxs-lookup"><span data-stu-id="0c8bd-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="0c8bd-124">要针对特定库配置链接器，请将 XML 链接器配置文件作为嵌入的资源添加到库中。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="0c8bd-125">嵌入的资源必须与程序集同名。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="0c8bd-126">在以下示例中，`LinkerConfig.xml` 文件被指定为与库的程序集同名的嵌入资源：</span><span class="sxs-lookup"><span data-stu-id="0c8bd-126">In the following example, the `LinkerConfig.xml` file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="0c8bd-127">配置链接器以实现国际化</span><span class="sxs-lookup"><span data-stu-id="0c8bd-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="0c8bd-128">默认情况下，:::no-loc(Blazor)::: 对于 :::no-loc(Blazor WebAssembly)::: 应用的链接器配置会去除国际化信息（显式请求的区域设置除外）。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-128">By default, :::no-loc(Blazor):::'s linker configuration for :::no-loc(Blazor WebAssembly)::: apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="0c8bd-129">删除这些程序集可最大程度地缩减应用的大小。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="0c8bd-130">要控制保留哪些国际化程序集，请在项目文件中设置 `<:::no-loc(Blazor):::WebAssemblyI18NAssemblies>` MSBuild 属性：</span><span class="sxs-lookup"><span data-stu-id="0c8bd-130">To control which I18N assemblies are retained, set the `<:::no-loc(Blazor):::WebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</:::no-loc(Blazor):::WebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="0c8bd-131">区域值</span><span class="sxs-lookup"><span data-stu-id="0c8bd-131">Region Value</span></span>     | <span data-ttu-id="0c8bd-132">Mono 区域程序集</span><span class="sxs-lookup"><span data-stu-id="0c8bd-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="0c8bd-133">包含的所有程序集</span><span class="sxs-lookup"><span data-stu-id="0c8bd-133">All assemblies included</span></span> |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| <span data-ttu-id="0c8bd-134">`none`（默认值）</span><span class="sxs-lookup"><span data-stu-id="0c8bd-134">`none` (default)</span></span> | <span data-ttu-id="0c8bd-135">None</span><span class="sxs-lookup"><span data-stu-id="0c8bd-135">None</span></span>                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

<span data-ttu-id="0c8bd-136">各个值之间用逗号分隔（例如：`mideast,west`）。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-136">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="0c8bd-137">有关详细信息，请参阅[国际化：Pnetlib 国际化框架库（mono/mono GitHub 存储库）](https://github.com/mono/mono/tree/master/mcs/class/I18N)。</span><span class="sxs-lookup"><span data-stu-id="0c8bd-137">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0c8bd-138">其他资源</span><span class="sxs-lookup"><span data-stu-id="0c8bd-138">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
