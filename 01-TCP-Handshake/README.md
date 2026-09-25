# Lab 1: TCP Three-Way Handshake and Teardown

## Capture
```bash
# Capture traffic to one site, then generate it
sudo tcpdump -i any -nn -w tcp.pcap host example.com and tcp port 443 &
curl -s https://example.com > /dev/null
sudo pkill tcpdump
```
Open `tcp.pcap` in Wireshark.

## Display Filters
| Purpose | Filter |
|---|---|
| Only handshake SYNs | `tcp.flags.syn == 1 && tcp.flags.ack == 0` |
| SYN-ACK replies | `tcp.flags.syn == 1 && tcp.flags.ack == 1` |
| Connection teardown | `tcp.flags.fin == 1` |
| Resets | `tcp.flags.reset == 1` |
| One conversation | `tcp.stream == 0` |

## What to Observe
1. **SYN**: client → server, random ISN, options: MSS, Window Scale, SACK permitted.
2. **SYN-ACK**: server's own ISN, ack = client ISN + 1.
3. **ACK**: the handshake completes. Measure the RTT as the time between SYN and SYN-ACK.
4. **FIN/ACK** exchange (graceful) or **RST** (abortive).

## Troubleshooting Patterns
| Symptom in the capture | Likely cause |
|---|---|
| SYN, SYN retransmissions, no reply | Firewall silently dropping, routing problem, host down |
| SYN → immediate RST | Port closed, or a firewall sending a reset (check the TTL: does it match the server's?) |
| Handshake OK, large packets retransmitted | MTU/MSS issue across a tunnel |
| `TCP Zero Window` | Receiver application too slow to read data |

Use **Statistics → Conversations** and **Analyze → Expert Information** to spot these quickly.
