**和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程可以同时写入 HashTable 并且不会导致数据不一致。它是遗留类，不应该去使用它。现在可以使用 ConcurrentHashMap 来支持线程安全，
并且 ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁**

* [HashTable源码解析](https://mrbird.cc/HashTable%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90.html)
