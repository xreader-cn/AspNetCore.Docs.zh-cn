```console
npm run release
```

<span data-ttu-id="ff8ac-101">此命令在运行应用时生成要提供的客户端资产。</span><span class="sxs-lookup"><span data-stu-id="ff8ac-101">This command generates the client-side assets to be served when running the app.</span></span> <span data-ttu-id="ff8ac-102">资产位于 wwwroot 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="ff8ac-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="ff8ac-103">Webpack 完成了以下任务：</span><span class="sxs-lookup"><span data-stu-id="ff8ac-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="ff8ac-104">清除了 wwwroot 目录的内容  。</span><span class="sxs-lookup"><span data-stu-id="ff8ac-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="ff8ac-105">将 TypeScript 转换为 JavaScript，该过程称为“转译”  。</span><span class="sxs-lookup"><span data-stu-id="ff8ac-105">Converted the TypeScript to JavaScript in a process known as *transpilation*.</span></span>
* <span data-ttu-id="ff8ac-106">破坏生成的 JavaScript 以降低文件大小，该过程称为“缩小”  。</span><span class="sxs-lookup"><span data-stu-id="ff8ac-106">Mangled the generated JavaScript to reduce file size in a process known as *minification*.</span></span>
* <span data-ttu-id="ff8ac-107">将已处理的 JavaScript、CSS 和 HTML 文件从 src 复制到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="ff8ac-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="ff8ac-108">将以下元素注入 wwwroot/index.html 文件  ：</span><span class="sxs-lookup"><span data-stu-id="ff8ac-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="ff8ac-109">一个引用 wwwroot/main.\<hash\>.css 文件的 `<link>` 标记。</span><span class="sxs-lookup"><span data-stu-id="ff8ac-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="ff8ac-110">此标记紧挨着 `</head>` 结束标记之前。</span><span class="sxs-lookup"><span data-stu-id="ff8ac-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="ff8ac-111">一个引用缩小后的 wwwroot/main.\<hash\>.js 文件的 `<script>` 标记。</span><span class="sxs-lookup"><span data-stu-id="ff8ac-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="ff8ac-112">此标记紧挨着 `</body>` 结束标记之前。</span><span class="sxs-lookup"><span data-stu-id="ff8ac-112">This tag is placed immediately before the closing `</body>` tag.</span></span>