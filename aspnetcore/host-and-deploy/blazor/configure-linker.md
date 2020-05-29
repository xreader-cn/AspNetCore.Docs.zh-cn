---
<span data-ttu-id="6329a-101">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-103">'Blazor'</span></span>
- <span data-ttu-id="6329a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-104">'Identity'</span></span>
- <span data-ttu-id="6329a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-106">'Razor'</span></span>
- <span data-ttu-id="6329a-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="6329a-108">配置 ASP.NET Core Blazor 链接器</span><span class="sxs-lookup"><span data-stu-id="6329a-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="6329a-109">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6329a-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="6329a-110"> WebAssembly 在生成期间执行[中间语言 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) 链接，以从应用的输出程序集中剪裁不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="6329a-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="6329a-111">在调试配置中生成时，将禁用链接器。</span><span class="sxs-lookup"><span data-stu-id="6329a-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="6329a-112">应用必须在发布配置中生成才能启用链接器。</span><span class="sxs-lookup"><span data-stu-id="6329a-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="6329a-113">部署 Blazor WebAssembly 应用时，建议在发布中生成。</span><span class="sxs-lookup"><span data-stu-id="6329a-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="6329a-114">链接应用可以优化大小，但可能会造成不利影响。</span><span class="sxs-lookup"><span data-stu-id="6329a-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="6329a-115">使用反射或相关动态功能的应用可能会在剪裁时中断，因为链接器不知道此动态行为，而且通常无法确定在运行时反射所需的类型。</span><span class="sxs-lookup"><span data-stu-id="6329a-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="6329a-116">若要剪裁此类应用，必须通知链接器应用所依赖的代码和包或框架中的反射所需的任何类型。</span><span class="sxs-lookup"><span data-stu-id="6329a-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="6329a-117">若要确保剪裁后的应用在部署后正常工作，请务必在开发时经常对应用的发行版本进行测试。</span><span class="sxs-lookup"><span data-stu-id="6329a-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="6329a-118">可以使用以下 MSBuild 功能配置 Blazor 应用的链接：</span><span class="sxs-lookup"><span data-stu-id="6329a-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="6329a-119">使用 [MSBuild 属性](#control-linking-with-an-msbuild-property)全局配置链接。</span><span class="sxs-lookup"><span data-stu-id="6329a-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="6329a-120">使用[配置文件](#control-linking-with-a-configuration-file)按程序集控制链接。</span><span class="sxs-lookup"><span data-stu-id="6329a-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="6329a-121">使用 MSBuild 属性控制链接</span><span class="sxs-lookup"><span data-stu-id="6329a-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="6329a-122">在 `Release` 配置中生成应用时，将启用链接。</span><span class="sxs-lookup"><span data-stu-id="6329a-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="6329a-123">若要对此进行更改，请在项目文件中配置 `BlazorWebAssemblyEnableLinking` MSBuild 属性：</span><span class="sxs-lookup"><span data-stu-id="6329a-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="6329a-124">使用配置文件控制链接</span><span class="sxs-lookup"><span data-stu-id="6329a-124">Control linking with a configuration file</span></span>

<span data-ttu-id="6329a-125">通过提供 XML 配置文件并在项目文件中将该文件指定为 MSBuild 项，按程序集控制链接：</span><span class="sxs-lookup"><span data-stu-id="6329a-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="6329a-126">LinkerConfig.xml：</span><span class="sxs-lookup"><span data-stu-id="6329a-126">*LinkerConfig.xml*:</span></span>

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

<span data-ttu-id="6329a-127">有关详细信息和示例，请参阅[数据格式（mono/链接器 GitHub 存储库）](https://github.com/mono/linker/blob/master/docs/data-formats.md)。</span><span class="sxs-lookup"><span data-stu-id="6329a-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="6329a-128">将 XML 链接器配置文件添加到库</span><span class="sxs-lookup"><span data-stu-id="6329a-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="6329a-129">要针对特定库配置链接器，请将 XML 链接器配置文件作为嵌入的资源添加到库中。</span><span class="sxs-lookup"><span data-stu-id="6329a-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="6329a-130">嵌入的资源必须与程序集同名。</span><span class="sxs-lookup"><span data-stu-id="6329a-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="6329a-131">在以下示例中，LinkerConfig.xml 文件被指定为与库的程序集同名的嵌入资源：</span><span class="sxs-lookup"><span data-stu-id="6329a-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="6329a-132">配置链接器以实现国际化</span><span class="sxs-lookup"><span data-stu-id="6329a-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="6329a-133">默认情况下，Blazor 对于 Blazor WebAssembly 应用的链接器配置会去除国际化信息（显式请求的区域设置除外）。</span><span class="sxs-lookup"><span data-stu-id="6329a-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="6329a-134">删除这些程序集可最大程度地缩减应用的大小。</span><span class="sxs-lookup"><span data-stu-id="6329a-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="6329a-135">要控制保留哪些国际化程序集，请在项目文件中设置 `<BlazorWebAssemblyI18NAssemblies>` MSBuild 属性：</span><span class="sxs-lookup"><span data-stu-id="6329a-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="6329a-136">区域值</span><span class="sxs-lookup"><span data-stu-id="6329a-136">Region Value</span></span>     | <span data-ttu-id="6329a-137">Mono 区域程序集</span><span class="sxs-lookup"><span data-stu-id="6329a-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="6329a-138">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-140">'Blazor'</span></span>
- <span data-ttu-id="6329a-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-141">'Identity'</span></span>
- <span data-ttu-id="6329a-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-143">'Razor'</span></span>
- <span data-ttu-id="6329a-144">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-145">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-147">'Blazor'</span></span>
- <span data-ttu-id="6329a-148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-148">'Identity'</span></span>
- <span data-ttu-id="6329a-149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-150">'Razor'</span></span>
- <span data-ttu-id="6329a-151">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-152">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-154">'Blazor'</span></span>
- <span data-ttu-id="6329a-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-155">'Identity'</span></span>
- <span data-ttu-id="6329a-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-157">'Razor'</span></span>
- <span data-ttu-id="6329a-158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-159">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-161">'Blazor'</span></span>
- <span data-ttu-id="6329a-162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-162">'Identity'</span></span>
- <span data-ttu-id="6329a-163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-164">'Razor'</span></span>
- <span data-ttu-id="6329a-165">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-166">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-168">'Blazor'</span></span>
- <span data-ttu-id="6329a-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-169">'Identity'</span></span>
- <span data-ttu-id="6329a-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-171">'Razor'</span></span>
- <span data-ttu-id="6329a-172">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-173">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-175">'Blazor'</span></span>
- <span data-ttu-id="6329a-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-176">'Identity'</span></span>
- <span data-ttu-id="6329a-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-178">'Razor'</span></span>
- <span data-ttu-id="6329a-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-179">'SignalR' uid:</span></span> 

<span data-ttu-id="6329a-180">-------- | --- title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-182">'Blazor'</span></span>
- <span data-ttu-id="6329a-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-183">'Identity'</span></span>
- <span data-ttu-id="6329a-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-185">'Razor'</span></span>
- <span data-ttu-id="6329a-186">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-187">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-189">'Blazor'</span></span>
- <span data-ttu-id="6329a-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-190">'Identity'</span></span>
- <span data-ttu-id="6329a-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-192">'Razor'</span></span>
- <span data-ttu-id="6329a-193">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-194">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-196">'Blazor'</span></span>
- <span data-ttu-id="6329a-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-197">'Identity'</span></span>
- <span data-ttu-id="6329a-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-199">'Razor'</span></span>
- <span data-ttu-id="6329a-200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-201">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-203">'Blazor'</span></span>
- <span data-ttu-id="6329a-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-204">'Identity'</span></span>
- <span data-ttu-id="6329a-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-206">'Razor'</span></span>
- <span data-ttu-id="6329a-207">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-208">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-210">'Blazor'</span></span>
- <span data-ttu-id="6329a-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-211">'Identity'</span></span>
- <span data-ttu-id="6329a-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-213">'Razor'</span></span>
- <span data-ttu-id="6329a-214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-215">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-217">'Blazor'</span></span>
- <span data-ttu-id="6329a-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-218">'Identity'</span></span>
- <span data-ttu-id="6329a-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-220">'Razor'</span></span>
- <span data-ttu-id="6329a-221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-222">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-224">'Blazor'</span></span>
- <span data-ttu-id="6329a-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-225">'Identity'</span></span>
- <span data-ttu-id="6329a-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-227">'Razor'</span></span>
- <span data-ttu-id="6329a-228">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-229">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-231">'Blazor'</span></span>
- <span data-ttu-id="6329a-232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-232">'Identity'</span></span>
- <span data-ttu-id="6329a-233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-234">'Razor'</span></span>
- <span data-ttu-id="6329a-235">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6329a-236">title:“配置 ASP.NET Core Blazor 链接器”author: description:“了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器。”</span><span class="sxs-lookup"><span data-stu-id="6329a-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="6329a-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6329a-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6329a-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6329a-238">'Blazor'</span></span>
- <span data-ttu-id="6329a-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6329a-239">'Identity'</span></span>
- <span data-ttu-id="6329a-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6329a-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="6329a-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6329a-241">'Razor'</span></span>
- <span data-ttu-id="6329a-242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6329a-242">'SignalR' uid:</span></span> 

<span data-ttu-id="6329a-243">------------ | | `all`            | 包含的所有程序集 | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none`（默认）| 无                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span><span class="sxs-lookup"><span data-stu-id="6329a-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="6329a-244">各个值之间用逗号分隔（例如：`mideast,west`）。</span><span class="sxs-lookup"><span data-stu-id="6329a-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="6329a-245">有关详细信息，请参阅[国际化：Pnetlib 国际化框架库（mono/mono GitHub 存储库）](https://github.com/mono/mono/tree/master/mcs/class/I18N)。</span><span class="sxs-lookup"><span data-stu-id="6329a-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6329a-246">其他资源</span><span class="sxs-lookup"><span data-stu-id="6329a-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
