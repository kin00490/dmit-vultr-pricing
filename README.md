# dmit vs vultr: CN2 GIA routing vs hourly cloud, full plan prices, and how to pick for your workload

There's an assumption buried in the phrase "dmit vs vultr" that deserves to be dug out first: that these two are rival versions of the same product.

They aren't. DMIT runs three locations and sells network routes. Vultr runs 32 data centers and sells flexibility — hourly compute, snapshots, APIs, bare metal, GPU nodes. One company's headline feature is a specific telecom route into mainland China; the other's headline feature is that a $2.50 instance exists and you can destroy it an hour later.

That distinction decides almost everything else. Below is what each one actually charges, what you get for it, and where the comparison stops being meaningful.

## The two companies are not the same kind of business

DMIT began running VPS services in the late 2010s (write-ups date it to 2017 or 2018), is registered in the US, and is operated by a Chinese-speaking team. Its footprint is Los Angeles, Hong Kong and Tokyo, plus a Standard line in San Jose that appears in some write-ups. That's it. Three-ish cities, and a catalogue built around routing quality rather than geographic reach.

Inside each location, DMIT splits its plans by network tier:

- **Premium (Pro)** — China Telecom CN2 GIA on the return path, with Tier 1 transit and the company's own backbone in the mix. The most expensive tier.
- **Eyeball (EB)** — China Mobile CMIN2 and China Unicom 9929-class routing on the way back. Cheaper, and described by the vendor as "best effort" Chinese optimisation rather than guaranteed.
- **Tier 1 (T1)** — plain international BGP. No China-specific routing at all, which is exactly why it's the cheapest.

Vultr launched around 2014 and sits in the Choopa group. It operates 32 locations (its public regions API lists 33), and the plan structure is a standard cloud ladder: Regular Cloud Compute on older Intel CPUs and SATA-era SSDs, High Performance on AMD EPYC or current Intel with NVMe, High Frequency on 3GHz-plus cores with larger NVMe, Optimized Cloud Compute with dedicated resources from $28/month, then VX1, bare metal from $120, and GPU instances. It also sells load balancers, block and object storage, managed databases and managed Kubernetes.

So the honest framing: you go to DMIT for one thing and pay a premium for it. You go to Vultr for breadth, and accept that its routes into China are ordinary.

## Where DMIT actually earns its price

Three things come up repeatedly in write-ups and community threads, and they're the real product.

**The route, on the way back as well as the way out.** DMIT's LAX Premium plans route China Telecom outbound via AS4809, Unicom outbound as direct AS4837, and China Mobile outbound through Hong Kong Mobile AS58453, with all three carriers returning over AS4809. The Eyeball line returns over CMIN2 (AS58807) with Telecom load-balanced across 9929 and 58807. What that buys you is late-evening stability. Third-party write-ups put ordinary international BGP lines at 5-15% packet loss between 20:00 and 23:00 Beijing time, against under 1% on CN2 GIA. Treat the exact percentages as rough, but the direction of the difference is consistent across every comparison I looked at.

**Traffic caps that throttle instead of cutting you off.** Hit your monthly allowance and DMIT drops your port speed and keeps the server online. On LAX Premium the throttle floor runs 4Mbps on the small tiers and 8-10Mbps higher up; on Tier 1 it's 100-200Mbps. For a site that occasionally spikes, that's the difference between a slow week and an outage.

**A stated no-overselling policy.** DMIT's marketing leans hard on it, and it's why popular plans sell out and sit out of stock — the low-priced annual configs in particular. You will see "in stock" appearing and vanishing within days.

DMIT also offers an **IP Guarantee+** option: if the IP you're assigned turns out to be unusable in sensitive regions, you can open a ticket for a free replacement. If you've ever provisioned a cheap VPS and found the IP already burned, this is the feature you'd pay for. Payment options cover Alipay, PayPal and credit cards, and support runs in Chinese.

The weak points, per community write-ups: the control panel is basic next to something like BandwagonHost's KiwiVM, plan inventory is genuinely annoying, and the refund policy and support response times get flagged. Read the terms before checkout rather than after.

## Where Vultr clearly wins

Almost everywhere that isn't routing.

Thirty-two locations is the headline, and it's not a vanity metric. If your users are in Seoul, São Paulo, Johannesburg, Mumbai and Warsaw, Vultr puts a box near each of them at consistent pricing. DMIT cannot do that, and no amount of CN2 GIA changes it.

Then there's the billing model. Vultr bills hourly with a monthly ceiling of 672 hours, so a machine left running all month never bills past its listed monthly price. Spin up an instance to test a deployment, destroy it, pay a few cents. The exception is VX1, which bills actual hours with no cap. The trap worth knowing: powering a server off does not stop billing. Only destroying it does.

The product ladder above plain VPS is the other half. Snapshots, block storage, object storage, managed databases from $18/month, load balancers at $10/month, Kubernetes with nodes billed at normal instance rates, and bare metal from $120/month. If your infrastructure is going to grow sideways rather than just get bigger, that matters more than a faster route.

Worth stating plainly, because blog posts tend to gloss over it: **Vultr publishes no standing compute coupon.** Its savings are structural — run IPv6-only where a routable IPv4 isn't needed, right-size the instance, don't bolt on a $10 balancer and $10 DDoS shield for a single small node. New accounts sometimes get promotional credits, but those expire and don't change the renewal price. Anyone advertising a permanent Vultr discount code is guessing.

## DMIT plans and current prices

DMIT sells several hardware generations under the same plan names — you'll see AS3, and AN4/AN5 platforms on newer chips. Specs and prices shift slightly between them, and inventory rotates constantly. Use this as a map of the lineup, then confirm the exact config on the order page.

| Plan | Location / network | CPU / RAM / SSD | Traffic & port | After the cap | Price | Order |
| --- | --- | --- | --- | --- | --- | --- |
| LAX.AS3.Pro.TINY | LA · Premium | 1 core / 2GB / 20GB | 1TB @ 1Gbps | 4Mbps unlimited | $10.90/mo | [check TINY stock and price](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.Pro.Pocket | LA · Premium | 2 cores / 2GB / 40GB | 1.5TB @ 4Gbps | 4Mbps unlimited | $16.90/mo | [view the Pocket plan](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.Pro.STARTER | LA · Premium | 2 cores / 2GB / 80GB | 3TB @ 10Gbps | 4Mbps unlimited | $34.90/mo | [view STARTER pricing](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.Pro.MINI | LA · Premium | 4 cores / 4GB / 80GB | 5TB @ 10Gbps | 8Mbps unlimited | $62.90/mo | [view the MINI config](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.Pro.MICRO | LA · Premium | 4 cores / 4GB / 160GB | 7TB @ 10Gbps | 8Mbps unlimited | $87.90/mo | [view MICRO details](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.Pro.MEDIUM | LA · Premium | 6 cores / 8GB / 160GB | 14TB @ 10Gbps | 10Mbps unlimited | $199.90/mo | [view the MEDIUM plan](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.EB.TINY | LA · Eyeball | 1 core / 2GB / 20GB | 1.5TB @ 2Gbps | 4Mbps unlimited | $10.90/mo | [view the Eyeball TINY plan](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.EB.Pocket | LA · Eyeball | 2 cores / 2GB / 40GB | 3TB @ 4Gbps | 4Mbps unlimited | $16.90/mo | [view Eyeball Pocket](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.EB.STARTER | LA · Eyeball | 2 cores / 2GB / 80GB | 5TB @ 10Gbps | 4Mbps unlimited | $34.90/mo | [view Eyeball STARTER](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.EB.MINI | LA · Eyeball | 4 cores / 4GB / 80GB | 10TB @ 10Gbps | 8Mbps unlimited | $62.90/mo | [view Eyeball MINI](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.EB.MICRO | LA · Eyeball | 4 cores / 4GB / 160GB | 14TB @ 10Gbps | 8Mbps unlimited | $87.90/mo | [view Eyeball MICRO](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.T1.WEE | LA · Tier 1 | 1 core / 1GB / 20GB | 1TB @ 4Gbps | 100Mbps unlimited | $36.90/yr | [check the annual WEE plan](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.T1.TINY | LA · Tier 1 | 1 core / 1GB / 20GB | 2TB @ 4Gbps | 200Mbps unlimited | $6.90/mo | [view Tier 1 TINY](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.T1.STARTER | LA · Tier 1 | 2 cores / 2GB / 40GB | 4TB @ 10Gbps | 200Mbps unlimited | $12.90/mo | [view Tier 1 STARTER](https://www.dmit.io/aff.php?aff=12046) |
| LAX.AS3.T1.MINI | LA · Tier 1 | 2 cores / 4GB / 80GB | 8TB @ 10Gbps | 200Mbps unlimited | $21.90/mo | [view Tier 1 MINI](https://www.dmit.io/aff.php?aff=12046) |
| HKG.AS3.Pro.TINY | Hong Kong · Premium | 1 core / 1GB / 20GB | 500GB @ 1Gbps | throttle, no suspension | $39.90/mo | [view Hong Kong Premium plans](https://www.dmit.io/aff.php?aff=12046) |
| HKG.AS3.Pro.STARTER | Hong Kong · Premium | 1 core / 2GB / 40GB | 1TB @ 1Gbps | throttle, no suspension | $79.90/mo | [view HKG STARTER](https://www.dmit.io/aff.php?aff=12046) |
| HKG.AS3.Pro.MINI | Hong Kong · Premium | 2 cores / 2GB / 60GB | 1.5TB @ 1Gbps | throttle, no suspension | $126.90/mo | [view HKG MINI](https://www.dmit.io/aff.php?aff=12046) |
| HKG.AS3.Pro.MICRO | Hong Kong · Premium | 2 cores / 4GB / 80GB | 2TB @ 1Gbps | throttle, no suspension | $179.90/mo | [view HKG MICRO](https://www.dmit.io/aff.php?aff=12046) |
| HKG.AS3.Pro.MEDIUM | Hong Kong · Premium | 4 cores / 4GB / 160GB | 2.5TB @ 1Gbps | throttle, no suspension | $239.90/mo | [view HKG MEDIUM](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.WEE | Hong Kong · Tier 1 | 1 core / 1GB / 20GB | 1TB @ 4Gbps | 50Mbps unlimited | $36.90/yr | [view HKG Tier 1 WEE](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.TINY | Hong Kong · Tier 1 | 1 core / 1GB / 20GB | 2TB @ 4Gbps | 100Mbps unlimited | $6.90/mo | [view HKG Tier 1 TINY](https://www.dmit.io/aff.php?aff=12046) |
| HKG.T1.STARTER | Hong Kong · Tier 1 | 1 core / 2GB / 40GB | 4TB @ 4Gbps | 100Mbps unlimited | $12.90/mo | [view HKG Tier 1 STARTER](https://www.dmit.io/aff.php?aff=12046) |
| TYO.Pro.TINY | Tokyo · Premium | 1 core / 1GB / 20GB | 500GB @ 1Gbps | throttle, no suspension | $21.90/mo | [view Tokyo Premium plans](https://www.dmit.io/aff.php?aff=12046) |

A few notes that matter more than the rows. Hong Kong Premium is roughly four times the Los Angeles price for weaker raw specs, because the Hong Kong CN2 GIA bandwidth costs what it costs. Hong Kong Eyeball has been reported as withdrawn in some periods, so the middle tier may not exist there when you look. Tokyo listings have shown more variation between hardware generations than the US ones.

On discounts: DMIT applies automatic reductions on longer billing cycles, and separately issues cart-level codes you validate at checkout. Community trackers have carried a recurring 20% code for LAX Eyeball on non-monthly terms (`LAX-EB-LAUNCH-NON-MONTHLY-RECURRING-20OFF`) and Lite-series codes for annual and semi-annual prepay. Seasonal campaigns have reached around 20% recurring plus account credit on the Los Angeles lines. Codes get retired without notice, so validate before you commit rather than trusting any list, including this one.

## Vultr plans and current prices

Vultr's prices are hourly rates with a monthly cap, unless you're on VX1. The entry point is lower than DMIT's, but read the storage and transfer columns rather than the dollar figure.

| Line | vCPU / RAM / Storage | Bandwidth | Monthly |
| --- | --- | --- | --- |
| Regular Cloud Compute (IPv6-only) | 1 / 512MB / 10GB | 0.5TB | $2.50 |
| Regular Cloud Compute (IPv4, limited regions) | 1 / 512MB / 10GB | 0.5TB | $3.50 |
| Regular Cloud Compute | 1 / 1GB / 25GB | 1TB | $5 |
| Regular Cloud Compute | 1 / 2GB / 55GB | 2TB | $10 |
| Regular Cloud Compute | 2 / 4GB / 80GB | 3TB | $20 |
| Regular Cloud Compute | 4 / 8GB / 160GB | 4TB | $40 |
| Regular Cloud Compute | 6 / 16GB / 320GB | 5TB | $80 |
| Regular Cloud Compute | 8 / 32GB / 640GB | 6TB | $160 |
| High Performance (EPYC / current Xeon, NVMe) | 1 / 1GB / 25GB NVMe | 2TB | $6 |
| High Performance | 1 / 2GB / 50GB NVMe | 3TB | $12 |
| High Performance | 2 / 4GB / 100GB NVMe | 5TB | $24 |
| High Performance | 4 / 8GB / 180GB NVMe | 6TB | $48 |
| High Performance | 8 / 16GB / 350GB NVMe | 8TB | $96 |
| High Frequency (3GHz+ cores, larger NVMe) | from 1 / 1GB / 32GB NVMe | 1TB | from $6 |
| Optimized Cloud Compute (dedicated resources) | from ~4GB RAM | varies | from $28 |
| VX1 Cloud Compute | enterprise tier | varies | from ~$43.80 |
| Bare Metal | dedicated hardware | 5TB+ | from $120 |

Add-ons that show up on real bills: load balancer $10/month, DDoS protection billed separately rather than universally bundled, managed databases from $18/month, block storage from around $0.10/GB/month, object storage from $5/month, reserved IPs $3/month, Windows licences around $16/month on top of the instance, and automated backups charged as a percentage of the instance price. Bandwidth beyond the allowance runs about $0.01/GB in North America and Europe and more in Asia-Pacific. Vultr has no free tier.

## dmit vs vultr at the same monthly spend

This is where the comparison gets concrete.

**Around $10/month.** DMIT gives you 1 vCPU, 2GB RAM, 20GB SSD and 1TB on a China-optimised return path. Vultr gives you 1 vCPU, 2GB, 55GB and 2TB on an unoptimised route — nearly triple the disk and double the transfer for the same money. If your users are in Frankfurt, Vultr is not close. If they're on China Telecom at 9pm, the extra 35GB of storage doesn't compensate for routing.

**Around $35-40/month.** DMIT's LAX Pro STARTER is 2 cores, 2GB, 80GB and 3TB on CN2 GIA. Vultr's $40 Regular plan is 4 cores, 8GB, 160GB and 4TB — more than double the RAM. Vultr's Optimized tier with dedicated CPU starts at $28. On raw specifications DMIT loses this round badly. On route quality it wins by a margin that's hard to quantify in a table.

**At the annual floor.** DMIT's Tier 1 WEE is $36.90/year, which is about $3.08/month for 1 core, 1GB, 20GB and 1TB with a 4Gbps port. Vultr's cheapest IPv4 option is $3.50/month for 512MB. On prepaid annual economics, DMIT's entry plan is the better spec — but it comes with no China routing and no hourly flexibility, so it's a different purchase, not a cheaper one.

## Which one to pick

**Choose DMIT if** your users are in mainland China and you care about evening performance on Telecom or Unicom. LAX Premium for Telecom-heavy audiences, LAX Eyeball if your traffic skews China Mobile or you want the lower price, Hong Kong Premium if 40-50ms latency justifies roughly four times the cost. Tier 1 WEE makes sense as a cheap annual relay, landing node or backup target where China routing doesn't matter.

**Choose Vultr if** your users are anywhere else, or you need hourly billing, snapshots, an API-driven workflow, more than three cities, Kubernetes, bare metal, or GPU capacity. It's also the better pick for CI/CD runners, staging environments and anything you'll destroy and rebuild — spending $0.02 on an instance to test a deploy script is a genuinely useful thing that DMIT's monthly billing doesn't offer as naturally.

**A hybrid worth knowing about.** Some teams put a Vultr instance in Tokyo or Los Angeles and route China traffic through a relay on a DMIT Tier 1 or Premium node. It's more moving parts, but it lets you keep the cheap flexible compute and buy routing only where it's needed.

## Common questions

**Is DMIT more expensive than Vultr?** Yes, at equivalent vCPU and RAM — typically double or more, and much more again in Hong Kong. The price buys routing, not hardware.

**Does Vultr have a China-optimised network?** No. Vultr doesn't sell China-specific routing, and write-ups note its Asia-Pacific locations can congest at peak. Pair it with a relay if China performance matters.

**Does DMIT cut off service when traffic runs out?** No. It throttles the port instead, from 4Mbps on the small Premium tiers up to 100-200Mbps on Tier 1.

**Does Vultr stop billing when I power off a server?** No. Only destroying the instance stops the meter.

**Can I pay with Alipay on both?** Yes, both accept Alipay, along with PayPal and cards. DMIT also offers Chinese-language support.

**Which has more locations?** Vultr, by a wide margin: 32 versus DMIT's three main cities.

## The short version

If you're searching "dmit vs vultr" because something feels slow at peak hours, you're not comparing prices — you're comparing a routing specialist against a general cloud, and the price gap is the routing. DMIT is the right answer when the route is the problem, and an expensive answer when it isn't. Vultr is the better platform for almost everything else, and no feature it offers will do anything about late-evening packet loss into China.

Pick the constraint you can't work around first, then buy.
