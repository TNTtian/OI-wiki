## 二维凸包

### 凸多边形

凸多边形是指所有内角大小都在 $[0,\pi]$ 范围内的 **简单多边形** 。

### 凸包

在平面上能包含所有给定点的最小凸多边形叫做凸包。

其定义为：对于给定集合 $X$ ，所有包含 $X$ 的凸集的交集 $S$ 被称为 $X$ 的 **凸包** 。

实际上可以理解为用一个橡皮筋包含住所有给定点的形态。

凸包用最小的周长围住了给定的所有点。如果一个凹多边形围住了所有的点，它的周长一定不是最小，如下图。根据三角不等式，凸多边形在周长上一定是最优的。

![](./images/ch.png)

### 凸包的求法

常用的求法有 Graham 扫描法和 Andrew 算法。

####  Graham 扫描法求凸包

Graham 算法的本质：

Graham 扫描算法维护一个凸壳，通过不断在凸壳中加入新的点和去除影响凸性的点，最后形成凸包。

凸壳：凸包的一部分。

此算法主要分为两部分：

- 排序
- 扫描

##### 排序

我们先选择一个 $y$ 最小的点（如 $y$ 相同选 $x$ 最小），记为 $p_1$。

剩下的点，按照极角的大小逆时针排序，记为 $p_1,p_2,\dots, p_m$。

![3.1.3.3.1-1](https://i.loli.net/2020/06/30/Xi7cFNM1P9GgbOn.png)

我们按照排序结束时的顺序枚举每一个点，依次连线，这里可以使用一个栈来存储，每次入栈，如果即将入栈的元素与栈顶两个元素所构成了一个类似于凹壳的东西，那么显然处于顶点的那个点一定不在这个点集的凸包上，而他正好在栈顶，所以把它弹出栈，新点入栈。

##### 扫描

（注：下列所说的左右等是指以上一条连线为铅垂线，新的连线偏移的方向）

刚开始，我们的点集是这样的：

![3.1.3.3.1-2](https://i.loli.net/2020/06/30/TbHLPcV94xvrKtO.png)

$p_1$ 为起始点。

随后，$p_2$ 准备入栈，由于栈元素很少，所以可以入栈。

![3.1.3.3.1-3](https://i.loli.net/2020/07/01/hpnwUIfumRPDcj8.png)

再看 $p_3$，因为 $p_3$ 向左，符合凸包条件，入栈。

![3.1.3.3.1-4](https://i.loli.net/2020/07/01/CPjer9L8yaI7KVl.png)

随后 $p_4$ 也一切正常，依然向左，入栈。

![3.1.3.3.1-5](https://i.loli.net/2020/07/01/u2rv43QySIpP5tl.png)

$p_5$ 依然向左，入栈。

![3.1.3.3.1-6](https://i.loli.net/2020/07/01/tiaQTMcZ1AYKeBj.png)

到 $p_6$ 时，我们发现了点问题，就是不再是向左了，而是向右了，所以我们此时要将 $p_5$ 出栈，$p_6$ 入栈。
 
![3.1.3.3.1-7](https://i.loli.net/2020/07/01/VpAYBD6cyjNFtlv.png)

入栈后，我们发现，相对于 $p_4$，$p_6$ 依然是向右的，所以我们还要把 $p_4$ 出栈，$p_6$ 入栈。

![8](https://i.loli.net/2020/07/01/Hk2a81zh9M3lgZu.png)

接下来 $p_7$ 没有问题。

![9](https://i.loli.net/2020/07/01/VnQT83rJ1NYfMxB.png)

$p_8$ 时，我们发现，也是向右的，所以将 $p_7$ 出栈，$p_8$ 入栈。

![10](https://i.loli.net/2020/07/01/QlEoV249qyeuGNi.png)

接下来 $p_9$ 正常，入栈。

![11](https://i.loli.net/2020/07/01/lRch9fBSgmKanIY.png)

最后，我们再把最后一个点和第一个点连起来。

![12](https://i.loli.net/2020/07/01/1YmXwVn3bKZESPf.png)

此时，我们的 Graham 算法的全过程就结束了。

扫描的时间复杂度：$O(n)$。

但是不可能那么优秀，还要加上排序的时间复杂度：$O(n\log n)$。

所以总时间复杂度为 $O(n \log n)$。

#### Andrew 算法求凸包

首先把所有点以横坐标为第一关键字，纵坐标为第二关键字排序。

显然排序后最小的元素和最大的元素一定在凸包上。而且因为是凸多边形，我们如果从一个点出发逆时针走，轨迹总是“左拐”的，一旦出现右拐，就说明这一段不在凸包上。因此我们可以用一个单调栈来维护上下凸壳。

因为从左向右看，上下凸壳所旋转的方向不同，为了让单调栈起作用，我们首先 **升序枚举** 求出下凸壳，然后 **降序** 求出上凸壳。

求凸壳时，一旦发现即将进栈的点（ $P$ ）和栈顶的两个点（ $S_1,S_2$ ，其中 $S_1$ 为栈顶）行进的方向向右旋转，即叉积小于 $0$ ： $\overrightarrow{S_2S_1}\times \overrightarrow{S_1P}<0$ ，则弹出栈顶，回到上一步，继续检测，直到 $\overrightarrow{S_2S_1}\times \overrightarrow{S_1P}\ge 0$ 或者栈内仅剩一个元素为止。

通常情况下不需要保留位于凸包边上的点，因此上面一段中 $\overrightarrow{S_2S_1}\times \overrightarrow{S_1P}<0$ 这个条件中的“ $<$ ”可以视情况改为 $\le$ ，同时后面一个条件应改为 $>$ 。

???+note "代码实现"
    ```cpp
    // stk[]是整型，存的是下标
    // p[]存储向量或点
    tp = 0;                       // 初始化栈
    std::sort(p + 1, p + 1 + n);  // 对点进行排序
    stk[++tp] = 1;
    //栈内添加第一个元素，且不更新used，使得1在最后封闭凸包时也对单调栈更新
    for (int i = 2; i <= n; ++i) {
      while (tp >= 2  // 下一行*被重载为叉积
             && (p[stk[tp]] - p[stk[tp - 1]]) * (p[i] - p[stk[tp]]) <= 0)
        used[stk[tp--]] = 0;
      used[i] = 1;  // used表示在凸壳上
      stk[++tp] = i;
    }
    int tmp = tp;  // tmp表示下凸壳大小
    for (int i = n - 1; i > 0; --i)
      if (!used[i]) {
        //      ↓求上凸壳时不影响下凸壳
        while (tp > tmp && (p[stk[tp]] - p[stk[tp - 1]]) * (p[i] - p[stk[tp]]) <= 0)
          used[stk[tp--]] = 0;
        used[i] = 1;
        stk[++tp] = i;
      }
    for (int i = 1; i <= tp; ++i)  // 复制到新数组中去
      h[i] = p[stk[i]];
    int ans = tp - 1;
    ```

根据上面的代码，最后凸包上有 $ans$ 个元素（额外存储了 $1$ 号点，因此 $h$ 数组中有 $ans+1$ 个元素），并且按逆时针方向排序。周长就是

$$
\sum_{i=1}^{ans}\left|\overrightarrow{h_ih_{i+1}}\right|
$$

### 例题

 [UVA11626 Convex Hull](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=78&page=show_problem&problem=2673) 

 [「USACO5.1」圈奶牛 Fencing the Cows](https://www.luogu.com.cn/problem/P2742) 

 [POJ1873 The Fortified Forest](http://poj.org/problem?id=1873) 

 [POJ1113 Wall](http://poj.org/problem?id=1113) 

 [「SHOI2012」信用卡凸包](https://www.luogu.com.cn/problem/P3829) 
