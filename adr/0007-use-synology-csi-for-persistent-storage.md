# ADR-0007: Use Synology CSI for Persistent Storage

*   **Status:** Accepted
*   **Date:** 2025-05-12

## Context

The Kubernetes cluster requires a persistent storage solution to support stateful applications. The homelab utilizes a Synology DS923+ NAS, which offers iSCSI capabilities. We need a way for Kubernetes to dynamically provision and manage storage volumes on this NAS.

Several options exist for Kubernetes storage, including NFS, hostPath volumes, and various CSI (Container Storage Interface) drivers. Given the existing Synology hardware, leveraging its native capabilities via a dedicated CSI driver is desirable for better integration and potentially performance.

The [Synology CSI driver](https://github.com/SynologyOpenSource/synology-csi) allows Kubernetes to interact directly with the Synology DSM (DiskStation Manager) API and iSCSI services to manage storage volumes (LUNs).

## Decision

We will use the Synology CSI driver to provide persistent storage for the Kubernetes cluster. This involves:

1.  Deploying the Synology CSI controller and node plugins to the cluster.
2.  Configuring the driver to connect to the Synology DS923+ NAS using dedicated credentials stored securely (e.g., using SOPS as per [ADR-0005](/adr/0005-use-sops-and-age-for-secrets-encryption.md)).
3.  Defining `StorageClass` resources that specify parameters like filesystem type (`ext4`), reclaim policy (`Retain` or `Delete`), and the target Synology volume (`/volume1`).
4.  Applications requiring persistent storage will use `PersistentVolumeClaim` (PVC) resources referencing these `StorageClass`es.

This approach leverages the existing NAS hardware directly and integrates with Kubernetes storage primitives (PVCs, PVs, StorageClasses).

## Consequences

**Pros:**

*   **Dynamic Provisioning:** Kubernetes can automatically create and attach iSCSI LUNs as needed by applications.
*   **Standard Kubernetes Workflow:** Uses standard `StorageClass` and `PersistentVolumeClaim` objects.
*   **Features:** Supports features like volume expansion and potentially snapshots (if configured).

**Cons:**

*   **Dependency:** Introduces a dependency on the Synology CSI driver software and its maintenance.
*   **Single Point of Failure:** The NAS itself remains a single point of failure for persistent data, although RAID configurations on the NAS mitigate disk failure risks.
*   **Drive Speed:** Synology NAS uses spinning HDDs so IOPS will be limited compared to local storage.

**Alternatives Considered:**

*   **NFS:** Using an NFS server (either on the Synology or elsewhere). Simpler setup but potentially lower performance and fewer features compared to block storage via iSCSI for certain workloads.
*   **Longhorn/Ceph/etc.:** Deploying a software-defined storage solution within Kubernetes. More complex, requires dedicated resources on worker nodes, and has less parallels to cloud kubernetes configurations.
*   **HostPath:** Using local node storage. Not suitable for multi-node clusters where pods can be rescheduled, and doesn't provide shared persistent storage.