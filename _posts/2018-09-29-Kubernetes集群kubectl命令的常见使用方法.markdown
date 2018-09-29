---
layout:     post
title:      "Kubernetes集群kubectl命令的常见使用方法"
subtitle:   " Kubernetes集群kubectl命令的常见使用方法"
date:       2018-09-29 12:00:00
author:     "kanglang"
header-img: "img/k8s.jpg"
catalog: true

tags:
    - k8s
    
---




### 简介：
kubectl是一个命令行界面，用于运行针对Kubernetes群集的命令。
### 语法：
**kubectl [command] [TYPE] [NAME] [flags]**
>* command：指定您希望对一个或多个资源执行的操作，例如创建，获取，描述，删除。
>* TYPE：指定资源类型。 资源类型区分大小写，可以使用单数，复数或缩写形式。
>* NAME：指定资源的名称。 名称区分大小写。 如果省略名称，则会显示所有资源的详细信息，例如$ kubectl get pod。
>* flags：指定可选标志。 例如，可以使用-s或--server标志来指定Kubernetes API服务器的地址和端口。

---------------------

本文来自 [运维个西瓜](https://blog.csdn.net/watermelonbig/article/details/79412012) 的CSDN 博客  

# 1、显示Pod的更多信息

`` kubectl get pod <pod-name> -o wide ``

以yaml格式显示Pod的详细信息

` kubectl get pod <pod-name> -o yaml `


# 2、创建资源对象
根据yaml配置文件一次性创建service和rc

`kubectl create -f my-service.yaml -f my-rc.yaml`

根据<directory>目录下所有.yaml、.yml、.json文件的定义进行创建操作

`kubectl create -f <directory>`

# 3、查看资源对象
查看所有Pod列表

`kubectl get pods`

# 4、查看rc和service列表

`kubectl get rc,service`

# 5、描述资源对象显示Node的详细信息
`kubectl describe nodes <node-name>` 

显示Pod的详细信息
`kubectl describe pods/<pod-name>`

显示由RC管理的Pod的信息
`kubectl describe pods <rc-name>`
# 6、删除资源对象

基于Pod.yaml定义的名称删除Pod

`kubectl delete -f pod.yaml`

删除所有包含某个label的Pod和service

`kubectl delete pods,services -l name=<label-name>`

删除所有Pod

`kubectl delete pods --all`


# 7、执行容器的命令

执行Pod的data命令，默认是用Pod中的第一个容器执行

`kubectl exec <pod-name> data` 

指定Pod中某个容器执行data命令

`kubectl exec <pod-name> -c <container-name> data`

通过bash获得Pod中某个容器的TTY，相当于登录容器

`kubectl exec -it <pod-name> -c <container-name> /bin/bash`

# 8、使用一些复杂过滤条件查看特定的资源对象

根据重启次数排序列出 pod

` $ kubectl get pods --sort-by='.status.containerStatuses[0].restartCount`

获取所有具有 app=cassandra 的 pod 中的 version 标签

`$ kubectl get pods --selector=app=cassandra rc -o  jsonpath='{.items[*].metadata.labels.version}` 

 获取所有节点的 ExternalIP

`$ kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="ExternalIP")].address}`

查看哪些节点已就绪

`$ JSONPATH='{range .items[*]}{@.metadata.name}:{range @.status.conditions[*]}{@.type}={@.status};{end}{end}' && kubectl get nodes -o jsonpath="$JSONPATH" | grep "Ready=True"`



# 9、Pod的扩容缩容、滚动升级以及更新资源配置


执行扩容缩容Pod的操作

`kubectl scale rc redis --replicas=3`

我们需要确认的是在rc配置文件中定义的replicas数量，当我们执行上述命令的结果大于replicas的数量时，则我们执行的命令相当于扩容操作，反之相反，可以理解为我们填写的数量是我们需要的Pod数量。需要注意的是，当我们需要进行永久性扩容时，不要忘记修改rc配置文件中的replicas数量。

Pod的滚动升级

执行滚动升级操作

`kubectl rolling-update redis -f redis-rc.update.yaml`

需要注意的是当我们执行rolling-update命令前需要准备好新的RC配置文件以及ConfigMap配置文件，RC配置文件中需要指定升级后需要使用的镜像名称，或者可以使用kubeclt rolling-update redis --image=redis-2.0直接指定镜像名称的方式直接升级。


强制替换，删除后重新创建资源。会导致服务中断。

`$ kubectl replace --force -f ./pod.json`

为 nginx RC 创建服务，启用本地 80 端口连接到容器上的 8000 端口

`$ kubectl expose rc nginx --port=80 --target-port=8000`

更新单容器 pod 的镜像版本（tag）到 v4

`$ kubectl get pod mypod -o yaml | sed 's/[Math Processing Error]image:myimageimage: myimage:.*$/\1:v4/' | kubectl replace -f -`



`$ kubectl label pods my-pod new-label=awesome  # 添加标签`

`$ kubectl annotate pods my-pod icon-url=http://goo.gl/XXBTWq # 添加注解`

` $ kubectl autoscale deployment foo --min=2 --max=10 # 自动扩展 deployment “foo”`


# 10、查看容器的日志

查看容器输出到stdout的日志：

`kubectl logs <pod-name>`

跟踪查看容器的日志（tail -f）：

`kubectl logs -f <pod-name> -n <namespace> -c <container-name>`

# 11、Kubectl context和配置

修改kubectl命令行工具默认使用的apiServer地址、集群CA和客户端证书：

 ```
[root@bogon ssl]# kubectl config set-cluster default-cluster --server=https://10.0.2.5:6443 --certificate-	authority=/etc/kubernetes/ssl/ca.crtCluster "default-cluster" set.
```
``` [root@bogon ssl]# kubectl config set-credentials default-admin --certificate-authority=/etc/kubernetes/ssl/ca.crt --client-key=/etc/kubernetes/ssl/cs_client.key --client-certificate=/etc/kubernetes/ssl/cs_client.crtUser "default-admin" set.
```
```[root@bogon ssl]# kubectl config set-context default-system --cluster=default-cluster --user=default-admin  在kubeconfig配置中添加一条名为default-system的上下文记录Context "default-system" created.
```
```
[root@bogon ssl]# kubectl config use-context default-system     #设置默认上下文为default-systemSwitched to context "default-system".
```
```
[root@bogon ~]# kubectl config current-context   #查看当前的上下文default-system
```




查看kubectl context配置信息：



```

# kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /etc/kubernetes/ssl/ca.crt
        server: https://10.0.2.5:6443
  name: default-cluster
contexts:
- context:
    cluster: default-cluster
    user: default-admin
  name: default-system
current-context: default-system
kind: Config
preferences: {}
users:
 - name: default-admin 
 user:
    client-certificate: /etc/kubernetes/ssl/cs_client.crt
     client-key: /etc/kubernetes/ssl/cs_client.key
```

显示当前的上下文：


```
[root@bogon ~]# kubectl config current-contextdefault-system
```
