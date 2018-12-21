`hpa`根据pod 资源使用情况自动扩容副本数量，需要依赖`metrics-server`，实验过程如下：


```bash
# create docker image
$ docker build -t php:v1 .

# run service
$ kubectl run php-apache --image=php:v1 --requests=cpu=200m --expose --port=80

# create hpa
$ kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=5

# use a pod
$ kubectl run --generator=run-pod/v1 -i --tty load-generator --image=busybox /bin/sh
while true; do wget -q -O- http://php-apache.default.svc.cluster.local; done



# watch the process...
carl-k8s-master root@~> k get hpa -w
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         5         1          35m
php-apache   Deployment/php-apache   169%/50%   1     5     1     35m
php-apache   Deployment/php-apache   169%/50%   1     5     4     35m
^C⏎                                                                                                                                                                               carl-k8s-master root@~> k get po
NAME                         READY   STATUS    RESTARTS   AGE
load-generator               1/1     Running   0          67s
nginx-875fb79d7-p99gd        1/1     Running   0          7d5h
nginx-875fb79d7-vcr7s        1/1     Running   0          5d22h
php-apache-dbcdd969d-j8q59   1/1     Running   0          21s
php-apache-dbcdd969d-sdrpp   1/1     Running   0          21s
php-apache-dbcdd969d-txr7n   1/1     Running   0          21s
php-apache-dbcdd969d-wrhkc   1/1     Running   0          41m
carl-k8s-master root@~> k get hpa -w
NAME         REFERENCE               TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   169%/50%   1         5         4          35m
php-apache   Deployment/php-apache   0%/50%   1     5     4     36m
php-apache   Deployment/php-apache   0%/50%   1     5     4     41m
php-apache   Deployment/php-apache   0%/50%   1     5     1     41m  

```



### 遇到的问题

metrice-server 无法获取资源信息，查看日志发现报错证书问题，添加以下参数：

```
- name: metrics-server
  image: k8s.gcr.io/metrics-server-amd64:v0.3.1
  command:
  - /metrics-server
  - --kubelet-insecure-tls
```


### 参考

- https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/
