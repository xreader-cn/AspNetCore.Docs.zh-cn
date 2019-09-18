---
title: 在 ASP.NET Core razor 页单元测试
author: guardrex
description: 了解如何为 Razor Pages 应用创建单元测试。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: afac97d686ef190ebb92d20a55a15dd774b0d1de
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081430"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>在 ASP.NET Core razor 页单元测试

作者：[Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 支持 Razor 页应用的单元测试。 数据访问层（DAL）和页面模型的测试有助于确保：

* 在应用程序构建过程中，Razor Pages 应用程序的各个部分将独立工作，并作为一个单元一起工作。
* 类和方法的责任范围有限。
* 在应用程序的行为方式上还存在其他文档。
* 回归是指在自动生成和部署过程中发现的代码更新导致的错误。

本主题假定你基本了解 Razor Pages 应用和单元测试。 如果不熟悉 Razor Pages 应用或测试概念，请参阅以下主题：

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）

示例项目包含两个应用：

| 应用         | 项目文件夹                     | 描述 |
| ----------- | ---------------------------------- | ----------- |
| 消息应用 | *src/RazorPagesTestSample*         | 允许用户添加消息、删除一条消息、删除所有消息和分析消息（查找每条消息的平均单词数）。 |
| 测试应用    | *tests/RazorPagesTestSample.Tests* | 用于对消息应用的 DAL 和索引页模型进行单元测试。 |

可以使用 IDE 的内置测试功能（如[Visual Studio](/visualstudio/test/unit-test-your-code)或[Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)）运行测试。 如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在 " *RazorPagesTestSample* " 文件夹中的命令提示符处执行以下命令：

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>消息应用组织

消息应用是 Razor Pages 的消息系统，具有以下特征：

* 应用的 "索引" 页（*Pages/索引. cshtml*和*pages/* node.js）提供了一个 UI 和页面模型方法来控制消息的添加、删除和分析（查找每条消息的平均单词数）。
* 消息由`Message`类（*Data/message .cs*）描述，具有两个属性： `Id` （键）和`Text` （message）。 此`Text`属性是必需的，并且限制为200个字符。
* 使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。
* 应用程序在其数据库上下文类`AppDbContext` （*Data/AppDbContext*）中包含 DAL。 DAL 方法被标记`virtual`为，这允许模拟方法在测试中使用。
* 如果数据库在应用启动时为空，则会用三条消息初始化消息存储。 这些*种子消息*还在测试中使用。

&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。 本主题使用[xUnit](https://xunit.github.io/)测试框架。 不同测试框架中的测试概念和测试实现相似，但并不完全相同。

尽管该示例应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。 有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和<xref:mvc/controllers/testing> （示例实现存储库模式）。

## <a name="test-app-organization"></a>测试应用组织

测试应用是 "*测试/RazorPagesTestSample* " 文件夹中的控制台应用。

| 测试应用文件夹 | 描述 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs*包含 DAL 的单元测试。</li><li>*IndexPageTests.cs*包含索引页模型的单元测试。</li></ul> |
| *公用*     | 包含用于为每个 DAL 单元测试创建新数据库上下文选项，以便将数据库重置为每个测试的基线条件的方法。`TestDbContextOptions` |

测试框架为[xUnit](https://xunit.github.io/)。 对象模拟框架为[Moq](https://github.com/moq/moq4)。

## <a name="unit-tests-of-the-data-access-layer-dal"></a>数据访问层（DAL）的单元测试

消息应用有一个 DAL，其中包含包含在`AppDbContext`类中的四个方法（*src/RazorPagesTestSample/Data/AppDbContext*）。 每个方法都在测试应用程序中有一个或两个单元测试。

| DAL 方法               | 函数                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | 从按`Text`属性排序的数据库中获取。 `List<Message>` |
| `AddMessageAsync`        | 将添加`Message`到数据库中。                                          |
| `DeleteAllMessagesAsync` | 删除数据库`Message`中的所有条目。                           |
| `DeleteMessageAsync`     | `Message` 删除`Id`数据库中的单个。                      |

为每个测试创建新<xref:Microsoft.EntityFrameworkCore.DbContextOptions> `AppDbContext`的时，DAL 的单元测试需要。 为每个测试创建`DbContextOptions`的一种方法是<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>使用：

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

此方法的问题是，每个测试都接收到数据库，并将其保留在上一个测试的任何状态。 尝试编写不相互干扰的原子单元测试时，这可能会出现问题。 若要强制`AppDbContext`将新的数据库上下文用于每个测试，请`DbContextOptions`提供基于新服务提供程序的实例。 测试应用程序演示如何使用其`Utilities`类方法`TestDbContextOptions` （test */RazorPagesTestSample/实用工具/实用工具*）执行此操作：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

在 DAL `DbContextOptions`单元测试中使用，允许每个测试以原子方式使用全新的数据库实例运行：

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest`类（*run-unittests/DataAccessLayerTest*）中的每个测试方法都遵循类似的 "顺序" 操作-断言模式：

1. 按为测试配置了数据库，并定义了预期的结果。
1. 意义执行测试。
1. 断言断言用于确定测试结果是否成功。

例如，该`DeleteMessageAsync`方法负责删除由其`Id` （*src/RazorPagesTestSample/Data/AppDbContext*）标识的单个消息：

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

此方法有两个测试。 一个测试检查方法是在数据库中存在消息时删除一条消息。 另一种方法测试如果要删除的消息`Id`不存在，数据库不会更改。 此`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`方法如下所示：

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

首先，方法执行 "排列" 步骤，在该步骤中执行 Act 步骤。 获取并保存`seedMessages`种子设定消息。 播种消息会保存到数据库中。 设置为`Id`的`1`消息将被设置为删除。 执行方法时，预期的消息应包含除为`Id`的`1`消息之外的所有消息。 `DeleteMessageAsync` `expectedMessages`变量表示此预期结果。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

方法的作用：执行方法，并传入`recId`的`1`： `DeleteMessageAsync`

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

最后，方法`Messages`从上下文中获取，并将其`expectedMessages`与断言等于二者相等：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

为了比较这两个`List<Message>`是否相同：

* 消息按`Id`排序。
* 在`Text`属性上比较消息对。

类似的测试方法`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`会检查尝试删除不存在的消息的结果。 在这种情况下，数据库中的预期消息应该等于执行`DeleteMessageAsync`方法后的实际消息。 不应更改数据库的内容：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>页面模型方法的单元测试

另一组单元测试负责页面模型方法的测试。 在 message 应用中，索引页模型`IndexModel`位于*src/RazorPagesTestSample/Pages/* 类中。

| 页面模型方法 | 函数 |
| ----------------- | -------- |
| `OnGetAsync` | 使用`GetMessagesAsync`方法获取来自该 UI 的 DAL 的消息。 |
| `OnPostAddMessageAsync` | 如果[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)有效，则调用`AddMessageAsync`将消息添加到数据库。 |
| `OnPostDeleteAllMessagesAsync` | 调用`DeleteAllMessagesAsync`以删除数据库中的所有消息。 |
| `OnPostDeleteMessageAsync` | 执行`DeleteMessageAsync`以删除具有指定的`Id`消息。 |
| `OnPostAnalyzeMessagesAsync` | 如果数据库中有一条或多条消息，则计算每条消息的平均字数。 |

使用`IndexPageTests`类中的七个测试（*RazorPagesTestSample/run-unittests/IndexPageTests*）测试页模型方法。 这些测试使用熟悉的 "排列方式-法" 模式。 这些测试重点关注：

* 确定在[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)无效时，方法是否遵循正确的行为。
* 确认方法生成正确<xref:Microsoft.AspNetCore.Mvc.IActionResult>。
* 检查是否已正确赋值。

这组测试通常模拟 DAL 的方法，以便为执行页面模型方法的 Act 步骤生成所需的数据。 例如， `GetMessagesAsync`的`AppDbContext`方法是模拟，以生成输出。 当页面模型方法执行此方法时，mock 返回结果。 数据不来自数据库。 这会创建可预测、可靠的测试条件，以便在页面模型测试中使用 DAL。

该`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`测试显示`GetMessagesAsync`了方法对于页面模型是模拟的：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

在 Act 步骤中执行`GetMessagesAsync` 方法时，它将调用页模型的方法。`OnGetAsync`

单元测试 Act 步骤（test */RazorPagesTestSample/run-unittests/IndexPageTests*）：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`页模型的`OnGetAsync`方法（*src/RazorPagesTestSample/Pages/* ）：

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL `GetMessagesAsync`中的方法不会返回此方法调用的结果。 此方法的模拟版本返回结果。

在该`Assert`步骤中，将从页面`actualMessages`模型的`Messages`属性中指定实际的消息（）。 分配消息时也会执行类型检查。 预期的和实际的消息按其`Text`属性进行比较。 测试将断言两个`List<Message>`实例包含相同的消息。

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

此组中的其他测试<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>创建包含<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `PageContext` <xref:Microsoft.AspNetCore.Mvc.ActionContext>的页模型对象，以及用于建立、 `ViewDataDictionary`和的`PageContext`。 它们在执行测试时很有用。 例如，消息`ModelState`应用程序与一起<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>建立错误，以检查在执行时<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> `OnPostAddMessageAsync`是否返回了有效的：

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>其他资源

* [单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [对代码进行单元测试](/visualstudio/test/unit-test-your-code)（Visual Studio）
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [使用 Visual Studio for Mac 在 macOS 上构建完整的 .NET Core 解决方案](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [XUnit.net 入门：通过 .NET SDK 命令行使用 .NET Core](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq 快速入门](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 支持 Razor 页应用的单元测试。 数据访问层（DAL）和页面模型的测试有助于确保：

* 在应用程序构建过程中，Razor Pages 应用程序的各个部分将独立工作，并作为一个单元一起工作。
* 类和方法的责任范围有限。
* 在应用程序的行为方式上还存在其他文档。
* 回归是指在自动生成和部署过程中发现的代码更新导致的错误。

本主题假定你基本了解 Razor Pages 应用和单元测试。 如果不熟悉 Razor Pages 应用或测试概念，请参阅以下主题：

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）

示例项目包含两个应用：

| 应用         | 项目文件夹                     | 描述 |
| ----------- | ---------------------------------- | ----------- |
| 消息应用 | *src/RazorPagesTestSample*         | 允许用户添加消息、删除一条消息、删除所有消息和分析消息（查找每条消息的平均单词数）。 |
| 测试应用    | *tests/RazorPagesTestSample.Tests* | 用于对消息应用的 DAL 和索引页模型进行单元测试。 |

可以使用 IDE 的内置测试功能（如[Visual Studio](/visualstudio/test/unit-test-your-code)或[Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)）运行测试。 如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在 " *RazorPagesTestSample* " 文件夹中的命令提示符处执行以下命令：

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>消息应用组织

消息应用是 Razor Pages 的消息系统，具有以下特征：

* 应用的 "索引" 页（*Pages/索引. cshtml*和*pages/* node.js）提供了一个 UI 和页面模型方法来控制消息的添加、删除和分析（查找每条消息的平均单词数）。
* 消息由`Message`类（*Data/message .cs*）描述，具有两个属性： `Id` （键）和`Text` （message）。 此`Text`属性是必需的，并且限制为200个字符。
* 使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。
* 应用程序在其数据库上下文类`AppDbContext` （*Data/AppDbContext*）中包含 DAL。 DAL 方法被标记`virtual`为，这允许模拟方法在测试中使用。
* 如果数据库在应用启动时为空，则会用三条消息初始化消息存储。 这些*种子消息*还在测试中使用。

&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。 本主题使用[xUnit](https://xunit.github.io/)测试框架。 不同测试框架中的测试概念和测试实现相似，但并不完全相同。

尽管该示例应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。 有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和<xref:mvc/controllers/testing> （示例实现存储库模式）。

## <a name="test-app-organization"></a>测试应用组织

测试应用是 "*测试/RazorPagesTestSample* " 文件夹中的控制台应用。

| 测试应用文件夹 | 描述 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs*包含 DAL 的单元测试。</li><li>*IndexPageTests.cs*包含索引页模型的单元测试。</li></ul> |
| *公用*     | 包含用于为每个 DAL 单元测试创建新数据库上下文选项，以便将数据库重置为每个测试的基线条件的方法。`TestDbContextOptions` |

测试框架为[xUnit](https://xunit.github.io/)。 对象模拟框架为[Moq](https://github.com/moq/moq4)。

## <a name="unit-tests-of-the-data-access-layer-dal"></a>数据访问层（DAL）的单元测试

消息应用有一个 DAL，其中包含包含在`AppDbContext`类中的四个方法（*src/RazorPagesTestSample/Data/AppDbContext*）。 每个方法都在测试应用程序中有一个或两个单元测试。

| DAL 方法               | 函数                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | 从按`Text`属性排序的数据库中获取。 `List<Message>` |
| `AddMessageAsync`        | 将添加`Message`到数据库中。                                          |
| `DeleteAllMessagesAsync` | 删除数据库`Message`中的所有条目。                           |
| `DeleteMessageAsync`     | `Message` 删除`Id`数据库中的单个。                      |

为每个测试创建新<xref:Microsoft.EntityFrameworkCore.DbContextOptions> `AppDbContext`的时，DAL 的单元测试需要。 为每个测试创建`DbContextOptions`的一种方法是<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>使用：

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

此方法的问题是，每个测试都接收到数据库，并将其保留在上一个测试的任何状态。 尝试编写不相互干扰的原子单元测试时，这可能会出现问题。 若要强制`AppDbContext`将新的数据库上下文用于每个测试，请`DbContextOptions`提供基于新服务提供程序的实例。 测试应用程序演示如何使用其`Utilities`类方法`TestDbContextOptions` （test */RazorPagesTestSample/实用工具/实用工具*）执行此操作：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

在 DAL `DbContextOptions`单元测试中使用，允许每个测试以原子方式使用全新的数据库实例运行：

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest`类（*run-unittests/DataAccessLayerTest*）中的每个测试方法都遵循类似的 "顺序" 操作-断言模式：

1. 按为测试配置了数据库，并定义了预期的结果。
1. 意义执行测试。
1. 断言断言用于确定测试结果是否成功。

例如，该`DeleteMessageAsync`方法负责删除由其`Id` （*src/RazorPagesTestSample/Data/AppDbContext*）标识的单个消息：

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

此方法有两个测试。 一个测试检查方法是在数据库中存在消息时删除一条消息。 另一种方法测试如果要删除的消息`Id`不存在，数据库不会更改。 此`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`方法如下所示：

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

首先，方法执行 "排列" 步骤，在该步骤中执行 Act 步骤。 获取并保存`seedMessages`种子设定消息。 播种消息会保存到数据库中。 设置为`Id`的`1`消息将被设置为删除。 执行方法时，预期的消息应包含除为`Id`的`1`消息之外的所有消息。 `DeleteMessageAsync` `expectedMessages`变量表示此预期结果。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

方法的作用：执行方法，并传入`recId`的`1`： `DeleteMessageAsync`

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

最后，方法`Messages`从上下文中获取，并将其`expectedMessages`与断言等于二者相等：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

为了比较这两个`List<Message>`是否相同：

* 消息按`Id`排序。
* 在`Text`属性上比较消息对。

类似的测试方法`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`会检查尝试删除不存在的消息的结果。 在这种情况下，数据库中的预期消息应该等于执行`DeleteMessageAsync`方法后的实际消息。 不应更改数据库的内容：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>页面模型方法的单元测试

另一组单元测试负责页面模型方法的测试。 在 message 应用中，索引页模型`IndexModel`位于*src/RazorPagesTestSample/Pages/* 类中。

| 页面模型方法 | 函数 |
| ----------------- | -------- |
| `OnGetAsync` | 使用`GetMessagesAsync`方法获取来自该 UI 的 DAL 的消息。 |
| `OnPostAddMessageAsync` | 如果[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)有效，则调用`AddMessageAsync`将消息添加到数据库。 |
| `OnPostDeleteAllMessagesAsync` | 调用`DeleteAllMessagesAsync`以删除数据库中的所有消息。 |
| `OnPostDeleteMessageAsync` | 执行`DeleteMessageAsync`以删除具有指定的`Id`消息。 |
| `OnPostAnalyzeMessagesAsync` | 如果数据库中有一条或多条消息，则计算每条消息的平均字数。 |

使用`IndexPageTests`类中的七个测试（*RazorPagesTestSample/run-unittests/IndexPageTests*）测试页模型方法。 这些测试使用熟悉的 "排列方式-法" 模式。 这些测试重点关注：

* 确定在[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)无效时，方法是否遵循正确的行为。
* 确认方法生成正确<xref:Microsoft.AspNetCore.Mvc.IActionResult>。
* 检查是否已正确赋值。

这组测试通常模拟 DAL 的方法，以便为执行页面模型方法的 Act 步骤生成所需的数据。 例如， `GetMessagesAsync`的`AppDbContext`方法是模拟，以生成输出。 当页面模型方法执行此方法时，mock 返回结果。 数据不来自数据库。 这会创建可预测、可靠的测试条件，以便在页面模型测试中使用 DAL。

该`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`测试显示`GetMessagesAsync`了方法对于页面模型是模拟的：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

在 Act 步骤中执行`GetMessagesAsync` 方法时，它将调用页模型的方法。`OnGetAsync`

单元测试 Act 步骤（test */RazorPagesTestSample/run-unittests/IndexPageTests*）：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`页模型的`OnGetAsync`方法（*src/RazorPagesTestSample/Pages/* ）：

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL `GetMessagesAsync`中的方法不会返回此方法调用的结果。 此方法的模拟版本返回结果。

在该`Assert`步骤中，将从页面`actualMessages`模型的`Messages`属性中指定实际的消息（）。 分配消息时也会执行类型检查。 预期的和实际的消息按其`Text`属性进行比较。 测试将断言两个`List<Message>`实例包含相同的消息。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

此组中的其他测试<xref:Microsoft.AspNetCore.Http.DefaultHttpContext>创建包含<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `PageContext` <xref:Microsoft.AspNetCore.Mvc.ActionContext>的页模型对象，以及用于建立、 `ViewDataDictionary`和的`PageContext`。 它们在执行测试时很有用。 例如，消息`ModelState`应用程序与一起<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>建立错误，以检查在执行时<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> `OnPostAddMessageAsync`是否返回了有效的：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>其他资源

* [单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [对代码进行单元测试](/visualstudio/test/unit-test-your-code)（Visual Studio）
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [使用 Visual Studio for Mac 在 macOS 上构建完整的 .NET Core 解决方案](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [XUnit.net 入门：通过 .NET SDK 命令行使用 .NET Core](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq 快速入门](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
