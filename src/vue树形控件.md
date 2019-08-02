父组件:

    <template>
        <div>
            <List :lists="list"/>
        </div>
    </template>
    <script>
    import List from "@/components/List.vue"
    export default {
      props: {
    
      },
      components: {
          List
      },
      data () {
        return {
          list: [{
            tit: '一级目录1',
            flag:true,
            id:1,
            children: [{
              tit: '二级目录1',
              flag:false,
              id:2,
            }, {
                tit: '二级目录2',
                flag:false,
                id:3,
                children: [{
                  tit: '三级目录1',
                  flag:false,
                  id:4
                }]
            }]
          }, {
            tit: '一级目录2',
            flag:true,
            id:5,
            children: [{
                tit: '二级目录1',
                flag:false,
                id:6
            }, {
                tit: '二级目录2',
                flag:false,
                id:7,
                children: [{
                  tit: '三级目录1',
                  flag:false,
                  id:8
            }, {
                  tit: '三级目录2',
                  flag:false,
                  id:9
                }]
            }]
          }, {
            tit: '一级目录3',
            flag:true,
            id:10,
            children: [{
              tit: '二级目录1',
              flag:false,
              id:11
            }]
          }]
        }
      },
      computed: {
    
      },
      methods: {
        
      },
      created () {
    
      },
      mounted () {
    
      }
    }
    </script>
    <style scoped lang=''>
        
    </style>  
    
子组件:

    <template>
    <div>
        <ul>
            <template v-for="(item,index) in lists" >
                <li :key="index" @click.stop="tab(item.id)" v-if="item.flag"> 
                    {{item.tit}}
                    <button @click.stop="add(item.id)">增加</button>
                    <button @click.stop="del(item.id)">删除</button>
                    <button @click.stop="edit(item.id)">修改</button>
                    <List v-if="item.children" :lists="item.children"/>
                </li>
            </template>
        </ul>
    </div>
        
    </template>
    <script>
    import List from "./List"
    export default {
        props:["lists"],
        name:"List",
        components:{
            List
        },
        data(){
            return {
                value:""
            }
        },
        computed:{
    
        },
        methods:{
            tab(id){
                let ind = this.lists.findIndex(item=>item.id===id)
                if(this.lists[ind].children){
                    this.lists[ind].children.forEach(item => {
                        item.flag = !item.flag
                    })
                }
            },
            del(id){
                let ind = this.lists.findIndex(item => item.id === id)
                this.lists.splice(ind,1)
            },
            add(id){
                let ind = this.lists.findIndex(item => item.id === id)
                let name = window.prompt("确定增加吗?")
                if(this.lists[ind].children){
                    this.lists[ind].children.push({
                        tit:name,
                        id:new Date *1,
                        flag:false
                    })
                }else{
                    //因为增加进去的数据没有被劫持,所以要用this.$set
                    this.$set(this.lists[ind],'children',[{
                        tit:name,
                        id:new Date *1,
                        flag:false
                    }])
                }
            },
            edit(id){
                let ind = this.lists.findIndex(item => item.id === id)
                let name = window.prompt("您确定要修改吗")
                this.lists[ind].tit = name
            }
        },
        created(){
    
        },
        mounted(){
    
        }
    }
    </script>
    <style scoped lang="">
        
    </style>
