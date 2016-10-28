---
layout:     post
title:      "iOS持久化存储 归档存储一组自定义类"
subtitle:   ""
date:       2016-10-26
author:     "王寅"
header-img: "img/post-bg-js-module.jpg"
tags:
    - iOS
    - 归档
---

### 一、为什么要用归档存储数据
目前在做的项目中，有一个这样的存储需求，需要把用户绑定的钥匙、支持的社区做持久化存储，并且钥匙数量不多，社区数量也不多，用plist存储又不能存储自定义类型，所以搜索了一下归档的使用，感觉可以满足需求，但网上的资料都只是讲了一下基本的使用，遂入坑，研究如何存储数组，数组中的对象是自定义类型，试过几次发现此法可行，并且相当轻量级。如果用SQLite或者Core Data的话，有点大材小用。
	
### 二、如何使用

#### 1.获取存储路径
  以我目前项目为例，项目要存储钥匙，并且每个不同的登录账号对应的钥匙都应该是不同的，我们应该考虑这种情况，所以，我们可以用用户的登录名来拼装字符串，这里我简单的封装了一个方法，来获取钥匙的存储数据

```
+ (NSString *)keyFilePath{
    NSString *paths = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject];
    UserModel *usermodel = [UserModel sharedInstanced];
    NSString *filename = [NSString stringWithFormat:@"%@key.key", usermodel.userID];
    NSString *keyFilePath = [paths stringByAppendingString:filename];
    return keyFilePath;
}   
```

### 2.创建需要归档的类

目前的项目中的钥匙类，包含如下属性，**当前类必须实现<NSCoding>协议 (instancetype)initWithCoder:(NSCoder *)aDecoder 和 encodeWithCoder方法，并且给每个属性定义一个唯一的键
**

```
#import <Foundation/Foundation.h>

typedef NS_ENUM(NSInteger, PPKeyType) {
    PPKeyTypeCommunityTheDoor,
    PPKeyTypeUnitDoor,
    PPKeyTypeElevator
};

@interface PPkeyArchiving : NSObject <NSCoding>

@property (nonatomic, copy) NSString *name;     //钥匙的名称
@property (nonatomic, copy) NSString *key;      //钥匙，蓝牙设备名称
@property (nonatomic, copy) NSString *password; //密码
@property (nonatomic, copy) NSString *door;     //门牌号
@property (nonatomic, copy) NSString *label;    //标签
@property (nonatomic, copy) NSString *kid;      //钥匙ID
@property (nonatomic, copy) NSString *status;   //钥匙状态
@property (nonatomic, copy) NSString *owner;    //业主
@property (nonatomic, copy) NSString *phone;    //电话
@property (nonatomic, copy) NSString *community; //所在小区 (中文)
@property (nonatomic, copy) NSString *communityID; //小区ID
@property (nonatomic, assign) PPKeyType keyType;

@end
```
```
//
//  PPkeyArchiving.m
//  PengPeng
//
//  Created by 王寅 on 16/8/16.
//  Copyright © 2016年 wangyin. All rights reserved.
//

#import "PPkeyArchiving.h"

@implementation PPkeyArchiving

- (instancetype)initWithCoder:(NSCoder *)aDecoder{

    if (self = [super init]) {
        self.key = [[aDecoder decodeObjectForKey:@"key"] copy];
        self.name = [[aDecoder decodeObjectForKey:@"name"] copy];
        self.password = [[aDecoder decodeObjectForKey:@"password"] copy];
        self.kid = [[aDecoder decodeObjectForKey:@"kid"] copy];
        self.status = [[aDecoder decodeObjectForKey:@"status"] copy];
        self.label = [[aDecoder decodeObjectForKey:@"label"] copy];
        self.owner = [[aDecoder decodeObjectForKey:@"owner"] copy];
        self.phone = [[aDecoder decodeObjectForKey:@"phone"] copy];
        self.community = [[aDecoder decodeObjectForKey:@"community"] copy];
        self.door = [[aDecoder decodeObjectForKey:@"door"] copy];
        self.communityID = [[aDecoder decodeObjectForKey:@"communityID"] copy];
    }
    return self;
}

-(void)encodeWithCoder:(NSCoder *)aCoder{
    [aCoder encodeObject:self.key forKey:@"key"];
    [aCoder encodeObject:self.name forKey:@"name"];
    [aCoder encodeObject:self.password forKey:@"password"];
    [aCoder encodeObject:self.kid forKey:@"kid"];
    [aCoder encodeObject:self.status forKey:@"status"];
    [aCoder encodeObject:self.label forKey:@"label"];
    [aCoder encodeObject:self.owner forKey:@"owner"];
    [aCoder encodeObject:self.phone forKey:@"phone"];
    [aCoder encodeObject:self.community forKey:@"community"];
    [aCoder encodeObject:self.door forKey:@"door"];
    [aCoder encodeObject:self.communityID forKey:@"communityID"];
}
```

### 3.保存、读取数据过程

```

//创建一个数组 数组中包含的元素必须是我们之前创建的自定义的类。将数组传入这个方法，将数组保存到步骤1里获取到的路径地址，可以根据项目需求来变更这个路径。
+ (void)saveKeysWithNSArray:(NSArray *)array{
    NSMutableArray *keyList = [[NSMutableArray alloc] init];
    [NSKeyedArchiver archiveRootObject:keyList toFile:[[self class] keyFilePath]];
}

//读取数据
+ (NSArray *)readKeysList{
    NSMutableArray *keyList = [[NSKeyedUnarchiver unarchiveObjectWithFile:[[self class] keyFilePath]] mutableCopy];
    if (!keyList) {
        keyList = [[NSMutableArray alloc] init];
    }
    return keyList;
}
```

OK，到此为止，通过以上代码，可以实现把一个数组里，实现了遵守NSCoding协议，并实现该协议中的两个方法的类保存和读取。

### 注意事项

##### 1.遵守NSCoding协议，并实现该协议中的两个方法。

##### 2.尾缀名可以自定义，此文章给出的代码路径可根据需求自行修改。