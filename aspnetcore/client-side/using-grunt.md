---
title: 在 ASP.NET Core中使用 Grunt
author: rick-anderson
description: ''
ms.author: riande
ms.date: 10/14/2016
uid: client-side/using-grunt
ms.openlocfilehash: fc912974fb6ed3c65bb46a7d616d9e531587d946
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208874"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="43fd3-102">在 ASP.NET Core中使用 Grunt</span><span class="sxs-lookup"><span data-stu-id="43fd3-102">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="43fd3-103">通过[Noel 大米](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span><span class="sxs-lookup"><span data-stu-id="43fd3-103">By [Noel Rice](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span></span>

<span data-ttu-id="43fd3-104">Grunt 是 JavaScript 任务运行程序，可以自动脚本缩小，TypeScript 编译、 代码质量"lint"工具，CSS 预处理器和几乎任何需要采取措施来支持客户端开发的重复性的任务。</span><span class="sxs-lookup"><span data-stu-id="43fd3-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="43fd3-105">尽管 ASP.NET 项目模板使用 Gulp，默认情况下 Visual Studio 中，完全支持 grunt (请参阅[使用 Gulp](using-gulp.md))。</span><span class="sxs-lookup"><span data-stu-id="43fd3-105">Grunt is fully supported in Visual Studio, though the ASP.NET project templates use Gulp by default (see [Use Gulp](using-gulp.md)).</span></span>

<span data-ttu-id="43fd3-106">此示例使用一个空的 ASP.NET Core 项目作为起始点，以显示如何从零开始的客户端生成过程中自动运行。</span><span class="sxs-lookup"><span data-stu-id="43fd3-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="43fd3-107">已完成的示例可清除目标部署目录、 组合 JavaScript 文件、 检查代码质量，会将 JavaScript 文件内容和将部署到 web 应用程序的根目录。</span><span class="sxs-lookup"><span data-stu-id="43fd3-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="43fd3-108">我们将使用以下包：</span><span class="sxs-lookup"><span data-stu-id="43fd3-108">We will use the following packages:</span></span>

* <span data-ttu-id="43fd3-109">**grunt**:Grunt 任务运行程序包。</span><span class="sxs-lookup"><span data-stu-id="43fd3-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="43fd3-110">**grunt contrib 清理**:删除文件或目录的插件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="43fd3-111">**grunt contrib jshint**:查看 JavaScript 代码质量插件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="43fd3-112">**grunt contrib concat**:将文件合并为单个文件的插件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="43fd3-113">**grunt contrib 丑化**:缩减 JavaScript 以减小大小的插件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="43fd3-114">**grunt-contrib-watch**:监视文件活动插件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="43fd3-115">准备应用程序</span><span class="sxs-lookup"><span data-stu-id="43fd3-115">Preparing the application</span></span>

<span data-ttu-id="43fd3-116">若要开始，设置新的空 web 应用程序并添加 TypeScript 示例文件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="43fd3-117">TypeScript 文件自动编译到 JavaScript 中使用默认的 Visual Studio 设置，并且将是我们要处理使用 Grunt 的原始材料。</span><span class="sxs-lookup"><span data-stu-id="43fd3-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="43fd3-118">在 Visual Studio 中，创建一个新`ASP.NET Web Application`。</span><span class="sxs-lookup"><span data-stu-id="43fd3-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="43fd3-119">在**新建 ASP.NET 项目**对话框中，选择 ASP.NET Core**空**模板，然后单击确定按钮。</span><span class="sxs-lookup"><span data-stu-id="43fd3-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="43fd3-120">在解决方案资源管理器，查看项目结构。</span><span class="sxs-lookup"><span data-stu-id="43fd3-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="43fd3-121">`\src`文件夹包含空`wwwroot`和`Dependencies`节点。</span><span class="sxs-lookup"><span data-stu-id="43fd3-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![空的 web 解决方案](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="43fd3-123">添加名为的新文件夹`TypeScript`为项目目录。</span><span class="sxs-lookup"><span data-stu-id="43fd3-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="43fd3-124">之前添加的任何文件，请确保 Visual Studio 可以选择编译保存 ' 检查 TypeScript 文件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="43fd3-125">导航到**工具** > **选项** > **文本编辑器** > **Typescript**  > **项目**:</span><span class="sxs-lookup"><span data-stu-id="43fd3-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![设置自动编译 TypeScript 文件的选项](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="43fd3-127">右键单击`TypeScript`目录，然后选择**添加 > 新建项**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="43fd3-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="43fd3-128">选择**JavaScript 文件**项，然后将文件命名*Tastes.ts* (请注意\*.ts 扩展)。</span><span class="sxs-lookup"><span data-stu-id="43fd3-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="43fd3-129">下面的 TypeScript 代码的行复制到的文件 (保存时，一个新*Tastes.js*文件将显示与 JavaScript 源)。</span><span class="sxs-lookup"><span data-stu-id="43fd3-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="43fd3-130">添加到第二个文件**TypeScript**目录并将其命名`Food.ts`。</span><span class="sxs-lookup"><span data-stu-id="43fd3-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="43fd3-131">将以下代码复制到该文件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-131">Copy the code below into the file.</span></span>

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

## <a name="configuring-npm"></a><span data-ttu-id="43fd3-132">配置 NPM</span><span class="sxs-lookup"><span data-stu-id="43fd3-132">Configuring NPM</span></span>

<span data-ttu-id="43fd3-133">接下来，配置 NPM 以下载 grunt 和 grunt 任务。</span><span class="sxs-lookup"><span data-stu-id="43fd3-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="43fd3-134">在解决方案资源管理器，右键单击该项目并选择**添加 > 新建项**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="43fd3-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="43fd3-135">选择**NPM 配置文件**项，请保留默认名称*package.json*，然后单击**添加**按钮。</span><span class="sxs-lookup"><span data-stu-id="43fd3-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="43fd3-136">在中*package.json*文件内,`devDependencies`对象大括号中，输入"grunt"。</span><span class="sxs-lookup"><span data-stu-id="43fd3-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="43fd3-137">选择`grunt`在 Intellisense 中列出，然后按 Enter 键。</span><span class="sxs-lookup"><span data-stu-id="43fd3-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="43fd3-138">Visual Studio 将用引号括起来 grunt 包名称，并添加一个冒号。</span><span class="sxs-lookup"><span data-stu-id="43fd3-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="43fd3-139">从智能感知列表的顶部中的冒号右侧，选择包的最新稳定版本 (按`Ctrl-Space`如果不会显示 Intellisense)。</span><span class="sxs-lookup"><span data-stu-id="43fd3-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="43fd3-141">使用 NPM[语义化版本控制](http://semver.org/)来组织依赖项。</span><span class="sxs-lookup"><span data-stu-id="43fd3-141">NPM uses [semantic versioning](http://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="43fd3-142">语义版本控制，也称为 SemVer，标识包具有单独的编号方案\<主要 >。\<次要 >。\<修补程序 >。</span><span class="sxs-lookup"><span data-stu-id="43fd3-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="43fd3-143">Intellisense 显示仅几个常用的选项，从而简化了语义化版本控制。</span><span class="sxs-lookup"><span data-stu-id="43fd3-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="43fd3-144">智能感知列表 (在上面的示例 0.4.5) 中的顶级项被视为包的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="43fd3-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="43fd3-145">插入符号 (^) 符号匹配的最新的主版本和颚化符 （~） 与最新次要版本匹配。</span><span class="sxs-lookup"><span data-stu-id="43fd3-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="43fd3-146">请参阅[NPM semver 版本分析器引用](https://www.npmjs.com/package/semver)作为 SemVer 提供的完整表现力的指南。</span><span class="sxs-lookup"><span data-stu-id="43fd3-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="43fd3-147">添加多个依赖项加载 grunt 的 contrib-\*打包以*干净*， *jshint*， *concat*，*丑化*，以及*监视*如下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="43fd3-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="43fd3-148">版本不需要与示例匹配。</span><span class="sxs-lookup"><span data-stu-id="43fd3-148">The versions don't need to match the example.</span></span>

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

4. <span data-ttu-id="43fd3-149">保存*package.json*文件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-149">Save the *package.json* file.</span></span>

<span data-ttu-id="43fd3-150">将下载的每一 devDependencies 项包，以及每个包所需的任何文件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-150">The packages for each devDependencies item will download, along with any files that each package requires.</span></span> <span data-ttu-id="43fd3-151">您可以找到包文件中的`node_modules`目录，从而**显示所有文件**在解决方案资源管理器中的按钮。</span><span class="sxs-lookup"><span data-stu-id="43fd3-151">You can find the package files in the `node_modules` directory by enabling the **Show All Files** button in the Solution Explorer.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="43fd3-153">如果需要可以通过右键单击手动还原解决方案资源管理器中的依赖关系`Dependencies\NPM`，然后选择**还原包**菜单选项。</span><span class="sxs-lookup"><span data-stu-id="43fd3-153">If you need to, you can manually restore dependencies in Solution Explorer by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![还原包](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="43fd3-155">Grunt 配置</span><span class="sxs-lookup"><span data-stu-id="43fd3-155">Configuring Grunt</span></span>

<span data-ttu-id="43fd3-156">使用名为的清单配置 grunt *Gruntfile.js*的定义、 加载和注册任务，可以手动运行或配置为自动基于运行 Visual Studio 中的事件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="43fd3-157">右键单击项目并选择**添加 > 新建项**。</span><span class="sxs-lookup"><span data-stu-id="43fd3-157">Right-click the project and select **Add > New Item**.</span></span> <span data-ttu-id="43fd3-158">选择**Grunt 配置文件**选项，请保留默认名称*Gruntfile.js*，然后单击**添加**按钮。</span><span class="sxs-lookup"><span data-stu-id="43fd3-158">Select the **Grunt Configuration file** option, leave the default name, *Gruntfile.js*, and click the **Add** button.</span></span>

   <span data-ttu-id="43fd3-159">初始代码包含模块定义和`grunt.initConfig()`方法。</span><span class="sxs-lookup"><span data-stu-id="43fd3-159">The initial code includes a module definition and the `grunt.initConfig()` method.</span></span> <span data-ttu-id="43fd3-160">`initConfig()`用于为每个包，设置选项和模块的剩余部分将加载和注册任务。</span><span class="sxs-lookup"><span data-stu-id="43fd3-160">The `initConfig()` is used to set options for each package, and the remainder of the module will load and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

2. <span data-ttu-id="43fd3-161">内部`initConfig()`方法中，添加用于`clean`任务中的示例所示*Gruntfile.js*下面。</span><span class="sxs-lookup"><span data-stu-id="43fd3-161">Inside the `initConfig()` method, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="43fd3-162">清理任务接受目录字符串的数组。</span><span class="sxs-lookup"><span data-stu-id="43fd3-162">The clean task accepts an array of directory strings.</span></span> <span data-ttu-id="43fd3-163">此任务从 wwwroot/lib 删除文件，并删除整个/临时目录。</span><span class="sxs-lookup"><span data-stu-id="43fd3-163">This task removes files from wwwroot/lib and removes the entire /temp directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. <span data-ttu-id="43fd3-164">下面 initConfig() 方法中，添加对的调用`grunt.loadNpmTasks()`。</span><span class="sxs-lookup"><span data-stu-id="43fd3-164">Below the initConfig() method, add a call to `grunt.loadNpmTasks()`.</span></span> <span data-ttu-id="43fd3-165">这将从 Visual Studio 来使该任务可运行。</span><span class="sxs-lookup"><span data-stu-id="43fd3-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. <span data-ttu-id="43fd3-166">保存*Gruntfile.js*。</span><span class="sxs-lookup"><span data-stu-id="43fd3-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="43fd3-167">该文件应类似于下面的屏幕截图。</span><span class="sxs-lookup"><span data-stu-id="43fd3-167">The file should look something like the screenshot below.</span></span>

    ![初始 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

5. <span data-ttu-id="43fd3-169">右键单击*Gruntfile.js* ，然后选择**Task Runner Explorer**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="43fd3-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="43fd3-170">任务运行程序资源管理器窗口将打开。</span><span class="sxs-lookup"><span data-stu-id="43fd3-170">The Task Runner Explorer window will open.</span></span>

    ![任务运行程序资源管理器菜单](using-grunt/_static/task-runner-explorer-menu.png)

6. <span data-ttu-id="43fd3-172">确认`clean`显示在下面**任务**在任务运行程序资源管理器。</span><span class="sxs-lookup"><span data-stu-id="43fd3-172">Verify that `clean` shows under **Tasks** in the Task Runner Explorer.</span></span>

    ![任务运行程序资源管理器任务列表](using-grunt/_static/task-runner-explorer-tasks.png)

7. <span data-ttu-id="43fd3-174">右键单击 clean 任务，然后选择**运行**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="43fd3-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="43fd3-175">命令窗口中显示任务的进度。</span><span class="sxs-lookup"><span data-stu-id="43fd3-175">A command window displays progress of the task.</span></span>

    ![任务运行程序资源管理器中运行清理任务](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="43fd3-177">不有任何文件或目录尚未清理。</span><span class="sxs-lookup"><span data-stu-id="43fd3-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="43fd3-178">如果你愿意，您可以在解决方案资源管理器中手动创建，并为测试运行 clean 任务。</span><span class="sxs-lookup"><span data-stu-id="43fd3-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

8. <span data-ttu-id="43fd3-179">在 initConfig() 方法中，添加一个条目`concat`使用下面的代码。</span><span class="sxs-lookup"><span data-stu-id="43fd3-179">In the initConfig() method, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="43fd3-180">`src`属性数组列出要结合起来，应组合它们的顺序文件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="43fd3-181">`dest`属性分配到合并生成的文件的路径。</span><span class="sxs-lookup"><span data-stu-id="43fd3-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="43fd3-182">`all`上面的代码中的属性为目标的名称。</span><span class="sxs-lookup"><span data-stu-id="43fd3-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="43fd3-183">在某些 Grunt 任务中使用目标以允许多个生成环境。</span><span class="sxs-lookup"><span data-stu-id="43fd3-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="43fd3-184">可以查看使用 Intellisense 的内置目标或分配你自己。</span><span class="sxs-lookup"><span data-stu-id="43fd3-184">You can view the built-in targets using Intellisense or assign your own.</span></span>

9. <span data-ttu-id="43fd3-185">添加`jshint`任务使用下面的代码。</span><span class="sxs-lookup"><span data-stu-id="43fd3-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="43fd3-186">针对临时目录中找到的每个 JavaScript 文件运行 jshint 代码质量实用程序。</span><span class="sxs-lookup"><span data-stu-id="43fd3-186">The jshint code-quality utility is run against every JavaScript file found in the temp directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="43fd3-187">选项"-W069"错误 jshint 时生成的 JavaScript 使用方括号语法，即分配属性而不是点表示法`Tastes["Sweet"]`而不是`Tastes.Sweet`。</span><span class="sxs-lookup"><span data-stu-id="43fd3-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="43fd3-188">选项将关闭该警告，以允许进程继续的其余部分。</span><span class="sxs-lookup"><span data-stu-id="43fd3-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

10. <span data-ttu-id="43fd3-189">添加`uglify`任务使用下面的代码。</span><span class="sxs-lookup"><span data-stu-id="43fd3-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="43fd3-190">任务缩减*combined.js*文件的临时目录中找到并创建 wwwroot/lib 以下标准命名约定中的结果文件*\<文件名\>。 min.js*.</span><span class="sxs-lookup"><span data-stu-id="43fd3-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

11. <span data-ttu-id="43fd3-191">在加载 grunt contrib 清理调用 grunt.loadNpmTasks()，包括相同的调用，用于 jshint，concat 和丑化使用下面的代码。</span><span class="sxs-lookup"><span data-stu-id="43fd3-191">Under the call grunt.loadNpmTasks() that loads grunt-contrib-clean, include the same call for jshint, concat and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. <span data-ttu-id="43fd3-192">保存*Gruntfile.js*。</span><span class="sxs-lookup"><span data-stu-id="43fd3-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="43fd3-193">该文件应类似于下面的示例。</span><span class="sxs-lookup"><span data-stu-id="43fd3-193">The file should look something like the example below.</span></span>

    ![完整 grunt 文件示例](using-grunt/_static/gruntfile-js-complete.png)

13. <span data-ttu-id="43fd3-195">请注意，任务运行程序资源管理器任务列表包含`clean`， `concat`，`jshint`和`uglify`任务。</span><span class="sxs-lookup"><span data-stu-id="43fd3-195">Notice that the Task Runner Explorer Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="43fd3-196">按顺序运行每个任务并观察在解决方案资源管理器中的结果。</span><span class="sxs-lookup"><span data-stu-id="43fd3-196">Run each task in order and observe the results in Solution Explorer.</span></span> <span data-ttu-id="43fd3-197">每个任务应该正常运行。</span><span class="sxs-lookup"><span data-stu-id="43fd3-197">Each task should run without errors.</span></span>

    ![任务运行程序资源管理器运行每个任务](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="43fd3-199">Concat 任务创建一个新*combined.js*文件并将其放置到临时目录。</span><span class="sxs-lookup"><span data-stu-id="43fd3-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="43fd3-200">只需运行 jshint 任务并不会生成输出。</span><span class="sxs-lookup"><span data-stu-id="43fd3-200">The jshint task simply runs and doesn't produce output.</span></span> <span data-ttu-id="43fd3-201">Uglify 任务创建一个新*combined.min.js*文件，并将其放到 wwwroot/lib。</span><span class="sxs-lookup"><span data-stu-id="43fd3-201">The uglify task creates a new *combined.min.js* file and places it into wwwroot/lib.</span></span> <span data-ttu-id="43fd3-202">完成后，解决方案应类似于下面的屏幕截图：</span><span class="sxs-lookup"><span data-stu-id="43fd3-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![毕竟，解决方案资源管理器任务](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="43fd3-204">对于每个包的选项的详细信息，请访问[ https://www.npmjs.com/ ](https://www.npmjs.com/)和查找在主页上的搜索框中的包名称。</span><span class="sxs-lookup"><span data-stu-id="43fd3-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="43fd3-205">例如，可以查找 grunt contrib 清理包以获取说明的所有参数的文档链接。</span><span class="sxs-lookup"><span data-stu-id="43fd3-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="43fd3-206">即时汇总</span><span class="sxs-lookup"><span data-stu-id="43fd3-206">All together now</span></span>

<span data-ttu-id="43fd3-207">使用 Grunt`registerTask()`方法以按特定顺序运行的一系列任务。</span><span class="sxs-lookup"><span data-stu-id="43fd3-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="43fd3-208">例如，若要运行示例上述步骤中清理的顺序-> concat-> jshint-> 丑化，将以下代码添加到该模块。</span><span class="sxs-lookup"><span data-stu-id="43fd3-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="43fd3-209">应将代码添加到作为外部 initConfig loadNpmTasks() 调用相同的级别。</span><span class="sxs-lookup"><span data-stu-id="43fd3-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="43fd3-210">新任务将显示在任务运行程序资源管理器的别名的任务。</span><span class="sxs-lookup"><span data-stu-id="43fd3-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="43fd3-211">您可以右键单击并像其他任务一样运行它。</span><span class="sxs-lookup"><span data-stu-id="43fd3-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="43fd3-212">`all`任务将运行`clean`， `concat`，`jshint`和`uglify`，按顺序。</span><span class="sxs-lookup"><span data-stu-id="43fd3-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![别名 grunt 任务](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="43fd3-214">监视更改</span><span class="sxs-lookup"><span data-stu-id="43fd3-214">Watching for changes</span></span>

<span data-ttu-id="43fd3-215">一个`watch`任务会监控文件和目录。</span><span class="sxs-lookup"><span data-stu-id="43fd3-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="43fd3-216">监视检测到更改时自动触发任务。</span><span class="sxs-lookup"><span data-stu-id="43fd3-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="43fd3-217">将以下代码添加到 initConfig 若要监视更改到\*TypeScript 目录中的.js 文件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="43fd3-218">如果更改 JavaScript 文件，请`watch`将运行`all`任务。</span><span class="sxs-lookup"><span data-stu-id="43fd3-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="43fd3-219">添加对的调用`loadNpmTasks()`以显示`watch`任务运行程序资源管理器中的任务。</span><span class="sxs-lookup"><span data-stu-id="43fd3-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="43fd3-220">右键单击任务运行程序资源管理器中的监视任务，并从上下文菜单中选择运行。</span><span class="sxs-lookup"><span data-stu-id="43fd3-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="43fd3-221">显示运行监视任务的命令窗口将显示"正在等待..."消息。</span><span class="sxs-lookup"><span data-stu-id="43fd3-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="43fd3-222">打开 TypeScript 文件之一，添加一个空格，然后保存该文件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="43fd3-223">这将触发监视任务并触发要按顺序运行的其他任务。</span><span class="sxs-lookup"><span data-stu-id="43fd3-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="43fd3-224">下面的屏幕截图显示了示例运行。</span><span class="sxs-lookup"><span data-stu-id="43fd3-224">The screenshot below shows a sample run.</span></span>

![运行任务输出](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="43fd3-226">绑定到 Visual Studio 事件</span><span class="sxs-lookup"><span data-stu-id="43fd3-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="43fd3-227">除非你想要手动启动你的任务，每次在 Visual Studio 中工作时，可以将绑定到的任务**生成前**，**后生成**，**清理**，和**项目打开**事件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-227">Unless you want to manually start your tasks every time you work in Visual Studio, you can bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="43fd3-228">接下来将绑定`watch`以便它在每次 Visual Studio 打开时运行。</span><span class="sxs-lookup"><span data-stu-id="43fd3-228">Let’s bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="43fd3-229">在任务运行程序资源管理器中，右键单击监视任务并选择**绑定 > 项目 Open**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="43fd3-229">In Task Runner Explorer, right-click the watch task and select **Bindings > Project Open** from the context menu.</span></span>

![将任务绑定到项目开始](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="43fd3-231">卸载并重新加载项目。</span><span class="sxs-lookup"><span data-stu-id="43fd3-231">Unload and reload the project.</span></span> <span data-ttu-id="43fd3-232">当再次加载该项目时，监视任务将启动自动运行。</span><span class="sxs-lookup"><span data-stu-id="43fd3-232">When the project loads again, the watch task will start running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="43fd3-233">总结</span><span class="sxs-lookup"><span data-stu-id="43fd3-233">Summary</span></span>

<span data-ttu-id="43fd3-234">Grunt 是可用于自动执行大多数客户端生成任务的功能强大的任务运行程序。</span><span class="sxs-lookup"><span data-stu-id="43fd3-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="43fd3-235">Grunt 利用 NPM 来提供其包和工具与 Visual Studio 集成的功能。</span><span class="sxs-lookup"><span data-stu-id="43fd3-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="43fd3-236">Visual Studio 的任务运行程序资源管理器检测到配置文件的更改，并提供了一个方便的界面，用于运行任务，查看正在运行的任务，并将任务绑定到 Visual Studio 事件。</span><span class="sxs-lookup"><span data-stu-id="43fd3-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43fd3-237">其他资源</span><span class="sxs-lookup"><span data-stu-id="43fd3-237">Additional resources</span></span>

* [<span data-ttu-id="43fd3-238">使用 Gulp</span><span class="sxs-lookup"><span data-stu-id="43fd3-238">Use Gulp</span></span>](using-gulp.md)
