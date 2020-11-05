---
title: ASP.NET Core 中的 Razor Pages 单元测试
author: rick-anderson
description: 了解如何为 Razor Pages 应用创建单元测试。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: test/razor-pages-tests
ms.openlocfilehash: 2486eb8c9fd0fc33ea77b0fedd99795218d7f4ca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058033"
---
# <a name="no-locrazor-pages-unit-tests-in-aspnet-core"></a>ASP.NET Core 中的 Razor Pages 单元测试

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 支持 Razor Pages 应用的单元测试。 数据访问层 (DAL) 和页面模型测试有助于确保：

* Razor Pages 应用的各个部分在应用构造过程中既可以独立运行，也可以作为一个整体运行。
* 类和方法具有有限责任范围。
* 存在有关应用应如何运行的其他文档。
* 回归指代码更新引起的错误，可在自动生成和部署过程中出现。

本主题假定你对 Razor Pages 应用和单元测试有基本的了解。 如果你不熟悉 Razor Pages 应用或测试概念，请参阅以下主题：

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [使用 dotnet test 和 xUnit 在 .NET Core 中进行 C# 单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）

示例项目包含两个应用：

| 应用         | 项目文件夹                     | 描述 |
| ----------- | ---------------------------------- | ----------- |
| 消息应用 | src/RazorPagesTestSample         | 允许用户添加消息、删除一条消息、删除所有消息以及分析消息（查找每条消息的平均字词数）。 |
| 测试应用    | tests/RazorPagesTestSample.Tests | 用于对消息应用的 DAL 和索引页面模型进行单元测试。 |

可使用 IDE 的内置测试功能（例如 [Visual Studio](/visualstudio/test/unit-test-your-code) 或 [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)）运行测试。 如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令行，请在 tests/RazorPagesTestSample.Tests 文件夹中的命令提示符处执行以下命令：

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>消息应用组织

消息应用是具有以下特征的 Razor Pages 消息系统：

* 应用的索引页面（ *Pages/Index.cshtml* 和 *Pages/Index.cshtml.cs* ）提供 UI 和页面模型方法，用于控制添加、删除和分析消息（查找每条消息的平均字词数）。
* 消息由 `Message` 类 ( *Data/Message.cs* ) 描述，并具有两个属性：`Id`（键）和 `Text`（消息）。 `Text` 属性是必需的，并限制为 200 个字符。
* 消息使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)†存储。
* 应用在其数据库上下文类 `AppDbContext` ( *Data/AppDbContext.cs* ) 中包含 DAL。 DAL 方法标记为 `virtual`，这允许模拟在测试中使用的方法。
* 如果应用启动时数据库为空，则消息存储初始化为三条消息。 这些 *种子消息* 也用于测试。

†EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明如何将内存中数据库用于 MSTest 测试。 本主题使用 [xUnit](https://xunit.github.io/) 测试框架。 不同测试框架中的测试概念和测试实现相似，但不完全相同。

尽管示例应用未使用存储库模式且不是[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些开发模式。 有关详细信息，请参阅[设计基础设施持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和 <xref:mvc/controllers/testing>（该示例实现存储库模式）。

## <a name="test-app-organization"></a>测试应用组织

测试应用是 tests/RazorPagesTestSample.Tests 文件夹中的控制台应用。

| 测试应用文件夹 | 描述 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* 包含 DAL 的单元测试。</li><li>*IndexPageTests.cs* 包含索引页面模型的单元测试。</li></ul> |
| *实用工具*     | 包含 `TestDbContextOptions` 方法，该方法用于为每个 DAL 单元测试创建新的数据库上下文选项，以便为每个测试将数据库重置为其基线条件。 |

测试框架为 [xUnit](https://xunit.github.io/)。 对象模拟框架为 [Moq](https://github.com/moq/moq4)。

## <a name="unit-tests-of-the-data-access-layer-dal"></a>数据访问层 (DAL) 的单元测试

消息应用具有 DAL，其中 `AppDbContext` 类 (src/RazorPagesTestSample/Data/AppDbContext.cs) 中包含 4 个方法。 每个方法在测试应用中都有一到两个单元测试。

| DAL 方法               | 函数                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | 从按 `Text` 属性排序的数据库获取 `List<Message>`。 |
| `AddMessageAsync`        | 向数据库添加 `Message`。                                          |
| `DeleteAllMessagesAsync` | 从数据库中删除所有 `Message` 条目。                           |
| `DeleteMessageAsync`     | 按 `Id` 从数据库中删除单个 `Message`。                      |

为每个测试创建新的 `AppDbContext` 时，DAL 的单元测试需要 <xref:Microsoft.EntityFrameworkCore.DbContextOptions>。 为每个测试创建 `DbContextOptions` 的一个方法是使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>：

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

此方法的问题在于，每个测试收到的数据库都处于之前测试中的状态。 尝试编写不会相互干扰的原子单元测试时，这可能会导致问题。 若要强制 `AppDbContext` 为每个测试使用新的数据库上下文，请提供基于新服务提供程序的 `DbContextOptions` 实例。 测试应用演示如何使用其 `Utilities` 类方法 `TestDbContextOptions` (tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs) 执行此操作：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

在 DAL 单元测试中使用 `DbContextOptions` 可使每个测试使用新的数据库实例自动运行：

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest` 类 ( *UnitTests/DataAccessLayerTest.cs* ) 中的每个测试方法都遵循类似的安排-执行-断言模式：

1. 安排：为测试配置数据库和/或定义预期结果。
1. 执行：执行测试。
1. 断言：进行断言以确定测试结果是否成功。

例如，`DeleteMessageAsync` 方法负责删除由其 `Id` (src/RazorPagesTestSample/Data/AppDbContext.cs) 标识的单个消息：

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

此方法有两个测试。 一个测试检查当数据库中存在消息时该方法是否删除消息。 另一个方法测试在要删除的消息 `Id` 不存在的情况下，数据库是否保持不变。 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 方法如下所示：

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

首先，方法执行“安排”步骤，并在该步骤中为“执行”步骤做好准备。 获取种子消息并将其保存在 `seedMessages` 中。 种子消息会保存到数据库中。 `Id` 为 `1` 的消息设置为删除。 执行 `DeleteMessageAsync` 方法时，预期的消息应是除 `Id` 为 `1` 的消息以外的所有消息。 `expectedMessages` 变量表示此预期结果。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

该方法执行：执行 `DeleteMessageAsync` 方法并传入值为 `1` 的 `recId`：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

最后，该方法从上下文中获取 `Messages` 并将其与断言两者相等的 `expectedMessages` 进行比较：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

若要比较两个 `List<Message>` 是否相同，请执行以下操作：

* 按 `Id` 排序消息。
* 在 `Text` 属性上比较消息对。

类似的测试方法 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 检查尝试删除不存在的消息的结果。 在这种情况下，执行 `DeleteMessageAsync` 方法后，数据库中的预期消息数应等于实际消息数。 数据库的内容不应有任何变化：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>页面模型方法的单元测试

另一组单元测试负责测试页面模型方法。 在消息应用中，可在 src/RazorPagesTestSample/Pages/Index.cshtml.cs 的 `IndexModel` 类中找到索引页面模型。

| 页面模型方法 | 函数 |
| ----------------- | -------- |
| `OnGetAsync` | 使用 `GetMessagesAsync` 方法从 DAL 获取 UI 的消息。 |
| `OnPostAddMessageAsync` | 如果 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 有效，则调用 `AddMessageAsync` 将消息添加到数据库。 |
| `OnPostDeleteAllMessagesAsync` | 调用 `DeleteAllMessagesAsync` 以删除数据库中的所有消息。 |
| `OnPostDeleteMessageAsync` | 执行 `DeleteMessageAsync` 以删除指定了 `Id` 的消息。 |
| `OnPostAnalyzeMessagesAsync` | 如果数据库中有一条或多条消息，请计算每条消息的平均字词数。 |

使用 `IndexPageTests` 类 (tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs) 中的 7 个测试来测试页面模型方法。 测试使用熟悉的安排-断言-执行模式。 这些测试的重点在于：

* 确定 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 无效时方法是否遵循正确的行为模式。
* 确认方法是否会生成正确的 <xref:Microsoft.AspNetCore.Mvc.IActionResult>。
* 检查属性值分配是否正确进行。

这一组测试经常模拟 DAL 的方法，以生成执行页面模型方法的“执行”步骤的预期数据。 例如，模拟 `AppDbContext` 的 `GetMessagesAsync` 方法以生成输出。 当页面模型方法执行此方法时，模拟返回结果。 数据不来自数据库。 这会创建可预测的可靠测试条件，以便在页面模型测试中使用 DAL。

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 测试演示如何为页面模型模拟 `GetMessagesAsync` 方法：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

在“执行”步骤中执行 `OnGetAsync` 方法时，它会调用页面模型的 `GetMessagesAsync` 方法。

单元测试“执行”步骤 (tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs)：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` 页面模型的 `OnGetAsync` 方法 (src/RazorPagesTestSample/Pages/Index.cshtml.cs)：

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL 中的 `GetMessagesAsync` 方法不会返回此方法调用的结果。 方法的模拟版本返回结果。

在 `Assert` 步骤中，从页面模型的 `Messages` 属性分配实际消息 (`actualMessages`)。 分配消息后，还会执行类型检查。 预期消息和实际消息通过其 `Text` 属性进行比较。 该测试断言两个 `List<Message>` 实例包含相同的消息。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

此组的其他测试创建页面模型对象，这些对象包括 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>、用于建立 `PageContext` 的 <xref:Microsoft.AspNetCore.Mvc.ActionContext>、`ViewDataDictionary` 和 `PageContext`。 这些对象对于执行测试很有用。 例如，消息应用与 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 建立 `ModelState` 错误，以检查执行 `OnPostAddMessageAsync` 时是否返回有效的 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>其他资源

* [使用 dotnet test 和 xUnit 在 .NET Core 中进行 C# 单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [对代码进行单元测试](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [使用 Visual Studio for Mac 在 macOS 上构建完整的 .NET Core 解决方案](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Getting started with xUnit.net:Using .NET Core with the .NET SDK command line](https://xunit.github.io/docs/getting-started-dotnet-core)（xUnit.net 入门：将 .NET Core 与 .NET SDK 命令行配合使用）
* [Moq](https://github.com/moq/moq4)
* [Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)（Moq 快速入门）

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 支持 Razor Pages 应用的单元测试。 数据访问层 (DAL) 和页面模型测试有助于确保：

* Razor Pages 应用的各个部分在应用构造过程中既可以独立运行，也可以作为一个整体运行。
* 类和方法具有有限责任范围。
* 存在有关应用应如何运行的其他文档。
* 回归指代码更新引起的错误，可在自动生成和部署过程中出现。

本主题假定你对 Razor Pages 应用和单元测试有基本的了解。 如果你不熟悉 Razor Pages 应用或测试概念，请参阅以下主题：

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [使用 dotnet test 和 xUnit 在 .NET Core 中进行 C# 单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）

示例项目包含两个应用：

| 应用         | 项目文件夹                     | 描述 |
| ----------- | ---------------------------------- | ----------- |
| 消息应用 | src/RazorPagesTestSample         | 允许用户添加消息、删除一条消息、删除所有消息以及分析消息（查找每条消息的平均字词数）。 |
| 测试应用    | tests/RazorPagesTestSample.Tests | 用于对消息应用的 DAL 和索引页面模型进行单元测试。 |

可使用 IDE 的内置测试功能（例如 [Visual Studio](/visualstudio/test/unit-test-your-code) 或 [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)）运行测试。 如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令行，请在 tests/RazorPagesTestSample.Tests 文件夹中的命令提示符处执行以下命令：

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>消息应用组织

消息应用是具有以下特征的 Razor Pages 消息系统：

* 应用的索引页面（ *Pages/Index.cshtml* 和 *Pages/Index.cshtml.cs* ）提供 UI 和页面模型方法，用于控制添加、删除和分析消息（查找每条消息的平均字词数）。
* 消息由 `Message` 类 ( *Data/Message.cs* ) 描述，并具有两个属性：`Id`（键）和 `Text`（消息）。 `Text` 属性是必需的，并限制为 200 个字符。
* 消息使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)†存储。
* 应用在其数据库上下文类 `AppDbContext` ( *Data/AppDbContext.cs* ) 中包含 DAL。 DAL 方法标记为 `virtual`，这允许模拟在测试中使用的方法。
* 如果应用启动时数据库为空，则消息存储初始化为三条消息。 这些 *种子消息* 也用于测试。

†EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明如何将内存中数据库用于 MSTest 测试。 本主题使用 [xUnit](https://xunit.github.io/) 测试框架。 不同测试框架中的测试概念和测试实现相似，但不完全相同。

尽管示例应用未使用存储库模式且不是[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些开发模式。 有关详细信息，请参阅[设计基础设施持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和 <xref:mvc/controllers/testing>（该示例实现存储库模式）。

## <a name="test-app-organization"></a>测试应用组织

测试应用是 tests/RazorPagesTestSample.Tests 文件夹中的控制台应用。

| 测试应用文件夹 | 描述 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* 包含 DAL 的单元测试。</li><li>*IndexPageTests.cs* 包含索引页面模型的单元测试。</li></ul> |
| *实用工具*     | 包含 `TestDbContextOptions` 方法，该方法用于为每个 DAL 单元测试创建新的数据库上下文选项，以便为每个测试将数据库重置为其基线条件。 |

测试框架为 [xUnit](https://xunit.github.io/)。 对象模拟框架为 [Moq](https://github.com/moq/moq4)。

## <a name="unit-tests-of-the-data-access-layer-dal"></a>数据访问层 (DAL) 的单元测试

消息应用具有 DAL，其中 `AppDbContext` 类 (src/RazorPagesTestSample/Data/AppDbContext.cs) 中包含 4 个方法。 每个方法在测试应用中都有一到两个单元测试。

| DAL 方法               | 函数                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | 从按 `Text` 属性排序的数据库获取 `List<Message>`。 |
| `AddMessageAsync`        | 向数据库添加 `Message`。                                          |
| `DeleteAllMessagesAsync` | 从数据库中删除所有 `Message` 条目。                           |
| `DeleteMessageAsync`     | 按 `Id` 从数据库中删除单个 `Message`。                      |

为每个测试创建新的 `AppDbContext` 时，DAL 的单元测试需要 <xref:Microsoft.EntityFrameworkCore.DbContextOptions>。 为每个测试创建 `DbContextOptions` 的一个方法是使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>：

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

此方法的问题在于，每个测试收到的数据库都处于之前测试中的状态。 尝试编写不会相互干扰的原子单元测试时，这可能会导致问题。 若要强制 `AppDbContext` 为每个测试使用新的数据库上下文，请提供基于新服务提供程序的 `DbContextOptions` 实例。 测试应用演示如何使用其 `Utilities` 类方法 `TestDbContextOptions` (tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs) 执行此操作：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

在 DAL 单元测试中使用 `DbContextOptions` 可使每个测试使用新的数据库实例自动运行：

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest` 类 ( *UnitTests/DataAccessLayerTest.cs* ) 中的每个测试方法都遵循类似的安排-执行-断言模式：

1. 安排：为测试配置数据库和/或定义预期结果。
1. 执行：执行测试。
1. 断言：进行断言以确定测试结果是否成功。

例如，`DeleteMessageAsync` 方法负责删除由其 `Id` (src/RazorPagesTestSample/Data/AppDbContext.cs) 标识的单个消息：

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

此方法有两个测试。 一个测试检查当数据库中存在消息时该方法是否删除消息。 另一个方法测试在要删除的消息 `Id` 不存在的情况下，数据库是否保持不变。 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 方法如下所示：

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

首先，方法执行“安排”步骤，并在该步骤中为“执行”步骤做好准备。 获取种子消息并将其保存在 `seedMessages` 中。 种子消息会保存到数据库中。 `Id` 为 `1` 的消息设置为删除。 执行 `DeleteMessageAsync` 方法时，预期的消息应是除 `Id` 为 `1` 的消息以外的所有消息。 `expectedMessages` 变量表示此预期结果。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

该方法执行：执行 `DeleteMessageAsync` 方法并传入值为 `1` 的 `recId`：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

最后，该方法从上下文中获取 `Messages` 并将其与断言两者相等的 `expectedMessages` 进行比较：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

若要比较两个 `List<Message>` 是否相同，请执行以下操作：

* 按 `Id` 排序消息。
* 在 `Text` 属性上比较消息对。

类似的测试方法 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 检查尝试删除不存在的消息的结果。 在这种情况下，执行 `DeleteMessageAsync` 方法后，数据库中的预期消息数应等于实际消息数。 数据库的内容不应有任何变化：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>页面模型方法的单元测试

另一组单元测试负责测试页面模型方法。 在消息应用中，可在 src/RazorPagesTestSample/Pages/Index.cshtml.cs 的 `IndexModel` 类中找到索引页面模型。

| 页面模型方法 | 函数 |
| ----------------- | -------- |
| `OnGetAsync` | 使用 `GetMessagesAsync` 方法从 DAL 获取 UI 的消息。 |
| `OnPostAddMessageAsync` | 如果 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 有效，则调用 `AddMessageAsync` 将消息添加到数据库。 |
| `OnPostDeleteAllMessagesAsync` | 调用 `DeleteAllMessagesAsync` 以删除数据库中的所有消息。 |
| `OnPostDeleteMessageAsync` | 执行 `DeleteMessageAsync` 以删除指定了 `Id` 的消息。 |
| `OnPostAnalyzeMessagesAsync` | 如果数据库中有一条或多条消息，请计算每条消息的平均字词数。 |

使用 `IndexPageTests` 类 (tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs) 中的 7 个测试来测试页面模型方法。 测试使用熟悉的安排-断言-执行模式。 这些测试的重点在于：

* 确定 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 无效时方法是否遵循正确的行为模式。
* 确认方法是否会生成正确的 <xref:Microsoft.AspNetCore.Mvc.IActionResult>。
* 检查属性值分配是否正确进行。

这一组测试经常模拟 DAL 的方法，以生成执行页面模型方法的“执行”步骤的预期数据。 例如，模拟 `AppDbContext` 的 `GetMessagesAsync` 方法以生成输出。 当页面模型方法执行此方法时，模拟返回结果。 数据不来自数据库。 这会创建可预测的可靠测试条件，以便在页面模型测试中使用 DAL。

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 测试演示如何为页面模型模拟 `GetMessagesAsync` 方法：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

在“执行”步骤中执行 `OnGetAsync` 方法时，它会调用页面模型的 `GetMessagesAsync` 方法。

单元测试“执行”步骤 (tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs)：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` 页面模型的 `OnGetAsync` 方法 (src/RazorPagesTestSample/Pages/Index.cshtml.cs)：

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL 中的 `GetMessagesAsync` 方法不会返回此方法调用的结果。 方法的模拟版本返回结果。

在 `Assert` 步骤中，从页面模型的 `Messages` 属性分配实际消息 (`actualMessages`)。 分配消息后，还会执行类型检查。 预期消息和实际消息通过其 `Text` 属性进行比较。 该测试断言两个 `List<Message>` 实例包含相同的消息。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

此组的其他测试创建页面模型对象，这些对象包括 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>、用于建立 `PageContext` 的 <xref:Microsoft.AspNetCore.Mvc.ActionContext>、`ViewDataDictionary` 和 `PageContext`。 这些对象对于执行测试很有用。 例如，消息应用与 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 建立 `ModelState` 错误，以检查执行 `OnPostAddMessageAsync` 时是否返回有效的 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>其他资源

* [使用 dotnet test 和 xUnit 在 .NET Core 中进行 C# 单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [对代码进行单元测试](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [使用 Visual Studio for Mac 在 macOS 上构建完整的 .NET Core 解决方案](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Getting started with xUnit.net:Using .NET Core with the .NET SDK command line](https://xunit.github.io/docs/getting-started-dotnet-core)（xUnit.net 入门：将 .NET Core 与 .NET SDK 命令行配合使用）
* [Moq](https://github.com/moq/moq4)
* [Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)（Moq 快速入门）
* [JustMockLite](https://github.com/telerik/JustMockLite)：面向 .NET 开发人员的模拟框架。 （ *不由 Microsoft 进行支持或维护* 。）

::: moniker-end
