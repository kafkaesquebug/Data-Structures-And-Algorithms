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

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0511.jpg?raw=true)

* 快捷方式

在BinNode模板类各接口以及后续相关算法的实现中，将频繁检查和判断二叉树节点的状态与性质，有时还需要定位与之相关的（兄弟、叔叔等）特定节点，为简化算法描述同时增强可读性，将其中常用功能以宏的形式加以整理归纳：

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0512.jpg?raw=true)

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0513.jpg?raw=true)



### 5.3.2 二叉树节点操作接口

由于BinNode模板类本身处于底层，故这里也将所有操作接口统一设置为开放权限，以简化描述。

* 插入孩子节点

```c++
template <typename T> BinNodePosi(T) BinNode<T>::insertAsLC ( T const& e )
{ return lc = new BinNode ( e, this ); } //将e作为当前节点的左孩子插入二叉树

template <typename T> BinNodePosi(T) BinNode<T>::insertAsRC ( T const& e )
{ return rc = new BinNode ( e, this ); } //将e作为当前节点的右孩子插入二叉树
```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0514.jpg?raw=true)

* 遍历

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0515.jpg?raw=true)



### 5.3.3 二叉树

* BinTree模板类

```c++
#include "BinNode.h" //引入二叉树节点类
template <typename T> class BinTree { //二叉树模板类
protected:
    int _size; BinNodePosi(T) _root; //规模、根节点
    virtual int updateHeight ( BinNodePosi(T) x ); //更新节点x的高度
    void updateHeightAbove ( BinNodePosi(T) x ); //更新节点x及其祖先的高度
public:
    BinTree() : _size ( 0 ), _root ( NULL ) { } //构造函数
    ~BinTree() { if ( 0 < _size ) remove ( _root ); } //析构函数
    int size() const { return _size; } //规模
    bool empty() const { return !_root; } //判空
    BinNodePosi(T) root() const { return _root; } //树根
    BinNodePosi(T) insertAsRoot ( T const& e ); //插入树根节点
    BinNodePosi(T) insertAsLC ( BinNodePosi(T) x, T const& e ); //e作为x的左孩子（原无）插入
    BinNodePosi(T) insertAsRC ( BinNodePosi(T) x, T const& e ); //e作为x的右孩子（原无）插入
    BinNodePosi(T) attachAsLC ( BinNodePosi(T) x, BinTree<T>* &T ); //T作为x左子树接入
    BinNodePosi(T) attachAsRC ( BinNodePosi(T) x, BinTree<T>* &T ); //T作为x右子树接入
    int remove ( BinNodePosi(T) x ); //删除以位置x处节点为根的子树，返回该子树原先的规模
    BinTree<T>* secede ( BinNodePosi(T) x ); //将子树x从当前树中摘除，并将其转换为一棵独立子树
    template <typename VST> //操作器
    void travLevel ( VST& visit ) { if ( _root ) _root->travLevel( visit ); } //层次遍历
    template <typename VST> //操作器
    void travPre ( VST& visit ) { if ( _root ) _root->travPre ( visit ); } //先序遍历
    template <typename VST> //操作器
    void travIn ( VST& visit ) { if ( _root ) _root->travIn ( visit ); } //中序遍历
    template <typename VST> //操作器
    void travPost ( VST& visit ) { if ( _root ) _root->travPost ( visit ); } //后序遍历
    bool operator< ( BinTree<T> const& t ) //比较器（其余自行补充）
    { return _root && t._root && lt ( _root, t._root ); }
    bool operator== ( BinTree<T> const& t ) //判等器
    { return _root && t._root && ( _root == t._root ); }
}; //BinTree
```

* 高度更新

  二叉树一节点的高度，都等于其孩子节点的最大高度加一。于是，每当某一节点的孩子或后代有所增减，其高度都右必要及时更新。然而实际上，节点自身很难发现后代的变化，因此这里不妨反过来采用另一处理策略：一旦有节点加入或离开二叉树，则更新其所有祖先的高度。

  在每一节点v处，只需读出其左、右孩子的高度并取二者之间的大者，再计入当前节点本身，就得到了v的新高度。通常，接下来还需要从v出发沿parent指针逆行向上，依次更新各代祖先的高度记录。

```c++
template <typename T> int BinTree<T>::updateHeight ( BinNodePosi(T) x ) //更新节点x高度
{ return x->height = 1 + max ( stature ( x->lc ), stature ( x->rc ) ); } //具体规则因树而异

template <typename T> void BinTree<T>::updateHeightAbove ( BinNodePosi(T) x ) //更新高度
{ while ( x ) { updateHeight ( x ); x = x->parent; } } //从x出发，覆盖历代祖先。可优化
```

更新每一节点本身的高度，只需执行两次getHeight()操作、两次加法以及两次取最大操作，不过常数时间，故updateHeight()算法总体运行时间也是O(depth(v) + 1)，其中depth(v)为节点v的深度。

* 节点插入

```c++
template <typename T> BinNodePosi(T) BinTree<T>::insertAsRoot ( T const& e )
{ _size = 1; return _root = new BinNode<T> ( e ); } //将e当作根节点插入空的二叉树

template <typename T> BinNodePosi(T) BinTree<T>::insertAsRC ( BinNodePosi(T) x, T const& e )
{ _size++; x->insertAsRC ( e ); updateHeightAbove ( x ); return x->rc; } //e插入为x的右孩子
//insertAsLC()完全对称，在此省略
```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0516.jpg?raw=true)

* 子树接入

```c++ 
template <typename T> //二叉树子树接入算法：将S当作节点x的右子树接入，S本身置空
BinNodePosi(T) BinTree<T>::attachAsRC ( BinNodePosi(T) x, BinTree<T>* &S ) {
    if ( x->rc = S->_root ) x->rc->parent = x;
    _size += S->_size; updateHeightAbove ( x );
    S->_root = NULL
}
```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0517.jpg?raw=true)

* 子树删除

```c++
template <typename T> //删除二叉树中位置x处的节点及其后代，返回被删除节点的数值
int BinTree<T>::remove ( BinNodePosi(T) x ) { //assert: x为二叉树中的合法位置
    FromParentTo ( *x ) = NULL; //切断来自父节点的指针
    updateHeightAbove ( x->parent ); //更新祖先高度
    int n = removeAt ( x ); _size -= n; return n; //删除子树x，更新规模，返回删除节点总数
}

template <typename T> //删除二叉树中位置x处的节点及其后代，返回被删除节点的数值
static int removeAt ( BinNodePosi(T) x ) { //assert: x为二叉树中的合法位置
    if ( !x ) return 0; //递归基：空树
    int n = 1 + removeAt ( x->lc ) + removeAt ( x->rc ); //递归释放左、右子树
    release ( x->data ); release ( x ); return n; //释放被摘除节点，并返回删除节点总数  
} //release()负责释放复杂结构，与算法无直接关系，详见代码包
```

* 子树分离

```c++
template <typename T> //二叉树子树分离算法：将子树x从当前树中摘除，将其封装为一棵独立子树返回
BinTree<T>* BinTree<T>::secede ( BinNodePosi(T) x ) { //assert: x为二叉树中的合法位置
    FromParentTo ( *x ) = NULL;//切断来自父节点的指针
    updateHeightAbove ( x->parent ); //更新原树中所有祖先的高度
    BinTree<T>* S = new BinTree<T>; S->_root = x; x->parent = NULL; //新树以x为根
    S->_size = x->size(); _size -= S->_size; return S; //更新规模，返回分离出来的子树
}
```

* 复杂度

就二叉树拓扑结构的变化范围而言，以上算法均只涉及局部的常数个节点。因此，除了更新祖先高度和释放节点等操作，只需常数时间。



## 5.4 遍历

 对二叉树的访问多可抽象为如下形式：按照某种约定的次序，对节点各访问一次且仅依次。与向量和列表等线性结构一样，二叉树这类访问也称作遍历（traversal）。

### 5.4.1 递归式遍历

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0518.jpg?raw=true)

* 先序遍历

```c++
template <typename T, typename VST> //元素类型、操作器
void travPre_R ( BinNodePosi(T) x, VST& visit ) { //二叉树先序遍历算法（递归版）
    if ( !x ) return;
    visit ( x->data );
    travPre_R ( x->lc, visit );
    travPre_R ( x->rc, visit );
}
```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0519.jpg?raw=true)

* 后序遍历

```c++
template <typename T, typename VST> //元素类型、操作器
void travPost_R ( BinNodePosi(T) x, VST& visit ) { //二叉树后序遍历算法（递归版）
    if ( !x ) return;
    travPost_R ( x->lc, visit );
    travPost_R ( x->rc, visit );
    visit ( x->data );
}
```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0520.jpg?raw=true)

* 中序遍历

```c++
template <typename T, typename VST> //元素类型、操作器
void travIn_R ( BinNodePosi(T) x, VST& visit ) { //二叉树中序遍历算法（递归版）
    if ( !x ) return;
    travIn_R ( x->lc, visit );
    visit ( x->data );
    travIn_R ( x->rc, visit );
}
```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0521.jpg?raw=true)



### 5.4.2 *迭代版先序遍历

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0522.jpg?raw=true)

先序遍历可分解为两段：沿最左侧通路自顶而下访问的各节点，以及自底而上遍历的对应右子树。

```c++
//从当前节点出发，沿左分支不断深入，直至没有左分支的节点；沿途节点遇到后立即访问
template <typename T, typename VST> //元素类型、操作器
static void visitAlongLeftBranch ( BinNodePosi(T) x, VST& visit, Stack<BinNodePosi(Ta)>& S ) {
    while ( x ) {
        visit ( x->data ); //访问当前节点
        S.push ( x->rc ); //右孩子入栈暂存（可优化：通过判断，避免空的右孩子入栈）
        x = x->lc; //沿左分支深入一层
    }
}

template <typename T, typename VST> //元素类型、操作器
void travPre_I2 ( BinNodePosi(T) x, VST& visit { //二叉树先序遍历算法（迭代版#2）
	Stack<BinNodePosi(T)> S; //辅助栈
    while ( true ) {
        visitAlongLeftBranch ( x, visit, S ); //从当前节点出发，逐批访问
        if ( S.empty() ) break; //直到栈空
        x = S.pop(); //弹出下一批的起点
    }
}
```

在全树以及其中每一棵子树的根节点处，该算法都首先调用函数VisitAlongLeftBranch()，自顶而下访问最左侧通路沿途的各个节点。这里也使用了一个辅助栈，逆序记录最左侧通路上的节点，以便确定其对应右子树自底而上的遍历次序。



### 5.4.3 *迭代版本中序遍历

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0523.jpg?raw=true)

```c++
template <typename T> //从当前节点出发，沿左分支不断深入，直至没有左分支的节点
static void goAlongLeftBranch ( BinNodePosi(T) x, Stack<BinNodePosi(T)>& S ) {
    while ( x ) { S.push ( x ); x = x->lc; } //当前节点入栈后随即向左侧分支深入，迭代直到无左孩子
}

template <typename T, typename VST> //元素类型、操作器
void travIn_I1 ( BinNodePosi(T) x, VST& visit ) { //二叉树中序遍历算法（迭代版#1）
    Stack<BinNodePosi(T)> S; //辅助线
    while ( true ) {
        goAlongLeftBranch ( x, S ); //从当前节点出发，逐批入栈
        if ( S.empty() ) break; //直至所有节点处理完毕
        x = S.pop(); visit ( x->data ); //弹出栈顶节点并访问之
        x = x->rc; //转向右子树
    }
}
```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0524.jpg?raw=true)

* 直接后继及其定位

中序遍历的实质功能也可理解为，为所有节点赋予一个次序，从而将半线性的二叉树转为线性结构。于是一旦指定了遍历策略，即可与向量和列表一样，在二叉树的节点之间定义前驱与后继关系。其中没有前驱（后继）的节点称作首（末）节点。

对于后面将要介绍的二叉搜索树，中序遍历的作用至关重要。相关算法必需的一项基本操作，就是定位任一节点在中序遍历序列中的直接后继。为此，可实现succ()接口：

```c++
template <typename T> BinNodePosi(T) BinNode<T>::succ() { //定位节点v的直接后继
    BinNodePosi(T) s = this; //记录后继的临时变量
    if ( rc ) { //若有右孩子，则直接后继必在右子树中，具体地就是
        s = rc; //右子树中
        while ( HasLChild ( *S ) ) s = s->lc; //最靠左（最小）的节点
    } else { //否则，直接后继应是“将当前节点包含于其左子树中的最低祖先”，具体地就是
        while ( IsRChild ( *S ) ) s = s->parent; //逆向地沿右向分支，不断朝左上方移动
        s = s->parent; //最后再朝右上方移动一步，即抵达直接后继（如果存在）
    }
    return s;
}
```

* 版本2

```c++
template <typename T, typename VST> //元素类型、操作器
void travIn_I2 ( BinNodePosi(T) x, VST& visit ) { //二叉树中序遍历算法（迭代版#2）
    Stack<BinNodePosi(T)> S; //辅助线
    while ( true )
        if ( x ) {
            S.push ( x ); //根节点进栈
            x = x->lc; //深入遍历左子树
        } else if ( !S.empty() ) {
            x = S.pop(); //尚未访问的最低祖先节点退栈
            visit ( x->data ); //访问该祖先节点
            x = x->rc; //遍历祖先的右子树
        } else
            break; //遍历完成
}
```

虽然版本2只不过是版本1的等价形式，但借助它可便捷地设计和实现以下版本3。

* 版本3

以上迭代式遍历算法都需要使用辅助栈，尽管这对遍历算法的渐进时间复杂度没有实质影响，但所需辅助空间的规模将线性正比于二叉树的高度，在最坏情况下与节点总数相当。

为此可继续改进，借助BinNode对象内部的parent指针，该版本无需使用任何结构，总体仅需O(1)的辅助空间，属于就地算法。当然，因需要反复调用succ()，时间效率有所倒退。

```c++
template <typename T, typename VST> //元素类型、操作器
void travIn_I3 ( BinNodePosi(T) x, VST& visit ) { //二叉树中序遍历算法（迭代版#2，无需辅助栈）
    bool backtrack = false; //前一步是否刚从右子树回溯——省去栈，仅O(1)辅助空间
    while ( true )
        if ( !backtrack && HasLChild ( *x ) ) //若有左子树且不是刚刚回溯，则
            x = x->lc; //深入遍历左子树
    	else { //否则——无左子树或刚刚回溯（相当于无左子树）
            visit ( x->data ); //访问该节点
            if ( HasRChild ( *x ) ) { //若其右子树非空，则
                x = x->rc; //深入右子树继续遍历
                backtrack = false; //并关闭回溯标志
            } else { //若右子树空，则
                if ( ! ( x = x->succ() ) ) break; //回溯（含抵达末节点时的退出返回）
                backtrack = true; //并设置回溯标志
            }
        }
}
```

可见，这里相当于将原辅助栈替换为一个标志位backtrack。每当抵达一个节点，借助该标志即可判断此前是否刚做过一次自下而上的回溯。若不是，则按照中序遍历的策略优先遍历左子树。反之，若刚发生过依次回溯，则意味着当前节点的左子树已经遍历完毕（或等效地，左子树为空），于是便可访问当前节点，然后再深入其右子树继续遍历。

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0525.jpg?raw=true)

每个节点被访问之后，都应转向其在遍历序列中的直接后继。按照以上的分析，通过检查右子树是否为空，即可在两种情况间做出判断：该后继要么在当前节点的右子树（若该子树非空）中，要么（当右子树为空时）是其某一祖先。如图5.21，后一情况即所谓的回溯。

请注意，由succ()返回的直接后继可能是NULL，此时意味着已经遍历至中序遍历意义下的末节点，于是遍历即告完成。



### 5.4.4 *迭代版后序遍历

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0526.jpg?raw=true)

```c++
template <typename T> //在以S栈顶节点为根的子树中，找到最高左侧可见叶节点
static void gotoHLVFL ( Stack<BinNodePosi(T)>& S ) { //沿途所遇节点依次入栈
    while ( BinNodePosi(T) x = S.top() ) //自顶而下，反复检查当前节点（即栈顶）
        if ( HasLChild ( *x ) ) { //尽可能向左
            if ( HasRChild ( *x ) ) S.push ( x->rc ); //若有右孩子，优先入栈
            S.push ( x->lc ); //然后才转至左孩子
        } else //实不得已
            S.push ( x->rc ); //才向右
    S.pop(); //返回之前，弹出栈顶的空节点
}

template <typename T, typename VST>
void travPost_I ( BinNodePosi(T) x, VST& visit ) { //二叉树的后序遍历（迭代版）
    Stack<BinNodePosi(T)> S; //辅助栈
    if ( x ) S.push ( x ); //根节点入栈
    while ( !S.empty() ) {
        if ( S.top() != x->parent ) //若栈顶非当前节点之父（则必为其右兄），此时需
            gotoHLVFL ( S ); //在以其右兄为根之子树中，找到HLVFL（相当于递归深入其中）
        x = S.pop(); visit ( x->data ); //弹出栈顶（即前一节点之后继），并访问之
    }
}
```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0527.jpg?raw=true)



### 5.4.5 层次遍历

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0528.jpg?raw=true)

* 算法实现

```c++
template <typename T> template <typename VST> //元素类型、操作器
void BinNode<T>::travLevel ( VST& visit ) { //二叉树层次遍历算法
    Queue<BinNodePosi(T)> Q; //辅助队列
    Q.enqueue ( this ); //根节点入队
    while ( !Q.empty() ) { //在队列再次变空之前，反复迭代
        BinNodePosi(T) x = Q.dequeue(); visit ( x->data ); //取出队首节点并访问之
        if ( HasLChild ( *x ) ) Q.enqueue ( x->data ); //左孩子入队
        if ( HasRChild ( *x ) ) Q.enqueue ( x-rc ); //右孩子入队
    }
}
```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0529.jpg?raw=true)

初始化时先令树根入队，随后进入循环。每一步迭代中，首先 取出并访问队首节点，然后其左、右孩子（若存在）将顺序入队。一旦在试图进入下一迭代前发现队列为空，遍历即告完成。

* 完全二叉树

* 满二叉树



## 5.5 Huffman编码

### 5.5.1 PFC编码及解码

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0530.jpg?raw=true)

* 总体框架

```c++
int main ( int argc, char* argv[] ) { //PFC编码、解码算法统一测试入口
    PFCForest* forest = initForest(); //初始化PFC森林
    PFCTree* tree = generateTree ( forest ); release ( forest ); //生成PFC编码树
    PFCTable* table = generateTable ( tree ); //将PFC编码树转换为编码表
    for ( int i = 1; i < argc; i++ ) { //对于命令行传入的每一名明文串
        Bitmap codeString; //二进制编码串
        int n = encode ( table, codeString, argv[i] ); //将根据编码表生成（长度为n）
        decode ( tree, codeString, n ); //利用编码树，对长度为n的二进制编码串解码（直接输出）
    }
    release ( table ); release ( tree ); return 0; //释放编码表、编码树
} //release()负责释放复杂结构，与算法无直接关系，详见代码包
```

* 数据结构的选取与设计

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0531.jpg?raw=true)

* 初始化PFC森林

```c++
PFCForest* initForest() {
    PFCForest* forest = new PFCForest;
    for ( int i = 0; i < N_CHAR; i++ ) {
        forest->insert ( i, new PFCTree() );
        ( *forest ) [i]->insertAsRoot ( 0x20 + i );
    }
    return forest;
}
```

* 构造PFC编码树

```c++
PFCTree* generateTree ( PFCForest* forest ) { //构造PFC树
    srand ( ( unsigned int ) time ( NULL ) ); //这里将随机取树合并，故先设置随机种子
    while ( 1 < forest->size() ) { //共做|forest|-1次合并
        PFCTree* s = new PFCTree; s->insertAsRoot ( '^' ); //创建新树（根标记为"^"）
        Rank r1 = rand() % forest->size(); //随机选取r1，且
        s->attachAsLC ( s->root(), ( *forest )[r1] ); //作为左子树介入后
        forest->remove ( r1 ); //随即剔除
        Rank r2 = rand() % forest->size(); //随机选取r2，且
        s-attachAsRC ( s->root(), ( *forest ) [r2] ); //作为右子树接入后
        forest->remove ( r2 ); //随即剔除
        forest->insert ( forest->size(), s ); //合并后的PFC树重新植入PFC森林
    }
    return ( *forest ) [0]; //至此，森林中尚存的最后一棵树，即全局PFC编码树
}
```

* 生成PFC编码表

```c++
void generateCT //通过遍历获取各字符的编码
( Bitmap* code, int length, PFCTable* table, BinNodePosi ( char ) v ) {
   if ( IsLeaf ( *v ) ) //若是叶节点
      { table->put ( v->data, code->bits2string ( length ) ); return; }
   if ( HasLChild ( *v ) ) //Left = 0
      { code->clear ( length ); generateCT ( code, length + 1, table, v->lc ); }
   if ( HasRChild ( *v ) ) //right = 1
      { code->set ( length ); generateCT ( code, length + 1, table, v->rc ); }
}
 
PFCTable* generateTable ( PFCTree* tree ) { //构造PFC编码表
   PFCTable* table = new PFCTable; //创建以Skiplist实现的编码表
   Bitmap* code = new Bitmap; //用于记录RPS的位图
   generateCT ( code, 0, table, tree->root() ); //遍历以获取各字符（叶节点）的RPS
   release ( code ); return table; //释放编码位图，返回编码表
} //release()负责释放复杂结构，与算法无直接关系，具体实现详见代码包
```

* 编码

```c++
int encode ( PFCTable* table, Bitmap& codeString, char* s ) { //PFC编码算法
   int n = 0;
   for ( size_t m = strlen ( s ), i = 0; i < m; i++ ) { //对于明文s[]中的每个字符
      char** pCharCode = table->get ( s[i] ); //取出其对应的编码串
      if ( !pCharCode ) pCharCode = table->get ( s[i] + 'a' - 'a' ); //小写字母转为大写
      if ( !pCharCode ) pCharCode = table->get ( ' ' ); //无法识别的字符统一视作空格
      printf ( "%s", *pCharCode ); //输出当前字符的编码
      for ( size_t m = strlen ( *pCharCode ), j = 0; j < m; j++ ) //将当前字符的编码接入编码串
         '1' == * ( *pCharCode + j ) ? codeString.set ( n++ ) : codeString.clear ( n++ );
   }
   return n; //二进制编码串记录于codeString中，返回编码串总长
}
```

* 解码

```c++
void decode ( PFCTree* tree, Bitmap& code, int n ) { //PFC解码算法
   BinNodePosi ( char ) x = tree->root(); //根据PFC编码树
   for ( int i = 0; i < n; i++ ) { //将编码（二进制位图）
      x = code.test ( i ) ? x->rc : x->lc; //转译为明码并
      if ( IsLeaf ( *x ) ) { printf ( "%c", x->data ); x = tree->root(); } //打印输出
   }
}
```

* 优化

在计算资源固定的条件下，不同编码方法的效率主要体现于所生成二进制编码串的总长，或者更确切地，体现于二进制码长与原始文本长度的比率。



### 5.5.2 最优编码树

* 平均编码长度与叶节点平均深度

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0532.jpg?raw=true)

* 最优编码树

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0533.jpg?raw=true)

* 双子性

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0534.jpg?raw=true)

* 层次性

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0535.jpg?raw=true)

* 最优编码树的构造

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0536.jpg?raw=true)



### 5.5.3 Huffman编码树

* 字符出现概率

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0537.jpg?raw=true)

* 带权平均编码长度与叶节点带权平均深度

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0538.jpg?raw=true)

* 完全二叉树编码 ≠ wald()最短

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0539.jpg?raw=true)

* 最优带权编码树

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0540.jpg?raw=true)

* 层次性

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0541.jpg?raw=true)

