Provide your solution here:
## Troubleshooting Steps

### **Step 1: Verify the Monitoring Alert**
- **Objective**: Confirm that the reported memory usage is accurate.
- **Actions**:
  1. Log into the VM via SSH.
  2. Run the following commands to check memory usage:
     ```bash
     free -h
     ```
     - This shows the total, used, and free memory.
  3. Use `htop` to identify processes consuming the most memory:
     ```bash
     htop
     ```
  4. Check if the memory usage is consistent with the monitoring alert.

---

### **Step 2: Analyze NGINX Configuration**
- **Objective**: Determine if NGINX is misconfigured and consuming excessive memory.
- **Actions**:
  1. Check the NGINX configuration file:
     ```bash
     sudo nano /etc/nginx/nginx.conf
     ```
  2. Look for the following:
     - **Worker processes**: Ensure the `worker_processes` value is appropriate for the VM's CPU cores.
     - **Worker connections**: Verify that `worker_connections` is not set too high, which could lead to excessive memory usage.
     - Example:
       ```nginx
       worker_processes auto;
       events {
           worker_connections 1024;
       }
       ```
  3. Restart NGINX to apply changes:
     ```bash
     sudo systemctl restart nginx
     ```

---

### **Step 3: Check for Memory Leaks**
- **Objective**: Identify if there are memory leaks in NGINX or other processes.
- **Actions**:
  1. Use `ps` to monitor memory usage over time:
     ```bash
     ps aux --sort=-%mem | head -n 10
     ```
  2. Check NGINX logs for errors:
     ```bash
     sudo tail -f /var/log/nginx/error.log
     ```
  3. If memory usage grows continuously, consider updating NGINX to the latest version:
     ```bash
     sudo apt update && sudo apt install nginx
     ```

---

### **Step 4: Investigate Upstream Services**
- **Objective**: Determine if upstream services are causing excessive load on the NGINX load balancer.
- **Actions**:
  1. Check the NGINX access logs for unusual traffic patterns:
     ```bash
     sudo tail -f /var/log/nginx/access.log
     ```
  2. Look for:
     - High request rates from specific IPs (potential DDoS attack).
     - Large payloads or frequent retries from upstream services.
  3. If necessary, implement rate limiting in NGINX:
     ```nginx
     http {
         limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s;
         server {
             location / {
                 limit_req zone=one burst=20;
             }
         }
     }
     ```

---

### **Step 5: Check System-Level Issues**
- **Objective**: Identify if system-level issues are contributing to high memory usage.
- **Actions**:
  1. Check for zombie processes:
     ```bash
     ps aux | grep 'Z'
     ```
  2. Check for swap usage:
     ```bash
     swapon --show
     ```
     - If swap is heavily used, consider increasing swap space.
  3. Check disk space (low disk space can cause memory issues):
     ```bash
     df -h
     ```

---

## Potential Root Causes, Impacts, and Recovery Steps

### **Root Cause 1: NGINX Misconfiguration**
- **Scenario**: NGINX is configured with too many worker processes or connections, leading to excessive memory usage.
- **Impact**:
  - High memory usage could cause the VM to become unresponsive.
  - Traffic routing may fail, leading to downtime for upstream services.
- **Recovery Steps**:
  1. Adjust `worker_processes` and `worker_connections` in the NGINX configuration.
  2. Restart NGINX to apply changes.
  3. Monitor memory usage to confirm the issue is resolved.

---

### **Root Cause 2: Memory Leak in NGINX**
- **Scenario**: A bug in NGINX or a third-party module is causing a memory leak.
- **Impact**:
  - Gradual memory exhaustion could lead to service crashes.
  - Degraded performance for upstream services.
- **Recovery Steps**:
  1. Update NGINX to the latest version.
  2. Disable or replace problematic third-party modules.
  3. Restart NGINX and monitor memory usage.

---

### **Root Cause 3: High Traffic or DDoS Attack**
- **Scenario**: The VM is overwhelmed by high traffic or malicious requests.
- **Impact**:
  - High memory usage could cause the VM to crash.
  - Legitimate traffic may be dropped, leading to service disruptions.
- **Recovery Steps**:
  1. Implement rate limiting in NGINX.
  2. Use AWS WAF (Web Application Firewall) to block malicious traffic.
  3. Scale the VM vertically (increase memory) or horizontally (add more VMs).

---

### **Root Cause 4: Insufficient System Resources**
- **Scenario**: The VM does not have enough memory or swap space to handle the load.
- **Impact**:
  - The VM may become unresponsive or crash.
  - Upstream services may experience downtime.
- **Recovery Steps**:
  1. Increase the VM's memory allocation in the cloud provider's dashboard.
  2. Add or increase swap space:
     ```bash
     sudo fallocate -l 4G /swapfile
     sudo chmod 600 /swapfile
     sudo mkswap /swapfile
     sudo swapon /swapfile
     ```
  3. Monitor memory usage to confirm the issue is resolved.

---
