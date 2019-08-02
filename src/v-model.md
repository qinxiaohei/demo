#### v-model在组件中的使用
```
<template>
  <div id="app">
    <Child v-model="inp" />
    <button @click="changeInp">打印</button>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {
    Child:{
      props:['value'],
      template:`
        <div>
          子组件
          {{value}}
          <button @click="changeValue">修改</button>
        </div>
      `,
      methods:{
        changeValue(){
          this.$emit('input','修改之后的数据') //事件名必须为input，否则子传父数据双向绑定不生效
        }
      }
    }
  },
  data(){
    return {
      inp:'父组件的数据'
    }
  },
  methods:{
    changeInp(){
      console.log(this.inp) //子组件传来的数据是什么，这时this.inp就是什么
    }
  }
}
</script>
```