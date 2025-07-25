# 🛡️ DoS Mitigation with NGINX + Traffic Analysis via Wireshark

> **_Category_**: Security Engineering  
> **_Tools Used_**: NGINX, Wireshark, NGINX Amplify, Slowloris

---

## 🎯 Objective

This project simulates a **Slowloris Denial-of-Service (DoS) attack** against an NGINX web server and shows how to detect and mitigate it.

I monitored NGINX performance using **NGINX Amplify** and captured two sets of traffic — one normal and one during the attack — to analyze with **Wireshark**.

---

## 🌱 What I Did

### 1. **🧪 Simulated Real vs. Attack Traffic**
- **Normal Traffic**: Simple page requests from a browser  
- **Attack Traffic**: Ran Slowloris to flood NGINX with many half-open TCP connections

### 2. **📈 Captured System Metrics with NGINX Amplify**
- Monitored CPU, memory, and network impact before, during, and after the attack

### 🧠 NGINX Amplify Before–During–After View

This screenshot captures the full lifecycle of the Slowloris attack:

<img width="1911" height="728" alt="image" src="https://github.com/user-attachments/assets/085dba0b-9b5c-48c7-a573-ef591033bfef" />

- **Before** (left): System shows low CPU, traffic, and load average
- **During** (middle): Sharp spikes in CPU usage, load average, and outbound traffic as the DoS attack begins
- **After** (right): Metrics decline as the attack ends and the server stabilizes

---

### 3. **🔬 Captured PCAP Files with Wireshark**
- `A.pcapng`: Normal baseline traffic  
- `B.pcapng`: Traffic during active DoS attack

### 4. **🧠 Analyzed Traffic Behavior**
- Used `tcp.flags.syn == 1 && tcp.flags.ack == 0` to identify SYN/ACK handshakes
- Compared how many ports and sessions were opening and how fast
-| File         | Packets | Timeframe             | Description                                    |
|--------------|---------|------------------------|------------------------------------------------|
| `A.pcapng`   | 40,986  | ✅ **Before Attack**     | Baseline traffic with completed TCP handshakes |
| `B.pcapng`   | 17,008  | ⚠️ **During/After Attack** | Contains SYN floods and half-open connections  |

📸 **Side-by-Side Analysis**:

**Normal Traffic (SYN/ACK filter)**  
<img width="1919" height="1032" alt="image" src="https://github.com/user-attachments/assets/2e2fdb55-567d-452c-b975-7302d495d3ee" />

**Attack Traffic (SYN flood)**  
<img width="1919" height="1034" alt="image" src="https://github.com/user-attachments/assets/bbb06514-8fbb-4f89-aecf-8c450c711dc4" />

### 5. **🛡️ Applied NGINX Rate Limiting**
- Edited `nginx.conf` with:
  ```nginx
  limit_conn_zone $binary_remote_addr zone=addr:10m;
  limit_conn addr 1;
