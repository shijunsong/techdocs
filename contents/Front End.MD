# 生命周期

<img src="https://v2.cn.vuejs.org/images/lifecycle.png" width="50%"/>

### 1. 四大生命周期，八大钩子

- 第一阶段（创建阶段）：beforeCreate，created
- 第二阶段（挂载阶段）：beforeMount（render），mounted
- 第三阶段（更新阶段）：beforeUpdate，updated
- 第四阶段（销毁阶段）：beforeDestroy

### 2. vue对象创建

```javascript
var vm = new Vue({
  el: '#demo',
  prop: [],
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  },
  method: {
      
  }
})
```

### 3. 组件

#### 3.1 组件注册

`Vue.component('my-component-name', { /* ... */ })`



# 常见问题

### 1. computed 和 watch区别

- computed(计算属性)

  > `支持缓存`，只有`依赖数据发生改变，才会重新进行计算`  
  > `不支持异步`，当computed内有异步操作时无效，无法监听数据的变化  
  > `computed 属性值会默认走缓存`，计算属性是基于它们的响应式依赖进行缓存的，  
  > 也就是基于data中声明过或者父组件传递的props中的数据通过计算得到的值  
  > 如果一个属性是由其他属性计算而来的，这个属性依赖其他属性，是一个多对一或者一对一，一般用computed  
  > 如果computed属性属性值是函数，那么默认会走get方法；函数的返回值就是属性的属性值；  
  > 在computed中的，属性都有一个get和一个set方法，当数据变化时，调用set方法。

- watch(侦听属性)

  > `支持缓存`，数据变，直接会触发相应的操作；  
  > `watch支持异步`；  
  > 监听的函数接收两个参数，第一个参数是最新的值；第二个参数是输入之前的值；  
  > 当一个属性发生变化时，需要执行对应的操作；一对多；  
  > 监听数据必须是data中声明过或者父组件传递过来的props中的数据，当数据变化时，触发其他操作，函数有两个参数，  
  > 	immediate：组件加载立即触发回调函数执行，  
  > 	deep: 深度监听，为了发现对象内部值的变化 

### 2. 数据对象动态绑定原理

### 3. 对象的生命周期

### 4. webpack原理

### 5. 什么是原型链

### 6. 什么是闭包

### 7. 什么是响应式及其原理

### 8. 跨域产生的原因及如何解决