---
title: 3-WooAudio-初始化
author: yy
comment: 'on'
tags:
  - WooAudio
category:
  - 文档
  - WooAudio
date: 2025.3.19 23:11:00
---

# 初始化
* WooAudio使用前需要先进行的必要操作

``` csharp
//脚本需要先继承 IAudioPrefRecorder、IAudioAsset 接口

void Start()
{
    // 初始化音频系统，传入当前实例作为 IAudioPrefRecorder 和 IAudioAsset 实现
    Audio.Init(this, this);
    //设置通道默认音量
    Audio.SetDefaultVolume(0, 0.7f);
    Audio.SetDefaultVolume(1, 0.1f);
    //设置AudioConfig文件
    Audio.SetConfig(AudioConfig);
    //生成音频配置的映射表，方便通过音频 ID 快速获取配置信息
    AudioConfig.GenMap();
}

//方法 Read 实现 IAudioPrefRecorder 接口，从 PlayerPrefs 中读取音频偏好设置
public AudioPref Read()
{
   //使用 JsonUtility 从 PlayerPrefs 中读取音频偏好设置的 JSON 字符串并反序列化为 AudioPref 对象
   return JsonUtility.FromJson<AudioPref>(PlayerPrefs.GetString(nameof(AudioPref)));
}

//方法 Write 实现 IAudioPrefRecorder 接口，将音频偏好设置写入 PlayerPrefs
public void Write(AudioPref pref)
{
    // 使用 JsonUtility 将 AudioPref 对象序列化为 JSON 字符串，并保存到 PlayerPrefs 中
    PlayerPrefs.SetString(nameof(AudioPref), JsonUtility.ToJson(pref));
}

//方法 CreateContext 实现 IAudioAsset 接口，创建并返回一个 AudioAssetContext 实例
public IAudioAssetContext CreateContext(string path)
{
    return new AudioAssetContext();
}

//定义一个内部类 AudioAssetContext，用于实现 IAudioAssetContext 接口，管理音频资源的加载和释放
private class AudioAssetContext : IAudioAssetContext
{
    // 属性 isDone 表示音频资源是否加载完成，通过检查 clip 是否为空来判断
    public bool isDone => clip != null;
    // 方法 GetClip 返回加载的音频剪辑
    public AudioClip GetClip()
    {
        return clip;
    }
    // 私有变量 clip 用于存储加载的音频剪辑
    private AudioClip clip;
    // 方法 Load 用于加载指定路径的音频资源
    public void Load(string path)
    {
        clip = AssetDatabase.LoadAssetAtPath<AudioClip>(path);
    }
    // 方法 Release 用于释放音频资源（可添加资源卸载逻辑）
    public void Release(string path)
    {

    }
}

// Update 方法每帧被调用一次，用于处理用户输入和播放音频
void Update()
{
    // 如果按下 A 键，播放音频 ID 为 0 的音频
    if (Input.GetKeyDown(KeyCode.A))
    {
        Audio.Play(0);
    }

    // 如果按下 D 键，播放音频 ID 为 1 的音频
    if (Input.GetKeyDown(KeyCode.D))
    {
        Audio.Play(1);
    }
    }


```