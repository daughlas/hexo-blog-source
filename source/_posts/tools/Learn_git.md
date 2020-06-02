---
title: git 学习
date: 
categories:
- 工具
tags:
- 工具
---

1. 输入git 或者 git help 会显示出常用的命令和解释
2. git help -a 显示所有命令
3. git help -g 显示手册，继续输入标题，进入详细说明，例如git help add，F 键乡下翻页，B 键向上翻页，q 推出介绍
4. 配置 git
	1. system设置：所有使用这台电脑的人
	2. global设置： 全局，用户设置
		1. `git config --global user.name "吕嘉文"`，设置名字
		2. `git config --list` ,查看配置
		3. `git config --unset --global user.name`，删除掉 user.name 这个配置
		4. 其他可以配置的`git config --global user.email/color.ui(true)/ "设置项"`
	3. project设置： 针对项目进行设置
5. 初始化一个项目
	1. 命令init ，是initialize 的简写
	2. 在项目的目录下面执行`git init`
	3. 不想 git 跟踪项目的话，删除 .git 这个文件就行了 
6. 提交
	1. 修改了文件之后，添加一条修改描述的信息
	2. 在工作目录下输入 `git status`，会显示当前所在的分支，
	3. initial commit 表示一个初始的提交
	4. nothng to commit 目前没有变化需要提交
	5. 新建文件之后，输入 `git status`，这些文件会显示为untracked files，输入`git add .` 跟踪所有文件，或者 `git add 文件名`，跟踪指定的文件。
	6. 跟踪的文件有变化的话，输入git status 会提示changes to be commit 将要提交的修改
	7. `git commit 描述信息` -m '确认提交'`
	8. git log 查看以往的提交
7. 修改一个文件之后
	1. `git status` 的结果是 changes not staged for commit 没有存到暂存区的修改
	2. `git diff 文件名称` 会显示文件修改之后的对比，如果不加文件名称，会对比所有修改过的文件，默认对比的是暂存区和工作目录的对比，想要比较仓库中的文件可以输入`git diff --staged`
	3. `git add 文件名称`， 就把文件添加到暂存区中了
