[toc]

# 第4章：栈与队列

栈与队列，与此前介绍的向量和列表一样，它们也属于线性序列结构，故其中存放的数据对象之间有具有线性次序。相对于一般的序列结构，栈与队列的数据操作范围仅限于逻辑上的特定某端。

在信息处理领域，栈与队列的身影随处可见。许多程序语言本身就是建立于栈结构之上，无论PostScript或者Java，其实时运行环境都是基于栈结构的虚拟机。再如，网络浏览器多会将用户最近访问过的地址组织为一个栈。这样，用户每访问一个新页面，其地址就会被存放至栈顶；而用户每按下一次“后退”按钮，即可沿相反的次序返回此前刚访问过的页面。



## 4.1 栈

### 4.1.1 ADT接口

* 入栈与出栈

  栈（stack）是存放数据对象的一种特殊容器，其中的数据元素按线性的逻辑次序排列，故也可定义首、末元素。不过，尽管栈结构也支持对象的插入和删除操作，但其操作的范围仅限于栈的某一特定端。也就是说，若约定新的元素只能从某一端插入其中，则反过来也只能从这一端删除已有的元素。禁止操作的另一端，称作盲端。

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0400.jpg?raw=true)

* 后进先出

  栈中元素接受操作的次序必然始终遵循所谓“后进先出”（last-in-first-out, LIFO）的规律：从栈结构的整个生命期来看，更晚（早）出栈的元素，应为更早（晚）入栈者；反之，更晚（早）入栈者应更早（晚）出栈。



### 4.1.2 操作实例

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0401.jpg?raw=true)



### 4.1.3 模板类

```c++
#include "../Vector/Vector.h" //以向量为基类，派生出栈模板类
template <typename T> class Stack: public Vector<T> { //将向量的首/末端作为栈底/顶
public: //size()、empty()以及其他开放接口，均可直接沿用
    void push ( T const& e ) { insert ( size(), e ); } //入栈：等效于将新元素作为向量的末元素插入
    T pop() { return remove ( size() - 1 ); } //出栈：等效于删除向量的末元素
    T& top() { return ( *this ) [size() - 1]; } //取顶：直接返回向量的末元素
};
```



## 4.2 栈与递归

递归算法所需的空间量，主要决定于最大递归深度。在达到这一深度的时刻，同时活跃的递归实例达到最多。那么，操作系统具体是如何实现函数（递归）调用的？如何记录调用与被调用函数（递归）实例之间的关系？如何实现函数（递归）调用的返回？又是如何维护同时活跃的所有函数（递归）实例的？所有这些问题的答案，都可归结于栈。

### 4.2.1 函数调用栈

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0402.jpg?raw=true)

在Windows等大部分操作系统中，每个运行中的二进制程序都配有一个调用栈（call stack）或执行栈（execution stack）。借助调用栈可以跟踪属于同一程序的所有函数，记录它们之间的相互调用关系，并保证在每一调用实例执行完毕之后，可以准确地返回。

* 函数调用

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0403.jpg?raw=true)

* 递归

  图4.3中，对应于funcB()的自我调用，也会新压入一帧。可见，同一函数可能同时拥有多个实例，并在调用栈中各自占有一帧。这些帧的结构完全相同，但其中同名的参数或变量，都是独立的副本。比如在funcB()的两个实例中，入口参数m和内部变量i都各有一个副本。



### 4.2.2 避免递归

系统在后台隐式地维护调用栈的过程中，难以区分哪些参数和变量是对计算过程有实质作用的，更无法以通用的方式对它们进行优化，因此不得不将描述调用现场的所有参数和变量悉数入栈。再加上每一帧都必须保存的执行返回地址以及前一帧起始位置，往往导致程序的空间效率不高甚至更低：同时，隐式的入栈和出栈操作也会令实际的运行时间增加不少。因此在追求更高效率的场合，应尽可能地避免递归，尤其是过度的递归。



# 4.3 栈的典型应用

### 4.3.1 逆序输出

在栈所擅长解决的典型问题中，有一类具有以下共同特征：首先，虽有明确的算法，但其解答却以线性序列的形式给出；其次，无论是递归还是迭代实现，该序列都是依逆序计算输出的；最后，输入和输出规模不确定，难以事先确定盛放输出数据的容器大小。因其特有的“后进先出”特性及其在容量方面的自适应性，使用栈来解决此类问题可谓恰到好处。

* 进制转换

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0404.jpg?raw=true)

* 递归实现

  ```c++
  void convert ( Stack<char>& S, __int64 n, int base ) { //十进制正整数n到base进制的转换（递归版）
      static char digit[] //0 < n, 1 < base <= 16, 新进制下的数位符号，可视base取值范围适当扩充
      = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F'}; 
      if ( 0 < n ) { //在尚有余数之前，反复地
          S.push ( digit[n % base] ); //逆向记录当前最低位，在
          convert ( S, n / base, base ); //通过递归得到所有更高位
      }
  } //新进制下由高到低的各数位，自顶而下保存于栈S中
  ```

* 迭代实现

  这里的静态数位符号表在全局只需保留一份，但与一般的递归函数一样，该函数在递归调用栈中的每一帧都仍需记录参数S、n和base。将它们改为全局变量固然可以节省这部分空间，但依然不能彻底避免因调用栈操作而导致的空间和时间消耗。为此，不妨考虑改写为如下所示的迭代版本，既能充分发挥栈处理此类问题的特长，又可将空间消耗降低至O(1)：

  ```c++
  void convert ( Stack<char>& S, __int64 n, int base ) { //十进制正整数n到base进制的转换（迭代版）
      static char digit[] //0 < n, 1 < base <= 16, 新进制下的数位符号，可视base取值范围适当扩充
      = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F'}; 
      while ( n > 0 ) { //由低到高，逐一计算出新进制下的各数位
          int remainder = ( int ) ( n % base ); S.push ( digit[remainder] ); //余数（当前位）入栈
          n /= base; //n更新为其对base的除商
      }
  } //新进制下由高到低的各数位，自顶而下保存于栈S中
  ```



### 4.3.2 递归嵌套

* 栈混洗

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0405.jpg?raw=true)

  从图4.5中也可以看出，一般对于长度为n的输入序列，每一栈混洗都对应于由栈S的n次push和n次pop构成的某一合法操作序列，比如[3, 2, 4, 1>即对应于操作序列：
  
  {push, push, push, pop, pop, push, pop, pop}
  
  反之由n次push和n次pop构成的任何操作序列，只要满足“任一前缀中的push不少于pop”这一限制，则该序列也必然对应于某个栈混洗。
  
* 括号匹配的递归实现

  先只考虑圆括号，用'+'表示表达式的串接。一般地，若表达式S可分解为如下形式：

  S = S0 + "(" + S1 + ")" + S2 + S3

  其中S0和S3不含括号，且S1中左、右括号数相等，则S匹配当且仅当S1和S2均匹配。

  按照这一理解，可采用分治策略设计算法如下：将表达式划分为子表达式S0、S1和S2，分别递归地判断S1和S2是否匹配。这一构思可具体实现如代码4.4所示：

```c++
void trim ( const char exp[], int& lo, int& hi ) { //删除exp[lo, hi]不含括号的最长前缀、后缀
    while ( ( lo <= hi ) && ( exp[lo] != '(' ) && ( exp[lo] != ')' ) ) lo++; //查找第一个和
    while ( ( lo <= hi ) && ( exp[lo] != '(' ) && ( exp[lo] != ')' ) ) hi--;//最后一个括号
}

int divide ( const char exp[], int lo, int hi ) { //切分exp[lo, hi]，使exp匹配仅当子表达式匹配
    int mi = lo; int crc = 1; //crc为[lo, mi]范围内左、右括号数目之差
    while ( ( 0 < crc ) && ( ++mi < hi ) ) //逐个检查各字符，直到左、右括号数目相等，或者越界
    	{ if ( exp[mi] == ')' ) crc--; if ( exp[mi] == '(' ) crc++; } //左、右括号分别计数
    return mi; //若mi <= hi，则为合法切分点；否则意味着局部不可能匹配
}

bool paren ( const char exp[], int lo, int hi ) { //检查表达式exp[lo, hi]是否括号匹配（递归版）
    trim ( exp, lo, hi ); if ( lo > hi ) return true; //清除不含括号的前缀、后缀
    if ( exp[lo] != '(' ) return false; //首字符非左括号，则必不匹配
    if ( exp[hi] != ')' ) return false; //末字符非右括号，则必不匹配
    int mi = divide ( exp, lo, hi ); //确定适当的切分点
    if ( mi > hi ) return false; //切分点不合法，意味着局部以至整体不匹配
    return paren ( exp, lo + 1, mi - 1 ) && paren ( exp, mi + 1, hi ); //分别检查左、右子表达式
}
```

其中，trim()函数用于截除表达式中不含括号的头部和尾部，即前缀S0和后缀S3。divide()函数对表达式做线性扫描，并动态地记录已经扫描的左、右括号数目之差。如此，当已扫过同样多的左右括号时，即确定了一个合适的切分点mi，并得到子表达式S1=exp(lo, mi)和S2=exp(mi, hi]。以下，经递归地检查S1和S2，即可判断原表达式是否匹配。

在最坏情况下divide()需要线性时间，且递归深度为O(n)，故以上算法共需O(n^2)时间。此外，该方法也难以处理含有多种括号表达式，故有必要进一步优化。

* 迭代实现

  实际上，只要将push、pop操作分别与左、右括号相对应，则长度为n的栈混洗，必然与由n对括号组成的合法表达式彼此对应。按照这一理解，借助栈结构，只需扫描一趟表达式，即可在线性时间内判定其中的括号是否匹配。

  ```c++
  bool paren ( const char exp[], int lo, int hi ) { //表达式括号匹配检查，可兼顾三种括号
      Stack<char> S; //使用栈记录已发现但尚未匹配的左括号
      for ( int i = lo; i <= hi; i++ ) /* 逐一检查当前字符 */
          switch ( exp[i] ) { //左括号直接进栈；右括号若与栈顶失配，则表达式必不匹配
              case '(': case '[': case '{': S.push ( exp[i] ); break;
              case ')': if ( ( S.empty() ) || ( '(' != S.pop() ) ) return false; break;   
              case ']': if ( ( S.empty() ) || ( '[' != S.pop() ) ) return false; break;  
              case '}': if ( ( S.empty() ) || ( '{' != S.pop() ) ) return false; break;  
              default: break;//非括号字符一律忽略
          }
      return S.empty(); //整个表达式扫描过后，栈中若仍残留（左）括号，则不匹配；否则（栈空）匹配
  }
  ```

  ![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0406.jpg?raw=true)

  

### 4.3.3 延迟缓冲

在一些应用问题中，输入可分解为多个单元并通过迭代依次扫描处理，但过程中的各步计算往往滞后于扫描的进度，需要待到必要的信息已完整到一定程度之后，才能作出判断并实施计算。在这类场合，栈结构则可扮演数据缓冲区的角色。

* 优先级表

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0407.jpg?raw=true)

* 求值算法

```c++
float evaluate ( char* S, char*& RPN ) { //对（已剔除白空格的）表达式S求值，并转换为逆波兰RPN
    Stack<float> opnd; Stack<char> optr; //运算数栈、运算符栈
    optr.push( '\0' ); //尾哨兵'\0'也作为头哨兵首先入栈
    while ( !optr.empty() ) { //在运算符栈非空之前，逐个处理表达式中各字符
        if ( isdigit ( *S ) ) { //若当前字符为操作数，则
            readNumber ( S, opnd ); append ( RPN, opnd.top() ); //读入操作数，并将其接至RPN末尾
        } else //若当前字符为运算符，则
            switch ( orderBetween ( optr.top(), *S ) ) { //视其与栈顶运算符之间优先级高低分别处理
                case '<': //栈顶运算符优先级更低时
                    optr.push( *S ); S++; //计算推迟，当前运算符进栈
                    break;
                case '=': //优先级相等
                    optr.pop(); S++; //脱括号并接收下一个字符
                    break;
                case '>': { //栈顶运算符优先级更高时，可实施相应的计算，并将结果重新入栈
                    char op = optr.pop(); append ( RPN, op ); //栈顶运算符出栈并续接至RPN末尾
                    if ( '!' == op ) { //若属于一元运算符
                        float pOpnd = opnd.pop(); //只需取出一个操作数，并
                        opnd.push ( calcu ( op, pOpnd ) );//实施一元计算，结果入栈
                    } else { //对于其他（二元）运算符
                        float pOpnd2 = opnd.pop(), pOpnd1 = opnd.pop();//取出后、前操作数
                        opnd.push ( calcu ( pOpnd1, op, pOpnd2 ) ); //实施二元计算，结果入栈
                    }
                    break;
                }
                default : exit ( -1 ); //逢语法错误，不做处理直接退出
            }
    }
    return opnd.pop(); //弹出并返回最后的计算结果
}
```

该算法自左向右扫描表达式，并对其中字符逐一做相应的处理。那些已经扫描过但（因信息不足）尚不能处理的操作数与运算符，将分别缓冲至栈opnd和栈optr。一旦判定已缓存的子表达式优先级足够高，便弹出相关的操作数和运算符，随即执行运算，并将结果压入栈opnd。

请留意这里区分操作数和运算符的技巧。一旦当前字符由非数字转为数字，则意味着开始进入一个对应于操作数的子串范围。由于这里允许操作数含有多个数位，甚至可能是小数，故可调用readNumber()函数，根据当前字符及其后续的若干字符，利用另一个栈解析出当前的操作数。解析完毕，当前字符将再次聚焦于一个非数字字符。



### 4.3.4 逆波兰表达式

RPN表达式称作后缀表达式(postfix)，原表达式则称作中缀表达式(infix)。

* 求值算法

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0408.jpg?raw=true)

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0409.jpg?raw=true)

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0410.jpg?raw=true)

* 手工转换

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0411.jpg?raw=true)

* 自动转换

  evaluate()算法在对表达式求值的同时，也顺便完成了从常规表达式到RPN表达式的转换。在求值过程中，该算法借助append()函数将各操作数和运算符适时地追加至串rpn的末尾，直至得到完整的RPN表达式。

  这里采用的规则十分简明：凡遇到操作数，即追加至rpn；而运算符只有在从栈中弹出并执行时才被追加。这一过程，与上述手工转换的方法完全等效，其正确性也因此得以确立。



## 4.4 试探回溯法

* 剪枝

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0412.jpg?raw=true)

*如何保证搜索过的部分不被重复搜索：在剪枝的位置留下某种标记



### 4.2.2 八皇后

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0413.jpg?raw=true)

```c++
struct Queen { //皇后类
    int x, y; //皇后在棋盘上的位置坐标
    Queen ( int xx = 0, int yy = 0 ) : x ( xx ), y ( yy ) {};
    bool operator== ( Queen const& q ) const { //重载判等操作符，以检测不同皇后之间可能的冲突
        return ( x == q.x ) //行冲突（这一情况其实并不会发生，可省略）
               || ( y == q.y ) //列冲突
               || ( x + y = q.x + q.y ) //沿正对角线冲突
               || ( x - y == q.x -q.y ); //沿反对角线冲突
    }
    bool operator!= ( Queen const& q ) const { return ! ( *this == q ); } //重载不等操作符
};
```

* 算法实现

  既然每行能且仅能放置一个皇后，故不妨首先将各皇后分配至每一行。然后，从空棋盘开始，逐个尝试着将她们放置到无冲突的某列。每放置好一个皇后，才继续试探下一个。若当前皇后在任何列都会造成冲突，则后续皇后的试探必将时徒劳的，故此时应该回溯到上一皇后。

```c++
void placeQueens ( int N ) { //N皇后算法（迭代版）：采用试探/回溯的策略，借助栈记录查找的结果
    Stack<Queen> solu; //存放（部分）解的栈
    Queen q ( 0, 0 ); //从原点位置出发
    do { //反复试探、回溯
        if ( N <= solu.size() || N <= q.y ) { //若已出界，则
            q = solu.pop(); q.y++; //回溯一行，并继续试探下一列
        } else { //否则，试探下一行
            while ( ( q.y < N ) && ( 0 <= solu.find( q ) ) ) //通过与已有皇后的对比
            	{ q.y++; nCheck++; } //尝试找到可摆放下一皇后的列
            if ( N > q.y ) { //若存在可摆放的列，则
                solu.push ( q ); //摆上当前皇后，并
                if ( N <= solu.size() ) nSolu++; //若部分解已成为全局解，则通过全局变量nSolu计数
                q.x++; q.y = 0; //转入下一行，从第0列开始，试探下一皇后
            }
        }
    } while ( ( 0 < q.x ) || ( q.y < N ) ); //所有分支均已或穷举或剪枝之后，算法结束
}
```

这里借助栈solu来动态地记录各皇后的列号。当该栈的规模增至N时，即得到全局解。该栈即可依次给出各皇后在可行棋局中所处的位置。

* 实例

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0414.jpg?raw=true)



### 4.4.3 迷宫寻径

* 问题描述

  空间区域限定为由n X n个方格组成的迷宫，除了四周的围墙，还有分布其间的若干障碍物；只能水平或垂直移动。我们的任务是，在任意指定的起始格点与目标格点之间，找出一条通路（如果的确存在）。

* 迷宫格点

```c++
typedef enum { AVAILABLE, ROUTE, BACKTRACKED, WALL } Status; //迷宫单元状态
//原始可用的、在当前路径上的、所有方向均尝试失败后回溯过的、不可使用的（墙）

typedef enum { UNKNOWN, EAST, SOUTH, WEST, NORTH, NO_WAY } ESWN; //单元的相对邻接方向
//未定、东、男、西、北、无路可通

inline ESWN nextESWN ( ESWN eswn ) { return ESWN ( eswn + 1 ); } //依次转职下一邻接方向

struct Cell { //迷宫格点
    int x, y; Status status; //x坐标、y坐标、类型
    ESWN incoming, outgoing; //进入、走出方向
};

#define LABY_MAX 24 //最大迷宫尺寸
Cell laby[LABY_MAX][LABY_MAX]; //迷宫
```

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0415.jpg?raw=true)

* 邻格查询

```c++
inline Cell* neighbor ( Cell* cell ) { //查询当前位置的相邻格点
    switch ( cell->outgoing ) {
        case EAST : return cell + LABY_MAX; //向东
        case SOUTH : return cell + 1; //向南
        case WEST : return cell - LABY_MAX; //向西
        case NORTH : return cell - 1; //向北
        default : exit ( -1 );
    }
}
```

* 邻格转入

  在确认某一相邻格点可用之后，算法将朝对应的方向向前试探一步，同时路径延长一个单元。

```c++
inline Cell* advance ( Cell* cell ) {
    Cell* next;
    switch ( cell->outgoing ) {
        case EAST: next = cell + LABY_MAX; next->incoming = WEST; break; //向东
        case SOUTH: next = cell + 1; next->incoming = NORTH; break; //向南
        case WEST: next = cell - LABY_MAX; next->incoming = EAST; break; //向西
        case NORTH: next = cell - 1; next->incoming = SOUTH; break; //向北
        default : exit ( -1 );
    }
    return next;
}
```

* 算法实现

```c++
//迷宫寻径算法：在格单元s至t之间规划一条通路（如果的确存在）
bool labyrinth ( Cell Laby[LABY_MAX][LABY_MAX], Cell* s, Cell* t ) {
    if ( ( AVAILABLE != s->status ) || ( AVAILABLE != t->status ) ) return false; //退化情况
    Stack<Cell*> path; //用栈记录通路（Theseus的线绳）
    s->incoming = UNKNOWN; s->status = ROUTE; path.push ( s ); //起点
    do { //从起点出发不断试探、回溯，直到抵达终点，或者穷尽所有可能
        Cell* c = path.top(); //检查当前位置（栈顶）
        if ( c == t ) return true; //若已抵达终点，则找到了一条通路；否则，沿尚未试探的方向继续试探
        while ( NO_WAY > ( c->outgoing = nextESWN ( c->outgoing ) ) ) //逐一检查所有方向
            if ( AVAILABLE == neighbor ( c )->status ) break; //试图找到尚未试探的方向
        if ( NO_WAY <= c->outgoing ) //若所有方向都已尝试过
            { c->status = BACLTRACKED; c = path.pop(); } //则向后回溯一步
        else //否则，向前试探一步
            { path.push ( c = advance ( c ) ); c->outgoing = UNKNOWN; c->status = ROUTE; }
    } while ( !path.empty() );
    return false;
}
```

该问题的搜索过程中，局部解时一条源自起始格点的路径，它随着试探、回溯相应地伸长、缩短。因此，这里借助栈path按次序记录组成当前路径地所有格点，并动态地随着试探、回溯做入栈、出栈操作。路径的起始格点、当前的末端格点分别对应于path的栈底和栈顶，当后者抵达目标格点时搜索成功，此时path所对应的路径即可作为全局解返回。

* 实例

![](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0416.jpg?raw=true)

* 复杂度

  算法的每一步迭代仅需常数时间，故总体时间的复杂度线性正比于试探、回溯操作的总数。由于每个格点至多参与试探和回溯各依次，故亦可度量为所有被访问过的格点总数——在图4.10中，也就是最终路径的总长度再加上圆圈标记的数目。



## 4.5 队列

