# Denial-of-Service Attacks (Module 11)

Essential knowledge for defending against one of the most disruptive cyber threats facing organizations today.

---

## 1. What Is a Denial-of-Service (DoS) Attack?

A **Denial-of-Service (DoS) attack** is a malicious attempt to disrupt normal operations by overwhelming a network, service, or server with excessive traffic or resource requests. Critically, DoS attacks don't breach a system or steal data — they simply make it unusable for legitimate users.

### Key Characteristics

- Originates from a **single source** — one machine or network
- Generally **easier to defend against** than distributed variants, since the traffic can be traced and blocked at one origin
- Still capable of causing serious operational disruption despite its relative simplicity

### Business Impact

| Impact | Description |
|---|---|
| **Downtime** | Service becomes unavailable to legitimate users |
| **Revenue Loss** | Direct financial impact from inaccessible services |
| **Reputation Damage** | Erosion of customer trust and confidence |

---

## 2. DDoS Attacks: The Modern Menace

A **Distributed Denial-of-Service (DDoS) attack** is an evolution of the basic DoS concept — instead of one source, it leverages thousands of compromised devices scattered across the globe to flood a target simultaneously. This distributed nature makes DDoS attacks far harder to block, since there's no single IP or network to filter out.

### How a DDoS Attack Unfolds

| Phase | What Happens |
|---|---|
| **1. Infection** | Malware spreads to vulnerable devices worldwide, silently compromising them |
| **2. Botnet Formation** | Infected devices become part of a remotely controllable "army" |
| **3. Coordinated Attack** | Thousands of compromised devices flood the target with traffic simultaneously |

### Real-World Impact

A 2024 resurgence of the **Mirai botnet** infected millions of IoT devices globally, causing widespread internet outages — a clear demonstration that connected devices (routers, cameras, smart home gadgets) remain a persistent and underestimated attack surface.

### DoS vs. DDoS at a Glance

| Feature | DoS | DDoS |
|---|---|---|
| **Source** | Single machine/network | Thousands of distributed, compromised devices |
| **Scale** | Limited by one source's bandwidth | Massive — aggregated bandwidth of an entire botnet |
| **Traceability** | Easier to identify and block | Difficult — traffic originates from many legitimate-looking IPs |
| **Defense difficulty** | Relatively straightforward | Requires dedicated infrastructure (CDN, scrubbing centers, Anycast) |

---

## 3. Botnets: The Attackers' Army

A **botnet** is a network of compromised devices — computers, IoT gadgets, servers — infected with malware and controlled remotely by an attacker (often called a "bot herder"). Botnets are the engine behind nearly every large-scale DDoS attack, since they give an attacker access to far more aggregate bandwidth than any single machine could produce.

### What Modern Botnets Are Made Of

- Home routers and security cameras
- Compromised personal computers
- Infected servers and cloud instances
- Smart home devices and industrial IoT equipment

### Notable Botnet Families

| Botnet | Known For |
|---|---|
| **Mirai** | Exploiting default/weak credentials on IoT devices; responsible for some of the largest DDoS attacks in history |
| **Emotet** | Originally a banking trojan, later evolved into a malware delivery platform used to build large compromised networks |
| **Qbot** | Credential theft and lateral movement, also leveraged for large-scale botnet activity |

---

## 4. Attack Techniques: The Arsenal

DoS/DDoS attacks generally fall into four broad categories, distinguished by *what* they exhaust — bandwidth, protocol resources, application resources, or amplification via third parties.

| # | Category | How It Works | Example |
|---|---|---|---|
| 1 | **Volumetric Attacks** | Flood available bandwidth with sheer traffic volume, preventing legitimate traffic from getting through | UDP floods that saturate network capacity |
| 2 | **Protocol Attacks** | Exploit weaknesses in network protocols to exhaust server-side resources like connection tables and memory | SYN floods that abuse the TCP handshake |
| 3 | **Application-Layer Attacks** | Target specific services with requests that look legitimate but overwhelm application resources at scale | HTTP floods mimicking normal user traffic |
| 4 | **Amplification Attacks** | Abuse vulnerable third-party servers to multiply a small request into a massive response directed at the victim | DNS amplification — a tiny query triggers a disproportionately large response |

> **Exam tip:** Volumetric and amplification attacks target **bandwidth**; protocol attacks target **server/network resources** (e.g., connection tables); application-layer attacks target **application-level resources** (e.g., thread pools, database connections).

---

## 5. Classic DoS Attack Examples

These are the foundational, frequently-tested attack patterns that illustrate how DoS techniques actually work at the packet level.

### Ping of Death

Sends oversized or malformed ICMP packets designed to crash a vulnerable system when it attempts to reassemble them. Largely mitigated by modern operating systems and network stacks, but remains historically significant as one of the earliest examples of a buffer overflow exploit used for denial of service.

### SYN Flood

Abuses the TCP three-way handshake (SYN → SYN-ACK → ACK). The attacker sends a continuous stream of SYN requests but never completes the handshake with the final ACK. The server keeps each "half-open" connection in its connection table waiting for a response, eventually exhausting available memory and connection slots — leaving no room for legitimate users to connect.

### Smurf Attack

Exploits ICMP echo requests with a **spoofed source IP address** (the victim's IP), broadcast to an entire network. Every device on that network replies to the echo request — but since the source IP was spoofed, all those replies flood the victim instead of the actual sender. This amplifies a single forged request into a flood of responses.

### Slowloris

An application-layer attack that opens numerous HTTP connections to a web server and sends partial requests at an extremely slow pace — just enough to keep each connection alive without ever completing it. Over time, this exhausts the server's pool of available connections, starving out legitimate users, all while using minimal attacker bandwidth.

---

## 6. Attacker Tools and Capabilities

| Tool | Type | Description |
|---|---|---|
| **LOIC (Low Orbit Ion Cannon)** | Volumetric flooding tool | A user-friendly tool popular among hacktivist groups for launching coordinated DoS attacks. Its simplicity is deceptive — when used collectively by many participants, it has real destructive potential. |
| **Slowloris** | Application-layer tool | Holds HTTP connections open indefinitely using minimal bandwidth, making it especially effective against web servers with limited connection pools. |
| **Botnet Malware Kits (e.g., Mirai)** | Botnet builder | Automates large-scale compromise by exploiting default/weak credentials on IoT devices, assembling massive armies of infected smart devices. |
| **RUDY (R-U-Dead-Yet)** | Application-layer (slow POST) tool | Targets web applications by submitting HTTP POST form data at an extremely low speed, keeping connections alive and gradually exhausting server resources. |

> **Exam tip:** Slowloris and RUDY are both "low-and-slow" application-layer attacks — the difference is Slowloris keeps connections open with incomplete *headers*, while RUDY does the same with incomplete *POST body data*.

---

## 7. Defense Tools and Techniques

| Tool/Technique | Purpose |
|---|---|
| **Web Application Firewalls (WAFs)** | Filter malicious HTTP traffic and block suspicious request patterns before they reach the application |
| **Rate Limiting** | Restricts the number of requests allowed per IP address within a given timeframe, preventing flooding |
| **Anycast Networks** | Distribute incoming traffic across multiple geographically dispersed data centers, absorbing volumetric attacks by spreading the load |
| **Cloud-Based Mitigation** | Leverages elastic, scalable bandwidth and real-time traffic analysis to identify and filter attack traffic |
| **Traffic Filtering (Blackhole Routing)** | Drops malicious traffic before it reaches critical infrastructure, sacrificing some traffic to protect the core network |

---

## 8. Best Practices for Mitigation

| Practice | Why It Matters |
|---|---|
| **Continuous Network Monitoring** | Real-time anomaly detection enables rapid response before services actually go down |
| **Incident Response Planning** | A tested, DoS/DDoS-specific response plan with clear escalation paths and communication protocols shortens recovery time |
| **Strategic Partnerships** | Working closely with ISPs and dedicated DDoS mitigation providers ensures fast traffic diversion and filtering during an active attack |
| **System Hardening** | Regular patching closes the protocol-level vulnerabilities that many DoS techniques rely on |
| **Staff Education** | Training teams to recognize early warning signs and report them promptly creates a "human firewall" as the first line of defense |

---

## 9. Staying Resilient Against DoS Threats

- **Evolving threat landscape** — DoS and DDoS remain among the most prevalent cyber threats, with attacker tactics continuously adapting to bypass new defenses.
- **Layered defense strategy** — effective protection isn't any single tool; it's technology, process, and strategic partnerships working together in an adaptive framework.
- **Proactive preparation** — investing in mitigation tools and incident response capability *before* an attack happens is what actually limits downtime and financial damage when one occurs.

---

## Key Takeaways / Exam Summary

- **DoS** = single-source attack; **DDoS** = distributed attack via a botnet of compromised devices. DDoS is harder to trace and mitigate due to its distributed origin.
- A **botnet** is the infrastructure behind most large DDoS attacks — built by infecting IoT devices, PCs, servers, and other connected hardware (Mirai is the textbook example, largely via default IoT credentials).
- Attacks fall into four categories: **Volumetric** (bandwidth), **Protocol** (connection/resource tables), **Application-Layer** (app resources), **Amplification** (third-party reflection/multiplication).
- Know the four classic attacks cold: **Ping of Death** (malformed packets), **SYN Flood** (half-open TCP connections), **Smurf Attack** (spoofed-source ICMP broadcast amplification), **Slowloris** (slow, incomplete HTTP requests).
- Common attacker tools: **LOIC** (volumetric), **Slowloris** and **RUDY** (low-and-slow application-layer), **Mirai-style botnet kits** (IoT-based botnet building).
- Defense is layered: **WAFs, rate limiting, Anycast, cloud-based mitigation, and blackhole routing** form the technical stack; **monitoring, incident response planning, ISP partnerships, patching, and staff training** form the process/people stack.
- The exam-relevant mindset: DoS/DDoS defense is never a single product — it's a **combination of technology, process, and partnerships**, built and rehearsed *before* an attack occurs.
