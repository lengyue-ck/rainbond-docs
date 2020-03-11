---
title: 应用备份管理
description: Rainbond应用全量备份、迁移和恢复
---

### 应用备份、恢复与迁移

Rainbond目前提供应用级的全量备份功能，主要用于两类场景：

* 应用整体的版本备份，包含代码和运行环境，持久化数据，组件&应用配置等所有属性。有了备份，应用即可随时恢复到备份版本。
* 应用迁移，将应用完整迁移到其他团队或其他数据中心。

Rainbond备份功能目前的设计是全量的冷备份机制，传统意义上我们对组件的备份主要是对持久化数据的备份，比如Mysql数据库的数据。在微服务架构的状态下，如果只是备份某一个组件的数据，应用内包含多个组件时很难在出现故障的情况下同步恢复到历史状态。我们对于应用管理的追求必须要做到应用级的完整备份。在容器化的情况下，对于代码和运行环境的备份是容易的，我们可以方便的做到对所有组件的运行环境热备份。但是对于持久化数据，特别是有状态组件的持久化数据，我们不能保证在工作状态下将数据能够安全备份。因此Rainbond目前要求备份应用时需要停止有状态组件。

后续的版本中我们将以operator的方式支持有状态组件的数据热备份，然后接入应用整体备份流程中。即可实现对应用的完整热备份和定期备份。

### 应用备份

从应用的操作列表中即可进入应用备份管理页面，备份操作分为`本地备份`和`云端备份`两种。

* 本地备份：将一组应用备份在本地，本地备份的应用无法进行跨数据中心和租户的迁移操作。
* 云端备份：Rainbond企业版本支持，将备份数据存储于云端。

只需要添加备份，选择备份模式即可，备份是一个异步的过程，且根据组件的数量的不同耗时不同。如果应用下存在运行中的有状态组件，将拒绝备份操作。

<img src="https://grstatic.oss-cn-shanghai.aliyuncs.com/images/docs/5.2/user-manual/app-manage/app-backup/backup.png" width="100%" />

<img src="https://grstatic.oss-cn-shanghai.aliyuncs.com/images/docs/5.2/user-manual/app-manage/app-backup/localbackup.png" width="100%" />

在5.0以后的版本中新添加`全部备份`页面，进入后会展示出当前团队数据中心下的所有备份记录，以便清晰查看，同时也解决了之前版本应用有备份记录无法删除的问题。

<img src="https://grstatic.oss-cn-shanghai.aliyuncs.com/images/docs/5.2/user-manual/app-manage/app-backup/backup02.png" width="100%">

<img src="https://grstatic.oss-cn-shanghai.aliyuncs.com/images/docs/5.2/user-manual/app-manage/app-backup/allbackup.png" width="100%">

### 备份恢复

恢复对于已经备份成功的一组应用，使用恢复可以将该组应用进行恢复操作。恢复通常是在当前应用出现不可解决的问题。
恢复操作如下：
<img src="https://grstatic.oss-cn-shanghai.aliyuncs.com/images/docs/5.2/user-manual/app-manage/app-backup/recovery.png" width="100%">

> 恢复操作过程中请勿关闭恢复页面，否则可能会导致恢复失败。为了保证您的数据安全，恢复操作过程我们会生成一份您的备份应用的拷贝，您可以在恢复的最后一步中选择删除原有的应用。

- `导出备份` 导出备份会将会导出一份备份的数据，目前只有云端备份支持备份的导出。

- `导入备份` 在导出备份以后，您可以在别的云帮平台（可以访问网络）将导出的备份进行导入，导入后会生成相应的备份记录，您可继续后续操作。

#### 本地恢复后的注意事项

应用恢复后网关访问策略需要用户手工配置。

#### 导入备份

导入备份适用于云端备份所导出的备份文件，云端备份在Rainbond企业版本中支持。

### 应用迁移

由于我们做到全局的全量备份，借此我们可以做到应用的整体迁移，包括跨租户迁移和跨数据中心迁移；跨租户和跨数据中心迁移Rainbond开源版暂不支持，企业版提供支持；开源版本支持同数据中心下的跨团队迁移。

在已经备份的情况下，可以选择迁移操作来进行应用的迁移。

点击`迁移`按钮，选择要迁移到的团队及数据中心，点击`迁移`即可触发备份迁移操作

<img src="https://grstatic.oss-cn-shanghai.aliyuncs.com/images/docs/5.2/user-manual/app-manage/app-backup/transfer.png" width="100%">

应用完成迁移以后，会跳转到对应的数据中心和租户以方便您查看迁移后的应用。


