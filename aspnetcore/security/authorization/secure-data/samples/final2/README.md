# <a name="how-to-buildrun-secure-user-data-sample"></a>如何生成/运行安全的用户数据示例

* 使用机密管理器工具设置密码：

  `dotnet user-secrets set SeedUserPW <pw>`

* 更新数据库：

  `dotnet ef database update`

* 在项目中启用 HTTPS
