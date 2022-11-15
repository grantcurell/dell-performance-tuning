
## Memory Settings

### Dram Refresh Delay

#### Original Sources

https://utaharch.blogspot.com/2013/11/a-dram-refresh-tutorial.html
https://electronics.stackexchange.com/a/454717/279031
https://my.eng.utah.edu/~cs7810/pres/11-7810-12.pdf (page 3 in particular)
https://www.micron.com/-/media/client/global/documents/products/data-sheet/dram/ddr4/16gb_ddr4_sdram.pdf

#### What is a DRAM Refresh

The charge on a DRAM cell weakens over time.  The DDR standard requires every cell to be refreshed within a 64 ms interval, referred to as the retention time.  At temperatures higher than 85° C (referred to as extended temperature range), the retention time is halved to 32 ms to account for the higher leakage rate.  The refresh of a memory rank is partitioned into 8,192 smaller refresh operations.  One such refresh operation has to be issued every 7.8 µs (64 ms/8192).  This 7.8 µs interval is referred to as the refresh interval, tREFI.  The DDR3 standard requires that eight refresh operations be issued within a time window equal to 8 x tREFI, giving the memory controller some flexibility when scheduling these refresh operations.  Refresh operations are issued at rank granularity in DDR3 and DDR4.  Before issuing a refresh operation, the memory controller precharges all banks in the rank.  It then issues a single refresh command to the rank.  DRAM chips maintain a row counter to keep track of the last row that was refreshed -- this row counter is used to determine the rows that must be refreshed next.

#### tRFC and recovery time

Upon receiving a refresh command, the DRAM chips enter a refresh mode that has been carefully designed to perform the maximum amount of cell refresh in as little time as possible.  During this time, the current carrying capabilities of the power delivery network and the charge pumps are stretched to the limit.  The operation lasts for a time referred to as the refresh cycle time, tRFC.  Towards the end of this period, the refresh process starts to wind down and some recovery time is provisioned so that the banks can be precharged and charge is restored to the charge pumps.  Providing this recovery time at the end allows the memory controller to resume normal operation at the end of tRFC.  Without this recovery time, the memory controller would require a new set of timing constraints that allow it to gradually ramp up its operations in parallel with charge pump restoration.  Since such complexity can't be expected of every memory controller, the DDR standards include the recovery time in the tRFC specification.  As soon as the tRFC time elapses, the memory controller can issue four consecutive Activate commands to different banks in the rank.

#### Refresh penalty

On average, in every tREFI window, the rank is unavailable for a time equal to tRFC.  So for a memory-bound application on a 1-rank memory system, the percentage of execution time that can be attributed to refresh (the refresh penalty) is tRFC/tREFI.  In reality, the refresh penalty can be a little higher because directly prior to the refresh operation, the memory controller wastes some time precharging all the banks.  Also, right after the refresh operation, since all rows are closed, the memory controller has to issue a few Activates to re-populate the row buffers.  These added delays can grow the refresh penalty from (say) 8% in a 32 Gb chip to 9%.  The refresh penalty can also be lower than the tRFC/tREFI ratio if the processors can continue to execute independent instructions in their reorder buffers while the memory system is unavailable.  In a multi-rank memory system, the refresh penalty depends on whether ranks are refreshed together or in a staggered manner.  If ranks are refreshed together, the refresh penalty, as above, is in the neighborhood of tRFC/tREFI.  If ranks are refreshed in a staggered manner, the refresh penalty can be greater.  Staggered refresh is frequently employed because it reduces the memory's peak power requirement.


### Memory Operating Mode

> the DRAM controllers operate independently in 64-bit mode and provide optimized memory performance.

What does this mean?

### Memory Interleaving

TODO - need to bring in docs

### Opportunistic Self Refresh

[TODO need to study](https://infohub.delltechnologies.com/l/day-three-best-practices-8/poweredge-dram-refresh-delay-and-opportunistic-self-refresh)

### DIMM Self Healing (Post Package Repair) on Uncorrectable Memory Error

[TODO need to study](https://www.dell.com/support/kbdoc/en-us/000053203/what-is-ddr4-self-healing-on-dell-poweredge-servers-with-intel-xeon-scalable-processors)

## Processor Settings

### Logical Processor

In what specific scenarios would it be worth disabling? The help says:

> However, there are some floating point/scientific workloads, including HPC workloads, where disabling this feature may result in higher performance.

How do I know when I'm in those scenarios

### IOMMU Support

TODO - what is it?
Why is this fixed?

### Kernel DMA Protection

TODO: https://learn.microsoft.com/en-us/windows/security/information-protection/kernel-dma-protection-for-thunderbolt

### L1 Stream HW Prefetcher

https://www.reddit.com/r/Amd/comments/7hhj0s/l1l2_stream_hw_prefetcher/

> Suppose you were working on a big data set that you processed in a repeatable (and detectable) pattern, the CPU would identify that access pattern as a 'stream' of accesses and start speculatively bringing that data into the L1 (high confidence) or L2 (lower confidence) caches. I'm not sure if there is any information on Ryzen's prefetcher, but you can easily find plenty of papers on the topic. Jouppi's stream buffer paper from 1990 is a good place to start and should be fairly readable if you are from a CS/ECE background.

https://ieeexplore.ieee.org/abstract/document/134547

### L2 Stream HW Prefetcher

https://stackoverflow.com/questions/60027056/is-l2-hw-prefetcher-really-helpful

### MADT Core Enumeration

TODO - https://learn.microsoft.com/en-us/windows-hardware/drivers/bringup/acpi-system-description-tables

### NUMA Nodes Per Socket

TODO - write description

### Minimum SEV non-ES ASID

TODO

### Transparent Secure Memory Encryption

TODO - same as the intel thing? How much would it impact performance?

### Configurable TDP

TODO

### x2APIC Mode

TODO

### Number of CCDs per Processor

When wouldn't I want this to be maxed?

### Number of Cores per CCD

When wouldn't I want this to be maxed?