# Kubectl 命令速查表（详细注释版）

## 基础命令

### 集群信息
```bash
# 查看集群基本信息
# 输出包含Kubernetes控制平面和CoreDNS的访问地址
kubectl cluster-info

# 查看集群组件状态
# 显示etcd、scheduler、controller-manager等组件的健康状态
kubectl get componentstatuses
# 简写：kubectl get cs

# 查看API资源类型
# 列出集群支持的所有资源类型（pods, services, deployments等）
kubectl api-resources

# 查看API版本
# 显示集群支持的API版本
kubectl api-versions
```

### 节点管理
```bash
# 查看所有节点
# 显示节点的状态、角色、年龄和版本信息
kubectl get nodes
# 简写：kubectl get no

# 查看节点详细信息
# 包含节点的资源容量、条件、标签、注解等详细信息
kubectl describe node <node-name>
# 示例：kubectl describe node minikube

# 给节点打标签
# 用于节点选择器，控制Pod调度到特定节点
kubectl label node <node-name> <label-key>=<label-value>
# 示例：kubectl label node minikube disktype=ssd

# 查看节点标签
# 显示节点的所有标签
kubectl get nodes --show-labels

# 节点排水（驱逐节点上的Pod）
# 用于节点维护，会将节点上的Pod迁移到其他节点
kubectl drain <node-name> --ignore-daemonsets

# 节点恢复
# 将节点重新标记为可调度
kubectl uncordon <node-name>
```

## 资源管理

### Pod管理
```bash
# 查看所有Pod
kubectl get pods
# 简写：kubectl get po

# 查看所有Pod（包含所有命名空间）
# -A 表示所有命名空间（--all-namespaces）
kubectl get pods -A

# 查看指定命名空间的Pod
# -n 指定命名空间
kubectl get pods -n <namespace>
# 示例：kubectl get pods -n kube-system

# 查看Pod详细信息
# 包含Pod的事件、容器状态、IP地址等
kubectl describe pod <pod-name>

# 查看Pod标签
# 显示Pod的标签信息
kubectl get pods --show-labels

# 按标签筛选Pod
# -l 按标签选择器筛选
kubectl get pods -l <label-key>=<label-value>
# 示例：kubectl get pods -l app=nginx

# 查看Pod日志
# 查看Pod内容器的标准输出日志
kubectl logs <pod-name>

# 实时查看Pod日志（类似tail -f）
kubectl logs -f <pod-name>

# 查看Pod的最后100行日志
kubectl logs --tail=100 <pod-name>

# 查看之前容器的日志（容器重启后）
kubectl logs --previous <pod-name>

# 进入Pod容器
# 在Pod容器中执行命令
kubectl exec -it <pod-name> -- /bin/bash

# 在Pod中执行单次命令
kubectl exec <pod-name> -- <command>
# 示例：kubectl exec my-pod -- ls /tmp

# 复制文件到Pod
kubectl cp <local-file> <pod-name>:<container-path>
# 示例：kubectl cp ./test.txt nginx-pod:/tmp/

# 从Pod复制文件到本地
kubectl cp <pod-name>:<container-path> <local-file>
# 示例：kubectl cp nginx-pod:/etc/nginx/nginx.conf ./nginx.conf

# 端口转发
# 将本地端口转发到Pod端口
kubectl port-forward <pod-name> <local-port>:<pod-port>
# 示例：kubectl port-forward nginx-pod 8080:80
# 然后访问 http://localhost:8080

# 删除Pod
kubectl delete pod <pod-name>

# 强制删除Pod（立即删除，不等待优雅终止）
kubectl delete pod <pod-name> --grace-period=0 --force
```

### Deployment管理
```bash
# 查看所有Deployment
kubectl get deployments
# 简写：kubectl get deploy

# 查看Deployment详细信息
kubectl describe deployment <deployment-name>

# 创建Deployment（命令方式）
kubectl create deployment <name> --image=<image>
# 示例：kubectl create deployment nginx --image=nginx:1.23

# 扩缩容Deployment
kubectl scale deployment <deployment-name> --replicas=<number>
# 示例：kubectl scale deployment nginx --replicas=5

# 更新Deployment镜像
kubectl set image deployment <deployment-name> <container-name>=<new-image>
# 示例：kubectl set image deployment nginx nginx=nginx:1.24

# 查看滚动更新状态
kubectl rollout status deployment <deployment-name>

# 查看Deployment更新历史
kubectl rollout history deployment <deployment-name>

# 回滚到上一个版本
kubectl rollout undo deployment <deployment-name>

# 回滚到指定版本
kubectl rollout undo deployment <deployment-name> --to-revision=<revision>

# 暂停滚动更新
kubectl rollout pause deployment <deployment-name>

# 恢复滚动更新
kubectl rollout resume deployment <deployment-name>

# 重启Deployment（触发滚动更新）
kubectl rollout restart deployment <deployment-name>
```

### Service管理
```bash
# 查看所有Service
kubectl get services
# 简写：kubectl get svc

# 查看Service详细信息
kubectl describe service <service-name>

# 查看Service的Endpoints
# 显示Service后端Pod的IP和端口
kubectl get endpoints <service-name>
# 简写：kubectl get ep

# 创建Service（命令方式）
kubectl expose deployment <deployment-name> --type=<type> --port=<port>
# 示例：kubectl expose deployment nginx --type=NodePort --port=80

# 删除Service
kubectl delete service <service-name>

# 查看Service的DNS名称
kubectl get service <service-name> -o jsonpath='{.metadata.name}.{.metadata.namespace}.svc.cluster.local'
```

### 命名空间管理
```bash
# 查看所有命名空间
kubectl get namespaces
# 简写：kubectl get ns

# 创建命名空间
kubectl create namespace <namespace-name>
# 示例：kubectl create namespace production

# 删除命名空间（会删除该命名空间下的所有资源）
kubectl delete namespace <namespace-name>

# 在指定命名空间中运行命令
kubectl <command> -n <namespace>
# 示例：kubectl get pods -n production

# 设置当前默认命名空间
kubectl config set-context --current --namespace=<namespace>
# 示例：kubectl config set-context --current --namespace=production
```

## 配置管理

### ConfigMap管理
```bash
# 查看所有ConfigMap
kubectl get configmaps
# 简写：kubectl get cm

# 创建ConfigMap（命令方式）
kubectl create configmap <name> --from-literal=<key>=<value>
# 示例：kubectl create configmap app-config --from-literal=database=mysql

# 从文件创建ConfigMap
kubectl create configmap <name> --from-file=<path>
# 示例：kubectl create configmap nginx-config --from-file=nginx.conf

# 查看ConfigMap详细信息
kubectl describe configmap <configmap-name>

# 查看ConfigMap的YAML配置
kubectl get configmap <configmap-name> -o yaml

# 删除ConfigMap
kubectl delete configmap <configmap-name>
```

### Secret管理
```bash
# 查看所有Secret
kubectl get secrets

# 创建Secret（命令方式）
kubectl create secret generic <name> --from-literal=<key>=<value>
# 示例：kubectl create secret generic db-password --from-literal=password=mypass

# 从文件创建Secret
kubectl create secret generic <name> --from-file=<path>

# 查看Secret（base64编码的值）
kubectl get secret <secret-name> -o yaml

# 查看Secret的解码值
kubectl get secret <secret-name> -o jsonpath='{.data.<key>}' | base64 --decode

# 删除Secret
kubectl delete secret <secret-name>
```

## 调试与故障排查

### 事件查看
```bash
# 查看所有事件
kubectl get events

# 按时间排序查看事件
kubectl get events --sort-by=.metadata.creationTimestamp

# 查看指定命名空间的事件
kubectl get events -n <namespace>

# 查看特定资源的事件
kubectl get events --field-selector involvedObject.name=<resource-name>
```

### 资源使用情况
```bash
# 查看节点资源使用情况
kubectl top nodes

# 查看Pod资源使用情况
kubectl top pods

# 查看指定命名空间的Pod资源使用
kubectl top pods -n <namespace>

# 查看Pod内容器的资源使用
kubectl top pod <pod-name> --containers
```

### 网络调试
```bash
# 测试DNS解析
kubectl run test-pod --image=busybox -it --rm -- nslookup <service-name>

# 测试网络连通性
kubectl run test-pod --image=busybox -it --rm -- ping <ip-or-hostname>

# 测试HTTP服务
kubectl run test-pod --image=busybox -it --rm -- wget -qO- http://<service-name>

# 查看网络策略
kubectl get networkpolicies
```

### 权限调试
```bash
# 查看服务账户
kubectl get serviceaccounts
# 简写：kubectl get sa

# 查看角色
kubectl get roles

# 查看集群角色
kubectl get clusterroles

# 查看角色绑定
kubectl get rolebindings

# 查看集群角色绑定
kubectl get clusterrolebindings
```

## YAML文件操作

### 应用YAML文件
```bash
# 创建或更新资源
kubectl apply -f <file.yaml>

# 创建资源（已存在会报错）
kubectl create -f <file.yaml>

# 删除YAML文件中的资源
kubectl delete -f <file.yaml>

# 预览YAML文件的更改（不实际应用）
kubectl apply -f <file.yaml> --dry-run=client

# 生成YAML文件但不应用（用于调试）
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > nginx.yaml
```

### 导出资源为YAML
```bash
# 导出Deployment为YAML
kubectl get deployment <deployment-name> -o yaml > deployment.yaml

# 导出Service为YAML
kubectl get service <service-name> -o yaml > service.yaml

# 导出所有资源为YAML
kubectl get all -o yaml > all-resources.yaml
```

## 高级命令

### 批量操作
```bash
# 删除所有Pod（谨慎使用）
kubectl delete pods --all

# 删除所有指定标签的资源
kubectl delete pods -l <label-key>=<label-value>

# 强制删除所有终止状态的Pod
kubectl delete pods --field-selector status.phase=Failed
```

### 资源编辑
```bash
# 编辑资源（实时修改）
kubectl edit <resource-type> <resource-name>
# 示例：kubectl edit deployment nginx

# 替换资源（使用YAML文件）
kubectl replace -f <file.yaml>

# 打补丁（部分修改）
kubectl patch <resource-type> <resource-name> -p '<json-patch>'
# 示例：kubectl patch deployment nginx -p '{"spec":{"replicas":3}}'
```

### 输出格式化
```bash
# JSON格式输出
kubectl get pods -o json

# YAML格式输出
kubectl get pods -o yaml

# 自定义列输出
kubectl get pods -o custom-columns=NAME:.metadata.name,STATUS:.status.phase

# 只输出特定字段
kubectl get pods -o jsonpath='{.items[*].metadata.name}'

# 宽格式输出（显示更多信息）
kubectl get pods -o wide
```

## 实用技巧

### 命令别名设置
```bash
# 在~/.bashrc或~/.zshrc中添加别名
alias k='kubectl'
alias kgp='kubectl get pods'
alias kgd='kubectl get deployments'
alias kgs='kubectl get services'
alias kdesc='kubectl describe'
alias kdel='kubectl delete'
```

### 自动补全
```bash
# 启用kubectl命令补全（bash）
source <(kubectl completion bash)

# 添加到~/.bashrc使其永久生效
echo "source <(kubectl completion bash)" >> ~/.bashrc

# 启用kubectl命令补全（zsh）
source <(kubectl completion zsh)
```

### 常用组合命令
```bash
# 查看最近创建的Pod
kubectl get pods --sort-by=.metadata.creationTimestamp | tail -10

# 查看失败的Pod
kubectl get pods --field-selector status.phase=Failed

# 查看特定标签的所有资源
kubectl get all -l <label-key>=<label-value>

# 查看资源使用情况并排序
kubectl top pods --sort-by=cpu | head -10

# 查看Pod的IP地址
kubectl get pods -o wide

# 批量删除Evicted状态的Pod
kubectl get pods --field-selector status.phase=Failed -o name | xargs kubectl delete
```

## 故障排查流程

### Pod问题排查步骤
1. **查看Pod状态**：`kubectl get pods`
2. **查看Pod事件**：`kubectl describe pod <pod-name>`
3. **查看Pod日志**：`kubectl logs <pod-name>`
4. **进入容器调试**：`kubectl exec -it <pod-name> -- /bin/bash`
5. **检查资源使用**：`kubectl top pod <pod-name>`

### Service问题排查步骤
1. **检查Service存在**：`kubectl get svc`
2. **检查Endpoints**：`kubectl get endpoints <service-name>`
3. **测试Service连通性**：`kubectl run test --image=busybox -it --rm -- wget <service-name>`
4. **检查Pod标签**：`kubectl get pods --show-labels`
5. **检查网络策略**：`kubectl get networkpolicies`

### 常用调试命令
```bash
# 创建临时调试Pod
kubectl run debug-pod --image=busybox -it --rm -- /bin/sh

# 查看Pod的环境变量
kubectl exec <pod-name> -- env

# 查看Pod的DNS配置
kubectl exec <pod-name> -- cat /etc/resolv.conf

# 测试Pod的网络连通性
kubectl exec <pod-name> -- ping <target>

# 查看Pod的挂载信息
kubectl exec <pod-name> -- mount | grep <mount-point>
```