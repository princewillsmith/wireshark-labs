# Lab 2: DNS Resolution

## Capture
```bash
sudo tcpdump -i any -nn -w dns.pcap port 53 &
dig example.com A; dig example.com AAAA; dig nonexistent-domain-xyz.com; dig example.com MX
sudo pkill tcpdump
```

## Display Filters
| Purpose | Filter |
|---|---|
| All DNS | `dns` |
| Queries only | `dns.flags.response == 0` |
| NXDOMAIN answers | `dns.flags.rcode == 3` |
| Server failures | `dns.flags.rcode == 2` |
| Slow answers (>200 ms) | `dns.time > 0.2` |
| Specific name | `dns.qry.name contains "example"` |
| DNS over TCP (large or zone transfer) | `tcp.port == 53` |

## What to Observe
- Transaction ID matching between query and response
- Record types: A, AAAA, CNAME chains, MX
- TTL values in the answers
- The `dns.time` response-time field (Wireshark calculates it)

## Security Angle
- **DNS tunnelling / exfiltration:** very long or high-entropy subdomains, many TXT queries to one domain: `dns.qry.name.len > 50`
- **DGA malware:** many NXDOMAIN responses from one host: `dns.flags.rcode == 3`, then **Statistics → Endpoints**
- Queries to external resolvers that bypass the corporate DNS: `dns && !(ip.dst == <corp-dns>)`
