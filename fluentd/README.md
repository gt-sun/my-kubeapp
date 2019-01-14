
- 来自于https://github.com/kubernetes/kubernetes/tree/master/cluster/addons/fluentd-elasticsearch/fluentd-es-image
- 构建了`bvac/fluentd-elasticsearch:v2.4.0`镜像
- 增加了`TZ`环境变量
- 使用了k8s 集群外部的es
- 精简了目标日志源
- 在节点上添加label `beta.kubernetes.io/fluentd-ds-ready=true`
