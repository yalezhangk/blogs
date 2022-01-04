# 一、概述
1. Nginx同Apache一样都是一种web服务器。基于REST架构风格，以URL或URI作为沟通依据，通过http协议提供各种网络服务。
2. **特点：轻量级高并发**
3. 使用C语言开发，特点:
- Nginx 使用基于**事件驱动架构**，使得其可以支持数以百万级别的 TCP 连接。
- 跨平台且稳定

# 二、使用
1. **nginx常用于反向代理、负载均衡、HTTP缓存**。
2. **将服务器接收到的请求按照规则分发的过程，称为负载均衡。**，分为：**硬件负载均衡和软件负载**均衡两种
3. 硬负载成本高，软负载成本低，**软件负载均衡是利用现有的技术结合主机硬件实现的一种消息队列分发机制。**

## 1. Nginx支持的负载均衡调度算法

### 1. 轮询（默认）round_robin
1. **nginx默认的调度策略，轮询**。接收到的请求按照顺序逐一分配到不同的后端服务器，即使在使用过程中，某一台后端服务器宕机，Nginx 会自动将该服务器剔除出队列，请求受理情况不会受到任何影响。

### 2. 权重 weight
1. 给**后端服务器设置一个权重值（weight）**。权重数据越大，被分配到请求的几率越大；该权重值，主要是针对实际工作环境中不同的后端服务器硬件配置进行调整的。

### 3. 最少连接 least_conn
1. 下一个请求将被分派到活动连接数量最少的服务器

### 4. ip_hash
1. 每个请求按照发起客户端的 ip 的 hash 结果进行匹配，这样的算法下一个**固定 ip 地址的客户端总会访问到同一个后端服务器**，这也在一定程度上解决了**集群部署环境下 Session 共享**的问题。
> 前4种默认都支持，后边的几种需要安装第三方插件

### 5. fair(第三方)
1. fair: 智能调整调度算法，动态的根据后端服务器的请求处理到响应的时间进行均衡分配。
3. 但是需要注意的是 Nginx 默认不支持 fair 算法，如果要使用这种调度算法，请**安装 upstream_fair 模块。**

### 6. url_hash(第三方)
1. 按照访问的 **URL 的 hash 结果分配请求**，每个请求的 URL 会指向后端固定的某个服务器，可以在 **Nginx 作为静态服务器的情况下提高缓存效率。**
2. 同样要注意 Nginx 默认不支持这种调度算法，要使用的话需要安装 Nginx 的 hash 软件包

```bash
upstream tomcatserver1 {  
    server 192.168.72.49:8080 weight=3;  
    server 192.168.72.49:8081;  
    }   
  
 server {  
        listen       80;  
        server_name  8080.max.com;  
        #charset koi8-r;  
        #access_log  logs/host.access.log  main;  
        location / {  
            proxy_pass   http://tomcatserver1;  
            index  index.html index.htm;  
        }  
     }
```

# 三、Nginx如何处理HTTP请求的？
1. 它结合**多进程机制(单线程)和异步非阻塞方式**

## 1. 多进程机制(单线程)
1. 服务器每当收到一个客户端时，就有 服务器主进程 （ master process ）生成一个 子进程（ worker process ）出来和客户端建立连接进行交互，直到连接断开，该子进程就结束了。

## 2. 异步非阻塞机制
1. 每个工作进程 使用 异步非阻塞方式 ，可以处理 多个客户端请求 。 运用了**epoll模型**，**提供了一个队列，排队解决**。
2. 当某个 工作进程 接收到客户端的请求以后，调用 IO 进行处理，如果不能立即得到结果，就去 处理其他请求 （即为 非阻塞 ）；而 客户端 在此期间也 无需等待响应 ，可以去处理其他事情（即为 异步 ）。当 IO 返回时，就会通知此 工作进程 ；该进程得到通知，暂时 挂起 当前处理的事务去 响应客户端请求 。

# 四、Nginx的master和worker是如何工作的？
1. **这跟Nginx的多进程、单线程有关。（一个进程只有一个主线程）。**

## 1. 为什么要用单线程？
1. 采用单线程来异步非阻塞处理请求（管理员可以配置Nginx主进程的工作进程的数量），不会为每个请求分配cpu和内存资源，节省了大量资源，同时也减少了大量的CPU的上下文切换，所以才使得Nginx支持更高的并发。

## 2. 简单过程
1. 主程序Master process 启动后，通过一个for 循环来接收和处理外部信号；主进程通过 fork() 函数产生 worker 子进程 ，每个子进程执行一个 for循环来实现Nginx服务器对事件的接收和处理 。

## 3. 详细过程
1. Nginx 在启动后，会有一个 master 进程和多个相互独立的 worker 进程。 2、master 接收来自外界的信号，先建立好需要 listen 的 socket（listenfd） 之后，然后再 fork 出多个 worker 进程，然后向各worker进程发送信号，每个进程都有可能来处理这个连接。 3、所有 worker 进程的 listenfd 会在新连接到来时变得可读 ，为保证只有一个进程处理该连接，所有 worker 进程在注册 listenfd 读事件前抢占 accept_mutex ，抢到互斥锁的那个进程注册 listenfd 读事件 ，在读事件里调用 accept 接受该连接。 4、当一个 worker 进程在 accept 这个连接之后，就开始读取请求、解析请求、处理请求，产生数据后，再返回给客户端 ，最后才断开连接。

# 五、Nginx处理压缩？
1. 开启nginx **gzip压缩**后，图片、css、js等静态资源的大小会减小，可节省带宽，提高传输效率，但是会消耗CPU资源。

```bash
# 开启gzip
gzip on;
# 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
gzip_min_length 1k;
# gzip 压缩级别，1-9，数字越大压缩的越好，也越占用CPU时间，后面会有详细说明
gzip_comp_level 1;
# 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png application/vnd.ms-fontobject font/ttf font/opentype font/x-woff image/svg+xml;
```

# 六、Nginx和Apache、Tomcat之间的不同点？
1. nginx和apache只是web服务器，可以简单理解为只能提供html静态文件服务；tomcat可以对jsp进行解析
2. Nginx和Apache区别：
- Nginx轻量级，同样起web 服务，比apache占用更少的内存及资源 。
- Nginx 抗并发，nginx 处理请求是异步非阻塞的，而apache 则是阻塞型的，在高并发下nginx 能保持低资源低消耗高性能 。
- Nginx提供负载均衡，可以做做反向代理，前端服务器
- **Nginx多进程单线程，异步非阻塞；Apache多进程同步，阻塞**。

# 七、Nginx动静态资源分离做过吗，为什么要这样做？
1. 动态资源、静态资源分离，是让动态网站里的动态网页根据一定规则把不变的资源和经常变的资源区分开来。
2. 比如说 js、css、html从A服务器返回。图片 从B服务器返回，其他请求从Tomcat服务器C返回。
3. 后台应用分开部署，提高用户访问静态代码的速度。而且现在还有CDN服务，不需要限制于服务器的带宽。

# 八、Nginx解决跨域
1. Nginx解决跨域问题通过**Nginx反向代理将对真实服务器的请求转移到本机服务器**来避免浏览器的"同源策略限制"。
