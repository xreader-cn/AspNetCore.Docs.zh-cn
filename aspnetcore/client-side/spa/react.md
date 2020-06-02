---
title: 通过 ASP.NET Core 使用 React 项目模板
author: SteveSandersonMS
description: 了解如何开始使用适用于 React 和 create-react-app 的 ASP.NET Core 单页应用程序 (SPA) 项目模板。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/react
ms.openlocfilehash: 1dd352f1a00d30df915fe1936d7dcb34af1b6634
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773671"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>通过 ASP.NET Core 使用 React 项目模板

更新的 React 项目模板为使用 React 和 [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) 约定实现丰富的客户端用户界面 (UI) 的 ASP.NET Core 应用程序提供了便捷起点。

该模板等同于创建两个项目，即用作 API 后端的 ASP.NET Core 项目和用作 UI 的标准 CRA React 项目，但均可在可以生成并发布为单个单元的单个应用程序项目中进行托管。

React 项目模板不适用于服务器端呈现 (SSR)。 对于带有 React 和 Node.js 的 SSR，请考虑使用 [Next.js](https://github.com/zeit/next.js/) 或 [Razzle](https://github.com/jaredpalmer/razzle)。

## <a name="create-a-new-app"></a>创建新应用

如果已安装 ASP.NET Core 2.1，则无需安装 React 项目模板。

在空目录中使用命令 `dotnet new react` 从命令提示符创建一个新项目。 例如，以下命令在 my-new-app 目录中创建应用并切换到该目录：

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

从 Visual Studio 或 .NET Core CLI 运行应用：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

打开生成的 .csproj 文件，并从此文件正常运行应用。

生成过程会在首次运行时还原 npm 依赖关系，这可能需要几分钟的时间。 后续版本要快得多。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

确保你有一个名为 `ASPNETCORE_Environment` 的环境变量，其值为 `Development`。 在 Windows 上（在非 PowerShell 提示下）运行 `SET ASPNETCORE_Environment=Development`。 在 Linux 或 macOS 上运行 `export ASPNETCORE_Environment=Development`。

运行 [dotnet build](/dotnet/core/tools/dotnet-build) 以验证应用是否正确生成。 首次运行时，生成过程会还原 npm 依赖关系，这可能需要几分钟的时间。 后续版本要快得多。

运行 [dotnet run](/dotnet/core/tools/dotnet-run) 以启动应用。

---

该项目模板创建一个 ASP.NET Core 应用和一个 React 应用。 ASP.NET Core 应用旨在用于数据访问、授权和其他服务器端问题。 位于 ClientApp 子目录中的 React 应用旨在用于所有 UI 问题。

## <a name="add-pages-images-styles-modules-etc"></a>添加页面、映像、样式、模块等。

ClientApp 目录是标准的 CRA React 应用。 有关详细信息，请参阅官方 [CRA 文档](https://create-react-app.dev/docs/getting-started/)。

此模板创建的 React 应用与 CRA 自身创建的应用之间存在细微差异；但是，该应用的功能未变。 该模板创建的应用包含基于 [Bootstrap](https://getbootstrap.com/) 的布局和基本路由示例。

## <a name="install-npm-packages"></a>安装 npm 包

要安装第三方 npm 程序包，请使用 ClientApp 子目录中的命令提示符。 例如：

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>发布和部署

在开发过程中，应用在为开发人员之便而优化的模式下运行。 例如，JavaScript 捆绑包包含源映射（这样在调试时可以查看原始源代码）。 该应用程序监视磁盘上的 JavaScript、HTML 和 CSS 文件更改，并在看到这些文件更改时自动重新编译和重新加载。

在生产中，提供针对性能进行了优化的应用版本。 该操作被配置为自动发生。 发布时，生成配置会发出转换的压缩客户端代码版本。 与开发版本不同，生产版本不需要在服务器上安装 Node.js。

你可以使用标准 [ASP.NET Core 托管和部署方法](xref:host-and-deploy/index)。

## <a name="run-the-cra-server-independently"></a>独立运行 CRA 服务器

在 ASP.NET Core 应用以开发模式启动时，该项目配置为在后台启动自己的 CRA 开发服务器实例。 这很方便，因为这表示你无需手动运行单独的服务器。

这种默认设置有一个缺点。 每次修改 C# 代码并且需要重启 ASP.NET Core 应用时，CRA 服务器都会重启。 大约需要几秒才能开始备份。 如果你经常进行 C# 代码编辑并且不希望等待 CRA 服务器重启，请在外部运行独立于 ASP.NET Core 进程的 CRA 服务器。 为此，请执行以下操作：

1. 使用以下设置将 .env 文件添加到 ClientApp 子目录 ：

    ```
    BROWSER=none
    ```

    这将阻止在外部启动 CRA 服务器时打开 Web 浏览器。

2. 在命令提示符中，切换到 ClientApp 子目录，并启动 CRA 开发服务器：

    ```console
    cd ClientApp
    npm start
    ```

3. 修改 ASP.NET Core 应用以使用外部 CRA 服务器实例，而不是启动它自己的实例。 在 Startup 类中，将 `spa.UseReactDevelopmentServer` 调用替换为以下内容：

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

当启动 ASP.NET Core 应用时，它不会启动 CRA 服务器， 而是使用你手动启动的实例。 这使它能够更快地启动和重新启动。 不再需要每次等待 React 应用重新生成。

> [!IMPORTANT]
> 此模板不支持“服务器端呈现”功能。 使用此模板是为了通过“create-react-app”满足奇偶一致性。 因此，不支持“create-react-app”项目中没有的方案和功能（例如 SSR），这些内容作为练习提供给用户。

## <a name="additional-resources"></a>其他资源

* <xref:security/authentication/identity/spa>
