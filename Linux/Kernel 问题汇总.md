# Kernel 问题汇总

- "Kernel panic - not syncing: Hard LOCKUP"
- "Kernel panic - not syncing: Fatal hardware error!"
- "INFO: task `<process>`:`<pid>` blocked for more than 120 seconds"
- "BUG: unable to handle kernel NULL pointer dereference at 0000000000000010"
- "kernel: EPT: Misconfiguration"


----------


## "Kernel panic - not syncing: Hard LOCKUP"

### 故障信息

在 vmcore-dmesg.txt 中有如下输出

```
[2704379.696670] IPv6: ADDRCONF(NETDEV_CHANGE): veth8b9af39: link becomes ready
[2704379.696698] dockermesos: port 7(veth8b9af39) entered forwarding state
[2704379.696704] dockermesos: port 7(veth8b9af39) entered forwarding state
[2704379.950037] dockermesos: port 7(veth8b9af39) entered disabled state
[2704379.980383] dockermesos: port 7(veth8b9af39) entered disabled state
[2704379.980606] device veth8b9af39 left promiscuous mode
[2704379.980613] dockermesos: port 7(veth8b9af39) entered disabled state
[2704380.002151] XFS (dm-9): Unmounting Filesystem
（上面的日志和内核问题无关）
[2704411.028804] ------------[ cut here ]------------
[2704411.028815] WARNING: at net/sched/sch_generic.c:297 dev_watchdog+0x276/0x280()
[2704411.028817] NETDEV WATCHDOG: enp2s0f1 (ixgbe): transmit queue 6 timed out
[2704411.028818] Modules linked in: binfmt_misc veth nf_conntrack_netlink nfnetlink iptable_filter xt_conntrack nf_nat xfs tcp_diag inet_diag dm_thin_pool dm_persistent_data dm_bio_prison dm_bufio libcrc32c bridge stp llc ipmi_si ipmi_devintf ipmi_msghandler bonding dm_mirror dm_region_hash dm_log dm_mod intel_powerclamp coretemp intel_rapl iosf_mbi kvm_intel kvm irqbypass crc32_pclmul ghash_clmulni_intel aesni_intel lrw gf128mul glue_helper ablk_helper cryptd i2c_i801 i2c_core pcspkr iTCO_wdt iTCO_vendor_support sb_edac edac_core shpchp mei_me lpc_ich mei acpi_power_meter ip_tables ext4 mbcache jbd2 sd_mod crc_t10dif crct10dif_generic crct10dif_pclmul crct10dif_common crc32c_intel ahci ixgbe libahci mdio libata ptp pps_core megaraid_sas dca fjes sg nf_conntrack
[2704411.028859] CPU: 23 PID: 0 Comm: swapper/23 Tainted: G    B          ------------   3.10.0-514.26.2.el7.x86_64 #1
[2704411.028860] Hardware name: Huawei RH1288 V3/BC11HGSC0, BIOS 3.57 02/26/2017
[2704411.028861]  ffff88203f043d88 5f237ad32f0df3b0 ffff88203f043d40 ffffffff81687133
[2704411.028863]  ffff88203f043d78 ffffffff81085cb0 0000000000000006 ffff88103afa0000
[2704411.028865]  ffff88103af9cf40 0000000000000040 0000000000000017 ffff88203f043de0
[2704411.028867] Call Trace:
[2704411.028868]  <IRQ>  [<ffffffff81687133>] dump_stack+0x19/0x1b
[2704411.028879]  [<ffffffff81085cb0>] warn_slowpath_common+0x70/0xb0
[2704411.028880]  [<ffffffff81085d4c>] warn_slowpath_fmt+0x5c/0x80
[2704411.028883]  [<ffffffff81597286>] dev_watchdog+0x276/0x280
[2704411.028886]  [<ffffffff81597010>] ? dev_graft_qdisc+0x80/0x80
[2704411.028890]  [<ffffffff81095eb6>] call_timer_fn+0x36/0x110
[2704411.028891]  [<ffffffff81597010>] ? dev_graft_qdisc+0x80/0x80
[2704411.028894]  [<ffffffff81098ba7>] run_timer_softirq+0x237/0x340
[2704411.028898]  [<ffffffff8108f63f>] __do_softirq+0xef/0x280
[2704411.028902]  [<ffffffff8169929c>] call_softirq+0x1c/0x30
[2704411.028906]  [<ffffffff8102d365>] do_softirq+0x65/0xa0
[2704411.028908]  [<ffffffff8108f9d5>] irq_exit+0x115/0x120
[2704411.028910]  [<ffffffff81699f15>] smp_apic_timer_interrupt+0x45/0x60
[2704411.028912]  [<ffffffff8169845d>] apic_timer_interrupt+0x6d/0x80
[2704411.028913]  <EOI>  [<ffffffff81060fe6>] ? native_safe_halt+0x6/0x10
[2704411.028921]  [<ffffffff810347ff>] default_idle+0x1f/0xc0
[2704411.028923]  [<ffffffff81035146>] arch_cpu_idle+0x26/0x30
[2704411.028929]  [<ffffffff810e82f5>] cpu_startup_entry+0x245/0x290
[2704411.028934]  [<ffffffff8104f0da>] start_secondary+0x1ba/0x230
[2704411.028935] ---[ end trace 711f66d1ee31804f ]---
[2704411.028938] ixgbe 0000:02:00.1 enp2s0f1: initiating reset due to tx timeout
[2704411.028958] ixgbe 0000:02:00.1 enp2s0f1: Reset adapter
[2704411.551817] ixgbe 0000:02:00.1 enp2s0f1: detected SFP+: 6
[2704411.552004] ixgbe 0000:02:00.1 enp2s0f1: initiating reset to clear Tx work after link loss
[2704411.584813] ixgbe 0000:02:00.1 enp2s0f1: Reset adapter
[2704412.612334] NMI watchdog: Watchdog detected hard LOCKUP on cpu 7
[2704412.612350] Modules linked in:
[2704412.612531]  binfmt_misc veth nf_conntrack_netlink nfnetlink iptable_filter xt_conntrack nf_nat xfs tcp_diag inet_diag dm_thin_pool dm_persistent_data dm_bio_prison dm_bufio libcrc32c bridge stp llc ipmi_si ipmi_devintf ipmi_msghandler bonding dm_mirror dm_region_hash dm_log dm_mod intel_powerclamp coretemp intel_rapl iosf_mbi kvm_intel kvm irqbypass crc32_pclmul ghash_clmulni_intel aesni_intel lrw gf128mul glue_helper ablk_helper cryptd i2c_i801 i2c_core pcspkr iTCO_wdt iTCO_vendor_support sb_edac edac_core shpchp mei_me lpc_ich mei acpi_power_meter ip_tables ext4 mbcache jbd2 sd_mod crc_t10dif crct10dif_generic crct10dif_pclmul crct10dif_common crc32c_intel ahci ixgbe libahci mdio libata ptp pps_core megaraid_sas dca fjes sg nf_conntrack
[2704412.612570] CPU: 7 PID: 0 Comm: swapper/7 Tainted: G    B   W      ------------   3.10.0-514.26.2.el7.x86_64 #1
[2704412.612572] Hardware name: Huawei RH1288 V3/BC11HGSC0, BIOS 3.57 02/26/2017
[2704412.612573] task: ffff880169986dd0 ti: ffff8801699a0000 task.ti: ffff8801699a0000
[2704412.612575] RIP: 0010:[<ffffffff81060fe6>]  [<ffffffff81060fe6>] native_safe_halt+0x6/0x10
[2704412.612581] RSP: 0018:ffff8801699a3e98  EFLAGS: 00000286
[2704412.612582] RAX: 00000000ffffffed RBX: ffff88103fbcd080 RCX: 0100000000000000
[2704412.612583] RDX: 0000000000000000 RSI: 0000000000000000 RDI: 0000000000000046
[2704412.612584] RBP: ffff8801699a3e98 R08: 0000000000000000 R09: 0000000000000000
[2704412.612585] R10: 0000000000000000 R11: 0000000000000000 R12: 00099b9bb0645f00
[2704412.612586] R13: ffff88103fbcfde0 R14: f21bf8c4662d3c34 R15: 0000000000000082
[2704412.612587] FS:  0000000000000000(0000) GS:ffff88103fbc0000(0000) knlGS:0000000000000000
[2704412.612588] CS:  0010 DS: 0000 ES: 0000 CR0: 0000000080050033
[2704412.612589] CR2: 00000007480c6820 CR3: 000000202b6e3000 CR4: 00000000003407e0
[2704412.612590] DR0: 0000000000000000 DR1: 0000000000000000 DR2: 0000000000000000
[2704412.612591] DR3: 0000000000000000 DR6: 00000000fffe0ff0 DR7: 0000000000000400
[2704412.612592] Stack:
[2704412.612592]  ffff8801699a3eb8 ffffffff810347ff ffff8801699a0000 ffffffff81ae6300
[2704412.612594]  ffff8801699a3ec8 ffffffff81035146 ffff8801699a3f20 ffffffff810e82f5
[2704412.612596]  ffff8801699a3fd8 ffff8801699a0000 f21bf8c4662d3c34 ec27dbe17bf619e7
[2704412.612597] Call Trace:
[2704412.612603]  [<ffffffff810347ff>] default_idle+0x1f/0xc0
[2704412.612605]  [<ffffffff81035146>] arch_cpu_idle+0x26/0x30
[2704412.612609]  [<ffffffff810e82f5>] cpu_startup_entry+0x245/0x290
[2704412.612613]  [<ffffffff8104f0da>] start_secondary+0x1ba/0x230
[2704412.612614] Code: 00 00 00 00 00 55 48 89 e5 fa 5d c3 66 0f 1f 84 00 00 00 00 00 55 48 89 e5 fb 5d c3 66 0f 1f 84 00 00 00 00 00 55 48 89 e5 fb f4 <5d> c3 0f 1f 84 00 00 00 00 00 55 48 89 e5 f4 5d c3 66 0f 1f 84
（这里是个分界点）
[2704412.612632] Kernel panic - not syncing: Hard LOCKUP
[2704412.612820] CPU: 7 PID: 0 Comm: swapper/7 Tainted: G    B   W      ------------   3.10.0-514.26.2.el7.x86_64 #1
[2704412.613179] Hardware name: Huawei RH1288 V3/BC11HGSC0, BIOS 3.57 02/26/2017
[2704412.613342]  ffffffff818daacd f21bf8c4662d3c34 ffff88103fbc5b18 ffffffff81687133
[2704412.613661]  ffff88103fbc5b98 ffffffff8168053a 0000000000000010 ffff88103fbc5ba8
[2704412.613979]  ffff88103fbc5b48 f21bf8c4662d3c34 ffff88103fbc5ba8 ffffffff818da7d3
[2704412.614297] Call Trace:
[2704412.614449]  <NMI>  [<ffffffff81687133>] dump_stack+0x19/0x1b
[2704412.614620]  [<ffffffff8168053a>] panic+0xe3/0x1f2
[2704412.614788]  [<ffffffff81085abf>] nmi_panic+0x3f/0x40
[2704412.614980]  [<ffffffff8112f879>] watchdog_overflow_callback+0xf9/0x100
[2704412.615174]  [<ffffffff81174d2e>] __perf_event_overflow+0x8e/0x1f0
[2704412.615365]  [<ffffffff81175974>] perf_event_overflow+0x14/0x20
[2704412.615555]  [<ffffffff81009d88>] intel_pmu_handle_irq+0x1f8/0x4e0
[2704412.619641]  [<ffffffff8131a78c>] ? ioremap_page_range+0x27c/0x3e0
[2704412.619833]  [<ffffffff811bf684>] ? vunmap_page_range+0x1c4/0x310
[2704412.620024]  [<ffffffff811bf7e1>] ? unmap_kernel_range_noflush+0x11/0x20
[2704412.620221]  [<ffffffff813ca194>] ? ghes_copy_tofrom_phys+0x124/0x210
[2704412.620411]  [<ffffffff813ca320>] ? ghes_read_estatus+0xa0/0x190
[2704412.620607]  [<ffffffff8168ed6b>] perf_event_nmi_handler+0x2b/0x50
[2704412.620798]  [<ffffffff816901b7>] nmi_handle.isra.0+0x87/0x160
[2704412.620989]  [<ffffffff816903c3>] do_nmi+0x133/0x410
[2704412.621176]  [<ffffffff8168f5d3>] end_repeat_nmi+0x1e/0x2e
[2704412.621362]  [<ffffffff810ce3cf>] ? update_curr+0xf/0x190
[2704412.621542]  [<ffffffff810ce3cf>] ? update_curr+0xf/0x190
[2704412.621729]  [<ffffffff810ce3cf>] ? update_curr+0xf/0x190
[2704412.621914]  <<EOE>>  <IRQ>  [<ffffffff810d042d>] enqueue_entity+0x3d/0xb60
[2704412.622115]  [<ffffffff810d16f4>] unthrottle_cfs_rq+0xe4/0x170
[2704412.622307]  [<ffffffff810d1932>] distribute_cfs_runtime+0xf2/0x100
[2704412.622500]  [<ffffffff810d1acf>] sched_cfs_period_timer+0xcf/0x160
[2704412.622692]  [<ffffffff810d1a00>] ? sched_cfs_slack_timer+0xc0/0xc0
[2704412.622882]  [<ffffffff810b4d72>] __hrtimer_run_queues+0xd2/0x260
[2704412.623071]  [<ffffffff810b5310>] hrtimer_interrupt+0xb0/0x1e0
[2704412.623259]  [<ffffffff81051037>] local_apic_timer_interrupt+0x37/0x60
[2704412.623450]  [<ffffffff81699f0f>] smp_apic_timer_interrupt+0x3f/0x60
[2704412.623635]  [<ffffffff8169845d>] apic_timer_interrupt+0x6d/0x80
[2704412.623819]  <EOI>  [<ffffffff81060fe6>] ? native_safe_halt+0x6/0x10
[2704412.624012]  [<ffffffff810347ff>] default_idle+0x1f/0xc0
[2704412.624197]  [<ffffffff81035146>] arch_cpu_idle+0x26/0x30
[2704412.624386]  [<ffffffff810e82f5>] cpu_startup_entry+0x245/0x290
[2704412.624574]  [<ffffffff8104f0da>] start_secondary+0x1ba/0x230
```

> 补充说明：
>
> - 上述内容中出现问号的部分，如 `? update_curr+0xf/0x190` ，可能和 bt 中的输出不完全对应；


### 系统信息

```
[root@xg-mesos-142 ~]# uname -a
Linux xg-mesos-142 3.10.0-514.26.2.el7.x86_64 #1 SMP Tue Jul 4 15:04:05 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
[root@xg-mesos-142 ~]# lsb_release -a
LSB Version:    :core-4.1-amd64:core-4.1-noarch
Distributor ID: CentOS
Description:    CentOS Linux release 7.3.1611 (Core)
Release:    7.3.1611
Codename:   Core
[root@xg-mesos-142 ~]# lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                56
On-line CPU(s) list:   0-55
Thread(s) per core:    2
Core(s) per socket:    14
Socket(s):             2
NUMA node(s):          2
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 79
Model name:            Intel(R) Xeon(R) CPU E5-2680 v4 @ 2.40GHz
Stepping:              1
CPU MHz:               2899.875
BogoMIPS:              4794.33
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              35840K
NUMA node0 CPU(s):     0-13,28-41
NUMA node1 CPU(s):     14-27,42-55
[root@xg-mesos-142 ~]#
```

网卡信息

```
[root@xg-mesos-142 ~]# ethtool -i enp2s0f0
driver: ixgbe
version: 4.4.0-k-rh7.3
firmware-version: 0x800003df
expansion-rom-version:
bus-info: 0000:02:00.0
supports-statistics: yes
supports-test: yes
supports-eeprom-access: yes
supports-register-dump: yes
supports-priv-flags: no
[root@xg-mesos-142 ~]# ethtool -i enp2s0f1
driver: ixgbe
version: 4.4.0-k-rh7.3
firmware-version: 0x800003df
expansion-rom-version:
bus-info: 0000:02:00.1
supports-statistics: yes
supports-test: yes
supports-eeprom-access: yes
supports-register-dump: yes
supports-priv-flags: no
```


### 故障原因

- There is a bug that should be fixed by a patch from upstream commit [c06f04c70489b9deea3212af8375e2f0c2f0b184](https://git.kernel.org/pub/scm/linux/kernel/git/tip/tip.git/commit/?id=c06f04c70489b9deea3212af8375e2f0c2f0b184).
- `distribute_cfs_runtime()` intentionally only hands out enough runtime to bring each `cfs_rq` to 1 ns of runtime, expecting the `cfs_rqs` to then take the runtime they need only once they actually get to run. However, if they get to run sufficiently quickly, the period timer is still in `distribute_cfs_runtime()` and no runtime is available, causing them to throttle. Then distribute has to handle them again, and this can go on until distribute has handed out all of the runtime 1ns at a time, which takes far too long.
- **Hard lockup occurs due to an infinite loop encountered in `distribute_cfs_runtime()`**.

> Our engineering team is working on this bug on the private rhbz#1399391.


### 解决方案

- Issue has been fixed in kernel-3.10.0-693.el7.x86_64.rpm via [https://access.redhat.com/errata/RHSA-2017:1842](https://access.redhat.com/errata/RHSA-2017:1842)


### 其它

Hard LOCKUP 是因为中断被禁掉了（关中断），长时间（默认应该是 5秒）没有打开，这时 NMI 会中断当前进程；

所以关键在于：目标进程为什么长时间关闭中断，可能的一种情况：有进程拿了锁，忘记放锁，而另一个线程调用 spin_lock_irqsave() ，该函数会先关闭中断再去申请锁，如果一直拿不到锁，就会触发NMI watchdog 中断行为；

问题分析办法：基于 crash 工具对所有进程栈执行 foreach bt，看是否有进程处于锁内的情况，如果有，分析为什么长时间没有放锁；如果没有进程在锁内，则需要遍历代码，查找所有使用该锁的地方，看是否有忘记放锁的流程。

对于 Hard LOCKUP 而言，是指在关中断情况下发生死锁（大多数情况都是在硬中断中），不一定是真的死锁，实际中如果关中断时间太长 ，就会触发 hard panic。

Soft LOCKUP 是由于抢占被长时间关闭，系统无法正常调度其他进程。这时 NMI watchdog 会中断该线程；

soft LOCKUP 其实就是在没有关中断的情况下发生了 lockup，由于在没有关闭硬中断的情况下，正常的话时钟中断应该会被及时响应，时钟中断的处理函数会触发 kernel watchdog 更新时间戳。如果这种情况下系统非常繁忙以至时钟中断都响应不及时，那么将造成 kernel watchdog 时间戳间隔过大，那么他将发出侦测到 soft lockup 的情况。


相关内核参数设置：

```
kernel.hardlockup_all_cpu_backtrace = 0
kernel.hardlockup_panic = 1
kernel.softlockup_all_cpu_backtrace = 0
kernel.softlockup_panic = 0

kernel.nmi_watchdog = 1
kernel.watchdog = 1
kernel.watchdog_cpumask = 0-55
kernel.watchdog_thresh = 10
```

相关链接：

- [panic 流程](https://lp007819.wordpress.com/2012/04/28/%E8%BD%ACpanic-%E6%B5%81%E7%A8%8B/)
- [Kernel panic - not syncing: Watchdog detected hard LOCKUP on cpu 28](https://bugs.centos.org/view.php?id=11488)
- [关于panic之LOCKUP](http://www.cnblogs.com/jawfeng/p/4938792.html)
- [Kernel panic - not syncing: Watchdog detected hard LOCKUP on cpu 1](https://bugs.centos.org/view.php?id=10965)
- [Kernel panic - not syncing: Hard LOCKUP](https://bugs.centos.org/view.php?id=13497) -- 和我们的问题机器一样的版本
- [Server crashed](https://www.centos.org/forums/viewtopic.php?t=56511)
- [Hard lockup occurs due to an infinite loop encountered in distribute_cfs_runtime()](https://access.redhat.com/solutions/2786591)


----------


## "Kernel panic - not syncing: Fatal hardware error!"

### 故障信息

```
[root@sh-talos-hmaster2-128-101.elenet.me 127.0.0.1-2016-10-26-21:22:34]# crash /usr/lib/debug/lib/modules/2.6.32-504.el6.x86_64/vmlinux vmcore

crash 6.1.0-5.el6
Copyright (C) 2002-2012  Red Hat, Inc.
Copyright (C) 2004, 2005, 2006, 2010  IBM Corporation
Copyright (C) 1999-2006  Hewlett-Packard Co
Copyright (C) 2005, 2006, 2011, 2012  Fujitsu Limited
Copyright (C) 2006, 2007  VA Linux Systems Japan K.K.
Copyright (C) 2005, 2011  NEC Corporation
Copyright (C) 1999, 2002, 2007  Silicon Graphics, Inc.
Copyright (C) 1999, 2000, 2001, 2002  Mission Critical Linux, Inc.
This program is free software, covered by the GNU General Public License,
and you are welcome to change it and/or distribute copies of it under
certain conditions.  Enter "help copying" to see the conditions.
This program has absolutely no warranty.  Enter "help warranty" for details.

GNU gdb (GDB) 7.3.1
Copyright (C) 2011 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-unknown-linux-gnu"...

      KERNEL: /usr/lib/debug/lib/modules/2.6.32-504.el6.x86_64/vmlinux
    DUMPFILE: vmcore  [PARTIAL DUMP]
        CPUS: 32
        DATE: Wed Oct 26 21:20:43 2016
      UPTIME: 18 days, 06:59:05
LOAD AVERAGE: 2.03, 1.93, 1.68
       TASKS: 1003
    NODENAME: sh-talos-hmaster2-128-101.elenet.me
     RELEASE: 2.6.32-504.el6.x86_64
     VERSION: #1 SMP Wed Oct 15 04:27:16 UTC 2014
     MACHINE: x86_64  (2594 Mhz)
      MEMORY: 95.6 GB
       PANIC: "Kernel panic - not syncing: Fatal hardware error!"
         PID: 0
     COMMAND: "swapper"
        TASK: ffffffff81a8d020  (1 of 32)  [THREAD_INFO: ffffffff81a00000]
         CPU: 0
       STATE: TASK_RUNNING (PANIC)

crash>
crash>
crash> bt
PID: 0      TASK: ffffffff81a8d020  CPU: 0   COMMAND: "swapper"
 #0 [ffff880028207cc0] machine_kexec at ffffffff8103b68b
 #1 [ffff880028207d20] crash_kexec at ffffffff810c9852
 #2 [ffff880028207df0] panic at ffffffff815292c3
 #3 [ffff880028207e70] ghes_notify_nmi at ffffffff8131c171
 #4 [ffff880028207ea0] notifier_call_chain at ffffffff81530075
 #5 [ffff880028207ee0] atomic_notifier_call_chain at ffffffff815300da
 #6 [ffff880028207ef0] notify_die at ffffffff810a4eae
 #7 [ffff880028207f20] do_nmi at ffffffff8152dd69
 #8 [ffff880028207f50] nmi at ffffffff8152d600
    [exception RIP: native_safe_halt+11]
    RIP: ffffffff81040f8b  RSP: ffffffff81a01ea8  RFLAGS: 00000246
    RAX: 0000000000000000  RBX: 0000000000000000  RCX: 0000000000000000
    RDX: 0000000000000000  RSI: 0000000000000001  RDI: ffffffff81ded228
    RBP: ffffffff81a01ea8   R8: 0000000000000000   R9: 0000000000000000
    R10: 00059d5266138f4b  R11: 0000000000000000  R12: ffffffff81c09ec0
    R13: 0000000000000000  R14: ffffffffffffffff  R15: ffffffff81de9000
    ORIG_RAX: ffffffffffffffff  CS: 0010  SS: 0018
--- <NMI exception stack> ---
 #9 [ffffffff81a01ea8] native_safe_halt at ffffffff81040f8b
#10 [ffffffff81a01eb0] default_idle at ffffffff8101687d
#11 [ffffffff81a01ed0] cpu_idle at ffffffff81009fc6
crash>
crash> log
...
{1}[Hardware Error]: Hardware error from APEI Generic Hardware Error Source: 1
{1}[Hardware Error]: event severity: fatal
{1}[Hardware Error]:  Error 0, type: fatal
{1}[Hardware Error]:   section_type: PCIe error
{1}[Hardware Error]:   port_type: 4, root port
{1}[Hardware Error]:   version: 1.16
{1}[Hardware Error]:   command: 0x0547, status: 0x4010
{1}[Hardware Error]:   device_id: 0000:00:03.0
{1}[Hardware Error]:   slot: 0
{1}[Hardware Error]:   secondary_bus: 0x04
{1}[Hardware Error]:   vendor_id: 0x8086, device_id: 0x2f08
{1}[Hardware Error]:   class_code: 040000
{1}[Hardware Error]:   bridge: secondary_status: 0x0000, control: 0x0003
Kernel panic - not syncing: Fatal hardware error!
Pid: 0, comm: swapper Not tainted 2.6.32-504.el6.x86_64 #1
Call Trace:
 <NMI>  [<ffffffff815292bc>] ? panic+0xa7/0x16f
 [<ffffffff8131c171>] ? ghes_notify_nmi+0x241/0x250
 [<ffffffff81530075>] ? notifier_call_chain+0x55/0x80
 [<ffffffff815300da>] ? atomic_notifier_call_chain+0x1a/0x20
 [<ffffffff810a4eae>] ? notify_die+0x2e/0x30
 [<ffffffff8152dd69>] ? do_nmi+0x1e9/0x340
 [<ffffffff8152d600>] ? nmi+0x20/0x30
 [<ffffffff81040f8b>] ? native_safe_halt+0xb/0x10
 <<EOE>>  [<ffffffff8101687d>] ? default_idle+0x4d/0xb0
 [<ffffffff81009fc6>] ? cpu_idle+0xb6/0x110
 [<ffffffff8151061a>] ? rest_init+0x7a/0x80
 [<ffffffff81c2af8f>] ? start_kernel+0x424/0x430
 [<ffffffff81c2a33a>] ? x86_64_start_reservations+0x125/0x129
 [<ffffffff81c2a453>] ? x86_64_start_kernel+0x115/0x124
crash>
```

同样，可以在 `vmcore-dmesg.txt` 的最后可以看到上述信息

```
...
<0>{1}[Hardware Error]: Hardware error from APEI Generic Hardware Error Source: 1
<0>{1}[Hardware Error]: event severity: fatal
<0>{1}[Hardware Error]:  Error 0, type: fatal
<0>{1}[Hardware Error]:   section_type: PCIe error
<0>{1}[Hardware Error]:   port_type: 4, root port
<0>{1}[Hardware Error]:   version: 1.16
<0>{1}[Hardware Error]:   command: 0x0547, status: 0x4010
<0>{1}[Hardware Error]:   device_id: 0000:00:03.0
<0>{1}[Hardware Error]:   slot: 0
<0>{1}[Hardware Error]:   secondary_bus: 0x04
<0>{1}[Hardware Error]:   vendor_id: 0x8086, device_id: 0x2f08
<0>{1}[Hardware Error]:   class_code: 040000
<0>{1}[Hardware Error]:   bridge: secondary_status: 0x0000, control: 0x0003
<0>Kernel panic - not syncing: Fatal hardware error!
<4>Pid: 0, comm: swapper Not tainted 2.6.32-504.el6.x86_64 #1
<4>Call Trace:
<4> <NMI>  [<ffffffff815292bc>] ? panic+0xa7/0x16f
<4> [<ffffffff8131c171>] ? ghes_notify_nmi+0x241/0x250
<4> [<ffffffff81530075>] ? notifier_call_chain+0x55/0x80
<4> [<ffffffff815300da>] ? atomic_notifier_call_chain+0x1a/0x20
<4> [<ffffffff810a4eae>] ? notify_die+0x2e/0x30
<4> [<ffffffff8152dd69>] ? do_nmi+0x1e9/0x340
<4> [<ffffffff8152d600>] ? nmi+0x20/0x30
<4> [<ffffffff81040f8b>] ? native_safe_halt+0xb/0x10
<4> <<EOE>>  [<ffffffff8101687d>] ? default_idle+0x4d/0xb0
<4> [<ffffffff81009fc6>] ? cpu_idle+0xb6/0x110
<4> [<ffffffff8151061a>] ? rest_init+0x7a/0x80
<4> [<ffffffff81c2af8f>] ? start_kernel+0x424/0x430
<4> [<ffffffff81c2a33a>] ? x86_64_start_reservations+0x125/0x129
<4> [<ffffffff81c2a453>] ? x86_64_start_kernel+0x115/0x124
```

### 系统信息

```
[root@sh-talos-hmaster2-128-101.elenet.me ~]# uname -a
Linux sh-talos-hmaster2-128-101.elenet.me 2.6.32-504.el6.x86_64 #1 SMP Wed Oct 15 04:27:16 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
[root@sh-talos-hmaster2-128-101.elenet.me ~]#
[root@sh-talos-hmaster2-128-101.elenet.me ~]# lsb_release -a
LSB Version:    :base-4.0-amd64:base-4.0-noarch:core-4.0-amd64:core-4.0-noarch
Distributor ID: CentOS
Description:    CentOS release 6.6 (Final)
Release:    6.6
Codename:   Final
[root@sh-talos-hmaster2-128-101.elenet.me ~]#
[root@sh-talos-hmaster2-128-101.elenet.me ~]# lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                32
On-line CPU(s) list:   0-31
Thread(s) per core:    2
Core(s) per socket:    8
Socket(s):             2
NUMA node(s):          2
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 63
Stepping:              2
CPU MHz:               2594.075
BogoMIPS:              5187.60
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              20480K
NUMA node0 CPU(s):     0-7,16-23
NUMA node1 CPU(s):     8-15,24-31
[root@sh-talos-hmaster2-128-101.elenet.me ~]#
```

### 故障原因

网络接口光衰异常，导致网络传输速率异常；

关键路径：

```
Kernel panic - not syncing: Fatal hardware error! -->
device_id: 0000:00:03.0 -->
lspci -tv -->
Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection -->
SFI/SFP+ -->
ethtool -m | grep power
```

相关输出

```
# lspci -tv
-+-[0000:ff]-+-08.0  Intel Corporation Xeon E5 v3/Core i7 QPI Link 0
...
 \-[0000:00]-+-00.0  Intel Corporation Xeon E5 v3/Core i7 DMI2
             +-01.0-[01]----00.0  LSI Logic / Symbios Logic MegaRAID SAS-3 3108 [Invader]
             +-02.0-[02]--+-00.0  Intel Corporation I350 Gigabit Network Connection
             |            \-00.1  Intel Corporation I350 Gigabit Network Connection
             +-02.2-[03]--
             +-03.0-[04]--+-00.0  Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection
             |            \-00.1  Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection
...
# ethtool --version
ethtool version 3.15
# ethtool -m enp2s0f0|grep power
    Laser output power                        : 0.5832 mW / -2.34 dBm   -- 1
    Receiver signal average optical power     : 0.5730 mW / -2.42 dBm   -- 2
    Laser output power high alarm             : Off
    Laser output power low alarm              : Off
    Laser output power high warning           : Off
    Laser output power low warning            : Off
    Laser rx power high alarm                 : Off
    Laser rx power low alarm                  : Off
    Laser rx power high warning               : Off
    Laser rx power low warning                : Off
    Laser output power high alarm threshold   : 1.2589 mW / 1.00 dBm
    Laser output power low alarm threshold    : 0.1175 mW / -9.30 dBm
    Laser output power high warning threshold : 0.7943 mW / -1.00 dBm
    Laser output power low warning threshold  : 0.1862 mW / -7.30 dBm
    Laser rx power high alarm threshold       : 1.2589 mW / 1.00 dBm
    Laser rx power low alarm threshold        : 0.0646 mW / -11.90 dBm
    Laser rx power high warning threshold     : 0.7943 mW / -1.00 dBm
    Laser rx power low warning threshold      : 0.1023 mW / -9.90 dBm
```

主要关注上面 1 和 2 两个和光衰相关的值；若 "Receiver signal average optical power" 小于 -7 dbm 则视为有光衰问题；


### 解决方案

确定为华为 PCI-e 万兆网卡故障，更换万兆网卡；

- 更换对应设备（例如 eth0）的光纤线缆（有 bond 用户影响较小）；
- 升级 centos 6.8 的 ethtool 程序版本（低版本 ethtool 无法检测光衰）；

检查步骤：

- 检测是否为光纤线问题（通过更换光纤线配合 ethtool 进行光衰检测）；
- 检测是否为光模块问题（通过更换故障网口的光模块进行测试验证）；
- 检测是否为万兆交换机连接问题（通过更换万兆交换机端的对应光纤连接口进行测试验证）；


### 其它

> 可以通过 `iperf` 和 `iptraf` 进行测速；

测试服务端

```
iperf -s
```

测试客户端

```
iperf -c <dst_ip>
```

在不同机器组合上，进行多组测试；

使用 iptraf 工具进行测速比较观察；


> 10G 模块经历了从 300Pin ，XENPAK，X2，XFP 的发展，最终实现了用和 SFP 一样的尺寸传输 10G 的信号，这就是 SFP+ 。SFP 凭借其小型化低成本等优势满足了设备对光模块高密度的需求，从 2002 年标准推了，到 2010 年已经取代 XFP 成为 10G 市场主流。



----------



## "INFO: task `<process>`:`<pid>` blocked for more than 120 seconds"

### 故障信息

在 `/var/log/message`、`/var/log/kern.log` 和 `dmesg` 中均报出如下（不同 distro 可能有所不同）

模式

```
err kernel: INFO: task <process>:<pid> blocked for more than 120 seconds
```

具体

```
2017-06-30 16:25:43.012 err kernel: [261108.341940] INFO: task kworker/u113:10:666 blocked for more than 120 seconds.
2017-06-30 16:25:43.186 err kernel: [261108.523796] INFO: task jbd2/sdb-8:45757 blocked for more than 120 seconds.
2017-06-30 16:25:43.317 err kernel: [261108.655818] INFO: task java:47487 blocked for more than 120 seconds.
2017-06-30 16:25:43.477 err kernel: [261108.821962] INFO: task java:47489 blocked for more than 120 seconds.
```

### 系统信息

配合监控系统查看：

- cached 指标
- dirty 指标
- disk write requests (write_ios) / disk write ticks (write_ticks) / disk write bytes (write_sectors) 指标

> 结合上述指标可以看到：
> 
> - 在故障时间段，磁盘写入出现**断崖式下跌**，基本可以认为**磁盘无法写入**。基于以上分析，怀疑为该主机在故障时间点出现**只读情况**，cached 中脏数据无法刷入磁盘；
> - 在故障时间段，cached 占用持续增加直至最大，dirty 在某个时间点后变为直线，并且一直保持固定大小，可以理解为不再刷新到磁盘；


### 故障原因

- 如果 `<process>`是**第三方应用程序**，则有可能是程序资源消耗过快，导致系统脏数据回收不及时。一般此种情况为警告，可以忽略；
- 如果 `<process>` 为 **Linux 内核程序**，此消息通常意味着系统正在经历磁盘或内存拥堵，系统可用资源匮乏；
- **硬件的故障**也有可能会导致此信息出现，比如磁盘损坏、I/O 异常等情况；

在没有故障当时的 `kcore` 的情况下，无法确认故障时的具体问题点，只能根据当时监控情况分析并尝试复现问题。

> 何为 kcore ：
> 
> /proc/kcore is like an "alias" for the memory in your computer. Its
size is the same as the amount of RAM you have, and if you read it as
a file, the kernel does memory reads.


> 查阅 redhat 相关资料，
"INFO: task `<process>`:`<pid>`` blocked for more than 120 seconds" 是 kernel 在 2.6.18-194 后用于发现任务超过 120s 处于 D-state (Uninterruptible Sleep (UN)) 引入的。出现该状态往往是由于**系统磁盘或者内存阻塞**导致（日志第二条也直接与设备相关）。

### 故障模拟

思路：

- 挂载磁盘磁盘分区，创建文件系统为 ext4 ；
- 挂载后进行写入操作，随后让设备无法写入，查看日志情况（需要等待 120s）；


1. 创建分区并挂载

```
fdisk xxx
mount -t ext4 xxx
```

2. 查看挂载情况

```
# df -h
...
/dev/sdb1 459G 73M 435G 1% /data1
```

3. 使用 dd 持续向该目录写入数据

```
while true; do dd if=/dev/zero of=/data1/test_dd bs=1024 count=100; done
```

4. 使用 fsfreeze 禁止写入

```
fsfreeze -f /data1
```

观察系统运行情况

- dd 写入 hang 住；
- 系统内存中缓冲不断增加，且为写缓冲脏数据；
- 通过 dmesg 观察系统日志可以看到 120s 相关打印；

> 对应测试过程中 4 次调用 `fsfreeze -f`
> 
> 以下内容取自 Linux 4.4.0-87-generic x86_64 Ubuntu 16.04.3 LTS

```
...
[494886.031776] INFO: task rs:main Q:Reg:9724 blocked for more than 120 seconds.
[494886.031915]       Not tainted 4.4.0-87-generic #110-Ubuntu
[494886.031988] "echo 0 > /proc/sys/kernel/hung_task_timeout_secs" disables this message.
[494886.032081] rs:main Q:Reg   D ffff880818267dc8     0  9724      1 0x00000000
[494886.032086]  ffff880818267dc8 ffff8808159d2a80 ffff88081bf43800 ffff88081addd400
[494886.032089]  ffff880818268000 ffff880814ede2c0 ffff880814ede2d8 00000000000000a2
[494886.032092]  00000000000000a2 ffff880818267de0 ffffffff8183dda5 ffff88081addd400
[494886.032096] Call Trace:
[494886.032103]  [<ffffffff8183dda5>] schedule+0x35/0x80
[494886.032106]  [<ffffffff81840c50>] rwsem_down_read_failed+0xe0/0x140
[494886.032110]  [<ffffffff81393eee>] ? common_file_perm+0x6e/0x1a0
[494886.032114]  [<ffffffff81407e44>] call_rwsem_down_read_failed+0x14/0x30
[494886.032117]  [<ffffffff810caa75>] ? percpu_down_read+0x35/0x50
[494886.032121]  [<ffffffff8121216c>] __sb_start_write+0x2c/0x40
[494886.032124]  [<ffffffff8120f5e4>] vfs_write+0x184/0x1a0
[494886.032127]  [<ffffffff8120e49f>] ? do_sys_open+0x1bf/0x2a0
[494886.032129]  [<ffffffff812101c5>] SyS_write+0x55/0xc0
[494886.032132]  [<ffffffff81841eb2>] entry_SYSCALL_64_fastpath+0x16/0x71
[494886.032136] INFO: task bash:9768 blocked for more than 120 seconds.
[494886.032221]       Not tainted 4.4.0-87-generic #110-Ubuntu
[494886.032311] "echo 0 > /proc/sys/kernel/hung_task_timeout_secs" disables this message.
[494886.032408] bash            D ffff8808197e3be8     0  9768   9737 0x00000004
[494886.032411]  ffff8808197e3be8 80000007f13b4865 ffff88081bf41c00 ffff880818470e00
[494886.032414]  ffff8808197e4000 ffff880814ede2c0 ffff880814ede2d8 ffff8808197e3dd0
[494886.032417]  00000000ffffff9c ffff8808197e3c00 ffffffff8183dda5 ffff880818470e00
[494886.032420] Call Trace:
[494886.032423]  [<ffffffff8183dda5>] schedule+0x35/0x80
[494886.032425]  [<ffffffff81840c50>] rwsem_down_read_failed+0xe0/0x140
[494886.032428]  [<ffffffff81407e44>] call_rwsem_down_read_failed+0x14/0x30
[494886.032430]  [<ffffffff810caa75>] ? percpu_down_read+0x35/0x50
[494886.032432]  [<ffffffff8121216c>] __sb_start_write+0x2c/0x40
[494886.032436]  [<ffffffff81230924>] mnt_want_write+0x24/0x50
[494886.032439]  [<ffffffff8121e7ba>] path_openat+0xd5a/0x1330
[494886.032441]  [<ffffffff810cb061>] ? __raw_callee_save___pv_queued_spin_unlock+0x11/0x20
[494886.032443]  [<ffffffff8121ff81>] do_filp_open+0x91/0x100
[494886.032446]  [<ffffffff810cb061>] ? __raw_callee_save___pv_queued_spin_unlock+0x11/0x20
[494886.032448]  [<ffffffff8122d948>] ? __alloc_fd+0xc8/0x190
[494886.032451]  [<ffffffff8120e418>] do_sys_open+0x138/0x2a0
[494886.032454]  [<ffffffff8120e59e>] SyS_open+0x1e/0x20
[494886.032456]  [<ffffffff81841eb2>] entry_SYSCALL_64_fastpath+0x16/0x71
[494886.032459] INFO: task dd:8811 blocked for more than 120 seconds.
[494886.032539]       Not tainted 4.4.0-87-generic #110-Ubuntu
[494886.032616] "echo 0 > /proc/sys/kernel/hung_task_timeout_secs" disables this message.
[494886.032708] dd              D ffff880813fd3be8     0  8811   9499 0x00000000
[494886.032711]  ffff880813fd3be8 ffff88083ffe06c0 ffffffff81e11500 ffff8800bbb38e00
[494886.032714]  ffff880813fd4000 ffff880814ede2c0 ffff880814ede2d8 ffff880813fd3dd0
[494886.032717]  00000000ffffff9c ffff880813fd3c00 ffffffff8183dda5 ffff8800bbb38e00
[494886.032720] Call Trace:
[494886.032722]  [<ffffffff8183dda5>] schedule+0x35/0x80
[494886.032725]  [<ffffffff81840c50>] rwsem_down_read_failed+0xe0/0x140
[494886.032727]  [<ffffffff81407e44>] call_rwsem_down_read_failed+0x14/0x30
[494886.032729]  [<ffffffff810caa75>] ? percpu_down_read+0x35/0x50
[494886.032731]  [<ffffffff8121216c>] __sb_start_write+0x2c/0x40
[494886.032733]  [<ffffffff81230924>] mnt_want_write+0x24/0x50
[494886.032736]  [<ffffffff8121e7ba>] path_openat+0xd5a/0x1330
[494886.032739]  [<ffffffff811cd735>] ? page_add_file_rmap+0x25/0x60
[494886.032742]  [<ffffffff8118f6b9>] ? unlock_page+0x69/0x70
[494886.032744]  [<ffffffff8121ff81>] do_filp_open+0x91/0x100
[494886.032746]  [<ffffffff810cb061>] ? __raw_callee_save___pv_queued_spin_unlock+0x11/0x20
[494886.032748]  [<ffffffff8122d948>] ? __alloc_fd+0xc8/0x190
[494886.032751]  [<ffffffff8120e418>] do_sys_open+0x138/0x2a0
[494886.032753]  [<ffffffff8120e59e>] SyS_open+0x1e/0x20
[494886.032756]  [<ffffffff81841eb2>] entry_SYSCALL_64_fastpath+0x16/0x71
[494886.032758] INFO: task sshd:8848 blocked for more than 120 seconds.
[494886.032840]       Not tainted 4.4.0-87-generic #110-Ubuntu
[494886.032914] "echo 0 > /proc/sys/kernel/hung_task_timeout_secs" disables this message.
[494886.033007] sshd            D ffff880813fffdc8     0  8848   8818 0x00000004
[494886.033010]  ffff880813fffdc8 0000000000000007 ffff88081bf46200 ffff880816928e00
[494886.033013]  ffff880814000000 ffff880814ede2c0 ffff880814ede2d8 0000000000000180
[494886.033016]  0000562c5fdf2a8c ffff880813fffde0 ffffffff8183dda5 ffff880816928e00
[494886.033019] Call Trace:
[494886.033021]  [<ffffffff8183dda5>] schedule+0x35/0x80
[494886.033024]  [<ffffffff81840c50>] rwsem_down_read_failed+0xe0/0x140
[494886.033026]  [<ffffffff81393eee>] ? common_file_perm+0x6e/0x1a0
[494886.033028]  [<ffffffff81407e44>] call_rwsem_down_read_failed+0x14/0x30
[494886.033030]  [<ffffffff810caa75>] ? percpu_down_read+0x35/0x50
[494886.033032]  [<ffffffff8121216c>] __sb_start_write+0x2c/0x40
[494886.033035]  [<ffffffff8120f5e4>] vfs_write+0x184/0x1a0
[494886.033037]  [<ffffffff812101c5>] SyS_write+0x55/0xc0
[494886.033040]  [<ffffffff81841eb2>] entry_SYSCALL_64_fastpath+0x16/0x71
[#13#root@sre-net-test-1 ~]$
```

`/var/log/messagens` 中的信息如下

```
...
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032081] rs:main Q:Reg   D ffff880818267dc8     0  9724      1 0x00000000
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032086]  ffff880818267dc8 ffff8808159d2a80 ffff88081bf43800 ffff88081addd400
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032089]  ffff880818268000 ffff880814ede2c0 ffff880814ede2d8 00000000000000a2
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032092]  00000000000000a2 ffff880818267de0 ffffffff8183dda5 ffff88081addd400
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032096] Call Trace:
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032103]  [<ffffffff8183dda5>] schedule+0x35/0x80
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032106]  [<ffffffff81840c50>] rwsem_down_read_failed+0xe0/0x140
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032110]  [<ffffffff81393eee>] ? common_file_perm+0x6e/0x1a0
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032114]  [<ffffffff81407e44>] call_rwsem_down_read_failed+0x14/0x30
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032117]  [<ffffffff810caa75>] ? percpu_down_read+0x35/0x50
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032121]  [<ffffffff8121216c>] __sb_start_write+0x2c/0x40
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032124]  [<ffffffff8120f5e4>] vfs_write+0x184/0x1a0
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032127]  [<ffffffff8120e49f>] ? do_sys_open+0x1bf/0x2a0
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032129]  [<ffffffff812101c5>] SyS_write+0x55/0xc0
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032132]  [<ffffffff81841eb2>] entry_SYSCALL_64_fastpath+0x16/0x71
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032408] bash            D ffff8808197e3be8     0  9768   9737 0x00000004
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032411]  ffff8808197e3be8 80000007f13b4865 ffff88081bf41c00 ffff880818470e00
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032414]  ffff8808197e4000 ffff880814ede2c0 ffff880814ede2d8 ffff8808197e3dd0
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032417]  00000000ffffff9c ffff8808197e3c00 ffffffff8183dda5 ffff880818470e00
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032420] Call Trace:
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032423]  [<ffffffff8183dda5>] schedule+0x35/0x80
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032425]  [<ffffffff81840c50>] rwsem_down_read_failed+0xe0/0x140
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032428]  [<ffffffff81407e44>] call_rwsem_down_read_failed+0x14/0x30
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032430]  [<ffffffff810caa75>] ? percpu_down_read+0x35/0x50
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032432]  [<ffffffff8121216c>] __sb_start_write+0x2c/0x40
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032436]  [<ffffffff81230924>] mnt_want_write+0x24/0x50
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032439]  [<ffffffff8121e7ba>] path_openat+0xd5a/0x1330
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032441]  [<ffffffff810cb061>] ? __raw_callee_save___pv_queued_spin_unlock+0x11/0x20
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032443]  [<ffffffff8121ff81>] do_filp_open+0x91/0x100
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032446]  [<ffffffff810cb061>] ? __raw_callee_save___pv_queued_spin_unlock+0x11/0x20
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032448]  [<ffffffff8122d948>] ? __alloc_fd+0xc8/0x190
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032451]  [<ffffffff8120e418>] do_sys_open+0x138/0x2a0
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032454]  [<ffffffff8120e59e>] SyS_open+0x1e/0x20
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032456]  [<ffffffff81841eb2>] entry_SYSCALL_64_fastpath+0x16/0x71
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032708] dd              D ffff880813fd3be8     0  8811   9499 0x00000000
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032711]  ffff880813fd3be8 ffff88083ffe06c0 ffffffff81e11500 ffff8800bbb38e00
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032714]  ffff880813fd4000 ffff880814ede2c0 ffff880814ede2d8 ffff880813fd3dd0
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032717]  00000000ffffff9c ffff880813fd3c00 ffffffff8183dda5 ffff8800bbb38e00
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032720] Call Trace:
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032722]  [<ffffffff8183dda5>] schedule+0x35/0x80
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032725]  [<ffffffff81840c50>] rwsem_down_read_failed+0xe0/0x140
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032727]  [<ffffffff81407e44>] call_rwsem_down_read_failed+0x14/0x30
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032729]  [<ffffffff810caa75>] ? percpu_down_read+0x35/0x50
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032731]  [<ffffffff8121216c>] __sb_start_write+0x2c/0x40
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032733]  [<ffffffff81230924>] mnt_want_write+0x24/0x50
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032736]  [<ffffffff8121e7ba>] path_openat+0xd5a/0x1330
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032739]  [<ffffffff811cd735>] ? page_add_file_rmap+0x25/0x60
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032742]  [<ffffffff8118f6b9>] ? unlock_page+0x69/0x70
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032744]  [<ffffffff8121ff81>] do_filp_open+0x91/0x100
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032746]  [<ffffffff810cb061>] ? __raw_callee_save___pv_queued_spin_unlock+0x11/0x20
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032748]  [<ffffffff8122d948>] ? __alloc_fd+0xc8/0x190
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032751]  [<ffffffff8120e418>] do_sys_open+0x138/0x2a0
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032753]  [<ffffffff8120e59e>] SyS_open+0x1e/0x20
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.032756]  [<ffffffff81841eb2>] entry_SYSCALL_64_fastpath+0x16/0x71
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033007] sshd            D ffff880813fffdc8     0  8848   8818 0x00000004
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033010]  ffff880813fffdc8 0000000000000007 ffff88081bf46200 ffff880816928e00
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033013]  ffff880814000000 ffff880814ede2c0 ffff880814ede2d8 0000000000000180
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033016]  0000562c5fdf2a8c ffff880813fffde0 ffffffff8183dda5 ffff880816928e00
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033019] Call Trace:
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033021]  [<ffffffff8183dda5>] schedule+0x35/0x80
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033024]  [<ffffffff81840c50>] rwsem_down_read_failed+0xe0/0x140
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033026]  [<ffffffff81393eee>] ? common_file_perm+0x6e/0x1a0
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033028]  [<ffffffff81407e44>] call_rwsem_down_read_failed+0x14/0x30
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033030]  [<ffffffff810caa75>] ? percpu_down_read+0x35/0x50
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033032]  [<ffffffff8121216c>] __sb_start_write+0x2c/0x40
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033035]  [<ffffffff8120f5e4>] vfs_write+0x184/0x1a0
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033037]  [<ffffffff812101c5>] SyS_write+0x55/0xc0
Sep 12 11:38:25 sre-net-test-1 kernel: [494886.033040]  [<ffffffff81841eb2>] entry_SYSCALL_64_fastpath+0x16/0x71
```


5. 使用 fsfreeze 允许写入

```
fsfreeze -u /data1
```


### 解决方案

- Redhat 建议首先检查硬件是否故障，其次是第三方应用程序；
- 如果无法排除硬件和软件故障，但希望快速发现此类问题并剔除对应节点，则可以设置 `kernel.hung_task_panic = 1` ；设置后，再次出现此类问题时，系统将抛出 kernel panic 并立刻重启。RedHat 不建议设置为 1 ，具体说明见附件；
- 如果仅仅是想降低 hang 住的时间，可以设置 `kernel.hung_task_timeout_secs=20` , 这样 hang 住的时间就降低到 20 秒，但并不保证 20 秒后不会继续 hang 住。

### 其它

```
$sysctl -a|grep hung
kernel.hung_task_check_count = 4194304
kernel.hung_task_panic = 0
kernel.hung_task_timeout_secs = 120
kernel.hung_task_warnings = 6
```

- [RHEL 5, 6, or 7 host reboots on its own when all storage paths fail and 'kernel.hung_task_panic = 1'](https://access.redhat.com/solutions/1428623)
- [Why does kernel.hung_task_panic = 1 not trigger a system panic in RHEL?](https://access.redhat.com/solutions/2721611)
- [How do I confirm that the 'kernel.hung_task_panic' parameter works?](https://access.redhat.com/solutions/728253)
- [How do I use hung task check in RHEL](https://access.redhat.com/solutions/60572#CAUTIONS)
- [System becomes unresponsive with message "INFO: task <process>:<pid> blocked for more than 120 seconds".](https://access.redhat.com/solutions/31453)




----------


## "BUG: unable to handle kernel NULL pointer dereference at 0000000000000010"

### 故障信息

vmcore-dmesg.txt 信息

```
...
[47113590.457363] BUG: unable to handle kernel NULL pointer dereference at 0000000000000010
[47113590.458030] IP: [<ffffffff812db1e1>] rb_next+0x1/0x50
[47113590.458377] PGD 1b8f60067 PUD 460d95067 PMD 0
[47113590.458723] Oops: 0000 [#1] SMP
[47113590.459060] Modules linked in: iptable_filter tcp_diag inet_diag binfmt_misc bridge stp llc ipmi_si ipmi_devintf ipmi_msghandler bonding dm_mirror dm_region_hash dm_log dm_mod coretemp intel_rapl kvm_intel kvm crct10dif_pclmul crc32_pclmul crc32c_intel ghash_clmulni_intel aesni_intel lrw gf128mul glue_helper iTCO_wdt ablk_helper cryptd iTCO_vendor_support sg pcspkr lpc_ich mei_me shpchp mei sb_edac mfd_core edac_core i2c_i801 acpi_power_meter ip_tables xfs libcrc32c sd_mod crc_t10dif crct10dif_common igb ahci libahci ptp pps_core libata i2c_algo_bit i2c_core megaraid_sas dca
[47113590.461921] CPU: 3 PID: 16179 Comm: java Not tainted 3.10.0-229.el7.x86_64 #1
[47113590.462585] Hardware name: Huawei RH1288 V3/BC11HGSC0, BIOS 1.71 12/03/2015
[47113590.463137] task: ffff88085f0838e0 ti: ffff8803bd398000 task.ti: ffff8803bd398000
[47113590.463690] RIP: 0010:[<ffffffff812db1e1>]  [<ffffffff812db1e1>] rb_next+0x1/0x50
[47113590.464256] RSP: 0018:ffff8803bd39bc28  EFLAGS: 00010046
[47113590.464587] RAX: 0000000000000000 RBX: 0000000000000000 RCX: 0000000000000000
[47113590.465256] RDX: 0000000000000001 RSI: ffff880470073728 RDI: 0000000000000010
[47113590.465899] RBP: ffff8803bd39bc70 R08: 0000000000000000 R09: 0000000000000001
[47113590.466540] R10: 0000000000000001 R11: 0000000000000000 R12: ffff8804691dfe00
[47113590.467180] R13: 0000000000000000 R14: 0000000000000000 R15: 0000000000000000
[47113590.467842] FS:  00007f8f39f83700(0000) GS:ffff880470060000(0000) knlGS:0000000000000000
[47113590.468486] CS:  0010 DS: 0000 ES: 0000 CR0: 0000000080050033
[47113590.468821] CR2: 0000000000000010 CR3: 000000045539c000 CR4: 00000000001407e0
[47113590.469476] DR0: 0000000000000000 DR1: 0000000000000000 DR2: 0000000000000000
[47113590.470137] DR3: 0000000000000000 DR6: 00000000ffff0ff0 DR7: 0000000000000400
[47113590.470798] Stack:
[47113590.471117]  ffff8803bd39bc70 ffffffff810aff99 00000001bd39bc60 ffff880470073680
[47113590.471792]  ffff88085f083ec0 ffff880470073680 0000000000000003 ffff8803bd39bde0
[47113590.472467]  ffffc900135db000 ffff8803bd39bcd0 ffffffff81609782 ffff8803bd39bfd8
[47113590.473142] Call Trace:
[47113590.473473]  [<ffffffff810aff99>] ? pick_next_task_fair+0x129/0x1d0
[47113590.473820]  [<ffffffff81609782>] __schedule+0x122/0x7b0
[47113590.474159]  [<ffffffff81609e39>] schedule+0x29/0x70
[47113590.474497]  [<ffffffff810d242e>] futex_wait_queue_me+0xde/0x140
[47113590.474838]  [<ffffffff810d2fa9>] futex_wait+0x179/0x280
[47113590.475173]  [<ffffffff8109af80>] ? hrtimer_get_res+0x50/0x50
[47113590.475513]  [<ffffffff8109b914>] ? hrtimer_start_range_ns+0x14/0x20
[47113590.475848]  [<ffffffff810d508e>] do_futex+0xfe/0x5b0
[47113590.476178]  [<ffffffff810d55c0>] SyS_futex+0x80/0x180
[47113590.476517]  [<ffffffff81614a29>] system_call_fastpath+0x16/0x1b
[47113590.476842] Code: 89 06 48 8b 47 08 48 89 46 08 48 8b 47 10 48 89 46 10 c3 0f 1f 80 00 00 00 00 48 89 32 eb b2 0f 1f 00 48 89 70 10 eb a9 66 90 55 <48> 8b 17 48 89 e5 48 39 d7 74 3b 48 8b 47 08 48 85 c0 75 0e eb
[47113590.477909] RIP  [<ffffffff812db1e1>] rb_next+0x1/0x50
[47113590.478196]  RSP <ffff8803bd39bc28>
[47113590.478477] CR2: 0000000000000010
```

### 系统信息

```
[root@xg-napos-shop-service-11 ]# uname -a
Linux xg-napos-shop-service-11 3.10.0-229.el7.x86_64 #1 SMP Fri Mar 6 11:36:42 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
[root@xg-napos-shop-service-11 ]# lsb_release -a
LSB Version:	:core-4.1-amd64:core-4.1-noarch
Distributor ID:	CentOS
Description:	CentOS Linux release 7.1.1503 (Core)
Release:	7.1.1503
Codename:	Core
[root@xg-napos-shop-service-11 ~]# lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                24
On-line CPU(s) list:   0-23
Thread(s) per core:    2
Core(s) per socket:    6
Socket(s):             2
NUMA node(s):          2
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 63
Model name:            Intel(R) Xeon(R) CPU E5-2620 v3 @ 2.40GHz
Stepping:              2
CPU MHz:               2599.968
BogoMIPS:              4793.14
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              15360K
NUMA node0 CPU(s):     0-5,12-17
NUMA node1 CPU(s):     6-11,18-23
```

### 故障原因

（取自这里：[RHBA-2016:2966 - Bug Fix Advisory](https://access.redhat.com/errata/RHBA-2016:2966)）：

> * Previously, a "NULL pointer dereference" problem in the pick_next_task_fair()
function occurred. This update fixes the bug by applying a set of patches on the
Completely Fair Scheduler (CFS) group scheduling. As a result, the "NULL pointer
dereference" problem no longer occurs. (BZ#1373820)

Cgroup created inside throttled group must inherit current throttle_count. Broken throttle_count allows to nominate throttled entries as a next buddy, later this leads to null pointer dereference in pick_next_task_fair().

### 解决方案

升级内核到 kernel-3.10.0-327.44.2 以上可解决；


### 其它

- [Inside the Linux 2.6 Completely Fair Scheduler](https://www.ibm.com/developerworks/library/l-completely-fair-scheduler/)
- [Process scheduler](http://myaut.github.io/dtrace-stap-book/kernel/sched.html)
- [Per-entity load tracking](https://lwn.net/Articles/531853/)
- [CFS bandwidth control](https://lwn.net/Articles/428230/)
- [bug fix mail list](https://marc.info/?l=linux-kernel&m=146658385207269&w=2)
- kernel bug fix commit: 094f469172e00d6ab0a3130b0e01c83b3cf3a98d
- kernel related commit: 
    - 5aface53d1a0ef7823215c4078fca8445995d006
    - 18f649ef344127ef6de23a5a4272dbe2fdb73dde
    - 8e5bfa8c1f8471aa4a2d30be631ef2b50e10abaf
    - f7b8a47da17c9ee4998f2ca2018fcc424e953c0e



----------


## "kernel: EPT: Misconfiguration"

### 故障信息

在 `/var/log/messages` 或 dmesg 中可以看到

```
Mar 7 13:38:04 xg-app-zstack-118 kernel: EPT: Misconfiguration.
Mar 7 13:38:04 xg-app-zstack-118 kernel: EPT: GPA: 0x108ba4538
Mar 7 13:38:04 xg-app-zstack-118 kernel: ept_misconfig_inspect_spte: spte 0x84c236107 level 4
Mar 7 13:38:04 xg-app-zstack-118 kernel: ept_misconfig_inspect_spte: spte 0x10515c7107 level 3
Mar 7 13:38:04 xg-app-zstack-118 kernel: ept_misconfig_inspect_spte: spte 0xde4657107 level 2
Mar 7 13:38:04 xg-app-zstack-118 kernel: ept_misconfig_inspect_spte: spte 0x1028125d77 level 1
```

关键：

- **EPT**: Extended Page Table
- **GPA**: Guest Physical address

> 说明上述错误和 KVM 虚拟化有关；


可能的表现：

- Found VM in paused state
- Unable to get it running again
- 服务没有响应，目标 KVM 虚拟机无法登陆



### 系统信息

kvm 虚拟机

```
[ops@xg-account-core-srv-10 ~]$ uname -a
Linux xg-account-core-srv-10 3.10.0-229.el7.x86_64 #1 SMP Fri Mar 6 11:36:42 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
[ops@xg-account-core-srv-10 ~]$ lsb_release -a
LSB Version:	:core-4.1-amd64:core-4.1-noarch
Distributor ID:	CentOS
Description:	CentOS Linux release 7.1.1503 (Core)
Release:	7.1.1503
Codename:	Core
[root@xg-account-core-srv-10 ~]# lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    1
Core(s) per socket:    8
Socket(s):             1
NUMA node(s):          1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 13
Model name:            QEMU Virtual CPU version 2.3.0
Stepping:              3
CPU MHz:               2599.996
BogoMIPS:              5199.99
Hypervisor vendor:     KVM
Virtualization type:   full
L1d cache:             32K
L1i cache:             32K
L2 cache:              4096K
NUMA node0 CPU(s):     0-7
```

宿主机

```
[root@xg-app-zstack-118 ~]# uname -a
Linux xg-app-zstack-118 3.10.0-229.el7.x86_64 #1 SMP Fri Mar 6 11:36:42 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
[root@xg-app-zstack-118 ~]# lsb_release -a
LSB Version:	:core-4.1-amd64:core-4.1-noarch
Distributor ID:	CentOS
Description:	CentOS Linux release 7.1.1503 (Core)
Release:	7.1.1503
Codename:	Core
[root@xg-app-zstack-118 ~]# lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                32
On-line CPU(s) list:   0-31
Thread(s) per core:    2
Core(s) per socket:    8
Socket(s):             2
NUMA node(s):          2
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 63
Model name:            Intel(R) Xeon(R) CPU E5-2640 v3 @ 2.60GHz
Stepping:              2
CPU MHz:               2799.976
BogoMIPS:              5204.37
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              20480K
NUMA node0 CPU(s):     0,2,4,6,8,10,12,14,16,18,20,22,24,26,28,30
NUMA node1 CPU(s):     1,3,5,7,9,11,13,15,17,19,21,23,25,27,29,31
```


### 故障原因

（取自这里：[RHEV: VM paused, cannot resume](https://access.redhat.com/solutions/1758133)）：

> A MMIO Page Fault related bug in KVM causes this. This is a Hypervisor side bug and any VM OS might hit it.
The probability of hitting it appears to be quite low (many VMs running for days).


### 解决方案

- **RHEL 7.3**: Upgrade to kernel-3.10.0-514.el7 from Errata [RHSA-2016:2574](https://rhn.redhat.com/errata/RHSA-2016-2574.html) or later
- **RHEL 7.2.z**: Upgrade to kernel-3.10.0-327.3.1.el7 from Errata [RHSA-2015:2552](https://access.redhat.com/errata/RHSA-2015:2552) or later


### 其他

- Red Hat Enterprise Virtualization (**RHEV**) is Red Hat Inc.'s server virtualization platfor.
- ZStack 最新推出的混合云产品，领先业内、第一家实现了控制面和数据面的完全打通，给用户提供无缝混合云的体验。ZStack 通过标准的混合云产品，提供“互连、灾备、服务、一键迁云”四大场景。



----------


## more kernel bugs

### 故障信息

### 系统信息

### 故障原因

### 解决方案


----------
