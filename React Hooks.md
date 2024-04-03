# Hook 使用
## Hook使用规则：
### 1，在函数组件的顶级作用域或其他hook中使用hook
### 2，避免在循环、条件中使用hook，也就是无条件调用hook
## useRef
#### 优点：
   1，re-render时不会创建一个新的ref对象；
   2，如果修改ref.current的值，component不会re-render，这个优点同时也是缺点
#### 缺点：
正是因为修改ref.current，component不会re-render，所以ref不适用于屏幕渲染，因为screen不会update.
   父组件：
```js
import {useRef} from 'react;
const Far = () => ReactElement {
    const ref = useRef({a:1});
    const handel = () => console.log(ref.current.data)
    return (
      <>
      <Son ref={ref} />
      <button onClick={handel}>Click</button>
     </>
    )
}
```
子组件：

```js
import {forwardRef,useImperativeHandel} from 'react;
const Son = forwardRef((props:any,ref:any):ReactElement=>{//props一定要写来占位，不然浏览器会报错
    const data = 'some data……';
    useImperativeHandel(ref,()=>{
      return {
         data
      }
    };
    return(<div>123</div>)
})
```
常见错误：子组件要用forwardRef包裹，不然会报错，且props一定要写来占位，不然浏览器会报错。
## useImperativeHandle
这个hook是为了搭配useRef使用，子组件用来向父组件回传数据的
用法：
```
useImperativeHandle(
    ref,//param 1:父组件传递的ref
    () => {
      return {};
    },//param 2:回传给父组件的函数
    [dependencies]//param 3:可选的依赖项，依赖项不变则回传的数据不变
  );
```
demo:
```
父组件：
import { useRef } from "react";
export default function GrandPa() {
  const farRef = useRef<any>(list);
  const handel = () => console.log(farRef.current.list, "data===");
  return (
    <>
      <Farther ref={farRef} />
      <button onClick={handel}>grandPa button</button>
    </>
  );
};
子组件：
import { useImperativeHandle, forwardRef, useState } from "react";
const Farther = forwardRef((props: any, ref: any) => {
  const [data, setData] = useState([1, 2, 3]);
  const [list, setList] = useState(["apple", "banana"]);

  const handelClick = () => {
    setList([...list, "pear"]);
  };

  useImperativeHandle(
    ref,
    () => {
      return {
        list,
      };
    },
    [data]//这个依赖项是永远不变的，所以即便list发生变化了，回传给父组件的list也永远不变；只有这个依赖项变了list才会变化。
  );

  return (<button onClick={handelClick}>farther button</button> );
});

export default Farther;
```
## useMemo & useCallBack
这两个hook都是做性能优化的,避免组件多次不必要的re-render。useMemo是缓存计算结果，useCallBack是缓存函数，两个都是常常与memo搭配使用。
### useMemo:
```
import {useMemo} from 'react;
const list = useMemo(
calculateValue,//param 1:calculateValue fn(has no it's param)
[dependencies] //param 2:dependencies
);
```
param 2的依赖项中任意一个的值与上次render相比发生了变化（数组是浅比较），才会重新执行calculateValue函数，返回新的值，否则返回上次calculateValue返回的值。
param 2如果是空数组，只会记住第一次的计算，某些场景下这样使用useMemo就没有意义了。
### useCallBack:
```
import {useCallBack} from 'react;
const list = useCallBack(
fn,//param 1:the function you want to cache
[dependencies]);//param 2:dependencies
```
param 2中的任意一个依赖项发生变化，将会重新执行，返回一个新的函数，否则返回上次render返回的函数。

useCallback可适用场景：
父组件通过props给子组件传递函数，子组件使用React.Memo包裹，不想产生不必要的rerender，此时父组件中的这个函数使用useCallBack包裹，目的是缓存函数，每次props传的都是同一个函数，React.Memo浅比较时发现函数的引用地址不变，子组件才不会rerender。

注意①：useMemo的使用场景如下,除此之外没有必要使用。
![image](https://github.com/Lujinghui1234/React/assets/109168485/bc75bb85-ff13-4276-8fa4-1a6d87988181)

注意②：useCallBack 的使用场景如下，除此之外没有必要使用，不但没有意义，而且代码可读性差。
![image](https://github.com/Lujinghui1234/React/assets/109168485/67ee5076-e8fd-4072-a884-35c4098ef996)
## memo  (使用Object.is，默认是浅比较)
当组件的props与上次相比发生变化，才会re-render该组件，否则不会re-render。通常与useMemo或useCallBack搭配使用。
```
const MemoizedComponent = memo(
SomeComponent,//param 1:Component name
 arePropsEqual?)//optional param 2:boolean
//param 2用于比较上次和这次的props是否相同（使用Object.is，默认是浅比较），为true时不会re-render该组件，为false会re-render。
```
## useContext   嵌套组件传递数据使用
```
//父组件：
export const ListContext = createContext(undefined);//记得要导出context，不然子组件获取不到
export default function GrandPa() {
  const [list, setList] = useState<any>([{ name: "rose", age: 18 }]);

  const handelClick = () => {
    const newItem = { name: "jack", age: 20 };
    setList((prev: any) => [...prev, newItem]);//修改context的值
  };

  return (
    <ListContext.Provider value={list}>//context传递状态数据
      <Farther />
      <button onClick={handelClick}>button</button>
    </ListContext.Provider>
  );
};

//子组件：
import { useContext } from "react";
import Son from "../son";
import { ListContext } from "../grandPa";//记得引入context

export default function Farther() {
  const context = useContext(ListContext);
  console.log(context);//可以获取到传递过来的context数据
  return (
      <Son />
  );
}；
```
使用useContext，子组件不能直接修改context数据，可以通过传递回调函数通知父组件修改，本质上还是传递回调函数，和useContext没有关系。




