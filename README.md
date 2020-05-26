pip install threadpool_executor_shrink_able



史上最强的python线程池。

最智能的可自动实时调节线程数量的线程池。此线程池和官方concurrent.futures的线程池 是鸭子类关系，所以可以一键替换类名 或者 import as来替换类名。
对比官方线程池，有4个创新功能或改进。

1、主要是不仅能扩大，还可自动缩小(官方内置的ThreadpoolExecutor不具备此功能，此概念是什么意思和目的，可以百度java ThreadpoolExecutor的KeepAliveTime参数的介绍)，

2、非常节制的开启多线程，例如实例化一个最大100线程数目的pool，每隔2秒submit一个函数任务，而函数每次只需要1秒就能完成，实际上只需要调节增加到1个线程就可以，不需要慢慢增加到100个线程
官方的线程池不够智能，会一直增加到最大线程数目，此线程池则不会。

3、线程池任务的queue队列，修改为有界队列

4、此线程池运行函数出错时候，直接显示线程错误，官方的线程池则不会显示错误，例如函数中写1/0,任然不现实错误。

5.有线程池BoundedThreadPoolExecutor改善线程报错和有界队列。

6.patch_builtin_concurrent_futeres_threadpoolexecutor 支持给内置线程池打猴子补丁的方式，一键替换项目中所有原有的Thredpoolexecutor

用法例子：

```python
import time
from nb_log import nb_print
from threadpool_executor_shrink_able import ThreadPoolExecutorShrinkAble

def f1(a):
    time.sleep(0.2)  # 可修改这个数字测试多线程数量调节功能。
    
    nb_print(f'{a} 。。。。。。。')
    
    # raise Exception('抛个错误测试')  # 官方的不会显示函数出错你，你还以为你写的代码没毛病呢。


pool = ThreadPoolExecutorShrinkAble(200)

# pool = ThreadPoolExecutor(200)  # 测试对比官方自带

for i in range(300):

    time.sleep(0.3)  # 这里的间隔时间模拟，当任务来临不密集，只需要少量线程就能搞定f1了，因为f1的消耗时间短，不需要开那么多线程，CustomThreadPoolExecutor比ThreadPoolExecutor 优势之一。
    
    pool.submit(f1, str(i))

# 1/下面测试阻塞主线程退出的情况。注释掉可以测主线程退出的情况。

# 2/此代码可以证明，在一段时间后，连续长时间没任务，官方线程池的线程数目还是保持在最大数量了。
# 而此线程池会自动缩小，实现了java线程池的keppalivetime功能。

time.sleep(1000000)
```
