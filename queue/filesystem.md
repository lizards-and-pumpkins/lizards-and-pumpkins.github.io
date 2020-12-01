---
layout: wiki
title: Filesystem Queue
---
## Filesystem Queue

> The `LizardsAndPumpkins\Messaging\Queue\File` uses the PHP function `flock` for locking. That function is known to have issues when used on NFS mounts. Because of this, the file queue implementation will only work reliably if all processes reside on the same host and use the same local file system.
