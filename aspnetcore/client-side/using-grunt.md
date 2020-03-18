---
title: 在 ASP.NET Core 中使用 Grunt
author: rick-anderson
description: 在 ASP.NET Core 中使用 Grunt
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646422"
---
# <a name="use-grunt-in-aspnet-core"></a>在 ASP.NET Core 中使用 Grunt

Grunt 是一种 JavaScript 任务运行程序，可自动执行脚本缩减、TypeScript 编译、代码质量“lint”工具、CSS 预处理器以及支持客户端开发所需的所有重复性工作。 Visual Studio 完全支持 Grunt。

此示例使用空的 ASP.NET Core 项目作为其起始点，演示如何从头开始自动执行客户端生成过程。

完成的示例可清理目标部署目录、合并 JavaScript 文件和检查代码质量，将浓缩后的 JavaScript 文件内容部署至 Web 应用程序的根目录。 我们将使用以下包：

* **grunt**：Grunt 任务运行程序包。

* **grunt-contrib-clean**：用于删除文件或目录的插件。

* **grunt-contrib-jshint**：用于检查 JavaScript 代码质量的插件。

* **grunt-contrib-concat**：用于将多个文件联接为单个文件的插件。

* **grunt-contrib-uglify**：缩减 JavaScript 以减小尺寸的插件。

* **grunt-contrib-watch**：用于监视文件活动的插件。

## <a name="preparing-the-application"></a>准备应用程序

若要开始，请设置一个新的空 Web 应用程序并添加 TypeScript 示例文件。 TypeScript 文件采用默认的 Visual Studio 设置自动编译为 JavaScript，并且将是 Grunt 处理的原始材料。

1. 在 Visual Studio 中，新建一个 `ASP.NET Web Application`。

2. 在“新建 ASP.NET 项目”对话框中，选择 ASP.NET Core“空”模板，然后单击“确定”按钮   。

3. 在“解决方案资源管理器”中，查看项目结构。 `\src` 文件夹包含空的 `wwwroot` 和 `Dependencies` 节点。

    ![空 Web 解决方案](using-grunt/_static/grunt-solution-explorer.png)

4. 将名为 `TypeScript` 的新文件夹添加到项目目录中。

5. 在添加任何文件之前，请确保 Visual Studio 已为 TypeScript 文件勾选“在保存时编译”选项。 导航至“工具” > “选项” > “文本编辑器” > “Typescript” > “项目”      ：

    ![设置 TypeScript 文件自动编译的选项](using-grunt/_static/typescript-options.png)

6. 右键单击 `TypeScript` 目录，然后从上下文菜单中选择“添加”>“新建项”  。 选择“JavaScript 文件”项，并将文件命名为“Tastes.ts”（注意 \*.ts 扩展名   ）。 将下面这行 TypeScript 代码复制到文件中，保存后，将显示一个新的 Tastes.js 文件，其中包含 JavaScript 源  。

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. 将第二个文件添加到 TypeScript 目录，并将其命名为 `Food.ts`  。 将下面的代码复制到该文件中。

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

1. 在“解决方案资源管理器”中右键单击项目，并从上下文菜单中选择“添加”>“新建项”  。 选择“NPM 配置文件”项，保留默认名称 (package.json) 并单击“添加”按钮    。

2. 在 package.json 文件中的 `devDependencies` 对象大括号内输入“grunt”  。 从 Intellisense 列表中选择 `grunt` 并按下 Enter 键。 Visual Studio 将引用 grunt 包名称，并添加一个冒号。 在冒号右侧，从 Intellisense 列表顶部选择包的最新稳定版本（如果 Intellisense 列表未显示，请按 `Ctrl-Space`）。

    ![Grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM 使用[语义化版本控制](https://semver.org/)来组织依赖项。 语义化版本控制（也称为 SemVer）使用编号方案 \<major>.\<minor>.\<patch> 识别包。 Intellisense 只显示几个常见选项，从而简化语义化版本控制。 Intellisense 列表的第一项（在上面的示例中为 0.4.5）被视为程序包的最新稳定版本。 脱字号 (^) 匹配最新的主版本，波形符 (~) 匹配最新的次版本。 有关 SemVer 提供的完整表达能力，请参阅 [NPM semver 版本分析程序参考](https://www.npmjs.com/package/semver)。

3. 添加更多依赖项以加载 clean、jshint、concat、uglify 和 watch 的 grunt-contrib-\* 包，如以下示例中所示      。 版本不需要与示例匹配。

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

4. 保存 package.json 文件  。

每个 `devDependencies` 项的包将随每个包需要的所有文件一起下载。 在解决方案资源管理器中启用“显示所有文件”按钮，可以找到 node_modules 目录中的包文件    。

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> 若有需要，可以在解决方案资源管理器中手动还原依赖项，方法是右键单击 `Dependencies\NPM` 并选择“还原包”菜单选项   。

![还原包](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>配置 Grunt

使用名为 Gruntfile.js 的清单配置 Grunt，该清单定义、加载和注册了多个任务，这些任务可手动运行或配置为基于 Visual Studio 中的事件自动运行  。

1. 右键单击该项目，选择“添加” > “新建项”   。 选择“JavaScript 文件”项模板，将名称更改为 Gruntfile.js，然后单击“添加”按钮    。

1. 将以下代码添加到 Gruntfile.js  。 `initConfig` 函数设置每个包的选项，模块的其余部分加载并注册任务。

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. 在 `initConfig` 函数中，添加 `clean` 任务的选项，如下面的示例 Gruntfile.js 中所示  。 `clean` 任务接受目录字符串的数组。 此任务从 wwwroot/lib 删除文件，并删除整个 /temp 目录   。

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. 在 `initConfig` 函数下，添加对 `grunt.loadNpmTasks` 的调用。 这使得任务可从 Visual Studio 中运行。

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. 保存 Gruntfile.js  。 该文件应类似于下面的屏幕截图所示。

    ![初始 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. 右键单击 Gruntfile.js 并从上下文菜单中选择“任务运行程序资源管理器”   。 随即打开任务运行程序浏览器窗口  。

    ![任务运行程序资源管理器菜单](using-grunt/_static/task-runner-explorer-menu.png)

1. 验证任务运行程序资源管理器中的“任务”下是否显示 `clean`   。

    ![任务运行程序资源管理器任务列表](using-grunt/_static/task-runner-explorer-tasks.png)

1. 右键单击清理任务并在上下文菜单中选择“运行”  。 显示任务进度的命令窗口。

    ![任务运行程序资源管理器运行清理任务](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > 尚无需要清理的文件或目录。 如果需要，可以在解决方案资源管理器中手动创建文件或目录，然后运行清理任务来进行测试。

1. 在 `initConfig` 函数中，使用下面的代码添加 `concat` 的条目。

    `src` 属性数组按合并顺序列出要合并的文件。 `dest` 属性指定生成的合并文件的路径。

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > 以上代码中的 `all` 属性是目标的名称。 某些 Grunt 任务会使用目标以允许多个生成环境。 可以使用 IntelliSense 查看内置目标，或自行分配。

1. 使用以下代码添加 `jshint` 任务。

    系统会对在 temp 目录中找到的每个 JavaScript 文件运行 jshint `code-quality` 实用工具  。

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > 当 JavaScript 使用括号语法分配属性而不是点标识符，也就是采用 `Tastes["Sweet"]` 而不是 `Tastes.Sweet`，jshint 会生成一个错误选项“-W069”。 该选项关闭警告，以允许其余过程继续进行。

1. 使用以下代码添加 `uglify` 任务。

    该任务缩减在 temp 目录中找到 combined.js 文件，并按照标准命名约定 \<file name\>.min.js 在 wwwroot/lib 中创建结果文件   。

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. 在对 `grunt.loadNpmTasks` 发起的加载 `grunt-contrib-clean` 的调用下，使用以下代码包括 jshint、concat 和 uglify 的相同调用。

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. 保存 Gruntfile.js  。 该文件应类似于以下示例所示。

    ![完成 grunt 文件示例](using-grunt/_static/gruntfile-js-complete.png)

1. 请注意，任务运行程序资源管理器的“任务”列表包括 `clean`、`concat`、`jshint` 和 `uglify` 任务  。 按顺序运行每个任务，并在解决方案资源管理器中观察结果  。 每个任务都应正常运行，不会出错。

    ![任务运行程序资源管理器运行每个任务](using-grunt/_static/task-runner-explorer-run-each-task.png)

    concat 任务会创建一个新的 combined.js 文件，并将其放入 temp 目录  。 `jshint` 任务仅运行，并不生成输出。 `uglify` 任务创建新的 combined.min.js 文件，并将其放入 wwwroot/lib 中   。 完成后，解决方案应类似于下面的屏幕截图所示：

    ![完成所有任务之后的解决方案资源管理器](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > 若要详细了解每个包的选项，请访问 [https://www.npmjs.com/](https://www.npmjs.com/) 并在主页上的搜索框中搜索包名称。 例如，可以查找 grunt-contrib-clean 包，以获取介绍其所有参数的文档链接。

### <a name="all-together-now"></a>现在将所有任务一起运行

使用 Grunt `registerTask()` 方法，按特定顺序运行一系列任务。 例如，若要按 clean -> concat -> jshint -> uglify 的顺序中运行上述示例步骤，请将以下代码添加到模块。 请将代码添加到与 loadNpmTasks() 调用相同的级别，在 initConfig 之外。

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

在任务运行程序资源管理器中，新任务显示在“别名任务”下。 可以右键单击并运行它，就像执行其他任务一样。 `all` 任务将按顺序运行 `clean`、`concat`、`jshint` 和 `uglify`。

![别名 grunt 任务](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>监视更改

`watch` 任务用于监视文件和目录。 如果检测到更改，监视会自动触发任务。 将以下代码添加到 initConfig，以监视对 TypeScript 目录中 \*.js 文件的更改。 如果 JavaScript 文件发生了更改，`watch` 将运行 `all` 任务。

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

添加对 `loadNpmTasks()` 的调用，以在任务运行程序资源管理器中显示 `watch` 任务。

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

右键单击任务运行程序资源管理器中的监视任务，然后从上下文菜单中选择“运行”。 显示正在运行的监视任务的命令窗口将显示一条“正在等待…”消息。 打开其中一个 TypeScript 文件，添加一个空格，然后保存该文件。 这会触发监视任务，并触发要按顺序运行的其他任务。 下面的屏幕截图展示的是一个示例运行。

![正在运行的任务输出](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>绑定到 Visual Studio 事件

请将任务绑定到“生成前”、“生成后”、“清理”和“项目打开”事件，除非想在每次在 Visual Studio 中工作时都手动启动任务     。

绑定 `watch`，使其在每次打开 Visual Studio 时运行。 右键单击任务运行程序资源管理器中的监视任务，然后在上下文菜单中选择“绑定” > “项目打开”   。

![将任务绑定到项目开头](using-grunt/_static/bindings-project-open.png)

卸载并重载项目。 再次加载项目时，监视任务会自动开始运行。

## <a name="summary"></a>总结

Grunt 是一个功能强大的任务运行程序，可用于自动执行大多数客户端生成任务。 Grunt 利用 NPM 提供它的包，特点是能与 Visual Studio 进行工具集成。 Visual Studio 的任务运行程序资源管理器检测对配置文件所做的更改，并提供便捷的界面，用于运行任务、查看运行中的任务以及将任务绑定到 Visual Studio 事件。
