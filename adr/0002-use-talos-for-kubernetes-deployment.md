# ADR-0002: Use Talos and Omni for Kubernetes Deployment

**Status:** Accepted
**Date:** 2025-04-27 (Decision revised: 2025-05-11)

## Context

The goal for the homelab Kubernetes setup is to achieve a highly secure, consistent, and modern infrastructure, emphasizing API-driven management and operational simplicity. Key requirements include a robust and immutable operating system for Kubernetes nodes, streamlined cluster lifecycle management, and a user-friendly interface for day-to-day operations and observability, provided by Omni.

## Decision Drivers

*   **Security:** Prioritize a minimal, hardened, and immutable OS to reduce attack surface and enhance cluster security.
*   **Consistency & Predictability:** Need a distribution that ensures identical node configurations and predictable behavior across the cluster.
*   **API-Driven Management:** Embrace modern infrastructure-as-code principles with comprehensive API control over the cluster.
*   **Operational Simplicity:** Seek a solution that simplifies cluster creation, upgrades, and maintenance, ideally with a dedicated management platform like Omni.
*   **Learning Focus:** Gain experience with cutting-edge, cloud-native technologies and immutable infrastructure patterns.
*   **Efficiency:** Prefer solutions with sensible defaults and streamlined workflows to reduce configuration overhead.

## Decision

We will use Talos as the Kubernetes distribution, managed with Omni, for the homelab cluster.

## Rationale

Talos and Omni were chosen because they best meet the decision drivers for a modern, secure, and manageable homelab Kubernetes environment:

*   **Enhanced Security & Immutability:** Talos provides a minimal, hardened, and immutable Linux distribution designed specifically for Kubernetes. This significantly reduces the attack surface and ensures a consistent, secure baseline for all nodes. The entire OS is managed via API, eliminating direct SSH access and traditional package management, which enhances security.
*   **API-Centric & Declarative Configuration:** Talos is entirely API-driven, promoting infrastructure-as-code practices. Cluster configuration is declarative, ensuring predictability and reproducibility. This aligns with modern cloud-native operational models.
*   **Simplified Cluster Lifecycle Management:** Talos is designed for streamlined cluster upgrades and maintenance. Its immutable nature means upgrades are atomic and rollbacks are cleaner.
*   **User-Friendly Management with Omni:** Sidero Omni provides a web-based UI and CLI tools for managing Talos clusters. Omni simplifies tasks such as cluster creation, node bootstrapping, viewing logs, accessing a Kubernetes dashboard, and managing cluster configurations, making Talos more accessible and easier to operate day-to-day.
*   **Learning Modern Practices:** Adopting Talos and Omni provides hands-on experience with immutable infrastructure, API-driven systems, and modern Kubernetes management paradigms, which are valuable skills in the cloud-native ecosystem.
*   **Efficiency and Reduced Overhead:** The combination of Talos's purpose-built design and Omni's management capabilities leads to a lean and efficient Kubernetes platform with reduced operational overhead once the initial learning curve is overcome.

**Alternatives Considered:**

*   **K3s:** A lightweight Kubernetes distribution that offers a 1 step install and management. While it offers simplicity and allows SSH access for direct node interaction, it runs on general-purpose Linux distributions, which do not offer the same level of immutability and security hardening as Talos. The desire for a more robust, enterprise-level security, and immutable system led to preferring Talos.
*   **Kubeadm:** The standard tool for bootstrapping Kubernetes clusters, offering maximum flexibility. Rejected due to its higher operational complexity and manual maintenance demands.

## Consequences

*   **Positive:**
    *   Significantly improved security posture due to Talos's immutable and minimal OS.
    *   Consistent and predictable cluster state across all nodes.
    *   Simplified and more reliable cluster upgrades and maintenance.
    *   Enhanced learning of modern, API-driven, and immutable infrastructure principles.
    *   User-friendly cluster management and observability through Omni.
*   **Trade-offs:**
    *   No traditional SSH access to nodes. Troubleshooting and interaction occur via `talosctl` and the Talos API, or through Omni. This requires a shift in operational practices.
    *   Steeper initial learning curve for those accustomed to traditional Linux server management and SSH.
    *   Reliance on the Talos API and Omni for most interactions, which, while powerful, is a different paradigm than direct OS access.
