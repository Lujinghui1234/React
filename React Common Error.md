# React项目开发易错点
## 1. react中的setState是异步行为
1,
```js
const [data,setData] = useState('');
const res = await callApi1();
const resName = res.payload.data.name;
setData(resName);
await callApi2(data);//data还是''
问题：setState一直是异步行为，所以data还是旧数据。
正确写法：
await callApi2(resName);//考虑useState是否有必要？
```
2,setState数组不更新（setState使用Object.is()比较新旧值，对象比较的是引用地址）
```js
//错误写法：
const [list,setList] = useState([]);
const data = await callApi();
setList(data);
const newList = list.map(item=>{//此处的list并不是最新的。
    a:item.a;
});
```

```
//正确写法：
const [list,setList] = useState([]);
const data = await callApi();
const newList = [...list,...data];//因为setState使用Object.is()比较的是引用地址，所以需要赋值给一个新数组，再去setState
setList(newList);
console.log(newList);//可获取到最新的，如果打印list是获取不到的，但jsx中render list会是最新的。
```
参考React文档：
https://zh-hans.react.dev/reference/react/useState#ive-updated-the-state-but-logging-gives-me-the-old-value
参考别人总结的资料：
![e03fed3e04d226de56b2c28664b79d83](https://github.com/Lujinghui1234/Coding-Common-Error/assets/109168485/a9a510f8-0feb-4b45-9676-dfd68a555154)
## 2. 使用react-router的location.state存储数据，刷新页面数据丢失
这个方案不是持久化存储数据方式，可替换为redux-persist（redux的插件，刷新数据不丢失），或者query string的方式，把参数拼接在url问号后面。
## 3. 为什么不会发生变化的常量，要写在Function外面？
因为写在Function里面，re-render时diff算法会去计算这个变量的值是否和上次一样，而写在外面不会，这样写可以省去计算消耗的时间。补充：写在Function外面的变量和import的资源在re-render时都不会去执行。
```js
const data = '123';
const Com = ():ReactElement=>{
    <div>hello world !</div>
}
```
## 4. eslint检查报错:'xxx' is missing in props validation react/prop-types
这是因为组件的prop没写类型声明，加上类型声明就可以解决。
## 5. To many re-renders.React limit the number of renders to prevent an infinity loop.
原因之一是useState的setter函数不规范调用。
```
if(someCondition){
    setList(xxx);
}else{
    setList(xxx);
};
```
以上代码报错是因为React不允许在条件判断中调用useState的setter函数。解决方法一：不使用useState;二：不在条件判断中使用useState。

## 6. React中父组件更新子组件也更新，但有时候子组件并没有更新，想让子组件强制刷新
给子组件传入key，绑定父组件中可以更新的值，一旦父组件这个值更新，子组件会强制刷新
```
const [itemKey, setItemKey] = useState<string>("");

const Farther = ()=>{
    return (
    <>
        <Child key={itemKey} />
        <button onClick={()=>setItemKey("update")}>btn</button>
    </>
    )
}

```

