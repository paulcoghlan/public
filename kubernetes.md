# Kubernetes

## Overview

Nodes:
Containers -> Pods -> [Optionally: ReplicaSets (n pods)] -> Deployments -> Services

- Control Plane exposes Kubernetes API Server (HTTP/gRPC, CLI - e.g. kubectl, UI)
- Nodes run Kubernets node agent (kublet) and some special per-node agents
- Nodes grouped in node pools, each having same config

## Service Mesh

- e.g. Linkerd, Istio
- Runs traffic between services
- Data plane: proxies running on nodes
- Control plane: Management process
- Handle service discovery, TLS certs, metrics

## L4 vs L7 Load Balancing

- L4 is TCP/UDP - only knows no. connections and response times
- L7 is HTTP - knows about URLs, content type, cookies