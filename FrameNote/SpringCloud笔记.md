# SpringCloud配置文件加载顺序

**优先级由高到低：**

* 操作系统配置项（System properties）——JVM的配置。
* 环境变量（env vars）
* config server中的远程配置文件
* 本地application.yml
* 本地bootstrap.yml

加载顺序：bootstrap.yml——config server——application.yml——环境变量——操作系统配置，即从优先级低的开始加载，虽然config server中的远程配置先加载，但是在**默认配置**下，不会被本地的配置文件覆盖。

通过`spring.cloud.config.allowOverride`、`spring.cloud.config.overrideNone`、`spring.cloud.config.overrideSystemProperties`来配置本地配置文件能否覆盖远程配置文件，**默认配置下**远程配置文件的优先级最高，不会被本地配置文件覆盖。

![image-20210427172111351](SpringCloud笔记.assets/image-20210427172111351.png)