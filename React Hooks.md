# Hook 使用
## Hook使用规则：
### 1，在函数组件的顶级作用域或其他hook中使用hook
### 2，避免在循环、条件中使用hook，也就是无条件调用hook
## useRef
   父组件：
```js
import {useRef} from 'react;
const Far = () => ReactElement {
    const ref = useRef({a:1});
    return (<Son ref={ref} data='123'/>)
}
```
子组件：

```js
import {forwardRef,useImperativeHandel} from 'react;
const Son = forwardRef((prop:any,ref:any):ReactElement=>{
    const data = '一些传给父组件的数据……';
    useImperativeHandel(ref,data);
    return(<div>123</div>)
})
```
常见错误：子组件要用forwardRef包裹，不然会报错。

## useMemo & useCallBack
这两个是为了做性能优化的hook,避免组件多次不必要的re-render。useMemo是缓存计算结果，useCallBack是缓存函数，两个都是常常与memo搭配使用。
### useMemo:
```
import {useMemo} from 'react;
const list = useMemo(calculateValue,[dependencies]);
```
第一个参数是函数（没有形参），第二个参数是依赖项，依赖项中任意一个的值与上次render相比发生了变化（数组是浅比较），才会重新执行calculateValue函数，返回新的值，否则返回上次calculateValue返回的值。
第二个参数如果是空数组，只会记住第一次的计算，某些场景下这样使用useMemo就没有意义了。
### useCallBack:
```
import {useCallBack} from 'react;
const list = useCallBack(fn,[dependencies]);
//useCallBack第一个参数是想要缓存的函数；第二个参数是依赖项，可以是props、state或组件中的任意变量，任意一个依赖项发生变化，将会重新执行，返回一个新的函数。否则返回上次render返回的fn。

useCallback可适用场景：
父组件通过props给子组件传递函数，子组件使用React.Memo包裹，不想产生不必要的rerender，此时父组件中的这个函数使用useCallBack包裹，目的是缓存函数，每次props传的都是同一个函数，React.Memo浅比较时发现函数的引用地址不变，子组件才不会rerender。
```
注意①：useMemo的使用场景如下,除此之外没有必要使用。
![image](https://github.com/Lujinghui1234/React/assets/109168485/bc75bb85-ff13-4276-8fa4-1a6d87988181)

注意②：useCallBack 的使用场景如下，除此之外没有必要使用，不但没有意义，而且代码可读性差。
![image](https://github.com/Lujinghui1234/React/assets/109168485/67ee5076-e8fd-4072-a884-35c4098ef996)
## memo  (使用Object.is，默认是浅比较)
当组件的props与上次相比发生变化，才会re-render该组件，否则不会re-render。通常与useMemo或useCallBack搭配使用。
```
const MemoizedComponent = memo(SomeComponent, arePropsEqual?)
//第一个参数是组件名称；第二个可选参数是布尔值，比较上次和这次的props是否相同（使用Object.is，默认是浅比较），为true时不会re-render该组件，为false会re-render。
```



