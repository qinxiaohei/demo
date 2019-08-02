    <a href="javascript:;" data-to="/">首页</a>
    <a href="javascript:;" data-to="/home">home</a>
    <a href="javascript:;" data-to="/index">index</a>
    <div id="box"></div>
    <script>
        class Router{
            constructor({routes}){
                this.routes = routes
                this.init()
                this.clickView()
            }
            init(){
                window.addEventListener("popstate",this.changeView.bind(this))
            }
            changeView(){
                console.log(1)
                let box = document.getElementById("box");
                let pathView = window.location.pathname//地址栏的路径
                box.innerHTML = this.routes.filter(val => val.path === pathView)[0].component
            }
            clickView(){
                let list = document.querySelectorAll("a");//伪数组
                [].forEach.call(list, item => {//伪数组变为数组,循环数组
                    let pathData = item.getAttribute("data-to")
                    item.addEventListener('click',()=>{
                        window.history.pushState({},null,pathData)
                        this.changeView()
                    })
                })
            }
        }
        new Router({
            routes:[{
                path:"/",
                component:"这是一个/"
            },{
                path:"/home",
                component:"这是home"
            },{
                path:"/index",
                component:"这是index"
            }]
        })
    </script>