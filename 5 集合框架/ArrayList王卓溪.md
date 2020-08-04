```java
// 版本号
    private static final long serialVersionUID = 8683452581122892189L;
    // 缺省容量
    private static final int DEFAULT_CAPACITY = 10;
    // 空对象数组
    private static final Object[] EMPTY_ELEMENTDATA = {};
    // 缺省空对象数组
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    // 元素数组
    transient Object[] elementData;
    // 实际元素大小，默认为0
    private int size;
```

# 二、ArrayList的主要成员变量

private static final int DEFAULT_CAPACITY = 10；
当ArrayList的构造方法中没有显示指出ArrayList的数组长度时，类内部使用默认缺省时对象数组的容量大小，为10。

private static final Object[] EMPTY_ELEMENTDATA = {};
当ArrayList的构造方法中显示指出ArrayList的数组长度为0时，类内部将EMPTY_ELEMENTDATA 这个空对象数组赋给elemetData数组。

private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
当ArrayList的构造方法中没有显示指出ArrayList的数组长度时，类内部使用默认缺省时对象数组为DEFAULTCAPACITY_EMPTY_ELEMENTDATA。

transient Object[] elemetData;
ArrayList的底层数据结构，只是一个对象数组，用于存放实际元素，并且被标记为transient，也就意味着在序列化的时候此字段是不会被序列化的。

private int size;
实际ArrayList中存放的元素的个数，默认时为0个元素。

private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE – 8;
————————————————

# 三、ArrayList的构造方法

### 1.无参构造方法

对于无参构造方法，将成员变量elementData的值设为DEFAULTCAPACITY_EMPTY_ELEMENTDATA。

```java
public ArrayList() { 
        // 无参构造函数，设置元素数组为空 
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

### 2.int类型参数构造方法

**参数为希望的ArrayList的数组的长度，initialCapacity。**

首先要判断参数initialCapacity与0的大小关系：

如果initialCapacity大于0，则创建一个大小为initialCapacity的对象数组赋给elementData。

如果initialCapacity等于0，则将EMPTY_ELEMENTDATA赋给elementData。

如果initialCapacity小于0，抛出异常（非法的容量）。

```java
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) { // 初始容量大于0
        this.elementData = new Object[initialCapacity]; // 初始化元素数组
    } else if (initialCapacity == 0) { // 初始容量为0
        this.elementData = EMPTY_ELEMENTDATA; // 为空对象数组
    } else { // 初始容量小于0，抛出异常
        throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
    }
}
```

### 3.Collection<? extends E>类型构造方法

第一步，将参数中的集合转化为数组赋给elementData；

第二步，参数集合是否是空。通过比较size与第一步中的数组长度的大小。

第三步，如果参数集合为空，则设置元素数组为空，即将EMPTY_ELEMENTDATA赋给elementData；

第四步，如果参数集合不为空，接下来判断是否成功将参数集合转化为Object类型的数组，如果转化成Object类型的数组成功，则将数组进行复制，转化为Object类型的数组。

```java
public ArrayList(Collection<? extends E> c) { // 集合参数构造函数
    elementData = c.toArray(); // 转化为数组
    if ((size = elementData.length) != 0) { // 参数为非空集合
        if (elementData.getClass() != Object[].class) // 是否成功转化为Object类型数组
            elementData = Arrays.copyOf(elementData, size, Object[].class); // 不为Object数组的话就进行复制
    } else { // 集合大小为空，则设置元素数组为空
        this.elementData = EMPTY_ELEMENTDATA;
    }


```

# 六、ArrayList的add()方法

在add()方法中主要完成了三件事：

首先确保能够将希望添加到集合中的元素能够添加到集合中，即确保ArrayList的容量（判断是否需要扩容）；

然后将元素添加到elementData数组的指定位置；

最后将集合中实际的元素个数加1。

```java
public boolean add(E e) { // 添加元素
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
```

# 七、ArrayList的扩容机制

ArrayList的扩容主要发生在向ArrayList集合中添加元素的时候。由add()方法的分析可知添加前必须确保集合的容量能够放下添加的元素。

主要经历了以下几个阶段：

第一，在add()方法中调用ensureCapacityInternal(size + 1)方法来确保添加元素成功的最小集合容量为minCapacity的值。参数为size+1，代表的含义是如果集合添加元素成功后，集合中的实际元素个数。换句话说，**<u>集合为了确保添加元素成功，那么集合的最小容量minCapacity应该是size+1。</u>**在ensureCapacityInternal方法中，首先判断elementData是否为默认的空数组，如果是，minCapacity为minCapacity与集合默认容量大小中的较大值。

```java
private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) { // 判断元素数组是否为空数组
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity); // 取较大值
    }
        
    ensureExplicitCapacity(minCapacity);
}
```





第二，调用ensureExplicitCapacity(minCapacity)方法来确定集合为了确保添加元素成功是否需要对现有的元素数组进行扩容。

首先将结构性修改计数器加一；然后判断minCapacity与当前元素数组的长度的大小，如果minCapacity比当前元素数组的长度的大小大的时候需要扩容，进入第三阶段。

```java
private void ensureExplicitCapacity(int minCapacity) {
    // 结构性修改加1
        modCount++;
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```



第三，如果需要对现有的元素数组进行扩容，则调用grow(minCapacity)方法，参数minCapacity表示集合为了确保添加元素成功的最小容量。

在扩容的时候，**<u>首先将原元素数组的长度增大1.5倍</u>**（oldCapacity + (oldCapacity >> 1)），然后对扩容后的容量与minCapacity进行比较：① 新容量小于minCapacity，则将新容量设为minCapacity；②新容量大于minCapacity，则指定新容量。

最后将旧数组拷贝到扩容后的新数组中。

```java

private void grow(int minCapacity) {
    int oldCapacity = elementData.length; // 旧容量
    int newCapacity = oldCapacity + (oldCapacity >> 1); // 新容量为旧容量的1.5倍
    if (newCapacity - minCapacity < 0) // 新容量小于参数指定容量，修改新容量
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0) // 新容量大于最大容量
        newCapacity = hugeCapacity(minCapacity); // 指定新容量
    // 拷贝扩容
    elementData = Arrays.copyOf(elementData, newCapacity);
}


```

# 八、ArrayList的set(int index,E element)方法

set(int index, E element)方法的作用是***指定下标索引处的元素的值。***

在ArrayList的源码实现中，方法内首先判断传递的元素数组下标参数是否合法，

然后将原来的值取出，设置为新的值，**<u>将旧值作为返回值返回。</u>**

```java
public E set(int index, E element) {
    // 检验索引是否合法
    rangeCheck(index);
    // 旧值
    E oldValue = elementData(index);
    // 赋新值
    elementData[index] = element;
    // 返回旧值
    return oldValue;
}
```



# 九、ArrayList的indexOf(Object o)方法

indexOf(Object o)方法的作用是**从头开始**查找与指定元素相等的元素，如果找到，则返回找到的元素在元素数组中的下标，如果没有找到返回-1。

与该方法类似的是lastIndexOf(Object o)方法，该方法的作用**是从尾部开始**查找与指定元素相等的元素。

查看该方法的源码可知，该方法从需要查找的元素是否为空的角度分为两种情况分别讨论。这也意味着该方法的参数可以是null元素，也意味着ArrayList集合中能够保存null元素。

方法实现的逻辑也比较简单，直接循环遍历元素数组，通过equals方法来判断对象是否相同，相同就返回下标，找不到就返回-1。**这也解释了为什么要把情况分为需要查找的对象是否为空两种情况讨论，不然的话空对象调用equals方法则会产生空指针异常。**

```java
// 从首开始查找数组里面是否存在指定元素
public int indexOf(Object o) {
    if (o == null) { // 查找的元素为空
        for (int i = 0; i < size; i++) // 遍历数组，找到第一个为空的元素，返回下标
            if (elementData[i]==null)
                return i;
    } else { // 查找的元素不为空
        for (int i = 0; i < size; i++) // 遍历数组，找到第一个和指定元素相等的元素，返回下标
            if (o.equals(elementData[i]))
                    return i;
    } 
    // 没有找到，返回空
    return -1;
}
```

# 十、ArrayList的get(int index)方法

get(int index)方法是返回指定下标处的元素的值。

get函数会检查索引值是否合法（**只检查是否大于size，而没有检查是否小于0**）。如果所引致合法，则调用elementData(int index)方法获取值。在elementData(int index)方法中返回元素数组中指定下标的元素，并且对其进行了向下转型。

```java
public E get(int index) {
    // 检验索引是否合法
    rangeCheck(index);
 
    return elementData(index);
}
```

```java
E elementData(int index) {
    return (E) elementData[index];
}
```

# 十一、ArrayList的remove(int index)方法

remove(int index)方法的作用是删除指定下标的元素。

在该方法的源码中，将指定下标后面一位到数组末尾的全部元素向前移动一个单位，并且把数组最后一个元素设置为null，这样方便之后将整个数组不再使用时，会被GC，可以作为小技巧。而需要移动的元素个数为：size-index-1。

```java
public E remove(int index) {
    // 检查索引是否合法
    rangeCheck(index);
        
    modCount++;
    E oldValue = elementData(index);
    // 需要移动的元素的个数
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
    // 赋值为空，有利于进行GC
    elementData[--size] = null; 
    // 返回旧值
    return oldValue;
}
```

十二、ArrayList的优缺点
ArrayList的优点
ArrayList底层以数组实现，是一种随机访问模式，再加上它实现了RandomAccess接口，因此查找也就是get的时候非常快。
ArrayList在顺序添加一个元素的时候非常方便，只是往数组里面添加了一个元素而已。
根据下标遍历元素，效率高。
根据下标访问元素，效率高。
可以自动扩容，默认为每次扩容为原来的1.5倍。
ArrayList的缺点
插入和删除元素的效率不高。
根据元素下标查找元素需要遍历整个元素数组，效率不高。
线程不安全。
————————————————

# ArrayList源码分析

 System.arraycopy()和Arrays.copyOf()方法

　　通过上面源码我们发现这两个实现数组复制的方法被广泛使用而且很多地方都特别巧妙。比如下面<font color="red">add(int index, E element)</font>方法就很巧妙的用到了<font color="red">arraycopy()方法</font>让数组自己复制自己实现让index开始之后的所有成员后移一个位置:

```java 
    /**
     * 在此列表中的指定位置插入指定的元素。 
     *先调用 rangeCheckForAdd 对index进行界限检查；然后调用 ensureCapacityInternal 方法保证capacity足够大；
     *再将从index开始之后的所有成员后移一个位置；将element插入index位置；最后size加1。
     */
    public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // Increments modCount!!
        //arraycopy()方法实现数组自己复制自己
        //elementData:源数组;index:源数组中的起始位置;elementData：目标数组；index + 1：目标数组中的起始位置； size - index：要复制的数组元素的数量；
        System.arraycopy(elementData, index, elementData, index + 1, size - index);
        elementData[index] = element;
        size++;
    }
```

又如toArray()方法中用到了copyOf()方法

```java
    /**
     *以正确的顺序（从第一个到最后一个元素）返回一个包含此列表中所有元素的数组。 
     *返回的数组将是“安全的”，因为该列表不保留对它的引用。 （换句话说，这个方法必须分配一个新的数组）。
     *因此，调用者可以自由地修改返回的数组。 此方法充当基于阵列和基于集合的API之间的桥梁。
     */
    public Object[] toArray() {
    //elementData：要复制的数组；size：要复制的长度
        return Arrays.copyOf(elementData, size);
    }
```

**两者联系与区别**

**联系：**

看两者源代码可以发现`copyOf()`内部调用了`System.arraycopy()`方法

**区别：**

1. arraycopy()需要目标数组，将原数组拷贝到你自己定义的数组里，而且可以选择拷贝的起点和长度以及放入新数组中的位置
2. copyOf()是系统自动在内部新建一个数组，并返回该数组。