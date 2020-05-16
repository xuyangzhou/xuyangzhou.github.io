---

title: js 数组方法总结 之 一道面试题引发的血案

date: 2019-11-07 10:09:31

tags: [js,数组方法]

categories: js

toc: true # 是否启用内容索引

---

在前端面试当中高频出现的一道面试题，让我产生了把js数组的方法总结一下的念头，呐，就是下面这道题目引发的血案！看了解析先别撤哦，后面留了几道提升题目，你值得拥有（皮这一下很开心~~）

```javascript
[1, 2, 3].map(parseInt);

// 解析：

[1, 2, 3].map((item, index, arr) => parseInt(item, index));

parseInt(1, 0); // 1

parseInt(2, 1); // NaN

parseInt(3, 2); // NaN

// 故：结果为 [1, NaN, NaN]

// 考点分析：

map(function(currentValue, index, arr){})方法回调函数的参数

// currentValue 当前元素的值

// index 当前元素的索引值

// arr 原数组

parseInt(string, radix) 函数的参数

// radix该值介于 2 ~ 36 之间,如果省略该参数或其值为 0，则数字将以 10 为基础来解析

// 如果它以 “0x” 或 “0X” 开头，将以 16 为基数

// 如果该参数小于 2 或者大于 36，则 parseInt() 将返回 NaN

```

### 转化方法

> toString() 返回数组中每个值的字符串形式拼接而成的一个以逗号分隔的字符串

```javascript

[1, 2, '3'].toString();  // "1,2,3"

```

> valueOf() 返回原数组

```javascript
[1, 2, '3'].valueOf(); // [1, 2, "3"]
```

> join() 通过指定的分隔符进行分隔的

```javascript
[1, 2, '3'].join(); // "1,2,3"

[1, 2, '3'].join(''); // "123"

[1, 2, '3'].join('-'); // "1-2-3"
```

### 栈方法 LIFO(Last-In-First-Out,后进先出的数据结构)

> push() 添加到数组末尾，并返回修改后数组的长度

```javascript
[1, 2].push(3); // 3,原数组: [1, 2, 3]

[1, 2].push([3, 4]); // 3, 原数组：[1, 2, [3, 4]]

[1, 2].push(3,4); // 4, 原数组：[1, 2, 3, 4]

```

> pop() 从数组末尾移除一项，返回移除的项

```javascript
[1, 2].pop(); // 2, 原数组： [1]
```

### 队列方法(先进先出)

> unshift() 在数组前添加，返回修改后数组的长度

```javascript
[1, 2].unshift(2); // 3, 原数组：[2, 1, 2]
```

> shift()

```javascript
[1, 2].shift(); // 1, 原数组：[2]
```

### 重排方法

> reverse() 反转数组项的顺序，返回反转之后的数组

```javascript
[1, 2, 3].reverse(); // [3, 2, 1]
```

> sort() 排序

```javascript
// 当不带参数调用sort（）时，数组元素以字母表顺序排序（如有必要将临时转化为字符串进行比较）

[1, 3, 11, 2].sort(); // [1, 11, 2, 3]

['2','1','3','11'].sort(); //  ["1", "11", "2", "3"]

// 给sort方法传递一个比较函数，该函数决定了它的两个参数在排好序的数组中的先后顺序：

// 假设第一个参数应该在前，比较函数应该返回一个小于0的数值，反之，

// 假设第一个参数应该在后，函数应该返回一个大于0的数值，

// 并且，假设两个值相等，函数应该返回0

[1, 11, 2, 22, 33, 3].sort((a, b) => a - b); // [1, 2, 3, 11, 22, 33]

// 可以指定对象的属性进行排序

[{name:'zs',age:16},{name:'ls', age:25}].sort((a, b) => {

​    if(a['name'] < b['name']) {

​        return -1;

​    } else if (a['name'] > b['name']) {

​        return 1;

​    } else{

​        return 0;

​    }

})
```

### 操作方法

> concat() 连接两个或多个数组，不会改变原数组，返回连接的新数组

```javascript
[1, 2].concat([3, 4]); // [1, 2, 3, 4]
```

> slice() 从已有数组中返回选定的元素，不会改变原数组，返回截取之后的新数组

```javascript
[1, 2, 3, 4].slice(1, 3); // [2, 3]
```

> splice() 向/从数组中添加/删除元素，然后返回被删除的元素

```javascript
[1, 2].splice(0, 1); // [1]

[1, 2].splice(0, 0, 3); // [], 原数组[1, 2, 3]
```

### 位置方法

> indexOf() 从数组开头（0）开始查找，返回查找项在数组中的位置，如果没有返回-1

```javascript
[1, 2, 3].indexOf(3); // 2

[1, 2, 3].indexOf(4); // -1
```

> lastIndexOf() 从数组末尾开始查找，返回查找项在数组中的位置，如果没有返回-1

```javascript
[1, 2, 3].lastIndexOf(3); // 2
```

### 迭代方法

> every() 对数组中的每一项运行给定函数，如果该函数对每一项都返回 true，则返回 true

```javascript
[1, 2, 3].every(item => item > 0); // true
```

> some() 对数组中的每一项运行给定函数，如果该函数对任一项返回 true，则返回 true

```javascript
[1,2,3].some(item => item > 2); // true
```

> filter() 对数组中的每一项运行给定函数，返回该函数会返回 true 的项组成的新数组数组，不改变原数组

```javascript
[1, 2, 3].filter(item => item > 2); // [3]
```

> forEach() 对数组中的每一项运行给定函数, 没有返回值

> map() 对数组中的每一项运行给定函数，返回每次函数调用的结果组成的新数组，不改变原数组

```javascript

[1, 2, 3].map(item => item  2); // [2, 4, 6]

```

### 归并方法

> reduce(callback,[initialValue]) 对数组中的每个元素执行一个由callback提供的reducer函数(升序执行)，将其结果汇总为单个返回值

```javascript
[1, 2, 3, 4].reduce((prev, cur) => prev + cur); // 10

[1, 2, 3, 4].reduce((prev, cur) => prev + cur, 10); // 20
```

> reduceRight(callback,[initialValue])  和reduce方法相同，执行顺序变为从最后一项开始

### 测试题

```javascript
[1, 2, 3].map(parseInt);    // [1, NaN, NaN]

==> [1, 2, 3].map((item, index, arr) => parseInt(item, index));

[1, 2, 3].filter(parseInt); // [1]

[1, 2, 3].every(parseInt);  // false

[1, 2, 3].some(parseInt);   // true

[1, 2, 3].reduce(parseInt); // 1

==> [1, 2, 3].reduce((prev, cur) => parseInt(prev, cur))
```
