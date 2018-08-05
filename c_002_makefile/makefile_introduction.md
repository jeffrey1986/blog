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

当我们执行make的时候，clean不会自动执行，因为它不是任何target的依赖。当我们想执行clean的时候，我们可以再执行的时候加上参数：

```
make clean
```

## 编写Makefile



## 编写规则

## 规则中的命令

## 变量

