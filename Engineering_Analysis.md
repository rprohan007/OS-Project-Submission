# Engineering Analysis

## Process Isolation
Containers run as separate processes with isolated root filesystems.

## Kernel-User Interaction
Uses ioctl via /dev/container_monitor.

## Memory Management
Soft limit warns, hard limit kills process.

## IPC
UNIX sockets + logging pipeline.

## Scheduling
CPU vs IO workloads show scheduler behavior.
