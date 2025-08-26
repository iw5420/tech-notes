---
title: 三年學習 Roadmap（Cloud + MLOps + Java 架構師）
tags:
  - Cloud
  - MLOps
  - Java
  - Architect
  - Plan
---

# 🚀 三年學習計畫：Cloud + MLOps + Java 架構師

## 基本設定
- 年齡：37 歲
- 背景：6 年 Java 資深後端工程師
- 目標：3 年內能應徵 Cloud / Backend Architect（兜底）與 MLOps / AI 平台工程師（進攻）
- 主線語言：Java（後端架構）、Python（MLOps/Infra）

---

# 📅 半年為單位的計畫

## 🔹 0–6 個月：Cloud & MLOps 入門
- **Cloud / 架構師**
  - 系統設計題基礎（CAP、負載均衡、快取、事件驅動架構）
  - Java + Spring Boot + Redis + Kafka → 微服務 Demo
  - 部署 Java 專案到 AWS/GCP (Cloud Run / ECS)
- **MLOps**
  - Python：FastAPI + NumPy + Pandas
  - Docker 化 ML 模型（Whisper / BERT） → FastAPI → 部署到 K8s（Minikube/EKS）
- **證照**
  - AWS Solutions Architect – Associate
- **作品集**
  - **Side Project 1**：語音轉文字 API Demo（FastAPI + Docker + K8s）

---

## 🔹 6–12 個月：K8s + Observability + MLFlow
- **Cloud / 架構師**
  - Kubernetes 進階：GKE/EKS + Helm + HPA（自動水平擴展）
  - Observability：Prometheus + Grafana + Jaeger
  - 專案：高併發聊天室（Java + Kafka + Redis Cluster + K8s）
- **MLOps**
  - MLFlow 入門：Model Registry + Tracking
  - CI/CD：GitHub Actions / GitLab CI 自動化部署模型
- **證照**
  - Certified Kubernetes Administrator (CKA)
- **作品集**
  - **Side Project 2**：高併發聊天室（Kafka + Redis Cluster + K8s） + MLFlow 模型追蹤

---

## 🔹 12–18 個月：Terraform + MLOps Pipeline
- **Cloud / 架構師**
  - Terraform (IaC) → 多環境管理
  - API Gateway + gRPC → 微服務整合
- **MLOps**
  - Kubeflow Pipeline 入門（資料 → 訓練 → 部署）
  - 模型部署加速：ONNX Runtime, TensorRT
- **證照**
  - AWS Architect – Professional（或 GCP Architect Associate）
- **作品集**
  - **Side Project 3**：MLOps Pipeline Demo（自動 retrain → Canary 部署 → Drift 監控）

---

## 🔹 18–24 個月：進階 Observability + Triton
- **Cloud / 架構師**
  - Service Mesh (Istio / Linkerd) → 微服務治理
  - Multi-tenant SaaS 架構設計
- **MLOps**
  - Triton Inference Server（多模型 Serving）
  - 模型監控：Latency、Data Drift 檢測
- **證照**
  - GCP Professional ML Engineer / AWS Machine Learning Specialty
- **作品集**
  - **Side Project 4**：高併發模型 Serving 平台（支援 1000+ QPS, P95 < 1s）

---

## 🔹 24–30 個月：分散式訓練 + 領導專案
- **Cloud / 架構師**
  - 領導一個模擬專案（金融/電商系統重構）
  - 使用 DDD (Domain Driven Design) 切分微服務
- **MLOps**
  - 分散式訓練：Ray / Horovod / PyTorch Distributed
  - 模型 Checkpoint、GPU 調度、資源隔離
- **證照**
  - AWS Solutions Architect – Professional / GCP Cloud Architect
- **作品集**
  - **Side Project 5**：分散式訓練 Demo（多 GPU 模型訓練 + 自動恢復）

---

## 🔹 30–36 個月：AI SaaS 平台 + 技術品牌
- **Cloud / 架構師**
  - 完成 Terraform + K8s + Service Mesh 全鏈路架構
  - 系統設計文件輸出（英文）
  - 模擬帶 5–10 人團隊
- **MLOps**
  - AI SaaS Mini Platform → 登入 / API Key / 限流 / 計費
  - 全面監控：延遲、吞吐量、漂移報告
- **證照**
  - GCP Professional Cloud Architect（或 AWS 同級別）
- **作品集**
  - **Side Project 6**：AI SaaS Mini Platform（多租戶 + API 金流 + 壓測報告）

- **技術品牌輸出（新增）**
  - 在 GitHub + Blog 發布完整架構文章
  - 在 Medium / LinkedIn 發布英文技術文章（1–2 篇）
  - 若時間允許，可釋出一個小型開源工具（如 MLFlow Helper 或 K8s Dashboard）

---

## 🔹 37 個月後：面試準備期（新增）
- **系統設計題準備**
  - Grokking the System Design Interview
  - 模擬面試（Mock Interview, Pramp）
- **演算法題基礎**
  - LeetCode 50–80 題（Python 為主，避免 coding round 掛掉）
- **模擬面試 / 求職**
  - 投遞履歷，針對 Cloud Architect / MLOps Engineer 職缺進行 mock interview
  - 重點練習行為面試（Behavioral Questions）

---

# 🎯 三年後成果
- **Cloud 架構師能力**：K8s, Terraform, Observability, Service Mesh, DDD, 雲端證照（AWS/GCP）
- **MLOps 能力**：MLFlow, Kubeflow, Triton, ONNX, TensorRT, Ray/Horovod
- **作品集**：語音 API Demo → MLOps Pipeline → 高併發 Serving → 分散式訓練 → AI SaaS 平台
- **技術品牌**：Blog、GitHub、英文文章、開源工具
- **定位**：可同時應徵 Cloud/Backend Architect（穩定兜底）與 MLOps / AI 平台工程師（高薪進攻）
- **面試準備**：系統設計 + LeetCode 基礎題，確保轉職成功
