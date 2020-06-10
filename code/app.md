# 简答题
1. 描述引用计数的工作原理和优缺点
**引用计数（reference counting）算法的工作原理是**：标记每个对象被引用的次数，当新增一处引用，计数加一；反之如果一处引用被断开，该对象的计数减一。当计数为0时，该对象*会被当作垃圾立即处理*。
**这种GC算法的优点在于**：不同于其他GC算法，引用计数算法可以立即清除垃圾对象，即，当某个对象的引用计数为0时，就会被当作垃圾立即清除，不需要等待。
**这种算法的缺点也很明显**：它无法清除循环引用的垃圾对象。例如：对象a的next属性引用了b，对象b的prev属性引用了a，除此之外再无其他引用，这时引用计数法无法处理这种情况。虽然在工作中我们可以尽量避免这种循环引用的写法，但是在部分业务中，比如游戏开发中，buff和角色等数据对象相互引用是十分常见的情况，

2. 描述标记整理的算法和工作流程
标记整理算法可以理解为标记清除算法的强化版，它主要解决了标记清除算法容易产生内存碎片化的问题。
在标记阶段，标记整理算法和标记清除算法是一样的，它会遍历所有对象，标记出所有的活动对象（可达对象）。在清除非活动对象前，它会先整理活动对象和非活动对象的位置，使活动对象连续存储在内存中，然后再清除所有的非活动对象。

3. 描述V8中新生代存储区垃圾回收的流程
V8中新生代存储主要使用了复制算法和标记整理算法。首先，新生代存储区分为From区和To区，第一次生成的对象会存储再From区，第一轮GC算法开始，From区中所有的对象会被遍历，活动对象会被标记整理并且被复制到To区，然后From区会被清空，最后两个区交换空间完成释放。这个过程中还会存在晋升的情况，当新生代中的对象存活过一代GC流程后，它会被晋升到老生代，如果To区的使用率超过25%，To区的所有对象也会被晋升到老生代。

4. 增量标记算法在何时使用，及工作原理
在V8中，增量标记算法被使用在老生代。
它的原理是：在标记阶段，让代码的进行和垃圾回收穿插进行，全部标记完成后在一并清除垃圾对象。

# 代码题1
1. 
```js
let isLastInStock = fp.flowRight(fp.prop('in_stock'), fp.last)
```

2. 
```js
let getFirstCarName = fp.flowRight(fp.prop, fp.first)
let name = getFirstCarName(cars)
```

3. 
```js
let getAverageDollarValue = fp.flowRight(_average, fp.map(car => car.dollar_value))
let averageDollarValue = getAverageDollarValue(cars)
```

4. 
```js
let sanitizeNames = fp.map(fp.flowRight(fp.toLower, _underscore))
```

# 代码题2
1. 
```js
let ex1 = function(value){
    return fp.map(fp.add, value)
}
```

2. 
```js
let ex2 = function(value){
    let first = fp.first(value)
    console.log(first)
    return first
}
xs.map(ex2)
```

3. 
```js
let maybe = safeProp('name')(user)
let ex3 = function(value){
    let first = fp.first(value)
    console.log(first)
    return first
}
maybe.map(ex3)
```

4. 
```js
class Left extends Maybe {
    map(fn){
        return this
    }
}
let ex4 = function(n){
    try {
        return Maybe.of(parseInt(n))
    } catch (e){
        return Left.of({err: e.message})
    }
}
```
思路：采用either函子的思路，Left继承Maybe函子并重写map方法，来处理异常