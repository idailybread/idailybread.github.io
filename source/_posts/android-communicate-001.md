---
title: 通讯篇 第一章 线程间通信
date: 2015-4-29 11:41:12
author: Cutler
categories: Android - 初级开发
---
　　本章来介绍一些线程间通信的技术。

# 第一节 Handler #
## 概述 ##
　　Android的消息机制主要是指`Handler`的运行机制，因此本章会围绕着`Handler`的工作过程来分析消息机制。
　　整个过程主要涉及到了如下5个类：

    -  Handler、Message、MessageQueue、Looper、ThreadLocal

<br>**Handler的作用**
　　对于任何一个线程来说，同一时间只能做一件事，主线程也不例外。
　　因此，若我们让主线程去执行上传/下载等耗时的任务时，在任务执行完毕之前，用户点击了界面中的按钮，主线程是无法响应用户的操作的。
　　并且，如果主线程`5`秒后仍没响应用户，则`Android`系统会弹出`ANR`对话框，询问用户是否强行关闭该应用。

　　这样说的话，我们就只能把耗时的操作放在子线程中执行了。不过，Android规定访问UI只能在主线程中进行，如果在其他线程中访问UI，那么程序就会抛出异常。
　　这个验证线程的操作由`ViewRootImpl`类的`checkThread`方法完成：
``` java
void checkThread() {
    if (mThread != Thread.currentThread()) {
        throw new CalledFromWrongThreadException(
                "Only the original thread that created a view hierarchy can touch its views.");
    }
}
```
　　这意味着，当子线程执行完毕耗时操作后，`得想办法通知主线程一下`，然后借助主线程来修改UI。  
　　而`Handler`就可以完成子线程向父线程发送通知的需求。

<br>**知识扩展**
　　这里再延伸一点，系统为什么不允许在子线程中访问UI呢？ 

    -  这是因为Android的UI控件并不是线程安全的，如果允许在多线程中并发访问，可能会导致UI控件处于不可预知的状态。
    -  也许你会说，加上同步不就行了？ 但是加同步有两个缺点：
       -  第一，Android控件众多，加上同步会让UI的逻辑变得复杂。
       -  第二，过多的同步操作会降低UI的访问效率，因为锁机制会阻塞某些线程的执行。
    -  基于这两个缺点，最简单和高效的方法就是采用单线程来处理UI操作，而且对于开发者来说也不是很麻烦，只需要使用Handler切换一下线程即可。

　　另外，并不是所有的更新UI的操作都只能在主线程中完成的。

    -  比如在子线程中可以简单的修改ProgressBar、SeekBar、ProgressDialog等控件。
    -  也就是只能调用这些控件的某些方法（如setProgress()等），若调用其他方法，则仍然会抛异常。
    -  如果追踪源码的话，会发现setProgress等方法最终还是会通过Handler来更新。

## 基础应用 ##
　　`Handler`的用法十分简单，笔者不打算过多介绍如何使用它，下面给出两个范例，如果不理解请自行搜索。

<br>　　范例1：发送消息。
``` java
public class TestActivity extends Activity {
    public Handler mHandler = new Handler() {
        public void handleMessage(Message msg) {
            // 获取Message对象中的数据。
            System.out.println(msg.arg1);
        }
    };

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        new Thread(){// 创建一个线程对象。
            public void run(){
                // 创建一个Message对象。
                Message msg = new Message();
                // 为Message对象设置数据。
                msg.arg1= 100;
                mHandler.sendMessage(msg); // 将消息对象发送到Handler对象中。
            }
        }.start();
    }
}
```
    语句解释：
    -  本范例中，使用Handler对象的sendMessage方法，给它自己发送消息。
    -  Message类用来封装一个消息，在Message对象中可以保存一些数据，以供Handler使用。
       -  若需要传递给Handler的数据是int类型的，则可以使用Message类提供的两个int类型的属性arg1、arg2，它们方便使用且会更节约系统资源。
       -  若需要传递一个Object类型的数据，则可以使用Message类提供的obj属性。
       -  若需要传递多个Object类型的数据，则可以使用Bundle对象。当然也可以仍然使用obj属性，万物皆对象嘛。

<br>　　范例2：处理消息。
``` java
// 发送数据：
public void sendMessage() {
    new Thread() {
        public void run() {
            //  发送消息1：
            Message msg = new Message();
            msg.obj = "已下载 80%";
            msg.what = 1;
            mHandler.sendMessage(msg);
 
            //  发送消息2：
            msg = new Message();
            msg.obj = "Hi";
            msg.what = 2;
            mHandler.sendMessage(msg); 
        }
    }.start();
}
// 接收数据：
public Handler mHandler = new Handler() {
    public void handleMessage(Message msg) {
        switch(msg.what){
        case 1:
            System.out.println("更新进度条"+msg.obj);
            break;
        case 2:
            System.out.println("打印数据"+msg.obj);
            break;
        }
    }
};
```
    语句解释：
    -  父线程中创建的Handler对象可以接收来自其n个子线程中发送过来的消息。
    -  这些不同的子线程所要完成的任务是不尽相同的，因而他们发送的Message对象需要区别开来处理。
    -  Message的what属性类似于给消息增加一个“唯一标识”，以此来区分不同的Message 。

<br>　　在继续向下之前，先来介绍一下`ThreadLocal`类。
## ThreadLocal ##
　　假设现在有一个需求：

    -  有A、B、C、D四个类，它们的调用顺序是：A → B → C → D，即A类调用B类，B调用C，C调用D。
    -  若现在有个变量n，在ABCD四个类中都会用到它，若是将变量n随着程序的执行流程从A类开始依次传递，最后转给D，则代码会很乱。若是这类变量有很多，则这种传递数据的方式就很繁琐。
    -  用静态变量吗? 若是这个功能模块会被多个线程并发调用，那么静态变量很显然就不行了。
　　此时可以使用`ThreadLocal`类来完成数据的传递。

　　`ThreadLocal`可以将一些变量存放到当前线程对象中，那么只要是这个线程能走到的地方(代码)，都可以获取该变量的值。

<br>　　范例1：传送变量。
``` java
public class MainActivity extends ActionBarActivity {

    ThreadLocal<String> threadLocal = new ThreadLocal<String>();

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        // 主线程调用set方法保存自己的数据。
        threadLocal.set("1");
        // 创建第一个子线程。
        new Thread() {
            public void run() {
                threadLocal.set("2");
                System.out.println("Thread 1 = " + threadLocal.get());  // 输出：Thread 1 = 2
            }
        }.start();
        // 创建第二个子线程。
        new Thread() {
            public void run() {
                threadLocal.set("3");
                System.out.println("Thread 2 = " + threadLocal.get());  // 输出：Thread 2 = 3
            }
        }.start();
        // 主线程调用get方法读取自己的数据。
        System.out.println("Main Thread = " + threadLocal.get());       // 输出：Main Thread = 1
    }
}
```
    语句解释：
    -  多个线程可以共用一个ThreadLocal对象，每个线程都可以通过ThreadLocal的set方法来保存数据，各线程的数据互不影响。
    -  在同一个线程中，每个ThreadLocal只会为它保存一个值，若set两次，则新值覆盖旧值。
    -  可以将ThreadLocal封装入一个单例类中，不同的线程调用get和set方法，操作的都是其自己的数据。

<br>　　范例2：一个线程对应多个`ThreadLocal`。
``` java
public class MainActivity extends ActionBarActivity {

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        ThreadLocal<String> t1 = new ThreadLocal<String>();
        ThreadLocal<String> t2 = new ThreadLocal<String>();

        t1.set("大家好,");
        t2.set("我是崔杰伦!");

        System.out.println(t1.get()+""+t2.get());
    }
}
```
    语句解释：
    -  由此可以得出结论，Thread和ThreadLocal是多对多的关系：
       -  一个Thread对象内可以有多个数据。
       -  一个ThreadLocal对象可以在多个Thread中被使用。

<br>　　介绍完了基础用法，下面说一下它的内部原理。首先看`ThreadLocal`的`set`方法，如下所示：
``` java
public void set(T value) {
    // 获取当前线程对象。
    Thread currentThread = Thread.currentThread();
    // Thread类中有一个ThreadLocal.Values类型的属性，用于保存ThreadLocal传递过去的数据。
    // 获取线程对象的Values属性。
    Values values = values(currentThread);
    if (values == null) {
        // 如果线程中还没有任何数据，则调用下面的方法初始化。
        values = initializeValues(currentThread);
    }
    // 将数据保存到Values对象中。
    values.put(this, value);
}
```

<br>　　`ThreadLocal.Values`类的`put`方法用来实现数据的存储，这里不去分析它的具体算法，但是可以看出如下几点：

    -  第一，ThreadLocal的值存在ThreadLocal.Values类的table属性中，它是一个Object[]。
    -  第二，每次存储新数据时，都会检查table是否存满，若满了则会自动扩充数组的长度。
    -  第三，ThreadLocal的值最终会被存储在table[index+1]的位置上，这个index是依据ThreadLocal的一些属性计算出来的。

<br>　　然后看`ThreadLocal`的`get`方法，如下所示：
``` java
public T get() {
    // Optimized for the fast path.
    Thread currentThread = Thread.currentThread();
    Values values = values(currentThread);
    if (values != null) {
        Object[] table = values.table;
        int index = hash & values.mask;
        if (this.reference == table[index]) {
            return (T) table[index + 1];
        }
    } else {
        // 如果当前线程没有保存过数据，则为它初始化values属性。
        values = initializeValues(currentThread);
    }

    // 返回默认值。
    return (T) values.getAfterMiss(this);
}
```
    语句解释：
    -  get方法也很简单，一看就明白，也是不多说。

<br>　　最后，从`ThreadLocal`的`set`和`get`方法可以看出：

    -  第一，数据最终是存储在table属性中的，table是一个数组，可以保存多个值。
    -  第二，每个ThreadLocal只能在当前线程的table属性中保存一个值，因为保存值的时候，保存的位置是通过ThreadLocal计算出来的，因此两次计算的结果肯定是一样的。
    -  第三，就像前面说的那样，ThreadLocal和Thread是多对多的关系。

## 运行原理 ##
　　接下来咱们就需要研究一下`Message`对象到底是如何被发送给其他线程中创建的`Handler`的。

　　`Handler`机制中主要牵扯到了`Handler`、`Message`、`MessageQueue`、`Looper`四个类。

<br>　　它们四者的身份：

    -  Message表示一个消息对象，它封装了子线程想要做的事情。
    -  MessageQueue表示一个消息队列，队列中的每个元素都是一个Message对象，各个子线程发送给Handler的消息，都会先被放到消息队列中排队等待处理。
    -  Looper：表示一个循环器，它会不断的从MessageQueue的头部获取Message对象，然后将该Message对象交给Handler去处理。
    -  Handler：表示一个处理器，用于处理Message对象。

<br>　　它们四者的关系：

    -  Handler中有一个Looper对象。
    -  Looper中有一个MessageQueue对象。
    -  MessageQueue是一个链队，链队中的每个节点都是一个Message对象，每个Message对象的next域指向下一个Message对象。

<br>**Handler对象**
<br>　　既然上面说`Handler`类中有一个`Looper`对象，我们就来看看`Handler`的构造方法：
``` java
public Handler() {
    this(null, false);
}
public Handler(Callback callback, boolean async) {
    if (FIND_POTENTIAL_LEAKS) {
        final Class<? extends Handler> klass = getClass();
        if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                (klass.getModifiers() & Modifier.STATIC) == 0) {
            Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                klass.getCanonicalName());
        }
    }
    // 从ThreadLocal中获取Looper对象。
    mLooper = Looper.myLooper();
    if (mLooper == null) {
        throw new RuntimeException(
            "Can't create handler inside thread that has not called Looper.prepare()");
    }
    mQueue = mLooper.mQueue;
    mCallback = callback;
    mAsynchronous = async;
}
```
    语句解释：
    -  如果当前线程中没有保存过Looper对象，那么就会抛异常。
    -  也可以通过构造方法Handler(Looper looper)人为的为Handler设置Looper对象。

<br>　　但是，以前我们在实例化`Handler`对象时，并没有为其提供`Looper`对象，为什么没有抛异常呢？

    -  因为在主线程被创建的时候，会同时为其创建一个Looper对象，所以不会抛异常。

<br>　　我们常说的主线程其实就是指的`ActivityThread`类，主线程的入口方法为`main`：
``` java
public static void main(String[] args) {

    // 此处省略若干代码...

    // 在主线程中创建一个Looper对象。
    Looper.prepareMainLooper();

    ActivityThread thread = new ActivityThread();
    thread.attach(false);

    if (sMainThreadHandler == null) {
        sMainThreadHandler = thread.getHandler();
    }

    if (false) {
        Looper.myLooper().setMessageLogging(new
                LogPrinter(Log.DEBUG, "ActivityThread"));
    }

    // End of event ActivityThreadMain.
    Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
    Looper.loop();

    throw new RuntimeException("Main thread loop unexpectedly exited");
}
```
    语句解释：
    -  从上面第6和22行代码可以看出来，当主线程被创建时，会同时创建一个Looper对象，并调用它的loop方法。

<br>　　总之，当`Handler`被成功创建的时候，就意味着它里面已经存在一个`Looper`对象了。

<br>**Looper对象**
<br>　　接下来咱们再来看看`Looper`类，`Looper`在消息机制中扮演着循环器的角色，具体来说就是：

    -  Handler负责发送和处理消息，Handler发送的消息最终会被保存在Looper对象的MessageQueue属性中。
    -  Looper对象就是一个循环器，它通过一个无限for循环，不断的从它的MessageQueue中读取消息。
       -  若读到了消息，则会处理；若读不到，则会阻塞在那里，等待新消息的到来。

<br>　　在`Looper`类中提供了三个静态方法，用来在当前线程中创建`Looper`对象。
``` java
public static void prepare() {
    prepare(true);
}

private static void prepare(boolean quitAllowed) {
    if (sThreadLocal.get() != null) {
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed));
}

public static void prepareMainLooper() {
    prepare(false);
    synchronized (Looper.class) {
        if (sMainLooper != null) {
            throw new IllegalStateException("The main Looper has already been prepared.");
        }
        sMainLooper = myLooper();
    }
}
```
    语句解释：
    -  其中prepareMainLooper用法在主线程中创建Looper对象，另外两个方法用来在当前线程中创建Looper对象。

<br>　　创建完`Looper`对象后，需要调用`loop`方法来启动`Looper`，一旦启动成功后，`Looper`就可以不断的接收和处理消息了。

<br>　　范例1：在子线程中使用`Handler`。
``` java
public class TestActivity extends Activity {
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        new Thread() {
            public void run() {
                Looper.prepare();// 在当前线程中创建一个Looper。
                Handler mHandler = new Handler() {
                    public void handleMessage(Message msg) {
                        String currThdName = Thread.currentThread().getName();
                        System.out.println("当前线程 = " + currThdName);
                    }
                };
                Message msg = new Message();
                mHandler.sendMessage(msg);
                Looper.loop();
            }
        }.start();
    }
}
```
    语句解释：
    -  想在子线程中创建Handler对象，需要先调用Looper.prepare方法在当前线程中创建一个Looper对象。 
    -  创建完Looper对象后，还需要调用Looper对象的loop方法来启动它本身。
    -  Looper提供了quit和quitSafely两个方法来退出loop循环，二者的区别是，quit会直接退出，quitSafely会设定一个退出标记，然后等消息队列中的所有消息都处理完毕后才安全退出。

<br>**消息的发送流程**
　　假设现在主线程中创建了一个`Handler`对象，子线程A要向主线程中的`Handler`发送消息。

<br>　　首先，子线程A通过`Handler`类发送消息：

    -  Handler类中提供的sendMessage等方法，可以将Message对象发送到MessageQueue中。
    -  当消息被发送到MessageQueue中后，当前线程A就直接返回，它接着就去执行sendMessage之后的代码。
    -  类似于去邮局寄信，当把信放入信箱后，就可以回去了，至于信如何被发送到目的地，不需要关心。
    -  事实上，每个Message对象都有一个target属性，它指出由哪个Handler对象来处理当前消息对象。

<br>　　然后，来看一下`Looper`类的`loop`方法：
``` java
public static void loop() {

    // 此处省略若干代码...

    for (;;) {
        Message msg = queue.next(); // might block
        if (msg == null) {
            // No message indicates that the message queue is quitting.
            return;
        }

        // 此处省略若干代码...

        msg.target.dispatchMessage(msg);

        // 此处省略若干代码...

        msg.recycleUnchecked();
    }
}
```
    语句解释：
    -  从上面的代码可以看出：
       -  当Looper会使用无限for循环，不断的调用MessageQueue的next方法，读取消息。
       -  若MessageQueue当前没有需要处理的消息，则它的next方法就会被阻塞，一直不返回。
       -  当next方法返回时，Looper就会将Message发送给其target属性指向的Handler的dispatchMessage方法。
    -  当Message被处理后，Looper会执行上面第18行代码，清空Message的所有属性，并将其加入到回收栈中。
       -  当Handler调用obtainXxx()方法获取Message对象时，就从回收栈顶弹出一个Message对象。
       -  若回收栈栈中没有任何Message对象，则会new一个Message对象返回，但该Message不会被入栈。

<br>　　为什么每个`Message`对象要存在一个`target`属性呢?  

    -  一个线程中只会有一个Looper对象，但是却可能存在多个Handler对象。
    -  当Looper从消息队列中拿到一个消息时，需要把这个消息交给某个具体的Handler处理。
    -  通常这个Handler就是发送该消息的Handler对象。

<br>　　接着看一下`Handler`的`dispatchMessage`方法：
``` java
public void dispatchMessage(Message msg) {
    // 若Message对象的callback属性不为null，则调用callback属性的run()方法。
    if (msg.callback != null) {
        handleCallback(msg);
    } else {
        // 若当前Handler对象的mCallback属性不为null，则将消息交给它处理。
        if (mCallback != null) {
            if (mCallback.handleMessage(msg)) {
                return;
            }
        }
        // 最后，才会调用当前Handler对象的handleMessage(Message)方法去处理。
        handleMessage(msg);
    }
}
```

<br>　　最后，介绍三个`Handler`类的常用方法：
``` java
// 向当前Handler的消息队列中添加一个Runnable 。
// 在Handler内部会构建一个Message对象，并将该对象的callback属性设为r，然后再将这个Message对象加入到消息队列。
// 返回值：
// -  若r被成功加入到消息队列中则返回true。
public final boolean post(Runnable r);

// 向当前Handler的消息队列中添加一个Runnable 。Handler会等待delayMillis毫秒后，才调用Runnable的run()方法。
public final boolean postDelayed(Runnable r, long delayMillis);

// 从当前Handler的消息队列中删除一个Message对象，若找不到该Message则不删除。
public final void removeMessages(int what);
```

## HandlerThread ##
　　通常我们使用Handler是为了在主线程更新UI，但是这并不是Handler的唯一作用，客观点说Handler可以实现线程间的通信，比如主线程给子线程发消息。

<br>　　范例1：在子线程中使用`Handler`。
``` java
public class MainActivity extends Activity {
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

         // 创建一个线程对象，并为其指定名称。
        HandlerThread handlerThread = new HandlerThread("HandlerThread") {
            @Override
            protected void onLooperPrepared() {
                // 使用子线程中的Looper对象来创建Handler。
                Handler handler = new Handler(getLooper()) {
                    // 虽然Handler对象是在主线程中创建的，但是Looper却是运行在子线程中的。
                    // 而handleMessage方法又是由Looper调用的，所以该方法也是运行在子线程中的。
                    public void handleMessage(Message msg) {
                        System.out.println(Thread.currentThread() + " 准备处理消息");
                    }
                };
                // 在主线程中发送消息给子线程。
                handler.sendEmptyMessage(1);
            }
        };
        // 启动这个线程，在其内部会初始化并启动Looper对象。
        handlerThread.start();
    }
}
```
    语句解释：
    -  按照前面学的知识，为了让主线程给子线程发消息，我们得先创建一个子线程，并在其中创建Looper对象，然后才能通信。而系统已经帮我们封装好了一个类专门去做这件事，它叫HandlerThread类，它继承自Thread类，本质上是一个线程对象，其内部封装了一个Looper对象，同时它重写了run方法，并在其内执行Looper的创建和启动操作。
    -  如果想停止HandlerThread，则可以调用它的quit或quitSafely方法。
    -  HandlerThread是一个很有用的类，它在Android中的一个具体的使用场景是IntentService类。

<br>**本节参考阅读：**
- [Android HandlerThread用法](http://blog.csdn.net/qq_695538007/article/details/43376985)

# 第二节 AsyncTask #
　　本节将介绍一个开发中常用的类：`AsyncTask`，它可以实现在子线程执行任务，在主线程更新UI。

## 基础应用 ##
　　`AsyncTask`与`Thread`一样，都是用来执行一些耗时的操作的类，但与传统方式不同：

    -  内部使用线程池管理线程，这样就减少了线程创建和销毁时的消耗。
    -  内部使用Handler处理线程切换，这样省去了我们自己处理的过程，代码直观、方便。

<br>　　范例1：最简单的`AsyncTask`。
``` java
public class MyAsyncTask extends AsyncTask {

    // 此方法用于执行当前异步任务。 
    // 此方法会在AsyncTask的线程池中取出的线程上运行。此方法和Thread类的run方法类似。
    protected Object doInBackground(Object... params) {
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            try {
                sum = sum + i;
                Thread.sleep(200);
                System.out.println("sum + " + i + " = " + sum);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        return null;
    }
}

// 调用execute方法启动当前异步任务，此方法和Thread类的start方法类似。 
AsyncTask task = new MyAsyncTask();
task.execute();
```
    语句解释：
    -  本范例是AsyncTask的最简单应用。
    -  完全可以将本范例中的AsyncTask替换成Thread，把doInBackground方法替换成run方法。启动异步任务的execute方法和启动Thread的start方法是一样的。

<br>　　假设现在有一个任务，要求在计算的时候显示一个进度条对话框，当计算完毕后关闭该对话框，并将计算的结果通过`Toast`输出。此时就需要使用`AsyncTask`类提供的其他方法了。

<br>　　范例2：完成任务。
``` java
private final class MyAsyncTask extends AsyncTask {
    private ProgressDialog dialog;

    // 此方法用于在开始执行当前异步任务之前，做一些初始化操作。 
    // 此方法在主线程中运行。
    // 此方法由execute方法调用。此方法会在doInBackground方法之前被调用。
    protected void onPreExecute() {
        // 创建一个对话框。
        dialog = new ProgressDialog(AsyncTaskActivity.this);
        dialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
        dialog.setTitle("百数相加");
        dialog.setMessage("计算出：1+2+3+...+100的和 !");
        dialog.setMax(5050);
        dialog.show();
    }

    // 在当前异步任务正在执行时，我们可以向AsyncTask里的Handler发送更新UI的消息。
    // Handler接到消息后，会在主线程中，回调用此方法方法更新UI。 
    protected void onProgressUpdate(Object... values) {// 更新进度条。
        this.dialog.setProgress((Integer)values[0]);
    }

    protected Object doInBackground(Object... params) {// 计算结果。
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            try {
                sum = sum + i;
                Thread.sleep(20);
                // 此方法用于在当前异步任务正在执行时，向AsyncTask里的Handler发送更新UI的消息。
                // Handler接到消息后，会调用onProgressUpdate方法更新UI。 此方法由用户根据需求手工调用。
                this.publishProgress(sum); // 通知Handler更新UI。
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        return sum;
    }

    // 此方法用于在当前异步任务执行完成之后，做一些收尾操作。 
    // 此方法在主线程中运行。
    // 此方法会在doInBackground方法之后被调用。doInBackground方法的返回值会被当作此方法的参数。
    protected void onPostExecute(Object result) {
        // 销毁进度条。
        dialog.dismiss();
        Toast.makeText(AsyncTaskActivity.this,"计算结果为："+result,0).show();
    }
}
```
    语句解释：
    -  AsyncTask内的各个方法调用顺序：
       -  第一，我们调用execute方法启动AsyncTask 。
       -  第二，调用onPreExecute方法，执行初始化操作。
       -  第三，从线程池中取出一个空闲的线程，并使用该线程调用doInBackground方法，执行耗时的操作。
       -  第四，当doInBackground方法执行完毕后，onPostExecute方法将被调用（onPostExecute方法的参数就是doInBackground方法的返回值）。
       -  第五，若想更新UI控件，则可以在doInBackground方法中调用publishProgress方法。
          -  提示：调用publishProgress方法时设置的参数将被传递给onProgressUpdate方法。

<br>　　在上面的范例中，各个方法的参数、返回值都是`Object`类型的，这对于严格控制程序有很大负面的影响。
　　但是事实上，`AsyncTask`类是有泛型的。即`AsyncTask<Params, Progress, Result>`其中：

    -  Params：用于设置execute和doInBackground方法的参数的数据类型。
    -  Progress：用于设置onProgressUpdate和publishProgress方法的参数的数据类型。
    -  Result：用于设置onPostExecute方法的参数的数据类型和doInBackground方法的返回值类型。

<br>　　`AsyncTask`类经过几次修改，导致了不同的API版本中的`AsyncTask`具有不同的表现：

    -  Android1.6之前，AsyncTask是串行执行任务的。
    -  Android1.6时，开始采用线程池处理并行任务。
    -  Android3.0开始，为了避免AsyncTask所带来的并发错误，AsyncTask又采用一个线程来串行执行任务。

　　尽管如此，在Android3.0以及之后的版本中，我们仍然可以通过`AsyncTask`的`executeOnExecutor`方法来并行的执行任务。

## 运行原理 ##
<br>　　我们先从`AsyncTask`的`execute`方法开始分析：
``` java
public final AsyncTask<Params, Progress, Result> execute(Params... params) {
    // 转调用executeOnExecutor方法。
    return executeOnExecutor(sDefaultExecutor, params);
}

public final AsyncTask<Params, Progress, Result> executeOnExecutor(Executor exec,
        Params... params) {
    if (mStatus != Status.PENDING) {
        switch (mStatus) {
            case RUNNING:
                throw new IllegalStateException("Cannot execute task:"
                        + " the task is already running.");
            case FINISHED:
                throw new IllegalStateException("Cannot execute task:"
                        + " the task has already been executed "
                        + "(a task can be executed only once)");
        }
    }

    mStatus = Status.RUNNING;
    // 在当前线程中调用AsyncTask的onPreExecute方法。
    onPreExecute();

    // 将params保存到当前AsyncTask对象的mWorker属性中。
    mWorker.mParams = params;
    // 调用sDefaultExecutor的execute方法来将当前AsyncTask对象的mWorker属性添加到队列中。
    exec.execute(mFuture);

    return this;
}
```
    语句解释：
    -  sDefaultExecutor是一个static属性，它实际上是一个串行的线程池，一个进程中的所有AsyncTask都在它里面排队，按照先进先出的顺序依次执行。

<br>　　接着看一下`sDefaultExecutor`属性：
``` java
public static final Executor SERIAL_EXECUTOR = new SerialExecutor();
private static volatile Executor sDefaultExecutor = SERIAL_EXECUTOR;

private static class SerialExecutor implements Executor {
    // 这是一个队列，用来保存等待执行的任务。
    final ArrayDeque<Runnable> mTasks = new ArrayDeque<Runnable>();
    Runnable mActive;

    public synchronized void execute(final Runnable r) {
        // 向队列中添加一个任务。
        mTasks.offer(new Runnable() {
            public void run() {
                try {
                    // 当任务被执行时，调用mFuture的run方法。
                    r.run();
                } finally {
                    // 执行下一个任务。
                    scheduleNext();
                }
            }
        });
        // 如果当前没有任务正在执行，则立刻开始执行队首任务。
        if (mActive == null) {
            scheduleNext();
        }
    }

    protected synchronized void scheduleNext() {
        // 尝试获取任务。
        if ((mActive = mTasks.poll()) != null) {
            // 使用THREAD_POOL_EXECUTOR线程池来执行队首任务。
            THREAD_POOL_EXECUTOR.execute(mActive);
        }
    }
}
```
    语句解释：
    -  AsyncTask中有两个线程池：
       -  sDefaultExecutor用来保存等待执行的任务。
       -  THREAD_POOL_EXECUTOR用来执行任务。

<br>　　接着看一下`mFuture`的定义：
``` java
public AsyncTask() {
    mWorker = new WorkerRunnable<Params, Result>() {
        // 在FutureTask的run方法中会调用mWorker的call方法。
        // call方法是在子线程中被调用的。
        public Result call() throws Exception {
            mTaskInvoked.set(true);

            Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND);
            // 执行任务，并调用postResult方法处理结果。
            return postResult(doInBackground(mParams));
        }
    };

    mFuture = new FutureTask<Result>(mWorker) {
        @Override
        protected void done() {
            try {
                postResultIfNotInvoked(get());
            } catch (InterruptedException e) {
                android.util.Log.w(LOG_TAG, e);
            } catch (ExecutionException e) {
                throw new RuntimeException("An error occured while executing doInBackground()",
                        e.getCause());
            } catch (CancellationException e) {
                postResultIfNotInvoked(null);
            }
        }
    };
}
```
    语句解释：
    -  从上面代码可以看出，mWorker和mFuture都是实例属性。
    -  也就是说，当线程池执行任务的时候，程序的流程会从THREAD_POOL_EXECUTOR中回到某个具体的AsyncTask对象上。

<br>　　接着看一下`postResult`方法：
``` java
private Result postResult(Result result) {
    @SuppressWarnings("unchecked")
    // 封装一个AsyncTaskResult对象，并将它发送到主线程中。
    Message message = getHandler().obtainMessage(MESSAGE_POST_RESULT,
            new AsyncTaskResult<Result>(this, result));
    message.sendToTarget();
    return result;
}

private static Handler getHandler() {
    synchronized (AsyncTask.class) {
        if (sHandler == null) {
            sHandler = new InternalHandler();
        }
        return sHandler;
    }
}

private static class InternalHandler extends Handler {
    public InternalHandler() {
        // 使用主线程的Looper对象。
        super(Looper.getMainLooper());
    }

    @SuppressWarnings({"unchecked", "RawUseOfParameterizedType"})
    @Override
    public void handleMessage(Message msg) {
        AsyncTaskResult<?> result = (AsyncTaskResult<?>) msg.obj;
        switch (msg.what) {
            case MESSAGE_POST_RESULT:
                // 如果任务执行完毕，则调用AsyncTask类的finish方法。
                result.mTask.finish(result.mData[0]);
                break;
            case MESSAGE_POST_PROGRESS:
                // 这个你懂的。
                result.mTask.onProgressUpdate(result.mData);
                break;
        }
    }
}

private void finish(Result result) {
    // 如你所见。
    if (isCancelled()) {
        onCancelled(result);
    } else {
        onPostExecute(result);
    }
    mStatus = Status.FINISHED;
}
```
    语句解释：
    -  InternalHandler类用来将程序的从子线程切换到主线程中。

## 线程池 ##
　　提到线程池就必须先说一下线程池的好处：

    -  它可以维持其内线程不死，让线程重复使用，避免因为线程的创建和销毁所带来的性能开销。
    -  比较常用的一个场景是`http`请求，如果程序需要频繁的、大量的执行请求，那么推荐使用线程池。

<br>　　线程池都是直接或者间接通过配置`ThreadPoolExecutor`来实现的，因此我们来看一下它的构造方法：
``` java
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler)
```
    语句解释：
    -  corePoolSize表示线程池的核心线程数，默认情况下，核心线程会在线程池中一直存活，即使它们处于空闲状态。
       -  线程池中有两类线程，一类是核心线程，另一类是非核心线程。
       -  默认情况下，当任务的数量超过corePoolSize时，就会尝试开启非核心线程去执行任务，执行完毕后非核心线程将被销毁。
    -  maximumPoolSize表示线程池所能容纳的最大线程数，即核心线程+非核心线程的和。
       -  当线程池中的线程数达到这个数值后，新任务将交给handler处理。
    -  keepAliveTime表示非核心线程闲置的时间，即非核心线程执行完任务会等待keepAliveTime时间后才会销毁。
       -  当线程池的allowCoreThreadTimeOut属性设置为true时，keepAliveTime同样会作用于核心线程。
    -  unit表示keepAliveTime的单位，常用取值为：TimeUnit.SECONDS（秒）、TimeUnit.MINUTES（分钟）等。
    -  workQueue表示任务队列，通过线程池的execute方法提交的任务，会保存在此队列中。
    -  threadFactory表示线程工厂，用来创建线程的。
    -  handler当线程池无法执行新任务时（比如任务队列已满或者无法成功执行任务），就会调用RejectedExecutionHandler的rejectedException方法来处理。
       -  线程池的默认实现是抛出一个RejectedExecution异常。

<br>　　若任务队列使用`LinkedBlockingQueue`类，则`ThreadPoolExecutor`类在执行任务时大致遵循如下规则：

    -  若线程池中的核心线程的数量 < corePoolSize，那么会直接启动一个核心线程来执行任务。
    -  若大于或等于corePoolSize，则检测任务队列是否有空位，若有，则将任务直接添加到任务队列中等待执行。
    -  若任务队列已满，则会尝试开启一个非核心线程来执行队首的任务，并把新任务放入队尾。
       -  注意，若任务队列未满，则不会开启非核心线程，所有的任务都会交给核心线程来执行。
       -  也就是说，若我们不为任务队列指定长度的话，那么线程池永远都不会开启非核心线程。
    -  若任务队列满了，且线程池中的线程总数大于maximumPoolSize，那么就拒绝执行此任务，并调用RejectedExecutionHandler来处理。

<br>　　我们来看看`AsyncTask`类的线程池：
``` java
private static final int CPU_COUNT = Runtime.getRuntime().availableProcessors();
private static final int CORE_POOL_SIZE = CPU_COUNT + 1;
private static final int MAXIMUM_POOL_SIZE = CPU_COUNT * 2 + 1;
private static final int KEEP_ALIVE = 1;

private static final ThreadFactory sThreadFactory = new ThreadFactory() {
    private final AtomicInteger mCount = new AtomicInteger(1);

    public Thread newThread(Runnable r) {
        return new Thread(r, "AsyncTask #" + mCount.getAndIncrement());
    }
};

private static final BlockingQueue<Runnable> sPoolWorkQueue =
        new LinkedBlockingQueue<Runnable>(128);

/**
 * An {@link Executor} that can be used to execute tasks in parallel.
 */
public static final Executor THREAD_POOL_EXECUTOR
        = new ThreadPoolExecutor(CORE_POOL_SIZE, MAXIMUM_POOL_SIZE, KEEP_ALIVE,
                TimeUnit.SECONDS, sPoolWorkQueue, sThreadFactory);
```

<br>　　上面只是简单的介绍了各个参数的含义，在`Executors`类为我们提供好了四种线程池，在大部分情况下我们是不需要自己创建线程池的，因为直接使用它们就可以满足需求了。

<br>　　范例1：FixedThreadPool。
``` java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
```
    语句解释：
    -  FixedThreadPool线程池中只有核心线程，除非线程池关闭，否则核心线程会一直存在。
    -  由于没有非核心线程，所以也就没设置超时时间。
    -  任务队列也没有设置长度，因而理论上可以无限接收任务。

<br>　　范例2：CachedThreadPool。
``` java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```
    语句解释：
    -  CachedThreadPool线程池中没有核心线程，有多少任务就会执行多少任务，任务执行完毕后就销毁线程。
    -  前面已经说过了线程池执行任务的流程，但那个过程是基于LinkedBlockingQueue做为任务队列的。
    -  需要注意的是，CachedThreadPool线程池使用SynchronousQueue做为任务队列，该队列不会存储元素，任何任务都会被立刻执行，具体介绍请自行搜索。

<br>　　范例3：ScheduledThreadPoolExecutor。
``` java
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
    return new ScheduledThreadPoolExecutor(corePoolSize);
}

public ScheduledThreadPoolExecutor(int corePoolSize) {
    super(corePoolSize, Integer.MAX_VALUE,
          DEFAULT_KEEPALIVE_MILLIS, MILLISECONDS,
          new DelayedWorkQueue());
}
```
    语句解释：
    -  需要注意的是，ScheduledThreadPoolExecutor线程池使用DelayedWorkQueue做为任务队列，具体介绍请自行搜索。

<br>　　范例4：SingleThreadExecutor。
``` java
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService(new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```
    语句解释：
    -  此线程池只有1个核心线程，所有任务都由这个核心线程来执行。

<br>　　最后，如果想让`AsyncTask`可以在`Android3.0`以及以上的系统上并行运行，可以使用如下代码：
``` java
task.executeOnExecutor(AsyncTask.THREAD_POOL_EXECUTOR, params)
```

# 第三节 RxJava #
<br>　　本节综合了十篇以上的博文内容，并以笔者自己的逻辑路线加以修改与汇总，希望对大家有帮助，所以如果你感觉本文中的某些内容似曾相识，不要疑惑，那一定是我抄的。

　　另外，RxJava本身的功能很多、很强大，而本文主要的目的是入门RxJava，因此很多高级用法没有介绍，各位请自学。

## 它到底是什么 ##

<br>　　一个词：**异步**。它在 GitHub 主页上的自我介绍是：

    RxJava – Reactive Extensions for the JVM – a library for composing asynchronous and event-based programs using observable sequences for the Java VM.

    翻译：一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库。

　　概括得非常精准，然而对于初学者来说，这太难看懂了，因为它是一个『总结』。
　　简单的说，RxJava就是一个实现异步操作的库。

<br>**简单异步和复杂异步：**

　　我知道你在想什么，和我当时想的一样，异步谁不会，为什么用它来搞异步？ 
　　异步无非就是开个线程干点事，然后在切换回主线程，或者给控件设置的监听器也可以算是异步操作，但这有什么难的，这点破事还用单独写一个开源库？

　　其实更准确的说，RxJava主要用来执行`复杂的`、`多层次的`异步操作，简单的异步操作发挥不了它的优势。

　　也就是说，当项目的业务逻辑足够复杂的时候，可能会需要同时发起多个异步操作、甚至存在嵌套的异步操作，考虑到异步操作在时间上地不确定性，此时再用上面列出的技术的话，先不说需要自己处理线程切换、AsyncTask的版本兼容性等问题，光是随之而来的复杂性就不容忽视了，比如若需求是等所有异步都返回的时候，再对结果统一处理，程序员就需要提供一些额外的逻辑来判断异步操作何时结束以及后续的操作何时开始。

　　此时就是RxJava系列库的用武之地，这就是大家之所以说小项目可以不用RxJava的真正原因。
　　但这并不是说就没必学RxJava了，其实RxJava还可以和其它开源库一起配合使用（比如Retrofit），效果非常棒。

<br>**本节参考阅读：**
- [抛物线 - 给 Android 开发者的 RxJava 详解](https://gank.io/post/560e15be2dca930e00da1083)
- [Rx概述（RxJava2）](https://www.jianshu.com/p/f0fd9d8803a3)


## 前世今生 ##

<br>　　既然知道了RxJava是用来做什么的了，那在正式介绍它的用法之前，先来简要说一下它的前世今生，本节的内容理解即可，不需要背诵。

### 源于ReactiveX

<br>　　RxJava 其实是 ReactiveX 的一个具体实现， ReactiveX 是 Reactive Extensions 的缩写，一般简写为 Rx 。

    Rx由微软的架构师Erik Meijer领导的团队开发一个项目，它主要是用来帮助开发者更方便的处理异步，事实上Rx只定义了一套编程接口。
    既然定义接口，那它就得有具体的实现，事实也确实如此，它在很多语言平台上都有自己的实现。
    截止至2018年9月份，从Rx官网上可以看到，Rx库支持.NET、js、java、swift、c++等18种语言，Rx的大部分语言由ReactiveX这个组织维护。

　　我们上面说的 RxJava 就是 ReactiveX 在JVM上的一个实现，帮助我们更方便的处理复杂异步。

### 源于LINQ

<br>　　如果继续向上追溯的话，就会知道还有一种名为LINQ的技术，Rx其实借鉴了LINQ的很多东西，也算是对LINQ的一种扩展，接下来咱们就稍微看一下LINQ到底是什么。

    提示：其实知不知道LINQ对咱们学习RxJava没有任何影响，但是了解一下也不会花多少时间。

<br>　　`LINQ`是`Language Integrated Query`的简称，翻译为“集成查询语言”。

　　我们知道`SQL`是一种操作数据库的通用语言，不论是Oracle、SQLite还是SyBase数据库，它们都支持标准的SQL语法，这样一来就给程序员带来的极大的方便，学一门语言就可以对各种DBMS进行基础操作。
　　但是，SQL仍然存在三个问题：

    1、SQL没法和编程语言直接配合使用，比如Java、Android中没法直接编译SQL语句，只是将它们视为一个字符串在运行的时候执行。
    2、实际开发中，数据并不一定都保存在数据库中，也可能保存在XML等任何地方，但SQL只服务于数据库。
    3、最主要的是，对于复杂的查询操作，SQL语句写出的代码也很复杂，维护成本高，编写效率低。

　　而 LINQ 其实是在 SQL 之上又进行了一层抽象：

    LINQ其实也提供了一套统一的编程接口，它可以高效的对数据进行增删查改操作，从它的官网上可以看到，目前提供了C#和VB两个语言平台的实现。
    有了LINQ，程序员便可以不再沉泥于不同的数据访问技术的学习，也不需要关心他将要操作的将是关系数据库还是XML，甚至是远程的对象，它都采用同样的查询方式。
    不管编程语言如何发展，还是数据库技术的发展，都不再需要程序员进行学习，数据存储的效率由数据库厂商进行优化，灵活的数据操作方法由数据访问中间件厂商提供，程序员只需要编写业务逻辑。

　　简而言之， LINQ 的语法既类似于SQL，又高效且简洁于 SQL ，它可以实现链式调用。笔者推荐各位看一下[《为什么说 LINQ 要胜过 SQL》](https://www.oschina.net/translate/why-linq-beats-sql)，这样可以对LINQ以及它的语法有一个更形象的了解。

<br>　　话说回来，事实上Rx借鉴了LINQ的操作符和链式调用的特点，并融合的观察者模式和Schedulers，因此可以这么说：

    Rx = Observables（观察者模式） + LINQ + Schedulers。

　　关于这个定义各位现在可能不太好理解，不用担心，等整体看完一遍本文后，再回头来看就能明白了。

### 扩展出RxAndroid

<br>　　ReactiveX 这个组织针对 Android 平台还提供了一个适配库，名为[ RxAndroid ](https://github.com/ReactiveX/RxAndroid)，这个库是在 RxJava 基础上编写的，它的源码一共也没多少，就是针对Android平台上线程切换等事情做了些适配。
　　也就是说，学会了RxJava你就学会了RxAndroid，因此后面再说到Rx的时候，各位就直接理解为RxJava就好了。

### 现存两大版本

<br>　　事实上，目前Rxjava有1.x和2.x两个大版本在被使用，ReactiveX在2014年11月18号发布了1.0，在2016年8月26号发布了2.0，相比于1.x版本主要的改动有：

    -  包名改变了，从ix.xx改为io.reactivex.xx。
    -  代码完全遵守Reactive Streams规范。
    -  新增和更新了若干API，如Flowable等。

　　关于RxJava1.x和2.x的更多区别，请去看本节末尾列出参考链接，为了适应的面更广，本文会主要介绍1.x的语法，其实学会了1.x再去看2.x就很快了。


<br>**本节参考阅读：**
- [极客学院 - ReactiveX](http://wiki.jikexueyuan.com/project/rx-docs/Intro.html)
- [reactivex.io](http://reactivex.io/#)
- [LINQ教程一：LINQ简介](https://www.cnblogs.com/dotnet261010/p/8278793.html)
- [所以什么是LINQ](https://ithelp.ithome.com.tw/articles/10194251)
- [RxJava 1.x 和 RxJava 2.x 的异同](https://www.jianshu.com/p/765591cb2fd8)

## RxJava 1.x ##

<br>　　范例1：环境配置。在 AndroidStudio 中使用如下代码：

``` gradle
api 'io.reactivex:rxandroid:1.2.1'
```
    语句解释：
    -  RxAndroid是和RxJava保持同步的，也分为1.x和2.x版本，其中1.2.1就是RxAndroid1.x的最后一个版本。
    -  由于在RxAndroid的内部就引用的RxJava库，所以我们只需要引用RxAndroid库就可以了。


<br>　　前面说了 RxJava 是基于观察者模式的，在继续往下学习之前，关于这个模式的定义咱们需要先达成共识：

    第一，常见的观察者模式里，被观察者内部持有一个观察者者列表，当数据变化时，被观察者会遍历列表，依次通知每一个观察者。
    第二，在更广义的观察者模式中，被观察者的内部可以不持有一个观察者列表，而只持有一个观察者的引用，比如我们经常调用setOnClickListener方法给Button设置回调函数，这个操作就可以理解为观察者模式：Button是被观察者，被点击时，会调用回调函数通知我们这个唯一的观察者。

　　事实上RxJava就是基于广义的观察者模式，它创建的被观察者的内部并没有维护一个观察者列表。


### 基础用法 ###

　　既然前面一直在说RxJava是基于观察者模式的，那么我们就以观察者模式为切入点讲解RxJava，首先来回想一下观察者模式的写法：

    第一步，创建被观察者。
    第二步，创建观察者。
    第三步，将二者绑定到一起。

　　在RxJava中也是一样的步骤，稍后我们就来搞一遍。
　　但是要事先说明的是，接下来笔者为了介绍Rx的语法，会写出一些没有实用价值的范例，请各位多包涵。

<br>　　范例1：先来创建被观察者。
``` java
// RxJava中最最常见的三个类在本范例中都出现了，它们分别是：
//     Observable类，表示被观察者，它的create方法用来创建一个对象。
//     Subscriber类，表示观察者。
//     Observable.OnSubscribe类，是一个回调接口，会被存储在返回的Observable对象中。
//          当有新的观察者绑定到Observable上时，Observable就会调用该接口的call方法，并将该观察者传给call方法。
Observable.create(new Observable.OnSubscribe<User>() {
    @Override
    public void call(final Subscriber<? super User> subscriber) {
        System.out.println("检测到新的观察者来访：____________ " + subscriber);
    }
});
```
    语句解释：
    -  User类表示具体的被观察的数据类型，也就是说我们创建了一个被观察者，它提供一个User类型的数据供外界观察。

<br>　　被观察者创建完毕了，然后咱们来创建观察者。
<br>　　范例2：创建观察者。

``` java
// 其实在RxJava中，使用Observer接口来表示观察者，而我们前面说的Subscriber是一个抽象类，它实现了Observer接口。 
// Subscriber对Observer接口进行了一些扩展，但它们的基本使用方式是完全一样的。
Subscriber observer = new Subscriber<User>() {

    // 对于观察者，RxJava其实有几条明确的规定：
    // 第一，Observer需要实现三个抽象方法：onNext、onCompleted、onError。
    // 第二，onNext用来接收Observable发来的数据，onCompleted表示正常终止，onError表示Observable异常终止。
    // 第三，在一个正确运行的事件序列中, onNext可以被多次调用，但onCompleted和onError有且只有一个，并且是得最后一个。
    // 第四，需要注意的是，onCompleted和onError二者也是互斥的，即在队列中调用了其中一个，就不应该再调用另一个。

    @Override
    public void onNext(User s) {
        System.out.println("____________ onNext " + s);
    }

    @Override
    public void onCompleted() {
        System.out.println("____________ onCompleted ");
    }

    @Override
    public void onError(Throwable e) {
        System.out.println("____________ onError ");
    }

};
```
    语句解释：
    -  上面说的那四条规定是一个规定，RxJava的官方API会遵守这个规定，但是如果你自定义Observable的时候非得在call方法里调用多次onCompleted方法，也没人会拦着你。
    -  对于本范例，你只需要知道自定义的Subscriber需要重写三个方法，至于实际开发中需要在里面写什么代码，先别管，爱他妈咋写咋写。

<br>　　上面提到了“`事件`”二字，其实很好理解，我们知道被观察者发生了任何变化，都会通过回调方法通知观察者，比如调用观察者的onNext、onCompleted和onError方法，我们把每次调用都看为：被观察者向观察者发送一个事件。

<br>　　范例3：建立链接。

``` java
observable.subscribe(observer);
```
    语句解释：
    -  使用这行代码就将observer绑定到observable身上了，然后程序会执行Observable.OnSubscribe的call方法。
    -  需要注意的是，这个subscribe()方法会返回一个Subscription对象，这个对象代表了被观察者和观察者之间的连接，你可以在后面使用这个Subscription对象来操作被观察者和观察者之间的连接，比如调用unsubscribe方法解除观察。

<br>　　至此，RxJava整个执行流程我们算是体验完了，但是目前学到的东西没有任何意义。
　　而且上面范例1只是展示了如何创建被观察者，那Observable.OnSubscribe的call方法应该写什么样的代码呢？

<br>　　范例4：再来一个没有意义的范例。
``` java
Observable observable = Observable.create(new Observable.OnSubscribe<User>() {
    @Override
    public void call(final Subscriber<? super User> subscriber) {
        try {
            // 加载数据
            User user = loadUserFromNetwork();
            // 将数据回调给观察者之前，先判断观察者当前是否仍然处于观察状态。
            if (!subscriber.isUnsubscribed()) {
                // 调用观察者的onNext方法将数据传递过去。
                subscriber.onNext(user);
                // 调用onCompleted方法告诉观察者，整个流程已经走完了，以后不再会接到新事件了。
                subscriber.onCompleted();
            }
        } catch (Exception e) {
            if (!subscriber.isUnsubscribed()) {
                // LoadFailureException是我们自定义的异常。
                subscriber.onError(e);
            }
        }
    }
});
```
    语句解释：
    -  通常情况下我们会在call方法中产生数据，并将数据通过call方法的参数传递给观察者。
    -  需要说明的是，之所以需要在call方法里判断Subscriber的连接是否还存在，是因为call方法是可能在子线程中被调用的，因此如果在第6行代码被执行的时候，Subscriber在主线程断开了连接，那么当第6行结束时不判断连接状态的话，就会有问题了。


<br>　　范例5：完整代码。

``` java
Observable observable = Observable.create(new Observable.OnSubscribe<User>() {
    @Override
    public void call(final Subscriber<? super User> subscriber) {
        try {
            User user = loadUserFromNetwork();
            if (!subscriber.isUnsubscribed()) {
                subscriber.onNext(user);
                subscriber.onCompleted();
            }
        } catch (Exception e) {
            if (!subscriber.isUnsubscribed()) {
                subscriber.onError(e);
            }
        }
    }
});
observable.subscribe(new Subscriber<User>() {
    public void onCompleted() {
        System.out.println("____________ onCompleted ");
    }
    public void onError(Throwable e) {
        System.out.println("____________ onError ");
    }
    public void onNext(User s) {
        System.out.println("____________ onNext " + s);
    }
});
```
    语句解释：
    -  此时再一看这段代码，整体的含义就一目了然了。

<br>　　补充一下，其实 Subscriber 和 Observer 的区别对于使用者来说主要有两点：

    -  onStart(): 这是Subscriber增加的方法。它会在subscribe刚开始，而事件还未发送之前被调用，可以用于做一些准备工作，例如数据的清零或重置。这是一个可选方法，默认情况下它的实现为空。
    -  unsubscribe(): 这是Subscriber所实现的另一个接口Subscription的方法，用于取消观察。在这个方法被调用后，Subscriber将不再接收事件。这个方法很重要，因为在subscribe之后， Observable会持有Subscriber的引用，如果不及时释放引用，将有内存泄露的风险。

<br>　　到现在为止，我们并没有看到RxJava的任何实际作用，仅仅是写了一遍观察者模式而已。不要慌，其实观察者模式就是核心，仔细想一下：

    -  被观察者作为事件的产生方，是整个流程的起点。
    -  观察者作为事件的处理方，是整个流程的终点。
    -  在起点和终点之间，我们是可以做手脚的，即事件传递的过程中是可以被加工，过滤，转换，合并等等方式处理的。
    -  而RxJava的强大之处，就是它做手脚的能力，也就是稍后要讲到的操作符，RxJava提供了各种操作符满足你的各种需求。


<br>　　比如说，前面说过RxJava是一个做异步的库，那么现在咱们先来想一下，实际开发中遇到的异步操作，有哪些特点：

    第一，输入的数据和输出的数据格式不一致。比如发送网络请求，输入时接口地址，返回一个是对象。
    第二，线程切换。
    第三，在某些场景下，客户端不会直接展示服务器返回的数据，会和本地的数据进行融合后在显示。
    第四，多个异步同时出发，并等待全部回来后，统一处理。
　　这些需求在RxJava中都有对应的操作符来应对，甚至于你还可以将多个操作符同时混合使用。

### 操作符 ###

<br>　　RxJava的操作符有很多，本文只会挑一些比较常用介绍，其它操作符请各位自行搜索：

![常用操作符一览](/img/android/android_BY_d02_01.png)

　　提示：操作符是从LINQ那里传递过来的术语，在RxJava中操作符其实就是指的一系列的方法。

#### 创建操作 ####

<br>　　在RxJava中，除了使用前面用到的`Observable.create`方法创建被观察者外，还提供了很多其它方便的方法进行创建。

<br>　　范例1：just方法。
``` java
// just方法创建的Observable，当有观察者接入时，会自动为你调用观察者的onNext发送数据。
// just方法可以支持多个参数，假设它有4个参数，那么当有新观察者绑定时，会调用该观察者的onNext方法四次，即每个参数对应一次调用。
Observable observable1 = Observable.just("a");
Observable observable2 = Observable.just("a","b","c");
```
    语句解释：
    -  查看源码可以发现just方法的参数是泛型类型的，也就意味着在RxJava中万事万物都可以被定义为一个Observable。
    -  之前我们是User，现在是String，以后还可以是Student或者是Fucker。


<br>　　范例2：from方法。
``` java
String[] array = new String[]{"1","2","3"};
Observable observable = Observable.from(array);
```
    语句解释：
    -  just方法最多只支持10个参数，如果您的参数数量大于10个就可以使用from方法。
    -  from方法可以从一个Iterable或者数组中获取数据，并创建一个Observable。
    -  其实从源码上可以看到，如果调用的一个参数的just方法，则会创建一个ScalarSynchronousObservable对象，超过一个参数的just方法其实会在内部转调用from方法。


<br>　　范例3：empty、error、never方法。
``` java
Observable observable1 = Observable.empty(); // 直接调用onCompleted
Observable observable2 = Observable.error(new RuntimeException());// 直接调用onError，这里可以自定义异常
Observable observable3 = Observable.never(); // 什么都不做
```
    语句解释：
    -  这三个方法创建的Observable，当有观察者绑定时，会按上面说的那样有各自的行为。
    -  前面说过，在某些场景下创建Observable和Subscriber的操作并不是重点，重点是事件的起点和重点之间所要做的事情，这一点本范例中已经能看出来了，我们有时候根本不需要定义被观察的数据是什么，我们要的仅仅是一个Observable对象而已。
    -  我们最需要其实是各种操作符，我们也会把核心代码写在各种操作符中，具体后面就会看到。

<br>　　范例3介绍了简化版的Observable，其实Subscriber的创建也是可以简化的。

<br>　　范例4：简化Subscriber。
``` java
Observable observable = Observable.just("a", "b");
Action1<String> onNextAction = new Action1<String>() {
    // onNext()
    @Override
    public void call(String s) {
        System.out.println("____________onNext call " + s);
    }
};
Action1<Throwable> onErrorAction = new Action1<Throwable>() {
    @Override
    public void call(Throwable throwable) {
        System.out.println("____________onError  call ");
    }
};
Action0 onCompletedAction = new Action0() {
    @Override
    public void call() {
        System.out.println("____________completed  call ");
    }
};
// 内部会自动创建Subscriber，并使用onNextAction来顶替Subscriber类的onNext()
observable.subscribe(onNextAction);
observable.subscribe(onNextAction, onErrorAction);
observable.subscribe(onNextAction, onErrorAction, onCompletedAction);
```
    语句解释：
    -  在RxJava中提供了Action0 - Action9等接口，它们内部都只有一个无返回值的call方法，它们的区别是参数的个数不同，Action0接口的call方法没有参数，Action1的有1个，其它依次类推。
    -  就像你看到的这样，我们在调用observable的subscribe方法时，其实可以不传递一个Subscriber对象，而只是传递一个Action接口即可。
    -  其实这些方法内部实现就是将我们传递的Action对象封装成一个Subscriber而已。


<br>　　范例5：timer方法。
``` java
final long start = System.currentTimeMillis();
Observable.timer(2, TimeUnit.SECONDS).subscribe(new Action1<Long>() {
    @Override
    public void call(Long aLong) {
        System.out.println(" ____________ onNext call " + (System.currentTimeMillis() - start));
    }
});
```
    语句解释：
    -  timer方法用来创建一个在给定的延时之后发射数据项为0的Observable，注意泛型必须是Long型的。
    -  定时相关的操作符还有interval，请自行测试。


<br>　　范例6：range方法。
``` java
Observable.range(2, 5).subscribe(new Action1<Integer>() {
    @Override
    public void call(Integer integer) {
        System.out.println(" ____________ onNext call " + integer);
    }
});
```
    语句解释：
    -  创建一个可以发送指定范围内数列的Observable，第一个参数是起点，第二参数是个数。


#### 线程控制 ####
<br>　　在不指定线程的情况下， RxJava 遵循的是线程不变的原则，即：在哪个线程调用 subscribe()，就在哪个线程生产事件；在哪个线程生产事件，就在哪个线程消费事件，如果需要切换线程，就需要用到 Scheduler（调度器）。

<br>　　在RxJava中，Scheduler相当于线程控制器，RxJava 通过它来指定每一段代码应该运行在什么样的线程。RxJava已经内置了几个Scheduler，它们已经适合大多数的使用场景：

    Schedulers.immediate(): 直接在当前线程运行，相当于不指定线程，这是默认的Scheduler。
    Schedulers.newThread(): 总是启用新线程，并在新线程执行操作。
    Schedulers.io(): I/O操作（读写文件、读写数据库、网络信息交互等）所使用的Scheduler。它和newThread()差不多，区别在于io()的内部实现是是用一个无数量上限的线程池，可以重用空闲的线程，因此多数情况下io()比newThread()更有效率。
    Schedulers.computation(): 计算所使用的Scheduler。这个计算指的是CPU密集型计算，即不会被I/O等操作限制性能的操作，例如图形的计算。这个Scheduler使用的固定的线程池，大小为CPU核数。不要把I/O操作放在computation()中，否则I/O操作的等待时间会浪费CPU。
　　另外， Android还有一个专用的`AndroidSchedulers.mainThread()`，它指定的操作将在Android主线程运行。


<br>　　范例1：线程切换方法。
``` java
Observable.create(new Observable.OnSubscribe<Integer>() {
    @Override
    public void call(Subscriber<? super Integer> subscriber) {
        System.out.println("call " + Thread.currentThread().getName());
        subscriber.onNext(1024);
    }
}).subscribeOn(Schedulers.io()) // 设置Observable.OnSubscribe被激活时所处的线程。
        .observeOn(AndroidSchedulers.mainThread()) // 指定Subscriber的各个回调方法在主线程中被调用
        .subscribe(new Subscriber<Integer>() {
            @Override
            public void onStart() {
                System.out.println("onStart " + Thread.currentThread().getName());
            }

            @Override
            public void onCompleted() {
                System.out.println("onCompleted " + Thread.currentThread().getName());
            }

            @Override
            public void onError(Throwable e) {
                System.out.println("onError " + Thread.currentThread().getName());
            }

            @Override
            public void onNext(Integer integer) {
                System.out.println("onNext " + Thread.currentThread().getName() + "," + integer);
            }
        });
// 程序运行结果：
// onStart main
// call RxIoScheduler-2
// onNext main,1024
```
    语句解释：
    -  使用subscribeOn()和observeOn()两个方法可以对线程进行控制。
       -  subscribeOn: 指定Observable.OnSubscribe被激活时所处的线程，或者叫做事件产生的线程。
       -  observeOn: 指定Subscriber所运行在的线程，或者叫做事件消费的线程。
    -  从程序运行的结果可以看出来，我们在call方法中调用了onNext，但并没有进行任何切换线程的操作，RxJava帮我们做了。
    -  事实上，这种在subscribe()之前写上两句subscribeOn(Scheduler.io())和observeOn(AndroidSchedulers.mainThread())的使用方式非常常见，它适用于多数的『后台线程取数据，主线程显示』的程序策略。
    -  另外，如果看源码的话，就会发现subscribeOn和observeOn的返回值其实是Observable对象，即对原有的Observable进行了封装。


#### 变换操作 ####

<br>　　我们在写异步的代码时，代码往往都有一个特点：输入数据和输出数据的类型不同。比如在接口请求时，输入数据是一个URL，输出数据则是一个对象（通过返回的json生成的）。
　　同时我们希望在子线程处理好一切后，再将返回值返回到主线程中，这个需求使用map操作符就可以。

<br>　　范例1：map方法 —— 依据学号查询出对应的Student对象。
``` java
// 输入数据一个String类型的，在本范例中这个字符串表示学生的学号。
Observable.just("20090403")
    .subscribeOn(Schedulers.io())  
    // 简单的来说，map方法就是一个转换器，接收String类型的参数，返回Student类型的值。
    .map(new Func1<String, Student>() {
        @Override
        public Student call(String userId) {   
            // 由于前面设置了，所以此处的call方法将在io线程中调用，因此这里可以执行网络请求，或者本地数据库查询。
            // 但是为了代码整洁，此处直接new了一个对象，并将当前线程的名字设置到Student上了。
            return new Student(userId, "张三 " + Thread.currentThread().getName());
        }
    })
    .observeOn(AndroidSchedulers.mainThread()) // 设置观察者的回调方法在主线程中被调用。
    .subscribe(new Action1<Student>() {
        @Override
        public void call(Student student) {
            System.out.println("onNext " + Thread.currentThread().getName() + "," + student.getUserName());
        }
    });
// 程序运行结果：
// onNext main,张三 RxIoScheduler-2
```
    语句解释：
    -  本范例中出现了一个叫做Func1的类，它和Action1非常相似，也是RxJava的一个接口，用于包装含有一个参数的方法。
    -  和ActionX一样，FuncX也有多个，用于不同参数个数的方法，Func1和Action的区别在于，Func里方法有返回值，Action则没有。
    -  简单的说：
       -  如果你只想在子线程中请求数据，那么使用Observable.create方法即可，需要自己调用观察者的onNext方法。
       -  如果你既需要子线程中请求数据，又需要将结果转成特定类型，就可以使用map操作符。
    -  另外，观察源码可以发现，map方法返回值也是一个Observable，即二次封装了。


<br>　　范例2：多次转换。
``` java
Observable.just("20090403")
    .subscribeOn(Schedulers.io()) 
    // 下面这个map会沿用之前的线程，即io线程。
    .map(new Func1<String, Student>() {
        @Override
        public Student call(String userId) {
            return new Student(userId, "张三 " + Thread.currentThread().getName());
        }
    })
    // 设置之后的代码将运行在主线程中
    .observeOn(AndroidSchedulers.mainThread())
    .map(new Func1<Student, String>() {
        @Override
        public String call(Student student) {
            return student.getUserName() + ", 二次转换：" + Thread.currentThread().getName();
        }
    })
    // 再次将后面的代码切换到子线程中
    .observeOn(Schedulers.newThread())
    .subscribe(new Action1<String>() {
        @Override
        public void call(String str) {
            System.out.println("onNext " + Thread.currentThread().getName() + "," + str);
        }
    });
// 程序运行结果：
// onNext RxNewThreadScheduler-1,张三 RxIoScheduler-2, 二次转换：main
```
    语句解释：
    -  observeOn方法可以多次调用，它会对其之后的代码产生影响。
    -  subscribeOn也可以调用多次，可以写在整个调用链的任何位置（但需要在subscribe方法之前），但只有第一次调用的会生效。
    -  map方法也可以多次调用，它会将之前的代码的返回值作为输入数据，进行转换。
    -  从上面可以看出，不论是observeOn、subscribeOn还是map方法，它们都会对原有的Observable对象进行二次封装，因此要使用这种链式调用才能保证代码正确执行。
    -  实际上，整个在subscribe方法调后程序才开始运转，在其之前仅仅是做了一层层的封装，即排在后面的操作符会将前面的操作符包装起来。



#### 其它操作 ####


<br>　　范例1：条件/布尔操作。
``` java
Observable.just(2, 3, 4, 5)
    .all(new Func1<Integer, Boolean>() {
        @Override
        public Boolean call(Integer integer) {
            return integer > 1;
        }
    })
    .subscribe(new Action1<Boolean>() {
        @Override
        public void call(Boolean aBoolean) {
            System.out.println("_____________ " + aBoolean);
        }
    });
```
    语句解释：
    -  all操作符用来判断所有数据项是否都满足某个条件。
    -  相应的还有exits、contains、isEmpty、takeWhile等操作符，请自行搜索。


<br>　　范例2：过滤操作。
``` java
bservable.just(3, 4, 5, 6)
    .filter(new Func1<Integer, Boolean>() {
        @Override
        public Boolean call(Integer integer) {
            return integer > 4;
        }
    })
    .subscribe(new Action1<Integer>() {
        @Override
        public void call(Integer integer) {
            System.out.println(integer);
        }
    }); //5,6
```
    语句解释：
    -  过滤数据，只有满足条件的数据才会执行后续操作。
    -  相应的还有ofType、take、takeFirst、distinct等操作符，请自行搜索。


<br>**本节参考阅读：**
- [RxJava 从入门到放弃再到不离不弃](https://www.daidingkang.cc/2017/05/19/Rxjava/)
- [关于RxJava最友好的文章](https://juejin.im/post/580103f20e3dd90057fc3e6d)


## RxJava 2.x ##

　　关于RxJava2.x的介绍网上已经有很多优秀的博文了，笔者就不再重复写一遍了，下面分享几个链接大家看一看就好了，RxJava入门稍微费点劲，但从1.x转到2.x则不难。


<br>**本节参考阅读：**
- [RxJava 1.x 和 RxJava 2.x 的异同](https://www.jianshu.com/p/765591cb2fd8)
- [RxJava1 升级到 RxJava2 所踩过的坑](https://www.jianshu.com/p/6d644ca1678f)
- [这可能是最好的RxJava 2.x 教程（完结版）](https://www.jianshu.com/p/0cd258eecf60)



<br><br>