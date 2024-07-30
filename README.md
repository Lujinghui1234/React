## 1.Class类组件和Function函数组件的区别？
    1，class组件继承自React.Component，并且通过render函数返回JSX结构
    2，function组件其实是一个javascript函数，只不过它以大写字母开头，接受props作为形参，内部直接返回JSX结构
    3，class组件有不同的生命周期钩子函数，在不同的钩子中做不同事情
    4，function组件没有生命周期钩子函数，在React V16.8之后用hooks去模拟class组件的钩子
    5，class组件有constructor,constructor里面存储状态this.state={count:0},this.xxx，this指向当前组件实例对象,通过this.state.xxx=xxx更新状态值
    6，function组件没有constructor，所以没有this,其指向undefined;状态保存在useState这个hook中，通过setState更新状态值

