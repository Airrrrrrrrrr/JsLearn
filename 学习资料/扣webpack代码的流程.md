# 扣webpack代码的流程

案例网址：https://spa2.scrape.center/

### 一、首先确定是否是webpack代码

![image-20241202160352531](img/image-20241202160352531.png)

这里的r就是加载器，跟进去

![image-20241202160433254](img/image-20241202160433254.png)

### 二、找到加载器的位置，导出加载器

![image-20241202160650197](img/image-20241202160650197.png)

通过全局变量保存加载器，使得加载器能在外部调用。

记得去除加载器初始化，一般在代码的最后一行类似 t()。

### 三、将主文件和多个子文件代码进行整合

![image-20241202160741710](img/image-20241202160741710.png)

在控制台输出webpackJsonp，会得到一个数组，包含各个子文件，在FunctionLocation可以找到子文件的位置，将这些子文件全部复制到一个新文件中；然后找到主文件，主文件就是包含加载器的文件，将主文件复制到新文件中的头部。

### 四、保存实际使用过的代码，摘取需要的代码放到包含加载器函数尾部需要传入的参数中去，缩减代码大小

1.见第二步；

2.在控制器输出window.code,复制后放入包含加载器函数尾部需要传入的参数中。

### 五、编写加密代码

调用需要的加载器函数，编写加密函数代码，获取返回值。