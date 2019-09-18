---
title: 通过 HTTPS 在 Docker 上宿主 ASP.NET Core 映像
author: rick-anderson
description: 了解如何通过 HTTPS 在 Docker 上托管 ASP.NET Core 映像
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
uid: security/docker-https
ms.openlocfilehash: c13ba02845eef5c53a939feec2be8a01bc4ca128
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082532"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="558bb-103">通过 HTTPS 在 Docker 上宿主 ASP.NET Core 映像</span><span class="sxs-lookup"><span data-stu-id="558bb-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="558bb-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="558bb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="558bb-105">[默认情况下](/aspnet/core/security/enforcing-ssl)，ASP.NET Core 使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="558bb-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="558bb-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS)依赖于信任、标识和加密的[证书](https://en.wikipedia.org/wiki/Public_key_certificate)。</span><span class="sxs-lookup"><span data-stu-id="558bb-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="558bb-107">本文档介绍如何通过 HTTPS 运行预生成的容器映像。</span><span class="sxs-lookup"><span data-stu-id="558bb-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="558bb-108">若要开发方案，请参阅[通过 HTTPS 上的 Docker 开发 ASP.NET Core 应用程序](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md)。</span><span class="sxs-lookup"><span data-stu-id="558bb-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="558bb-109">此示例需要 docker [17.06](https://docs.docker.com/release-notes/docker-ce)或更高版本的[docker 客户端](https://www.docker.com/products/docker)。</span><span class="sxs-lookup"><span data-stu-id="558bb-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="558bb-110">先决条件</span><span class="sxs-lookup"><span data-stu-id="558bb-110">Prerequisites</span></span>

<span data-ttu-id="558bb-111">本文档中的某些说明需要[.Net Core 2.2 SDK](https://www.microsoft.com/net/download)或更高版本。</span><span class="sxs-lookup"><span data-stu-id="558bb-111">The [.NET Core 2.2 SDK](https://www.microsoft.com/net/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="558bb-112">证书</span><span class="sxs-lookup"><span data-stu-id="558bb-112">Certificates</span></span>

<span data-ttu-id="558bb-113">针对域的[生产主机](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)需要[证书颁发机构颁发](https://en.wikipedia.org/wiki/Certificate_authority)的证书。</span><span class="sxs-lookup"><span data-stu-id="558bb-113">A certificate from a [certificate authority](https://en.wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span>  <span data-ttu-id="558bb-114">[我们的加密](https://letsencrypt.org/)是提供免费证书的证书颁发机构。</span><span class="sxs-lookup"><span data-stu-id="558bb-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="558bb-115">本文档使用[自签名开发证书](https://en.wikipedia.org/wiki/Self-signed_certificate)来托管预生成的映像`localhost`。</span><span class="sxs-lookup"><span data-stu-id="558bb-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="558bb-116">说明类似于使用生产证书。</span><span class="sxs-lookup"><span data-stu-id="558bb-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="558bb-117">对于生产证书：</span><span class="sxs-lookup"><span data-stu-id="558bb-117">For production certs:</span></span>

* <span data-ttu-id="558bb-118">此`dotnet dev-certs`工具不是必需的。</span><span class="sxs-lookup"><span data-stu-id="558bb-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="558bb-119">证书不需要存储在说明中使用的位置。</span><span class="sxs-lookup"><span data-stu-id="558bb-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="558bb-120">尽管不建议在网站目录中存储证书，但任何位置都应有效。</span><span class="sxs-lookup"><span data-stu-id="558bb-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="558bb-121">说明卷将证书装载到容器中。</span><span class="sxs-lookup"><span data-stu-id="558bb-121">The instructions volume mount certificates into containers.</span></span> <span data-ttu-id="558bb-122">可以使用 Dockerfile 中的`COPY`命令将证书添加到容器映像中。</span><span class="sxs-lookup"><span data-stu-id="558bb-122">You can add certificates into container images with a `COPY` command in a Dockerfile.</span></span> <span data-ttu-id="558bb-123">建议不要将证书复制到映像中：</span><span class="sxs-lookup"><span data-stu-id="558bb-123">Copying certificates into an image is not recommended:</span></span>

* <span data-ttu-id="558bb-124">使用同一个映像测试开发人员证书非常困难。</span><span class="sxs-lookup"><span data-stu-id="558bb-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="558bb-125">使用同一个映像通过生产证书进行托管很困难。</span><span class="sxs-lookup"><span data-stu-id="558bb-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="558bb-126">证书泄露存在重大风险。</span><span class="sxs-lookup"><span data-stu-id="558bb-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="558bb-127">用 HTTPS 运行预生成的容器映像</span><span class="sxs-lookup"><span data-stu-id="558bb-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="558bb-128">使用以下有关操作系统配置的说明。</span><span class="sxs-lookup"><span data-stu-id="558bb-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="558bb-129">使用 Linux 容器的 Windows</span><span class="sxs-lookup"><span data-stu-id="558bb-129">Windows using Linux containers</span></span>

<span data-ttu-id="558bb-130">生成证书并配置本地计算机：</span><span class="sxs-lookup"><span data-stu-id="558bb-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="558bb-131">在上述命令中，将`{ password here }`替换为密码。</span><span class="sxs-lookup"><span data-stu-id="558bb-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="558bb-132">运行容器映像，并为 HTTPS 配置 ASP.NET Core：</span><span class="sxs-lookup"><span data-stu-id="558bb-132">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="558bb-133">密码必须与用于证书的密码匹配。</span><span class="sxs-lookup"><span data-stu-id="558bb-133">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="558bb-134">macOS 或 Linux</span><span class="sxs-lookup"><span data-stu-id="558bb-134">macOS or Linux</span></span>

<span data-ttu-id="558bb-135">生成证书并配置本地计算机：</span><span class="sxs-lookup"><span data-stu-id="558bb-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="558bb-136">`dotnet dev-certs https --trust`仅在 macOS 和 Windows 上受支持。</span><span class="sxs-lookup"><span data-stu-id="558bb-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="558bb-137">你需要以发行版支持的方式信任 Linux 上的证书。</span><span class="sxs-lookup"><span data-stu-id="558bb-137">You need to trust certs on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="558bb-138">可能需要在浏览器中信任该证书。</span><span class="sxs-lookup"><span data-stu-id="558bb-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="558bb-139">在上述命令中，将`{ password here }`替换为密码。</span><span class="sxs-lookup"><span data-stu-id="558bb-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="558bb-140">运行容器映像，并为 HTTPS 配置 ASP.NET Core：</span><span class="sxs-lookup"><span data-stu-id="558bb-140">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="558bb-141">密码必须与用于证书的密码匹配。</span><span class="sxs-lookup"><span data-stu-id="558bb-141">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="558bb-142">使用 Windows 容器的 windows</span><span class="sxs-lookup"><span data-stu-id="558bb-142">Windows using Windows containers</span></span>

<span data-ttu-id="558bb-143">生成证书并配置本地计算机：</span><span class="sxs-lookup"><span data-stu-id="558bb-143">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="558bb-144">在上述命令中，将`{ password here }`替换为密码。</span><span class="sxs-lookup"><span data-stu-id="558bb-144">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="558bb-145">运行容器映像，并为 HTTPS 配置 ASP.NET Core：</span><span class="sxs-lookup"><span data-stu-id="558bb-145">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="558bb-146">密码必须与用于证书的密码匹配。</span><span class="sxs-lookup"><span data-stu-id="558bb-146">The password must match the password used for the certificate.</span></span>