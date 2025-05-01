# 🏡 KC01 - homelab

## 👋 Introduction

Welcome to the publicly available documentation and configuration of KC01, Simon's kubernetes homelab cluster.
As someone that occasionally interviews Site Reliablity Engineers, I've always found myself to appreciate candidates with a nice homelab.
This is my attempt to learn and document my homelab so I can have better conversations with Site Reliability/DevOps Engineers. 

## 🏃 GitOps

This project uses FluxCD to implement GitOps—managing Kubernetes infrastructure and application configuration through this git repository. Flux continuously monitors this repository and applies changes automatically to the cluster, ensuring the declared state in Git matches the live state in Kubernetes.

See [ADR-0004](/adr/0004-use-flux-cd-for-gitops.md)

## 🔐 Secrets

This project uses SOPS (Secrets OPerationS) to securely encrypt secrets that are safe to commit to a public GitHub repository. Secrets are encrypted using age keys and stored within yaml files. The decryption process is handled automatically in CI/CD pipelines by Flux. See [ADR-0005](/adr/0005-use-sops-and-age-for-secrets-encryption.md) for example usage and cold start procedures. 

## 🖥️ Hardware

This homelab utilizes cost-effective and energy-efficient mini PCs for its compute nodes, along with a dedicated NAS for persistent storage.

### Nodes (4 Total)

The cluster consists of four mini PCs selected for their small form factor, low power consumption, and affordability on the used market. They provide the compute resources for the Kubernetes cluster.

| Make/Model             | CPU         | RAM   | Storage     | Role          |
| ---------------------- | ----------- | ----- | ----------- | ------------- |
| Dell Optiplex 3060 Mini| i5-8500T    | 24GB  | 512GB NVMe  | Control Plane |
| HP ProDesk 600 G4 Mini | i5-8500T    | 8GB   | 128GB NVMe  | Worker Node   |
| HP ProDesk 600 G4 Mini | i5-8500T    | 8GB   | 128GB NVME  | Worker Node   |
| HP ProDesk 600 G4 Mini | i5-8500T    | 8GB   | 128GB NVME  | Worker Node   |

*Further details on node selection and OS can be found in [ADR-0003](/adr/0003-node-hardware-os.md).*

### Storage

Persistent storage for applications requiring state is provided by a Network Attached Storage (NAS) device:

*   **Model:** Synology DS923+
