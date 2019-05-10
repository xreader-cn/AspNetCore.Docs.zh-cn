---
title: 通过 ASP.NET Core 使用 Angular 项目模板
author: SteveSandersonMS
description: 了解如何开始使用适用于 Angular 和 Angular CLI 的 ASP.NET Core 单页应用程序 (SPA) 项目模板。
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/angular
ms.openlocfilehash: 6d0107ef52d63a0f6f5713c518ddc54ac4230d53
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893664"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a>通过 ASP.NET Core 使用 Angular 项目模板

更新的 Angular 项目模板为使用 Angular 和 Angular CLI 实现丰富的客户端用户界面 (UI) 的 ASP.NET Core 应用提供了便捷起点。

该模板等同于创建用作 API 后端的 ASP.NET Core 项目，而 Angular CLI 项目用作 UI。 该模板提供在单个应用项目中托管两种项目类型的便利。 因此，应用项目可以作为单个单元来生成和发布。

## <a name="create-a-new-app"></a>创建新应用

如果您有安装 ASP.NET Core 2.1，则无需安装角度项目模板。

在空目录中使用命令 `dotnet new angular` 从命令提示符创建一个新项目。 例如，以下命令在 my-new-app 目录中创建应用并切换到该目录：

```console
dotnet new angular -o my-new-app
cd my-new-app
```

从 Visual Studio 或 .NET Core CLI 运行应用：

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

打开生成的 .csproj 文件，并从此文件正常运行应用。

生成过程会在首次运行时还原 npm 依赖关系，这可能需要几分钟的时间。 后续版本要快得多。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

确保你有一个名为 `ASPNETCORE_Environment` 的环境变量，其值为 `Development`。 在 Windows 上（在非 PowerShell 提示下）运行 `SET ASPNETCORE_Environment=Development`。 在 Linux 或 macOS 上运行 `export ASPNETCORE_Environment=Development`。

运行 [dotnet build](/dotnet/core/tools/dotnet-build) 以验证应用是否正确生成。 首次运行时，生成过程会还原 npm 依赖关系，这可能需要几分钟的时间。 后续版本要快得多。

运行 [dotnet run](/dotnet/core/tools/dotnet-run) 以启动应用。 记录类似于以下内容的消息：

```console
Now listening on: http://localhost:<port>
```

在浏览器中导航到此 URL。

该应用在后台启动 Angular CLI 服务器的一个实例。 记录类似于以下内容的消息：*NG Live 开发服务器正在侦听 localhost:&lt;otherport&gt;，打开浏览器上 http://localhost:&lt; otherport&gt;/*。 忽略此消息&mdash;这**不是**组合 ASP.NET Core 和 Angular CLI 应用的 URL。

---

该项目模板创建一个 ASP.NET Core 应用和一个 Angular 应用。 ASP.NET Core 应用旨在用于数据访问、授权和其他服务器端问题。 位于 ClientApp 子目录中的 Angular 应用旨在用于所有 UI 问题。

## <a name="add-pages-images-styles-modules-etc"></a>添加页面、映像、样式、模块等。

ClientApp 目录包含标准的 Angular CLI 应用。 有关详细信息，请参阅官方 [Angular 文档](https://github.com/angular/angular-cli/wiki)。

此模板创建的 Angular 应用与 Angular CLI 本身创建的应用（通过 `ng new`）之间存在细微差异；但是，该应用的功能未变。 该模板创建的应用包含基于 [Bootstrap](https://getbootstrap.com/) 的布局和基本路由示例。

## <a name="run-ng-commands"></a>运行 ng 命令

在命令提示符中，切换到 ClientApp 子目录：

```console
cd ClientApp
```

如果全局安装了 `ng` 工具，则可以运行其任何命令。 例如，你可以运行 `ng lint`、`ng test` 或任何其他 [Angular CLI 命令](https://github.com/angular/angular-cli/wiki#additional-commands)。 不过无需运行 `ng serve`，因为 ASP.NET Core 应用为应用的服务器端和客户端部分提供服务。 在内部，它在开发中使用 `ng serve`。

如果未安装 `ng` 工具，请改为运行 `npm run ng`。 例如，你可以运行 `npm run ng lint` 或 `npm run ng test`。

## <a name="install-npm-packages"></a>安装 npm 包

要安装第三方 npm 程序包，请使用 ClientApp 子目录中的命令提示符。 例如：

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>发布和部署

在开发过程中，应用在为开发人员之便而优化的模式下运行。 例如，JavaScript 捆绑包包含源映射（这样在调试时可以查看原始 TypeScript 代码）。 该应用监视磁盘上的 TypeScript、HTML 和 CSS 文件更改，并在看到这些文件更改时自动重新编译和重新加载。

在生产中，提供针对性能进行了优化的应用版本。 该操作被配置为自动发生。 发布时，生成配置会发出预先 (AoT) 编译的压缩客户端代码版本。 开发内部版本，与生产版本并不要求 Node.js 安装在服务器上 （除非已启用服务器端呈现 (SSR)）。

你可以使用标准 [ASP.NET Core 托管和部署方法](xref:host-and-deploy/index)。

## <a name="run-ng-serve-independently"></a>独立运行“ng serve”

在 ASP.NET Core 应用以开发模式启动时，该项目配置为在后台启动自己的 Angular CLI 服务器实例。 这很方便，因为你无需手动运行单独的服务器。

这种默认设置有一个缺点。 每次修改 C# 代码并且需要重启 ASP.NET Core 应用时，Angular CLI 服务器都会重启。 大约需要 10 秒才能开始备份。 如果你经常进行 C# 代码编辑并且不希望等待 Angular CLI 重启，请在外部运行独立于 ASP.NET Core 进程的 Angular CLI 服务器。 为此，请执行以下操作：

1. 在命令提示符中，切换到 ClientApp 子目录，并启动 Angular CLI 开发服务器：

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > 使用 `npm start` 而不是 `ng serve` 启动 Angular CLI 开发服务器，以便遵守 package.json 中的配置。 要将其他参数传递给 Angular CLI 服务器，请将它们添加到 package.json 文件中的相关 `scripts` 行。

2. 修改 ASP.NET Core 应用以使用外部 Angular CLI 实例，而不是启动它自己的实例。 在 Startup 类中，将 `spa.UseAngularCliServer` 调用替换为以下内容：

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

当启动 ASP.NET Core 应用时，它不会启动 Angular CLI 服务器， 而是使用你手动启动的实例。 这使它能够更快地启动和重新启动。 不再需要每次等待 Angular CLI 重新生成客户端应用。

### <a name="pass-data-from-net-code-into-typescript-code"></a>将 .NET 代码中的数据传递到 TypeScript 代码中

在 SSR 期间，你可能希望将从 ASP.NET Core 应用预请求的数据传递到 Angular 应用。 例如，你可以传递 cookie 信息或从数据库读取的某些内容。 若要执行此操作，请编辑 Startup 类。 在 `UseSpaPrerendering` 的回叫中，为 `options.SupplyData` 设置一个值，如下所示：

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

`SupplyData` 回叫允许传递按请求的任意 JSON 序列化数据（例如字符串、布尔值或数字）。 main.server.ts 代码将此数据接收为 `params.data`。 例如，前面的代码示例将一个布尔值作为 `params.data.isHttpsRequest` 传递给 `createServerRenderer` 回叫。 你可以通过 Angular 支持的任何方式将此传递给应用的其他部分。 例如，请参阅 main.server.ts 如何将 `BASE_URL` 值传递给构造函数被声明为接收它的任何组件。

### <a name="drawbacks-of-ssr"></a>SSR 的缺点

并非所有应用都受益于 SSR。 主要优点是可感知的性能。 通过缓慢的网络连接或缓慢的移动设备连接你的应用的访问者可以快速查看初始 UI，即使需要一段时间才能提取或分析 JavaScript 捆绑包也是如此。 但是，许多 SPA 主要在具有快速内部公司网络的快速计算机（其中应用几乎立即显示）上使用。

同时，启用 SSR 也存在重大缺点。 它增加了开发过程的复杂性。 代码必须在两个不同的环境中运行：客户端和服务器端（在从 ASP.NET Core 中调用的 Node.js 环境中）。 以下是一些需要谨记的事项：

* SSR 需要在生产服务器上安装 Node.js。 对于某些部署方案（例如 Azure 应用服务）而言，情况自然如此，但对于其他方案（例如 Azure Service Fabric）则不适用。
* 启用 `BuildServerSideRenderer` 生成标志会导致发布 node_modules 目录。 该文件夹包含 20,000 多个文件，这会增加部署时间。
* 要在 Node.js 环境中运行代码，则不能依赖于特定浏览器的 JavaScript API（如 `window` 或 `localStorage`）。 如果代码（或引用的某个第三方库）尝试使用这些 API，则在 SSR 期间会出现错误。 例如，不要使用 jQuery，因为它在许多位置引用特定浏览器的 API。 要避免错误，你必须避免使用 SSR 或避免使用特定于浏览器的 API 或库。 你可以将对这些 API 的任何调用包装在检查中，以确保它们在 SSR 期间不被调用。 例如，在 JavaScript 或 TypeScript 代码中使用如下所示的检查：

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a>其他资源

* <xref:security/authentication/identity/spa>
