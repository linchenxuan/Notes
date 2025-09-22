# Kubernetes 学习阶段规划

基于你已有的快速入门基础，我将K8s学习分为6个阶段，每个阶段都有明确的目标、学习内容和实践任务。

## 🎯 阶段1：基础概念掌握（1-2周）

### 学习目标
- 理解K8s的核心概念和架构
- 掌握基本的kubectl命令
- 能够部署简单的应用

### 核心概念
```
容器编排思想 → K8s架构 → 核心资源类型 → 基本操作
```

### 学习重点
- **Pod**：最小调度单元，理解"容器组"概念
- **Deployment**：无状态应用管理，理解"期望状态"思想  
- **Service**：服务发现和负载均衡
- **Namespace**：资源隔离和分组管理

### 实践任务
1. 使用Minikube搭建本地集群
2. 部署第一个Nginx应用（使用你的nginx-deployment.yaml）
3. 创建Service暴露服务（使用你的nginx-service.yaml）
4. 练习扩缩容操作

### 验证标准
- ✅ 能够独立部署一个Web应用
- ✅ 能够通过Service访问应用
- ✅ 能够进行基本的扩缩容操作
- ✅ 理解Pod、Deployment、Service的作用

---

## 🔧 阶段2：资源配置与YAML精通（2-3周）

### 学习目标
- 掌握YAML语法和资源配置
- 理解资源对象的结构和字段含义
- 能够编写复杂的资源配置

### 学习重点
- **YAML语法**：缩进、数据结构、注释规范
- **资源结构**：apiVersion、kind、metadata、spec
- **标签和选择器**：资源组织和选择的机制
- **注解**：元数据管理和工具集成

### 实践任务
1. 手写Deployment配置（包含资源限制、健康检查）
2. 创建ConfigMap和Secret管理配置
3. 练习标签和选择器的使用
4. 学习使用kubectl explain查看字段说明

### 示例练习
```yaml
# 任务：创建一个带资源限制和健康检查的Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web
        image: nginx:1.23
        resources:           # 资源限制
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi
        readinessProbe:      # 就绪检查
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
        livenessProbe:       # 存活检查
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 30
```

### 验证标准
- ✅ 能够手写常见的资源配置文件
- ✅ 理解每个字段的作用和语法
- ✅ 能够使用标签组织和管理资源
- ✅ 掌握ConfigMap和Secret的使用

---

## 🚀 阶段3：应用部署与管理（3-4周）

### 学习目标
- 掌握不同类型应用的部署方式
- 理解有状态和无状态应用的区别
- 学会应用的更新和回滚策略

### 学习重点
- **无状态应用**：Deployment的最佳实践
- **有状态应用**：StatefulSet的使用场景
- **守护进程**：DaemonSet的部署模式
- **任务和定时任务**：Job和CronJob的使用

### 应用类型对比

| 应用类型 | 使用资源 | 典型场景 | 特点 |
|---------|---------|---------|------|
| 无状态应用 | Deployment | Web服务、API服务 | 可互换、可扩展 |
| 有状态应用 | StatefulSet | 数据库、消息队列 | 稳定标识、持久存储 |
| 守护进程 | DaemonSet | 日志收集、监控代理 | 每个节点运行一个 |
| 批处理任务 | Job/CronJob | 数据备份、定时清理 | 完成后退出 |

### 实践任务
1. 部署无状态的Web应用集群
2. 部署有状态的Redis主从集群
3. 使用DaemonSet部署日志收集器
4. 创建定时备份任务

### 更新策略学习
```bash
# 学习不同的更新策略
kubectl set image deployment web-app web=nginx:1.24

# 查看更新状态
kubectl rollout status deployment web-app

# 回滚到上一个版本
kubectl rollout undo deployment web-app

# 查看更新历史
kubectl rollout history deployment web-app
```

### 验证标准
- ✅ 能够部署不同类型的应用
- ✅ 理解有状态和无状态的区别
- ✅ 掌握滚动更新和回滚操作
- ✅ 能够选择合适的资源类型

---

## 🔌 阶段4：网络与服务治理（3-4周）

### 学习目标
- 掌握K8s网络模型和服务发现
- 理解Ingress和Service的工作原理
- 学会配置负载均衡和流量管理

### 学习重点
- **Service类型**：ClusterIP、NodePort、LoadBalancer、ExternalName
- **Ingress控制器**：HTTP/HTTPS路由管理
- **网络策略**：Pod间网络访问控制
- **DNS服务发现**：集群内服务发现机制

### 网络模型理解
```
Pod网络 → Service网络 → Ingress网络 → 外部访问
```

### Service类型详解

| Service类型 | 使用场景 | 访问方式 | 特点 |
|-----------|---------|---------|------|
| ClusterIP | 集群内部通信 | service-name.namespace.svc.cluster.local | 默认类型 |
| NodePort | 开发测试环境 | node-ip:node-port | 简单直接 |
| LoadBalancer | 生产环境 | 云负载均衡器IP | 需要云支持 |
| ExternalName | 外部服务 | 自定义域名 | 服务代理 |

### 实践任务
1. 配置不同类型的Service
2. 部署Ingress控制器和路由规则
3. 配置HTTPS证书和域名
4. 设置网络策略控制访问

### Ingress配置示例
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

### 验证标准
- ✅ 理解K8s网络模型
- ✅ 能够配置不同类型的Service
- ✅ 掌握Ingress的使用方法
- ✅ 能够配置基本的网络策略

---

## 💾 阶段5：存储与配置管理（2-3周）

### 学习目标
- 掌握K8s存储体系和持久化方案
- 理解ConfigMap和Secret的高级用法
- 学会管理应用配置和敏感信息

### 学习重点
- **存储类型**：EmptyDir、HostPath、NFS、云存储
- **持久卷**：PV和PVC的生命周期
- **配置管理**：ConfigMap的热更新、Secret的安全管理
- **存储类**：StorageClass和动态供应

### 存储体系结构
```
存储需求 → PVC申请 → PV匹配 → StorageClass动态创建 → Pod使用
```

### 存储类型对比

| 存储类型 | 生命周期 | 使用场景 | 数据持久性 |
|---------|---------|---------|-----------|
| EmptyDir | Pod生命周期 | 临时缓存 | Pod删除即丢失 |
| HostPath | 节点生命周期 | 单节点测试 | 节点相关 |
| NFS | 独立于K8s | 多节点共享 | 网络存储 |
| 云存储 | 独立于K8s | 生产环境 | 云平台保证 |

### 实践任务
1. 配置不同类型的存储卷
2. 创建持久卷和持久卷声明
3. 部署有状态数据库应用
4. 配置ConfigMap的热更新

### 存储配置示例
```yaml
# 持久卷声明
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: web-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: standard

---
# 在Pod中使用
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  template:
    spec:
      containers:
      - name: web
        image: nginx:1.23
        volumeMounts:
        - name: data
          mountPath: /usr/share/nginx/html
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: web-data
```

### 验证标准
- ✅ 理解不同存储类型的特点
- ✅ 能够配置持久卷和声明
- ✅ 掌握ConfigMap和Secret的使用
- ✅ 能够为有状态应用配置存储

---

## 🔐 阶段6：安全与运维（4-6周）

### 学习目标
- 掌握K8s安全机制和最佳实践
- 理解RBAC权限控制系统
- 学会集群监控和日志管理

### 学习重点
- **RBAC授权**：角色、集群角色、角色绑定
- **安全策略**：Pod安全策略、网络策略
- **服务账户**：身份认证和授权
- **监控日志**：Prometheus、Grafana、ELK栈

### 安全体系结构
```
认证 → 授权 → 准入控制 → 安全策略 → 审计日志
```

### RBAC组件

| RBAC组件 | 作用域 | 用途 |
|---------|--------|------|
| Role | 命名空间 | 定义命名空间内的权限 |
| ClusterRole | 集群 | 定义集群范围的权限 |
| RoleBinding | 命名空间 | 将角色绑定到用户/组 |
| ClusterRoleBinding | 集群 | 将集群角色绑定到用户/组 |

### 实践任务
1. 配置用户认证和RBAC授权
2. 设置Pod安全策略和网络策略
3. 部署Prometheus监控体系
4. 配置日志收集和分析

### RBAC配置示例
```yaml
# 创建角色
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]

---
# 绑定角色
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### 验证标准
- ✅ 理解K8s安全机制
- ✅ 能够配置RBAC授权
- ✅ 掌握基本的安全策略
- ✅ 能够设置监控和日志收集

---

## 📈 进阶学习路径（持续）

### 云原生生态
- **Helm**：K8s包管理器
- **Operators**：有状态应用自动化管理
- **Service Mesh**：Istio服务网格
- **GitOps**：ArgoCD持续交付

### 高级主题
- **多集群管理**：Federation、集群联邦
- **边缘计算**：K3s、KubeEdge
- **Serverless**：Knative、OpenFaaS
- **机器学习**：Kubeflow

### 学习资源推荐

#### 官方资源
- [Kubernetes官方文档](https://kubernetes.io/docs/)
- [Kubernetes API参考](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.28/)
- [kubectl命令参考](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)

#### 实践平台
- [Katacoda K8s教程](https://www.katacoda.com/courses/kubernetes)
- [Play with K8s](https://labs.play-with-k8s.com/)
- [Minikube官方文档](https://minikube.sigs.k8s.io/docs/)

#### 书籍推荐
- 《Kubernetes权威指南》
- 《Kubernetes in Action》
- 《Kubernetes进阶实战》

---

## 🎯 学习建议

### 总体策略
1. **循序渐进**：每个阶段都要扎实掌握再进入下一阶段
2. **理论结合实践**：50%理论学习 + 50%动手实践
3. **项目驱动**：用实际项目来驱动学习
4. **社区参与**：加入K8s社区，参与讨论和贡献

### 时间管理建议
- **阶段1-2**：每天1-2小时，4-5周完成
- **阶段3-4**：每天2小时，6-7周完成  
- **阶段5-6**：每天1-2小时，6-9周完成
- **总计**：4-6个月达到中级水平

### 实践项目建议
1. **个人博客系统**：从单应用到微服务架构
2. **电商系统**：包含前端、后端、数据库的完整应用
3. **监控系统**：Prometheus + Grafana + 自定义应用
4. **CI/CD系统**：Jenkins + GitLab + 自动化部署

记住：**Kubernetes学习是一个持续的过程**，每个阶段都需要大量的实践来巩固。不要急于求成，稳扎稳打才能真正掌握这门技术。