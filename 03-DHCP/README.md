# Lab 3: DHCP (DORA)

## Capture
```bash
sudo tcpdump -i en0 -nn -w dhcp.pcap port 67 or port 68 &
# Renew the lease
sudo ipconfig set en0 DHCP          # macOS
# sudo dhclient -r && sudo dhclient  # Linux
sudo pkill tcpdump
```

## Display Filters
| Purpose | Filter |
|---|---|
| All DHCP | `dhcp` |
| Discover | `dhcp.option.dhcp == 1` |
| Offer | `dhcp.option.dhcp == 2` |
| Request | `dhcp.option.dhcp == 3` |
| ACK | `dhcp.option.dhcp == 5` |
| NAK | `dhcp.option.dhcp == 6` |

## What to Observe
1. **Discover**: 0.0.0.0 → 255.255.255.255, client MAC in `chaddr`
2. **Offer**: offered IP (`yiaddr`), option 54 server identifier
3. **Request**: client requests the offered IP (still broadcast)
4. **ACK**: lease confirmed. Options: 1 subnet mask, 3 router, 6 DNS, 51 lease time.

With a relay (`ip helper-address`), the `giaddr` field shows the relay agent's IP.

## Troubleshooting
| Symptom | Cause |
|---|---|
| Discovers with no Offer | Server down, pool exhausted, relay missing or VLAN problem |
| NAK | Client requesting an IP from the wrong subnet (moved VLAN) |
| Two different servers offering | **Rogue DHCP server**. Mitigate with DHCP snooping. |
