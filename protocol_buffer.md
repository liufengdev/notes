##PB编码

记录pb的二进制通信格式。主要目的是理解pb是如何编码的。

###最基本的数据类型
对于以下最基本的数据类型进行编码

~~~
message Test1 {
  required int32 a = 1;
}
~~~

编码后的结果为

~~~
08 96 01
~~~

如何理解以上的编码数据，首先得了解***Varints***

###Varints
***Varints***是一种数字编码方式，它使用一个或多个字节进行编码，越小的数字占用的字节数越少。

***Varints***中的每个字节最高位表示当前字节后面紧跟的字节是否为当前数字的一部分，剩下的7位表示数字的值。数字值的存储方式为`least significant group first`，也就是低位存储在前面

> ***数字1的表示***
> 
> ~~~
> 0000 0001
> ~~~
> 
> 最高位为***0***， 表示该数字仅有此一个字节，剩下7位(***000 0001***)表示数字的值1


> ***数字300的表示***
> 
> ~~~
> 1010 1100 0000 0010
> ~~~
> 
> 第一个字节（`1010 1100`）的最高位为***1***,表示其后紧跟着一个字节。第二个字节（`0000 0010`）的最高位为***0***，表示当前字节为该数字的最后一个。
> 去掉标识位以后
> 
> ~~~
>  010 1100  000 0010
> ~~~
> 反转前后两个字节
> 
> ~~~
>  000 0010 010 1100
> ~~~
> 可以如上即是 <math display="block">
				    <msubsup><mi>2</mi> <mi></mi> <mi>8</mi></msubsup>
				    <mo>+</mo>
				    <msubsup><mi>2</mi> <mi></mi> <mi>5</mi></msubsup>
				    <mo>+</mo>
				    <msubsup><mi>2</mi> <mi></mi> <mi>3</mi></msubsup>
				    <mo>+</mo>
				    <msubsup><mi>2</mi> <mi></mi> <mi>2</mi></msubsup>
				    <mo>=</mo>
				    <mi>300</mi>
				</math>



---

####参考文档
Protocol Buffers Document - Encoding [https://developers.google.com/protocol-buffers/docs/encoding](https://developers.google.com/protocol-buffers/docs/encoding)