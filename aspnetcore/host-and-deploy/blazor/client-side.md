---
title: 托管和部署 Blazor 客户端
author: guardrex
description: 了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: 01a612029f415f583908c3bf2adc2e6d35167acb
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614633"
---
# <a name="host-and-deploy-blazor-client-side"></a>托管和部署 Blazor 客户端

作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="host-configuration-values"></a>主机配置值

使用[客户端托管模型](xref:blazor/hosting-models#client-side-hosting-model)的 Blazor 应用可以接受以下主机配置值，作为开发环境中运行时的命令行参数。

### <a name="content-root"></a>内容根

`--contentroot` 参数设置包含应用内容文件的目录的绝对路径。 在下面的示例中，`/content-root-path` 是应用的内容根路径。

* 以本地方式在命令提示符下运行应用时传递该参数。 在应用的目录中，执行以下操作：

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* 在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。 如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* 在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数。 在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>基路径

`--pathbase` 参数可设置使用非根虚拟路径本地运行的应用的应用基路径（将 `<base>` 标记 `href` 针对暂存和生产设置为 `/` 之外的路径）。 在下面的示例中，`/virtual-path` 是应用的基路径。 有关详细信息，请参阅[应用基路径](#app-base-path)部分。

> [!IMPORTANT]
> 不同于向 `href` 标记的 `<base>` 提供的路径，传递 `--pathbase` 参数值时不包括尾部反斜杠 (`/`)。 如果在 `<base>` 标记中以 `<base href="/CoolApp/">` 形式（包括尾部反斜杠）提供应用基路径，则以 `--pathbase=/CoolApp` 形式（无尾部反斜杠）传递命令行参数值。

* 以本地方式在命令提示符下运行应用时传递该参数。 在应用的目录中，执行以下操作：

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* 在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。 如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* 在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数。 在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a>URL

`--urls` 参数设置 IP 地址或主机地址，其中包含侦听请求的端口和协议。

* 以本地方式在命令提示符下运行应用时传递该参数。 在应用的目录中，执行以下操作：

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* 在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。 如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* 在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数。 在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a>部署

使用[客户端托管模型](xref:blazor/hosting-models#client-side-hosting-model)：

* 将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。
* 应用将在浏览器线程中直接执行。 支持以下策略之一：
  * Blazor 应用由 ASP.NET Core 应用提供服务。 [使用 ASP.NET Core 进行托管部署](#hosted-deployment-with-aspnet-core)部分中介绍了此策略。
  * Blazor 应用位于静态托管 Web 服务器或服务中，其中未使用 .NET 对 Blazor 应用提供服务。 [独立部署](#standalone-deployment)部分中介绍了此策略。

## <a name="configure-the-linker"></a>配置链接器

Blazor 对每个生成执行中间语言 (IL) 链接，以从输出程序集中删除不必要的 IL。 可以在生成时控制程序集链接。 有关更多信息，请参见<xref:host-and-deploy/blazor/configure-linker>。

## <a name="rewrite-urls-for-correct-routing"></a>重写 URL，以实现正确路由

客户端应用中对页组件的路由请求不如服务器端对托管应用的路由请求简单。 请考虑具有两个页面的客户端应用：

* Main.cshtml &ndash; 在应用的根目录下加载，且包含“关于”页链接 (`href="About"`)。
* About.cshtml &ndash;“关于”页。

使用浏览器的地址栏（例如，`https://www.contoso.com/`）请求应用的默认文档：

1. 浏览器发出请求。
1. 返回默认页，通常为 index.html。
1. index.html 启动应用。
1. 此时，Blazor 的路由器加载，并显示 Razor Main 页 (Main.cshtml)。

在主页上，选择“关于”页面的链接可加载“关于”页面。 选择“关于”页面的链接适用于客户端，因为 Blazor 路由器阻止浏览器对 Internet 发出请求，针对 `About` 转到 `www.contoso.com`，并为“关于”页本身提供服务。 针对客户端应用中的内部页的所有请求，工作原理都相同：这些请求不会触发对 Internet 上的服务器托管资源的基于浏览器的请求。 路由器将在内部处理请求。

如果针对 `www.contoso.com/About` 使用浏览器的地址栏发出请求，则请求会失败。 应用的 Internet 主机上不存在此类资源，所以返回的是“404 - 找不到”响应。

由于浏览器针对客户端页面请求基于 Internet 的主机，因此 Web 服务器和托管服务必须将对服务器上非物理方式资源的所有请求重写为 index.html 页。 如果返回 index.html，应用的客户端路由器将接管工作并使用正确的资源响应。

## <a name="app-base-path"></a>应用基路径

应用基路径是服务器上的虚拟应用根路径。 例如，位于 `/CoolApp/` 虚拟文件夹中 Contoso 服务器上的应用可通过 `https://www.contoso.com/CoolApp` 进行访问，并且该应用的虚拟基路径为 `/CoolApp/`。 通过将应用基路径设置为 `CoolApp/`，应用就会知道其在服务器上实际所在的位置。 应用可使用应用基路径通过不在根目录中的组件构造应用根目录的相对 URL。 通过这种方式，位于目录结构不同级别的组件可生成指向整个应用其他位置的资源链接。 应用基路径也用于截获超链接单击，其中链接的 `href` 目标位于应用基路径 URI 空间中 &mdash; Blazor 路由器可处理内部导航。

在许多托管方案中，应用的服务器虚拟路径为应用的根目录。 在这些情况下，应用基路径为正斜杠 (`<base href="/" />`)，它是应用的默认配置。 在其他托管方案中，例如 GitHub 页和 IIS 虚拟目录或子应用程序，应用基路径必须设置为应用的服务器虚拟路径。 要设置应用的基路径，请在 `<head>` 标记元素中找到的 index.html 中添加或更新 `<base>` 标记。 将 `href` 属性值设置为 `virtual-path/`（需要尾部反斜杠），其中 `virtual-path/` 是应用的服务器上的完整虚拟应用根路径。 在上述示例中，虚拟路径设置为 `CoolApp/`：`<base href="CoolApp/">`。

对于配置了非根虚拟路径的应用（例如，`<base href="CoolApp/">`），当在本地运行应用时，将无法查找其资源。 要在本地开发和测试过程中解决此问题，可提供 path base 参数，用于匹配运行时 `<base>` 标记的 `href` 值。

要在本地运行应用时传递带根路径 (`/`) 的 path base 参数，请在应用的目录中执行以下命令：

```console
dotnet run --pathbase=/CoolApp
```

应用在 `http://localhost:port/CoolApp` 本地响应。

有关详细信息，请参阅[基路径主机配置值](#path-base)部分。

如果应用使用[客户端托管模型](xref:blazor/hosting-models#client-side-hosting-model)（基于 Blazor 项目模板），并且作为 ASP.NET Core 应用中的 IIS 子应用程序进行托管，则有必要禁用已继承的 ASP.NET Core 模块处理程序或确保根应用的 web.config 文件中的 `<handlers>` 部分未由子应用继承。

通过向文件添加 `<handlers>` 部分，删除应用已发布 web.config 文件中的处理程序：

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

或者，通过使用 `<location>` 元素并且将 `inheritInChildApplications` 设置为 `false`，禁止继承根（父级）应用的 `<system.webServer>` 部分：

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" ... />
      </handlers>
      <aspNetCore ... />
    </system.webServer>
  </location>
</configuration>
```

如本部分中所述，除配置应用的基路径外，还需删除处理程序或禁用继承。 在 IIS 中配置子应用时，在应用的 index.html 文件中将应用基路径设置为 IIS 别名。

## <a name="hosted-deployment-with-aspnet-core"></a>使用 ASP.NET Core 进行托管部署

托管部署通过在服务器上运行的 [ASP.NET Core 应用](xref:index)为浏览器提供客户端 Blazor 应用。

Blazor 应用随附于已发布输出中的 ASP.NET Core 应用，因此这两个应用将一起进行部署。 需要能够托管 ASP.NET Core 应用的 Web 服务器。 对于托管部署，Visual Studio 包括 Blazor（托管 ASP.NET Core）项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令时为 `blazorhosted` 模板）。

有关托管和部署 ASP.NET Core 应用的详细信息，请参阅 <xref:host-and-deploy/index>。

若要了解如何部署到 Azure 应用服务，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。

## <a name="standalone-deployment"></a>独立部署

独立部署将客户端 Blazor 应用作为客户端直接请求的一组静态文件。 Web 服务器不用于对 Blazor 提供服务。

### <a name="iis"></a>IIS

IIS 是适用于 Blazor 应用的强大静态文件服务器。 要配置 IIS 以托管 Blazor，请参阅[在 IIS 上生成静态网站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。

已发布的资产是在 /bin/Release/{TARGET FRAMEWORK}/publish 文件夹中创建。 在 Web 服务器或托管服务上托管“publish”文件夹内容。

#### <a name="webconfig"></a>web.config

发布 Blazor 项目时，将使用以下 IIS 配置创建 web.config 文件：

* 对以下文件扩展名设置 MIME 类型：
  * .dll &ndash; `application/octet-stream`
  * .json &ndash; `application/json`
  * .wasm &ndash; `application/wasm`
  * .woff &ndash; `application/font-woff`
  * .woff2 &ndash; `application/font-woff`
* 对以下 MIME 类型启用 HTTP 压缩：
  * `application/octet-stream`
  * `application/wasm`
* 建立 URL 重写模块规则：
  * 提供应用的静态资产驻留所在的子目录 ({ASSEMBLY NAME}/dist/{PATH REQUESTED})。
  * 创建 SPA 回退路由，以便非文件资产请求能够重定向到应用的静态资产文件夹中的默认文档 ({ASSEMBLY NAME}/dist/index.html)。

#### <a name="install-the-url-rewrite-module"></a>安装 URL 重写模块

重写 URL 必须使用 [URL 重写模块](https://www.iis.net/downloads/microsoft/url-rewrite)。 此模块默认不安装，且不适用于安装为 Web 服务器 (IIS) 角色服务功能。 必须从 IIS 网站下载该模块。 使用 Web 平台安装程序安装模块：

1. 以本地方式导航到 [URL 重写模块下载页](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。 对于英语版本，请选择“WebPI”以下载 WebPI 安装程序。 对于其他语言，请选择适当的服务器体系结构 (x86/x64) 下载安装程序。
1. 将安装程序复制到服务器。 运行安装程序。 选择“安装”按钮，并接受许可条款。 安装完成后无需重启服务器。

#### <a name="configure-the-website"></a>配置网站

将网站的物理路径设置为应用的文件夹。 该文件夹包含：

* web.config 文件，IIS 使用该文件配置网站，包括所需的重定向规则和文件内容类型。
* 应用的静态资产文件夹。

#### <a name="troubleshooting"></a>疑难解答

如果你看到“500 - 内部服务器错误”，且 IIS 管理器在尝试访问网站配置时抛出错误，请确认是否已安装 URL 重写模块。 如果未安装该模块，则 IIS 无法分析 web.config 文件。 这可以防止 IIS 管理器加载网站配置，并防止网站对 Blazor 的静态文件提供服务。

有关排查部署到 IIS 的问题的详细信息，请参阅 <xref:host-and-deploy/iis/troubleshoot>。

### <a name="nginx"></a>Nginx

以下 nginx.conf 文件已简化，以展示如何将 Nginx 配置为，每当在磁盘上找不到相应文件时，发送 index.html 文件。

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

有关生产 Nginx Web 服务器配置的详细信息，请参阅 [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)（创建 NGINX 增强版和 NGINX 配置文件）。

### <a name="nginx-in-docker"></a>Docker 中的 Nginx

要使用 Nginx 在 Docker 中托管 Blazor，请设置 Dockerfile，以使用基于 Alpine 的 Nginx 映像。 更新 Dockerfile，以将 nginx.config 文件复制到容器。

向 Dockerfile 添加一行，如下面的示例中所示：

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a>GitHub 页

要处理 URL 重写，请使用脚本添加 404.html 文件，该脚本可处理到 index.html 页的重定向请求。 关于社区提供的示例实现，请参阅[用于 GitHub 页的单页应用](http://spa-github-pages.rafrex.com/)（[GitHub 上的 rafrex/spa-github-pages](https://github.com/rafrex/spa-github-pages#readme)）。 可在 [GitHub 上的 blazor-demo/blazor-demo.github.io](https://github.com/blazor-demo/blazor-demo.github.io)（[实时站点](https://blazor-demo.github.io/)）查看使用社区方法的示例。

如果使用项目站点而非组织站点，请在 index.html 中添加或更新 `<base>` 标记。 将 `href` 属性值设置为，包含尾部斜杠的 GitHub 存储库名称（例如，`my-repository/`）。
