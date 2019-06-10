安卓开发中一个最大的通点，就是异步任务的执行。
常用解决方案主要有如下几种：
1、AsyncTask
2、Handler
3、RxJava

方案很多，都能用，只有一点不爽，烦。
如果不必异步执行，代码一行行执行，多好。可以要异步执行，立刻就坠入地狱。

解决方案
kotlin提出一个新的解决方案：协程(coroutines)。

协程简要理解如下：

构建于线程之上，官方称之为轻量型线程。

每个协程包含特定代码块，协程可以进行调度执行，执行过程可以暂停、恢复。

万言不如一行代码

引入依赖

implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:1.1.1"
implementation "org.jetbrains.kotlinx:kotlinx-coroutines-core:1.1.1"
使用样例

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        GlobalScope.launch {
            doSomething()
            withContext(Dispatchers.Main)
            {
                textView.text = "coroutines完成执行"
                Toast.makeText(this@MainActivity, "coroutines hooray", Toast.LENGTH_SHORT).show()
            }
        }
    }

    private fun doSomething() {
        Thread.sleep(5000)
    }
}
代码简介：

异步任务在GlobalScope.launch块中

要执行异步任务，就将代码放到GlobalScope.launch或者GlobalScope. async(这两种方式返回值不同，本文暂不深入讲解)

要指定任务执行的线程，就用withContext指定，Dispatchers.Main表示主线程，Dispatchers.IO表示IO线程。(如果没有特别要求时，一般我们都将耗时任务放到IO线程里，要更新UI时，就用主线程)

和其他方案对比
采用协程后，异步代码写法上基本跟同步代码是一样，都是顺序执行的，不必使用回调。
