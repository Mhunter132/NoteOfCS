# VIM命令学习笔记

## 1.vim进入和退出保存

**启动vim**：vim  \<filename\>   * 打开文件

i 进入insert模式

versiom=normal 编辑完文件

esc 键是退出，进入末行模式，

~~~vim
:w 保存文件但不退出vi
:w file 将修改另外保存到file中，不退出vi
:w! 强制保存，不推出vi
:wq 保存文件并退出vi
:wq! 强制保存文件，并退出vi
:q 不保存文件，退出vi
:q! 不保存文件，强制退出vi
:e! 放弃所有修改，从上次保存文件开始再编辑
~~~

**文件命令**

1. 打开单文件/同时打开多个文件

~~~vim 
vim file1 file2 ---
mkdir  <filename>   //创建文件
mkdir -p  <文件/filename.txt> //创建文件和文件
mkdir -m  <filename.txt>  用于对新建目录设置存取权限，也可以用 chmod 命令进行设置。
~~~

2. 操作文件命令

     - /text 是匹配字符，N键下一个

     -  ？text 反向查找，N下一个

     - vim中有一些特殊字符在查找时需要转义　　.*[]^%/?~$

     - ~~~
       :e ignorecase  //忽略大小写
       ~~~

     - ~~~vim
       :e noignorecase  //不忽略大小写
       ~~~

     - ~~~vim
       :e hlsearch   // 高亮所有搜索结果
       ~~~

     - ~~~  vim 
       nohlsearch   // 取消高亮显示 n键查看下一个显示
       ~~~

     - ~~~
       :set incsearch  //逐步搜索
       ~~~

     - ~~~ 
       :set wrapscan //重新搜索,在搜索到偷吻加或者尾部时,返回继续搜索
       ~~~










- 在新文件打开文件  //打开了文件，紧接着执行修改操作

~~~
:open filename
~~~

- 在新窗口中打开文件

~~~vim
:split file
~~~

- 切换到下一个文件

~~~vim
:bn
~~~

- args

~~~vim
查看当前文件列表
~~~

- 打开远程文件  //巧记：执行个ftp协议请求

~~~vim
:e ftp://192.168.10.76/abc.text
:e \\qadrive\test\abc.txt
~~~

