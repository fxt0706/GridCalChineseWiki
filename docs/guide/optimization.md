# 优化

对 GridCal 的代码进行优化，可以有以下几个切入点：

1. 优化代码的结构，减少无谓的跳转和参数传递。
2. 优化代码中有关 python 数学库的使用方式。
3. 优化代码中有关数据存储的部分。
4. 在数学算法的代码内容上寻求更高效准确的算法。  

以下内容将根据四个所提到的点，来举例一些浅显的例子。

## 优化代码结构

根据本文档`算法开发` 中的内容，我们可以看到在进行潮流计算时，从按下计算按钮到输出结果，代码中进行了约 7 次的跳转，这其中包括了多次的对象创建，参数传递和逻辑判断，在性能上这可能没有造成很大的开销，但在开发者进行维护和二次开发时，这样的流程会显得庞大繁琐。在了解整个软件的代码架构的前提下，开发者可以尝试重新提炼 GridCal 的代码结构，并将可合并的概念尝试集成到一个类中。或者，开发者可以尝试将 Solver 的接口更加明显的暴露，形成一个规范接口，让二次开发更加简易直接。

熟练开发者通过这样的代码重构，可以使得 GridCal 项目更容易维护和开发。

## Python 数学库使用优化

GridCal 中使用了 `SciPy`，`NumPy` 两个数学库。`SciPy` 的功能囊括了线性代数、积分、傅里叶变换、常微分方程、信号处理等，`NumPy` 主要用于高阶矩阵的处理和运算，以及一些阵列计算。以下就 `NumPy` 的使用提出一些优化的思路。

`NumPy` 本身提供了一种在向量计算上经过优化的数组 `ndarry`，相比原生地使用 Python，`NumPy` 本身已具有了更好的性能，所以在 `NumPy` 的使用上，更多的是需要开发者避免一些重复和产生额外开销的操作，以此来优化性能。

例如，对数组进行操作时，单纯的重塑一个数组，和经过转置后重塑数组，这两种操作是不一样的。前者并不会出发变量的拷贝，而后者的操作中，则实际经历了数组在内存中的拷贝后再次处理的过程。

```python
import numpy as np
a = np.zeros((10,10))
b = a.resharpe((1,-1))
# c 变量的获得需要更长的操作时间
c = a.T.resharpe((1,-1))
```

再例如，对数组进行就地操作和产生新的变量后操作(隐式拷贝)也有不同的内存开销，如果数组原始的数据并不需要再次利用，那么就地操作可以提高一些的性能。

```python
a *= 2
# 隐式拷贝可能会比就地操作多话费两倍的时间
c = a * 2
```

内存无意义的开销往往会连带造成运算时间的延长，并且当代码中包含多次循环时，这样的问题会更加突出。开发者若能从头到尾对 `NumPy` 以及 `SciPy`的使用进行一次优化，那么 GridCal 的运算性能必然会上升。

## 数据存储优化

GridCal 读取和保存项目数据的原理很朴素，可以简单理解为对 Excel 表格文件数据的读取和保存。GridCal 中使用的是 `panda` 库来进行 excel 的操作读写，`panda` 库擅长的即为数据处理，但是除此之外，依然有很多优秀的处理 excel 的第三方库，例如 `openpyxl`、`xlutils`、`xlwings` 等等。例如，`openyxl` 的功能更加全面广泛，但是读写速率低，`pandas` 读写速率比 `openyxl` 稍微优秀一些，但是它的性能远远的慢于 `xlwings`。作者在此前也选择使用 `openyxl` 库来读写 Excel 文件，但此后采用了 `panda`，是由于其不仅仅能操作 Excel 文件，还能一并满足 GridCal 中其它的数据处理需求。

开发者可以考虑引入 `xlwings` 库来进行 Excel 表格的处理。`xlwings` 与 `panda` 相比，前者的写入速度约为后者 4 倍，读取速度约为后者的 8 倍。并且 `xlwings` 还可以友好配合 `panda` 的作业，并不会导致整个业务需要重写。`xlwings` 的缺点在于，其安装使用时需要一些额外的操作，来满足其生产环境的搭建。

## 数学算法优化

针对电力网络计算的数学算法进行优化应当是众多电力行业研究者的主要优化方向。实际上，GridCal 本身的性能和易用程度已经达到了令人满意的程度，而通过二次开发和算法优化，开发者可以引入自己的新算法，来从根本上提高计算的效率和准确度。

例如，在潮流计算中，可以优化大规模稀疏线性方程组的求解过程;对节点的编号进行处理;考虑计算时是并行处理，等等。软件框架的优化很大程度上取决于开发者的程序编写水平，而算法的优化取决于开发者的电力方向的学术水平。用更好的算法得到更好的计算结果，这也应当是 GridCal 开发者将项目开源的初衷。