# Kubernetes / 云原生学习文档

> 面向有游戏服务器开发经验的工程师，包含完整的概念讲解、实操步骤和验证清单。

---

## 学习路线

```
第一阶段          第二阶段           第三阶段          第四阶段          第五阶段       第六阶段
容器基础  ──→  K8s 核心概念  ──→  进阶编排    ──→  云原生工具链  ──→  生产实战  ──→  CKA认证
(1-2周)        (2-3周)          (2-3周)         (3-4周)          (2-3周)       (2-3周)
```

---

## 文档目录

### 第一阶段：容器基础（1-2 周）

| 编号 | 文档 | 核心内容 |
|------|------|----------|
| 1.1 | [Docker 核心概念](phase1/1.1-docker-core.md) | 镜像分层、Dockerfile、multi-stage build、docker compose |
| 1.2 | [容器网络与存储](phase1/1.2-container-network-storage.md) | bridge/host/none 网络、Volume 持久化 |

### 第二阶段：Kubernetes 核心概念（2-3 周）

| 编号 | 文档 | 核心内容 |
|------|------|----------|
| 2.1 | [集群搭建](phase2/2.1-cluster-setup.md) | K8s 架构、minikube/kind 搭建、组件协作流程 |
| 2.2 | [Pod / Deployment / Service](phase2/2.2-pod-deployment-service.md) | Pod 生命周期、滚动更新、Service 类型 |
| 2.3 | [ConfigMap / Secret / PV / PVC](phase2/2.3-configmap-secret-pv.md) | 配置注入、持久化存储、StatefulSet + PVC |
| 2.4 | [Namespace 与 RBAC](phase2/2.4-namespace-rbac.md) | 命名空间隔离、角色权限控制 |

### 第三阶段：进阶编排与游戏场景（2-3 周）

| 编号 | 文档 | 核心内容 |
|------|------|----------|
| 3.1 | [StatefulSet](phase3/3.1-statefulset.md) | 有状态服务、Headless Service、DNS 解析 |
| 3.2 | [DaemonSet / Job / CronJob](phase3/3.2-daemonset-job-cronjob.md) | 守护进程、一次性任务、定时任务 |
| 3.3 | [网络策略与 Ingress](phase3/3.3-networkpolicy-ingress.md) | NetworkPolicy 防火墙、Ingress 路由 |
| 3.4 | [资源管理与调度](phase3/3.4-resource-scheduling.md) | requests/limits、QoS、HPA、亲和性 |

### 第四阶段：云原生生态工具链（3-4 周）

| 编号 | 文档 | 核心内容 |
|------|------|----------|
| 4.1 | [Helm 包管理](phase4/4.1-helm.md) | Chart 开发、模板语法、环境区分 |
| 4.2 | [CI/CD GitOps](phase4/4.2-cicd-gitops.md) | GitOps 理念、ArgoCD 自动部署 |
| 4.3 | [可观测性](phase4/4.3-observability.md) | Prometheus + Grafana 监控、告警配置 |
| 4.4 | [Istio 服务网格](phase4/4.4-service-mesh-istio.md) | Sidecar、灰度发布、流量管理（选学） |

### 第五阶段：生产实战与游戏场景深化（2-3 周）

| 编号 | 文档 | 核心内容 |
|------|------|----------|
| 5.1 | [长连接与会话保持](phase5/5.1-long-connection.md) | WebSocket 优雅关闭、Session Affinity、PDB |
| 5.2 | [Agones 游戏服务器编排](phase5/5.2-agones-game-server.md) | GameServer/Fleet/Allocation、匹配系统集成 |
| 5.3 | [安全加固](phase5/5.3-security.md) | Pod 安全策略、Trivy 镜像扫描、Kyverno |

### 第六阶段：CKA 认证备考（可选，2-3 周）

| 编号 | 文档 | 核心内容 |
|------|------|----------|
| 6.1 | [CKA 备考指南](phase6/6.1-cka-prep.md) | 考试大纲、kubectl 技巧、高频考点模板、时间管理 |

---

## 学习建议

1. **按顺序推进**：每阶段的验证清单全部通过后再进入下一阶段
2. **动手为主**：每个文档的实操部分务必亲自执行，不要只看不练
3. **搭建本地环境**：用 minikube 或 kind 搭建本地集群，反复练习
4. **结合游戏场景**：用自己熟悉的游戏服务替换示例中的 nginx
5. **善用官方文档**：kubernetes.io/docs 是最权威的参考

---

## 时间总览

| 阶段 | 主题 | 建议时长 |
|------|------|----------|
| 一 | 容器基础 | 1-2 周 |
| 二 | K8s 核心概念 | 2-3 周 |
| 三 | 进阶编排 + 游戏场景 | 2-3 周 |
| 四 | 云原生工具链 | 3-4 周 |
| 五 | 生产实战 | 2-3 周 |
| 六 | CKA 认证（可选） | 2-3 周 |

**总计约 12-18 周**，按每天 1-2 小时业余学习估算。
