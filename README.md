# enterprise cloud service: a practical buyer's guide with real plan pricing, egress fees, and lock-in traps explained

Most people who search "enterprise cloud service" aren't looking for a definition. They're trying to answer one of a few practical questions: which provider should we actually go with, why is our current cloud bill twice what we budgeted, or how do we move serious workloads off on-prem hardware without losing control of the whole thing.

This article works through those questions in order — what an enterprise cloud service has to include, how the pricing models differ, where bills usually go wrong, and what to check before signing anything. Along the way I'll use Sharktech, an OpenStack-based cloud provider that's been in the infrastructure business since 2003, as a concrete worked example with its current plan pricing. Concrete numbers make the abstract stuff easier to judge, and you can map the same checklist onto AWS, Azure, GCP, or anyone else.

## What an enterprise cloud service actually has to include

At the infrastructure level, an enterprise cloud service is compute, storage, and networking delivered as a pool you can carve up yourself, instead of as fixed servers you rack and maintain. That's the short version. The longer version is a list of things the service has to do before it's genuinely usable by a business rather than a hobby project:

- **Resource pooling you control.** You get a block of vCPUs, RAM, and storage and distribute it across as many virtual machines as you need, in any combination. If a plan only sells you fixed VM sizes, that's VPS with better marketing, not a cloud.
- **Redundancy that survives hardware failure.** Your VMs should run across multiple physical hosts and storage nodes so a dead node means a live migration, not an outage page.
- **Security controls at the network layer.** Private networking between VMs, firewall/security groups with granular rules, DDoS protection, and ideally VPN connectivity back to on-prem systems.
- **Predictable exit.** If you can't download your disk images and leave, you don't own your infrastructure — you're renting a roach motel.
- **Support that answers.** When production is down at 2 a.m., a community forum is not an enterprise cloud service.

That last point about exit rights sounds paranoid until you've lived through a migration where the provider made it structurally painful to leave. It's one of the reasons open-source cloud platforms exist as a category.

## Pay-as-you-go vs. committed resources: the decision that shapes your bill

Every cloud provider, big or small, effectively offers two billing philosophies, and picking the wrong one for your workload pattern is the most common way budgets break.

**Pay-as-you-go (PAYG)** gives you a base allocation plus the ability to burst past it whenever needed, billed hourly for the overage. This is the right model when usage is spiky or unpredictable — batch jobs, seasonal traffic, staging environments that spin up and down. The catch is obvious: if your usage is actually flat and you're sitting above your included limits 24/7, you're paying hourly rates on a permanent basis.

**Committed/fixed plans** bill a set monthly fee for a set resource block. No variables, no surprises, same invoice every month. This wins when utilization is steady and you can forecast next quarter's needs reasonably well. The trade-off is that bursting past a committed allocation usually means a plan change, not an automatic overage.

Here's the worked example from Sharktech's own documentation, because the math is easier to trust than the theory. Their Public Cloud Large tier at 32 cores, 64 GB RAM, and 1,500 GB SSD costs $287.18/month at the full included configuration. Suppose you run six VMs that together consume 48 cores and 96 GB of RAM. Under PAYG, the overage is 16 cores and 32 GB at the hourly rates ($0.0025/core/hr and $0.0035/GB/hr), which works out to $287.18 + $28.80 + $80.64 = **$396.62/month**. If those six VMs had steady 24/7 usage, a fixed allocation covering 48 cores and 96 GB outright would likely cost less over a year. Spiky usage reverses the conclusion.

There's a third thing to check that neither model protects you from by default: **egress fees**.

## The egress trap: the line item nobody budgets for

Ask anyone who's managed a cloud budget where the pain came from, and data transfer shows up constantly. The pattern is consistent across the hyperscalers: inbound traffic is free, outbound (egress) traffic is billed per GB, and the per-GB rate is high enough that a data-heavy workload — media serving, database replication, large-scale backups leaving the platform — can quietly become the biggest line on the invoice.

The structural problem is worse than the price. Egress fees are effectively a lock-in mechanism. The more expensive it is to move your data out, the less likely you are to move it, ever. When you evaluate any enterprise cloud service, including this one, get written answers to three questions:

1. How many GB of outbound transfer are included per month, at every tier?
2. What's the per-GB rate after that?
3. Is there any fee for transferring data *between* the provider's own regions or to an external destination like your office?

For comparison at the concrete end: Sharktech includes unlimited inbound on cloud services, 5,000 GB of outbound on the base cloud offering, and charges $0.002/GB for additional outbound. Their tier listings show bandwidth starting at 20 TB on cloud plans. That per-GB rate is in the same single-mil-dollar range that smaller providers use to undercut hyperscaler egress pricing — which is precisely why you should always ask the three questions above rather than assume the rate travels across providers.

## Vendor lock-in: the expensive problem you can't see on a pricing page

Open-source cloud platforms matter here. Sharktech's infrastructure runs on OpenStack — the same open-source cloud framework used by large public and private clouds worldwide — and that has two practical consequences for a business buyer.

First, **no proprietary barrier on your own workloads**. You can upload your own VM disk images (qcow or custom ISOs) through the portal or API, run them, and download them again whenever you want — for backup, disaster recovery, or because you're moving to a different provider entirely. Your images and data remain yours in the literal sense that you can take the files with you.

Second, **standard APIs**. The platform exposes RESTful APIs for compute, storage, networking, and identity (OpenStack's Nova, Cinder, Swift, Neutron, and Keystone components, if you want the specific names), which means automation and orchestration tools built against open standards work without provider-specific adapters. There's also native VPN support for bridging cloud VMs to on-premises infrastructure at no extra charge, which matters if you're running hybrid rather than cloud-only.

Lock-in isn't only about software, though. It's also about *operational* gravity — the accumulated scripts, dashboards, and tribal knowledge that only work inside one vendor's console. Portability features reduce the software half of that problem. Nothing reduces the other half except discipline.

## A concrete look: Sharktech's enterprise cloud platform

To make the evaluation criteria less abstract, here's what Sharktech's cloud actually ships with, verified against their current product pages:

- **OpenStack-powered hyper-converged infrastructure** with fully redundant compute and storage, and automatic failover so VMs stay online through hardware failures
- **Multi-tier storage**: NVMe (about 1.2 GB/s, up to 18,000 IOPS per volume), SSD (350 MB/s, up to 6,000 IOPS), and HDD (120 MB/s, up to 3,000 IOPS) — you mix tiers within one allocation, putting databases on NVMe and archives on HDD
- **40G/100G internal network** with built-in DDoS protection on the infrastructure
- **Five data center locations**: Los Angeles, Las Vegas, Denver, Chicago, and Amsterdam
- **Cloud control panel** for deploying VMs, building networks, managing firewalls, load balancers, floating IPs, and user permissions — plus full API access for everything
- **Weekly-updated official Linux cloud images** with support for SSH keys, cloud-init scripts, and custom images at launch
- **99.999% uptime guarantee**, stated on their cloud pricing page
- **24/7/365 support** that includes reaching a human by phone — which, as anyone who has tried the hyperscaler support maze knows, is a genuine differentiator at this price level

On the cost side, their marketing claims are worth separating. The cloud pricing page advertises 50–80% savings versus hyperscalers, while their FAQ commits to "at least 40% cost savings" compared to AWS, Azure, and GCP. Treat those as vendor claims and pressure-test them against your own workload math — but the underlying structural reasons the claim is plausible are the ones already covered: no proprietary licensing, cheaper egress, and flat-rate plans.

Third-party coverage is generally consistent on a few points. Reviews aggregated on hosting review sites consistently praise support responsiveness and uptime, and HostAdvice's reviews of their cloud and VPS lines are substantive. One caveat worth knowing in advance: HostAdvice's VPS review flags a **no-refund policy** — common for unmanaged infrastructure, but it means you should validate fit with a smaller commitment before moving anything production-critical. 👉 You can check the current plans and pricing directly to see how the tiers line up with your workload.

## Full plan comparison: every current Sharktech cloud tier

Both billing models run on the same infrastructure; the difference is purely how resources and billing work. Public Cloud is PAYG with a resource cap on lower tiers so a runaway process can't produce a runaway bill (Enterprise and Custom plans are uncapped). Dedicated Cloud bills a fixed monthly fee for exactly the resources you order — pay for 8 cores, get 8 cores.

Here's the complete current tier list from their order pages, Los Angeles location, monthly billing:

| Tier | vCPU | RAM | Storage (SSD / HDD / NVMe) | Bandwidth | Price (from) | Get it |
| --- | --- | --- | --- | --- | --- | --- |
| **Public Cloud – Small** | 4–16 | 8–32 GB | 300–2,400 GB / up to 4,800 GB / up to 1,200 GB | 20 TB+ | $39.00/mo | [Configure the Small tier](https://portal.sharktech.net/aff.php?aff=1611&rp=/store/public-cloud-hosting/public-cloud-hosting-los-angeles-small) |
| **Public Cloud – Medium** | 8–32 | 16–64 GB | 800–6,400 GB / up to 12,800 GB / up to 3,200 GB | 20 TB+ | $79.00/mo | [Configure the Medium tier](https://portal.sharktech.net/aff.php?aff=1611&rp=/store/public-cloud-hosting/public-cloud-hosting-los-angeles-medium) |
| **Public Cloud – Large** | 32–128 | 64–256 GB | 1,500–12,000 GB / up to 24,000 GB / up to 6,000 GB | 20 TB+ | $249.00/mo | [Configure the Large tier](https://portal.sharktech.net/aff.php?aff=1611&rp=/store/public-cloud-hosting/public-cloud-hosting-los-angeles-large) |
| **Public Cloud – Enterprise** | 64+ (no cap) | 128 GB+ (no cap) | 5,000 GB+ (no cap) | 20 TB+ | $499.00/mo | [Configure the Enterprise tier](https://portal.sharktech.net/aff.php?aff=1611&rp=/store/public-cloud-hosting/public-cloud-hosting-los-angeles-enterprise) |
| **Public Cloud – Custom** | Custom | Custom | Custom | Custom | Contact sales | [Ask sales about a custom build](https://bit.ly/SharKTech) |
| **Dedicated Cloud** (fixed monthly billing) | 8–512 | 16–1,024 GB | SSD / HDD / NVMe tiers | 5–300 TB | $86.23/mo | [Configure Dedicated Cloud](https://portal.sharktech.net/aff.php?aff=1611&rp=/store/dedicated-public-cloud-bare-metal/dedicated-cloud) |

A few notes on reading that table. "From" prices are the entry point of each tier's configurable range — a Large tier loaded to its full 128-core / 256 GB / 12 TB SSD maximum costs considerably more than $249. Every plan includes unlimited VMs within the resource pool, one free public IPv4 (additional IPs are $1.50/month), unlimited inbound traffic, and 5,000 GB of outbound on cloud services with additional outbound at $0.002/GB.

The hourly rates for bursting past your included Public Cloud allocation:

| Resource | Hourly rate | Approx. 24/7 monthly equivalent |
| --- | --- | --- |
| vCPU | $0.0025/hr | ~$1.80 per core |
| RAM | $0.0035/hr | ~$2.52 per GB |
| NVMe storage | $0.00009/hr/GB | ~$0.065 per GB |
| SSD storage | $0.00006/hr/GB | ~$0.043 per GB |
| HDD storage | $0.00002/hr/GB | ~$0.014 per GB |

That table is the whole PAYG story in miniature: bursting a Medium plan by 4 cores and 8 GB for a week costs roughly the price of lunch; running that burst 24/7 for a month costs about $31. Bursting is a feature. Living in the burst is a planning failure.

## Public or Dedicated: how to choose without a spreadsheet migraine

The shorthand I'd use after looking at both:

- **Steady, forecastable workloads** — production databases, long-running application servers, anything that runs 24/7 — belong on fixed monthly billing. Dedicated Cloud from $86.23/month with a locked resource block makes your finance team happy and removes the burst-math question entirely.
- **Spiky or unpredictable workloads** — CI/CD fleets, seasonal retail, staging that scales with developer activity, batch processing — suit PAYG. Start at the tier covering your baseline, let bursts handle the peaks, and let the cap (on Small through Large) prevent invoice surprises.
- **Not sure which you are?** That's a legitimate state, and Sharktech offers a free cloud consultation where their team looks at your workload and recommends a plan — 👉 [book the free consultation and let them do the sizing math](https://bit.ly/SharKTech) before you commit to anything.

One more thing that doesn't fit neatly into either column: the platform treats billing systems and infrastructure as separately isolated environments. That's a security decision rather than a billing one — separating financial data from infrastructure management reduces cross-system attack risk and lets you assign billing vs. cloud operations to different teams. Boring detail, but it's the kind of architecture question enterprise buyers should be asking every provider.

## Who this fits — and who it doesn't

An honest assessment cuts both ways.

**Sharktech fits if:** you want hyperscaler-style resource pooling without hyperscaler pricing or lock-in, your team is comfortable running unmanaged infrastructure (it's a self-managed platform — you get the tools, not a managed service), you value phone-accessible 24/7 support, and DDoS protection included at the infrastructure level matters for what you're hosting. Game infrastructure, SaaS backends, e-commerce platforms handling traffic spikes, and dev/staging fleets are all squarely in the target zone.

**Look elsewhere if:** you need the full managed-service catalog of a hyperscaler (managed Kubernetes control planes, serverless everything, ML pipelines as a service), you require a formally documented enterprise SLA contract negotiated at scale, or your organization needs a provider with a huge third-party marketplace. A 20-year-old infrastructure specialist and a $250B cloud platform are different products, and pretending otherwise wastes everyone's time.

## A five-point checklist you can apply to any enterprise cloud service

Whether you end up on Sharktech, a hyperscaler, or something in between, run every candidate through this:

1. **Model your actual bill.** Take last month's real usage (cores, RAM, storage, and — critically — outbound GB), plug it into each provider's pricing, and compare totals, not headline rates.
2. **Read the egress terms in writing.** Included GB, overage rate, inter-region transfer costs. This is where "cheap" clouds get expensive.
3. **Test the exit before you enter.** Can you export disk images and data, on demand, without a support ticket negotiation? Try it during a trial period, not during an emergency.
4. **Verify the support path.** What channels exist, what are the response commitments, and is there a phone number that reaches an engineer? For production workloads this is a feature, not a courtesy.
5. **Check where your data physically lives.** Jurisdiction and latency both follow geography. Five US locations plus Amsterdam covers most needs, but confirm your specific compliance requirements against actual data center locations, not marketing maps.

## Getting started, step by step

If the numbers above line up with your workload, the practical path is short:

1. Pick a candidate tier based on your baseline usage — Small for dev and small production, Medium or Large for serious application loads, Enterprise for uncapped scale, or Dedicated Cloud if you want a fixed invoice.
2. Spin up one non-critical VM first. Verify networking, storage tier performance (NVMe vs. SSD vs. HDD against your actual I/O patterns), and image upload/download.
3. Run it for a billing cycle and compare the actual invoice against your modeled bill. This is the moment any billing-model mismatch reveals itself, while it's still cheap.
4. Only then migrate production, and download your images on a schedule from day one — not because you plan to leave, but because the option to leave is what keeps the relationship honest.

👉 [Start with the Small tier and validate the platform on a real workload](https://portal.sharktech.net/aff.php?aff=1611&rp=/store/public-cloud-hosting/public-cloud-hosting-los-angeles-small) — at $39/month it's the cheapest due diligence you'll do all year — or 👉 [compare the full Public and Dedicated Cloud lineup side by side](https://bit.ly/SharKTech) if you already know your resource footprint.

The enterprise cloud service you pick should be decided by arithmetic and exit options, not by logo recognition. Run the numbers, test the escape hatch, and keep the egress terms pinned to the wall where you can see them.
