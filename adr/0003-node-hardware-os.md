# ADR-003: Select Mini PCs and Debian 13 for Node Hardware

**Status:** Accepted  
**Date:** 2025-04-27

## Context

Hardware selection needed to balance cost, reliability, and energy efficiency for the homelab Kubernetes deployment. Additionally, an operating system with a stable and predictable release cycle was important to minimize maintenance overhead.

## Decision

Use cheap, used mini PCs as cluster nodes, running Debian 13 as the operating system.

## Rationale

- **Cost Efficiency**: Used mini PCs offer substantial savings compared to new hardware.
- **Form Factor**: Mini PCs have small footprints, ideal for homelab setups with limited space.
- **Power Consumption**: Mini PCs are more energy-efficient than traditional server hardware, reducing operating costs.
- **Failure Tolerance**: Kubernetes itself provides resilience against individual node failures, making the risk of using used hardware more acceptable.
- **Operating System Stability**: Debian 13 was selected for its long and stable release cycles, critical for minimizing disruptive upgrades.
- **Timing**: Debian 13 entered soft freeze as of April 15, 2025, and is expected to be fully released before the completion of the KC01 cluster project.

**Hardware Details:**
- Three HP ProDesk 600 G4 Mini Desktop PCs
  - CPU: Intel Core i5-8500T (6-core, 6-thread)
  - RAM: 8GB DDR4 (Upgradeable to 64GB)
  - Storage: 128GB NVMe SSD (Supports additional NVMe drive for expanded storage)
- Units were purchased used via eBay.
- Compact design, low electricity usage, and affordable cost were key selection factors.

**Potential Upgrade Paths:**
- Increasing RAM up to 64GB per node.
- Adding an additional NVMe drive per node to enable persistent storage solutions such as Longhorn.

**Likely Upgrade Paths:**
- Increase RAM to 16GB per worker node. The OS + Kubernetes services take around 2GB of memory, which leaves 6GB per worker node for workloads. 8GB DDR4 SODIMM can be purchased for ~$17 each on amazon and would provide 14GB per worker for workloads. 

**Alternatives Considered:**
- **New Hardware**: More reliable, but cost-prohibitive for the intended scale.
- **Other Distributions (e.g., Ubuntu, Fedora)**: Faster-moving distributions considered, but Debian's conservative approach and predictable support timelines better matched homelab stability goals.

## Consequences

- **Positive**: Lower upfront costs, acceptable reliability with Kubernetes redundancy, efficient power usage.
- **Trade-off**: Potential for higher node failure rates over time.
