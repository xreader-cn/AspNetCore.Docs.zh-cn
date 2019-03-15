---
ms.openlocfilehash: b1f7c306533e70f84e73a020c74756b91cae7ea7
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665745"
---
# <a name="error-handling-sample-application"></a><span data-ttu-id="68c10-101">错误处理示例应用程序</span><span class="sxs-lookup"><span data-stu-id="68c10-101">Error Handling Sample Application</span></span>

<span data-ttu-id="68c10-102">本示例应用演示[处理 ASP.NET Core 中的错误](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling)主题中所述的方案。</span><span class="sxs-lookup"><span data-stu-id="68c10-102">This sample app demonstrates the scenarios described in the [Handle errors in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling) topic.</span></span>

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="68c10-103">配置自定义异常处理页</span><span class="sxs-lookup"><span data-stu-id="68c10-103">Configure a custom exception handling page</span></span>

<span data-ttu-id="68c10-104">如果应用未在开发环境中运行，异常处理中间件将：</span><span class="sxs-lookup"><span data-stu-id="68c10-104">When the app isn't running in the Development environment, Exception Handling Middleware:</span></span>

* <span data-ttu-id="68c10-105">捕获异常。</span><span class="sxs-lookup"><span data-stu-id="68c10-105">Catches exceptions.</span></span>
* <span data-ttu-id="68c10-106">记录异常。</span><span class="sxs-lookup"><span data-stu-id="68c10-106">Logs exceptions.</span></span>
* <span data-ttu-id="68c10-107">在所提供路径的备用管道中重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="68c10-107">Re-executes the request in an alternate pipeline at the path provided.</span></span>

## <a name="configure-custom-exception-handling-code"></a><span data-ttu-id="68c10-108">配置自定义异常处理代码</span><span class="sxs-lookup"><span data-stu-id="68c10-108">Configure custom exception handling code</span></span>

<span data-ttu-id="68c10-109">使用自定义异常处理页为错误提供终结点的另一种方法是为 `UseExceptionHandler` 提供 lambda。</span><span class="sxs-lookup"><span data-stu-id="68c10-109">An alternative to serving an endpoint for errors with a custom exception handling page is to provide a lambda to `UseExceptionHandler`.</span></span> <span data-ttu-id="68c10-110">使用带 `UseExceptionHandler` 的 lambda 允许在返回响应之前访问错误。</span><span class="sxs-lookup"><span data-stu-id="68c10-110">Using a lambda with `UseExceptionHandler` allows access to the error before returning the response.</span></span>

<span data-ttu-id="68c10-111">示例应用演示了 `Startup.Configure` 中的自定义异常处理代码。</span><span class="sxs-lookup"><span data-stu-id="68c10-111">The sample app demonstrates custom exception handling code in `Startup.Configure`.</span></span> <span data-ttu-id="68c10-112">按照 Startup.cs 文件 (`LambdaErrorHandler`) 顶部的说明操作。</span><span class="sxs-lookup"><span data-stu-id="68c10-112">Follow the instructions at the top of the *Startup.cs* file (`LambdaErrorHandler`).</span></span> <span data-ttu-id="68c10-113">应用启动后，使用“索引”页上的“引发异常”链接触发异常。</span><span class="sxs-lookup"><span data-stu-id="68c10-113">After the app starts, trigger an exception with the **Throw Exception** link on the Index page.</span></span>
