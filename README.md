

一些部署模板，仅供参考。



## weave scope

```bash
$ kubectl apply -f "https://cloud.weave.works/k8s/scope.yaml?k8s-version=$(kubectl version | base64 | tr -d '\n')"

$ k -n weave edit svc weave-scope-app   #替换为NodePort 
```
