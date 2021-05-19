## java内存模型
https://www.cnblogs.com/czwbig/p/11127124.html

## [Synchronized锁优化](https://www.huaweicloud.com/articles/275e924eb2e1536a68735c656b3017ba.html)

## [ConcurrentHashMap](https://juejin.cn/post/6844904018729254926)

## CopyOnWriteArrayList  
1、添加的逻辑很简单，先将原容器copy一份，然后在新副本上执行写操作，之后再切换引用。当然此过程是要加锁的。  
2、删除类似  
3、读取没有锁  
适用于读多写少场景  
```
public boolean add(E e) {
        //ReentrantLock加锁，保证线程安全
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            //拷贝原容器，长度为原容器长度加一
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            //在新副本上执行添加操作
            newElements[len] = e;
            //将原容器引用指向新副本
            setArray(newElements);
            return true;
        } finally {
            //解锁
            lock.unlock();
        }
    }
```

## [ReentrantLock](https://zhuanlan.zhihu.com/p/82992473)


## java学习
https://github.com/Snailclimb/JavaGuide
