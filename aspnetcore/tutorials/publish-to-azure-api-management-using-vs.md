---
title: 使用 Visual Studio 将 ASP.NET Core Web API 发布到 Azure API 管理
author: codemillmatt
description: 了解如何使用 Visual Studio 将 ASP.NET Core Web API 发布到 Azure API 管理。
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 08/26/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 3cc6b8c0bd93f133151e1c8ad18a55b11975a9be
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945453"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a>使用 Visual Studio 将 ASP.NET Core Web API 发布到 Azure API 管理

作者：[Matt Soucoup](https://twitter.com/codemillmatt)

## <a name="set-up"></a>设置

- 如果没有[免费 Azure 帐户](https://azure.microsoft.com/free/dotnet/)，请开设一个。
- [创建新的 Azure API 管理实例](/azure/api-management/get-started-create-service-instance)（如果尚未创建）。
- [创建 Web API 应用项目](xref:tutorials/first-web-api#create-a-web-project).

## <a name="configure-the-app"></a>配置应用

通过将 Swagger 定义添加到 ASP.NET Core Web API，Azure API 管理可读取应用的 API 定义。 完成以下步骤。

### <a name="add-swagger"></a>添加 Swagger

1. 将 [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet 包添加到 ASP.NET Core Web API 项目：

    ![配置 NuGet 对话框的屏幕截图](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. 将以下行添加到 `Startup.ConfigureServices` 方法中：
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. 将以下行添加到 `Startup.Configure` 方法中：

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a>更改 API 路由

接下来将更改访问 `WeatherForecastController` 的 `Get` 操作所需的 URL 结构。 完成以下步骤：

1. 打开 WeatherForecastController.cs 文件。
1. 删除 `[Route("[controller]")]` 类级特性。 类定义将如下所示：

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. 向 `Get()` 操作添加 `[Route("/")]` 特性。 函数定义将如下所示：

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a>将 Web API 发布到 Azure 应用服务

完成以下步骤以将 ASP.NET Core Web API 发布到 Azure API 管理：

1. 将 API 应用发布到 Azure 应用服务。
1. 将空白 API 添加到 Azure API 管理服务实例。
1. 将 ASP.NET Core Web API 应用发布到 Azure API 管理服务实例。

### <a name="publish-the-api-app-to-azure-app-service"></a>将 API 应用发布到 Azure 应用服务

完成以下步骤以将 ASP.NET Core Web API 发布到 Azure API 管理：

1. 在“解决方案资源管理器”中，右键单击项目，然后选择“发布” ：

    ![打开且突出显示“发布”链接的上下文菜单](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. 在“发布”对话框中，选择“Azure”，然后选择“下一步”按钮  ：

    ![“发布”对话框](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. 选择“Azure 应用服务(Windows)”，然后选择“下一步”按钮 ：

    ![“发布”对话框：选择应用服务](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. 选择“创建新的 Azure 应用服务”。

    ![“发布”对话框：选择 Azure 服务实例](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    此时会显示“创建应用服务”对话框。 “应用名称”、“资源组”和“应用服务计划”输入字段已填充  。 可以保留这些名称，也可以进行更改。
1. 选择“创建”按钮。

    ![“创建应用服务”对话框](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

创建完成后，对话框将自动关闭，“发布”对话框将再次成为焦点。 将自动选择创建的实例。

![“发布”对话框：选择应用服务实例](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

此时，需要将 API 添加到 Azure API 管理服务。 完成以下任务时，请使 Visual Studio 保持打开状态。

### <a name="add-an-api-to-azure-api-management"></a>将 API 添加到 Azure API 管理

1. 在 Azure 门户中打开先前创建的 API 管理服务实例。 选择“API”边栏选项卡：

    ![从 API 管理服务实例中选择了“API”边栏选项卡](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. 从“添加新 API”面板中，选择“空白 API”磁贴 ：

    ![突出显示了“空白 API”磁贴的屏幕](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. 在出现的“创建空白 API”对话框中输入以下值：    

    - **显示名称**：WeatherForecasts
    - **名称**：weatherforecasts
    - **API URL 后缀**：v1
    - 将“Web 服务 URL”字段保留为空。

1. 选择“创建”按钮。

    ![“创建空白 API”对话完成时的屏幕截图](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

已创建空白 API。

![API 管理门户中空白 API 的屏幕截图](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a>将 ASP.NET Core Web API 发布到 Azure API 管理

1. 切换回 Visual Studio。 “发布”对话框应在你之前离开的位置保持打开状态。
1. 选择新发布的 Azure 应用服务，使其突出显示。
1. 选择“下一步”按钮。

    ![“发布”对话框的屏幕截图，其中已选择应用服务](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. 此对话框现在显示之前创建的 Azure API 管理服务。 将其和 APIs 文件夹展开，查看你创建的空白 API。
1. 选择空白 API 的名称，然后选择“完成”按钮。

    ![“发布”对话框的屏幕截图，其中已选择新创建的 Azure API 管理空白 API](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. 该对话框关闭并显示一个摘要屏幕，提供有关发布的信息。 选择“发布”按钮。

    ![Visual Studio 的屏幕截图，其中显示了发布配置文件](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    Web API 将同时发布到 Azure 应用服务和 Azure API 管理。 将显示一个新的浏览器窗口，并显示在 Azure 应用服务中运行的 API。 可关闭该窗口。

1. 在 Azure 门户中切换回 Azure API 管理实例。
1. 刷新浏览器窗口。
1. 选择在前面步骤中创建的空白 API。 它现在已填充，你可以仔细浏览。

    ![Azure API 管理中已填充的 API 的屏幕截图](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a>配置已发布的 API 名称

请注意，API 名称不同于你命名的名称。 已发布的 API 名称为 WeatherAPI；但你在创建时将其命名为 WeatherForecasts 。 完成以下步骤来修复该名称：

![已填充 API 的屏幕截图，其中突出显示了不同名称](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. 删除 `Startup.ConfigureServices` 方法中的以下行：
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. 将以下代码添加到 `Startup.ConfigureServices` 方法中：
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. 打开新创建的发布配置文件。 可从“解决方案资源管理器”在 Properties/PublishProfiles 文件夹中找到它。

    ![突出显示发布配置文件位置的屏幕截图](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. 将 `<OpenAPIDocumentName>` 元素的值从 `v1` 更改为 `WeatherForecasts`。

    ![发布配置文件所需更改的屏幕截图](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. 重新发布 ASP.NET Core Web API 并在 Azure 门户中打开 Azure API 管理实例。
1. 在浏览器中刷新页面。 此时会显示 API 的名称是正确的。

    ![门户中已完成的 API 的屏幕截图](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a>验证 Web API 是否正常工作

可通过以下步骤从 Azure 门户的“Azure API 管理”中测试已部署的 ASP.NET Core Web API：

1. 打开“测试”选项卡。 
1. 选择 / 或 Get 操作 。
1. 选择**Send**。

    ![测试前门户的屏幕截图](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

成功的响应如下所示：

![API 管理中成功的响应的屏幕截图](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a>知识巩固

完成应用测试后，转到 [Azure 门户](https://portal.azure.com/)并删除该应用。

1. 选择“资源组”，然后选择所创建的资源组。

    ![Azure 门户：侧边栏菜单中的资源组](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. 在“资源组”页面中，选择“删除” 。

    ![Azure 门户：“资源组”页](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. 输入资源组的名称并选择“删除”。 现已从 Azure 中删除了本教程中创建的应用和其他所有资源。

## <a name="next-steps"></a>后续步骤

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>其他资源

- [Azure API 管理](/azure/api-management/api-management-key-concepts)
- [Azure 应用服务](/azure/app-service/app-service-web-overview)
