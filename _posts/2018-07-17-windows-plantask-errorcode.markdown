---
layout: post
title:  "windows 任务计划失败返回值"
date:   2018-07-17 15:50:05 +0800
categories: blog
tags:   [windows]
---

- 0 or 0x0: The operation completed successfully.
- 1 or 0x1: Incorrect function called or unknown function called.
- 2 or 0x2: File not found.
- 10 or 0xa: The environment is incorrect.
- 0x41300: Task is ready to run at its next scheduled time.
- 0x41301: Task is currently running.
- 0x41302: Task is disabled.
- 0x41303: Task has not yet run.
- 0x41304: There are no more runs scheduled for this task.
- 0x41306: Task is terminated.
- 0x8004130F: Credentials became corrupted (*)
- 0x8004131F: An instance of this task is already running.
- 0x80070002: Basically something like file not available (2147942402)
- 0x800704DD: The service is not available (is 'Run only when an user is logged on' checked?)
- 0xC000013A: The application terminated as a result of a CTRL+C.
- 0xC06D007E: Unknown software exception.

---

参考：                                   

- <https://blog.csdn.net/sirria1/article/details/52044576>