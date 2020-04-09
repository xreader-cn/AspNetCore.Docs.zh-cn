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
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="c5f92-103">托管ASP.NET核心映像与 Docker 组成通过 HTTPS</span><span class="sxs-lookup"><span data-stu-id="c5f92-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="c5f92-104">ASP.NET核心默认情况下使用[HTTPS。](/aspnet/core/security/enforcing-ssl)</span><span class="sxs-lookup"><span data-stu-id="c5f92-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="c5f92-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS)依赖于[证书](https://en.wikipedia.org/wiki/Public_key_certificate)进行信任、标识和加密。</span><span class="sxs-lookup"><span data-stu-id="c5f92-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="c5f92-106">本文档介绍如何使用 HTTPS 运行预构建的容器映像。</span><span class="sxs-lookup"><span data-stu-id="c5f92-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="c5f92-107">有关开发方案[，请参阅使用 Docker 在 HTTPS 上开发ASP.NET核心应用程序](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)。</span><span class="sxs-lookup"><span data-stu-id="c5f92-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="c5f92-108">此示例需要[Docker 17.06](https://docs.docker.com/release-notes/docker-ce)或更高版本的[Docker 客户端](https://www.docker.com/products/docker)。</span><span class="sxs-lookup"><span data-stu-id="c5f92-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c5f92-109">先决条件</span><span class="sxs-lookup"><span data-stu-id="c5f92-109">Prerequisites</span></span>

<span data-ttu-id="c5f92-110">本文档中的某些说明需要[.NET Core 2.2 SDK](https://dotnet.microsoft.com/download)或更高版本。</span><span class="sxs-lookup"><span data-stu-id="c5f92-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="c5f92-111">证书</span><span class="sxs-lookup"><span data-stu-id="c5f92-111">Certificates</span></span>

<span data-ttu-id="c5f92-112">域[的生产托管](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)需要[证书颁发机构的](https://wikipedia.org/wiki/Certificate_authority)证书。</span><span class="sxs-lookup"><span data-stu-id="c5f92-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="c5f92-113">[Let's Encrypt](https://letsencrypt.org/)是提供免费证书的证书颁发机构。</span><span class="sxs-lookup"><span data-stu-id="c5f92-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="c5f92-114">本文档使用[自签名开发证书](https://wikipedia.org/wiki/Self-signed_certificate)在 上`localhost`托管预构建的图像。</span><span class="sxs-lookup"><span data-stu-id="c5f92-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="c5f92-115">这些说明类似于使用生产证书。</span><span class="sxs-lookup"><span data-stu-id="c5f92-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="c5f92-116">对于生产证书：</span><span class="sxs-lookup"><span data-stu-id="c5f92-116">For production certificates:</span></span>

* <span data-ttu-id="c5f92-117">该工具`dotnet dev-certs`不是必需的。</span><span class="sxs-lookup"><span data-stu-id="c5f92-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="c5f92-118">证书不需要存储在说明中使用的位置。</span><span class="sxs-lookup"><span data-stu-id="c5f92-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="c5f92-119">将证书存储在站点目录之外的任何位置。</span><span class="sxs-lookup"><span data-stu-id="c5f92-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="c5f92-120">以下部分卷中包含的说明使用*docker-compose.yml*中的`volumes`属性将证书装入容器中。</span><span class="sxs-lookup"><span data-stu-id="c5f92-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="c5f92-121">您可以使用`COPY`*Dockerfile*中的命令将证书添加到容器映像中，但不建议这样做。</span><span class="sxs-lookup"><span data-stu-id="c5f92-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="c5f92-122">出于以下原因，不建议将证书复制到映像：</span><span class="sxs-lookup"><span data-stu-id="c5f92-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="c5f92-123">这使得使用同一映像使用开发人员证书进行测试变得困难。</span><span class="sxs-lookup"><span data-stu-id="c5f92-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="c5f92-124">它使使用同一映像进行生产证书托管变得困难。</span><span class="sxs-lookup"><span data-stu-id="c5f92-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="c5f92-125">证书披露存在重大风险。</span><span class="sxs-lookup"><span data-stu-id="c5f92-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="c5f92-126">使用 docker 组合使用 https 支持启动容器</span><span class="sxs-lookup"><span data-stu-id="c5f92-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="c5f92-127">对操作系统配置使用以下说明。</span><span class="sxs-lookup"><span data-stu-id="c5f92-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="c5f92-128">使用 Linux 容器的窗口</span><span class="sxs-lookup"><span data-stu-id="c5f92-128">Windows using Linux containers</span></span>

<span data-ttu-id="c5f92-129">生成证书并配置本地计算机：</span><span class="sxs-lookup"><span data-stu-id="c5f92-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="c5f92-130">在前面的命令中，替换为`{ password here }`密码。</span><span class="sxs-lookup"><span data-stu-id="c5f92-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="c5f92-131">创建包含以下内容的_docker-compose.debug.yml_文件：</span><span class="sxs-lookup"><span data-stu-id="c5f92-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="c5f92-132">docker 撰写文件中指定的密码必须与用于证书的密码匹配。</span><span class="sxs-lookup"><span data-stu-id="c5f92-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="c5f92-133">使用为 HTTPS 配置ASP.NET核心启动容器：</span><span class="sxs-lookup"><span data-stu-id="c5f92-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="c5f92-134">macOS 或 Linux</span><span class="sxs-lookup"><span data-stu-id="c5f92-134">macOS or Linux</span></span>

<span data-ttu-id="c5f92-135">生成证书并配置本地计算机：</span><span class="sxs-lookup"><span data-stu-id="c5f92-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="c5f92-136">`dotnet dev-certs https --trust`仅在 macOS 和 Windows 上受支持。</span><span class="sxs-lookup"><span data-stu-id="c5f92-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="c5f92-137">您需要以发行版支持的方式信任 Linux 上的证书。</span><span class="sxs-lookup"><span data-stu-id="c5f92-137">You need to trust certificates on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="c5f92-138">您可能需要在浏览器中信任证书。</span><span class="sxs-lookup"><span data-stu-id="c5f92-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="c5f92-139">在前面的命令中，替换为`{ password here }`密码。</span><span class="sxs-lookup"><span data-stu-id="c5f92-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="c5f92-140">创建包含以下内容的_docker-compose.debug.yml_文件：</span><span class="sxs-lookup"><span data-stu-id="c5f92-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="c5f92-141">docker 撰写文件中指定的密码必须与用于证书的密码匹配。</span><span class="sxs-lookup"><span data-stu-id="c5f92-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="c5f92-142">使用为 HTTPS 配置ASP.NET核心启动容器：</span><span class="sxs-lookup"><span data-stu-id="c5f92-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="c5f92-143">使用 Windows 容器的窗口</span><span class="sxs-lookup"><span data-stu-id="c5f92-143">Windows using Windows containers</span></span>

<span data-ttu-id="c5f92-144">生成证书并配置本地计算机：</span><span class="sxs-lookup"><span data-stu-id="c5f92-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="c5f92-145">在前面的命令中，替换为`{ password here }`密码。</span><span class="sxs-lookup"><span data-stu-id="c5f92-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="c5f92-146">创建包含以下内容的_docker-compose.debug.yml_文件：</span><span class="sxs-lookup"><span data-stu-id="c5f92-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

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
<span data-ttu-id="c5f92-147">docker 撰写文件中指定的密码必须与用于证书的密码匹配。</span><span class="sxs-lookup"><span data-stu-id="c5f92-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="c5f92-148">使用为 HTTPS 配置ASP.NET核心启动容器：</span><span class="sxs-lookup"><span data-stu-id="c5f92-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
