# 11-状态管理VUEX

1. 存在的意义

   组件之间共享状态, 但是状态的关系不好传递, 维护也会变得很麻烦, 我们也无从得知状态时怎么发生变化的

2. vuex 是 vue的专有的状态管理模式

   * state 数据和需要记录的状态
   * vue 视图表现,渲染的值
   * action 响应状态的变化

3. **state 数据源**

   表示存储在store中的数据, 维护数据独立完整

   * 在vuex中数据发生了改变, 对应的组件也会更新

   * 不能直接修改state, 因为状态的变化不可控
   * 需要使用commit提交, commit的内容放在mutation中

   ```js
   // 创建存储仓库
   const store = new Vuex.store({
     state: {
       count: 0
     }
   })
   
   // 注册到全局
   new Vue({
     store
   })
   ```

   在组件中

   ```html
   <div>
   	{{v}}
   </div>
   <script>
   	export default {
       data(){
         return {
           v: this.$store.state.count // 这里是静态赋值, state发生改变的时候不会更新
         }
       }
     }
   </script>
   ```

   动态的获取值要放到computed里面

   ```html
   <div>
   	{{v}}
   </div>
   <script>
   	export default {
       computed: {
         v(){
           return this.$store.state.count //state发生改变的时候触发更新
         }
       }
     }
   </script>
   ```

   或者直接在html中使用

   ```html
   <div>
     {{$store.state.count}}
   </div>
   ```

4. **getters: 状态的抽离和批量赋值**

   我们需要对原始数据进行处理, 得到想要的数据, 可以使用getters

   ```js
   const store = new Vuex.store({
     state: {
       list: [
         {sex: "male", age: 18},
         {sex: "female", age: 16},
         {sex: "male", age: 20},
         {sex: "female", age: 14},
       ]
     },
     getters: {
       boys(state){
         return state.list.filter(person => person.sex === "male")
       },
       girls(state){
         return state.list.filter(person => person.sex === "female")
       }
     }
   })
   
   // 使用的时候
   Vue.extend({
     computed: {
       boys(){
         return this.$store.getters.boys // 注意 是一个getter计算属性
       },
       girls(){
         return this.$store.getters.girls
       },
     }
   })
   ```

   可以进行简化: (使用mapGetter)

   ```js
   import {mapGetters} from "vuex"
   Vue.extend({
     computed: {
       ...mapGetters(["boys"]), // 数组映射
       ...mapGetters({     			// 对象映射, 可以改名
         myGirl: "girls"
       })
     }
   })
   ```

5. **mutations: 数据的更新**

   基本功能, 唯一能够直接操作数据state的模块, 同步操作

   ```js
   const store = new Vuex.store({
     state: {
       count: 0,
       bool: false
     },
     mutations: {
       add(state, val){ // 支持传入参数表示改变方式
         state.count += val.num
       }
     }
   })
   ```

   使用: 

   ```js
   Vue.extend({
     methods: {
       add(val){
         this.$store.commit("add",val) // val 是 {num: 3} 这种对象
       }
     }
   })
   ```

   可以使用对象风格

   ```js
   import {mapMutations}
   Vue.extend({
     methods: {
       add(val){
         this.$store.commit({
           type: "mutations", 
           num: 3
         })
       }
     }
   })
   ```

   可以批量注册

   ```js
   import {mapMutations} from "vuex"
   Vue.extend({
     methods: {
       ...mapMutations(["add"]) // 等同于 mapGetters 
       // 略
     }
   })
   ```

6. **actions 提交操作指令**

   不需要直接操作状态, 相当于发布指令, 可以进行异步操作

   ```js
   const store = new Vuex.store({
     state: {
       a: 1
     },
     mutations: {
       add(state, val){
         state.a += val
       }
     },
     actions: {
       addOrder({commit, state}, val){
         commit("add", val)
       }
     }
   })
   ```

   使用: 

   ```js
   Vue.extend({
     methods: {
       addOrder: function(){
         this.$store.dispatch("addOrder", {
           val: 0
         })
       }
     }
   })
   // 或者以对象形式
   Vue.extend({
     methods: {
       addOrder: function(){
       	this.$store.dispatch({
       		type: "addOrder", 
       		val: 0
     		})
      },
     }
   })
   ```

   结构到methods中

   ```js
   import {mapActions} from "vuex"
   
   Vue.extend({
     methods: {
       ...mapActions(["addOrder"]) // 同样参考前面的 mapGetters
     }
   })
   ```

7. **模块化分割 modules**

   每个模块有各自的数据, 数据提交 ,操作提交, 数据赛选获取, 保证数据的独立

   ```js
   const moduleA = {
     state: () => ({ ... }),
     mutations: { ... },
     actions: { ... },
     getters: { ... }
   }
   const moduleB = {
     state: () => ({ ... }),
     mutations: { ... },
     actions: { ... }
   }
   
   const store = new Vuex.store({
     modules: {
       a: moduleA,
       b: moduleB
     }
   })
   ```

   具体在使用的过程中

   ```js
   store.state.a
   store.state.b
   ```

   项目结构

   ```text
   store
       ├── index.js          # 我们组装模块并导出 store 的地方
       ├── actions.js        # 根级别的 action
       ├── mutations.js      # 根级别的 mutation
       └── modules
           ├── cart.js       # 购物车模块
           └── products.js   # 产品模块
   ```

   