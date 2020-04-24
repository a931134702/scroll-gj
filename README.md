# scroll-gj

> A Vue.js project

## Usage

``` bash
# install dependencies
npm install scroll-gj
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
| loadmore | Boolean | true   | 是否开启上拉                                    |
| innerpad | Number  | 0      | 该盒子padding值                                 |
| backTop  | Boolean | false  | 是否开启回到页面顶部                            |

## event

| 事件名   | 描述                          | 回调参数 |
| -------- | ----------------------------- | -------- |
| pulldown | 下拉事件，refresh为true时生效 | null     |
| pullUp   | 上拉触底                      | null     |

