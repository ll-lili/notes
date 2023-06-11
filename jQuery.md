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

##### jQuery对象复制`.clone()`

* clone()可以复制jQuery对象

* #### [.clone( [withDataAndEvents \] [, deepWithDataAndEvents ] )](https://api.jquery.com/clone/#clone-withDataAndEvents-deepWithDataAndEvents)

* 第一个参数：一个布尔值，是否应将事件处理程序与元素一起复制。最新版本默认值都为false，有版本差异

* 第二个参数：是否应复制克隆元素的所有子元素的事件处理程序和数据。最新版本默认值都为false，有版本差异

* 第二个参数：其值与第一个参数的值匹配（默认为 `false` ）

```javascript
const $firstLi = $('ul li:first-child')
const $cloneLi = $firstLi.clone()
$('.ul2').append($cloneLi)
```

##### jQuery 容器操作（DOM插入：外部，父级）

* `.unwrap([selector])`从 DOM 中删除匹配元素集的父元素，将匹配的元素保留在其位置。

  ```javascript
  /**
  	selector要检查父元素的选择器。如果元素的父元素与选择器不匹配，则不会解开该元素的包装。
  */
  var $p = $( "p" );
  $( "button" ).on( "click", function() {
    if ( pTags.parent().is( "div" ) ) {
      pTags.unwrap();
    } else {
      pTags.wrap( "<div></div>" );
    }
  });
  ```

* `.wrap(wrappingElement)`将 HTML 结构包装在匹配元素集中的每个元素外面。

  ```javascript
  /**
  	wrappingElement: 选择器、元素、HTML 字符串或 jQuery 对象，指定要环绕匹配元素的结构。
  	当您传递包含多个元素的 jQuery 集合或与多个元素匹配的选择器时，将使用第一个元素。
  */
  $('p').wrap('<div></div>')
  $('p').wrap(document.createElement('div'))
  $('p').wrap($('.wrap'))
  /**
  	.wrap( function )
  	返回 HTML 内容或 jQuery 对象的回调函数，以环绕匹配的元素。
  	接收集合中元素的索引位置作为参数。在函数中， this 引用集合中的当前元素。
  */
  
  $( ".inner" ).wrap(function(index) {
    return "<div class='" + $( this ).text() + "'></div>";
  });
  ```

* `wrapAll(wrappingElement)` 将HTML 结构包装在匹配元素集中的所有元素外面。
* `wrapInner(wrappingElement)` 将 HTML 结构包装在匹配元素集中每个**元素的内容**外面。

##### jQuery添加子元素（DOM插入：内部）

* `.append()`将**参数指定的内容**插入到匹配元素集中每个**元素的末尾**。

  ```javascript
  $('.box1').append('<div class="box2"></div>')
  ```

* `.appendTo()`将**匹配元素集中的每个元素**插入到目标的末尾。

  ```javascript
  $('<div class="box2"></div>').appendTo('.box')
  ```

* `.prepend()`将参数指定的内容插入到匹配元素集中每个元素的开头。

* `.prependTo()`将匹配元素集中的每个元素插入到目标的开头。
* `.html()`获取匹配元素集中第一个元素的 HTML 内容，或设置每个匹配元素的 HTML 内容。
* `.text()`获取匹配元素集中每个元素的组合文本内容（包括其后代），或设置匹配元素的文本内容。

##### jQuery添加同级元素（DOM插入：外部，同级）

* `.after()`在匹配元素集中的每个**元素之后**插入由参数指定的内容。

* `.before()`在匹配元素集中的每个**元素之前**插入由参数指定的内容。
* `insertAfter()`在目标之后插入匹配元素集中的每个元素。

* `insertBefore()`在目标之前插入匹配元素集中的每个元素。

##### jQuery删除DOM

* `.detach([selector])`从 DOM 中删除匹配的元素集。
* `.remove([selector])`从 DOM 中删除匹配的元素集（和绑定事件）。
* `.empty()`从 DOM 中删除匹配元素集的所有子节点。
* `.unwrap([selector])`从 DOM 中删除匹配元素集的父元素，将匹配的元素保留在其位置。

##### jQuery 替换DOM

* `replaceAll`()将每个目标元素替换为匹配的元素集。
* `replaceWith()`将匹配元素集中的每个元素替换为提供的新内容，并返回已删除的元素集。

##### jQuery操作属性

