# 一、概述
1. 开源的监控告警解决方案。时间序列数据库。

# 二、各个组件
![Prometheus架构图](https://note.youdao.com/yws/res/24522/7FD57747F3CB445FA09F3B0D02F438A5)
- Prometheus Server：用于抓取和存储时间序列化数据
- Exporters：主动拉取数据的插件
- Pushgateway：被动拉取数据的插件
- Altermanager：告警发送模块
- Prometheus web UI：界面化，也包含结合Grafana进行数据展示或告警发送

## 1. Prometheus Server
1. Prometheus Server是Prometheus组件中的核心部分，负责实现对监控数据的获取，存储以及查询。
2. 通过配置文件设置监控目标(target),==去这些target上pull采集监控数据==。
3. Prometheus Server本身就是一个时序数据库，将采集到的监控数据按照时间序列的方式存储在本地磁盘当中。最后Prometheus Server对外提供了自定义的PromQL语言，实现对数据的查询以及分析。

## 2. Exporters
1. Exporter将监控数据采集的端点==通过HTTP服务的形式暴露给Prometheus Server==，Prometheus Server通过访问该Exporter提供的Endpoint端点，即可获取到需要采集的监控数据。
2. 如node exporter收集机器的监控信息，Mysql Exporter。自定义编写的dspace_target,将收集到的数据，由prometheus server pull拉取获取。

## 3. AlertManager
1. prometheus server 获取到监控数据后，将需要告警的那些信息push推送给alertmanager,告警规则及告警信息有alertmanager生成定制。推送告警邮件、微信或Webhook自定义告警处理方式。

## 4. PushGateway
1. 由于Prometheus数据采集基于Pull模型进行设计，因此在网络环境的配置上必须要让Prometheus Server能够直接与Exporter进行通信。 当这种网络需求无法直接满足时，就可以利用PushGateway来进行中转。可以通过PushGateway将内部网络的监控数据主动Push到Gateway当中。而Prometheus Server则可以采用同样Pull的方式从PushGateway中获取到监控数据。

## 5. 查询语法
1. 批量查询
2. query={__name__=~"node_network_receive_bytes_total|node_filesystem_files"}

## 6. 数据类型
1. counter（计数器）、gauge（仪表盘）、histogram（直方图）、summary（摘要）
2. counter: 那些数据只能增大不能减小的，上传、下载, ops等
3. guage: 数据可大可小的，容量、对象数、cup /内存
