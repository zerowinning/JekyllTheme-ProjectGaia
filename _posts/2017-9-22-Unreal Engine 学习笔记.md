### 学习网站
+ 官网    [https://docs.unrealengine.com](https://docs.unrealengine.com)
+ sss

### 学习目录
+ 

### 学习表
+ 编程指南
+ 编程快速入门
+ UE4 中的 C++ 编程介绍
+ 使用虚幻引擎中的C++的导论
+ C++ 编程教程

#### 编程指南
用UPROPETY宏来设置编辑器与蓝图中的属性，公开给设计师使用
```
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category="Damage")
int32 TotalDamage;
```
```
AMyActor::AMyActor()
{
    TotalDamage = 200;
    DamageTimeInSeconds = 1.f;
}

AMyActor::AMyActor() :
    TotalDamage(200),
    DamageTimeInSeconds(1.f)
{
}
```
支持热重载

意下图中选中的基类名显示为 MyActor，而非 AMyActor。这是刻意设置的结果。对设计师隐藏工具使用的命名规则，使命名更加浅显易懂。

最佳方法是使用 C++ 构建基础游戏性系统和与性能关系密切的代码，而蓝图则用于自定义行为或从 C++ 构建块创建合成行为。

对象迭代器是非常实用的工具，用于在特定 UObject 类型和子类的所有实例上进行迭代。
```
// 将找到当前所有的 UObjects 实例
for (TObjectIterator<UObject> It; It; ++It)
{
    UObject* CurrentObject = *It;
    UE_LOG(LogTemp, Log, TEXT("Found UObject named:%s"), *CurrentObject.GetName());
}
```


