

参考：

1. https://github.com/coreos/prometheus-operator
2. https://www.qikqiak.com/post/prometheus-operator-advance/



部署：

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