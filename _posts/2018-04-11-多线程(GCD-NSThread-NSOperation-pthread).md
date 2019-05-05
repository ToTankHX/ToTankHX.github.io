# 多线程(GCD/NSThread/NSOperation/pthread)

线程是CPU调用的最小单位，进程是CPU分配资源和调度的单位。
一个程序可以对应多个进程，一个进程可以有多个线程，但至少有一个线程。
同一个进程里的线程共享进程资源。


## GCD
* 并发 Concurrent
* 串行 Serial

容易发生的问题

* 死锁
* 资源竞争 dispatch_async_barrier()

代码：

```swift

/*
disaptch_barrier_async解决了并发问题导致的资源竞争，例如数据的读写。防止资源读取时写入
 */
// create concurrent queue
dispatch_queue_t gcd_queue = dispatch_queue_create("com.huxn.gcd",DISPATCH_QUEUE_CONCURRENT); 

// add async in queue
dispatch_async(gcd_queue,^{
// enter async queue
});

// create barrier async in queue
dispatch_barrier_async(gcd_queue,^{
// wariting async queue finish
});
```

等待线程结束

```
dispatch_queue_t gcd_queue = dispatch_queue_create("com.huxn.gcd",DISPATCH_QUEUE_CONCURRENT);

dispatch_queue_t gcd_queue1 = dispatch_get_global_queue(0,0);
dispatch_group_t gcd_group = dispatch_group_create();

dispatch_group_async(groud,gcd_queue,^{
// fist async queue
});

dispatch_group_async(group,gcd_queue,^{
//sencond async queue
});

dispatch_group_async(group,gcd_queue1,^{
// last async queue for gcd_quque1
});

dispatch_group_notify(group,dispatch_get_main_queue(),^{
//  wariting all queue finish
});
```

* lock

```
// 信号数量必须大于0
dispatch_semaphore_t semaphore = dispatch_semaphore_create(1);

//锁死
dispatch_semphore_wait(semaphore,DISPATCH_TIME_FOREVER);

// 释放解锁信号
dispatch_semaphore_signal(semaphore);
```


## NSThread

## NSOperation

## 线程锁
* 互斥锁

    * `@synchronized`
    
        ```
        @synchronized(self){
        // lock
        };
        ```

    * `NSLock`
        
        ```
        NSLock *lock = [[NSLock alloc] init];
        [lock lock]; // 加锁
        // 期间其他线程无法访问
        [lock unlock]; // 解锁
        ```

