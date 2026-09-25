# Lab 4: ICMP (Ping, Traceroute, Path MTU)

## Capture
```bash
sudo tcpdump -i any -nn -w icmp.pcap icmp &
ping -c 4 8.8.8.8
traceroute -I 8.8.8.8
ping -c 2 -D -s 1472 8.8.8.8   # macOS: set DF bit (Linux: ping -M do -s 1472)
sudo pkill tcpdump
```

## Display Filters
| Purpose | Filter |
|---|---|
| Echo request / reply | `icmp.type == 8` / `icmp.type == 0` |
| TTL exceeded (traceroute hops) | `icmp.type == 11` |
| Destination unreachable | `icmp.type == 3` |
| Fragmentation needed (PMTUD) | `icmp.type == 3 && icmp.code == 4` |
| Port unreachable | `icmp.type == 3 && icmp.code == 3` |

## What to Observe
- Identifier and sequence number pair each request with its reply, and Wireshark shows the response time.
- Traceroute: the TTL increments 1, 2, 3… and each router returns *Time-to-live exceeded* from its own IP.
- `Fragmentation needed` carries the next-hop MTU. If a firewall blocks it, you get a PMTUD black hole.

## Security Angle
- Ping sweeps: one source sending echo requests to many destinations (**Statistics → Conversations**)
- ICMP tunnelling: unusually large or variable payloads: `icmp && data.len > 64`
