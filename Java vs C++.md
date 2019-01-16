1.推荐阅读：[用 C++ 和 Java 写算法，差别大吗?](https://blog.csdn.net/valada/article/details/86503375)
2.think in java中的RTTI即是反射


# Java
1. LinkedBlockingDeque内部只有一把锁以及该锁上关联的两个条件，所以可以推断同一时刻只有一个线程可以在队头或者队尾执行入队或出队操作。LinkedBlockingQueue可以同时有两个线程在两端执行操作