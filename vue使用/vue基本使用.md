## vue基本使用

#### 常用指令

```vue
<template>
    <div>
        <p>文本插值 {{message}}</p>
        <p>JS 表达式 {{ flag ? 'yes' : 'no' }} （只能是表达式，不能是 js 语句）</p>

        <p :id="dynamicId">动态属性 id</p>

        <hr/>
        <p v-html="rawHtml">
            <span>有 xss 风险</span>
            <span>【注意】使用 v-html 之后，将会覆盖子元素</span>
        </p>
    </div>
</template>

<script>
export default {
    data() {
        return {
            message: 'hello vue',
            flag: true,
            rawHtml: '指令 - 原始 html <b>加粗</b> <i>斜体</i>',
            dynamicId: `id-${Date.now()}`
        }
    }
}
</script>
```

#### 动态样式

```vue
<template>
    <div>
        <p :class="{ black: isBlack, yellow: isYellow }">使用 class</p>
        <p :class="[black, yellow]">使用 class （数组）</p>
        <p :style="styleData">使用 style</p>
    </div>
</template>

<script>
export default {
    data() {
        return {
            isBlack: true,
            isYellow: true,

            black: 'black',
            yellow: 'yellow',

            styleData: {
                fontSize: '40px', // 转换为驼峰式
                color: 'red',
                backgroundColor: '#ccc' // 转换为驼峰式
            }
        }
    }
}
</script>

<style scoped>
    .black {
        background-color: #999;
    }
    .yellow {
        color: yellow;
    }
</style>
```

#### 方法、计算属性和监听器

```vue
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>lesson 8</title>
  <script src="https://unpkg.com/vue@next"></script>
</head>
<body>
  <div id="root"></div>
</body>
<script>
  // data & methods & computed & watcher
  // computed 和 method 都能实现的一个功能，建议使用 computed，因为有缓存
  // computed 和 watcher 都能实现的功能，建议使用 computed 因为更加简洁
  const app = Vue.createApp({
    data() {
      return {
        message: "hello world",
        count: 2,
        price: 5,
        newTotal: 10,
      }
    },
    watch: {
      // price 发生变化时，函数会执行
      price(current, prev) {
        this.newTotal = current * this.count;
      }
    },
    computed: {
      // 当计算属性依赖的内容发生变更时，才会重新执行计算
      total() {
        return Date.now() + this.count;
        // return this.count * this.price
      }
    },
    methods: {
      formatString(string) {
        return string.toUpperCase();
      },
      // 只要页面重新渲染，才会重新计算
      getTotal() {
        return Date.now();
        // return this.count * this.price;
      },
    },
    template: `
     <div> {{message}} {{total}} </div>
    `
  });
  const vm = app.mount('#root');
</script>
</html>
```

#### 条件渲染

```vue
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>lesson 10</title>
  <script src="https://unpkg.com/vue@next"></script>
</head>
<body>
  <div id="root"></div>
</body>
<script>
  const app = Vue.createApp({
    data() {
      return {
        show: false,
        conditionOne: false,
        conditionTwo: true
      }
    },
      //当需要频繁切换展示与隐藏时，使用v-show
    template: `
      <div v-if="show">Hello World</div>

      <div v-if="conditionOne">if</div>
      <div v-else-if="conditionTwo">elseif</div>
      <div v-else>else</div>

      <div v-show="show">Bye World</div>
    `
  });

  const vm = app.mount('#root');
</script>
</html>
```

#### 列表渲染

```vue
<template>
    <div>
        <p>遍历数组</p>
        <ul>
            <li v-for="(item, index) in listArr" :key="item.id">
                {{index}} - {{item.id}} - {{item.title}}
            </li>
        </ul>

        <p>遍历对象</p>
        <ul >
            <li v-for="(val, key, index) in listObj" :key="key">
                {{index}} - {{key}} -  {{val.title}}
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    data() {
        return {
            flag: false,
            listArr: [
                { id: 'a', title: '标题1' }, // 数据结构中，最好有 id ，方便使用 key
                { id: 'b', title: '标题2' },
                { id: 'c', title: '标题3' }
            ],
            listObj: {
                a: { title: '标题1' },
                b: { title: '标题2' },
                c: { title: '标题3' },
            }
        }
    }
}
</script>
```

#### 表单

```vue
<template>
    <div>
        <p>输入框: {{name}}</p>
        <input type="text" v-model.trim="name"/>
        <input type="text" v-model.lazy="name"/>
        <input type="text" v-model.number="age"/>

        <p>多行文本: {{desc}}</p>
        <textarea v-model="desc"></textarea>
        <!-- 注意，<textarea>{{desc}}</textarea> 是不允许的！！！ -->

        <p>复选框 {{checked}}</p>
        <input type="checkbox" v-model="checked"/>

        <p>多个复选框 {{checkedNames}}</p>
        <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
        <label for="jack">Jack</label>
        <input type="checkbox" id="john" value="John" v-model="checkedNames">
        <label for="john">John</label>
        <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
        <label for="mike">Mike</label>

        <p>单选 {{gender}}</p>
        <input type="radio" id="male" value="male" v-model="gender"/>
        <label for="male">男</label>
        <input type="radio" id="female" value="female" v-model="gender"/>
        <label for="female">女</label>

        <p>下拉列表选择 {{selected}}</p>
        <select v-model="selected">
            <option disabled value="">请选择</option>
            <option>A</option>
            <option>B</option>
            <option>C</option>
        </select>

        <p>下拉列表选择（多选） {{selectedList}}</p>
        <select v-model="selectedList" multiple>
            <option disabled value="">请选择</option>
            <option>A</option>
            <option>B</option>
            <option>C</option>
        </select>
    </div>
</template>

<script>
export default {
    data() {
        return {
            name: 'namu',
            age: 18,
            desc: '自我介绍',

            checked: true,
            checkedNames: [],

            gender: 'male',

            selected: '',
            selectedList: []
        }
    }
}
</script>
```

