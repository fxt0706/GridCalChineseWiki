# 算法类

GridCal 内自带了多种的算法，以下通过具体分析算法文件的结构，来表达如何在 GridCal 中修改和加入算法。

## GridCal 集成的潮流算法

在运行 GridCal 后，我们可以在上方标签栏的 `Settings` 标签下的 `Power flow` 标签块下的 `Solver` 标签看到所有 GridCal 集成的适用于解决潮流计算的算法。

![ShowPowerFlowSolver](pics/ShowPowerFlowSolver.png)

此处，我们选择传统的 Newton-Raphson 法来分析其代码，以了解如何在 GridCal 接入一个抽象的算法。

## 牛顿拉夫逊法（Newton-Raphson）

Newton-Raphson 法集成在了 `JacobianBased.py` 文件中的 `IwamotoNR` 方法中。`IwamotoNR` 方法为使用了 Iwamoto 最佳步长因子的纯牛顿拉夫逊潮流算法，使用其方法需要提供的参数如下：

| 参数 | 含义 |
|-----|-----| 
|Ybus|导纳矩阵|
|Sbus|节点注功率入阵列|
|V0|节点电压阵列（初始解）|
|Ibus|节点注入电流阵列|
|PV|具有PV总线索引的阵列|
|pq|包含PQ总线索引的阵列|
|tol|公差|
|max_it|最大迭代次数|
|robust|使用Iwamoto最佳步长因子的布尔变量| 

运算结束后将返回如下结果：

| 参数 | 含义 |
|-----|-----|
|V|电压计算结果阵列|
|converged|是否收敛，为 1 时收敛|
|normF|计算结果的误差，小于 tol 时判断为收敛|
|Scalc||
|iter_||
|elapsed||

## 连续潮流法（Continuation Power Flow）

连续潮流法是用于电网稳定性分析的一个重要算法。