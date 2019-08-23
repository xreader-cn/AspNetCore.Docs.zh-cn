---
title: ASP.NET Core 的 Docker 映像
author: tdykstra
description: 了解如何使用 Docker 注册表中发布的 .NET Core Docker 映像。 拉取映像并生成你自己的映像。
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/18/2019
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 578f6f8cd54597fe0a6186d182cccc3955331e49
ms.sourcegitcommit: 2fa0ffe82a47c7317efc9ea908365881cbcb8ed7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2019
ms.locfileid: "69572860"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="d82d4-104">ASP.NET Core 的 Docker 映像</span><span class="sxs-lookup"><span data-stu-id="d82d4-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="d82d4-105">本教程演示如何在 Docker 容器中运行 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="d82d4-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="d82d4-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="d82d4-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="d82d4-107">了解 Microsoft.NET 核心 Docker 映像</span><span class="sxs-lookup"><span data-stu-id="d82d4-107">Learn about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="d82d4-108">下载 ASP.NET Core 示例应用</span><span class="sxs-lookup"><span data-stu-id="d82d4-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="d82d4-109">本地运行示例应用</span><span class="sxs-lookup"><span data-stu-id="d82d4-109">Run the sample app locally</span></span>
> * <span data-ttu-id="d82d4-110">在 Linux 容器中运行示例应用</span><span class="sxs-lookup"><span data-stu-id="d82d4-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="d82d4-111">在 Windows 容器中运行示例应用</span><span class="sxs-lookup"><span data-stu-id="d82d4-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="d82d4-112">手动生成和部署</span><span class="sxs-lookup"><span data-stu-id="d82d4-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="d82d4-113">ASP.NET Core Docker 映像</span><span class="sxs-lookup"><span data-stu-id="d82d4-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="d82d4-114">在本教程中，你下载 ASP.NET Core 示例应用并在 Docker 容器中运行它。</span><span class="sxs-lookup"><span data-stu-id="d82d4-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="d82d4-115">此示例适用于 Linux 和 Windows 容器。</span><span class="sxs-lookup"><span data-stu-id="d82d4-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="d82d4-116">示例 Dockerfile 使用 [Docker 多阶段构建功能](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)在不同的容器中生成和运行。</span><span class="sxs-lookup"><span data-stu-id="d82d4-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="d82d4-117">生成和运行容器是由 Microsoft 从 Docker 中心提供的映像中创建的：</span><span class="sxs-lookup"><span data-stu-id="d82d4-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="d82d4-118">此示例将此映像用于生成应用。</span><span class="sxs-lookup"><span data-stu-id="d82d4-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="d82d4-119">此映像包含带有命令行工具 (CLI) 的 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="d82d4-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="d82d4-120">此映像对本地开发、调试和单元测试进行了优化。</span><span class="sxs-lookup"><span data-stu-id="d82d4-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="d82d4-121">为开发和编译而安装的工具使其成为一个相对较大的映像。</span><span class="sxs-lookup"><span data-stu-id="d82d4-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet` 

   <span data-ttu-id="d82d4-122">此示例将此映像用于运行应用。</span><span class="sxs-lookup"><span data-stu-id="d82d4-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="d82d4-123">此映像包含 ASP.NET Core 运行时和库，并针对在生产中运行应用进行了优化。</span><span class="sxs-lookup"><span data-stu-id="d82d4-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="d82d4-124">此映像专为部署和应用启动的速度而设计，相对较小，因此优化了从 Docker 注册表到 Docker 主机的网络性能。</span><span class="sxs-lookup"><span data-stu-id="d82d4-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="d82d4-125">仅将运行应用所需的二进制文件和内容复制到容器。</span><span class="sxs-lookup"><span data-stu-id="d82d4-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="d82d4-126">已准备运行内容，以此实现从 `Docker run` 到应用启动的最快时间。</span><span class="sxs-lookup"><span data-stu-id="d82d4-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="d82d4-127">Docker 模型中不需要动态代码编译。</span><span class="sxs-lookup"><span data-stu-id="d82d4-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d82d4-128">系统必备</span><span class="sxs-lookup"><span data-stu-id="d82d4-128">Prerequisites</span></span>

* [<span data-ttu-id="d82d4-129">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="d82d4-129">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/core)

* <span data-ttu-id="d82d4-130">Docker 客户端 18.03 或更高版本</span><span class="sxs-lookup"><span data-stu-id="d82d4-130">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="d82d4-131">Linux 分布</span><span class="sxs-lookup"><span data-stu-id="d82d4-131">Linux distributions</span></span>
    * [<span data-ttu-id="d82d4-132">CentOS</span><span class="sxs-lookup"><span data-stu-id="d82d4-132">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="d82d4-133">Debian</span><span class="sxs-lookup"><span data-stu-id="d82d4-133">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="d82d4-134">Fedora</span><span class="sxs-lookup"><span data-stu-id="d82d4-134">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="d82d4-135">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="d82d4-135">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="d82d4-136">macOS</span><span class="sxs-lookup"><span data-stu-id="d82d4-136">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="d82d4-137">Windows</span><span class="sxs-lookup"><span data-stu-id="d82d4-137">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="d82d4-138">Git</span><span class="sxs-lookup"><span data-stu-id="d82d4-138">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="d82d4-139">下载示例应用</span><span class="sxs-lookup"><span data-stu-id="d82d4-139">Download the sample app</span></span>

* <span data-ttu-id="d82d4-140">通过克隆 [.NET Core Docker 存储库](https://github.com/dotnet/dotnet-docker)下载示例：</span><span class="sxs-lookup"><span data-stu-id="d82d4-140">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="d82d4-141">本地运行应用</span><span class="sxs-lookup"><span data-stu-id="d82d4-141">Run the app locally</span></span>

* <span data-ttu-id="d82d4-142">导航到 dotnet-docker/samples/aspnetapp/aspnetapp  下的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="d82d4-142">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="d82d4-143">运行以下命令以本地生成并运行应用：</span><span class="sxs-lookup"><span data-stu-id="d82d4-143">Run the following command to build and run the app locally:</span></span>

  ```console
  dotnet run
  ```

* <span data-ttu-id="d82d4-144">在浏览器中转到 `http://localhost:5000` 以测试应用。</span><span class="sxs-lookup"><span data-stu-id="d82d4-144">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="d82d4-145">在命令提示符处按 Ctrl+C 以停止应用。</span><span class="sxs-lookup"><span data-stu-id="d82d4-145">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="d82d4-146">在 Linux 容器中运行</span><span class="sxs-lookup"><span data-stu-id="d82d4-146">Run in a Linux container</span></span>

* <span data-ttu-id="d82d4-147">在 Docker 客户端中，切换到 Linux 容器。</span><span class="sxs-lookup"><span data-stu-id="d82d4-147">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="d82d4-148">导航到 dotnet-docker/samples/aspnetapp  下的 Dockerfile 文件夹。</span><span class="sxs-lookup"><span data-stu-id="d82d4-148">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="d82d4-149">运行以下命令以在 Docker 中生成并运行示例：</span><span class="sxs-lookup"><span data-stu-id="d82d4-149">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="d82d4-150">`build` 命令参数：</span><span class="sxs-lookup"><span data-stu-id="d82d4-150">The `build` command arguments:</span></span>
  * <span data-ttu-id="d82d4-151">将映像命名为 aspnetapp。</span><span class="sxs-lookup"><span data-stu-id="d82d4-151">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="d82d4-152">在当前文件夹（末尾的句点）中查找 Dockerfile。</span><span class="sxs-lookup"><span data-stu-id="d82d4-152">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="d82d4-153">运行命令参数：</span><span class="sxs-lookup"><span data-stu-id="d82d4-153">The run command arguments:</span></span>
  * <span data-ttu-id="d82d4-154">分配伪 TTY，即使未附加也使其保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="d82d4-154">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="d82d4-155">（与 `--interactive --tty` 的效果相同。）</span><span class="sxs-lookup"><span data-stu-id="d82d4-155">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="d82d4-156">容器在退出时自动删除。</span><span class="sxs-lookup"><span data-stu-id="d82d4-156">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="d82d4-157">将本地计算机上的端口 5000 映射到容器中的端口 80。</span><span class="sxs-lookup"><span data-stu-id="d82d4-157">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="d82d4-158">将容器命名为 aspnetcore_sample。</span><span class="sxs-lookup"><span data-stu-id="d82d4-158">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="d82d4-159">指定 aspnetapp 映像。</span><span class="sxs-lookup"><span data-stu-id="d82d4-159">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="d82d4-160">在浏览器中转到 `http://localhost:5000` 以测试应用。</span><span class="sxs-lookup"><span data-stu-id="d82d4-160">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="d82d4-161">在 Windows 容器中运行</span><span class="sxs-lookup"><span data-stu-id="d82d4-161">Run in a Windows container</span></span>

* <span data-ttu-id="d82d4-162">在 Docker 客户端中，切换到 Windows 容器。</span><span class="sxs-lookup"><span data-stu-id="d82d4-162">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="d82d4-163">导航到 `dotnet-docker/samples/aspnetapp` 下的 docker 文件文件夹。</span><span class="sxs-lookup"><span data-stu-id="d82d4-163">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="d82d4-164">运行以下命令以在 Docker 中生成并运行示例：</span><span class="sxs-lookup"><span data-stu-id="d82d4-164">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="d82d4-165">对于 Windows 容器，你需要容器的 IP 地址（浏览到 `http://localhost:5000` 不起作用）：</span><span class="sxs-lookup"><span data-stu-id="d82d4-165">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="d82d4-166">打开另一个命令提示符。</span><span class="sxs-lookup"><span data-stu-id="d82d4-166">Open up another command prompt.</span></span>
  * <span data-ttu-id="d82d4-167">运行 `docker ps` 以查看正在运行的容器。</span><span class="sxs-lookup"><span data-stu-id="d82d4-167">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="d82d4-168">验证其中是否包含“aspnetcore_sample”容器。</span><span class="sxs-lookup"><span data-stu-id="d82d4-168">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="d82d4-169">运行 `docker exec aspnetcore_sample ipconfig` 以显示容器的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="d82d4-169">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="d82d4-170">该命令的输出如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="d82d4-170">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="d82d4-171">将容器 IPv4 地址（例如，172.29.245.43）复制并粘贴到浏览器地址栏以测试应用。</span><span class="sxs-lookup"><span data-stu-id="d82d4-171">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="d82d4-172">手动生成和部署</span><span class="sxs-lookup"><span data-stu-id="d82d4-172">Build and deploy manually</span></span>

<span data-ttu-id="d82d4-173">在某些情况下，你可能希望通过将运行时所需的应用程序文件复制到容器来将应用部署到容器。</span><span class="sxs-lookup"><span data-stu-id="d82d4-173">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="d82d4-174">本部分演示如何手动进行部署。</span><span class="sxs-lookup"><span data-stu-id="d82d4-174">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="d82d4-175">导航到 dotnet-docker/samples/aspnetapp/aspnetapp  下的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="d82d4-175">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="d82d4-176">运行 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令：</span><span class="sxs-lookup"><span data-stu-id="d82d4-176">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```console
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="d82d4-177">命令参数：</span><span class="sxs-lookup"><span data-stu-id="d82d4-177">The command arguments:</span></span>
  * <span data-ttu-id="d82d4-178">在发布模式（默认为调试模式）下生成应用程序。</span><span class="sxs-lookup"><span data-stu-id="d82d4-178">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="d82d4-179">在“已发布”文件夹中创建文件  。</span><span class="sxs-lookup"><span data-stu-id="d82d4-179">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="d82d4-180">运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="d82d4-180">Run the application.</span></span>

  * <span data-ttu-id="d82d4-181">Windows：</span><span class="sxs-lookup"><span data-stu-id="d82d4-181">Windows:</span></span>

    ```console
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="d82d4-182">Linux：</span><span class="sxs-lookup"><span data-stu-id="d82d4-182">Linux:</span></span>

    ```bash
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="d82d4-183">浏览到 `http://localhost:5000` 以查看主页。</span><span class="sxs-lookup"><span data-stu-id="d82d4-183">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="d82d4-184">要在 Docker 容器中使用手动发布的应用程序，请创建新的 Dockerfile，并使用 `docker build .` 命令构建容器。</span><span class="sxs-lookup"><span data-stu-id="d82d4-184">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

```console
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="d82d4-185">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="d82d4-185">The Dockerfile</span></span>

<span data-ttu-id="d82d4-186">下面是先前运行的 `docker build` 命令使用的 Dockerfile。</span><span class="sxs-lookup"><span data-stu-id="d82d4-186">Here's the Dockerfile used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="d82d4-187">它以本部分中所用的方式使用 `dotnet publish` 进行生成和部署。</span><span class="sxs-lookup"><span data-stu-id="d82d4-187">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

## <a name="additional-resources"></a><span data-ttu-id="d82d4-188">其他资源</span><span class="sxs-lookup"><span data-stu-id="d82d4-188">Additional resources</span></span>

* [<span data-ttu-id="d82d4-189">Docker 生成命令</span><span class="sxs-lookup"><span data-stu-id="d82d4-189">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="d82d4-190">Docker 运行命令</span><span class="sxs-lookup"><span data-stu-id="d82d4-190">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="d82d4-191">[ASP.NET Core Docker 示例](https://github.com/dotnet/dotnet-docker)（本教程中使用的示例。）</span><span class="sxs-lookup"><span data-stu-id="d82d4-191">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="d82d4-192">配置 ASP.NET Core 以使用代理服务器和负载均衡器</span><span class="sxs-lookup"><span data-stu-id="d82d4-192">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="d82d4-193">使用 Visual Studio Docker 工具</span><span class="sxs-lookup"><span data-stu-id="d82d4-193">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="d82d4-194">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="d82d4-194">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers) 

## <a name="next-steps"></a><span data-ttu-id="d82d4-195">后续步骤</span><span class="sxs-lookup"><span data-stu-id="d82d4-195">Next steps</span></span>

<span data-ttu-id="d82d4-196">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="d82d4-196">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="d82d4-197">了解 Microsoft .NET Core Docker 映像</span><span class="sxs-lookup"><span data-stu-id="d82d4-197">Learned about Microsoft .NET Core Docker images</span></span> 
> * <span data-ttu-id="d82d4-198">下载 ASP.NET Core 示例应用</span><span class="sxs-lookup"><span data-stu-id="d82d4-198">Downloaded an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="d82d4-199">本地运行示例应用</span><span class="sxs-lookup"><span data-stu-id="d82d4-199">Run the sample app locally</span></span>
> * <span data-ttu-id="d82d4-200">在 Linux 容器中运行示例应用</span><span class="sxs-lookup"><span data-stu-id="d82d4-200">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="d82d4-201">在 Windows 容器中运行示例应用</span><span class="sxs-lookup"><span data-stu-id="d82d4-201">Run the sample with in Windows containers</span></span>
> * <span data-ttu-id="d82d4-202">已手动生成和部署</span><span class="sxs-lookup"><span data-stu-id="d82d4-202">Built and deployed manually</span></span>

<span data-ttu-id="d82d4-203">包含示例应用的 Git 存储库还包括文档。</span><span class="sxs-lookup"><span data-stu-id="d82d4-203">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="d82d4-204">有关存储库中可用资源的概述，请参阅[自述文件](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)。</span><span class="sxs-lookup"><span data-stu-id="d82d4-204">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="d82d4-205">特别是，了解如何实现 HTTPS：</span><span class="sxs-lookup"><span data-stu-id="d82d4-205">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d82d4-206">使用 Docker over HTTPS 开发 ASP.NET Core 应用程序</span><span class="sxs-lookup"><span data-stu-id="d82d4-206">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md)
