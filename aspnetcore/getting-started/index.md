---
title: ASP.NET Core 入门
author: rick-anderson
description: 介绍如何使用 ASP.NET Core 创建并运行简单的 Hello World 应用的快速教程。
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: getting-started
ms.openlocfilehash: 0f05ab120d64832a4bc2fd70921efc7238ee9eac
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187067"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>教程：ASP.NET Core 入门

本教程展示了如何使用 .NET Core 命令行接口来创建并运行 ASP.NET Core Web 应用。

你将了解如何：

> [!div class="checklist"]
> * 创建 Web 应用项目。
> * 信任开发证书。
> * 运行应用。
> * 编辑 Razor 页面。

最后，在本地计算机上运行工作 Web 应用。

![Web 应用主页](_static/home-page.png)

## <a name="prerequisites"></a>系统必备

[!INCLUDE[](~/includes/3.0-SDK.md)]

## <a name="create-a-web-app-project"></a>创建 Web 应用项目

打开命令行界面，然后输入以下命令：

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

上面的命令：

* 创建新 Web 应用。  
* `-o` 参数使用应用的源文件创建名为 aspnetcoreapp  的目录。

### <a name="trust-the-development-certificate"></a>信任开发证书

信任 HTTPS 开发证书：

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

以上命令会显示以下对话：

![安全警告对话](~/getting-started/_static/cert.png)

如果你同意信任开发证书，请选择“是”。 

# <a name="macostabmacos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

以上命令会显示以下消息：

*请求信任 HTTPS 开发证书。如果证书尚不受信任，我们将运行以下命令：* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

此命令可能会提示你输入密码以在系统密钥链上安装证书。 如果你同意信任开发证书，请输入密码。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

对于适用于 Linux 的 Windows 子系统，请参阅[在适用于 Linux 的 Windows 子系统中信任 HTTPS 证书](xref:security/enforcing-ssl#wsl)。

查看你的 Linux 分发对应的文档，了解如何信任 HTTPS 开发证书。

---

有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)

## <a name="run-the-app"></a>运行应用

运行以下命令：

```dotnetcli
cd aspnetcoreapp
dotnet run
```

在命令行界面指明应用已启动后，转到 [https://localhost:5001](https://localhost:5001)。 单击“接受”，接受隐私和 cookie 政策  。 此应用不保留个人信息。

## <a name="edit-a-razor-page"></a>编辑 Razor 页面

打开 Pages/Index.cshtml  ，并使用以下突出显示标记修改页面：

[!code-cshtml[](sample/index.cshtml?highlight=9)]

转到 [https://localhost:5001](https://localhost:5001)，并验证更改是否显示。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Web 应用项目。
> * 信任开发证书。
> * 运行该项目。
> * 进行更改。

要了解有关 ASP.NET Core 的详细信息，请参阅简介中推荐的学习路径：

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
