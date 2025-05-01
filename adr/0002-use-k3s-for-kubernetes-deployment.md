# ADR-0002: Use K3s for Kubernetes Deployment

**Status:** Accepted  
**Date:** 2025-04-27

## Context

The goal for the homelab Kubernetes setup is to balance ease of management, robust debugging capabilities, and hands-on learning. Key requirements include a straightforward initial setup, the ability to access nodes via SSH for troubleshooting and exploration, and direct exposure to Kubernetes administration tasks.

## Decision Drivers

*   **Simplicity:** Need a distribution that minimizes setup and maintenance overhead for a homelab environment.
*   **Accessibility:** Require SSH access to nodes for direct system interaction, debugging, and learning.
*   **Learning Focus:** Prioritize gaining practical experience with Kubernetes and underlying Linux systems.
*   **Efficiency:** Prefer solutions with useful defaults to reduce initial configuration effort.

## Decision

We will use K3s as the Kubernetes distribution for the homelab cluster.

## Rationale

K3s was chosen because it best meets the decision drivers compared to the alternatives:

*   **Simplified Management:** K3s is a lightweight distribution designed for ease of installation and operation, significantly reducing the maintenance burden compared to `kubeadm`. This aligns with the need for simplicity in a homelab.
*   **Full System Access:** Unlike Talos, which uses a minimal, locked-down OS, K3s runs on standard Linux distributions. This preserves SSH access, crucial for direct node inspection, troubleshooting, and deeper learning about both Kubernetes and Linux internals.
*   **Integrated Ingress:** K3s includes the Traefik Ingress Controller by default. Since Traefik is already used elsewhere in the homelab, this simplifies the stack and reduces initial setup time.
*   **Balance:** K3s strikes a good balance between the operational simplicity desired for a homelab and the learning opportunities afforded by accessible underlying systems, unlike the more complex `kubeadm` or the restricted Talos environment.

**Alternatives Considered:**

*   **Talos:** Provides superior security and immutability through a minimal, API-managed OS. However, its lack of traditional SSH access and more rigid configuration model conflict with the requirements for accessibility and hands-on learning in this homelab context.
*   **Kubeadm:** The standard tool for bootstrapping Kubernetes clusters, offering maximum flexibility and alignment with certifications (e.g., CKA/CKAD). Rejected due to its higher operational complexity and manual maintenance demands, which are less suitable for a resource-constrained homelab focused on ease of use.

## Consequences

*   **Positive:**
    *   Faster cluster deployment and easier management.
    *   Simplified troubleshooting and recovery due to familiar SSH access.
    *   Better alignment with homelab goals of flexibility, learning, and practical experience.
*   **Trade-offs:**
    *   K3s running on a general-purpose OS offers less inherent security hardening compared to the immutable, minimal OS approach of Talos. This is an acceptable trade-off for the homelab's learning and accessibility goals.
*   **Future Considerations:**
    *   As operational experience increases, we may re-evaluate transitioning to a more production-oriented distribution like Talos or standard `kubeadm` if homelab priorities shift towards security or mimicking enterprise environments more closely.
