# Appendix A: Wireshark Display Filters Cheat Sheet

## Basic Syntax

### Comparison Operators

| Operator | Meaning |
|----------|---------|
| `eq` or `==` | Equal to |
| `ne` or `!=` | Not equal to |
| `gt` or `>` | Greater than |
| `lt` or `<` | Less than |
| `ge` or `>=` | Greater than or equal |
| `le` or `<=` | Less than or equal |

### Logical Operators

| Operator | Meaning |
|----------|---------|
| `and` or `&&` | Logical AND |
| `or` or `||` | Logical OR |
| `xor` or `^^` | Logical XOR |
| `not` or `!` | Logical NOT |
| `[n]`, `[...]` | Substring operator |

---

# Ethernet Filters

```text
eth.addr
eth.len
eth.src
eth.dst
eth.lg
eth.trailer
eth.ig
eth.multicast
eth.type
```

---

# IEEE 802.1Q (VLAN) Filters

```text
vlan.cfi
vlan.id
vlan.priority
vlan.etype
vlan.len
vlan.trailer
```

---

# IPv4 Filters

```text
ip.addr
ip.checksum
ip.checksum_bad
ip.checksum_good
ip.dsfield
ip.dsfield.ce
ip.dsfield.dscp
ip.dsfield.ect
ip.dst
ip.dst_host
ip.flags
ip.flags.df
ip.flags.mf
ip.flags.rb
ip.frag_offset
ip.fragment
ip.fragment.error
ip.fragment.multipletails
ip.fragment.overlap
ip.fragment.overlap.conflict
ip.fragment.toolongfragment
ip.fragments
ip.hdr_len
ip.host
ip.id
ip.len
ip.proto
ip.reassembled_in
ip.src
ip.src_host
ip.tos
ip.tos.cost
ip.tos.delay
ip.tos.precedence
ip.tos.reliability
ip.tos.throughput
ip.ttl
ip.version
```

---

# IPv6 Filters

```text
ipv6.addr
ipv6.class
ipv6.dst
ipv6.dst_host
ipv6.dst_opt
ipv6.flow
ipv6.fragment
ipv6.fragment.error
ipv6.fragment.more
ipv6.fragment.offset
ipv6.fragment.overlap
ipv6.fragment.overlap.conflict
ipv6.fragment.toolongfragment
ipv6.fragments
ipv6.fragment.id
ipv6.hlim
ipv6.hop_opt
ipv6.host
ipv6.mipv6_home_address
ipv6.mipv6_length
ipv6.mipv6_type
ipv6.nxt
ipv6.opt.pad1
ipv6.opt.padn
ipv6.plen
ipv6.reassembled_in
ipv6.routing_hdr
ipv6.routing_hdr.addr
ipv6.routing_hdr.left
ipv6.routing_hdr.type
ipv6.src
ipv6.src_host
ipv6.version
```

---

# ARP Filters

```text
arp.dst.hw_mac
arp.dst.proto_ipv4
arp.hw.size
arp.hw.type
arp.opcode
arp.proto.size
arp.proto.type
arp.src.hw_mac
arp.src.proto_ipv4
```

---

# TCP Filters

```text
tcp.ack
tcp.checksum
tcp.checksum_bad
tcp.checksum_good
tcp.continuation_to
tcp.dstport
tcp.flags
tcp.flags.ack
tcp.flags.cwr
tcp.flags.ecn
tcp.flags.fin
tcp.flags.push
tcp.flags.reset
tcp.flags.syn
tcp.flags.urg
tcp.hdr_len
tcp.len
tcp.nxtseq
tcp.options
tcp.options.cc
tcp.options.ccecho
tcp.options.ccnew
tcp.options.echo
tcp.options.echo_reply
tcp.options.md5
tcp.options.mss
tcp.options.mss_val
tcp.options.qs
tcp.options.sack
tcp.options.sack_le
tcp.options.sack_perm
tcp.options.sack_re
tcp.options.time_stamp
tcp.options.wscale
tcp.options.wscale_val
tcp.pdu.last_frame
tcp.pdu.size
tcp.pdu.time
tcp.port
tcp.reassembled_in
tcp.segment
tcp.segment.error
tcp.segment.multipletails
tcp.segment.overlap
tcp.segment.overlap.conflict
tcp.segment.toolongfragment
tcp.segments
tcp.seq
tcp.srcport
tcp.time_delta
tcp.time_relative
tcp.urgent_pointer
tcp.window_size
```

---

# UDP Filters

```text
udp.checksum
udp.checksum_bad
udp.checksum_good
udp.dstport
udp.length
udp.port
udp.srcport
```

---

# HTTP Filters

```text
http.accept
http.accept_encoding
http.accept_language
http.authbasic
http.authorization
http.cache_control
http.connection
http.content_encoding
http.content_length
http.content_type
http.cookie
http.date
http.host
http.last_modified
http.location
http.notification
http.proxy_authenticate
http.proxy_authorization
http.proxy_connect_host
http.proxy_connect_port
http.referer
http.request
http.request.method
http.request.uri
http.request.version
http.response
http.response.code
http.server
http.set_cookie
http.transfer_encoding
http.user_agent
http.www_authenticate
http.x_forwarded_for
```

---

# ICMP Filters

```text
icmp.checksum
icmp.checksum_bad
icmp.code
icmp.ident
icmp.mtu
icmp.redir_gw
icmp.seq
icmp.type
```

---

# ICMPv6 Filters

```text
icmpv6.checksum
icmpv6.checksum_bad
icmpv6.code
icmpv6.comp
icmpv6.identifier
icmpv6.option
icmpv6.option.cga
icmpv6.option.length
icmpv6.option.name_type
icmpv6.option.name_type.fqdn
icmpv6.option.name_x501
icmpv6.option.rsa.key_hash
icmpv6.option.type
icmpv6.ra.cur_hop_limit
icmpv6.ra.reachable_time
icmpv6.ra.retrans_timer
icmpv6.ra.router_lifetime
icmpv6.recursive_dns_serv
icmpv6.type
```

---

# BGP Filters

```text
bgp.aggregator_as
bgp.aggregator_origin
bgp.as_path
bgp.cluster_identifier
bgp.cluster_list
bgp.community_as
bgp.community_value
bgp.local_pref
bgp.mp_nlri_tnl_id
bgp.mp_reach_nlri_ipv4_prefix
bgp.mp_unreach_nlri_ipv4_prefix
bgp.multi_exit_disc
bgp.next_hop
bgp.nlri_prefix
bgp.origin
bgp.originator_id
bgp.type
bgp.withdrawn_prefix
```

---

# RIP Filters

```text
rip.auth.passwd
rip.auth.type
rip.command
rip.family
rip.ip
rip.metric
rip.netmask
rip.next_hop
rip.route_tag
rip.routing_domain
rip.version
```

---

# MPLS Filters

```text
mpls.bottom
mpls.cw.control
mpls.cw.res
mpls.exp
mpls.label
mpls.oam.bip16
mpls.oam.defect_location
mpls.oam.defect_type
mpls.oam.frequency
mpls.oam.function_type
mpls.oam.ttsi
mpls.ttl
```

---

# DTP Filters

```text
dtp.neighbor
dtp.tlv_len
dtp.tlv_type
dtp.version
```

---

# VTP Filters

```text
vtp.code
vtp.conf_rev_num
vtp.followers
vtp.md
vtp.md5_digest
vtp.md_len
vtp.seq_num
vtp.start_value
vtp.upd_id
vtp.upd_ts
vtp.version
vtp.vlan_info.802_10_index
vtp.vlan_info.isl_vlan_id
vtp.vlan_info.len
vtp.vlan_info.mtu_size
vtp.vlan_info.status.vlan_susp
vtp.vlan_info.tlv_len
vtp.vlan_info.tlv_type
vtp.vlan_info.vlan_name
vtp.vlan_info.vlan_name_len
vtp.vlan_info.vlan_type
```

---

# Frame Relay Filters

```text
fr.becn
fr.chdlctype
fr.control
fr.control.f
fr.control.ftype
fr.control.n_r
fr.control.n_s
fr.control.p
fr.control.s_ftype
fr.control.u_modifier_cmd
fr.control.u_modifier_resp
fr.cr
fr.dc
fr.de
fr.dlci
fr.dlcore_control
fr.ea
fr.fecn
fr.lower_dlci
fr.nlpid
fr.second_dlci
fr.snap.oui
fr.snap.pid
fr.snaptype
fr.third_dlci
fr.upper_dlci
```

---

# Frequently Used Filters (SOC / Pentesting)

| Purpose | Filter |
|----------|---------|
| DNS Traffic | `dns` |
| HTTP Traffic | `http` |
| HTTPS Traffic | `tls` |
| TCP Traffic | `tcp` |
| UDP Traffic | `udp` |
| ARP Traffic | `arp` |
| ICMP Traffic | `icmp` |
| WPA Handshake | `eapol` |
| Deauth Frames | `wlan.fc.type_subtype == 0x0c` |
| Specific IP | `ip.addr == 192.168.1.1` |
| Source IP | `ip.src == 192.168.1.1` |
| Destination IP | `ip.dst == 192.168.1.1` |
| Specific Port | `tcp.port == 80` |
| HTTP POST Requests | `http.request.method == "POST"` |
| Login Credentials | `http contains "password"` |
| User-Agent | `http.user_agent` |

---
