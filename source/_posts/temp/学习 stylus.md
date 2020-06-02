---
title: 学习 stylus
date: 
categories:
- 临时
tags:
- 临时
---

#### package.json 中使用 stylus
```css
scripts: {
  'stylus': 'mkdir css & stylus -m -w styl/ -o css/'
}

```

#### gulp 中使用 stylus
```bash
npm i gulp-stylus gulp-clean-css -D
```

gulpfile.js
``` javascript
gulp.task('stylus', () => {
  return gulp.src('./styl/..')
    .pipe(stylus({
      compress: true,
      'include css': true
    }))
    .pipe(cleanCSS({
      level: 2,
      rebaseTo: 'css/'
    }))
    .pipe(gulp.dest(DEST + 'css/));
});
```
#### ^[N..M] 选择器
```css
a
  b
    c
      d 
        font-size 14px
        .abc ^[-1..-1]:hover
          color red
```

#### 引用属性值
```css
p
  margin 10px
  padding (@margin / 2)
```

场景： 已知宽高绝对居中

```css
#logo
position absolute
top 50%
left 50%
width 150px
height 80px
margin-left -(@width / 2)
margin-top -(@height / 2)
```

#### 循环
属性名里边用 n 要带上{}，属性值里边直接用
```css
for n in 1..10
  .col-{n}
    width 10% * n
```

使用场景：日历组件
```css
for n in (0..6)
  saturday = 8 - n
  sunday = 9 - n
  &.empty-{n}
    flex n n 14.285714% * n
    min-width 14.285714% * n
    max-width 100%

    ~:nth-child(7n + {saturday}),
    ~:nth-child(7n + {sunday})
      color orange
```

使用场景：spin 页面加载
```css
.bar
  animation suofang 1s ease-in-out infinite
  for n in 1..5
    &:nth-child({n})
      animation-delay .1s * n

@keyframe suofang
  0%
    transform scale(.2)
  50%
    transform scale(1)
  100%
    transform scale(.2)
```

#### 条件判断
```css
$need-support-ie = true
body
  if $need-support-ie 
    padding 5px
  else
    margin 5px
```

#### mixin & functions
```css
border-radius(n)
  -webkit-border-radius n
  -moz-border-radius n
  border-radius n

form input[type=button]
  border-radius(5px) // function
  border-radius 5px // mixin
```