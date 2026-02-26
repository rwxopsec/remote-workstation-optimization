# Remote Workstation Lab: Leveraging High-Compute Power via Encrypted Mesh

### 🌐 The Objective
I needed a way to access my desktop's massive compute power (RTX 5070ti) while on the go. Standard Windows RDP was a non-starter due to protocol performance throttling and a lack of support for high refresh rates. I built this custom stack to get full 165Hz fluidity over a secure, private tunnel without paying for premium RDP features.

### 🗺️ Network Architecture
Instead of using risky UPnP or port forwarding, I hardened the network by disabling UPnP on the router and using a mesh VPN.

`Laptop` → `Encrypted Tailscale Tunnel (WireGuard)` → `Desktop`

---

### 🛠️ The Tech Stack
* **Streaming Server:** [Sunshine](https://github.com/LizardByte/Sunshine)
* **Client:** [Moonlight](https://github.com/moonlight-stream/moonlight-qt)
* **VPN:** [Tailscale](https://tailscale.com/)
* **Display Driver:** [Iidd-Driver](https://github.com/itsmikethetech/Virtual-Display-Driver)

---

### 🚀 Implementation & Ops
* **Custom Resolution:** I added a virtual monitor by editing the `vdd_settings.xml` file to inject my native **1920x1200** resolution at **165Hz**.
* **Remote Management:** Configured the host PC for remote wake-up. I used **Terminal-based Ping Tests** across the tunnel to verify route stability before initiating sessions.
* **Security:** Disabled **UPnP** on the router to reduce the attack surface; all traffic is strictly routed through the Tailscale tunnel.

---

### 🚧 Challenges & Troubleshooting

#### 1. Bypassing UI Deadlocks
I encountered an issue where the Tailscale GUI hung and wouldn't open. I pivoted to the terminal and used `tailscale up` to force the handshake and manually cycled the service in `services.msc`.

#### 2. Mobile Hotspot Optimization
Cellular hotspots caused massive input lag and "teleporting" cursors due to jitter.
* **The Fix:** I shifted from a 165Hz performance profile to a **30 FPS / 10 Mbps** stability profile. This reduced the packet frequency, allowing the mobile gateway to handle the stream without bufferbloat.

#### 3. Sunshine "Streaming Paused" Error
Ran into an error where the stream would pause on connection. I resolved this by ensuring the Virtual Display was set as the primary output and verifying the user session was active.

---

### 📊 Performance Results
| Environment | Resolution | Refresh Rate | Bitrate | Result |
| :--- | :--- | :--- | :--- | :--- |
| **Home Wi-Fi** | 1920x1200 | 165Hz | 50 Mbps | Flawless |
| **Mobile Hotspot** | 1920x1200 | 30Hz | 10 Mbps | Stable |

---

### 💡 Conclusion
By moving away from RDP and implementing a headless virtual display, I successfully turned my RTX 5070ti desktop into a private cloud workstation accessible from anywhere.
