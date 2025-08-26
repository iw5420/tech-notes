---
title: AI 平台工程師技能圖譜
tags:
  - AI Platform
  - MLOps
  - Cloud
  - Java
  - Python
---

# 🗂 AI 平台工程師技能圖譜

## 1️⃣ 模型生命週期管理（Model Lifecycle）
**目標**：讓模型能從訓練 → 部署 → 更新 → 監控，自動化流暢跑起來。  
- **核心技能**  
  - **MLOps 工具**：Kubeflow, MLFlow, Airflow, Argo Workflows  
  - **模型格式**：ONNX, TorchScript, SavedModel  
  - **模型部署**：TensorRT, Triton Inference Server, vLLM  
- **主要語言**：  
  - **Python** → Pipeline 寫法、MLFlow API、訓練腳本  
  - Java → 很少用於這一層（除非 API 化模型）

---

## 2️⃣ 模型部署與推理（Model Serving & Inference）
**目標**：讓 AI 模型能在高併發環境下被調用，延遲低、成本可控。  
- **核心技能**  
  - **容器化**：Docker, Kubernetes (EKS/GKE/AKS)  
  - **Serving**：Triton, TorchServe, TensorFlow Serving  
  - **優化**：Batching, Quantization, Distillation  
  - **監控**：Prometheus + Grafana, Logging, Tracing  
- **主要語言**：  
  - **Python** → 部署工具鏈（Triton config, Serving SDK）  
  - **Java** → 把模型包成 REST/gRPC API（Spring Boot + gRPC）  

---

## 3️⃣ 平台基礎設施（Platform Infrastructure）
**目標**：支撐 AI 模型服務的整個雲端架構。  
- **核心技能**  
  - **Kubernetes**：Deployment, Autoscaling, Helm, Operators  
  - **Infra as Code**：Terraform, Ansible  
  - **Service Mesh**：Istio / Linkerd（流量治理）  
  - **Cloud Service**：AWS SageMaker, GCP Vertex AI  
- **主要語言**：  
  - **Go** → K8s operator & cloud-native 工具常用  
  - **Java** → 高併發後端服務（API Gateway、金流、認證、SaaS 架構）  

---

## 4️⃣ 資料流與監控（Data & Monitoring）
**目標**：保證模型輸入輸出數據穩定，及時發現漂移。  
- **核心技能**  
  - **資料流處理**：Kafka, Flink, Spark Streaming（僅需概念，非主線）  
  - **特徵管理**：Feature Store (Feast, Tecton)  
  - **監控工具**：EvidentlyAI, Alibi Detect（模型漂移偵測）  
- **主要語言**：  
  - **Python** → 特徵工程、漂移檢測腳本  
  - **Java** → 與既有後端系統整合（例：資料 pipeline → 模型 API）  

---

## 5️⃣ 商業化與產品化（Productization）
**目標**：讓模型不只是跑起來，而是能「成為產品」：安全、計費、可多租戶。  
- **核心技能**  
  - **API Gateway**：Kong, Envoy, Spring Cloud Gateway  
  - **認證 / 授權**：OAuth2, Keycloak  
  - **計費 / 限流**：Rate Limiting, API Key, Stripe Integration  
  - **多租戶 SaaS 架構**  
- **主要語言**：  
  - **Java** → SaaS 架構主力（Spring Boot + Microservices）  
  - Python → 僅負責模型部分的 API 包裝  

---

# 📌 總結語言分工
- **Python = 模型為核心的工具鏈**  
  - 訓練、Pipeline、MLFlow、Kubeflow、Serving Config、漂移檢測  
- **Java = 平台產品化 / 高併發服務**  
  - API Gateway、SaaS 架構、金流/計費、認證/授權、與既有後端整合  
- **Go = 系統底層 / Cloud Native 工具**  
  - K8s operator、Service Mesh、Infra tooling  

---

# 🎯 對你的建議
- **主力補 Python**：因為沒有它，你沒辦法碰 MLOps 工具鏈。  
- **保留 Java 優勢**：不要丟，你能在「AI 平台產品化」這層做出差異化。  
- **Go 不急**：只要能懂 K8s / Service Mesh，會用就好。  
