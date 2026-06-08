# AKS billing model

This document summarizes a competitive pricing model for hosting customer applications on a shared Azure Kubernetes Service (AKS) platform, compared with Azure App Service Premium and Azure Linux virtual machines.

## Prerequisites and responsibility boundary

Before onboarding, the customer must meet the following prerequisites:

- the application or website source code must be stored in **GitHub** or **Azure DevOps**
- the customer must provide and maintain access to the source repository and deployment pipeline inputs

Customer responsibility must also be clearly defined:

- the customer is responsible for their **own application**
- this includes application code, dependencies, security issues in the application, testing, business logic, content, and release approval
- the hosting provider is responsible for the **platform**, including cluster operations, deployment platform management, patching, and the agreed infrastructure services

## Goal

Offer managed hosting that is:

- cheaper than Azure App Service Premium for equivalent production workloads
- more operationally valuable than a raw Linux VM
- simple to explain and sell
- profitable once shared AKS density is achieved

## Core pricing principle

Charge customers for **reserved capacity**, not raw runtime usage.

In a shared AKS cluster, costs are driven by:

- reserved CPU and memory
- cluster headroom for failover, scaling, upgrades, and system workloads
- ingress, storage, and platform operations
- monitoring and log ingestion

That means the commercial model should be:

1. **Hosting tier** based on reserved vCPU and RAM
2. **Storage** billed separately when persistent volumes are used
3. **Monitoring** as a separate add-on
4. Optional **overage** for burst usage above agreed reservation

## Azure reference costs used

Region used for reference: **West Europe**

Monthly figures below assume approximately **730 hours per month**.

| Service | Capacity | Azure retail price |
|---|---:|---:|
| Linux VM `Standard_D2s_v5` | 2 vCPU / 8 GiB | ~$84/month |
| Windows VM `Standard_D2s_v5` | 2 vCPU / 8 GiB | ~$151/month |
| App Service Premium v3 Linux `P1 v3` | 2 vCPU / 8 GiB | ~$130/month |
| App Service Premium v3 Linux `P1mv3` | 2 vCPU / 16 GiB | ~$156/month |
| App Service Premium v3 Linux `P2 v3` | 4 vCPU / 16 GiB | ~$260/month |
| App Service Premium v3 Linux `P3 v3` | 8 vCPU / 32 GiB | ~$520/month |
| AKS Standard SLA overhead | per cluster | ~$73/month |

Notes:

- Linux VM pricing above is raw compute only.
- Windows VM pricing above includes the Windows license premium on top of compute.
- VM pricing does **not** include management, ingress, backups, platform operations, or monitoring.
- App Service pricing is per **plan**, not per app.
- AKS cost also includes node VMs, storage, networking, and platform overhead beyond the control plane.

## Recommended shared AKS hosting tiers

| Tier | Reserved CPU | Reserved RAM | Suggested monthly price | VM reference | App Service reference |
|---|---:|---:|---:|---:|---:|
| **S** | 0.5 vCPU | 1 GiB | **$15-20** | - | - |
| **M** | 1 vCPU | 2 GiB | **$28-38** | - | - |
| **L** | 2 vCPU | 4 GiB | **$70-85** | ~$84 Linux / ~$151 Windows VM floor | ~$130 App Service P1v3 |

## What is included in the suggested monthly price

Yes, the **suggested monthly price** for the shared AKS tiers already assumes that management overhead is included. It is not intended to be a raw compute resale price only.

### Internal pricing split

| Component | Typical share |
|---|---:|
| **Raw infrastructure** | **55-70%** |
| **Platform overhead** | **15-25%** |
| **Management / support margin** | **15-25%** |

### What each component covers

- **Raw infrastructure** covers node VM capacity, AKS cluster SLA overhead, baseline storage, load balancer usage, and shared network costs
- **Platform overhead** covers shared cluster headroom, ingress, upgrades, patching, CI/CD integration, backup platform, registry, and platform tooling
- **Management / support margin** covers service operations, platform troubleshooting, onboarding effort, administration time, and business margin

### Example split

For an **L** tier at **$80/month**, a sensible internal model could be:

- **$45-55** for infrastructure
- **$12-18** for shared platform overhead
- **$10-15** for management and margin

### Recommended way to package it

The simplest commercial packaging is:

1. one **hosting price** that already includes platform management
2. **storage** billed separately when needed
3. **monitoring** billed separately as an add-on

Internally, it can still be modeled as:

**Customer price = reserved resource price + platform fee**

Example:

- **Resource fee:** based on CPU and RAM reservation
- **Platform fee:** fixed per application, such as **$10-30/app/month** depending on tier
- **Monitoring:** separate

## Recommended positioning

### Against Linux VMs

Do not try to beat raw VM pricing.

A Linux VM is cheaper because the customer or provider still needs to cover:

- operating system management
- patching
- container runtime and deployment management
- ingress and routing
- backups
- scaling and failover planning
- monitoring setup and retention

The AKS offer should therefore be **more expensive than raw VM cost** while still providing clearly better operational value.

### Against App Service Premium

The shared AKS offer should generally land at about:

- **10-20% below App Service Premium** for equivalent medium and large workloads
- much lower than App Service at very small slices, where App Service is hard to justify commercially

This makes the offer:

- attractive to customers who want container hosting
- competitive for production workloads
- profitable if the shared cluster maintains good tenant density

## Capacity planning guidance

To protect margin, do not sell 100% of cluster capacity.

Recommended planning target by pool type:

| Pool type | Sellable utilization | Reserved capacity | Why |
|---|---:|---:|---|
| **Shared multi-tenant pool** | **60-70%** | **30-40%** | Needed for Kubernetes system workloads, autoscaling headroom, failover, upgrades, and noisy-neighbor protection |
| **Dedicated customer pool** | **75-85%** | **15-25%** | Noisy-neighbor risk is lower, but headroom is still needed for upgrades, failover, and autoscaling |
| **Low-risk dedicated workload** | **85-90%** | **10-15%** | Only sensible when the customer accepts lower resilience and less spare capacity |

For a dedicated user pool, the same **60-70% sellable** model is usually too conservative unless very high elasticity or failover margin is required.

This is why the price model must be based on **reserved** resources, not instantaneous resource use.

## Monitoring pricing

Monitoring should be sold as a separate add-on.

### Azure reference costs used

| Azure monitoring cost component | Reference price |
|---|---:|
| Analytics logs ingestion | ~$2.99/GB |
| Basic logs ingestion | ~$0.65/GB |
| Auxiliary logs ingestion | ~$0.07/GB |
| Analytics retention | ~$0.13/GB-month |
| Common alerting meters | roughly ~$0.30-$0.50 per rule/month |

### Recommended customer-facing monitoring tiers

| Monitoring tier | What is included | Suggested monthly price |
|---|---|---:|
| **None** | No customer-visible monitoring | **$0** |
| **Basic** | Metrics dashboard, uptime checks, 1-2 alerts, 2 GB basic logs | **$10-15** |
| **Standard** | Metrics, dashboards, 3-5 alerts, 5 GB analytics logs | **$25-35** |
| **Advanced** | Full app monitoring, alerting, longer retention, 15 GB analytics logs | **$60-80** |

### Recommended overage pricing

| Item | Suggested customer price |
|---|---:|
| Analytics logs overage | **$4-5/GB** |
| Basic logs overage | **$1-1.5/GB** |
| Extra retention | **$0.20-0.30/GB-month** |

### Suggested default monitoring catalog

| Tier | Suggested price |
|---|---:|
| **Basic** | **$12/month** |
| **Standard** | **$29/month** |
| **Advanced** | **$69/month** |

## Authentication add-on

If customer authentication is required, it should be billed as a separate add-on.

### Recommended authentication component

Use **oauth2-proxy** as the shared authentication layer in front of the customer application when login is required.

This add-on typically covers:

- oauth2-proxy deployment and configuration
- identity provider integration such as **Azure Entra ID**, **GitHub**, or another OIDC provider
- ingress authentication configuration
- session and cookie management
- platform-side maintenance of the authentication component

### Suggested pricing

| Authentication option | What is included | Suggested monthly price |
|---|---|---:|
| **None** | No authentication layer managed by the platform | **$0** |
| **Standard auth** | Shared oauth2-proxy setup for one application | **$15-25** |
| **Advanced auth** | More complex identity provider setup, additional configuration, or stricter access rules | **$30-50** |

For most customers, a practical default is:

- **oauth2-proxy authentication add-on:** **$19/month**

## Recommended sales model

For each customer application:

1. Choose a **hosting tier** based on reserved CPU and memory
2. Add **persistent storage** if required
3. Add **monitoring** if required
4. Add **authentication** if required
5. Add optional **overage/burst policy**

Example:

- Hosting: **L** = 2 vCPU / 4 GiB = **$70-85/month**
- Monitoring: **Standard** = **$29/month**
- Authentication: **oauth2-proxy** = **$19/month**
- Storage: separate

That gives a clear production offer that is still below a comparable App Service Premium plan.

## Recommended default commercial package

For most customer applications, the strongest default offer is:

- **Hosting:** L (2 vCPU / 4 GiB)
- **Monitoring:** Standard
- **Authentication:** oauth2-proxy when login is required
- **Storage:** billed separately

This positions the service as:

- cheaper than App Service Premium for similar capacity
- more operationally complete than a raw VM
- easy to understand for customers

## Sources

The pricing in this document was based on Azure retail prices and Azure pricing pages for:

- Azure Linux Virtual Machines
- Azure App Service Premium v3 for Linux
- Azure Kubernetes Service
- Azure Monitor / Log Analytics
