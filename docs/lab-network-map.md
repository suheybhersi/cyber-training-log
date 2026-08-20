# Lab Network Map — WebSploit Labs

**Environment:** Kali Linux VM running Docker, hosting the WebSploit Labs range (Omar Santos / cyberacademylabs) — a set of deliberately vulnerable apps split across isolated networks, used for practicing reconnaissance and exploitation in a safe, contained environment.

Kali's main interface (talks to the Mac host): `eth0 — 192.168.64.2/24`

---

## Networks

Found using:
```bash
docker network ls                          # list networks
docker network inspect <network-name>      # get containers + IPs on a network
```

| Network | Subnet | Kali's Gateway | Type |
|---|---|---|---|
| DMZ-SA | 192.168.0.0/24 | 192.168.0.1 | Internal (isolated) |
| SA | 10.5.5.0/24 | 10.5.5.1 | Internal (isolated) |
| root_websploit | 10.6.6.0/24 | 10.6.6.1 | Internal (isolated) |
| default bridge (docker0) | 172.17.0.0/16 | 172.17.0.1 | Internal (isolated) |

All networks confirmed `"Internal": true` in Docker — no internet access, which is correct and intentional for a hacking lab.

## Targets

Found using:
```bash
docker ps                                          # list running containers
docker inspect <container-name> | grep IPAddress   # get a specific container's IP
```

| Container | Network | IP | What it is | Status |
|---|---|---|---|---|
| metasploitable.pc | DMZ-SA | 192.168.0.10 | Metasploitable2 — intentionally vulnerable Linux system with multiple outdated/misconfigured services | ✅ Working |
| mutillidae.pc | SA | 10.5.5.11 | Mutillidae — deliberately vulnerable PHP web app covering the OWASP Top 10 | ✅ Working |
| dvwa.pc | SA | 10.5.5.12 | DVWA (Damn Vulnerable Web App) — PHP/MySQL app with adjustable difficulty, older build | ✅ Working |
| juice-shop.pc | SA | 10.5.5.13 | OWASP Juice Shop — vulnerable e-commerce app (Node.js) | ❌ Not responding on any port |
| gravemind.pc | SA | 10.5.5.14 | Gravemind — custom vulnerable app (santosomar) with a Halo theme; built around enumeration challenges | ✅ Working |
| webgoat.pc | SA | 10.5.5.15 | OWASP WebGoat — vulnerable app used for structured security lessons | ✅ Working — confirmed via landing page on port 8888, app itself at `:8080/WebGoat/` |
| webgoat.vm | root_websploit | 10.6.6.11 | Same as webgoat.pc | ✅ Working — confirmed via landing page on port 8888, app itself at `:8080/WebGoat/` |
| juice-shop.vm | root_websploit | 10.6.6.12 | Same as juice-shop.pc | ❌ Not responding on any port |
| dvwa.vm | root_websploit | 10.6.6.13 | Same as dvwa.pc, newer build (different image version than dvwa.pc/.sa) | ✅ Working |
| mutillidae.vm | root_websploit | 10.6.6.14 | Same as mutillidae.pc | ✅ Working |
| gravemind.vm | root_websploit | 10.6.6.23 | Same as gravemind.pc | ✅ Working |
| dvwa.sa | root_websploit | 10.6.6.100 | Same as dvwa.pc, older build | ✅ Working |
| metasploitable.vm | default bridge | 172.17.0.2 | Same as metasploitable.pc | ✅ Working |

**Why the same app appears multiple times:** most duplicated apps (Mutillidae, WebGoat, Gravemind, Juice Shop, Metasploitable2) have exactly one copy per network — one on SA, one on root_websploit (or DMZ-SA/default bridge for Metasploitable2) — so the same target can be practiced against from different network positions. **DVWA is the exception**: it has 3 copies, and `dvwa.vm` and `dvwa.sa` actually sit on the *same* network (root_websploit) at different IPs, not one-per-network like the others.

## Connectivity check

```bash
ping -c 4 <target-ip>                      # check basic connectivity to a target
```

Pinged one target per network from Kali — all replied, 0% packet loss:
- 192.168.0.10 (DMZ-SA) ✅
- 10.5.5.11 (SA) ✅
- 10.6.6.11 (root_websploit) ✅
- 172.17.0.2 (default bridge) ✅

## Accessing targets

Most targets are web apps, reached by browser or `curl` from inside Kali (not from the Mac host — these networks are isolated).

```bash
curl -I http://<target-ip>                 # check if something's listening on port 80 (or add :<port> for others)
```

Ports tried when the default (80) didn't respond — all actually tested in this lab:

- **WebGoat** (webgoat.pc and webgoat.vm — both confirmed identical): port 8080 alone gives a 404 (that's just the root path, not the app). Full findings:
  ```bash
  nmap -p- --open <target-ip>              # scan every port, show only the open ones — found 8080, 8888, 9001
  curl http://<target-ip>:8888             # pull full page content, not just headers — this revealed the landing page
  curl -I http://<target-ip>:8080/WebGoat  # confirmed: 302 redirect to /WebGoat/
  ```
  Port **8888** hosts a landing page linking to the actual app: WebGoat lives at `:8080/WebGoat/` (confirmed on both — redirects cleanly). The landing page also references a companion tool, **WebWolf**, meant to run on port 9090 — but 9090 isn't actually open/running, so WebWolf isn't available here.

- **Juice Shop**: tried the common port 3000; it didn't respond either.
  ```bash
  curl -I http://<target-ip>:3000          # failed to connect
  nmap -p- --open <target-ip>              # confirmed zero open ports at all
  ```
  Not responding on any port — not fixed.
