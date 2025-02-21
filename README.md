[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/IAASVEAZ)
# CSIT5970 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Friday

---

### Name: Meng Shengnan
### Student Id: 21078969
### Email: smengad@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

    > Your answer goes here.
    * Measurement tool: sysbench
    * Configuration:
        * Install sysbench in Ubuntu:
        ```
        sudo apt -y install make automake libtool pkg-config libaio-dev
        curl -s https://packagecloud.io/install/repositories/akopytov/sysbench/script.deb.sh | sudo bash
        sudo apt -y install sysbench
        ```
        * CPU test:
        ```
        sysbench cpu --cpu-max-prime=20000 run
        ```
        * Memeory test:
        ```
        sysbench memory --memory-block-size=1M --memory-total-size=10G run
        ```
    * Parameter explanation:
        * --cpu-max-prime=20000: Set the maximum prime number value for the CPU test to 20000. The CPU test in sysbench calculates prime numbers up to this value. Setting a high value like 20000 ensures that the CPU is sufficiently stressed, allowing for a meaningful measurement of its computational performance. This value is chosen to balance between test duration and the intensity of the CPU workload.
        * --memory-block-size=1M: Set the block size for the memory test to 1M. The memory test in sysbench performs read and write operations using blocks of this size. A block size of 1M is chosen to simulate realistic memory usage patterns and to ensure that the memory bandwidth is effectively utilized. This size is large enough to provide a good measure of memory performance without being too small to cause excessive overhead.
        * --memory-total-size=10G: Set the total size for the memory test to 10G. The memory test will continue until this amount of data has been read and written. Setting the total size to 10G ensures that the memory test runs long enough to provide accurate and stable results. This value is chosen to be significantly larger than the typical cache sizes, ensuring that the test measures the performance of the main memory rather than just the cache.
    * Meaning of measurement results:
        * CPU test results represent the time taken to calculate prime numbers.
        * Memory test results represent the speed of memory read/write operations.

2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?

    In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

    | Size        | CPU performance | Memory performance |
    | ----------- | --------------- | ------------------ |
    | `t2.micro`  |     10.0005s    |  19094.52 MiB/sec  |
    | `t2.medium` |     10.0009s    |  18977.09 MiB/sec  |
    | `c5d.large` |     10.0020s    |  19666.64 MiB/sec  |

    Based on the above table, I do not believe that the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource.

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI.

## Question 2: Measure the EC2 Network performance

    * Measurement tool: iPerf to measure TCP bandwidth and Ping to measure RTT
    * Configuration:
        * Install iPerf in Ubuntu:
        ```
        sudo apt-get update
        sudo apt-get install iperf
        ```
        * Run iPerf server:
        ```
        iperf -s -w 256K
        ```
        * Run iPerf client:
        ```
        iperf -c <server-ip> -w 256K
        ```
        * Use Ping to measure RTT:
        ```
        ping -c 10 <server-ip>
        ```
    * Parameter explanation:
        * iperf -c: Run as the client.
        * iperf -s: Run as the server.
        * iperf -w: Set the window size (of TCP by default)
        * ping -c 10 <server-ip>: Send 10 data packets to the specified server IP address and report the round-trip time (RTT) for each packet.
    * Meaning of measurement results:
        * TCP bandwidth refers to the maximum rate at which data can be transmitted over a TCP connection between two endpoints.
        * RTT refers to the time it takes for a signal to travel from the source to the destination and back again.


1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

    | Type                      | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | `t3.medium` - `t3.medium` |    3850Mbps    |  0.292ms |
    | `m5.large` - `m5.large`   |    4910Mbps    |  0.242ms |
    | `c5n.large` - `c5n.large` |    4950Mbps    |  0.159ms |
    | `t3.medium` - `c5n.large` |    2180Mbps    |  0.798ms |
    | `m5.large` - `c5n.large`  |    2530Mbps    |  0.735ms |
    | `m5.large` - `t3.medium`  |    3860Mbps    |  0.356ms |

    > Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. Note: Use private IP address when using iPerf within the same region. You'll need iPerf for measuring TCP bandwidth and Ping for measuring Round-Trip time.

    Based on the above table, I believe that network performance is generally better between instances of the same type and there is a noticeable difference in performance when instances of different types communicate with each other.


2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

    | Connection                | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | N. Virginia - Oregon      |    3230Mbps    | 59.968ms |
    | N. Virginia - N. Virginia |    4120Mbps    |  0.264ms |
    | Oregon - Oregon           |    4770Mbps    |  0.170ms |
 
    > Region: US East (N. Virginia), US West (Oregon). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI. All instances are `c5.large`. Note: Use public IP address when using iPerf within the same region.

    Based on the above table, I believe that network performance is generally better within the same region and there is a noticeable decrease in performance when instances are deployed in different regions due to the increased distance and potential network congestion between the regions.