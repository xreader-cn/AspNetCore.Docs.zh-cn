---
title: 在 ASP.NET Core 中使用 Gulp
author: rick-anderson
description: 了解如何在 ASP.NET Core 中使用 Gulp。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 10/04/2018
uid: client-side/using-gulp
ms.openlocfilehash: 9f6d03a1e8a81bceca15cb1e1aa664c22c31e1d3
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209867"
---
# <a name="use-gulp-in-aspnet-core"></a><span data-ttu-id="428fd-103">在 ASP.NET Core 中使用 Gulp</span><span class="sxs-lookup"><span data-stu-id="428fd-103">Use Gulp in ASP.NET Core</span></span>

<span data-ttu-id="428fd-104">通过[Scott Addie](https://scottaddie.com)， [Shayne Boyer](https://twitter.com/spboyer)，和[David 松树](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="428fd-104">By [Scott Addie](https://scottaddie.com), [Shayne Boyer](https://twitter.com/spboyer), and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="428fd-105">在典型的现代 Web 应用中，生成过程可能包括以下操作：</span><span class="sxs-lookup"><span data-stu-id="428fd-105">In a typical modern web app, the build process might:</span></span>

* <span data-ttu-id="428fd-106">捆绑和缩小 JavaScript 和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="428fd-106">Bundle and minify JavaScript and CSS files.</span></span>
* <span data-ttu-id="428fd-107">运行工具以调用之前每个生成的绑定和缩减任务。</span><span class="sxs-lookup"><span data-stu-id="428fd-107">Run tools to call the bundling and minification tasks before each build.</span></span>
* <span data-ttu-id="428fd-108">将 LESS 或 SASS 文件编译成 CSS。</span><span class="sxs-lookup"><span data-stu-id="428fd-108">Compile LESS or SASS files to CSS.</span></span>
* <span data-ttu-id="428fd-109">将 CoffeeScript 或 TypeScript 文件编译成 JavaScript。
</span><span class="sxs-lookup"><span data-stu-id="428fd-109">Compile CoffeeScript or TypeScript files to JavaScript.</span></span>

<span data-ttu-id="428fd-110">任务运行程序是一种自动执行这些常规开发任务和其他任务的工具。</span><span class="sxs-lookup"><span data-stu-id="428fd-110">A *task runner* is a tool which automates these routine development tasks and more.</span></span> <span data-ttu-id="428fd-111">Visual Studio 为两个常用的基于 JavaScript 的任务运行程序提供的内置支持：[Gulp](https://gulpjs.com/)并[Grunt](using-grunt.md)。</span><span class="sxs-lookup"><span data-stu-id="428fd-111">Visual Studio provides built-in support for two popular JavaScript-based task runners: [Gulp](https://gulpjs.com/) and [Grunt](using-grunt.md).</span></span>

## <a name="gulp"></a><span data-ttu-id="428fd-112">Gulp</span><span class="sxs-lookup"><span data-stu-id="428fd-112">Gulp</span></span>

<span data-ttu-id="428fd-113">Gulp 是一个基于 JavaScript 的流式处理生成工具包，用于客户端代码。</span><span class="sxs-lookup"><span data-stu-id="428fd-113">Gulp is a JavaScript-based streaming build toolkit for client-side code.</span></span> <span data-ttu-id="428fd-114">它通常用于通过一系列的进程的客户端文件流式传输特定的事件触发的生成环境中时。</span><span class="sxs-lookup"><span data-stu-id="428fd-114">It's commonly used to stream client-side files through a series of processes when a specific event is triggered in a build environment.</span></span> <span data-ttu-id="428fd-115">例如，使用 Gulp 来自动执行[捆绑和缩小](bundling-and-minification.md)或清理之前新生成的开发环境。</span><span class="sxs-lookup"><span data-stu-id="428fd-115">For instance, Gulp can be used to automate [bundling and minification](bundling-and-minification.md) or the cleaning of a development environment before a new build.</span></span>

<span data-ttu-id="428fd-116">中定义一组的 Gulp 任务*gulpfile.js*。</span><span class="sxs-lookup"><span data-stu-id="428fd-116">A set of Gulp tasks is defined in *gulpfile.js*.</span></span> <span data-ttu-id="428fd-117">以下 JavaScript 包括 Gulp 模块，并指定文件路径，以引用中即将推出的任务：</span><span class="sxs-lookup"><span data-stu-id="428fd-117">The following JavaScript includes Gulp modules and specifies file paths to be referenced within the forthcoming tasks:</span></span>

```javascript
/// <binding Clean='clean' />
"use strict";

var gulp = require("gulp"),
    rimraf = require("rimraf"),
    concat = require("gulp-concat"),
    cssmin = require("gulp-cssmin"),
    uglify = require("gulp-uglify");

var paths = {
  webroot: "./wwwroot/"
};

paths.js = paths.webroot + "js/**/*.js";
paths.minJs = paths.webroot + "js/**/*.min.js";
paths.css = paths.webroot + "css/**/*.css";
paths.minCss = paths.webroot + "css/**/*.min.css";
paths.concatJsDest = paths.webroot + "js/site.min.js";
paths.concatCssDest = paths.webroot + "css/site.min.css";
```

<span data-ttu-id="428fd-118">上面的代码中指定需要哪些节点模块。</span><span class="sxs-lookup"><span data-stu-id="428fd-118">The above code specifies which Node modules are required.</span></span> <span data-ttu-id="428fd-119">`require`函数导入的每个模块，以便依赖任务能够利用其功能。</span><span class="sxs-lookup"><span data-stu-id="428fd-119">The `require` function imports each module so that the dependent tasks can utilize their features.</span></span> <span data-ttu-id="428fd-120">每个导入的模块被分配给一个变量。</span><span class="sxs-lookup"><span data-stu-id="428fd-120">Each of the imported modules is assigned to a variable.</span></span> <span data-ttu-id="428fd-121">模块可位于通过名称或路径。</span><span class="sxs-lookup"><span data-stu-id="428fd-121">The modules can be located either by name or path.</span></span> <span data-ttu-id="428fd-122">在此示例中，模块名为`gulp`， `rimraf`， `gulp-concat`， `gulp-cssmin`，和`gulp-uglify`按名称检索。</span><span class="sxs-lookup"><span data-stu-id="428fd-122">In this example, the modules named `gulp`, `rimraf`, `gulp-concat`, `gulp-cssmin`, and `gulp-uglify` are retrieved by name.</span></span> <span data-ttu-id="428fd-123">此外，创建一系列的路径，以便可以重复使用并在任务中引用的 CSS 和 JavaScript 文件的位置。</span><span class="sxs-lookup"><span data-stu-id="428fd-123">Additionally, a series of paths are created so that the locations of CSS and JavaScript files can be reused and referenced within the tasks.</span></span> <span data-ttu-id="428fd-124">下表提供了说明中包含的模块*gulpfile.js*。</span><span class="sxs-lookup"><span data-stu-id="428fd-124">The following table provides descriptions of the modules included in *gulpfile.js*.</span></span>

| <span data-ttu-id="428fd-125">模块名</span><span class="sxs-lookup"><span data-stu-id="428fd-125">Module Name</span></span> | <span data-ttu-id="428fd-126">描述</span><span class="sxs-lookup"><span data-stu-id="428fd-126">Description</span></span> |
| ----------- | ----------- |
| <span data-ttu-id="428fd-127">gulp</span><span class="sxs-lookup"><span data-stu-id="428fd-127">gulp</span></span>        | <span data-ttu-id="428fd-128">Gulp 流式处理生成系统。</span><span class="sxs-lookup"><span data-stu-id="428fd-128">The Gulp streaming build system.</span></span> <span data-ttu-id="428fd-129">有关详细信息，请参阅[gulp](https://www.npmjs.com/package/gulp)。</span><span class="sxs-lookup"><span data-stu-id="428fd-129">For more information, see [gulp](https://www.npmjs.com/package/gulp).</span></span> |
| <span data-ttu-id="428fd-130">rimraf</span><span class="sxs-lookup"><span data-stu-id="428fd-130">rimraf</span></span>      | <span data-ttu-id="428fd-131">节点删除模块。</span><span class="sxs-lookup"><span data-stu-id="428fd-131">A Node deletion module.</span></span> <span data-ttu-id="428fd-132">有关详细信息，请参阅[rimraf](https://www.npmjs.com/package/rimraf)。</span><span class="sxs-lookup"><span data-stu-id="428fd-132">For more information, see [rimraf](https://www.npmjs.com/package/rimraf).</span></span> |
| <span data-ttu-id="428fd-133">gulp-concat</span><span class="sxs-lookup"><span data-stu-id="428fd-133">gulp-concat</span></span> | <span data-ttu-id="428fd-134">连接基于操作系统的换行字符的文件的模块。</span><span class="sxs-lookup"><span data-stu-id="428fd-134">A module that concatenates files based on the operating system's newline character.</span></span> <span data-ttu-id="428fd-135">有关详细信息，请参阅[gulp concat](https://www.npmjs.com/package/gulp-concat)。</span><span class="sxs-lookup"><span data-stu-id="428fd-135">For more information, see [gulp-concat](https://www.npmjs.com/package/gulp-concat).</span></span> |
| <span data-ttu-id="428fd-136">gulp-cssmin</span><span class="sxs-lookup"><span data-stu-id="428fd-136">gulp-cssmin</span></span> | <span data-ttu-id="428fd-137">缩减 CSS 文件的模块。</span><span class="sxs-lookup"><span data-stu-id="428fd-137">A module that minifies CSS files.</span></span> <span data-ttu-id="428fd-138">有关详细信息，请参阅[gulp cssmin](https://www.npmjs.com/package/gulp-cssmin)。</span><span class="sxs-lookup"><span data-stu-id="428fd-138">For more information, see [gulp-cssmin](https://www.npmjs.com/package/gulp-cssmin).</span></span> |
| <span data-ttu-id="428fd-139">gulp-uglify</span><span class="sxs-lookup"><span data-stu-id="428fd-139">gulp-uglify</span></span> | <span data-ttu-id="428fd-140">一个模块，缩减 *.js*文件。</span><span class="sxs-lookup"><span data-stu-id="428fd-140">A module that minifies *.js* files.</span></span> <span data-ttu-id="428fd-141">有关详细信息，请参阅[gulp 丑化](https://www.npmjs.com/package/gulp-uglify)。</span><span class="sxs-lookup"><span data-stu-id="428fd-141">For more information, see [gulp-uglify](https://www.npmjs.com/package/gulp-uglify).</span></span> |

<span data-ttu-id="428fd-142">一旦导入必要模块，可以指定任务。</span><span class="sxs-lookup"><span data-stu-id="428fd-142">Once the requisite modules are imported, the tasks can be specified.</span></span> <span data-ttu-id="428fd-143">此处有六个任务注册，通过以下代码表示：</span><span class="sxs-lookup"><span data-stu-id="428fd-143">Here there are six tasks registered, represented by the following code:</span></span>

```javascript
gulp.task("clean:js", done => rimraf(paths.concatJsDest, done));
gulp.task("clean:css", done => rimraf(paths.concatCssDest, done));
gulp.task("clean", gulp.series(["clean:js", "clean:css"]));

gulp.task("min:js", () => {
  return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
    .pipe(concat(paths.concatJsDest))
    .pipe(uglify())
    .pipe(gulp.dest("."));
});

gulp.task("min:css", () => {
  return gulp.src([paths.css, "!" + paths.minCss])
    .pipe(concat(paths.concatCssDest))
    .pipe(cssmin())
    .pipe(gulp.dest("."));
});

gulp.task("min", gulp.series(["min:js", "min:css"]));

// A 'default' task is required by Gulp v4
gulp.task("default", gulp.series(["min"]));
```

---

<span data-ttu-id="428fd-144">下表提供了上面的代码中指定的任务的说明：</span><span class="sxs-lookup"><span data-stu-id="428fd-144">The following table provides an explanation of the tasks specified in the code above:</span></span>

|<span data-ttu-id="428fd-145">任务名称</span><span class="sxs-lookup"><span data-stu-id="428fd-145">Task Name</span></span>|<span data-ttu-id="428fd-146">描述</span><span class="sxs-lookup"><span data-stu-id="428fd-146">Description</span></span>|
|--- |--- |
|<span data-ttu-id="428fd-147">clean:js</span><span class="sxs-lookup"><span data-stu-id="428fd-147">clean:js</span></span>|<span data-ttu-id="428fd-148">一个使用 rimraf Node 删除模块删除缩减版 site.js 文件的任务。</span><span class="sxs-lookup"><span data-stu-id="428fd-148">A task that uses the rimraf Node deletion module to remove the minified version of the site.js file.</span></span>|
|<span data-ttu-id="428fd-149">clean:css</span><span class="sxs-lookup"><span data-stu-id="428fd-149">clean:css</span></span>|<span data-ttu-id="428fd-150">一个使用 rimraf Node 删除模块删除缩减版 site.css 文件的的任务。</span><span class="sxs-lookup"><span data-stu-id="428fd-150">A task that uses the rimraf Node deletion module to remove the minified version of the site.css file.</span></span>|
|<span data-ttu-id="428fd-151">清理</span><span class="sxs-lookup"><span data-stu-id="428fd-151">clean</span></span>|<span data-ttu-id="428fd-152">调用的任务`clean:js`任务中后, 跟`clean:css`任务。</span><span class="sxs-lookup"><span data-stu-id="428fd-152">A task that calls the `clean:js` task, followed by the `clean:css` task.</span></span>|
|<span data-ttu-id="428fd-153">min:js</span><span class="sxs-lookup"><span data-stu-id="428fd-153">min:js</span></span>|<span data-ttu-id="428fd-154">缩减，并将连接的 js 文件夹中的所有.js 文件的任务。</span><span class="sxs-lookup"><span data-stu-id="428fd-154">A task that minifies and concatenates all .js files within the js folder.</span></span> <span data-ttu-id="428fd-155">。 Min.js 文件将不会。</span><span class="sxs-lookup"><span data-stu-id="428fd-155">The .min.js files are excluded.</span></span>|
|<span data-ttu-id="428fd-156">min:css</span><span class="sxs-lookup"><span data-stu-id="428fd-156">min:css</span></span>|<span data-ttu-id="428fd-157">一个任务，缩减，并将连接的 css 文件夹中的所有.css 文件。</span><span class="sxs-lookup"><span data-stu-id="428fd-157">A task that minifies and concatenates all .css files within the css folder.</span></span> <span data-ttu-id="428fd-158">。 Min.css 文件将不会。</span><span class="sxs-lookup"><span data-stu-id="428fd-158">The .min.css files are excluded.</span></span>|
|<span data-ttu-id="428fd-159">min</span><span class="sxs-lookup"><span data-stu-id="428fd-159">min</span></span>|<span data-ttu-id="428fd-160">调用的任务`min:js`任务中后, 跟`min:css`任务。</span><span class="sxs-lookup"><span data-stu-id="428fd-160">A task that calls the `min:js` task, followed by the `min:css` task.</span></span>|

## <a name="running-default-tasks"></a><span data-ttu-id="428fd-161">正在运行的默认任务</span><span class="sxs-lookup"><span data-stu-id="428fd-161">Running default tasks</span></span>

<span data-ttu-id="428fd-162">如果你尚未创建新的 Web 应用，Visual Studio 中创建一个新的 ASP.NET Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="428fd-162">If you haven't already created a new Web app, create a new ASP.NET Web Application project in Visual Studio.</span></span>

1. <span data-ttu-id="428fd-163">打开*package.json*文件 (添加如果不是存在) 并添加以下。</span><span class="sxs-lookup"><span data-stu-id="428fd-163">Open the *package.json* file (add if not there) and add the following.</span></span>

    ```json
    {
      "devDependencies": {
        "gulp": "^4.0.0",
        "gulp-concat": "2.6.1",
        "gulp-cssmin": "0.2.0",
        "gulp-uglify": "3.0.0",
        "rimraf": "2.6.1"
      }
    }
    ```

2. <span data-ttu-id="428fd-164">将新的 JavaScript 文件添加到你的项目并将其命名*gulpfile.js*，然后将以下代码复制。</span><span class="sxs-lookup"><span data-stu-id="428fd-164">Add a new JavaScript file to your project and name it *gulpfile.js*, then copy the following code.</span></span>

    ```javascript
    /// <binding Clean='clean' />
    "use strict";

    const gulp = require("gulp"),
          rimraf = require("rimraf"),
          concat = require("gulp-concat"),
          cssmin = require("gulp-cssmin"),
          uglify = require("gulp-uglify");

    const paths = {
      webroot: "./wwwroot/"
    };

    paths.js = paths.webroot + "js/**/*.js";
    paths.minJs = paths.webroot + "js/**/*.min.js";
    paths.css = paths.webroot + "css/**/*.css";
    paths.minCss = paths.webroot + "css/**/*.min.css";
    paths.concatJsDest = paths.webroot + "js/site.min.js";
    paths.concatCssDest = paths.webroot + "css/site.min.css";

    gulp.task("clean:js", done => rimraf(paths.concatJsDest, done));
    gulp.task("clean:css", done => rimraf(paths.concatCssDest, done));
    gulp.task("clean", gulp.series(["clean:js", "clean:css"]));

    gulp.task("min:js", () => {
      return gulp.src([paths.js, "!" + paths.minJs], { base: "." })
      .pipe(concat(paths.concatJsDest))
      .pipe(uglify())
      .pipe(gulp.dest("."));
    });

    gulp.task("min:css", () => {
      return gulp.src([paths.css, "!" + paths.minCss])
      .pipe(concat(paths.concatCssDest))
      .pipe(cssmin())
      .pipe(gulp.dest("."));
    });

    gulp.task("min", gulp.series(["min:js", "min:css"]));

    // A 'default' task is required by Gulp v4
    gulp.task("default", gulp.series(["min"]));
    ```

3. <span data-ttu-id="428fd-165">在中**解决方案资源管理器**，右键单击*gulpfile.js*，然后选择**Task Runner Explorer**。</span><span class="sxs-lookup"><span data-stu-id="428fd-165">In **Solution Explorer**, right-click *gulpfile.js*, and select **Task Runner Explorer**.</span></span>

    ![从解决方案资源管理器中打开任务运行程序资源管理器](using-gulp/_static/02-SolutionExplorer-TaskRunnerExplorer.png)

    <span data-ttu-id="428fd-167">**Task Runner Explorer**显示 Gulp 任务的列表。</span><span class="sxs-lookup"><span data-stu-id="428fd-167">**Task Runner Explorer** shows the list of Gulp tasks.</span></span> <span data-ttu-id="428fd-168">(您可能需要单击**刷新**显示项目名称左侧的按钮。)</span><span class="sxs-lookup"><span data-stu-id="428fd-168">(You might have to click the **Refresh** button that appears to the left of the project name.)</span></span>

    ![任务运行程序资源管理器](using-gulp/_static/03-TaskRunnerExplorer.png)

    > [!IMPORTANT]
    > <span data-ttu-id="428fd-170">**Task Runner Explorer**才会显示上下文菜单项*gulpfile.js*根项目目录中。</span><span class="sxs-lookup"><span data-stu-id="428fd-170">The **Task Runner Explorer** context menu item appears only if *gulpfile.js* is in the root project directory.</span></span>

4. <span data-ttu-id="428fd-171">在“任务运行程序资源管理器”中的“任务”下，右键单击“clean”，然后从弹出菜单中选择“运行”。</span><span class="sxs-lookup"><span data-stu-id="428fd-171">Underneath **Tasks** in **Task Runner Explorer**, right-click **clean**, and select **Run** from the pop-up menu.</span></span>

    ![Clean 任务，任务运行程序资源管理器](using-gulp/_static/04-TaskRunner-clean.png)

    <span data-ttu-id="428fd-173">**任务运行程序资源管理器**将创建名为“clean”的新选项卡，并根据 *gulpfile.js* 中的定义执行 clean 任务。</span><span class="sxs-lookup"><span data-stu-id="428fd-173">**Task Runner Explorer** will create a new tab named **clean** and execute the clean task as it's defined in *gulpfile.js*.</span></span>

5. <span data-ttu-id="428fd-174">右键单击“clean”任务，然后选择“绑定”>“生成之前” > 。</span><span class="sxs-lookup"><span data-stu-id="428fd-174">Right-click the **clean** task, then select **Bindings** > **Before Build**.</span></span>

    ![绑定 BeforeBuild 任务运行程序资源管理器](using-gulp/_static/05-TaskRunner-BeforeBuild.png)

    <span data-ttu-id="428fd-176">“生成之前”绑定会配置 clean 任务，使之在每次生成项目之前自动运行。</span><span class="sxs-lookup"><span data-stu-id="428fd-176">The **Before Build** binding configures the clean task to run automatically before each build of the project.</span></span>

<span data-ttu-id="428fd-177">使用**任务运行程序资源管理器**设置的绑定以注释形式存储在 *gulpfile.js* 顶部，仅在 Visual Studio 中有效。</span><span class="sxs-lookup"><span data-stu-id="428fd-177">The bindings you set up with **Task Runner Explorer** are stored in the form of a comment at the top of your *gulpfile.js* and are effective only in Visual Studio.</span></span> <span data-ttu-id="428fd-178">一种不需要 Visual Studio 的替代方法是将 *csproj* 文件中的 gulp 任务配置为自动执行。</span><span class="sxs-lookup"><span data-stu-id="428fd-178">An alternative that doesn't require Visual Studio is to configure automatic execution of gulp tasks in your *.csproj* file.</span></span> <span data-ttu-id="428fd-179">例如，将以下代码置于 *.csproj* 文件中：</span><span class="sxs-lookup"><span data-stu-id="428fd-179">For example, put this in your *.csproj* file:</span></span>

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
  <Exec Command="gulp clean" />
</Target>
```

<span data-ttu-id="428fd-180">现在，在 Visual Studio 中或从命令提示符处使用运行项目时执行清理任务[dotnet 运行](/dotnet/core/tools/dotnet-run)命令 (运行`npm install`第一个)。</span><span class="sxs-lookup"><span data-stu-id="428fd-180">Now the clean task is executed when you run the project in Visual Studio or from a command prompt using the [dotnet run](/dotnet/core/tools/dotnet-run) command (run `npm install` first).</span></span>

## <a name="defining-and-running-a-new-task"></a><span data-ttu-id="428fd-181">定义和运行新任务</span><span class="sxs-lookup"><span data-stu-id="428fd-181">Defining and running a new task</span></span>

<span data-ttu-id="428fd-182">若要定义新的 Gulp 任务，请修改*gulpfile.js*。</span><span class="sxs-lookup"><span data-stu-id="428fd-182">To define a new Gulp task, modify *gulpfile.js*.</span></span>

1. <span data-ttu-id="428fd-183">将以下 JavaScript 添加到末尾*gulpfile.js*:</span><span class="sxs-lookup"><span data-stu-id="428fd-183">Add the following JavaScript to the end of *gulpfile.js*:</span></span>

    ```javascript
    gulp.task('first', done => {
      console.log('first task! <-----');
      done(); // signal completion
    });
    ```

    <span data-ttu-id="428fd-184">此任务名为`first`，和它就会显示一个字符串。</span><span class="sxs-lookup"><span data-stu-id="428fd-184">This task is named `first`, and it simply displays a string.</span></span>

2. <span data-ttu-id="428fd-185">保存*gulpfile.js*。</span><span class="sxs-lookup"><span data-stu-id="428fd-185">Save *gulpfile.js*.</span></span>

3. <span data-ttu-id="428fd-186">在中**解决方案资源管理器**，右键单击*gulpfile.js*，然后选择*Task Runner Explorer*。</span><span class="sxs-lookup"><span data-stu-id="428fd-186">In **Solution Explorer**, right-click *gulpfile.js*, and select *Task Runner Explorer*.</span></span>

4. <span data-ttu-id="428fd-187">在中**Task Runner Explorer**，右键单击**第一个**，然后选择**运行**。</span><span class="sxs-lookup"><span data-stu-id="428fd-187">In **Task Runner Explorer**, right-click **first**, and select **Run**.</span></span>

    ![任务运行程序资源管理器运行第一个任务](using-gulp/_static/06-TaskRunner-First.png)

    <span data-ttu-id="428fd-189">此时会显示输出文本。</span><span class="sxs-lookup"><span data-stu-id="428fd-189">The output text is displayed.</span></span> <span data-ttu-id="428fd-190">若要查看基于常见方案的示例，请参阅 [Gulp 脚本](#gulp-recipes)。</span><span class="sxs-lookup"><span data-stu-id="428fd-190">To see examples based on common scenarios, see [Gulp Recipes](#gulp-recipes).</span></span>

## <a name="defining-and-running-tasks-in-a-series"></a><span data-ttu-id="428fd-191">定义和运行一系列任务</span><span class="sxs-lookup"><span data-stu-id="428fd-191">Defining and running tasks in a series</span></span>

<span data-ttu-id="428fd-192">运行多个任务时，默认情况下这些任务会并发运行。</span><span class="sxs-lookup"><span data-stu-id="428fd-192">When you run multiple tasks, the tasks run concurrently by default.</span></span> <span data-ttu-id="428fd-193">但是，如果需要以特定顺序运行任务，则必须指定每个任务的具体完成时间，以及哪些任务依赖于其他任务的完成。</span><span class="sxs-lookup"><span data-stu-id="428fd-193">However, if you need to run tasks in a specific order, you must specify when each task is complete, as well as which tasks depend on the completion of another task.</span></span>

1. <span data-ttu-id="428fd-194">若要定义一系列任务按顺序运行，请替换`first`中前面添加的任务*gulpfile.js*以下：</span><span class="sxs-lookup"><span data-stu-id="428fd-194">To define a series of tasks to run in order, replace the `first` task that you added above in *gulpfile.js* with the following:</span></span>

    ```javascript
    gulp.task('series:first', done => {
      console.log('first task! <-----');
      done(); // signal completion
    });
    gulp.task('series:second', done => {
      console.log('second task! <-----');
        done(); // signal completion
    });

    gulp.task('series', gulp.series(['series:first', 'series:second']), () => { });

    // A 'default' task is required by Gulp v4
      gulp.task('default', gulp.series('series'));
    ```

    <span data-ttu-id="428fd-195">现在有三个任务： `series:first`， `series:second`，和`series`。</span><span class="sxs-lookup"><span data-stu-id="428fd-195">You now have three tasks: `series:first`, `series:second`, and `series`.</span></span> <span data-ttu-id="428fd-196">`series:second`任务包括第二个参数，以便指定要运行和前完成的任务的数组`series:second`任务将运行。</span><span class="sxs-lookup"><span data-stu-id="428fd-196">The `series:second` task includes a second parameter which specifies an array of tasks to be run and completed before the `series:second` task will run.</span></span> <span data-ttu-id="428fd-197">上面，唯一的代码中指定的那样`series:first`必须在任务完成之前`series:second`任务将运行。</span><span class="sxs-lookup"><span data-stu-id="428fd-197">As specified in the code above, only the `series:first` task must be completed before the `series:second` task will run.</span></span>

2. <span data-ttu-id="428fd-198">保存*gulpfile.js*。</span><span class="sxs-lookup"><span data-stu-id="428fd-198">Save *gulpfile.js*.</span></span>

3. <span data-ttu-id="428fd-199">在中**解决方案资源管理器**，右键单击*gulpfile.js* ，然后选择**Task Runner Explorer**如果尚未打开。</span><span class="sxs-lookup"><span data-stu-id="428fd-199">In **Solution Explorer**, right-click *gulpfile.js* and select **Task Runner Explorer** if it isn't already open.</span></span>

4. <span data-ttu-id="428fd-200">在中**Task Runner Explorer**，右键单击**系列**，然后选择**运行**。</span><span class="sxs-lookup"><span data-stu-id="428fd-200">In **Task Runner Explorer**, right-click **series** and select **Run**.</span></span>

    ![任务运行程序资源管理器运行任务序列](using-gulp/_static/07-TaskRunner-Series.png)

## <a name="intellisense"></a><span data-ttu-id="428fd-202">IntelliSense</span><span class="sxs-lookup"><span data-stu-id="428fd-202">IntelliSense</span></span>

<span data-ttu-id="428fd-203">IntelliSense 提供了代码完成、 参数说明和其他功能以提高工作效率并减少错误。</span><span class="sxs-lookup"><span data-stu-id="428fd-203">IntelliSense provides code completion, parameter descriptions, and other features to boost productivity and to decrease errors.</span></span> <span data-ttu-id="428fd-204">Gulp 任务是用 JavaScript 编写的;因此，IntelliSense 可以提供开发时获得帮助。</span><span class="sxs-lookup"><span data-stu-id="428fd-204">Gulp tasks are written in JavaScript; therefore, IntelliSense can provide assistance while developing.</span></span> <span data-ttu-id="428fd-205">使用 JavaScript 时，IntelliSense 会列出对象、 函数、 属性和可用的参数根据当前上下文。</span><span class="sxs-lookup"><span data-stu-id="428fd-205">As you work with JavaScript, IntelliSense lists the objects, functions, properties, and parameters that are available based on your current context.</span></span> <span data-ttu-id="428fd-206">从 intellisense 以完成代码提供的弹出列表中选择编码选项。</span><span class="sxs-lookup"><span data-stu-id="428fd-206">Select a coding option from the pop-up list provided by IntelliSense to complete the code.</span></span>

![gulp IntelliSense](using-gulp/_static/08-IntelliSense.png)

<span data-ttu-id="428fd-208">有关 IntelliSense 的详细信息，请参阅[JavaScript IntelliSense](/visualstudio/ide/javascript-intellisense)。</span><span class="sxs-lookup"><span data-stu-id="428fd-208">For more information about IntelliSense, see [JavaScript IntelliSense](/visualstudio/ide/javascript-intellisense).</span></span>

## <a name="development-staging-and-production-environments"></a><span data-ttu-id="428fd-209">开发、 过渡和生产环境</span><span class="sxs-lookup"><span data-stu-id="428fd-209">Development, staging, and production environments</span></span>

<span data-ttu-id="428fd-210">当 Gulp 用于优化过渡和生产客户端的文件时，已处理的文件保存到本地的过渡和生产位置。</span><span class="sxs-lookup"><span data-stu-id="428fd-210">When Gulp is used to optimize client-side files for staging and production, the processed files are saved to a local staging and production location.</span></span> <span data-ttu-id="428fd-211">*_Layout.cshtml*文件使用**环境**的标记帮助程序提供两个不同版本的 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="428fd-211">The *_Layout.cshtml* file uses the **environment** tag helper to provide two different versions of CSS files.</span></span> <span data-ttu-id="428fd-212">CSS 文件的一个版本用于开发，另一个版本适用于过渡和生产。</span><span class="sxs-lookup"><span data-stu-id="428fd-212">One version of CSS files is for development and the other version is optimized for both staging and production.</span></span> <span data-ttu-id="428fd-213">在 Visual Studio 2017 中，更改时**ASPNETCORE_ENVIRONMENT**环境变量为`Production`，Visual Studio 将生成 Web 应用并链接到最小化的 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="428fd-213">In Visual Studio 2017, when you change the **ASPNETCORE_ENVIRONMENT** environment variable to `Production`, Visual Studio will build the Web app and link to the minimized CSS files.</span></span> <span data-ttu-id="428fd-214">下面的标记演示**环境**标记帮助程序包含将标记与`Development`CSS 文件和缩小`Staging, Production`CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="428fd-214">The following markup shows the **environment** tag helpers containing link tags to the `Development` CSS files and the minified `Staging, Production` CSS files.</span></span>

```html
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.2.0.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery"
            crossorigin="anonymous"
            integrity="sha384-K+ctZQ+LL8q6tP7I94W+qzQsfRV2a+AfHIi9k8z8l9ggpc8X+Ytst4yBo/hH+8Fk">
    </script>
    <script src="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/bootstrap.min.js"
            asp-fallback-src="~/lib/bootstrap/dist/js/bootstrap.min.js"
            asp-fallback-test="window.jQuery && window.jQuery.fn && window.jQuery.fn.modal"
            crossorigin="anonymous"
            integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa">
    </script>
    <script src="~/js/site.min.js" asp-append-version="true"></script>
</environment>
```

## <a name="switching-between-environments"></a><span data-ttu-id="428fd-215">在环境之间切换</span><span class="sxs-lookup"><span data-stu-id="428fd-215">Switching between environments</span></span>

<span data-ttu-id="428fd-216">若要针对不同的环境编译之间切换，请修改**ASPNETCORE_ENVIRONMENT**环境变量的值。</span><span class="sxs-lookup"><span data-stu-id="428fd-216">To switch between compiling for different environments, modify the **ASPNETCORE_ENVIRONMENT** environment variable's value.</span></span>

1. <span data-ttu-id="428fd-217">在**任务运行程序资源管理器**中，验证 **min** 任务是否已设置为在**生成之前**运行。</span><span class="sxs-lookup"><span data-stu-id="428fd-217">In **Task Runner Explorer**, verify that the **min** task has been set to run **Before Build**.</span></span>

2. <span data-ttu-id="428fd-218">在中**解决方案资源管理器**，右键单击项目名称并选择**属性**。</span><span class="sxs-lookup"><span data-stu-id="428fd-218">In **Solution Explorer**, right-click the project name and select **Properties**.</span></span>

    <span data-ttu-id="428fd-219">显示 Web 应用的属性表。</span><span class="sxs-lookup"><span data-stu-id="428fd-219">The property sheet for the Web app is displayed.</span></span>

3. <span data-ttu-id="428fd-220">单击“调试”选项卡。</span><span class="sxs-lookup"><span data-stu-id="428fd-220">Click the **Debug** tab.</span></span>

4. <span data-ttu-id="428fd-221">设置的值**宿主： 环境**环境变量为`Production`。</span><span class="sxs-lookup"><span data-stu-id="428fd-221">Set the value of the **Hosting:Environment** environment variable to `Production`.</span></span>

5. <span data-ttu-id="428fd-222">按**F5**在浏览器中运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="428fd-222">Press **F5** to run the application in a browser.</span></span>

6. <span data-ttu-id="428fd-223">在浏览器窗口中，右键单击页并选择**查看源**若要查看该页面的 HTML。</span><span class="sxs-lookup"><span data-stu-id="428fd-223">In the browser window, right-click the page and select **View Source** to view the HTML for the page.</span></span>

    <span data-ttu-id="428fd-224">请注意，样式表链接指向的缩减的 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="428fd-224">Notice that the stylesheet links point to the minified CSS files.</span></span>

7. <span data-ttu-id="428fd-225">关闭浏览器来停止 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="428fd-225">Close the browser to stop the Web app.</span></span>

8. <span data-ttu-id="428fd-226">在 Visual Studio 中，返回到 Web 应用的属性表，并更改**宿主： 环境**环境变量回`Development`。</span><span class="sxs-lookup"><span data-stu-id="428fd-226">In Visual Studio, return to the property sheet for the Web app and change the **Hosting:Environment** environment variable back to `Development`.</span></span>

9. <span data-ttu-id="428fd-227">按**F5**再次在浏览器中运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="428fd-227">Press **F5** to run the application in a browser again.</span></span>

10. <span data-ttu-id="428fd-228">在浏览器窗口中，右键单击页并选择**查看源**若要查看该页面的 HTML。</span><span class="sxs-lookup"><span data-stu-id="428fd-228">In the browser window, right-click the page and select **View Source** to see the HTML for the page.</span></span>

    <span data-ttu-id="428fd-229">请注意，样式表链接指向 CSS 文件的 unminified 版本。</span><span class="sxs-lookup"><span data-stu-id="428fd-229">Notice that the stylesheet links point to the unminified versions of the CSS files.</span></span>

<span data-ttu-id="428fd-230">有关 ASP.NET Core 中的环境的详细信息，请参阅[使用多个环境](../fundamentals/environments.md)。</span><span class="sxs-lookup"><span data-stu-id="428fd-230">For more information related to environments in ASP.NET Core, see [Use multiple environments](../fundamentals/environments.md).</span></span>

## <a name="task-and-module-details"></a><span data-ttu-id="428fd-231">任务和模块的详细信息</span><span class="sxs-lookup"><span data-stu-id="428fd-231">Task and module details</span></span>

<span data-ttu-id="428fd-232">Gulp 任务已注册到函数名称。</span><span class="sxs-lookup"><span data-stu-id="428fd-232">A Gulp task is registered with a function name.</span></span> <span data-ttu-id="428fd-233">如果当前任务之前必须运行其他任务，你可以指定依赖关系。</span><span class="sxs-lookup"><span data-stu-id="428fd-233">You can specify dependencies if other tasks must run before the current task.</span></span> <span data-ttu-id="428fd-234">其他函数，您可以运行和监视的 Gulp 任务，以及将源设置 (*src*) 和目标 (*dest*) 正在修改的文件。</span><span class="sxs-lookup"><span data-stu-id="428fd-234">Additional functions allow you to run and watch the Gulp tasks, as well as set the source (*src*) and destination (*dest*) of the files being modified.</span></span> <span data-ttu-id="428fd-235">下面是主要的 Gulp API 函数：</span><span class="sxs-lookup"><span data-stu-id="428fd-235">The following are the primary Gulp API functions:</span></span>

|<span data-ttu-id="428fd-236">Gulp 函数</span><span class="sxs-lookup"><span data-stu-id="428fd-236">Gulp Function</span></span>|<span data-ttu-id="428fd-237">语法</span><span class="sxs-lookup"><span data-stu-id="428fd-237">Syntax</span></span>|<span data-ttu-id="428fd-238">描述</span><span class="sxs-lookup"><span data-stu-id="428fd-238">Description</span></span>|
|---   |--- |--- |
|<span data-ttu-id="428fd-239">任务</span><span class="sxs-lookup"><span data-stu-id="428fd-239">task</span></span>  |`gulp.task(name[, deps], fn) { }`|<span data-ttu-id="428fd-240">`task`函数创建的任务。</span><span class="sxs-lookup"><span data-stu-id="428fd-240">The `task` function creates a task.</span></span> <span data-ttu-id="428fd-241">`name`参数定义任务的名称。</span><span class="sxs-lookup"><span data-stu-id="428fd-241">The `name` parameter defines the name of the task.</span></span> <span data-ttu-id="428fd-242">`deps`参数包含要完成此任务运行之前任务的数组。</span><span class="sxs-lookup"><span data-stu-id="428fd-242">The `deps` parameter contains an array of tasks to be completed before this task runs.</span></span> <span data-ttu-id="428fd-243">`fn`参数表示执行任务的操作的回调函数。</span><span class="sxs-lookup"><span data-stu-id="428fd-243">The `fn` parameter represents a callback function which performs the operations of the task.</span></span>|
|<span data-ttu-id="428fd-244">监视</span><span class="sxs-lookup"><span data-stu-id="428fd-244">watch</span></span> |`gulp.watch(glob [, opts], tasks) { }`|<span data-ttu-id="428fd-245">`watch` Function 可监视文件和运行任务发生文件更改时。</span><span class="sxs-lookup"><span data-stu-id="428fd-245">The `watch` function monitors files and runs tasks when a file change occurs.</span></span> <span data-ttu-id="428fd-246">`glob`参数是`string`或`array`，它确定要监视哪些文件。</span><span class="sxs-lookup"><span data-stu-id="428fd-246">The `glob` parameter is a `string` or `array` that determines which files to watch.</span></span> <span data-ttu-id="428fd-247">`opts`参数提供了监视选项的其他文件。</span><span class="sxs-lookup"><span data-stu-id="428fd-247">The `opts` parameter provides additional file watching options.</span></span>|
|<span data-ttu-id="428fd-248">src</span><span class="sxs-lookup"><span data-stu-id="428fd-248">src</span></span>   |`gulp.src(globs[, options]) { }`|<span data-ttu-id="428fd-249">`src`函数提供了与 glob 值匹配的文件。</span><span class="sxs-lookup"><span data-stu-id="428fd-249">The `src` function provides files that match the glob value(s).</span></span> <span data-ttu-id="428fd-250">`glob`参数是`string`或`array`，它确定哪些文件读取。</span><span class="sxs-lookup"><span data-stu-id="428fd-250">The `glob` parameter is a `string` or `array` that determines which files to read.</span></span> <span data-ttu-id="428fd-251">`options`参数提供了附加文件选项。</span><span class="sxs-lookup"><span data-stu-id="428fd-251">The `options` parameter provides additional file options.</span></span>|
|<span data-ttu-id="428fd-252">dest</span><span class="sxs-lookup"><span data-stu-id="428fd-252">dest</span></span>  |`gulp.dest(path[, options]) { }`|<span data-ttu-id="428fd-253">`dest`函数用于定义要向其写入文件的位置。</span><span class="sxs-lookup"><span data-stu-id="428fd-253">The `dest` function defines a location to which files can be written.</span></span> <span data-ttu-id="428fd-254">`path`参数是字符串或函数，用于确定目标文件夹。</span><span class="sxs-lookup"><span data-stu-id="428fd-254">The `path` parameter is a string or function that determines the destination folder.</span></span> <span data-ttu-id="428fd-255">`options`参数是一个对象，指定输出文件夹选项。</span><span class="sxs-lookup"><span data-stu-id="428fd-255">The `options` parameter is an object that specifies output folder options.</span></span>|

<span data-ttu-id="428fd-256">有关其他 Gulp API 参考信息，请参阅[Gulp Docs API](https://gulpjs.org/API.html)。</span><span class="sxs-lookup"><span data-stu-id="428fd-256">For additional Gulp API reference information, see [Gulp Docs API](https://gulpjs.org/API.html).</span></span>

## <a name="gulp-recipes"></a><span data-ttu-id="428fd-257">Gulp 脚本</span><span class="sxs-lookup"><span data-stu-id="428fd-257">Gulp recipes</span></span>

<span data-ttu-id="428fd-258">Gulp 社区提供 Gulp [脚本](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md)。</span><span class="sxs-lookup"><span data-stu-id="428fd-258">The Gulp community provides Gulp [Recipes](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md).</span></span> <span data-ttu-id="428fd-259">这些脚本包含适用于常见方案的 Gulp 任务。</span><span class="sxs-lookup"><span data-stu-id="428fd-259">These recipes consist of Gulp tasks to address common scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="428fd-260">其他资源</span><span class="sxs-lookup"><span data-stu-id="428fd-260">Additional resources</span></span>

* [<span data-ttu-id="428fd-261">Gulp 文档</span><span class="sxs-lookup"><span data-stu-id="428fd-261">Gulp documentation</span></span>](https://github.com/gulpjs/gulp/blob/master/docs/README.md)
* [<span data-ttu-id="428fd-262">绑定和缩减中 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="428fd-262">Bundling and minification in ASP.NET Core</span></span>](bundling-and-minification.md)
* [<span data-ttu-id="428fd-263">在 ASP.NET Core 中使用 Grunt</span><span class="sxs-lookup"><span data-stu-id="428fd-263">Use Grunt in ASP.NET Core</span></span>](using-grunt.md)
