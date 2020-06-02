---
title: 学习 angular
date: 
categories:
- 前端框架
tags:
- angular
---

## 第 1 部分 ChangeDetection 

检测程序内部状态，然后反映到 UI 上

引起状态变化的东西包括：Events、XHR、Timers

ApplicationRef 监听 NgZone 的 onTurnDone，然后执行检测

angular CD 有两种策略，默认策略（把整个树跑一遍），OnPush策略（除非我的属性发生变化，否则不用来检查我了，大的应用可以显著提高应用性能）。

```
@Component({
  ...
  changeDetection: ChangeDetectionsStrategy.Default
  changeDetection: ChangeDetectionsStrategy.OnPush
})
```



如何主动告诉 angular 来检查我

```
constructor(
	private cd: ChangeDetectorRef
) {
  this.cd.markForCheck();
}

doSomethingNeedChangeDetection() {
  ...
  this.cd.markForCheck();
}
```



## 第 2 部分 angular 依赖注入

### 2.1 依赖注入之前的写法

问题 Person 类，需要清清楚楚知道 Id 和 Address 的细节和构造方式，Id 或者 Address 重构之后，很麻烦
而且以后用的多了之后，重构修改起来很麻烦

```
class Id {
  static getInstance(type: string) {
    // 省略了一些设置
    return new Id();
  }
}

class Address {
  provice: string;
  city: string;
  district: string;
  street: string;
  constructor(province, city, district, street) {
    this.provice = province;
    this.city = city;
    this.district  = district;
    this.street = street;
  }
}

class Person {
  id: Id;
  address: Address;
  constructor() {
    this.id =  Id.getInstance('idcard');
    this.address =  new Address('北京', '北京', '朝阳区', '某某街道');
  }
}



```

### 2.2 依赖注入的写法

```
class Id {
  static getInstance(type: string) {
    // 省略了一些设置
    return new Id();
  }
}

class Address {
  provice: string;
  city: string;
  district: string;
  street: string;
  constructor(province, city, district, street) {
    this.provice = province;
    this.city = city;
    this.district  = district;
    this.street = street;
  }
}

class Person {
  id: Id;
  address: Address;
  constructor(id: Id, address: Address) {
    this.id =  id;
    this.address =  address;
  }
}

  // 
```

伪代码，原理就是一级一级向上推，把责任推给入口函数，依赖注入框架可以帮我们处理这个事情

```
main() {
	const id = Id.getInstance('idcard');
    const address = new Address('北京', '北京', '朝阳区', '某某街道');
    const person = new PersonDI(id, address);
}
```

### 2.3 利用 angular 的依赖注入

```
class PersonAngularDI {
  constructor(private oc: OverlayContainer) {
    const injector = ReflectiveInjector.resolveAndCreate([
      {provide: Person, useClass: PersonAngularDIInstance},
      {provide: Address, useFactory: () => {
        if (environment.production) {
          return new Address('北京', '北京', '朝阳区', '某某街道');
        } else {
          return new Address('上海', '上海', '闵行区', '某某街道');
        }
      }},
      {provide: Id, useFactory: () => {
        return Id.getInstance('idcard');
      }},
    ]);

    const person = injector.get(Person);
  }
}

class PersonAngularDIInstance {
  id;
  address;
  constructor(
    @Inject(Id) id,
    @Inject(Address) address
  ) {
    this.id = id;
    this.address = address;
  }
}
```

在任意组件中注入 `providers: [{provide: 'BASE_CONFIG', useValue: 'http://localhost:3000'}]`
// 在其他组件中可以在consturctor 中得到他， `constructor(@Inject('BASE_CONFIG') config)`
// 默认情况下，注入之后变成单件，大家取到的以来都是一样的
// 在那个injector 的函数里边，useFactory 返回一个方法，而不是值可以每次创建一个新的实例
// 写一个 `childInjector = injector.resolveAndCreateChild([Person]); const personFromChild = childInjector.get(Person)`;





## 第 3 部分 animation

BrowserAnimationModule 放在最后导入，先导入可能会引起异常

```
trigger(
'square', [
state('green', style({
'background-color': 'green',
'transform': 'translateX(0)'
})),
state('red', style({
'background-color': 'red',
'width': '50px',
'height': '50px',
'transform': 'translateX(200%)'
})),
transition('green => red', animate(1000)),
transition('red => green', animate(1000)),
]
)
```



animate的参数，第一个持续时间，第二个延续时间，第三个参数是缓动函数（动画效果执行时的速度，例如，先加速后减速）。



## 路由动画

Void 空状态

路由动画需要在 host 元数据中指定触发器。

动画不要太多，否则适得其反。引导用户去注意一些东西，内容、服务优先。

## group

同时进行一组的动画变换。

```
group([animate(...), animate(...) ...])
```



## Query & Stagger

Query 用于父节点寻找子节点，然后把动画应用到你选中的这些元素上

Stagger 指定有多个满足 Query 的元素的动画分别播放，每个的动画之间有间隔



## 其他

新增一项

```
this.projects = [...this.projects, {id: 3, name: '新增'}]
```

删除一项

```
this.projects = this.projects.filter(p => p.id !== project.id)
```

## 第 4 部分 属性型指令

```
import { Directive, ElementRef } from '@angular/core';
@Directive({selector: '[highlight]'})
export class HighlightDirective {
    constructor(el: ElementRef) {
        el.nativeElement.style.backgroundColor = 'gold';
        console.log(`
        * AppRoot highlight called for ${el.nativeElement.tagName}`)
    }
}

// 使用
<h1 highlight>{{title}}</h1>


```

指令、组件，作为 NgModule 元数据中的 declarations 导入。

```
declarations: [
  AppComponent,
  HighlightDirective,
]
```

## 第 5 部分 自定义组件

```
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-title',
  templateUrl: '<h1 highlight>{{title}} {{subtitle}}</h1>',
})
export class TitleComponent {
  @Input() subtitle = '';
  title = 'Angular Modules';
}
```

引入自定义组件，作为 NgModule 元数据中的 declarations 导入。

```
 @NgModule({
  imports:      [ BrowserModule ],
  declarations: [ AppComponent, HighlightDirective,TitleComponent, ],
  bootstrap:    [ AppComponent ]
})
export class AppModule { }
```

服务提供商

模块可以往应用的“根依赖注入器”中添加提供商，让那些服务在应用中到处可用。

```typescript
import { Injectable } from '@angular/core';

@Injectable()
export class UserService {
  userName = 'Sherlock Holmes';
}
```



```html
<h1 highlight>{{title}} {{subtitle}}</h1>
<p *ngIf="user">
  <i>Welcome, {{user}}</i>
<p>
```



```typescript
import { Component, Input } from '@angular/core';
import { UserService } from './user.service';

@Component({
  selector: 'app-title',
  templateUrl: './title.component.html',
})
export class TitleComponent {
  @Input() subtitle = '';
  title = 'Angular Modules';
  user = '';

  constructor(userService: UserService) {
    this.user = userService.userName;
  }
}
```



```typescript
 @NgModule({
  imports:      [ BrowserModule ],
  declarations: [ AppComponent, HighlightDirective,TitleComponent, ],
  bootstrap:    [ AppComponent ],
  providers: 	[ UserService ]
})
export class AppModule { }
```



#### 导入模块

导入`BrowserModule`会让该模块公开的所有组件、指令和管道在`AppModule`下的任何组件模板中可用。

更准确的说，`NgIf`是在来自`@angular/common`的`CommonModule`中声明的。

`CommonModule`提供了很多应用程序中常用的指令，包括`NgIf`和`NgFor`等。`BrowserModule`导入了`CommonModule`并且[*重新导出*](https://v2.angular.cn/docs/ts/latest/cookbook/ngmodule-faq.html#q-re-export)了它。

 最终的效果是：只要导入`BrowserModule`就自动获得了`CommonModule`中的指令。

很多熟悉的 Angular 指令并不属于`CommonModule`。 例如，`NgModel`和`RouterLink`分别属于 Angular 的`FormsModule`模块和`RouterModule`模块。



模版驱动的表单

```html
<h2>Contact of {{userName}}</h2>
<div *ngIf="msg" class="msg">{{msg}}</div>
<form *ngIf="contacts" (ngSubmit)="onSubmit()" #contactForm="ngForm">
  <h3 highlight>{{ contact.name | awesome }}</h3>
  <div class="form-group">
    <label for="name">Name</label>
    <input type="text" class="form-control" required
      [(ngModel)]="contact.name"
        name="name"  #name="ngModel" >
    <div [hidden]="name.valid" class="alert alert-danger">
      Name is required
    </div>
  </div>
  <br>
  <button type="submit" class="btn btn-default" [disabled]="!contactForm.form.valid">Save</button>
  <button type="button" class="btn" (click)="next()" [disabled]="!contactForm.form.valid">Next Contact</button>
  <button type="button" class="btn" (click)="newContact()">New Contact</button>
</form>
```



## 第 6 部分 angular 

是将 `<base>` 标签添加到我们的 `index.html` 文件中，在 html 的 head 中，添加一段 `<base href="/">`

### 定义带参数的路由

```
import { HomeComponent } from './home/home.component';
import { ProfileComponent } from './profile/profile.component';

export const ROUTES: Routes = [
  { path: '', component: HomeComponent },
  { path: '/profile/:username', component: ProfileComponent }
];
```

### 获取路由的参数

```
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'profile-page',
  template: `
    <div class="profile">
      <h3>{{ username }}</h3>
    </div>
  `
})
export class SettingsComponent implements OnInit {
  username: string;
  constructor(private route: ActivatedRoute) {}
  ngOnInit() {
    this.route.params.subscribe((params) => this.username = params.username);
  }
}
```

### 子路由的例子

```
import { SettingsComponent } from './settings/settings.component';
import { ProfileSettingsComponent } from './settings/profile/profile.component';
import { PasswordSettingsComponent } from './settings/password/password.component';

export const ROUTES: Routes = [
  { 
    path: 'settings', 
    component: SettingsComponent,
    children: [
      { path: 'profile', component: ProfileSettingsComponent },
      { path: 'password', component: PasswordSettingsComponent }
    ]
  }
];

@NgModule({
  imports: [
    BrowserModule,
    RouterModule.forRoot(ROUTES)
  ],
})
export class AppModule {}
```

