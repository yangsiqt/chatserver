# chatserver
工作在nginx tcp负载均衡环境中的集群聊天服务器和客户端源码，基于muduo、mysql、redis实现

### 功能

- 基于muduo库的chatserver支持8G内存4核CPU服务器的并发连接访问
- 客户端新用户注册、登录
- 添加好友和添加群组
- 好友聊天、群组聊天
- 离线消息
- nginx配置tcp负载均衡
- 集群聊天系统支持客户端跨服务器通信

### 环境

- Ubuntu 2204
- nginx-1.14.0
- boost_1_69_0
- json 3.6.1
- muduo
- cmake 3.22.1
- mysql 8.0.41
- redis 6.0.16

### 配置方式

- 下载安装mysql，登录，创建chat数据库，执行，SOURCE [文件路径/文件名.sql];导入数据库文件

- 下载安装redis

- 下载安装Nginx，在Nginx配置文件nginx.conf中添加，完成配置

  ```
  stream {
      upstream MyServer {
          server 127.0.0.1:6000 weight=1 max_fails=3 fail_timeout=30s;
          server 127.0.0.1:6002 weight=1 max_fails=3 fail_timeout=30s;
      }
  
  server {
  	proxy_connect_timeout 1s;
  	proxy_timeout 3s;
  	listen 8000;
  	proxy_pass MyServer;
  	tcp_nodelay on;
  }
  
  }
  ```

- 运行sudo netstat -tanp，确保mysql、redis、Nginx服务在线

### 编译方式

mkdir build
cd build
rm -rf *
cmake ..
make

在bin目录下生成ChatServer和ChatClient可执行文件

### 运行

- 在bin目录下执行./ChatServer 127.0.0.1 6000开启第一台服务器
- 在bin目录下执行./ChatServer 127.0.0.1 6002开启第二台服务器
- 在bin目录下执行./ChatClient 127.0.0.1 8000，访问127.0.0.1 8000负载均衡器，开启客户端

