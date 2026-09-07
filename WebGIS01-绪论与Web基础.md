WebGIS就是通过浏览器Browser使用地理信息系统。不仅能显示地图，还能将位置与业务信息联系，回答带有空间条件的问题。
[^1]一个典型的WebGIS应用

[^1]: 用户在浏览器中操作
	        ↓
	前端应用：展示地图、接收点击、显示查询结果
	        ↓
	服务端：提供地图数据、处理业务或空间分析请求
	        ↓
	数据：道路、建筑、影像，以及名称、用途等属性
并不是每次操作都要经过完整流程，例如缩放地图主要由前端地图引擎处理；简单的空间计算也可以在浏览器中完成。
项目已有的前端应用基础：

| 已有部分           | WebGIS中的作用        |
| -------------- | ----------------- |
| Vue            | 组织地图页面、侧边栏和信息面板   |
| Ant Design Vue | 提供按钮、表单、表格等界面组件   |
| Vue Router     | 切换地图首页、数据管理等页面    |
| Pinia          | 保存选中图层、当前要素等共享状态  |
| Axios          | 向服务端请求数据          |
| 尚未接入的地图引擎      | 绘制地图，处理缩放、平移和地图点击 |
当前的项目的前端架构包括：
[^2]

[^2]: webgis-app/
	├── public/             静态资源
	├── src/
	│   ├── api/request.js  Axios 请求封装
	│   ├── assets/         图片等资源
	│   ├── components/     公共组件
	│   ├── router/         路由配置
	│   ├── store/          Pinia 状态管理入口
	│   ├── views/home/     首页
	│   ├── App.vue         路由视图入口
	│   └── main.js         应用初始化
	├── .env*               环境配置
	├── .eslintrc.cjs        代码检查配置
	├── .prettierrc          格式化配置
	├── vite.config.js      构建与开发配置
	└── package.json        依赖与脚本
技术栈为**Vue 3 + Vite + JavaScript + Ant Design Vue + Pinia + Vue Router + Axios**。已接入路由、状态管理、组件自动导入，以及携带 Token 的请求封装；首页目前是按钮和接口地址测试页面。
程序是怎么运行的，打开网页时，文件之间的配合：
[^3]

[^3]: index.html       提供网页和挂载位置
	    ↓
	src/main.js     创建并启动 Vue 应用
	    ↓
	src/App.vue     提供页面的整体入口
	    ↓
	src/router/index.js   根据网址选择页面
	    ↓
	src/views/home/index.vue   显示首页内容
 `main.js` 是：
```
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';
import pinia from './store';

const app = createApp(App);

app.use(pinia);
app.use(router);

app.mount('#app');
```
代码理解（回顾基础）：

| 写法                  | 含义                  | 在这里做什么                        |
| ------------------- | ------------------- | ----------------------------- |
| `import … from …`   | 从其他模块引入功能           | 引入 Vue、根组件、路由和状态管理            |
| `const app = …`     | 声明一个变量，保存结果         | 保存创建好的应用实例                    |
| `createApp(App)`    | 调用函数，将 `App` 作为参数传入 | 以 `App.vue` 为根组件创建应用          |
| `app.mount('#app')` | 调用对象提供的方法           | 把应用挂载到 HTML 中 `id="app"` 的元素上 |
`.vue`文件。Vue把一个界面组件通常分成三部分：

```
<template>
  <!-- 界面：放按钮、文字、地图容器等 -->
</template>

<script setup>
// 逻辑：保存数据、响应点击、调用地图功能等`
</script>

<style scoped>
/* 样式：设置大小、颜色、布局等 */
</style>
```
<template>
  <!-- 界面：放按钮、文字、地图容器等 -->
</template>
如何写一个Web页面：
- **template**：放一个“缓冲区分析”按钮和地图容器。
- **script**：读取缓冲距离，调用分析功能，把结果加到地图上。
- **style**：让地图占满页面，把工具栏放到合适的位置。
WebGIS开发的常用逻辑：**用变量保存数据，用函数处理数据，通过点击触发函数。**
假设页面上有一个“查询周边设施”按钮，程序需要保存用户选择的位置和查询半径：
```
const center = [116.40, 39.90];
let radius = 500;
```
这里涉及两个声明变量的关键字：

- `const`：这个变量不能再被赋成另一个值。
- `let`：这个变量后续可以重新赋值。

例如，用户把查询半径改为 1000 米：
```
radius = 1000; // 可以，因为 radius 用 let 声明
```

`=` 在 JavaScript 中表示**赋值**：将右边的结果存进左边的变量。判断是否相等通常用 `===`：
```
radius === 1000; // 得到 true，也就是“成立”
```
`center` 使用方括号 `[]`，表示一个**数组**。数组按顺序保存多个值，下标从 `0` 开始：
```
center[0]; // 116.40
center[1]; // 39.90
```
如果我们还需要保存设施的名称、位置和类型，用**对象**会更清楚：
```
const facility = {
  name: '图书馆',
  coordinates: [116.40, 39.90],
  category: '学习设施'
};
```
对象使用 `{}`，里面是“属性名: 属性值”。通过点号读取属性：
```
facility.name;           // '图书馆'
facility.coordinates;   // [116.40, 39.90]
facility.coordinates[0]; // 116.40
```
注意，`const` 限制的是**重新给变量赋值**，并不禁止修改对象内部的属性：
```
facility.name = '新图书馆'; // 可以
```
有了数据，再写一个处理数据的函数。我们先做最简单的事：生成查询说明。
```
function describeQuery(place, distance) {
  return `查询${place.name}周围 ${distance} 米内的设施`;
}
```
将代码逐步拆开看：
|代码|含义|
|---|---|
|`function describeQuery`|定义一个名为 `describeQuery` 的函数|
|`(place, distance)`|接收两个参数，作为函数内部使用的变量|
|`{ ... }`|函数执行的代码|
|`return`|把结果返回给调用者|
|反引号中的 `${...}`|把变量或表达式的值插入字符串|