---
title: 在React项目中，如何优雅的优化长列表
date: "2021-02-20 21:45"
description: react的长列表的优化方案-react-virtualized
---

<a name="TWtP7"></a>

#### 一、虚拟列表优化长列表的原理

<br />优化长列表的原理很简单，基本原理可以一句话概括：<br />_**用数组保存所有列表元素的位置，只渲染可视区内的列表元素，当可视区滚动时，根据滚动的 offset 大小以及所有列表元素的位置，计算在可视区应该渲染哪些元素。**_<br />\_<br />具体实现步骤如下所示：

1. 首先确定长列表所在父元素的大小，父元素的大小决定了可视区的宽和高
1. 确定长列表每一个列表元素的宽和高，同时初始的条件下计算好长列表每一个元素相对于父元素的位置，并用一个数组来保存所有列表元素的位置信息
1. 首次渲染时，只展示相对于父元素可视区内的子列表元素，在滚动时，根据父元素的滚动的 offset 重新计算应该在可视区内的子列表元素。这样保证了无论如何滚动，真实渲染出的 dom 节点只有可视区内的列表元素。
1. 假设可视区内能展示 5 个子列表元素，及时长列表总共有 1000 个元素，但是每时每刻，真实渲染出来的 dom 节点只有 5 个。
1. 补充说明，这种情况下，父元素一般使用 position：relative，子元素的定位一般使用：position：absolute 或 sticky

<a name="PXTg1"></a>

#### 二、通过 react-virtualized 来优化长列表

react-virtualized 的基础组件包含：

- Grid：用于优化构建任意网状的结构，传入一个二维的数组，渲染出类似棋盘的结构。
- List：List 是基于 Grid 来实现的，但是是一个维的列表，而不是网状。
- Table：Table 也是基于 Grid 来实现，表格具有固定的头部，并且可以在垂直方向上滚动
- Masonry：同样可以在水平方向，也可以在垂直方向滚动，不同于 Grid 的是可以自定义每个元素的大小，或者子元素的大小也可以是动态变化的
- Collection：类似于瀑布流的形式，同样可以水平和垂直方向滚动。

<br />值得注意的是这些基础组件都是继承于 React 中的 PureComponent，因此当 state 变化的时候，只会做一个浅比较来确定重新渲染与否 。<br />
<br />除了这几个基础组件外，react-virtualized 还提供了几个高阶组件，比如 ArrowKeyStepper 、AutoSizer、CellMeasurer、InfiniteLoader 等，本文具体介绍常用的 AutoSizer、CellMeasurer 和 InfiniteLoader。<br />

- AutoSizer：使用于一个子元素的情况，通过 AutoSizer 包含的子元素会根据父元素 Resize 的变化，自动调节该子元素的可视区的宽度和高度，同时调节的还有该子元素可视区真实渲染的 dom 元素的数目。(子元素会随父元素的长、宽自适应)
- CellMeasurer：这个高阶组件可以动态的改变子元素的高度，适用于提前不知道长列表中每一个子元素高度的情况。（会损耗性能，不建议使用）
- InfiniteLoader：这个高阶组件用于 Table 或者 List 的无限滚动，适用于滚动时异步请求等情况。（滚动加载）

<br />作者：yuxiaoliang<br />链接：[https://juejin.cn/post/6844903729460674567](https://juejin.cn/post/6844903729460674567)<br />来源：掘金<br />著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。<br />
<br />

<a name="t7LD9"></a>

#### 三、Vlist 的实际使用

```jsx
<List loading={loading} bordered>
  {" "}
  // antd的List组件
  <div style={{ height: 420 }}>
    {" "}
    // vlist父元素的高度
    <AutoSizer>
      {" "}
      // 可根据父元素的高度动态计算出列表项的高度
      {({ width, height }) => (
        <VList
          width={width} // 列表的宽度
          height={height} // 列表的高度
          rowCount={pocList.length} // 列表项数
          overscanRowCount={7} // 不在视窗范围上下现实的列表项数
          rowHeight={60} // 列表项的高
          rowRenderer={this.renderItem} // 列表项渲染方式
          noRowsRenderer={() => (
            // 空数据的渲染方式
            <Empty
              style={{
                position: "absolute",
                top: "50%",
                left: "50%",
                transform: "translate(-60%,-50%)",
              }}
            />
          )}
        />
      )}
    </AutoSizer>
  </div>
</List>
```

```jsx
// key：数组行中的唯一值
// style: vlist的列表项的定位样式和默认样式
// parent: 参考父级Grid元素（使用CellMeasurer组件要注意：该组件会不断估算每个
// 列表项的最佳高度，所以会带来一定的性能损耗，个人建议不要使用该组件，可以像我一样将
// 列表项的高度rowHeight写死）
// index: 被渲染数据的index索引
renderItem = ({ index, key, style: styles }) => {
  const { pocList } = this.state
  const name = pocList[index].name || ""
  return (
    <List.Item style={styles} key={key}>
      <Tooltip title={name}>
        <Checkbox value={pocList[index].poc_id}>
          {React.createElement(
            "span",
            null,
            ...this.highLightValue(ellipsis(pocList[index].name, 46))
          )}
        </Checkbox>
      </Tooltip>
    </List.Item>
  )
}
```

<br />其他优秀的社区库：

1. [阿里的 ant-table 的丰富 api 虚拟表格](https://ali-react-table.js.org/docs/)
1. [兼容性比较好的虚拟表格](https://github.com/ctq123/ant-virtual-table)
