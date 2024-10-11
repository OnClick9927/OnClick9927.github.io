---
title: Could not produce class with ID
author: 灵
comment: 'on'
tags:
  - Unity
category:
  - Unity
date: 2024-06-18 21:29:37

---
创建 Assets目录下 link.xml
``` xml
<linker>
	<assembly fullname="IFramework" preserve="all"/>
	<assembly fullname="UnityEngine">
		<type fullname="UnityEngine.BoxCollider" preserve="all"/>
		<type fullname="UnityEngine.SkinnedMeshRenderer" preserve="all"/>
		<type fullname="UnityEngine.ParticleSystemRenderer" preserve="all"/>
		<type fullname="UnityEngine.ParticleSystem" preserve="all"/>
		<type fullname="UnityEngine.AudioListener" preserve="all"/>
	</assembly>
</linker>
```
[查id的   网页](https://docs.unity3d.com/Manual/ClassIDReference.html)

