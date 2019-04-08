#### Install docker and start redis-docker
1. `brew cask install docker` 
2. Click logo for complete installing docker
3. change the mirrors and restart
    - [change mirror for mac](https://www.jianshu.com/p/419eaf4425a6)
    - set mirror: `https://docker.mirrors.ustc.edu.cn`
    
    
#### Redis Docker

1. `docker search redis`
2. `docker pull reids`
3. config the docker-compose.yml
    ```yml
    version: "3"
    services:
      redis:
        image: redis
        container_name: redis
        ports:
          - "6379:6379"
    ```
4. `docker-compose up -d`
5. `docker-compose down`
6. Install [Redis-Desktop](http://docs.redisdesktop.com/en/latest/install/)
    - `brew cask install Caskroom/cask/rdm`
    - https://pan.baidu.com/s/10vpdhw7YfDD7G4yZCGtqQg

#### MySQL Docker
1. `docker pull mysql:5.6`
2. config the docker-compose.yml
    ```yaml
    mysql:
        image: mysql:5.6
        container_name: mysql
        ports:
          - "3306:3306"
        environment:
          MYSQL_ROOT_PASSWORD: "minusli123456"
        volumes:
          - ./mysql-data:/var/lib/mysql
    ```
3. tips: 
    - 连接报错：`ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)`
        - 很有可能是mysql映射路径权限不对，全部加上可读可写，重启mysql即可
        - 还有就是可以试试mysql连接时指定mysql host(默认client连接本机走socket file，此时mysql是docker启动的，没有socket file)
#### ElasticSearch Docker
1. `docker pull elasticsearch:5.6`
2. config the docker-compose.yml
    ```yaml
    elasticsearch:
        image: elasticsearch:5.6
        container_name: elasticsearch
        ports:
        - "9200:9200"
        environment:
        - cluster.name=docker-cluster
        - bootstrap.memory_lock=true
        - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
        volumes:
        - ./share/elasticsearch/data:/usr/share/elasticsearch/data
        - ./share/elasticsearch/plugins:/usr/share/elasticsearch/plugins
    ```