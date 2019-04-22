---
title: 使用 Apache 在 Linux 上托管 ASP.NET Core
author: guardrex
description: 了解如何在 CentOS 上将 Apache 设置为反向代理服务器，以将 HTTP 流量重定向到在 Kestrel 上运行的 ASP.NET Core Web 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: spboyer
ms.custom: mvc
ms.date: 03/31/2019
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 34da0653ff29acf3044e69e032307d1a3da7044a
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "58809271"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>使用 Apache 在 Linux 上托管 ASP.NET Core

作者：[Shayne Boyer](https://github.com/spboyer)

使用本指南，了解如何在 [CentOS 7](https://www.centos.org/) 上将 [Apache](https://httpd.apache.org/) 设置为反向代理服务器，以将 HTTP 流量重定向到在 [Kestrel](xref:fundamentals/servers/kestrel) 服务器上运行的 ASP.NET Core Web 应用。 [mod_proxy extension](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) 和相关模块可创建服务器的反向代理。

## <a name="prerequisites"></a>系统必备

* 运行 CentOS 7 的服务器，使用具有 sudo 特权的标准用户帐户。
* 在服务器上安装 .NET Core 运行时。
   1. 访问 [.NET Core“所有下载”页](https://www.microsoft.com/net/download/all)。
   1. 从“运行时”下的列表中选择最新的非预览运行时。
   1. 选择并执行适用于 CentOS/Oracle 的说明。
* 一个现有 ASP.NET Core 应用。

## <a name="publish-and-copy-over-the-app"></a>通过应用发布和复制

配置应用以进行[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)。

如果应用在本地运行，且未配置为建立安全连接 (HTTPS)，则采用以下任一方法：

* 配置应用，以处理安全的本地连接。 有关详细信息，请参阅 [HTTPS 配置](#https-configuration)部分。
* 从 Properties/launchSettings.json 文件中的 `applicationUrl` 属性中删除 `https://localhost:5001`（如果存在）。

在开发环境中运行 [dotnet publish](/dotnet/core/tools/dotnet-publish)，将应用打包到可在服务器上运行的目录中（例如 bin/Release/&lt;target_framework_moniker&gt;/publish）：

```console
dotnet publish --configuration Release
```

如果不希望维护服务器上的 .NET Core 运行时，还可将应用发布为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)。

使用集成到组织工作流的工具（例如 SCP、SFTP）将 ASP.NET Core 应用复制到服务器。 通常可在 var 目录（例如 var/www/helloapp）下找到 Web 应用。

> [!NOTE]
> 在生产部署方案中，持续集成工作流会执行发布应用并将资产复制到服务器的工作。

## <a name="configure-a-proxy-server"></a>配置代理服务器

反向代理是为动态 Web 应用提供服务的常见设置。 反向代理终止 HTTP 请求，并将其转发到 ASP.NET 应用。

代理服务器将客户端请求转发到另一个服务器，而不是自身实现这些请求。 反向代理转发到固定的目标，通常代表任意客户端。 在本指南中，Apache 被配置为反向代理，在 Kestrel 为 ASP.NET Core 应用提供服务的同一服务器上运行。

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

有关更多信息，请参见<xref:host-and-deploy/proxy-load-balancer>。

### <a name="install-apache"></a>安装 Apache

将 CentOS 包更新为其最新稳定版本：

```bash
sudo yum update -y
```

使用单个 `yum` 命令在 CentOS 上安装 Apache Web 服务器：

```bash
sudo yum -y install httpd mod_ssl
```

运行该命令后的示例输出：

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> 在此示例中，输出反映了 httpd.86_64，因为 CentOS 7 版本是 64 位的。 若要验证 Apache 的安装位置，请从命令提示符运行 `whereis httpd`。

### <a name="configure-apache"></a>配置 Apache

Apache 的配置文件位于 `/etc/httpd/conf.d/` 目录内。 除了 `/etc/httpd/conf.modules.d/` 中的模块配置文件外（其中包含加载模块所需的任何配置文件），将对任何带 .conf 扩展名的文件按字母顺序进行处理。

为应用创建名为 helloapp.conf 的配置文件：

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

`VirtualHost` 块可以在服务器上的一个或多个文件中多次出现。 在前面的配置文件中，Apache 接受端口 80 上的公共流量。 正在向域 `www.example.com` 提供服务，`*.example.com` 别名解析为同一网站。 有关详细信息，请参阅[基于名称的虚拟主机支持](https://httpd.apache.org/docs/current/vhosts/name-based.html)。 请求会通过代理从根位置转到 127.0.0.1 处的服务器的端口 5000。 对于双向通信，需要 `ProxyPass` 和 `ProxyPassReverse`。 若要更改 Kestrel 的 IP/端口，请参阅 [Kestrel：终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。

> [!WARNING]
> 未能指定 VirtualHost 块中的正确 [ServerName 指令](https://httpd.apache.org/docs/current/mod/core.html#servername)会公开应用的安全漏洞。 如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.example.com`）不具有此安全风险。 有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。

可以使用 `ErrorLog` 和 `CustomLog` 指令配置每个 `VirtualHost` 的日志记录。 `ErrorLog` 是服务器记录错误的位置，`CustomLog` 则设置文件名和日志文件的格式。 在这种情况下，这是记录请求信息的位置。 每个请求将各占一行。

保存文件，并测试配置。 如果一切正常，响应应为 `Syntax [OK]`。

```bash
sudo service httpd configtest
```

重新启动 Apache：

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>监视应用

Apache 现在已设置为将对 `http://localhost:80` 发起的请求转发到运行在 `http://127.0.0.1:5000` 处的 Kestrel 上的 ASP.NET Core 应用。 但是，未将 Apache 设置为管理 Kestrel 进程。 使用 systemd，并创建服务文件以启动和监视基础 Web 应用。 systemd 是一个 init 系统，可以提供用于启动、停止和管理进程的许多强大的功能。

### <a name="create-the-service-file"></a>创建服务文件

创建服务定义文件：

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

应用的一个示例服务文件：

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

如果配置未使用用户 *apache*，则必须先创建用户，并为该用户提供适当的文件所有权。

使用 `TimeoutStopSec` 配置在收到初始中断信号后等待应用程序关闭的持续时间。 如果应用程序在此时间段内未关闭，则将发出 SIGKILL 以终止该应用程序。 提供作为无单位秒数的值（例如，`150`）、时间跨度值（例如，`2min 30s`）或 `infinity` 以禁用超时。 `TimeoutStopSec` 默认为管理器配置文件（*systemd-system.conf*、*system.conf.d*、*systemd-user.conf*、*user.conf.d*）中 `DefaultTimeoutStopSec` 的值。 大多数分发版的默认超时时间为 90 秒。

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

必须转义某些值（例如，SQL 连接字符串）以供配置提供程序读取环境变量。 使用以下命令生成适当的转义值以供在配置文件中使用：

```console
systemd-escape "<value-to-escape>"
```

环境变量名不支持冒号 (`:`) 分隔符。 使用双下划线 (`__`) 代替冒号。 环境变量读入配置时，[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)将双下划线转换为冒号。 以下示例中，连接字符串密钥 `ConnectionStrings:DefaultConnection` 以 `ConnectionStrings__DefaultConnection` 形式设置到服务定义文件中：

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

保存该文件并启用该服务：

```bash
sudo systemctl enable kestrel-helloapp.service
```

启动该服务，并确认它正在运行：

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

在配置了反向代理并通过 systemd 管理 Kestrel 后，Web 应用现已完全配置，并能在本地计算机上的浏览器中从 `http://localhost` 进行访问。 检查响应标头，服务器标头表示 ASP.NET Core 应用由 Kestrel 提供服务：

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>查看日志

由于使用 Kestrel 的 Web 应用是通过 systemd 进行管理的，因此事件和进程将记录到集中日志。 但是，此日志包含由 systemd 管理的所有服务和进程的条目。 若要查看特定于 `kestrel-helloapp.service` 的项，请使用以下命令：

```bash
sudo journalctl -fu kestrel-helloapp.service
```

若要进行时间筛选，请使用命令指定时间选项。 例如，使用 `--since today` 筛选出当天或 `--until 1 hour ago` 来查看前一小时的条目。 有关详细信息，请参阅 [journalctl 手册页](https://www.unix.com/man-page/centos/1/journalctl/)。

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

## <a name="secure-the-app"></a>保护应用

### <a name="configure-firewall"></a>配置防火墙

*Firewalld* 是管理防火墙的动态守护程序，支持网络区域。 仍可以使用 iptable 管理端口和数据包筛选。 默认情况下应安装 *Firewalld*。 `yum` 可用于安装包或验证是否已安装。

```bash
sudo yum install firewalld -y
```

使用 `firewalld` 仅打开应用所需的端口。 在此示例中，使用的是端口 80 和 443。 以下命令将端口 80 和 443 永久设置为打开：

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

重新加载防火墙设置。 检查默认区域中可用的服务和端口。 通过检查 `firewall-cmd -h` 获取可用选项。

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a>HTTPS 配置

配置应用，以进行安全的 (HTTPS) 本地连接

[dotnet run](/dotnet/core/tools/dotnet-run) 命令使用应用的 Properties/launchSettings.json 文件，该文件将应用配置为侦听 `applicationUrl` 属性（例如 `https://localhost:5001; http://localhost:5000`）提供的 URL。

使用以下方法之一配置应用，使其在开发过程中将证书用于 `dotnet run` 命令或开发环境（Visual Studio Code 中的 F5 或 Ctrl+F5）：

* [从配置中替换默认证书](xref:fundamentals/servers/kestrel#configuration)（推荐）
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

配置反向代理，以便进行安全 (HTTPS) 客户端连接

若要为 Apache 配置 HTTPS，请使用 mod_ssl 模块。 安装了 httpd 模块时，也会安装了 mod_ssl 模块。 如果未安装，请使用 `yum` 将其添加到配置。

```bash
sudo yum install mod_ssl
```

若要强制使用 HTTPS，请安装 `mod_rewrite` 模块以启用 URL 重写：

```bash
sudo yum install mod_rewrite
```

修改 helloapp.conf 文件以启用 URL 重写和端口 443 上的安全通信：

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> 此示例中使用了本地生成的证书。 SSLCertificateFile 应为域名的主证书文件。 SSLCertificateKeyFile 应为创建 CSR 时生成的密钥文件。 SSLCertificateChainFile 应为证书颁发机构提供的中间证书文件（如有）。

保存文件，并测试配置：

```bash
sudo service httpd configtest
```

重新启动 Apache：

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>其他 Apache 建议

### <a name="additional-headers"></a>其他标头

为了防止恶意攻击，应对一些标头进行修改或添加一些标头。 确保已安装 `mod_headers` 模块：

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>保护 Apache 免受点击劫持攻击

[点击劫持](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)（也称为 *UI 伪装攻击*）是一种恶意攻击，其中网站访问者会上当受骗，从而导致在与当前要访问的页面不同的页面上单击链接或按钮。 使用 `X-FRAME-OPTIONS` 可保护网站。

缓解点击劫持攻击：

1. 编辑 *httpd.conf* 文件：

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   添加行 `Header append X-FRAME-OPTIONS "SAMEORIGIN"`。
1. 保存该文件。
1. 重启 Apache。

#### <a name="mime-type-sniffing"></a>MIME 类型探查

`X-Content-Type-Options` 标头阻止 Internet Explorer 进行 *MIME 探查*（从文件内容中确定文件的 `Content-Type`）。 如果服务器通过设置 `nosniff` 选项将 `Content-Type` 标头设置为 `text/html`，则不管文件内容为何，Internet Explorer 都会将内容呈现为 `text/html`。

编辑 *httpd.conf* 文件：

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

添加行 `Header set X-Content-Type-Options "nosniff"`。 保存该文件。 重启 Apache。

### <a name="load-balancing"></a>负载平衡

此示例演示如何在同一实例计算机上的 CentOS 7 和 Kestrel 上设置和配置 Apache。 为了不出现单一故障点；使用 mod_proxy_balancer 并修改 VirtualHost 可实现在 Apache 代理服务器后方管理 Web 应用的多个实例。

```bash
sudo yum install mod_proxy_balancer
```

在下面所示的配置文件中，`helloapp` 的其他实例设置为在端口 5001 上运行。 “代理”部分设置了具有两个成员的均衡器配置，以便对 byrequests 进行负载均衡。

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>速率限制

使用 httpd 模块中包含的 mod_ratelimit，客户端的带宽可以限制为：

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

示例文件将根位置下的带宽限制为 600 KB/秒：

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>较长的请求标头字段

如果应用需要的请求标头字段超过代理服务器的默认设置允许的长度（通常为 8,190 字节），请调整 [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) 指令的值。 要应用的值依赖应用场景。 有关详细信息，请参见服务器文档。

> [!WARNING]
> 除非必要，否则不要提高 `LimitRequestFieldSize` 的默认值。 提高该值将增加缓冲区溢出的风险和恶意用户的拒绝服务 (DoS) 攻击风险。

## <a name="additional-resources"></a>其他资源

* [Linux 上 .NET Core 的先决条件](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
