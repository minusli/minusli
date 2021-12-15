# ES脑裂问题
- discovery.zen.minimum_master_nodes
    - 默认3s，网络环境不好，可考虑适当调大
- discovery.zen.ping_timeout
    - 默认1，官方推荐只(N/2)+1。如果使用官方推荐值，如果集群N=2，一个master挂了，另一个肯定也就无法成为master了，这一点需要注意。