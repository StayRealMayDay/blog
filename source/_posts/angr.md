---
title: angr
date: 2018-01-18 17:50:31
comments: true
tags:
  - angr
  - Vulnerability mining
  - System security
categories: python
---

## **什么是angr：**

angr是一个二进制代码分析工具，能够自动化完成二进制文件的分析，并找出漏洞。在二进制代码中寻找并且利用漏洞是一项非常具有挑战性的工作，它的挑战性主要在于人工很难直观的看出二进制代码中的数据结构、控制流信息等。angr是一个基于python的二进制漏洞分析框架，它将以前多种分析技术集成进来，­­­它能够进行动态的符号执行分析（如，KLEE和Mayhem），也能够进行多种静态分析。

## **angr的基本过程：**

------

1）将二进制程序载入angr分析系统

2）将二进制程序转换成中间语言（intermediate representation, IR）

3）将IR语言转换成语义较强的表达形式，比如，这个程序做了什么，而不是它是什么。

4）执行进一步的分析，比如，完整的或者部分的静态分析（依赖关系分析，程序分块）、程序空间的符号执行探索（挖掘溢出漏洞）、一些对于上面方式的结合。

<!--more-->

## **如何安装：**

### **Linux**

首先安装依赖

```
sudo apt-get install python-dev libffi-dev build-essential virtualenvwrapper
```

安装angr

```
mkvirtualenv angr && pip install angr
```

到此angr安装结束

### **Mac OS**

首先安装依赖

```
pip install -I --no-use-wheel angr-only-z3-custom
```

安装angr

```
pip install angr
```

安装完成之后，就可以导入angr了，但是这个地方我与网上一些教程不同，在导入angr的时候，有的教程上面说需要先进入angr的虚拟环境 然后再进入python环境中，再导入angr模块，但是我按照他说的那样做报错，是没有angr模块的，本人用的ubuntu14.04虚拟机，直接进入python环境才可以导入angr模块，不需要进入angr的虚拟环境。

## **angr过程分析**

### **angr-CLE**

CLE是angr加载二进制文件的组建，在加载二进制文件的时候会分析病读取binary的信息，包括指令地址、shared library、arch information等等。

```
>>> import angr

>>> b = angr.Project("./test")
```

### angr_IR

angr用VEX IR将指令转化为中间语言IR，分析IR并且模拟，搞清楚它是什么并且做了什么。

如下的ARM指令

```
subs R2, R2, #8
```

转化为VEX IR

```
t0 = GET:I32(16)
t1 = 0x8:I32
t3 = Sub32(t0,t1)
PUT(16) = t3
PUT(68) = 0x59FC8:I32
```

### angr-Solver Engine

angr的求解引擎叫Claripy，具体这一步做什么呢，根据程序所需要的输入设置符号变量以及收集限制式等等。

```
>>> import claripy

>>> bv = claripy.BVV(0x41424344, 32)
```

## 例子

这个例子说明了angr的简单用法，用例程序来自https://github.com/angr/angr-doc/tree/master/examples/sym-write，其中有三个文件，一个是C源码，一个是二进制代码，还有一个是python脚本文件也就是我们的测试脚本。

源码如下

```
#include <stdio.h>


char u=0;
int main(void)
{
	int i, bits[2]={0,0};
	for (i=0; i<8; i++) {
		bits[(u&(1<<i))!=0]++;
	}
	if (bits[0]==bits[1]) {
		printf("you win!");
	}
	else {
		printf("you lose!");
	}
	return 0;
}
```

这个源码很简单就不介绍了。

接下来就是python脚本文件，GitHub上面的脚本文件在我的虚拟机上面运行出错，据本人分析应该是angr更新之后对于有一些属性进行了修改，比如脚本文件中的sm = p.factory.simgr(state)  这一行代码就会报错，因为没有simgr这个属性，以及在main函数return的地方sm.found[0].state.se.any_int(u)，这样修改才不会报错，不然会出现path没有se属性的错误。为了便于阅读，我自己写了一个python脚本对这个二进制代码进行分析。

```
import angr
import claripy
def main():
        p = angr.Project('./issue', load_options={"auto_load_libs": False})
        state = p.factory.entry_state(add_options={"SYMBOLIC_WRITE_ADDRESSES"})
        u = claripy.BVS("u", 8)
        state.memory.store(0x804a021, u)
        sm = p.factory.path_group(state)
        sm.step(until = lambda lpg: len(lpg.active) > 1)
        for I in range(len(sm.active)):
           print "possible %d: " % I , sm.active[I].state.se.any_int(u)
        return sm.active[0].state.se.any_int(u)
if __name__ == '__main__':
        print(repr(main()))
```

代码分析：

新建一个angr工程，导入二进制文件,./issue是二进制文件的路径。后面选项是是否自动加载依赖。

```
 p = angr.Project('./issue', load_options={"auto_load_libs": False})
```

紧接着创建一个SimState对象，SimState的对象在angr其中的一个子模块SimuVEX中，这个对象记录着符号信息，符号对应的内存信息，寄存器信息等等。

```
state = p.factory.entry_state(add_options={"SYMBOLIC_WRITE_ADDRESSES"})
```

设置符号变量，读C源码可以知道，变量u其实可以视为一个外部输入，根据u的值的不同，最后得到的结果也不相同，如果u的二进制表示中1和0的个数相同就返回win，否则返回lose。所以在此设计符号变量u

```
u = claripy.BVS("u", 8)
```

将符号变量保存在内存的指定地址处

```
state.memory.store(0x804a021, u)
```

接下来根据状态可以获取路径组对象，这个路径和你的状态参数有关，如果是入口状态那么你得到的路径就是入口处的路径。从获得的路径点开始，之后所有此路径分裂出去的路径都会包含在此路径组里面。

```
sm = p.factory.path_group(state)
```

然后开始执行，一直执行到active状态的路径超过一条的时候停止，即执行到C源码的if(bits[0]==bits[1])处，产生了两条路径，然后停止。

```
 sm.step(until = lambda lpg: len(lpg.active) > 1)
```

然后打印出所有路径下的的U的取值

```
for I in range(len(sm.active)):
           print "possible %d: " % I , sm.active[I].state.se.any_int(u)
```

返回结果是win的u的取值

```
return sm.active[0].state.se.any_int(u)
```

到此主要代码分析结束，在GitHub上面有原始的python脚本，那个脚本只是输出结果为win时候的U的取值，但是那个脚本有错误，并且不太简介，由此我在此基础上做了修改，写了上述脚本，供大家参考分析。

## 总结

以上就是angr的基本用法，本人对于angr的理解也还是很浅显，有些地方也不是特别清楚，如果有错的地方，希望大家指点。