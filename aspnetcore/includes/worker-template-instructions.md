# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 创建新项目。
1. 选择“辅助角色服务”。 选择“下一步”。
1. 在“项目名称”字段提供项目名称，或接受默认项目名称。 选择“创建”。
1. 在“创建辅助角色服务”对话框中，选择“创建”。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 创建新项目。
1. 在侧栏中的“.NET Core”下，选择“应用”。
1. 在“ASP.NET Core”下，选择“辅助角色”。 选择“下一步”。
1. 对于“目标框架”，选择“.NET Core 3.0”或更高版本。 选择“下一步”。
1. 在“项目名称”字段中提供名称。 选择“创建”。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

将辅助角色服务 (`worker`) 模板用于命令行界面中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。 下面的示例中创建了名为 `ContosoWorker` 的辅助角色服务应用。 执行命令时会自动为 `ContosoWorker` 应用创建文件夹。

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
