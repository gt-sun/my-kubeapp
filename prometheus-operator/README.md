

参考：

1. https://github.com/coreos/prometheus-operator
2. https://www.qikqiak.com/post/prometheus-operator-advance/



## 部署

```bash
$ k apply -f manifests/
```



添加自定义node 节点：

```bash
$ vim my-external-node-exporter.yaml
$ kubectl create secret generic additional-configs --from-file=my-external-node-exporter.yaml -n monitoring
$ vim prometheus-prometheus.yaml
// add
additionalScrapeConfigs:
    name: additional-configs
    key: my-external-node-exporter.yaml

# use NodePort svc type
# reload prometheus
$ curl -X POST http://10.0.106.160:9090/-/reload
```

## grafana

1. 使用configmap 自定义`grafana.ini`

```bash
$ kubectl create configmap my-grafana-config --from-file=grafana.ini -n monitoring
```

在`grafana-deployment.yaml`里面添加：

```
volumes:
- configMap:
    name: my-grafana-config
  name: my-grafana-config

volumeMounts:
- mountPath: /etc/grafana
  name: my-grafana-config
```

2. 自定义dashboard

按照不同的staging进行分组，效果如下：

<img src="../assets/grafana-dashboard1.png" width = 50% height = 50% />

导出dashboard的json，[json文件](grafana/HCP-dashboard-1546517356842.json)
