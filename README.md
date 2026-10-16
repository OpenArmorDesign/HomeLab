# Home Network Lab

A segmented home network built on UniFi, designed for reliability and isolation. It runs seven VLANs, redundant DNS filtering, and a dedicated server segment. I use it as my hands-on lab for learning networking, from cabling in the attic to firewall rules between segments.

## Topology

![Network diagram](network-diagram.png)

Wired and Wi-Fi client devices are not shown.

**Links:** 1 GbE to the ISP, a 10 GbE uplink from the router to the core switch, 2.5 GbE to the access points and the Plex server, 1 GbE to the other switches and servers, and Fast Ethernet to the cameras.

## Hardware and Roles

| Device | Role |
|---|---|
| Spaceballs the Router | Gateway and firewall, inter-VLAN routing |
| Planet Druidia | Core switch (10 GbE uplink, 2.5 GbE downlinks) |
| Eagle 1 | Switch for DNS and home automation hosts |
| Spaceballs the Switch | Switch for the server segment |
| Schwartz Upside / Downside | Access points (2nd floor and basement) |
| Pi-hole + Unbound (primary and secondary) | Redundant DNS filtering with a local recursive resolver |
| Home Assistant | Home automation controller |
| Plex server, backup server, LLM server, HDHomeRun | Server segment |
| PiKVM (not shown) | Remote console for the servers |

## VLANs

The network is 192.168.0.0, split into /24 segments:

| VLAN | Subnet | Purpose | Firewall rules |
|---|---|---|---|
| Management | 192.168.1.0/24 | Network gear, Pi-holes, Home Assistant | Can reach all VLANs and the internet; internet cannot initiate in |
| PiKVM (not shown) | 192.168.4.0/24 | Remote console for servers | Internet access; internet cannot initiate in |
| Clients | 192.168.5.0/24 | Trusted wired and Wi-Fi devices | Can reach all VLANs and the internet |
| Cameras | 192.168.7.0/24 | Security cameras | No internet access |
| Servers | 192.168.8.0/24 | Plex, backup, LLM server, HDHomeRun | Same access as Clients |
| Guest | 192.168.13.0/24 | Visitor Wi-Fi | Isolated from all other VLANs; limited to 150 Mbps |
| IoT | 192.168.69.0/24 | Smart-home devices | Internet access; isolated from other VLANs; internet cannot initiate in |

## Design Notes

- **Cameras have no internet access**, so a compromised camera cannot phone home.
- **IoT is isolated** from the trusted networks and cannot initiate connections to them.
- **Guest is isolated and rate-limited** to 150 Mbps so visitors cannot affect the rest of the network.
- **DNS** runs on a primary and secondary Pi-hole with Unbound as the recursive resolver, so queries stay local and filtered, and DNS survives one host going down.
- **Management** can initiate connections to every segment, but nothing from the internet can initiate inward.
- **Home Assistant** sits in Management because it acts as the controller for home automation. Because the firewall is stateful, it can reach IoT devices while IoT devices cannot start connections back.

## A Problem I Troubleshot

[TODO: Replace this with one real fault. Cover the symptom, what you checked and in what order, the root cause, and the fix. Include how long it took and what you would check first next time.]

## What I'd Improve Next

[TODO: One or two planned improvements.]

## What This Lab Covers

Physical cabling and PoE, VLAN segmentation, inter-VLAN firewall rules, DNS (Pi-hole and a recursive Unbound resolver), wireless coverage across floors, and self-hosted services. I am working toward CCNA and Security+ certification.
