# 🏡 KC01 - homelab

## 👋 Introduction

Welcome to KC01, Simon's Kubernetes homelab cluster. This repository contains its public documentation and configuration.
As someone who occasionally interviews Site Reliability Engineers, I appreciate candidates with well-documented homelabs.
This project serves as both a learning exercise and documentation effort, aiming to facilitate better conversations with Site Reliability and DevOps Engineers.

## 🏃 GitOps

This project uses FluxCD to implement GitOps—managing Kubernetes infrastructure and application configuration through this git repository. Flux continuously monitors this repository and applies changes automatically to the cluster, ensuring the declared state in Git matches the live state in Kubernetes.

See [ADR-0004](/adr/0004-use-flux-cd-for-gitops.md)

## 🔐 Secrets

This project uses SOPS (Secrets OPerationS) to securely manage secrets committed to this public GitHub repository. Secrets are encrypted using age keys and stored within YAML files. Decryption is handled automatically by Flux during deployment. See [ADR-0005](/adr/0005-use-sops-and-age-for-secrets-encryption.md) for example usage and cold start procedures.

## 🖥️ Hardware

This homelab utilizes cost-effective and energy-efficient mini PCs for its compute nodes, along with a dedicated NAS for persistent storage.

### Nodes (4 Total)

The cluster consists of four mini PCs selected for their small form factor, low power consumption, and affordability on the used market. They provide the compute resources for the Kubernetes cluster.

| Make/Model             | CPU         | RAM   | Storage     | Role          | IP         |
| ---------------------- | ----------- | ----- | ----------- | ------------- | ---------- |
| HP ProDesk 600 G4 Mini | i5-8500T    | 8GB   | 128GB NVMe  | Control Plane | 10.0.10.10 |
| HP ProDesk 600 G4 Mini | i5-8500T    | 8GB   | 128GB NVME  | Worker Node   | 10.0.10.11 |
| HP ProDesk 600 G4 Mini | i5-8500T    | 8GB   | 128GB NVME  | Worker Node   | 10.0.10.12 |
| Dell Optiplex 3060 Mini| i5-8500T    | 24GB  | 512GB NVMe  | Worker Node   | 10.0.10.13 |

*Further details on node selection and OS can be found in [ADR-0003](/adr/0003-node-hardware-os.md).*

### Storage

Persistent storage for applications requiring state is provided by a Network Attached Storage (NAS) device:

*   **Model:** Synology DS923+
