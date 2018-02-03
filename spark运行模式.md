# Local模式
本地模式，可以启动本地一个线程来运行 job ，可以启动N个线程或者使用系统所有核运行 job ；常用于本地开发测试;

# Standalone模式
简单模式或称独立模式，典型的 Mater/Slave 模式，可以单独部署到一个集群中，无依赖任何其他资源管理系统。不使用其他调度工具时会存在单点故障，使用 Zookeeper 等可以解决；

# Spark YARN模式
运行在 yarn 资源管理器框架之上，由 yarn 负责资源管理，Spark 负责任务调度和计算；

# Spark Mesos模式
通用集群管理，有两种调度模式：粗粒度模式（Coarse-grained Mode）与细粒度模式（Fine-grained Mode）；运行在 mesos 资源管理器框架之上，由 mesos 负责资源管理，Spark 负责任务调度和计算
