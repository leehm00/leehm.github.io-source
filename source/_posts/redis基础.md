---
title: redis基础
date: 2022-01-27 00:08:30
top: false
cover: false
toc: true
mathjax: true
password:
summary:
tags:
- redis
categories:
- 学习


---

# redis基础

## 数据结构

### sds

- 字符串长度保存在本体中,获取需要常数时间复杂度
- header包含的部分:
  - len: 表示字符串的真正长度（不包含NULL结束符在内）。
  - alloc: 表示字符串的最大容量（不包含最后多余的那个字节）。
  - flags: 总是占用一个字节。其中的最低3个bit用来表示header的类型。
- 缓冲区不会溢出
- 减少修改时的内存重分配次数
  - 空间预分配
  - alloca记录分配的总空间,惰性空间释放
- 除了自己加上的头部字段,其他和c函数一样,兼容相关函数

### 链表

- 双向链表
- 无环,头指针的prev,尾指针的next都指向null
- 自带指向头指针和尾指针的指针
- 链表具有链表长度计数器,标识长度
- void *可以保存不同类型的值

### dict

基于哈希表实现,采用某个哈希函数从key计算得到在哈希表中的位置，采用拉链法解决冲突，并在装载因子超过预定值时自动扩展内存，引发重哈希,并且在重哈希的过程中,每次增删改查推进一步重哈希,避免重哈希期间单个请求的响应时间剧烈增加.

实现增量式重哈希，dict的数据结构里包含两个哈希表。在重哈希期间，数据从第一个哈希表向第二个哈希表迁移

- hash table结构:

  - 一个dictEntry指针数组（table）。key的哈希值最终映射到这个数组的某个位置上（对应一个bucket）。如果多个key映射到同一个位置，就发生了冲突，那么就拉出一个dictEntry链表。
  - size：标识dictEntry指针数组的长度。它总是2的指数。
  - sizemask：用于将哈希值映射到table的位置索引。它的值等于(size-1)，比如7, 15, 31, 63，等等，也就是用二进制表示的各个bit全1的数字。每个key先经过hashFunction计算得到一个哈希值，然后计算(哈希值 & sizemask)得到在table上的位置。相当于计算取余(哈希值 % size)。
  - used：记录dict中现有的数据个数。它与size的比值就是装载因子（load factor）。这个比值越大，哈希值冲突概率越高。

- 单个dictentry结构:

  - ```c
    typedef struct dictEntry {
        void *key;//键
        union {
            void *val;
            uint64_t u64;
            int64_t s64;
            double d;
        } v;//值
        struct dictEntry *next;     /* 具有相同的hash值,先通过链表链接起来Next entry in the same hash bucket. */
        void *metadata[];           /* An arbitrary number of bytes (starting at a
                                     * pointer-aligned address) of size as returned
                                     * by dictType's dictEntryMetadataBytes(). */
    } dictEntry;
    ```

    

- dict本身结构:

  ```c
  //字典本身
  struct dict {
      dictType *type;//类型函数
      /*dictEntry指针数组（table）。
      key的哈希值最终映射到这个数组的某个位置上（对应一个bucket）。
      如果多个key映射到同一个位置，就发生了冲突，那么就拉出一个dictEntry链表。
      只有在重哈希的过程中，ht_table[0]和ht_table[1]才都有效。
      而在平常情况下，只有ht_table[0]有效，ht_table[1]里面没有任何数据。*/
      dictEntry **ht_table[2];//hash表
      /*记录dict中现有的数据个数。
      它与size的比值就是装载因子（load factor）。
      这个比值越大，哈希值冲突概率越高。*/
      unsigned long ht_used[2];
      // rehash 索引
      // 当 rehash 不在进行时，值为 -1,它的值记录了当前重哈希进行到哪一步了
      long rehashidx; /* rehashing not in progress if rehashidx == -1 */
  
      /* Keep small vars at end for optimal (minimal) struct padding */
      /* If >0 rehashing is paused (<0 indicates coding error) */
      int16_t pauserehash; //大于0表示rehash计算停止,小于0编码错误
      signed char ht_size_exp[2]; /* 记录大小的次方,exponent of size. (size = 1<<exp) */
  };
  ```

- rehash过程:

  - 大小选择:
    - 扩展时第二个hash表的大小是大于等于第一个的used*2的第一个$2^n$
    - 收缩时第二个hash表的大小是大于等于第一个的used的第一个$2^n$
  - rehash的时候重新计算键的哈希值和索引值
  - 所有键值对rehash结束之后就直接把新的hash表当作原来的hash表
  - rehash进行时,每次增删改查都会顺带把第一个哈希表上面相应rehashidx索引上面的所有键值对rehash到第二个表上,rehash完成之后将rehashidx值增加一
  - 当rehash完成之后,rehashidx的值设置成-1

### skiplist

- 基本结构:

  - ```c
    typedef struct zskiplistNode {
        //zadd命令在将数据插入到skiplist里面之前先进行了解码，所以存储的一定是一个sds
        //为了方便在查找的时候对数据进行字典序的比较，
        //而且，skiplist里的value部分是数字的可能性也比较小,不是很方便压缩
        sds ele;
        double score;
        //指向链表前一个节点的指针（前向指针）
        //每个节点只有1个前向指针(没有依靠level分开)，所以只有第1层链表是一个双向链表
        struct zskiplistNode *backward;
        //存放指向各层链表后一个节点的指针（后向指针）
        //是一个柔性数组,占用内存不在zskiplistNode结构里面，需要插入节点的时候单独为它分配
        //所以skiplist的每个节点所包含的指针数目是不固定的
        struct zskiplistLevel {
            //每层对应1个后向指针，用forward字段表示
            struct zskiplistNode *forward;
            //表示当前的指针跨越了多少个节点,用于计算元素排名
            unsigned long span;
        } level[];
    } zskiplistNode;
    ```

  - ```c
    //真正的skiplist结构
    typedef struct zskiplist {
        struct zskiplistNode *header, *tail;
        unsigned long length;//链表长度,头指针为空,不算在计数中
        int level;//总层数
    } zskiplist;
    ```

- 层数随机决定(1-32,幂次随机算法)

- 分值可以相同,但是对应成员必须不一样

### intset

- 不会出现重复元素

- 结构:

  - ```c
    //由整数组成的有序集合,便于在上面进行二分查找，用于快速地判断一个元素是否属于这个集合
    typedef struct intset {
        //整个统一的数据编码，表示intset中的每个数据元素用几个字节来存储
        //intset中存储的整数最多只能占用64bit
        //随着数据添加,可能改变数据编码,比如一些比较大的整数
        uint32_t encoding;
        //inset中的元素个数
        uint32_t length;
        //一个柔性数组,长度等于encoding * length,用于表达偏移量
        //contents需要单独为其分配空间，这部分内存不包含在intset结构当中
        int8_t contents[];
    } intset;
    ```

- 当原来的encoding大小不够新的数据存储时,需要升级数据编码

  - 先扩充整个集合底层数组大小,分配新元素空间
  - 原来的数据类型转换
  - 添加新元素(一般来说新元素都在开头或者末尾位置)

- 有点像数组的排列,中间插入元素需要整个向后移动,修改代价比较高

### ziplist

- 列表键只包含少量列表键,值的储存是变长的

- 将表中每一项存放在前后连续的地址空间内，一个ziplist整体占用一大块内存

- 结构:

  - ```c
    typedef struct {
        /* When string is used, it is provided with the length (slen). */
        unsigned char *sval;
        unsigned int slen;
        /* When integer is used, 'sval' is NULL, and lval holds the value. */
        long long lval;
    } ziplistEntry;
    //ziplist的数据类型，没有用自定义的struct之类的来表达，
    //而就是简单的unsigned char *。
    //这是因为ziplist本质上就是一块连续内存，
    //内部组成结构又是一个高度动态的设计（变长编码），也没法用一个固定的数据结构来表达。
    ```

  - ```c
    /* We use this function to receive information about a ziplist entry.
     * Note that this is not how the data is actually encoded, is just what we
     * get filled by a function in order to operate more easily. */
    //每一个entry的格式
    //记录长度是为了方便指针运算确定偏移量
    typedef struct zlentry {
        unsigned int prevrawlensize; /* Bytes used to encode the previous entry len*/
        unsigned int prevrawlen;     /* Previous entry len. */
        unsigned int lensize;        /* Bytes used to encode this entry type/len.
                                        For example strings have a 1, 2 or 5 bytes
                                        header. Integers always use a single byte.*/
        unsigned int len;            /* Bytes used to represent the actual entry.
                                        For strings this is just the string length
                                        while for integers it is 1, 2, 3, 4, 8 or
                                        0 (for 4 bit immediate) depending on the
                                        number range. */
        unsigned int headersize;     /* prevrawlensize + lensize. */
        unsigned char encoding;      /* Set to ZIP_STR_* or ZIP_INT_* depending on
                                        the entry encoding. However for 4 bits
                                        immediate integers this can assume a range
                                        of values and must be range-checked. */
        unsigned char *p;            /* Pointer to the very start of the entry, that
                                        is, this points to prev-entry-len field. */
    } zlentry;
    ```

- 添加/删除节点的时候很可能出现连锁更新

## robj(server.h)

对象的类型一般是指value的类型,和key类型无关(key一般都是string)

- 作用:

  - 为多种数据类型提供一种统一的表示方式。
  - 允许同一类型的数据采用不同的内部表示，从而在某些情况下尽量节省内存。
  - 支持对象共享和引用计数。当对象被共享的时候，只占用一份内存拷贝，进一步节省内存。

- 结构:

  - ```c
    typedef struct redisObject {
        //对象的数据类型
        //可能的取值有5种：OBJ_STRING, OBJ_LIST, OBJ_SET, OBJ_ZSET, OBJ_HASH，
        //分别对应Redis对外暴露的5种数据结构
        unsigned type:4;
        //对象的内部表示方式（也可以称为编码）
        //可能的取值有10种，即前面代码中的10个OBJ_ENCODING_XXX常量
        unsigned encoding:4;
        //lru算法记录使用的数据
        unsigned lru:LRU_BITS; /* LRU time (relative to global lru_clock) or
                                * LFU data (least significant 8 bits frequency
                                * and most significant 16 bits access time). */
        //引用计数,允许robj在一定情况下被共享
        int refcount;
        //数据指针,指向真正的数据
        void *ptr;
    } robj;
    ```

    

- string对象:

  - 接收到的value值（string类型）表示成一个type = OBJ_STRING并且encoding = OBJ_ENCODING_RAW的robj对象
  - 然后在存入内部存储之前先执行一个编码过程，试图将它表示成另一种更节省内存的encoding方式

- list对象

  - 一般使用ziplist

- 哈希对象

  - 使用ziplist或者hashtable
  - ziplist:
    - 键值对相邻,先压入键再压入值
    - 直接往尾部添加
  - hashtable:
    - 使用dict实现
    - dict的键保存键,值保存值

- 集合

  - 使用intset或者hashtable
  - 使用hashtable实现时,字典每个键都是一个字符串对象,对应一个集合元素,字典值设为null

- 有序集合

  - 使用ziplist或者skiplist

- 多态指令:

  - 根据对象的类型编码决定指令的具体函数与实现方式决定要调用的函数
  - 执行指令前像检查对应键或者值能否正确执行相关函数指令

- 内存回收

  - 通过引用计数在适当时候释放对象并回收内存
    - 创建时引用数加一
    - 被新程序使用时引用数加一
    - 程序不再使用,引用数减一
    - 引用数变成0,内存释放

- 对象共享

  - 共享就是利用refcount实现的,每多一个共享的就加一
  - 提前准备了0到9999的字符串对象共享

- 空转时长

  - lru记录最后一次被程序访问的时间,当前时间减去lru时间就是空转时长
  - (打印空转时长并不会更新lru值)
  - 当内存比较满时,最先被释放的是lru最早的那些键

## 数据类型实现

### t_string.c

- setGenericCommand()函数实现了SET,SETEX,PSETEX,SETNX指令
  - expire 定义了 Redis 对象的过期时间
  - *ok_reply 和 abort_reply 决定了命令回复的内容，NX 参数和 XX 参数也会改变回复。*
    - *如果 ok_reply 为 NULL ，那么 "+OK" 被返回。如果 abort_reply 为 NULL ，那么 "$-1" 被返回。*
- getExpireMillisecondsOrReply()实现了过期处理,从客户请求中处理过期的robj
- parseExtendedStringArgumentsOrReply()判断当前字符串操作指令的有效性
- *getexCommand()*对于GET的多余参数进行处理,和GET不同,这个指令不是仅可读的
- setrangeCommand()函数中感觉有个地方可以优化,sdslen只能为0或者正值,那么可以减少一个地方的判断,少用一个if语句

# 
