# Red Hat Virtualization 4.3 Performance Tuning

## 참고자료 

 url : 4.10. CONFIGURING HIGH PERFORMANCE VIRTUAL MACHINES, TEMPLATES, AND POOLS

https://access.redhat.com/documentation/en-us/red_hat_virtualization/4.3/html/virtual_machine_management_guide/Configuring_High_Performance_Virtual_Machines_Templates_and_Pools#Pinning_CPU

---------

## 목차

1. 가상머신 프로파일 정의
2. Pinning CPUs
3. Setting the NUMA Nodes and Pining Topology
4. Configuring Huge Pages
5. Disabling KSM

----------

### 1. 가상머신 프로파일 정의

(1) 컴퓨팅 -> 가상머신 -> **새로 만들기** or **편집**

![image-20201217200906350](C:\Users\jakim\Desktop\pictures\image-20201217200906350.png)



(2) 일반 -> 최적화 옵션 -> **고성능** 선택

![image-20201217201059845](C:\Users\jakim\Desktop\pictures\image-20201217201059845.png)



(참고) 해당 프로파일을 적용시 자동으로 하기와 같이 적용된다.

- VM - Virtual machine
- T - Template
- P - Pool
- C - Cluster

| Setting                                                      | Enabled (Y/N) | Applies to |
| :----------------------------------------------------------- | :------------ | :--------- |
| **Headless Mode** (Console tab)                              | `Y`           | `VM, T, P` |
| **USB Support** (Console tab)                                | `N`           | `VM, T, P` |
| **Smartcard Enabled** (Console tab)                          | `N`           | `VM, T, P` |
| **Soundcard Enabled** (Console tab)                          | `N`           | `VM, T, P` |
| **Enable VirtIO serial console** (Console tab)               | `Y`           | `VM, T, P` |
| **Allow manual migration only** (Host tab)                   | `Y`           | `VM, T, P` |
| **Pass-Through Host CPU** (Host tab)                         | `Y`           | `VM, T, P` |
| **Highly Available** [[a\]](https://access.redhat.com/documentation/en-us/red_hat_virtualization/4.3/html/virtual_machine_management_guide/Configuring_High_Performance_Virtual_Machines_Templates_and_Pools#ftn.idm140380531734128) (High Availability tab) | `N`           | `VM, T, P` |
| **No-Watchdog** (High Availability tab)                      | `N`           | `VM, T, P` |
| **Memory Balloon Device** (Resource Allocation tab)          | `N`           | `VM, T, P` |
| **IO Threads Enabled** [[b\]](https://access.redhat.com/documentation/en-us/red_hat_virtualization/4.3/html/virtual_machine_management_guide/Configuring_High_Performance_Virtual_Machines_Templates_and_Pools#ftn.idm140380531711744) (Resource Allocation tab) | `Y`           | `VM, T, P` |
| Paravirtualized Random Number Generator PCI (virtio-rng) device (Random Generator tab) | `Y`           | `VM, T, P` |
| IO and emulator threads pinning topology                     | `Y`           | `VM, T`    |
| CPU cache layer 3                                            | `Y`           | `VM, T, P` |
| [[a\] ](https://access.redhat.com/documentation/en-us/red_hat_virtualization/4.3/html/virtual_machine_management_guide/Configuring_High_Performance_Virtual_Machines_Templates_and_Pools#idm140380531734128)`Highly Available` is not automatically enabled. If you select it manually, high availability should be enabled for pinned hosts only.[[b\] ](https://access.redhat.com/documentation/en-us/red_hat_virtualization/4.3/html/virtual_machine_management_guide/Configuring_High_Performance_Virtual_Machines_Templates_and_Pools#idm140380531711744)Number of IO threads = 1 | -             | -          |



-----------------

### 2. Pinning CPUs

(참고) 해당 설정은 하기와 같이 정의 된다.

- VM - Virtual machine
- T - Template
- P - Pool
- C - Cluster

| Setting                                            | Enabled (Y/N) | Applies to |
| :------------------------------------------------- | :------------ | :--------- |
| **NUMA Node Count** (Host tab)                     | `Y`           | `VM`       |
| **Tune Mode** (Host tab)                           | `Y`           | `VM`       |
| **NUMA Pinning** (Host tab)                        | `Y`           | `VM`       |
| **CPU Pinning topology** (Resource Allocation tab) | `Y`           | `VM, P`    |
| **hugepages** (Custom Properties tab)              | `Y`           | `VM, T, P` |
| **KSM** (Optimization tab)                         | `N`           | `C`        |



(참고) 다음 문서는 하기 RHV-H환경에서 한쪽 NUMA만 쓸 경우이다.

```
[root@rhvh ~]# lscpu
Architecture:        x86_64
CPU op-mode(s):      32-bit, 64-bit
Byte Order:          Little Endian
CPU(s):              40         ### CPU 40개
On-line CPU(s) list: 0-39
Thread(s) per core:  2
Core(s) per socket:  10
Socket(s):           2
NUMA node(s):        2          ### NUMA 2개
Vendor ID:           GenuineIntel
CPU family:          6
Model:               85
Model name:          Intel(R) Xeon(R) Silver 4114 CPU @ 2.20GHz
Stepping:            4
CPU MHz:             950.012
CPU max MHz:         3000.0000
CPU min MHz:         800.0000
BogoMIPS:            4400.00
Virtualization:      VT-x
L1d cache:           32K
L1i cache:           32K
L2 cache:            1024K
L3 cache:            14080K
NUMA node0 CPU(s):   0-9,20-29     ### NUMA node0에만 pinning 예정
NUMA node1 CPU(s):   10-19,30-39
Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid dca sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb cat_l3 cdp_l3 invpcid_single pti intel_ppin ssbd mba ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid rtm cqm mpx rdt_a avx512f avx512dq rdseed adx smap clflushopt clwb intel_pt avx512cd avx512bw avx512vl xsaveopt xsavec xgetbv1 xsaves cqm_llc cqm_occup_llc cqm_mbm_total cqm_mbm_local dtherm ida arat pln pts hwp hwp_act_window hwp_epp hwp_pkg_req pku ospke md_clear flush_l1d
[root@rhvh ~]#
```



(참고) 제약사항

- 가상 머신의 소켓 수는 호스트의 소켓 수보다 클 수 없습니다.
- 가상 컴퓨터의 가상 소켓 당 코어 수는 호스트의 코어 수보다 크지 않아야합니다.
- CPU 집약적 워크로드는 호스트와 가상 머신이 동일한 캐시 사용량을 예상할 때 가장 잘 수행됩니다. 최상의 성능을 얻으려면 가상 머신의 코어 당 스레드 수가 호스트의 스레드 수보다 크지 않아야합니다



(1) 호스트 -> **특정 호스트**

![image-20201217201707743](C:\Users\jakim\Desktop\pictures\image-20201217201707743.png)



(2) 리소스 할당 -> **CPU 할당**

- CPU 프로파일 : **Default**
- CPU 공유 : **비활성화됨**
- CPU 피닝 토폴로지 : **0#0_1#1_2#2_3#3**
  - vCPU 4개를 pCPU 1:1로 pinning

![image-20201217204000723](C:\Users\jakim\Desktop\pictures\image-20201217204000723.png)



----------------------------------

### 3. Setting the NUMA Nodes and Pining Topology

(참고) NUMA 설정 / 조정모드

*`Strict`*

**제한** 정책은 대상노드에 메모리를 할당할 수없는 경우, 할당이 실패함을 의미합니다.

메모리 모드 속성을 정의하지 않고 NUMA 노드 세트 목록을 지정하면 기본적으로 *`strict`*mode 가 됩니다.

*`Interleave`*

메모리 페이지는 노드 집합에 의해 지정된 노드에 할당되지만 라운드 로빈 방식으로 할당됩니다.

*`Preferred`*

메모리는 단일 기본 메모리 노드에서 할당됩니다. 충분한 메모리를 사용할 수없는 경우 다른 노드에서 메모리를 할당 할 수 있습니다.



(1) 호스트 -> **NUMA 설정**

- NUMA 노드 수 : 가상머신의 vNUMA의 개수를 의미
- 조정 모드 : **기본 설정**

![image-20201217204647715](C:\Users\jakim\Desktop\pictures\image-20201217204647715.png)



(2) **NUMA 고정** 클릭

- vNUMA를 드래그 하기 전

![image-20201217204921692](C:\Users\jakim\Desktop\pictures\image-20201217204921692.png)

- vNUMA를 드래그 후
  - CPU pinning한 NUMA node에 드래그 할 것!

![image-20201217205040930](C:\Users\jakim\Desktop\pictures\image-20201217205040930.png)



-------------------------

### 4. Configuring Huge Pages

(참고) RHV-H hugepages 사이즈 확인

```
[root@rhvh ~]# grep AnonHugePages /proc/meminfo
AnonHugePages:  41795584 kB
[root@rhvh ~]#
```



(1) 사용자 정의 속성 -> **키를 선택하십시오** 클릭 -> **hugepages** 선택

- 입력단위 : KB

- Red Hat은 **hugepages** 크기를 고정된 호스트에서 지원하는 가장 큰 크기로 설정할 것을 권장합니다. x86_64의 권장 크기는 1GB입니다.
- 가상 머신의 **hugepages** 크기는 고정된 호스트의 hugepages 크기와 같아야합니다.

![image-20201217210426181](C:\Users\jakim\Desktop\pictures\image-20201217210426181.png)

---------------------

### 5. Disabling KSM

(1) 컴퓨팅 -> **클러스터** -> **편집** 클릭

![image-20201217211104924](C:\Users\jakim\Desktop\pictures\image-20201217211104924.png)



(2) 최적화 -> **KSM 활성화** 체크박스 제거

![image-20201217211323230](C:\Users\jakim\Desktop\pictures\image-20201217211323230.png)



------------------------

### A. 시나리오

- vCPU : 4ea
- mem : 8GB



(참고) 가상 머신 구성이 호스트 구성과 호환되는지 확인합니다.

- 가상 머신의 소켓 수는 호스트의 소켓 수보다 클 수 없습니다.
- 가상 컴퓨터의 가상 소켓 당 코어 수는 호스트의 코어 수보다 크지 않아야합니다.
- CPU 집약적 워크로드는 호스트와 가상 머신이 동일한 캐시 사용량을 예상 할 때 가장 잘 수행됩니다. 최상의 성능을 얻으려면 가상 머신의 코어 당 스레드 수가 호스트의 스레드 수보다 크지 않아야합니다.

![image-20201217210819714](C:\Users\jakim\Desktop\pictures\image-20201217210819714.png)



(참고) 가상머신 그림확인

![image-20201217211916147](C:\Users\jakim\Desktop\pictures\image-20201217211916147.png)



(참고) 

**Table 4.4. High Performance Icons**

| Icon                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| ![hp vm](https://access.redhat.com/webassets/avalon/d/Red_Hat_Virtualization-4.3-Virtual_Machine_Management_Guide-en-US/images/9273520988fa69c82e193ec06761c06d/hp_vm.png) | High performance virtual machine                             |
| ![hp vm next run](https://access.redhat.com/webassets/avalon/d/Red_Hat_Virtualization-4.3-Virtual_Machine_Management_Guide-en-US/images/3ef1569c9fb5578683cedfdcb2626a8a/hp_vm_next_run.png) | High performance virtual machine with Next Run configuration |
| ![stateless hp vm](https://access.redhat.com/webassets/avalon/d/Red_Hat_Virtualization-4.3-Virtual_Machine_Management_Guide-en-US/images/b2248ec5c57e04432ef3fdbc6c792ad0/stateless_hp_vm.png) | Stateless, high performance virtual machine                  |
| ![stateless hp vm next run](https://access.redhat.com/webassets/avalon/d/Red_Hat_Virtualization-4.3-Virtual_Machine_Management_Guide-en-US/images/f27bb41d52f5669baa040b4b58be3ccb/stateless_hp_vm_next_run.png) | Stateless, high performance virtual machine with Next Run configuration |
| ![vm hp pool](https://access.redhat.com/webassets/avalon/d/Red_Hat_Virtualization-4.3-Virtual_Machine_Management_Guide-en-US/images/c6daf3d3f38f4c380b6a32060c9d9f67/vm_hp_pool.png) | Virtual machine in a high performance pool                   |
| ![vm hp pool next run](https://access.redhat.com/webassets/avalon/d/Red_Hat_Virtualization-4.3-Virtual_Machine_Management_Guide-en-US/images/273456fc32bfc02258eb703001c8cacc/vm_hp_pool_next_run.png) | Virtual machine in a high performance pool with Next Run configuration |