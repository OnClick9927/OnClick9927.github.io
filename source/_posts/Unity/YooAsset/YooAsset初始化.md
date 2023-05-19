---
title: YooAsset初始化
author: YueZhenpeng
comment: '学会YooAsset的初始化'

tags:
  - Unity
  - YooAsset
category:
  - Unity
  - YooAsset

date: 2023-05-19 07:57:04

---

#  零、导入YooAsset

## 0.注意结合官方文档一起看，他写的很清晰

> [官方文档](https://www.yooasset.com/docs/Introduce)

## 1.UPM导入
在ProjectSetting里找到这个，这么填
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305191648812.png)

> package.openupm.cn
> https://package.openupm.cn
> com.tuyoogame.yooasset

然后在PackageManager里，选择MyRegistry ，导入YooAsset
# 一、编辑器

## 0.创建YooAsset设置文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/fdf9851314224fe68cc3e5f82ecbbaf4.png)
在使用这些东西之前，我们需要在Project面板右键，选择YooAsset/Create Setting创建这个文件
然后MainfestFileName就是我们的资源清单文件名，(不改也没事，不影响使用，只是名字)![在这里插入图片描述](https://img-blog.csdnimg.cn/e34908e9d0cb4866adf1c86f4c923eba.png)![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305191648751.png)

## 1.AssetBundle Collector
![在这里插入图片描述](https://img-blog.csdnimg.cn/d000d17c75f64b3b9c3e38d5c523b1d2.png)
第一个是是否显示包，第二个是是否采用中文，第三个是是否开启寻址，第四个是在包名前面加东西保证包名唯一。
接下来我们把前三个打勾，按下面的加号创建一些东西。
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305191648779.png)
其中Package就是包了，Group是分组，而Collector是收集器。
我们能编写自定义的收集规则，当Collector选择目录时，我们可以用自己的脚本来控制选择什么样的资源。

我们简单选择几个资源
![在这里插入图片描述](https://img-blog.csdnimg.cn/4707ea7b40cb40a89df3741e9c0564a1.png)
## 2. Builder
![在这里插入图片描述](https://img-blog.csdnimg.cn/9e4f904fbc8d46b7afa47ee064d60e90.png)
第二个是构建管线，默认是默认的，否则可以改成可编程的（不介绍）。
第三是构建模式，**ForceRebuild会删除原来的AB，重新打新的**。
而**Incremental是增量更构建，也就是打出来的包是多出来的资源，不会移除也不会生成之前的**，所以在**更新到CDN时可以直接覆盖到原来的包上**，或者差异分析上传
然后是**DryRun,只会产生一些Mainfest文件，不会打包，用于演练，**。
接下来是**Simulate，需要配合特殊的初始化方式才能模拟构建，只在编辑器有效**![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305191648951.png)
然后是版本号，这个版本号随着时间自动生成，不需要处理
Compressaion是压缩方式，LZ4时间略微优异，性价比比较高，LZMA保证最大的压缩率，其实差距不大，太大不会放在这里，说明都可用。
Encryption是加密方式，需要自己写一个，好像要实现一个接口，在[YooAsset官方文档](https://www.yooasset.com/docs/api/YooAsset/IEncryptionServices)有的
最后一个就是是否拷贝到StreamingAsset
![在这里插入图片描述](https://img-blog.csdnimg.cn/28eecdbd5e324027ab08a370e84a618a.png)
我们能根据Group的标签区分。
## 3.Reporter
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305191648070.png)
在目录里，Bundles是打包输出路径，SandBox是AB从服务器下载缓存的路径
我们进入Reporter，选择导入，选择这个打出的以时间命名的包下面的一个文件
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305191648309.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/b1b82b44f209442780bf264c5f3e8695.png)
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305191648064.png)
就这个用处，简单吧，还有一个调试器，这个是在运行时检测资源直接有没有循环引用问题的，基于引用计数的方案最害怕这个。
# 二、代码
## 0.每一个模式都要做的初始化

```csharp
// 初始化资源系统
YooAssets.Initialize();
// 创建默认的资源包，这里是需要先在Collector里创建同名Package的
var package = YooAssets.CreatePackage("DefaultPackage");
// 设置该资源包为默认的资源包，可以使用YooAssets相关加载接口加载该资源包内容。
YooAssets.SetDefaultPackage(package);
```
## 0.1 选择一个运行模式

```csharp
 public EPlayMode PlayMode = EPlayMode.EditorSimulateMode;
switch (PlayMode)
        {
            case EPlayMode.EditorSimulateMode:
                {
                   //开启协程
                    break;
                }
            case EPlayMode.OfflinePlayMode:
                {
                //开启协程
                    break;
                }
            case EPlayMode.HostPlayMode:
                {
                           //开启协程
                    break;
                }
        }
```

## 1.编辑器模拟运行
执行完这个协程之后，就可以任意加载，不过**注意只能在编辑器里用**，传入的package就是上面创建的DefaultPackage
```csharp
  private IEnumerator EditorInitializeYooAsset(ResourcePackage package)
    {
        var initParameters = new EditorSimulateModeParameters();
        initParameters.SimulateManifestFilePath = EditorSimulateModeHelper.SimulateBuild("DefaultPackage");
        yield return package.InitializeAsync(initParameters);
    }
```
## 2. 单机模式运行
这个运行模式**要求在打AB包的时候，在调整最后一个设置选择，把AB包拷贝到streamingAsset/buildIn里面**，不然加载资源会失败！！注意不是自己手动拷贝
```csharp
 private IEnumerator SingleInitializeYooAsset(ResourcePackage package)
    {
        var initParameters = new OfflinePlayModeParameters();
        yield return package.InitializeAsync(initParameters);
    }
```

## 3.联网模式运行
### （0）本地测试环境
首先我们从网上找个软件叫做HFS，Http File Server，搜一下就有。
![在这里插入图片描述](https://img-blog.csdnimg.cn/ba7f19fc165941ff94b95d6e71807fbc.png)
把右边的FTP文件夹拖进去。我们就能访问这个地址了
![在这里插入图片描述](https://raw.githubusercontent.com/yueh0607/MyPicueres/main/202305191648399.png)
然后把我们的AB包扔进去

### （1）初始化资源包
```csharp
public string netPath = "http://127.0.0.1:9999/FTP/2023-04-20-1108";


 private IEnumerator NetInitializeYooAsset(ResourcePackage package)
    {
        var initParameters = new HostPlayModeParameters();
        
        initParameters.QueryServices = new QueryStreamingAssetsFileServices();
        
        //主资源服务器地址
        initParameters.DefaultHostServer = netPath;
        //备用资源服务器地址
        initParameters.FallbackHostServer = netPath;
        
        yield return package.InitializeAsync(initParameters);
    }
    
 // 内置文件查询服务类，这个类只需要返回ApplicationstreamingAsset下面的文件存在性就好
    private class QueryStreamingAssetsFileServices : IQueryServices
    {
        public bool QueryStreamingAssets(string fileName)
        {
            // StreamingAssetsHelper.cs是太空战机里提供的一个查询脚本。
            string buildinFolderName = YooAssets.GetStreamingAssetBuildinFolderName();
            return File.Exists($"{Application.streamingAssetsPath}/{buildinFolderName}/{fileName}");
        }
    }
```
### （2）更新资源版本

```csharp

string packageVersion = "2023-4-20-1108";
 IEnumerator UpdatePack(ResourcePackage package)
 {
        //2.获取资源版本
        var operation = package.UpdatePackageVersionAsync();
        yield return operation;
        if(operation.Status!=EOperationStatus.Succeed)
        {
            Debug.LogError("版本号更新失败，可能是找不到服务器");
            yield break;
        }
        //这是获取到的版本号，在下一个步骤要用
        packageVersion = operation.PackageVersion;
}
```

### (3) 更新补丁清单

```csharp
 IEnumerator UpdatePack(ResourcePackage package)
    {
        //3.获取补丁清单
        var op=  package.UpdatePackageManifestAsync(packageVersion);
        yield return op;
        if(op.Status!=EOperationStatus.Succeed)
        {
            Debug.LogError("Mainfest更新失败！");
        }
    }
```
### （4）下载补丁包

```csharp

int downloadingMaxNum = 10;
int failedTryAgain = 3;
int timeout = 60;
        
 IEnumerator Download()
    {
        var package = YooAssets.GetPackage("DefaultPackage");
        var downloader = package.CreateResourceDownloader(downloadingMaxNum, failedTryAgain, timeout);
        //下载数量是0，直接就完成了
        if(downloader.TotalDownloadCount==0)
        {
            yield break;
        }

		//注册一些回调
        downloader.OnDownloadErrorCallback += OnError;
        downloader.OnDownloadProgressCallback += OnProcess;
        downloader.OnDownloadOverCallback += OnOver;
        downloader.OnStartDownloadFileCallback += OnStartDownOne;

		//开始下载
        downloader.BeginDownload();
        //等待下载完成
        yield return downloader;
		//检查状态
        if(downloader.Status==EOperationStatus.Succeed)
        {
            Debug.Log("下载完成");
        }
        else
        {
            Debug.Log("下载失败");
        }
    }
```

当这些步骤执行完，初始化完毕。随心所欲的使用yooasset吧。

# 三、针对各个平台的方案
## 1.编辑器模拟
为了加载更快，我们可以专门的按照上面的编辑器模拟初始化方式进行运行，节约开发时间。
## 2.纯单机不联网
只需要在打AB的时候调最后一个选择，拷贝到streamingAsset/BuiltIn
然后按照上面给出的单机初始化方式进行即可。
## 3.单机弱连网
先按照联网模式的初始化步骤走，然后在获取版本号那一步，如果成功则继续按联网的走，否则直接按

```csharp
private IEnumerator Start()
{
//先获取包
    var package = YooAssets.GetPackage("DefaultPackage");
    //更新版本号
    var operation = package.UpdatePackageVersionAsync(60);
    yield return operation;

    if (operation.Status == EOperationStatus.Succeed)
    {
        // 如果获取远端资源版本成功，说明当前网络连接通畅，可以走正常更新流程。继续从服务器按照联网模式更新
    }
    else
    {
        // 如果获取远端资源版本失败，说明当前网络无连接。
        // 在正常开始游戏之前，需要验证本地清单内容的完整性。
        //如果清单都没了，那就不用继续了，请联网进行清单的补全
        string packageVersion = package.GetPackageVersion();
        var operation = package.PreDownloadContentAsync(packageVersion);
        yield return operation;
        if (operation.Status != EOperationStatus.Succeed)
        {
             ShowMessageBox("请检查本地网络，有新的游戏内容需要更新！");
            yield break;
        }

		//注意这里没真的开始下载，只是用本地清单看看需要下载数量有几个
        int downloadingMaxNum = 10;
        int failedTryAgain = 3;
        int timeout = 60;
        var downloader = operation.CreateResourceDownloader(downloadingMaxNum, failedTryAgain, timeout);
        if (downloader.TotalDownloadCount > 0)   
        {
            // 资源内容本地并不完整，需要提示玩家联网更新。
            ShowMessageBox("请检查本地网络，有新的游戏内容需要更新！");
            yield break;
        }
       
        // 开始游戏
        StartGame();
    }
}
```



## 4.联网
按照联网模式走就行，有一步失败就不能让玩家进去