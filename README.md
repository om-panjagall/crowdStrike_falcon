# Kubernetes Runtime Security with CrowdStrike Falcon

## 📌 Overview
Kubernetes clusters are dynamic and distributed, making them a prime target for runtime threats.  
**CrowdStrike Falcon** provides **real-time threat detection, visibility, and protection** across both nodes and containers.  

This guide explains how to deploy the **Falcon Operator**, **Falcon Node Sensor**, and **Falcon Container Sensor** on Kubernetes for end-to-end runtime security.

---

## 🔧 Components Created
When deploying Falcon on Kubernetes, the following resources are created:

- **Falcon Operator**
  - Installs Custom Resource Definitions (CRDs) such as `FalconNodeSensor` and `FalconContainerSensor`.
  - Manages lifecycle of Falcon sensors in the cluster.

- **Falcon Node Sensor (DaemonSet)**
  - Runs on every node.
  - Provides host-level runtime visibility and protection.

- **Falcon Container Sensor (DaemonSet)**
  - Integrates with the container runtime (containerd, CRI-O, Docker).
  - Provides workload-level visibility and protection.

---

## 🚀 Deployment Steps

### 1. Install Falcon Operator
```bash
kubectl apply -f https://github.com/CrowdStrike/falcon-operator/releases/latest/download/falcon-operator.yaml
