# VM Stress Testing and Scaling Commands

This README documents essential commands for creating load on a virtual machine (VM), testing CPU performance, and setting up scalable infrastructure.

## Prerequisites
- Ubuntu virtual machine set up on VirtualBox
- Internet access for package installation

## 1. Install Stress Tool
The `stress` tool is used to create artificial load on the system:

```bash
sudo apt update
sudo apt install stress
```

## 2. CPU Stress Test
Max out 4 CPU cores for 60 seconds:

```bash
stress --cpu 4 --timeout 60s
```

To run the stress test indefinitely:

```bash
stress --cpu 4
```

Stop the stress test anytime with `Ctrl + C`.

## 3. Memory Stress Test
Allocate and fill 1GB of memory for 60 seconds:

```bash
stress --vm 1 --vm-bytes 1G --timeout 60s
```

## 4. I/O (Disk) Stress Test
Write and read temporary files for 60 seconds:

```bash
stress --io 4 --timeout 60s
```

## 5. Combined Load Test
Combine CPU, memory, and I/O stress:

```bash
stress --cpu 2 --vm 1 --vm-bytes 512M --io 2 --timeout 120s
```

## 6. Monitoring System Load
While running stress tests, monitor system performance:

```bash
top
```

Or for more detailed system stats:

```bash
htop
```

(Install `htop` if needed: `sudo apt install htop`)

## 7. Optional: Auto-Scaling Setup on GCP
If you’re working with Google Cloud Platform:

### 7.1 Create a VM Instance Template
```bash
gcloud compute instance-templates create vm-instance-template \
    --machine-type=e2-medium \
    --image-family=debian-10 \
    --image-project=debian-cloud
```

### 7.2 Create a Managed Instance Group
```bash
gcloud compute instance-groups managed create vm-instance-group \
    --base-instance-name=vm-instance \
    --template=vm-instance-template \
    --size=1 \
    --zone=us-central1-a
```

### 7.3 Configure Auto-Scaling
```bash
gcloud compute instance-groups managed set-autoscaling vm-instance-group \
    --max-num-replicas=10 \
    --target-cpu-utilization=0.5 \
    --cool-down-period=60 \
    --zone=us-central1-a
```

## 8. Stopping and Cleaning Up
To stop stress tests:

```bash
killall stress
```

To delete the instance group (if using GCP):

```bash
gcloud compute instance-groups managed delete vm-instance-group --zone=us-central1-a
```

