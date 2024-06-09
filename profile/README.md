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
