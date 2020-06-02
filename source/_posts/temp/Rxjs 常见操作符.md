---
title:  Rxjs 常见操作符
date: 
categories:
- 临时
tags:
- 临时
---

在思考维度上，添加时间的考量。

把事件想象成随着时间变化的流。

流主动把事件推给你，不像传统编程我们要不同去拉。

```javascript
const length$ = Rx.Observable.fromEvent(length, 'keyup').pluck('target','value');

// 下面的代码等同于 Rx.Observable.fromEvent(width, 'keyup').map(ev => ev.target.value);
const width$ = Rx.Observable.fromEvent(width, 'keyup').pluck('target' 'value');

//const area$ = Rx.Observable.combineLatest(length$, width$, (l, w) => {return l*w});
const area$ = Rx.Observable.zip(length$, width$, (l, w) => {return l*w});
area$.subscribe(value => area.innerHTML = value);
```

## 常见操作符（一）

### 常见的创建类操作符

1. from：把数组、Promise、以及 Iterable 转化为 Observable，例如 `Rx.Observable.from([1, 2, 3, 4])`
2. fromEvent：把事件转化为 Observable
3. of：接受一系列的数据并把它们 emit 出去，例如 `Rx.Observable.of({id:1,value:20},{id:2,value:30})`

### 常见转换类操作符

1. map：对原始流中的元素进行处理，映射成新的元素
2. mapTo：将流的值变成一个常量，例如上面的例子 `Rx.Observable.fromEvent(width, 'keyup').mapTo(1)`，等不关心一个点击事件的内容，只关心事件发生了的时候可以用，等同于 `Rx.Observable.fromEvent(width, 'keyup').map(_ => 1)`
3. pluck：

# 使用

新建一个 quote.module.ts

```
export interface Quote {
  id?: string;
  cn: string;
  pic: string;
  en: string
}
```

在根目录下建立 mock 文件夹和 data.json 文件

```
{
  "quotes": [
    {
      "id": 1,
      "cn": 'ads',
      "en": 'adfs',
      "pic": '/abc_hello.png'
    }
  ]
}
```

敲击` json-server ./mock`



新建 quote.service.ts，快捷方式 `ng-service`

```
import { Injectable, Inject } from '@angular/core';
import { Http } from '@angular/http'
import { Observable } from 'rxjs/Observable';
import { Quote } from '../domain/quote.model';

@Injectable()
export class QuoteService {
  constructor(
  	private http: Http,
  	@Inject('BASE_CONFIG) private config
  ) {}
  
  getQuote():Observable<Quote> {
  	const uri = `${this.config.uri}/quote/Math.floor(${Math.random()*10)}`
    return this.http.get(uri)
    	.map(res => res.json() as Quote)
  }
}
```



修改 services 目录下的 services.module.ts 文件

```
import {NgModule,ModuleWithProviders} from '@angular/core';
import { CommonModule } from '@angular/common';
import { QuoteService } from './quote.service';

@NgModule()
export class ServicesModule {
  static forRoot(): ModuleWithProviders {
    return {
      ngModule: ServiceModule,
      providers: [QuoteService]
    }
  }
}
```



在 CoreModule 的 @NgModule 中添加一个 provider

```
...
providers: [
  {provide: 'BASE_CONFIG', useValue: {
  	uri: 'http://localhost:3000'
  }}
]
...
```



修改loginComponent，在 constructor 中添加 `private quoteService$:QuoteService`，生命一个变量 `quote: Quote`	继续修改constructor，注意，要给 quote 一个初始只，防止网络不通的时候

```
this.quoteService$
	.getQuote()
	.subscribe(q => this.quote = q);
```



## 常见操作符（二）

### Observable 的性质

1. next：
2. error：
3. Complete：
4. 特殊的 
   1. 永不结束
   2. Never（不结束，也不发射）
   3. Empty（结束但不发射）
   4. Throw（直接进入error 状态）

### 操作符

1. 工具类操作符：do
2. 变换类操作符：scan
3. 数学类操作符：reduce
4. 过滤类操作符： filter, take, first/last, skip
5. 创建类操作符：Interval, Timer



### 例子

subscribe 可以接受三个**函数**作为参数

```Js
const interval$ = Rx.Observable.interval(100).take(3);
interval$.subscribe(
	val => console.log(val),
	err => console.log(err),
	() => console.log('I am complete')
)
```



## 例子

```js
// const timer$ = Rx.Observable.timer(100);
const timer$ = Rx.Observable.timer(100, 100);
timer$.subscribe(val => console.log(val));
```



### 例子 - do

do 可以作为一个跟外部进行一些交互，做一些处理

```
const logLabel = '当前的·值改变了';
const interval$ = Rx.Observable.interval(100)
	.map(val => val * 2)
	.do(v => console.log(logLabel + v))
	.take(3);
interval$.subscribe(val => console.log(val));
```



### 例子 - filter

本来应该 0，1，2 结束，现在变成0，1，2，3，4

```
const logLabel = '当前的·值改变了';
const interval$ = Rx.Observable.interval(100)
	.filter(val => val % 2 === 0)
	.do(v => console.log(logLabel + v))
	.take(3);
interval$.subscribe(val => console.log(val));
```



### 例子 - first 和 last 

.first 和 .take(1)

无穷序列的last() 是永远不会结束的，认为制造出了 empty，无法发射值

```
const interval$ = Rx.Observable.interval(100)
	.filter(val => val % 2 === 0)
	.first()
interval$.subscribe(
	val => console.log(val),
	err => console.log(err),
	() => console.log('completed)
)
```



### 例子 - skip

```
const interval$ = Rx.Observable.interval(100)
	.filter(val => val % 2 === 0)
	.skip(2)
interval$.subscribe(
	val => console.log(val),
	err => console.log(err),
	() => console.log('completed)
)
```



### 例子 - scan

接受一个函数作为参数 ，第一个参数是累加结果，y 是序列的新值

```
const interval$ = Rx.Observable.interval(100)
	.filter(val => val % 2 === 0)
	.scan((x, y) => return x + y) 
	.take(4)
interval$.subscribe(
	val => console.log(val),
	err => console.log(err),
	() => console.log('completed)
)
```





# 其他高阶操作符

## mergeMap 和 switchMap

删除文章的时候，后台要同时删除文章下面的评论，如果连续两个删除文章请求，服务器收到第二个请求的话，如果要继续删除第一篇文章的数据，就是mergeMap，如果不再删除第一篇的数据了，直接去删除第二篇的数据的话，就用 switchMap。 



