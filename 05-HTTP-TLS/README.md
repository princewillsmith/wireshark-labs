# Lab 5: HTTP and TLS

## Capture
```bash
sudo tcpdump -i any -nn -w web.pcap port 80 or port 443 &
curl -s http://neverssl.com > /dev/null
curl -s https://example.com > /dev/null
sudo pkill tcpdump
```

## Display Filters
| Purpose | Filter |
|---|---|
| HTTP requests | `http.request` |
| HTTP errors | `http.response.code >= 400` |
| TLS Client Hello | `tls.handshake.type == 1` |
| Server Name (SNI) | `tls.handshake.extensions_server_name` |
| TLS alerts | `tls.alert_message` |
| Old TLS versions | `tls.handshake.version < 0x0303` |

## What to Observe
- **HTTP (cleartext):** the method, Host, User-Agent and full content are visible. **Follow → HTTP Stream**.
- **TLS:** the Client Hello shows SNI, cipher suites and supported versions. After the handshake, only encrypted *Application Data* is visible.
- The certificate chain is visible in TLS 1.2; in TLS 1.3 the certificate is encrypted.

## Decrypting Your Own TLS (lab only)
```bash
export SSLKEYLOGFILE=~/tls-keys.log
curl -s https://example.com > /dev/null
```
In Wireshark, go to **Preferences → Protocols → TLS → (Pre)-Master-Secret log filename** and select `tls-keys.log`. The HTTP/2 content becomes readable.

## Relevance to NGFW
Palo Alto App-ID uses the SNI and certificate to identify applications without decryption. SSL Forward Proxy decryption is needed for full Content-ID and threat inspection.
