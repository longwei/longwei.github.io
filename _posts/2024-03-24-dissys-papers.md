---
layout: post
title: note on architect design db
---

池化技术
create db connection take time.
定期检测连pool connection is still good.
它们的创建过程都比较耗时，也比较消耗系统资源 。所以，我们把它们放在一个池子里统一管理起来，以达到 提升性能和资源复用的目的


如何做主从分离














