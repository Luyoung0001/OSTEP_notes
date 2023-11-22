# 指南
## 编译和执行
gcc不是真正的编译器，而是所谓的“编译器驱动程序”，因此它协调了编译的许多步骤。

通常有4～5个步骤。
- 首先，gcc将执行cpp（C预处理器）来处理某些指令（例如#define和#include。
- 程序cpp只是一个源到源的转换器，所以它的最终产品仍然只是源代码（ 即一个C文件）。
- 然后真正的编译将开始，通常是一个名为cc1的命令。这会将源代码级别的C代码转换为特定主机的低级汇编代码。
- 然后执行汇编程序as，生成目标代码（机器可以真正理解的数据位和代码位），
- 最后链接编辑器（或链接器）ld将它们组合成最终的可执行程序。

幸运的是（！），在大多数情况下，你可以不明白gcc如何工作，只需愉快地使用正确的标志。

## 有用的标志
```bash
prompt> gcc -o hw hw.c # -o: to specify the executable name
prompt> gcc -Wall hw.c # -Wall: gives much better warnings
prompt> gcc -g hw.c     # -g: to enable debugging with gdb
prompt> gcc -O hw.c     # -O: to turn on optimization

```

## 与库链接

有时，你可能想在程序中使用库函数。因为C库中有很多函数（可以自动链接到每个程序），所以通常要做的就是找到正确的#include文件。


有两种类型的库：静态链接库（以.a结尾）和动态链接库（以.so结尾）。

静态链接库直接组合到可执行文件中。也就是说，链接器将库的低级代码插入到可执行文件中，从而产生更大的二进制对象。

动态链接通过在程序可执行文件中包含对库的引用来改进这一点。程序运行时，操作系统加载程序动态链接库。这种方法优于静态方法，因为它节省了磁盘空间（没有不必要的大型可执行文件），并允许应用程序在内存中共享库代码和静态数据。

对于数学库，静态和动态版本都可用，静态版本是/usr/lib/libm.a，动态版本是/usr/lib/libm.so。

人们有时更喜欢库的静态版本，因为使用动态库有一点点性能开销。

## 分别编译

一旦程序开始变得足够大，你可能希望将其拆分为单独的文件，分别编译每个文件，然后将它们链接在一起。例如，假设你有两个文件，hw.c和helper.c，希望单独编译它们，然后将它们链接在一起。


```bash
# we are using -Wall for warnings, -O for optimization
prompt> gcc -Wall -O -c hw.c
prompt> gcc -Wall -O -c helper.c
prompt> gcc -o hw hw.o helper.o -lm

```

当然，你可以在一行中为gcc指定所有C源文件（gcc -Wall -O -o hw hw.c helper.c），但这需要系统重新编译每个源代码文件，这个过程可能很耗时。通过单独编译每个源文件，你只需重新编译编辑修改过的文件，从而节省时间，提高工作效率。这个过程最好由另一个程序make来管理，我们接下来介绍它。

## Makefile文件

```makefile
hw: hw.o helper.o
    gcc -o hw hw.o helper.o -lm
　
hw.o: hw.c
    gcc -O -Wall -c hw.c
　
helper.o: helper.c
    gcc -O -Wall -c helper.c
　
clean:
    rm -f hw.o helper.o hw
```

target（目标）通常是程序生成的文件的名称。目标的例子是可执行文件或目标文件。目标也可以是要执行的操作的名称，例如在我们的示例中为“clean”。

prerequisite（先决条件）是用于生成目标的输入文件。目标通常依赖于几个文件。

例如，要构建可执行文件hw，需要首先构建两个目标文件：hw.o和helper.o。

最后，command（命令）是一个执行的动作。一条规则可能有多个命令，每个命令都在自己的行上。重要提示：必须在每个命令行的开头放一个制表符！如果你只放空格，make会打印出一些含糊的错误信息并退出。

通常，命令在具有先决条件的规则中，如果任何先决条件发生更改，就要重新创建目标文件。但是，为目标指定命令的规则不需要先决条件。例如，包含delete命令、与目标“clean”相关的规则中，没有先决条件。

虽然我们不会详细介绍make语法，但如你所见，这个makefile可以让生活更轻松一些。例如，它允许你轻松地将新的源文件添加到构建中，只需将它们加入makefile顶部的SRCS变量即可。你还可以通过更改TARG行轻松更改可执行文件的名称，并且可以轻松修改指定编译器，标志和库。



