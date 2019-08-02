#### 发布订阅者模式封装($bus)
```
想要实现的结果:
bus.$emit(eventName,params)
bus.$on(eventName,() => {})

class Bus{
    counstructor(){
        this.events = {}
    }
    $emit(eventName,params){
        this.events[eventName].forEach(val => {
          //这里this.events[eventName]不能写成 . 的形式，因为eventName是一个变量，变量只能写成中括号的形式。如果写成 . 的形式，则表示eventName这个属性名
          val(params)
        })
    }
    $on(eventName,cbk){
        if(this.events[eventName]){
            this.events.push(cbk)
        } else {
           this.events.eventName = [cbk] 
        }
        
    }
}

let bus = new Bus()
```