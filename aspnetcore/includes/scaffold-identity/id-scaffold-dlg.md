---
ms.openlocfilehash: 648ed8087c64eaf80fd055fa7dd08041ab9a8752
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320272"
---
运行标识基架：

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从**解决方案资源管理器**，右键单击该项目 >**添加** > **新基架项**。
* 从左窗格**添加基架**对话框中，选择**标识** > **添加**。
* 在中**ADD 标识添加**对话框中，选择所需的选项。
  * 选择现有的布局页上，或不正确的标记将被覆盖布局文件。 例如`~/Pages/Shared/_Layout.cshtml`Razor 页面`~/Views/Shared/_Layout.cshtml`对于 MVC 项目
  * 选择 **+** 按钮以创建一个新**数据上下文类**。
* 选择**添加**。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果以前未安装 ASP.NET Core 基架，请立即进行安装：

```cli
dotnet tool install -g dotnet-aspnet-codegenerator
```

添加到包引用[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)到项目 (\*.csproj) 文件。 在项目目录中运行以下命令：

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

运行以下命令以列出标识基架选项：

```cli
dotnet aspnet-codegenerator identity -h
```

在项目文件夹中，运行所需的选项标识基架。 例如，若要设置默认值 UI 标识和最小的文件数，运行以下命令：

```cli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
