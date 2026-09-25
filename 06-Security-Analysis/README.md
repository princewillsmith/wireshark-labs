# Lab 6: Security Analysis Filters

Filters I use when investigating suspicious traffic or validating firewall and IDS alerts.

| Investigation | Filter |
|---|---|
| Port scan (SYNs with no handshake) | `tcp.flags.syn == 1 && tcp.flags.ack == 0 && tcp.window_size <= 1024` |
| Many RSTs (closed-port responses) | `tcp.flags.reset == 1` |
| Cleartext credentials | `ftp.request.command == "PASS" \|\| http.authorization \|\| pop.request.command == "PASS"` |
| Suspicious user agents | `http.user_agent contains "curl" \|\| http.user_agent contains "python"` |
| Executable downloads | `http.response && http.content_type contains "application/x-msdownload"` |
| Beaconing candidates | Statistics → Conversations, sort by packets; look for regular intervals |
| SMB activity (lateral movement) | `smb2` |
| Kerberos errors | `kerberos.error_code` |
| Traffic to a suspicious IP | `ip.addr == 198.51.100.23` |
| Non-standard port for TLS | `tls && !(tcp.port == 443)` |

## Workflow
1. **Statistics → Protocol Hierarchy**: what's in the capture?
2. **Statistics → Conversations / Endpoints**: who talks to whom, and how much?
3. **Analyze → Expert Information**: errors, retransmissions, resets.
4. Filter down to suspicious streams, then **Follow Stream**.
5. **File → Export Objects → HTTP** to extract transferred files for hashing and sandboxing.
6. Document IOCs (IPs, domains, hashes) for the SIEM and EDR. See [siem-edr-detection-labs](https://github.com/princewillsmith/siem-edr-detection-labs).

## Practice Captures
- Wireshark sample captures: https://wiki.wireshark.org/SampleCaptures
- Malware traffic exercises: https://www.malware-traffic-analysis.net (handle with care)
