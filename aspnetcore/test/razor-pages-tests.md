---
title: ASP.NET Core 中的 Razor Pages 单元测试
author: rick-anderson
description: 了解如何为 Razor Pages 应用创建单元测试。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/razor-pages-tests
ms.openlocfilehash: 756af7f2b14512bd43aefd1a4e63e195c2daa138
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407754"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="c68db-103">ASP.NET Core 中的 Razor Pages 单元测试</span><span class="sxs-lookup"><span data-stu-id="c68db-103">Razor Pages unit tests in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c68db-104">ASP.NET Core 支持 Razor Pages 应用的单元测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-104">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="c68db-105">数据访问层 (DAL) 和页面模型测试有助于确保：</span><span class="sxs-lookup"><span data-stu-id="c68db-105">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="c68db-106">Razor Pages 应用的各个部分在应用构造过程中既可以独立运行，也可以作为一个整体运行。</span><span class="sxs-lookup"><span data-stu-id="c68db-106">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="c68db-107">类和方法具有有限责任范围。</span><span class="sxs-lookup"><span data-stu-id="c68db-107">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="c68db-108">存在有关应用应如何运行的其他文档。</span><span class="sxs-lookup"><span data-stu-id="c68db-108">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="c68db-109">回归指代码更新引起的错误，可在自动生成和部署过程中出现。</span><span class="sxs-lookup"><span data-stu-id="c68db-109">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="c68db-110">本主题假定你对 Razor Pages 应用和单元测试有基本的了解。</span><span class="sxs-lookup"><span data-stu-id="c68db-110">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="c68db-111">如果你不熟悉 Razor Pages 应用或测试概念，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="c68db-111">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="c68db-112">使用 dotnet test 和 xUnit 在 .NET Core 中进行 C# 单元测试</span><span class="sxs-lookup"><span data-stu-id="c68db-112">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="c68db-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c68db-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c68db-114">示例项目包含两个应用：</span><span class="sxs-lookup"><span data-stu-id="c68db-114">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="c68db-115">应用</span><span class="sxs-lookup"><span data-stu-id="c68db-115">App</span></span>         | <span data-ttu-id="c68db-116">项目文件夹</span><span class="sxs-lookup"><span data-stu-id="c68db-116">Project folder</span></span>                     | <span data-ttu-id="c68db-117">描述</span><span class="sxs-lookup"><span data-stu-id="c68db-117">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="c68db-118">消息应用</span><span class="sxs-lookup"><span data-stu-id="c68db-118">Message app</span></span> | <span data-ttu-id="c68db-119">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="c68db-119">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="c68db-120">允许用户添加消息、删除一条消息、删除所有消息以及分析消息（查找每条消息的平均字词数）。</span><span class="sxs-lookup"><span data-stu-id="c68db-120">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="c68db-121">测试应用</span><span class="sxs-lookup"><span data-stu-id="c68db-121">Test app</span></span>    | <span data-ttu-id="c68db-122">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="c68db-122">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="c68db-123">用于对消息应用的 DAL 和索引页面模型进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-123">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="c68db-124">可使用 IDE 的内置测试功能（例如 [Visual Studio](/visualstudio/test/unit-test-your-code) 或 [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)）运行测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-124">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="c68db-125">如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令行，请在 *tests/RazorPagesTestSample.Tests* 文件夹中的命令提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c68db-125">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="c68db-126">消息应用组织</span><span class="sxs-lookup"><span data-stu-id="c68db-126">Message app organization</span></span>

<span data-ttu-id="c68db-127">消息应用是具有以下特征的 Razor Pages 消息系统：</span><span class="sxs-lookup"><span data-stu-id="c68db-127">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="c68db-128">应用的索引页面（*Pages/Index.cshtml* 和 *Pages/Index.cshtml.cs*）提供 UI 和页面模型方法，用于控制添加、删除和分析消息（查找每条消息的平均字词数）。</span><span class="sxs-lookup"><span data-stu-id="c68db-128">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="c68db-129">消息由 `Message` 类 (*Data/Message.cs*) 描述，并具有两个属性：`Id`（键）和 `Text`（消息）。</span><span class="sxs-lookup"><span data-stu-id="c68db-129">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="c68db-130">`Text` 属性是必需的，并限制为 200 个字符。</span><span class="sxs-lookup"><span data-stu-id="c68db-130">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="c68db-131">消息使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)†存储。</span><span class="sxs-lookup"><span data-stu-id="c68db-131">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="c68db-132">应用在其数据库上下文类 `AppDbContext` (*Data/AppDbContext.cs*) 中包含 DAL。</span><span class="sxs-lookup"><span data-stu-id="c68db-132">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="c68db-133">DAL 方法标记为 `virtual`，这允许模拟在测试中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="c68db-133">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="c68db-134">如果应用启动时数据库为空，则消息存储初始化为三条消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-134">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="c68db-135">这些*种子消息*也用于测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-135">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="c68db-136">†EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明如何将内存中数据库用于 MSTest 测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-136">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="c68db-137">本主题使用 [xUnit](https://xunit.github.io/) 测试框架。</span><span class="sxs-lookup"><span data-stu-id="c68db-137">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="c68db-138">不同测试框架中的测试概念和测试实现相似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="c68db-138">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="c68db-139">尽管示例应用未使用存储库模式且不是[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些开发模式。</span><span class="sxs-lookup"><span data-stu-id="c68db-139">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="c68db-140">有关详细信息，请参阅[设计基础设施持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和 <xref:mvc/controllers/testing>（该示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="c68db-140">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="c68db-141">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="c68db-141">Test app organization</span></span>

<span data-ttu-id="c68db-142">测试应用是 *tests/RazorPagesTestSample.Tests* 文件夹中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="c68db-142">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="c68db-143">测试应用文件夹</span><span class="sxs-lookup"><span data-stu-id="c68db-143">Test app folder</span></span> | <span data-ttu-id="c68db-144">描述</span><span class="sxs-lookup"><span data-stu-id="c68db-144">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="c68db-145">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="c68db-145">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="c68db-146">*DataAccessLayerTest.cs* 包含 DAL 的单元测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-146">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="c68db-147">*IndexPageTests.cs* 包含索引页面模型的单元测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-147">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="c68db-148">*实用工具*</span><span class="sxs-lookup"><span data-stu-id="c68db-148">*Utilities*</span></span>     | <span data-ttu-id="c68db-149">包含 `TestDbContextOptions` 方法，该方法用于为每个 DAL 单元测试创建新的数据库上下文选项，以便为每个测试将数据库重置为其基线条件。</span><span class="sxs-lookup"><span data-stu-id="c68db-149">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="c68db-150">测试框架为 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="c68db-150">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="c68db-151">对象模拟框架为 [Moq](https://github.com/moq/moq4)。</span><span class="sxs-lookup"><span data-stu-id="c68db-151">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="c68db-152">数据访问层 (DAL) 的单元测试</span><span class="sxs-lookup"><span data-stu-id="c68db-152">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="c68db-153">消息应用具有 DAL，其中 `AppDbContext` 类 (*src/RazorPagesTestSample/Data/AppDbContext.cs*) 中包含四个方法。</span><span class="sxs-lookup"><span data-stu-id="c68db-153">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="c68db-154">每个方法在测试应用中都有一到两个单元测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-154">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="c68db-155">DAL 方法</span><span class="sxs-lookup"><span data-stu-id="c68db-155">DAL method</span></span>               | <span data-ttu-id="c68db-156">函数</span><span class="sxs-lookup"><span data-stu-id="c68db-156">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="c68db-157">从按 `Text` 属性排序的数据库获取 `List<Message>`。</span><span class="sxs-lookup"><span data-stu-id="c68db-157">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="c68db-158">向数据库添加 `Message`。</span><span class="sxs-lookup"><span data-stu-id="c68db-158">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="c68db-159">从数据库中删除所有 `Message` 条目。</span><span class="sxs-lookup"><span data-stu-id="c68db-159">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="c68db-160">按 `Id` 从数据库中删除单个 `Message`。</span><span class="sxs-lookup"><span data-stu-id="c68db-160">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="c68db-161">为每个测试创建新的 `AppDbContext` 时，DAL 的单元测试需要 <xref:Microsoft.EntityFrameworkCore.DbContextOptions>。</span><span class="sxs-lookup"><span data-stu-id="c68db-161">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="c68db-162">为每个测试创建 `DbContextOptions` 的一个方法是使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>：</span><span class="sxs-lookup"><span data-stu-id="c68db-162">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="c68db-163">此方法的问题在于，每个测试收到的数据库都处于之前测试中的状态。</span><span class="sxs-lookup"><span data-stu-id="c68db-163">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="c68db-164">尝试编写不会相互干扰的原子单元测试时，这可能会导致问题。</span><span class="sxs-lookup"><span data-stu-id="c68db-164">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="c68db-165">若要强制 `AppDbContext` 为每个测试使用新的数据库上下文，请提供基于新服务提供程序的 `DbContextOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="c68db-165">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="c68db-166">测试应用演示如何使用其 `Utilities` 类方法 `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*) 执行此操作：</span><span class="sxs-lookup"><span data-stu-id="c68db-166">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="c68db-167">在 DAL 单元测试中使用 `DbContextOptions` 可使每个测试使用新的数据库实例自动运行：</span><span class="sxs-lookup"><span data-stu-id="c68db-167">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="c68db-168">`DataAccessLayerTest` 类 (*UnitTests/DataAccessLayerTest.cs*) 中的每个测试方法都遵循类似的安排-执行-断言模式：</span><span class="sxs-lookup"><span data-stu-id="c68db-168">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="c68db-169">安排：为测试配置数据库和/或定义预期结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-169">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="c68db-170">执行：执行测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-170">Act: The test is executed.</span></span>
1. <span data-ttu-id="c68db-171">断言：进行断言以确定测试结果是否成功。</span><span class="sxs-lookup"><span data-stu-id="c68db-171">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="c68db-172">例如，`DeleteMessageAsync` 方法负责删除由其 `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*) 标识的单个消息：</span><span class="sxs-lookup"><span data-stu-id="c68db-172">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="c68db-173">此方法有两个测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-173">There are two tests for this method.</span></span> <span data-ttu-id="c68db-174">一个测试检查当数据库中存在消息时该方法是否删除消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-174">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="c68db-175">另一个方法测试在要删除的消息 `Id` 不存在的情况下，数据库是否保持不变。</span><span class="sxs-lookup"><span data-stu-id="c68db-175">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="c68db-176">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="c68db-176">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="c68db-177">首先，方法执行“安排”步骤，并在该步骤中为“执行”步骤做好准备。</span><span class="sxs-lookup"><span data-stu-id="c68db-177">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="c68db-178">获取种子消息并将其保存在 `seedMessages` 中。</span><span class="sxs-lookup"><span data-stu-id="c68db-178">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="c68db-179">种子消息会保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="c68db-179">The seeding messages are saved into the database.</span></span> <span data-ttu-id="c68db-180">`Id` 为 `1` 的消息设置为删除。</span><span class="sxs-lookup"><span data-stu-id="c68db-180">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="c68db-181">执行 `DeleteMessageAsync` 方法时，预期的消息应是除 `Id` 为 `1` 的消息以外的所有消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-181">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="c68db-182">`expectedMessages` 变量表示此预期结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-182">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="c68db-183">该方法执行：执行 `DeleteMessageAsync` 方法并传入值为 `1` 的 `recId`：</span><span class="sxs-lookup"><span data-stu-id="c68db-183">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="c68db-184">最后，该方法从上下文中获取 `Messages` 并将其与断言两者相等的 `expectedMessages` 进行比较：</span><span class="sxs-lookup"><span data-stu-id="c68db-184">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="c68db-185">若要比较两个 `List<Message>` 是否相同，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c68db-185">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="c68db-186">按 `Id` 排序消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-186">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="c68db-187">在 `Text` 属性上比较消息对。</span><span class="sxs-lookup"><span data-stu-id="c68db-187">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="c68db-188">类似的测试方法 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 检查尝试删除不存在的消息的结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-188">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="c68db-189">在这种情况下，执行 `DeleteMessageAsync` 方法后，数据库中的预期消息数应等于实际消息数。</span><span class="sxs-lookup"><span data-stu-id="c68db-189">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="c68db-190">数据库的内容不应有任何变化：</span><span class="sxs-lookup"><span data-stu-id="c68db-190">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="c68db-191">页面模型方法的单元测试</span><span class="sxs-lookup"><span data-stu-id="c68db-191">Unit tests of the page model methods</span></span>

<span data-ttu-id="c68db-192">另一组单元测试负责测试页面模型方法。</span><span class="sxs-lookup"><span data-stu-id="c68db-192">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="c68db-193">在消息应用中，可在 *src/RazorPagesTestSample/Pages/Index.cshtml.cs* 的 `IndexModel` 类中找到索引页面模型。</span><span class="sxs-lookup"><span data-stu-id="c68db-193">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="c68db-194">页面模型方法</span><span class="sxs-lookup"><span data-stu-id="c68db-194">Page model method</span></span> | <span data-ttu-id="c68db-195">函数</span><span class="sxs-lookup"><span data-stu-id="c68db-195">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="c68db-196">使用 `GetMessagesAsync` 方法从 DAL 获取 UI 的消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-196">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="c68db-197">如果 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 有效，则调用 `AddMessageAsync` 将消息添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="c68db-197">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="c68db-198">调用 `DeleteAllMessagesAsync` 以删除数据库中的所有消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-198">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="c68db-199">执行 `DeleteMessageAsync` 以删除指定了 `Id` 的消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-199">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="c68db-200">如果数据库中有一条或多条消息，请计算每条消息的平均字词数。</span><span class="sxs-lookup"><span data-stu-id="c68db-200">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="c68db-201">使用 `IndexPageTests` 类 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*) 中的七个测试来测试页面模型方法。</span><span class="sxs-lookup"><span data-stu-id="c68db-201">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="c68db-202">测试使用熟悉的安排-断言-执行模式。</span><span class="sxs-lookup"><span data-stu-id="c68db-202">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="c68db-203">这些测试的重点在于：</span><span class="sxs-lookup"><span data-stu-id="c68db-203">These tests focus on:</span></span>

* <span data-ttu-id="c68db-204">确定 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 无效时方法是否遵循正确的行为模式。</span><span class="sxs-lookup"><span data-stu-id="c68db-204">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="c68db-205">确认方法是否会生成正确的 <xref:Microsoft.AspNetCore.Mvc.IActionResult>。</span><span class="sxs-lookup"><span data-stu-id="c68db-205">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="c68db-206">检查属性值分配是否正确进行。</span><span class="sxs-lookup"><span data-stu-id="c68db-206">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="c68db-207">这一组测试经常模拟 DAL 的方法，以生成执行页面模型方法的“执行”步骤的预期数据。</span><span class="sxs-lookup"><span data-stu-id="c68db-207">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="c68db-208">例如，模拟 `AppDbContext` 的 `GetMessagesAsync` 方法以生成输出。</span><span class="sxs-lookup"><span data-stu-id="c68db-208">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="c68db-209">当页面模型方法执行此方法时，模拟返回结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-209">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="c68db-210">数据不来自数据库。</span><span class="sxs-lookup"><span data-stu-id="c68db-210">The data doesn't come from the database.</span></span> <span data-ttu-id="c68db-211">这会创建可预测的可靠测试条件，以便在页面模型测试中使用 DAL。</span><span class="sxs-lookup"><span data-stu-id="c68db-211">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="c68db-212">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 测试演示如何为页面模型模拟 `GetMessagesAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="c68db-212">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="c68db-213">在“执行”步骤中执行 `OnGetAsync` 方法时，它会调用页面模型的 `GetMessagesAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="c68db-213">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="c68db-214">单元测试“执行”步骤 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)：</span><span class="sxs-lookup"><span data-stu-id="c68db-214">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="c68db-215">`IndexPage` 页面模型的 `OnGetAsync` 方法 (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="c68db-215">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="c68db-216">DAL 中的 `GetMessagesAsync` 方法不会返回此方法调用的结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-216">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="c68db-217">方法的模拟版本返回结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-217">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="c68db-218">在 `Assert` 步骤中，从页面模型的 `Messages` 属性分配实际消息 (`actualMessages`)。</span><span class="sxs-lookup"><span data-stu-id="c68db-218">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="c68db-219">分配消息后，还会执行类型检查。</span><span class="sxs-lookup"><span data-stu-id="c68db-219">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="c68db-220">预期消息和实际消息通过其 `Text` 属性进行比较。</span><span class="sxs-lookup"><span data-stu-id="c68db-220">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="c68db-221">该测试断言两个 `List<Message>` 实例包含相同的消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-221">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="c68db-222">此组的其他测试创建页面模型对象，这些对象包括 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>、用于建立 `PageContext` 的 <xref:Microsoft.AspNetCore.Mvc.ActionContext>、`ViewDataDictionary` 和 `PageContext`。</span><span class="sxs-lookup"><span data-stu-id="c68db-222">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="c68db-223">这些对象对于执行测试很有用。</span><span class="sxs-lookup"><span data-stu-id="c68db-223">These are useful in conducting tests.</span></span> <span data-ttu-id="c68db-224">例如，消息应用与 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 建立 `ModelState` 错误，以检查执行 `OnPostAddMessageAsync` 时是否返回有效的 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>：</span><span class="sxs-lookup"><span data-stu-id="c68db-224">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="c68db-225">其他资源</span><span class="sxs-lookup"><span data-stu-id="c68db-225">Additional resources</span></span>

* [<span data-ttu-id="c68db-226">使用 dotnet test 和 xUnit 在 .NET Core 中进行 C# 单元测试</span><span class="sxs-lookup"><span data-stu-id="c68db-226">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="c68db-227">[对代码进行单元测试](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="c68db-227">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="c68db-228">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="c68db-228">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="c68db-229">使用 Visual Studio for Mac 在 macOS 上构建完整的 .NET Core 解决方案</span><span class="sxs-lookup"><span data-stu-id="c68db-229">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* <span data-ttu-id="c68db-230">[Getting started with xUnit.net:Using .NET Core with the .NET SDK command line](https://xunit.github.io/docs/getting-started-dotnet-core)（xUnit.net 入门：将 .NET Core 与 .NET SDK 命令行配合使用）</span><span class="sxs-lookup"><span data-stu-id="c68db-230">[Getting started with xUnit.net: Using .NET Core with the .NET SDK command line](https://xunit.github.io/docs/getting-started-dotnet-core)</span></span>
* [<span data-ttu-id="c68db-231">Moq</span><span class="sxs-lookup"><span data-stu-id="c68db-231">Moq</span></span>](https://github.com/moq/moq4)
* <span data-ttu-id="c68db-232">[Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)（Moq 快速入门）</span><span class="sxs-lookup"><span data-stu-id="c68db-232">[Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c68db-233">ASP.NET Core 支持 Razor Pages 应用的单元测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-233">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="c68db-234">数据访问层 (DAL) 和页面模型测试有助于确保：</span><span class="sxs-lookup"><span data-stu-id="c68db-234">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="c68db-235">Razor Pages 应用的各个部分在应用构造过程中既可以独立运行，也可以作为一个整体运行。</span><span class="sxs-lookup"><span data-stu-id="c68db-235">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="c68db-236">类和方法具有有限责任范围。</span><span class="sxs-lookup"><span data-stu-id="c68db-236">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="c68db-237">存在有关应用应如何运行的其他文档。</span><span class="sxs-lookup"><span data-stu-id="c68db-237">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="c68db-238">回归指代码更新引起的错误，可在自动生成和部署过程中出现。</span><span class="sxs-lookup"><span data-stu-id="c68db-238">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="c68db-239">本主题假定你对 Razor Pages 应用和单元测试有基本的了解。</span><span class="sxs-lookup"><span data-stu-id="c68db-239">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="c68db-240">如果你不熟悉 Razor Pages 应用或测试概念，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="c68db-240">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="c68db-241">使用 dotnet test 和 xUnit 在 .NET Core 中进行 C# 单元测试</span><span class="sxs-lookup"><span data-stu-id="c68db-241">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="c68db-242">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c68db-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c68db-243">示例项目包含两个应用：</span><span class="sxs-lookup"><span data-stu-id="c68db-243">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="c68db-244">应用</span><span class="sxs-lookup"><span data-stu-id="c68db-244">App</span></span>         | <span data-ttu-id="c68db-245">项目文件夹</span><span class="sxs-lookup"><span data-stu-id="c68db-245">Project folder</span></span>                     | <span data-ttu-id="c68db-246">描述</span><span class="sxs-lookup"><span data-stu-id="c68db-246">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="c68db-247">消息应用</span><span class="sxs-lookup"><span data-stu-id="c68db-247">Message app</span></span> | <span data-ttu-id="c68db-248">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="c68db-248">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="c68db-249">允许用户添加消息、删除一条消息、删除所有消息以及分析消息（查找每条消息的平均字词数）。</span><span class="sxs-lookup"><span data-stu-id="c68db-249">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="c68db-250">测试应用</span><span class="sxs-lookup"><span data-stu-id="c68db-250">Test app</span></span>    | <span data-ttu-id="c68db-251">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="c68db-251">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="c68db-252">用于对消息应用的 DAL 和索引页面模型进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-252">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="c68db-253">可使用 IDE 的内置测试功能（例如 [Visual Studio](/visualstudio/test/unit-test-your-code) 或 [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)）运行测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-253">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="c68db-254">如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令行，请在 *tests/RazorPagesTestSample.Tests* 文件夹中的命令提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c68db-254">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="c68db-255">消息应用组织</span><span class="sxs-lookup"><span data-stu-id="c68db-255">Message app organization</span></span>

<span data-ttu-id="c68db-256">消息应用是具有以下特征的 Razor Pages 消息系统：</span><span class="sxs-lookup"><span data-stu-id="c68db-256">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="c68db-257">应用的索引页面（*Pages/Index.cshtml* 和 *Pages/Index.cshtml.cs*）提供 UI 和页面模型方法，用于控制添加、删除和分析消息（查找每条消息的平均字词数）。</span><span class="sxs-lookup"><span data-stu-id="c68db-257">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="c68db-258">消息由 `Message` 类 (*Data/Message.cs*) 描述，并具有两个属性：`Id`（键）和 `Text`（消息）。</span><span class="sxs-lookup"><span data-stu-id="c68db-258">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="c68db-259">`Text` 属性是必需的，并限制为 200 个字符。</span><span class="sxs-lookup"><span data-stu-id="c68db-259">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="c68db-260">消息使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)†存储。</span><span class="sxs-lookup"><span data-stu-id="c68db-260">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="c68db-261">应用在其数据库上下文类 `AppDbContext` (*Data/AppDbContext.cs*) 中包含 DAL。</span><span class="sxs-lookup"><span data-stu-id="c68db-261">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="c68db-262">DAL 方法标记为 `virtual`，这允许模拟在测试中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="c68db-262">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="c68db-263">如果应用启动时数据库为空，则消息存储初始化为三条消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-263">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="c68db-264">这些*种子消息*也用于测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-264">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="c68db-265">†EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明如何将内存中数据库用于 MSTest 测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-265">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="c68db-266">本主题使用 [xUnit](https://xunit.github.io/) 测试框架。</span><span class="sxs-lookup"><span data-stu-id="c68db-266">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="c68db-267">不同测试框架中的测试概念和测试实现相似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="c68db-267">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="c68db-268">尽管示例应用未使用存储库模式且不是[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些开发模式。</span><span class="sxs-lookup"><span data-stu-id="c68db-268">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="c68db-269">有关详细信息，请参阅[设计基础设施持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和 <xref:mvc/controllers/testing>（该示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="c68db-269">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="c68db-270">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="c68db-270">Test app organization</span></span>

<span data-ttu-id="c68db-271">测试应用是 *tests/RazorPagesTestSample.Tests* 文件夹中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="c68db-271">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="c68db-272">测试应用文件夹</span><span class="sxs-lookup"><span data-stu-id="c68db-272">Test app folder</span></span> | <span data-ttu-id="c68db-273">描述</span><span class="sxs-lookup"><span data-stu-id="c68db-273">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="c68db-274">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="c68db-274">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="c68db-275">*DataAccessLayerTest.cs* 包含 DAL 的单元测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-275">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="c68db-276">*IndexPageTests.cs* 包含索引页面模型的单元测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-276">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="c68db-277">*实用工具*</span><span class="sxs-lookup"><span data-stu-id="c68db-277">*Utilities*</span></span>     | <span data-ttu-id="c68db-278">包含 `TestDbContextOptions` 方法，该方法用于为每个 DAL 单元测试创建新的数据库上下文选项，以便为每个测试将数据库重置为其基线条件。</span><span class="sxs-lookup"><span data-stu-id="c68db-278">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="c68db-279">测试框架为 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="c68db-279">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="c68db-280">对象模拟框架为 [Moq](https://github.com/moq/moq4)。</span><span class="sxs-lookup"><span data-stu-id="c68db-280">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="c68db-281">数据访问层 (DAL) 的单元测试</span><span class="sxs-lookup"><span data-stu-id="c68db-281">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="c68db-282">消息应用具有 DAL，其中 `AppDbContext` 类 (*src/RazorPagesTestSample/Data/AppDbContext.cs*) 中包含四个方法。</span><span class="sxs-lookup"><span data-stu-id="c68db-282">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="c68db-283">每个方法在测试应用中都有一到两个单元测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-283">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="c68db-284">DAL 方法</span><span class="sxs-lookup"><span data-stu-id="c68db-284">DAL method</span></span>               | <span data-ttu-id="c68db-285">函数</span><span class="sxs-lookup"><span data-stu-id="c68db-285">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="c68db-286">从按 `Text` 属性排序的数据库获取 `List<Message>`。</span><span class="sxs-lookup"><span data-stu-id="c68db-286">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="c68db-287">向数据库添加 `Message`。</span><span class="sxs-lookup"><span data-stu-id="c68db-287">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="c68db-288">从数据库中删除所有 `Message` 条目。</span><span class="sxs-lookup"><span data-stu-id="c68db-288">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="c68db-289">按 `Id` 从数据库中删除单个 `Message`。</span><span class="sxs-lookup"><span data-stu-id="c68db-289">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="c68db-290">为每个测试创建新的 `AppDbContext` 时，DAL 的单元测试需要 <xref:Microsoft.EntityFrameworkCore.DbContextOptions>。</span><span class="sxs-lookup"><span data-stu-id="c68db-290">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="c68db-291">为每个测试创建 `DbContextOptions` 的一个方法是使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>：</span><span class="sxs-lookup"><span data-stu-id="c68db-291">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="c68db-292">此方法的问题在于，每个测试收到的数据库都处于之前测试中的状态。</span><span class="sxs-lookup"><span data-stu-id="c68db-292">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="c68db-293">尝试编写不会相互干扰的原子单元测试时，这可能会导致问题。</span><span class="sxs-lookup"><span data-stu-id="c68db-293">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="c68db-294">若要强制 `AppDbContext` 为每个测试使用新的数据库上下文，请提供基于新服务提供程序的 `DbContextOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="c68db-294">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="c68db-295">测试应用演示如何使用其 `Utilities` 类方法 `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*) 执行此操作：</span><span class="sxs-lookup"><span data-stu-id="c68db-295">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="c68db-296">在 DAL 单元测试中使用 `DbContextOptions` 可使每个测试使用新的数据库实例自动运行：</span><span class="sxs-lookup"><span data-stu-id="c68db-296">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="c68db-297">`DataAccessLayerTest` 类 (*UnitTests/DataAccessLayerTest.cs*) 中的每个测试方法都遵循类似的安排-执行-断言模式：</span><span class="sxs-lookup"><span data-stu-id="c68db-297">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="c68db-298">安排：为测试配置数据库和/或定义预期结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-298">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="c68db-299">执行：执行测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-299">Act: The test is executed.</span></span>
1. <span data-ttu-id="c68db-300">断言：进行断言以确定测试结果是否成功。</span><span class="sxs-lookup"><span data-stu-id="c68db-300">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="c68db-301">例如，`DeleteMessageAsync` 方法负责删除由其 `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*) 标识的单个消息：</span><span class="sxs-lookup"><span data-stu-id="c68db-301">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="c68db-302">此方法有两个测试。</span><span class="sxs-lookup"><span data-stu-id="c68db-302">There are two tests for this method.</span></span> <span data-ttu-id="c68db-303">一个测试检查当数据库中存在消息时该方法是否删除消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-303">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="c68db-304">另一个方法测试在要删除的消息 `Id` 不存在的情况下，数据库是否保持不变。</span><span class="sxs-lookup"><span data-stu-id="c68db-304">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="c68db-305">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="c68db-305">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="c68db-306">首先，方法执行“安排”步骤，并在该步骤中为“执行”步骤做好准备。</span><span class="sxs-lookup"><span data-stu-id="c68db-306">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="c68db-307">获取种子消息并将其保存在 `seedMessages` 中。</span><span class="sxs-lookup"><span data-stu-id="c68db-307">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="c68db-308">种子消息会保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="c68db-308">The seeding messages are saved into the database.</span></span> <span data-ttu-id="c68db-309">`Id` 为 `1` 的消息设置为删除。</span><span class="sxs-lookup"><span data-stu-id="c68db-309">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="c68db-310">执行 `DeleteMessageAsync` 方法时，预期的消息应是除 `Id` 为 `1` 的消息以外的所有消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-310">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="c68db-311">`expectedMessages` 变量表示此预期结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-311">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="c68db-312">该方法执行：执行 `DeleteMessageAsync` 方法并传入值为 `1` 的 `recId`：</span><span class="sxs-lookup"><span data-stu-id="c68db-312">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="c68db-313">最后，该方法从上下文中获取 `Messages` 并将其与断言两者相等的 `expectedMessages` 进行比较：</span><span class="sxs-lookup"><span data-stu-id="c68db-313">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="c68db-314">若要比较两个 `List<Message>` 是否相同，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c68db-314">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="c68db-315">按 `Id` 排序消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-315">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="c68db-316">在 `Text` 属性上比较消息对。</span><span class="sxs-lookup"><span data-stu-id="c68db-316">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="c68db-317">类似的测试方法 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 检查尝试删除不存在的消息的结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-317">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="c68db-318">在这种情况下，执行 `DeleteMessageAsync` 方法后，数据库中的预期消息数应等于实际消息数。</span><span class="sxs-lookup"><span data-stu-id="c68db-318">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="c68db-319">数据库的内容不应有任何变化：</span><span class="sxs-lookup"><span data-stu-id="c68db-319">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="c68db-320">页面模型方法的单元测试</span><span class="sxs-lookup"><span data-stu-id="c68db-320">Unit tests of the page model methods</span></span>

<span data-ttu-id="c68db-321">另一组单元测试负责测试页面模型方法。</span><span class="sxs-lookup"><span data-stu-id="c68db-321">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="c68db-322">在消息应用中，可在 *src/RazorPagesTestSample/Pages/Index.cshtml.cs* 的 `IndexModel` 类中找到索引页面模型。</span><span class="sxs-lookup"><span data-stu-id="c68db-322">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="c68db-323">页面模型方法</span><span class="sxs-lookup"><span data-stu-id="c68db-323">Page model method</span></span> | <span data-ttu-id="c68db-324">函数</span><span class="sxs-lookup"><span data-stu-id="c68db-324">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="c68db-325">使用 `GetMessagesAsync` 方法从 DAL 获取 UI 的消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-325">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="c68db-326">如果 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 有效，则调用 `AddMessageAsync` 将消息添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="c68db-326">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="c68db-327">调用 `DeleteAllMessagesAsync` 以删除数据库中的所有消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-327">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="c68db-328">执行 `DeleteMessageAsync` 以删除指定了 `Id` 的消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-328">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="c68db-329">如果数据库中有一条或多条消息，请计算每条消息的平均字词数。</span><span class="sxs-lookup"><span data-stu-id="c68db-329">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="c68db-330">使用 `IndexPageTests` 类 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*) 中的七个测试来测试页面模型方法。</span><span class="sxs-lookup"><span data-stu-id="c68db-330">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="c68db-331">测试使用熟悉的安排-断言-执行模式。</span><span class="sxs-lookup"><span data-stu-id="c68db-331">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="c68db-332">这些测试的重点在于：</span><span class="sxs-lookup"><span data-stu-id="c68db-332">These tests focus on:</span></span>

* <span data-ttu-id="c68db-333">确定 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 无效时方法是否遵循正确的行为模式。</span><span class="sxs-lookup"><span data-stu-id="c68db-333">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="c68db-334">确认方法是否会生成正确的 <xref:Microsoft.AspNetCore.Mvc.IActionResult>。</span><span class="sxs-lookup"><span data-stu-id="c68db-334">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="c68db-335">检查属性值分配是否正确进行。</span><span class="sxs-lookup"><span data-stu-id="c68db-335">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="c68db-336">这一组测试经常模拟 DAL 的方法，以生成执行页面模型方法的“执行”步骤的预期数据。</span><span class="sxs-lookup"><span data-stu-id="c68db-336">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="c68db-337">例如，模拟 `AppDbContext` 的 `GetMessagesAsync` 方法以生成输出。</span><span class="sxs-lookup"><span data-stu-id="c68db-337">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="c68db-338">当页面模型方法执行此方法时，模拟返回结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-338">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="c68db-339">数据不来自数据库。</span><span class="sxs-lookup"><span data-stu-id="c68db-339">The data doesn't come from the database.</span></span> <span data-ttu-id="c68db-340">这会创建可预测的可靠测试条件，以便在页面模型测试中使用 DAL。</span><span class="sxs-lookup"><span data-stu-id="c68db-340">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="c68db-341">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 测试演示如何为页面模型模拟 `GetMessagesAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="c68db-341">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="c68db-342">在“执行”步骤中执行 `OnGetAsync` 方法时，它会调用页面模型的 `GetMessagesAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="c68db-342">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="c68db-343">单元测试“执行”步骤 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)：</span><span class="sxs-lookup"><span data-stu-id="c68db-343">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="c68db-344">`IndexPage` 页面模型的 `OnGetAsync` 方法 (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="c68db-344">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="c68db-345">DAL 中的 `GetMessagesAsync` 方法不会返回此方法调用的结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-345">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="c68db-346">方法的模拟版本返回结果。</span><span class="sxs-lookup"><span data-stu-id="c68db-346">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="c68db-347">在 `Assert` 步骤中，从页面模型的 `Messages` 属性分配实际消息 (`actualMessages`)。</span><span class="sxs-lookup"><span data-stu-id="c68db-347">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="c68db-348">分配消息后，还会执行类型检查。</span><span class="sxs-lookup"><span data-stu-id="c68db-348">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="c68db-349">预期消息和实际消息通过其 `Text` 属性进行比较。</span><span class="sxs-lookup"><span data-stu-id="c68db-349">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="c68db-350">该测试断言两个 `List<Message>` 实例包含相同的消息。</span><span class="sxs-lookup"><span data-stu-id="c68db-350">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="c68db-351">此组的其他测试创建页面模型对象，这些对象包括 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>、<xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>、用于建立 `PageContext` 的 <xref:Microsoft.AspNetCore.Mvc.ActionContext>、`ViewDataDictionary` 和 `PageContext`。</span><span class="sxs-lookup"><span data-stu-id="c68db-351">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="c68db-352">这些对象对于执行测试很有用。</span><span class="sxs-lookup"><span data-stu-id="c68db-352">These are useful in conducting tests.</span></span> <span data-ttu-id="c68db-353">例如，消息应用与 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 建立 `ModelState` 错误，以检查执行 `OnPostAddMessageAsync` 时是否返回有效的 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>：</span><span class="sxs-lookup"><span data-stu-id="c68db-353">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="c68db-354">其他资源</span><span class="sxs-lookup"><span data-stu-id="c68db-354">Additional resources</span></span>

* [<span data-ttu-id="c68db-355">使用 dotnet test 和 xUnit 在 .NET Core 中进行 C# 单元测试</span><span class="sxs-lookup"><span data-stu-id="c68db-355">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="c68db-356">[对代码进行单元测试](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="c68db-356">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="c68db-357">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="c68db-357">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="c68db-358">使用 Visual Studio for Mac 在 macOS 上构建完整的 .NET Core 解决方案</span><span class="sxs-lookup"><span data-stu-id="c68db-358">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* <span data-ttu-id="c68db-359">[Getting started with xUnit.net:Using .NET Core with the .NET SDK command line](https://xunit.github.io/docs/getting-started-dotnet-core)（xUnit.net 入门：将 .NET Core 与 .NET SDK 命令行配合使用）</span><span class="sxs-lookup"><span data-stu-id="c68db-359">[Getting started with xUnit.net: Using .NET Core with the .NET SDK command line](https://xunit.github.io/docs/getting-started-dotnet-core)</span></span>
* [<span data-ttu-id="c68db-360">Moq</span><span class="sxs-lookup"><span data-stu-id="c68db-360">Moq</span></span>](https://github.com/moq/moq4)
* <span data-ttu-id="c68db-361">[Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)（Moq 快速入门）</span><span class="sxs-lookup"><span data-stu-id="c68db-361">[Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)</span></span>

::: moniker-end
