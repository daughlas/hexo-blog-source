---
title:  学习 Rxjs
date: 
categories:
- 临时
tags:
- 临时
---
## 基础

`Rx.Observable.fromEvent(element, event)` 可以将一个事件转化为一个流

```javascript
const height$ = Rx.Observable.fromEvent(heightInput, 'keyup')
// 每一次抬起键盘打印出一个事件的对象，一大堆
height$.subscribe(val => {console.log(val)})
// 每次输入之后，log 出input 的值
height$.subscribe(val => {console.log(val。target.value)})
```



## 常见的创建类操作符

from：可以吧数组、Promise、以及Iterable 转化为Observable

fromEvent： 可以吧事件转化为Observable

of：接受一系列的数据，并把他们 emit 出去，和数组的区别是，可以单独的四个数，可以不在一个数组里边存着。



## 常见转换累操作符：map、mapTo、pluck

map 是对原始流中的元素进行处理，映射成另一个元素。

`.map(x => 10 * x)`

pluck 是 map 对于对象的一种特殊应用

```javascript
var width$ = Rx.Observable.fromEvent(widthInput, 'keyup')
	.pluck('target', 'value')
// 下面的代码一样的作用
var width$ = Rx.Observable.fromEvent(widthInput, 'keyup')
	.map(ev => ev.target.value)
```

mapTo 后面可以输入一个常量，我只关心你发生了，不管你到底是什么事件

```javascript
var width$ = Rx.Observable.fromEvent(widthInput, 'keyup')
	.mapTo(1)
// 下面的代码效果一样
var width$ = Rx.Observable.fromEvent(widthInput, 'keyup')
	.map(_ => 1)
```





## Observable 的性质

三种状态： next, error, complete

subscribe 有三个函数作为参数

特殊的：永不结束, Never, Empty(结束但不发书，直接进入complete 状态)，Throw（直接进入 error状态）

1. 永不结束的，例如，计时器，每隔一秒发送一个信息给你；
2. Never：完全不发射元素，流也不结束，测试的时候用到；
3. Empty：不发射，流里面没有元素，但是直接进入结束状态，测试的时候用到；
4. Throw：不发射热和元素，流里面没有东西，直接进入error状态。



```javascript
const interval$ = Rx.Observable.interval(100)
	.filter(val => {
      throw '出错了'
	}} )
	.take(4);
	.reduce((x, y) => {return [...x, y]}, []);
	
interval$.subscribe(
	next = > console.log(next),
	err => console.log('Error: ' + err),
	() => console.log('I am complete')
);
```



```javascript
// 直接进入error
const interval$ = Rx.Observable.throw('出错了');
// 直接进入结束
const interval$ = Rx.Observable.empty();
const interval$ = Rx.Observable.never();
```

通常要避免认为制造never 和 empty，要考虑无穷序列和条件的匹配

## 过滤类操作符： filter, take, first/last, skip；两个常见的创建类操作符： Interval， Timer

1. filter 过滤器，满足一定条件的过滤掉
2. take 第几个，first 和 last 是 take 的特殊表达形式
3. skip省略掉前面的几个东西

```javascript
const interval$ = Rx.Observable.interval(100);
// 永远不结束
interval$.subscribe(
	next = > console.log(next),
	err => console.log(err),
	() => console.log('I am complete')
);
```



```javascript
// 取前三个
const interval$ = Rx.Observable.interval(100).take(3);
interval$.subscribe(
	next = > console.log(next),
	err => console.log(err),
	() => console.log('I am complete')
);
```



```javascript
const timer$ = Rx.Observable.timer(100);
timer$.subscribe(v => console.log(v));
```



```javascript
// 第一个参数表示第一次运行的延迟，第二个表示之后间隔运行的时间，timer 相当于包含了 interval 的功能
const timer$ = Rx.Observable.timer(100, 100);
timer$.subscribe(v => console.log(v));
```



## 常见工具操作符（do、scan、reduce)

1. do 一般做调试
2. do 还可以进行一些外部条件的设置，对流之外的东西进行一些改变
3. scan，变换类操作符，频繁
4. reduce 数学类操作符，特别频繁



```javascript
const logLabel = '当前值是'
const interval$ = Rx.Observable.interval(100)
	.map(val => val * 2)
	.do(v => {
		console.log(logLabel' + v);
		logLabel = "当前";
    })
	.take(3);
// do 的作用是 map 之后， take 之前，看看流里面的内容
interval$.subscribe(
	next = > console.log(next),
	err => console.log(err),
	() => console.log('I am complete')
);
```



```javascript
const interval$ = Rx.Observable.interval(100)
	// 只有当这个条件为真的时候才会把它放行出来
	.filter(val => val % 2 === 0  )
	.do(v => console.log('val is ' + v))
	.take(3);
// do 的作用是 map 之后， take 之前，看看流里面的内容
interval$.subscribe(
	next = > console.log(next),
	err => console.log(err),
	() => console.log('I am complete')
);
```



```javascript
const interval$ = Rx.Observable.interval(100)
	.filter(val => val % 2 === 0  )
	.do(v => console.log('val is ' + v))
	// .take(1) 和 .first() 效果一样，
	// 对一个无穷的序列取.last()，拿最后一个，会变成 never 状态，永远不会实现，人为制造出来一个empty，要注意，不要编写一个无法发射值的流
	.take(1);
// do 的作用是 map 之后， take 之前，看看流里面的内容
interval$.subscribe(
	next = > console.log(next),
	err => console.log(err),
	() => console.log('I am complete')
);
```

skip 跳过两个，4，6。。。。。

```javascript
const interval$ = Rx.Observable.interval(100)
	.filter(val => val % 2 === 0  )
	.do(v => console.log('val is ' + v))
	.skip(2);
interval$.subscribe(
	next = > console.log(next),
	err => console.log(err),
	() => console.log('I am complete')
);
```

scan 接受一个函数作为参数，第一个参数增加器，将这一次累加的结果，作为下一次的第一个参数传进去，默认的初始值，第二个参数是序列中当前的发射值，返回的是0，2，6，12，有点像数组的 scan， 记住之前的运算结果

```javascript
const interval$ = Rx.Observable.interval(100)
	.filter(val => val % 2 === 0  )
	.scan((x, y) => {
      return x + y
	});
	.take(4);
interval$.subscribe(
	next = > console.log(next),
	err => console.log(err),
	() => console.log('I am complete')
);
```



reduce 和 scan 的计算是一样的，它要计算一个最终值，而且也只发射最后的一个值，注意写的时候无限序列不能用reduce

```javascript
const interval$ = Rx.Observable.interval(100)
	.filter(val => val % 2 === 0 )
	.take(4);
	.reduce((x, y) => {
      return x + y
	});
	
interval$.subscribe(
	next = > console.log(next),
	err => console.log(err),
	() => console.log('I am complete')
);
```



也可以给 reduce 和 scan 设置初始值，在第一个函数参数的后面写，可以给初始值设置为数组或者字典对象，例如

```javascript
const interval$ = Rx.Observable.interval(100)
	.filter(val => val % 2 === 0 )
	.take(4);
	.reduce((x, y) => {return [...x, y]}, []);
	
interval$.subscribe(
	next = > console.log(next),
	err => console.log(err),
	() => console.log('I am complete')
);
```



## 例子

写一个 debug.util.ts

```javascript
import {Observable} from 'rxjs/Observable;

declare module 'rxjs/Observable' {
  interface Observable<T> {
    debug: (...any) => Observable<T>;
  }
}

Observable.prototype.debug = function(message: string) {
  return this.do(
  	next => {
      if (!environment.production) {
        console.log(message, next)
      }
  	},
  	error => {
       if (!environment.production) {
          console.log(message, error)
        }
  	},
  	() => {
      	if (!environment.production) {
          console.log('Completed')
        }
  	}
  )
}
```



## 过滤类操作符，debounce， debounceTime

想象成滤波器或整流器，希望得到的元素是又一定时间间隔的，有一定规矩的

和 filter 有点像，但是这个是时间相关的

```Html
<input type="number" id="length" />
<input type="number" id="width" />
```

```javascript
const length = document.getElementById('length');
const width = document.getElementById('length');

// 不操作
const length$ = Rx.Observable.fromEvent(length, 'keyup').pluck('target', 'value');
length$.subscribe(val => {
  console.log(val);
})
```

debounce 可以接时间，单位是毫秒，也可以接Observable

```javascript
const length$ = Rx.Observable.fromEvent(length, 'keyup')
	.pluck('target', 'value')
	// 300ms 之内的动作不理会的
	.debounceTime(300);
length$.subscribe(val => {
  console.log(val);
})
```



```javascript
const length$ = Rx.Observable.fromEvent(length, 'keyup')
	.pluck('target', 'value')
	// 300ms 之内的动作不理会的
	.debounceTime(() => Rx.Observable.interval(300));
length$.subscribe(val => {
  console.log(val);
})
```



## 过滤类操作符： distinct， distinctUntilChanged

distinct 把流里边不一样的保留下来，重复的过滤掉，队列中所有元素不能重复。

distinctUtilChanged 跟前一个元素对比，一样的话，过滤掉，对劣质中不连续出现两个一样的元素。

Distinct 要小心使用，无尽序列一直监控会极大地消耗内存

```javascript
const length$ = Rx.Observable.fromEvent(length, 'keyup')
	.pluck('target', 'value')
	.distinct()
length$.subscribe(val => {
  console.log(val);
})
```



## 合并类操作符： merge，concat，startWith

对多个流，进行合并。

merge 两个流按照发射元素的时间顺序，合并成一个流。每个元素的时间点是不会变的。

concat 对接，第一个流排放到新的流里边之后，再把第二个流放排在第一个流的后面，放到新生成的流里面，要等待第一个流结束。

startWith 默认值，一开始就发射出来

```javascript
const length$ = Rx.Observable.fromEvent(length, 'keyup')
	.pluck('target', 'value')
const width$ = Rx.Observable.fromEvent(width, 'keyup')
	.pluck('target', 'value')

const merge$ = Rx.Observable.merge(width$, length$)
merge$.subscribe(val => {
  console.log(val);
})
```

startWith 赋初始值，避免流一开始没有值

```javascript
const width$ = Rx.Observable.fromEvent(width, 'keyup')
	.pluck('target', 'value')
const length$ = Rx.Observable.fromEvent(length, 'keyup')
	.pluck('target', 'value')

const merge$ = Rx.Observable.concat(width$, length$)
merge$.subscribe(val => {
  console.log(val);
})
```



```javascript
const width$ = Rx.Observable.fromEvent(width, 'keyup')
	.pluck('target', 'value')
const first$ = Rx.Observable.from([1, 2, 3, 4]).startWith(0)

const merge$ = Rx.Observable.concat(first$, width$)
merge$.subscribe(val => {
  console.log(val);
})
```





## 合并类操作符： combineLatest， withLatestFrom， zip

combineLatest 组成新流的两个或多个原始流中每个有新元素出现，会按照一定的规则，将其他流中最后的那个元素生成一个新元素放到新的流中。

```javascript
const width$ = Rx.Observable.fromEvent(width, 'keyup')
	.pluck('target', 'value')
const length$ = Rx.Observable.fromEvent(length, 'keyup')
	.pluck('target', 'value')

const merge$ = Rx.Observable.combineLatest(width$, length$,(l, w) => l * w)
merge$.subscribe(val => {
  console.log(val);
})
```

withLatestFrom，是一个流为主体，当这个流中产生新的元素的时候，去另外一个流中取元素。

```javascript
const width$ = Rx.Observable.fromEvent(width, 'keyup')
	.pluck('target', 'value')
const length$ = Rx.Observable.fromEvent(length, 'keyup')
	.pluck('target', 'value')

const merge$ = length$.withLatestFrom(width$);
merge$.subscribe(val => {
  console.log(val);
})
```



zip 是严格要求成对的，各自都要产生一个元素，才能在新的流中生成一个元素。我的第一个和你的第一个对齐，我的第二个和你的第二个对齐，最慢的流决定最终的速度

```javascript
const width$ = Rx.Observable.fromEvent(width, 'keyup')
	.pluck('target', 'value')
const length$ = Rx.Observable.fromEvent(length, 'keyup')
	.pluck('target', 'value')

const merge$ = Rx.Observable.zip(width$, length$,(l, w) => l * w)
merge$.subscribe(val => {
  console.log(val);
})
```



## 流的好处

#### 同步和异步统一的代码

```
function getDataP() {
  if (a) {
    return Promise.resolve(a)
  } else {
    return AJAX.get('a')
  }
}

getDataP().then(data => {
  // Promise 只有一个返回值，响应一次
  console.log(data)
})

service.on('task', data => {
  // render
})

service.getData()   // 加了这么一句来主动触发请求
```



```
function getDataO() {
  if (a) {
    return Observable.of(a)
  } else {
    return Observable.fromPromise(AJAX.get('a'))
  }
}

getDataO().subscribe(data => {
  // Observable 可以有多个返回值，响应多次
  // do render
  console.log(data)
})


const A$ = Observable.interval(1000)
const B$ = Observable.of(3)
const C$ = Observable.from([5, 6, 7])

const D$ = C$.toArray()
  .map(arr => arr.reduce((a, b) => a + b), 0)
const E$ = Observable.combineLatest(A$, B$, D$)
   .map(arr => arr.reduce((a, b) => a + b), 0)
```





在业务开发中，我们时常遇到这么一种场景：

已过滤排序的列表中加入一条新数据，要重新按照这条规则走一遍。

我用一个简单的类比来描述这件事：

> 每个进教室的同学都可以得到一颗糖

这句话表达了两个含义：

- 在这句断言产生之前，对于已经在教室里的每个人，都应当去给他们发一颗糖
- 在这句断言形成以后，再进入这个教室的每个人，都应当得到一颗糖

```
const final$ = source$.map(filterA).map(sorterA)
const source$ = start$.merge(patch$)
```

来源等于初始数据与新增数据的合并。

然后，实现出filterA和sorterA，就完成了整个这段业务逻辑的抽象定义。给start和patch分别进行定义，比如说，start是一个查询，而patch是一个推送，它就是可运行的了。最后，我们在final上添加一个订阅，整个过程就完美地映射到了界面上。