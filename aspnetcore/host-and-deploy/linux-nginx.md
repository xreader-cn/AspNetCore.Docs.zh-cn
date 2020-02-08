---
title: 使用 Nginx 在 Linux 上托管 ASP.NET Core
author: guardrex
description: 了解如何在 Ubuntu 16.04 上将 Nginx 设置为反向代理，从而将 HTTP 流量转发到在 Kestrel 上运行的 ASP.NET Core Web 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 1a83b7d1b211862793e3ba086234b97248f9ae70
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928492"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>使用 Nginx 在 Linux 上托管 ASP.NET Core

作者：[Sourabh Shirhatti](https://twitter.com/sshirhatti)

本指南介绍如何在 Ubuntu 16.04 服务器上设置生产就绪 ASP.NET Core 环境。 这些说明可能适用于较新版本的 Ubuntu，但尚未使用较新版本进行测试。

有关 ASP.NET Core 支持的其他 Linux 分配的信息，请参阅 [Linux 上 .NET Core 的先决条件](/dotnet/core/linux-prerequisites)。

> [!NOTE]
> 对于 Ubuntu 14.04，建议进行监控，以此作为监视 Kestrel 进程的解决方案。 在 Ubuntu 14.04 上不提供 systemd。 有关 Ubuntu 14.04 的说明，请参阅[本主题的以前版本](https://github.com/aspnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)。

本指南：

* 将现有 ASP.NET Core 应用置于反向代理服务器后方。
* 设置反向代理服务器，以便将请求转发到 Kestrel Web 服务器。
* 确保 Web 应用在启动时作为守护程序运行。
* 配置进程管理工具以帮助重新启动 Web 应用。

## <a name="prerequisites"></a>先决条件

1. 使用具有 sudo 特权的标准用户帐户访问 Ubuntu 16.04 服务器。
1. 在服务器上安装 .NET Core 运行时。
   1. 访问[下载 .NET Core 页面](https://dotnet.microsoft.com/download/dotnet-core)。
   1. 选择最新的 .NET Core 非预览版。
   1. 在“运行应用”-“运行时”下的表格中，下载最新的非预览版运行时。
   1. 选择 Linux 包管理器说明链接，然后按照 Ubuntu 版本的 Ubuntu 说明进行操作。
1. 一个现有 ASP.NET Core 应用。

升级共享框架后，可随时重启服务器托管的 ASP.NET Core 应用。

## <a name="publish-and-copy-over-the-app"></a>通过应用发布和复制

配置应用以进行[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)。

如果应用在本地运行，且未配置为建立安全连接 (HTTPS)，则采用以下任一方法：

* 配置应用，以处理安全的本地连接。 有关详细信息，请参阅 [HTTPS 配置](#https-configuration)部分。
* 从 Properties/launchSettings.json 文件中的 `applicationUrl` 属性中删除 `https://localhost:5001`（如果存在）。

在开发环境中运行 [dotnet publish](/dotnet/core/tools/dotnet-publish)，将应用打包到可在服务器上运行的目录中（例如 bin/Release/&lt;target_framework_moniker&gt;/publish）：

```dotnetcli
dotnet publish --configuration Release
```

如果不希望维护服务器上的 .NET Core 运行时，还可将应用发布为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)。

使用集成到组织工作流的工具（例如 SCP、SFTP）将 ASP.NET Core 应用复制到服务器。 通常可在 var 目录（例如 var/www/helloapp）下找到 Web 应用。

> [!NOTE]
> 在生产部署方案中，持续集成工作流会执行发布应用并将资产复制到服务器的工作。

测试应用：

1. 在命令行中运行应用：`dotnet <app_assembly>.dll`。
1. 在浏览器中，导航到 `http://<serveraddress>:<port>` 以确认应用在 Linux 本地正常运行。

## <a name="configure-a-reverse-proxy-server"></a>配置反向代理服务器

反向代理是为动态 Web 应用提供服务的常见设置。 反向代理终止 HTTP 请求，并将其转发到 ASP.NET Core 应用。

### <a name="use-a-reverse-proxy-server"></a>使用反向代理服务器

Kestrel 非常适合从 ASP.NET Core 提供动态内容。 但是，Web 服务功能不像服务器（如 IIS、Apache 或 Nginx）那样功能丰富。 反向代理服务器可以卸载 HTTP 服务器的工作负载，如提供静态内容、缓存请求、压缩请求和 HTTPS 终端。 反向代理服务器可能驻留在专用计算机上，也可能与 HTTP 服务器一起部署。

鉴于此指南的目的，使用 Nginx 的单个实例。 它与 HTTP 服务器一起运行在同一服务器上。 根据要求，可以选择不同的设置。

由于请求是通过反向代理转接的，因此使用 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 包中的[转接头中间件](xref:host-and-deploy/proxy-load-balancer)。 此中间件使用 `X-Forwarded-Proto` 标头来更新 `Request.Scheme`，使重定向 URI 和其他安全策略能够正常工作。

调用转接头中间件后，必须放置依赖于该架构的组件，例如身份验证、链接生成、重定向和地理位置。 作为一般规则，转接头中间件应在诊断和错误处理中间件以外的其他中间件之前运行。 此顺序可确保依赖于转接头信息的中间件可以使用标头值进行处理。

在调用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 或类似的身份验证方案中间件之前，调用 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 方法。 配置中间件以转接 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头：

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

如果没有为中间件指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，则要转接的默认标头为 `None`。

默认情况下，在环回地址 (127.0.0.0/8, [::1])（包括标准 localhost 地址 (127.0.0.1)）上运行的代理受信任。 如果组织内的其他受信任代理或网络处理 Internet 与 Web 服务器之间的请求，请使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 将其添加到 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> 或 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> 的列表。 以下示例会将 IP 地址为 10.0.0.100 的受信任代理服务器添加到 `Startup.ConfigureServices` 中的转接头中间件 `KnownProxies`：

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。

### <a name="install-nginx"></a>安装 Nginx

使用 `apt-get` 安装 Nginx。 安装程序将创建一个 systemd init 脚本，该脚本运行 Nginx，作为系统启动时的守护程序。 按照以下网站上的 Ubuntu 安装说明操作：[Nginx：官方 Debian/Ubuntu 包](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)。

> [!NOTE]
> 如果需要可选 Nginx 模块，则可能需要从源代码生成 Nginx。

因为是首次安装 Nginx，通过运行以下命令显式启动：

```bash
sudo service nginx start
```

确认浏览器显示 Nginx 的默认登陆页。 可在 `http://<server_IP_address>/index.nginx-debian.html` 访问登陆页面。

### <a name="configure-nginx"></a>配置 Nginx

若要将 Nginx 配置为反向代理以将请求转接到 ASP.NET Core 应用，请修改 /etc/nginx/sites-available/default。 在文本编辑器中打开它，并将内容替换为以下内容：

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

如果要用是依赖于 SignalR WebSocket 的 Blazor Server 应用，请参阅 <xref:host-and-deploy/blazor/server#linux-with-nginx> 了解如何设置 `Connection` 标头。

当没有匹配的 `server_name` 时，Nginx 使用默认服务器。 如果没有定义默认服务器，则配置文件中的第一台服务器是默认服务器。 作为最佳做法，添加指定默认服务器，它会在配置文件中返回状态代码 444。 默认的服务器配置示例是：

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

使用上述配置文件和默认服务器，Nginx 接受主机标头 `example.com` 或 `*.example.com` 端口 80 上的公共流量。 与这些主机不匹配的请求不会转接到 Kestrel。 Nginx 将匹配的请求转接到 `http://localhost:5000` 中的 Kestrel。 有关详细信息，请参阅 [nginx 如何处理请求](https://nginx.org/docs/http/request_processing.html)。 若要更改 Kestrel 的 IP/端口，请参阅 [Kestrel：终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。

> [!WARNING]
> 未能指定正确的 [server_name 指令](https://nginx.org/docs/http/server_names.html)会公开应用的安全漏洞。 如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.example.com`）不具有此安全风险。 有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。

完成配置 Nginx 后，运行 `sudo nginx -t` 来验证配置文件的语法。 如果配置文件测试成功，可以通过运行 `sudo nginx -s reload` 强制 Nginx 选取更改。

要直接在服务器上运行应用：

1. 请导航到应用目录。
1. 运行应用：`dotnet <app_assembly.dll>`，其中 `app_assembly.dll` 是应用的程序集文件名。

如果应用在服务器上运行，但无法通过 Internet 响应，请检查服务器的防火墙，并确认端口 80 已打开。 如果使用 Azure Ubuntu VM，请添加启用入站端口 80 流量的网络安全组 (NSG) 规则。 不需要启用出站端口 80 规则，因为启用入站规则后会自动许可出站流量。

测试应用完成后，请在命令提示符处按 `Ctrl+C` 关闭应用。

## <a name="monitor-the-app"></a>监视应用

服务器设置为将对 `http://<serveraddress>:80` 发起的请求转接到在 `http://127.0.0.1:5000` 中的 Kestrel 上运行的 ASP.NET Core 应用。 但是，未将 Nginx 设置为管理 Kestrel 进程。 systemd 可用于创建服务文件以启动和监视基础 Web 应用。 systemd 是一个 init 系统，可以提供用于启动、停止和管理进程的许多强大的功能。 

### <a name="create-the-service-file"></a>创建服务文件

创建服务定义文件：

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

以下是应用的一个示例服务文件：

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

在前面的示例中，管理服务的用户由 `User` 选项指定。 用户 (`www-data`) 必须存在并且拥有正确应用文件的所有权。

使用 `TimeoutStopSec` 配置在收到初始中断信号后等待应用程序关闭的持续时间。 如果应用程序在此时间段内未关闭，则将发出 SIGKILL 以终止该应用程序。 提供作为无单位秒数的值（例如，`150`）、时间跨度值（例如，`2min 30s`）或 `infinity` 以禁用超时。 `TimeoutStopSec` 默认为管理器配置文件（*systemd-system.conf*、*system.conf.d*、*systemd-user.conf*、*user.conf.d*）中 `DefaultTimeoutStopSec` 的值。 大多数分发版的默认超时时间为 90 秒。

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux 具有区分大小写的文件系统。 将 ASPNETCORE_ENVIRONMENT 设置为“生产”会导致搜索配置文件 appsettings.Production.json，而不是 appsettings.production.json。

必须转义某些值（例如，SQL 连接字符串）以供配置提供程序读取环境变量。 使用以下命令生成适当的转义值以供在配置文件中使用：

```console
systemd-escape "<value-to-escape>"
```

环境变量名不支持冒号 (`:`) 分隔符。 使用双下划线 (`__`) 代替冒号。 环境变量读入配置时，[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)将双下划线转换为冒号。 以下示例中，连接字符串密钥 `ConnectionStrings:DefaultConnection` 以 `ConnectionStrings__DefaultConnection` 形式设置到服务定义文件中：

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

保存该文件并启用该服务。

```bash
sudo systemctl enable kestrel-helloapp.service
```

启用该服务，并确认它正在运行。

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

在配置了反向代理并通过 systemd 管理 Kestrel 后，Web 应用现已完全配置，并能在本地计算机上的浏览器中从 `http://localhost` 进行访问。 也可以从远程计算机进行访问，同时限制可能进行阻止的任何防火墙。 检查响应标头，`Server` 标头显示由 Kestrel 所提供的 ASP.NET Core 应用。

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>查看日志

使用 Kestrel 的 Web 应用是通过 `systemd` 进行管理的，因此所有事件和进程都被记录到集中日志。 但是，此日志包含由 `systemd` 管理的所有服务和进程的全部条目。 若要查看特定于 `kestrel-helloapp.service` 的项，请使用以下命令：

```bash
sudo journalctl -fu kestrel-helloapp.service
```

有关进一步筛选，使用时间选项（如 `--since today`、`--until 1 hour ago`）或这些选项的组合可以减少返回的条目数。

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>数据保护

[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)（包括 cookie 中间件等身份验证中间件）和跨站点请求伪造 (CSRF) 保护使用。 即使用户代码不调用数据保护 API，也应该配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。 如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。

如果密钥环存储于内存中，则在应用重启时：

* 所有基于 cookie 的身份验证令牌都无效。
* 用户需要在下一次请求时再次登录。
* 无法再解密使用密钥环保护的任何数据。 这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。

若要配置数据保护以持久保存并加密密钥环，请参阅：

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a>较长的请求标头字段

代理服务器默认设置通常将请求标头字段限制为 4 K 或 8 K，具体取决于平台。 某些应用可能需要超过默认值的字段（例如，使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 的应用）。 如果需要更长的字段，则代理服务器的默认设置需要进行调整。 要应用的值具体取决于方案。 有关详细信息，请参见服务器文档。

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> 除非必要，否则不要提高代理缓冲区的默认值。 提高这些值将增加缓冲区溢出的风险和恶意用户的拒绝服务 (DoS) 攻击风险。

## <a name="secure-the-app"></a>保护应用

### <a name="enable-apparmor"></a>启用 AppArmor

Linux 安全模块 (LSM) 是一个框架，它是自 Linux 2.6 后的 Linux kernel 的一部分。 LSM 支持安全模块的不同实现。 [AppArmor](https://wiki.ubuntu.com/AppArmor) 是实现强制访问控制系统的 LSM，它允许将程序限制在一组有限的资源内。 确保已启用并成功配置 AppArmor。

### <a name="configure-the-firewall"></a>配置防火墙

关闭所有未使用的外部端口。 通过为配置防火墙提供 CLI，不复杂的防火墙 (ufw) 为 `iptables` 提供了前端。

> [!WARNING]
> 如果未正确配置，防火墙将阻止对整个系统的访问。 在使用 SSH 进行连接时，未能指定正确的 SSH 端口最终会将你关在系统之外。 默认端口为 22。 有关详细信息，请参阅 [ufw 简介](https://help.ubuntu.com/community/UFW)和[手册](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)。

安装 `ufw`，并将其配置为允许所需任何端口上的流量。

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>保护 Nginx

#### <a name="change-the-nginx-response-name"></a>更改 Nginx 响应名称

编辑 src/http/ngx_http_header_filter_module.c：

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>配置选项

用其他必需模块配置服务器。 请考虑使用 [ModSecurity](https://www.modsecurity.org/) 等 Web 应用防火墙来加强对应用的保护。

#### <a name="https-configuration"></a>HTTPS 配置

配置应用，以进行安全的 (HTTPS) 本地连接

[dotnet run](/dotnet/core/tools/dotnet-run) 命令使用应用的 Properties/launchSettings.json 文件，该文件将应用配置为侦听 `applicationUrl` 属性（例如 `https://localhost:5001;http://localhost:5000`）提供的 URL。

使用以下方法之一配置应用，使其在开发过程中将证书用于 `dotnet run` 命令或开发环境（Visual Studio Code 中的 F5 或 Ctrl+F5）：

* [从配置中替换默认证书](xref:fundamentals/servers/kestrel#configuration)（推荐）
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

配置反向代理，以便进行安全 (HTTPS) 客户端连接

* 通过指定由受信任的证书颁发机构 (CA) 颁发的有效证书来配置服务器，以侦听端口 `443` 上的 HTTPS 流量。

* 通过采用以下“/etc/nginx/nginx.conf”文件中所示的某些做法来增强安全保护。 示例包括选择更强的密码并将通过 HTTP 的所有流量重定向到 HTTPS。

* 添加 `HTTP Strict-Transport-Security` (HSTS) 标头可确保由客户端发起的所有后续请求都通过 HTTPS。

* 如果以后会禁用 HTTPS，请勿添加 HSTS 头或选择相应的 `max-age`。

添加 /etc/nginx/proxy.conf 配置文件：

[!code-nginx[](linux-nginx/proxy.conf)]

编辑 /etc/nginx/nginx.conf 配置文件。 示例包含一个配置文件中的 `http` 和 `server` 部分。

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>保护 Nginx 免受点击劫持的侵害

[点击劫持](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)（也称为 *UI 伪装攻击*）是一种恶意攻击，其中网站访问者会上当受骗，从而导致在与当前要访问的页面不同的页面上单击链接或按钮。 使用 `X-FRAME-OPTIONS` 可保护网站。

缓解点击劫持攻击：

1. 编辑 nginx.conf 文件：

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   添加行 `add_header X-Frame-Options "SAMEORIGIN";`。
1. 保存该文件。
1. 重启 Nginx。

#### <a name="mime-type-sniffing"></a>MIME 类型探查

此标头可阻止大部分浏览器通过 MIME 方式探查来自已声明内容类型的响应，因为标头会指示浏览器不要替代响应内容类型。 使用 `nosniff` 选项后，如果服务器认为内容是“文本/html”，则浏览器将其显示为“文本/html”。

编辑 nginx.conf 文件：

```bash
sudo nano /etc/nginx/nginx.conf
```

添加行 `add_header X-Content-Type-Options "nosniff";` 并保存文件，然后重新启动 Nginx。

## <a name="additional-nginx-suggestions"></a>其他 Nginx 建议

在服务器上升级共享框架后，重启服务器托管的 ASP.NET Core 应用。

## <a name="additional-resources"></a>其他资源

* [Linux 上 .NET Core 的先决条件](/dotnet/core/linux-prerequisites)
* [Nginx：二进制版本：官方 Debian/Ubuntu 包](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX：使用转接头](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
