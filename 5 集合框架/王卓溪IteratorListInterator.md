# Iterator

是一个接口，它是集合的迭代器。集合可以通过Iterator去遍历集合中的元素。Iterator提供的API接口如下

![image-20200607080136798](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200607080136798.png)



# 2.ListIterator

只能用于链表

区别：Iterator 和 ListIterator 主要区别



- ListIterator有`add()`方法，可以向List中添加对象，而Iterator不能
- ListIterator和Iterator都有`hasNext()`和`next()`方法，可以实现顺序向后遍历，但是ListIterator有`hasPrevious()`和`previous()`方法，可以实现逆向（顺序向前）遍历。Iterator就不可以。
- ListIterator可以定位当前的索引位置，`nextIndex()`和`previousIndex()`可以实现。Iterator没有此功能。
- ListIterator 可以再迭代时对集合进行`add`、`set`、`remove`操作，而`Iterator`迭代器只能在迭代时对集合进行 remove 操作

![image-20200607080647458](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200607080647458.png)