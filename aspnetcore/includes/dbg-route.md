## <a name="debug-diagnostics"></a><span data-ttu-id="c303c-101">调试诊断</span><span class="sxs-lookup"><span data-stu-id="c303c-101">Debug diagnostics</span></span>

<span data-ttu-id="c303c-102">要获取详细的路由诊断输出，请将 `Logging:LogLevel:Microsoft` 设置为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="c303c-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="c303c-103">在开发环境中，在 appsettings.Development.json 中设置日志级别：</span><span class="sxs-lookup"><span data-stu-id="c303c-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

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
