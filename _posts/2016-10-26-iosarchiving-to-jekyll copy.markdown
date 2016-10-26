---
layout: post
title:  "iOS持久化存储 归档存储一组自定义类"
date:   2016-10-26 10:16:32
categories: iOS 
---
### 一、为什么要用归档存储数据
	目前在做的项目中，有一个这样的存储需求，需要把用户绑定的钥匙、支持的社区做持久化存储，并且钥匙数量不多，社区数量也不多，用plist存储又不能存储自定义类型，所以搜索了一下归档的使用，感觉可以满足需求，但网上的资料都只是讲了一下基本的使用，遂入坑，研究如何存储数组，数组中的对象是自定义类型，试过几次发现此法可行，并且相当轻量级。如果用SQLite或者Core Data的话，有点大材小用。
	
### 二、如何使用
#### 1.获取存储路径
以我目前项目为例，项目要存储钥匙，并且每个不同的登录账号对应的钥匙都应该是不同的，我们应该考虑这种情况，所以，我们可以用用户的登录名来拼装字符串，这里我简单的封装了一个方法，来获取钥匙的存储数据

```cpp
+ (NSString *)keyFilePath{
    NSString *paths = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject];
    UserModel *usermodel = [UserModel sharedInstanced];
    NSString *filename = [NSString stringWithFormat:@"%@key.key", usermodel.userID];
    NSString *keyFilePath = [paths stringByAppendingString:filename];
    return keyFilePath;
}   
```