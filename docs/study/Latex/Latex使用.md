
## 报错问题

### Error: ‘\bibliographystyle‘ invalid for ‘biblatex‘.

[https://blog.csdn.net/qq_34529292/article/details/138138525](‘\bibliographystyle‘ invalid for ‘biblatex‘)

注释掉原文中的\usepackage{biblatex}等和biblatex 有关内容

### 解决Latex编译报错 Font shape `TU/ptm/b/n‘ undefined (Font) using ‘TU/ptm/bx/n‘ instead

[https://blog.csdn.net/veritasalice/article/details/118542772](Font shape `TU/ptm/b/n‘ undefined (Font) using ‘TU/ptm/bx/n‘ instead)

选择用PDFLaTex就可以成功编译出正常的字体

### 使用Letax引用文献一直报错： LaTeX Error: Something‘s wrong--perhaps a missing \item

[https://blog.csdn.net/qq_41368074/article/details/108308334](： LaTeX Error: Something‘s wrong--perhaps a missing \item)

出错修改后，删除生成的临时文件，重新进行编译

　　1、第一步点击Latex编译，可以获得*.aux文件、*.dvi文件、*.log文件以及*.gz文件；
　　2、第二步点击Bibtex编译，可以获得*.blg(性能监视器文件)和*.bbl文件；
　　3、第三步再次点击Latex编译，获得新的*.aux文件、*.dvi文件、*.log文件以及*.gz文件；
　　4、第四步再次点击Latex编译。

### overleaf在一处引用多个文献操作

[https://blog.csdn.net/tenglixin/article/details/141453000?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-141453000-blog-90727123.235^v43^pc_blog_bottom_relevance_base6&amp;spm=1001.2101.3001.4242.1&amp;utm_relevant_index=3](latex一处引用多个文献)

### winedt pdf无法打开

缺少pdf程序
(WinEdt编译提示pdf文件打不开，Cannot Run pdf)[https://blog.csdn.net/Davidietop/article/details/104658778]

### winedt 使用bibtex编译报错

bibtex i found no \citation
(LaTeX编译参考文献“I found no \citation commands---while reading file”问题)[https://blog.csdn.net/ya6543/article/details/112542915]

### latex 所写文章不需要底部页码时

(LaTeX 删除页码)[https://blog.csdn.net/sincerehz/article/details/133813751]

#### 解决方案

除了要在导言区调用 \pagestyle{empty}，还要在 \maketitle 之后紧接着调用\thispagestyle{empty}。

## bibtex转换成bibitem格式

(如何将参考文献从bibtex转换成bibitem格式)[https://blog.csdn.net/XIA_RU/article/details/131557919]

## I found no \bibstyle & \bibdata & \citation command

[Latex 编译报错 I found no \bibstyle &amp; \bibdata &amp; \citation command_found no bibstyle command--CSDN博客](https://blog.csdn.net/qq_41554005/article/details/120711081)

## latex 同一个图片窗插入多个图片

[Latex如何插入多个图片，实现并排排列或者多行多列排列_latex图片并排-CSDN博客](https://blog.csdn.net/weixin_44044161/article/details/116736257)


[利用Overleaf使用Latex插入算法伪代码_overleaf伪代码-CSDN博客](https://blog.csdn.net/qq_45100200/article/details/132171188)



[Latex合并多个公式且居中](https://blog.csdn.net/qq_43733107/article/details/131774488)