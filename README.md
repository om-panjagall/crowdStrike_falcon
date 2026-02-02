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
```
This creates:

falcon-operator Deployment in the falcon-system namespace

CRDs for FalconNodeSensor and FalconContainerSensor

### 2. Create Falcon Secrets
Store Falcon API credentials securely in Kubernetes:
```bash
kubectl create secret generic falcon-secrets \
   -n falcon-system \
   --from-literal=falcon-client-id="<value>" \
   --from-literal=falcon-client-secret="<value>" \
   --from-literal=falcon-cid="<value>"
```
### 3. Deploy Falcon Node Sensor
Create a file named falcon-node-sensor.yaml:

```bash
apiVersion: falcon.crowdstrike.com/v1alpha1
kind: FalconNodeSensor
metadata:
  name: falcon-node-sensor
spec:
  installNamespace: falcon-system
  falconSecret: 
    enabled: true
    secretName: falcon-secrets
    namespace: falcon-system
  falcon_api:
    cloud_region: "eu-1"
```
Apply it:
```bash
kubectl apply -f falcon-node-sensor.yaml
```
👉 This triggers the operator to create a DaemonSet named falcon-node-sensor in falcon-system.

### 4. Handle Tainted Nodes (Optional)
If your cluster has taints (e.g., dedicated prod nodes), add tolerations:
```bash
kubectl edit falconnodesensor falcon-node-sensor
Example toleration:
```bash
- effect: NoSchedule
  key: nodegroup
  operator: Equal
  value: prod
```
### 5. Deploy Falcon Container Sensor
Create a file named falcon-container-sensor.yaml:

```bash
apiVersion: falcon.crowdstrike.com/v1alpha1
kind: FalconContainerSensor
metadata:
  name: falcon-container-sensor
spec:
  installNamespace: falcon-system
  falconSecret:
    enabled: true
    secretName: falcon-secrets
    namespace: falcon-system
  falcon_api:
    cloud_region: "eu-1"
```
Apply it:
```bash
kubectl get ds -n falcon-system
```
Expected output:

falcon-node-sensor
falcon-container-sensor
Both should have pods running across all nodes.

✅ Outcome
With both sensors deployed:
Falcon Node Sensor → Protects the host runtime.
Falcon Container Sensor → Protects workloads inside containers.
Together, they deliver end-to-end runtime security for Kubernetes clusters.

📌 Purpose of CrowdStrike Falcon
CrowdStrike Falcon ensures:
Real-time detection of threats
Visibility across nodes and containers
Protection against advanced attacks
Seamless integration into cloud-native environments

#Kubernetes #CloudSecurity #CrowdStrike #DevSecOps #RuntimeSecurity #Containers
