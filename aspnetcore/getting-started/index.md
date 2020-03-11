---
title: ASP.NET Core 入门
author: rick-anderson
description: 介绍如何使用 ASP.NET Core 创建并运行简单的 Hello World 应用的快速教程。
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
uid: getting-started
ms.openlocfilehash: 047fd7a74d3d53f68a730d67b63c65fe6bda529f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644304"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="3d832-103">教程：ASP.NET Core 入门</span><span class="sxs-lookup"><span data-stu-id="3d832-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="3d832-104">本教程介绍如何使用 .NET Core CLI 创建并运行 ASP.NET Core Web 应用。</span><span class="sxs-lookup"><span data-stu-id="3d832-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="3d832-105">你将了解如何：</span><span class="sxs-lookup"><span data-stu-id="3d832-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3d832-106">创建 Web 应用项目。</span><span class="sxs-lookup"><span data-stu-id="3d832-106">Create a web app project.</span></span>
> * <span data-ttu-id="3d832-107">信任开发证书。</span><span class="sxs-lookup"><span data-stu-id="3d832-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="3d832-108">运行应用。</span><span class="sxs-lookup"><span data-stu-id="3d832-108">Run the app.</span></span>
> * <span data-ttu-id="3d832-109">编辑 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="3d832-109">Edit a Razor page.</span></span>

<span data-ttu-id="3d832-110">最后，在本地计算机上运行工作 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="3d832-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Web 应用主页](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="3d832-112">先决条件</span><span class="sxs-lookup"><span data-stu-id="3d832-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="3d832-113">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="3d832-113">Create a web app project</span></span>

<span data-ttu-id="3d832-114">打开命令行界面，然后输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="3d832-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="3d832-115">上面的命令：</span><span class="sxs-lookup"><span data-stu-id="3d832-115">The preceding command:</span></span>

* <span data-ttu-id="3d832-116">创建新 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="3d832-116">Creates a new web app.</span></span>  
* <span data-ttu-id="3d832-117">`-o aspnetcoreapp` 参数使用应用的源文件创建名为 aspnetcoreapp  的目录。</span><span class="sxs-lookup"><span data-stu-id="3d832-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="3d832-118">信任开发证书</span><span class="sxs-lookup"><span data-stu-id="3d832-118">Trust the development certificate</span></span>

<span data-ttu-id="3d832-119">信任 HTTPS 开发证书：</span><span class="sxs-lookup"><span data-stu-id="3d832-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="3d832-120">Windows</span><span class="sxs-lookup"><span data-stu-id="3d832-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="3d832-121">以上命令会显示以下对话：</span><span class="sxs-lookup"><span data-stu-id="3d832-121">The preceding command displays the following dialog:</span></span>

![安全警告对话](~/getting-started/_static/cert.png)

<span data-ttu-id="3d832-123">如果你同意信任开发证书，请选择“是”。 </span><span class="sxs-lookup"><span data-stu-id="3d832-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="3d832-124">macOS</span><span class="sxs-lookup"><span data-stu-id="3d832-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="3d832-125">以上命令会显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="3d832-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="3d832-126">*请求信任 HTTPS 开发证书。如果证书尚不受信任，我们将运行以下命令：* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="3d832-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="3d832-127">此命令可能会提示你输入密码以在系统密钥链上安装证书。</span><span class="sxs-lookup"><span data-stu-id="3d832-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="3d832-128">如果你同意信任开发证书，请输入密码。</span><span class="sxs-lookup"><span data-stu-id="3d832-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="3d832-129">Linux</span><span class="sxs-lookup"><span data-stu-id="3d832-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="3d832-130">查看你的 Linux 分发对应的文档，了解如何信任 HTTPS 开发证书。</span><span class="sxs-lookup"><span data-stu-id="3d832-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="3d832-131">有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="3d832-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="3d832-132">运行应用</span><span class="sxs-lookup"><span data-stu-id="3d832-132">Run the app</span></span>

<span data-ttu-id="3d832-133">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3d832-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="3d832-134">在命令行界面指明应用已启动后，转到 [https://localhost:5001](https://localhost:5001)。</span><span class="sxs-lookup"><span data-stu-id="3d832-134">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="3d832-135">编辑 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="3d832-135">Edit a Razor page</span></span>

<span data-ttu-id="3d832-136">打开 Pages/Index.cshtml  ，并使用以下突出显示标记修改并保存页面：</span><span class="sxs-lookup"><span data-stu-id="3d832-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="3d832-137">浏览到 [https://localhost:5001](https://localhost:5001)，刷新页面并验证更改是否显示。</span><span class="sxs-lookup"><span data-stu-id="3d832-137">Browse to [https://localhost:5001](https://localhost:5001), refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3d832-138">后续步骤</span><span class="sxs-lookup"><span data-stu-id="3d832-138">Next steps</span></span>

<span data-ttu-id="3d832-139">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="3d832-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3d832-140">创建 Web 应用项目。</span><span class="sxs-lookup"><span data-stu-id="3d832-140">Create a web app project.</span></span>
> * <span data-ttu-id="3d832-141">信任开发证书。</span><span class="sxs-lookup"><span data-stu-id="3d832-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="3d832-142">运行该项目。</span><span class="sxs-lookup"><span data-stu-id="3d832-142">Run the project.</span></span>
> * <span data-ttu-id="3d832-143">进行更改。</span><span class="sxs-lookup"><span data-stu-id="3d832-143">Make a change.</span></span>

<span data-ttu-id="3d832-144">要了解有关 ASP.NET Core 的详细信息，请参阅简介中推荐的学习路径：</span><span class="sxs-lookup"><span data-stu-id="3d832-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
