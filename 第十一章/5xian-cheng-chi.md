```
起因:
    近日因做一个活体存图需求需要保存每个动作(准备、左转、右转、眨眼等)下Camera预览每一帧图像，及人脸关键点信息；每个动作的数据
    (图片和关键点信息)zip压缩后上传至服务器。最开始使用AsyncTask.THREAD_POOL_EXECUTOR去执行，发现耗时较长。后分析了一下，可以
    分阶段进行处理。
```

```
    第一个阶段:写文件--存图、存关键点；
    第二个阶段:zip压缩
    第三个阶段:上传压缩包
    想到这里，不由想起图片加载框架UIL里，图片下载、图片处理、图片显示分别用不同的线程池去处理，加速并发执行任务。因此，我按照上述
    三个阶段，建立了三个线程池。然在实际运行过程中发现，压缩和上传总是比较靠后才执行，且压缩一张640x480大小的图片大约需要0.5s;因此
    为压缩线程池的核心线程数改为2。另外，每个线程池都可以allowCoreThreadTimeOut(JDK1.6中引入)设置为true，让线程池的核心线程在空闲
    一定时间后销毁，避免没需要执行的任务时，仍然占用线程资源。
    那么，接下来，正式开始谈谈线程池。脑海里出现一连串问号。什么是线程池？为什么要用线程池？线程池与线程有什么差？怎么合理使用线程池？
    因线程是稀缺资源，引入线程池，可避免重复创建和销毁线程，降低资源消耗，提高相应速度。另外，可以对需要执行的任务进行管理。











 参考文章:
 1.线程池的介绍及简单实现(https://www.ibm.com/developerworks/cn/java/l-threadPool/)
 2.线程池ThreadPoolExecutor、Executors参数详解与源代码分析(http://www.cnblogs.com/nullzx/p/5184164.html)  
 3.并发新特性—Executor 框架与线程池(http://wiki.jikexueyuan.com/project/java-concurrency/executor.html)
 4.Java中线程池ThreadPoolExecutor原理探究
 http://ifeve.com/java%E4%B8%AD%E7%BA%BF%E7%A8%8B%E6%B1%A0threadpoolexecutor%E5%8E%9F%E7%90%86%E6%8E%A2%E7%A9%B6/
 Java中调度线程池ScheduledThreadPoolExecutor原理探究
 http://ifeve.com/33981-2/
 5.AbstractQueuedSynchronizer的介绍和原理分析
 http://ifeve.com/introduce-abstractqueuedsynchronizer/
 6.并发队列-无界阻塞优先级队列PriorityBlockingQueue原理探究
 7.JAVA互斥锁(synchronized&Lock)：行为分析及源码
 http://ifeve.com/java%E4%BA%92%E6%96%A5%E9%94%81synchronizedlock%EF%BC%9A%E8%A1%8C%E4%B8%BA%E5%88%86%E6%9E%90%E5%8F%8A%E6%BA%90%E7%A0%81/
```



