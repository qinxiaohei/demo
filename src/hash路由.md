### 用原生写一个hash模式的路由

    <a href="#/">index</a>
    <a href="#/home">home</a>
    <a href="#/hot">hot</a>
    <div id="box">

    </div>
    <script>
        class Router {
            constructor({routes}){//接受所有的路由信息
                this.routes = routes
                this.eventRoutes = {}
                this.routes.forEach((item,index) => {
                    this.eventRoutes[item.path] = function(){//让每一个path对应每一个组件的内容
                        document.getElementById('box').innerHTML = item.component
                    }
                })
                this.init()
            }
            init () {
                window.addEventListener('load',()=>{//页面刚开始的时候调用自动刷新的方法
                    let pathRoutes = window.location.hash.slice(1)//截取hash模式的路由,包括左边不包括右边
                    this.eventRoutes[pathRoutes]()
                })
                window.addEventListener('hashchange',()=>{//点击的时候调用hash路由改变的方法
                    let pathRoutes = window.location.hash.slice(1)
                    this.eventRoutes[pathRoutes]()
                })
            }
        }
      new Router({//路由表
            routes:[{
                path:"/",
                component:'我是index1'
            },{
                path:"/home",
                component:'我是home1'
            },{
                path:"/hot",
                component:'我是hot1'
            }]
      })
        
    </script>