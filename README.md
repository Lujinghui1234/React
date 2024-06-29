## 1.Class类组件和Function函数组件的区别？
    1. 类组件都继承自React.Component
    2. 类组件this指向当前组件的实例对象，函数组件没有this，指向undefined
    3. 类组件有它的的生命周期钩子函数，函数组件没有，使用hooks去实现类组件生命周期钩子函数的功能
    4. 类组件状态保存在this.state.xxx中，通过this.state.xxx=xxx更修状态值；函数组件状态保存在useState这个hook中，通过setState更新状态值
    5. ……

