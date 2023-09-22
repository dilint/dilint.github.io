+++
title = 'CSAPP LAB'
date = 2023-09-22T17:26:42+08:00
draft = false
tags = ['assignment', 'open-course']
+++

# DataLab - howmanybits
## 题目
用有限的运算符（逻辑运算、移位运算等）实现功能：表示输入x所需要的最少bit位数。例：f(12)=5，f(-1)=1。
```c
/*howManyBits - return the minimum number of bits required to represent x in tow's complement
*   Examples:   howManyBits(12) = 5
*               howManyBits(296) = 10
*               howManyBits(-5) = 4
*               howManyBits(0) = 1
*               howManyBits(0x80000000) = 32
*   Legal ops: ! ~ & ^ | + << >>
*   Max ops: 90
*/
int howManyBits(int x){

}
```

## 分析
*可能有更好的方法，本方法仅为一种解决方案。*
首先分为补码 正负分析，然后考虑边界情况。
### 一般情况分析
先用变量minus来记录补码的正负，int类型共有32位，如果第31位为0说明是非负，否则为负数。

非负情况：当补码为非负时，需要找出数中从大到小第一个为1的位置pos(0-31)。再添加上一位符号位即可。（由于索引范围为0-31，而个数范围为1-32，最后pos需要+1，最高位1在第pos位指的是共有(pos+1)bits）。

负数情况：当补码为负时，将x转换为相反数-x(tmin=-0x80000000有越界风险需要另外讨论)，同样寻找相反数|x|中从大到小第一个为1的位置pos，观察例子f(-5)的绝对值2进制形式为101，3个bits但结果为4需要添加1；f(-1)绝对值2进制形式为1，1个bit结果为1不需要添加1。思考这是由于n位补码的表示范围为$[-2^{ n-1},2^{n-1}-1]$，补码中负数与绝对值的无符号代数关系为：
$$x+|x|=2^n$$①当$|x|=2^{pos}$时，假设$n=pos+1$，x刚好为能表达的最小数，$x=2^{pos}$,$x+|x|=2^{pos+1}$式子刚好满足；
②而当$|x|>2^{pos}$，根据补码的特性可知在x中在pos之前的位拥有一个符号位1，则假设$n=pos+1$，$x+|x|>2^{pos+1}=2^{n}$不成立，所以假设$n=pos+2$，pos+1存在一个符号位，$x+|x|=2^{n}$，则该补码的表示范围在$[-2^{ pos+1},2^{pos+1}-1]$，$|x|<2^{pos+1}，x>-2^{pos+1}$在范围内，所以可以表示；
使用变量flag对①和②情况进行区分。

综上：
1. 首先$x=|x|$
2. 得到最高位1的位置索引pos
3. 获取flag
4. 统计结果：ans=pos+!minus+minus&flag+1
### 求解pos和flag
pos：首先构造16位(pos1)mask1(0xffff0000)与x进行&运算获得tmp1，(tmp1=!tmp1使得当前16位拥有1时tmp1为0，否则为1)。然后根据tmp1，16+-8决定下一个判断位置(pos2)是24还是8，然后构造前pos2位mask2与x进行&运算获得tmp2，以此类推，5次就可以获得pos的位置。(具体操作方式见技巧部分)

flag：在获得1最大位pos后，构造前后(pos-1)位的mask，flag=!!(mask & x)，若pos之后还有1则flag=1，否则flag=0。

### 边界情况
①当x=0x80000000，取反加1为本身，所以得到pos=31，符合。
②当x=0，由于没有1，最后pos=1-1=0，当作为正数结果+1，以及原本转换+1，ans=2，有误需要特别处理；在判断flag时用到了(0-1)，而移位操作右操作数不能为负数，需要处理。
③当x=-1，最后pos=1，与情况②遇到的问题相同。
综上，对于$pos-1=-1$的情况，采取将$pos+1$以及$x=x<<1$方式计算flag避开；而对于x=0情况添加bool变量zero记录x是否为0，如果为0最后pos-1即可。
## 技巧
### 补码取相反数

```c
x = ~ x + 1
```

(其中x!=MIN 0x80000000)
### MASK构造
(1 << pos) - 1可构造出形如[00000,...,1111]的掩码
按位取反可获得[1111,...,0000]的掩码
### 绝对值
前提：x!=MIN 0x80000000
minus = x >> 31 & 1    判断正负
x=(~ 0 + minux) & ( ~ x + 1) + x & (~ 0 + ! minux)
### z=x+y*（+-1）用逻辑运算计算
说明：假设根据bool值tmp判断+1或者-1   且可以使用+运算 不能使用-运算
①将$z=x+y*(+-1)$转变为问题$z=x-y+(y+y)*(0/1)$
$x=x-y$ =》$x=x+(\~{~~~}y+1)$；$y=y+y$
②使tmp值可以映射如下：
|0|0x000000  |
|--|--|
| 1 |0x11111111  |

tmp = ~ 0 + ! tmp
综上：转换为表达式
```c
x = x + (~y + 1) & (~0 + tmp)
```

### a ? x : y 用逻辑运算表达

```c
x1 = !a
x2 = !!a
ans = (~0 + x1) & x + (~0 + x2) & y
```

## 代码

```c
int howManyBits(int x) {
        int minus = (x >> 31) & 1;
        int mask1, mask2, mask3, mask4, mask5, mask6;
        int fac2, fac3, fac4;
        int tmp1, tmp2, tmp3, tmp4, tmp5;
        int pos1, pos2, pos3, pos4, pos5;
        int flag;
        int ans;
        int constant1, constant2;
        int zero = !x;
        constant1 = ~1 + 1; // -1
        constant2 = ~0; // 11111111111111
        x = ((constant2 + !minus) & (~x + 1)) + (x & (constant2 + minus));
        mask1 = ~((1 << 16) + constant1);
        fac2 = ~4 + 1;
        fac3 = ~2 + 1;
        fac4 = ~1 + 1;
        tmp1 = !(mask1 & x);
        pos1 = 8 + (16 & (constant2 + tmp1));
        mask2 = ~((1 << pos1) + constant1);
        tmp2 = !(mask2 & x);
        pos2 = (pos1 + fac2) + (8 & (constant2 + tmp2));
        mask3 = ~((1 << pos2) + constant1);
        tmp3 = !(mask3 & x);
        pos3 = (pos2 + fac3) + (4 & (constant2 + tmp3));
        mask4 = ~((1 << pos3) + constant1);
        tmp4 = !(mask4 & x);
        pos4 = (pos3 + fac4) + (2 & (constant2 + tmp4));
        mask5 = ~((1 << pos4) + constant1);
        tmp5 = !(mask5 & x);
        pos5 = (pos4 + fac4) + (1 & (constant2 + tmp5));
        pos5 += 1;
        x = x << 1;
        mask6 = ((1 << pos5) + constant1);
        flag = !!(mask6 & x);
        ans = pos5 + !minus + (minus & flag) + ((~0 + !zero) & constant1);
  return ans;
}
```

## 更好的代码
> [链接](https://blog.csdn.net/perry0528/article/details/89425661?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2~default~OPENSEARCH~default-1-89425661-blog-125849757.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~OPENSEARCH~default-1-89425661-blog-125849757.pc_relevant_aa&utm_relevant_index=1)
```c
int howManyBits(int x) {
	int temp = x ^ (x << 1);
	int bit_16,bit_8,bit_4,bit_2,bit_1;
	bit_16 = !!(temp >> 16) << 4;
	temp = temp >> bit_16;
	bit_8 = !!(temp >> 8) << 3;
	temp = temp >> bit_8;
	bit_4 = !!(temp >> 4) << 2;
	temp = temp >> bit_4;
	bit_2 = !!(temp >> 2) << 1;
	temp = temp >> bit_2;
	bit_1 = !!(temp >> 1);
	return 1 + bit_1 + bit_2 + bit_4 + bit_8 + bit_16;
}

```
