# js的尾后逗号

在看源码时，经常看到下面的代码

```js
  var source;
  if (typeof src === 'string') {
    source = { url: src, };
  } else if (isArrayBuffer(src)) {
    source = { data: src, };
  } else if (src instanceof PDFDataRangeTransport) {
    source = { range: src, };
  }
```

一开始看到`source = { url: src, };`中`src`后面的逗号时，就觉得很奇怪，这个逗号多次一举，后来看到好多都这么写，就越来越疑惑。直到看到下面的介绍。

[尾后逗号](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Trailing_commas)

> 作用很单纯，就是为了`版本管理`起来更加清晰明了。

### 字面值的尾后逗号

1.  数组

```js
var arr = [
  1, 
  2, 
  3, 
];
```

> 当数组尾后逗号多于一个时，忽略最后一个。

2. 对象

```js
var object = {
  a : 1,
  b : 2,
}
```



### 函数的尾后逗号

1. 函数参数

```js
function add(a,b,){
  
}
```

2. 函数调用

```js
add(a,b,);
```

### 解构赋值的尾后逗号

1. 数组

```js
var [a,b,c,] = [1,2,3];
```

2. 对象

```js
var {a,b,} = {a : 1,b : 2}
```

