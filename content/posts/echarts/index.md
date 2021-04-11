---
title: 前端大屏可视化
path: /visualization
tags: ["Echarts"]
featuredImage: "./echarts.jpg"
excerpt: 从零开始的基于echarts的可视化大屏开发.
created: 2021-02-20 21:45
updated: 2021-04-11
---

## 技术栈

> 可视化技术栈：react，echarts，react-for-echarts，sass

<a name="3gFyR"></a>

## 开始布局啦！

---

1. 设置项目的最小宽度 min-width,确保项目不会过于小导致布局混乱
1. 横向自适应宽度：采用 ant-design 的栅栏布局
1. 纵向自适应高度，我一开始采用的是 rem 的适应方案，可是发现有如下问题
   - rem 方案使用的是[这篇文章的方案](https://www.njleonzhang.com/2018/08/15/flexible-pc-full-screen.html)，大家可以看看工程运用那里，我关于 rem 的计算方案并不是采用的该文章的方法，如果你使用的是 vscode，那有款 cssrem 的插件很适合你
   - 会修改根元素的 font-size，容易导致项目的其他页面字体大小有问题（可通过修改 document.body.fontSize 来解决）
   - 如果放在大屏上，比如分辨率为 1920\*1080 以上，屏幕下方会留白（对 rem 方案的不熟练）

- 于是，我采用的是 vh 的方案，查看[caniuse](https://caniuse.com/?search=vh)，发现兼容性还不错

<a name="IF4ph"></a>

## 可视化组件的背景图片

---

### svg 作为背景图片带来的坑

一开始 ued 给我的切的 svg，也没做过可视化，不太懂，也是欣然接受并使用的 svg 作为背景图片，可是发现调整屏幕的分辨率，组件自适应了，但是背景图却有问题，<b>高度和宽度一致是按照一定比例来缩放的。后来发现原来这是 svg 的特性，怪不得很适合用来做 icon。

### 于是改变背景图方案

使用了[svg-to-png](https://cloudconvert.com/svg-to-png)，采用 png 来设置背景图片，并通过调整 css 来实现背景图片覆盖整个组件,完美的实现了背景图跟随组件自适应。

```css
@mixin background($urlImg) {
  background-image: url($urlImg);
  background-size: 100% 100%;
  background-repeat: no-repeat;
}
```

<a name="z2ibq"></a>

## 世界地图并实现地图下钻

---

1. 首先得把世界地图和各个国家的地图找到，这是一个很庞大的工作。而且以前的地图和现在的地图并非一致，请合理使用（毕竟地球的板块是在不断的运动中的）。

   - [中国->国、省、市地图 Json](http://datav.aliyun.com/tools/atlas/#&lat=30.332329214580188&lng=106.72278672066881&zoom=3.5)（由于有 datav 官方维护，地图数据新）
   - [中国->省、市、区/县地图 Json](https://hxkj.vip/demo/echartsMap/)（地图数据新）
   - [世界->各个国家的地图 Json（包括世界地图）](https://github.com/pissang/starbucks/tree/gh-pages/json)(地图数据旧)
   - [世界->各个国家的地图 Json（不包括世界地图）](https://geojson-maps.ash.ms/)（地图数据较新）
   - [全世界地图数据集](https://img.hcharts.cn/mapdata/)（只更新到了 2015 年的数据）
   - [当你找到 json 的地图，放上来看看是否一致](http://geojson.io/#map=2/20.0/0.0)（权威地图，地图数据新）
   - 如果不懂 geoJson 规范，可看[GeoJSON 格式规范说明](https://www.oschina.net/translate/geojson-spec)

2. 找到了地图 json，下钻实现

```jsx
// 获取地图json注册地图
// map为json所对应的名称（比如：世界地图为world,中国地图为China）
registerMap = async map => {
  // 由于json是存在静态资源中的
  const path_env =
    process.env.NODE_ENV === "development"
      ? `/public/json/${map}.json`
      : `/assets/public/json/${map}.json`
  await axios(path_env)
    .then(res => {
      echarts.registerMap("world", res.data)
    })
    .finally(() => {
      // 使用的是echarts-for-react，this.echartsReact获取到的是该图表实例
      this.echartsReact.dispatchAction({
        type: "restore",
      })
    })
}
```

<a name="OmSJq"></a>

## 词云

> 我采用的库是[TagCloud](https://github.com/mcc108/TagCloud#readme),同事推荐的一个小巧，可定制的库，挺好用的。

<a name="vtI9Q"></a>

## 自定义一个时钟

> 1. 使用一个组件来显示时钟，保证时钟的渲染在该组件内进行，不影响其他组件
> 1. 字体样式采用免费可商用的[「优设标题黑」](https://www.uisdc.com/uisdc-first-free-font)

```jsx
import React, { PureComponent } from "react"
import dayjs from "dayjs"

export default class DataVisual extends PureComponent {
  state = {
    time: dayjs().format("YYYY.MM.DD HH:mm:ss"),
  }

  timer = null

  componentDidMount() {
    this.timer = setInterval(() => {
      this.setState({
        time: dayjs().format("YYYY.MM.DD HH:mm:ss"),
      })
    })
  }

  componentWillUnmount() {
    clearInterval(this.timer)
  }

  render() {
    return <div style={{ display: "inline-block" }}>{this.state.time}</div>
  }
}
```

<a name="2QaXU"></a>

## 使用@font-face

> [MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@font-face)

```css
@font-face {
  font-family: electronicNumber;
  src: url("./font/DS-DIGI-1.ttf"), url("./font//DS-DIGIB-2.ttf"),
    url("./font//DS-DIGII-3.ttf"), url("./font/DS-DIGIT-4.ttf");
}

.myFontFace {
  font-family: electronicNumber;
}
```

<a name="suEmG"></a>

## 使用@media（设置响应式）

> [MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@media)

```css
@mixin screenMedia($fontSize) {
  // 如果小于1440px，则font-size生效
  // 同理min-width:1440px，如果大于1440px，则font-size生效
  @media screen and (max-width: 1440px) {
    font-size: $fontSize;
  }
}

.mymMedia {
  @include screenMedia(20px);
}
```

## 最后

> 文章针对前端可视化的大屏展示，根据需求来完善文章，欢迎各位大佬评论哦。<br/>
> 如果有需要修改文章的内容，我会改进。<br/>
> 附上一张性能图片，没做这方面的优化。<br/> > [使用 chrome performance 查看页面性能](https://juejin.im/post/6844903552070975495) <br> > [著名的 RAIL model](https://web.dev/rail/#goals-and-guidelines)
