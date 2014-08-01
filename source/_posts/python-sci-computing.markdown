author: richardkwo
comments: true
date: 2012-10-06 17:19:58+00:00
layout: post
slug: python-sci-computing
title: 用Python做科学计算 (+网络分析)
wordpress_id: 194
categories:
- 科研开发
- 计算机科学

---

科学计算，花样繁多，各有所长。

大家一般比较熟悉MATLAB这样的综合科学计算平台，需要符号计算的童鞋可能会使用Mathematica，统计爱好者喜欢R，对性能要求高的可能就直接上C/C++、Fortran 编译+运行（顺带说一句，C/C++用于科学计算的库还是挺多的，比如[LAPACK](http://www.netlib.org/lapack/)用于线性代数，[GSL](http://www.gnu.org/software/gsl/)提供复数运算、随机数、微分方程等许多数值算法）。

那么为什么还要用Python做科学计算呢？笔者觉得主要有以下几个原因：

<!-- more -->
	
  1. 轻量级。用Python写的科学计算脚本只需要Python解释器和相应的库即可运行，不需要MATLAB那样庞大的平台。
  2. 超强的扩展性。谁让咱爹是Python啊！所以可以相当方便地完成一些其他工具很难搞定的任务，比如把电子邮件的通信抓取下来，表示成网络，统计并绘图（[here](http://networkx.lanl.gov/examples/graph/unix_email.html)）。用MATLAB、C++搞这个，蛋疼。。
  3. Interactive. 和MATLAB用起来一个感觉，配合后面提到的 iPython/Spyder，既可以在命令行调戏，也可以写成脚本程序慢慢搞，非常方便。因为做科研往往需要try small ideas，总是编译运行太累人。同时，Python交互式的解释器和良好的错误处理也使得debug更方便
  4. Free and open source. 这个不用说了，MATLAB还是很贵的（MATLAB有开源的替代品[GNU Octave](http://www.gnu.org/software/octave/)，但是速度奇慢。听说[Scilab](http://www.scilab.org/)的功能可以和MATLAB匹敌，而且开源，大家可以试试）
  5. 丰富的库。最基本的两个库是[NumPy](http://numpy.scipy.org/)和[SciPy](http://www.scipy.org/)，前者提供一般的数值计算、线性代数功能，后者依赖于前者，实现了更多的科学计算功能。另外本文还会提到用于网络分析的[NetworkX](http://networkx.lanl.gov/)。还有很多其他的库待大家发掘。	
  6. 漂亮的可视化。[matplotlib](http://matplotlib.org/)库提供非常强劲的绘图功能，语法与MATLAB相近，但是个人认为比MATLAB画得更好看。




## 安装


Linux用户安装很简单，很多发行版从源里就可以安装。以笔者使用的Ubuntu为例，键入
``` bash
$ sudo apt-get install python-numpy python-scipy python-matplotlib ipython python-networkx
```
就搞定了。如果嫌发行版提供的版本太旧，可以上官网下载安装。追求性能的童鞋还可以自行编译安装。

另外，Python库还有一个自动下载安装的好方法：使用[pip](http://pypi.python.org/pypi/pip)，比如输入命令
``` bash
$ sudo pip install networkx
```
就可以乖乖地下载安好。

Windows用户最好还是从官网一个个下安装包安装。或者，可以使用Enthought一体化的科学计算Python发行版（[Enthought Python Distribution](http://www.enthought.com/products/epd.php)），里面应有尽有啦。Scipy、Numpy就是Enthought公司赞助的。


## 开始使用




### iPython 交互式终端


使用[iPython](http://ipython.org/)，可以交互式地做科学计算。iPython是一个加载了科学计算包的Python解释器，同时对画图等功能提供更好的支持。

在Linux，打开终端，输入
``` bash
$ ipython --pylab
```

就进入iPython的科学计算模式，试试看

``` python
In [1]: x=linspace(0,2*pi,1000)
In [2]: y=sin(x)
In [3]: plot(x,y,'-k')
```

是不是很像MATLAB呢？

[![使用iPython](/images/python-sci-computing/Figure-1_001.png)](/images/python-sci-computing/Figure-1_001.png)









### Spyder 开发环境


[Spyder](https://code.google.com/p/spyderlib/)是一个专门用于Python科学计算的IDE，貌似知道的人很少，这里推荐一下。有童鞋可能会问，反正都是Python，用Eclipse这种IDE来写不成么？笔者最早也是用Eclipse写计算的脚本，后来试了试Spyder，顿时感觉方便许多。

[![Spyder 截屏](/images/python-sci-computing/Workspace-2_004.png)](/images/python-sci-computing/Workspace-2_004.png) 

Spyder有这些特点：

1. 内部集成iPython。
2. 完整的Python编辑器。
3. 运行时可以监视变量。
4. **增量式加载，边写边算。**


最后这条描述有些不知所云，但笔者感觉这是Spyder最爽的地方。有多爽呢？我只能说爽如MATLAB（我咋老提MATLAB。。。）

举个例子，现在我写了个脚本loaddata.py，从文件里读数据，保存在变量data里面。然后，我准备对这个数据画个图，画图的代码写在plotdata.py里面。在Spyder里，先写好loaddata.py，然后F5执行，注意选择"_Execute in current Python or IPython interpreter_"，于是数据加载进来了；

![Execute in current Python or IPython interpreter](/images/python-sci-computing/Run-.temp_.py_001.png)

再切换到plotdata.py，F5，图出来了。赫然一条直线，于是突然想做线性回归。这时只需要在Spyder里，新建一个linearReg.py，输入对data变量做线性回归的代码（不用写成函数，脚本就行），按F5即可！也就是说只要保持同一个Interpreter，Spyder里面执行一次，就相当于import一次：如果linearReg运行以后有改动，修改后就再F5；如果数据发生了变化，随时可以切换到loaddata.py，按F5。简单地说，Spyder实现了** Scripting 和 Interactive 两种模式的完美融合**。

不过熟悉MATLAB的童鞋要注意，**Spyder的加载模式还是不同于MATLAB**。比如说，MATLAB里面写了一个函数文件funxx.m，后来又做了改动，那么下次调用该函数的时候改动就生效了，因为MATLAB每次都重新执行一边funxx.m的**代码**。但是，在Spyder里面修改过funxxLib.py（里面定义了funxx这个函数）以后，要使得改动生效，就必须**按F5加载**到解释器里面去。

### NumPy/SciPy


NumPy/SciPy是Python下面科学计算最常用的库。NumPy提供基本的运算、线性代数、随机数功能，在此基础上，SciPy提供了诸如特殊函数、微积分、优化、Fourier变换等功能。

如果熟悉MATLAB等操作向量、矩阵的语言，上手会很容易：

  1. 先从官网的NumPy教程开始：[Tentative NumPy Tutorial](http://www.scipy.org/Tentative_NumPy_Tutorial
  2. 然后弄清楚MATLAB和NumPy有哪些异同：[NumPy for Matlab Users](http://www.scipy.org/NumPy_for_Matlab_Users)
  3. 大概看看SciPy提供了哪些功能：[http://docs.scipy.org/doc/scipy/reference/tutorial/](http://docs.scipy.org/doc/scipy/reference/tutorial/)	
  4. 然后就在用中学啦，不明白的地方看文档即可（Spyder集成了便捷的help），just like this:


[![](/images/python-sci-computing/怎样画马1.jpg)](/images/python-sci-computing/怎样画马1.jpg)



这个页面上有很多值得参考的资料：[Additional Documentation for SciPy/NumPy](http://www.scipy.org/Additional_Documentation?action=show&redirect=Documentation).





###  用matplotlib绘图


笔者是个绘图控+排版控，科研和大作业约有一半的动力来自于画出漂亮的图、排出漂亮的文档。LaTeX当然是学术排版的不二之选，而绘图就是open question了。笔者为此试过很多软件，屡试也不爽，但matplotlib令我满意，尤其和Python的计算功能相得益彰。

笔者方才打开[matplotlib](http://matplotlib.org/)官网，看到了matplotlib作者因癌症与世长辞的消息，不禁惋惜。

matplotlib做图是靠Scripting的，不能直接在图上拖来拖去，但语法和MATLAB非常相似（不得不说，MATLAB做图的一套语法设计得相当好。笔者试过gnuplot，蛋疼而知返；Mathematica做图命令，方括弧里冗长的配置也每每令笔者困惑）。

通过下面这个例子，读者应该可以对用matplotlib编程绘图有直观的印象。

``` python
import numpy as np from scipy.stats
import norm import matplotlib.pyplot as plt # many people favor this fashion from matplotlib.patches 
import Polygon 
x = np.linspace(-5,5,200) 
y = norm.pdf(x, ) 
x2 = np.linspace(-2,2,10) 
y2 = norm.pdf(x2, ) 
x3 = np.linspace(-2,2,100)
y3 = norm.pdf(x3, ) 
vertices = [(-2,0)] + zip(x3, y3) + [(2,0)] 
poly = Polygon(vertices, facecolor = 'orange', edgecolor = 'k') 
fig = plt.figure(figsize=(5,5)) 
ax = fig.add_subplot(111) 
ax.grid(True) 
ax.plot(x,y) 
ax.plot(x2, y2, 'ro') 
ax.add_patch(poly) 
ax.set_ylim(0,0.41) 
ax.set_xlabel('x') 
ax.set_ylabel('y') 
ax.text(0.7,0.36,r"$ \int_{-2\sigma}^{2\sigma}p(x)dx $") plt.show(fig)
```

[![matplotlib 绘图示例](/images/python-sci-computing/image.png)](/images/python-sci-computing/image.png)

笔者觉得matplotlib画的图比MATLAB更加平滑，而且有更完善的格式支持、数学字体支持。Wikipedia上的[这个页面](http://commons.wikimedia.org/wiki/Category:Made_with_Matplotlib)展示了一些matplotlib绘制的插图。




### 用NetworkX做网络分析


网络，数学上就是“图”，是经常会遇到的一种描述数据的方式，常见的有无向图、有向图、二部分图等等。操作和分析网络长久以来依赖于大家自己动手，用C/C++等自己写数据结构和算法，技术高的童鞋则可能会用Boost这样的库，但Boost比较难学而且功能有限。

[NetworkX](http://networkx.lanl.gov/)是专门用于处理、分析网络的Python库，最初的开发者是大名鼎鼎的 Los Alamos 实验室的两名研究员。NetworkX功能很全面，而且易学易用，直接以点、边、图为对象进行操作，免去了C/C++编程里面莫名奇妙的很多错误。NetworkX给用户的自由度也很大，点可以是任意的对象，点和边可以附带自定义的数据。举个例子，可以创建一个有向网络，节点是电子邮件地址（字符串），一个点到另一个点的边表示前者给后者发过信件，边的权重表示发送信件的数目。写成代码就像这样：

``` python
import networkx as nx
# construct a directed graph
G = nx.DiGraph()
# add email addresses as nodes
for emailAddress in emailAddressList:
G.add_node(emailAddress)
# add edges
for emailAdd1, emailAdd2 in emailCommunications:
	G.add_edge(emailAdd1, emailAdd2, weight = mailNumber(emailAdd1, emailAdd2))	
```


NetworkX还有以下特点：



	
  1. 实现了关于网络的很多算法，诸如Erdos-Renyi随机图生成、最短路、PageRank等。

	
  2. 结合GraphViz、matplotlib，对网络进行可视化。需要两步走：先产生一个图的布局（Layout），然后做图。官网的[Gallery](http://networkx.lanl.gov/gallery.html)里面有漂亮的例子，笔者从中得到不少启示。

	
  3. 多种格式的输入、输出。由于网络并没有统一的数据格式，NetworkX对主流的一些格式（如Edge list、GEXF、GraphML）都有支持。用NetworkX转换格式很方便。





### Caveat


用Python做科学计算不可避免地有一些缺陷。最大的缺陷就是速度虽然不慢，但也不够快，尤其计算量巨大时相比与C/C++、Fortran的差距明显。这里就有一个trade-off：怎样平衡开发和计算的时间，比如使用Python会不会在开发上节约了一天，却在计算上多花了三天？当然，合理地用一些 trick 可以大大提高Python做计算的效率，比如向量化、合理的数据结构、用C/C++实现一些性能要求高的模块。

NetworkX也有这样的问题。我的感觉是，如果网络的规模很大（节点>50万，边>100万），NetworkX在空间和速度上就很吃力了。规模小一些的网络用NetworkX是没有问题的。




by Richard Kwo,




thanks, @chaolu for hosting this post




Oct 7, 2012



