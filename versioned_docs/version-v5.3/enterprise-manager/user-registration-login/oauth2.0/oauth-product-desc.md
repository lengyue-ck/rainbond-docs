---
title: Oauth2.0集成功能说明
description: 关于Rainbond平台企业集成Oauth2.0后能够获得的功能说明
hidden: false
weight: 6001
---

为了帮助用户更加方便的使用产品，基于 OAuth2.0 协议我们实现以下功能。

#### 第三方用户认证

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;基于 OAuth 协议，用户通过配置 OAuth 服务可以通过第三方平台实现一键登录(Github, Gitlab, Gitee), 增加用户体验

#### 代码仓库对接

- 通用场景：

在源码构建组件的场景中，用户需要将项目路径和帐号密码进行配置才能进行创建，也需要用户自行设置 webhook，使用起来繁琐；且账号密码容易泄露。

- 使用 Oauth 后：

在 5.1.9 版本中，我们基于 OAuth 协议，实现了源码构建仓库对接。用户只需要在自己的代码仓库注册好客户端，并在本平台配置好 OAuth 服务,即可快速选择自己仓库内的项目版本进行构建，自动设置 webhook，全过程不需要账号密码，手动操作，大大提高安全性，可用性，便捷性。