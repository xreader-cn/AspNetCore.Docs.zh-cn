## <a name="debug-diagnostics"></a><span data-ttu-id="46413-101">调试诊断</span><span class="sxs-lookup"><span data-stu-id="46413-101">Debug diagnostics</span></span>

<span data-ttu-id="46413-102">要获取详细的路由诊断输出，请将 `Logging:LogLevel:Microsoft` 设置为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="46413-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="46413-103">在开发环境中，将*appsettings.Development.js上*的日志级别设置为：</span><span class="sxs-lookup"><span data-stu-id="46413-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

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
