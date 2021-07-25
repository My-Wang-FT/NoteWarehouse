# VS code 使用教程

[Visual Studio Code 官方教程](https://code.visualstudio.com/docs)

## 快捷键

`Ctrl+Shift+G`：切换到Git分支管理
`Ctrl+Shift+I`：整个文档格式化
`Ctrl+Shift+M`：快速跳入项目中的错误和警告
`Ctrl+Shift+P` `F1`：命令面板
`Ctrl+Shift+V`：打开Markdown预览
`Ctrl+Shift+X`：扩展
`F8` `Shift+F8`：循环浏览错误
`Ctrl+B`：缩放侧边栏
`Ctrl+G`：导航到特定行
`Ctrl+J`：缩放下方面板
`Ctrl+L`：选择当前行
`Ctrl+P`：快速打开文件
`Ctrl+R`：快速切换工作区
`Ctrl+U`：撤销到上一个光标位置
`Ctrl+W`：关闭活动的编辑器
`Ctrl+,`：打开用户设置
`Ctrl+·`：综合终端
`Ctrl+\`：横排分屏编辑
`Ctrl+1`：切换编辑器组
`Ctrl+PageUp` `Ctrl+PageDown`：切换编辑器
`Alt+UP` `Alt+DOWN`：上下移动一整行

更多键盘快捷键的配置可以添加到`keybindings.json`文件中
更多用户设置可以添加到文件`settings.json`中
工作区特定文件位于根文件夹`.vscode`中。如`tasks.json`对应 Task Runner 、`launch.json`对应调试器。

## 技巧窍门

[More Tips](https://code.visualstudio.com/docs/getstarted/tips-and-tricks)

### 多光标选择

要在任意位置添加光标，请使用鼠标选择一个位置并使用Alt+单击
要在当前位置上方或下方设置光标，请使用：
键盘快捷键：`Shift+Alt+Up`或`Shift+Alt+Down`

### 列（框）选择

可以在拖动鼠标时按住`Shift+Alt`来选择文本块。将在每个选定行的末尾添加一个单独的光标。

### 快速滚动

按`Alt`键可以在编辑器和资源管理器中快速滚动。默认情况下，快速滚动使用`5X`速度倍增器

### 转换文本命令

可以在命令面板中使用`transform`将选定文本转换为大写，小写或者标题大小写

### Git分支管理

从源代码管理视图中，选择一个文件以打开差异
差异的默认视图是并排视图。
切换内嵌视图点击更多操作右上角（...）按钮，然后选择切换内联视图。
使用`F7`和`Shift+F7`浏览差异。这将以统一的补丁格式呈现它们。可以使用箭头键导航行，按`Enter`将跳回差异编辑器和所选行。
点击左下角状态栏在Git分支中间进行切换
在源代码管理中点击文件的加减号暂存或者取消暂存
点击`√`将暂存文件进行提交，点击`···`进行更多操作


## 关于插件

[插件推荐](https://cloud.tencent.com/developer/article/1796162)

