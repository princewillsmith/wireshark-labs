# Wireshark Labs

Packet analysis labs covering the core protocols every network and security engineer troubleshoots, plus security-focused filters for investigations. Each lab covers how to capture, which display filters to use, what to look for, and the troubleshooting or security patterns.

## Labs

| # | Lab | Focus |
|---|---|---|
| 1 | [TCP Handshake](01-TCP-Handshake) | SYN/SYN-ACK/ACK, RST vs FIN, retransmissions, MSS/MTU problems |
| 2 | [DNS](02-DNS) | Query/response, NXDOMAIN, slow DNS, tunnelling and DGA detection |
| 3 | [DHCP](03-DHCP) | DORA, relay (`giaddr`), NAKs, rogue DHCP servers |
| 4 | [ICMP](04-ICMP) | Ping, traceroute, Path MTU Discovery black holes |
| 5 | [HTTP & TLS](05-HTTP-TLS) | SNI, TLS versions and alerts, decrypting with SSLKEYLOGFILE |
| 6 | [Security Analysis](06-Security-Analysis) | Scans, cleartext credentials, beaconing, file extraction |

## Tools
- Wireshark / `tshark`
- `tcpdump` for capturing on servers and firewalls
- Palo Alto `debug dataplane packet-diag` captures (see [network-engineer-labs](https://github.com/princewillsmith/network-engineer-labs/blob/main/PaloAlto/CLI-Cheat-Sheet.md))

## Useful `tshark` One-Liners
```bash
tshark -r cap.pcap -q -z conv,tcp                                  # TCP conversations
tshark -r cap.pcap -Y "dns.flags.rcode == 3" -T fields -e dns.qry.name | sort | uniq -c | sort -rn
tshark -r cap.pcap -Y "tls.handshake.type == 1" -T fields -e ip.dst -e tls.handshake.extensions_server_name
tshark -r cap.pcap -q -z expert                                     # expert info summary
```

## Skills Demonstrated
Packet capture and analysis · TCP/IP troubleshooting · DNS/DHCP/ICMP/TLS deep-dive · Network forensics and threat hunting
