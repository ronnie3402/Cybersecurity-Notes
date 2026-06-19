# 📉 IP Subnetting Fundamentals: Dividing the Network

> *This guide covers the mechanics of IP subnetting, explaining how to break down large networks into smaller, more efficient segments. Understanding CIDR notation, subnet masks, and host capacity is essential for network architecture and traffic isolation.*

## 📌 What is Subnetting?
Subnetting is the process of taking a single large physical network and dividing it into multiple, smaller logical "sub-networks" (subnets). 

**Why do we do it?**
1. **Reduce Broadcast Traffic:** Smaller networks have fewer devices, reducing overall congestion.
2. **Improved Security:** Allows you to isolate sensitive devices (like database servers) from public-facing devices (like web servers).
3. **Better IP Management:** Prevents wasting large blocks of IP addresses.

---

## 🏗️ The Core Concept: CIDR Notation
Instead of writing the full subnet mask like `255.255.255.0`, we use **CIDR (Classless Inter-Domain Routing)** notation, which is just the count of the number of '1's in the subnet mask.

* **Example:** `255.255.255.0` has 24 ones in binary. So, we write it as **/24**.

### Common Subnet Masks and CIDR
| Subnet Mask | CIDR | Total IPs | Usable IPs (Total - 2) |
| :--- | :--- | :--- | :--- |
| `255.255.255.0` | /24 | 256 | 254 |
| `255.255.255.128` | /25 | 128 | 126 |
| `255.255.255.192` | /26 | 64 | 62 |
| `255.255.255.224` | /27 | 32 | 30 |

> **Note:** In every subnet, we subtract 2 from the total because:
> 1. The **first address** is the Network ID.
> 2. The **last address** is the Broadcast ID.

---

## 🧪 Practical Example: Subnetting a /24 Network
Let's take a network `192.168.1.0/24` and split it into two subnets using a **/25** mask.

**1. Original Network:** `192.168.1.0` to `192.168.1.255`
**2. Splitting into two /25 subnets:**

* **Subnet A:**
  * Network ID: `192.168.1.0`
  * First Usable IP: `192.168.1.1`
  * Last Usable IP: `192.168.1.126`
  * Broadcast ID: `192.168.1.127`

* **Subnet B:**
  * Network ID: `192.168.1.128`
  * First Usable IP: `192.168.1.129`
  * Last Usable IP: `192.168.1.254`
  * Broadcast ID: `192.168.1.255`

---

## 🔍 How to Calculate Subnets?
If you need to divide a network, use this basic logic:

1. **Borrowing Bits:** To create subnets, you "borrow" bits from the Host portion.
   * If you borrow 1 bit, you get $2^1 = 2$ subnets.
   * If you borrow 2 bits, you get $2^2 = 4$ subnets.
   * If you borrow 3 bits, you get $2^3 = 8$ subnets.

2. **Finding the Increment (Magic Number):** Subtract the last octet of your subnet mask from 256.
   * Example: Mask `255.255.255.192`
   * $256 - 192 = 64$
   * This means your subnets will jump by 64 (0, 64, 128, 192).

---

## ⚠️ Important Rules for SOC Analysts
* **Default Gateway:** Usually, the first or last usable IP in a subnet is assigned to the router interface (the gateway).
* **Isolation:** Devices in different subnets **cannot** talk to each other directly; they must pass traffic through a Layer-3 Router or a Firewall that permits the communication.
* **Troubleshooting:** If you see a device with IP `192.168.1.5` and another with `192.168.1.150` on a `/25` subnet, they **cannot** ping each other because they are in different subnets.
