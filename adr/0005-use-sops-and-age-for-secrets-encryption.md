# ADR-0005: Use SOPS and Age for Secrets Encryption

**Status:** Accepted  
**Date:** 2025-04-30

## Context

Managing sensitive data such as Kubernetes secrets in a GitOps workflow requires a secure method for storing secrets in version control. Since FluxCD was chosen for GitOps (see [ADR-0004](./0004-use-flux-cd-for-gitops.md)), it is important to integrate a secrets management solution compatible with Flux. SOPS (Secrets OPerationS) is natively supported by FluxCD, making it a strong candidate for encrypting Kubernetes secrets stored in a public Git repository.

## Decision

Use Mozilla SOPS with Age encryption to secure Kubernetes secrets. The Age key is securely stored in 1Password and backed up offline for cold start scenarios.

## Rationale

- **FluxCD Native Support**: SOPS is directly supported by FluxCD, enabling seamless decryption of secrets during GitOps-based deployments.
- **Secure and Modern Encryption**: Age is a modern encryption tool with a simple and secure design, preferred over older GPG-based methods.
- **Public Git Storage**: Encryption allows safe storage of secrets in a public repository.

**Alternatives Considered:**
- **GPG with SOPS**: While more mature, GPG is harder to manage and error-prone, especially in team or recovery scenarios.

## Consequences

- **Positive**:
  - Enables fully declarative, Git-based secret management.
  - Maintains a secure recovery path through 1Password key storage.
  - Minimal operational overhead due to native FluxCD support.

- **Trade-off**:
  - Requires all operators to be familiar with the SOPS workflow.
  - Manual effort needed to update encrypted secrets (e.g., via `sops` CLI).

## Implementation Notes:

### How to generate an encrypted secret yaml
```
# ssh into master node
ssh node00

# Create base64 encoded secret yaml
kubectl create secret generic test-secret \
--from-literal=user=admin \
--from-literal=password=adminpassword \
--dry-run=client \
-o yaml > test-secret.yaml

# Encrypt secret
export AGE_PUBLIC=age19ktsh93hekpjk5cwjex3njgl9fyxtsfd3lz49khjwxtnlk8ucc6qeqad4a
sops --age=$AGE_PUBLIC \
--encrypt --encrypted-regex '^(data|stringData)$' --in-place test-secret.yaml

# View secret
cat test-secret.yaml

# Move to repository, commit.
```

### Cold Start

If cluster has to be restarted in a cold start scenario, it is neccesary to recreate the `sops-age` secret manually as it is not included in this repository. 

The contents of the age.agekey file can be found in this [1Password Link](https://start.1password.com/open/i?a=TG2G6YLPWFCLZO3XBOCX5EM57A&v=h2b7pqgkqmgrybvdvjxnkmtm2y&i=sset5ijhy4hgjwkje2qmiouq2y&h=my.1password.com)

```
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