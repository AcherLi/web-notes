## 理解new运算符

```
// new 的模拟实现
function objectFactory() {
  var obj = new Object(),
  cons = [].shift.call(arguments)
  obj.__proto__ = cons.prototype
  var ret = cons.apply(obj, arguments)
  return typeof ret === 'object' ? ret|| obj : obj
}
function Person(name) {
  this.name = name;
  return {a: 1}
}
var p = objectFactory(Person, 'fefeng')

```  
* 首先是创建一个对象
* cons 是调用 objectFactory 方法的第一个参数，即构造函数; 
* 因为 shift 会改变原数组，所以改变后的 argument 即为调用构造函数的参数 (这里补充说明下： arguments 是一个对应于传递给函数的参数的类数组对象。)
* 将 obj 的原型指向构造函数, 这样 obj 就能访问到构造函数原型上的属性
* 将构造函数 cons 的 this 指向 obj，这样 obj 能访问构造函数里的属性
* 判断返回的值是不是一个对象，如果是对象即返回它（当然这里要处理 return null 的特例）;如果不是对象就返回 obj (注意：这里的 obj 已经不是一个空对象）
