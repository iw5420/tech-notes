---
title: Kubernetes 四週學習計畫
tags:
  - Plan - K8s
---

# 🏗️ Kubernetes 四週學習計畫

目標：從基礎 → 多機 → 部署應用 → 體驗高可用  
環境：3 台 Windows + VMware Ubuntu VM  

---

## 📅 第 1 週：K8s 基礎概念 + 單機體驗
🎯 目標：理解 K8s 的組件與資源，並能在單機跑起來  

### 學習資源
- 官方文件（中文）：[Kubernetes 基本概念](https://kubernetes.io/zh-cn/docs/concepts/)  
- 影片：  
  - [Kubernetes Tutorial for Beginners (TechWorld with Nana)](https://www.youtube.com/watch?v=X48VuDVv0do)  
  - [Amos 老師 Kubernetes 新手村 (YouTube)](https://www.youtube.com/playlist?list=PLj6h78yzYM2PZf9eA7bhWnIh_mK1vyOfU)  
- 工具：  
  - [Minikube](https://minikube.sigs.k8s.io/docs/start/)  
  - [k3s 輕量版 Kubernetes](https://rancher.com/docs/k3s/latest/en/)  

### 練習任務
- 在 Ubuntu VM 安裝 minikube 或 k3s  
- 建立 nginx Pod 與 Deployment  
- 使用 Service 對外暴露 nginx  
- 體驗滾動更新（修改 replicas 數量）

---

## 📅 第 2 週：多機叢集 (kubeadm)
🎯 目標：建立一個 3 節點叢集（1 Master + 2 Worker）  

### 學習資源
- 官方文件：[使用 kubeadm 建立叢集](https://kubernetes.io/zh-cn/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)  
- 教學文章：  
  - [DigitalOcean - Create a Kubernetes Cluster Using kubeadm](https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-20-04)  
  - [LinuxTechi - Install Kubernetes Cluster on Ubuntu 22.04](https://www.linuxtechi.com/install-kubernetes-cluster-on-ubuntu-22-04/)  
- Pod 網路：  
  - [Flannel](https://github.com/flannel-io/flannel#deploying-flannel-manually)  
  - [Calico](https://docs.tigera.io/calico/latest/getting-started/kubernetes/quickstart)  

### 練習任務
- 在三台 Ubuntu VM 上安裝 kubeadm + Docker  
- 初始化 Master 節點 (`kubeadm init`)  
- Worker 節點使用 `kubeadm join` 加入叢集  
- 安裝 CNI（Flannel/Calico）  
- 驗證 `kubectl get nodes` 三節點正常  

---

## 📅 第 3 週：應用部署 & 資源管理
🎯 目標：能在叢集上跑一個多層應用（前端 + 後端 + DB）  

### 學習資源
- [ConfigMap](https://kubernetes.io/zh-cn/docs/concepts/configuration/configmap/)  
- [Secret](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/)  
- 範例應用：  
  - [WordPress + MySQL (官方範例)](https://kubernetes.io/zh-cn/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/)  
  - [Guestbook 應用](https://kubernetes.io/zh-cn/docs/tutorials/stateless-application/guestbook/)  
- [ingress-nginx Controller](https://kubernetes.github.io/ingress-nginx/deploy/)  

### 練習任務
- 部署一個前端 + 後端 + DB 應用  
- 使用 PVC 保存 DB 資料  
- 使用 ConfigMap / Secret 管理設定與密碼  
- 安裝 Ingress Controller，模擬多網站入口  

---

## 📅 第 4 週：高可用 / 自動擴展 / 監控
🎯 目標：模擬企業真實場景，體驗 HA 與自動擴展  

### 學習資源
- [節點管理](https://kubernetes.io/zh-cn/docs/concepts/architecture/nodes/)  
- [Horizontal Pod Autoscaler (HPA)](https://kubernetes.io/zh-cn/docs/tasks/run-application/horizontal-pod-autoscale/)  
- [metrics-server](https://github.com/kubernetes-sigs/metrics-server)  
- [Prometheus + Grafana 監控](https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/)  

### 練習任務
- 模擬節點維護：`kubectl drain nodeX`  
- 測試 Pod 自動遷移到健康節點  
- 設定 HPA，壓測並觀察 Pod 自動擴增  
- 部署 metrics-server，查看 Pod 資源使用  
- 挑戰：安裝 Prometheus + Grafana  

---

## 📘 推薦書籍（進階）
- 《Kubernetes in Action》  
- 《The Kubernetes Book》  
- 《Production Kubernetes》  

---

✅ **最後成果**  
- 建立一個 **3 節點 K8s 叢集**  
- 能部署多層應用並對外提供服務  
- 能操作滾動更新、Pod 調度、故障模擬  
- 熟悉 HPA、自動擴展、基本監控  
