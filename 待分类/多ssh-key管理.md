# 多ssh-key管理
1. `ssh-keygen -t rsa -f github`
2. 配置~/.ssh/config
    ```
    Host github.com
      HostName github.com
      User minusli
      IdentityFile /Users/lichengming/.ssh/github
    ```