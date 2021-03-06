```
IntentService是一种特殊的Service，它继承自Service。IntentService可用于执行后台耗时操作，当任务执行完毕后它会自动停止。
相比于Service,IntentService因为封装了HandlerThread的缘故，会新建一个线程；相比于Thread,因为继承了Service，线程不会轻易被杀死。
```

```
那么为什么在任务执行完之后它会自动停止呢？真正的原因在其内部类ServiceHandler的handleMessage()方法中调用的stopSelf(int id)
private final class ServiceHandler extends Handler {
        public ServiceHandler(Looper looper) {
            super(looper);
        }

        @Override
        public void handleMessage(Message msg) {
            onHandleIntent((Intent)msg.obj);
            stopSelf(msg.arg1);//停止服务--IntentService在处理完后会结束
        }
    }


对比继承Service的子类，处理业务代码无需重写onStartCommand(Intent intent, int flags, int startId)而是重写
onHandleIntent(Intent intent)。因为在onStart(Intent intent, int startId)方法中通过消息传递到ServiceHandler中了。
如果非要重写onStartCommand(),那么业务将不是在子线程中处理了。
另外在调用stopSelf()或stopService()之间如果服务异常终止，如果是Service的子类需要在onStartCommand()里设置不同的返回值；
而IntentService的子类则可以通过调用void setIntentRedelivery(boolean enabled)决定是否重传。


onStartCommand()返回值的意义:
可参考文章:Android Service生命周期 Service里面的onStartCommand()方法详解
这个函数返回值可以有四个：START_STICKY、START_NOT_STICKY、START_REDELIVER_INTENT、START_STICKY_COMPATIBILITY。  
它们的含义分别是：
1):START_STICKY：如果service进程被kill掉，保留service的状态为开始状态，但不保留递送的intent对象。随后系统会尝试重新创建service，
由于服务状态为开始状态，所以创建服务后一定会调用onStartCommand(Intent,int,int)方法。如果在此期间没有任何启动命令被传递到service，
那么参数Intent将为null。  
2):START_NOT_STICKY：“非粘性的”。使用这个返回值时，如果在执行完onStartCommand后，服务被异常kill掉，系统不会自动重启该服务  
3):START_REDELIVER_INTENT：重传Intent。使用这个返回值时，如果在执行完onStartCommand后，服务被异常kill掉，系统会自动重启该服务，
并将Intent的值传入。
4):START_STICKY_COMPATIBILITY：START_STICKY的兼容版本，但不保证服务被kill后一定能重启。


可参考packages/apps/Email/provider_src/com/android/email/service/EmailBroadcastProcessorService.java
```



