##Bit manipulation
####342. Power of Four
学习一条位运算基础知识：如果n是2^m，那么n-1是m-1个1的重复而n是1后面跟着m-1个0，这样的话n & (n-1) == 0

接着这道题要用到的就是等比数列求和，Sn = a1(q^n - 1)/(q - 1)，赋值q=4, a1 = 1，得到(4^n - 1)必然为3的倍数
~~~~
return num > 0 and num & (num - 1) == 0 and (num - 1) % 3 == 0
~~~~

####191. Number of 1 Bits
继续刚才的那条奇技淫巧，n & (n - 1)能够消掉二进制数字最末尾的1，为什么呢？因为最末尾的1以两种形式出现，1后面n个0和1，前者的话，1后面n个0减去1=n个1，两者与操作一下就变成n+1个0，后者的话1-1=0，两者与操作还是0；这题了解这项奇技淫巧之后就反复消掉末尾0即可，我就不上代码了
