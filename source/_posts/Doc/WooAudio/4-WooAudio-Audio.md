---
title: 1-WooAudio-Audio
author: yy
comment: 'on'
tags:
  - WooAudio
category:
  - 文档
  - WooAudio
date: 2025.3.19 23:11:00
---

# Audio
Audio 组件是 WooAudio 插件的核心管理类，负责集中管理和操作音频功能，以下是其功能描述：


# 初始化与配置
Init(IAudioPrefRecorder recorder, IAudioAsset asset)
* 功能：初始化音频系统，设置音频偏好记录器和音频资源管理器。
* 参数：
    * recorder：音频偏好记录器，用于读取和写入音频偏好设置。
    * asset：音频资源管理器，用于加载和管理音频资源。

SetConfig(IAudioConfig config)
* 功能：设置音频配置。
* 参数：
    * config：音频配置接口，提供音频的相关设置。
  
# 音频播放控制
Play(int sound_id)
* 功能：播放指定音频。
* 参数：
    * sound_id：音频的唯一标识符。

Stop(int sound_id, bool all = false)
* 功能：停止播放指定音频。
* 参数：
    * sound_id：音频的唯一标识符。
    * all：是否停止所有实例，默认为 false。

StopChannel(int channel)
* 功能：停止指定通道的所有音频播放。
* 参数：
    * channel：音频通道。

StopAllChannel()
* 功能：停止所有通道的音频播放。


# 音量管理
SetVolume(int channel, float volume)
* 功能：设置指定通道的音量。
* 参数：
    * channel：音频通道。
    * volume：音量值，范围为 0 到 1。

GetVolume(int channel)
* 功能：获取指定通道的当前音量。
* 参数：
    * channel：音频通道。
* 返回值：当前音量值Float。

# 音频偏好保存
SetDefaultVolume(int channel, float vol)
* 功能：设置指定通道的默认音量。
* 参数：
    * channel：音频通道。
    * vol：默认音量值。


