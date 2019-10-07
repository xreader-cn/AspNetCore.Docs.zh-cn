---
title: 使用 OpenAPI 开发 ASP.NET Core 应用
author: ryanbrandenburg
description: 演示如何使用“Microsoft.dotnet-openapi”工具添加 OpenAPI 文件引用。
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: f5eae9e871bc8efc30d500769adb845ff244a90c
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317774"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>使用 OpenAPI 工具开发 ASP.NET Core 应用

作者：Ryan Brandenburg

[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) 是用于管理项目内 [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 引用的 [.NET Core 全局工具](/dotnet/core/tools/global-tools)。

## <a name="installation"></a>安装

若要安装 `Microsoft.dotnet-openapi`，请运行以下命令：

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>添加

使用本页上的任意一个命令添加 OpenAPI 引用，将向 .csproj 文件添加如下所示的 `<OpenApiReference />` 元素  ：

```xml
<OpenApiReference Include="openapi.json" />
```

必须有上述引用，应用才可以调用生成的客户端代码。

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -v|--verbose | Show verbose output. |dotnet openapi add project *-v* ../Ref/ProjRef.csproj |
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>添加文件

#### <a name="options"></a>选项

| 短选项| 长选项| 说明 | 示例 |
|-------|------|-------|---------|
| -v|--verbose | 显示详细输出。 |dotnet openapi add file -v  .\OpenAPI.json |
| -p|--updateProject | 要操作的项目。 |dotnet openapi add file --updateProject .\Ref.csproj  .\OpenAPI.json |
| -c|--code-generator| 应用于引用的代码生成器。 选项包括 `NSwagCSharp` 和 `NSwagTypeScript`。 如果未指定 `--code-generator`，则工具将默认为 `NSwagCSharp`。|dotnet openapi add file .\OpenApi.json --code-generator
| -h|--help|显示帮助信息|dotnet openapi add file --help|

#### <a name="arguments"></a>自变量

|  参数  | 说明 | 示例 |
|-------------|-------------|---------|
| source-file | 要创建的引用的源。 必须为 OpenAPI 文件。 |dotnet openapi add file .\OpenAPI.json  |

### <a name="add-url"></a>添加 URL

#### <a name="options"></a>选项

| 短选项| 长选项| 说明 | 示例 |
|-------|------|-------------|---------|
| -v|--verbose | 显示详细输出。 |dotnet openapi add url -v  `https://contoso.com/openapi.json` |
| -p|--updateProject | 要操作的项目。 |dotnet openapi add url --updateProject .\Ref.csproj  `https://contoso.com/openapi.json` |
| -o|--output-file | 用于放置 OpenAPI 文件本地副本的位置。 |dotnet openapi add url `https://contoso.com/openapi.json` --output-file myclient.json  |
| -c|--code-generator| 应用于引用的代码生成器。 选项包括 `NSwagCSharp` 和 `NSwagTypeScript`。 |dotnet openapi add file .\OpenApi.json --code-generator
| -h|--help|显示帮助信息|dotnet openapi add url --help|

#### <a name="arguments"></a>自变量

|  参数  | 说明 | 示例 |
|-------------|-------------|---------|
| source-URL | 要创建的引用的源。 必须是 URL。 |dotnet openapi add url `https://contoso.com/openapi.json` |

## <a name="remove"></a>删除

删除与 .csproj 文件中给定文件名匹配的 OpenAPI 引用。  删除 OpenAPI 引用后，将不会生成客户端。 将删除本地 .json 和 .yaml 文件。  

### <a name="options"></a>选项

| 短选项| 长选项| 说明| 示例 |
|-------|------|------------|---------|
| -v|--verbose | 显示详细输出。 |dotnet openapi remove -v |
| -p|--updateProject | 要操作的项目。 |dotnet openapi remove --updateProject .\Ref.csproj  .\OpenAPI.json |
| -h|--help|显示帮助信息|dotnet openapi remove --help|

### <a name="arguments"></a>自变量

|  参数  | 说明| 示例 |
| ------------|------------|---------|
| source-file | 要删除的引用的源。 |dotnet openapi remove .\OpenAPI.json  |

## <a name="refresh"></a>刷新

使用下载 URL 中的最新内容刷新已下载的文件本地版本。

### <a name="options"></a>选项

| 短选项| 长选项| 说明 | 示例 |
|-------|------|-------------|---------|
| -v|--verbose | 显示详细输出。 | dotnet openapi refresh -v  `https://contoso.com/openapi.json` |
| -p|--updateProject | 要操作的项目。 | dotnet openapi refresh --updateProject .\Ref.csproj  `https://contoso.com/openapi.json` |
| -h|--help|显示帮助信息|dotnet openapi refresh --help|

### <a name="arguments"></a>自变量

|  参数  | 说明 | 示例 |
| ------------|-------------|---------|
| source-URL | 用于刷新引用的 URL。 | dotnet openapi refresh `https://contoso.com/openapi.json` |
