---
ms.openlocfilehash: b1f7c306533e70f84e73a020c74756b91cae7ea7
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665745"
---
# <a name="error-handling-sample-application"></a>错误处理示例应用程序

本示例应用演示[处理 ASP.NET Core 中的错误](https://docs.microsoft.com/aspnet/core/fundamentals/error-handling)主题中所述的方案。

## <a name="configure-a-custom-exception-handling-page"></a>配置自定义异常处理页

如果应用未在开发环境中运行，异常处理中间件将：

* 捕获异常。
* 记录异常。
* 在所提供路径的备用管道中重新执行请求。

## <a name="configure-custom-exception-handling-code"></a>配置自定义异常处理代码

使用自定义异常处理页为错误提供终结点的另一种方法是为 `UseExceptionHandler` 提供 lambda。 使用带 `UseExceptionHandler` 的 lambda 允许在返回响应之前访问错误。

示例应用演示了 `Startup.Configure` 中的自定义异常处理代码。 按照 Startup.cs 文件 (`LambdaErrorHandler`) 顶部的说明操作。 应用启动后，使用“索引”页上的“引发异常”链接触发异常。
