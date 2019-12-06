---
title: 在 ASP.NET Core中使用 Grunt
author: rick-anderson
description: 在 ASP.NET Core中使用 Grunt
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879793"
---
# <a name="use-grunt-in-aspnet-core"></a>在 ASP.NET Core中使用 Grunt

Grunt 是一种 JavaScript 任务运行程序，可自动执行脚本缩减、TypeScript 编译、代码质量 "不起毛" 的工具、CSS 预处理器以及任何需要执行以支持客户端开发的重复工作。 Visual Studio 完全支持 Grunt。

此示例使用一个空的 ASP.NET Core 项目作为起始点，以显示如何从零开始的客户端生成过程中自动运行。

完成的示例清理目标部署目录，合并 JavaScript 文件，检查代码质量，会将 JavaScript 文件内容并将其部署到 web 应用程序的根目录。 我们将使用以下包：

* **grunt**： grunt 任务运行程序包。

* **grunt-contrib**：用于删除文件或目录的插件。

* **grunt-contrib-jshint**：用于检查 JavaScript 代码质量的插件。

* **grunt-contrib**：用于将文件加入单个文件的插件。

* **grunt-contrib-uglify-js**：缩减 JavaScript 以减小大小的插件。

* **grunt-contrib**：监视文件活动的插件。

## <a name="preparing-the-application"></a>准备应用程序

若要开始，请设置新的空 web 应用程序并添加 TypeScript 示例文件。 TypeScript 文件使用默认的 Visual Studio 设置自动编译为 JavaScript，并且将是我们使用 Grunt 处理的原始材料。

1. 在 Visual Studio 中创建一个新 `ASP.NET Web Application`。

2. 在**新建 ASP.NET 项目**对话框中，选择 ASP.NET Core**空**模板，然后单击确定按钮。

3. 在解决方案资源管理器中，查看项目结构。 `\src` 文件夹包含空 `wwwroot` 和 `Dependencies` 节点。

    ![空 web 解决方案](using-grunt/_static/grunt-solution-explorer.png)

4. 将名为 `TypeScript` 的新文件夹添加到项目目录。

5. 在添加任何文件之前，请确保 Visual Studio 已选中 "保存时对 TypeScript 文件进行编译" 选项。 导航到 "**工具**" > **选项**" > **文本编辑器**" > **Typescript** > **项目**：

    ![设置 TypeScript 文件自动编译的选项](using-grunt/_static/typescript-options.png)

6. 右键单击 `TypeScript` 目录，然后从上下文菜单中选择 "**添加 > 新项**"。 选择**JavaScript 文件**项，并将该文件命名为*偏好*（请注意 \*。 将下面的 TypeScript 代码行复制到文件中（保存后，将显示一个新的*偏好*文件，其中包含 JavaScript 源）。

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. 将另一个文件添加到**TypeScript**目录，并将其命名 `Food.ts`。 将下面的代码复制到该文件中。

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

接下来，配置 NPM 以下载 grunt 和 grunt。

1. 在解决方案资源管理器中，右键单击项目，然后从上下文菜单中选择 "**添加 > 新项**"。 选择**NPM 配置文件**项，保留默认名称， *package*，并单击 "**添加**" 按钮。

2. 在*package*文件的 `devDependencies` 对象大括号内，输入 "grunt"。 从 Intellisense 列表中选择 "`grunt`"，并按 Enter 键。 Visual Studio 将引用 grunt 包名称，并添加一个冒号。 在冒号右侧，从 Intellisense 列表顶部选择包的最新稳定版本（如果 Intellisense 未显示，请按 `Ctrl-Space`）。

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM 使用[语义版本控制](https://semver.org/)来组织依赖项。 语义版本控制（也称为 SemVer）通过编号方案 \<主要 > 来识别包。\<次 >。\<修补 >。 Intellisense 只显示几个常见选项，从而简化语义版本控制。 Intellisense 列表（在上面的示例中为0.4.5）中的顶部项被视为包的最新稳定版本。 脱字号（^）符号与最近的主要版本匹配，波形符（~）与最新的次要版本匹配。 请参阅[NPM semver 版本分析器参考](https://www.npmjs.com/package/semver)，了解 semver 提供的完整表现力。

3. 添加更多依赖项，以便为*clean*、 *jshint*、 *concat*、 *uglify-js*和*watch*加载\* grunt 包，如以下示例中所示。 版本不需要与示例匹配。

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

4. 保存*包 json*文件。

每个 `devDependencies` 项的包将随每个包所需的任何文件一起下载。 可以通过启用**解决方案资源管理器**中的 "**显示所有文件**" 按钮，在*node_modules*目录中查找包文件。

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> 如果需要，你可以通过右键单击 `Dependencies\NPM` 并选择 "**还原包**" 菜单选项，在**解决方案资源管理器**中手动还原依赖项。

![还原包](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>配置 Grunt

使用名为*Gruntfile*的清单配置 Grunt，该清单定义、加载和注册可手动运行或配置为基于 Visual Studio 中的事件自动运行的任务。

1. 右键单击该项目，然后选择 "**添加** > **新项**"。 选择**JavaScript 文件**项模板，将名称更改为*Gruntfile*，然后单击 "**添加**" 按钮。

1. 将以下代码添加到*Gruntfile*。 `initConfig` 函数设置每个包的选项，模块的其余部分加载并注册任务。

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. 在 `initConfig` 函数内，为 `clean` 任务添加选项，如下面的示例*Gruntfile*中所示。 `clean` 任务接受目录字符串的数组。 此任务将从*wwwroot/lib*中删除文件，并删除整个 */temp*目录。

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. 在 `initConfig` 函数下，添加对 `grunt.loadNpmTasks`的调用。 这会使任务可从 Visual Studio 中运行。

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. 保存*Gruntfile*。 该文件应类似于下面的屏幕截图。

    ![初始 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. 右键单击*Gruntfile* ，然后从上下文菜单中选择 "**任务运行程序资源管理器**"。 "**任务运行程序资源管理器**" 窗口将打开。

    ![任务运行程序资源管理器菜单](using-grunt/_static/task-runner-explorer-menu.png)

1. 验证 `clean` 显示在**任务运行程序资源管理器**中的 "**任务**" 下。

    ![任务运行程序资源管理器任务列表](using-grunt/_static/task-runner-explorer-tasks.png)

1. 右键单击 "清除" 任务，然后从上下文菜单中选择 "**运行**"。 命令窗口显示任务的进度。

    ![任务运行程序资源管理器运行清理任务](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > 尚无要清理的文件或目录。 如果需要，可以在解决方案资源管理器中手动创建它们，然后将清理任务作为测试来运行。

1. 在 `initConfig` 函数中，使用下面的代码添加 `concat` 的条目。

    `src` 属性数组按应合并的顺序列出要合并的文件。 `dest` 属性指定生成的组合文件的路径。

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > 以上代码中的 `all` 属性是目标的名称。 某些 Grunt 任务中使用目标以允许多个生成环境。 可以使用 IntelliSense 查看内置目标，或自行分配。

1. 使用以下代码添加 `jshint` 任务。

    对于在*temp*目录中找到的每个 JavaScript 文件，jshint `code-quality` 实用程序运行。

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > 选项 "-W069" 是 jshint 在 JavaScript 使用方括号语法分配属性而不是点表示法（即 `Tastes["Sweet"]` 而不是 `Tastes.Sweet`）时生成的错误。 选项关闭警告，以允许其余过程继续进行。

1. 使用以下代码添加 `uglify` 任务。

    任务缩减*combined.js*文件的临时目录中找到并创建 wwwroot/lib 以下标准命名约定中的结果文件 *\<文件名\>。 min.js* .

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. 在对加载 `grunt-contrib-clean``grunt.loadNpmTasks` 的调用下，请使用以下代码包括 jshint、concat 和 uglify-js 的相同调用。

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. 保存*Gruntfile*。 该文件应类似于下面的示例。

    ![完整的 grunt 文件示例](using-grunt/_static/gruntfile-js-complete.png)

1. 请注意，**任务运行程序资源管理器**任务列表包括 `clean`、`concat`、`jshint` 和 `uglify` 任务。 按顺序运行每个任务，并观察**解决方案资源管理器**中的结果。 每个任务都应该运行而不会出错。

    ![任务运行程序资源管理器运行每个任务](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Concat 任务会创建一个新的*组合 .js*文件，并将其放入 temp 目录中。 `jshint` 任务仅运行并不生成输出。 `uglify` 任务创建一个新的*组合*的文件，并将其放入*wwwroot/lib*。 完成后，解决方案应类似于下面的屏幕截图：

    ![所有任务之后的解决方案资源管理器](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > 有关每个包的选项的详细信息，请访问[https://www.npmjs.com/](https://www.npmjs.com/)并在主页上的搜索框中查找包名称。 例如，可以查找 grunt-contrib 包，以获取说明其所有参数的文档链接。

### <a name="all-together-now"></a>综述

使用 Grunt `registerTask()` 方法，按特定顺序运行一系列任务。 例如，若要在 order clean > concat-> jshint-> uglify-js 中运行上述示例步骤，请将以下代码添加到模块。 应将代码添加到与 loadNpmTasks （）调用相同的级别，initConfig 外。

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

新任务显示在 "别名任务" 下的 "任务运行程序资源管理器" 中。 你可以右键单击并运行它，就像执行其他任务一样。 `all` 任务将按顺序运行 `clean`、`concat`、`jshint` 和 `uglify`。

![别名 grunt 任务](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>监视更改

`watch` 任务可随时关注文件和目录。 如果检测到更改，则监视会自动触发任务。 将以下代码添加到 initConfig，以监视对 TypeScript 目录中 \*.js 文件的更改。 如果更改了 JavaScript 文件，`watch` 将运行 `all` 任务。

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

右键单击任务运行程序资源管理器中的 "监视" 任务，然后从上下文菜单中选择 "运行"。 显示运行的监视任务的命令窗口将显示 "正在等待 ..."消息。 打开其中一个 TypeScript 文件，添加一个空格，然后保存该文件。 这会触发监视任务，并触发要按顺序运行的其他任务。 下面的屏幕截图显示了一个示例运行。

![正在运行任务输出](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>绑定到 Visual Studio 事件

除非您希望每次在 Visual Studio 中工作时都手动启动任务，否则将任务**绑定到 "生成"** 、"**生成**"、"**清理**" 和 "**项目打开**" 事件。

绑定 `watch`，使其在每次打开 Visual Studio 时运行。 在任务运行程序资源管理器中，右键单击 "监视" 任务，然后从上下文菜单中选择 "**绑定**" > **项目 "打开**"。

![将任务绑定到项目打开](using-grunt/_static/bindings-project-open.png)

卸载并重新加载项目。 再次加载项目时，"监视" 任务会自动开始运行。

## <a name="summary"></a>总结

Grunt 是一个功能强大的任务运行程序，可用于自动执行大多数客户端生成任务。 Grunt 利用 NPM 提供其包和功能工具与 Visual Studio 的集成。 Visual Studio 的任务运行程序资源管理器检测到对配置文件所做的更改，并提供用于运行任务、查看运行任务和将任务绑定到 Visual Studio 事件的便利界面。
