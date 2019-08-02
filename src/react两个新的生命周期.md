父组件:

    import React, { Component } from 'react';
    import Children from "../Children/index"
    class Home extends Component {
        constructor(props) {
            super(props);
            this.state = { 
                count: 0
             };
        }
        render() {
            let {count} = this.state
            return (
                <>
                    <span>{count}</span>
                    <br/>
                    <Children count={count}/>
                    <br/>
                    <button onClick={()=>{this.setState({count:'我是父组件'})}}>修改</button>
                </>
            );
        }
    }
    
    export default Home;
    
子组件:

    import React, { Component } from 'react';
    class Children extends Component {
        constructor(props) {
            super(props);
            this.state = { 
                ...props
             };
        }
        //这一生命周期方法是静态的，它在组件实例化或接收到新的 props 时被触发
        static getDerivedStateFromProps(nextprops,prevstate){//nextprops为下一个props,prevstate为当前state
            //若它的返回值是对象，则将被用于更新 state 
            //若是 null ，则不触发 state 的更新
            if(nextprops.count !== prevstate.count){
                return {//返回一个对象,自带this.setState机制
                    count:nextprops.count
                }
            }else{
                return null
            }
        }
        getSnapshotBeforeUpdate(nextprops,nextstate){
        //触发时机是 React 进行修改前（通常是更新 DOM）的“瞬间”,它的返回值会作为第三个参数传入 componentDidUpdate
            console.log('getSnapshotBeforeUpdate')
            return 532
        }
        componentDidUpdate(prevprops,prevstate,getSnapshotBeforeUpdate){
            console.log('componentDidUpdate',getSnapshotBeforeUpdate)
        }
        render() {
            console.log('render')
            let {count} = this.state
            return (
                <>
                    子组件:{count}
                </>
            );
        }
    }
    
    export default Children;