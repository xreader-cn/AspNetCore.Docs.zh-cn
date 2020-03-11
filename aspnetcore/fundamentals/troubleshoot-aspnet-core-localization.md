---
title: 对 ASP.NET Core 本地化进行故障排除
author: hishamco
description: 了解如何诊断 ASP.NET Core 应用中本地化的问题。
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647892"
---
# <a name="troubleshoot-aspnet-core-localization"></a>对 ASP.NET Core 本地化进行故障排除

作者：[Hisham Bin Ateya](https://github.com/hishamco)

本文说明了如何诊断 ASP.NET Core 应用本地化问题。

## <a name="localization-configuration-issues"></a>本地化配置问题

**本地化中间件顺序**  
由于本地化中间件未按预期方式排序，因此应用可能未本地化。

要解决此问题，请确保在 MVC 中间件之前注册本地化中间件。 否则，不会应用本地化中间件。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**找不到本地化资源路径**

**RequestCultureProvider 中受支持的区域性与注册的区域性不匹配**  

## <a name="resource-file-naming-issues"></a>资源文件命名问题

ASP.NET Core 具有针对本地化资源文件命名的预定义规则和指导，[此处](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)是其详细描述。

## <a name="missing-resources"></a>缺少资源

找不到资源的常见原因包括：

- `resx` 文件或本地化工具请求中资源名称拼写错误。
- `resx` 中缺少某些语言的资源，但存在其他语言的资源。
- 如果仍有问题，请检查本地化日志消息（在 `Debug` 日志级别），了解有关缺少资源的详细信息。

_**提示：** 使用 `CookieRequestCultureProvider` 时，验证未对本地化 cookie 值内的区域性使用单引号。例如，`c='en-UK'|uic='en-US'` 是无效的 cookie 值，而 `c=en-UK|uic=en-US` 有效。_

## <a name="resources--class-libraries-issues"></a>资源和类库问题

默认情况下，ASP.NET Core 提供一种方法，允许类库通过 [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1) 查找其资源文件。

类库的常见问题包括：
- 类库中缺少 `ResourceLocationAttribute` 会使 `ResourceManagerStringLocalizerFactory` 无法发现资源。
- 资源文件命名。 有关详细信息，请参阅[资源文件命名](#resource-file-naming-issues)部分。
- 更改类库的根命名空间。 有关详细信息，请参阅[根命名空间问题](#root-namespace-issues)部分。

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider 未按预期工作

`RequestLocalizationOptions` 类具有三个默认提供程序：

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) 可用于自定义在应用中提供本地化区域性的方式。 在默认提供程序无法满足需求时，可使用 `CustomRequestCultureProvider`。

- 自定义提供程序未正常工作的一个常见原因是它不是 `RequestCultureProviders` 列表中的第一个提供程序。 若要解决此问题，请执行以下操作：

- 在 `RequestCultureProviders` 列表的位置 0 处插入自定义提供程序，如下所示：

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

- 使用 `AddInitialRequestCultureProvider` 扩展方法将自定义提供程序设置为初始提供程序。

## <a name="root-namespace-issues"></a>根命名空间问题

如果程序集的根命名空间不同于程序集名称，则默认情况下本地化无法工作。 若要避免此问题，请使用 [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)，[此处](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)是其详细描述

> [!WARNING]
> 当项目名称不是有效的 .NET 标识符时，可能会发生这种情况。 例如，`my-project-name.csproj` 将使用根命名空间 `my_project_name` 和导致此错误的程序集名称 `my-project-name`。 

## <a name="resources--build-action"></a>设置和生成操作

如果使用资源文件进行本地化，则该资源文件必须具有相应的生成操作。 它们应是“嵌入的资源”  ，否则 `ResourceStringLocalizer` 找不到这些资源。
