---
title: 在 ASP.NET Core 中使用 Grunt
author: rick-anderson
description: 在 ASP.NET Core 中使用 Grunt
ms.author: riande
ms.date: 12/05/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-grunt
ms.openlocfilehash: e8e4459f7fe496135d6cfd7f4ff52511a5e1c064
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628022"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="9ce39-103">在 ASP.NET Core 中使用 Grunt</span><span class="sxs-lookup"><span data-stu-id="9ce39-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="9ce39-104">Grunt 是一种 JavaScript 任务运行程序，可自动执行脚本缩减、TypeScript 编译、代码质量“lint”工具、CSS 预处理器以及支持客户端开发所需的所有重复性工作。</span><span class="sxs-lookup"><span data-stu-id="9ce39-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="9ce39-105">Visual Studio 完全支持 Grunt。</span><span class="sxs-lookup"><span data-stu-id="9ce39-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="9ce39-106">此示例使用空的 ASP.NET Core 项目作为其起始点，演示如何从头开始自动执行客户端生成过程。</span><span class="sxs-lookup"><span data-stu-id="9ce39-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="9ce39-107">完成的示例可清理目标部署目录、合并 JavaScript 文件和检查代码质量，将浓缩后的 JavaScript 文件内容部署至 Web 应用程序的根目录。</span><span class="sxs-lookup"><span data-stu-id="9ce39-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="9ce39-108">我们将使用以下包：</span><span class="sxs-lookup"><span data-stu-id="9ce39-108">We will use the following packages:</span></span>

* <span data-ttu-id="9ce39-109">**grunt**：Grunt 任务运行程序包。</span><span class="sxs-lookup"><span data-stu-id="9ce39-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="9ce39-110">**grunt-contrib-clean**：用于删除文件或目录的插件。</span><span class="sxs-lookup"><span data-stu-id="9ce39-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="9ce39-111">**grunt-contrib-jshint**：用于检查 JavaScript 代码质量的插件。</span><span class="sxs-lookup"><span data-stu-id="9ce39-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="9ce39-112">**grunt-contrib-concat**：用于将多个文件联接为单个文件的插件。</span><span class="sxs-lookup"><span data-stu-id="9ce39-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="9ce39-113">**grunt-contrib-uglify**：缩减 JavaScript 以减小尺寸的插件。</span><span class="sxs-lookup"><span data-stu-id="9ce39-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="9ce39-114">**grunt-contrib-watch**：用于监视文件活动的插件。</span><span class="sxs-lookup"><span data-stu-id="9ce39-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="9ce39-115">准备应用程序</span><span class="sxs-lookup"><span data-stu-id="9ce39-115">Preparing the application</span></span>

<span data-ttu-id="9ce39-116">若要开始，请设置一个新的空 Web 应用程序并添加 TypeScript 示例文件。</span><span class="sxs-lookup"><span data-stu-id="9ce39-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="9ce39-117">TypeScript 文件采用默认的 Visual Studio 设置自动编译为 JavaScript，并且将是 Grunt 处理的原始材料。</span><span class="sxs-lookup"><span data-stu-id="9ce39-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="9ce39-118">在 Visual Studio 中，新建一个 `ASP.NET Web Application`。</span><span class="sxs-lookup"><span data-stu-id="9ce39-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="9ce39-119">在“新建 ASP.NET 项目”对话框中，选择 ASP.NET Core“空”模板，然后单击“确定”按钮 。</span><span class="sxs-lookup"><span data-stu-id="9ce39-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="9ce39-120">在“解决方案资源管理器”中，查看项目结构。</span><span class="sxs-lookup"><span data-stu-id="9ce39-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="9ce39-121">`\src` 文件夹包含空的 `wwwroot` 和 `Dependencies` 节点。</span><span class="sxs-lookup"><span data-stu-id="9ce39-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![空 Web 解决方案](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="9ce39-123">将名为 `TypeScript` 的新文件夹添加到项目目录中。</span><span class="sxs-lookup"><span data-stu-id="9ce39-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="9ce39-124">在添加任何文件之前，请确保 Visual Studio 已为 TypeScript 文件勾选“在保存时编译”选项。</span><span class="sxs-lookup"><span data-stu-id="9ce39-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="9ce39-125">导航至“工具” > “选项” > “文本编辑器” > “Typescript” > “项目”    ：</span><span class="sxs-lookup"><span data-stu-id="9ce39-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![设置 TypeScript 文件自动编译的选项](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="9ce39-127">右键单击 `TypeScript` 目录，然后从上下文菜单中选择“添加”>“新建项”。</span><span class="sxs-lookup"><span data-stu-id="9ce39-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="9ce39-128">选择“JavaScript 文件”项，并将文件命名为“Tastes.ts”（注意 \*.ts 扩展名）。</span><span class="sxs-lookup"><span data-stu-id="9ce39-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="9ce39-129">将下面这行 TypeScript 代码复制到文件中，保存后，将显示一个新的 Tastes.js 文件，其中包含 JavaScript 源。</span><span class="sxs-lookup"><span data-stu-id="9ce39-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="9ce39-130">将第二个文件添加到 TypeScript 目录，并将其命名为 `Food.ts`。</span><span class="sxs-lookup"><span data-stu-id="9ce39-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="9ce39-131">将下面的代码复制到该文件中。</span><span class="sxs-lookup"><span data-stu-id="9ce39-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="9ce39-132">配置 NPM</span><span class="sxs-lookup"><span data-stu-id="9ce39-132">Configuring NPM</span></span>

<span data-ttu-id="9ce39-133">接下来，配置 NPM 以下载 grunt 和 grunt 任务。</span><span class="sxs-lookup"><span data-stu-id="9ce39-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="9ce39-134">在“解决方案资源管理器”中右键单击项目，并从上下文菜单中选择“添加”>“新建项”。</span><span class="sxs-lookup"><span data-stu-id="9ce39-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="9ce39-135">选择“NPM 配置文件”项，保留默认名称 (package.json) 并单击“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="9ce39-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="9ce39-136">在 package.json 文件中的 `devDependencies` 对象大括号内输入“grunt”。</span><span class="sxs-lookup"><span data-stu-id="9ce39-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="9ce39-137">从 Intellisense 列表中选择 `grunt` 并按下 Enter 键。</span><span class="sxs-lookup"><span data-stu-id="9ce39-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="9ce39-138">Visual Studio 将引用 grunt 包名称，并添加一个冒号。</span><span class="sxs-lookup"><span data-stu-id="9ce39-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="9ce39-139">在冒号右侧，从 Intellisense 列表顶部选择包的最新稳定版本（如果 Intellisense 列表未显示，请按 `Ctrl-Space`）。</span><span class="sxs-lookup"><span data-stu-id="9ce39-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![Grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="9ce39-141">NPM 使用[语义化版本控制](https://semver.org/)来组织依赖项。</span><span class="sxs-lookup"><span data-stu-id="9ce39-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="9ce39-142">语义化版本控制（也称为 SemVer）使用编号方案 \<major>.\<minor>.\<patch> 识别包。Intellisense 只显示几个常见选项，从而简化语义化版本控制。</span><span class="sxs-lookup"><span data-stu-id="9ce39-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="9ce39-143">Intellisense 列表的第一项（在上面的示例中为 0.4.5）被视为程序包的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="9ce39-143">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="9ce39-144">脱字号 (^) 匹配最新的主版本，波形符 (~) 匹配最新的次版本。</span><span class="sxs-lookup"><span data-stu-id="9ce39-144">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="9ce39-145">有关 SemVer 提供的完整表达能力，请参阅 [NPM semver 版本分析程序参考](https://www.npmjs.com/package/semver)。</span><span class="sxs-lookup"><span data-stu-id="9ce39-145">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="9ce39-146">添加更多依赖项以加载 clean、jshint、concat、uglify 和 watch 的 grunt-contrib-\* 包，如以下示例中所示    。</span><span class="sxs-lookup"><span data-stu-id="9ce39-146">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="9ce39-147">版本不需要与示例匹配。</span><span class="sxs-lookup"><span data-stu-id="9ce39-147">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="9ce39-148">保存 package.json 文件。</span><span class="sxs-lookup"><span data-stu-id="9ce39-148">Save the *package.json* file.</span></span>

<span data-ttu-id="9ce39-149">每个 `devDependencies` 项的包将随每个包需要的所有文件一起下载。</span><span class="sxs-lookup"><span data-stu-id="9ce39-149">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="9ce39-150">在解决方案资源管理器中启用“显示所有文件”按钮，可以找到 node_modules 目录中的包文件 。</span><span class="sxs-lookup"><span data-stu-id="9ce39-150">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="9ce39-152">若有需要，可以在解决方案资源管理器中手动还原依赖项，方法是右键单击 `Dependencies\NPM` 并选择“还原包”菜单选项 。</span><span class="sxs-lookup"><span data-stu-id="9ce39-152">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![还原包](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="9ce39-154">配置 Grunt</span><span class="sxs-lookup"><span data-stu-id="9ce39-154">Configuring Grunt</span></span>

<span data-ttu-id="9ce39-155">使用名为 Gruntfile.js 的清单配置 Grunt，该清单定义、加载和注册了多个任务，这些任务可手动运行或配置为基于 Visual Studio 中的事件自动运行。</span><span class="sxs-lookup"><span data-stu-id="9ce39-155">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="9ce39-156">右键单击该项目，选择“添加” > “新建项” 。</span><span class="sxs-lookup"><span data-stu-id="9ce39-156">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="9ce39-157">选择“JavaScript 文件”项模板，将名称更改为 Gruntfile.js，然后单击“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="9ce39-157">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="9ce39-158">将以下代码添加到 Gruntfile.js。</span><span class="sxs-lookup"><span data-stu-id="9ce39-158">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="9ce39-159">`initConfig` 函数设置每个包的选项，模块的其余部分加载并注册任务。</span><span class="sxs-lookup"><span data-stu-id="9ce39-159">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="9ce39-160">在 `initConfig` 函数中，添加 `clean` 任务的选项，如下面的示例 Gruntfile.js 中所示。</span><span class="sxs-lookup"><span data-stu-id="9ce39-160">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="9ce39-161">`clean` 任务接受目录字符串的数组。</span><span class="sxs-lookup"><span data-stu-id="9ce39-161">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="9ce39-162">此任务从 wwwroot/lib 删除文件，并删除整个 /temp 目录 。</span><span class="sxs-lookup"><span data-stu-id="9ce39-162">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="9ce39-163">在 `initConfig` 函数下，添加对 `grunt.loadNpmTasks` 的调用。</span><span class="sxs-lookup"><span data-stu-id="9ce39-163">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="9ce39-164">这使得任务可从 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="9ce39-164">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="9ce39-165">保存 Gruntfile.js。</span><span class="sxs-lookup"><span data-stu-id="9ce39-165">Save *Gruntfile.js*.</span></span> <span data-ttu-id="9ce39-166">该文件应类似于下面的屏幕截图所示。</span><span class="sxs-lookup"><span data-stu-id="9ce39-166">The file should look something like the screenshot below.</span></span>

    ![初始 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="9ce39-168">右键单击 Gruntfile.js 并从上下文菜单中选择“任务运行程序资源管理器”。</span><span class="sxs-lookup"><span data-stu-id="9ce39-168">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="9ce39-169">随即打开任务运行程序浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="9ce39-169">The **Task Runner Explorer** window will open.</span></span>

    ![任务运行程序资源管理器菜单](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="9ce39-171">验证任务运行程序资源管理器中的“任务”下是否显示 `clean` 。</span><span class="sxs-lookup"><span data-stu-id="9ce39-171">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![任务运行程序资源管理器任务列表](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="9ce39-173">右键单击清理任务并在上下文菜单中选择“运行”。</span><span class="sxs-lookup"><span data-stu-id="9ce39-173">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="9ce39-174">显示任务进度的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="9ce39-174">A command window displays progress of the task.</span></span>

    ![任务运行程序资源管理器运行清理任务](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="9ce39-176">尚无需要清理的文件或目录。</span><span class="sxs-lookup"><span data-stu-id="9ce39-176">There are no files or directories to clean yet.</span></span> <span data-ttu-id="9ce39-177">如果需要，可以在解决方案资源管理器中手动创建文件或目录，然后运行清理任务来进行测试。</span><span class="sxs-lookup"><span data-stu-id="9ce39-177">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="9ce39-178">在 `initConfig` 函数中，使用下面的代码添加 `concat` 的条目。</span><span class="sxs-lookup"><span data-stu-id="9ce39-178">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="9ce39-179">`src` 属性数组按合并顺序列出要合并的文件。</span><span class="sxs-lookup"><span data-stu-id="9ce39-179">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="9ce39-180">`dest` 属性指定生成的合并文件的路径。</span><span class="sxs-lookup"><span data-stu-id="9ce39-180">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="9ce39-181">以上代码中的 `all` 属性是目标的名称。</span><span class="sxs-lookup"><span data-stu-id="9ce39-181">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="9ce39-182">某些 Grunt 任务会使用目标以允许多个生成环境。</span><span class="sxs-lookup"><span data-stu-id="9ce39-182">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="9ce39-183">可以使用 IntelliSense 查看内置目标，或自行分配。</span><span class="sxs-lookup"><span data-stu-id="9ce39-183">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="9ce39-184">使用以下代码添加 `jshint` 任务。</span><span class="sxs-lookup"><span data-stu-id="9ce39-184">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="9ce39-185">系统会对在 temp 目录中找到的每个 JavaScript 文件运行 jshint `code-quality` 实用工具。</span><span class="sxs-lookup"><span data-stu-id="9ce39-185">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="9ce39-186">当 JavaScript 使用括号语法分配属性而不是点标识符，也就是采用 `Tastes["Sweet"]` 而不是 `Tastes.Sweet`，jshint 会生成一个错误选项“-W069”。</span><span class="sxs-lookup"><span data-stu-id="9ce39-186">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="9ce39-187">该选项关闭警告，以允许其余过程继续进行。</span><span class="sxs-lookup"><span data-stu-id="9ce39-187">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="9ce39-188">使用以下代码添加 `uglify` 任务。</span><span class="sxs-lookup"><span data-stu-id="9ce39-188">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="9ce39-189">该任务缩减在 temp 目录中找到 combined.js 文件，并按照标准命名约定 \<file name\>.min.js 在 wwwroot/lib 中创建结果文件 。</span><span class="sxs-lookup"><span data-stu-id="9ce39-189">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="9ce39-190">在对 `grunt.loadNpmTasks` 发起的加载 `grunt-contrib-clean` 的调用下，使用以下代码包括 jshint、concat 和 uglify 的相同调用。</span><span class="sxs-lookup"><span data-stu-id="9ce39-190">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="9ce39-191">保存 Gruntfile.js。</span><span class="sxs-lookup"><span data-stu-id="9ce39-191">Save *Gruntfile.js*.</span></span> <span data-ttu-id="9ce39-192">该文件应类似于以下示例所示。</span><span class="sxs-lookup"><span data-stu-id="9ce39-192">The file should look something like the example below.</span></span>

    ![完成 grunt 文件示例](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="9ce39-194">请注意，任务运行程序资源管理器的“任务”列表包括 `clean`、`concat`、`jshint` 和 `uglify` 任务。</span><span class="sxs-lookup"><span data-stu-id="9ce39-194">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="9ce39-195">按顺序运行每个任务，并在解决方案资源管理器中观察结果。</span><span class="sxs-lookup"><span data-stu-id="9ce39-195">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="9ce39-196">每个任务都应正常运行，不会出错。</span><span class="sxs-lookup"><span data-stu-id="9ce39-196">Each task should run without errors.</span></span>

    ![任务运行程序资源管理器运行每个任务](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="9ce39-198">concat 任务会创建一个新的 combined.js 文件，并将其放入 temp 目录。</span><span class="sxs-lookup"><span data-stu-id="9ce39-198">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="9ce39-199">`jshint` 任务仅运行，并不生成输出。</span><span class="sxs-lookup"><span data-stu-id="9ce39-199">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="9ce39-200">`uglify` 任务创建新的 combined.min.js 文件，并将其放入 wwwroot/lib 中 。</span><span class="sxs-lookup"><span data-stu-id="9ce39-200">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="9ce39-201">完成后，解决方案应类似于下面的屏幕截图所示：</span><span class="sxs-lookup"><span data-stu-id="9ce39-201">On completion, the solution should look something like the screenshot below:</span></span>

    ![完成所有任务之后的解决方案资源管理器](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="9ce39-203">若要详细了解每个包的选项，请访问 [https://www.npmjs.com/](https://www.npmjs.com/) 并在主页上的搜索框中搜索包名称。</span><span class="sxs-lookup"><span data-stu-id="9ce39-203">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="9ce39-204">例如，可以查找 grunt-contrib-clean 包，以获取介绍其所有参数的文档链接。</span><span class="sxs-lookup"><span data-stu-id="9ce39-204">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="9ce39-205">现在将所有任务一起运行</span><span class="sxs-lookup"><span data-stu-id="9ce39-205">All together now</span></span>

<span data-ttu-id="9ce39-206">使用 Grunt `registerTask()` 方法，按特定顺序运行一系列任务。</span><span class="sxs-lookup"><span data-stu-id="9ce39-206">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="9ce39-207">例如，若要按 clean -> concat -> jshint -> uglify 的顺序中运行上述示例步骤，请将以下代码添加到模块。</span><span class="sxs-lookup"><span data-stu-id="9ce39-207">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="9ce39-208">请将代码添加到与 loadNpmTasks() 调用相同的级别，在 initConfig 之外。</span><span class="sxs-lookup"><span data-stu-id="9ce39-208">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="9ce39-209">在任务运行程序资源管理器中，新任务显示在“别名任务”下。</span><span class="sxs-lookup"><span data-stu-id="9ce39-209">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="9ce39-210">可以右键单击并运行它，就像执行其他任务一样。</span><span class="sxs-lookup"><span data-stu-id="9ce39-210">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="9ce39-211">`all` 任务将按顺序运行 `clean`、`concat`、`jshint` 和 `uglify`。</span><span class="sxs-lookup"><span data-stu-id="9ce39-211">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![别名 grunt 任务](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="9ce39-213">监视更改</span><span class="sxs-lookup"><span data-stu-id="9ce39-213">Watching for changes</span></span>

<span data-ttu-id="9ce39-214">`watch` 任务用于监视文件和目录。</span><span class="sxs-lookup"><span data-stu-id="9ce39-214">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="9ce39-215">如果检测到更改，监视会自动触发任务。</span><span class="sxs-lookup"><span data-stu-id="9ce39-215">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="9ce39-216">将以下代码添加到 initConfig，以监视对 TypeScript 目录中 \*.js 文件的更改。</span><span class="sxs-lookup"><span data-stu-id="9ce39-216">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="9ce39-217">如果 JavaScript 文件发生了更改，`watch` 将运行 `all` 任务。</span><span class="sxs-lookup"><span data-stu-id="9ce39-217">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="9ce39-218">添加对 `loadNpmTasks()` 的调用，以在任务运行程序资源管理器中显示 `watch` 任务。</span><span class="sxs-lookup"><span data-stu-id="9ce39-218">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="9ce39-219">右键单击任务运行程序资源管理器中的监视任务，然后从上下文菜单中选择“运行”。</span><span class="sxs-lookup"><span data-stu-id="9ce39-219">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="9ce39-220">显示正在运行的监视任务的命令窗口将显示一条“正在等待…”消息。</span><span class="sxs-lookup"><span data-stu-id="9ce39-220">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="9ce39-221">打开其中一个 TypeScript 文件，添加一个空格，然后保存该文件。</span><span class="sxs-lookup"><span data-stu-id="9ce39-221">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="9ce39-222">这会触发监视任务，并触发要按顺序运行的其他任务。</span><span class="sxs-lookup"><span data-stu-id="9ce39-222">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="9ce39-223">下面的屏幕截图展示的是一个示例运行。</span><span class="sxs-lookup"><span data-stu-id="9ce39-223">The screenshot below shows a sample run.</span></span>

![正在运行的任务输出](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="9ce39-225">绑定到 Visual Studio 事件</span><span class="sxs-lookup"><span data-stu-id="9ce39-225">Binding to Visual Studio events</span></span>

<span data-ttu-id="9ce39-226">请将任务绑定到“生成前”、“生成后”、“清理”和“项目打开”事件，除非想在每次在 Visual Studio 中工作时都手动启动任务   。</span><span class="sxs-lookup"><span data-stu-id="9ce39-226">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="9ce39-227">绑定 `watch`，使其在每次打开 Visual Studio 时运行。</span><span class="sxs-lookup"><span data-stu-id="9ce39-227">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="9ce39-228">右键单击任务运行程序资源管理器中的监视任务，然后在上下文菜单中选择“绑定” > “项目打开” 。</span><span class="sxs-lookup"><span data-stu-id="9ce39-228">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![将任务绑定到项目开头](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="9ce39-230">卸载并重载项目。</span><span class="sxs-lookup"><span data-stu-id="9ce39-230">Unload and reload the project.</span></span> <span data-ttu-id="9ce39-231">再次加载项目时，监视任务会自动开始运行。</span><span class="sxs-lookup"><span data-stu-id="9ce39-231">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="9ce39-232">总结</span><span class="sxs-lookup"><span data-stu-id="9ce39-232">Summary</span></span>

<span data-ttu-id="9ce39-233">Grunt 是一个功能强大的任务运行程序，可用于自动执行大多数客户端生成任务。</span><span class="sxs-lookup"><span data-stu-id="9ce39-233">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="9ce39-234">Grunt 利用 NPM 提供它的包，特点是能与 Visual Studio 进行工具集成。</span><span class="sxs-lookup"><span data-stu-id="9ce39-234">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="9ce39-235">Visual Studio 的任务运行程序资源管理器检测对配置文件所做的更改，并提供便捷的界面，用于运行任务、查看运行中的任务以及将任务绑定到 Visual Studio 事件。</span><span class="sxs-lookup"><span data-stu-id="9ce39-235">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
