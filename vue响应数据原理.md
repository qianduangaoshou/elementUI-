#### 1.实现vue数据响应的代码
```
window.onload = function () {
        let p = Observe.prototype;
        p.walk = function (obj) {
            for (let key in obj) {
            // 通过使用 hasOwnProperty 来判断是否含有该属性
                if (obj.hasOwnProperty(key)) {
                    this.define(obj[key],key)
                    if (typeof obj[key] === 'object') {
                        new Observe(obj[key])
                    }
                    //如果发现该属性是一个对象的话，使用递归的算法来执行
                }
                
            }
        };
        p.define = function (val,key) {
            Object.defineProperty(
                this.data,
                key,
                {
                    get: function () {
                        console.log('我被获取到了',key);
                        return  val;
                    },
                    set: function (newVal) {
                        console.log('我被设置了新的'+key);
                        console.log('这个新设置的属性值是'+newVal);
                        if (val === newVal) {
                        //注意：这里使用的val是外界传入的数据，不可以使用 data[key]的形式被访问到，因为这会造成循环
                            val = newVal;
                            return val;
                        } else {
                            return val;
                        }
                    }
                }

            )
        };
        function  Observe(obj) {
            this.data = obj;
            this.walk(this.data)
        }
        let app = {
            name: '张宁宁',
            age: 23
        };
        let a = new Observe(app);
    };
```
上面是vue的相应数据变化的原理   
使用到的知识点：  
1：理解对于原型的操作：  
在上面中我们 Observe 函数上使用了原型定义了两种方法： walk函数和 define 函数，使用 new操作符定义了函数的一个实例，定义的这个实例会继承函数原型上的方法  
+，对于这个过程，我们需要知道的是：在这个过程中，发生了什么？  
使用new 操作符定义的过程：  
1.创建一个全新的对象；  
2，这个新对象会被执行原型链接  
3，这个新对象会被绑定到函数调用的this  
4,如果函数没有返回其他对象，那么new表达式中的函数调用自动返回这个对象  
也就是说：我们通过使用 new Observe 构造出的一个对象这个函数使用this值进行的数据绑定， this值指向的是新创建的对象，在这段代码中，就是 a
2,使用 defineProperty 定义的get set方法：  
对于一个对象我们使用 get set方法，动态获取属性的值，是对象的属性时可响应式的，有两种方法：  
```
var myObj = {
    get a() {
      return 2
    }
    set a(newVal) {
      return newVal
    }
};

```
第二种方法：使用 defineProperty 方法：
```
Object.defineProperty(  
  myObj, // 表示目标对象  
  'a', // 表示要进行相应的属性  
  { // 应用 get set方法  
    get: function () {  
      return this.a;  
    },  
    set: function (newVal) { 
      return newVal;  
    }
  }
)
```
#### 2.使用Vue实现数据驱动的原理图
当vue中数据变化的时候触发视图更新，反过来，视图更新引起的数据变化也会相应要data数据中,下图是使用vue导致的数据驱动变化图
![数据驱动](http://shellming.com/img/vue-data-binding/2.png)
使用 vue实例化的时候，会遍历传给data中的数据，将这些数据转化为getter 和 setter  
每一个实例对象都有一个 watcher 实例对象，在渲染组件的时候将属性记录为依赖，当数据中的setter变化的时候，变化之后，会通知 watcher (观察者)重新计算，从而导致

> vue tips>
在实际上使用 vue 的时候要注意，一个页面上不能有两个路由对象，因为路由变化为 `active` 的路由只能有一个

细心，耐心， 恒心

> 在 vue 中获取dom 的时候，使用常规的方式获取到 dom 的时候, 要保证 dom 元素已经被获取到了

```
mounted () {
  this.$nextTick (() => {
        // 这里执行 dom 操作
  })
}
```
```
使用 object.keys () 实现的是获得对象的属性
这个方法返回的是一个数组，数组中的是字符串，返回的数组中包含的是遍历对象的属性集合

```
