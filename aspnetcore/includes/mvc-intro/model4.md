<span data-ttu-id="e41d2-101">下表详细说明了 ASP.NET Core 代码生成器参数：</span><span class="sxs-lookup"><span data-stu-id="e41d2-101">The following table details the ASP.NET Core code generator parameters:</span></span>

| <span data-ttu-id="e41d2-102">参数</span><span class="sxs-lookup"><span data-stu-id="e41d2-102">Parameter</span></span>               | <span data-ttu-id="e41d2-103">说明</span><span class="sxs-lookup"><span data-stu-id="e41d2-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="e41d2-104">-m</span><span class="sxs-lookup"><span data-stu-id="e41d2-104">-m</span></span>  | <span data-ttu-id="e41d2-105">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="e41d2-105">The name of the model.</span></span> |
| <span data-ttu-id="e41d2-106">-dc</span><span class="sxs-lookup"><span data-stu-id="e41d2-106">-dc</span></span>  | <span data-ttu-id="e41d2-107">数据上下文。</span><span class="sxs-lookup"><span data-stu-id="e41d2-107">The data context.</span></span> |
| <span data-ttu-id="e41d2-108">-udl</span><span class="sxs-lookup"><span data-stu-id="e41d2-108">-udl</span></span> | <span data-ttu-id="e41d2-109">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="e41d2-109">Use the default layout.</span></span> |
| <span data-ttu-id="e41d2-110">--relativeFolderPath</span><span class="sxs-lookup"><span data-stu-id="e41d2-110">--relativeFolderPath</span></span> | <span data-ttu-id="e41d2-111">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="e41d2-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="e41d2-112">--useDefaultLayout</span><span class="sxs-lookup"><span data-stu-id="e41d2-112">--useDefaultLayout</span></span> | <span data-ttu-id="e41d2-113">应为视图使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="e41d2-113">The default layout should be used for the views.</span></span> |
| <span data-ttu-id="e41d2-114">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="e41d2-114">--referenceScriptLibraries</span></span> | <span data-ttu-id="e41d2-115">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="e41d2-115">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="e41d2-116">使用 `h` 开关获取 `aspnet-codegenerator controller` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="e41d2-116">Use the `h` switch to get help on the `aspnet-codegenerator controller` command:</span></span>

```console
dotnet aspnet-codegenerator controller -h
```