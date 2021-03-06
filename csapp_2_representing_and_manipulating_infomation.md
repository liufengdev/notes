## 信息的表示与处理

信息 = 位 + 解释

- `无符号`(unsigned)编码基于传统的二进制表示法，表示大于或等于零的数字； 
- `补码`(two's-complement)编码是表示有符号整数的最常见的方式； 
- `浮点数`(floating-point)编码是表示实数的科学计数法的以2为基数的版本。

### 1. 信息存储
最小可寻址的内存单元为`字节`(byte)。

> C语言中的指针提供了引用数据结构(包括数组)的元素的机制。与变量类似，指针也有两个方面：值和类型。
> 值表示某个对象的位置，而类型表示那个位置上所存储对象的类型（比如整数或浮点数）。

#### 1.1 十六进制表示法
较大数值的十进制和十六进制之间的转换，最好让计算机或者计算器来完成。

#### 1.2 字数据大小
- 字长决定虚拟地址空间的最大大小。
- 我们将程序成为“32位程序”或“64位程序”时，区别在于该程序是如何编译的，而不是其运行的机器类型。
- 固定大小的数据类型，如 *int32_t*、*int64_t*

#### 1.3 寻址和字节顺序
- `大端法` 最高有效字节在最前面
- `小端法` 最低有效字节在最前面

字节顺序可能产生问题的三种情况
1. 在不同类型的机器之间通过网络传输二进制数据
2. 阅读表示整数数据的字节序列
3. 编写规避正常的类型系统的程序，如打印对象的字节表示

#### 1.4 表示字符串
C语言中的字符串被编码为一个以`null`（其值为0）字符串结尾的字符数组。每个字符都由某个标准编码来表示，最常见的是ASCII字符码。

#### 1.5 表示代码
从机器的角度来看，程序仅仅是字节序列。机器没有关于原始源程序的任何信息，除了可能用来帮助调试的辅助表意外。

#### 1.6 布尔代数
将布尔运算扩展到`位向量`的运算，也就是固定长度为w，由0和1组成的串。位向量的运算可以定义成参数的每个对应元素之间的运算。
在实际应用中，位向量经常用于表示编码集合，通过位向量掩码，有选择的使用或屏蔽一些信息。

#### 1.7 C语言中的位级运算、逻辑运算及移位运算
1. C语言支持按位布尔运算。位级运算的常用于实现`掩码`运算

> 掩码0xFF表示一个字的低位字节，例如对x=0x89ABCDEF进行掩码运算 x & 0xFF 将得到x的地位字节 0x000000EF

2. 逻辑运算在第一个参数进行求值就能确定结果时，不会对第二个参数求值

> 如 p && *p++ 不会产生空指针 

3. 移位k位

> 对于一个由w位组成的数据类型，在移动k>=w位时，实际移位量是 k mode w
> 如 int val = 0xFEDCBA98 >> 36，实际结果等同于 0xFEDCBA98 >> (36 % 32)

### 2. 整数表示

#### 2.1 无符号数
有符号数到无符号数的隐式转换会带来一些安全隐患，如函数getpeername，所以避免使用无符号数。实际上，除了C语言意外，很少有语言支持
无符号数。

#### 2.2 有符号数
`补码`表示，最高位的权重为 - 2^(w - 1)

### 3. 整数运算
算数运算`溢出`指完整的整数结果不能放到数据类型的字长限制中去。

#### 无符号加法
检测无符号加法的溢出
> 对在 0 <= x, y <= UMax 中的x和y，令s = x + y，则对于计算s，当且仅当 s < x （或等价的s < y）时，发生了溢出。

#### 有符号加法
检测有符号加法的溢出
> 对在 TMin <= x, y <= TMax 中的x和y，令s = x + y，则对于计算s，当且仅当 x < 0 && y < 0, s >= 0时，发生了负溢出；
> 当 x > 0 && y > 0，s <= 0时发送了正溢出

#### 整数运算的思考
计算机执行的“整数”运算实际上是一种模运算形式。表示数字的有限字长限制了可能的值的取值范围，结果运算可能溢出。补码表示提供了一种技能表示
正数也能表示负数的灵活方法，同时使用了与执行无符号算术相同的位级实现，包括加法、减法、乘法、除法在内的运算，无论是以无符号形式还是补码
形式表示的，都有完全一样或非常类似的位级行为。

### 4. 浮点数
`IEEE`浮点格式中数字的表示 *V = (-1)^s × M × 2^E*的形式来表示一个数:
- *符号*(sign) s决定这数是负数(s=1)还是整数(s=0)
- *尾数*(significand) M是一个二进制小数，它的范围数 1\~2-ε 或者 0\~1-ε
- *阶码*(exponent) E的作用是对浮点数加权，这个权重是2的E次幂（可能是负数）
