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
ms.openlocfilehash: 27cb64dc8734fcb6d165795515096c9d9dd2a9cc
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552578"
---
<span data-ttu-id="18179-101">运行以下 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="18179-101">Run the following .NET CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

<span data-ttu-id="18179-102">上述命令添加：</span><span class="sxs-lookup"><span data-stu-id="18179-102">The preceding commands add:</span></span>

* <span data-ttu-id="18179-103">[aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="18179-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="18179-104">适用于 .NET CLI 的 EF Core 工具。</span><span class="sxs-lookup"><span data-stu-id="18179-104">The EF Core Tools for the .NET CLI.</span></span>
* <span data-ttu-id="18179-105">EF Core SQLite 提供程序将 EF Core 包作为依赖项进行安装。</span><span class="sxs-lookup"><span data-stu-id="18179-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="18179-106">基架需要的包：`Microsoft.VisualStudio.Web.CodeGeneration.Design` 和 `Microsoft.EntityFrameworkCore.SqlServer`。</span><span class="sxs-lookup"><span data-stu-id="18179-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="18179-107">有关允许应用按环境配置其数据库上下文的多个环境配置指南，请参阅 <xref:fundamentals/environments#environment-based-startup-class-and-methods>。</span><span class="sxs-lookup"><span data-stu-id="18179-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
