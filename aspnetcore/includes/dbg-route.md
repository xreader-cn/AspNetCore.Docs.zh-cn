## <a name="debug-diagnostics"></a>调试诊断

要获取详细的路由诊断输出，请将 `Logging:LogLevel:Microsoft` 设置为 `Debug`。 在开发环境中，在 appsettings.Development.json 中设置日志级别：

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
