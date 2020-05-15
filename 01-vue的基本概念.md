# 01-VUE的基本概念

1. VUE是什么?

   是一个构建用户界面的渐进式框架, 关注视图, 具有完整的开发维护工具链脚手架, 能完成复杂的SPA应用, 基于`node` 

   提供了: virtual DOM , 响应式, 组件化, 核心库, 也有相关方库(关注路由vue-router, 状态管理vuex)

   渲染效率:  组件的依赖是在渲染过程中自动追踪的，所以系统能精确知晓哪个组件确实需要被重渲染, 

   模板: 提供了渲染函数甚至是jsx, 但是推荐用模板

   CSS: CSS的控制可以集中在同一文件中, 是组件的一部分, 支持样式作用域

   状态管理: vuex的状态管理简单, 易上手

   调试: 有第三方插件Vue Devtools 更方便审查调试vue的应用

2. VUE的引入

   1. 直接script引入 CDN

   ```html
   <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
   ```

   2. npm引入

   ```shell
   npm i vue
   ```

   3. 命令行工具(cli)

      官方cli,快速搭建SPA的脚手架, 具有 热重载 lint校验

   ```shell
   npm i -g @vue/cli
   ```

3. vue的三种版本

   vue.js 完整版 包含编译器和运行时版本

    vue.runtime.js 只包含运行时版本

    vue.min.js  完整版 压缩版

4. 实例

   Vue 构造函数, 可实例vue, 实例时传入一个vue的配置参数, 

   html

   ```html
   <div id="app">{{msg}}</div>
   ```

   js

   ```js
   new Vue({
     el: "#app", // 实例
     data(){ // 数据
       return {
         msg: "hello world"
       }
     }
   })
   ```

   