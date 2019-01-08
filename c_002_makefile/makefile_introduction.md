# Makefile

本文摘自GNU官方教程，更详细的介绍请参考[GNU Makefile官方教程](http://www.gnu.org/software/make/manual/make.html)

[TOC]

## Makefile简介

### Makefile中的规则

简单makefile通常由一些规则组成，类似如下：

```makefile
target … : prerequisites …
        recipe
        …
        …
```

*target*通常是要生成的文件的文件名，可以是可执行文件或者obj文件；也可以是一个动作的名字，比如’clean'。

*prerequisites*是生成target所依赖的文件。

*recipe*是make所执行的指令。recipe必须以tab键开始。

这个规则表示，执行recipe指定的指令，把prerequisites生成target。

### 一个简单的makefile

下面是个简单的makefile的例子，该makefile目的是生成可执行文件*edit*， 它依赖8个C文件和3个头文件：

```makefile
edit : main.o kbd.o command.o display.o \
       insert.o search.o files.o utils.o
        cc -o edit main.o kbd.o command.o display.o \
                   insert.o search.o files.o utils.o

main.o : main.c defs.h
        cc -c main.c
kbd.o : kbd.c defs.h command.h
        cc -c kbd.c
command.o : command.c defs.h command.h
        cc -c command.c
display.o : display.c defs.h buffer.h
        cc -c display.c
insert.o : insert.c defs.h buffer.h
        cc -c insert.c
search.o : search.c defs.h buffer.h
        cc -c search.c
files.o : files.c defs.h buffer.h command.h
        cc -c files.c
utils.o : utils.c defs.h
        cc -c utils.c
clean :
        rm edit main.o kbd.o command.o display.o \
           insert.o search.o files.o utils.o
```

反斜杠（\）用来将比较长的句子分成两行。

如何生成可执行文件edit：

把该makefile文件名取为Makefile, 然后在命令行执行：

```
make
```

如何删除当前存在的可执行文件edit和.o文件：

```
make clean
```

这个makefile由一些列的规则组成：

- edit 

  target是edit，也就是要生成一个名为edit的文件，prerequisites(依赖)是8个.o文件，recipe是(生成edit所执行的指令是:

          cc -o edit main.o kbd.o command.o display.o \
                     insert.o search.o files.o utils.o

- .o文件：

  target是.o文件，prerequisites是对应的.c和.h文件，recipe是对应的cc -c指令。

- clean

  target是clean，这不是要生成的文件名，它只是一个动作的名字，recipe是删除当前文件夹下面的edit和.o文件。它没有依赖，也不是其他任何target的依赖，这个规则的唯一目的就是执行recipe中的指令。这类target我们称之为“伪目标(phony targets)”，这个将在后面的章节中详细介绍。


## make如何执行

一般情况下，make会从makefile文件中的第一个target（非"."开头的target）开始执行。

make执行一条规则的时候会对比target和其依赖的文件的时间戳，如果target文件不存在，或者依赖文件的时间戳比target新，那么就会执行这条规则。

## Makefile中的变量

在上面的makefile中，那些.o文件我们重复写了几遍， 我们可以使用变量来使得该makefile变得更加简单一些：

```makefile
objects = main.o kbd.o command.o display.o \
          insert.o search.o files.o utils.o

edit : $(objects)
        cc -o edit $(objects)
main.o : main.c defs.h
        cc -c main.c
kbd.o : kbd.c defs.h command.h
        cc -c kbd.c
command.o : command.c defs.h command.h
        cc -c command.c
display.o : display.c defs.h buffer.h
        cc -c display.c
insert.o : insert.c defs.h buffer.h
        cc -c insert.c
search.o : search.c defs.h buffer.h
        cc -c search.c
files.o : files.c defs.h buffer.h command.h
        cc -c files.c
utils.o : utils.c defs.h
        cc -c utils.c
clean :
        rm edit $(objects)
```

## make自动推导

针对以'.o'结尾的的target，make可以自动推导出其依赖的.c文件和执行的recipe，比如

``` makefile
main.o : main.c defs.h
        cc -c main.c
```

我们可以简单的写为：

``` makefile
main.o : defs.h
```

因为make会自动为其加上依赖main.c和recipe: cc -c main.c -o main.o。所以上面的makefile可以简化为：

```makefile
objects = main.o kbd.o command.o display.o \
          insert.o search.o files.o utils.o

edit : $(objects)
        cc -o edit $(objects)

main.o : defs.h
kbd.o : defs.h command.h
command.o : defs.h command.h
display.o : defs.h buffer.h
insert.o : defs.h buffer.h
search.o : defs.h buffer.h
files.o : defs.h buffer.h command.h
utils.o : defs.h

clean :
        rm edit $(objects)
```

## target - clean

makefile除了能够编译出文件之外，还可以做一些其他事情，比如上面例子中的clean就是用来删除当前目录下的edit文件和上面列举的.o文件：

```makefile
clean:
        rm edit $(objects)
```

比较完美的写法应该是：

```makefile
.PHONY : clean
clean :
        -rm edit $(objects)
```

.PHONY关键字表示这是一个伪目标(后面会细讲)而不是某个文件的名字， -rm前面的“-”表示即使rm执行出错，也继续执行。

当我们执行make的时候，clean不会自动执行，因为它不是任何target的依赖。当我们想执行clean的时候，我们可以在执行的时候加上参数：

```
make clean
```

## 编写Makefile

### Makefile包含哪些内容

- 显示规则: 定义target, target依赖的文件及其要执行的recipe.
- 隐式规则: makefile根据target名字的自动推导功能. 比如main.o可以自动推导出依赖文件main.c和recipe: cc -c main.c -o main.o
- 变量定义: 把字符串赋值给一个变量
- 指令: 包含3个部分:1. include其他makefile; 2. 使用条件判断来决定执行makefile的哪部分代码; 3. 定义一个包含多行字符串的变量.
- 注释: 用#开头

### Makefile如何命名

默认情况下make指令会按照下面的顺序来寻找

1. GNUmakefile
2. makefile
3. Makefile

一般推荐使用Makefile作为名字.

也可以用"-f"或者"--file"来指定任意文件作为makefile:

```
make -f customize_makefile
```



## 编写规则

### 规则示例
```
foo.o : foo.c defs.h       # module for twiddling the frobs
        cc -c -g foo.c
```
该规则告诉我们两件事：
1. 何时该更新foo.o：当foo.o不存在或者它的依赖文件（foo.c defs.h）比它新时。
2. 如何更新foo.o：cc -c -g foo.c
### 规则语法
一般情况下，规则语法如下：
```
targets : prerequisites
        recipe
        …
```
或者：
```
targets : prerequisites ; recipe
        recipe
        …
```
- targets可以有多个，以空格分开
- recipe必须以tab开始
- $为特殊符号，一般用在变量开始，如果需要一个$符号，那么需要这样写$$
- 如果一行太长，可以在行尾加 \，然后新起一行，这两行会被当做一行
### 依赖种类
依赖可以分为普通依赖和纯指令依赖。纯指令依赖在执行后，并不会强制target更新，比如创建一个文件夹等。两种依赖以“|”分隔，左边为普通依赖，右边为纯指令依赖。
### 在文件名中适用通配符
#### 示例
在recipe中使用通配符：
```
clean:
        rm -f *.o
```
在依赖中使用通配符：
```
print: *.c
        lpr -p $?
        touch print
```
错误的用法：
```
objects = *.o
```
正确的用法应该为：
```
objects := $(wildcard *.o)
```
#### 函数wildcard
格式：
```
$(wildcard pattern…)
```
示例
```
$(wildcard *.c)
```
```
$(patsubst %.c,%.o,$(wildcard *.c))
```
```
objects := $(patsubst %.c,%.o,$(wildcard *.c))

foo : $(objects)
        cc -o foo $(objects)
```
### 为依赖文件搜索文件夹
#### VPATH
- make会在当前文件夹和VPATH指定的文件夹搜索依赖文件或者目标文件；
- VPATH指定的路径用冒号或者空格分开；
- make会按照指定的VPATH中指定的顺序来搜索。
示例：
```
VPATH = src:../headers
```
make会从src和../headers中去搜索文件。
#### vpath指令

vpath和VPATH的区别在于，vpath可以指定搜索指定类型的文件，比如只搜索以.c结尾的文件。

vpath有三种使用方法：

1. vpath pattern directories

   在directories文件夹下，搜索符合pattern格式的文件。
2. vpath pattern
   清除符合pattern格式的搜索。
3. vpath
   清除前面所有通过vpath指定的搜索。
示例：
```
vpath %.h ../headers
```
在../headers文件夹中搜寻以.h结尾的文件。
```
vpath %.c src1:src2
```
先在src1文件夹然后在src2文件夹中搜寻以.c结尾的文件。

不管是VPATH还是vpath，都是针对target或者prerequisites，和gcc编译时候的-I路径无关。
#### 文件夹搜索场景下编写recipes
```
foo.o : foo.c
        cc -c $(CFLAGS) $^ -o $@
```
[todo: need to verify]假设foo.c在另外一个目录src1里面，那么它的路径真实路径为src1/foo.c, 在写recipe的时候，我们无法知道foo.c的真实路径，如果这个时候我们继续用foo.c的话，会出现找不到文件的错误。那么这个时候我们就需要引入"$"这个关键字。比如"$^"表示所有prerequisites的列表，"$@"表示target，这样即使foo.c在src1里面，"$^"也能够保证make能找到该文件。
### 伪目标 - Phony Targets
伪目标不同于普通的target，它并不是一个文件名字，它可能只是一个指令，比如删除某些文件，或者创建文件夹，等等。使用伪目标有两个好处：
1. 避免和真实的文件冲突；
2. 提高效率
假设有这样一个用来删除某些文件的指令：
```
clean:
        rm *.o temp
```
[todo: need to verify]我们的预期是每次执行 make clean的时候，就调用 rm \*.o tmp 指令。但是这个规则有一个风险：如果存在clean这样一个文件，该规则又没有任何依赖，那么make会认为clean是最新的，所以就不需要执行，这样就与我们的预期不符了。所以这就需要用伪目标来解决这类问题。
看一个伪目标的例子：
```
.PHONY: clean
clean:
        rm *.o temp
```
.PHONY是make预留的关键字，作为target时，系统会知道这是一个伪目标。
### 静态模式规则
静态模式规则指定多个target，并且可以根据名字为target的推导出所需的依赖。
#### 静态模式规则语法
```
targets …: target-pattern: prereq-patterns …
        recipe
        …
```
示例：
```
objects = foo.o bar.o

all: $(objects)

$(objects): %.o: %.c
        $(CC) -c $(CFLAGS) $< -o $@
```
"$<"表示prerequisite， "$@"表示target。$(objects)中的每一个target，都应该满足target-pattern, 也就是%.o这种格式，如果不能满足，则需要用filter过滤下，比如下面的例子：
```
files = foo.elc bar.o lose.o

$(filter %.o,$(files)): %.o: %.c
        $(CC) -c $(CFLAGS) $< -o $@
$(filter %.elc,$(files)): %.elc: %.el
        emacs -f batch-byte-compile $<
```
#### 静态模式规则 vs 隐式规则

## 规则中的命令

## 变量

