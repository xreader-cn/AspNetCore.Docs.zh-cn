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
# <a name="use-grunt-in-aspnet-core"></a>在 ASP.NET Core中使用 Grunt

通过[Noel 大米](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)

Grunt 是 JavaScript 任务运行程序，可以自动脚本缩小，TypeScript 编译、 代码质量"lint"工具，CSS 预处理器和几乎任何需要采取措施来支持客户端开发的重复性的任务。 尽管 ASP.NET 项目模板使用 Gulp，默认情况下 Visual Studio 中，完全支持 grunt (请参阅[使用 Gulp](using-gulp.md))。

此示例使用一个空的 ASP.NET Core 项目作为起始点，以显示如何从零开始的客户端生成过程中自动运行。

已完成的示例可清除目标部署目录、 组合 JavaScript 文件、 检查代码质量，会将 JavaScript 文件内容和将部署到 web 应用程序的根目录。 我们将使用以下包：

* **grunt**:Grunt 任务运行程序包。

* **grunt contrib 清理**:删除文件或目录的插件。

* **grunt contrib jshint**:查看 JavaScript 代码质量插件。

* **grunt contrib concat**:将文件合并为单个文件的插件。

* **grunt contrib 丑化**:缩减 JavaScript 以减小大小的插件。

* **grunt-contrib-watch**:监视文件活动插件。

## <a name="preparing-the-application"></a>准备应用程序

若要开始，设置新的空 web 应用程序并添加 TypeScript 示例文件。 TypeScript 文件自动编译到 JavaScript 中使用默认的 Visual Studio 设置，并且将是我们要处理使用 Grunt 的原始材料。

1. 在 Visual Studio 中，创建一个新`ASP.NET Web Application`。

2. 在**新建 ASP.NET 项目**对话框中，选择 ASP.NET Core**空**模板，然后单击确定按钮。

3. 在解决方案资源管理器，查看项目结构。 `\src`文件夹包含空`wwwroot`和`Dependencies`节点。

    ![空的 web 解决方案](using-grunt/_static/grunt-solution-explorer.png)

4. 添加名为的新文件夹`TypeScript`为项目目录。

5. 之前添加的任何文件，请确保 Visual Studio 可以选择编译保存 ' 检查 TypeScript 文件。 导航到**工具** > **选项** > **文本编辑器** > **Typescript**  > **项目**:

    ![设置自动编译 TypeScript 文件的选项](using-grunt/_static/typescript-options.png)

6. 右键单击`TypeScript`目录，然后选择**添加 > 新建项**从上下文菜单。 选择**JavaScript 文件**项，然后将文件命名*Tastes.ts* (请注意\*.ts 扩展)。 下面的 TypeScript 代码的行复制到的文件 (保存时，一个新*Tastes.js*文件将显示与 JavaScript 源)。

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. 添加到第二个文件**TypeScript**目录并将其命名`Food.ts`。 将以下代码复制到该文件。

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

## <a name="configuring-npm"></a>配置 NPM

接下来，配置 NPM 以下载 grunt 和 grunt 任务。

1. 在解决方案资源管理器，右键单击该项目并选择**添加 > 新建项**从上下文菜单。 选择**NPM 配置文件**项，请保留默认名称*package.json*，然后单击**添加**按钮。

2. 在中*package.json*文件内,`devDependencies`对象大括号中，输入"grunt"。 选择`grunt`在 Intellisense 中列出，然后按 Enter 键。 Visual Studio 将用引号括起来 grunt 包名称，并添加一个冒号。 从智能感知列表的顶部中的冒号右侧，选择包的最新稳定版本 (按`Ctrl-Space`如果不会显示 Intellisense)。

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > 使用 NPM[语义化版本控制](http://semver.org/)来组织依赖项。 语义版本控制，也称为 SemVer，标识包具有单独的编号方案\<主要 >。\<次要 >。\<修补程序 >。 Intellisense 显示仅几个常用的选项，从而简化了语义化版本控制。 智能感知列表 (在上面的示例 0.4.5) 中的顶级项被视为包的最新稳定版本。 插入符号 (^) 符号匹配的最新的主版本和颚化符 （~） 与最新次要版本匹配。 请参阅[NPM semver 版本分析器引用](https://www.npmjs.com/package/semver)作为 SemVer 提供的完整表现力的指南。

3. 添加多个依赖项加载 grunt 的 contrib-\*打包以*干净*， *jshint*， *concat*，*丑化*，以及*监视*如下面的示例中所示。 版本不需要与示例匹配。

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

4. 保存*package.json*文件。

将下载的每一 devDependencies 项包，以及每个包所需的任何文件。 您可以找到包文件中的`node_modules`目录，从而**显示所有文件**在解决方案资源管理器中的按钮。

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> 如果需要可以通过右键单击手动还原解决方案资源管理器中的依赖关系`Dependencies\NPM`，然后选择**还原包**菜单选项。

![还原包](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Grunt 配置

使用名为的清单配置 grunt *Gruntfile.js*的定义、 加载和注册任务，可以手动运行或配置为自动基于运行 Visual Studio 中的事件。

1. 右键单击项目并选择**添加 > 新建项**。 选择**Grunt 配置文件**选项，请保留默认名称*Gruntfile.js*，然后单击**添加**按钮。

   初始代码包含模块定义和`grunt.initConfig()`方法。 `initConfig()`用于为每个包，设置选项和模块的剩余部分将加载和注册任务。

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

2. 内部`initConfig()`方法中，添加用于`clean`任务中的示例所示*Gruntfile.js*下面。 清理任务接受目录字符串的数组。 此任务从 wwwroot/lib 删除文件，并删除整个/临时目录。

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. 下面 initConfig() 方法中，添加对的调用`grunt.loadNpmTasks()`。 这将从 Visual Studio 来使该任务可运行。

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. 保存*Gruntfile.js*。 该文件应类似于下面的屏幕截图。

    ![初始 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

5. 右键单击*Gruntfile.js* ，然后选择**Task Runner Explorer**从上下文菜单。 任务运行程序资源管理器窗口将打开。

    ![任务运行程序资源管理器菜单](using-grunt/_static/task-runner-explorer-menu.png)

6. 确认`clean`显示在下面**任务**在任务运行程序资源管理器。

    ![任务运行程序资源管理器任务列表](using-grunt/_static/task-runner-explorer-tasks.png)

7. 右键单击 clean 任务，然后选择**运行**从上下文菜单。 命令窗口中显示任务的进度。

    ![任务运行程序资源管理器中运行清理任务](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > 不有任何文件或目录尚未清理。 如果你愿意，您可以在解决方案资源管理器中手动创建，并为测试运行 clean 任务。

8. 在 initConfig() 方法中，添加一个条目`concat`使用下面的代码。

    `src`属性数组列出要结合起来，应组合它们的顺序文件。 `dest`属性分配到合并生成的文件的路径。

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > `all`上面的代码中的属性为目标的名称。 在某些 Grunt 任务中使用目标以允许多个生成环境。 可以查看使用 Intellisense 的内置目标或分配你自己。

9. 添加`jshint`任务使用下面的代码。

    针对临时目录中找到的每个 JavaScript 文件运行 jshint 代码质量实用程序。

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > 选项"-W069"错误 jshint 时生成的 JavaScript 使用方括号语法，即分配属性而不是点表示法`Tastes["Sweet"]`而不是`Tastes.Sweet`。 选项将关闭该警告，以允许进程继续的其余部分。

10. 添加`uglify`任务使用下面的代码。

    任务缩减*combined.js*文件的临时目录中找到并创建 wwwroot/lib 以下标准命名约定中的结果文件*\<文件名\>。 min.js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

11. 在加载 grunt contrib 清理调用 grunt.loadNpmTasks()，包括相同的调用，用于 jshint，concat 和丑化使用下面的代码。

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. 保存*Gruntfile.js*。 该文件应类似于下面的示例。

    ![完整 grunt 文件示例](using-grunt/_static/gruntfile-js-complete.png)

13. 请注意，任务运行程序资源管理器任务列表包含`clean`， `concat`，`jshint`和`uglify`任务。 按顺序运行每个任务并观察在解决方案资源管理器中的结果。 每个任务应该正常运行。

    ![任务运行程序资源管理器运行每个任务](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Concat 任务创建一个新*combined.js*文件并将其放置到临时目录。 只需运行 jshint 任务并不会生成输出。 Uglify 任务创建一个新*combined.min.js*文件，并将其放到 wwwroot/lib。 完成后，解决方案应类似于下面的屏幕截图：

    ![毕竟，解决方案资源管理器任务](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > 对于每个包的选项的详细信息，请访问[ https://www.npmjs.com/ ](https://www.npmjs.com/)和查找在主页上的搜索框中的包名称。 例如，可以查找 grunt contrib 清理包以获取说明的所有参数的文档链接。

### <a name="all-together-now"></a>即时汇总

使用 Grunt`registerTask()`方法以按特定顺序运行的一系列任务。 例如，若要运行示例上述步骤中清理的顺序-> concat-> jshint-> 丑化，将以下代码添加到该模块。 应将代码添加到作为外部 initConfig loadNpmTasks() 调用相同的级别。

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

新任务将显示在任务运行程序资源管理器的别名的任务。 您可以右键单击并像其他任务一样运行它。 `all`任务将运行`clean`， `concat`，`jshint`和`uglify`，按顺序。

![别名 grunt 任务](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>监视更改

一个`watch`任务会监控文件和目录。 监视检测到更改时自动触发任务。 将以下代码添加到 initConfig 若要监视更改到\*TypeScript 目录中的.js 文件。 如果更改 JavaScript 文件，请`watch`将运行`all`任务。

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

添加对的调用`loadNpmTasks()`以显示`watch`任务运行程序资源管理器中的任务。

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

右键单击任务运行程序资源管理器中的监视任务，并从上下文菜单中选择运行。 显示运行监视任务的命令窗口将显示"正在等待..."消息。 打开 TypeScript 文件之一，添加一个空格，然后保存该文件。 这将触发监视任务并触发要按顺序运行的其他任务。 下面的屏幕截图显示了示例运行。

![运行任务输出](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>绑定到 Visual Studio 事件

除非你想要手动启动你的任务，每次在 Visual Studio 中工作时，可以将绑定到的任务**生成前**，**后生成**，**清理**，和**项目打开**事件。

接下来将绑定`watch`以便它在每次 Visual Studio 打开时运行。 在任务运行程序资源管理器中，右键单击监视任务并选择**绑定 > 项目 Open**从上下文菜单。

![将任务绑定到项目开始](using-grunt/_static/bindings-project-open.png)

卸载并重新加载项目。 当再次加载该项目时，监视任务将启动自动运行。

## <a name="summary"></a>总结

Grunt 是可用于自动执行大多数客户端生成任务的功能强大的任务运行程序。 Grunt 利用 NPM 来提供其包和工具与 Visual Studio 集成的功能。 Visual Studio 的任务运行程序资源管理器检测到配置文件的更改，并提供了一个方便的界面，用于运行任务，查看正在运行的任务，并将任务绑定到 Visual Studio 事件。

## <a name="additional-resources"></a>其他资源

* [使用 Gulp](using-gulp.md)
