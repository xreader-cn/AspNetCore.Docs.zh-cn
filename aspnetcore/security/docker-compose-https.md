---
title: 使用使用 HTTPS 编写的 docker 在容器中托管ASP.NET核心映像
author: ravipal
description: 了解如何通过 HTTPS 使用 Docker 合成托管ASP.NET核心映像
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Let's Encrypt
uid: security/docker-compose-https
ms.openlocfilehash: 616ccf906e98534ffda08c0c2b6d0a171f063cc1
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381808"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>托管ASP.NET核心映像与 Docker 组成通过 HTTPS


ASP.NET核心默认情况下使用[HTTPS。](/aspnet/core/security/enforcing-ssl) [HTTPS](https://en.wikipedia.org/wiki/HTTPS)依赖于[证书](https://en.wikipedia.org/wiki/Public_key_certificate)进行信任、标识和加密。

本文档介绍如何使用 HTTPS 运行预构建的容器映像。

有关开发方案[，请参阅使用 Docker 在 HTTPS 上开发ASP.NET核心应用程序](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)。

此示例需要[Docker 17.06](https://docs.docker.com/release-notes/docker-ce)或更高版本的[Docker 客户端](https://www.docker.com/products/docker)。

## <a name="prerequisites"></a>先决条件

本文档中的某些说明需要[.NET Core 2.2 SDK](https://dotnet.microsoft.com/download)或更高版本。

## <a name="certificates"></a>证书

域[的生产托管](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)需要[证书颁发机构的](https://wikipedia.org/wiki/Certificate_authority)证书。 [Let's Encrypt](https://letsencrypt.org/)是提供免费证书的证书颁发机构。

本文档使用[自签名开发证书](https://wikipedia.org/wiki/Self-signed_certificate)在 上`localhost`托管预构建的图像。 这些说明类似于使用生产证书。

对于生产证书：

* 该工具`dotnet dev-certs`不是必需的。
* 证书不需要存储在说明中使用的位置。 将证书存储在站点目录之外的任何位置。

以下部分卷中包含的说明使用*docker-compose.yml*中的`volumes`属性将证书装入容器中。 您可以使用`COPY`*Dockerfile*中的命令将证书添加到容器映像中，但不建议这样做。 出于以下原因，不建议将证书复制到映像：

* 这使得使用同一映像使用开发人员证书进行测试变得困难。
* 它使使用同一映像进行生产证书托管变得困难。
* 证书披露存在重大风险。

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>使用 docker 组合使用 https 支持启动容器

对操作系统配置使用以下说明。

### <a name="windows-using-linux-containers"></a>使用 Linux 容器的窗口

生成证书并配置本地计算机：

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

在前面的命令中，替换为`{ password here }`密码。

创建包含以下内容的_docker-compose.debug.yml_文件：

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
docker 撰写文件中指定的密码必须与用于证书的密码匹配。

使用为 HTTPS 配置ASP.NET核心启动容器：

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS 或 Linux

生成证书并配置本地计算机：

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`仅在 macOS 和 Windows 上受支持。 您需要以发行版支持的方式信任 Linux 上的证书。 您可能需要在浏览器中信任证书。

在前面的命令中，替换为`{ password here }`密码。

创建包含以下内容的_docker-compose.debug.yml_文件：

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
docker 撰写文件中指定的密码必须与用于证书的密码匹配。

使用为 HTTPS 配置ASP.NET核心启动容器：

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>使用 Windows 容器的窗口

生成证书并配置本地计算机：

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

在前面的命令中，替换为`{ password here }`密码。

创建包含以下内容的_docker-compose.debug.yml_文件：

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
docker 撰写文件中指定的密码必须与用于证书的密码匹配。

使用为 HTTPS 配置ASP.NET核心启动容器：

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
