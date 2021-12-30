# Ansible

# 一、概述
1. **ansible是一个自动化运维工具，编写语言为python，使用时的配置文件为yaml**。
2. 实现了批量系统配置，批量程序部署，批量运行命令等。
3. **核心组件使用了paramiko和PyYaml**。其中paramiko是python的ssh支持库，而PyYaml则用于支持yaml配置文件的使用和playbook的应用。
3. **ansible基于模块工作**，本身没有批量部署的能力。真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架。
- 连接插件connection plugins：负责和被监控端实现通信；
- host inventory：指定操作的主机，是一个配置文件里面定义监控的主机；
- 各种模块核心模块、command模块、自定义模块；
- 借助于插件完成记录日志邮件等功能；
- **playbook：剧本**执行多个任务时，非必需可以让节点一次性运行多个任务。

# 二、配置
1. 安装 yum install ansible
2. 宿主机对受管机的免密登录
3. 配置完公钥之后记得把受管机的IP信息记录**到/etc/ansible/hosts**文件中

```bash
# /etc/ansible/hosts 配置文件
192.168.103.162  ##不分组直接声明IP
[yalezhang]  ##申明了受管机的列表和分类名
192.168.18.111  # 直接声明IP
120 ansible_ssh_host=192.168.18.120  ##定义别名和IP
160 ansible_ssh_host=192.168.103.160  ##定义别名和IP
```

## 2.1 ansible 命令语法
1. Usage: ansible <host-pattern> [options]
2. ansible 192.168.18.120 -m command -a "touch 'yalezhangyalezhang.py'"   --> 在120机器上创建一个yalezhangyalezhang.py文件

# 三、modules模块
1. 查看ansible的所有模块：**ansible-doc -l**

## 3.1 copy
1. **复制本机（或远程主机）文件/文件夹到远程主机**
2. 与fetch相反

## 3.2 fetch 拉取文件
1. 从远程主机拉取文件到本机
2. 只能拉取**文件**

## 3.3 synchronise 文件/文件夹同步模块
1. 拉取远端主机的**文件夹**到本机

## 3.4 file 文件模块
1. 来修改文件、目录和符号文件的属性权限等

# 四、ansible-playbook剧本
**yaml语言，yaml一种配置语言，可读性好，交互性好。**

## 4.1. playbook模块
1. **Playbooks** 是Ansible的配置,**部署,编排语言**.他们可以被描述为一个需要希望远程主机执行命令的方案,或者一组IT程序运行的**命令集合**
2. 包括**roles(角色)、tasks(任务)、variables(变量)、templates(模板)、handlers(处理器)、files(文件)**
3. tasks：一些任务，variables：一些变量，**handlers：一些服务任务，大多是设置start/restart/stop/自启动等的服务**， templates和files：一些模板及文件，roles：主要是一些角色任务编排

## 4.2 变量
1. **变量的优先级**(官方文档上写的)
* **extra vars (在命令行中使用 -e)优先级最高**
* 然后是在inventory中定义的连接变量(比如ansible_ssh_user)
* 接着是大多数的其它变量(命令行转换,play中的变量,included的变量,role中的变量等)
* 然后是在inventory定义的其它变量
* 然后是由系统发现的facts
* 然后是 "**role默认变量**",即每个roles里default/main.yml 这个是最默认的值,很容易丧失优先权
2. 优先级由高到低(简书上搜的)
- **命令行传递extra_vars -e xxx=xxx**
- roles tasks
- roles vars
- playbook
- group_vars
- hosts
- roles default

# 五、ansible-vault 加密数据
1. ansible-vault是 用于加密或保护剧本(playbook)或文件中的敏感信息，而不是任由信息采用纯文本格式，这类数据包括密码、API令牌和SSL证书等

## 1. 创建加密文件
1. **ansible-vault借助AES256算法来加密敏感信息**。这提供了嵌入到已定义密码中的对称加密。用户可以使用同一个密码来加密或解密文件，以便访问内容
2. $ ansible-vault create secret_file.yml 会提示输入密码，输入文件内容并保存。

## 2. 编辑、查看加密文件
1. $ ansible-vault edit file.yml 编辑
2. $ ansible-vault view file.yml 查看

## 3. 加密/解密现有文件
1. **ansible-vault encrypt file1.yml** 加密现有文件
2. ansible-vault decrypt file1.yml 解密现有文件

## 4. Playbook中运行时解密内容
1. Ansible 2.4之前：$ ansible-playbook playbook_example.yml --ask-vault-pass 输入密码即可
2. Ansible 2.4以后：ansible-playbook playbook_example.yml --vault-id @prompt 输入密码即可
3. **ansible2.4以后：将保管库密码存储在文件中：$ ansible-playbook playbook_example.yml --vault-id /etc/ansible/vault_pass.txt**

## 5. 只加密某些变量
1. 除了加密整个剧本外，ansible-vault还使你能够仅加密变量。
2. 要加密剧本示例中变量my_secret的值，命令将是：

```bash
# 将变量‘my_secret’加密，在剧本文件中替换为加密后的值
$ ansible-vault encrypt_string 'P@ssword123' --name 'my_secret'
# 运行剧本时，会显示设置的值‘P@ssword123’
```

# 六、ansible例子

## 1、register、with_items

```yaml
---
- hosts: all
  tasks:
    - name: ls osd name
      shell: ls | grep osd.1
      args:
        chdir: /var/log/ceph/
      register: ls_name_result
    - name: stat file info
      shell: stat {{ item }}
      args:
        chdir: /var/log/ceph/
      with_items:
        - "{{ ls_name_result.stdout_lines }}"
```

1. register：某个task的任务结果；with_items 遍历

## 2、 fetch 拉取文件

```yaml
---
- hosts: all
  tasks:
    - name: pull log file
      fetch:
        src: /var/log/ceph/{{ log_name }}
        dest: /var/log/ceph_log/
        flat: yes
        validate_checksum: no
      become: false
      ignore_errors: yes
```

***[Ansible官网](https://www.ansible.com/)***
