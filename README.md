# Azure managed application hosting pricing

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
| App Service Premium v3 Linux `P1 v3` | 2 vCPU / 8 GiB | ~$130/month |
| App Service Premium v3 Linux `P1mv3` | 2 vCPU / 16 GiB | ~$156/month |
| App Service Premium v3 Linux `P2 v3` | 4 vCPU / 16 GiB | ~$260/month |
| App Service Premium v3 Linux `P3 v3` | 8 vCPU / 32 GiB | ~$520/month |
| AKS Standard SLA overhead | per cluster | ~$73/month |

Notes:

- Linux VM pricing above is raw compute only.
- VM pricing does **not** include management, ingress, backups, platform operations, or monitoring.
- App Service pricing is per **plan**, not per app.
- AKS cost also includes node VMs, storage, networking, and platform overhead beyond the control plane.

## Recommended shared AKS hosting tiers

| Tier | Reserved CPU | Reserved RAM | Suggested monthly price | Linux VM reference | App Service reference |
|---|---:|---:|---:|---:|---:|
| **XS** | 0.25 vCPU | 0.5 GiB | **$8-12** | - | - |
| **S** | 0.5 vCPU | 1 GiB | **$15-20** | - | - |
| **M** | 1 vCPU | 2 GiB | **$28-38** | - | - |
| **L** | 2 vCPU | 4 GiB | **$70-85** | ~$84 VM floor | ~$130 App Service P1v3 |
| **XL** | 2 vCPU | 8 GiB | **$110-125** | ~$84 Linux VM D2s v5 | ~$130 App Service P1v3 |
| **2XL** | 4 vCPU | 8 GiB | **$150-175** | - | - |
| **3XL** | 4 vCPU | 16 GiB | **$210-240** | - | ~$260 App Service P2v3 |
| **4XL** | 8 vCPU | 16 GiB | **$300-340** | - | - |
| **5XL** | 8 vCPU | 32 GiB | **$430-480** | - | ~$520 App Service P3v3 |

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

Recommended planning target:

- **60-70%** sellable utilization
- **30-40%** reserved for Kubernetes system workloads, autoscaling headroom, failover, upgrades, and noisy-neighbor protection

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

## Recommended sales model

For each customer application:

1. Choose a **hosting tier** based on reserved CPU and memory
2. Add **persistent storage** if required
3. Add **monitoring** if required
4. Add optional **overage/burst policy**

Example:

- Hosting: **XL** = 2 vCPU / 8 GiB = **$110-125/month**
- Monitoring: **Standard** = **$29/month**
- Storage: separate

That gives a clear production offer that is still below a comparable App Service Premium plan.

## Recommended default commercial package

For most customer applications, the strongest default offer is:

- **Hosting:** XL (2 vCPU / 8 GiB)
- **Monitoring:** Standard
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
