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

* 子节点

  如图5.4所示，对于拥有r个孩子的节点，可在O(r+1)时间内列举出其所有的孩子。

* 父节点+子节点

  结合两种表示法的优势，但在节点插入与删除频繁的场合，为了动态地维护和更新树地拓扑结构，不得不反复地遍历和调整一些节点所对应的子序列，势必会影响到整体效率。

* 有序多叉树 = 二叉树

  解决上述问题的方法之一，就是采用支持高效动态调整的二叉树结构，建立从多叉树到二叉树的转换关系。当然，为了保证作为多叉树特例的二叉树有足够的能力表示任何一棵多叉树，我们只需给多叉树增加一项约束条件——同一节点的所有孩子之间必须具有某一线性次序。凡符合这一条件的多叉树也称作有序树(ordered tree)。以互联网域名系统对应的多叉树为例，其中同一域名下的分支通常即按照字典序排列。

* 长子 + 兄弟

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0504.jpg?raw=true)

  

## 5.2 编码树

### 5.2.1 二进制编码

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0505.jpg?raw=true)

* 生成编码表

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0506.jpg?raw=true)

* 二进制编码与解码

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0507.jpg?raw=true)

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0508.jpg?raw=true)

* 解码歧义

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0509.jpg?raw=true)

* 前缀无歧义编码

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0510.jpg?raw=true)



## 5.3 二叉树的实现

作为图的特殊形式，二叉树的基本组成单元是节点与边；作为数据结构，其基本的组成实体是二叉树节点（binary tree node），而边则对应于节点之间的相互引用。

### 5.3.1 二叉树节点

* BinNode模板类

```c++
#define BinNodePosi(T) BinNode<T>* //节点位置
#define stature(p) ((p) ? (p)->height : -1) //节点高度（与“空树高度为-1”的约定相统一）
typedef enum { RB_RED, RB_BLACK} RBColor; //节点颜色
template <typename T> struct BinNode { //二叉树节点模板类
// 成员（为简化描述起见统一开放）
    T data; //数值
    BinNodePosi(T) parent; BinNodePosi(T) lc; BinNodePosi(T) rc; //父节点及左、右孩子
    int height; //高度（通用）
    int npl; //Null Path Length（左式堆，也可直接用height代替）
    RBColor color; //颜色（红黑树）
// 构造函数
    BinNode() :
    	parent ( NULL ), lc ( NULL ), rc ( NULL ), height ( 0 ), npl ( 1 ), color ( RB_RED ) { }
    BinNode ( T e, BinNodePosi(T) p = NULL, BinNodePosi(T) lc = NULL, BinNodePosi(T) rc = NULL,
            int h = 0, int l = 1, RBColor c = RB_RED ) :
    	data ( e ), parent ( p ), lc ( lc ), rc ( rc ), height ( h ), npl ( l ), color ( c ) { }
// 操作接口
    int size(); //统计当前节点后代总数，亦即以其为根的子树的规模
    BinNodePosi(T) insertAsLC ( T const& ); //作为当前节点的左孩子插入新节点
    BinNodePosi(T) insertAsRC ( T const& ); //作为当前节点的右孩子插入新节点
    BinNodePosi(T) succ(); //取当前节点的直接后继
    template <typename VST> void travLevel ( VST& ); //子树层次遍历
    template <typename VST> void travPre ( VST& ); //子树先序遍历
    template <typename VST> void travIn ( VST& ); //子树中序遍历
    template <typename VST> void travPost ( VST& ); //子树后序遍历
// 比较器、判等器（各列其一，其余自行补充）
    bool operator< ( BinNode const& bn ) { return data < bn.data; } //小于
    bool operator== ( BinNode const& bn ) { return data == bn.data; } //等于
};
```

这里，通过宏BinNodePosi来指代节点位置，以简化后续代码的描述；通过定义宏stature，则可以保证从节点返回的高度值，能够与“空树高度为-1”的约定相统一。

* 成员变量

  