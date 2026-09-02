Here is a clean RCA you can share with your students/team.

# Root Cause Analysis (RCA) – MongoDB Container Exited with Code 139

## Issue Summary

The MongoDB Docker container was starting successfully and the Spring Boot application was able to connect to it.

After running for some time, the MongoDB container unexpectedly moved to the stopped state.

Observed status:

```bash
docker ps -a
```

Output:

```text
mongo:8.0.9-noble
Exited (139)
```

---

## Environment

* AWS EC2
* OS: Ubuntu
* Kernel: `7.0.0-1006-aws`
* Architecture: `x86_64`
* CPU: Intel Xeon Platinum 8259CL
* MongoDB Image: `mongo:8.0.9-noble`
* Application: Spring Boot
* Java: Temurin Java 8
* Docker Network: `jionetwork`

---

## Initial Symptoms

MongoDB initially started successfully.

The logs showed successful authentication:

```text
Successfully authenticated
```

The Spring Boot application was also able to communicate with MongoDB.

However, after some time:

```bash
docker ps -a
```

showed:

```text
mongo   Exited (139)
```

---

## Investigation

### 1. Checked Memory

Command:

```bash
free -h
```

Result:

```text
Total RAM:      15 GB
Available RAM:  14 GB
Swap:           0
```

There was sufficient memory available.

Therefore, this was not an Out Of Memory issue.

---

### 2. Checked Docker OOM Status

Command:

```bash
docker inspect mongo \
--format='ExitCode={{.State.ExitCode}} OOMKilled={{.State.OOMKilled}} Error={{.State.Error}}'
```

Result:

```text
ExitCode=139
OOMKilled=false
Error=
```

This confirmed that the container was not killed because of memory exhaustion.

---

### 3. Checked Disk Space

Command:

```bash
df -Th
```

Result:

```text
/dev/root
Size: 6.7G
Used: 4.8G
Available: 1.9G
Usage: 73%
```

Enough disk space was available.

Therefore, disk exhaustion was also ruled out.

---

### 4. Checked CPU Compatibility

Command:

```bash
lscpu
```

CPU:

```text
Intel(R) Xeon(R) Platinum 8259CL CPU @ 2.50GHz
```

The processor supports:

```text
AVX
AVX2
AVX512
```

Therefore, MongoDB CPU instruction requirements were satisfied.

---

### 5. Checked Linux Kernel

Command:

```bash
uname -r
```

Output:

```text
7.0.0-1006-aws
```

This was the key finding.

---

## Root Cause

The MongoDB container was using:

```text
mongo:8.0.9-noble
```

on Linux kernel:

```text
7.0.0-1006-aws
```

MongoDB has a known incompatibility involving TCMalloc with Linux kernel versions in the affected kernel range around `6.19` through early `7.0.x`.

This can cause the `mongod` process to crash with a segmentation fault.

Docker reported this as:

```text
Exit Code 139
```

Exit code `139` corresponds to:

```text
128 + 11 = 139
```

where signal `11` is:

```text
SIGSEGV
Segmentation Fault
```

Therefore, the MongoDB process was crashing at the process/kernel level.

---

## Resolution

Instead of MongoDB 8, MongoDB 7 was used.

The problematic container was removed:

```bash
docker rm -f mongo
```

MongoDB 7 was pulled:

```bash
docker pull mongo:7.0
```

A new MongoDB container was created:

```bash
docker run -d \
  --name mongo \
  --network jionetwork \
  --restart unless-stopped \
  -e MONGO_INITDB_ROOT_USERNAME=devdb \
  -e MONGO_INITDB_ROOT_PASSWORD=dev123 \
  mongo:7.0
```

---

## Verification

Command:

```bash
docker ps -a
```

Result:

```text
mongo:7.0                    Up
kkdevopsb10/springapp:1.1    Up
```

MongoDB logs showed:

```text
Listening on 0.0.0.0
Waiting for connections
port: 27017
mongod startup complete
```

The Spring Boot container also successfully established connectivity with MongoDB.

---

## Final Architecture

```text
Client / Browser
       |
       | Port 8080
       v
+----------------------+
| Spring Boot Container|
|     springapp        |
+----------+-----------+
           |
           | mongo:27017
           |
           v
+----------------------+
| MongoDB Container    |
|      mongo:7.0       |
+----------------------+

Docker Network:
jionetwork
```

---

## Root Cause Summary

```text
MongoDB 8.0.9
       +
Linux Kernel 7.0.0
       |
       v
TCMalloc / Kernel Compatibility Issue
       |
       v
mongod Segmentation Fault
       |
       v
SIGSEGV (Signal 11)
       |
       v
Docker Exit Code 139
       |
       v
MongoDB Container Stopped
```

---

## Corrective Action

The immediate corrective action was:

```text
mongo:8.0.9-noble
        ↓
mongo:7.0
```

After downgrading MongoDB to version 7.0, the MongoDB container remained healthy and the Spring Boot application was able to communicate with it successfully.

---

## Preventive Actions

Before upgrading critical infrastructure components such as MongoDB:

1. Verify database compatibility with the operating system and kernel version.
2. Review MongoDB release notes before upgrading major versions.
3. Avoid using floating Docker tags such as:

```text
mongo:latest
```

Prefer fixed versions:

```text
mongo:7.0
```

4. Configure restart policies:

```bash
--restart unless-stopped
```

5. Monitor container exit codes:

```bash
docker ps -a
```

6. Inspect failure information:

```bash
docker inspect <container>
```

7. Check container logs:

```bash
docker logs <container>
```

8. Monitor resource utilization:

```bash
docker stats
```

---

## Conclusion

This incident was not caused by:

```text
RAM            ❌
Disk Space     ❌
CPU/AVX        ❌
Docker Network ❌
Spring Boot    ❌
Authentication ❌
```

The issue was caused by the combination of:

```text
MongoDB 8.0.9
+
Linux Kernel 7.0.0-1006-aws
```

which resulted in a MongoDB segmentation fault and Docker exit code `139`.

Changing MongoDB from `8.0.9` to `7.0` resolved the issue.

If you want, I can also make this into a **short interview-style RCA** with **Issue → Investigation → Root Cause → Resolution → Prevention**.
