## 1.Class类组件和Function函数组件的区别？
    1，class组件继承自React.Component，并且通过render函数返回JSX结构
    2，function组件其实是一个javascript函数，只不过它以大写字母开头，接受props作为形参，内部直接返回JSX结构
    3，class组件有不同的生命周期钩子函数，在不同的钩子中做不同事情
    4，function组件没有生命周期钩子函数，在React V16.8之后用hooks去模拟class组件的钩子
    5，class组件有constructor,constructor里面存储状态this.state={count:0},this.xxx，this指向当前组件实例对象,通过this.state.xxx=xxx更新状态值
    6，function组件没有constructor，所以没有this,其指向undefined;状态保存在useState这个hook中，通过setState更新状态值
## 2.React事件机制  https://juejin.cn/post/6955636911214067720?searchId=202407151638456BB6FC7DEE51D408AC7F#heading-32
    1，我们在jsx中绑定的事件，根本就没有注册在真实dom身上，而是被统一绑在了document身上
    2，真实的dom事件，已经被react底层替换成了空函数
    3，我们在react绑定的事件，比如onChange，在document上可能会有多个事件与之对应
    4，在react中，我们绑定的事件onClick等，并不是原生的事件，而是由原生事件合成的react合成事件，比如click合成为onClick事件，blur、change、input、keydown、keyup合成为onChange事件
    5，react为什么要设计这种合成事件模式？
        1，因为将事件绑定在document上统一管理，如果事件分别注册在dom上，就会造成不可控的情况
        2，react想实现一个全浏览器框架，就要做到浏览器一致性，抹平浏览器之间的差异
