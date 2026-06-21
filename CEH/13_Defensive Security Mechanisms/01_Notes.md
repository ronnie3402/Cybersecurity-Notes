# Bypassing IDS, Firewalls, and Honeypots

This topic is essentially cybersecurity's version of a cat-and-mouse game. The goal isn't to learn hacking for its own sake — it's to understand exactly where security controls fall short, so those gaps can be closed. These notes cover three core defensive tools (IDS, Firewalls, Honeypots), how each one works, and the specific techniques attackers use to slip past them.

---

## 1. The Three Layers of Defense — A Quick Overview

| Tool | Role | Analogy | Key Bypass Risk |
|---|---|---|---|
| **IDS** | Detection | Smoke alarm / CCTV | Fragmentation, encoding |
| **Firewall** | Prevention | Security guard with a list | Port spoofing, tunneling |
| **Honeypot** | Deception | Fake bait | Fingerprinting |

The important distinction to hold onto throughout: an **IDS detects**, a **Firewall prevents**, and a **Honeypot deceives**. Each plays a different role, and each has a different blind spot.

---

## 2. Intrusion Detection Systems (IDS)

### What It Is

An IDS is a monitoring system that scans network traffic for suspicious patterns (signatures) or unusual behavior. Critically, it only **alerts** — it does not block anything on its own.

**Analogy:** Think of it as a smoke alarm or CCTV camera. It doesn't stop a burglar from entering; it just raises the alarm once something's wrong.

### Detection Methods

**Signature-Based Detection (the "blacklist" method)**
Matches traffic against a database of known attack patterns.

> Analogy: a guard holding "WANTED" photos. If someone matching a photo walks in, the alarm goes off instantly. But a brand-new threat (zero-day) wearing an unfamiliar disguise won't be recognized.

- **Pro:** Fast and highly accurate for known threats.
- **Con:** Blind to novel, never-before-seen attacks.

**Anomaly-Based Detection (the "behavior" method)**
Learns a baseline of "normal" activity first (e.g., a server that typically handles 5GB/day), then flags significant deviations (e.g., a sudden spike to 500GB).

> Analogy: a guard who knows the building's regular rhythm. Someone arriving at 9 AM daily is normal; that same person sneaking in through the back door at 3 AM raises suspicion — even with no "wanted" photo to match.

- **Pro:** Can catch unknown/novel attacks.
- **Con:** Prone to false positives (e.g., flagging a legitimate late-night access as malicious).

**Hybrid Approach**
Combines both: checks signatures *and* monitors behavior. This is what most modern Next-Gen Firewalls/IDS solutions use, since it balances the strengths of each method.

### How an IDS Actually Sees Traffic: Mirror Ports (SPAN)

For an IDS to monitor a network without interrupting it, it needs a copy of the traffic — not the traffic itself. This is achieved through a **Mirror Port**, technically called **SPAN (Switch Port Analyzer)**.

**Analogy:** Picture a busy road. Stopping every car to inspect it would cause a massive traffic jam. Instead, imagine a large mirror beside the road that reflects every car into a side alley, where a detective watches the reflections — without ever slowing down traffic.

**How it works on a switch:** Normally, if Port A sends data to Port B, the switch delivers it directly — Port C (where the IDS sits) sees nothing. A Mirror Port configuration tells the switch: *"send an extra copy of everything passing between Port A and Port B to Port C as well."*

- **Source Port** — the port whose traffic needs monitoring
- **Destination/Mirror Port** — where the IDS or sniffer (e.g., Wireshark) is connected

**Why this matters:**
1. **Zero latency** — the IDS sits outside the traffic's actual path, so the network isn't slowed down.
2. **Invisibility** — since the IDS operates passively, an attacker has no way of knowing they're being watched.

**Example Cisco configuration:**

```bash
monitor session 1 source interface fastEthernet 0/1
monitor session 1 destination interface fastEthernet 0/5
```

This copies all traffic from Port 1 to Port 5 (the mirror port).

> **Practical tip:** When using tools like Wireshark or Snort, your laptop must be connected to the actual mirror port — otherwise you'll only see your own machine's traffic, not the network's.

### Key Limitation

**IDS systems detect but don't prevent** — they require a rapid human or automated response to actually block an identified threat.

---

## 3. IDS Evasion Techniques

Attackers use several well-established methods to slip malicious traffic past an IDS without triggering an alert.

### Fragmentation

A large malicious packet is broken into smaller fragments. The IDS inspects each fragment individually and sees nothing alarming — but the destination machine reassembles the fragments into the full, original payload once they arrive.

### Obfuscation & Encoding

Signature-based IDS only recognizes patterns already in its database. By encoding the payload (Base64, Hex, URL encoding), the attacker turns a recognizable pattern into what looks like gibberish to the detection engine.

```
Original:    SELECT * FROM users        → caught immediately
Obfuscated:  U0VMRUNUICogRlJPTSB1c2Vycw==  (Base64)   → passes through unrecognized
```

Until the IDS actively decodes the traffic, it has no idea anything malicious is there.

### Junk Data Insertion

Meaningless ("junk" or NOP) data is inserted in between malicious commands. This breaks up the continuous pattern the signature engine is trying to match.

> Analogy: a thief leaves a trail of fake footprints so the real ones get lost in the noise.

### Overlapping Fragments

A more advanced fragmentation variant where fragments are deliberately crafted to overlap and overwrite each other during reassembly. The IDS reconstructs the packet differently than the target system does — letting malicious content slip through unnoticed by the IDS while still executing on the victim.

### Timing Manipulation (Low and Slow Scanning)

Anomaly-based IDS triggers on sudden spikes (e.g., 1,000 ports scanned in one second). Attackers evade this by spreading the same scan out over hours — sometimes scanning one port, then waiting two hours before the next. This is sometimes called **"inching."** Since the activity never crosses the anomaly threshold, it looks like ordinary background traffic.

### Defensive Countermeasures

| Evasion Technique | Countermeasure | What It Means |
|---|---|---|
| **Encoding/Obfuscation** | Normalization | Decode all traffic first, *then* scan it |
| **Fragmentation/Overlapping** | Defragmentation | Reassemble traffic exactly as the target system would before inspecting it |
| **Slow scanning** | Behavioral analysis | Analyze long-term logs (weeks, not seconds) to catch patterns that unfold slowly |
| **Junk data** | Protocol validation | Verify traffic conforms to expected protocol rules; drop anything with unexplained extra data |

> **Tool tip:** IDS engines like Snort and Suricata include "pre-processors" specifically built to normalize and defragment traffic *before* it reaches the detection engine — this is what makes the above countermeasures actually work in practice.

---

## 4. Firewalls

### What It Is

A firewall is a software or hardware barrier that allows or blocks traffic based on a defined rule set (an **ACL — Access Control List**), typically inspecting ports and IP addresses.

**Analogy:** A building's security guard with a list — only people on the list, or matching specific criteria, get through.

### Rule-Based Filtering: Order Matters

Rules in an ACL are evaluated **top to bottom**, and the first matching rule wins. If an early rule says "Block All," every "Allow" rule listed below it becomes irrelevant — it will never even be reached.

**Analogy:** A bouncer first checks "are they dressed appropriately?" (port/protocol), then "are they on the blacklist?" (source IP). If the very first rule on the list says "no entry for anyone," the bouncer never even bothers checking the rest of the list.

### Stateful Inspection: Firewalls With Memory

Older (stateless) firewalls treat every packet as brand new. Modern firewalls maintain a **state table** — they remember which outbound requests were initiated from inside the network.

**Analogy — ordering pizza:**
- **Stateless:** the delivery person arrives, and the firewall blocks them because "inbound traffic is blocked," with no memory that *you* placed the order.
- **Stateful:** the firewall remembers you placed an order 15 minutes ago, so the delivery is let through without question — because it has the context.

### The 5-Step Packet Processing Workflow

| Step | Action | What Happens |
|---|---|---|
| 01 | **Arrival** | Packet hits the interface |
| 02 | **Examination** | The header is opened (IP, port, protocol are checked) |
| 03 | **Rule Match** | Rules are checked top to bottom — first match wins |
| 04 | **State Check** | Is this packet part of an existing, already-established connection? |
| 05 | **Decision** | Final verdict: Allow, or Drop/Reject |

> **Lab tip:** When configuring real firewalls (`iptables`, pfSense), always remember **implicit deny** — the final rule is almost always "deny all," meaning anything not explicitly listed is blocked by default. Statefulness also makes basic IP spoofing much harder, since a forged packet without the correct sequence number can't convincingly claim to be part of an existing connection.

### Packet Filtering vs. Application-Layer Firewalls (WAF)

These two operate at completely different depths of inspection.

**Packet Filtering Firewall (the gatekeeper)** — the most basic firewall level. It only checks the packet's outer "envelope" (header): Source IP, Destination IP, Port, Protocol. It never looks at what's actually inside the packet.

> Analogy: a bouncer at the main gate who checks your ID and which office (port) you're heading to — but never opens your bag.
>
> **Weakness:** if something malicious is hidden inside that "bag" (the payload), this firewall has no way of catching it — it only ever looks at the envelope.

**Application-Layer Firewall / WAF (the X-ray scanner)** — operates at Layer 7, inspecting the actual payload/content of the packet, not just the header.

> Analogy: an X-ray scanner that opens the bag and checks what's actually inside — if a "virus" is hidden inside a "pizza box," this is what catches it.

### Comparison Table

| Feature | Packet Filtering (Network Layer) | WAF (Application Layer) |
|---|---|---|
| **Inspects** | Header only (IP/Port) | Full payload/content |
| **Speed** | Very fast (minimal checking) | Slower (deep scanning) |
| **Protects Against** | Port scanning, IP spoofing | SQL Injection, XSS, buffer overflow |
| **Cost** | Cheap, often built into routers | Expensive, complex to set up |

**Real-world example:** for a typical website, a packet filter simply allows "yes, port 80 is open, anyone can view the site." A WAF goes further — it notices when someone types `DROP TABLE users` into a search box and blocks that specific request, even though the connection itself was allowed.

> **Defense-in-depth note:** Modern setups use both — the packet filter handles bulk traffic filtering first, and the WAF then scans whatever traffic survives, keeping the overall security tight without sacrificing performance.

---

## 5. IP Spoofing

### What It Is

IP Spoofing means hiding your real identity (IP address) and using someone else's instead — comparable to a thief wearing a friend's mask to get into a house.

**Analogy:** Imagine mailing an abusive letter, but writing your enemy's name and address in the "From" field instead of your own. The recipient assumes your enemy sent it.

In networking terms: every packet header carries a **Source IP** and **Destination IP**. IP Spoofing tampers with the header so the Source IP shows a trusted machine or victim's address instead of the attacker's own.

### Why Attackers Use It

- **Bypassing firewalls** — traffic appearing to originate from a trusted internal server may skip inspection entirely.
- **DDoS / Reflection attacks** — the attacker spoofs the *victim's* IP and sends requests to thousands of servers. All of those servers send their replies to the victim, not the attacker — flooding and jamming the victim's network. (Comparable to ordering 100 pizzas under someone else's name and address — all the delivery drivers show up at their door.)

### Does It Still Work Against Stateful Firewalls?

A natural question: if stateful firewalls track full TCP handshakes, shouldn't simple IP spoofing fail? **Mostly, yes — but it still has real uses:**

**A. Blind Spoofing (one-way only)**
A stateful firewall checks the TCP three-way handshake (SYN → SYN-ACK → ACK).
1. The attacker sends a SYN with a spoofed IP.
2. The firewall sends the SYN-ACK back to the *real* owner of that spoofed IP — not the attacker.
3. The attacker never receives that SYN-ACK, so they can't complete the handshake. No full connection gets established.

This means a complete, two-way connection genuinely can't be spoofed easily under TCP. But if the goal is simply to **crash a system**, or the protocol in use is **UDP** (which has no handshake at all), spoofing still works fine.

**B. Reflection / Amplification (DDoS)**
The attacker was never trying to establish a connection in the first place — the goal is to flood the victim. By spoofing the victim's IP and sending requests to thousands of servers, all those servers' replies land on the victim instead, overwhelming their network even though the victim never sent any of those original requests.

**C. Internal Spoofing**
Firewalls sometimes apply looser stateful checks to traffic that's already "internal." If an attacker has already compromised one machine inside the network, they can use it to spoof and deceive other internal systems.

### Defenses

| Technique | What It Does |
|---|---|
| **Ingress filtering** | Blocks packets entering the network that falsely claim to originate *from inside* it |
| **Egress filtering** | Blocks outbound packets whose source IP doesn't actually belong to the local network |
| **Sequence number validation** | Spoofed packets struggle to guess the correct TCP sequence number, which stateful firewalls rely on to confirm legitimacy |

> **Technical fact:** IP spoofing is much easier over **UDP** (no handshake required) than **TCP** (handshake makes full-connection spoofing significantly harder) — which is exactly why spoofing is so strongly associated with UDP-based DDoS attacks.

---

## 6. Firewall Evasion Techniques

### Port Manipulation

Firewalls typically apply strict rules to "non-standard" ports while leaving common ones (80, 443) wide open. Attackers run their service on a port that's almost always allowed.

> **Example:** SSH (port 22) is blocked, but port 443 (HTTPS) is open — so the attacker reconfigures their SSH server to run on port 443 instead.

### Protocol Tunneling

A blocked protocol (e.g., SSH, Telnet) is packed *inside* an allowed protocol (e.g., DNS, HTTP). The firewall only ever sees the "allowed" outer protocol — never the hidden payload inside.

> Analogy: a Trojan Horse — what's visible from outside (a DNS request) looks harmless, but a hidden payload (malicious command) travels inside it.

A common real example is **DNS Tunneling** — since almost no organization blocks DNS traffic outright, attackers exploit it as a covert channel.

### Port Manipulation vs. Port Tunneling — The Real Difference

These sound similar but solve different problems: one is about *disguise*, the other about *concealment*.

| Feature | Port Manipulation | Port Tunneling |
|---|---|---|
| **Core idea** | Just change which door (port) you use | Pack one protocol inside another |
| **Appearance** | Traffic looks exactly like what it actually is | Traffic looks like the "allowed" protocol |
| **Complexity** | Simple | More advanced |
| **Analogy** | Sneaking in wearing civilian clothes instead of a uniform | Hiding a phone inside a textbook to get it into class |

**Why this distinction matters in practice:** with simple **Port Manipulation** (e.g., a game server run on port 443), a firewall doing only basic port checks will let it through — but **Deep Packet Inspection (DPI)** will immediately notice the traffic doesn't actually look like HTTPS and flag it. With **Port Tunneling** (e.g., SSH encapsulated inside genuine HTTPS), even DPI sees only valid, well-formed HTTPS on the outside — it can't "open the box" to see the SSH traffic hidden within.

### Source Address Spoofing

Covered in detail in Section 5 — the header's sender IP is swapped for a trusted IP, making the firewall believe traffic is coming from somewhere it already trusts.

### Fragmentation Attacks

Same underlying concept as IDS fragmentation evasion (Section 3): a malicious packet is broken into small fragments that individually appear harmless. The firewall allows each fragment through; the target system reassembles them into the full attack.

> Analogy: smuggling a disassembled weapon in separate, individually-innocuous bags — and reassembling it after arrival.

### Defensive Response

| Technique | Defense | What It Does |
|---|---|---|
| **DPI (Deep Packet Inspection)** | X-ray scanning | Inspects the payload itself, not just the header — catches hidden tunneling |
| **Egress Filtering** | Exit checking | Monitors outbound traffic — e.g., flags a server suddenly sending data to an unknown IP via DNS |
| **Rule Audits** | Spring cleaning | Removes outdated/unnecessary rules, since attackers frequently exploit forgotten misconfigurations |

> **Lab tip:** In Nmap, the `-f` flag triggers fragmentation, and `-D` triggers decoy/spoofed scanning. Testing these against your own lab firewall is a good way to see whether it actually catches them.

---

## 7. WAF (Web Application Firewall) Evasion

WAF evasion is the art of crafting a malicious payload so the WAF doesn't recognize it in transit — but it still executes correctly once it reaches the target database or server.

### Character Encoding

WAFs often look for plain-text keywords (`<script>`, `SELECT`). Encoding the same characters can slip past that matching.

- **URL Encoding:** `<` → `%3C`, `>` → `%3E`
- **Double Encoding:** encoding twice (`%253C`) in case the WAF only decodes once before checking
- **Hex/Unicode encoding:** converting characters into their hex equivalents

```
Instead of: admin' OR 1=1
Hacker uses: admin'%20OR%201%3D1
```

### Case Variation

Many basic WAFs only check for lowercase keywords. Writing `SeLeCt` instead of `select` can bypass the filter while a case-insensitive backend database executes it normally regardless.

### Comment Insertion

SQL comments are used to split a keyword in two so the WAF doesn't recognize the full string, while the database engine simply ignores the comment and runs the whole command.

```sql
-- Normal query (WAF catches this):
SELECT * FROM users

-- Evasion (WAF sees "SEL" and "ECT", not "SELECT"):
SEL/**/ECT * FR/**/OM users
```

### Logic/Syntax Manipulation

WAFs often expect classic attack patterns like `1=1`. Changing the logic — `OR 5>2` or `OR 'a'='a'` — or swapping whitespace for tabs, newlines, or null bytes (`%00`) can break signature matching without changing the actual effect.

### Chunked Transfer Encoding

A more advanced technique: the malicious payload is sent in small chunks. The WAF inspects each chunk individually and finds nothing alarming — but once all chunks reach the web server, they're reassembled into the complete attack.

### Defenses

1. **Normalization** — decode all incoming traffic fully before scanning it.
2. **Strict validation (whitelisting)** — only allow data matching an expected, known-good format.
3. **Regular signature/regex updates** — keep WAF rules current against newly discovered evasion patterns.

> **Tool tip:** Burp Suite extensions like "Hackvertor" can automate testing of these encoding-based evasion techniques during lab work.

---

## 8. Honeypots

### What It Is

A honeypot is bait — a deliberately weak or exposed system set up to lure attackers in, so the defensive team can study their methods (TTPs — Tactics, Techniques, and Procedures).

**Analogy:** A fake safe placed in the middle of a room. A thief tries it, finds it opens easily, and gets distracted hunting for real treasure inside it — buying time before authorities catch on.

### Low-Interaction Honeypots (the simple trap)

Not a real operating system — just a simulation of specific services (SSH, Telnet, HTTP) that returns pre-written fake responses to any command.

> Analogy: an empty safe left outside. A thief rattles the handle, finds it locked, and assumes it's real — but won't stick around since there's nothing inside.

- **Pros:** Easy to deploy, low resource cost, zero security risk.
- **Cons:** Skilled attackers can often detect it's fake quickly (fingerprinting).

### High-Interaction Honeypots (the deep trap)

A genuinely real operating system (real Windows/Linux server) deliberately left vulnerable, allowing an attacker to fully explore, download files, and install malware — with every action logged.

> Analogy: a fake house with real furniture, a real fridge, real files. The thief settles in and steals — unaware that hidden cameras are recording every move.

- **Pros:** Reveals an attacker's full advanced tactics and tools.
- **Cons:** Higher risk — a sufficiently skilled attacker could break out of the honeypot environment and reach the real production network (**Honeypot Breakout**).

### Why Honeypots Are So Effective: The Key Stats

- **Zero false positives** — nobody legitimate has any reason to know a honeypot's address exists. Anyone who connects to it is, by definition, an attacker — unlike a firewall, which can mistakenly flag a legitimate user as a threat.
- **~85% detection rate** — many "quiet" attacks slip past IDS/Firewalls entirely, but honeypots catch them because attackers inevitably probe every IP they find during reconnaissance.
- **~3 hours average discovery time** — the internet has so many bots and scanners constantly active that almost any newly exposed, vulnerable system gets found within hours.

### Fingerprinting: How Smart Attackers Detect a Honeypot

Experienced attackers check a system before committing to an attack. If a target seems "too easy," or shows no signs of real-world file activity and user behavior, they recognize it as a honeypot and back away.

> **Practical caution:** if an Nmap scan reveals an old, outdated Windows system with every port wide open, be careful — it may well be a honeypot logging your IP and attack style.

### Tools to Know

- **Cowrie** — a popular SSH honeypot.
- **Canary Tokens** — extremely simple: create a fake file, and the moment anyone opens it, you get notified by email.

---

## 9. Why Blue Teams Need to Understand Evasion

Learning bypass techniques isn't about offense for its own sake — for a defender, this knowledge directly enables:

1. **Reducing false negatives** — catching attacks the system would otherwise miss entirely.
2. **Implementing Deep Packet Inspection (DPI)** — so fragmented or hidden payloads get caught.
3. **Enforcing strict egress filtering** — preventing data from leaking out undetected.

### Practical Tuning Reminders

- **Tuning:** anomaly detection needs ongoing tuning to avoid excessive false positives ("alert fatigue") that burn out the security team.
- **Placement:** IDS sensors should sit where all important traffic actually flows (e.g., a switch's mirror/SPAN port).
- **Updates:** signature databases need regular updates — just like keeping apps current on a phone.

---

## Key Takeaways / Exam Summary

- **IDS detects, Firewalls prevent, Honeypots deceive** — three distinct roles, three distinct blind spots.
- **Signature-based** IDS is fast but blind to new threats; **anomaly-based** catches novel attacks but risks false positives; **hybrid** systems combine both.
- IDS sees network traffic via a **Mirror Port (SPAN)** — a passive copy of traffic that adds zero latency and stays invisible to the attacker.
- Common **IDS evasion**: fragmentation, encoding/obfuscation, junk data insertion, and low-and-slow timing attacks. Countered via normalization, defragmentation, behavioral analysis, and protocol validation.
- **Firewalls** filter via ordered ACL rules (first match wins, implicit deny at the end) and use **stateful inspection** to remember legitimate connection context.
- **Packet filtering** firewalls inspect only headers (fast, shallow); **WAFs** inspect full payloads at Layer 7 (slower, deep) — real-world security uses both together (defense-in-depth).
- **IP Spoofing** fakes the source IP. Full TCP connections are hard to spoof under stateful inspection (blind spoofing), but it remains highly effective for **UDP-based attacks** and **DDoS reflection/amplification**.
- **Port Manipulation** = changing *which* port traffic uses; **Port Tunneling** = hiding one protocol *inside* another — the latter survives even Deep Packet Inspection.
- **WAF evasion** relies on encoding, case variation, comment-splitting, logic manipulation, and chunked transfer encoding — all defeated by proper normalization before inspection.
- **Honeypots** come in **low-interaction** (simulated, safe, easily fingerprinted) and **high-interaction** (real systems, rich intel, risk of breakout) flavors.
- Across every category, the recurring defensive theme is the same: **decode/normalize/reassemble traffic before inspecting it** — most evasion techniques exploit the gap between what the detector sees and what the target actually receives.
