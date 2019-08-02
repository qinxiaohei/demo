        function Person () {
            if(!Person.prototype.isPrototypeOf(this)){//如果this的指向不是window，返回一个Person实例
               return new Person()
            }
            this.tasks = []//存放所有任务的数组
            this.init()
            this.runTask()
        }
        Person.prototype.runTask = async function () {
            for(let val of this.tasks){//循环所有的任务
                await val.task(val.time)//执行所有的任务
            }
        }
        Person.prototype.pushTask = function (fn) {
            this.tasks.push(fn)//把所有的任务都push在this.tasks
        }
        Person.prototype.init = function () {//用async把所有任务放到一个队列里
            async function task () {
                console.log('This is li')
            }
            this.pushTask({task})
            return this;//为了可以链式调用
        }
        Person.prototype.eat = function () {
            async function task () {
                console.log('eat food')
            }
            this.pushTask({task})
            return this;
        }
        Person.prototype.sleepFirst = function (time) {
            async function task (time) {
                await new Promise((resolve,reject) => {
                    setTimeout(()=>{
                        resolve()
                    },time)
                })
                console.log('sleep before 5')  
            }
            this.pushTask({task,time})
            return this
        }
        Person.prototype.sleep = function (time) {
            async function task (time) {
                await new Promise((resolve,reject) => {
                    setTimeout(()=>{
                        resolve()
                    },time)
                })
                console.log('sleep after 10')
            }
            this.pushTask({task,time})
            return this
        }
        Person().sleepFirst(5).eat().sleep(3)