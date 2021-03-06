[toc]



# 绪论

## 1.1 计算机与算法

### 1.1.3 起泡排序(bubblesort)

* 局部有序和整体有序

满足A[i-1] <= A[i]称为顺序，否则逆序；有序序列中每一对相邻元素都是顺序的。

* 扫描交换

  从前向后依次检查每一对相邻元素，一旦发现逆序即交换二者的位置。

  ![image-bubblesort](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0100.png?raw=true)

* 实现

  ```c++
  /*与教材相比有私人改动*/
  void bubblesort1A ( int A[], int n ) { 
      bool sorted = false; 
      while ( !sorted ) { 
          sorted = true; 
          for ( int i = 1; i < n; i++ ) {
              if ( A[i - 1] > A[i] ) {
                  std::swap(A[i - 1], A[i]);
                  sorted = false;
              }
          }
          n--;
      }
  }
  
  int main() {
  	int A[] = {5, 2, 7, 4, 6, 3, 1};
  	int len = sizeof(A) / sizeof(A[0]);
  	int n = len;
  	bubblesort1A(A, n);
  	for ( int i = 0; i < len; i++ ) {
  		std::cout << A[i] << std::endl;
  	}
      
      return 0;
  }
  // 注：该算法不具有重用性，在后面的章节中将会改进
  ```



## 1.2 复杂度度量

### 1.2.1 时间复杂度(time complexity)

算法执行时间的变化趋势可表示为输入规模的一个函数，称作该算法的时间复杂度(time complexity)。特定算法处理规模为n的问题所需的时间可记作T(n)。从保守估计的角度出发，在规模为n的所有输入中选择执行时间最长者作为T(n)，并以T(n)度量该算法的时间复杂度。



### 1.2.2 渐进复杂度

评价算法运行效率时应当更关注大规模问题，这种着眼长远、更为注重时间复杂度的总体变化趋势和增长速度的策略和方法，即所谓渐进分析(asymptotic analysis)。

* 大O记号(big-O notation)

  若存在正的常数c和函数f(n)，使得对任何n >> 2都有:

  T(n) <= c·f(n)

  则可认为在n足够大后，f(n)给出了T(n)增长速度的一个渐进上界。此时记为：

  T(n) = O(f(n))

  由这一定义，可导出大O记号的以下性质：

  (1) 对于任一常数c > 0， 有O(f(n)) = O(c·f(n))

  (2) 对于任意常数a > b > 0，有O(n^a + n^b) = O(n^a)

  性质(1)意味着在大O记号的意义下，函数各项正的常系数可以忽略并等同于1。性质(2)意味着多项式中低次项均可忽略，只需保留最高次项。

* 基本操作

  在图灵机和随机存储机等计算模型中，指令语句均可分解为若干次基本操作，而在大多数实际的计算环境中，每一次这类基本操作都可在常数时间内完成。如此，不妨将T(n)定义为算法所执行的基本操作的总次数。

  以bubblesort1A的算法为例，外循环（while循环）至多执行n - 1轮，内循环需要扫描和比较n - 1对元素，至多交换n - 1对元素，故内循环至多需要执行2(n - 1)轮。因此总共需要执行的基本操作不会超过2(n-1)^2次，根据大O记号的性质可简化整理为：

  T(n) = O(2(n-1)^2) = O(n^2)

* 大Ω记号(乐观估计)

  为了对算法的最好情况做出估计。

  若存在正的常数c和函数f(n)，使得对任何n >> 2都有:

  T(n) >= c·g(n)

  则可认为在n足够大后，g(n)给出了T(n)增长速度的一个渐进上界。此时记为：

  T(n) = Ω(g(n))

* 大Θ记号(准确估计)

  从渐进的趋势看，T(n)介于Ω(g(n))与O(f(n))之间。若恰好出现g(n) = f(n)的情况，则可以使用另一记号来表示。

  若存在正的常数c1 < c2 和函数h(n)，使得对任何n >> 2都有:

  c1·h(n)  <= T(n) <= c2·h(n)

  就可以认为在n足够大后，h(n)给出了T(n)的一个确界，此时记为：

  T(n) = Θ(h(n))

  对于规模为n的任何输入，算法的运行时间T(n)都与Θ(h(n))同阶。

![image](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0101.png?raw=true)



### 1.2.3 空间复杂度(space complexity)

针对时间复杂度所引入的几种渐进记号，也适用于对空间复杂度的度量。

为了更客观地评价算法性能的优劣，除非特别申明，空间复杂度通常并不计入原始输入本身所占用的空间。反之，其它（如转储、中转、索引、映射、缓冲等）各个方面所消耗的空间，则都应计入。

另外，很多时候不必对空间复杂度做专门的考查，因为在任一算法的任何一次运行过程中所消耗的存储空间，都不会多于其间所执行的基本操作的累计次数。实际上根据定义，每次基本操作所设计的存储空间都不会超过常数规模。时间复杂度本身就是空间复杂度的一个天然的上界。



## 1.3 复杂度分析

### 1.3.1 常数O(1)

* 问题与算法

  ```
  ordinaryElement(S[], n) //从n≥3个互异整数中，除去最大、最小元素，任取一个常规元素
      任取三个元素x, y, z ∈ S; //三个元素互异
      通过比较排序; //排序后依次重命名为a < b < c
      输出b;
  ```

* 复杂度

  不妨取前三个元素x = S[0]、y = S[1]和z = S[2]，耗费O(3)时间。接下来比较三个元素确定大小次序，也需要O(3)时间。最后输出非极端元素只需O(1)时间。综合起来为：

  T(n) = O(3) + O(3) + O(1) = O(7) = O(1)

  运行时间可表示和度量为T(n) = O(1)的这一类算法统称作“常数时间复杂度算法”(constant-time algorithm)。除了输入数组等参数之外，该算法仅需常数规模的辅助空间，亦称作就地算法(in-place algorithm)。

  

### 1.3.2 对数O(logn)

* 问题与算法

  对任意非负整数，统计其二进制展开中数位1的总数。

  ```c++
  int countOnes ( unsigned int n ) { 
      int ones = 0; 
      while ( 0 < n ) {
          ones += ( 1 & n ); //检查最低位，若为1则计数
          n >>= 1;
      }
      return ones;
  } 
  
  int main() {
  	unsigned int n = 441;
  	std::cout << countOnes(n);
  	
  	return 0;
  } 
  ```

* 复杂度

  每右移一位，n都至少缩减一半，也就是说至多经过1 + [log2(n)]次循环n必然缩减至0。countOnes()算法的执行时间为：

  O( 1 + [log2(n)] ) = O( [log2(n)] ) = O(log2(n))

  由大O记号定义，在用函数logr(n)界定渐进复杂度时，常底数r的具体取值无所谓，故通常不予专门标出而笼统地记作logn。此类算法称作具有“对数时间复杂度”(logarithmic-time algorithm)。

* 对数多项式复杂度算法(polylogarithmic-time algorithm)



### 1.3.3 线性O(n)

* 问题和算法

  ```c++
  int sumI ( int A[], int n ) { //数组求和算法（迭代）
      int sum = 0; //O(1)
      for ( int i = 0; i < n; i++ ) //对O(n)个元素
          sum += A[i]; //O(1)
      return sum; //O(1)
  } // O(1) + O(n)*O(1) + O(1) = O(n+2) = O(n)
  ```

* 复杂度

  凡运行时间可以表示为T(n) = O(n)这一形式的算法，称作线性时间复杂度算法(linear-time algorithm)。

  

### 1.3.4 多项式O(polynomial(n))

形式：T(n) = O(f(n))，且f(x)为多项式。bubblesort1A()算法属于此类。线性时间复杂度算法属于此类的特例，其中n = 1。

多项式级的运行时间成本在实际应用中一般被认为是可接受或可忍受的。某个问题若存在一个复杂度在此范围内的算法，则称该问题是可有效求解或易解的(tractable)。



### 1.3.5 指数O(2^n)

* 问题与算法

  在禁止超过1位的移位运算的前提下，对任意非负整数n，计算幂2^n。

  ```C++
  __int64 power2BF_I ( int n ) { //幂函数2^n算法（迭代）
      __int64 pow = 1; //O(1)
      while ( 0 < n -- ) //O(n)
          pow <<= 1; //O(1)
      return pow; //O(1)
  } //O(n) = O(2^r)
  ```

* 复杂度

  凡运行时间可以表示为T(n) = O(a^n)这一形式的算法(a > 1)，称作指数时间复杂度算法(exponential-time algorithm)。

* 从多项式到指数

  通常认为指数复杂度算法无法真正应用于实际问题中，它们不是有效算法，甚至不能称作算法。相应地，不存在多项式复杂度算法的问题，也称作难解的(intractable)问题。

  

### 1.3.6 复杂度层次

![image-20200703143045335](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0102.jpg?raw=true)



### 1.3.7 输入规模

严格地说，所谓待计算问题的输入规模，应严格定义为“用以描述输入所需的空间规模”。将输入参数n二进制展开的宽度r作为输入规模更为合理。对应地，以输入参数n本身的数值作为基准而得出的O(logn)和O(n)复杂度，则应分别称作伪对数的(pseudo-logarithmic)和伪线性的(pseudo-linear)复杂度。



## 1.4递归

### 1.4.1 线性递归

* 数组求和

  ```c++
  int sum ( int A[], int n ) { //数组求和（递归）
      if ( 1 > n ) //base case
          return 0; 
      else 
          return sum ( A, n - 1 ) + A[n - 1]; //前n - 1项之和，再累计第n - 1项
  } //O(1)*递归深度 = O(1)*(n + 1) = O(n)
  
  int main() {
  	int A[] = {1, 2, 3, 4};
  	std::cout << sum(A, 4);
  	
  	return 0;
  }
  ```

* 线性递归

  算法sum()可能朝着更深一层进行自我调用，且每一递归实例对自身的调用至多一次。于是，每一层次上至多只有一个实例，且它们构成一个线性的次序关系。此类递归模式被称作线性递归（linear recursion），也是递归的最基本形式。

  这种形式中，应用问题总可以分解为两个独立的子问题：其一对应于单独某个元素，故可直接求解（比如A[n - 1]）；另一个对应于剩余部分，其结构与原问题相同（比如A[0, n - 1]）。另外，子问题的解经过简单的合并之后，即可得到原问题的解。



### 1.4.2 递归分析

* 递归跟踪(recursion trace)

  ![image-20200703145541236](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0103.jpg?raw=true)

  就上述算法而言，每一递归实例中非递归部分所涉及的计算无非三类（判断n是否为0、累加sum(n - 1)与A[n - 1]、返回当前总和），且至多各自执行一次。每个递归实例实际所需的计算时间都为O(3)。对于长度为n的输入数组，递归深度应为n + 1，故整个sum()算法的运行时间为：

  (n + 1) X O(2) = O(3·(n+1)) = O(n)

  在创建了最后一个递归实例时，占用空间达到最大，等于所有递归实例各自占用空间的总和（为常数）。sum()算法的空间复杂度线性正比于递归深度，即O(n)。

* 递推方程(recurrence equation)

  sum()算法的一般性递推关系：

  T(n) = T(n - 1) + O(1) = T(n - 1) + c1(常数)

  边界条件（抵达递归基时，求解sum(A, 0)）：

  T(0) = O(1) = c2（常数）

  联立解得：

  T(n) = c1·n + c2 = O(n)



### 1.4.3 递归模式

* 多递归基

  ```c++
  void reverse ( int* A, int lo, int hi ) { //数组倒置
  	if ( lo < hi ) {
  		std::swap( A[lo], A[hi] );
  		reverse ( A, lo + 1, hi - 1 );
  	} //else隐含了两种递归基
  } //O(hi - lo + 1)
  
  int main() {
  	int A[] = {1, 2, 3, 4, 5, 6};
  	reverse(A, 0, 5);
  	for ( int i = 0; i < 6; i++ ) {
  		std::cout << A[i];
  	}
  }
  ```

* 多向递归

  ![image-20200703175718789](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0104.jpg?raw=true)

  ```c++
  inline __int64 sqr ( __int64 a ) { return a * a; }
  __int64 power2 ( int n ) {
  	if ( 0 == n ) return 1;
  	return ( n & 1 ) ? sqr ( power2 ( n >> 1 ) ) << 1 : sqr ( power2 ( n >> 1 ) );
  }
  
  int main() {
  	std::cout << power2(3);	//计算2^3
  	return 0;
  } //结果为8
  ```

  这个例子有不同的递归方向，尽管如此，每个递归实例都只能沿其中的一个方向深入到下层递归，依然属于线性递归，该算法的时间复杂度为：

  O(log n) X O(1) = O(log n) = O(r)



### 1.4.4 递归消除

* 空间成本

  递归算法所消耗的空间量主要取决于递归深度，故较之同一算法的迭代版，递归版往往需要耗费更多的空间，进而影响实际的运行速度。另外，为实现递归调用需要花费大量额外的时间以创建、维护和销毁各递归实例，这些也会令计算的负担雪上加霜。

* 尾递归及其消除

  在线性递归算法中，若递归调用恰好在递归实例中以最后一步操作的形式出现，则称作尾递归(tail recursion)。属于尾递归形式的算法均可以转换为等效的迭代版本。

  ```c++
  void reverse ( int* A, int lo, int hi ) {
  	while ( lo < hi )
  	std::swap ( A[lo++], A[hi--] );
  }
  
  int main() {
  	int A[] = {1, 2, 3, 4, 5, 6};
  	reverse(A, 0, 5);
  	for ( int i = 0; i < 6; i++ ) {
  		std::cout << A[i];
  	}
  }
  ```



### 1.4.5 二分递归

* 用分而治之求数组和

  ```c++
  int sum ( int A[], int lo, int hi ) {
  	if ( lo == hi )
  	    return A[lo];
  	else {
  		int mi = ( lo + hi ) >> 1;
  		return sum ( A, lo, mi ) + sum ( A, mi + 1, hi );
  	}
  }
  
  int main() {
  	int A[] = {1, 2, 3, 4};
  	std::cout << sum(A, 0, 3);
  	
  	return 0;
  }
  ```

  ![image-20200703182057237](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0105.jpg?raw=true)

* Fibonacci数：二分递归

  分治策略必须保证原问题分解和子解答合并都能高效率实现，并且子问题之间相互独立。以下以Fibonacci数列的计算说明这一点。

  ```c++
  __int64 fib ( int n ) {
  	return ( 2 > n ) ?
  	        ( __int64 ) n
  	        : fib ( n - 1 ) + fib ( n - 2 );  
  }
  
  int main() {
      int n = 5;
      std::cout << fib(n);
      return 0;
  } //Fibonacci数列的第5项为5
  ```

  ![image-20200703184113199](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0106.jpg?raw=true)

  ![image-20200703184119979](https://github.com/kafkaesquebug/Data-Structures-And-Algorithms/blob/master/images/TsingHua_DSA/0107.jpg?raw=true)

  这一版fib()算法的时间复杂度大的原因是：计算过程中所出现的递归实例重复度极高。

* 优化策略

  制表(tabulation)/记忆(memoization)：从原问题出发自顶而下，每当遇到一个子问题，都首先查验它是否已经计算过，以期通过直接调阅记录获得解答。

  动态规划(dynamic programming)：从递归基出发，自底而上递推地得出各个子问题的解，直至最终原问题的解。

* Fibonacci数：线性递归

  ```c++
  __int64 fib ( int n, __int64& prev ) {
  	if ( 0 == n )
  	    { prev = 1; return 0; }
  	else {
  		__int64 prevPrev; prev = fib ( n -1, prevPrev );
  		return prevPrev + prev;
  	}
  }
  ```

* Fibonacci数：迭代

  ```c++
  __int64 fib ( int n ) {
  	__int64 f = 1, g = 0;
  	while ( 0 < n -- ) { g += f; f = g - f; }
  	return g;
  }
  
  int main() {
      int n = 5;
      std::cout << fib(n);
      return 0;
  }
  ```

  
