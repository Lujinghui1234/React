# React项目开发易错点
## 1. react中的setState是异步行为
```js
const [data,setData] = useState('');
const res = await callApi1();
const resName = res.payload.data.name;
setData(resName);
await callApi2(data);//data还是''
```
   问题：setState一直是异步行为，所以data还是旧数据。
   
   正确写法：
```js
await callApi2(resName);//考虑useState是否有必要？
```
## 2.自测的时候没有覆盖到所有场景，比如某些数据没有填写直接提交，导致空数据场景报错，如：在undefined或null中解构某属性
## 3. 同一个api，页面call：cancel，但postman call：200
因为postman和浏览器的环境不同，postman会设置了某些东西，项目上线后用户使用环境也是浏览器，肯定要以浏览器为准。
## 4. 使用react-router的location.state存储数据，刷新页面数据丢失
这个方案不是持久化存储数据方式，可替换为redux-persist（redux的插件，刷新数据不丢失），或者query string的方式，把参数拼接在url问号后面。
## 5. 页面跳转传递数据，为什么开发中极少使用localStorage?
    1, 假设换了个浏览器（考虑用户转发页面），不会有本地缓存数据
    2，数据可在控制台看到，容易被窃取和XSS攻击，这点可以通过https协议加密/数据加密/数据转义和过滤解决，但是比较麻烦
    3，由于数据是持久化存储，除非人为清除数据不然数据会一直存在，数据量过大容易导致存储空间不足
一般会使用什么替换方案？
    1，query string，把参数放在url上，问号后面
    2，有待补充……
## 6. 不要滥用可选链，看为什么是空数据，是api原因还是前端代码写错了？有些明知道不会是空数据的，就不要加可选链了。
## 7.常见ts报错：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/912ef097268a48f780741251f314f871~tplv-k3u1fbpfcp-watermark.image?)
答案：https://blog.csdn.net/qq_40864647/article/details/125764130

最简单的方法是断言：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b13858456251461d879e5ee68fd8337a~tplv-k3u1fbpfcp-watermark.image?)
## 8. 为什么不会发生变化的常量，要写在Function外面？
因为写在Function里面，re-render时diff算法会去计算这个变量的值是否和上次一样，而写在外面不会，这样写可以省去计算消耗的时间。补充：写在Function外面的变量和import的资源在re-render时都不会去执行。
```js
const data = '123';
const Com = ():ReactElement=>{
    <div>hello world !</div>
}
```
## 9. setState数组不更新（setState使用Object.is()比较新旧值，对象比较的是引用地址）
错误写法：
```js
const [list,setList] = useState([]);
const data = await callApi();
setList(data);
const newList = list.map(item=>{//此处的list并不是最新的。
    a:item.a;
});
```
正确写法：
```
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

## 10. git commit 报错：pidtree\lib\pidtree.js    new Error: no matching pid found
这个报错是因为本地进程太多了，解决方法是清理本地内存空间：清空回收站、关闭不使用的进程，关闭编辑器，直接用git bash操作。
## 11. 大数值精度问题
前言：在ES6以前，Javascript没有专门的整数类型，所有的数字都被表示为双精度64位浮点数，这就意味着Javascript能安全地处理的整数范围是有限的，超过16位数的大数字会失去精度。

场景：API返回数据：obj_id为18位数值

![image](https://github.com/Lujinghui1234/Coding-Common-Error/assets/109168485/d08c40e2-15de-4fb3-ba48-6a3f01b1e967)

问题：使用JSON.parse（obj_id）,变成了898186844400803800，失去了精度。

解决：判断数据类型，如果是大精度数据，就不要JSON.parse()了（这个方法会变成Number,大数据会丢失精度）。
```
if(typeof obj_id === 'string' && /^\d{16,}$/.test(obj_id)){
    return obj_id;
}else{
    return JSON.parse(obj_id)
};
```
## 12. 自己在本地新建了个项目，关联远程仓库时git push报错：The requested URL returned error: 403
  解决：https://blog.csdn.net/qq_40226073/article/details/119801341
  思路是生成仓库的token令牌，git绑定密码为这个令牌。
## 13. eslint检查报错:'xxx' is missing in props validation react/prop-types
这是因为组件的prop没写类型声明，加上类型声明就可以解决。
## 14. To many re-renders.React limit the number of renders to prevent an infinity loop.
原因之一是useState的setter函数不规范调用。
```
if(someCondition){
    setList(xxx);
}else{
    setList(xxx);
};
```
以上代码报错是因为React不允许在条件判断中调用useState的setter函数。解决方法一：不使用useState;二：不在条件判断中使用useState。

