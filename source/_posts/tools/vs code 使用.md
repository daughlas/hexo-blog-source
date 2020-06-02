---
title: VS code 使用
date: 
categories:
- 工具
tags:
- 工具
---

# VSCode 使用指南

### 快速导航

可以使用 Command + P 调出类似 sublime text 的 go to everywhere



### 编辑功能

* 多光标选取 `Command + 鼠标单击`

* 逐步选中其他相同的 symbol，`Command + D`

* 代码格式化 `Shift + Alt + F`

* 重构（重命名）Symbol `F2`

* 查找所有引用 Symbol 的代码 `Shift + F12`

* 跳转到 symbol 的定义处`F12`，也可以在 symbol 下面展开一个面板，显示定义处的代码 `Alt + F12`
* 如果你是指格式化，快捷键是：shift+alt+f



### 其他

* `Command + P` ，? 查询所有命令，> 找到所有命令，：跳转到指定行··
* `Command +鼠标左键`



## 从别的地方借鉴来的

#### 外观增强



1. 配色[Solarized Dark](https://link.zhihu.com/?target=http%3A//ethanschoonover.com/solarized)

2. 字体[Fira Code](https://link.zhihu.com/?target=https%3A//github.com/tonsky/FiraCode/wiki/VS-Code-Instructions)

   ```
   {
     "editor.cursorStyle": "block",
     "editor.fontFamily": "Fira Code",
     "editor.fontLigatures": true,
     "editor.fontSize": 16,
     "editor.lineHeight": 24,
     "editor.lineNumbers": "on",
     "editor.minimap.enabled": false,
     "editor.renderIndentGuides": false,
     "editor.rulers": [120],
     "workbench.colorTheme": "Solarized Dark",
     "workbench.iconTheme": "vscode-great-icons"
   } 
   ```

   

3. Guides 缩进参考，与内置的缩进参考线不同，Guides 能够让你通过配置项来修改参考线的颜色、样式、缩进空白的背景色等，如果你愿意折腾，甚至能够配置出类似 Indent Rainbow 那样风格的参考线。下图是我使用 Solarized Dark 主题时参考线的配置：

   ```
   {
       "guides.normal.color.dark": "rgba(91, 91, 91, 0.6)",
       "guides.normal.color.light": "rgba(220, 220, 220, 0.7)",
       "guides.active.color.dark": "rgba(210, 110, 210, 0.6)",
       "guides.active.color.light": "rgba(200, 100, 100, 0.7)",
       "guides.active.style": "dashed",
       "guides.normal.style": "dashed",
       "guides.stack.style": "dashed",
   }
   ```

4. TODO Highlight TODO Highlight 能够帮我们把这些关键词高亮出来



#### 风格检查和插件

1. ESlint

2. EditorConfig

   ```
   [*]
   end_of_line = lf
   charset = utf-8
   trim_trailing_whitespace = false
   insert_final_newline = true
   indent_style = space
   indent_size = 2
   
   [{*.yml,*.json}]
   indent_style = space
   indent_size = 2 
   ```

3. [Prettier ](https://link.zhihu.com/?target=https%3A//marketplace.visualstudio.com/items%3FitemName%3Desbenp.prettier-vscode) 格式化代码的工具

#### 智能建议、代码片段、自动补全和插件

```
{
  "editor.quickSuggestions": {
    "other": true,
    "comments": true,
    "strings": true
  },
} 
```

1. 代码补全：

   1. [Javascript (ES6) Code Snippets](https://link.zhihu.com/?target=https%3A//marketplace.visualstudio.com/items%3FitemName%3Dxabikos.JavaScriptSnippets) 
   2. [Javascript Patterns Snippets](https://link.zhihu.com/?target=https%3A//marketplace.visualstudio.com/items%3FitemName%3Dnikhilkumar80.js-patterns-snippets)

2. 自动补全

   1. [Path Intellisense](https://link.zhihu.com/?target=https%3A//marketplace.visualstudio.com/items%3FitemName%3Dchristian-kohler.path-intellisense)，文件路径补全
   2. [IntelliSense for CSS class names](https://link.zhihu.com/?target=https%3A//marketplace.visualstudio.com/items%3FitemName%3DZignd.html-css-class-completion) CSS 类名补全，会自动扫描整个项目里面的 CSS 类名并在你输入类名时做智能提示
   3. [Emmet](https://link.zhihu.com/?target=https%3A//emmet.io/)，以前叫做 Zen Coding，我发现后，也是爱不释手，可以把类 CSS 选择符的字符串展开成 HTML 标签，VSCode 已经内置，官方介绍文档[参见](https://link.zhihu.com/?target=https%3A//code.visualstudio.com/docs/editor/emmet)

3. 功能增强

   1. [Color Highlight](https://link.zhihu.com/?target=https%3A//marketplace.visualstudio.com/items%3FitemName%3Dnaumovs.color-highlight)，识别代码中的颜色，包括各种颜色格式；
   2. [Bracket Pair Colorizer](https://link.zhihu.com/?target=https%3A//marketplace.visualstudio.com/items%3FitemName%3DCoenraadS.bracket-pair-colorizer)，识别代码中的各种括号，并且标记上不同的颜色
   3. [Project Manager](https://link.zhihu.com/?target=https%3A//marketplace.visualstudio.com/items%3FitemName%3Dalefragnani.project-manager)，项目管理，让我们方便的在命令面板中切换项目文件夹
   4. Git Lens 把 VSCode 结合 Git 的使用体验优化到了极致
   5. Code Outline 能在单独窗口中列出当源代码中的各种符号
   6. Code Spell Checker 强烈推荐
   7. Code Runner，名副其实的代码运行插件，支持数十种语言，在不离开代码编辑器的前提下通过命令面板可直接执行代码
   8. Open-In-Browser

   

#### 其他插件

1. Faker  能够帮你快速的插入用例数据。Faker 可以随机生成姓名、地址、图像、电话号码，或者经典的乱数假文段落，并且每个类别还包含了各种子类别，你可以根据自身的需求来使用这些数据。
2. Icon Fonts 这是一个能够在项目中添加图标字体的插件。该插件支持超过 20 个热门的图标集，包括了 Font Awesome、Ionicons、Glyphicons 和 Material Design Icons。

#### 总结

提高效率有没有法门？是有的，简单的事情重复化，重复的事情标准化，标准的事情自动化，发现一个痛点，用插件解决一个痛点，你的效率自然就上来了。

