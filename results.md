# HPCC key results

We evaluate both INT-based HPCC (HPCC-INT) and [PINT-based HPCC (HPCC-PINT)](https://liyuliang001.github.io/publications/pint.pdf). HPCC-PINT **uses PINT to reduce the INT overhead to ≤2 bytes per packet**. While HPCC-INT has to tolerate the extra header overhead (~5%), **HPCC-PINT evaluates the real power of HPCC’s algorithm** with negligible extra header overhead.

Experiment settings and metrics are mostly the same as those in the HPCC paper, except that η=99% and maxStage=0, which are the best setting for HPCC. Details are [here](#settings).

We also aim to answer questions about HPCC under extremely high network load. HPCC performs very well with the correct setting. Please see [here](#under-extreme-network-load-70).

## Under normal network load (30%)
We first evaluate under the normal network load, which is around 30% [[Microsoft](https://conferences.sigcomm.org/sigcomm/2017/files/program-kbnets/keynote-2.pdf), [Facebook](https://conferences.sigcomm.org/sigcomm/2015/pdf/papers/p123.pdf)].

### Web search workload
<table>
    <tr>
      <td align="center"><img src="figs/fct_wb30_50pct.png" width="100%"> <b>50-th percentile</b></td>
      <td align="center"><img src="figs/fct_wb30_95pct.png" width="100%"> <b>95-th percentile</b></td>
      <td align="center"><img src="figs/fct_wb30_99pct.png" width="100%"> <b>99-th percentile</b></td>
    </tr>
</table>

HPCC significantly improves the FCT for flows < 200KB, especially at the tail. This is because HPCC resolves congestion in just one RTT, and converge to zero queue thereafter. HPCC-INT has longer FCT for long flows, which is expected because of the INT overhead, which means HPCC-INT effectively runs at a higher network load (~5% more) than other CC schemes. HPCC-PINT almost remove this extra overhead, so it improves the long flows' FCT over HPCC-INT, and makes them comparable to other schemes that keeps the buffer highly occupied. This effect is explained in detail in [PINT paper](https://liyuliang001.github.io/publications/pint.pdf).

### Hadoop workload
<table>
    <tr>
      <td align="center"><img src="figs/fct_fb30_50pct.png" width="100%"> <b>50-th percentile</b></td>
      <td align="center"><img src="figs/fct_fb30_95pct.png" width="100%"> <b>95-th percentile</b></td>
      <td align="center"><img src="figs/fct_fb30_99pct.png" width="100%"> <b>99-th percentile</b></td>
    </tr>
</table>


Under the Hadoop workload, the trend is similar. Moreover, since there are more short flows, HPCC's advantage is more evident.

## Under high network load (50%)
It is possible that the network load sometime exceeds the normal condition. So it is also worth evaluating 50% load to represent the occasional high load.

### Web search workload
<table>
    <tr>
      <td align="center"><img src="figs/fct_wb50_50pct.png" width="100%"> <b>50-th percentile</b></td>
      <td align="center"><img src="figs/fct_wb50_95pct.png" width="100%"> <b>95-th percentile</b></td>
      <td align="center"><img src="figs/fct_wb50_99pct.png" width="100%"> <b>99-th percentile</b></td>
    </tr>
</table>

The trend is similar to the 30% load case. But it is important to note that under 50% load, HPCC improves the short flows FCT even at 50-th percentile (unlike the 30% load case where 50-th percentile FCT are similar across different schemes).

### Hadoop workload
<table>
    <tr>
      <td align="center"><img src="figs/fct_fb50_50pct.png" width="100%"> <b>50-th percentile</b></td>
      <td align="center"><img src="figs/fct_fb50_95pct.png" width="100%"> <b>95-th percentile</b></td>
      <td align="center"><img src="figs/fct_fb50_99pct.png" width="100%"> <b>99-th percentile</b></td>
    </tr>
</table>

The trend is similar to the web search workload.

## Under extreme network load (70%)
A network load higher than 50% can make the network brittle--link or switch failures can cause severe congestion or even ever-increasing number of unfinished flows [[CONGA](https://people.csail.mit.edu/alizadeh/papers/conga-sigcomm14.pdf)]. This is because ECMP balances traffic poorly under asymmetry. Failrues are so common in datacenters that the topology is typically guaranteed to be asymmetric [[Google](http://www.sysnet.ucsd.edu/sysnet/miscpapers/wcmp-eurosys-final.pdf)].

Since HPCC is published, many people expressed concern about HPCC under extreme network loads. We acknowledge that, zero queue is designed for reasonable network loads that are evaluated in HPCC paper, not for extreme loads. However, **HPCC performs very well under extreme loads, if setting η correctly to keep non-zero but small queues.** We will show this by evaluating HPCC under 70% network load. Note that our definition of 70% only considers the payload, so the actual load to the network is >77.7%.

We note that at such an extreme load, keeping queue zero may waste bandwidth, harming long flows. This is because if two flows share a link with zero queue, and one of them finishes, the bandwidth is immediately under-utilized for at least an RTT, until the other flow grabs the free bandwidth. At higher loads, flows come and leave more frequently, so the RTT-waste of bandwidth is more frequently too. In contrast, if there is non-zero queue, even if a flow finishes, the packets in the queue can still keep the bandwidth fully utilized. So this is a fundamental tradeoff.  **Therefore, we also evaluate η=150% in HPCC to keep a small queue (0.5 BDP).** 

We didn't use η=150% under 30% and 50% network loads because this tradeoff is only evident at extreme network loads, and η=99% brings the largest benefit. But η=150% also out-performs other schemes under 30% and 50% loads.

### Web search workload
<table>
    <tr>
      <td align="center"><img src="figs/fct_wb70_50pct.png" width="100%"> <b>50-th percentile, HPCC η=99%</b></td>
      <td align="center"><img src="figs/fct_wb70_95pct.png" width="100%"> <b>95-th percentile, HPCC η=99%</b></td>
      <td align="center"><img src="figs/fct_wb70_99pct.png" width="100%"> <b>99-th percentile, HPCC η=99%</b></td>
    </tr>
</table>

<table>
    <tr>
      <td align="center"><img src="figs/fct_wb70_utgt150_50pct.png" width="100%"> <b>50-th percentile, HPCC η=150%</b></td>
      <td align="center"><img src="figs/fct_wb70_utgt150_95pct.png" width="100%"> <b>95-th percentile, HPCC η=150%</b></td>
      <td align="center"><img src="figs/fct_wb70_utgt150_99pct.png" width="100%"> <b>99-th percentile, HPCC η=150%</b></td>
    </tr>
</table>

As the network load increases, HPCC η=99% is more evidently better than other schemes on short flows, at 50, 95, and 99-th percentile (note the y axis range), while on long flows, it's slower. This is an expected tradeoff of choose zero queue, as analyzed above.

Under this case, HPCC η=150% offers another balance, which is slightly slower than HPCC η=99% on short flows, but is **better than or comparable to all other schemes on all flows.** 


### Hadoop workload
<table>
    <tr>
      <td align="center"><img src="figs/fct_fb70_50pct.png" width="100%"> <b>50-th percentile, HPCC η=99%</b></td>
      <td align="center"><img src="figs/fct_fb70_95pct.png" width="100%"> <b>95-th percentile, HPCC η=99%</b></td>
      <td align="center"><img src="figs/fct_fb70_99pct.png" width="100%"> <b>99-th percentile, HPCC η=99%</b></td>
    </tr>
</table>

<table>
    <tr>
      <td align="center"><img src="figs/fct_fb70_utgt150_50pct.png" width="100%"> <b>50-th percentile, HPCC η=150%</b></td>
      <td align="center"><img src="figs/fct_fb70_utgt150_95pct.png" width="100%"> <b>95-th percentile, HPCC η=150%</b></td>
      <td align="center"><img src="figs/fct_fb70_utgt150_99pct.png" width="100%"> <b>99-th percentile, HPCC η=150%</b></td>
    </tr>
</table>

The trend is similar to the web search workload.

## Effect of η
In the paper we use η=95% because traditionally other proposals use 95% [[RCP](http://yuba.stanford.edu/~nanditad/thesis-NanditaD.pdf),[HULL](https://www.usenix.org/system/files/conference/nsdi12/nsdi12-final187.pdf)]. Now we show that HPCC is not sensitive to the setting of η. We compare η=95% vs 99%.

Specifically, under reasonable load (≤50%), η=99% slightly improves the long flow FCT, while slightly degrades short flow FCT. At extremely high load (70%), η=99%'s improvement of the long flow FCT is more evident for HPCC-INT, but not HPCC-PINT. This is because HPCC-INT has more overhead from the INT header, so it is closer to the limit of network load than HPCC-PINT. The closer to the limit, the more improvement brought by the extra bandwidth. Therefore, we do expect at even higher loads, η=99% also brings more improvement to HPCC-PINT, although such high loads are very unlikely in practice.

<table>
    <tr>
        <td></td>
        <td align="center"><b>50-th percentile</b></td>
        <td align="center"><b>95-th percentile</b></td>
        <td align="center"><b>99-th percentile</b></td>
    </tr>
    <tr>
        <td><b>Web search 30% load</b></td>
        <td align="center"><img src="figs/fct_wb30_diffUtgt_50pct.png" width="100%"> </td>
        <td align="center"><img src="figs/fct_wb30_diffUtgt_95pct.png" width="100%"> </td>
        <td align="center"><img src="figs/fct_wb30_diffUtgt_99pct.png" width="100%"> </td>
    </tr>
    <tr>
        <td><b>Web search 50% load</b></td>
        <td><img src="figs/fct_wb50_diffUtgt_50pct.png" width="100%"></td>
        <td><img src="figs/fct_wb50_diffUtgt_95pct.png" width="100%"></td>
        <td><img src="figs/fct_wb50_diffUtgt_99pct.png" width="100%"></td>
    </tr>
    <tr>
        <td><b>Web search 70% load</b></td>
        <td><img src="figs/fct_wb70_diffUtgt_50pct.png" width="100%"></td>
        <td><img src="figs/fct_wb70_diffUtgt_95pct.png" width="100%"></td>
        <td><img src="figs/fct_wb70_diffUtgt_99pct.png" width="100%"></td>
    </tr>
    <tr>
        <td><b>Hadoop 30% load</b></td>
        <td><img src="figs/fct_fb30_diffUtgt_50pct.png" width="100%"></td>
        <td><img src="figs/fct_fb30_diffUtgt_95pct.png" width="100%"></td>
        <td><img src="figs/fct_fb30_diffUtgt_99pct.png" width="100%"></td>
    </tr>
    <tr>
        <td><b>Hadoop 50% load</b></td>
        <td><img src="figs/fct_fb50_diffUtgt_50pct.png" width="100%"></td>
        <td><img src="figs/fct_fb50_diffUtgt_95pct.png" width="100%"></td>
        <td><img src="figs/fct_fb50_diffUtgt_99pct.png" width="100%"></td>
    </tr>
    <tr>
        <td><b>Hadoop 70% load</b></td>
        <td><img src="figs/fct_fb70_diffUtgt_50pct.png" width="100%"></td>
        <td><img src="figs/fct_fb70_diffUtgt_95pct.png" width="100%"></td>
        <td><img src="figs/fct_fb70_diffUtgt_99pct.png" width="100%"></td>
    </tr>
</table>

## Settings
We run in [ns3 simulation](https://github.com/alibaba-edu/High-Precision-Congestion-Control), with the same topology and configurations of CC schemes as those in the [HPCC paper](https://liyuliang001.github.io/publications/hpcc.pdf). In the paper, we already show that DCQCN+w and TIMELY+w is much better than DCQCN and TIMELY, so here we only compare with their +w versions. 

For HPCC, we run at η=99%; in our paper we evaluate η=95% because other proposals typically use 95% [[RCP](http://yuba.stanford.edu/~nanditad/thesis-NanditaD.pdf),[HULL](https://www.usenix.org/system/files/conference/nsdi12/nsdi12-final187.pdf)], but now we find that η=99% does not introduce any problems and improves link utilization, which is important at high network load. Besides, HPCC's maxStage=0, which is the same as the simulation setting in our paper. The maxStage=5 in the HPCC paper is due to a typo for the simulation.

*Network load* definition: The network load we evaluate (30%, 50%, 70%) are defined as the ratio between the number of total application bytes (payload) per second to the bandwidth. So the actual load to the network is ~11% higher (each data packet has 50-byte header (HPCC has more due to the INT header) and 60-byte ACK, so for 1 KB paylaod, 110 bytes are 11% more). Other papers may have different definitions.

*Flow completion time (FCT)* definition: a flow's FCT is defined as the time period from when the flow is created to when the last byte is acknowledged at the sender.

*Slowdown* definition: a flow's slowdown is calcualted as the actual FCT normalized by it ideal FCT when the network only has this flow and the packets only have basic headers (i.e., no INT header).
