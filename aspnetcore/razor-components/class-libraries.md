---
title: Razor 组件类库
author: guardrex
description: 发现如何组件可以包含在外部组件库中的 Razor 组件应用程序。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/14/2019
uid: razor-components/class-libraries
ms.openlocfilehash: 1064ad60d90af15af483ba9bca5ed85fb63c2924
ms.sourcegitcommit: 10e14b85490f064395e9b2f423d21e3c2d39ed8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "59515353"
---
# <a name="razor-components-class-libraries"></a>Razor 组件类库

通过[Simon Timms](https://github.com/stimms)

跨项目，可以在 Razor 类库中共享组件。 组件可以从包含：

* 在解决方案中的另一个项目。
* NuGet 包。
* 引用的.NET 库。

正如组件是常规的.NET 类型，由 Razor 类库提供的组件是普通的.NET 程序集。

使用`razorclasslib`与 （Razor 类库） 模板[dotnet 新](/dotnet/core/tools/dotnet-new)命令：

```console
dotnet new razorclasslib -o MyComponentLib1
```

添加 Razor 组件文件 (*.razor*) 对 Razor 类库。

若要将库添加到现有项目，请使用[dotnet sln](/dotnet/core/tools/dotnet-sln)命令：

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```console
dotnet sln add .\MyComponentLib1
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```console
dotnet add WebApplication1 reference MyComponentLib1
```

---

> [!NOTE]
> Razor 类库不兼容 ASP.NET Core 预览版 3 中的 Blazor 应用。
>
> 若要创建可与 Blazor 和 Razor 组件应用程序共享的库组件，使用创建的 Blazor 类库`blazorlib`模板。
>
> Razor 类库不支持在 ASP.NET Core 预览版 3 中的静态资产。 使用的组件库`blazorlib`模板可以包括静态文件，如图像、 JavaScript 和样式表。 在生成时，静态文件嵌入到生成的程序集文件 (*.dll*)，而无需担心如何包含其资源允许使用的组件。 中包括的任何文件`content`目录标记为嵌入的资源。

## <a name="consume-a-library-component"></a>使用的库组件

为了使用在另一个项目中，库中定义的组件[ @addTagHelper ](xref:mvc/views/tag-helpers/intro#add-helper-label)必须使用指令。 单个组件可能添加的名称。

指令的常规格式为：

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

例如，添加以下指令`Component1`的`MyComponentLib1`:

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1
```

但是，很常见，以包含所有中使用通配符的程序集的组件 (`*`):

```cshtml
@addTagHelper *, MyComponentLib1
```

`@addTagHelper`指令将其纳入 *_ViewImport.cshtml*若要使组件可用于整个项目或已应用到单个页面或组的文件夹中的页面。 使用`@addTagHelper`指令后，组件库的组件可以使用像与应用位于同一程序集中。

## <a name="build-pack-and-ship-to-nuget"></a>生成、 包和发送到 NuGet

由于组件库是标准.NET 库，打包并传送到 NuGet 没有任何差别打包和传送到 NuGet 的任何库。 使用执行打包[dotnet pack](/dotnet/core/tools/dotnet-pack)命令：

```console
dotnet pack
```

将包上载到使用 NuGet [dotnet nuget 发布](/dotnet/core/tools/dotnet-nuget-push)命令：

```console
dotnet nuget publish
```

当使用`blazorlib`模板，NuGet 包中包含静态资源。 库的使用者自动接收脚本和样式表，因此使用者无需手动安装的资源。
