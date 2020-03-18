<a name="codegenerator"></a> <span data-ttu-id="af15d-101">下表详细说明了 ASP.NET Core 代码生成器参数：</span><span class="sxs-lookup"><span data-stu-id="af15d-101">The following table details the ASP.NET Core code generator parameters:</span></span>

| <span data-ttu-id="af15d-102">参数</span><span class="sxs-lookup"><span data-stu-id="af15d-102">Parameter</span></span>               | <span data-ttu-id="af15d-103">描述</span><span class="sxs-lookup"><span data-stu-id="af15d-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="af15d-104">-m</span><span class="sxs-lookup"><span data-stu-id="af15d-104">-m</span></span>  | <span data-ttu-id="af15d-105">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="af15d-105">The name of the model.</span></span> |
| <span data-ttu-id="af15d-106">-dc</span><span class="sxs-lookup"><span data-stu-id="af15d-106">-dc</span></span>  | <span data-ttu-id="af15d-107">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="af15d-107">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="af15d-108">-udl</span><span class="sxs-lookup"><span data-stu-id="af15d-108">-udl</span></span> | <span data-ttu-id="af15d-109">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="af15d-109">Use the default layout.</span></span> |
| <span data-ttu-id="af15d-110">-outDir</span><span class="sxs-lookup"><span data-stu-id="af15d-110">-outDir</span></span> | <span data-ttu-id="af15d-111">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="af15d-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="af15d-112">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="af15d-112">--referenceScriptLibraries</span></span> | <span data-ttu-id="af15d-113">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="af15d-113">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="af15d-114">使用 `h` 开关获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="af15d-114">Use the `h` switch to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="af15d-115">有关详细信息，请查看 [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="af15d-115">For more information, see [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
