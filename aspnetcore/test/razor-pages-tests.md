---
title: 在 ASP.NET Core razor 页单元测试
author: guardrex
description: 了解如何创建 Razor 页面应用的单元测试。
ms.author: riande
ms.custom: mvc
ms.date: 11/27/2017
uid: test/razor-pages-tests
ms.openlocfilehash: f1526b8803f43ec8cbe77c1d2c100d9daf6cd316
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893714"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>在 ASP.NET Core razor 页单元测试

作者：[Luke Latham](https://github.com/guardrex)

ASP.NET Core 支持 Razor 页应用的单元测试。 测试数据的访问层 (DAL) 和页面模型帮助确保：

* Razor 页面应用的部分独立工作和一起作为一个单元应用构建过程。
* 类和方法具有有限的作用域的责任。
* 应用程序的行为方式上存在的其他文档。
* 自动的生成和部署期间发现错误由更新的代码，这些错误的回归。

本主题假定你基本了解 Razor 页面应用程序和单元测试。 如果您是熟悉 Razor 页面应用程序或测试概念，请参阅以下主题：

* [Razor 页面介绍](xref:razor-pages/index)
* [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)
* [单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）

示例项目包含两个应用：

| 应用         | 项目文件夹                        | 描述 |
| ----------- | ------------------------------------- | ----------- |
| 消息应用程序 | *src/RazorPagesTestSample*            | 允许用户添加、 删除其中一个，删除所有，并分析消息。 |
| 测试应用    | *tests/RazorPagesTestSample.Tests*    | 用于对消息应用进行单元测试：数据访问层 (DAL) 和索引页面模型。 |

可以使用内置测试功能的 IDE，如运行测试[Visual Studio](https://visualstudio.microsoft.com)。 如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行中，执行以下命令在命令提示符处*tests/RazorPagesTestSample.Tests*文件夹：

```console
dotnet test
```

## <a name="message-app-organization"></a>消息应用的组织

消息应用程序是一个简单的 Razor 页面消息系统，具有以下特征：

* 应用程序的索引页 (*pages/Index.cshtml*并*Pages/Index.cshtml.cs*) 提供 UI 和页面模型方法，用于控制添加、 删除和分析的消息 （每个消息的平均词）.
* 一条消息由描述`Message`类 (*Data/Message.cs*) 具有两个属性： `Id` （密钥） 和`Text`（消息）。 `Text`属性是必需的限制为 200 个字符。
* 使用存储的消息[Entity Framework 的内存中数据库](/ef/core/providers/in-memory/)&#8224;。
* 应用程序包含在其数据库上下文类中，数据访问层 (DAL) `AppDbContext` (*Data/AppDbContext.cs*)。 DAL 方法被标记为`virtual`，它允许模拟在测试中使用的方法。
* 如果数据库为空应用程序启动时，使用三个消息初始化消息存储区。 这些*设定种子的消息*还在测试中使用。

&#8224;EF 主题[测试与 InMemory](/ef/core/miscellaneous/testing/in-memory)，说明如何使用内存中数据库的使用 MSTest 的测试。 本主题使用[xUnit](https://xunit.github.io/)测试框架。 测试概念和跨不同测试框架的测试实现有类似，但不是完全相同。

虽然应用不使用存储库模式，并且不是有效的示例[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)，Razor 页面支持开发这些模式。 有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)并[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（此示例实现存储库模式）。

## <a name="test-app-organization"></a>测试应用程序的组织

测试应用程序是一个控制台应用程序内的*tests/RazorPagesTestSample.Tests*文件夹。

| 测试应用程序文件夹 | 描述 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* DAL 包含单元测试。</li><li>*IndexPageTests.cs*包含针对索引页面模型的单元测试。</li></ul> |
| *实用程序*     | 包含`TestingDbContextOptions`用来创建新的数据库上下文选项为每个 DAL 单元测试，以便将数据库重置为其基线条件的每个测试方法。 |

测试框架这[xUnit](https://xunit.github.io/)。 模拟框架的对象是[Moq](https://github.com/moq/moq4)。

## <a name="unit-tests-of-the-data-access-layer-dal"></a>单元测试的数据访问层 (DAL)

消息应用程序中包含的四个方法都具有 DAL`AppDbContext`类 (*src/RazorPagesTestSample/Data/AppDbContext.cs*)。 每个方法中测试应用程序具有一个或两个单元测试。

| DAL 方法               | 函数                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | 获取`List<Message>`从数据库按`Text`属性。 |
| `AddMessageAsync`        | 添加`Message`到数据库。                                          |
| `DeleteAllMessagesAsync` | 将删除所有`Message`数据库中的条目。                           |
| `DeleteMessageAsync`     | 将删除单个`Message`从数据库`Id`。                      |

需要的 DAL 单元测试[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)时创建一个新`AppDbContext`为每个测试。 一种方法创建`DbContextOptions`每个测试是使用[DbContextOptionsBuilder](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

此方法的问题是每个测试中的任何状态前次测试保留它接收的数据库。 在尝试写入不会相互干扰的原子单元测试时，则可能存在问题。 若要强制`AppDbContext`若要为每个测试中使用新的数据库上下文，提供`DbContextOptions`基于新的服务提供程序的实例。 测试应用演示了如何执行此操作使用其`Utilities`类方法`TestingDbContextOptions`(*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

使用`DbContextOptions`接 DAL 单元测试，以使用新数据库实例以原子方式运行每个测试：

```csharp
using (var db = new AppDbContext(Utilities.TestingDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

在每个测试方法`DataAccessLayerTest`类 (*UnitTests/DataAccessLayerTest.cs*) 遵循类似的排列 Act 断言模式：

1. 排列：为测试配置数据库和/或定义预期的结果。
1. Act:执行测试。
1. 断言：做出声明来确定测试结果是否成功完成。

例如，`DeleteMessageAsync`方法负责删除一条消息由标识其`Id`(*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

有两个测试此方法。 一个测试检查数据库中存在消息时，将方法删除一条消息。 如果不会更改数据库的其他方法测试消息`Id`为删除不存在。 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`方法如下所示：

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

首先，该方法执行准备步骤中，执行步骤准备发生。 获取和保存在种子设定消息`seedMessages`。 种子设定消息保存到数据库中。 与消息`Id`的`1`设置为删除。 当`DeleteMessageAsync`执行方法时，预期的消息应具有的所有消息使用除`Id`的`1`。 `expectedMessages`变量表示此预期的结果。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

方法的行为：`DeleteMessageAsync`执行方法并传入`recId`的`1`:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

最后，此方法获取`Messages`上下文中并将其到比较`expectedMessages`这两个相等的断言：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

要比较的两个`List<Message>`相同：

* 消息按排序`Id`。
* 消息对比较上`Text`属性。

类似的测试方法，`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`检查尝试删除不存在一条消息的结果。 在这种情况下，在数据库中预期的消息应等于后的实际消息`DeleteMessageAsync`执行方法。 应未更改数据库的内容：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>页面模型方法的单元测试

另一组单元测试的测试的页面模型方法负责。 在邮件应用中，索引页面模型中找到`IndexModel`类中*src/RazorPagesTestSample/Pages/Index.cshtml.cs*。

| 页面模型方法 | 函数 |
| ----------------- | -------- |
| `OnGetAsync` | 从 UI 使用 DAL 获取消息`GetMessagesAsync`方法。 |
| `OnPostAddMessageAsync` | 如果`ModelState`有效，调用`AddMessageAsync`向数据库添加一条消息。 |
| `OnPostDeleteAllMessagesAsync` | 调用`DeleteAllMessagesAsync`若要删除所有数据库中的消息。 |
| `OnPostDeleteMessageAsync` | 执行`DeleteMessageAsync`若要删除的消息`Id`指定。 |
| `OnPostAnalyzeMessagesAsync` | 如果一个或多个消息是在数据库中，将计算的平均每个消息的单词数。 |

使用七个测试中的测试页面模型方法`IndexPageTests`类 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)。 测试使用熟悉的排列断言 Act 模式。 这些测试的重点：

* 确定是否方法遵循正确的行为时`ModelState`无效。
* 确认方法产生正确`IActionResult`。
* 正在检查正确进行属性值分配。

测试此组通常模拟 DAL 以生成预期的页面模型方法会执行步骤的数据的方法。 例如，`GetMessagesAsync`方法的`AppDbContext`模拟以生成输出。 当页面模型方法执行此方法时，模拟将返回的结果。 数据并不是来自该数据库。 这将创建页面模型测试中使用 DAL 可预测、 可靠的测试的条件。

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`测试显示了如何将`GetMessagesAsync`方法模拟的页面模型：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

当`OnGetAsync`Act 步骤中执行方法时，它调用的页面模型`GetMessagesAsync`方法。

Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` 页面模型`OnGetAsync`方法 (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync` DAL 中的方法不会返回此方法调用的结果。 模拟的版本的方法返回的结果。

在中`Assert`步骤，实际消息 (`actualMessages`) 从分配的`Messages`的页面模型的属性。 分配消息时，也执行类型检查。 通过进行比较的预期和实际消息及其`Text`属性。 测试断言，这两个`List<Message>`实例包含相同的消息。

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

此组中的其他测试创建页包含的模型对象`DefaultHttpContext`，则`ModelStateDictionary`、`ActionContext`建立`PageContext`即`ViewDataDictionary`，和一个`PageContext`。 这些可执行测试。 例如，消息应用程序建立`ModelState`错误`AddModelError`检查是否是有效`PageResult`时，将返回`OnPostAddMessageAsync`执行：

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>其他资源

* [单元测试 C# 中使用 dotnet 测试和 xUnit 的.NET Core](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [测试控制器](xref:mvc/controllers/testing)
* [单元测试代码](/visualstudio/test/unit-test-your-code)(Visual Studio)
* [集成测试](xref:test/integration-tests)
* [xUnit.net](https://xunit.github.io/)
* [入门 xUnit.net （.NET Core/ASP.NET 核）](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq 快速入门](https://github.com/Moq/moq4/wiki/Quickstart)
