## <a name="debug-diagnostics"></a><span data-ttu-id="779e3-101">调试诊断</span><span class="sxs-lookup"><span data-stu-id="779e3-101">Debug diagnostics</span></span>

<span data-ttu-id="779e3-102">要获取详细的路由诊断输出，请将 `Logging:LogLevel:Microsoft` 设置为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="779e3-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="779e3-103">例如，在开发环境中，设置 appsettings.Development.json  ：</span><span class="sxs-lookup"><span data-stu-id="779e3-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

```JSON
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}