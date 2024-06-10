# Topology Aware Kube Scheduler for SFC

Topology-Aware Service Function Chain Scheduling using GNN and DRL in kubernetes.

## Abstract

To manage various network requirements flexibly and effectively, the concepts of Network Function Virtualization (NFV) and Service Function Chain (SFC) have emerged.
However, without efficient operation, these can result in significant costs in terms of power consumption, Quality of Service (QoS), and service availability.
Previous research has attempted to overcome these challenges using various SFC scheduling technologies with Deep Reinforcement Learning (DRL) and Graph Neural Networks (GNN).
However, these studies have not clearly defined the relationships between nodes in a cluster and containers within services, limiting efficient scheduling.
In this thesis, we redefined and implemented these relationships and combined off-policy Multi-Agent DRL method.
And, we built a system to apply these concepts in a real environment with Kubernetes.


## Repos

We have 6 repos:

- [rl](https://github.com/k8s-SFC-deployment/rl)  
  RL model for Kubernetes Scheduling. We use DRL+GNN.
- [k8s](https://github.com/k8s-SFC-deployment/k8s)  
  You can find kubernetes cluster setup Information. 
- [scheduler-plugins](https://github.com/k8s-SFC-deployment/scheduler-plugins)  
  We forked from [kube/scheduler-plugins](https://github.com/kubernetes-sigs/scheduler-plugins), and add remote scoring to call RL model.
- [VNF-SCC-SFC](https://github.com/k8s-SFC-deployment/vnf-scc-sfc)  
  VNF Simulating Container Considering SFC with HTTP
- [SFC-E2E-collector](https://github.com/k8s-SFC-deployment/sfc-e2e-collector)  
  Collecting SFC e2e latency.
- [nmbn-exporter](https://github.com/k8s-SFC-deployment/nmbn-exporter)  
  Prometheus Exporter for network metrics between nodes with iptables.

## How to run

1. Set up K8S environment. You can refer [k8s](https://github.com/k8s-SFC-deployment/k8s) repo.
2. Set up kubernetes scheduler to remote scoring scheduler and learn Model with RL model ([rl](https://github.com/k8s-SFC-deployment/rl) repo).

## Architecture

### Overall Architecture

Our scheduling process has 9 steps.

1. The user sends a request to add a new pod.
2. The Kubernetes cluster requests the scheduler to find a node to place the pod.
3. The scheduler requests scores to DRL model implemented in our system. These scores will be assigned to nodes to find the optimal one for the pod placement.
4. The DRL model requests cluster metric information from the monitoring system to select the node.
5. The monitoring system collects the required information.
6. The monitoring metrics are delivered to the DRL model.
7. The DRL model selects the optimal node and provides the score to the Kubernetes scheduler.
8. The Kubernetes scheduler communicates this information to the Kubernetes cluster.
9. Finally, the Kubernetes cluster binds the pod to the selected node.

<div align="center">
  
  <img width="500px" src="https://github.com/k8s-SFC-deployment/.github/blob/main/gt_overall_architecture.png" />

</div>

### Monitoring System

We develop monitoring system with 6 modules (prometheus exporters and custom metric collector).

<div align="center">
  
  <img width="500px" src="https://github.com/k8s-SFC-deployment/.github/blob/main/gt_monitoring_overall.png" />

</div>

As a result, we make a heterogeneous graph information.
1. node : cpu usage, memory usage, total receive bytes, total transmit bytes
2. pod : cpu usage, memory usage, total receive bytes, total transmit bytes
3. service : cpu usage, memory usage, total receive bytes, total transmit bytes
4. node-to-node : receive bytes, transmit bytes, ping latency
5. service-to-service : http request bytes, http response bytes, http request duration
6. node-to-pod : if pod is located in node, then exist.
7. pod-to-service : if pod is belong to service, then exist.

<div align="center">
  
  <img width="500px" src="https://github.com/k8s-SFC-deployment/.github/blob/main/gt_state.png" />

</div>

### DRL Model

To embed graph information, we use GAT(GNN) model. And, to optimize multi-objective problem (power consumption, QoS-latency, service availability), we use TD3, SAC (Off-policy DRL).

<div align="center">

  <img width="500px" src="https://github.com/k8s-SFC-deployment/.github/blob/main/gt_rl_model_multiple_agent.png" />
  
</div>
