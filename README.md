# HPCC: High Precision Congestion Control

## Overview
HPCC, a new high-speed CC mechanism, achieves ultra-low latency, high bandwidth, and network stability simultaneously. HPCC leverages in-network telemetry (INT) to obtain precise link load information and controls traffic precisely. HPCC can quickly converge to utilize free bandwidth while avoiding congestion, and can maintain near-zero in-network queues for ultra-low latency.

<p align="center"><img src="figs/overview.png" width="60%"></p>

HPCC has been deployed in Alibaba. It shortens the flow completion times by up to 95% compared to the previous generation CC (DCQCN) for datacenters.

**[New] We have recently enhanced HPCC with PINT, a probalistic version of INT that reduces the INT overhead to ≤2 bytes without losing precision for HPCC.**

## News

* We post [extensive results](results.md) with both INT-based and PINT-based HPCC.
* **[PINT is published in SIGCOMM' 2020](https://liyuliang001.github.io/publications/pint.pdf)**, which enhances HPCC by removing the INT overhead without losing HPCC's precision.
* **[HPCC is widely reported by ≥20 media globally](media-reports.md)**
* **[HPCC is published in SIGCOMM' 2019](https://liyuliang001.github.io/publications/hpcc.pdf)**. Please watch our [conference talk video](https://dlnext.acm.org/doi/abs/10.1145/3341302.3342085).

## Publications
* [PINT: Probabilistic In-band Network Telemetry](https://liyuliang001.github.io/publications/pint.pdf)  
Ran Ben Basat, Sivaramakrishnan Ramanathan, Yuliang Li, Gianni Antichi, Minlan Yu, Michael Mitzenmacher  
ACM SIGCOMM, 2020
* [HPCC: High Precision Congestion Control](https://liyuliang001.github.io/publications/hpcc.pdf)  
Yuliang Li, Rui Miao, Hongqiang Liu, Yan Zhuang, Fei Feng, Lingbo Tang, Zheng Cao, Ming Zhang, Frank Kelly, Mohammad Alizadeh, Minlan Yu  
ACM SIGCOMM, 2019

## Key results
We post [latest results](results.md) with both INT-based and PINT-based HPCC.
We evaluate HPCC comprehensively under different application workload (web search and hadoop) and different network load (30%, 50%, 70%). 

## Code
* [NS3 simulation](https://github.com/alibaba-edu/High-Precision-Congestion-Control)
