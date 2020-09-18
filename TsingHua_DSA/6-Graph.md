[TOC]

# 第6章：图

相互之间军可能存在二元关系的一组对象，从数据结构的角度分类，属于非线性结构。此类一般性的二元关系，属于图论（Graph Theory）的研究范畴。

## 6.1 概述

* 图

图(graph)，可定义为G = (V, E)。其中，集合V中的元素称作顶点(vertex)；集合E中的元素分别对应于V中的某一对顶点(u, v)，表示它们之间存在某种关系，故亦称作边(edge)。从计算的需求出发，我们约定V和E均为有限集，通常将其规模分别记n = |V|和e = |E|。

* 无向图、有向图及混合图

若边(u, v)所对应顶点u和v的次序无所谓，则称作无向边(undirected edge)，反之若u和v不对等，则称(u, v)为有向边(directed edge)。

如此，无向边(u, v)也可记作(v, u)，而有向的(u, v)和(v, u)则不可混淆。这里约定，有向边(u, v)从u指向v，其中u称作该边的起点(origin)或尾顶点(tail)，而v称作该边的终点(destination)或头顶点(head)。

若E中各边均无方向，则G称作无向图(undirected graph，简称undigraph)。反之，若E中只含有向边，则G称作有向图(directed graph，简称digraph)。

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0600.jpg?raw=true)

相对而言，有向图的通用性更强，因为无向图和混合图都可转化为有向图——每条无向边(u, v)都可等效地替换为对称地一对有向边(u, v)和(v, u)。

* 度

对于任何边e = (u, v)，称顶点u和v彼此邻接(adjacent)，互为邻居；而它们都与边e彼此关联(incident)。在无向图中，与顶点v关联的边数，称作v的度数(degree)，记作deg(v)。

对于有向边e = (u, v)，e称作u的出边(outgoing edge)、v的入边(incoming edge)。v的出边总数称作其出度(out-degree)，记作outdeg(v)；入边总数称作其入度(in-degree)，记作indeg(v)。

* 简单图

联接于同一顶点之间的边，称作自环(self-loop)。不含任何自环的图称作简单图(simple graph)，也是本书的主要讨论对象。

* 通路与环路

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0601.jpg?raw=true)

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0602.jpg?raw=true)

* 带权网络

图不仅需要表示顶点之间是否存在某种关系，有时还需要表示这一关系的具体细节。以铁路运输为例，可以用顶点表示城市，用顶点之间的联边，表示对应的城市之间是否有客运铁路联接；同时，往往还需要记录各段铁路的长度、承运能力，以及运输成本等信息。

为适应这类应用要求，需通过一个权值函数，为每一边e指定一个权重(weight)，比如wt(e)即为边e的权重。各边均带有权重的图，称作带权图(weighted graph)或带权网络(weighted network)，有时也简称网络(network)，记作G(V, E, wt())。

* 复杂度

对于无向图来说，每一对顶点至多贡献一条边，故总共不超过n(n - 1)/2条边，且这个上界由完全图达到。对于有向图，每一对顶点都可能贡献（互逆的）两条边，因此至多可有n(n - 1)条边。总而言之，必有e = O(n^2)。



## 6.2 抽象数据类型

### 6.2.1 操作接口

边操作接口

e()：边总数|E|

exist(v, u)：判断联边(v, u)是否存在

insert(v, u)：引入从顶点v到u的联边

remove(v, u)：删除从顶点v到u的联边

type(v, u)：边在遍历树中所属的类型

edge(v, u)：边所对应的数据域

weight(v, u)：边的权重



顶点操作接口

n()：顶点总数|N|

insert(v, u)：在顶点集V中插入新顶点v

remove(v, u)：将顶点v从顶点集中删除

inDegree(v)/outDegree(v)：顶点v的入度，出度

firstNbr(v)：顶点v的首个邻接顶点

nextNbr(v, u)：在v的邻接定点中，u的后继

status(v)：顶点v的状态

dTime(v)、fTime(v)：顶点v的时间标签

parent(v)：顶点v在遍历树中的父节点

priority(v)：顶点v在遍历树中的权重



### 6.2.2 Graph模板类

```c++
typedef enum { UNDISCOVERED, DISCOVERED, VISITED } VStatus; //顶点状态
typedef enum { UNDETERMINED, TREE, CROSS, FORWARD, BACKWARD } EType; //边在遍历树中所属的类型

template <typename Tv, typename Te> //顶点类型、边类型
class Graph { //图Graph模板类
private:
    void reset() { //所有顶点、边的辅助信息复位
        for ( int i = 0; i < n; i++ ) { //所有顶点的
            status ( i ) = UNDISCOVERED; dTime ( i ) = fTime ( i ) = -1; //状态、时间标签
            parent ( i ) = -1; priority ( i ) = INT_MAX; //（在遍历树中的）父节点，优先级数
            for ( int j = 0; j < n; j++ ) //所有边的
                if ( exists ( i, j ) ) type ( i, j ) = UNDETERMINED; //类型
        }
    }
    void BFS ( int, int& ); //（连通域）广度优先搜索算法
    void DFS ( int, int& ); //（连通域）深度优先搜索算法
    void BCC ( int, int&, Stack<int>& ); //（连通域）基于DFS的双连通分量分解算法
    bool TSort ( int, int&, Stack<Tv>* ); //（连通域）基于DFS的拓扑排序算法
    template <typename PU> void PFS ( int, PU ); //（连通域）优先级搜索框架
    public:
// 顶点
    int n; //顶点总数
    virtual int insert ( Tv const& ) = 0; //插入顶点，返回编号
    virtual Tv remove ( int ) = 0; //删除顶点及其关联边，返回该顶点信息
    virtual Tv& vertex ( int ) = 0; //顶点v的数据（该顶点的确存在）
    virtual int inDegree ( int ) = 0; //顶点v的入度（该顶点的确存在）
    virtual int outDegree ( int ) = 0; //顶点v的出度（该顶点的确存在）
    virtual int firstNbr ( int ) = 0; //顶点v的首个邻接顶点
    virtual int nextNbr ( int, int ) = 0; //顶点v的（相对于顶点j的）下一邻接顶点
    virtual VStatus& status ( int ) = 0; //顶点v的状态
    virtual int& dTime ( int ) = 0; //顶点v的时间标签dTime
    virtual int& fTime ( int ) = 0; //顶点v的时间标签fTime
    virtual int& parent ( int ) = 0; //顶点v在遍历树中的父亲
    virtual int& priority ( int ) = 0; //顶点v在遍历树中的优先级数
// 边：这里约定，无向边均统一转化为方向互逆的一对有向边，从而将无向图视作有向图的特例
    int e; //边总数
    virtual bool exists ( int, int ) = 0; //边(v, u)是否存在
    virtual void insert ( Te const&, int, int, int ) = 0; //在顶点v和u之间插入权重为w的边e
    virtual Te remove ( int, int ) = 0; //删除顶点v和u之间的边e，返回该边信息
    virtual EType & type ( int, int ) = 0; //边(v, u)的类型
    virtual Te& edge ( int, int ) = 0; //边(v, u)的数据（该边的确存在）
    virtual int& weight ( int, int ) = 0; //边(v, u)的权重
// 算法
    void bfs ( int ); //广度优先搜索算法
    void dfs ( int ); //深度优先搜索算法
    void bcc ( int ); //基于DFS的双连通分量分解算法
    Stack<Tv>* tSort ( int ); //基于DFS的拓扑排序算法
    void prim ( int ); //最小支撑树Prim算法
    void dijkstra ( int ); //最短路径Dijkstra算法
    template <typename PU> void pfs ( int, PU ); //优先级搜索框架
};
```

仍为简化起见，这里直接开放了变量n和e。除以上所列的操作接口，这里还明确定义了顶点和边可能处于的若干状态，并通过内部接口reset()复位顶点和边的状态。



## 6.3 邻接矩阵

### 6.3.1 原理

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0603.jpg?raw=true)



### 6.3.2 实现

```c++
#include "Vector/Vector.h" //引入向量
#include "Graph/Graph.h" //引入图ADT

template <typename Tv> struct Vertex { //顶点对象（为简化起见，并未严格封装）
   Tv data; int inDegree, outDegree; VStatus status; //数据、出入度数、状态
   int dTime, fTime; //时间标签
   int parent; int priority; //在遍历树中的父节点、优先级数
   Vertex ( Tv const& d = ( Tv ) 0 ) : //构造新顶点
      data ( d ), inDegree ( 0 ), outDegree ( 0 ), status ( UNDISCOVERED ),
      dTime ( -1 ), fTime ( -1 ), parent ( -1 ), priority ( INT_MAX ) {} //暂不考虑权重溢出
};

template <typename Te> struct Edge { //边对象（为简化起见，并未严格封装）
   Te data; int weight; EType type; //数据、权重、类型
   Edge ( Te const& d, int w ) : data ( d ), weight ( w ), type ( UNDETERMINED ) {} //构造
};

template <typename Tv, typename Te> //顶点类型、边类型
class GraphMatrix : public Graph<Tv, Te> { //基于向量，以邻接矩阵形式实现的图
private:
   Vector< Vertex< Tv > > V; //顶点集（向量）
   Vector< Vector< Edge< Te > * > > E; //边集（邻接矩阵）
public:
   GraphMatrix() { n = e = 0; } //构造
   ~GraphMatrix() { //析构
      for ( int j = 0; j < n; j++ ) //所有动态创建的
         for ( int k = 0; k < n; k++ ) //边记录
            delete E[j][k]; //逐条清除
   }
// 顶点的基本操作：查询第i个顶点（0 <= i < n）
   virtual Tv& vertex ( int i ) { return V[i].data; } //数据
   virtual int inDegree ( int i ) { return V[i].inDegree; } //入度
   virtual int outDegree ( int i ) { return V[i].outDegree; } //出度
   virtual int firstNbr ( int i ) { return nextNbr ( i, n ); } //首个邻接顶点
   virtual int nextNbr ( int i, int j ) //相对于顶点j的下一邻接顶点（改用邻接表可提高效率）
   { while ( ( -1 < j ) && ( !exists ( i, --j ) ) ); return j; } //逆向线性试探
   virtual VStatus& status ( int i ) { return V[i].status; } //状态
   virtual int& dTime ( int i ) { return V[i].dTime; } //时间标签dTime
   virtual int& fTime ( int i ) { return V[i].fTime; } //时间标签fTime
   virtual int& parent ( int i ) { return V[i].parent; } //在遍历树中的父亲
   virtual int& priority ( int i ) { return V[i].priority; } //在遍历树中的优先级数
// 顶点的动态操作
   virtual int insert ( Tv const& vertex ) { //插入顶点，返回编号
      for ( int j = 0; j < n; j++ ) E[j].insert ( NULL ); n++; //各顶点预留一条潜在的关联边
      E.insert ( Vector<Edge<Te>*> ( n, n, ( Edge<Te>* ) NULL ) ); //创建新顶点对应的边向量
      return V.insert ( Vertex<Tv> ( vertex ) ); //顶点向量增加一个顶点
   }
   virtual Tv remove ( int i ) { //删除第i个顶点及其关联边（0 <= i < n）
      for ( int j = 0; j < n; j++ ) //所有出边
         if ( exists ( i, j ) ) { delete E[i][j]; V[j].inDegree--; e--; } //逐条删除
      E.remove ( i ); n--; //删除第i行
      Tv vBak = vertex ( i ); V.remove ( i ); //删除顶点i
      for ( int j = 0; j < n; j++ ) //所有入边
         if ( Edge<Te> * x = E[j].remove ( i ) ) { delete x; V[j].outDegree--; e--; } //逐条删除
      return vBak; //返回被删除顶点的信息
   }
// 边的确认操作
   virtual bool exists ( int i, int j ) //边(i, j)是否存在
   { return ( 0 <= i ) && ( i < n ) && ( 0 <= j ) && ( j < n ) && E[i][j] != NULL; }
// 边的基本操作：查询顶点i与j之间的联边（0 <= i, j < n且exists(i, j)）
   virtual EType & type ( int i, int j ) { return E[i][j]->type; } //边(i, j)的类型
   virtual Te& edge ( int i, int j ) { return E[i][j]->data; } //边(i, j)的数据
   virtual int& weight ( int i, int j ) { return E[i][j]->weight; } //边(i, j)的权重
// 边的动态操作
   virtual void insert ( Te const& edge, int w, int i, int j ) { //插入权重为w的边e = (i, j)
      if ( exists ( i, j ) ) return; //确保该边尚不存在
      E[i][j] = new Edge<Te> ( edge, w ); //创建新边
      e++; V[i].outDegree++; V[j].inDegree++; //更新边计数与关联顶点的度数
   }
   virtual Te remove ( int i, int j ) { //删除顶点i和j之间的联边（exists(i, j)）
      Te eBak = edge ( i, j ); delete E[i][j]; E[i][j] = NULL; //备份后删除边记录
      e--; V[i].outDegree--; V[j].inDegree--; //更新边计数与关联顶点的度数
      return eBak; //返回被删除边的信息
   }
};
```



### 6.3.3 时间性能

按照如上代码的实现方式，各顶点的编号可直接转换为其在邻接矩阵中对应的秩，从而使得图ADT中所有的静态操作接口均只需O(1)时间——这主要是得益于向量“循秩访问”的特长与优势。另外，边的静态和动态操作也仅需O(1)时间——其代价是邻接矩阵的空间冗余。

然而，这种方法并非完美无缺。其不足主要体现在，顶点的动态操作接口均十分耗时。为了插入新顶点，顶点集向量V[]需要添加一个元素；边集向量E[]\[]也需要增加一行，且每行都需要添加一个元素。顶点删除操作，亦与此类似。不难看出，这些恰恰也是向量结构固有的不足。

好在通常的算法中，顶点的动态操作远少于其他操作。而且，即便计入向量扩容的代价，就分摊意义而言，单次操作的耗时亦不过O(n)。



### 6.3.4 空间性能

上述实现方式所用空间，主要消耗于邻接矩阵，亦即其中的二维边集向量E[]\[]。每个Edge对象虽需记录多项信息，但总体不过常数。根据2.4.4节的分析结论，Vector结构的装填因子始终不低于50%。故空间总量渐进地不超过O(n x n) = O(n^2)。

当然，对于无向图而言，仍有改进地余地。如图6.5(a)所示，无向图地邻接矩阵必为对称矩阵，其中除自环以外地每条边，都被重复地存放了两次。也就是说，近一半地单元都是冗余地。为消除这一缺点，可采用压缩存储等技巧，进一步提高空间利用率。



## 6.4 邻接表

### 6.4.1 原理

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0604.jpg?raw=true)

### 6.4.2 复杂度

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0605.jpg?raw=true)



## 6.5 图遍历算法概述

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0606.jpg?raw=true)



## 6.6 广度优先搜索

### 6.6.1 策略

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0607.jpg?raw=true)



### 6.6.2 实现

```c++
template <typename Tv, typename Te> //广度优先搜索BFS算法（全图）
void Graph<Tv, Te>::bfs ( int s ) { //assert: 0 <= s < n
	reset(); int clock = 0; int v = s; //初始化
    do //逐一检查所有顶点
        if ( UNDISCOVERED == status ( v ) ) //一旦遇到尚未发现地顶点
            BFS ( v, clock ); //即从该顶点出发启动一次BFS
    while ( s != ( v = ( ++v % n ) ) ); //按序号检查，故不漏不重
}

template <typename Tv, typename Te> //广度优先搜索BFS算法（单个连通域）
void Graph<Tv, Te>::BFS ( int v, int& clock ) { //assert: 0 <= v < n
    Queue<int> Q; //引入辅助队列
    status ( v ) = DISCOVERED; Q.enqueue ( v ); //初始化起点
    while ( !Q.empty() ) { //在Q变空之前，不断
    	int v = Q.dequeue(); dTime( v ) = ++clock; //取出队首顶点v
        for ( int u = firstNbr ( v ); -1 < u; u = nextNbr ( v, u ) ) //枚举v的所有邻居u
            if ( UNDISCOVERED == status ( u ) ) { //若u尚未被发现，则
                status ( u ) = DISCOVERED; Q.enqueue ( u ); //发现该顶点
                type ( v, u ) = TREE; parent ( u ) = v; //引入树边拓展支撑树
            } else { //若u已被发现，或者甚至已访问完毕，则
                type ( v, u ) = CROSS; //将(v, u)归类于跨边
            }
        status ( v ) = VISITED; //至此，当前顶点访问完毕
    }
}
```

