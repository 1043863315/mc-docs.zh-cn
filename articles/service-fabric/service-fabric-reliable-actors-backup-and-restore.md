---
title: 备份和还原 Azure Service Fabric 执行组件
description: 了解如何在 Azure Service Fabric 执行组件中实现备份和还原。
author: rockboyfor
ms.topic: conceptual
origin.date: 10/29/2018
ms.date: 01/13/2020
ms.author: v-yeche
ms.openlocfilehash: e88634df11de2afc44a6f82d0beb257b242c68c8
ms.sourcegitcommit: 713136bd0b1df6d9da98eb1da7b9c3cee7fd0cee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75742415"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>实现 Reliable Actors 备份和还原

> [!NOTE]
> Azure 建议使用[定期备份和还原](service-fabric-backuprestoreservice-quickstart-azurecluster.md)来配置可靠有状态服务和 Reliable Actors 的数据备份。 
> 

在下面的示例中，自定义执行组件服务通过利用已存在于 `ActorService` 中的远程侦听器公开备份执行组件数据的方法：

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```

在本示例中，`IMyActorService` 是一个实现 `IService` (C#) 和 `Service` (Java)，并由 `MyActorService` 实现的远程协定。 通过添加此远程协定，并通过 `ActorServiceProxy` 创建远程代理，现在 `IMyActorService` 的方法也可用于客户端：

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

有关 Reliable Actors 的详细信息，请阅读以下文章：
* [执行组件状态管理](service-fabric-reliable-actors-state-management.md)
* [执行组件生命周期和垃圾回收](service-fabric-reliable-actors-lifecycle.md)
* [执行组件 API 参考文档](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET 代码示例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 代码示例](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->

[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

<!-- Update_Description: update meta properties, wording update -->
