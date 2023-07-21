---
title: 1-WooBind-案例
author: yy
comment: 'on'
tags:
  - WooBind
category:
  - 文档
  - WooBind
date: 2023-07-05 00:53:57
---

# ObservableObject

## Subscribe
* Subscribe函数用于注册数值变化的监听函数。当属性发生变化时，会触发已注册的监听函数。具体使用方式如下：
```csharp
public void Subscribe(string propertyName, Action listener)
``` 
  * propertyName：要监听的属性名称。
  * listener：监听函数，当属性变化时被调用。
```csharp
Subscribe("Name", () =>
{
    Console.WriteLine("属性Name发生了变化！");
});
```

## UnSubscribe
* UnSubscribe函数用于取消注册数值变化的监听函数。如果之前通过Subscribe函数注册了一个或多个监听函数，但后来不再需要监听，则可以通过UnSubscribe函数进行取消注册。具体使用方式如下：
```csharp
public void UnSubscribe(string propertyName, Action listener)
```
* propertyName：要取消监听的属性名称。
* listener：要取消的监听函数。

```csharp 
UnSubscribe("Name", () =>
{
    Console.WriteLine("属性Name发生了变化！");
});
```
## GetProperty
  ```csharp
protected T GetProperty<T>(ref T property, [CallerMemberName]string propertyName = "")
  ```

* T：属性值的类型。
* property：表示要获取的属性的变量的引用。
* propertyName：表示属性的名称，默认值为调用该方法的成员的名称（通过[CallerMemberName]属性实现）。

* GetProperty函数用于获取属性的值。它接收一个属性变量的引用以及可选的属性名称。如果使用可选的属性名称参数，则可以在调用时指定属性的名称，例如GetProperty(ref myVariable, "MyProperty")；如果不提供属性名称参数，则默认使用调用该方法的成员的名称作为属性的名称。该函数会返回属性的当前值。

## SetProperty
## GetProperty
  ```csharp
protected void SetProperty<T>(ref T property, T value, [CallerMemberName]string propertyName = "")
  ```

  * T：属性值的类型。
* property：表示要设置的属性的变量的引用。
* value：表示要设置的属性的新值。
* propertyName：表示属性的名称，默认值为调用该方法的成员的名称（通过[CallerMemberName]属性实现）。
* SetProperty函数用于设置属性的值。它接收一个属性变量的引用、新的属性值以及可选的属性名称。如果使用可选的属性名称参数，则可以在调用时指定属性的名称，例如SetProperty(ref myVariable, newValue, "MyProperty")；如果不提供属性名称参数，则默认使用调用该方法的成员的名称作为属性的名称。该函数会更新属性的值，并在值发生变化时发布属性更改通知。



# ObservableObjectHandler
## Subscribe
```csharp
internal ObservableObjectHandler Subscribe(ObservableObject _object, string propertyName)
```
* ObservableObjectHandler：方法的返回类型为 ObservableObjectHandler，表示该方法将返回一个可观察对象处理器的实例。这个处理器可以用来管理和控制属性变化的监听。

* _object：ObservableObject 类型的参数 _object 是要订阅的可观察对象实例。你可以将你感兴趣的可观察对象传递给该参数。

* propertyName：string 类型的参数 propertyName 是要订阅的属性名称。你可以指定你希望监听的特定属性的名称。

```csharp
  public ObservableObjectHandler Subscribe(ObservableObject _object, string propertyName, Action listener)
```
* listener：Action 类型的参数 listener 是一个回调函数，表示在属性变化时要执行的操作。你可以将你希望在属性变化时执行的逻辑封装在这个回调函数中，例如更新 UI、执行其他方法等。
  

##  UnSubscribe()
```csharp
 public void UnSubscribe()
```
* 取消所有监听

```csharp
public void UnSubscribe(ObservableObject _object, string propertyName)
```
* 取消符合条件的监听


# BindableObject
## Subscribe
```csharp
public void Subscribe(string propertyName, Action<string, object> listener)
```
* 注册监听器，订阅属性变化事件。

* propertyName：要监听的属性名称。
* listener：当属性变化时要执行的回调方法。

## UnSubscribe
```csharp 
public void UnSubscribe(string propertyName, Action<string, object> listener)
```
* 移除监听器，取消订阅属性变化事件。

* propertyName：要取消监听的属性名称。
* listener：要移除的监听器回调方法。


# Subscribe
```csharp
internal BindableObjectHandler Subscribe(BindableObject _object, Type propertyType, string propertyName)
```
* 用于订阅对象的属性变化。
* _object：需要订阅属性变化的对象。
* propertyType：属性的数据类型，用于指定要订阅的属性的类型。
* propertyName：要订阅的属性名称。

# GetValue
```csharp
public T GetValue<T>(string name)
public object GetValue(Type type,string name)
```
* 用于获取指定名称的属性值

# PublishProperty
```csharp
   public BindableObjectHandler PublishProperty(Type type,object value, string propertyName)
   public BindableObjectHandler PublishProperty<T>(T value, string propertyName)
```
* 用于发布属性的变化，通知所有订阅了该属性的监听者。
  
# UnBind
  ```csharp
  public void UnBind()
  public void UnBind(BindableObject _object)
  public void UnBind(string propertyName)
  public void UnBind(BindableObject _object, string propertyName)
  public void UnBind(BindableObject _object, Type type, string propertyName)
 ```

  * 解绑全部绑定的属性。
  * 按照对象解绑属性。
  * 按照属性名称解绑属性。
  * 按照对象和属性名称解绑属性。
  * 按照对象、属性类型和属性名称解绑属性。