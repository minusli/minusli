# 阿里云实例Debian(Jessie)安装Docker.md
- [官网安装 Docker-CE](https://docs.docker.com/install/linux/docker-ce/debian/)
    - 安装过程中, 发现apt-get update非常慢, 原因是阿里云的镜像源存在问题, 而且apt-get install的时候, 也提示找不到软件包. 所以需要先解决apt-get源的问题
        - [Debian源收集](https://www.iabmu.com/archives/debian-mirror-list.html), 我选择的是ustc, 但在apt-get update的时候会报404(原因是jessie-proposed-updates这个找不到, 直接删除即可)
        - 修改源/etc/apt/source.list.d/(备份旧源.bak, 新建新源ustc.list)
        - apt-get update; apt-get upgrade;
    - 尝试官网安装, docker-ce-cli还是找不到. 决定不装docker-ce, 装docker-engine.
- [民间安装 Docker-engine](https://blog.programster.org/debian-8-install-docker)
    - 安装之后, 运行`docker info`查看是否运行了docker服务:
        ```
        Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
        ``` 
    - 运行`systemctl start docker`启动docker:
        ```
        Job for docker.service failed. See "systemctl status docker.service" and "journalctl -xe" for details.
        root@n108-017-048:/etc/apt/sources.list.d# journalctl -xe
        -- Subject: Unit docker.service has failed
        -- Defined-By: systemd
        -- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
        -- 
        -- Unit docker.service has failed.
        -- 
        -- The result is failed.
        ```
    - 运行`journalctl -xe`查看日志:
        ```
        Jul 14 11:18:16 n108-017-048 systemd[1]: Cannot add dependency job for unit resolvconf.service, ignoring: Unit resolvconf.service is masked.
        Jul 14 11:18:16 n108-017-048 systemd[1]: start request repeated too quickly for docker.service
        Jul 14 11:18:16 n108-017-048 systemd[1]: Failed to start Docker Application Container Engine.
        -- Subject: Unit docker.service has failed
        -- Defined-By: systemd
        -- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
        -- 
        -- Unit docker.service has failed.
        -- 
        -- The result is failed.
        Jul 14 11:18:16 n108-017-048 systemd[1]: Unit docker.socket entered failed state.
        Jul 14 11:18:16 n108-017-048 systemd[1]: docker.service failed.
        ```
    - 为了看docker的原始报错信息, 使用`dockerd -H 0.0.0.0:6666`启动, 也报错
        ```
        WARN[0000] [!] DON'T BIND ON ANY IP ADDRESS WITHOUT setting --tlsverify IF YOU DON'T KNOW WHAT YOU'RE DOING [!] 
        INFO[0000] libcontainerd: new containerd process, pid: 220275 
        WARN[0001] failed to rename /var/lib/docker/tmp for background deletion: %!s(<nil>). Deleting synchronously 
        INFO[0001] [graphdriver] using prior storage driver: overlay2 
        INFO[0001] Graph migration to content-addressability took 0.00 seconds 
        WARN[0001] Your kernel does not support kernel memory limit 
        WARN[0001] Your kernel does not support cgroup rt period 
        WARN[0001] Your kernel does not support cgroup rt runtime 
        INFO[0001] Loading containers: start.                   
        Error starting daemon: Error initializing network controller: error obtaining controller instance: failed to create NAT chain: iptables failed: iptables --wait -t nat -N DOCKER: iptables v1.4.21: can't initialize iptables table `nat': Table does not exist (do you need to insmod?)
        Perhaps iptables or your kernel needs to be upgraded.
         (exit status 3)
        ```
    - 发现是运行`iptables --wait -t nat -N DOCKER`报错, 无法添加一条iptables规则:
        ```
        iptables v1.4.21: can't initialize iptables table `nat': Table does not exist (do you need to insmod?)
        Perhaps iptables or your kernel needs to be upgraded.
        ```
    - 网上查, 说是没有加载iptable_nat模块, 运行 `modprobe iptable_nat`加载模块:
        ```
        modprobe: ERROR: ../libkmod/libkmod-module.c:816 kmod_module_insert_module() could not find module by name='none'
        modprobe: ERROR: could not insert 'none': Function not implemented
        ```
    - 加载模块报错, 以为是内核中没有iptable_nat模块, 于是去内核中找有没有对应的.ko文件
        - `uname -r`查看内核版本
        - `/lib/modules/4.4.0-33.bm.1-amd64`文件夹下执行`find -name "*_nat*"`, 其中`4.4.0-33.bm.1-amd64`是机器当前内核版本.
        ```
        ./kernel/net/ipv4/netfilter/iptable_nat.ko   
        ```
    - 发现本机上,是有对应模块的,可是加载模块却报错.
        1. 可能是内核中该模块有问题(可能性比较低)
        2. 可能是一些配置造成的原因(modprobe有配置文件)
    - 于是又去查modprobe的配置文件, `/etc/modprobe.d/`目录里有相关配置文件:
        ```
        # ls
        > fbdev-blacklist.conf  nf-blacklist.conf  nf_conntrack.conf
        ```
    - 有个blacklist.conf, 这名字一看就是黑名单, 赶快看看
        ```
        # cat nf-blacklist.conf.bak 
        > alias iptable_nat none
          blacklist iptable_nat
          blacklist nf_nat
          blacklist nf_conntrack_ipv4
          blacklist nf_defrag_ipv4
          blacklist nf_conntrack
        ```
    - 我艹,终于找到原因了,黑名单中设置了iptable_nat别名为none, 是说为什么无法加载该模块,并且报错是找不到'none'. 直接bak这个blacklist就解决了!!!

- [民间安装 Docker-CE](https://www.itzgeek.com/how-tos/linux/debian/how-to-install-docker-on-debian-9.html)



#### docker-compose
- [官网安装](https://docs.docker.com/compose/install/)