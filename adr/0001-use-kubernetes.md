# ADR-001: Use Kubernetes for Container Orchestration

**Status:** Accepted  
**Date:** 2025-04-27

## Context

Managing multiple containers and services manually became cumbersome and error-prone. Availablity and ease of management became critical requirements.

## Decision

Adopt Kubernetes as the primary container orchestration solution for managing home self-hosted deployments.

## Rationale

- Mature, widely adopted, and actively maintained.
- Strong community and ecosystem.
- Supports automated scaling and resource management.
- Facilitate in better understanding of AWS ECS, which is used professionally.

Alternatives such as Docker Swarm were considered but rejected due to lower feature richness and community support. Previous homelab configuration involved management of docker containers using Portainer across Virtual Machines hosted on Proxmox hosts. Portainer greatly improved ease of management, but still saw reliablity issues with key services such as Plex.

## Consequences

- Increased complexity in initial setup.
- Requires familiarity with Kubernetes concepts.
- Enables automated deployments and improved reliability.
