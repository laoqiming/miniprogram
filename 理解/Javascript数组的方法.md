# javascript数组的方法



## 改变原数组的操作



### push(newElement1, newElement2, ..., newElementX)

向数组追加元素，返回追加后的数组长度

```javascript
let arr = []
let l = arr.push(1) // arr == [1], l == 1
l = arr.push(2,3) // 一次push多个， arr == [1, 2, 3], l == 3
```


### unshift(newElement1, newElement2, ..., newElementX)

向数组前面添加元素，返回追加后的数组长度

```javascript
let arr = []
let l = arr.unshift(1) // arr == [1], l == 1
l = arr.unshift(2, 3) // arr == [2, 3, 1], l == 3
```


### pop()

移除数组最后一个元素，返回被移除的元素

```javascript
let arr = [1, 2, 3]
let o = arr.pop() // arr == [1, 2], o == 3
o = arr.pop() // arr == [1], o == 2
o = arr.pop() // arr == [], o == 1
o = arr.pop() // arr == [], o == undefined
```



### shift()

移除数组第一个元素，返回被移除的元素

```javascript
let arr = [1, 2]
let o = arr.shift() // arr == [2], o == 1
o = arr.shift() // arr == [], o == 2
o = arr.shift() // arr == [], o == undefined
```



### splice(index, howMany, item1, ..., itemX)

移除数组指定的元素，添加新元素，并返回被删除的元素组成的数组

```
let arr = [1, 2, 3, 4]
let del = arr.splice(1, 2, 'a', 'b') // arr == [1, "a", "b", 4] , del == [2, 3]

arr = [1, 2, 3, 4]
del = arr.splice(1, 0, 'a', 'b') // arr = [1, "a", "b", 2, 3, 4], del == []

arr = [1, 2, 3, 4]
del = arr.splice(-1, 0, 'a', 'b') // arr = [1, 2, 3, "a", "b", 4], del == []

arr = [1, 2, 3, 4]
del = arr.splice(2) // arr = [1, 2], del == [3, 4]

arr = [1, 2, 3, 4]
del = arr.splice() // arr = [1, 2, 3, 4], del == []
```



### sort(sortby)

对数组**按照字符编码的顺序**进行排序，修改原数组并返回原数组

```javascript
let arr = [2, 1, 4, 5, 3]
let arr2 = [1, 2, 3, 4, 5]
let l = arr.sort()  // arr == l == [1, 2, 3, 4, 5]
l == arr // true
l == arr2 // false

arr = [10, 2, 1, 200, 3]
arr.sort() // arr == [1, 10, 2, 200, 3]

arr.sort((a, b) => a-b) // arr == [1, 2, 3, 10, 200]
```



### reverse()

对数组元素倒序排序

```javascript
let arr = [1, 2, 3, 4, 5]
let l = arr.reverse() // l == arr == [5, 4, 3, 2, 1]
```



### forEach((value, index, array) => {})

遍历数组，并提供在回调函数中修改原数组

```javascript
let arr = [1, 2, 3, 4, 5]
arr.forEach((v, i, a) => { // v 当前值， i 当前下标 a 原数组
	a[i] = v * 2
}) // arr == [2, 4, 6, 8, 10]
```

> forEach过程中不能跳出循环



### fill(value, startIndex, endIndex)

使用指定的值从startIndex 到 endIndex-1填充数组

```javascript
let arr = [1, 2, 3, 4, 5]
arr.fill(7, 3, 4) // arr == [1, 2, 3, 7, 5]
```



## 不修改原数组,返回新数组的操作



### concat(array1, array2, ..., arrayX)

连接两个数组，返回连接后的新数组，原有数组不变

```javascript
let arr1 = [1, 2, 3], arr2 = [4, 5, 6]
let arr3 = arr1.concat(arr2) // arr3 == [1, 2, 3, 4, 5, 6]
```



### slice(start, end)

从数组中选择下标从start到end-1的元素并返回这些元素组成的新数组

```javascript
let arr = [1, 2, 3, 4, 5]
let l = arr.slice(1, 4) // l == [2, 3, 4]
```



### map((value, index, array) => {})

```javascript
let arr = [1, 2, 3, 4, 5]
let arr2 = arr.map((v, i, a) => { // v 当前值， i 当前下标 a 原数组
	v = v * 2
}) // arr == [undefined, undefined, undefined, undefined, undefined]

arr2 = arr.map((v, i, a) => { // v 当前值， i 当前下标 a 原数组
    v = v * 2
	return v
}) // arr == [2, 4, 6, 8, 10]
```

> 如果需要返回修改后的新数组，必须在回调函数中返回修改后的值



### filter((value, index) => {})

过滤符合条件的元素并返回符合条件的元素组成的新数组

```javascript
let arr = [1, 2, 3, 4, 5]
let arr2 = arr.filter((v, i) => { // v 当前值， i 当前下标
	return v > 3
}) // arr2 == [4, 5]

arr2 = arr.filter((v, i) => { // v 当前值， i 当前下标
	return i < 3
}) // arr2 == [1, 2, 3]
```



## 条件判断



### every((value, index) => {})

只有数组每一个元素都符合条件才返回true，否则返回 false

```javascript
let arr = [1, 2, 3, 4]
let b = arr.every(v => v < 5) // b == true
b = arr.every(v => v > 5) // b == false
```



### some((value, index) => {})

只要数组有一个元素符合条件就返回true，否则返回 false

```javascript
let arr = [1, 2, 3, 4]
let b = arr.some(v => v == 2) // b == true
b = arr.some(v => v == 5) // b == false
```



### includes(searchElement, fromIndex)

判断数组是否包含指定元素

```javascript
let arr = [1, 2, 3, 4]
let b = arr.includes(3) // b == true
b = arr.includes(5) // b == false
```



## 查找



### indexOf(item, startIndex)

查找元素在数组中的下标，返回数组下标

```javascript
let arr = [1, 2, 3, 4]
let i = arr.indexOf(3) // i == 2
i = arr.indexOf(5) // i == -1
```



### lastIndexOf(item, startIndex)

从数组后面开始查找元素在数组中的下标，返回数组下标

```javascript
let arr = [1, 2, 3, 4, 3, 5]
let i = arr.lastIndexOf(3) // i == 4
i = arr.lastIndexOf(3, 1) // i == -1
```



### find((value, index, array) => {})

查找数组中第一个符合条件的元素，返回该元素

```javascript
let arr = [{name: '张三', id: 1}, {name: '李四', id: 2}]
let v = arr.find(v => v.id == 2) // v == {name: '李四', id: 2}
```

> 找不到返回undefined



### findIndex((value, index, array) => {})

查找数组中第一个符合条件的元素，返回数组下标

```javascript
let arr = [{name: '张三', id: 1}, {name: '李四', id: 2}]
let i = arr.findIndex(v => v.id == 2) // i == 1
```

> 找不到返回-1



## 迭代累加



### reduce((total, value, index, array) => {}, initValue)

把数组的每一个元素按回调函数定义的规则累加

```javascript
let arr = [1, 2, 3, 4, 5]
let sum = arr.reduce((n, v) => n + v, 0) // sum == 15
```

> 第一个迭代，total = initValue，value = arr[0]，如果initValue未提供，则total = arr[0]，value = arr[1]
>
> 如果initValue未提供且数组只有一个元素，直接返回arr[0]，不执行迭代



### reduceRight((total, value, index, array) => {}, initValue)

同reduce，只是从后向前迭代

```javascript
let arr = [1, 2, 3, 4, 5]
let sum = arr.reduce((n, v) => n + v, 0) // sum == 15
```

> 第一个迭代，total = initValue，value = arr[arr.length-1]，如果initValue未提供，则total = arr[arr.length-1]，value = arr[arr.length-2]
>
> 如果initValue未提供且数组只有一个元素，直接返回arr[0]，不执行迭代



## 字符串操作

### split(separator, howMany)

把字符串按指定分割符转换为字符串数组并返回数组

```javascript
let str = '1,2,3,4,5,6,7,8,9'
let arr = str.split(',') // arr == ["1", "2", "3", "4", "5", "6", "7", "8", "9"],注意，返回的是字符串数组

str = 'abcde'
arr = str.split() // arr == ['abcde']
arr = str.split('') // arr == ["a", "b", "c", "d", "e"]
arr = str.split('', 3) // arr == ["a", "b", "c"]

str = 'a|b,c d'
arr = str.split(/[|, ]/) // arr = ["a", "b", "c", "d"]
```

