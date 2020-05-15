# 02- vue的实例对象 与 生命周期

1. new Vue()会生成一个vue实例对象, 一般用vm表示(ViewModel) 创建实例的时候会传入一个参数options, 用来描述这个view实例的特点以及你想要的行为.

2. Vue应用由一个通过new Vue创建的 根Vue实例, 以及嵌套组件树构成

   ```html
   <div id="app">
   	{{msg}}
   </div>
   <script>
   	let vm = new Vue({
       el: "#app"
     })
   </script>
   ```

3. Vue实例的数据与方法

   在Vue的options中有一个属性data, 接受一个数据对象, 数据是vm的内置数据

   数据是响应式的, 当数据发生改变时 vm也会发生改变, vm改变时 数据也会发生改变

   ```js
   let data = {
     a: 1
   }
   let vm = new Vue({
     data: data
   })
   vm.a = 2
   data.a // 2 跟随改变
   data.a = 3
   vm.a // 3 // 跟随改变
   ```

   所以, 需要响应的数据都应该设置到data中, 给定初始值, 数据就会响应变化

   data数据建议使用函数返回对象写法

   ```js
   let vm = new Vue({
     data: function (){
       return {
         a: 1
       }
     }
   })
   ```

   

4. vm的自带的一些方法:  以$开头的一些方法,记录信息或者执行操作

   ```js
   let data = {
     a: 1
   }
   let vm = new Vue({
     data: data
   })
   
   vm.$data === data // 数据对象
   vm.$el // dom真实节点
   vm.$props // 组件接受的属性表
   // ... 还有很多
   ```

5. vm的生命周期, 描述了vue实例创建经历的阶段

   创建阶段

      1. 实例初始化的一些属性, 基础状态, 记录组件的位置, 事件系统, 初始化渲染工具
      2. 创建之前 beforeCreate 作为初始化实例需要

         * 数据, 事件, 数据监听之前, 实例初始化之前
         * 用法: 插件引入 路由跳转判断
      3. 创建注入(inject)选项
      4. 初始化实例状态(props, data, methods, computed, watch)等
      5. 创建实例之后 created 
         * 数据观测, 事件回调 完成, 
         * 实例节点未初始化
         * this.$el 不存在
         * 用法: 数据修改, 和dom无关的操作
   6. 挂载dom节点, 此时是虚拟节点, 真实内容尚未替换
   7. 判断的渲染方法, 准备渲染函数
   8. 挂载实例之前beforeMount
      * this.$el已经存在, 但是是尚未更新模板的dom
      * 修改数据, 对dom进行维护等
   9. 渲染真实dom 替换尚未更新的模板的dom

   ```html
   <div ref="div" class="app">{{a}}</div>
   <script>
   	new Vue({
       el: "#app",
       data: {
         a: 1
       },
       beforeCreate(){
         console.log(this.$data) // undefined
         console.log(this.$el)// undefined
       },
       created(){
         console.log(this.$data) // {a: 1}
         console.log(this.$el) // undefined
       },
       beforeMount(){
         console.log(this.$el) // <div ref="div" class="app">{{a}}</div> 尚未更新
         console.log(this.$refs.div) // undefined 尚未挂载完成
       },
       mounted(){
         console.log(this.$el) // <div ref="div" class="app">1</div>
         console.log(this.$refs.div) // <div ref="div" class="app">1</div> 已经挂载完成了
       }
     })
   </script>
   ```

   更新阶段

   1. beforeUpdate 数据更新之前影响dom之前
      * 可以对修改之前的dom进行操作, 比如删除定时器删除事件等
   2. updated 数据更新之后影响dom之后
      * 可以在修改数据之后进行操作, 比如获取修改之后的状态
      * 不建议在这修改数据状态, 会无限递归

   激活阶段

   1. activated 在keep-alive组件处于获得激活状态时调用
      * 控制激活状态, 播放动画等
   2. deactived 在keep-alvie组件处于失去激活状态时调用
      * 注销定时器等

   删除阶段 (组件执行 vm.$destory()时进入此生命周期)

    	1. beforeDestroy 实例销毁前最后一刻触发
        * 保存数据或者dom状态
   	2. destoryed 实例销毁之后触发

6. 等待子组件生命周期完成之后的操作

   在声明周期中要等到子组件加载完成之后进行操作 使用 `this.$nextTick(function(){})`
