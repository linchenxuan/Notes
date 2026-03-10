# Kubernetes / 云原生学习计划

> 面向有5年游戏服务器开发经验的工程师，每阶段有明确的验证标准。

---

## 第一阶段：容器基础（1-2 周）

### 1.1 Docker 核心概念

- **学习内容**：镜像、容器、Dockerfile、镜像分层原理、Union FS
- **实操**：
  - 将你熟悉的一个游戏服务（如 gate/login/game server）编写 Dockerfile 并构建镜像
  - 使用 multi-stage build 优化镜像体积
  - 用 `docker compose` 编排一套本地多服务环境（至少包含游戏服务 + Redis + MySQL）
- **验证标准**：
  - [ ] 能用 `docker build` 构建镜像，体积 < 100MB（Go/C++ 服务）
  - [ ] `docker compose up` 一键拉起本地完整开发环境，服务间可正常通信
  - [ ] 能解释 `COPY` vs `ADD`、`CMD` vs `ENTRYPOINT`、`.dockerignore` 的作用

### 1.2 容器网络与存储

- **学习内容**：bridge/host/none 网络模式、端口映射、volume 与 bind mount
- **实操**：
  - 用 `docker network create` 创建自定义网络，让两个容器通过容器名互相访问
  - 用 volume 持久化 MySQL 数据，删除容器后重建验证数据不丢失
- **验证标准**：
  - [ ] 能画出 Docker bridge 网络下容器间通信的数据流路径
  - [ ] 删除并重建 MySQL 容器后，数据完整保留

---

## 第二阶段：Kubernetes 核心概念（2-3 周）

### 2.1 集群搭建

- **学习内容**：K8s 架构（API Server、etcd、Scheduler、Controller Manager、kubelet、kube-proxy）
- **实操**：
  - 用 **minikube** 或 **kind** 搭建本地单节点集群
  - 用 `kubectl cluster-info`、`kubectl get nodes` 确认集群状态
- **验证标准**：
  - [ ] 能在白板上画出 K8s 控制面和数据面的完整架构图，标注各组件职责
  - [ ] 本地集群 `Ready` 状态运行

### 2.2 核心资源对象 — Pod / Deployment / Service

- **学习内容**：Pod 生命周期、ReplicaSet、Deployment 滚动更新策略、Service 类型（ClusterIP/NodePort/LoadBalancer）
- **实操**：
  - 编写 Deployment YAML 部署一个无状态游戏网关服务（replicas: 3）
  - 创建 ClusterIP Service 暴露服务，用 `kubectl port-forward` 本地访问
  - 执行滚动更新：修改镜像版本 → `kubectl apply` → 观察 Pod 逐步替换
  - 执行回滚：`kubectl rollout undo`
- **验证标准**：
  - [ ] `kubectl get pods` 显示 3 个 Running 的 Pod
  - [ ] 滚动更新过程中服务不中断（用脚本持续请求验证）
  - [ ] 回滚后镜像版本恢复到上一版

### 2.3 配置与存储 — ConfigMap / Secret / PV / PVC

- **学习内容**：配置注入方式（env / volume mount）、Secret 编码、StorageClass、PV/PVC 绑定机制
- **实操**：
  - 将游戏服务的配置文件（如 server.yaml）通过 ConfigMap 挂载进 Pod
  - 将数据库密码存入 Secret，以环境变量注入
  - 创建 PVC 给 MySQL StatefulSet 使用
- **验证标准**：
  - [ ] 修改 ConfigMap 后，Pod 内的配置文件内容同步更新（volume 挂载方式）
  - [ ] `kubectl get secret xxx -o yaml` 能看到 base64 编码的值，Pod 内能正确读取明文
  - [ ] 删除 MySQL Pod 后自动重建，数据通过 PVC 保留

### 2.4 命名空间与 RBAC

- **学习内容**：Namespace 隔离、ServiceAccount、Role/ClusterRole、RoleBinding
- **实操**：
  - 创建 `game-dev` 和 `game-prod` 两个命名空间
  - 为 `game-dev` 创建只读 ServiceAccount，验证无法执行 `delete` 操作
- **验证标准**：
  - [ ] 使用受限 SA 执行 `kubectl delete pod xxx` 返回 `Forbidden`

---

## 第三阶段：进阶编排与游戏服务场景（2-3 周）

### 3.1 StatefulSet — 有状态游戏服务

- **学习内容**：StatefulSet 有序部署/删除、稳定网络标识、Headless Service
- **实操**：
  - 用 StatefulSet 部署一个游戏房间服务（game-room-0, game-room-1, game-room-2）
  - 配合 Headless Service，验证 Pod 通过 `game-room-0.game-room-svc` 互访
- **验证标准**：
  - [ ] Pod 名称稳定有序，重启后名称不变
  - [ ] 从 Pod 内部 `nslookup game-room-0.game-room-svc` 解析成功

### 3.2 DaemonSet / Job / CronJob

- **学习内容**：DaemonSet（每节点一个）、Job（一次性任务）、CronJob（定时任务）
- **实操**：
  - DaemonSet 部署日志采集 agent（如 Fluent Bit）
  - CronJob 模拟每日凌晨执行游戏数据统计任务
- **验证标准**：
  - [ ] 每个 Node 上都有且只有一个 DaemonSet Pod
  - [ ] CronJob 按 schedule 触发，`kubectl get jobs` 可看到历史执行记录

### 3.3 网络策略与 Ingress

- **学习内容**：NetworkPolicy、Ingress Controller（Nginx Ingress）、路径路由、TLS 终止
- **实操**：
  - 安装 Nginx Ingress Controller
  - 配置 Ingress 规则：`/api/gate` → gate-service，`/api/login` → login-service
  - 用 NetworkPolicy 限制只有 gate Pod 能访问 game Pod
- **验证标准**：
  - [ ] 通过 Ingress 外部地址访问不同路径，流量正确路由到对应服务
  - [ ] 从非 gate Pod 访问 game Pod 被拒绝

### 3.4 资源管理与调度

- **学习内容**：requests/limits、QoS 等级（Guaranteed/Burstable/BestEffort）、HPA、Node Affinity/Taint/Toleration
- **实操**：
  - 为游戏服务设置 CPU/Memory requests 和 limits
  - 配置 HPA：当 CPU > 60% 时自动扩容至最多 10 个 Pod
  - 用压测工具（wrk/vegeta）触发扩容，观察 Pod 数量变化
- **验证标准**：
  - [ ] 压测期间 `kubectl get hpa` 显示 replicas 自动增加
  - [ ] 压测结束后 replicas 自动缩回

---

## 第四阶段：云原生生态工具链（3-4 周）

### 4.1 Helm — 包管理

- **学习内容**：Chart 结构、values.yaml、模板语法、Release 管理
- **实操**：
  - 将第二、三阶段的所有 YAML 封装为一个 Helm Chart（game-server chart）
  - 通过 `values-dev.yaml` / `values-prod.yaml` 区分环境配置
  - `helm install` / `helm upgrade` / `helm rollback`
- **验证标准**：
  - [ ] `helm install game ./game-chart -f values-dev.yaml` 一键部署完整游戏服务栈
  - [ ] `helm upgrade` 修改副本数后生效，`helm rollback` 恢复

### 4.2 CI/CD — GitOps 流水线

- **学习内容**：GitOps 理念、ArgoCD 或 FluxCD、镜像自动构建
- **实操**：
  - 搭建 ArgoCD，连接 Git 仓库
  - 推送代码 → CI 自动构建镜像 → 更新 Helm values 中的 image tag → ArgoCD 自动同步部署
- **验证标准**：
  - [ ] `git push` 后 ArgoCD 界面显示 Sync 状态变化，新版本自动部署
  - [ ] ArgoCD 中执行 Rollback 恢复到上一版本

### 4.3 可观测性 — 监控 / 日志 / 链路追踪

- **学习内容**：Prometheus + Grafana（监控）、Loki 或 EFK（日志）、Jaeger（链路追踪）
- **实操**：
  - 用 `kube-prometheus-stack` Helm chart 部署监控栈
  - 在游戏服务中暴露自定义 metrics（如在线人数、匹配延迟）
  - 配置 Grafana Dashboard 展示游戏关键指标
  - 配置告警规则：匹配延迟 > 500ms 时触发告警
- **验证标准**：
  - [ ] Grafana Dashboard 上实时展示游戏服务的自定义指标
  - [ ] 人为制造延迟后，Alertmanager 触发告警通知

### 4.4 服务网格 — Istio（选学）

- **学习内容**：Sidecar 模式、流量管理（VirtualService/DestinationRule）、灰度发布、故障注入
- **实操**：
  - 安装 Istio，启用 sidecar 自动注入
  - 配置灰度发布：90% 流量到 v1，10% 到 v2
  - 用 Kiali 观察服务拓扑和流量分布
- **验证标准**：
  - [ ] 持续请求后统计，流量比例接近 9:1
  - [ ] Kiali 服务拓扑图正确展示服务间调用关系

---

## 第五阶段：生产实战与游戏场景深化（2-3 周）

### 5.1 游戏服务专项：长连接与会话保持

- **学习内容**：WebSocket/TCP 长连接在 K8s 中的挑战、Session Affinity、连接优雅关闭
- **实操**：
  - 部署 WebSocket 游戏网关，配置 Service 的 `sessionAffinity: ClientIP`
  - 实现 preStop hook + SIGTERM 优雅关闭：通知客户端重连 → 等待连接排空 → 退出
  - 滚动更新时验证玩家连接平滑迁移
- **验证标准**：
  - [ ] 滚动更新期间，已连接客户端收到重连通知，无异常断连
  - [ ] `terminationGracePeriodSeconds` 期间连接正常排空

### 5.2 游戏服务专项：专用游戏服务器（DS）编排

- **学习内容**：[Agones](https://agones.dev) 开源框架、GameServer / Fleet / FleetAutoscaler CRD
- **实操**：
  - 安装 Agones，部署一个简单的 UDP 游戏服务器 Fleet
  - 通过 Agones Allocator 分配 GameServer，模拟匹配系统
  - 配置 FleetAutoscaler 基于 buffer 策略自动扩缩
- **验证标准**：
  - [ ] Allocate 请求返回一个 Ready → Allocated 状态的 GameServer 地址和端口
  - [ ] 持续 Allocate 后，Fleet 自动扩容新的 GameServer Pod

### 5.3 安全加固

- **学习内容**：Pod Security Standards、镜像扫描（Trivy）、OPA/Kyverno 策略
- **实操**：
  - 配置 Pod Security Admission：禁止 privileged 容器
  - 用 Trivy 扫描游戏服务镜像，修复 HIGH/CRITICAL 漏洞
- **验证标准**：
  - [ ] 部署 `privileged: true` 的 Pod 被拒绝
  - [ ] Trivy 扫描结果无 CRITICAL 漏洞

---

## 第六阶段：CKA 认证备考（可选，2-3 周）

- **学习资源**：Killer.sh 模拟考试、[K8s 官方文档](https://kubernetes.io/docs/)
- **练习重点**：纯 `kubectl` + YAML 手写能力（考试可查文档但不能联网）
- **模拟考试**：Killer.sh 提供 2 次模拟，目标得分 > 80%
- **验证标准**：
  - [ ] Killer.sh 模拟考试得分 ≥ 80%
  - [ ] 通过 CKA 认证考试（及格线 66%）

---

## 推荐学习资源

| 类型 | 资源 | 说明 |
|------|------|------|
| 交互式 | [Play with Kubernetes](https://labs.play-with-k8s.com/) | 免费在线 K8s 环境 |
| 交互式 | [Killercoda](https://killercoda.com/) | 免费 K8s 交互实验 |
| 书籍 | 《Kubernetes in Action》 | 体系化深入 |
| 官方文档 | [kubernetes.io/docs](https://kubernetes.io/docs/) | 最权威最新 |
| 游戏专项 | [Agones 文档](https://agones.dev/site/docs/) | 游戏服务器编排 |

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

**总计约 12-18 周**，按每天 1-2 小时业余学习估算。全日制投入可压缩到 6-8 周。

建议按顺序推进，每个阶段的验证标准全部通过后再进入下一阶段。
