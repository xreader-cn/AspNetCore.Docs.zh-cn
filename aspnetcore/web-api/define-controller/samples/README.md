---
ms.openlocfilehash: 07abb12af390c0f2a50e98fc5e53545b6635f968
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665504"
---
# <a name="aspnet-core-web-api-controller-sample"></a><span data-ttu-id="ad451-101">ASP.NET Core Web API 控制器示例</span><span class="sxs-lookup"><span data-stu-id="ad451-101">ASP.NET Core Web API Controller Sample</span></span>

<span data-ttu-id="ad451-102">本示例应用包含以下项目：</span><span class="sxs-lookup"><span data-stu-id="ad451-102">This sample app consists of the following projects:</span></span>

- <span data-ttu-id="ad451-103">**WebApiSample.Api.22**：面向 .NET Core 2.2 的 ASP.NET Core 2.2 项目。</span><span class="sxs-lookup"><span data-stu-id="ad451-103">**WebApiSample.Api.22**: An ASP.NET Core 2.2 project targeting .NET Core 2.2.</span></span>
- <span data-ttu-id="ad451-104">**WebApiSample.Api.21**：面向 .NET Core 2.1 的 ASP.NET Core 2.1 项目。</span><span class="sxs-lookup"><span data-stu-id="ad451-104">**WebApiSample.Api.21**: An ASP.NET Core 2.1 project targeting .NET Core 2.1.</span></span>
- <span data-ttu-id="ad451-105">**WebApiSample.Api.Pre21**：面向 .NET Core 2.0 的 ASP.NET Core 2.0 项目。</span><span class="sxs-lookup"><span data-stu-id="ad451-105">**WebApiSample.Api.Pre21**: An ASP.NET Core 2.0 project targeting .NET Core 2.0.</span></span>
- <span data-ttu-id="ad451-106">**WebApiSample.DataAccess**：用作 2 个 Web API 项目的数据访问层的 .NET Standard 2.0 类库。</span><span class="sxs-lookup"><span data-stu-id="ad451-106">**WebApiSample.DataAccess**: A .NET Standard 2.0 class library serving as a data access tier for the 2 Web API projects.</span></span>

<span data-ttu-id="ad451-107">此示例说明 Web API 控制器的各种创建方式：</span><span class="sxs-lookup"><span data-stu-id="ad451-107">This sample illustrates variations of Web API controller creation:</span></span>

- [<span data-ttu-id="ad451-108">从 ControllerBase 派生类</span><span class="sxs-lookup"><span data-stu-id="ad451-108">Derive class from ControllerBase</span></span>](https://docs.microsoft.com/aspnet/core/web-api#derive-class-from-controllerbase)
- [<span data-ttu-id="ad451-109">使用 ApiControllerAttribute 批注类</span><span class="sxs-lookup"><span data-stu-id="ad451-109">Annotate class with ApiControllerAttribute</span></span>](https://docs.microsoft.com/aspnet/core/web-api#annotate-class-with-apicontrollerattribute)
