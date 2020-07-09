---
title: ASP.NET Core Razor 组件类库
author: guardrex
description: 了解如何从外部组件库将组件包含在 Blazor 应用中。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: b172059407f9a08dacc0fadd804864c7aee7fb90
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944494"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core Razor 组件类库

作者：[Simon Timms](https://github.com/stimms)

组件可在 [Razor 类库 (RCL)](xref:razor-pages/ui-class) 中跨项目共享。 Razor 组件类库可以包含在以下各项中：

* 解决方案中的另一个项目。
* NuGet 程序包。
* 引用的 .NET 库。

正如组件是常规的 .NET 类型一样，RCL 提供的组件也是普通的 .NET 程序集。

## <a name="create-an-rcl"></a>创建 RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 创建新项目。
1. 选择“Razor 类库”。 选择“下一步”。
1. 在“创建新的 Razor 类库”对话框中，选择“创建” 。
1. 在“项目名称”字段提供项目名称，或接受默认项目名称。 本主题中的示例使用项目名称 `MyComponentLib1`。 选择“创建”。
1. 将 RCL 添加到一个解决方案：
   1. 右键单击该解决方案。 选择“添加” > “现有项目” 。
   1. 导航到 RCL 的项目文件。
   1. 选择 RCL 的项目文件 (`.csproj`)。
1. 从应用中添加 RCL 的引用：
   1. 右键单击该应用项目。 选择“添加” > “引用” 。
   1. 选择 RCL 项目。 选择“确定”。

> [!NOTE]
> 如果在从模板生成 RCL 时选中了“支持页面和视图”复选框，还请通过以下内容，将 `_Imports.razor` 文件添加到所生成项目的根目录中，以启用 Razor 组件创作：
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> 手动将该文件添加到所生成项目的根目录中。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. 在命令行界面中通过 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令使用 Razor 类库模板 (`razorclasslib`)。 下面的示例创建了名为 `MyComponentLib1` 的 RCL。 执行命令时，自动创建包含 `MyComponentLib1` 的文件夹：

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > 如果在从模板生成 RCL 时使用 `-s|--support-pages-and-views` 开关，还请通过以下内容将 `_Imports.razor` 文件添加到所生成项目的根目录中，以启用 Razor 组件创作：
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > 手动将该文件添加到所生成项目的根目录中。

1. 若要将库添加到现有项目中，请在命令行界面中使用 [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) 命令。 在下面的示例中，将 RCL 添加到应用中。 使用指向库的路径从应用的项目文件夹执行以下命令：

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>使用库组件

若要使用另一项目的库中定义的组件，请使用以下任一方法：

* 使用命名空间的完整类型名称。
* 使用 Razor 的 [`@using`](xref:mvc/views/razor#using) 指令。 单个组件可以按名称添加。

在下面的示例中，`MyComponentLib1` 是一个包含 `SalesReport` 组件的组件库。

`SalesReport` 组件可通过其命名空间的完整类型名称进行引用：

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

如果将库纳入作用域内，该组件也可通过 `@using` 指令进行引用：

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

在顶级 `_Import.razor` 文件中包含 `@using MyComponentLib1` 指令，使库的组件可用于整个项目。 将指令添加到任何级别的 `_Import.razor` 文件，将命名空间应用于文件夹中的单个页面或一组页面。

## <a name="create-a-razor-components-class-library-with-static-assets"></a>创建包括静态资源的 Razor 组件类库

RCL 可以包括静态资产。 静态资产可用于任何使用该库的应用。 有关详细信息，请参阅 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>。

## <a name="build-pack-and-ship-to-nuget"></a>生成并打包库，再将其传送到 NuGet

由于组件库是标准的 .NET 库，因此将它们打包并传送到 NuGet 与将任何库打包并传送到 NuGet 没有什么区别。 在命令行界面中使用 [`dotnet pack`](/dotnet/core/tools/dotnet-pack) 命令，执行打包操作：

```dotnetcli
dotnet pack
```

在命令行界面中使用 [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) 命令，将包上传到 NuGet。

## <a name="additional-resources"></a>其他资源

* <xref:razor-pages/ui-class>
* [将 XML 链接器配置文件添加到库](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
