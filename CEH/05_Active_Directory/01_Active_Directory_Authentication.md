# Active Directory, Authentication & Trust Relationships (Module 05)

# Introduction

A small network containing only a few users and computers can usually be managed manually. Administrators can configure users, policies, and systems individually without much difficulty.

As organizations grow, manual administration becomes impractical. Managing hundreds of users and systems across multiple locations requires centralized administration.

To solve this problem, organizations use **Windows Domains** and **Active Directory (AD)**.

---

# Windows Domain

A **Windows Domain** is a collection of users and computers managed centrally within an organization.

The main purpose of a domain is to centralize administration using **Active Directory (AD)**.

The server responsible for running Active Directory services is known as a **Domain Controller (DC)**.

## Advantages of a Windows Domain

### Centralized Identity Management

- Users can be created and managed from a single location.
- Password policies and account management become easier.

### Centralized Security Policies

- Security settings can be applied across the entire organization.
- Policies can be enforced on users and computers automatically.

---

# Protocols Used in Active Directory

## LDAP / LDAPS

LDAP is used to query and modify objects stored in Active Directory.

| Port | Purpose |
|-------|---------|
| 389 | LDAP (Unencrypted) |
| 636 | LDAPS (Encrypted using SSL/TLS) |
| 3268 | Global Catalog LDAP |
| 3269 | Global Catalog LDAPS |

---

## Kerberos

Kerberos is the default authentication protocol in modern Windows domains.

| Port | Purpose |
|-------|---------|
| 88 TCP/UDP | Ticket requests and authentication |

---

## DNS

Active Directory relies heavily on DNS.

DNS allows clients to locate Domain Controllers using SRV records.

| Port | Purpose |
|-------|---------|
| 53 TCP/UDP | Name resolution |

---

## SMB

SMB is used for:

- File sharing
- SYSVOL access
- Group Policy distribution

| Port | Purpose |
|-------|---------|
| 445 TCP | File sharing and GPO distribution |

---

## RPC / WMI

Used for:

- Remote administration
- Communication between Domain Controllers
- Replication

| Port | Purpose |
|-------|---------|
| 135 TCP | RPC Endpoint Mapper |

---

# Protocol Summary

| Protocol | Port | Primary Purpose |
|-----------|------|----------------|
| DNS | 53 | Locating Domain Controllers |
| Kerberos | 88 | User Authentication |
| RPC Endpoint | 135 | Communication Setup |
| LDAP | 389 | Directory Queries |
| SMB | 445 | File Sharing and GPO |
| LDAPS | 636 | Secure Directory Queries |

---

# Active Directory Core Concepts

## What is Active Directory?

Active Directory is a centralized database that stores information about network resources.

It performs two major functions:

- Authentication
- Authorization

---

# AD Objects

Every entity stored inside Active Directory is called an **Object**.

## User Objects

Represents:

- Employees
- Service Accounts

Examples:

- `john`
- `sqlsvc`

---

## Computer Objects

Every computer joined to a domain has its own account.

Example:

```text
HR-PC$
```

Characteristics:

- Ends with `$`
- Password changes automatically

---

## Security Groups

A collection of users and computers.

Used to simplify permission management.

Instead of assigning permissions individually, administrators assign permissions to groups.

---

# Important Default Security Groups

| Group | Description |
|--------|-------------|
| Domain Admins | Full control over the domain |
| Server Operators | Manage servers and DCs |
| Backup Operators | Perform backups regardless of permissions |
| Account Operators | Create and manage accounts |
| Domain Users | Default group for users |

---

# Organizational Units (OU)

Organizational Units are containers used to organize objects.

Common examples:

- HR
- IT
- Sales

Characteristics:

- Group Policies are linked to OUs.
- A user object exists in only one OU at a time.

---

# Default Containers

## Builtin

Contains default groups.

## Computers

Stores newly joined computers.

## Domain Controllers

Contains all Domain Controllers.

## Users

Contains default users and groups.

---

# Security Groups vs Organizational Units

| Feature | Security Groups | Organizational Units |
|----------|----------------|---------------------|
| Main Purpose | Permissions | Policy Management |
| Example | Folder Access | Wallpaper, USB Restrictions |
| Membership | Multiple Groups Allowed | Single OU Location |
| Managed Through | ACLs | GPOs |

---

# Management Tool

Active Directory is commonly managed using:

**Active Directory Users and Computers (ADUC)**

---

# Understanding OU Membership

A user can belong to multiple security groups but can exist in only one OU.

Example:

User:

```text
Rahul
```

Groups:

```text
IT_Sales
IT_Admin
```

OUs:

```text
Sales_OU
Admin_OU
```

Rahul may reside inside:

```text
Admin_OU
```

while still being a member of both:

```text
IT_Sales
IT_Admin
```

## Key Idea

- OU determines policies.
- Group membership determines permissions.

---

# Authentication in Active Directory

# Kerberos Authentication

Kerberos uses a ticket-based authentication system.

## Step 1: Authentication

User logs in.

The Key Distribution Center (KDC) issues a:

```text
TGT (Ticket Granting Ticket)
```

---

## Step 2: Service Request

User presents the TGT to request access to a service.

KDC returns:

```text
TGS (Ticket Granting Service Ticket)
```

---

## Step 3: Service Access

The client presents the TGS to the target service.

Access is granted.

## Important Notes

- Passwords never travel across the network.
- The `krbtgt` account is highly sensitive.

---

# NetNTLM Authentication

NetNTLM uses a challenge-response mechanism.

## Process

1. Client requests authentication.
2. Server sends a random challenge.
3. Client generates a response using its password hash.
4. Domain Controller validates the response.

## Important Notes

- Passwords are never transmitted directly.
- Captured responses can be abused in relay attacks.

---

# Kerberos vs NetNTLM

| Feature | Kerberos | NetNTLM |
|----------|-----------|---------|
| Authentication | Ticket-based | Challenge-Response |
| Performance | Faster | Slower |
| Security | Strong | Weaker |
| Default | Modern Windows | Legacy Compatibility |

---

# Important Terms

## KDC

Key Distribution Center responsible for issuing tickets.

## TGT

Ticket used to request additional service tickets.

## TGS

Ticket for accessing a specific service.

## SPN

Identifies a service instance in Active Directory.

---

# Common AD Attacks

## Kerberoasting

Extracting service tickets and cracking service account passwords offline.

## LLMNR/NBT-NS Poisoning

Capturing NetNTLM hashes through name resolution poisoning.

---

# Trees, Forests and Trusts

# Tree

A Tree is a collection of domains sharing a common namespace.

Example:

```text
thm.local
uk.thm.local
us.thm.local
```

---

# Forest

A Forest contains one or more Trees.

Example:

```text
thm.local
mht.com
```

---

# Enterprise Admins

Enterprise Admins have administrative privileges across the entire forest.

---

# Trust Relationships

Trusts allow users in one domain to access resources in another.

## One-Way Trust

Users from Domain B can access resources in Domain A.

Domain A users cannot access Domain B.

---

## Two-Way Trust

Users in both domains can access each other's resources.

Windows automatically creates two-way trusts within trees and forests.

---

# Quick Summary

| Concept | Description | Example |
|----------|-------------|---------|
| Domain | Single administrative unit | thm.local |
| Tree | Multiple domains with common namespace | uk.thm.local |
| Forest | Multiple trees | thm.local + mht.com |
| Trust | Resource sharing relationship | Cross-domain access |

---

# Lab Notes

## Nmap Default Scripts

```bash
nmap -sC <target>
```

### What does `-sC` do?

Runs Nmap's default NSE scripts to gather additional information such as:

- Service versions
- Configuration details
- Web titles
- Supported methods
- Domain names
- Misconfigurations

---

# RID Reference

| RID | Account/Group |
|------|---------------|
| 500 | Administrator |
| 501 | Guest |
| 512 | Domain Admins |
| 513 | Domain Users |
| 514 | Domain Guests |

Normal user accounts generally start from:

```text
RID 1000+
```
