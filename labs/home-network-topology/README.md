# Week 2 — Home Network Topology (Packet Tracer)

**What this is:** an example home network built in Cisco Packet Tracer, used to practice identifying devices, link types, and IP addressing on a typical home network.

## Topology

One router as the gateway, connected to 6 end devices — 5 over Wi-Fi, 1 wired via Ethernet.

| Device | Connection | IP | Role |
|---|---|---|---|
| Home router | — | 192.168.0.1 | Gateway |
| Laptop | Wi-Fi | 192.168.0.105 | Host |
| Smartphone | Wi-Fi | 192.168.0.100 | Host |
| Smart TV | Wi-Fi | 192.168.0.102 | Host |
| Smart Speaker | Wi-Fi | 192.168.0.103 | Host |
| Printer | Wi-Fi | 192.168.0.104 | Host |
| PC0 | Ethernet | 192.168.0.106 | Host |

All devices sit on the same `/24` subnet (`192.168.0.0/24`), assigned via DHCP from the router. All 6 IPs were confirmed individually via each device's IP Configuration page.

## What was verified

- **The Laptop needed a wireless NIC module added manually** (Linksys-WPC300N) before it could connect wirelessly — its default build shipped wired-only. The other wireless devices (Smartphone, TV, Smart Speaker, Printer) connected automatically with no module changes needed.
- **Connectivity test:** pinged the Laptop (192.168.0.105) from PC0, which is on the wired side of the network.
  ```
  Pinging 192.168.0.105 with 32 bytes of data:
  Reply from 192.168.0.105: bytes=32 time=17ms TTL=128
  Reply from 192.168.0.105: bytes=32 time=12ms TTL=128
  Reply from 192.168.0.105: bytes=32 time=16ms TTL=128
  Reply from 192.168.0.105: bytes=32 time=14ms TTL=128
  
  Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
  ```
  0% packet loss — confirms end-to-end connectivity between the wired PC and the wireless laptop through the home router, not just that each device individually has an IP.

## Files in this folder

- `home-network-topology.pkt` — the Packet Tracer file
- `topology-screenshot.png` — the logical topology view
- `ping-test-screenshot.png` — the successful connectivity test above
