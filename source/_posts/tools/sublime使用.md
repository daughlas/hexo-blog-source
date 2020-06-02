---
title: sublime 使用
date: 
categories:
- 工具
tags:
- 工具
---

1. [ctrl + p]是 go to anything
	a. [/]之后直接跟地址
	b. [:]之后接行号
	c. [@]之后可以接选择器或者函数
2. 多行游标功能：利用光标对他们进行整体修改。也可以选择“：{”修改的内容。
	a. 光标移动到一个单词上，按快捷键 [Ctrl + D]，会选中当前单词，再按一次选中下一个单词，可以一直重复操作。[Ctrl + K]跳过当前选择，再按[Ctrl + D]，选择下一个。
	b. 也可以使用[Ctrl]，然后用鼠标选择。
	c. 选中第一个之后按快捷键 [Alt+ F3] 全选内容。
	d. 选中之后按[Ctrl + Shift + L]，进行拆行。
	e. 选择一个位置，然后按住[Shift]键，再拖动鼠标右键。
3. Ctrl + H 能够实现替换
4. 命令模式
	a. [ctrl + shift + P]启动命令模式
	b. set syntax javascript，切换语法模式，支持模糊匹配，js就可以表示javascript
	c. 可以减少记忆快捷键和使用鼠标
5. 综合技巧演示
	a. ctrl + N 新建工作区
	b. ctrl + shift + P，打开命令模式
	c. 输入ss html，通过模糊匹配，确定语法
	d. [edit - tag - closetag]，闭合标记，快捷键为Alt + .
	e. 输入感叹号，快捷键[ctrl + E]，直接输出一大段html代码（需要插件）
	f. 输入[ctrl + P]，go to anything，在页面中搜索用#开头，输入#body，按一下回车，然后光标移动到body，再按[ctrl + Enter]，在下面添加一行
	e. 生成无序列表ul>.item$*10，"."代表class，"$"代表序号，再按ctrl + e
	f. 选中一块代码，ctrl + ]，就是增加缩进
	g. 鼠标选中"<>"标记，按住[alt + F3]全选，移动方向键，按回车，输入h2{this is a tittle}，大括号在插件中的意思是输出内容，光标移动到行末尾，按[ctrl + E]
	h. [ctrl + shift +enter]，可以在上一行插入代码
	i. [ctrl + shift + v]，在粘贴的同时保留了缩进
6. sublime的高级特性：
	a. 安装package control 插件
	b. ctrl + shift + P，搜索pci，theme进行
	c. snippets 以模板的方式进行
	d. 使用插件 Javascript & NodeJS Snippets,输入gi就会出现document.getElementById('id');用好tab键；更多功能上https://packagecontrol.io/上面输入插件名称，在里边找
	e. 安装JQuery
	f. PersonModule.findOne({name:'test'},function(err,result){
		console.log(result);
	});安装插件Insert Callback 之后，可以用快捷键[alt +c]实现这个功能。
	g. advanceNewfile插件，用[ctrl + alt + N]，可以建了之后直接输入名称，或者用……/……直接在指定目录下创建
	h. 测试get和post请求，用httpRequest,快捷键[Ctrl +alt + R]
	i. nettus fetch，写前端程序，下载类库和更新类库，命令模式，fetch manage管理，可以下载和管理underscore
	j. sidebarenhancement 增强sidebar
	k. 快速添加注释，docblocker，/*或者/**之后按回车，在函数的上方输入/**，之后按住tab键就会自动生成注释
	l. 使用lint进行语法及风格校验，先安装sublimeLinter，之后NodeJS，然后`cnpm install jshint`,配置 jshint, 在项目文件夹下创建 .jshintrc 文件，配置三个等号在文件中写如下内容，在 jshint.com 中有所有可配置信息的所有说明
```
{
	"eqeqeq": true;
}
```

