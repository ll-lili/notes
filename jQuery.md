#### jQuery核心函数

* 引入jQuery库，其实就是想网页添加了一个新的函数$(jQuery)

* $是jQuery中的核心函数，jQuery的所有功能都是通过该函数来实现的

##### 核心函数的功能

  1. 将它作为工具类使用

     ```js
     $.isFunction(val) // 判断是否为函数
     $.isArray(val) // 判断是否为数组
     $.isNumberic(val) // 判断是否为数字
     ```

  2. 将它作为函数使用

     * jQuery(函数)
     * jQuery(选择器)
     * jQuery(DOM对象)
     * jQuery(HTML代码)

     ```javascript
     /**
     	将函数作为参数：文档就绪函数
     	相当于：document.addEventlisten('DOMContentLoaded', function(){})
     */
     $(function(){
         //...
     })
     /**
     	将选择器作为参数
     	查找网页中的元素
     	相当于document.querySelectorAll('div') 
     	区别：原生DOM对象，jQuery：包装过的对象
     	jQuery对象提供很多新的方法
     	jQuery对象不能直接调用原生DOM的方法
     	jQuery对象命名是使用$开头，加以区分
     */
     
     $('div').click(function(){
         // ..
     })
     /**
     	将DOM对象作为参数
     	将DOM对象转换为jQuery对象，从而使用jQuery方法
     */
     $(document).reday(function(){
         //...
     })
     /**
     	将HTML代码作为参数
     	根据HTML代码，创建元素
     	相当于document.createElement('h1').innHTML('大标题')
     */
     var $h1 = $('<h1>大标题</h1>')
     
     // https://www.lanmper.cn/jquery
     ```

#### jQuery对象

* 通过jQuery核心函数获取到的对象就是jQuery对象
* jQuery对象是jQuery中定义的对象
* 提供很多简单易用的api,简化原生DOM操作
* jQuery对象本质是一个DOM对象的数组（类数组）
* 可以通过索引获取jQuery对象中的DOM对象
* 修改jQuery对象时，会自动修改jQuery中所有的元素($('div').attr('id')会获得第一个元素的id)
* 通常情况下jQuery对象方法的返回值依然是一个jQuery对象，从而可以进行链式调用

##### jQuery对象操作class

```javascript
$('.box').addClass('box1')
$('.box').addClass(['box1', 'box2'])
// 函数作为参数，函数会调用多次，由jQuery对象的length决定
$('.box').addClass(function(index, className){
    if (index % 2 === 0 ) {
        // 回调函数中this是当前元素DOM对象,调用原生对象方法
        // this.classList.add('box3')
        // $(this)jQuery对象，调用jQuery对象方法
        // $(this).addClass('box4')
        return 'box5'
    }
	// 函数返回值将成为当前元素的class
})
/**
	其他class操作
	hasClass() 检查jQuery对象是否包括某个class,返回一个boolean
	removeClass() 移除class
	toggleClass() 在元素中切换类名
*/
```

