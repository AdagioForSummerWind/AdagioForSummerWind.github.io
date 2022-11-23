# 

大咖助阵｜曹春晖：聊聊 Go 语言的 GC 实现
曹春晖 2022-02-04

作者注：本文只作了解，不建议作为面试题考察。
你好，我是曹春晖，是《Go 语言高级编程》的作者之一。
今天我想跟你分享一下 Go 语言内存方面的话题，聊一聊 Go 语言中的垃圾回收（GC）机制的实现，希望你能从中有所收获。
武林秘籍救不了段错误

包教包会包分配
在各种流传甚广的 C 语言葵花宝典里，一般都有这么一条神秘的规则，不能返回局部变量：
int * func(void) {
    int num = 1234;
    /* ... */
    return &num;
}
duang!
当函数返回后，函数的栈帧（stack frame）就会被销毁，引用了被销毁位置的内存，轻则数据错乱，重则 segmentation fault。
可以说，即使经过了八十一难，终于成为了 C 语言绝世高手，我们还是逃不过复杂的堆上对象引用关系导致的 dangling pointer：

当 B 被 free 掉之后
你看，在这张图中，当 B 被 free 掉之后，应用程序依然可能会使用指向 B 的指针，这就是比较典型的 dangling pointer 问题，堆上的对象依赖关系可能会非常复杂。所以，我们要正确地写出 free 逻辑，还得先把对象图给画出来。
不过，依赖人去处理复杂的对象内存管理的问题是不科学、不合理的。C 和 C++ 程序员已经被折磨了数十年，我们不应该再重蹈覆辙了，于是，后来的很多编程语言就用上垃圾回收（GC）机制。