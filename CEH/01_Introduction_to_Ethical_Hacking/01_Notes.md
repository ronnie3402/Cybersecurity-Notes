# Introduction to Ethical Hacking (Module 01)

## Overview
A comprehensive foundation in ethical hacking and information security. This module covers the core bedrock of modern cybersecurity practice, including security frameworks, attack methodologies, and compliance standards.


## 1. Information Security Fundamentals
The core foundation of information security is built on the **CIA Triad**, expanding into the **IAS (Information Assurance & Security) Model**.

* **Confidentiality:** Ensuring data is accessed only by authorized individuals.
* **Integrity:** Ensuring data has not been altered or tampered with.
* **Availability:** Ensuring systems and data are accessible when needed.

**Additional IAS Triad Concepts:**
1. **Authenticity:** Verifying the true identity of a user, process, or system.
2. **Non-Repudiation:** Ensuring that a party cannot deny the authenticity of their signature or the sending of a message (Proof of action).

---

## 2. The Cyber Kill Chain Methodology
Developed by Lockheed Martin, this is a seven-stage framework used to track and understand an adversary's progression during a cyberattack.



1. **Reconnaissance:** Gathering intelligence and information about the target.
2. **Weaponization:** Creating malicious payloads tailored to the target's vulnerabilities.
3. **Delivery:** Transmitting the weapon to the target (e.g., via phishing or USB).
4. **Exploitation:** Triggering vulnerabilities to execute code on the target system.
5. **Installation:** Establishing persistence on the system (e.g., backdoors).
6. **Command & Control (C2):** Establishing a communication channel to remotely manipulate the system.
7. **Action on Objectives:** Achieving the final goal (e.g., data exfiltration, ransomware encryption).

*Understanding each stage enables security teams to implement defensive controls and break the attack chain before significant damage occurs.*

---

## 3. MITRE ATT&CK Framework
* **What It Is:** A globally accessible knowledge base documenting adversary tactics and techniques based on real-world cyber attacks.
* **Why It Matters:** It provides structured threat intelligence that helps organizations understand how attackers operate, assess defensive gaps, and prioritize security investments effectively.

---

## 4. Hacker Classifications

| Classification | Description | Typical Roles |
| :--- | :--- | :--- |
| **White Hat** | Ethical professionals who identify and fix security vulnerabilities with strict authorization. | Penetration Testers, Security Researchers, Compliance Auditors. |
| **Black Hat** | Malicious actors who exploit systems for personal gain, causing harm without authorization. | Cybercriminals, Data Thieves, Ransomware Operators. |
| **Gray Hat** | Operators in the ethical middle ground who may violate laws but often have good intentions (lack formal authorization). | Unauthorized security testers, Hacktivists. |

---

## 5. The Ethical Hacking Process
A structured methodology followed by White Hat hackers during penetration testing:



1. **Reconnaissance:** Gathering information about target systems using passive and active techniques.
2. **Scanning & Enumeration:** Identifying live systems, open ports, services, and potential vulnerabilities.
3. **Gaining Access:** Exploiting identified vulnerabilities to ethically penetrate target systems.
4. **Maintaining Access:** Testing persistence mechanisms to understand how attackers retain control.
5. **Covering Tracks:** Understanding how attackers hide evidence and testing the organization's detection capabilities.
6. **Reporting:** Documenting findings, providing remediation recommendations, and briefing stakeholders.

---

## 6. Risk & Incident Management

| Domain | Definition | Key Goal/Process |
| :--- | :--- | :--- |
| **Information Assurance (IA)** | Protecting information systems by ensuring availability, integrity, authentication, confidentiality, and non-repudiation. | Maintain trusted information flows throughout the system lifecycle. |
| **Risk Management** | Systematic identification, assessment, and mitigation of security risks to acceptable levels. | Identification → Analysis → Evaluation → Treatment → Monitoring. |
| **Incident Management** | Structured approach to detecting, responding to, and recovering from security incidents. | Preparation → Detection → Containment → Eradication → Recovery → Lessons Learned. |

---

## 7. Critical Compliance Standards

### PCI DSS (Payment Card Industry Data Security Standard)
Protects credit card transactions and reduces fraud through strict security requirements:
* **Secure Network:** Install/maintain firewall configurations; avoid vendor defaults.
* **Protect Data:** Encrypt transmission of cardholder data across open networks.
* **Vulnerability Management:** Maintain anti-virus software; develop secure systems/apps.
* **Access Control:** Restrict access by business "need-to-know" and assign unique IDs.
* **Monitoring & Policy:** Track all access to network resources; maintain a strict security policy.

### Other Major Frameworks
* **SOX (Sarbanes-Oxley):** Protects investors through accurate corporate financial disclosure and internal controls. Mandates CEO/CFO certification of financial reports.
* **GDPR (General Data Protection Regulation):** EU data privacy and security law. Grants individuals control over personal data with strict consent, breach notification rules, and hefty fines.
* **HIPAA (Health Insurance Portability and Accountability Act):** Protects patient health information (PHI). Establishes privacy rules, security standards, and breach notification requirements for healthcare entities.
