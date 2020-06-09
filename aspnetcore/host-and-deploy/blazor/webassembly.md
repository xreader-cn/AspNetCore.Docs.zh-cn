---
title: "title:'托管和部署 ASP.NET Core Blazor WebAssembly' author: guardrex description:“了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。”"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date:2020 年 5 月 28 日 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 09f74edaa3d1cb0d51e0ce8d0209383885b81f5f
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239383"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>'Blazor'

'Identity'

'Let's Encrypt'

* 'Razor'
* 'SignalR' uid: host-and-deploy/blazor/webassembly

托管和部署 ASP.NET Core Blazor WebAssembly

* 作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams) 和 [Safia Abdalla](https://safia.rocks) 使用 [Blazor WebAssembly 托管模型](xref:blazor/hosting-models#blazor-webassembly)：
* 将 Blazor 应用、其依赖项及 .NET 运行时并行下载到浏览器。 应用将在浏览器线程中直接执行。

## <a name="precompression"></a>支持以下部署策略：

Blazor 应用由 ASP.NET Core 应用提供服务。 [使用 ASP.NET Core 进行托管部署](#hosted-deployment-with-aspnet-core)部分中介绍了此策略。

* Blazor 应用位于静态托管 Web 服务器或服务中，其中未使用 .NET 对 Blazor 应用提供服务。
* [独立部署](#standalone-deployment)部分介绍了此策略，包括有关将 Blazor WebAssembly 应用作为 IIS 子应用托管的信息。

预压缩

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

在发布 Blazor WebAssembly 应用时，会对输出内容进行预压缩，这样可以减小应用的大小，并且无需再进行运行时压缩。

## <a name="rewrite-urls-for-correct-routing"></a>使用以下压缩算法：

[Brotli](https://tools.ietf.org/html/rfc7932)（级别最高） [Gzip](https://tools.ietf.org/html/rfc1952)

* 若要禁用压缩，请将 `BlazorEnableCompression` MSBuild 属性添加到应用的项目文件，并将值设置为 `false`：
* 有关 IIS web.config 压缩配置，请参阅 [IIS：Brotli 和 Gzip 压缩](#brotli-and-gzip-compression) 部分。

重写 URL，以实现正确路由

1. 在 Blazor WebAssembly 应用中路由对页组件的请求不如在 Blazor Server 对托管应用中路由请求直接。
1. 假设有一个 Blazor WebAssembly 应用包含以下两个组件：
1. Main.razor：在应用的根目录处加载，并包含指向 `About` 组件 (`href="About"`) 的链接。
1. About.razor：`About` 组件。

使用浏览器的地址栏（例如，`https://www.contoso.com/`）请求应用的默认文档： 浏览器发出请求。 返回默认页，通常为 index.html。

index.html 启动应用。 Blazor 的路由器进行加载，然后呈现 Razor `Main` 组件。

在 Main 页中，选择指向 `About` 组件的链接适用于客户端，因为 Blazor 路由器阻止浏览器在 Internet 上发出请求，针对 `About` 转到 `www.contoso.com`，并为呈现的 `About` 组件本身提供服务。 针对 Blazor WebAssembly 应用中的内部终结点的所有请求，工作原理都相同：这些请求不会触发对 Internet 上的服务器托管资源的基于浏览器的请求。

路由器将在内部处理请求。 如果针对 `www.contoso.com/About` 使用浏览器的地址栏发出请求，则请求会失败。

## <a name="hosted-deployment-with-aspnet-core"></a>应用的 Internet 主机上不存在此类资源，所以返回的是“404 - 找不到”响应。

由于浏览器针对客户端页面请求基于 Internet 的主机，因此 Web 服务器和托管服务必须将对服务器上非物理方式资源的所有请求重写为 index.html 页。

如果返回 index.html，应用的 Blazor 路由器将接管工作并使用正确的资源响应。 部署到 IIS 服务器时，可以将 URL 重写模块与应用的已发布 web.config 文件一起使用。 有关详细信息，请参阅 [IIS](#iis) 部分。 使用 ASP.NET Core 进行托管部署

托管部署通过在 Web 服务器上运行的 [ASP.NET Core](xref:index) 应用为浏览器提供 Blazor WebAssembly 应用。

客户端 Blazor WebAssembly 应用将与服务器应用的任何其他静态 Web 资产一起发布到服务器应用的 /bin/Release/{TARGET FRAMEWORK}/publish/wwwroot 文件夹。

## <a name="standalone-deployment"></a>这两个应用一起部署。

需要能够托管 ASP.NET Core 应用的 Web 服务器。 对于托管部署，Visual Studio 会在选择“托管”选项（使用 `dotnet new` 命令时为 `-ho|--hosted`）的情况下，包含 Blazor WebAssembly App 项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new)命令时为 `blazorwasm` 模板） 。

有关托管和部署 ASP.NET Core 应用的详细信息，请参阅 <xref:host-and-deploy/index>。

### <a name="azure-app-service"></a>若要了解如何部署到 Azure 应用服务，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。

独立部署

独立部署将 Blazor WebAssembly 应用作为客户端直接请求的一组静态文件提供。 任何静态文件服务器均可提供 Blazor 应用。 独立部署资产发布到 /bin/Release/{TARGET FRAMEWORK}/publish/wwwroot 文件夹中。

### <a name="iis"></a>Azure 应用服务

可以将 Blazor WebAssembly 应用部署到 Windows 上的 Azure 应用服务，该服务在 [IIS](#iis) 上托管应用。 目前不支持将独立的 Blazor WebAssembly 应用部署到适用于 Linux 的 Azure 应用服务。

目前无法提供用于托管应用的 Linux 服务器映像。 正在进行此工作以支持此场景。

#### <a name="webconfig"></a>IIS

IIS 是适用于 Blazor 应用的强大静态文件服务器。

* 要配置 IIS 以托管 Blazor，请参阅[在 IIS 上生成静态网站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。
  * 已发布的资产是在 /bin/Release/{TARGET FRAMEWORK}/publish 文件夹中创建。
  * 在 Web 服务器或托管服务上托管“publish”文件夹内容。
  * web.config
  * 发布 Blazor 项目时，将使用以下 IIS 配置创建 web.config 文件：
  * 对以下文件扩展名设置 MIME 类型：
* .dll：`application/octet-stream`
  * `application/octet-stream`
  * `application/wasm`
* .json：`application/json`
  * .wasm：`application/wasm`
  * .woff：`application/font-woff`
  
#### <a name="use-a-custom-webconfig"></a>.woff2：`application/font-woff`

对以下 MIME 类型启用 HTTP 压缩：

#### <a name="install-the-url-rewrite-module"></a>建立 URL 重写模块规则：

提供应用的静态资产驻留所在的子目录 (wwwroot/{PATH REQUESTED})。 创建 SPA 回退路由，以便非文件资产请求能够重定向到应用的静态资产文件夹中的默认文档 (wwwroot/index.html)。 使用自定义 web.config 要使用自定义 web.config 文件，请将自定义 web.config 文件放在项目文件夹的根目录下，然后发布该项目 。

1. 安装 URL 重写模块 重写 URL 必须使用 [URL 重写模块](https://www.iis.net/downloads/microsoft/url-rewrite)。 此模块默认不安装，且不适用于安装为 Web 服务器 (IIS) 角色服务功能。
1. 必须从 IIS 网站下载该模块。 使用 Web 平台安装程序安装模块： 以本地方式导航到 [URL 重写模块下载页](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。 对于英语版本，请选择“WebPI”以下载 WebPI 安装程序。

#### <a name="configure-the-website"></a>对于其他语言，请选择适当的服务器体系结构 (x86/x64) 下载安装程序。

将安装程序复制到服务器。 运行安装程序。

* 选择“安装”按钮，并接受许可条款。
* 安装完成后无需重启服务器。

#### <a name="host-as-an-iis-sub-app"></a>配置网站

将网站的物理路径设置为应用的文件夹。

* 该文件夹包含：

  web.config 文件，IIS 使用该文件配置网站，包括所需的重定向规则和文件内容类型。

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* 应用的静态资产文件夹。

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

作为 IIS 子应用托管 如果独立应用作为 IIS 子应用托管，请执行下列任一操作：

#### <a name="brotli-and-gzip-compression"></a>禁用继承的 ASP.NET Core 模块处理程序。

通过向文件添加 `<handlers>` 部分，删除 Blazor 应用已发布 web.config 文件中的处理程序： 通过使用 `<location>` 元素并且将 `inheritInChildApplications` 设置为 `false`，禁止继承根（父级）应用的 `<system.webServer>` 部分：

#### <a name="troubleshooting"></a>除[配置应用的基路径](xref:host-and-deploy/blazor/index#app-base-path)外，还需删除处理程序或禁用继承。

在 IIS 中配置子应用时，在应用的 index.html 文件中将应用基路径设置为 IIS 别名。 Brotli 和 Gzip 压缩 通过 web.config 可将 IIS 配置为提供 Brotli 或 Gzip 压缩的 Blazor 资产。

有关示例配置，请参阅 [web.config](webassembly/_samples/web.config?raw=true)。

### <a name="azure-storage"></a>疑难解答

如果你看到“500 - 内部服务器错误”，且 IIS 管理器在尝试访问网站配置时抛出错误，请确认是否已安装 URL 重写模块。 如果未安装该模块，则 IIS 无法分析 web.config 文件。

这可以防止 IIS 管理器加载网站配置，并防止网站对 Blazor 的静态文件提供服务。

* 有关排查部署到 IIS 的问题的详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。
* Azure 存储 [Azure 存储](/azure/storage/)静态文件承载允许无服务器的 Blazor 应用承载。 支持自定义域名、Azure 内容分发网络 (CDN) 以及 HTTPS。 为存储帐户上的静态网站承载启用 blob 服务时：

设置“索引文档名称”到 `index.html`。

* 设置“错误文档路径”到 `index.html`。
* Razor 组件和其他非文件终结点不会驻留在由 blob 服务存储的静态内容中的物理路径中。

  当收到 Blazor 路由器应处理的对这些资源之一的请求时，由 blob 服务生成的“404 - 未找到”错误会将此请求路由到“错误文档路径”。
  
  1. 返回 Index.html blob，Blazor 路由器会加载并处理此路径。
  1. 如果由于文件的 `Content-Type` 标头中的 MIME 类型不正确，导致在运行时未加载文件，请执行以下任一操作：

配置工具，用于在部署文件时设置正确的 MIME 类型（`Content-Type` 标头）。

### <a name="nginx"></a>在部署应用后更改文件的 MIME 类型（`Content-Type` 标头）。

在每个文件的存储资源管理器（Azure 门户）中，执行以下操作：

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

右键单击该文件并选择“属性”。

### <a name="nginx-in-docker"></a>设置“ContentType”并选择“保存”按钮 。

有关更多信息，请参阅 [Azure 存储中的静态网站承载](/azure/storage/blobs/storage-blob-static-website)。 Nginx

以下 nginx.conf 文件已简化，以展示如何将 Nginx 配置为，每当在磁盘上找不到相应文件时，发送 index.html 文件。

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>有关生产 Nginx Web 服务器配置的详细信息，请参阅 [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)（创建 NGINX 增强版和 NGINX 配置文件）。

Docker 中的 Nginx

1. 要使用 Nginx 在 Docker 中托管 Blazor，请设置 Dockerfile，以使用基于 Alpine 的 Nginx 映像。 更新 Dockerfile，以将 nginx.config 文件复制到容器。

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. 向 Dockerfile 添加一行，如下面的示例中所示：

1. Apache

1. 若要将 Blazor WebAssembly 应用部署到 CentOS 7 或更高版本：

创建 Apache 配置文件。

### <a name="github-pages"></a>下面的示例展示了一个简化的配置文件 (*blazorapp.config*)：

将 Apache 配置文件放入 `/etc/httpd/conf.d/` 目录（这是 CentOS 7 中的默认 Apache 配置目录）。 将应用的文件放入 `/var/www/blazorapp` 目录（配置文件中特定于 `DocumentRoot` 的位置）。 重启 Apache 服务。

有关详细信息，请参阅 [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 和 [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)。 GitHub 页

## <a name="host-configuration-values"></a>要处理 URL 重写，请使用脚本添加 404.html 文件，该脚本可处理到 index.html 页的重定向请求 。

关于社区提供的示例实现，请参阅[用于 GitHub 页的单页应用](https://spa-github-pages.rafrex.com/)（[GitHub 上的 rafrex/spa-github-pages](https://github.com/rafrex/spa-github-pages#readme)）。

### <a name="content-root"></a>可在 [GitHub 上的 blazor-demo/blazor-demo.github.io](https://github.com/blazor-demo/blazor-demo.github.io)（[实时站点](https://blazor-demo.github.io/)）查看使用社区方法的示例。

如果使用项目站点而非组织站点，请在 index.html 中添加或更新 `<base>` 标记。 将 `href` 属性值设置为，包含尾部斜杠的 GitHub 存储库名称（例如，`my-repository/`）。

* 主机配置值 在开发环境中，[Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)可以在运行时接受以下主机配置值作为命令行参数。

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* 内容根 `--contentroot` 参数设置包含应用内容文件的目录的绝对路径（[内容根目录](xref:fundamentals/index#content-root)）。

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* 在下面的示例中，`/content-root-path` 是应用的内容根路径。 以本地方式在命令提示符下运行应用时传递该参数。

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>在应用的目录中，执行以下操作：

在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。 如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。 在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。

> [!IMPORTANT]
> 在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。 基路径

* `--pathbase` 参数可设置使用非根相对 URL 路径本地运行的应用的应用基路径（将 `<base>` 标记 `href` 针对暂存和生产设置为 `/` 之外的路径）。 在下面的示例中，`/relative-URL-path` 是应用的基路径。

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* 有关详细信息，请参阅[应用基路径](xref:host-and-deploy/blazor/index#app-base-path)。 不同于向 `href` 标记的 `<base>` 提供的路径，传递 `--pathbase` 参数值时不包括尾部反斜杠 (`/`)。

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* 如果在 `<base>` 标记中以 `<base href="/CoolApp/">` 形式（包括尾部反斜杠）提供应用基路径，则以 `--pathbase=/CoolApp` 形式（无尾部反斜杠）传递命令行参数值。 以本地方式在命令提示符下运行应用时传递该参数。

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>在应用的目录中，执行以下操作：

在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。

* 如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。 在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* 在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。 URL

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* `--urls` 参数设置 IP 地址或主机地址，其中包含侦听请求的端口和协议。 以本地方式在命令提示符下运行应用时传递该参数。

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>在应用的目录中，执行以下操作：

在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。 如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。

## <a name="custom-boot-resource-loading"></a>在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。

在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。 配置链接器

* Blazor 对每个发布版本执行中间语言 (IL) 链接，以从输出程序集中删除不必要的 IL。
* 有关详细信息，请参阅 <xref:host-and-deploy/blazor/configure-linker>。
* 自定义启动资源加载

Blazor WebAssembly 应用可使用 `loadBootResource` 函数进行初始化，以覆盖内置的启动资源加载机制。

| 在以下场景中使用 `loadBootResource`：    | 允许用户从 CDN 加载静态资源，例如时区数据或 dotnet wasm。 |
| ------------ | ----------- |
| `type`       | 使用 HTTP 请求加载压缩的程序集，并将其解压缩到不支持从服务器提取压缩内容的主机的客户端上。 将每个 `fetch` 请求重定向到新名称，从而为资源提供别名。 |
| `name`       | `loadBootResource` 参数如下表所示。 |
| `defaultUri` | 参数 |
| `integrity`  | 描述 |

资源类型。

* 允许使用的类型：`assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata` 资源的名称。

  * 资源的相对或绝对 URI。
  * 表示响应中的预期内容的完整性字符串。
  * `loadBootResource` 返回以下任何内容以替代加载过程：

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* URI 字符串。 在以下示例中 (wwwroot/index.html)，以下文件来自 CDN (`https://my-awesome-cdn.com/`)：

  dotnet.\*.js
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* dotnet.wasm

时区数据 `Promise<Response>`。

在标头中传递 `integrity` 参数以保持默认的完整性检查行为。 以下示例 (wwwroot/index.html) 将一个自定义 HTTP 标头添加到出站请求，并将 `integrity` 参数传递到 `fetch` 调用：

## <a name="change-the-filename-extension-of-dll-files"></a>`null`/`undefined`，这将导致默认的加载行为。

外部源必须返回浏览器所需的 CORS 标头以允许跨域资源加载。

CDN 通常会默认提供所需的标头。 你只需指定自定义行为的类型。

框架会根据默认的加载行为加载未指定到 `loadBootResource` 的类型。

更改 DLL 文件的文件扩展名

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

如果需要更改应用的已发布 .dll 文件的文件扩展名，请按照本部分中的指导进行操作。

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

发布应用后，使用 shell 脚本或 DevOps 生成管道将 .dll 文件重命名，以使用其他文件扩展名。

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

将 .dll 文件的目标位置设为应用的已发布输出的 wwwroot 目录中（例如 {CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot）  。

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
在下面的示例中，重命名 .dll 文件，以使用 .bin 文件扩展名 。

在 Windows 上：

* 如果服务工作进程资产也在使用中，请添加以下命令： 在 Linux 或 macOS 上：
* 如果服务工作进程资产也在使用中，请添加以下命令：

要使用不同于 .bin 的其他文件扩展名，请在前面的命令中替换 .bin 。 要处理压缩的 blazor.boot.json.gz 和 blazor.boot.json.br 文件，请采用以下方法之一 ： 删除压缩的 blazor.boot.json.gz 和 blazor.boot.json.br 文件 。

此方法禁用压缩。

重新压缩更新后的 blazor.boot.json 文件。

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

上述指导也适用于正在使用服务工作进程资产的情况。

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

删除或重新压缩 wwwroot/service-worker-assets.js.br 和 wwwroot/service-worker-assets.js.gz。

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

否则，浏览器中的文件完整性检查将失败。
 
