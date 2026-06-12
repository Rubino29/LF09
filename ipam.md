# 📋 IPAM (IP Address Management) - IPv6-only Netzwerk

Diese IPAM dokumentiert die komplette Adressstruktur des IPv6-only Firmennetzwerks. Alle Standortnetze nutzen Global Unicast Adressen (GUA) aus dem Provider Independent Address Space `2001:db8::/32`. Die WAN-Transportnetze nutzen Unique Local Adressen (ULA) aus dem Bereich `fd00::/8`.

---

## 1. Übersicht der Subnetze

### Standorte (Global Unicast - GUA)
| Standort | Subnetz (GUA) | VLAN | Beschreibung | DHCPv6-Modus | Gateway-IP | DNS-Server |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Hamburg (HH)** | `2001:db8:10:10::/64` | 10 | Client Netz 1 | **Stateful DHCPv6** | `2001:db8:10:10::1` | `2001:db8:30:50::10` |
| **Hamburg (HH)** | `2001:db8:10:20::/64` | 20 | Client Netz 2 | **Stateful DHCPv6** | `2001:db8:10:20::1` | `2001:db8:30:50::10` |
| **Lübeck (HL)** | `2001:db8:20:30::/64` | 30 | Client Netz 3 | **Stateless DHCPv6 (SLAAC)** | `2001:db8:20:30::1` | `2001:db8:30:50::10` |
| **Lübeck (HL)** | `2001:db8:20:40::/64` | 40 | Client Netz 4 | **Stateless DHCPv6 (SLAAC)** | `2001:db8:20:40::1` | `2001:db8:30:50::10` |
| **Berlin (B)** | `2001:db8:30:50::/64` | 50 | Server Netz (DNS/Web) | **Stateful DHCPv6** | `2001:db8:30:50::1` | `2001:db8:30:50::10` |
| **München (M)** | `2001:db8:40:60::/64` | 60 | Server Netz (Web) | **Stateful DHCPv6** | `2001:db8:40:60::1` | `2001:db8:30:50::10` |

### Transportnetze (Unique Local Address - ULA)
| WAN-Verbindung | Subnetz (ULA) | Interface (DCE-Seite) | Interface (DTE-Seite) |
| :--- | :--- | :--- | :--- |
| **HH <-> HL** | `fd00:12::/64` | RT-HH-01 (`fd00:12::1` / Se0/1/0) | RT-HL-01 (`fd00:12::2` / Se0/1/0) |
| **HH <-> B** | `fd00:13::/64` | RT-HH-01 (`fd00:13::1` / Se0/1/1) | RT-B-01 (`fd00:13::2` / Se0/1/0) |
| **HH <-> M** | `fd00:14::/64` | RT-HH-01 (`fd00:14::1` / Se0/2/0) | RT-M-01 (`fd00:14::2` / Se0/1/0) |
| **HL <-> B** | `fd00:23::/64` | RT-HL-01 (`fd00:23::1` / Se0/1/1) | RT-B-01 (`fd00:23::2` / Se0/1/1) |
| **HL <-> M** | `fd00:24::/64` | RT-HL-01 (`fd00:24::1` / Se0/2/0) | RT-M-01 (`fd00:24::2` / Se0/1/1) |
| **B <-> M** | `fd00:34::/64` | RT-B-01 (`fd00:34::1` / Se0/2/0) | RT-M-01 (`fd00:34::2` / Se0/2/0) |

---

## 2. Geräte-Adressierungsdetails

### Router (ISR 4331)
| Gerät | Interface | IP-Adresse | Beschreibung | OSPFv3 Status |
| :--- | :--- | :--- | :--- | :--- |
| **RT-HH-01** | `G0/0/0.10` | `2001:db8:10:10::1/64` | Gateway Hamburg VLAN 10 | Aktiv (Passive) |
| | `G0/0/0.20` | `2001:db8:10:20::1/64` | Gateway Hamburg VLAN 20 | Aktiv (Passive) |
| | `Se0/1/0` | `fd00:12::1/64` | WAN-Link nach Lübeck | Aktiv (Active) |
| | `Se0/1/1` | `fd00:13::1/64` | WAN-Link nach Berlin | Aktiv (Active) |
| | `Se0/2/0` | `fd00:14::1/64` | WAN-Link nach München | Aktiv (Active) |
| **RT-HL-01** | `G0/0/0.30` | `2001:db8:20:30::1/64` | Gateway Lübeck VLAN 30 | Aktiv (Passive) |
| | `G0/0/0.40` | `2001:db8:20:40::1/64` | Gateway Lübeck VLAN 40 | Aktiv (Passive) |
| | `Se0/1/0` | `fd00:12::2/64` | WAN-Link nach Hamburg | Aktiv (Active) |
| | `Se0/1/1` | `fd00:23::1/64` | WAN-Link nach Berlin | Aktiv (Active) |
| | `Se0/2/0` | `fd00:24::1/64` | WAN-Link nach München | Aktiv (Active) |
| **RT-B-01** | `G0/0/0.50` | `2001:db8:30:50::1/64` | Gateway Berlin VLAN 50 | Aktiv (Passive) |
| | `Se0/1/0` | `fd00:13::2/64` | WAN-Link nach Hamburg | Aktiv (Active) |
| | `Se0/1/1` | `fd00:23::2/64` | WAN-Link nach Lübeck | Aktiv (Active) |
| | `Se0/2/0` | `fd00:34::1/64` | WAN-Link nach München | Aktiv (Active) |
| **RT-M-01** | `G0/0/0.60` | `2001:db8:40:60::1/64` | Gateway München VLAN 60 | Aktiv (Passive) |
| | `Se0/1/0` | `fd00:14::2/64` | WAN-Link nach Hamburg | Aktiv (Active) |
| | `Se0/1/1` | `fd00:24::2/64` | WAN-Link nach Lübeck | Aktiv (Active) |
| | `Se0/2/0` | `fd00:34::2/64` | WAN-Link nach Berlin | Aktiv (Active) |

### Switches (Catalyst 3650)
*Alle Switches besitzen ein Management-VLAN (SVI) im lokalen Netz, um SSH-Verbindungen von Hamburg zu akzeptieren.*
| Switch | Management-SVI | IPv6-Adresse | Standard-Gateway | Beschreibung |
| :--- | :--- | :--- | :--- | :--- |
| **SW-HH-01** | `Vlan 10` | `2001:db8:10:10::2/64` | `2001:db8:10:10::1` | Switch Hamburg |
| **SW-HL-01** | `Vlan 30` | `2001:db8:20:30::2/64` | `2001:db8:20:30::1` | Switch Lübeck |
| **SW-B-01** | `Vlan 50` | `2001:db8:30:50::2/64` | `2001:db8:30:50::1` | Switch Berlin |
| **SW-M-01** | `Vlan 60` | `2001:db8:40:60::2/64` | `2001:db8:40:60::1` | Switch München |

### Endgeräte (PCs & Server)
*Die Endgeräte beziehen ihre Adressen vollständig dynamisch über DHCPv6.*
| Endgerät | Standort / VLAN | IPv6-Adressvergabe | DNS-Server | Bemerkung |
| :--- | :--- | :--- | :--- | :--- |
| **Client-HH-1** | Hamburg (V10) | **DHCPv6 (Stateful)** | `2001:db8:30:50::10` | Bezieht IP & DNS vom Router |
| **Client-HH-2** | Hamburg (V20) | **DHCPv6 (Stateful)** | `2001:db8:30:50::10` | Bezieht IP & DNS vom Router |
| **Client-HL-3** | Lübeck (V30) | **Stateless DHCPv6 (SLAAC)** | `2001:db8:30:50::10` | IP über SLAAC, DNS über DHCPv6 |
| **Client-HL-4** | Lübeck (V40) | **Stateless DHCPv6 (SLAAC)** | `2001:db8:30:50::10` | IP über SLAAC, DNS über DHCPv6 |
| **DNS-Webserver**| Berlin (V50) | **DHCPv6 (Stateful)** | `::` / local | Erster Lease aus Serverpool (Empfohlen: `::10`) |
| **Webserver-M** | München (V60) | **DHCPv6 (Stateful)** | `2001:db8:30:50::10` | Erster Lease aus Serverpool (Empfohlen: `::10`) |

---

## 3. Dynamische Routing-Parameter (OSPFv3)

- **Prozess-ID:** 42
- **Area-ID:** 0 (Single Area Backbone)
- **Router-IDs:**
  - `RT-HH-01`: `1.1.1.1`
  - `RT-HL-01`: `2.2.2.2`
  - `RT-B-01`: `3.3.3.3`
  - `RT-M-01`: `4.4.4.4`
- **Sicherheitsmaßnahme (Passive Interfaces):** Alle LAN-Schnittstellen (Gigabit-Subinterfaces) sind als `passive-interface` definiert, sodass keine OSPF-Protokollpakete an Clients gesendet werden. Dies verhindert Manipulationen der Routing-Tabellen durch lokale Rechner.
