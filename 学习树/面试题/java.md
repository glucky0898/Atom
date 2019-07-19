### 集合类

- HashMap  
   相关博客：[1.7jdk resize 过程] https://blog.csdn.net/qq_27093465/article/details/52270519  
   [1.8jdk resize 过程] https://blog.csdn.net/qq_27093465/article/details/52270519  
   [1.7jdk hashmap] https://blog.csdn.net/vking_wang/article/details/14166593  
   [1.8jdk hashmap] https://blog.csdn.net/u014026363/article/details/56342142  
   [为什么取长度为偶数] https://blog.csdn.net/qq_27093465/article/details/52207152  
   [红黑树]https://blog.csdn.net/v_july_v/article/details/6105630 https://www.cnblogs.com/longdouhzt/archive/2011/07/20/2112086.html

  > 存储模式:数组+链表(解决 hash 冲突：链地址法) 数组的下标等于 key 的 hash 值%数组长度 怎么判断是否应该覆盖原先值而不是：key 相等且 hash 值也相等 否则数组值放入链表 新值存数组中成为链头  
  > 链表过长 会增加数组长度 此时 hashmap 重新规划存储位置  
  > hashmap 可以存 key 为 null 当 hashtable 不可以  
  > 如果 key 相同，但是 hashcode 不同，那么 value 不会被覆盖 如果 key 相同，并且 hashCode 相同，那么 value 会被覆盖  
  > key 相同 hashcode 不一定相同【看 hashcode()方法的实现】 hashcode 相同 key 不一定相同

  - JDK 1.7 & 1.8
    > 1.7jdk resize 是增加数据长度 遍历旧数组全部 entry 进行 rehash 而 1.8 entry 根据高位 bit 散列到当前位置 i 和 size+i 位置[resize 后得到的 rehash 只有这两种可能,要么原位置，要么原位置+旧数组长度的和]  
    > jdk1.8 在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）时，将链表转化为红黑树，以减少搜索时间。原本 Map.Entry 接口的实现类 Entry 改名为了 Node。转化为红黑树时改用另一种实现 TreeNode。

  ```java
  //数组  //链表 ：Entry结点  其重要的属性有 key , value, next
  transient Entry[] table;
  // 存储时:
  int hash = key.hashCode(); // 这个hashCode方法这里不详述,只要理解每个key的hash是一个固定的int值
  int index = hash & Entry[].length-1;
  // HashMap的底层数组长度总是2的n次方，在构造函数中存在：capacity <<= 1;这样做总是能够保证HashMap的底层数组长度为2的n次方。当length为2的n次方时，h&(length - 1)就相当于对length取模，而且速度比直接取模快得多
  //为什么长度取2的n次方，因为是&运算 奇数二进制末尾都是0 与任何hash数相与都是0 导致数组的奇数位永远无法存放值 2的n次方为偶数 可使数据分布均匀
  Entry[index] = value;

  // 取值时:
  int hash = key.hashCode();
  int index = hash % Entry[].length;//hash & Entry[].length-1;
  return Entry[index];

  public V put(K key, V value) {
          if (key == null)
              return putForNullKey(value); //null总是放在数组的第一个链表中
          int hash = hash(key.hashCode());
          int i = indexFor(hash, table.length);//计算存放的数组下标
          //遍历链表
          for (Entry<K,V> e = table[i]; e != null; e = e.next) {
              Object k;
              //如果key在链表中已存在，则替换为新value
              if (e.hash == hash && ((k = e.key) == key || key.equals(k))) { //get也是根据key和hash获取
                  V oldValue = e.value;
                  e.value = value;
                  e.recordAccess(this);
                  return oldValue;
              }
          }
          modCount++;
          addEntry(hash, key, value, i);
          return null;
      }
  void addEntry(int hash, K key, V value, int bucketIndex) {
      Entry<K,V> e = table[bucketIndex];
      table[bucketIndex] = new Entry<K,V>(hash, key, value, e); //参数e, 是Entry.next
      //如果size超过threshold，则扩充table大小。把旧的table重新散列到扩容后新的table中
      if (size++ >= threshold)//扩容 threshhod=capacity*loadfactor
              resize(2 * table.length);
  }

  static int indexFor(int h, int length) {
          return h & (length - 1);
      }

  ```

- hashtable vector

  > 每个方法都被 synchronized 修饰 非常低效

  ```java
  public synchronized V get(Object key) {
      ……
      }

  public synchronized V put(K key, V value) {
      ……
      }
  public synchronized V remove(Object key) {
      ……
      }
  ```

- concurrenthashmap

可重入
接口 抽象类

hive hbase
kafka 为什么不直接消费 生产者 broker 作用
sparksql 实现原理
死锁原因
二叉树之字路
netty
