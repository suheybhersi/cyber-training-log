# Lab Network Map — WebSploit Labs (Cisco EH 1.3)

**Date built:** 16 Aug 2026
**Environment:** Kali Linux VM running Docker, hosting the WebSploit Labs range (Omar Santos / cyberacademylabs)

Kali's main interface (talks to the Mac host): `eth0 — 192.168.64.2/24`

---

## Networks

| Network | Subnet | Kali's Gateway | Type |
|---|---|---|---|
| DMZ-SA | 192.168.0.0/24 | 192.168.0.1 | Internal (isolated) |
| SA | 10.5.5.0/24 | 10.5.5.1 | Internal (isolated) |
| root_websploit | 10.6.6.0/24 | 10.6.6.1 | Internal (isolated) |
| default bridge (docker0) | 172.17.0.0/16 | 172.17.0.1 | Internal (isolated) |

All networks confirmed `"Internal": true` in Docker — no internet access, which is correct and intentional for a hacking lab.

## Targets

| Container | Network | IP | Notes |
|---|---|---|---|
| metasploitable.pc | DMZ-SA | 192.168.0.10 | |
| mutillidae.pc | SA | 10.5.5.11 | |
| dvwa.pc | SA | 10.5.5.12 | |
| juice-shop.pc | SA | 10.5.5.13 | |
| gravemind.pc | SA | 10.5.5.14 | |
| webgoat.pc | SA | 10.5.5.15 | |
| webgoat.vm | root_websploit | 10.6.6.11 | |
| juice-shop.vm | root_websploit | 10.6.6.12 | |
| dvwa.vm | root_websploit | 10.6.6.13 | |
| mutillidae.vm | root_websploit | 10.6.6.14 | |
| gravemind.vm | root_websploit | 10.6.6.23 | |
| dvwa.sa | root_websploit | 10.6.6.100 | |
| metasploitable.vm | default bridge | 172.17.0.2 | Only target with published ports (21-23, 80, 445, 3306, etc.) |

## Connectivity check (16 Aug)
Pinged one target per network from Kali — all replied, 0% packet loss:
- 192.168.0.10 (DMZ-SA) ✅
- 10.5.5.11 (SA) ✅
- 10.6.6.11 (root_websploit) ✅

## Useful commands for this environment
```bash
docker ps                                  # list running containers
docker network ls                          # list networks
docker network inspect <network-name>      # get containers + IPs on a network
docker inspect <container-name> | grep IPAddress   # get a specific container's IP
```
