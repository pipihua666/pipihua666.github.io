---
title: sass常用指令和常用函数
date: "2021-02-20 21:45"
description: sass常用指令和常用函数,方便快速查找
---

<a name="3e525057"></a>

#### scss 常用指令（注意：scss 与 sass 的区别）

<br />[sass 官方文档](https://www.sass.hk/docs/)<br />

- 混合样式：`@mixin 名字(参数1，参数2...){...}`
- 取用混合样式：`@include 名字（@mixin的名字）`
- 继承样式：`@extend 需要继承的类、ID名、自定义的混合样式等的名字`
- 导入 scss 样式：`@import "scss文件名"`
- 条件控制指令：`@if 条件{...}`
- 循环控制指令：<br />`@for $var from <开始值> through <结束值> -----------包括结束值`<br />`@for $var from <开始值> to <结束值> ------------不包括结束值`
- 循环 List 项目的控制指令：`@each $var in $List{}`
- 条件判断循环：`@while 条件{...}`
- 用户自定义的函数：`@function 名称(参数1，参数2...){...}`
- 警告和错误的提示：<br />`@warn "..."------------------------在终端输出警告`<br />`@error "..."----------------在.css文件和终端输出错误`

<a name="b468e5e2"></a>

#### scss 常用函数

- 数字函数 ：

```
$theNumber:4.5;
```

```
  percentage($theNumber)：将一个不带单位的数转换成百分比值；  //450%

  round($theNumber)：将数值四舍五入，转换成一个最接近的整数；  //5

  ceil($theNumber)：将大于自己的小数转换成下一位整数；   //5

  floor($theNumber)：将一个数去除他的小数部分；  //4

  abs($theNumber)：返回一个数的绝对值；//4.5

  min($numbers…)：找出几个数值之间的最小值； //min(1,2,3) =1

  max($numbers…)：找出几个数值之间的最大值； //max(1,2,3)=3

  random(): 获取随机数   //随机数
```

- 字符串函数：

```
$theString:"Hello World";
```

```
to-upper-case($theString)：输出$theString的大写                 //HELLO WORLD

to-lower-case($theString)：输出$theString的小写                 //hello world

str-length($theString):输出$theString的长度                     //11

str-index($theString,"Hello"):输出$theString第二个参数的开始索引          //1

str-insert($theString,".com",12):在索引为12的地方为$theString插入".com" //"Hello World.com"
```

- 颜色函数

```

> 调整色相h的值

$base-color-hsl:hsl(0,100,50%);  //	red
$base-color:#ff0000;  //red

adjust-hue($base-color-hsl,137deg);        //#00ff48
adjust-hue($base-color,137deg);          //#00ff48
```

```

> 调整亮度l的值

$base-color:hsl(222,100%,50%);             //#004cff

$light-color:lighten($base-color,30%);        //#99b8ff（变亮）
$dark-color:darken($base-color,20%);         //#002e99（变暗）
```

```

> 调整饱和度s的值

$base-color:hsl(221,50%,50%);            //#4068bf

$saturate-color:saturate($color:$base-color,$amount:50%);          //#0051ff  (更饱和)
$desaturate-color:desatudate($color:$base-color,$amount:30%);        //667699 (更不饱和)
```

```

> 调整透明度a的值

$base-color:hsla(222,100%,50%,0.5);  //rgba(0, 77, 255, 0.5)

$fade-in-color:opacify($color: $primary-color, $amount: 0.3);  //rgba(0, 77, 255, 0.8)
$fase-out-color:transparentize($color: $primary-color, $amount: 0.2);  //rgba(0, 77, 255, 0.3)
```

- 列表函数（1px solid black：这样称为一个列表有三个项）

```
length(5px 10px)：列表长度     //2
length(5px 10px 0px 2px)  列表长度      //4

nth(5px 10px,1)：列表第一项   //5px
nth(5px 10px,2)：列表第二项   //10px

index(1px solid red,solid)：列表solid的项目索引  //2

append(5px 10px,5px)：列表中插入项目  //5px 10px 5px

join(5px 10px,5px 10px):列表之间连接   //5px 10px 5px 10px
join(5px 10px,5px 10px,参数)：列表之间条件连接  //参数为comma=5px,10px,5px,10px ---------参数为space=5px 10px 5px 10px
```

- Map 函数(Map 为带有键值对的列表)

```
$colors:(light:#ffffff,dark:#000000);   //定义map
```

```
length($colors)：map的长度  //2

map-get($colors,dark):取得键值为dark的值     //#000000

map-keys($colors)：取得所有键      //("light","dark")

map-values($colors) ：取得所有值      //(#ffffff,#000000)

map-has-key($colors,light)：map是否有light键       //true

map-merge($colors,(color-red:#ff0000)) ：插入键值对到map   //(light:#ffffff,dark:#000000,color-red:#ff0000)

map-remove($colors,light,dark) :从map删除键值对      //(color-red:#ff0000)
```
