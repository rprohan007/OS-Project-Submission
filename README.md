# OS Container Supervisor Project

## 1. Team Information

* Name: Rohan Rampura Praveen Kumar
* SRN: PES1UG24CS382
* Name: Roshan
* SRN: PES1UG24CS387

---

## 2. Build, Load, and Run Instructions

### Environment Setup

* Ubuntu 22.04 / 24.04 required
* Install dependencies:

```bash
sudo apt update
sudo apt install -y build-essential linux-headers-$(uname -r)
```

---

### Build Project

```bash
make
```

---

### Load Kernel Module

```bash
sudo insmod monitor.ko
```

---

### Start Supervisor

```bash
sudo ./engine supervisor ./rootfs-base
```

---

### Start Containers

```bash
sudo ./engine start alpha ./rootfs-alpha /bin/sh
sudo ./engine start beta ./rootfs-beta /bin/sh
```

---

### View Container Metadata

```bash
sudo ./engine ps
```

---

### Check Logs

```bash
dmesg | tail
cat logs/alpha.log
```

---

### Stop Containers and Cleanup

```bash
sudo ./engine stop alpha
sudo ./engine stop beta
sudo ./engine stop gamma
pkill cpu_hog
pkill io_pulse
sudo rmmod monitor
make clean
```

---

## 3. Demo Screenshots

### 1. Multi-container supervision

![Multi](screenshots/multi_container.png.jpeg)
Multiple containers running simultaneously under a single supervisor process.

---

### 2. Metadata tracking

![PS](screenshots/ps_output.png.jpeg)
Output of `engine ps` showing container metadata such as PID and status.

---

### 3. Bounded-buffer logging

![Log](screenshots/logging.png.jpeg)
Log file output demonstrating continuous logging from workloads via the logging pipeline.

---

### 4. CLI and IPC

![CLI](screenshots/cli_ipc.png.jpeg)
Command issued through CLI and supervisor response, demonstrating inter-process communication.

---

### 5. Soft-limit warning

![Soft](screenshots/soft_limit.png.jpeg)
Kernel logs showing memory tracking with soft and hard limits for containers.

---

### 6. Hard-limit enforcement

![Hard](screenshots/hard_limit.png.jpeg)
Kernel monitor enforcing memory limits and registering containers with defined thresholds.

---

### 7. Scheduling experiment

![Sched](screenshots/scheduling.png.jpeg)
System resource usage observed using `top`, demonstrating scheduling behavior under load.

---

### 8. Clean teardown

![Clean](screenshots/cleanup.png.jpeg)
Containers and workloads are stopped, and system processes are cleaned up, confirming proper shutdown.

---

## 4. Engineering Analysis

This project demonstrates key operating system concepts through a container runtime built in C.

### Process Isolation

Containers are created using Linux namespaces, isolating processes and filesystem environments. This enables lightweight virtualization while sharing the same kernel.

### Kernel-User Communication

A custom kernel module (`monitor.c`) monitors memory usage and communicates with user space via `ioctl`. This reflects how real systems expose kernel data safely.

### Supervisor Design

The supervisor (`engine.c`) manages container lifecycle, tracks metadata, and enforces policies such as memory limits.

### Logging System

A bounded-buffer logging mechanism is used to handle concurrent log generation, demonstrating producer-consumer synchronization.

### Scheduling Behavior

Running multiple workloads simultaneously reveals how the Linux scheduler distributes CPU time fairly among processes.

---

## 5. Design Decisions and Tradeoffs

### Namespace Isolation

* Choice: Use Linux namespaces
* Tradeoff: Less isolation than virtual machines
* Justification: Lightweight and efficient

### Supervisor Architecture

* Choice: Single supervisor process
* Tradeoff: Single point of failure
* Justification: Simplifies control and coordination

### Kernel Module

* Choice: Custom memory monitor
* Tradeoff: Increased complexity
* Justification: Direct access to kernel-level data

### Logging System

* Choice: Bounded buffer
* Tradeoff: Requires synchronization
* Justification: Prevents overflow and ensures consistency

### Scheduling Experiment

* Choice: Real workloads
* Tradeoff: Less controlled environment
* Justification: More realistic behavior observation

---

## 6. Scheduler Experiment Results

### Setup

Multiple workloads (CPU-bound and I/O-bound) were executed concurrently across containers.

### Observations

| Scenario            | Behavior                                        |
| ------------------- | ----------------------------------------------- |
| Single container    | Stable execution, full CPU utilization          |
| Multiple containers | CPU shared fairly among processes               |
| High load           | Increased context switching and execution delay |

### Key Insights

* Linux uses fair scheduling (CFS)
* CPU time is distributed evenly
* Performance degrades predictably under load, without starvation

---

## Conclusion

This project demonstrates core OS concepts including process isolation, kernel interaction, IPC, scheduling, and resource management. The system successfully manages multiple containers while enforcing limits and maintaining stability.
