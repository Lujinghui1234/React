# React为什么重新渲染
https://blog.skk.moe/post/react-re-renders-101/

### 两个根本原因：
1. 状态改变
2. 类组件的React.Component的forceUpdate

### 开发者常说的导致React重新渲染的几大原因：
1. 状态改变
2. props改变
3. context改变
4. 父组件重新渲染导致所有子组件重新渲染

**以上第2、3个说法其实存在漏洞。**
1. props改变：

```js
import BigNumber from './big-number'; 

const SomeDecoration = () => <div>Hooray!</div>； 

const Counter = () => { 
const [count, setCount] = useState(0); 
const handleButtonClick = useCallback(() => setCount(count => count + 1), []); 

return (
<div> 
    <BigNumber number={count} /> 
    <button 
        onClick={handleButtonClick}>
        Increment
    </button> 
    <SomeDecoration /> 
</div> 
)}; 

const App = () => ( 
<> 
    <Counter />
    <footer> 
        <a href="https://skk.moe/">Sukka</a> 
    </footer> 
</> 
);
```
组件不接受任何 prop、不使用其父组件 的 count 状态，但是当 count 状态发生改变时， 组件仍然发生了更新。当一个组件更新时，React 会更新 所有的子组件，不管这个子组件是否接受一个 prop：React 并不能百分之百肯定 组件是否直接/间接地依赖了 count 状态。

1. context改变
Context 的 value 发生改变的根本原因还是状态的改变，（具体看文章便知）

## React中setState会引发组件重新render，如何优化？
使用useRef存储组件中的数据，修改useRef的返回值并不会引发组件重新render
