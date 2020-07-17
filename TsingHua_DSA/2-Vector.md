[toc]



# 第2章：向量

数据结构是数据项的结构化集合，其结构性表现为数据项之间的相互联系及作用，也可以理解为定义于数据项之间的某种逻辑次序。根据这种逻辑次序的复杂程度，大致可以将各种数据结构划分为`线性结构`、`半线性结构`与`非线性结构`三大类。

在线性结构中，各数据项按照一个线性次序构成一个整体。最基本的线性结构统称为序列(sequence)，根据其中数据项的逻辑次序与其无里存储地址的对应关系不同，又可进一步地将序列区分为向量(vector)和列表(list)。

在向量中，所有数据项的物理存放位置与其逻辑次序完全吻合，此时的逻辑次序也称作秩(rank)；而在列表中，逻辑上相邻的数据项在物理上未必相邻，而是采用间接定址的方式通过封装后的位置(position)相互引用。



## 2.1 从数组到向量

### 2.2.1 数组

若集合S由n个元素组成，且各元素之间具有一个线性次序，则可将他们存放于起始于地址A、物理位置连续的一段存储空间，并统称作数组(array)。若数组A[]存放空间的起始地址为A，且每个元素占用s个单位的空间，则元素A[i]对应的物理地址为：

A + i X s

因其中元素的物理地址与其下标满足这种线性关系，故亦称作线性数组(linear array)。



### 2.1.2 向量

向量就是线性数组的一种抽象和泛化，也是由具有线性次序的一组元素构成的集合，其中的元素分别由秩互相区分。

以此前介绍的线性递归为例，运行过程中所出现过的所有递归实例，按照相互调用的关系可构成一个线性序列。在此序列中，各递归实例的秩反映了它们各自被创建的时间先后，每一递归实例的秩等于早于它出现的实例总数。反过来，通过r亦可唯一确定e = v(r)。这时向量特有的元素访问方式，称作“循秩访问”(call-by-rank)。



## 2.2 接口

### 2.2.1 ADT接口

适用对象为`向量`的：

* size()：报告向量当前的规模（元素总数）
* get(r)：获取秩为r的元素
* put(r, e)：用e替换秩为r元素的数值
* insert(r, e)：e作为秩为r元素插入，原后继元素依次后移
* remove(r)：删除秩为r的元素，返回该元素中原存放的对象
* disordered()：判断所有元素是否已按照非降序排列
* sort()：调整各元素的位置，使之按非降序排列
* find(e)：查找等于e且秩最大的元素
* deduplicate()：剔除重复元素
* traverse()：遍历向量并统一处理所有元素，处理方法由函数对象指定



适用对象为`有序向量`的：

* search(e)：查找目标元素e，返回不大于e且秩最大的元素
* uniquify()：剔除重复元素



### 2.2.2 操作实例

![image](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0200.jpg?raw=true)



### 2.2.3 Vector模板类

```c++
typedef int Rank; //秩
#define DEFAULT_CAPACITY 3 //默认的初始容量（实际应用中可设置为更大）

template <typename T> class Vector { //向量模板类
protected:
    Rank _size; int _capacity; T* _elem; //规模、容量、数据区
    void copyFrom ( T const* A, Rank lo, Rank hi ); //复制数组区间A[lo, hi)
    void expand(); //空间不足时扩容
    void shrink(); //装填因子过小时压缩
    bool bubble ( Rank lo, Rank hi ); //扫描交换
    void bubbleSort ( Rank lo, Rank hi ); //起泡排序算法
    Rank max ( Rank lo, Rank hi ); //选取最大元素
    void selectionSort ( Rank lo, Rank hi ); //选择排序算法
    void merge ( Rank lo, Rank mi, Rank hi ); //归并算法
    void mergeSort ( Rank lo, Rank hi ); //归并排序算法
    Rank partition ( Rank lo, Rank hi ); //轴点构造算法
    void quickSort ( Rank lo, Rank hi ); //快速排序算法
    void heapSort ( Rank lo, Rank hi ); //堆排序算法
public:
// 构造函数
    Vector ( int c = DEFAULT_CAPACITY, int s = 0, T v = 0 ) //容量为c、规模为s、所有元素初始为v
    { _elem = new T[_capacity = c]; for ( _size = 0; _size < s; _elem[_size++] = v ); } //s<=c
    Vector ( T const* A, Rank n ) { copyFrom ( A, 0, n ); } //数组整体复制
    Vector ( T const* A, Rank lo, Rank hi ) { copyFrom ( A, 0, n ); } //区间
    Vector ( Vector<T> const& V ) { copyFrom ( V._elem, 0, V._size ); } //向量整体复制
    Vector ( Vector<T> const& V, Rank lo, Rank hi ) { copyFrom ( V._elem, lo, hi ); } //区间
// 析构函数
    ~Vector() { delete [] _elem; } //释放内部空间
// 只读访问接口
    Rank size() const { return _size; } //规模
    bool empty() const { return !_size; } //判空
    int disordered() const; //判断向量是否已经排序
    Rank find ( T const& e ) const { return find ( e, 0, _size ); } //无序向量整体查找
    Rank find ( T const& e, Rank lo, Rank hi ) const; //无序向量区间查找
    Rank search ( T const& e ) const; //有序向量整体查找
    { return ( 0 >= _size ) ? -1 : search ( e, 0, _size ); }
    Rank search ( T const& e, Rank lo, Rank hi ) const; //有序向量区间查找
// 可写访问接口
    T& operator[] ( Rank r ) const; //重载下标操作符，可以类似于数组形式引用各元素
    Vector<T> & operator= ( Vector<T> const& ); //重载赋值操作符，以便直接克隆向量
    T remove ( Rank r ); //删除秩为r的元素
    int remove ( Rank lo, Rank hi ); //删除秩在区间[lo, hi)之间的元素
    Rank insert ( Rank r, T const& e); //插入元素
    Rank insert ( T const& e ) { return insert ( _size, e); } //默认作为末元素插入
    void sort ( Rank lo, Rank hi ); //对[lo, hi)排序
    void sort() { sort ( 0, _size ); } //整体排序
    void unsort ( Rank lo, Rank hi ); //对[lo, hi)置乱
    void unsort() { unsort ( 0, _size ); } //整体置乱
    int deduplicate(); //无序去重
    int uniquify(); //有序去重
// 遍历
    void traverse ( void (* ) ( T& ) ); //遍历（使用函数指针，只读或局部性修改）
    template <typename VST> void traverse ( VST& ); //遍历（使用函数对象，可全局性修改）
}; //Vector
```



## 2.3 构造与析构

在向量元素的秩、数组单元的逻辑编号以及物理地址之间，具有如下对应关系：向量中秩为r的元素，对应于内部数组中的\_elem[r]，其物理地址为\_elem + r。

因此，向量对象的构造与析构，将主要围绕这些私有变量和数据区的初始化与销毁展开。



### 2.3.1 默认构造方法

借助构造函数(constructor)做初始化(initialization)。由代码2.2.3可见，这里为向量重载了多个构造函数。

其中默认的构造方法是，首先根据创建者制定的初始容量，向系统申请空间，以创建内部私有数组\_elem[]；若容量未明确指定，则使用默认值DEFAULT_CAPACITY。接下来，鉴于初生的向量尚不包含任何元素，故将指示规模的变量_size初始化为0。



### 2.3.2 基于复制的构造方法

以某个已有的向量或数组为蓝本，进行局部或整体克隆。

```c++
template <typename T> //元素类型
void Vector<T>::copyFrom ( T const* A, Rank lo, Rank hi ) { //以数组区间A[lo,hi)为蓝本复制向量
    _elem = new T[_capacity = 2 * ( hi - lo ) ]; _size = 0; //分配空间，规模清零
    while ( lo < hi ) //A[lo, hi)内的元素逐一
        _elem[_size++] = A[lo++]; //复制至_elem[0, hi - lo)
}
```

需要强调的是，由于向量内部含有动态分配的空间，默认的运算符“=”不足与支持向量之间的直接赋值。例如，6.3节将以二维向量形式实现图邻接表，其主向量中的每一元素本身都是一堆向量，为适应此类赋值操作的需求，可重载向量赋值操作符：

```c++
template <typename T> Vector<T>& Vector<T>::operator= ( Vector<T> const& V ) { //重载
    if ( _elem ) delete [] _elem; //释放原有内容
	copyFrom ( V._elem, 0, V.size() ); //整体复制
    retur *this; //返回当前对象的引用，以便链式赋值
}
```



### 2.3.3 析构方法

不再需要的向量应借助析构函数(destructor)及时清理(cleanup)，以释放其占用的系统资源。与构造函数不同，同一对象只能有一个析构函数，不得重载。

向量的析构过程如2.2.3的方法~Vector()所示：只需释放用于存放元素的内部数组_elem[]，将其占用的空间交还操作系统。\_capacity和\_size之类的内部变量无需做任何处理，它们将作为向量对象自身的一部分被系统回收，此后既无需也无法被引用。

向量析构之前应该提前释放对应空间，出于简化的考虑，这里约定“谁申请谁释放”的原则，由上层调用者负责确定是否释放。



## 2.4 动态空间管理

### 2.4.1 静态空间管理

内部数组所占的物理空间的容量，若在向量的生命期内不允许调整，则称作静态空间管理策略。此方法容易导致上溢(overflow)。

向量实际规模与其内部数组容量的比值\_size/\_capacity亦称作装填因子(load factor)，是衡量空间利用率的重要指标。如何才能保证向量的装填因子既不至于超过1，也不至于太接近于0？



### 2.4.2 可扩充向量

如何实现扩容？新的容量取多少才算合适？

一种可行的方法如下图所示，申请一个容量更大的数组B[]，并将原数组中的成员复制到新数组中，然后插入新元素e，当然，原数组的空间需要释放并归还操作系统。

![image](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0201.jpg?raw=true)



### 2.4.3 扩容

基于以上策略的扩容算法expan()，可实现代码：

```c++
template <typename T> void Vector<T>::expand() { //向量空间不足时扩容
    if ( _size < _capacity ) return; //尚未满员时不必扩容
    if ( _capacity < DEFAULT_CAPACITY ) _capacity = DEFAULT_CAPACITY; //不低于最小容量
    T* oldElem = _elem; _elem = new T[_capacity <<= 1]; //容量加倍
    for ( int i = 0; i < _size; i++ )
        _elem[i] = oldElem[i]; //复制原向量内容
    delete [] oldElem; //释放原空间
}
```



### 2.4.4 分摊分析

* 分摊复杂度

  考查对可扩充向量的足够多次连续操作，并将其间所消耗的时间，分摊至所有的操作。如此分摊至单次操作的时间成本，称作分摊运行时间(amortized running time)。

  这一指标与平均运行时间(average running time)有本质的区别。后者是按照某种假定的概率分布，对各种情况下所需执行时间的加权平均，故亦称作期望运行时间(expected running time)。而前者要求，参与分摊的操作必须构成和来自一个真实可行的操作序列，而且该序列必须足够长。

  相对而言，分摊复杂度可以针对计算成本和效率做出更为客观而准确的估计。

* O(1)分摊时间

  ![image](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0202.jpg?raw=true)

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0203.jpg?raw=true)



### 2.4.5 缩容

装填因子低于某一阈值时，称数组发生了下溢(underflow)。在格外关注空间利用率的场合，下溢也有必要适当缩减内部数组容量。

```c++
template <typename T> void Vector<T>::shrink() { 
    if ( _capacity < DEFAULT_CAPACITY << 1 ) return; //不致收缩到DEFAULT_CAPACITY以下
    if ( _size << 2 > _capcacity ) return; //以25%为界
    T* oldElem = _elem; _elem = new T[_capacity >>= 1]; //容量减半
    for ( int i = 0; i < _size; i++ ) _elem[i] = oldElem[i]; //复制原向量内容
    delete [] oldElem; //释放原空间
}
```



## 2.5 常规向量

### 2.5.1 直接引用元素

对向量元素的访问可否沿用数组的方式呢？解决方法之一是重载操作符“[]”：

```c++
template <typename T> T& Vector<T>::operator[] ( Rank r ) const //重载下标操作符
{ return _elem[r]; } // assert: 0 <= r < _size
```



### 2.5.2 置乱器

* 置乱算法

  ```c++
  template <typename T> void permute ( Vector<T>& V ) { //随机置乱向量，使各元素等概率出现于各位置
      for ( int i = V.size(); i > 0; i-- ) //自后向前
          swap ( V[i - 1], V[rand() % i ] ); //V[i - 1]与V[0, i)中某一随机元素交换
  }
  ```

* 区间置乱接口

  为了便于对各种向量算法的测试与比较，这里不妨将以上permute()算法封装至向量ADT中，并对外提供向量的置乱操作接口Vector::unsort()。

  ``` C++
  template <typename T> void Vector<T>::unsort ( Rank lo, Rank hi ) {
      T* V = _elem + lo; //将子向量_elem[lo, hi)视作另一向量V[0, hi - lo)
      for ( Rank i = hi - lo; i > 0; i-- ) //自后向前
          swap ( V[i - 1], V[rand() % i] ); //将V[i - 1]与V[0, i)中某一元素随机交换
  }
  ```



### 2.5.3 判等器与比较器

`比对`和`比较`通常采用两种方法：一、将比对操作和比较操作分别封装成通用的判等器和比较器。二、在定义对应数据类型时，通过重载“<”和“==”之类的操作符，给出大小和相等关系的具体定义及其判别方法。

```c++
template <typename T> static bool lt ( T* a, T* b ) { return lt ( *a, *b ); } //less than
template <typename T> static bool lt ( T& a, T& b ) { return a < b; } //less than
template <typename T> static bool eq ( T* a, T* b ) { return eq ( *a, *b ); } //equal
template <typename T> static bool eq ( T& a, T& b ) { return a == b; } //equal 
```



### 2.5.4 无序查找

支持对比但未必支持比较的向量，称作无序向量(unsorted vector)。

```c++
template <typename T> //无序向量的顺序查找：返回最后一个元素e的位置；失败时返回lo - 1
Rank Vector<T>::find ( T const& e, Rank lo, Rank hi ) const { //assert: 0 <= lo < hi <= _size
    while ( ( lo < hi-- ) && ( e != _elem[hi] ) ); //从后向前，顺序查找
    return hi; //若hi < lo，则意味着失败；否则hi即命中元素的秩
}
```

* 复杂度

  最坏情况下，查找终止于首元素_elem[lo]，运行时间为O(hi - lo) = O(n)。最好情况下，查找命中于末元素\_elem[hi - 1]，仅需O(1)时间。对于规模相同、内部组成不同的输入，渐进运行时间却有本质区别，故此类算法也称作输入敏感的(input sensitive)算法。



### 2.5.5 插入

* 实现

  ```c++
  template <typename T> //将e作为秩为r元素插入
  Rank Vector<T>::insert ( Rank r, T const& e ) { //assert: 0 <= r <= size
      expand(); //若有必要，扩容
      for ( int i = _size; i > r; i-- ) _elem[i] = _elem[i-1]; //自后向前，后继元素依此后移一个单元
      _elem[r] = e; _size++; //置入新元素并更新容量
      return r; //返回秩
  } 
  ```

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0204.jpg?raw=true)

* 复杂度

  时间主要消耗于后继元素的后移，线性正比于后缀的长度，总体为O(_size - r + 1)。



### 2.5.6 删除

* 区间删除：remove(lo, hi)

  ```c++
  template <typename T> int Vector<T>::remove ( Rank lo, Rank hi ) {
      if ( lo == hi ) return 0; //单独处理退化情况，比如remove(0, 0)
      while ( hi < _size ) _elem[lo++] = _elem[hi++]; //[hi, _size)顺次前移hi - lo个单元
      _size = ho; //更新规模，直接丢弃尾部[lo, _size = hi)区间
      shrink(); //若有必要，则缩容
      return hi - lo; //返回被删除元素的数目
  }
  ```

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0205.jpg?raw=true)

* 单元素删除remove(r)

  ``` c++
  template <typename T> T Vector<T>::remove ( Rank r ) { 
      T e = _elem[r]; //备份被删除元素
      remove ( r, r + 1 ); //调用区间删除算法，等效于对区间[r, r + 1)的删除
      return e; //返回被删除元素
  }
  ```

* 复杂度

  remove(lo, hi)的计算成本，主要消耗于后续元素的前移，线性正比于后缀的长度，总体不过O(m + 1) = O(_size - hi + 1)。



### 2.5.7 唯一化

* 实现

  ```c++
  template <typename T> int Vector<T>::deduplicate() { //删除无序向量中重复元素（高效版）
      int oldSize = _size; //记录原规模
      Rank i = 1; //从_elem[1]开始
      while  ( i < _size ) //自前向后逐一考查各元素_elem[i]
          ( find ( _elem[i], 0, i ) < 0 ) ? //在其前缀中寻找与之雷同者（至多一个）
          i++ : remove ( i ); //若无雷同则继续考查其后继，否则删除雷同者
      return oldSize - _size; //向量规模变化量，即被删除元素总数
  }
  ```

* 复杂度

  这里所需的时间主要消耗于find()和remove()两个接口。前一部分的时间应线性正比于查找区间的宽度，即前驱的总数；后一部分时间应线性正比于后继的总数。因此，每步迭代所需时间为O(n)，总体复杂度应为O(n^2)

  

### 2.5.8 遍历

* 实现

  ```c++
  template <typename T> void Vector<T>::traverse( void ( *visit ) ( T& ) ) //借助函数指针机制
  { for ( int i = 0; i < _size; i++ ) visit ( _elem[i]); } //遍历向量
  
  template <typename T> template <typename VST> //元素类型、操作器
  void Vector<T>::traverse( VST& visit ) //借助函数对象机制
  { for ( int i = 0; i < _size; i++ ) visit ( _elem[i] ); } //遍历向量
  ```

  第一种方式借助函数指针*visit()指定某一函数，该函数只有一个参数，其类型为对向量元素的引用，故通过该函数即可直接访问或修改向量元素。另外，也可以函数对象的形式，指定具体的遍历操作。这类对象的操作符“（）”经重载之后，在形式上等效于一个函数接口，故因此得名。

  第二种功能更强，适用范围更广。比如，函数对象的形式支持对向量元素的关联修改。

* 实例

  ```c++
  template <typename T> struct Increase //函数对象：递增一个T类对象
  { virtual void operator() ( T& e ) { e++; } }; //假设T可直接递增或已重载++
  
  template <typename T> void increase ( Vector<T> & V ) //统一递增向量中的各元素
  { V.traverse( Increase<T>() ); } //以Increase<T>()为基本操作进行遍历
  ```

* 复杂度

  遍历所需时间线性正比于所统一指定的基本操作所需的时间，比如上例中Increase<T>()仅需常数时间，故这一遍历的总体时间复杂度为O(n)。

  

## 2.6 有序向量

若向量S[0, n)中的所有元素不仅按线性次序存放，而且其数值大小也按此次序单调分布，则称作有序向量(sorted vector)。与通常的向量一样，有序向量依然不要求元素互异，故通常约定其中的元素自前向后构成一个非降序列。



### 2.6.1 比较器

有序向量支持判等，也支持比较大小。这一条件构成了“次序”的概念基础。



### 2.6.2 有序性甄别

```c++
template <typename T> int Vector<T>::disordered() const { //返回向量中逆序相邻元素对的总数
    int n = 0; //计数器
    for ( int i = 1; i < _size; i++ ) 
        if ( _elem[i-1] > _elem[i] ) n++;
    return n;
}
```



### 2.6.3 唯一化

* 低效版

  ```c++
  //借助有序向量中重复元素必然前后紧邻的事实
  template <typename T> int Vecotr<T>::uniquify() {
      int oldSize= _size; int i = 1; //当前对比元素的秩，起始于首元素
      while ( i < _size ) //从前向后，逐一对比各相邻元素
          _elem[i - 1] == _elem[i] ? remove ( i ) : i++; //若雷同则删除后者，否则转至下一元素
      return oldSize - _size; //向量规模变化量
  }
  ```

  运行时间主要消耗于while循环，共需迭代_size - 1 = n - 1步，此外在最坏的情况下，每次循环都需执行一次remove()操作，因此最坏情况的时间总量：

  (n - 2) + (n - 3) + ... + 2 + 1 = O(n^2)

* 改进思路

  可以区间为单位成批删除重复元素，并将后继元素（若存在）统一地大跨度前移。

* 高效版

  ```c++
  template <typename T> int Vector<T>::uniquify() { 
      Rank i = 0, j = 0; //各对互异“相邻”元素的秩
      while ( ++j < _size ) //逐一扫描直至末元素
          if ( _elem[i] != _elem[j] ) //跳过雷同者
              _elem[++i] = _elem[j]; //发现不同元素时，向前移至紧邻于前者右侧
      _size = ++i; shrink(); //直接截除尾部多余元素
      return j - i; //向量规模变化量，即被删除元素总数
  }
  ```

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0206.jpg?raw=true)

* 复杂度

  while循环的每一步仅需对元素做一次比较，向后移动一到两个位置指针，以及至多向前复制一个元素，故只需常数时间。while循环共迭代向量规模n的次数，故时间复杂度为O(n)。



### 2.6.4 查找

```c++
template <typename T> //在有序向量的区间[lo, hi)内，确定不大于e的最后一个节点的秩
Rank Vector<T>::search ( T const& e, Rank lo, Rank hi ) const { //assert: 0 <= lo < hi <= _size
    return ( rand() % 2 ) ? //按各50%的概率随机使用二分查找或Fibonacci查找
        binSearch ( _elem, e, lo, hi ) : fibSearch ( _elem, e, lo, hi );
}
```



### 2.6.5 二分查找（版本A）

* 减而治之

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0207.jpg?raw=true)

* 实现

  ```c++
  template <typename T> static Rank binSearch ( T* A, T const& e, Rank lo, Rank hi ) {
      while ( lo < hi ) { //每步迭代可能要做两次比较判断，有三个分支
          Rank mi = ( lo + hi ) >> 1; //以中点为轴点
          if ( e < A[mi] ) hi = mi; //深入前半段[lo, mi)继续查找
          else if ( A[mi] < e ) lo =mi + 1; //深入后半段(mi, hi)继续查找
          else return mi; //在mi处命中
      } //成功查找可以提前终止
      return -1; //查找失败
  } //有多个命中元素时，不能保证返回秩最大者；查找失败时，简单地返回-1，而不能指示失败的位置
  ```

* 实例

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0208.jpg?raw=true)

* 复杂度

  随着迭代不断深入，有效的查找区间宽度将按1/2的比例以几何级数的速度递减。于是经过至多log2(hi - lo)步迭代后，算法必然终止。鉴于每步迭代仅需常数时间，故总体时间复杂度不超过：

  O(log2(hi - lo)) = O(logn)

* 查找长度

  向量元素类型更为复杂，故查找未必能在常数时间内完成。因此就时间复杂度的常系数而言，元素（即无符号整数）的查找的权重远远高于向量元素，而查找算法的整体效率也更主要地取决于其中所执行的元素大小比较操作的次数，即所谓查找长度（search length）。

  通常，可针对查找成功或失败等情况，从最好、最坏和平均情况等角度，分别测算查找长度，并凭此对查找算法的总体性能做一评估。

* 成功查找长度

  对于长度为n的有序向量，共有n种可能的成功查找，分别对应于某一元素。实际上，每一种成功查找所对应的查找长度，仅取决于n以及目标元素所对应的秩，而与元素的具体数值无关。

  平均查找长度O(1.5k) = O(1.5·log2(n)) （推导见书P51）

* 失败查找长度

  O(1.5k) = O(1.5·log2(n)) （推导见书P51）

* 不足

  以成功查找为例，即便是迭代次数相同的情况，对应的查找长度也不尽相等。究其根源在于每一步迭代中确定左右分支方向，分别需要做1次或2次元素比较，从而造成不同情况所对应查找长度的不均衡。



### 2.6.6 Fibonacci查找

* 递推方程

  根据2.6.5的查找长度递推方程可知解决二分查找不足的思路有两种：

  其一，调整前后区域的宽度

  其二，统一沿两个方向深入所需要执行的比较次数

* 黄金分割

  减而治之本身并不要求子向量切分点mi必须居中，故按上述改进思路，不妨设向量长度为fib(k) - 1。

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0209.jpg?raw=true)

  无论朝哪个方向深入，新向量的长度从形式上都依然是某个Fibonacci数减一。

* 实现

  ```c++
  #include "..\fibonacci\Fib.h" //引入Fib数列类
  template <typename T> static Rank fibSearch ( T* A, T const& e, Rank lo, Rank hi ) {
      Fib fib ( hi - lo ); //用O(log_phi(n = hi - lo))时间创建Fib数列
      while ( lo < hi ) { //每步迭代可能要做两次比较判断，有三个分支
          while ( hi - lo < fib.get() ) fib.prev(); //通过向前顺序查找（分摊O(1)）——至多迭代几次？
          Rank mi = lo + fib.get() - 1; //确定形如Fib(k) - 1的轴点
          if ( e < A[mi] ) hi = mi; //深入前半段[lo, mi)继续查找
          else if ( A[mi] < e ) lo = mi + 1; //深入后半段(mi, hi)继续查找
          else return mi; //在mi处命中
      } //成功查找可以提前终止
      return -1; //查找失败
  } //有多个命中元素时，不能保证返回秩最大者；失败时，简单地返回-1而不能指示失败的位置
  ```

  这一实现可用于任意长度的向量，只需要在进入循环之前调用构造器Fib(n = hi - lo)，将初始长度设置为“不小于n的最小Fibonacci项”。这一步所需花费的时间并不影响算法整体的渐进复杂度。

  平均查找长度O(1.44·log2(n))



### 2.6.7 二分查找（版本B）

在每个切分点A[mi]只做一次比较，具体地，若目标元素小于A[mi]，则深入前端子向量A[lo, mi)继续查找；否则，深入后端子向量A[mi, hi)继续查找。

* 实现

  ```c++
  template <typename T> static Rank binSearch ( T* A, T const& e, Rank lo, Rank hi ) {
     while ( 1 < hi - lo ) { //每步迭代仅需做一次比较判断，有两个分支；成功查找不能提前终止
         Rank mi = ( lo + hi ) >> 1; //以中点为轴点
         ( e < A[mi] ) ? hi = mi : lo = mi; //经比较后确定深入[lo, mi)或[mi, hi)
     } //出口时hi = lo + 1，查找区间仅含一个元素A[lo]
     return ( e == A[lo] ) ? lo : -1; //查找成功时返回对应的秩；否则统一返回-1
  } //有多个命中元素时，不能保证返回秩最大者；查找失败时，简单地返回-1而不能指示失败的位置
  ```

* 进一步的要求

  同时多个元素命中时，返回其中最靠后者。查找失败时，返回不大于（小于）e的最后（前）一个元素，以便将e作为后继（前驱）插入向量。



### 2.6.8 二分查找（版本C）

* 实现

  ```c++
  template <typename T> static Rank binSearch ( T* A, T const& e, Rank lo, Rank hi ) {
      while ( lo < hi ) { //每步迭代仅需做一次比较判断，有两个分支
          Rank mi = ( lo + hi ) >> 1; //以中点为轴点
          ( e < A[mi] ) ? hi = mi : lo = mi + 1; //经比较厚确定深入[lo, mi)或(mi, hi)
      } //成功查找不能提前终止
      return --lo; //循环结束时，lo为大于e的元素的最小秩，故lo-1为不大于e元素的最大秩
  } //有多个迷宫中元素时，总能保证返回秩最大者；查找失败时，能够返回失败的位置
  ```

* 正确性

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0210.jpg?raw=true)



## 2.7 排序与下界

### 2.7.2 排序及其分类

根据处理数据的规模与存储的特点不同，可分为`内部排序算法`和`外部排序算法`，前者处理的数据规模相对不大，内存足以容纳；后者处理的数据规模很大，必须将借助外部甚至分布式存储器，在排序计算过程的任意时刻，内存中只能容纳其中一小部分数据。

根据输入形式不同，可分为`离线算法`和`在线算法`，前者待排序的数据以批处理的形式整体给出；而在网络计算之类的环境中，待排序的数据通常需要实时生成，在排序算法启动后数据才陆续到达。

根据所依赖的体系结构不同，可分为`串行`和`并行`两大排序算法。

根据排序算法是否采用随机策略，可分为`确定式`和`随机式`。

本书的讨论范围主要集中于确定式串行脱机的内部排序算法。



### 2.7.3 下界

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0211.jpg?raw=true)

任一问题在最坏情况下的最低计算成本即为该问题的复杂度下界(lower bound)。以下结合比较树模型，介绍界定问题复杂度下界的一种重要方法。



### 2.7.4 比较树

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0212.jpg?raw=true)

根节点：算法入口的起始状态（三个苹果）

内部节点：过程中某步计算

叶节点：一系列计算后某次运行的终止状态（最下方三种结果）

* 比较树(comparison tree)

  算法所有可能的执行过程，都涵盖于这一树形结构中。具体地，该树具有以下性质：

  1. 每一内部节点各对应于一次比对操作
  2. 内部节点的左右分支，分别对应于两种比对结果下的执行方向
  3. 根到叶节点的路径对应于算法某次执行的完整过程及输出
  4. 反过来，算法的每一运行过程都对应于从根到某一叶节点的路径

  可如此描述的算法，都称作基于比较式算法(comparison-based algorithm)，简称CBA算法。本书中除了散列之外的算法大多属于此类。



### 2.7.5 估计下界

* 最小树高

  考查任一CBA式算法A，设CT(A)为与之对应的一棵比较树。

  算法A每一次运行所需的时间，将取决于其对应叶节点到根节点的距离（叶节点的深度）；

  算法A在最坏情况下的运行时间，将取决于比较树中所有叶节点的最大深度（称作该树的高度，记作h(CT(A))）。

  因此就渐进的意义而言，算法A的时间复杂度不应低于Ω(h(CT(A)))。

  如何估计树的最小高度？只需考查树中所含叶节点（可能的输出结果）的数目。具体地，在一棵高度为h的二叉树中，叶节点的数目不可能多于2^h，反过来，若某一个问题的输出结果不少于n种，则比较树中的叶节点不可能少于n个，则树高h不可能低于log2(n)。



## 2.8 排序器

### 2.8.1 统一入口

```c++
template <typename T> void Vector<T>::sort ( Rank lo, Rank hi ) { //向量区间[lo, hi)排序
    switch ( rand() % 5 ) { //随机选取排序算法，可根据具体问题的特点灵活选取或扩充
        case 1: bubbleSort ( lo, hi ); break; //起泡排序
        case 2: selectionSort ( lo, hi ); break; //选择排序
        case 3: mergeSort ( lo, hi ); break; //归并排序
        case 4: heapSort ( lo, hi ); break; //堆排序
        default: quickSort ( lo, hi ); break; //快速排序
    }
    
}
```



### 2.8.2 起泡排序

* 起泡排序

  原理已在1.1.3讲解，此处只需集成到向量ADT中

  ```c++
  template <typename T> //向量的起泡排序
  void Vector<T>::bubbleSort ( Rank lo, Rank hi ) //assert: 0 <= lo < hi <= size
  { while ( !bubble ( lo, hi-- ) ); } //逐趟做扫描交换直至全序
  ```

* 扫描交换

  单趟扫描交换算法如下

  ```c++
  template <typename T> bool Vector<T>::bubble ( Rank lo, Rank hi ) {
      bool sorted = true; //整体有序
      while ( ++lo < hi ) //自左向右逐一检查相邻元素
          if ( _elem[lo - 1] > _elem[lo] ) { //若逆序，则
              sorted = false; //意味着尚未整体有序，并需要
              swap ( _elem[lo - 1], _elem[lo] ); //通过交换使局部有序
          }
      return sorted; //返回有序标志
  }
  ```

* 再改进

  当遇到左侧少量元素乱序，右侧大量元素有序的一组向量时，上方扫描交换的算法效率不高，可改进如下：

  ```c++
template <typename T> void Vector<T>::bubbleSort(Rank lo, Rank hi)
  { while (lo < (hi = bubble(lo, hi))); } //逐趟扫描交换，直至全序
  
  template <typename T> Rank Vector<T>::bubble(Rank lo, Rank hi) {
      Rank last = lo; //最右侧的逆序对初始化为[lo - 1, lo]
      while (++lo < hi) //自左向右，逐一检查各对相邻元素
          if (_elem[lo - 1] > _elem[lo]) { //若逆序，则
              last = lo; //更新最右侧逆序对位置记录，并
              swap(_elem[lo - 1], _elem[lo]); //交换
          }
      return last; //返回最右侧的逆序对位置
  } //前一版本中的逻辑型标志sorted改为秩last
  ```
  
* 重复元素与稳定性

  稳定算法(stable algorithm)的特征是，重复元素之间的相对次序在排序前后保持一致。不具有这一特征的排序算法都是不稳定算法(unstable algorithm)。

  比如，起泡排序过程中元素的相对位置有所调整的唯一可能是，某元素_elem[i - 1]严格大于其后继\_elem[i]。也就是说，重复元素虽然可能靠拢，但不会相互跨越，这就是稳定算法。

  稳定的排序算法，可用以实现同时对多个关键码按照字典顺序的排序。



### 2.8.3 归并排序

* 有序向量的二路归并

  二路归并(2-way merge)，是将两个有序序列合并为一个有序序列。这里的序列既可以是向量也可以是列表，归并所需的时间取决于各趟二路归并所需时间的总和。

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0213.jpg?raw=true)

  二路归并在任何时刻只需载入两个向量的首元素，除了归并输出的向量外，仅需要常数规模的辅助空间。另外，该算法严格按照顺序处理输入和输出向量，故特别适用于使用磁带机等顺序存储器的场合。

* 分治

  归并排序的主体结构是典型的分治：

  ```c++
  template <typename T> //向量归并排序
  void Vector<T>::mergeSort ( Rank lo, Rank hi ) { //0 <= lo < hi <= size
      if ( hi - lo < 2 ) return; //单元素区间自然酉戌，否则...
      int mi = ( lo + hi ) / 2; //以中点为界
      mergeSort ( lo, mi ); mergeSort ( mi, hi ); //分别排序
      merge ( lo, mi, hi ); //归并
  }
  ```

  注意这里的递归终止条件是当前向量长度：n = hi -  lo = 1

* 实例

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0214.jpg?raw=true)

* 二路归并接口的实现

  ```c++
  template <typename T> //有序向量的归并
  void Vector<T>::merge ( Rank lo, Rank mi, Rank hi ) { //各自有序的子向量[lo, mi)和[mi, hi)
      T* A = _elem + lo; //合并后的向量A[0, hi - lo) = _elem[lo, hi)
      int lb = mi - lo; T* B = new T[lb]; //前子向量B[0, lb) = _elem[lo, mi)
      for ( Rank i = 0; i < lb; B[i] = A[i++] ); //复制前子向量
      int lc = hi - mi; T* C = _elem + mi; //后子向量c[0, lc) = _elem[mi, hi)
      for ( Rank i = 0, j = 0, k = 0; ( j < lb ) || ( k < lc ); ) { //B[j]和C[k]中的小者续至A末尾
      	if ( ( j < lb ) && ( ! ( k < lc ) || ( B[j] <= C[k] ) ) ) A[i++] = B[j++]; //j不超过lb的范围，同时k超过lc的范围或B[j]不大于C[k]，可假设k超出lc的范围表示C[k]为正无穷的哨兵节点
          if ( ( k < lc ) && ( ! ( j < lb ) || ( C[k] < B[j] ) ) ) A[i++] = C[k++]; //如上，同理
      } 
      delete [] B; //
  } //归并后得到完整的有序向量[lo, hi)
  
  //删除冗余算法语句可见视频02F-5，虽然从渐进角度而言没有实质的提高
  ```

* 归并时间

  merge()的渐进时间成本取决于循环迭代的总次数。每经过一次迭代，B[j]和C[k]之间的小者都会被移出并接至A的末尾，这意味着每经过一次迭代，总和s = j + k都会加一。初始时，有s = 0 + 0 = 0；迭代期间：

  s < lb + lc = (mi - lo) + (hi - mi) = hi - lo

  因此迭代次数及所需时间均不超过O(hi - mi) = O(n)。反之，按照算法的流程控制逻辑，只有s = hi - lo时迭代才能终止，所以该算法在最好情况下仍需Ω(n)的时间。

* 排序时间

  将归并排序算法处理长度为n的向量所需的时间记作T(n)。为对长度为n的向量归并排序，需递归地对长度各为n/2的两个子向量做归并排序，再花线性时间做一次二路归并。如此，可得以下递推关系：

  T(n) = 2 X T(n/2) + O(n)

  当子向量长度缩短到1时，递归即可终止并直接返回该向量。故有边界条件

  T(1) = O(1)

  联立以上递推式，可以解得：

  T(n = O(nlogn)

  `*`利用图2.19也可知，既然每次二路归并只需线性时间，故同层所有二路归并累计也只需线性时间（前者指线性正比于一对待归并子向量长度之和，后者指线性正比于所有参与归并的子向量长度之和）。由图不难看出，原向量中每个元素在同一层次恰好出现一次，故同层递归实例所消耗时间之和应为Θ(n)。另外，递归实例规模以2为倍数按几何级数逐层变化，故共有Θ(log2(n))层，共计Θ(nlogn)时间。
