    
    class Dep{
        constructor(){
            this.arr = []//存放每一个Watcher实例的数组
        }
        addWatcher(watcher){
            this.arr.push(watcher)//把所有的watcher都push到数组里，也就是让他们在同一队列里
        }
        touchWatcher(){
            this.arr.forEach(item => {//item为每一个watcher实例
                item.initCbk()//执行每一个watcher实例上的initCbk方法
            })
        }
    }
    Dep.target = null//让这个类上的一个静态方法target=null
    let dep = new Dep()//实例化Dep
    
    class Watcher{
        constructor(data,key,callback){
            Dep.target = this//把当前Watcher的实例挂载到Dep的静态方法target上
            this.data = data
            this.key = key
            this.callback = callback
            this.init()// 每一次的实例都会调用该函数
        }
        init(){
            this.value = utils.getValue(this.key,this.data)
            Dep.target = null //一获取数据就会执行数据劫持的get方法,为了避免多余的操作,把每一次的watcher实例都清空
            return this.value
        }
        initCbk(){//一执行initCbk这个方法,this.callback就会执行
            let value = this.init()
            this.callback(value)
        }
    }
    
    class Observer{
        constructor(data){
            if(!data || typeof data !== 'object'){//如果没有下一级或下一级不为对象的话直接返回
                return;
            }
            this.data = data
            this.initData()
        }
        initData(){
            Object.keys(this.data).forEach(item => {//获取所有的key值
                this.observer(this.data,item,this.data[item])//this.data[item]为默认值,this为要操作的对象
            })
        }
        observer(obj,key,value){
            new Observer(obj[key])//递归为了每一级都能被劫持
            Object.defineProperty(obj,key,{//数据劫持
                get(){// 添加劫持之后的属性获取方法
                    if(Dep.target){//如果Dep的target静态方法有值的话
                        dep.addWatcher(Dep.target)//执行dep的addWatcher方法,此时Dep.target为每一个watcher实例
                    }
                    return value
                },
                set(newValue){// 添加劫持之后的属性设置方法
                    if(value === newValue){//如果没有改变值,则直接返回
                        return;
                    }
                    value = newValue//把新值赋给value
                    console.log(value)
                    dep.touchWatcher()//调用touchWatcher方法
                    new Observer(value)//递归为了可以设置让每一级
                    // 为了兼容新值为一个对象的时候，该对象的属性也得添加劫持
                }
            })
        }
    }
    const utils = {
        getValue(value,data){
            console.log(value,data)
            if(value.indexOf(".">-1)){//school.name如果包含.的话
                value.split(".").forEach(item => {//把value转为数组["school","name"]
                    data = data[item]
                    //data被赋值了两次
                    //1.{name: "我是div的值"}
                    //2.我是div的值
                })
                return data
            }else{
                return data[value]
            }
        },
        changeValue(data,key,newValue){//改变对应的属性值
            if(key.indexOf(".")>-1){//如果包含.的话
                let arr = key.split(".")
                //arr.length-1因为要修改最后一级的值，所以要减1
                for(let i=0;i<arr.length-1;i++){
                    data = data[arr[i]]
                }
                data[arr[arr.length-1]] = newValue//把新值赋给arr的最后一项
            }else{
                data[key] = newValue//把新值赋给key对应的data
            }
        }
    }
    class Mvvm {
        constructor({el,data}){
            //把传过来的值挂载到类上
            this.$el = document.getElementById(el)
            this.data = data
            this.init()
            this.initData()
        }
        initData(){//把data里的属性挂载到this里
            Object.keys(this.data).forEach(item => {//获取所有的key值
                this.observer(this,item,this.data[item])//this.data[item]为默认值,this为要操作的对象
            })
            new Observer(this.data)//给当前数据集合的每一个属性添加劫持
        }
        observer(obj,key,value){
            Object.defineProperty(obj,key,{//数据劫持各个属性的get,set方法,监听属性的变动
                get(){//获取
                    return value
                },
                set(newValue){//设置
                    value = newValue
                }
            })
        }
        init(){
            //为了防止多次操作dom导致重绘设置一个变量newFargument
            let newFargument = this.createFragment()//获取到的每一个元素赋值给newFargument
            this.compiler(newFargument)//解析每一个节点
            this.$el.appendChild(newFargument)//把newFargument追加到this.$el,让浏览器可以识别
        }
        createFragment(){
            let fragment = document.createDocumentFragment()//创建一个碎片流(还没有渲染到页面上的虚拟dom)
            let firstChild;
            while(firstChild = this.$el.firstChild){//把$el上的每一个节点都给firstChild,不是深拷贝，相当于给与
                fragment.appendChild(firstChild)//把firstChild追加到fragment
            }   
            return fragment
        }
        compiler(node){
            //node.nodeType为11的话,nodeName为#document 片段 
            if(node.childNodes && node.childNodes.length >0){//如果node.childNodes存在且为一个数组的话
                node.childNodes.forEach(item=>{
                    this.compiler(item)//递归为了去解析node的每一个childNodes
                })
            }
            if(node.nodeType === 1){//如果nodeType为1的话是标签节点
                let attributes = node.attributes;//attributes是一个伪数组,伪数组是一个含有length属性的json对象
                [...attributes].forEach(val => {//伪数组转数组
                    //indexOf() 方法可返回某个指定的字符串值在字符串中首次出现的位置。
                   if(val.nodeName === "v-model"){
                        //node.value = this.data[val.nodeValue]//input里的值为this.data里val.nodeValue对应的值
                        node.addEventListener("input",(e)=>{//绑定input事件,获取每个input里的内容
                            utils.changeValue(this.data,val.nodeValue,e.target.value)
                        })
                        node.value = utils.getValue(val.nodeValue,this.data)//input里的内容
                   }
                })
            }else if(node.nodeType === 3){//如果nodeType为3的话是文本节点
                if(node.textContent.indexOf("{{")>-1){//如果文本节点包括{{
                    // console.log(node.textContent.split("{{"))
                    // ["", "value}}"]
                    // ["↵        ", "school.name}}↵    "]
                    let content = node.textContent.split("{{")[1].split("}}")[0]//获取到{{}}里面的值
                    node.textContent = utils.getValue(content,this.data)//文本节点对应的内容
                    content && new Watcher(this.data,content,(value)=>{//如果{{}}里有值并且可以监听到返回来的值的时候
                        node.textContent = value//把返回来的value赋给节点对应的内容,然后改变视图
                        console.log(value)
                    })
                }
            }
        }
    }
