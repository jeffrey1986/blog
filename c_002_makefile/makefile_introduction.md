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

## 编写Makefile

## 编写规则

## 规则中的命令

## 变量

