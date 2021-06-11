### 数据类型
#### 数值
- parseInt() 将字符串转为整数
- parseFolart() 将字符转转为浮点数
- isNaN() 判断一个值是否为NaN
- isFinite() 返回一个布尔值，表示某个值是否为正常的数值。（Infinity、-Infinity、NaN、undefined返回false）

#### 字符串
- btoa() 任意值转为Base64编码
  atob() Base64编码转为原来的值（不适合非ASCLL码的字符）

#### 对象
- Object.keys() 查看对象本身的所有属性
- delete obj.p 删除对象的属性
- obj.hasOwnProperty() 判断是否为自身属性

#### 函数
1. 属性
- name
- length
- toString() 返回一个字符串，内容是函数的源码

2. 参数
- arguments[] 对象 包含了函数运行时的所有参数
   callee 属性 返回它所对应的原函数

3. eval
- 接受一个字符串作为参数，并将这个字符串当作语句执行

#### 数组
- length
- for...in 会遍历非数字键
- forEach()
- var arr = Array.prototype.slice.call(arryLike) 将类似数组的对象没变成真正的数组
- Array.prototype.forEach.call(arrayLike, print) 在类似数组的对象使用数组的forEach方法

### console对象与控制台
#### 静态方法
- console.log()
- console.info()
- console.debuge()
- console.warn()
- console.error()
- console.table()
- console.count()
- console.dir()
- console.dirxml()
- console.time()
- console.timeEnd()
- console.group()
- console.groupEnd()
- console.groupCollapsed()
- console.trace()
- console.clear()

#### debugger

### 标准库
#### Object对象
- Object.keys()
- Object.getOwnPropertyNames()