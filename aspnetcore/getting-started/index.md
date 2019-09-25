---
title: ASP.NET Core 入门
author: rick-anderson
description: 介绍如何使用 ASP.NET Core 创建并运行简单的 Hello World 应用的快速教程。
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: getting-started
ms.openlocfilehash: 0f05ab120d64832a4bc2fd70921efc7238ee9eac
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187067"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="5e8af-103">教程：ASP.NET Core 入门</span><span class="sxs-lookup"><span data-stu-id="5e8af-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="5e8af-104">本教程展示了如何使用 .NET Core 命令行接口来创建并运行 ASP.NET Core Web 应用。</span><span class="sxs-lookup"><span data-stu-id="5e8af-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="5e8af-105">你将了解如何：</span><span class="sxs-lookup"><span data-stu-id="5e8af-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5e8af-106">创建 Web 应用项目。</span><span class="sxs-lookup"><span data-stu-id="5e8af-106">Create a web app project.</span></span>
> * <span data-ttu-id="5e8af-107">信任开发证书。</span><span class="sxs-lookup"><span data-stu-id="5e8af-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="5e8af-108">运行应用。</span><span class="sxs-lookup"><span data-stu-id="5e8af-108">Run the app.</span></span>
> * <span data-ttu-id="5e8af-109">编辑 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="5e8af-109">Edit a Razor page.</span></span>

<span data-ttu-id="5e8af-110">最后，在本地计算机上运行工作 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="5e8af-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Web 应用主页](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="5e8af-112">系统必备</span><span class="sxs-lookup"><span data-stu-id="5e8af-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.0-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="5e8af-113">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="5e8af-113">Create a web app project</span></span>

<span data-ttu-id="5e8af-114">打开命令行界面，然后输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="5e8af-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="5e8af-115">上面的命令：</span><span class="sxs-lookup"><span data-stu-id="5e8af-115">The preceding command:</span></span>

* <span data-ttu-id="5e8af-116">创建新 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="5e8af-116">Creates a new web app.</span></span>  
* <span data-ttu-id="5e8af-117">`-o` 参数使用应用的源文件创建名为 aspnetcoreapp  的目录。</span><span class="sxs-lookup"><span data-stu-id="5e8af-117">The `-o` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="5e8af-118">信任开发证书</span><span class="sxs-lookup"><span data-stu-id="5e8af-118">Trust the development certificate</span></span>

<span data-ttu-id="5e8af-119">信任 HTTPS 开发证书：</span><span class="sxs-lookup"><span data-stu-id="5e8af-119">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="5e8af-120">Windows</span><span class="sxs-lookup"><span data-stu-id="5e8af-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="5e8af-121">以上命令会显示以下对话：</span><span class="sxs-lookup"><span data-stu-id="5e8af-121">The preceding command displays the following dialog:</span></span>

![安全警告对话](~/getting-started/_static/cert.png)

<span data-ttu-id="5e8af-123">如果你同意信任开发证书，请选择“是”。 </span><span class="sxs-lookup"><span data-stu-id="5e8af-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="5e8af-124">macOS</span><span class="sxs-lookup"><span data-stu-id="5e8af-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="5e8af-125">以上命令会显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="5e8af-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="5e8af-126">*请求信任 HTTPS 开发证书。如果证书尚不受信任，我们将运行以下命令：* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="5e8af-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="5e8af-127">此命令可能会提示你输入密码以在系统密钥链上安装证书。</span><span class="sxs-lookup"><span data-stu-id="5e8af-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="5e8af-128">如果你同意信任开发证书，请输入密码。</span><span class="sxs-lookup"><span data-stu-id="5e8af-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="5e8af-129">Linux</span><span class="sxs-lookup"><span data-stu-id="5e8af-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="5e8af-130">对于适用于 Linux 的 Windows 子系统，请参阅[在适用于 Linux 的 Windows 子系统中信任 HTTPS 证书](xref:security/enforcing-ssl#wsl)。</span><span class="sxs-lookup"><span data-stu-id="5e8af-130">For Windows Subsystem for Linux, see [Trust HTTPS certificate from Windows Subsystem for Linux](xref:security/enforcing-ssl#wsl).</span></span>

<span data-ttu-id="5e8af-131">查看你的 Linux 分发对应的文档，了解如何信任 HTTPS 开发证书。</span><span class="sxs-lookup"><span data-stu-id="5e8af-131">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="5e8af-132">有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="5e8af-132">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="5e8af-133">运行应用</span><span class="sxs-lookup"><span data-stu-id="5e8af-133">Run the app</span></span>

<span data-ttu-id="5e8af-134">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5e8af-134">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="5e8af-135">在命令行界面指明应用已启动后，转到 [https://localhost:5001](https://localhost:5001)。</span><span class="sxs-lookup"><span data-stu-id="5e8af-135">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="5e8af-136">单击“接受”，接受隐私和 cookie 政策  。</span><span class="sxs-lookup"><span data-stu-id="5e8af-136">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="5e8af-137">此应用不保留个人信息。</span><span class="sxs-lookup"><span data-stu-id="5e8af-137">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="5e8af-138">编辑 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="5e8af-138">Edit a Razor page</span></span>

<span data-ttu-id="5e8af-139">打开 Pages/Index.cshtml  ，并使用以下突出显示标记修改页面：</span><span class="sxs-lookup"><span data-stu-id="5e8af-139">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="5e8af-140">转到 [https://localhost:5001](https://localhost:5001)，并验证更改是否显示。</span><span class="sxs-lookup"><span data-stu-id="5e8af-140">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5e8af-141">后续步骤</span><span class="sxs-lookup"><span data-stu-id="5e8af-141">Next steps</span></span>

<span data-ttu-id="5e8af-142">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="5e8af-142">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5e8af-143">创建 Web 应用项目。</span><span class="sxs-lookup"><span data-stu-id="5e8af-143">Create a web app project.</span></span>
> * <span data-ttu-id="5e8af-144">信任开发证书。</span><span class="sxs-lookup"><span data-stu-id="5e8af-144">Trust the development certificate.</span></span>
> * <span data-ttu-id="5e8af-145">运行该项目。</span><span class="sxs-lookup"><span data-stu-id="5e8af-145">Run the project.</span></span>
> * <span data-ttu-id="5e8af-146">进行更改。</span><span class="sxs-lookup"><span data-stu-id="5e8af-146">Make a change.</span></span>

<span data-ttu-id="5e8af-147">要了解有关 ASP.NET Core 的详细信息，请参阅简介中推荐的学习路径：</span><span class="sxs-lookup"><span data-stu-id="5e8af-147">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
