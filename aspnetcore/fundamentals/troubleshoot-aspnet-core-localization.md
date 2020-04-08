---
title: 对 ASP.NET Core 本地化进行故障排除
author: hishamco
description: 了解如何诊断 ASP.NET Core 应用中本地化的问题。
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647892"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="db4f5-103">对 ASP.NET Core 本地化进行故障排除</span><span class="sxs-lookup"><span data-stu-id="db4f5-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="db4f5-104">作者：[Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="db4f5-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="db4f5-105">本文说明了如何诊断 ASP.NET Core 应用本地化问题。</span><span class="sxs-lookup"><span data-stu-id="db4f5-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="db4f5-106">本地化配置问题</span><span class="sxs-lookup"><span data-stu-id="db4f5-106">Localization configuration issues</span></span>

<span data-ttu-id="db4f5-107">**本地化中间件顺序**</span><span class="sxs-lookup"><span data-stu-id="db4f5-107">**Localization middleware order**</span></span>  
<span data-ttu-id="db4f5-108">由于本地化中间件未按预期方式排序，因此应用可能未本地化。</span><span class="sxs-lookup"><span data-stu-id="db4f5-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="db4f5-109">要解决此问题，请确保在 MVC 中间件之前注册本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="db4f5-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="db4f5-110">否则，不会应用本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="db4f5-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="db4f5-111">**找不到本地化资源路径**</span><span class="sxs-lookup"><span data-stu-id="db4f5-111">**Localization resources path not found**</span></span>

<span data-ttu-id="db4f5-112">**RequestCultureProvider 中受支持的区域性与注册的区域性不匹配**</span><span class="sxs-lookup"><span data-stu-id="db4f5-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="db4f5-113">资源文件命名问题</span><span class="sxs-lookup"><span data-stu-id="db4f5-113">Resource file naming issues</span></span>

<span data-ttu-id="db4f5-114">ASP.NET Core 具有针对本地化资源文件命名的预定义规则和指导，[此处](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)是其详细描述。</span><span class="sxs-lookup"><span data-stu-id="db4f5-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="db4f5-115">缺少资源</span><span class="sxs-lookup"><span data-stu-id="db4f5-115">Missing resources</span></span>

<span data-ttu-id="db4f5-116">找不到资源的常见原因包括：</span><span class="sxs-lookup"><span data-stu-id="db4f5-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="db4f5-117">`resx` 文件或本地化工具请求中资源名称拼写错误。</span><span class="sxs-lookup"><span data-stu-id="db4f5-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="db4f5-118">`resx` 中缺少某些语言的资源，但存在其他语言的资源。</span><span class="sxs-lookup"><span data-stu-id="db4f5-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="db4f5-119">如果仍有问题，请检查本地化日志消息（在 `Debug` 日志级别），了解有关缺少资源的详细信息。</span><span class="sxs-lookup"><span data-stu-id="db4f5-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="db4f5-120">_**提示：** 使用 `CookieRequestCultureProvider` 时，验证未对本地化 cookie 值内的区域性使用单引号。例如，`c='en-UK'|uic='en-US'` 是无效的 cookie 值，而 `c=en-UK|uic=en-US` 有效。_</span><span class="sxs-lookup"><span data-stu-id="db4f5-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="db4f5-121">资源和类库问题</span><span class="sxs-lookup"><span data-stu-id="db4f5-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="db4f5-122">默认情况下，ASP.NET Core 提供一种方法，允许类库通过 [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1) 查找其资源文件。</span><span class="sxs-lookup"><span data-stu-id="db4f5-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="db4f5-123">类库的常见问题包括：</span><span class="sxs-lookup"><span data-stu-id="db4f5-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="db4f5-124">类库中缺少 `ResourceLocationAttribute` 会使 `ResourceManagerStringLocalizerFactory` 无法发现资源。</span><span class="sxs-lookup"><span data-stu-id="db4f5-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="db4f5-125">资源文件命名。</span><span class="sxs-lookup"><span data-stu-id="db4f5-125">Resource file naming.</span></span> <span data-ttu-id="db4f5-126">有关详细信息，请参阅[资源文件命名](#resource-file-naming-issues)部分。</span><span class="sxs-lookup"><span data-stu-id="db4f5-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="db4f5-127">更改类库的根命名空间。</span><span class="sxs-lookup"><span data-stu-id="db4f5-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="db4f5-128">有关详细信息，请参阅[根命名空间问题](#root-namespace-issues)部分。</span><span class="sxs-lookup"><span data-stu-id="db4f5-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="db4f5-129">CustomRequestCultureProvider 未按预期工作</span><span class="sxs-lookup"><span data-stu-id="db4f5-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="db4f5-130">`RequestLocalizationOptions` 类具有三个默认提供程序：</span><span class="sxs-lookup"><span data-stu-id="db4f5-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="db4f5-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) 可用于自定义在应用中提供本地化区域性的方式。</span><span class="sxs-lookup"><span data-stu-id="db4f5-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="db4f5-132">在默认提供程序无法满足需求时，可使用 `CustomRequestCultureProvider`。</span><span class="sxs-lookup"><span data-stu-id="db4f5-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="db4f5-133">自定义提供程序未正常工作的一个常见原因是它不是 `RequestCultureProviders` 列表中的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="db4f5-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="db4f5-134">若要解决此问题，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="db4f5-134">To resolve this issue:</span></span>

- <span data-ttu-id="db4f5-135">在 `RequestCultureProviders` 列表的位置 0 处插入自定义提供程序，如下所示：</span><span class="sxs-lookup"><span data-stu-id="db4f5-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- <span data-ttu-id="db4f5-136">使用 `AddInitialRequestCultureProvider` 扩展方法将自定义提供程序设置为初始提供程序。</span><span class="sxs-lookup"><span data-stu-id="db4f5-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="db4f5-137">根命名空间问题</span><span class="sxs-lookup"><span data-stu-id="db4f5-137">Root Namespace issues</span></span>

<span data-ttu-id="db4f5-138">如果程序集的根命名空间不同于程序集名称，则默认情况下本地化无法工作。</span><span class="sxs-lookup"><span data-stu-id="db4f5-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="db4f5-139">若要避免此问题，请使用 [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)，[此处](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)是其详细描述</span><span class="sxs-lookup"><span data-stu-id="db4f5-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

> [!WARNING]
> <span data-ttu-id="db4f5-140">当项目名称不是有效的 .NET 标识符时，可能会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="db4f5-140">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="db4f5-141">例如，`my-project-name.csproj` 将使用根命名空间 `my_project_name` 和导致此错误的程序集名称 `my-project-name`。</span><span class="sxs-lookup"><span data-stu-id="db4f5-141">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

## <a name="resources--build-action"></a><span data-ttu-id="db4f5-142">设置和生成操作</span><span class="sxs-lookup"><span data-stu-id="db4f5-142">Resources & Build Action</span></span>

<span data-ttu-id="db4f5-143">如果使用资源文件进行本地化，则该资源文件必须具有相应的生成操作。</span><span class="sxs-lookup"><span data-stu-id="db4f5-143">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="db4f5-144">它们应是“嵌入的资源”  ，否则 `ResourceStringLocalizer` 找不到这些资源。</span><span class="sxs-lookup"><span data-stu-id="db4f5-144">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
