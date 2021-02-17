---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: ca743cdb39423d833902c330f6f0682b600c9833
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551895"
---
::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> 由于路由中的 [bug](https://github.com/dotnet/aspnetcore/issues/18677)catch-all  参数可能无法正确匹配相应路由。 受此 Bug 影响的应用具有以下特征：
>
> * “全部捕获”路由，例如 `{**slug}"`
> * “全部捕获”路由未能匹配应与之匹配的请求。
> * 删除其他路由可使“全部捕获”路由开始运行。
>
> 请参阅 GitHub bug [18677](https://github.com/dotnet/aspnetcore/issues/18677) 和 [16579](https://github.com/dotnet/aspnetcore/issues/16579)，了解遇到此 bug 的示例。
>
> [.NET Core 3.1.301 SDK 及更高版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)中包含此 bug 的修补程序（可选用）。 以下代码设置了一个可修复此 bug 的内部开关：
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end