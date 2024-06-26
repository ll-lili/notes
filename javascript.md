### 递归

- 函数直接或间接调用自身

- 避免无限递归（需要终止条件）（执行栈溢出），对比死循环，死循环不会报错和栈溢出。

- 执行栈（call stack）
  - 任何代码的执行都必须有一个执行环境，执行环境为代码的执行提供支持
  - 执行环境是放到执行栈中的
  - 每个函数的调用，都需要创建一个函数的执行环境，函数调用结束，执行环境销毁。
  - 执行有相对固定的大小，超出报错。（栈溢出）

```js
/** 阶乘
	f(1): 1
	f(2): 2 * f(2 -1)
*/
function f (n) {
	if (n === 1) {
		return 1
    }
    return n * f(n - 1)
}

/**
	斐波拉契数列: 1 1 2 3 5 8 13 21 ...
	f1: 1
	f2: 1
	f3:  f(3 -1) + f(3 -2 )
*/
function f(n) {
	if (n === 1 || n  ===2 ) {
        return 1
    }
    return  f(n-1) + f(n-2)
}
```

