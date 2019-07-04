---
title: 使用 Web API 分析器
author: pranavkm
description: 了解 Microsoft.AspNetCore.Mvc.Api.Analyzers 中的 Web API 分析器。
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 12/14/2018
uid: web-api/advanced/analyzers
ms.openlocfilehash: 2aaef738ab2a64f85cb85708f63d2375c04cacb5
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538569"
---
# <a name="use-web-api-analyzers"></a>使用 Web API 分析器

ASP.NET Core 2.2 和更高版本包含 [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet 包，其中包含适用于 Web API 的分析器。 分析器使用带有 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> 批注的控制器，同时构建 [API 约定](xref:web-api/advanced/conventions)。

## <a name="package-installation"></a>包安装

可以使用以下方法之一添加 `Microsoft.AspNetCore.Mvc.Api.Analyzers`：

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从“程序包管理器控制台”  窗口：
  * 转到“视图” > “其他窗口” > “包管理器控制台”    。
  * 导航到 ApiConventions.csproj 文件所在的目录  。
  * 请执行以下命令：

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

* 从“管理 NuGet 程序包”  对话框中：
  * 右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目   。
  * 将“包源”设置为“nuget.org”  。
  * 在搜索框中输入“Microsoft.AspNetCore.Mvc.Api.Analyzers”。
  * 从“浏览”选项卡中选择“Microsoft.AspNetCore.Mvc.Api.Analyzers”包，然后单击“安装”   。

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 右键单击“Solution Pad” > “添加包...”中的“包”文件夹    。
* 将“添加包”窗口的“源”下拉列表设置为“nuget.org”   。
* 在搜索框中输入“Microsoft.AspNetCore.Mvc.Api.Analyzers”。
* 从结果窗格中选择“Microsoft.AspNetCore.Mvc.Api.Analyzers”包，然后单击“添加包”  。

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

从“集成终端”  中运行以下命令：

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

运行下面的命令：

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

## <a name="analyzers-for-api-conventions"></a>API 约定的分析器

OpenAPI 文档包含操作可能返回的状态代码和响应类型。 在 ASP.NET Core MVC 中，<xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> 和 <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> 等属性用于记录操作。 <xref:tutorials/web-api-help-pages-using-swagger> 进一步介绍有关记录 API 的详细信息。

包中的其中一个分析器检查使用 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> 进行批注的控制器，并标识不完全记录其响应的操作。 请看下面的示例：

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=9)]

上述操作记录了 HTTP 200 成功返回类型，但未记录 HTTP 404 失败状态代码。 分析器将 HTTP 404 状态代码的缺失文档报告为警告。 提供了修复此问题的选项。

![报告警告的分析器](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>其他资源

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
