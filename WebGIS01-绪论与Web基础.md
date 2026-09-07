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






