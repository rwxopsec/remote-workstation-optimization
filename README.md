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

🛡️ Security Posture & Architecture
• Zero-Trust Networking: By utilizing Tailscale (WireGuard), this workstation is never exposed to the public internet via port forwarding or UPnP.
• Encrypted Tunneling: All traffic is encapsulated within a ChaCha20-Poly1305 encrypted tunnel, ensuring the stream remains private even on untrusted cellular networks.
• Attack Surface Reduction: UPnP has been manually disabled at the router level to mitigate risks like NAT Hole Punching and unauthorized internal port mapping.
🔧 Advanced Troubleshooting: The "Hotspot Transition"
Transitioning from high-bandwidth LAN to a variable WAN (Cellular) connection introduced significant network jitter that required protocol-level tuning.
• Bufferbloat Management: High bitrate streams over cellular gateways often cause massive packet queuing. I implemented a "Travel Profile" (30 FPS / 10 Mbps) to ensure the gateway processes packets without inducing input lag.
• MTU Optimization: To account for cellular encapsulation overhead (LTE/5G), the MTU (Maximum Transmission Unit) was tuned to 1200 to prevent packet fragmentation and drop-outs.
• P2P Verification: I utilize the terminal to run tailscale status and ping commands to verify a Direct P2P connection, bypassing high-latency relay (DERP) nodes.
🗺️ Future Roadmap
• Automation: Develop a PowerShell script to automatically toggle the Virtual Display and swap primary display focus when a Sunshine session is initiated.
• Multi-Node Expansion: Adding secondary Linux-based workstations (Parrot OS nodes) to the mesh to test remote penetration testing and security auditing capabilities.
• Headless Hardening: Fully configuring the host PC for a "Headless" state, relying entirely on the Virtual Display Driver and encrypted remote management.

