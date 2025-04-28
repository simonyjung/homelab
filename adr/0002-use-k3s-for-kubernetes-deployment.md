# ADR-0002: Use K3s for Kubernetes Deployment

**Status:** Accepted  
**Date:** 2025-04-27

## Context

For the homelab Kubernetes deployment, a balance between ease of maintenance, debugging capability, and learning opportunities was necessary. Given the homelab environment, priorities included straightforward setup, ongoing system access via SSH, and hands-on exposure to Kubernetes management.

## Decision

Deploy Kubernetes clusters using K3s.

## Rationale

- **Ease of Maintenance**: K3s offers a simplified, lightweight Kubernetes distribution that is easier to set up and maintain compared to kubeadm or Talos.
- **SSH Access**: Maintaining standard Linux distributions for the nodes allows SSH access, enabling easier debugging, system inspection, and a broader learning experience.
- **Learning Opportunity**: Working within the full Linux environment provides deeper system understanding beneficial for homelab growth.

**Alternatives Considered:**
- **Talos**: Offers excellent security and immutability, ideal for production environments. However, lack of traditional SSH access and more rigid configuration made it less suitable for a learning-focused homelab.
- **Kubeadm**: Standard Kubernetes bootstrapping tool, widely used in certifications (e.g., CKA/CKAD). Rejected due to higher operational overhead and manual maintenance requirements, which could burden homelab management long-term.

## Consequences

- **Positive**: Faster cluster setup, easier recovery and troubleshooting, better alignment with homelab goals of flexibility and learning.
- **Trade-off**: Less security hardening compared to Talos.
- **Future Considerations**: As operational expertise grows, reevaluate if transitioning to a production-grade setup (e.g., Talos or full kubeadm) becomes desirable.
