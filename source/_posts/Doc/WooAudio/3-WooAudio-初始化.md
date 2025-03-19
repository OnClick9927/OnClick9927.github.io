---
title: 1-WooAudio-初始化
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
    //初始化 并设置通道音量 设置AudioConfig文件 
    Audio.Init(this, this);
    Audio.SetDefaultVolume(0, 0.7f);
    Audio.SetDefaultVolume(1, 0.1f);
    Audio.SetConfig(AudioConfig);
    AudioConfig.GenMap();
}

//实现这三个方法
public AudioPref Read()
{
   return JsonUtility.FromJson<AudioPref>(PlayerPrefs.GetString(nameof(AudioPref)));
}

public void Write(AudioPref pref)
{
    PlayerPrefs.SetString(nameof(AudioPref), JsonUtility.ToJson(pref));
}

public IAudioAssetContext CreateContext(string path)
{
    return new AudioAssetContext();
}

//在内部实现类 
private class AudioAssetContext : IAudioAssetContext
{
    public bool isDone => clip != null;

    public AudioClip GetClip()
    {
        return clip;
    }
    private AudioClip clip;
    public void Load(string path)
    {
        clip = AssetDatabase.LoadAssetAtPath<AudioClip>(path);
    }

    public void Release(string path)
    {

    }
}
```