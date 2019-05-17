---
title: ASP.NET Core 的 Docker 映像
author: tdykstra
description: 了解如何使用 Docker 注册表中发布的 .NET Core Docker 映像。 拉取映像并生成你自己的映像。
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/09/2019
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 48fc53a4c2139960c0f696af5732ff68fc6c4b8a
ms.sourcegitcommit: a3926eae3f687013027a2828830c12a89add701f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2019
ms.locfileid: "65451008"
---
# <a name="docker-images-for-aspnet-core"></a>ASP.NET Core 的 Docker 映像

本教程演示如何在 Docker 容器中运行 ASP.NET Core 应用。

在本教程中，你将了解：
> [!div class="checklist"]
> * 了解 Microsoft.NET 核心 Docker 映像 
> * 下载 ASP.NET Core 示例应用
> * 本地运行示例应用
> * 在 Linux 容器中运行示例应用
> * 在 Windows 容器中运行示例应用
> * 手动生成和部署

## <a name="aspnet-core-docker-images"></a>ASP.NET Core Docker 映像

在本教程中，你下载 ASP.NET Core 示例应用并在 Docker 容器中运行它。 此示例适用于 Linux 和 Windows 容器。

示例 Dockerfile 使用 [Docker 多阶段构建功能](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)在不同的容器中生成和运行。 生成和运行容器是由 Microsoft 从 Docker 中心提供的映像中创建的：

* `dotnet/core/sdk`

  此示例将此映像用于生成应用。 此映像包含带有命令行工具 (CLI) 的 .NET Core SDK。 此映像对本地开发、调试和单元测试进行了优化。 为开发和编译而安装的工具使其成为一个相对较大的映像。 

* `dotnet/core/aspnet` 

   此示例将此映像用于运行应用。 此映像包含 ASP.NET Core 运行时和库，并针对在生产中运行应用进行了优化。 此映像专为部署和应用启动的速度而设计，相对较小，因此优化了从 Docker 注册表到 Docker 主机的网络性能。 仅将运行应用所需的二进制文件和内容复制到容器。 已准备运行内容，以此实现从 `Docker run` 到应用启动的最快时间。 Docker 模型中不需要动态代码编译。

## <a name="prerequisites"></a>系统必备

* [.NET Core 2.2 SDK](https://www.microsoft.com/net/core)

* Docker 客户端 18.03 或更高版本

  * Linux 分布
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>下载示例应用

* 通过克隆 [.NET Core Docker 存储库](https://github.com/dotnet/dotnet-docker)下载示例： 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>本地运行应用

* 导航到 dotnet-docker/samples/aspnetapp/aspnetapp 下的项目文件夹。

* 运行以下命令以本地生成并运行应用：

  ```console
  dotnet run
  ```

* 在浏览器中转到 `http://localhost:5000` 以测试应用。

* 在命令提示符处按 Ctrl+C 以停止应用。

## <a name="run-in-a-linux-container"></a>在 Linux 容器中运行

* 在 Docker 客户端中，切换到 Linux 容器。

* 导航到 dotnet-docker/samples/aspnetapp 下的 Dockerfile 文件夹。

* 运行以下命令以在 Docker 中生成并运行示例：

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  `build` 命令参数：
  * 将映像命名为 aspnetapp。
  * 在当前文件夹（末尾的句点）中查找 Dockerfile。

  运行命令参数：
  * 分配伪 TTY，即使未附加也使其保持打开状态。 （与 `--interactive --tty` 的效果相同。）
  * 容器在退出时自动删除。
  * 将本地计算机上的端口 5000 映射到容器中的端口 80。
  * 将容器命名为 aspnetcore_sample。
  * 指定 aspnetapp 映像。

* 在浏览器中转到 `http://localhost:5000` 以测试应用。

## <a name="run-in-a-windows-container"></a>在 Windows 容器中运行

* 在 Docker 客户端中，切换到 Windows 容器。

导航到 `dotnet-docker/samples/aspnetapp` 下的 docker 文件文件夹。

* 运行以下命令以在 Docker 中生成并运行示例：

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* 对于 Windows 容器，你需要容器的 IP 地址（浏览到 `http://localhost:5000` 不起作用）：
  * 打开另一个命令提示符。
  * 运行 `docker ps` 以查看正在运行的容器。 验证其中是否包含“aspnetcore_sample”容器。
  * 运行 `docker exec aspnetcore_sample ipconfig` 以显示容器的 IP 地址。 该命令的输出如以下示例所示：

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* 将容器 IPv4 地址（例如，172.29.245.43）复制并粘贴到浏览器地址栏以测试应用。

## <a name="build-and-deploy-manually"></a>手动生成和部署

在某些情况下，你可能希望通过将运行时所需的应用程序文件复制到容器来将应用部署到容器。 本部分演示如何手动进行部署。

* 导航到 dotnet-docker/samples/aspnetapp/aspnetapp 下的项目文件夹。

* 运行 [dotnet publish](https://docs.microsoft.com/dotnet/core/tools/dotnet-publish.md) 命令：

  ```console
  dotnet publish -c Release -o published
  ```

  命令参数：
  * 在发布模式（默认为调试模式）下生成应用程序。
  * 在“已发布”文件夹中创建文件。

* 运行该应用程序。

  * Windows：

    ```console
    dotnet published\aspnetapp.dll
    ```

  * Linux：

    ```bash
    dotnet published/aspnetapp.dll
    ```

* 浏览到 `http://localhost:5000` 以查看主页。

### <a name="the-dockerfile"></a>Dockerfile

下面是先前运行的 `docker build` 命令使用的 Dockerfile。  它以本部分中所用的方式使用 `dotnet publish` 进行生成和部署。  

```console
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a>其他资源

* [Docker 生成命令](https://docs.docker.com/engine/reference/commandline/build)
* [Docker 运行命令](https://docs.docker.com/engine/reference/commandline/run)
* [ASP.NET Core Docker 示例](https://github.com/dotnet/dotnet-docker)（本教程中使用的示例。）
* [配置 ASP.NET Core 以使用代理服务器和负载均衡器](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [使用 Visual Studio Docker 工具](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [使用 Visual Studio Code 进行调试](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers) 

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：
> [!div class="checklist"]
> * 了解 Microsoft .NET Core Docker 映像 
> * 下载 ASP.NET Core 示例应用
> * 本地运行示例应用
> * 在 Linux 容器中运行示例应用
> * 在 Windows 容器中运行示例应用
> * 已手动生成和部署

包含示例应用的 Git 存储库还包括文档。 有关存储库中可用资源的概述，请参阅[自述文件](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)。 特别是，了解如何实现 HTTPS：

> [!div class="nextstepaction"]
> [使用 Docker over HTTPS 开发 ASP.NET Core 应用程序](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md)
