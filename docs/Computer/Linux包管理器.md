# Linux包管理器

# 一、YUM包管理器

## 一、简介
1. yum一个软件包管理器，**基于RPM包管理器**。
2. 能够从指定服务器自动下载**RPM包**并安装，并且**自动安装所需的依赖包**。
3. yum提供了查找、安装、删除某一个、一组甚至全部软件包的命令

## 二、用法
1. 安装：yum install <package_name>
2. 删除：yum remove <package_name>
3. 查找：**yum search <keyword>**
4. 仅更新指定的软件：yum update <package_name>
5. 列出所有可安裝的软件清单：yum list
6. 清除缓存: yum clean all

```shell
运行以下命令生成缓存:
yum clean all
yum makecache
```

## 三、更改yum源
1. yum配置文件**在/etc/yum.repos.d/文件夹**里
2. **备份旧的**yum文件：mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
3. **下载阿里源**：
- Base源：wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
- epel源：wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
4. epel源是Base源的额外补充源EPEL (Extra Packages for Enterprise Linux)
5. 执行**yum clean all和yum makecache**

# 二、PIP包管理器

## 一、使用

1. 升级pip: python -m pip install --upgrade pip
2. 安装包: pip install 安装包名
3. 查看包是否已安装：pip show --files 安装包名
4. 检查哪些包需要更新:pip list --outdated
5. 升级包：pip install --upgrade 要升级的包名
6. 卸载包：pip uninstall 要卸载的包名

## 二、pip换源
1. linux:
2. 修改 ~/.pip/pip.conf (没有就创建一个)， 内容如下：（清华源）

```shell
# /etc/pip.conf
[global] index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```
2. **pip install -i 临时源路径 包名** 临时换源
3. 阿里pip源: 
https://mirrors.aliyun.com/pypi/simple
1. 将pypi包放入pip源里后，
1. 如放入192.168.103.140:/opt/repo/t2stor/pip/ 后，在此目录下**执行dir2pi .**
1. 就会在simple目录, 里面包含所有的包索引，这样就能搜索到此pypi包了

## 二、搭建pypiserver
1. 搭建一个支持并发的pypiserver

## 1.1 安装包
1. yum install nginx -y
2. pip install passlib pypiserver gunicorn
## 1.2 创建pypiserver服务
1. 执行以下命令，将pypi服务托管给systemd
2. /usr/local/bin/gunicorn 为gunicorn服务器的解释器路径
3. -w16为启动16个进程
4. -b :10012 端口号

```shell
tee /usr/lib/systemd/system/pypiserver.service <<-'EOF'
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
PIDFile=/run/pypiserver.pid
ExecStart=/usr/local/bin/gunicorn -w16 \
    --pid /run/pypiserver.pid \
    -b :10012 \
    'pypiserver:app( \
    root="/opt/pip/packages", \
    password_file="/opt/pip/.htaccess")'
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target 
EOF
```

### 创建文件及目录
1. mkdir -p /opt/pip/packages
2. touch /opt/pip/.htaccess
## 1.3 启动pypiserver服务
1. systemctl enable pypiserver.service
2. systemctl start pypiserver.service
## 1.4 检查pypiserver服务
1. 向仓库中添加python package
2. cd /opt/pip/packages
3. pip download ipython
4. 搜索刚才下载的package
5. pip search -i **http://127.0.0.1:10012** ipython
6. 会输出ipython的信息
## 1.5 配置nginx做反向代理
1. nginx 负责提供文件下载服务，nginx监听10087端口

```shell
tee /etc/nginx/conf.d/pypi.conf <<-'EOF'
upstream pypiserver {
  server 127.0.0.1:10012;
}

server {
  listen 10087;

  # disable any limits to avoid HTTP 413 for large package uploads
  client_max_body_size 0;

  location / {
    proxy_pass http://pypiserver/;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    # When setting up pypiserver behind other proxy, such as an Nginx instance, remove the below line if the proxy already has similar settings.
    proxy_set_header X-Forwarded-Proto $scheme;

    proxy_buffering off;
    proxy_request_buffering off;
  }

  location /packages/ {
    alias /opt/pip/packages/; # static file
  }
}
EOF
```

## 1.6 启动nginx并检查服务是否可用
1. systemctl enable nginx
2. systemctl start nginx
3. pip search -i **http://192.168.159.201:10087** ipython
会输出ipython信息

# 三、RPM包管理器

## 1、简介
1. Red-Hat Package Manager（RPM软件包管理器）的缩写
2. **rpm是管理离线包的工具**

## 2、命令
1. 安装：**rpm -ivh ceph-mgr-dspace.rpm**
2. 搜索是否已安装：**rpm -qa | grep httpd**　　　　　 ＃[搜索指定rpm包是否安装]--all搜索*httpd*
3. 更新：rpm –Uvh  foo-2.0-1.i386.rpm
3. 列出安装目录：**rpm -ql httpd.rpm**　　**＃[搜索rpm包]--list所有文件安装目录**
4. **卸载：rpm -e ceph-mgr-dspace-2.4.0-1.gd2d7b4e.x86_64**
5. 只下载，不安装rpm包：**yum install --downloadonly --downloaddir=/root/yalezhang/ pack_name**
6. **rpm -qR** ceph-mgr-dspace-2.4.0-19.g8d3af1a.noarch.rpm  **查看包的依赖**

## 3、rpm打包
1. 使用rpmbuild打包
1. 安装：yum install rpm-build -y
1. 命令：rpmbuild -bb XXX.spec

## 4、更新rpm源
1. 将包放入rpm源中后，如：将120上build完的ceph-mgr-dspace-2.4.0-19.g8d3af1a.noarch.rpm包放入167上的/opt/athena/repo/rpm/x86_64/dspace 源里
2. 当前目录下执行**createrepo .**，会更新rpm包索引，这样才能搜索到此rpm包
