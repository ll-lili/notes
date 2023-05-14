#### vim基础操作（普通模式，插入（编辑）模式，命令模式，可视模式）

* vim进入vim编辑器
* :q退出
* i(insert)进入编辑模式:当前字符前插入
* I:当前行行首插入
* a(append)进入编辑模式：当前字符后插入
* A：当前行行尾插入
* o(open a line below)进入编辑模式 ：当前字符下面一行插入
* O：当前行的上一个行插入
* esc回到normal模式
* :wq保存退出
* :set nu设置行号 :set nonu取消设置行号
* :vs 竖分屏 :sp横分屏 :q退出
* Normal模式下使用v进入visual（可视模式）进行选择，d删除
* V选择行
* control + v 方块选择

#### 插入模式技巧

* 进入vim之后，使用i/a/o进入插入模式
* Ctrl + h 删除上一个字符
* Ctrl + w 删除上一个单词
* Ctrl + u删除当前行
* 快速切换insert和normal，ctrl + c 或者ctrl + [
* gi快速回到上次编辑的位置