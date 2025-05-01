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

## Implementation Notes

*(Note: Detailed operational procedures might be better suited for runbooks or separate documentation.)*

### Generating Encrypted Secrets

1.  Create a standard Kubernetes secret YAML manifest.
    ```bash
    # Example
    kubectl create secret generic test-secret \
      --from-literal=user=admin \
      --from-literal=password=adminpassword \
      --dry-run=client \
      -o yaml > secret.yaml
    ```
2.  Encrypt the `data` or `stringData` fields using the `sops` CLI with the appropriate Age public key:
    ```bash
    # Add AGE_PUBLIC_KEY to environment variables
    export AGE_PUBLIC_KEY=age1...
    # Encrypt YAML manifest
    sops --encrypt --age $AGE_PUBLIC_KEY --encrypted-regex '^(data|stringData)$' --in-place secret.yaml
    ```
3.  Commit the encrypted `secret.yaml` file to the Git repository.

### Cluster Cold Start / Disaster Recovery

In a cold start scenario where the cluster state is lost, the Age private key must be reintroduced to the cluster for FluxCD to decrypt secrets.

1.  Retrieve the Age private key from its secure storage (e.g., 1Password entry: [Link](https://start.1password.com/open/i?a=TG2G6YLPWFCLZO3XBOCX5EM57A&v=h2b7pqgkqmgrybvdvjxnkmtm2y&i=sset5ijhy4hgjwkje2qmiouq2y&h=my.1password.com)).
2.  Create the `sops-age` Kubernetes secret in the `flux-system` namespace containing the private key:
    ```bash
    # ssh into the new node00
    ssh node00

    # copy contents of 1password entry
    nano age.agekey

    # Create sops-age secret with age.agekey
    cat age.agekey |
    kubectl create secret generic sops-age \
    --namespace=flux-system \
    --from-file=age.agekey=/dev/stdin
    ```
FluxCD will then use this key to decrypt SOPS-encrypted secrets during synchronization.