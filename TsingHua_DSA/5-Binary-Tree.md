[TOC]

# 第5章：二叉树

在此前介绍的结构中，元素之间都存在一个自然的线性次序，故它们都属于所谓的线性结构(linear structure)。树则不然，其中的元素之间并不存在天然的直接后继或直接前驱的关系。不过，正如我们马上就要看到的，只要附加某种约束（比如遍历），也可以在树中的元素之间确定某种线性次序，因此树属于半线性结构(semi-linear structure)。

无论逻辑结构或算法功能而言，任何有根有序的多叉树，都可等价地转化并实现为二叉树。



## 5.1 二叉树及其表示

### 5.1.1 树

* 有根树

  从图论的角度看，树等价于连通无环图。因此与一般的图相同，树也由一组顶点(vertex)以及联接与其间的若干条边(edge)组成。在计算机科学中往往还会再指定某一特定顶点，并称之为根(root)。在指定根节点之后，我们也称之为有根树(rooted tree)。此时，从程序实现的角度，我们更多地将顶点称作节点(node)。

* 深度与层次

  每一节点与根之间都有一条路径相联；而根据树的无环性，由根通往每个节点的路径必然唯一。因此如图5.1所示，沿每个节点v到根r的唯一通路所经过边的数目，称作v的深度(depth)，记作depth(v)。根据深度排序，可对所有节点做分层归类。特别地，约定根节点的深度depth(r) = 0，故属于第0层。

* 祖先、后代与子树

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0500.jpg?raw=true)

* 高度

  树T中所有节点深度的最大值称作该树的高度(height)，记作height(T)。

  树的高度总是由其中某一叶节点的深度确定的。特别地，本书约定，仅含单个节点的树高度为0，空树高度为-1。

  推而广之，任一节点v所对应子树subtree(v)的高度亦称作该节点的高度，记作height(v)。特别地，全树的高度亦即其根节点r的高度，height(T) = height(r)。



### 5.1.2 二叉树

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0501.jpg?raw=true)



### 5.1.3 多叉树

树中各节点的孩子数目并不确定。每个节点的孩子均不超过k个的有根树，称作k叉树(k-ary tree)。

表示法：

root() 根节点

parent() 父节点

firstChild() 长子

nextSibling() 兄弟

insert(i, e) 将e作为第i个孩子插入

remove(i) 删除第i个孩子（及其后代）

traverse() 遍历

* 父节点

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0502.jpg?raw=true)

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0503.jpg?raw=true)

