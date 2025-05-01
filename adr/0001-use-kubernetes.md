# ADR-0001: Use Kubernetes for Container Orchestration

**Status:** Accepted  
**Date:** 2025-04-27

## Context

The previous homelab setup involved managing Docker containers using Portainer across multiple Virtual Machines hosted on Proxmox. While Portainer simplified some aspects, this approach faced several challenges:
- **Manual Management:** Scaling services, ensuring high availability, and managing configurations across VMs remained largely manual, becoming increasingly time-consuming.
- **Reliability Issues:** Key services, such as Plex Media Server, experienced downtime, highlighting the need for a more robust and self-healing infrastructure.
- **Inconsistent Deployments:** Ensuring consistent environments and deployment processes across different services was difficult.

As the number of self-hosted services grew, improving reliability, simplifying management, and enabling automated deployments became critical requirements.

## Decision Drivers

- **Improved Reliability & Availability:** Need for automated recovery from failures and higher uptime for services.
- **Simplified & Automated Management:** Reduce manual effort for deployment, scaling, and configuration updates.
- **Scalability:** Ability to easily scale services up or down based on demand.
- **Standardization:** Adopt industry-standard practices for container orchestration.
- **Learning Opportunity:** Gain practical experience with concepts applicable to cloud-native technologies like AWS ECS.

## Considered Options

1.  **Kubernetes:** A powerful, feature-rich container orchestration platform with strong community support and a large ecosystem.
2.  **Docker Swarm:** A simpler orchestration tool integrated with Docker, but with fewer features and a smaller ecosystem compared to Kubernetes.
3.  **Status Quo (Portainer on VMs):** Continue with the existing setup, potentially adding more manual scripting or tooling.

## Decision

Adopt Kubernetes as the primary container orchestration platform for the homelab.

**Rationale:**

- **Meets Core Requirements:** Kubernetes directly addresses the key decision drivers by providing built-in features for automated deployments, scaling, self-healing (improving reliability), and configuration management.
- **Rich Ecosystem & Community:** The vast ecosystem offers readily available tools and solutions (e.g., for monitoring, logging, ingress), and the large community provides extensive documentation and support.
- **Industry Standard:** Aligns with prevalent industry practices, making the skills acquired transferable.
- **Learning Value:** While distinct from AWS ECS, managing Kubernetes provides a deep understanding of core container orchestration principles applicable to other platforms.

Docker Swarm was rejected primarily due to its more limited feature set (e.g., less sophisticated networking, storage, and extensibility options) and smaller community compared to Kubernetes, which could hinder long-term flexibility and troubleshooting. Continuing with the status quo was deemed insufficient to address the growing reliability and management challenges.

## Consequences

- **Increased Initial Complexity:** Setting up and learning Kubernetes involves a steeper learning curve compared to Docker Swarm or the previous setup. Requires understanding concepts like Pods, Services, Deployments, Ingress, etc.
- **Resource Overhead:** Kubernetes itself consumes node resources (CPU, memory).
- **Enhanced Capabilities:** Enables automated, resilient, and scalable deployment of services.
- **Improved Manageability:** Centralizes configuration and management, facilitating GitOps workflows (explored in a subsequent ADR).
- **Foundation for Future Growth:** Provides a robust platform for adding more complex applications and services.
