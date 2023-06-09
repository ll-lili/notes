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