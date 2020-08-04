**哈希表的主干就是数组**。

在讨论哈希表之前，我们先大概了解下其他数据结构在新增，查找等基础操作执行性能

**数组**：采用一段连续的存储单元来存储数据。对于指定下标的查找，时间复杂度为O(1)；通过给定值进行查找，需要遍历数组，逐一比对给定关键字和数组元素，时间复杂度为O(n)，当然，对于有序数组，则可采用二分查找，插值查找，斐波那契查找等方式，可将查找复杂度提高为O(logn)；对于一般的插入删除操作，涉及到数组元素的移动，其平均复杂度也为O(n)

**线性链表**：对于链表的新增，删除等操作（在找到指定操作位置后），仅需处理结点间的引用即可，时间复杂度为O(1)，而查找操作需要遍历链表逐一进行比对，复杂度为O(n)

**二叉树**：对一棵相对平衡的有序二叉树，对其进行插入，查找，删除等操作，平均复杂度均为O(logn)。

**哈希表**：相比上述几种数据结构，在哈希表中进行添加，删除，查找等操作，性能十分之高，不考虑哈希冲突的情况下（后面会探讨下哈希冲突的情况），仅需一次定位即可完成，时间复杂度为O(1)，接下来我们就来看看哈希表是如何实现达到惊艳的常数阶O(1)的。

**比如我们要新增或查找某个元素，我们通过把当前元素的关键字 通过某个函数映射到数组中的某个位置，通过数组下标一次定位就可完成操作。**

这个函数可以简单描述为：**存储位置 = f(关键字)** ，这个函数f一般称为哈希函数，这个函数的设计好坏会直接影响到哈希表的优劣。举个例子，比如我们要在哈希表中执行插入操作：
插入过程如下图所示
![哈希表数据插入过程](https://img-blog.csdnimg.cn/2018110221063296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvc2hpbWF4aWFvMQ==,size_16,color_FFFFFF,t_70)

查找操作同理，先通过哈希函数计算出实际存储地址，然后从数组中对应地址取出即可

哈希冲突如何解决呢？哈希冲突的解决方案有多种:开放定址法（发生冲突，继续寻找下一块未被占用的存储地址），再散列函数法，链地址法，而HashMap即是采用了**链地址法**，也就是**数组+链表**的方式。

# 二、HashMap的实现原理

HashMap的主干是一个Entry数组。Entry是HashMap的基本组成单元，每一个Entry包含一个key-value键值对。（其实所谓Map其实就是保存了两个对象之间的映射关系的一种集合）

```java
//HashMap的主干数组，可以看到就是一个Entry数组，初始值为空数组{}，主干数组的长度一定是2的次幂。
//至于为什么这么做，后面会有详细分析。
transient Entry<K,V>[] table = (Entry<K,V>[]) EMPTY_TABLE;

```

```java
    static class Entry<K,V> implements Map.Entry<K,V> {
        final K key;
        V value;
        Entry<K,V> next;//存储指向下一个Entry的引用，单链表结构
        int hash;//对key的hashcode值进行hash运算后得到的值，存储在Entry，避免重复计算

        /**
         * Creates new entry.
         */
        Entry(int h, K k, V v, Entry<K,V> n) {
            value = v;
            next = n;
            key = k;
            hash = h;
        } 

```

![img](https://img-blog.csdnimg.cn/20181102221702492.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvc2hpbWF4aWFvMQ==,size_16,color_FFFFFF,t_70)

简单来说，**HashMap由数组+链表组成的**，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的，如果定位到的数组位置不含链表（当前entry的next指向null）,那么查找，添加等操作很快，仅需一次寻址即可；

如果定位到的数组包含链表，对于添加操作，其时间复杂度为O(n)，首先遍历链表，**存在即覆盖，否则新增**；对于查找操作来讲，仍需遍历链表，然后通过key对象的equals方法逐一比对查找。所以，性能考虑，**HashMap中的链表出现越少，性能才会越好。**

```java
/**实际存储的key-value键值对的个数*/
transient int size;

/**阈值，当table == {}时，该值为初始容量（初始容量默认为16）；当table被填充了，也就是为table分配内存空间后，
threshold一般为 capacity*loadFactory。HashMap在进行扩容时需要参考threshold，后面会详细谈到*/
int threshold;

/**负载因子，代表了table的填充度有多少，默认是0.75
加载因子存在的原因，还是因为减缓哈希冲突，如果初始桶为16，等到满16个元素才扩容，某些桶里可能就有不止一个元素了。
所以加载因子默认为0.75，也就是说大小为16的HashMap，到了第13个元素，就会扩容成32。
*/
final float loadFactor;

/**HashMap被改变的次数，
由于HashMap非线程安全，在对HashMap进行迭代时，
如果期间其他线程的参与导致HashMap的结构发生变化了（比如put，remove等操作），
需要抛出异常ConcurrentModificationException*/
transient int modCount;

```

HashMap有4个构造器，其他构造器如果用户没有传入initialCapacity 和loadFactor这两个参数，会使用默认值

initialCapacity默认为16，loadFactory默认为0.75

我们看下其中一个

```java
public HashMap(int initialCapacity, float loadFactor) {
　　　　　//此处对传入的初始容量进行校验，最大不能超过MAXIMUM_CAPACITY = 1<<30(230)
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);

        this.loadFactor = loadFactor;
        threshold = initialCapacity;
　　　　　
        init();//init方法在HashMap中没有实际实现，不过在其子类如 linkedHashMap中就会有对应实现
    }

```

从上面这段代码我们可以看出，在常规构造器中，没有为数组table分配内存空间（有一个入参为指定Map的构造器例外），**而是在执行put操作的时候才真正构建table数组**

put:

```java
public V put(K key, V value) {
        //如果table数组为空数组{}，进行数组填充（为table分配实际内存空间），入参为threshold，
        //此时threshold为initialCapacity 默认是1<<4(24=16)
        if (table == EMPTY_TABLE) {
            inflateTable(threshold);
        }
       //如果key为null，存储位置为table[0]或table[0]的冲突链上
        if (key == null)
            return putForNullKey(value);
        int hash = hash(key);//对key的hashcode进一步计算，确保散列均匀
        int i = indexFor(hash, table.length);//获取在table中的实际位置
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
        //如果该对应数据已存在，执行覆盖操作。用新value替换旧value，并返回旧value
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
        modCount++;//保证并发访问时，若HashMap内部结构发生变化，快速响应失败
        addEntry(hash, key, value, i);//新增一个entry
        return null;
    }

```

//空时，数组填充

```java
private void inflateTable(int toSize) {
        int capacity = roundUpToPowerOf2(toSize);//capacity一定是2的次幂
        /**此处为threshold赋值，取capacity*loadFactor和MAXIMUM_CAPACITY+1的最小值，
        capaticy一定不会超过MAXIMUM_CAPACITY，除非loadFactor大于1 */
        threshold = (int) Math.min(capacity * loadFactor, MAXIMUM_CAPACITY + 1);
        table = new Entry[capacity];
        initHashSeedAsNeeded(capacity);
    }

```

```java

 private static int roundUpToPowerOf2(int number) {
        // assert number >= 0 : "number must be non-negative";
        return number >= MAXIMUM_CAPACITY
                ? MAXIMUM_CAPACITY
                : (number > 1) ? Integer.highestOneBit((number - 1) << 1) : 1;
    }


```

```java
/**这是一个神奇的函数，用了很多的异或，移位等运算
对key的hashcode进一步进行计算以及二进制位的调整等来保证最终获取的存储位置尽量分布均匀*/
final int hash(Object k) {
        int h = hashSeed;
        if (0 != h && k instanceof String) {
            return sun.misc.Hashing.stringHash32((String) k);
        }

        h ^= k.hashCode();

        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }

```

##### 关于HashMap中的IndexOf方法为什么用&,并且和length-1做运算

static int indexFor(int h, int length) {  
     return h & (length-1);  
 } 

##### 前提

首先大家知道普通的Hash打散的算法都是mod表的长度，比如h%length,但是HashMap却用的是位运算

##### 分析

HashMap的初始大小和扩容都是以2的次方来进行的，换句话说length-1换成二进制永远是全部为1，比如容量为16，则length-1为1111，大家知道位运算的'&'规则是两个1才得1，遇0得0，也就是说length-1中的某一位为1，则对应位置的计算结果才取决于h中的对应位置（h中对应位取0,对应位结果为0，h对应位取1，对应位结果为1。这样就有两个结果），但是如果length-1中某一位为0，则不论h中对应位的数字为几，对应位结果都是0，这样就让两个h取到同一个结果，这就是hash冲突了，恰恰length-1又是全部为1的数，所以结果自然就将hash冲突最小化了

##### 对比

仔细观察可以发现其实老方法h%length与h&（length-1）得到的结果其实是一个值，但是为什么hashmap中要用后者呢
1.length（2的整数次幂）的特殊性导致了length-1的特殊性（二进制全为1）
2.位运算快于十进制运算，hashmap扩容也是按位扩容，所以相比较就选择了后者





```java

/**
     * 返回数组下标
     */
    static int indexFor(int h, int length) {
        return h & (length-1);
    }

```

![HashMap如何确定元素位置](https://img-blog.csdnimg.cn/20181102214046362.png)

```java
void addEntry(int hash, K key, V value, int bucketIndex) {
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);//当size超过临界阈值threshold，并且即将发生哈希冲突时进行扩容
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }

        createEntry(hash, key, value, bucketIndex);
    }

```

通过以上代码能够得知，**当发生哈希冲突并且size大于阈值的时候，需要进行数组扩容，扩容时，需要新建一个长度为之前数组2倍的新的数组，然后将当前的Entry数组中的元素全部传输过去，扩容后的新数组长度为之前的2倍，所以扩容相对来说是个耗资源的操作。**

**三、为何HashMap的数组长度一定是2的次幂？**

我们来继续看上面提到的resize方法

```java
void resize(int newCapacity) {
        Entry[] oldTable = table;
        int oldCapacity = oldTable.length;
        if (oldCapacity == MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return;
        }

        Entry[] newTable = new Entry[newCapacity];
        transfer(newTable, initHashSeedAsNeeded(newCapacity));
        table = newTable;
        threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
    }

```

get方法：

```java
 public V get(Object key) {
　　　　 //如果key为null,则直接去table[0]处去检索即可。
        if (key == null)
            return getForNullKey();
        Entry<K,V> entry = getEntry(key);
        return null == entry ? null : entry.getValue();
 }

```

```java
final Entry<K,V> getEntry(Object key) {
            
        if (size == 0) {
            return null;
        }
        //通过key的hashcode值计算hash值
        int hash = (key == null) ? 0 : hash(key);
        //indexFor (hash&length-1) 获取最终数组索引，然后遍历链表，通过equals方法比对找出对应记录
        for (Entry<K,V> e = table[indexFor(hash, table.length)];
             e != null;
             e = e.next) {
            Object k;
            if (e.hash == hash && 
                ((k = e.key) == key || (key != null && key.equals(k))))
                return e;
        }
        return null;
    }    

```

可以看出，get方法的实现相对简单，key(hashcode)–>hash–>indexFor–>最终索引位置，找到对应位置table[i]，再查看是否有链表，遍历链表，通过key的equals方法比对查找对应的记录。要注意的是，有人觉得上面在定位到数组位置之后然后遍历链表的时候，e.hash == hash这个判断没必要，仅通过equals判断就可以。其实不然，试想一下，如果传入的key对象重写了equals方法却没有重写hashCode，而恰巧此对象定位到这个数组位置，如果仅仅用equals判断可能是相等的，但其hashCode和当前对象不一致，这种情况，根据Object的hashCode的约定，不能返回当前对象，而应该返回null，后面的例子会做出进一步解释。

**四、重写equals方法需同时重写hashCode方法**

最后我们再聊聊老生常谈的一个问题，各种资料上都会提到，“重写equals时也要同时覆盖hashcode”，我们举个小例子来看看，如果重写了equals而不重写hashcode会发生什么样的问题

```java

public class MyTest {
    private static class Person{
        int idCard;
        String name;

        public Person(int idCard, String name) {
            this.idCard = idCard;
            this.name = name;
        }
        @Override
        public boolean equals(Object o) {
            if (this == o) {
                return true;
            }
            if (o == null || getClass() != o.getClass()){
                return false;
            }
            Person person = (Person) o;
            //两个对象是否等值，通过idCard来确定
            return this.idCard == person.idCard;
        }

    }
    public static void main(String []args){
        HashMap<Person,String> map = new HashMap<Person, String>();
        Person person = new Person(1234,"乔峰");
        //put到hashmap中去
        map.put(person,"天龙八部");
        //get取出，从逻辑上讲应该能输出“天龙八部”
        System.out.println("结果:"+map.get(new Person(1234,"萧峰")));
    }
}

实际输出结果：null

```

JDK1.8在JDK1.7的基础上针对增加了红黑树来进行优化。即当链表超过8时，链表就转换为红黑树，利用红黑树快速增删改查的特点提高HashMap的性能，其中会用到红黑树的插入、删除、查找等算法。
关于这方面的探讨我们以后的文章再做说明。

![img](https://img-blog.csdnimg.cn/20181105181728652.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvc2hpbWF4aWFvMQ==,size_16,color_FFFFFF,t_70)

# LinkedHashMap

## 2.1 应用场景

<u>HashMap是无序的，**当我们希望有顺序地去存储key-value时，就需要使用LinkedHashMap了。**</u>



```dart
        Map<String, String> hashMap = new HashMap<String, String>();
        hashMap.put("name1", "josan1");
        hashMap.put("name2", "josan2");
        hashMap.put("name3", "josan3");
        Set<Entry<String, String>> set = hashMap.entrySet();
        Iterator<Entry<String, String>> iterator = set.iterator();
        while(iterator.hasNext()) {
            Entry entry = iterator.next();
            String key = (String) entry.getKey();
            String value = (String) entry.getValue();
            System.out.println("key:" + key + ",value:" + value);
        }
```

![img](https:////upload-images.jianshu.io/upload_images/4843132-1c5470069f9626e5.png?imageMogr2/auto-orient/strip|imageView2/2/w/335/format/webp)

image.png

我们是按照xxx1、xxx2、xxx3的顺序插入的，但是输出结果并不是按照顺序的。

同样的数据，我们再试试LinkedHashMap



```dart
        Map<String, String> linkedHashMap = new LinkedHashMap<>();
        linkedHashMap.put("name1", "josan1");
        linkedHashMap.put("name2", "josan2");
        linkedHashMap.put("name3", "josan3");
        Set<Entry<String, String>> set = linkedHashMap.entrySet();
        Iterator<Entry<String, String>> iterator = set.iterator();
        while(iterator.hasNext()) {
            Entry entry = iterator.next();
            String key = (String) entry.getKey();
            String value = (String) entry.getValue();
            System.out.println("key:" + key + ",value:" + value);
        }
```

![img](https:////upload-images.jianshu.io/upload_images/4843132-2688396af2d206d1.png?imageMogr2/auto-orient/strip|imageView2/2/w/335/format/webp)

image.png



结果可知，LinkedHashMap是有序的，且默认为插入顺序。

## 2.2 简单使用

跟HashMap一样，它也是提供了key-value的存储方式，并提供了put和get方法来进行数据存取。



```dart
        LinkedHashMap<String, String> linkedHashMap = new LinkedHashMap<>();
        linkedHashMap.put("name", "josan");
        String name = linkedHashMap.get("name");
```

## 2.3 定义

LinkedHashMap继承了HashMap，所以它们有很多相似的地方。



```java
public class LinkedHashMap<K,V>
    extends HashMap<K,V>
    implements Map<K,V>
{
```

## 2.4 构造方法

![img](https:////upload-images.jianshu.io/upload_images/4843132-3ec0494c6b87c52f.png?imageMogr2/auto-orient/strip|imageView2/2/w/330/format/webp)

image.png



LinkedHashMap提供了多个构造方法，我们先看空参的构造方法。

### LinkedHashMap():accessOrder

```java
    public LinkedHashMap() {
        // 调用HashMap的构造方法，其实就是初始化Entry[] table
        super();
        // 这里是指是否基于访问排序，默认为false
        accessOrder = false;
    }
```

首先使用super调用了父类HashMap的构造方法，其实就是根据初始容量、负载因子去初始化Entry[] table，详细的看上一篇[HashMap解析](https://www.jianshu.com/p/dde9b12343c1)。

然后把accessOrder设置为false，这就跟存储的顺序有关了，LinkedHashMap存储数据是有序的，而且分为两种：插入顺序和访问顺序。

***这里accessOrder设置为false，***

***表示不是访问顺序而是插入顺序存储的，*这也是默认值，**

**表示LinkedHashMap中存储的顺序是按照调用put方法插入的顺序进行排序的。LinkedHashMap也提供了可以设置accessOrder的构造方法，我们来看看这种模式下，它的顺序有什么特点？**



```dart
                // 第三个参数用于指定accessOrder值
        Map<String, String> linkedHashMap = new LinkedHashMap<>(16, 0.75f, true);
        linkedHashMap.put("name1", "josan1");
        linkedHashMap.put("name2", "josan2");
        linkedHashMap.put("name3", "josan3");
        System.out.println("开始时顺序：");
        Set<Entry<String, String>> set = linkedHashMap.entrySet();
        Iterator<Entry<String, String>> iterator = set.iterator();
        while(iterator.hasNext()) {
            Entry entry = iterator.next();
            String key = (String) entry.getKey();
            String value = (String) entry.getValue();
            System.out.println("key:" + key + ",value:" + value);
        }
        System.out.println("通过get方法，导致key为name1对应的Entry到表尾");
        linkedHashMap.get("name1");
        Set<Entry<String, String>> set2 = linkedHashMap.entrySet();
        Iterator<Entry<String, String>> iterator2 = set2.iterator();
        while(iterator2.hasNext()) {
            Entry entry = iterator2.next();
            String key = (String) entry.getKey();
            String value = (String) entry.getValue();
            System.out.println("key:" + key + ",value:" + value);
        }
```

![img](https:////upload-images.jianshu.io/upload_images/4843132-442ce33c7124092c.png?imageMogr2/auto-orient/strip|imageView2/2/w/342/format/webp)

image.png



因为调用了get("name1")导致了name1对应的Entry移动到了最后，

**<u>这里只要知道LinkedHashMap有插入顺序和访问顺序两种就可以，</u>**

后面会详细讲原理。

### init()

还记得，上一篇HashMap解析中提到，在HashMap的构造函数中，调用了init方法，而在HashMap中init方法是空实现，但LinkedHashMap重写了该方法，所以在LinkedHashMap的构造方法里，调用了自身的init方法，init的重写实现如下：



```dart
    /**
     * Called by superclass constructors and pseudoconstructors (clone,
     * readObject) before any entries are inserted into the map.  Initializes
     * the chain.
     */
    @Override
    void init() {
        // 创建了一个hash=-1，key、value、next都为null的Entry
        header = new Entry<>(-1, null, null, null);
        // 让创建的Entry的before和after都指向自身，注意after不是之前提到的next
        // 其实就是创建了一个只有头部节点的双向链表
        header.before = header.after = header;
    }
```

这好像跟我们上一篇HashMap提到的Entry有些不一样，HashMap中静态内部类Entry是这样定义的：



```dart
    static class Entry<K,V> implements Map.Entry<K,V> {
        final K key;
        V value;
        Entry<K,V> next;
        int hash;
```

没有before和after属性啊！

原来，LinkedHashMap有自己的静态内部类Entry，它继承了HashMap.Entry，

定义如下:

### Entry

```cpp
    /**
     * LinkedHashMap entry.
     */
    private static class Entry<K,V> extends HashMap.Entry<K,V> {
        // These fields comprise the doubly linked list used for iteration.
        Entry<K,V> before, after;

        Entry(int hash, K key, V value, HashMap.Entry<K,V> next) {
            super(hash, key, value, next);
        }
```

**所以LinkedHashMap构造函数，主要就是调用HashMap<u>构造函数</u>初始化了一个Entry[] table，然后调用自身的init初始化了一个只有头结点的双向链表。完成了如下操作：**



![img](https:////upload-images.jianshu.io/upload_images/4843132-cac0b65e4d23b6bd.png?imageMogr2/auto-orient/strip|imageView2/2/w/524/format/webp)

LinkedHashMap构造函数.png

## 2.5 put方法

LinkedHashMap没有重写put方法，所以还是调用HashMap得到put方法，如下：



```csharp
    public V put(K key, V value) {
        // 对key为null的处理
        if (key == null)
            return putForNullKey(value);
        // 计算hash
        int hash = hash(key);
        // 得到在table中的index
        int i = indexFor(hash, table.length);
        // 遍历table[index]，是否key已经存在，存在则替换，并返回旧值
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
        
        modCount++;
        // 如果key之前在table中不存在，则调用addEntry，LinkedHashMap重写了该方法
        addEntry(hash, key, value, i);
        return null;
    }
```

我们看看LinkedHashMap的addEntry方法：

### addEntry

```csharp
    void addEntry(int hash, K key, V value, int bucketIndex) {
        // 调用父类的addEntry，增加一个Entry到HashMap中
        super.addEntry(hash, key, value, bucketIndex);

        // removeEldestEntry方法默认返回false，不用考虑
        Entry<K,V> eldest = header.after;
        if (removeEldestEntry(eldest)) {
            removeEntryForKey(eldest.key);
        }
    }
```

这里调用了父类HashMap的addEntry方法，如下：



```csharp
    void addEntry(int hash, K key, V value, int bucketIndex) {
        // 扩容相关
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }
        // LinkedHashMap进行了重写
        createEntry(hash, key, value, bucketIndex);
    }
```

前面是扩容相关的代码，在上一篇HashMap解析中已经讲过了。这里主要看createEntry方法，LinkedHashMap进行了重写。

head是双向链表，存在是为了保证顺序。

```csharp
   void createEntry(int hash, K key, V value, int bucketIndex) {
       HashMap.Entry<K,V> old = table[bucketIndex];
       // e就是新创建了Entry，会加入到table[bucketIndex]的表头
       Entry<K,V> e = new Entry<>(hash, key, value, old);
       table[bucketIndex] = e;
       // 把新创建的Entry，加入到双向链表中
       e.addBefore(header);
       size++;
   }
```

我们来看看LinkedHashMap.Entry的addBefore方法：



```cpp
        private void addBefore(Entry<K,V> existingEntry) {
            after  = existingEntry;
            before = existingEntry.before;
            before.after = this;
            after.before = this;
        }
```

从这里就可以看出，当put元素时，不但要把它加入到HashMap中去，还要加入到双向链表中，所以可以看出LinkedHashMap就是HashMap+双向链表，下面用图来表示逐步往LinkedHashMap中添加数据的过程，红色部分是双向链表，黑色部分是HashMap结构，

**<u>header是一个Entry类型的双向链表表头，本身不存储数据。</u>**

首先是只加入一个元素Entry1，假设index为0：



![img](https:////upload-images.jianshu.io/upload_images/4843132-d48bb58775418c95.png?imageMogr2/auto-orient/strip|imageView2/2/w/475/format/webp)

LinkedHashMap结构一个元素.png

当再加入一个元素Entry2，假设index为15：



![img](https:////upload-images.jianshu.io/upload_images/4843132-10c917166c1f4745.png?imageMogr2/auto-orient/strip|imageView2/2/w/525/format/webp)

LinkedHashMap结构两个元素.png

当再加入一个元素Entry3, 假设index也是0：



![img](https:////upload-images.jianshu.io/upload_images/4843132-32fb46d33b0ed3c0.png?imageMogr2/auto-orient/strip|imageView2/2/w/555/format/webp)

LinkedHashMap结构三个元素.png



以上，就是LinkedHashMap的put的所有过程了，总体来看，跟HashMap的put类似，只不过多了把新增的Entry加入到双向列表中。

## 2.6 扩容

在HashMap的put方法中，如果发现前元素个数超过了扩容阀值时，会调用resize方法，如下：



```java
    void resize(int newCapacity) {
        Entry[] oldTable = table;
        int oldCapacity = oldTable.length;
        if (oldCapacity == MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return;
        }

        Entry[] newTable = new Entry[newCapacity];
        boolean oldAltHashing = useAltHashing;
        useAltHashing |= sun.misc.VM.isBooted() &&
                (newCapacity >= Holder.ALTERNATIVE_HASHING_THRESHOLD);
        boolean rehash = oldAltHashing ^ useAltHashing;
       // 把旧table的数据迁移到新table
        transfer(newTable, rehash);
        table = newTable;
        threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
    }
```

LinkedHashMap重写了transfer方法，数据的迁移，它的实现如下：



```java
    void transfer(HashMap.Entry[] newTable, boolean rehash) {
        // 扩容后的容量是之前的2倍
        int newCapacity = newTable.length;
        // 遍历双向链表，把所有双向链表中的Entry，重新就算hash，并加入到新的table中
        for (Entry<K,V> e = header.after; e != header; e = e.after) {
            if (rehash)
                e.hash = (e.key == null) ? 0 : hash(e.key);
            int index = indexFor(e.hash, newCapacity);
            e.next = newTable[index];
            newTable[index] = e;
        }
    }
```

可以看出，LinkedHashMap扩容时，数据的再散列和HashMap是不一样的。

HashMap是先遍历旧table，再遍历旧table中每个元素的单向链表，取得Entry以后，重新计算hash值，然后存放到新table的对应位置。

LinkedHashMap是遍历的双向链表，取得每一个Entry，然后重新计算hash值，然后存放到新table的对应位置。

从遍历的效率来说，遍历双向链表的效率要高于遍历table，因为遍历双向链表是N次（N为元素个数）；而遍历table是N+table的空余个数（N为元素个数）。

## 2.7 双向链表的重排序



前面分析的，主要是当前LinkedHashMap中不存在当前key时，新增Entry的情况。

当key如果已经存在时，则进行更新Entry的value。就是HashMap的put方法中的如下代码：



```csharp
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                // 重排序
                e.recordAccess(this);
                return oldValue;
            }
        }
```

主要看e.recordAccess(this)，这个方法跟访问顺序有关，而HashMap是无序的，所以在HashMap.Entry的recordAccess方法是空实现，但是LinkedHashMap是有序的,LinkedHashMap.Entry对recordAccess方法进行了重写。

```csharp
       void recordAccess(HashMap<K,V> m) {
            LinkedHashMap<K,V> lm = (LinkedHashMap<K,V>)m;
            // 如果LinkedHashMap的accessOrder为true，则进行重排序
            // 比如前面提到LruCache中使用到的LinkedHashMap的accessOrder属性就为true
            if (lm.accessOrder) {
                lm.modCount++;
                // 把更新的Entry从双向链表中移除
                remove();
                // 再把更新的Entry加入到双向链表的表尾
                addBefore(lm.header);
            }
        }
```

在LinkedHashMap中，只有accessOrder为true，即是访问顺序模式，才会put时对更新的Entry进行重新排序，而如果是插入顺序模式时，不会重新排序，这里的排序跟在HashMap中存储没有关系，只是指在双向链表中的顺序。

举个栗子：开始时，HashMap中有Entry1、Entry2、Entry3，并设置LinkedHashMap为访问顺序，则更新Entry1时，会先把Entry1从双向链表中删除，然后再把Entry1加入到双向链表的表尾，而Entry1在HashMap结构中的存储位置没有变化，对比图如下所示：

## 2.8 get方法

LinkedHashMap有对get方法进行了重写，如下：



```kotlin
    public V get(Object key) {
        // 调用genEntry得到Entry
        Entry<K,V> e = (Entry<K,V>)getEntry(key);
        if (e == null)
            return null;
        // 如果LinkedHashMap是访问顺序的，则get时，也需要重新排序
        e.recordAccess(this);
        return e.value;
    }
```

先是调用了getEntry方法，通过key得到Entry，而LinkedHashMap并没有重写getEntry方法，所以调用的是HashMap的getEntry方法，在上一篇文章中我们分析过HashMap的getEntry方法：首先通过key算出hash值，然后根据hash值算出在table中存储的index，然后遍历table[index]的单向链表去对比key，如果找到了就返回Entry。

后面调用了LinkedHashMap.Entry的recordAccess方法，上面分析过put过程中这个方法，其实就是在访问顺序的LinkedHashMap进行了get操作以后，重新排序，把get的Entry移动到双向链表的表尾。

## 2.9 遍历方式取数据

我们先来看看HashMap使用遍历方式取数据的过程：



![img](https:////upload-images.jianshu.io/upload_images/4843132-51c76a3e45566c32.png?imageMogr2/auto-orient/strip|imageView2/2/w/552)

HashMap遍历.png

很明显，这样取出来的Entry顺序肯定跟插入顺序不同了，既然LinkedHashMap是有序的，那么它是怎么实现的呢？
先看看LinkedHashMap取遍历方式获取数据的代码：



```dart
        Map<String, String> linkedHashMap = new LinkedHashMap<>();
        linkedHashMap.put("name1", "josan1");
        linkedHashMap.put("name2", "josan2");
        linkedHashMap.put("name3", "josan3");
                // LinkedHashMap没有重写该方法，调用的HashMap中的entrySet方法
        Set<Entry<String, String>> set = linkedHashMap.entrySet();
        Iterator<Entry<String, String>> iterator = set.iterator();
        while(iterator.hasNext()) {
            Entry entry = iterator.next();
            String key = (String) entry.getKey();
            String value = (String) entry.getValue();
            System.out.println("key:" + key + ",value:" + value);
        }
```

LinkedHashMap没有重写entrySet方法，我们先来看HashMap中的entrySet，如下：



```dart
public Set<Map.Entry<K,V>> entrySet() {
        return entrySet0();
    }

    private Set<Map.Entry<K,V>> entrySet0() {
        Set<Map.Entry<K,V>> es = entrySet;
        return es != null ? es : (entrySet = new EntrySet());
    }

    private final class EntrySet extends AbstractSet<Map.Entry<K,V>> {
        public Iterator<Map.Entry<K,V>> iterator() {
            return newEntryIterator();
        }
        // 无关代码
        ......
    }
```

可以看到，HashMap的entrySet方法，其实就是返回了一个EntrySet对象。

我们得到EntrySet会调用它的iterator方法去得到迭代器Iterator，从上面的代码也可以看到，iterator方法中直接调用了newEntryIterator方法并返回，而LinkedHashMap重写了该方法



```dart
    Iterator<Map.Entry<K,V>> newEntryIterator() { 
        return new EntryIterator();
    }
```

这里直接返回了EntryIterator对象，这个和上一篇HashMap中的newEntryIterator方法中一模一样，都是返回了EntryIterator对象，其实他们返回的是各自的内部类。我们来看看LinkedHashMap中EntryIterator的定义：



```java
    private class EntryIterator extends LinkedHashIterator<Map.Entry<K,V>> {
        public Map.Entry<K,V> next() { 
          return nextEntry();
        }
    }
```

该类是继承LinkedHashIterator，并重写了next方法；而HashMap中是继承HashIterator。
我们再来看看LinkedHashIterator的定义：



```java
    private abstract class LinkedHashIterator<T> implements Iterator<T> {
        // 默认下一个返回的Entry为双向链表表头的下一个元素
        Entry<K,V> nextEntry    = header.after;
        Entry<K,V> lastReturned = null;

        public boolean hasNext() {
            return nextEntry != header;
        }

        Entry<K,V> nextEntry() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
            if (nextEntry == header)
                throw new NoSuchElementException();

            Entry<K,V> e = lastReturned = nextEntry;
            nextEntry = e.after;
            return e;
        }
        // 不相关代码
        ......
    }
```

我们先不看整个类的实现，只要知道在LinkedHashMap中，Iterator<Entry<String, String>> iterator = set.iterator()，这段代码会返回一个继承LinkedHashIterator的Iterator，它有着跟HashIterator不一样的遍历规则。

接着，我们会用while(iterator.hasNext())去循环判断是否有下一个元素，LinkedHashMap中的EntryIterator没有重写该方法，所以还是调用LinkedHashIterator中的hasNext方法，如下：



```java
        public boolean hasNext() {
            // 下一个应该返回的Entry是否就是双向链表的头结点
            // 有两种情况：1.LinkedHashMap中没有元素；2.遍历完双向链表回到头部
            return nextEntry != header;
        }
```

nextEntry表示下一个应该返回的Entry，默认值是header.after，即双向链表表头的下一个元素。而上面介绍到，LinkedHashMap在初始化时，会调用init方法去初始化一个before和after都指向自身的Entry，但是put过程会把新增加的Entry加入到双向链表的表尾，所以只要LinkedHashMap中有元素，第一次调用hasNext肯定不会为false。

然后我们会调用next方法去取出Entry，LinkedHashMap中的EntryIterator重写了该方法，如下：



```csharp
 public Map.Entry<K,V> next() { 
    return nextEntry(); 
}
```

而它自身又没有重写nextEntry方法，所以还是调用的LinkedHashIterator中的nextEntry方法：



```csharp
        Entry<K,V> nextEntry() {
            // 保存应该返回的Entry
            Entry<K,V> e = lastReturned = nextEntry;
            //把当前应该返回的Entry的after作为下一个应该返回的Entry
            nextEntry = e.after;
            // 返回当前应该返回的Entry
            return e;
        }
```

这里其实遍历的是双向链表，所以不会存在HashMap中需要寻找下一条单向链表的情况，从头结点Entry header的下一个节点开始，只要把当前返回的Entry的after作为下一个应该返回的节点即可。直到到达双向链表的尾部时，after为双向链表的表头节点Entry header，这时候hasNext就会返回false，表示没有下一个元素了。LinkedHashMap的遍历取值如下图所示：



![img](https:////upload-images.jianshu.io/upload_images/4843132-87fc7def1002acd4.png?imageMogr2/auto-orient/strip|imageView2/2/w/548)

LinkedHashMap遍历.png



易知，遍历出来的结果为Entry1、Entry2...Entry6。
可得，LinkedHashMap是有序的，且是通过双向链表来保证顺序的。



