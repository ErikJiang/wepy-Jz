## 小程序开发说明
本开发环境开启后将实时打包  将src下的文件自动打包到dist文件夹中，项目中使用less预编译 主要是嵌套使用 避免样式混肴

> 该项目中没有引入状态管理；所以对于业务逻辑比较深的页面组件尽量细化，组件开头注明组件的作用

**重要（组件头部加注释说明组件的作用）**

代码高亮 查看 http://blog.csdn.net/res_min  博客上有介绍 主流编辑器上有具体说明
> 开发编译

`
wepy build --watch
`


```
├── dist                   微信开发者工具指定的目录
├── node_modules           依赖
├── src                    代码编写的目录
|   ├── components         组件文件夹（非完整页面）
|   |   ├── footer-component.wpy      可复用组件 footer-component
|   |   └── headrt-component.wpy      可复用组件 headrt-component
|   |   ......                        其他组件
|   ├── pages              页面文件夹（完整页面）
|   |   ├── index.wpy      页面 index
|   |   └── page.wpy       页面 page
|   |   ......             其他页面
|   └── app.wpy            小程序配置项（全局样式配置、声明钩子等）
└── package.json           package 配置
```

## 注意事项

组件通信与交互

wepy.component基类提供三个方法$broadcast，$emit，$invoke，因此任一页面或任一组件都可以调用上述三种方法实现通信与交互，如：
```js
$this.$emit('some-event', 1, 2, 3, 4);
```
组件的事件监听需要写在events属性下。 如：

```js
import wepy form 'wepy';
export default class Com extends wepy.component {

    components = {};           //挂载组件

    data = {};                 //数据
    methods = {};              //相应事件

    events = {                 //事件监听
        'some-event': (p1, p2, p3, $event) => {
               console.log(`${this.name} receive ${$event.name} from ${$event.source.name}`);
        }
    };
    // Other properties
}
```

$invoke是一个组件对另一个组件的直接调用，通过传入的组件路径找到相应组件，然后再调用其方法。 如果想在Page_Index中调用组件A的某个方法：
```
this.$invoke('ComA', 'someMethod', 'someArgs');
```

如果想在组件A中调用组件G的某个方法：

```
this.$invoke('./../ComB/ComG', 'someMethod', 'someArgs');
```

## 组件内部的方法使用（全）

**列出的内容在项目中是经常用到的**

1. 不同组件引用
```js
<template>
    <child></child>
</template>
<script>
    import wepy form 'wepy';
    import Child from './coms/child';
    export default class Index extends wepy.component {
        components = {
            child: Child
        };
    }
</script>
```

2. 循环列表组件引用


```js
<template>
    <repeat for="{{list}}" key="index" index="index" item="item">
        <child :item="item"></child>
    </repeat>
</template>
<script>
    import wepy form 'wepy';
    import Child from './coms/child';
    export default class Index extends wepy.component {
        components = {
            child: Child
        };
        data = {
            list: [{id: 1, title: 'title1'}, {id: 2, title: 'title2'}]
        }
    }
</script>
```

3. computed 计算属性


```js
data = {
    a: 1
};

computed = {
    aPlus () {
        return this.a + 1;
    }
}
```

4. Props 传值


```js
<child title="mytitle"></child>

// child.wpy
props = {
    title: String
};

onLoad () {
    console.log(this.title); // mytitle
}

```

