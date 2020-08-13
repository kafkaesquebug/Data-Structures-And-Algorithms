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