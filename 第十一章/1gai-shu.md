```
  Android中线程除了继承Thread或实现Runnable之外，还封装一些基础类，在子线程中完成耗时操作，避免主线程阻塞而出现ANR现象。
  常见的有AsyncTask,IntentService和HandlerThread。不同形式的线程，有这不同的使用场景和特性。AsyncTask封装了线程池和Hanlder,
  因为有了Handler，AsyncTask更新UI很方便。IntentService是一个服务，能方便的执行后台任务，其内部封装了HandlerThread来执行任务,
  当任务执行完毕后IntentService会自动退出。
```



