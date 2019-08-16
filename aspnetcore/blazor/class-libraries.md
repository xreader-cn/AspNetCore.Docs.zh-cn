---
title: ASP.NET Core Razor 组件类库
author: guardrex
description: 了解如何在 Blazor 应用程序中将组件包含在外部组件库中。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/class-libraries
ms.openlocfilehash: b5857f2cf22bde801deeeaf227817fdf99862f4a
ms.sourcegitcommit: 4cb0c7e74355f2e87c60e2a196f842b937247a99
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69545781"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core Razor 组件类库

作者: [Simon Timms](https://github.com/stimms)

可在[Razor 类库 (RCL)](xref:razor-pages/ui-class)中跨项目共享组件。 *Razor 组件类库*可以包含在其中:

* 解决方案中的另一个项目。
* NuGet 包。
* 引用的 .NET 库。

正如组件是常规 .NET 类型一样, RCL 提供的组件是普通的 .NET 程序集。

## <a name="create-an-rcl"></a>创建 RCL

请按照<xref:blazor/get-started>文章中的指导配置 Blazor 的环境。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 创建新项目。
1. 选择 " **Razor 类库**"。 选择“下一步”。
1. 在 "**创建新的 Razor 类库**" 对话框中, 选择 "**创建**"。
1. 在“项目名称”字段提供项目名称，或接受默认项目名称。 本主题中的示例使用项目名称`MyComponentLib1`。 选择“创建”。
1. 将 RCL 添加到解决方案:
   1. 右键单击解决方案。 选择 "**添加** > **现有项目**"。
   1. 导航到 RCL 的项目文件。
   1. 选择 RCL 的项目文件 ( *.csproj*)。
1. 在应用中添加对 RCL 的引用:
   1. 右键单击应用程序项目。 选择 "**添加** > **引用**"。
   1. 选择 RCL 项目。 选择“确定”。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. 在命令行界面中, 将`razorclasslib` **Razor 类库**模板 () 与[dotnet new](/dotnet/core/tools/dotnet-new)命令一起使用。 在下面的示例中, 创建了一个名`MyComponentLib1`为的 RCL。 执行命令时, `MyComponentLib1`将自动创建包含的文件夹:

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. 若要将库添加到现有项目, 请在命令行界面中使用[dotnet add reference](/dotnet/core/tools/dotnet-add-reference)命令。 在下面的示例中, 将 RCL 添加到应用中。 在应用程序的项目文件夹中执行以下命令, 并在其中包含库的路径:

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>使用库组件

若要使用另一个项目的库中定义的组件, 请使用以下方法之一:

* 使用带有命名空间的完整类型名称。
* 使用 Razor 的[ \@using](xref:mvc/views/razor#using)指令。 可以按名称添加单个组件。

在下面的示例中`MyComponentLib1` , 是`SalesReport`包含组件的组件库。

可以`SalesReport`使用命名空间的完整类型名称引用该组件:

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

如果库与`@using`指令一起使用, 也可以引用组件:

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

在顶级 *_Import*文件中包含指令,使库的组件可用于整个项目。`@using MyComponentLib1` 将指令添加到任何级别的 *_Import*文件, 以将命名空间应用于文件夹中的单个页面或一组页面。

## <a name="build-pack-and-ship-to-nuget"></a>生成、打包和传送到 NuGet

由于组件库是标准的 .NET 库, 因此将其打包并传送到 NuGet 与将任何库打包到 NuGet 没有什么不同。 在命令行界面中使用[dotnet pack](/dotnet/core/tools/dotnet-pack)命令执行打包:

```console
dotnet pack
```

在命令行界面中使用[dotnet NuGet publish](/dotnet/core/tools/dotnet-nuget-push)命令将包上传到 nuget:

```console
dotnet nuget publish
```

## <a name="create-a-razor-components-class-library-with-static-assets"></a>使用静态资产创建 Razor 组件类库

RCL 可以包括静态资产。 静态资产可用于使用库的任何应用。 有关详细信息，请参阅 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets> 。

## <a name="additional-resources"></a>其他资源

* <xref:razor-pages/ui-class>
