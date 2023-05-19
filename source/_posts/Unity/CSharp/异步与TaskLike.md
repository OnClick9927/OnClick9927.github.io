---
title: 异步与Task-Like
author: YueZhenpeng
comment: on

tags:
  - C#
  - Async
category:
  - C#
  - Coroutine

date: 2023-05-19 07:57:04
---

### 文末有反编译IL代码，文首有原理分析！！！
[我的项目地址-github](https://github.com/yueh0607/AirFramework/tree/main/Framework/Core/Async)这个地址有可能要求梯子，球球了，觉得好就点个star吧
如果上面地址进不去，就进这个[”gitee地址“](https://gitee.com/imyueh/AirFramework/tree/main/Assets/Framework/Core/Async)这个更新可能不及时，但是作为参考没问题
# 1.异步
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202303241053728.png)

```csharp
//这个方法在开始时自动被调用
void Start()
{
    AAA();
    //这个代表输出到控制台”666“
    666.L();
}
//每隔一秒将输出一个数字，同时不会阻塞主线程的行为
public async void AAA()
    {
        await Async.Delay(1);
        1.L();
        await Async.Delay(1);
        2.L();
        await Async.Delay(1);
        3.L();
        await Async.Delay(1);
        4.L();
    }
```
按照同步的逻辑分析，666应该输出在1234之后，但是在异步中不同，AAA方法不会停留，而是继续向下，可以理解为新开了一个线程执行AAA方法，但是是假的，实际上还是在主线程里。

## 原理分析(结合后面一起看)
1.Start方法执行到AAA进入方法
2.调用AsyncTaskMethodBuilder.Create静态创建并返回Builder
3.进入Delay获取Delay返回的AsyncTask对象
4.await等待AsyncTask时调用AsyncTask.GetAwaiter返回AsyncTask(IAwiter)
5.调用OnCompleted方法，将continuation委托更新为await以后的代码
5.等待Dealy方法内的Timer对象完成回调AsyncTask.SetResult方法
6.调用SetResult后，执行continuation委托，执行await之后的代码
7.进入下一个await

以上大错特错，其实这个过程是在编译时生成了一个异步状态机。用代码生成的方式展开了这个异步的过程，而不是在调用来调用去。


总结：OnCompleted的continuation就是await之后，直到方法结束的代码，每次执行到await都会执行来更新到下一个段落，此前会检查IsCompleted，然后需要我们调用SetResult才能切换到下一个状态。
# 2.自定义await支持
首先毫无道理的讲，**C#就要求我们定义的类满足这个要求才能像下面这么写**，原因简单粗暴：编译器要调用这个方法，按照要求做就可以。

```csharp
await new AsyncTask();
```
<font color=red>  **要想使得类支持await关键字的唯一要求：** **包含GetAwaiter方法**</font>(也可以是拓展方法)
我们当然不允许项目内出现这样的无礼要求！！！用接口约束可等待的类，<font color=red>  **只要实现了这个接口，就能被await关键字支持。**</font>
```csharp
    public interface IAwaitable<out TAwaiter> where TAwaiter : IAwaiter
    {
        TAwaiter GetAwaiter();
    }
    public interface IAwaitable<out TAwaiter,out TResult> where TAwaiter : IAwaiter<TResult>
    {
        TAwaiter GetAwaiter();
    }
```
我们可以看到GetAwaiter方法返回了一个IAwaiter，接下来我们尝试自定义Awaiter

# 3.自定义Awaiter

<font color=red>**System.Runtime.CompilerServices**</font>命名空间下提供了这样两个接口，<font color=red>**编译器要求Awaiter必须实现其中一个接口**</font>
```csharp
	public interface INotifyCompletion
    {
        void OnCompleted(Action continuation);
    }
    public interface ICriticalNotifyCompletion : INotifyCompletion
    {
        void UnsafeOnCompleted(Action continuation);
    }
```

![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202303241053728.jpeg)
**关于Awaiter的要求：**

1. <font color=red>**有bool IsCompleted { get; }**   </font>
2. <font color=red>**有GetResult方法**</font>

关于IsCompleted，异步流程执行到await时，会检查IsCompleted，如果为true，则表明任务已经完成，await直接结束。


```csharp
int value = await GetValueAsync();
```
关于GetResult也很好理解，我们不陌生上面这样的语法，在await被结束时，会调用GetResult方法返回一个值。


```csharp
    public interface IAwaiter : INotifyCompletion
    {
        bool IsCompleted { get; }
        void GetResult();
    }
    
    public interface IAwaiter<T> : INotifyCompletion
    {
        bool IsCompleted { get; }
        T GetResult();
    }

    public interface ICriticalAwaiter :ICriticalNotifyCompletion,IAwaiter
    {
    }

    public interface ICriticalAwaiter<T>:ICriticalNotifyCompletion,IAwaiter<T>
    {
    }
```
在上面我们定义了四个接口，都是与Awaiter相关的，CriticalAwaiter不同的是UnsafeOnCompleted方法，在异步执行的代码可能给程序产生负面影响(如异常)时，我们可以使用这个Awaiter接口。
# 3. 自定义异步任务接口
我们进一步拓展，给异步任务更多的功能，这些不是编译器的要求了，而是我们自己的需要。
当任务完成时，**我们可以手动调用**SetResult，在出现异常时调用SetException。
```csharp
 public interface IAsyncTask : ICriticalAwaiter
    {   
        void SetResult();
        void SetException(Exception exception);
    }


    public interface IAsyncTask<T> : ICriticalAwaiter<T> 
    {
    	//参数就是GetResult拿到的结果
        void SetResult(T result);
        void SetException(Exception exception);
    }
```
# 4.实现异步任务类
**千万记住要加上这个特性**，只有这样才能指定AsyncTask作为返回值时进行AsyncTaskMethodBuilder自动创建，泛型时写	**[AsyncMethodBuilder(typeof(AsyncTaskMethodBuilder<>))]**
```csharp
	//特性指定AsyncMethodBuilder
	[AsyncMethodBuilder(typeof(AsyncTaskMethodBuilder))]
    public partial class AsyncTask : PoolableObject<AsyncTask>, IAsyncTask
    {
        public AsyncTask GetAwaiter() => this;
        public Action continuation;
        public Exception Exception { get; private set; }
        public bool IsCompleted { get; set; }
       
        public void OnCompleted(Action continuation)
        {
            UnsafeOnCompleted(continuation);
        }

        public void UnsafeOnCompleted(Action continuation)
        {
            this.continuation = continuation;
        }

        public void SetException(Exception exception)
        {
            IsCompleted= true;
            this.Exception = exception;
        }
		//啥也不返回
        public void GetResult(){ }

        public void SetResult()
        {
            //执行await以后的代码
            continuation?.Invoke();
            //回收到Pool内
            this.Dispose();
        }
    }
```
# 5.AsyncTaskMethodBuilder
没错，这些又是编译器的无礼要求，已经快要成模板了
```csharp
 public struct AsyncTaskMethodBuilder
    {
        private AsyncTask task;

        // 1. Static Create method 编译器调用静态创建方法来创建Builder
        [DebuggerHidden]
        public static AsyncTaskMethodBuilder Create() => new AsyncTaskMethodBuilder(AsyncTask.Create(fromPool:true));

        //2.Construct Method 构造Builder时调用
        public AsyncTaskMethodBuilder(AsyncTask task) => this.task = task;


        // . TaskLike Task property.
        [DebuggerHidden]
        public AsyncTask Task => task;

        // 3. Start 构造之后开启状态机
        [DebuggerHidden]
        public void Start<TStateMachine>(ref TStateMachine stateMachine) where TStateMachine : IAsyncStateMachine
        {
            stateMachine.MoveNext();
        }

        // 4. SetException 当出现异常时编译器调用，将异常绑定到任务
        [DebuggerHidden]
        public void SetException(Exception exception)
        {
            task.SetException(exception);
        }

        // 5. SetResult 当任务成功完成时编译器调用这个方法,将该任务标记为已成功完成
        [DebuggerHidden]
        public void SetResult()
        {
            task.SetResult();
        }

        // 6. AwaitOnCompleted  在SetResult之后编译器调用OnCompleted
        [DebuggerHidden]

        public void AwaitOnCompleted<TAwaiter, TStateMachine>(ref TAwaiter awaiter, ref TStateMachine stateMachine) where TAwaiter : INotifyCompletion where TStateMachine : IAsyncStateMachine
        {
            awaiter.OnCompleted(stateMachine.MoveNext);
        }

        // 7. AwaitUnsafeOnCompleted 同OnCompleted
        [SecuritySafeCritical]
        public void AwaitUnsafeOnCompleted<TAwaiter, TStateMachine>(ref TAwaiter awaiter, ref TStateMachine stateMachine) where TAwaiter : ICriticalNotifyCompletion where TStateMachine : IAsyncStateMachine
        {
            awaiter.OnCompleted(stateMachine.MoveNext);
        }

        // 9. SetStateMachine  将生成器与指定的状态机相关联
        [DebuggerHidden]
        public void SetStateMachine(IAsyncStateMachine stateMachine)
        {

        }
    }
```

不用担心这个OnCompleted参数委托的GC问题，因为代码会在编译时被静态编译为一个状态机，实际上编译后的IL代码不是我们所见的这样。
# 6.Delay方法
我写了一个例子来使用这个AsyncTask，由于全部从池内取出，并没有GC产出，而C#原生的Task类型显得十分臃肿，带有高昂的GC代价。
```csharp
 public static AsyncTask Delay(float seconds)
        {
            var task = Framework.Pool.Allocate<AsyncTask>();
            var timer = Framework.Pool.Allocate<TimerCall>();
            timer.OnCompleted += task.SetResult;
            timer.OnCompleted += timer.Dispose;
            timer.Start(TimeSpan.FromSeconds(seconds));
            return task;
        }
```
# C # 3.0 反编译 System.Tasks.Task 的 IL 代码
被反编译的代码
```csharp
        public async Task TaskTest()
        {
            await Task.Run(() => { });
        }
```
反编译结果IL代码
```csharp
    [CompilerGenerated]
    private sealed class <TaskTest>d__1 : IAsyncStateMachine
    {
        public int <>1__state;

        public AsyncTaskMethodBuilder <>t__builder;

        public ClassA <>4__this;

        private TaskAwaiter <>u__1;

        private void MoveNext()
        {
            int num = <>1__state;
            try
            {
                TaskAwaiter awaiter;
                if (num != 0)
                {
                    awaiter = Task.Run(delegate
                    {
                    }).GetAwaiter();
                    if (!awaiter.IsCompleted) //await修饰的状态未完成？把控制器交由该类
                    {
                        num = (<>1__state = 0);
                        <>u__1 = awaiter;
                        <TaskTest>d__1 stateMachine = this;
                        <>t__builder.AwaitUnsafeOnCompleted(ref awaiter, ref stateMachine);
                        return;
                    }
                }
                else
                {
                    awaiter = <>u__1;
                    <>u__1 = default(TaskAwaiter);
                    num = (<>1__state = -1);
                }
                awaiter.GetResult();
            }
            catch (Exception exception)
            {
                <>1__state = -2;
                <>t__builder.SetException(exception);
                return;
            }
            <>1__state = -2;
            <>t__builder.SetResult();
        }

        void IAsyncStateMachine.MoveNext()
        {
            //ILSpy generated this explicit interface implementation from .override directive in MoveNext
            this.MoveNext();
        }

        [DebuggerHidden]
        private void SetStateMachine(IAsyncStateMachine stateMachine)
        {
        }

        void IAsyncStateMachine.SetStateMachine(IAsyncStateMachine stateMachine)
        {
            //ILSpy generated this explicit interface implementation from .override directive in SetStateMachine
            this.SetStateMachine(stateMachine);
        }
    }

    [AsyncStateMachine(typeof(<TaskTest>d__1))]
    [DebuggerStepThrough]
    public Task TaskTest()
    {
        <TaskTest>d__1 stateMachine = new <TaskTest>d__1();
        stateMachine.<>4__this = this;
        stateMachine.<>t__builder = AsyncTaskMethodBuilder.Create(); //创那就状态机
        stateMachine.<>1__state = -1; //初始状态-1
        AsyncTaskMethodBuilder <>t__builder = stateMachine.<>t__builder;
        <>t__builder.Start(ref stateMachine); //启动状态机
        return stateMachine.<>t__builder.Task; //返回值
    }
```

