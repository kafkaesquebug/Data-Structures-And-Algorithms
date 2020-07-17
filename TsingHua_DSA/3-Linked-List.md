[toc]



# 第3章：列表

列表与向量同属序列结构的范畴，其中的元素也构成一个线性逻辑次序；但与向量极为不同的是，元素的物理地址可以任意。	

为保证对列表元素访问的可行性，逻辑上互为前驱和后继的元素之间，应维护某种索引关系。这种索引关系，可抽象地理解为被索引元素的位置(position)，故列表元素是“循位置访问”(call-by-position)的；也可形象地理解为通往被索引元素的链接(link)，故亦称作“循链接访问”(call-by-link)。向量中的秩同时对应于逻辑和物理次序，而位置仅对应于逻辑次序。



## 3.1 从向量到列表

二者之间的差异，表面上体现于对外的操作方式，但根源在于其内部存储方式不同。



### 3.1.1 从静态到动态

数据结构支持的操作通常是静态和动态两类：静态获取信息，动态修改数据结构。以第2章为例，size()和get()等静态操作均可在常数时间内完成，而insert()和remove()等动态操作却都可能需要线性时间。究其原因，在于“各元素物理地址连续”的约定——此即所谓的“静态存储”策略。

列表(list)结构尽管也要求各元素在逻辑上具有线性次序，但对其物理地址却未作任何限制——此即所谓“动态存储”策略。

链表(linked list)就是一种典型的动态存储结构。其中的数据分散为一系列称作节点(node)的单位，节点之间通过指针相互索引和访问。



### 3.2.1 列表节点

* ADT接口

  data()：当前节点所存数据对象

  pred()：当前节点前驱节点的位置

  succ()：当前节点后继节点的位置

  insertAsPred()：插入前驱节点，存入被引用对象e，返回新节点位置

  insertAsSucc()：插入后继节点，存入被引用对象e，返回新节点位置

* ListNode模板类

  ```c++
  typedef int Rank; //秩
  #define ListNodePosi(T) ListNode<T>* //列表节点位置
  
  template <typename T> struct ListNode { //列表节点模板类（以双向链表形式实现）
  // 成员
      T data; ListNodePosi(T) pred; ListNodePosi(T) succ; //数值、前驱、后继
  // 构造函数
      ListNode() {} //针对header和trailer的构造
      ListNode( T e, ListNodePosi(T) p = NULL, ListNodePosi(T) s = NULL)
          : data ( e ), pred ( p ), succ ( s ) {} //默认构造器
  // 操作接口
      ListNodePosi(T) insertAsPred ( T const& e ); //紧靠当前节点之前插入新节点
      ListNodePosi(T) insertAsSucc ( T const& e ); //紧随当前节点之后插入新节点
  };
  ```

  

### 3.2.2 列表

* ADT接口

  * 适用对象为列表

    size()：报告列表当前的规模（节点总数）

    first()、last()：返回首、末节点的位置

    insertAsFirst(e)/insertAsLast(e)：将e当作首、末节点插入

    insertA(p, e)/insertB(p, e)：将e当作节点p的直接后继、前驱插入

    remove(p)：删除位置p处的节点，返回其数值

    disordered()：判断所有节点是否已按非降序排列

    sort()：调整各节点的位置，使之按非降序排列

    find(e)：查找目标元素e，失败时返回NULL

    deduplicate()：剔除重复节点

    traverse()：遍历并统一处理所有节点，处理方法由函数对象指定

  * 有序列表

    search(e)：查找目标元素e，返回不大于e且秩最大的节点

    traverse()：遍历并统一处理所有节点，处理方法由函数对象指定

* List模板类

  ``` c++
  #include "listNode.h" //引入列表节点类
  
  template <typename T> class List { //列表模板类
      
  privat:
      int _size; ListNodePosi(T) header; ListNodePosi(T) trailer; //规模、头哨兵、尾哨兵
      
  protected:
      void init(); //列表创建时的初始化
      int clear(); //清除所有节点
      void copyNodes ( ListNodePosi(T), int ); //复制列表中自位置p起的n项
      void merge ( ListNodePosi(T)&, int, List<T>&, ListNodePosi(T), int ); //归并
      void mergeSort ( ListNodePosi(T)&, int ); //对从p开始连续的n个节点归并排序
      void selectionSort ( ListNodePosi(T), int ); //对从p开始连续的n个节点选择排序
      void insertionSort ( ListNodePosi(T), int ); //对从p开始连续的n个节点插入排序
      
  public:
  // 构造函数
      List() { init(); } //默认
      List ( List<T> const& L ); //整体复制列表L
      List ( List<T> const& L, Rank r, int n ); //复制列表L中自第r项起的n项
      List ( ListNodePosi(T) p, int n ); //复制列表中自位置p起的n项
  // 析构函数
      ~List(); //释放（包含头、尾哨兵在内的）所有节点
  // 只读访问接口
      Rank size() const { return _size; } //规模
      bool empty() const { return _size <= 0; } //判空
      T& operator[] ( Rank r ) const; //重载，支持循秩访问（效率低）
      ListNodePosi(T) first() const { return header->succ; } //首节点位置
      ListNodePosi(T) last() const { return trailer->pred; } //末节点位置
      bool valid ( ListNodePosi(T) p ) //判断位置p是否对外合法
      { return p && ( trailer != p ) && ( header != p ); } //将头、尾节点等同于NULL
      int disordered() const; //判断列表是否已排序
      ListNodePosi(T) find ( T const& e ) const //无序列表查找
      { return find ( e, _size, trailer ); }
      ListNodePosi(T) find ( T const& e, int n, ListNodePosi(T) p ) const; //无序区间查找
      ListNodePosi(T) search ( T const& e ) const //有序列表查找
      { return search ( e, _size, trailer ); }
      ListNodePosi(T) search ( T const& e, int n, ListNodePosi(T) p ) const; //有序区间查找
      ListNodePosi(T) selectMax {  ListNodePosi(T) p, int n ); } //在p及其n-1个后继者中选出最大者
      ListNodePosi(T) selectMax() { return selectMax ( header->succ, _size ); } //整体最大者
  // 可写访问接口
      ListNodePosi(T) insertAsFirst ( T const& e ); //将e当作首节点插入
      ListNodePosi(T) insertAsLast ( T const& e ); //将e当作末节点插入
      ListNodePosi(T) insertA ( ListNodePosi(T) p, T const& e ); //将e当作p的后继插入
      ListNodePosi(T) insertB ( ListNodePosi(T) p, T const& e ); //将e当作p的前驱插入
      T remove ( ListNodePosi(T) p ); //删除合法位置p处的节点，返回被删除节点
  	void merge ( List<T>& L ) { merge ( first(), size, L, L.first(), L._size ); } //全列表归并
      void sort ( ListNodePosi(T) p, int n ); //列表区间排列
      void sort() { sort ( first(), _size ); } //列表整体排序
      int deduplicate(); //无序去重
      int uniquify(); //有序去重
      void reverse(); //前后倒置
  // 遍历
  	void traverse ( void (* ) ( T& ) ); //遍历，依次实施visit操作（函数指针，只读或局部性修改）
      template <typename VST> //操作器
      void traverse ( VST& ); //遍历，依次实施visit操作（函数对象，可全局性修改） 
  };
  ```

  

## 3.3 列表

### 3.3.1 头、尾节点

头节点(header)和尾节点(trailer)始终存在，但对外不可见，这类封装后从外部不可见的节点称作哨兵节点(sentinel node)。对外部可见的数据节点如果存在，则其中的第一个和最后一个节点分别称作首节点(first node)和末节点(last node)。

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0215.jpg?raw=true)

头、首、末、尾节点的秩可分别理解为-1，0，n-1，n

### 3.3.2 默认构造方法

```c++
template <typename T> void List<T>::init() { //列表初始化，在创建列表对象时统一调用
    header = new ListNode<T>; //创建头哨兵节点
    trailer = new ListNode<T>; //创建尾哨兵节点
    header->succ = trailer; header->pred = NULL;
    trailer->pred = header; trailer->succ = NULL;
    _size = 0; //记录规模
}
```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0216.jpg?raw=true)



### 3.3.3 由秩到位置的转换

```c++
template <typename T> //重载下标操作符，以通过秩直接访问列表节点(虽方便，效率低)
T& List<T>::operator[] ( Rank r ) const { //assert: 0 <= r < size
    ListNodePosi(T) p = first(); //从首节点出发
    while ( 0 < r-- ) p = p->succ; //顺数第r个节点即是
    return p->data; //目标节点，返回其中所存元素
}
```



### 3.3.4 查找

列表ADT针对整体和区间查找，重载了操作接口find(e)和find(e, p, n)。其中，前者作为特例，可以直接调用后者。

```c++
template <typename T> //在无序列表内节点p（可能是trailer）的n个（真）前驱中，找到等于e的最后者
ListNodePosi(T) List<T>::find ( T const& e, int n, ListNodePosi(T) p ) const {
    while ( 0 < n-- ) // (0 <= n <= rank(p) < _size)对于p的最近的n个前驱，从右向左
        if ( e == ( p = p->pred )->data ) return p; //逐个对比，直至命中或范围越界
    return NULL; //p越出左边界意味着区间内不含e，查找失败
} //失败时返回NULL
```

* 复杂度

  以上算法的思路及过程与无序向量顺序查找算法Vector::find()相仿，时间复杂度也是O(n)，线性正比于查找区间的宽度。



### 3.3.5 插入

* 接口

  ```c++
  template <typename T> ListNodePosi(T) List<T>::insertAsFirst ( T const& e )
  { _size++; return header->insertAsSucc ( e ); } //e当作首节点插入
  
  template <typename T> ListNodePosi(T) List<T>::insertAsLast ( T const& e )
  { _size++; return trailer->insertAsPred ( e ); } //e当作末节点插入
  
  template <typename T> ListNodePosi(T) List<T>::insertA ( ListNodePosi(T) p, T const& e )
  { _size++; return p->insertAsSucc ( e ); } //e当作p的后继插入
  
  template <typename T> ListNodePosi(T) List<T>::insertB ( ListNodePosi(T) p, T const& e )
  { _size++; return p->insertAsPred ( e ); } //e当作p的前驱插入
  ```

* 前插入

  将新元素e作为当前节点的前驱插至列表的过程

  ```c++
  template <typename T> 
  ListNodePosi(T) List<T>::insertAsPred ( T const& e ) {
  	ListNodePosi(T) x = new ListNode ( e, pred, this ); //创建新节点
      pred->succ = x; pred = x; //设置正向链接
      return x; //返回新节点的位置
  }
  ```

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0217.jpg?raw=true)

* 后插入

  将新元素e作为当前节点的后继插至列表的过程

  ```c++
  template <typename T> 
  ListNodePosi(T) List<T>::insertAsSucc ( T const& e ) {
  	ListNodePosi(T) x = new ListNode ( e, this, succ ); //创建新节点
      succ->pred = x; succ = x; //设置正向链接
      return x; //返回新节点的位置
  }
  ```



### 3.3.6 基于复制的构造

* copyNodes()

  ```c++
  template <typename T>  //列表内部方法：复制列表中自位置p起的n项
  void List<T>::copyNodes ( ListNodePosi(T) p, int n ) { //p合法，且至少有n-1个真后继节点
  	init(); //创建头、尾哨兵节点并做初始化
      while ( n-- ) { insertAsLast ( p->data ); p = p->succ; } //将起自p的n项依次作为末节点插入
  }
  ```

  过程总体运行时间为O(n + 1)，线性正比于待复制列表区间的长度n。

* 基于复制的构造

  ```c++
  template <typename T>  //复制列表中自位置p起的n项（assert：p为合法位置，且至少有n-1个后继节点）
  List<T>::List ( ListNodePosi(T) p, int n ) { copyNodes ( p, n ); }
  
  template <typename T>  //整体复制列表L
  List<T>::List ( List<T> const& L ) { copyNodes ( L.first(), L._size ); }
  
  template <typename T>  //复制列表L中自第r项起的n项（assert: r+n <= L._size）
  List<T>::List ( List<T> const& L, int r, int n ) { copyNodes ( L[r], n ); }
  ```



### 3.3.7 删除

* 实现

  ```c++
  template <typename T> T List<T>::remove ( ListNodePosi(T) p ) { //删除合法节点p，返回其数值
      T e = p->data; //备份待删除节点的数值（假定T类型可直接赋值）
      p->pred->succ = p->succ->pred = p->pred; //后继、前驱
      delete p; __size--; //释放节点，更新规模
      return e; //返回备份的数值
  }
  ```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0218.jpg?raw=true)



### 3.3.8 析构

* 释放资源及清除节点

  ```c++
  template <typename T> List<T>::~List() //列表析构器
  { clear(); delete header; delete trailer; } //清空列表，释放头、尾哨兵节点
  ```

  可见，列表的析构首先需要调用clear()接口删除并释放所有对外部有效的节点，然后释放内部的头、尾哨兵节点。clear()过程则可描述和实现如：

  ```c++
  template <typename T> int List<T>::clear() { //清空列表
      int oldSize = _size;
      while ( 0 < _size ) remove ( header->succ ); //反复删除首节点，直至列表变空
      return oldSize;
  }
  ```



### 3.3.9 唯一化

* 实现

  ```c++
  template <typename T> int List<T>::deduplicate() {//剔除无序列表中的重复节点
      if ( _size < 2 ) return 0; //平凡列表自然无重复
      int oldSize = _size; //记录原规模
      ListNodePosi(T) p = header; Rank r = 0; //p从首节点开始
      while ( trailer != ( p = p->succ ) ) {//依次直到末节点
          ListNodePosi(T) q = find ( p->data, r, p ); //在p的r个（真）前驱中查找雷同者
          q ? remove ( q ) : r++; //若的确存在，则删除，否则秩加一
      } //assert: 循环过程中的任意时刻，p的所有前驱互不相同
      return oldSize - _size; //列表规模变化量，即被删除元素的总数
  }
  ```

* 复杂度

  O(n^2)，详情见P77

  

### 3.3.10 遍历

```c++
template <typename T> void List<T>::traverse ( void ( *visit ) ( T& ) ) //借助函数指针机制遍历
{ for ( ListNodePosi(T) p = header->succ; p != trailer; p = p->succ ) visit ( p-> data ); }

template <typename T> template <typename VST> //元素类型、操作器
void List<T>::traverse ( VST& visit ) //借助函数对象机制遍历
{ for ( ListNodePosi(T) p = header->succ; p != trailer; p = p->succ ) visit ( p-> data ); }
```



## 3.4 有序列表

### 3.4.1 唯一化

```c++
template <typename T> int List<T>::uniquify() { //成批剔除重复元素，效率更高
    if ( _size < 2 ) return 0; //平凡列表字然无重复
    int oldSize = _size; //记录原规模
    ListNodePosi(T) p = first(); ListNodePosi(T) q; //p为各区段起点，q为其后继
    while ( trailer != ( q = p->succ ) ) //反复考查紧邻节点对(p, q)
        if ( p->data != q->data ) p = q; //若互异，则转向下一区段；
        else remove ( q ); //否则删除后者
    return oldSize - _size; //列表规模变化量，即被删除元素总数
}
```

整个过程运行时间为O(_size) = O(n)。



### 3.4.2 查找

* 实现

  ```C++
  template <typename T> //在有序列表内节点p（可能是trailer）的n个（真）前驱中，找不到大于e的最后者
  ListNodePosi(T) List<T>::search ( T const& e, inre n, ListNodePosi(T) p ) const { // assert: 0 <= n <= rank(p) < _size
      while ( 0 <= n-- ) //对于p的最近的n个前驱，从右向左逐个比较
          if ( ( ( p = p->pred )->data ) <=e ) break; //直至命中、数值越界或范围越界
  // assert: 至此位置p必符合输出语义约定——尽管此前最后一次关键码比较可能没有意义（等效于与-inf比较）
      return p; //返回查找终止的位置
  } //失败时，返回区间左边界的前驱（可能是header）——调用者可通过valid()判断成功与否
  ```

* 顺序查找

  与3.3.4的算法差不多，与2.6.5-2.6.8的有序向量查找算法不同，因为列表中节点的物理地址与逻辑次序无关，列表无法像有序向量那样用减而治之策略。

* 复杂度

  与无序向量查找算法同理：最好情况下运行时间为O(1)，最坏情况下为O(n)。等概率的前提下，平均运行时间也是O(n)，线性正比于查找区间的宽度。



## 3.5 排序器

### 3.5.1 统一入口

```c++
template <typename T> void List<T>::sort ( ListNodePosi(T) p, int n ) { //列表区间排序
    switch ( rand() % 3 ) { //随机选取排序算法，可根据具体问题的特点灵活选取或扩充
        case 1: insertionSort ( p, n ); break; //插入排序
        case 2: selectionSort ( p, n ); break; //选择排序
        default: mergeSort ( p, n ); break; //归并排序
    }
}
```



### 3.5.2 插入排序

* 构思

  适用于任何序列结构。

  思路：始终将整个序列视作并切分为两部分：有序的前缀，无序的后缀；通过迭代反复将后缀的首元素转移到前缀中。

  不变性：在任何时刻，相对于当前节点e = S[r]，前缀S[0, r)总是有序

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0219.jpg?raw=true)

* 实例

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0220.jpg?raw=true)

* 实现

  ```c++
  template <typename T> //列表的插入排序算法：对起始于位置p的n个元素排序
  void List<T>::insertionSort ( ListNodePosi(T) p, int n ) { //valid(p) && rank(p) + n <= size
      for ( int r = 0; r < n; r++ ) { //逐一为各节点
          insertA ( search ( p->data, r, p ), p->data ); //查找适当的位置并插入
          p = p->succ; remove ( p->pred ); //转向下一节点
      }
  }
  ```

* 复杂度

  迭代时间O(n)，删除插入时间O(1)，查找时间在O(1)~O(n)之间，累计需要O(n^2)时间。



### 3.5.3 选择排序

- 构思

也分为前缀后缀两部分，还要求前缀不大于后缀，在算法初期，后缀为空。如此，每次只需从前缀中选出最大者，并作为最小元素转移至后缀中，即可使有序部分的范围不断扩张。不变性：在任何时刻，后缀S(r, n)已经有序，且不小于前缀S[0, r]

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0221.jpg?raw=true)

- 实例

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0222.jpg?raw=true)

- 实现

```c++
template <typename T> //列表的选择排序：对起始于位置p的n个元素排序
void List<T>::selectionSort ( ListNodePosi(T) p, int n ) { //valid(p) && rank(p) + n <= size
    ListNodePosi(T) head = p->pred; ListNodePosi(T) tail = p;
    for ( int i = 0; i < n; i++ ) tail = tail->succ; //待排序区间为(head, tail)
    while ( 1 < n ) { //在至少还剩两个结点之前，在待排序区间内
        ListNodePosi(T) max = selectMax ( head->succ, n ); //找出最大者（歧义时后者优先）
        insertB ( tail, remove ( max ) ); //将其移动至无序区间末尾（作为有序区间新的首元素）
        tail = tail->pred; n--;
    } 
}
```

其中selectMax()接口用于在无序列表中定位最大节点，实现如下：

```c++
template <typename T> //从起始于位置p的n个元素中选出最大者
ListNodePosi(T) List<T>::selectMax ( ListNodePosi(T) p, int n ) {
    ListNodePosi(T) max = p; //最大者暂时定为首节点p
    for ( ListNodePosi(T) cur = p； 1 < n; n-- ) //从p出发，将后续节点逐一与max比较
        if ( !lt ( ( cur = cur->succ )->data, max->data ) ) 
            max = cur;
    return max;
}
```

- 复杂度

选择排序由n步迭代组成，运行时间取决于各步迭代中查找及插入操作的效率。insert和remove均只需要O(1)时间，selectMax()每次必须遍历整个无序前缀，耗时线性正比于前缀长度：全程累计O(n^2)

实际上无论输入序列中各元素的大小次序如何，以上n次selectMax()调用的累计耗时总是Θ(n^2)。因此与插入排序算法不同，以上选择排序算法的时间复杂度固定为Θ(n^2)，也就是说其最好和最坏情况下的渐进效率相同。



### 3.5.4 归并排序`*`

* 实现

  ```c++
  template <typename T> //有序列表的归并：当前列表中自p起的n个元素，与列表L中自q起的m个元素归并
  void List<T>::merge ( ListNodePosi (T) & p, int n, List<T>& L, ListNodePosi(T) q, int m ) {
  // assert: this.valid(p) && rank(p) + n <= size && this.sorted(p, n)
  // L.valid(q) && rank(q) + m <= L._size && L.sorted(q, m)
  // 注意：在归并排序之类的场合，有可能this == L && rank(p) + n =rank(q)
      ListNodePosi(T) pp = p->pred; //借助前驱（可能是header），以便返回前...
      while ( 0 < m ) //在q尚未移出区间之前
          if ( ( 0 < n ) && ( p->data <= q->data ) ) { //若p仍在区间内且v(p) <= v(q)，则
              if ( q == ( p = p->succ ) ) break; n--; //p归入合并的列表，并替换为其直接后继
          } else { //若p已经超出右界或v(q) < v(p)，则
              insertB ( p, L.remove ( ( q = q->succ )->pred ) ); m--; //将q转移至p之前
          }
      p = pp->succ; //确定归并后区间的新起点
  }
  ```

* 归并时间

  merge()的时间成本主要消耗于其中的迭代。该迭代反复地比较两个子列表的首节点p和q，并视其大小相应地令p指向其后继，或将节点q取出并作为p的前驱插入前一子列表。当且仅当后以子列表中所有节点均处理完毕时，迭代才会终止。因此在最好的情况下，共需迭代m次：而在最坏的情况下，则需迭代n次。总体而言，共需O(n + m)时间，线性正比于两个子列表的长度之和。

* 特例：分治策略并基于有序列表

  ```c++
  template <typename T> //列表的归并排序：对起始于位置p的n个元素排序
  void List<T>::mergeSort ( ListNodePosi(T) & p, int n ) {//valid(p) && rank(p) + n <= size
      if ( n < 2 ) return; //若待排序范围已经足够小，则直接返回；否则...
      int m = n >> 1; //以中点为界
      ListNodePosi(T) q = p; for ( int i = 0; i < m; i++ ) q = q->succ; //均分列表
      mergeSort ( p, m); mergerSort ( q, n - m ); //对前后子列表分别排序
      merge ( p, m, *this, q, n - m ); //归并
  } //注意：排序后，p依然指向归并后区间的新起点
  ```

* 排序时间

  首先需要花费线性时间确定剧中的切分节点，然后递归地对长度均为n/2的两个子列表做归并排序，最后花时间做二路归并。仿照2.8.3的方法可知其复杂度为O(nlogn)。















































