---
title: 为 ASP.NET Core Kestrel Web 服务器配置终结点
author: rick-anderson
description: 了解如何为 Kestrel（跨平台 ASP.NET Core Web 服务器）配置终结点。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: 5fec573013da5bcb5039b7a189fd84d964349b3a
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658737"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a>为 ASP.NET Core Kestrel Web 服务器配置终结点

默认情况下，ASP.NET Core 绑定到：

* `http://localhost:5000`
* `https://localhost:5001`（存在本地开发证书时）

使用以下内容指定 URL：

* `ASPNETCORE_URLS` 环境变量。
* `--urls` 命令行参数。
* `urls` 主机配置键。
* `UseUrls` 扩展方法。

采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。 将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。

有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。

关于开发证书的创建：

* 安装 [.NET SDK](/dotnet/core/sdk) 时。
* [dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。

某些浏览器需要授予显式权限才能信任本地开发证书。

项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。

调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 方法以配置 URL 前缀和 Kestrel 的端口。

`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。

`KestrelServerOptions` 配置：

## <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults(Action\<ListenOptions>)

指定一个为每个指定的终结点运行的配置 `Action`。 多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> 通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 创建的终结点将不会应用默认值。

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)

指定一个为每个 HTTPS 终结点运行的配置 `Action`。 多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> 通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 创建的终结点将不会应用默认值。

## <a name="configureiconfiguration"></a>Configure(IConfiguration)

创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。 配置必须针对 Kestrel 的配置节。

`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

将 Kestrel 配置为使用 HTTPS。

`ListenOptions.UseHttps` 扩展：

* `UseHttps`：将 Kestrel 配置为使用 HTTPS，采用默认证书。 如果没有配置默认证书，则会引发异常。
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps` 参数：

* `filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。
* `password` 是访问 X.509 证书数据所需的密码。
* `configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。 返回 `ListenOptions`。
* `storeName` 是从中加载证书的证书存储。
* `subject` 是证书的主题名称。
* `allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。
* `location` 是从中加载证书的存储位置。
* `serverCertificate` 是 X.509 证书。

在生产中，必须显式配置 HTTPS。 至少必须提供默认证书。

下面要描述的支持的配置：

* 无配置
* 从配置中替换默认证书
* 更改代码中的默认值

### <a name="no-configuration"></a>无配置

Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a>从配置中替换默认证书

Kestrel 可以使用默认 HTTPS 应用设置配置架构。 从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。

在以下 appsettings.json 示例中：

* 将 `AllowInvalid` 设置为 `true`，从而允许使用无效证书（例如自签名证书）。
* 任何未指定证书的 HTTPS 终结点（下例中的 `HttpsDefaultCert`）会回退至在 `Certificates:Default` 下定义的证书或开发证书。

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertAndKeyFile": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Path": "<path to .pem/.crt file>",
          "KeyPath": "<path to .key file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5003",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5004"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

架构的注意事项：

* 终结点的名称[不区分大小写](xref:fundamentals/configuration/index#configuration-keys-and-values)。 例如，由于再也无法解析标识符“Families”，因此 `HTTPS` and `Https` 是等效的。
* 每个终结点都要具备 `Url` 参数。 此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。
* 这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。 通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。
* `Certificate` 部分是可选的。 如果未指定 `Certificate` 部分，则使用 `Certificates:Default` 中定义的默认值。 如果没有可用的默认值，则使用开发证书。 如果没有默认值，且开发证书不存在，则服务器将引发异常，并且无法启动。
* `Certificate` 部分支持多个[证书源](#certificate-sources)。
* 只要不会导致端口冲突，就能在[配置](xref:fundamentals/configuration/index)中定义任何数量的终结点。

#### <a name="certificate-sources"></a>证书源

可以将证书节点配置为从多个源加载证书：

* `Path` 和 `Password` 用于加载 .pfx 文件。
* `Path`、`KeyPath` 和 `Password` 用于加载 .pem/ *.crt* 和 .key 文件。
* `Subject` 和 `Store` 用于从证书存储中加载。

例如，可将 `Certificates:Default` 证书指定为：

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a>ConfigurationLoader

`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader>，可以用于补充已配置的终结点设置：

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 提供的加载程序。

* 每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。
* 通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。 只使用最新配置，除非之前的实例上显式调用了 `Load`。 元包不会调用 `Load`，所以可能会替换它的默认配置节。
* `KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。 这些重载不使用名称，且只使用配置中的默认设置。

### <a name="change-the-defaults-in-code"></a>更改代码中的默认值

可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。 需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a>使用服务器名称指示配置终结点

[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。 为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。 在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。

Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。 每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。 可以在项目的 Program.cs 文件的 `ConfigureWebHostDefaults` 方法调用中使用以下回调代码：

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

SNI 支持要求：

* 在目标框架 `netcoreapp2.1` 或更高版本上运行。 在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。 如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。
* 所有网站在相同的 Kestrel 实例上运行。 Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。

## <a name="connection-logging"></a>连接日志记录

调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> 以发出用于进行连接上的字节级别通信的调试级别日志。 连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。 如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。 如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。 这是内置[连接中间件](#connection-middleware)。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a>绑定到 TCP 套接字

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。 可使用相同 API 为特定终结点配置其他 Kestrel 设置。

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a>绑定到 Unix 套接字

可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* 在 Nginx 配置文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。 `{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。
* 确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。

## <a name="port-0"></a>端口 0

如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。 以下示例演示如何确定 Kestrel 在运行时绑定到的端口：

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a>限制

使用以下方法配置终结点：

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* `--urls` 命令行参数
* `urls` 主机配置键
* `ASPNETCORE_URLS` 环境变量

若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。 不过，请注意以下限制：

* HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本文前面的部分所示）。
* 如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。

## <a name="iis-endpoint-configuration"></a>IIS 终结点配置

使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。 有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。

## <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。 从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。

| `HttpProtocols` 枚举值 | 允许的连接协议 |
| -------------------------- | ----------------------------- |
| `Http1`                    | 仅 HTTP/1.1。 可以在具有 TLS 或没有 TLS 的情况下使用。 |
| `Http2`                    | 仅 HTTP/2。 仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。 |
| `Http1AndHttp2`            | HTTP/1.1 和 HTTP/2。 HTTP/2 要求客户端在 TLS [应用层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手过程中选择 HTTP/2；否则，连接默认为 HTTP/1.1。 |

任何终结点的默认 `ListenOptions.Protocols` 值都为 `HttpProtocols.Http1AndHttp2`。

HTTP/2 的 TLS 限制：

* TLS 版本 1.2 或更高版本
* 重新协商已禁用
* 压缩已禁用
* 最小的临时密钥交换大小：
  * 椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;：最小 224 位
  * 有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;：最小 2048 位
* 不禁止密码套件。 

默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。

以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。 TLS 使用提供的证书来保护连接：

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

在 Linux 上，<xref:System.Net.Security.CipherSuitesPolicy> 可用于针对每个连接筛选 TLS 握手：

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a>连接中间件

必要时，自定义连接中间件可以按连接为特定密码筛选 TLS 握手。

下面的示例针对应用不支持的任何密码算法引发 <xref:System.NotSupportedException>。 或者，定义 [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 并将其与可接受的密码套件列表进行比较。

没有哪种加密是使用 [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 密码算法。

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

连接筛选也可以通过 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda 进行配置：

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a>从配置中设置 HTTP 协议

`CreateDefaultBuilder` 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。

以下 appsettings.json 示例将 HTTP/1.1 建立为所有终结点的默认连接协议：

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

以下 appsettings.json 示例将为所有指定终结点建立 HTTP/1.1 连接协议：

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

代码中指定的协议覆盖了由配置设置的值。

## <a name="url-prefixes"></a>URL 前缀

如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。

仅 HTTP URL 前缀是有效的。 使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。

* 包含端口号的 IPv4 地址

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。

* 包含端口号的 IPv6 地址

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。

* 包含端口号的主机名

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  主机名、`*`和 `+` 并不特殊。 没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。 若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或反向代理服务器。 反向代理服务器示例包括 IIS、Nginx 或 Apache。

  > [!WARNING]
  > 采用反向代理配置进行托管需要[主机筛选](xref:fundamentals/servers/kestrel/host-filtering)。

* 包含端口号的主机 `localhost` 名称或包含端口号的环回 IP

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。 如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。 如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。
