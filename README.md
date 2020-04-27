# scroll-gj

> A Vue.js project

## Usage

``` bash
# install dependencies
npm install scroll-gj
yarn add scroll-gj
```

```js
// main.js
import scrollGj from 'scroll-gj'
Vue.use(scrollGj)
```

```html
// xx.vue
<scroll-gj>
    .....
</scroll-gj>
```

## prop

| 参数     | 类型    | 默认值 | 描述                                            |
| -------- | ------- | ------ | ----------------------------------------------- |
| listInfo | Object  | null   | listlength:Number，total:Number，finish:Boolean |
| refresh  | Boolean | false  | 是否开启下拉                                    |
| loadMore | Boolean | true   | 是否开启上拉                                    |
| innerPad | Number  | 0      | 该盒子padding值                                 |
| backTop  | Boolean | false  | 是否开启回到页面顶部                            |
| fullWind | Boolean | true   | 是否全窗口滚动    注：Tips                      |

## event

| 事件名   | 描述                          | 回调参数 |
| -------- | ----------------------------- | -------- |
| pullDown | 下拉事件，refresh为true时生效 | null     |
| pullUp   | 上拉触底                      | null     |

## Tips

该组件默认全窗口滚动，若将fullWind设为false可局部滚动，但需要套一层固定高度的父盒子作为容器