---
title: 将配置迁移到 ASP.NET Core
author: ardalis
description: 了解如何将配置从 ASP.NET MVC 项目迁移到 ASP.NET Core MVC 项目。
ms.author: riande
ms.date: 10/14/2016
uid: migration/configuration
ms.openlocfilehash: c379f1f64dc5ab8aeb48055124e86e4e60d93785
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894924"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="6045f-103">将配置迁移到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6045f-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="6045f-104">作者：[Steve Smith](https://ardalis.com/) 和 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="6045f-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="6045f-105">在以前的文章中，我们就已着手[将 ASP.NET MVC 项目迁移到 ASP.NET Core MVC](xref:migration/mvc)。</span><span class="sxs-lookup"><span data-stu-id="6045f-105">In the previous article, we began [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="6045f-106">在本文中，我们将迁移配置。</span><span class="sxs-lookup"><span data-stu-id="6045f-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="6045f-107">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6045f-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="6045f-108">安装程序配置</span><span class="sxs-lookup"><span data-stu-id="6045f-108">Setup configuration</span></span>

<span data-ttu-id="6045f-109">ASP.NET Core 不再使用*Global.asax*和*web.config* ASP.NET 的早期版本使用的文件。</span><span class="sxs-lookup"><span data-stu-id="6045f-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="6045f-110">在早期版本的 ASP.NET，应用程序启动逻辑已放置在`Application_StartUp`方法内的*Global.asax*。</span><span class="sxs-lookup"><span data-stu-id="6045f-110">In earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="6045f-111">更高版本，在 ASP.NET MVC 中， *Startup.cs*文件包含在项目; 的根目录，并当应用程序启动时调用。</span><span class="sxs-lookup"><span data-stu-id="6045f-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="6045f-112">ASP.NET Core 已完全采用这种方法，通过将中的所有启动逻辑*Startup.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="6045f-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="6045f-113">*Web.config*还在 ASP.NET Core 替换文件。</span><span class="sxs-lookup"><span data-stu-id="6045f-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="6045f-114">配置本身现在可以配置，应用程序启动过程中所述*Startup.cs*。</span><span class="sxs-lookup"><span data-stu-id="6045f-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="6045f-115">配置仍然可以利用 XML 文件，但通常 ASP.NET Core 项目将置于配置值的 JSON 格式文件，如*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="6045f-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="6045f-116">ASP.NET Core 配置系统可以方便地访问环境变量，可以提供[更安全、 极其可靠的位置](xref:security/app-secrets)特定于环境的值。</span><span class="sxs-lookup"><span data-stu-id="6045f-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="6045f-117">这是不应签入源代码管理的 API 密钥连接字符串等机密的尤其如此。</span><span class="sxs-lookup"><span data-stu-id="6045f-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="6045f-118">请参阅[配置](xref:fundamentals/configuration/index)若要了解有关 ASP.NET Core 中配置的详细信息。</span><span class="sxs-lookup"><span data-stu-id="6045f-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="6045f-119">有关本文中，我们开始使用从部分已迁移的 ASP.NET Core 项目[上一篇文章](xref:migration/mvc)。</span><span class="sxs-lookup"><span data-stu-id="6045f-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="6045f-120">若要设置配置，请添加以下构造函数和属性设置为*Startup.cs*文件位于项目根目录中：</span><span class="sxs-lookup"><span data-stu-id="6045f-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="6045f-121">请注意，此时， *Startup.cs*文件将无法编译，因为我们仍需要添加以下`using`语句：</span><span class="sxs-lookup"><span data-stu-id="6045f-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="6045f-122">添加*appsettings.json*到使用合适的项目模板的项目的根目录的文件：</span><span class="sxs-lookup"><span data-stu-id="6045f-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![添加 AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="6045f-124">从 web.config 中迁移的配置设置</span><span class="sxs-lookup"><span data-stu-id="6045f-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="6045f-125">我们的 ASP.NET MVC 项目包含中的所需的数据库连接字符串*web.config*，请在`<connectionStrings>`元素。</span><span class="sxs-lookup"><span data-stu-id="6045f-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="6045f-126">在我们 ASP.NET Core 项目中，我们将存储此信息在*appsettings.json*文件。</span><span class="sxs-lookup"><span data-stu-id="6045f-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="6045f-127">打开*appsettings.json*，并记下它已包含以下：</span><span class="sxs-lookup"><span data-stu-id="6045f-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="6045f-128">突出显示的行上面所示，在将更改从数据库的名称 **_CHANGE_ME**到数据库的名称。</span><span class="sxs-lookup"><span data-stu-id="6045f-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="6045f-129">总结</span><span class="sxs-lookup"><span data-stu-id="6045f-129">Summary</span></span>

<span data-ttu-id="6045f-130">ASP.NET Core 将置于单个文件，在其中的必要的服务和依赖项可以定义和配置应用程序的所有启动逻辑。</span><span class="sxs-lookup"><span data-stu-id="6045f-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="6045f-131">它将替换*web.config*具有一种灵活的配置功能，可以利用各种文件格式，如 JSON，以及环境变量文件。</span><span class="sxs-lookup"><span data-stu-id="6045f-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
