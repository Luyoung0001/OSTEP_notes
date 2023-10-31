# 插叙: 线程API

关键问题：如何创建和控制线程？

操作系统应该提供哪些创建和控制线程的接口？这些接口如何设计得易用和实用？

## 线程创建

在 POSIX 中:
```C
#include <pthread.h>
int
pthread_create(      pthread_t *        thread,
               const pthread_attr_t *  attr,
                     void *             (*start_routine)(void*),
                     void *             arg);
```


该函数有4个参数：thread、attr、start_routine和arg。第一个参数thread是指向pthread_t结构类型的指针，我们将利用这个结构与该线程交互，因此需要将它传入pthread_create()，以便将它初始化。