---
title: taskmgr 项目笔记
date: 
categories:
- 前端框架
tags:
- angular
---


# taskmgr 项目笔记

用 angular 做一个任务管理工具，笔记留念

## 创建工程

`ng new taskmgr --skip-install --style=scss `

`ng new taskmgr -si --style=scss`

`ng g m core`

`ng g m shared`



## 核心模块只加载一次

危险的写法

```typescript
import { NgModue} from '@angular/core';
import { CommonModule } from '@angular/common';

@NgModule({
  imports: [CommonModule],
  declarations: []
})

export class CoreModule {
  constructor (parent: CoreModule) {
    if (parent) {
      throw new Error('模块已经存在，不能再次加载！');
    }
  }
}
```

更好的写法，引入  SkipSelf 和Optional

```typescript
import { NgModue, SkipSelf, Optional} from '@angular/core';
import { CommonModule } from '@angular/common';

@NgModule({
  imports: [CommonModule],
  declarations: []
})

export class CoreModule {
  constructor (@Optional()@SkipSelf() parent: CoreModule) {
    if (parent) {
      throw new Error('模块已经存在，不能再次加载！');
    }
  }
}
```



## 共享模块



```typescript
import { NgModue} from '@angular/core';
import { CommonModule } from '@angular/common';

@NgModule({
  imports: [CommonModule],
  exports: [CommonModule],
  declarations: []
})

export class SharedModule {
  
}
```



## 	创建组件

`ng g c core/header --spec=false`

`g g c core/footer --spec=false`

`ng g c core/sidebar --spec=false`

`div.site>header+footer+main`

** 注意，要将 header、footer、sidebar组件从core 模块中导出，否则出了core 模块，他们就变成不可用的了。



## flex 布局

```css
html, body, app-root, .site {
  width: 100%;
  height: 100%;
  margin: 0;
}
.site {
  display: flex;
  flex-direction: column;
}
main {
  flex: 1;
}
header {
  background: green;
}
footer {
  background: blue;
}
```

flex 是对容器内元素的一种排列方式，**只对它的直接的字元素有影响**，

```
main> (div.small*3+div.big*3)
```

书接前文

```css
main {
  flex: 1;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap; /* 换行 */
  justify-content: center; /* 主轴方向,对齐方式，flex-start, center, flex-end, space-around, space-between*/
  align-content: flex-end; /* 次轴方向,对齐方式，flex-start, center, flex-end, space-around, space-between*/
  aligin-items: center; /* */
}
.small {
  width: 200px;
  height: 200px;
  background; red;
}
.big {
  width: 200px;
  height: 200px;
  background: red;
}
```

**继续学习其他的属性**



## angular material

### 侧边栏

`cnpm install --save @angular/material@2.0.0-beta`

```typescript
import { MdSidenvModule } from '@angular/material'
@NgModule({
  ...
  MdSidenavModule
  ...
})
```

正确引入 md-sidebar

```html
<md-sidenav-container>
	<md-sidenav>
		<app-sidebar></app-sidebar>
	</md-sidenav>
	<div class="site">
		<header>
			<app-header></app-header>
		</header>
		<main>
			<button (click)="sidenav.open()"
		</main>
		<footer>
			<app-footer></app-footer>
		</footer>
	</div>
</md-sidenav-container>
```

引入 angular material 的主题

侧边栏有over， side， push 三个mode



### toolbar

在core module 中导入

```typescript
import {MdToolbarModule } fomr '@angular/material'
```

底部

```Html
<md-toobar color="primary">
	<!-- 顶部 -->
	<button (click)="openSidebar()">打开菜单</button>
	<span>企业协作平台</span>
  	<!-- 底部 -->
  	<span class="fill-remaining-space"></span>
  	<span>@copy; 我的作品</span>
  	<span class="fill-remaining-space"></span>
  	<md-toolbar-row>
      <span class="fill-remaining-space"></span>
      <span>这里是第二行</span>
      <span class="fill-remaining-space"></span>
  	</md-toolbar-row>
</md-toolbar>
```

添加一个占位的元素，scss 如下，注意哦，md-toolbar 这些都是flex 容器

```css
.fill-remaining-space {
  flex: 1 1 auto;
}
```

实现一下 openSidebar，在header组件中引入 Output 和 EventEmitter

```typescript
@Output() toggle = new EventEmitter<void>();
...
export class HeaderComponent {
	openSidebar() {
      this.toggle.emit()
	}
}
```

在 app-component 里面监听 这个 toggle 事件， `(toggle)="sidenav.toggle()"`



### MdIconModule

导入 MdIconModule 到 core 模块

引入图标字体，谷歌的图标字体，`<link rel="stylesheet" href="//lib.baomitu.com/material-design-icons/3.0.1/iconfont/material-icons.min.css>"`，可以用360的镜像

`	<md-icon>menu</md-icon>`

导入自己的图标（某一个组件需要某个图标，不太好的实现）

* 首先去 iconfont 下载一个图标的 svg 	，例如 test.svg
* 然后webstorm 命令行中运行 `mv ~/Downloads/test.svg ./src/assets`
* 在 header 组件中添加 `import { MdIconRegistry } from '@angular/material'`
* 在 header 组件中添加 `import { DomSanitizer } from '@angular/platform-browser'`
* 在 header 的 constructor 中注入她们 `iconRegistryr: MdIconRegistry, sanitizer: DomSanitizer`
* 构造函数里边写 `iconRegistry.addSvgIcon('test', sanitizer.bypassSecurityTrustResourceUrl('assets/test.svg'))`
* 用法 `<md-icon svgIcon="gifts"></md-icon>`
* 报错的话在 core 中倒入 HttpModule

推荐的做法，在 app 文件夹 下面创建一个 utils 的文件夹，添加文件 svg.util.ts，内容如下

```typescript
import { MdIconRegistry } from '@angular/material';
import { DomSanitizer } from '@angular/platform-browser';

export const loadSvgResources = (ir: MdIconRegistry, ds: DomSanitizer) => {
  ir.addSvgIcon('test',ds.bypassSecurityTrustResourceUrl('assets/test.svg))
}
```

在 coreModule 中导入这个玩意

```typescript
// ...
import { MdIconRegistry } from '@angular/material';
import { DomSanitizer } from '@angular/platform-browser';
import { loadSvgResources } from '../utils/svg.util'
// ...

export class CoreModule {
  constructor (
  	ir: MdIconRegistry,
  	ds:DomSanitizer,
  	@Optional()@SkipSelf() parent: CoreModule
  ) {
    if (parent) {
      throw new Error('模块已经存在，不能再次加载！');
    }
    loadSvgResources(ir, ds)
  }
}
```



### input 组件

<md-input> 在 <md-input-container> 内部使用

<md-error> 只有在验证不通过的时候才会出现

<md-hint> 当 error 显示的时候，hint 会被隐藏

在app 下面新建一个 domain 的文件夹，新建一个user.model.ts

```typescript
export interface User {
  id?: string;
  email: string;
  password: string;
  name: string;
  avatar: string;
}
```

`ng g m login`

`ng g c login/login --spec=false`

把material 的模块 都导入到 sharedmodules 里边，引入input 和 card module

sharedmodule 的作用就是到如何导出

然后将sharedModule 引入到CoreModule 中

```html
<md-card>
	<md-card-header>
		<md-card-title>登陆：</md-card-title>
	</md-card-header>
	<md-card-content>
		<md-input-container hint-label="必填项">
			<span mdPrefix>wang</span>
			<input mdInput type="text" placeholder="您的email" />
			<span mdSuffix>@gmail.com</span>
			<md-hint>必填项</md-hint>
			<md-error></md-errod>
		</md-input-container>
	</md-card-content>
	<md-card-action>
		<p>还没有账户？<a href="">注册</a></p>
		<p>忘记密码？<a href="">找回<a/></p>
	</md-card-action>
</md-card>
```

在login 文件夹下面建一个字路由。login-routing.module.ts

```typescript
import { NgModule} from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { LoginComponent } from './login/login.component';

const routes: Routes = [
  {path: 'login', component: LoginComponent}
]

@NgModule({
  imports[RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class LoginRoutingModule{}
```

在app 的根目录下 建立 app-routing.module.ts

```typescript
import { NgModule} from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AppComponent} from "./app.component"; 

const routes: Routes = [
  {path: '', redirectTo: '/login', pathMatch:'full'}
]

@NgModule({
  imports[RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule{}
```

在跟模块，引入路由模块，在login module 中引入shared module 和loginRoutingModule

在根组件中的main 里边写上一个 router-outlet

`npm install —save @angular/animations`

在core module 下面

`import { BrowserAnimationsModule } from '@angular/platform-browser/animations'`





### button 和 card 组件

导入 MdButtonModule 到 core 模块，之后挪到 shared 里边去了

md-icon-button 只有图标的按钮

```Html
<form>
  <md-card>
    <md-card-header>
      <md-card-title>登陆</md-card-title>
    </md-card-header>
    <md-card-content>
      <md-input-container class="full-width">        
        <input mdInput type="text" placeholder="您的email">
      </md-input-container>
      <md-input-container class="full-width">        
          <input mdInput type="password" placeholder="您的密码">
        </md-input-container>
      <button md-raised-button type="button" color="primary">登陆</button>
    </md-card-content>
    <md-card-actions>
      <p class="text-right">还没有账号？<a href="">注册</a></p>
      <p class="text-right">忘记密码？<a href="">找回</a></p>      
    </md-card-actions>
  </md-card>

  <md-card>
      <md-card-header>
        <md-card-title>
          每日佳句
        </md-card-title>
        <md-card-subtitle>
          满足感在于不断的努力，而不是现在有成就。全心努力定会胜利满满。
        </md-card-subtitle>
      </md-card-header>
      <img md-card-image src="/assets/geek.png" alt="">
      <md-card-content>
        满足感在于不断的努力，而不是现在有成就。全心努力定会胜利满满。
      </md-card-content>
    </md-card>
</form>
```



```Css
form {
  display: flex;
  flex-direction: column;
  jsstify-content: center;
  align-items: center;
  width:100%:
  height:100%;
}
md-card {
  margin: 5px;
  width: 20em;
  flex: 0 0 20em;
}

.text-right {
  margin: 10px;
  text-align: right;
}
```



在 style.css 中添加一个类

```css
.full-width {
  width: 100%;
}
```



### MdList 组件

用于列表

<md-list> 和 <md-nav-list>

多行列表、带图表列表、带头像列表、密集列表、多段列表



更改项目： 

在 SharedModule 里面添加 MdListModule

把 sidenav 的 mode 改成 over，在 app component 里边修改宽度300px;

修改 sidebar 组件;

```html
<md-nav-list dense>
	<h3 mdSubheader>项目</h3>
	<md-list-item>
		<md-icon md-list-icon svgIcon="projects"></md-icon>
		<span md-line>项目首页</span>
		<span md-line mdSubheader>查看您的所有项目</span>
	</md-list-item>
	<h3 md-subheader>日历</h3>
	<md-list-item>
		<span>日历首页</span>
	</md-list-item>
	<md-list-item>
		<md-icon md-list-icon svgIcon="month"></md-icon>
		<span md-line>月视图</span>
		<span md-line md-subheader>按月查看您的任务</span>
	</md-list-item>
	<md-list-item>
		<md-icon md-list-icon svgIcon="week"></md-icon>
		<span md-line>周视图</span>
		<span md-line md-subheader>按周查看您的任务</span>
	</md-list-item>
	<md-list-item>
		<md-icon md-list-icon svgIcon="day"></md-icon>
		<span md-line>日视图</span>
		<span md-line md-subheader>按天查看您的任务</span>
	</md-list-item>
</md-list>
```

修改 svg.util.ts

```typescript
...
const imgDir = 'assets/img';
const sidebarDir = `${imgDir}/sidebar`;
const dayDir = `${imgDir}/days`;

const days = [
  1, 2, 3, 4,.......,31]
]

days.forEach(d => ir.addSvgIcon(`day${d}`, ds.bypassSecurityTrustResourcdUrl(`${dayDir}/day${d}.svg`)))

...
```

在sidebar 的css 中

```css
md-icon {
  align-self: flex-start
}
```

 

`cnpm install --save date-fns`

`cnpm i --save-dev @types/date-fns`

修改 sidebar 的 ts

```typescript
import {getDate } from 'date-fns';

export class Sidebar Component implements OnInit {
  today = 'day';
  
  ngOnInit() {
    this.today = `day${getDate(new Date)()}`;
  }
}
```

前台的svgIcon 修改 `[svgIcon]="today"`



### angular material 主题

主题： 调色板 + 明暗 + 叠加

自定义一个主题，修改 style.scss 文件

```typescript
import 'theme.scss';
```

新建一个 theme.scss 主题：

```typescript
import '~@angular/material/theming';
@include mat-core();

$my-app-primary: mat-palette($mat-indigo);
/* 第一个参数是默认的颜色的深浅，在亮的主题下，颜色深浅，和暗的主题下颜色的深浅*/
$my-app-accent:mat-palette($mat-pink,A200，A400);
$my-app-warn: mat-palette($mat-red);

$my-app-theme: mat-light-theme($my-app-primary, $my-app-accent, $my-app-warn);
@include angular-material-theme($my-app-theme);


$my-dark-primary: mat-palette($mat-blue-grey);
$my-dark-accent:mat-palette($mat-amber,A200，A400);
$my-dark-warn: mat-palette($mat-deep-orange);

$my-dark-theme: mat-light-theme($my-dark-primary, $my-dark-accent, $my-dark-warn);

.myapp-dark-theme {
  @include angular-material-theme($my-dark-theme);
}
```

在header 中加入开关

```html
<md-toolbar color="primary">
  <button md-button color="accent" (click)="toggleSidenav()" >
    <md-icon>menu</md-icon>
  </button>
  <span>任务管理系统</span>
  <span class="fill-remaining-space"></span>
  <md-slide-toggle (change)="onChange($event)">黑夜模式</md-slide-toggle>
</md-toolbar>
```

在 SharedModule 中引入 MdSlideToggle

```typescript
@Output() togleDarkTheme = newEventEmitter<boolean>();

onChange(checked: boolean) {
  this.toggleDarkTheme.emit(checked);
}
```

在 appComponent 中监听这个事件

```html
<md-sidenav-container [class.lmyapp-dark-theme]="darkTheme">
...
<app-header (toggle)="sidenav.toggle" (toggleDarkTheme)="switchTheme($event)"></app-header>

...
</md-sidenav-container>
```



```typescript
export AppComponent {
  darkTheme: false;
  switchTheme (dark) {
    this.darkTheme = dark;
  }
}
```



```css
md-sidenav-container {
  &.myapp-dark-theme {
    background: black;
  }
}
```





### Grid List

用于相似的数据展示，尤其是图片，类似相册等

行为很像表格，有colspan 和 rowspan 属性

`ng g c login/register --spec=false`

修改 register.html ，在sharedModule里面导入导出

```html
<form>
  <md-card>
    <md-card-header>
      <md-card-title>注册</md-card-title>
    </md-card-header>
    <md-card-content>
      <md-input-container class="full-width">        
        <input mdInput type="text" placeholder="您的email">
      </md-input-container>
      <md-input-container class="full-width">        
        <input mdInput type="text" placeholder="您的姓名">
      </md-input-container>
      <md-input-container class="full-width">        
          <input mdInput type="password" placeholder="您的密码">
      </md-input-container>
      <md-input-container class="full-width">        
         <input mdInput type="password" placeholder="重复密码">
      </md-input-container>
      <md-grid-list cols="8" rowHeight='1:1'>
      	<md-grid-tile *ngFor="let item of items">
      		<md-icon class="avatar" [svgIcon]="item"></md-icon>
      	</md-grid-tile>
      </md-grid-list>
      <button md-raised-button type="button" color="primary">注册</button>
    </md-card-content>
    <md-card-actions>
      <p class="text-right">已经有账号？<a href="">登陆</a></p>
      <p class="text-right">忘记密码？<a href="">找回</a></p>      
    </md-card-actions>
  </md-card>
</form>

<style>
	.avatar {
      width: 64px;
      height: 64px;
      overflow: hidden;
      border-radius: 50%;
      margin: 12px;
	}
</style>
```

`[routerLink]="/register"`

修改 svg.util.ts

```typescript
const avatarDir = `${imgDir}/avatar`
...
ir.addSvgIconSetInNamespace('avatars', ds.bypassSecurityTrustResourceUrl(`${avatarDir}/avatars.svg`))
```

修改 register.component 里面修改

```typescript
items: string[];
...
ngOnInit() {
  const nums = [1, 2, 3, 4....... 16];
  this.items = nums.map( d => {
    `avatars:svg-${d}`;
  })
}
```



### Dialog 对话框

对话框需要在模块的entryComponents 中声明， menu 也需要这样（因为她们事先要隐藏）

传递数据 `const dialogRef = dialog.open(YourDialog, {data: 'your data'})`

接受数据 `constructor(@Inject(MD_DIALOG_DATA) public data: any)`

`ng g m project`

`ng g c project/project-list --spec=false` 

`ng g c project/project-item --spec=false`

`ng g c project/new-project --spec=false`

`ng g c project/invite --spec=false`

修改 project module

```typescript
@NgModule({
	...
	entryComponent:[
      NewProjectComponent,
      InviteComponent
	]
})
```

修改 project list component 的 html

```html
<app-project-item *ngFor="let project of projects" [item]="project">

</app-project-item>
<button md-fab type="button" (click)="openNewProjectDialog()">
	<md-icon>add</md-icon>
</button>
```

改 project item component 的 html

```html
<md-card>
	<md-card-header>
		<md-card-title>
			{{item.name}}
		</md-card-title>
	</md-card-header>
	<img md-card-images [src]="item.coverImg" alt="项目封面">
	<md-card-content>
		{{item.desc}}
	</md-card-content>
	<md-card-actions>
		<button md-button type="button">
			<md-icon>note</md-icon>
			<span>编辑</span>
		</button>
		<button md-button type="button">
			<md-icon>group_add</md-icon>
			<span>邀请</span>
		</button>
		<button md-button type="button">
			<md-icon>delete</md-icon>
			<span>删除</span>
		</button>
	</md-card-actions>
</md-card>
```

新建一个project/project-routing.module.ts

```typescript
import { NgModule } from "@angular/core";
import { RouterModule, Routes } from '@angular/router';
import { ProjectListComponent } from './project-list/project-list.component';

const routes: Routes = [
  path: 'project', component: ProjectListComponent
]

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class ProjectRoutingModule {}
```

修改project module ts 文件，中导入刚才建立的路由，用shared Module 代替common Module，在AppModule中倒入 project module

在路由中，添加一个路由项 `{path: 'project', redirectTo: '/project', pathMatch: 'full'}`

给 project list 中的 project list item 组件 绑定一个item， `[item] = project`
在子组件 project list item 中引入一个 input 属性 item `@Input () item;`

在project list 里面 添加一个 projects 数组

```typescript
projects = [
  {
    "name": "企业协作平台",
    "desc": "这是一个企业内部项目",
    "coverImg": "assets/img/cover/0.jpg"
  },
  {
    "name": "自动化测试项目",
    "desc": "这是一个企业内部项目",
    "coverImg": "assets/img/cover/1.jpg"
  }
]
```

给 app-project-item 添加一个类card ， 修改project list component 的 scss

```css
.card {
  height: 360px;
  flex: 0 0 360px;
  margin: 10px;
}

:host {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}

.fab-button {
  position: fixed;
  right: 32px;
  bottom: 96px;
  z-index: 998
}
```

完成 new-project 组件的模版

```html
<form>
	<h2 md-dialog-title>新建项目</h2>
	<div md-dialog-content>
		<md-input-container class="full-width">        
			<input mdInput type="text" placeholder="项目名称">
        </md-input-container>
        <md-input-container class="full-width">        
			<input mdInput type="text" placeholder="项目描述">
        </md-input-container>
	</div>
	<div>
		<button type="button" md-raised-button color="primary" (click)="onClick()">
			保存
        </button>
		<button type="button" md-dialog-closes md-button>关闭</button>
	</div>
</form>
```

修改 project list 的 ts

```typescript
import {MdDialog} from '@angular/material';
import {NewProjectComponent} from '../new-project/new';
...
constructor(... private dialog: MdDialog ...) {}
...

openNewProjectDialog() {
  const dialogRef = this.dialog.open(NewProjectComponent, {
  width: '100px', 
  height: '100px',
  position: {
    left: 0,
    top: 0
   }}, 
   {data: {
   	'dark':true
   	}
   }
  });
    dialogRef.afterClosed().subscribe(result => { 
    	console.log(result)
    })
}
```

修改new project

```typescript
import {MD_DIALOG_DATA, MdDialogRef, OverlayContainer} from '@angular/material';
import {Component, OnInit, Inject} from '@angular/core';

export class NewProjectComponent {
  constructor(
  	@Inject(MD_DIALOG_DATA)private data,
  	private dialogRef: MdDialogRef<NewProjectComponent>,
  	private oc:OverlayContainer
  ) {
    
  }
  
  ngOnInit() {
    console.log(JSON.stringify(this.data));
    this.oc.themeClass = this.data.dark?'myapp-dark-theme: null
  }
  
  onClick() {
    this.dialogRef.close('I received your message");
  }
}
```

 

### autoComplete

```html
<md-input-container class="full-width">
	<input mdInput
		placeholder="更改执行者"
		formControlName="ownerSearch"
		[mdAutocomplete]="assignee">
</md-input-container>
<md-autocomplete #assignee="mdAutocomplete"
	[displayWith]="displayUser">
	<md-option 
		*ngFor="let item of ownerResults"
		[value]="item">
		{{item.name}}
	</md-option>
<md-autocomplete>
```

修改 invite component 的 html

```html
<form>
	<h3 md-dialog-title>邀请组员</h3>
	<div md-dialog-content>
		<md-input-container class="full-width">
			<input mdInput type="text" placeholder="组员姓名" [mdAutocomplete]="autoMembers">
		</md-input-container>
		
	</div>
	<div md-dialog-actions>
		<button type="buton" md-raised-button color="primary" (click)="onClick()">保存</button>
		<button type="buton" md-dialog-close md-button>关闭</button>
	</div>
</form>

<md-auto-complete #autoMembers="mdAutocomplete [displayWidth]="displayUser">
	<md-option *ngFor="let item of items" [value]="item">
		{{item.name}}
	</md-option>
</md-auto-complete>
```



修改 invite component 的 ts



```typescript
export class InviteComponent implements OnInit {
  items = [
    {id: 1, name: 'zhangsan'},
    {id: 2, name: 'lisi'},
    {id: 3, name: 'wangwu'},
    {id: 4, name: 'zhaoliu'},
    {id: 5, name: 'sunqi'},
    {id: 6, name: 'huba'}
  ]
  
  displayUser(user:{id: number; name: string}) {
    return user? user.name : "";
  }
  
  export interface User {
    id: number,
    name: string
  }
}
```



project item 当中有邀请这个按钮，自然的想法，在邀请按钮上，直接写click事件，调取dialog，不算太好，子组件最好只负责显示，有一些输入输出，不涉及逻辑，后期改起来很麻烦。

好的做法，在 project item 组件中，不做逻辑处理，把事件发射给父组件。

具体，在 project item 的 ts 中，引入Input 、 Output 和 EventEmitter，点击邀请后，`@Output() onInvite = new EventEmitter<void>()` 和 `onInviteClick() { this.onInvite.emit();}`， 让project list 组件处理这个事件，写个 launchInviteDialog

在 shared module 中导入导出 mdautocomplete 组件









