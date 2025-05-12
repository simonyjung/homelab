# ADR-0005: Use SOPS and Age for Secrets Encryption

**Status:** Accepted
**Date:** 2025-04-30

## Context

Adopting a GitOps workflow with FluxCD (see [ADR-0004](./0004-use-flux-cd-for-gitops.md)) necessitates a secure method for managing sensitive data, such as Kubernetes Secrets, directly within the Git repository. Storing secrets in version control requires robust encryption to prevent exposure, especially if the repository is public. The chosen solution must integrate smoothly with FluxCD for automated decryption during deployment.

## Decision Drivers

*   **Secure Git Storage:** Need a mechanism to safely store encrypted secrets alongside configuration in Git.
*   **FluxCD Compatibility:** The solution must be natively supported by FluxCD to ensure seamless decryption during GitOps synchronization. SOPS (Secrets OPerationS) meets this requirement.
*   **Modern and Simple Encryption:** Preference for a modern, secure, and user-friendly encryption tool over potentially complex legacy options. Age offers simplicity and strong security guarantees.
*   **Declarative Management:** Align with the GitOps principle of managing all configurations, including secrets, declaratively through Git.

## Decision

We will use Mozilla SOPS integrated with Age encryption to manage Kubernetes secrets stored within the project's Git repository.

The master Age private key, required for decryption, will be securely stored (e.g., in a password manager like 1Password) and backed up offline to facilitate disaster recovery and cluster cold starts.

## Alternatives Considered

*   **GPG with SOPS:** While SOPS also supports GPG, Age was chosen over it. GPG is generally considered more complex to manage (key generation, distribution, revocation), potentially increasing operational overhead and the risk of errors, especially in team environments or during recovery scenarios. Age provides comparable security with a simpler key management model.
*   **External Secrets Management (e.g., HashiCorp Vault, External Secrets Operator):** These solutions decouple secrets from Git but introduce additional infrastructure components to manage and maintain. Using SOPS keeps the secrets management self-contained within the GitOps tooling.

## Consequences

*   **Positive:**
    *   Enables fully declarative, Git-based management of Kubernetes secrets, aligning with the overall GitOps strategy.
    *   Provides strong encryption for secrets stored in the repository.
    *   Leverages FluxCD's native SOPS support for seamless decryption during deployment with minimal operational overhead.
    *   Establishes a clear and secure recovery path for secrets via the managed Age private key.

*   **Negative / Trade-offs:**
    *   **Learning Curve:** Operators require familiarity with the SOPS CLI and the workflow for encrypting, editing, and committing secrets.
    *   **Manual Encryption Step:** Creating or updating secrets involves a manual step using the `sops` CLI before committing changes to Git.
    *   **Key Management Responsibility:** Secure management (storage, backup, access control) of the Age private key is critical. Loss or compromise of the key would prevent decryption of secrets.
    *   **Potential for Commit Noise:** Binary diffs of encrypted files can make tracking changes to secret *values* less transparent in Git history compared to plaintext.
