## java.util.Map：映射关系（key-value）

### 1：Interface Map<K,V>：一种映射关系，将键 映射到 值的对象，存储键和值这样的双列数据的集合

######  特点：一个映射不能包含重复的键,每个键最多只能映射到一个值

K - 映射键的类型，泛型
V - 映射值的类型

static interface Map.Entry<K,V>  ：映射关系
	
  方法：	
V put(K key, V value)            -----------将指定的值与此映射中的指定键关联，想象成放入了集合
V get(Object key)            ---------------返回指定键所映射的值；如果此映射不包含该键的映射关系，则返回 null
V remove(Object key)             -----------如果存在一个键的映射关系，则将其从此映射中移除,返回值为被删除的value	

void clear()                    ------------从此映射中移除所有映射关系
boolean isEmpty()               ---------------如果此映射未包含键-值映射关系，则返回 true
int size()                      ------------返回此映射中的键-值映射关系数

Set<Map.Entry<K,V>> entrySet()  ---------------返回此映射中包含的映射关系的 Set集合
Set<K> keySet()                 ---------------返回此映射中只包含的键的 Set 集合
Collection<V>  values()         ---------------返回此映射中包含的值的collection集合

boolean containsKey(Object key) ---------------如果此映射包含指定键的映射关系，则返回 true 
boolean containsValue(Object value) -----------如果此映射将一个或多个键映射到指定值，则返回 true

void putAll(Map<? extends K,? extends V> m) ---从指定映射中将所有映射关系复制到此映射中
         		

### 2：Class HashMap<K,V>：基于哈希表的 Map 接口的实现类,非线程安全的类，并且不保证映射的顺序，但是查找高效，许 null 值和 null 键的存在

public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable 

###### 1：实现原理

   HashMap的底层主要是基于（jdk1.8）数组,链表和红黑树来实现，它的查询速度快主要是因为它是通过计算hash值来决定存储的位置

jdk1.7:采用数组+链表

###### :2：hash冲突

   HashMap：通过key值的hashCode（）来计算hash值，在位与运算产生分布相对均匀的位置，如果存储的对象对多了，就有可能不同的对象所算出来的hash值是相同的，这就出现了所谓的hash冲突。解决hash冲突的方法有很多，HashMap底层是通过链表来解决hash冲突的，拉链法，桶的深度

  构造方法：	
	HashMap() ----------- 构造一个具有默认初始容量 (16) 和默认加载因子 (0.75) 的空 HashMap，二倍扩容
       	HashMap(int initialCapacity) ------构造一个带指定初始容量和默认加载因子 (0.75) 的空 HashMap
       	HashMap(int initialCapacity, float loadFactor) ----------构造一个带指定初始容量和加载因子的空 HashMap

HashMap(Map<? extends K,? extends V> m) -----------------构造一个映射关系与指定 Map 相同的新 HashMap

######   3：影响hashmap的性能：Capacity 和 loadFactor

当节点数大于(threshold)阀值就需要扩容，这个值的计算方式是capacity * load factor

###### :4：二倍扩容：HashMap扩容时  ：当前容量X2。在扩大容量时须要重新计算hash

产生一个新的数组把原来的数组赋值过去，在原来的数组的区间基础上的任意存储

######  5：HashMap 为什么初始容量为16

为了减少hash值的碰撞,需要实现一个尽量均匀分布的hash函数,在HashMap中通过利用key的hashcode值,来进行位运算
公式:index = e.hash & (newCap - 1)

反观长度16或者其他2的幂, length - 1的值是所有最后二进制位全为1,这种情况下,index 的结果等同于hashcode后几位的值，只要输入的hashcode本身分布均匀,hash算法的结果就是均匀的，所以,HashMap的默认长度为16,是为了降低hash碰撞的几率

###### 6：Hash()，在Hash时用到

//用了很多的异或，移位等运算，对key的hashcode进一步进行计算以及二进制位的调整等来保证最终获取的存储位置尽量分布均匀

```java
final int hash(Object k) {
        int h = hashSeed;
        if (0 != h && k instanceof String) {
            return sun.misc.Hashing.stringHash32((String) k);
        }

	   h ^= k.hashCode();																 		   h ^= (h >>> 20) ^ (h >>> 12);
	   return h ^ (h >>> 7) ^ (h >>> 4);
}
```

###### 7：什么时候用红黑树什么时候用链表

在桶元素（桶的深度）超过8个并且表长超过64会将链表转化为红黑树，当红黑树中元素小于6个时、会将红黑树转化为链表

###### 8：为什么要引入红黑树

因为当两个线程同时进行put的操作时，刚好要扩容，一个线程扩容时休眠，另一个线程扩容，再hash完时，另一个线程继续，此时就导致环形链表

![](G:\Java\Java_note\4：泛型，集合，Map\HashMap循环链表.png)

###### 8：put操作（jdk 1.7 和jdk1.8）

```java

public V put(K key, V value) {
    // 当插入第一个元素的时候，需要先初始化数组大小
    if (table == EMPTY_TABLE) {
        inflateTable(threshold);
    }
    // 如果 key 为 null，感兴趣的可以往里看，最终会将这个 entry 放到 table[0] 中
    if (key == null)
        return putForNullKey(value);
    // 1. 求 key 的 hash 值
    int hash = hash(key);
    // 2. 找到对应的数组下标
    int i = indexFor(hash, table.length);
    // 3. 遍历一下对应下标处的链表，看是否有重复的 key 已经存在，
    //    如果有，直接覆盖，put 方法返回旧值就结束了
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
    // 4. 不存在重复的 key，将此 entry 添加到链表中，细节后面说
    addEntry(hash, key, value, i);
    return null;
}
```

jdk1.8 ，会在第一次put 的时候，会触发下面的 resize()，类似 java7 的第一次 put 也要初始化数组长度
第一次 resize 和后续的扩容有些不一样，因为这次是数组从 null 初始化到默认的 16 或自定义的初始容量

找到索引位置后，会取出一个值判断是否为红黑树节点

if` `(p instanceof TreeNode)
            ``e = ((TreeNode<K,V>)p).putTreeVal(``this, tab, hash, key, value);

否则插入，然后若是第9 个结点，会转变为红黑树，java，先插入，在扩容，插入采用尾插,jdk.1.7采用头插

###### 9：get操作（jdk 1.7 和jdk1.8）

jdk 1.7																																		根据 key 计算 hash 值
找到相应的数组下标：hash & (length – 1)
遍历该数组位置处的链表，直到找到相等(==或equals)的 key

```java
public V get(Object key) {
    // 之前说过，key 为 null 的话，会被放到 table[0]，所以只要遍历下 table[0] 处的链表就可以了
    if (key == null)
        return getForNullKey();
    // 
    Entry<K,V> entry = getEntry(key);
 
    return null == entry ? null : entry.getValue();
}
```

Jdk1.8

1. 计算 key 的 hash 值，根据 hash 值找到对应数组下标: hash & (length-1)
2. 判断数组该位置处的元素是否刚好就是我们要找的，如果不是，走第三步
3. 判断该元素类型是否是 TreeNode，如果是，用红黑树的方法取数据，如果不是，走第四步
4. 遍历链表，直到找到相等(==或equals)的 key

###### 方法：

V get(Object key)            ------------------返回指定键所映射的值；如果此映射不包含该键的映射关系，则返回 null
V put(K key, V value)          ----------------将指定的值与此映射中的指定键关联
       

boolean isEmpty()               ---------------如果此映射未包含键-值映射关系，则返回 true
V remove(Object key)             --------------如果存在一个键的映射关系，则将其从此映射中移除 

void clear()                    ---------------从此映射中移除所有映射关系
int size()                      ---------------返回此映射中的键-值映射关系数 
    

Set<K> keySet()               -----------------返回此映射中只包含的键的 Set 集合
Collection<V> values()   ----------------------返回此映射所包含的值的 Collection 视图 	
Set<Map.Entry<K,V>> entrySet()  ---------------返回此映射中包含的映射关系的 Set集合

boolean containsKey(Object key) ---------------如果此映射包含指定键的映射关系，则返回 true 
boolean containsValue(Object value) -----------如果此映射将一个或多个键映射到指定值，则返回 true?     	

### 3：Class LinkedHashMap<K,V>：为了解决hashmap不保证映射顺序的（无序）问题，迭代顺序

   public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V>

   HashMap和双向链表合二为一即是LinkedHashMap
   它是将所有Entry节点链入一个双向链表的HashMap,每次put进来Entry映射关系，除了将其保存到哈希表中对应的位置上之外，还会将其插入到双向链表的尾部，内部类额外增加的两个属性来维护的一个双向链表：before、After是用于维护Entry插入的先后顺序的

   next用于维护HashMap各个存储位置的Entry链，哈希冲突时 外部类新添加的两个属性Header,AccessOrder属性，当创建对象默认为False,使用插入实现有序，若传入ture，使用访问顺序进行有序 如果传入用来实现 LAU 算法

   没有额外增加方法

### 4：Class Hashtable<K,V> ：HashMap 的升级版，并发,多线程的情况下，使用Hashmap进行put操作会引起死循环,导致CPU利用率接近100%


public class Hashtable<K,V> extends Dictionary<K,V> implements Map<K,V>, Cloneable, java.io.Serializable 

###### 1：线程安全(synchronized)和非线程安全的

 Hashtable是线程安全给每个方法加了同步锁，所以在单线程环境下它比HashMap要慢，效率低，而HashMap没有

###### 2：支不支持null值和null键

HashTable不支持null值和null键,而HashMap是因为对null做了特殊处理，将null的hashCode值定为了0，从而将其存放在哈希表的第0个bucket中

###### 3：遍历方式不同：HashMap的迭代器(Iterator)是fail-fast迭代器，而Hashtable 是 enumerator迭代器

###### 4：初始容量和扩容机制不同

HashTable的初始容量是11，HashMap的初始容量是16.两者的填充因子默认都是0.75。
HashMap扩容时  ：当前容量X2。在扩大容量时须要重新计算hash
Hashtable扩容时：当前容量X2+1

### 5：ConcurrentHashMap：HashTable的升级版，HashTable容器使用synchronized来保证线程安全，但在线程竞争激烈的情况下HashTable的效率非常低下


当一个线程访问HashTable的同步方法时，其他线程访问HashTable的同步方法时，可能会进入阻塞或轮询状态

public class ConcurrentHashMap<K,V> extends AbstractMap<K,V> implements ConcurrentMap<K,V>, Serializable

######   JDK!.7实现 ConcurrentHashMap 的锁分段技术

将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问

HashTable容器在并发环境下表现出效率低下的原因，是因为所有访问HashTable的线程都必须竞争同一把锁，如果容器里有多把锁每一把锁用于锁容器其中一部分数据，那么当多线程访问容器里不同数据段的数据时，线程间就不会存在锁竞争，从而可以有效的提高并发访问效率，这就是ConcurrentHashMap所使用的锁分段技术结构
	由Segment(段)数组结构和HashEntry数组结构组成
	Segment是一种可重入锁(ReentrantLock)，在ConcurrentHashMap里扮演锁的角色，HashEntry则用于存储键值的映射关系

Segment的结构和HashMap类似，是一种数组和链表结构， 一个Segment里包含一个HashEntry数组，每个HashEntry是一个链表结构的元素,每个Segment守护着一个HashEntry数组里的元素,当对HashEntry数组的数据进行修改时，必须首先获得它对应的Segment锁

###### JDK1.8实现ConcurrentHashMap 

用Node数组+链表+红黑树的数据结构来实现，并发控制使用Synchronized和CAS来操作，整个看起来就像是优化过且线程安全的HashMap，虽然在JDK1.8中还能看到Segment的数据结构，但是已经简化了属性，只是为了兼容旧版本	



###### 默认是 16，也就是说 ConcurrentHashMap 有 16 个 Segments，所以理论上，这个时候，最多可以同时支持 16 个线程并发写，只要它们的操作分别分布在不同的 Segment 上。这个值可以在初始化的时候设置为其他值，但是一旦初始化以后，它是不可以扩容的

###### 构造函数进行初始化的，那么初始化完成后：

- Segment 数组长度为 16，不可以扩容
- Segment[i] 的默认大小为 2，负载因子是 0.75，得出初始阈值为 1.5，也就是以后插入第一个元素不会触发扩容，插入第二个会进行第一次扩容
- 这里初始化了 segment[0]，其他位置还是 null，至于为什么要初始化 segment[0]，后面的代码会介绍

###### 扩容机制：

segment 数组不能扩容，扩容是 segment 数组某个位置内部的数组 HashEntry\[] 进行扩容，扩容后，容量为原来的 2 倍



### 6：Interface SortedMap<K,V>：map的子接口，增加了排序的功能(comparator),TreeMap实现了它的继承接口

### 7：Class TreeMap<K,V>：该映射根据其键的自然顺序(升序)进行排序，或者根据创建映射时提供的 Comparator 进行排序

 TreeMap不支持null键，但是支持null值，排序时

 TreeMap本质是一颗红黑树,R-B Tree是一种二叉查找树，所有符合二叉查找树的特点


  构造方法：
	TreeMap()				    -------------使用键的自然顺序构造一个新的、空的树映射
        TreeMap(Comparator<? super K> comparator)   ------------ 构造一个新的、空的树映射，该映射根据给定比较器进行排序 
       	TreeMap(Map<? extends K,? extends V> m)     ------------ 构造一个与给定映射具有相同映射关系的新的树映射，
								 该映射根据其键的自然顺序 进行排序

  方法：提供一些对键值对进行关于顺序的操作方法

 	SortedMap<K,V> subMap(K fromKey, K toKey) --------------返回此映射的部分视图，其键值的范围从 fromKey（包括）到 toKey（不包括）

###### SortedMap<K,V> tailMap(K fromKey)        ---------------返回此映射的部分视图，其键大于等于 fromKey。 

Map.Entry<K,V> pollFirstEntry()          ---------------移除并返回与此映射中的最小键关联的键-值映射关系								        如果映射为空，则返回 null
Map.Entry<K,V> pollLastEntry()           ---------------移除并返回与此映射中的最大键关联的键-值映射关系；
如果映射为空，则返回 null

Set<K>	keySet()
Collection<V> values() 		
Set<Map.Entry<K,V>>	entrySet()

### 8：Map的遍历（三种）：转成Set集合，用迭代器遍历	

1：遍历key键，利用 Set<K> keySet()  ，返回的set集合
2：遍历所有的value，利用 Collection<V> values() ，返回collection集合
3：遍历键值对，利用 Set<Map.Entry<K,V>> entrySet() 方法，返回每一组键值对的set集合，
   	entry.getKey(), entry.getValue()



