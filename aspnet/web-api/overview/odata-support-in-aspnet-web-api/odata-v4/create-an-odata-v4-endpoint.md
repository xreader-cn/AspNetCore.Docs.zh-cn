---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
title: 创建 OData v4 终结点使用 ASP.NET Web API 2.2 |Microsoft Docs
author: MikeWasson
description: 开放数据协议 (OData) 是一种用于 web 的数据访问协议。 OData 提供统一的方式来查询和操作通过 CRUD 操作的数据集...
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 1e1927c0-ded1-4752-80fd-a146628d2f09
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
msc.type: authoredcontent
ms.openlocfilehash: c6a4aa4eb563fd77d5afd9248175d5f5b7984d19
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667565"
---
# <a name="create-an-odata-v4-endpoint-using-aspnet-web-api"></a>创建 OData v4 终结点使用 ASP.NET Web API 

> 开放数据协议 (OData) 是一种用于 web 的数据访问协议。 OData 提供统一的方式来查询和操作数据集通过 CRUD 操作 （创建、 读取、 更新和删除）。
>
> ASP.NET Web API 支持 v3 和 v4 协议。 你甚至可以让运行的并行的 v4 终结点与 v3 终结点。
>
> 本教程演示如何创建支持 CRUD 操作的 OData v4 终结点。
>
> ## <a name="software-versions-used-in-the-tutorial"></a>在本教程中使用的软件版本
>
> - Web API 5.2
> - OData v4
> - Visual Studio 2017 (下载 Visual Studio 2017[此处](https://visualstudio.microsoft.com/downloads/))
> - Entity Framework 6
> - .NET 4.7.2
>
> ## <a name="tutorial-versions"></a>教程版本
>
> OData 版本 3，请参阅[创建 OData v3 终结点](../odata-v3/creating-an-odata-endpoint.md)。

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

在 Visual Studio 中，从**文件**菜单中，选择**新建** &gt; **项目**。

展开**已安装** &gt; **Visual C#**  &gt; **Web**，然后选择**ASP.NET Web 应用程序 (.NET Framework)** 模板。 将项目命名&quot;ProductService&quot;。

[![](create-an-odata-v4-endpoint/_static/image7.png)](create-an-odata-v4-endpoint/_static/image7.png)

选择 **确定**。



[![](create-an-odata-v4-endpoint/_static/image8.png)](create-an-odata-v4-endpoint/_static/image8.png)

选择**空**模板。 下**添加文件夹和核心引用：**，选择**Web API**。 选择 **确定**。

## <a name="install-the-odata-packages"></a>OData 包安装

从“工具”菜单中，选择“NuGet 程序包管理器”&gt;“包管理器控制台”。 在包管理器控制台窗口中，键入：

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample1.cmd)]

此命令将安装最新的 OData NuGet 包。

## <a name="add-a-model-class"></a>添加模型类

一个*模型*是一个对象，表示在应用程序中的数据实体。

在解决方案资源管理器，右键单击模型文件夹。 从上下文菜单中，选择**外** &gt; **类**。

[![](create-an-odata-v4-endpoint/_static/image6.png)](create-an-odata-v4-endpoint/_static/image5.png)

> [!NOTE]
> 按照约定，模型类都位于 Models 文件夹中，但无需遵循此约定在您自己的项目中。


将此类命名为 `Product`。 在 Product.cs 文件中，将替换以下为样板代码：

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample2.cs)]

`Id`属性是实体键。 客户端可以通过键查询实体。 例如，若要获取 id 为 5 的产品，URI 是`/Products(5)`。 `Id`属性也是后端数据库中的主键。

## <a name="enable-entity-framework"></a>启用实体框架

对于本教程，我们将使用 Entity Framework (EF) Code First 创建后端数据库。

> [!NOTE]
> Web API OData 不需要 EF。 使用可以转换为模型的数据库实体的任何数据访问层。


首先，安装 EF 的 NuGet 包。 从“工具”菜单中，选择“NuGet 程序包管理器”&gt;“包管理器控制台”。 在包管理器控制台窗口中，键入：

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample3.cmd)]

打开 Web.config 文件中，并添加以下节内的**配置**元素后面**configSections**元素。

[!code-xml[Main](create-an-odata-v4-endpoint/samples/sample4.xml?highlight=6)]

此设置将添加 LocalDB 数据库的连接字符串。 在本地运行应用时，将使用此数据库。

接下来，添加一个名为类`ProductsContext`到 Models 文件夹：

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample5.cs)]

在构造函数`"name=ProductsContext"`提供连接字符串的名称。

## <a name="configure-the-odata-endpoint"></a>配置 OData 终结点

打开文件应用\_Start/WebApiConfig.cs。 添加以下**使用**语句：

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample6.cs)]

然后将以下代码添加到**注册**方法：

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample7.cs)]

此代码执行两项操作：

- 创建实体数据模型 (EDM)。
- 添加一个路由。

EDM 是抽象的数据模型。 EDM 用于创建服务元数据文档。 **ODataConventionModelBuilder**类使用默认命名约定创建 EDM。 此方法要求最少的代码。 如果你想更好地控制 EDM，则可以使用**ODataModelBuilder**类，以通过添加属性、 键和导航属性显式创建 EDM。

一个*路由*告知如何将 HTTP 请求路由到终结点的 Web API。 若要创建 OData v4 路由，请调用**MapODataServiceRoute**扩展方法。

如果你的应用程序具有多个 OData 终结点，每个创建一个单独的路由。 为每个路由指定唯一的路由名称和前缀。

## <a name="add-the-odata-controller"></a>添加 OData 控制器

一个*控制器*是处理 HTTP 请求的类。 创建 OData 服务中设置每个实体的单独控制器。 在本教程中，您将创建一个控制器为`Product`实体。

在解决方案资源管理器，右键单击 Controllers 文件夹并选择**外** &gt; **类**。 将此类命名为 `ProductsController`。

> [!NOTE]
> 本教程，了解 OData v3 使用新版**添加控制器**基架。 目前，OData v4 没有基架。


ProductsController.cs 中的样板代码替换为以下。

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample8.cs)]

控制器使用`ProductsContext`类，以使用 EF 访问数据库。 请注意，在控制器重写**Dispose**方法来释放**ProductsContext**。

这是在控制器的起始点。 接下来，我们将添加所有 CRUD 操作的方法。

## <a name="query-the-entity-set"></a>查询实体集

添加以下方法向`ProductsController`。

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample9.cs)]

无参数版本的`Get`方法将返回整个产品集合。 `Get`方法替换*密钥*根据键查找产品参数 (在这种情况下，`Id`属性)。

**[EnableQuery]** 属性使客户端能够通过使用查询选项，例如 $filter、 $sort 和 $page 修改查询。 有关详细信息，请参阅[支持 OData 查询选项](../supporting-odata-query-options.md)。

## <a name="add-an-entity-to-the-entity-set"></a>将实体添加到实体集

若要允许客户端向数据库添加一个新的产品，添加以下方法`ProductsController`。

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample10.cs)]

## <a name="update-an-entity"></a>更新实体

OData 支持用于更新实体，PATCH 和 PUT 的两个不同的语义。

- 修补程序执行部分更新。 客户端指定只是要更新的属性。
- PUT 会替换整个实体。

PUT 的缺点是客户端必须在实体中，其中包括未更改的值发送的所有属性的值。 [OData 规范](http://docs.oasis-open.org/odata/odata/v4.0/os/part1-protocol/odata-v4.0-os-part1-protocol.html#_Toc372793719)状态修补程序是首选。

在任何情况下，下面是修补程序和 PUT 方法的代码：

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample11.cs)]

控制器使用修补程序，对于**增量&lt;T&gt;** 类型以跟踪所做的更改。

## <a name="delete-an-entity"></a>删除实体

若要使客户端能够从数据库中删除某个产品，添加以下方法`ProductsController`。

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample12.cs)]
