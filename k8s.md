## Pod异常状态

1. pod处于CrashLoopBackOff (启动失败循环)

   Pod 中的某个容器启动后立即崩溃（退出），Kubernetes 根据 `restartPolicy` (通常是 `Always`) 尝试重启它，但容器又反复崩溃，进入了启动-失败-重启的循环。每次重启的间隔会逐渐延长（BackOff）。

   + **应用程序错误:** 容器内的应用程序启动失败、遇到未处理的异常、配置错误导致无法运行等。这是最常见的原因。
     + 使用 `kubectl logs <pod-name> -c <container-name> --previous` 查看**上一次**失败容器实例的日志。日志通常会包含错误信息或堆栈跟踪。
   + 容器启动时需要的内存或 CPU 超过了其 `limits`，导致被 OOMKilled (内存不足) 或 CPU 节流严重。
   + **依赖服务未就绪:** 应用程序依赖的其他服务
   + **命令或入口点错误:** Dockerfile 中的 `CMD` 或 `ENTRYPOINT` 配置错误，或者 Pod 定义中覆盖的 `command`/`args` 有误。
   + **权限问题:** 容器内的进程试图访问没有权限的文件或目录（例如，挂载的卷权限不正确）。

2. **`Error` (错误)**

    Pod 遇到了一个无法归类到其他状态的通用性错误，通常发生在容器启动之前。

   + **节点问题:** Pod 所在的节点出现故障。
   + **存储问题:** 挂载卷时发生严重错误。
   + 容器运行时 (Docker, containerd) 或 Kubelet 本身的错

