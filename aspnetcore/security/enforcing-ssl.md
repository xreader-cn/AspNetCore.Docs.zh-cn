---
title: 强制实施 HTTPS 在 ASP.NET Core
author: rick-anderson
description: 了解如何在 ASP.NET Core web 应用中需要 HTTPS/TLS。
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: security/enforcing-ssl
ms.openlocfilehash: 16cfa672fe4a81d9e8f09fc3dd1e6c036edd4c4e
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208971"
---
# <a name="enforce-https-in-aspnet-core"></a>强制实施 HTTPS 在 ASP.NET Core

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本文档演示如何：

* 要求所有请求使用 HTTPS。
* 将所有 HTTP 请求重都定向到 HTTPS。

没有 API 可以防止客户端上的第一个请求发送敏感数据。

> [!WARNING]
> 不要**不**使用[RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute)接收敏感信息的 Web api。 `RequireHttpsAttribute` 使用 HTTP 状态代码将从 HTTP 到 HTTPS 的浏览器重定向。 API 客户端可能无法理解或遵循从 HTTP 到 HTTPS 的重定向。 此类客户端可能会通过 HTTP 发送的信息。 Web Api 应具有下列任一：
>
> * 不侦听 HTTP。
> * 关闭与状态代码 400 （错误请求） 的连接并不为请求提供服务。

## <a name="require-https"></a>要求使用 HTTPS

::: moniker range=">= aspnetcore-2.1"

我们建议该生产 ASP.NET Core web 应用调用：

* HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) 将 HTTP 请求重定向到 HTTPS。
* HSTS 中间件 ([UseHsts](#http-strict-transport-security-protocol-hsts)) 发送给客户端的 HTTP 严格传输安全性协议 (HSTS) 标头。

> [!NOTE]
> 反向代理配置中部署的应用程序允许代理在处理连接安全 (HTTPS)。 如果代理还处理 HTTPS 的重定向，则无需使用 HTTPS 重定向中间件。 如果代理服务器还处理编写 HSTS 标头 (例如，[本机 HSTS 支持 IIS 10.0 (1709) 或更高版本](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support))，HSTS 中间件不所需的应用程序。 有关详细信息，请参阅[退出的 HTTPS/HSTS 在项目创建](#opt-out-of-httpshsts-on-project-creation)。

### <a name="usehttpsredirection"></a>UseHttpsRedirection

下面的代码调用`UseHttpsRedirection`在`Startup`类：

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=13)]

前面突出显示的代码：

* 使用默认[HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect))。
* 使用默认[HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport)除非被重写 (null)`ASPNETCORE_HTTPS_PORT`环境变量或[IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)。

我们建议使用临时重定向而不是永久重定向。 链接缓存可以在开发环境中导致不稳定行为。 如果想要发送的永久重定向状态代码时该应用位于非开发环境，请参阅[配置在生产环境中的永久重定向](#configure-permanent-redirects-in-production)部分。 我们建议使用[HSTS](#http-strict-transport-security-protocol-hsts)将信号发送到仅保护资源的客户端请求应发送到应用 （仅在生产中）。

### <a name="port-configuration"></a>端口配置

端口必须是可用于中间件将不安全的请求重定向到 HTTPS。 如果没有端口不可用：

* 不会发生重定向到 HTTPS。
* 中间件将记录警告"无法确定重定向的 https 端口。"

指定使用任何以下方法之一的 HTTPS 端口：

* 设置[HttpsRedirectionOptions.HttpsPort](#options)。
* 设置`ASPNETCORE_HTTPS_PORT`环境变量或[https_port Web 主机配置设置](xref:fundamentals/host/web-host#https-port):

  **密钥**: `https_port`  
  **类型**：string  
  **默认**：未设置默认值。  
  **设置使用**：`UseSetting`  
  **环境变量**:`<PREFIX_>HTTPS_PORT` (该前缀`ASPNETCORE_`使用时[Web 主机](xref:fundamentals/host/web-host)。)

  配置时<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>在`Program`:

  [!code-csharp[](enforcing-ssl/sample-snapshot/Program.cs?name=snippet_Program&highlight=10)]
* 指示具有安全方案使用的端口`ASPNETCORE_URLS`环境变量。 将服务器配置为环境变量。 中间件间接发现通过 HTTPS 端口<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>。 反向代理部署这种方法不起作用。
* 在开发中，在中设置 HTTPS URL *launchsettings.json*。 使用 IIS Express 时，请启用 HTTPS。
* 配置 HTTPS URL 终结点的面向公众 edge 部署[Kestrel](xref:fundamentals/servers/kestrel)服务器或[HTTP.sys](xref:fundamentals/servers/httpsys)服务器。 仅**一个 HTTPS 端口**应用使用。 中间件发现通过端口<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>。

> [!NOTE]
> 当应用在反向代理配置中，运行时<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>不可用。 设置使用一种其他方法在本部分中所述的端口。

当 Kestrel 或 HTTP.sys 用作面向公众的边缘服务器时，必须配置 Kestrel 或 HTTP.sys，若要在其上同时侦听：

* 其中重定向客户端的安全端口 (通常情况下，在生产和开发中的为 5001 的 443)。
* 不安全端口 (通常情况下，在生产环境中为 80) 和 5000 开发中。

不安全的端口必须可由客户端按顺序应用接收不安全的请求，并将客户端重定向到安全的端口。

有关详细信息，请参阅[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)或<xref:fundamentals/servers/httpsys>。

### <a name="deployment-scenarios"></a>部署方案

客户端和服务器之间的所有防火墙也都必须打开的流量的通信端口。

如果反向代理配置中将请求转发，则使用[转发头中间件](xref:host-and-deploy/proxy-load-balancer)调用前 HTTPS 重定向中间件。 转发头中间件更新`Request.Scheme`，并使用`X-Forwarded-Proto`标头。 中间件允许重定向 Uri 和其他安全策略才能正常工作。 转发头中间件不使用时后, 端应用程序可能不接收正确的方案和最终会在重定向循环。 常见的最终用户错误消息是已发生过多的重定向。

部署到 Azure 应用服务时，请按照中的指导[教程：将现有自定义 SSL 证书绑定到 Azure Web 应用](/azure/app-service/app-service-web-tutorial-custom-ssl)。

### <a name="options"></a>选项

以下突出显示的代码调用[AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection)配置中间件选项：

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=14-99)]

调用`AddHttpsRedirection`只是用来更改的值`HttpsPort`或`RedirectStatusCode`。

前面突出显示的代码：

* 集[HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*)到<xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>，这是默认值。 使用的字段<xref:Microsoft.AspNetCore.Http.StatusCodes>分配到的类`RedirectStatusCode`。
* 设置 HTTPS 端口为 5001。 默认值为 443。

#### <a name="configure-permanent-redirects-in-production"></a>在生产环境中配置永久重定向

中间件默认为发送[Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)与所有重定向。 如果想要发送的永久重定向状态代码，非开发环境中应用程序时，包装在对非开发环境的条件检查中的中间件的选项配置。

配置时`IWebHostBuilder`中*Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

## <a name="https-redirection-middleware-alternative-approach"></a>HTTPS 重定向中间件另一种方法

使用 HTTPS 重定向中间件的替代方法 (`UseHttpsRedirection`) 是使用 URL 重写中间件 (`AddRedirectToHttps`)。 `AddRedirectToHttps` 此外可以设置的状态代码和端口时执行重定向。 有关详细信息，请参阅[URL 重写中间件](xref:fundamentals/url-rewriting)。

当将重定向到 HTTPS 而无需其他重定向规则，我们建议使用 HTTPS 重定向中间件 (`UseHttpsRedirection`) 本主题中所述。

::: moniker-end

::: moniker range="< aspnetcore-2.1"

[RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute)用于要求 HTTPS。 `[RequireHttpsAttribute]` 可以修饰控制器或方法，也可以全局应用。 若要全局应用该属性，将以下代码添加到`ConfigureServices`在`Startup`:

[!code-csharp[](~/security/authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-999)]

前面突出显示的代码要求所有请求都使用`HTTPS`; 因此，HTTP 请求将被忽略。 以下突出显示的代码将所有 HTTP 请求重都定向到 HTTPS:

[!code-csharp[](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-999)]

有关详细信息，请参阅[URL 重写中间件](xref:fundamentals/url-rewriting)。 中间件还允许应用执行重定向时设置的状态代码或状态代码和端口。

全局需要 HTTPS (`options.Filters.Add(new RequireHttpsAttribute());`) 是最佳安全方案。 将应用`[RequireHttps]`属性设置为所有控制器/Razor 页面不会被视为与需要 HTTPS 全局一样安全。 不能保证`[RequireHttps]`时添加新的控制器和 Razor 页面应用属性。

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="http-strict-transport-security-protocol-hsts"></a>HTTP 严格传输安全协议 (HSTS)

每个[OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)， [HTTP 严格传输安全性 (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet)是通过响应标头使用的 web 应用指定选择的安全增强功能。 当[浏览器支持 HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)收到此标头：

* 在浏览器将会阻止通过 HTTP 发送的任何通信的域的配置存储。 在浏览器强制通过 HTTPS 进行的所有通信。
* 在浏览器可防止用户使用不受信任或无效的证书。 在浏览器禁用允许用户暂时信任此证书的提示。

因为由客户端强制执行 HSTS 它具有一些限制：

* 客户端必须支持 HSTS。
* HSTS 要求至少一个成功的 HTTPS 请求能够建立 HSTS 策略。
* 应用程序必须检查每个 HTTP 请求和重定向或拒绝的 HTTP 请求。

ASP.NET Core 2.1 或更高版本实现与 HSTS`UseHsts`扩展方法。 下面的代码调用`UseHsts`时应用不在[开发模式](xref:fundamentals/environments):

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=10)]

`UseHsts` 不建议在开发过程中由于 HSTS 设置高度可缓存的浏览器。 默认情况下，`UseHsts`排除本地环回地址。

对于生产环境首次实现 HTTPS 设置初始[HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*)为使用其中一个较小值<xref:System.TimeSpan>方法。 设置的值从小时数不超过一天的以防到时需要还原 HTTP 到 HTTPS 基础结构。 确信 HTTPS 配置的可持续发展中后，增加 HSTS 最大期限值;常用的值为一年。

下面的代码：

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=5-12)]

* 设置预加载严格传输安全性标头的参数。 预加载不属于[RFC HSTS 规范](https://tools.ietf.org/html/rfc6797)，但要预加载 HSTS 站点上执行全新安装的 web 浏览器支持。 有关详细信息，请参阅 [https://hstspreload.org/](https://hstspreload.org/)。
* 使[includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2)，这将 HSTS 策略应用到主机的子域。
* 显式将严格传输安全性标头的最大期限参数设置为 60 天。 如果未设置，默认值为 30 天。 请参阅[最大期限指令](https://tools.ietf.org/html/rfc6797#section-6.1.1)有关详细信息。
* 添加`example.com`到主机以排除列表。

`UseHsts` 不包括以下环回主机：

* `localhost`：IPv4 环回地址。
* `127.0.0.1`：IPv4 环回地址。
* `[::1]`：IPv6 环回地址。

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="opt-out-of-httpshsts-on-project-creation"></a>选择退出的 HTTPS/HSTS 在项目创建

在其中连接安全处理面向公众边缘网络的某些后端服务情况下，无需在每个节点上配置连接安全。 Web 应用从 Visual Studio 中或从模板生成[dotnet 新](/dotnet/core/tools/dotnet-new)命令启用[HTTPS 的重定向](#require-https)并[HSTS](#http-strict-transport-security-protocol-hsts)。 对于不需要这些方案的部署，你可以选择退出的 HTTPS/HSTS 时从模板创建的应用。

若要选择退出的 HTTPS/HSTS:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

取消选中**配置为支持 HTTPS**复选框。

![新 ASP.NET Core Web 应用程序对话框中显示为 HTTPS 复选框未选定的配置。](enforcing-ssl/_static/out.png)

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli) 

使用 `--no-https` 选项。 例如

```console
dotnet new webapp --no-https
```

---

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>信任在 Windows 和 macOS 上的 ASP.NET Core HTTPS 开发证书

.NET core SDK 包括 HTTPS 开发证书。 首次运行体验的一部分安装证书。 例如，`dotnet --info`生成类似于以下输出：

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

安装.NET Core SDK 将 ASP.NET Core HTTPS 开发证书安装到本地用户证书存储区。 已安装证书，但它具有不受信任。 若要信任该证书执行一次性步骤，运行 dotnet`dev-certs`工具：

```console
dotnet dev-certs https --trust
```

下面的命令上提供帮助`dev-certs`工具：

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>如何设置适用于 Docker 的开发人员证书

请参阅[此 GitHub 问题](https://github.com/aspnet/Docs/issues/6199)。

::: moniker-end

## <a name="additional-information"></a>其他信息

* <xref:host-and-deploy/proxy-load-balancer>
* [使用 Apache 在 Linux 上托管 ASP.NET Core:HTTPS 配置](xref:host-and-deploy/linux-apache#https-configuration)
* [使用 Nginx 在 Linux 上托管 ASP.NET Core:HTTPS 配置](xref:host-and-deploy/linux-nginx#https-configuration)
* [如何在 IIS 上设置 SSL](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [OWASP HSTS 浏览器支持](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
