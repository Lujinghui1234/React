# Hook 使用
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

## useMemo
## memo
