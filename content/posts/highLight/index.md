---
title: react 高亮显示关键词
path: /gatsby-antd
tags: ["React"]
featuredImage: "./tree.png"
excerpt: react 高亮显示关键词，不用库，原生显示
created: "2021-02-20 21:45"
updated: 2021-04-11
---

## 正则表达式转义，你会用到的

```javascript
// 正则特殊字符转义
export const escapeRegexp = (string = "") =>
  String(string).replace(/[\\^$*+?.()|[\]{}]/g, "\\$&")
```

## 针对关键词进行高亮

```javascript
import { escapeRegexp } from "Utils/string"

// 针对关键词进行高亮
// 正则匹配有可能会涉及到特定字符
// targetVal 要高亮的目标字符串
// 高亮关键词
export function hightLight(targetVal, highLightValue) {
  let res = ""
  let hasHighLight = false
  hasHighLight = highLightValue.some(value => {
    if (value) {
      const reg = new RegExp(escapeRegexp(value), "i") // 不区分大小写
      if (reg.test(targetVal)) {
        return true
      }
      return false
    }
  })
  if (hasHighLight) {
    res = `<span style="color:black;background:#f6e58d">${targetVal}</span>`
  } else {
    res = `<span>${targetVal}</span>`
  }
  return res
}
```

## 针对字符串进行高亮

```javascript
// 根据搜索条件替换字符串关键词高亮标签
// 针对长字符串的高亮显示
// 返回的数组可以用React.createElement('pre',null,stringReplaceMark(targetVal,highLighValue))
stringReplaceMark = (targetVal, highLightValue) => {
  const replaceVal = []
  if (Array.isArray(highLightValue) && highLightValue.length > 0) {
    highLightValue.forEach(value => {
      const reg = new RegExp(escapeRegexp(value), "i")
      // 高亮替换的字符
      const isReplaced = targetVal.match(reg) ? targetVal.match(reg)[0] : ""
      // 将字符串从高亮的位置切割成数组，好用上面的isReplaced插入到数组的分割空位中
      const replaceArr = targetVal.split(reg)
      const length = Array.isArray(replaceArr) && replaceArr.length
      replaceArr.forEach((item, index) => {
        replaceVal.push(item)
        // 在空位插入高亮标签，不在最后插入
        if (index !== length - 1 && isReplaced) {
          replaceVal.push(
            <span style={{ color: "black", background: "#f6e58d" }}>
              {isReplaced}
            </span>
          )
        }
      })
    })
  } else {
    replaceVal.push(targetVal)
  }
  return replaceVal
}
```

<br />推荐：<br />

- [正则转义相关地址](https://github.com/benjamingr/RegExp.escape)
