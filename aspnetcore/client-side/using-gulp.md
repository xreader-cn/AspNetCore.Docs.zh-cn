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
# <a name="use-gulp-in-aspnet-core"></a>在 ASP.NET Core 中使用 Gulp

通过[Scott Addie](https://scottaddie.com)， [Shayne Boyer](https://twitter.com/spboyer)，和[David 松树](https://twitter.com/davidpine7)

在典型的现代 Web 应用中，生成过程可能包括以下操作：

* 捆绑和缩小 JavaScript 和 CSS 文件。
* 运行工具以调用之前每个生成的绑定和缩减任务。
* 将 LESS 或 SASS 文件编译成 CSS。
* 将 CoffeeScript 或 TypeScript 文件编译成 JavaScript。


任务运行程序是一种自动执行这些常规开发任务和其他任务的工具。 Visual Studio 为两个常用的基于 JavaScript 的任务运行程序提供的内置支持：[Gulp](https://gulpjs.com/)并[Grunt](using-grunt.md)。

## <a name="gulp"></a>Gulp

Gulp 是一个基于 JavaScript 的流式处理生成工具包，用于客户端代码。 它通常用于通过一系列的进程的客户端文件流式传输特定的事件触发的生成环境中时。 例如，使用 Gulp 来自动执行[捆绑和缩小](bundling-and-minification.md)或清理之前新生成的开发环境。

中定义一组的 Gulp 任务*gulpfile.js*。 以下 JavaScript 包括 Gulp 模块，并指定文件路径，以引用中即将推出的任务：

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

上面的代码中指定需要哪些节点模块。 `require`函数导入的每个模块，以便依赖任务能够利用其功能。 每个导入的模块被分配给一个变量。 模块可位于通过名称或路径。 在此示例中，模块名为`gulp`， `rimraf`， `gulp-concat`， `gulp-cssmin`，和`gulp-uglify`按名称检索。 此外，创建一系列的路径，以便可以重复使用并在任务中引用的 CSS 和 JavaScript 文件的位置。 下表提供了说明中包含的模块*gulpfile.js*。

| 模块名 | 描述 |
| ----------- | ----------- |
| gulp        | Gulp 流式处理生成系统。 有关详细信息，请参阅[gulp](https://www.npmjs.com/package/gulp)。 |
| rimraf      | 节点删除模块。 有关详细信息，请参阅[rimraf](https://www.npmjs.com/package/rimraf)。 |
| gulp-concat | 连接基于操作系统的换行字符的文件的模块。 有关详细信息，请参阅[gulp concat](https://www.npmjs.com/package/gulp-concat)。 |
| gulp-cssmin | 缩减 CSS 文件的模块。 有关详细信息，请参阅[gulp cssmin](https://www.npmjs.com/package/gulp-cssmin)。 |
| gulp-uglify | 一个模块，缩减 *.js*文件。 有关详细信息，请参阅[gulp 丑化](https://www.npmjs.com/package/gulp-uglify)。 |

一旦导入必要模块，可以指定任务。 此处有六个任务注册，通过以下代码表示：

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

下表提供了上面的代码中指定的任务的说明：

|任务名称|描述|
|--- |--- |
|clean:js|一个使用 rimraf Node 删除模块删除缩减版 site.js 文件的任务。|
|clean:css|一个使用 rimraf Node 删除模块删除缩减版 site.css 文件的的任务。|
|清理|调用的任务`clean:js`任务中后, 跟`clean:css`任务。|
|min:js|缩减，并将连接的 js 文件夹中的所有.js 文件的任务。 。 Min.js 文件将不会。|
|min:css|一个任务，缩减，并将连接的 css 文件夹中的所有.css 文件。 。 Min.css 文件将不会。|
|min|调用的任务`min:js`任务中后, 跟`min:css`任务。|

## <a name="running-default-tasks"></a>正在运行的默认任务

如果你尚未创建新的 Web 应用，Visual Studio 中创建一个新的 ASP.NET Web 应用程序项目。

1. 打开*package.json*文件 (添加如果不是存在) 并添加以下。

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

2. 将新的 JavaScript 文件添加到你的项目并将其命名*gulpfile.js*，然后将以下代码复制。

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

3. 在中**解决方案资源管理器**，右键单击*gulpfile.js*，然后选择**Task Runner Explorer**。

    ![从解决方案资源管理器中打开任务运行程序资源管理器](using-gulp/_static/02-SolutionExplorer-TaskRunnerExplorer.png)

    **Task Runner Explorer**显示 Gulp 任务的列表。 (您可能需要单击**刷新**显示项目名称左侧的按钮。)

    ![任务运行程序资源管理器](using-gulp/_static/03-TaskRunnerExplorer.png)

    > [!IMPORTANT]
    > **Task Runner Explorer**才会显示上下文菜单项*gulpfile.js*根项目目录中。

4. 在“任务运行程序资源管理器”中的“任务”下，右键单击“clean”，然后从弹出菜单中选择“运行”。

    ![Clean 任务，任务运行程序资源管理器](using-gulp/_static/04-TaskRunner-clean.png)

    **任务运行程序资源管理器**将创建名为“clean”的新选项卡，并根据 *gulpfile.js* 中的定义执行 clean 任务。

5. 右键单击“clean”任务，然后选择“绑定”>“生成之前” > 。

    ![绑定 BeforeBuild 任务运行程序资源管理器](using-gulp/_static/05-TaskRunner-BeforeBuild.png)

    “生成之前”绑定会配置 clean 任务，使之在每次生成项目之前自动运行。

使用**任务运行程序资源管理器**设置的绑定以注释形式存储在 *gulpfile.js* 顶部，仅在 Visual Studio 中有效。 一种不需要 Visual Studio 的替代方法是将 *csproj* 文件中的 gulp 任务配置为自动执行。 例如，将以下代码置于 *.csproj* 文件中：

```xml
<Target Name="MyPreCompileTarget" BeforeTargets="Build">
  <Exec Command="gulp clean" />
</Target>
```

现在，在 Visual Studio 中或从命令提示符处使用运行项目时执行清理任务[dotnet 运行](/dotnet/core/tools/dotnet-run)命令 (运行`npm install`第一个)。

## <a name="defining-and-running-a-new-task"></a>定义和运行新任务

若要定义新的 Gulp 任务，请修改*gulpfile.js*。

1. 将以下 JavaScript 添加到末尾*gulpfile.js*:

    ```javascript
    gulp.task('first', done => {
      console.log('first task! <-----');
      done(); // signal completion
    });
    ```

    此任务名为`first`，和它就会显示一个字符串。

2. 保存*gulpfile.js*。

3. 在中**解决方案资源管理器**，右键单击*gulpfile.js*，然后选择*Task Runner Explorer*。

4. 在中**Task Runner Explorer**，右键单击**第一个**，然后选择**运行**。

    ![任务运行程序资源管理器运行第一个任务](using-gulp/_static/06-TaskRunner-First.png)

    此时会显示输出文本。 若要查看基于常见方案的示例，请参阅 [Gulp 脚本](#gulp-recipes)。

## <a name="defining-and-running-tasks-in-a-series"></a>定义和运行一系列任务

运行多个任务时，默认情况下这些任务会并发运行。 但是，如果需要以特定顺序运行任务，则必须指定每个任务的具体完成时间，以及哪些任务依赖于其他任务的完成。

1. 若要定义一系列任务按顺序运行，请替换`first`中前面添加的任务*gulpfile.js*以下：

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

    现在有三个任务： `series:first`， `series:second`，和`series`。 `series:second`任务包括第二个参数，以便指定要运行和前完成的任务的数组`series:second`任务将运行。 上面，唯一的代码中指定的那样`series:first`必须在任务完成之前`series:second`任务将运行。

2. 保存*gulpfile.js*。

3. 在中**解决方案资源管理器**，右键单击*gulpfile.js* ，然后选择**Task Runner Explorer**如果尚未打开。

4. 在中**Task Runner Explorer**，右键单击**系列**，然后选择**运行**。

    ![任务运行程序资源管理器运行任务序列](using-gulp/_static/07-TaskRunner-Series.png)

## <a name="intellisense"></a>IntelliSense

IntelliSense 提供了代码完成、 参数说明和其他功能以提高工作效率并减少错误。 Gulp 任务是用 JavaScript 编写的;因此，IntelliSense 可以提供开发时获得帮助。 使用 JavaScript 时，IntelliSense 会列出对象、 函数、 属性和可用的参数根据当前上下文。 从 intellisense 以完成代码提供的弹出列表中选择编码选项。

![gulp IntelliSense](using-gulp/_static/08-IntelliSense.png)

有关 IntelliSense 的详细信息，请参阅[JavaScript IntelliSense](/visualstudio/ide/javascript-intellisense)。

## <a name="development-staging-and-production-environments"></a>开发、 过渡和生产环境

当 Gulp 用于优化过渡和生产客户端的文件时，已处理的文件保存到本地的过渡和生产位置。 *_Layout.cshtml*文件使用**环境**的标记帮助程序提供两个不同版本的 CSS 文件。 CSS 文件的一个版本用于开发，另一个版本适用于过渡和生产。 在 Visual Studio 2017 中，更改时**ASPNETCORE_ENVIRONMENT**环境变量为`Production`，Visual Studio 将生成 Web 应用并链接到最小化的 CSS 文件。 下面的标记演示**环境**标记帮助程序包含将标记与`Development`CSS 文件和缩小`Staging, Production`CSS 文件。

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

## <a name="switching-between-environments"></a>在环境之间切换

若要针对不同的环境编译之间切换，请修改**ASPNETCORE_ENVIRONMENT**环境变量的值。

1. 在**任务运行程序资源管理器**中，验证 **min** 任务是否已设置为在**生成之前**运行。

2. 在中**解决方案资源管理器**，右键单击项目名称并选择**属性**。

    显示 Web 应用的属性表。

3. 单击“调试”选项卡。

4. 设置的值**宿主： 环境**环境变量为`Production`。

5. 按**F5**在浏览器中运行应用程序。

6. 在浏览器窗口中，右键单击页并选择**查看源**若要查看该页面的 HTML。

    请注意，样式表链接指向的缩减的 CSS 文件。

7. 关闭浏览器来停止 Web 应用。

8. 在 Visual Studio 中，返回到 Web 应用的属性表，并更改**宿主： 环境**环境变量回`Development`。

9. 按**F5**再次在浏览器中运行应用程序。

10. 在浏览器窗口中，右键单击页并选择**查看源**若要查看该页面的 HTML。

    请注意，样式表链接指向 CSS 文件的 unminified 版本。

有关 ASP.NET Core 中的环境的详细信息，请参阅[使用多个环境](../fundamentals/environments.md)。

## <a name="task-and-module-details"></a>任务和模块的详细信息

Gulp 任务已注册到函数名称。 如果当前任务之前必须运行其他任务，你可以指定依赖关系。 其他函数，您可以运行和监视的 Gulp 任务，以及将源设置 (*src*) 和目标 (*dest*) 正在修改的文件。 下面是主要的 Gulp API 函数：

|Gulp 函数|语法|描述|
|---   |--- |--- |
|任务  |`gulp.task(name[, deps], fn) { }`|`task`函数创建的任务。 `name`参数定义任务的名称。 `deps`参数包含要完成此任务运行之前任务的数组。 `fn`参数表示执行任务的操作的回调函数。|
|监视 |`gulp.watch(glob [, opts], tasks) { }`|`watch` Function 可监视文件和运行任务发生文件更改时。 `glob`参数是`string`或`array`，它确定要监视哪些文件。 `opts`参数提供了监视选项的其他文件。|
|src   |`gulp.src(globs[, options]) { }`|`src`函数提供了与 glob 值匹配的文件。 `glob`参数是`string`或`array`，它确定哪些文件读取。 `options`参数提供了附加文件选项。|
|dest  |`gulp.dest(path[, options]) { }`|`dest`函数用于定义要向其写入文件的位置。 `path`参数是字符串或函数，用于确定目标文件夹。 `options`参数是一个对象，指定输出文件夹选项。|

有关其他 Gulp API 参考信息，请参阅[Gulp Docs API](https://gulpjs.org/API.html)。

## <a name="gulp-recipes"></a>Gulp 脚本

Gulp 社区提供 Gulp [脚本](https://github.com/gulpjs/gulp/blob/master/docs/recipes/README.md)。 这些脚本包含适用于常见方案的 Gulp 任务。

## <a name="additional-resources"></a>其他资源

* [Gulp 文档](https://github.com/gulpjs/gulp/blob/master/docs/README.md)
* [绑定和缩减中 ASP.NET Core](bundling-and-minification.md)
* [在 ASP.NET Core 中使用 Grunt](using-grunt.md)
