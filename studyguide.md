# CCNA 200-301 v1.1 Master Curriculum, Subnetting & Command Guide

---

## Domain 1: Network Fundamentals (20% of Exam)

### Infrastructure Components & Roles
* **Routers:** Layer 3 devices; separate broadcast domains; make forwarding decisions based on destination IP addresses.
* **Layer 2 Switches:** Forward frames within a local network using a **MAC Address Table**.
    * *MAC Learning:* Learns the **Source MAC** address from an incoming frame.
    * *Frame Flooding:* Floods an **Unknown Destination MAC** out all ports except the arrival port.
* **Layer 3 Switches:** Multilayer switches; can perform hardware-based IP routing via Switch Virtual Interfaces (SVIs) and routed ports.
* **Next-Gen Firewalls (NGFW) & IPS:** Perform deep packet inspection, application visibility, stateful inspection, and inline threat prevention.
* **Access Points (APs):** Provide Layer 2 wireless connectivity to the wired infrastructure.
* **Controllers:** Centralized management hardware/software (e.g., Cisco DNA Center, Wireless LAN Controllers).
* **Endpoints & Servers:** Endpoints are user devices (PCs, laptops). Servers provide network services (web, file, email).
* **Power over Ethernet (PoE):** Supplies electrical power over standard copper Ethernet cabling to endpoints like IP phones and Access Points.
    * *Power Sourcing Equipment (PSE):* Usually the switchport supplying power.
    * *Powered Device (PD):* The endpoint consuming power (e.g., IP Phone, AP).

### Architecture & Topologies
* **Two-Tier (Collapsed Core):** Combines the **Core** and **Distribution** layers into one physical layer, connecting down to the **Access** layer. Saves cost in small-to-medium networks.
* **Three-Tier Campus:** Distinct **Core** (ultra-fast switching backbone), **Distribution** (routing boundaries, ACL policies, and VLAN aggregation), and **Access** (workgroup port connectivity) layers.
* **Spine-Leaf (Data Center):** Two-tier architecture where every Leaf switch (access) connects to every Spine switch (backbone). Optimizes predictable **East-West traffic** (server-to-server).
* **WAN:** Connects geographically separated local networks over long distances (e.g., MPLS, Metro Ethernet, Broadband).
* **SOHO:** Small office/home office architectures; typically relies on a single integrated router/switch/AP appliance.
* **On-Premises vs. Cloud:** On-prem requires physical hardware lifecycle ownership and CapEx; Cloud moves infrastructure off-site to a multi-tenant model using OpEx.

### Cabling, Interfaces, & Issues
* **Single-Mode Fiber (SMF):** Small core ($9\mu\text{m}$), utilizes lasers, very long distances, low attenuation.
* **Multi-Mode Fiber (MMF):** Larger core ($50\mu\text{m}$ or $62.5\mu\text{m}$), utilizes LEDs, shorter distances (data centers/campuses), susceptible to modal dispersion.
* **Copper Cables:** Category cables (Cat5e, Cat6); limited to 100 meters maximum distance; susceptible to EMI.
* **Interface Troubleshooting:**
    * *Collisions/Late Collisions:* Indicative of a **Duplex Mismatch** (one side Full, one side Half).
    * *Input Errors / CRC Errors:* Indicative of bad cabling, loose connections, or electrical interference.
    * *Speed Mismatch:* Link will drop entirely (Down/Down).

### Core IP Addressing & Transport Protocols
* **TCP vs. UDP:** TCP (Transmission Control Protocol) is connection-oriented, reliable, utilizes sequence numbers, windowing, and a 3-way handshake (`SYN` $\rightarrow$ `SYN-ACK` $\rightarrow$ `ACK`). UDP (User Datagram Protocol) is connectionless, lightweight, unreliable, and optimal for real-time traffic like Voice/Video.
* **Private IPv4 Ranges (RFC 1918):**
    * **Class A:** `10.0.0.0` to `10.255.255.255`
    * **Class B:** `172.16.0.0` to `172.31.255.255`
    * **Class C:** `192.168.0.0` to `192.168.255.255`
* **IPv6 Address Architecture:** 128-bit hexadecimal addresses.
    * **Global Unicast Address (GUA):** Publicly routable; starts with `2xxx::` or `3xxx::`.
    * **Unique Local Address (ULA):** Private/internal; starts with `fc00::` or `fd00::`.
    * **Link-Local Address (LLA):** Non-routable; auto-configured on every IPv6 interface; starts with `fe80::`.
    * **Anycast:** One-to-nearest communication targeting a shared service address.
    * **Multicast:** One-to-many communication; starts with `ffxx::`.
    * **Modified EUI-64:** Creates an interface ID automatically by splitting a 48-bit MAC address in half, inserting `ff:fe` in the middle, and flipping the 7th bit (Universal/Local bit).

### Wireless Principles
* **RF (Radio Frequency):** Electromagnetic waves used to transmit data wirelessly over space.
* **Non-Overlapping Wi-Fi Channels:**
    * *2.4 GHz Band:* 20 MHz wide channels; only channels **1, 6, and 11** do not overlap.
    * *5 GHz / 6 GHz Bands:* Provide significantly more non-overlapping channels to minimize co-channel interference.
* **SSID (Service Set Identifier):** The human-readable text name assigned to a wireless network broadcast.
* **Encryption Types:** Traditional WEP (insecure), WPA (TKIP), WPA2 (AES-CCMP), and WPA3 (GCMP with SAE).

### The IPv4 Subnetting Emergency Toolkit
Cisco will test your ability to look at an IP/prefix and determine boundaries under time constraints. Follow these three steps:

#### 1. The Core Formulas
* **Total IP Addresses in a Subnet:** $2^H$ (where $H$ is the number of remaining Host bits).
* **Usable Host Addresses:** $2^H - 2$ (subtracting the unassignable **Subnet ID** and **Broadcast Address**).
* **Total Created Subnets:** $2^S$ (where $S$ is the number of borrowed Subnet bits).

#### 2. The "Magic Number" Method
To find the boundaries of any subnet, find its **Block Size** (Magic Number):
1. Identify which octet the mask changes in (the "interesting octet").
2. Subtract that octet's mask value from **256**.
3. **Example:** Subnet mask is `255.255.255.224` (/27).
   * The changing value is in the 4th octet (`.224`).
   * $256 - 224 = 32$. Your **Magic Number is 32**.
   * Subnets increment by 32: `.0, .32, .64, .96, .128, .160...`
   * If given host IP `192.168.1.142`, it falls between `.128` and `.160`.
     * **Subnet ID:** `192.168.1.128`
     * **First Usable Host:** `192.168.1.129`
     * **Broadcast Address:** `192.168.1.159` (one less than the next subnet ID)
     * **Last Usable Host:** `192.168.1.158`

#### 3. High-Yield Subnet Cheat Sheet

| Prefix | Subnet Mask | Interesting Octet | Magic Number (Increment) | Total IPs | Usable Hosts |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **/25** | 255.255.255.128 | 4th Octet | **128** | 128 | 126 |
| **/26** | 255.255.255.192 | 4th Octet | **64** | 64 | 62 |
| **/27** | 255.255.255.224 | 4th Octet | **32** | 32 | 30 |
| **/28** | 255.255.255.240 | 4th Octet | **16** | 16 | 14 |
| **/29** | 255.255.255.248 | 4th Octet | **8** | 8 | 6 |
| **/30** | 255.255.255.252 | 4th Octet | **4** | 4 | 2 (Point-to-Point links) |
| **/32** | 255.255.255.255 | 4th Octet | **1** | 1 | 1 (Host Route / Loopback) |
| | | | | | |
| **/21** | 255.255.248.0 | 3rd Octet | **8** | 2048 | 2046 |
| **/22** | 255.255.252.0 | 3rd Octet | **4** | 1024 | 1022 |
| **/23** | 255.255.254.0 | 3rd Octet | **2** | 512 | 510 |
| **/24** | 255.255.255.0 | 3rd Octet | **1** | 256 | 254 |

### Domain 1: Essential Commands
* `show ip interface brief` - View the device interface status, IP assignments, and line protocol states.
* `show ipv6 interface brief` - Check status of active IPv6 ports and Link-Local assignments.
* `show version` - Verify running hardware characteristics, configuration files, and software versions.
* `show running-config` - View the full configuration running live in volatile RAM.
* `username admin privilege 15 secret Cisco123` - Configure a local privilege 15 user account with encrypted secret.
* `line console 0` -> `login local` - Secure local console port access with the database.
* `line vty 0 4` -> `login local` -> `transport input ssh` - Force SSH usage and secure remote management access lines.
* `ip address 192.168.1.1 255.255.255.0` - Interface-level command to assign an IPv4 address.
* `ipv6 address 2001:db8:acad:1::1/64` - Interface-level command to assign a Global Unicast IPv6 address.

---

## Domain 2: Network Access & Switching (20% of Exam)

### VLANs & Inter-Switch Trunking
* **VLAN Ranges:** Normal range is `1-1005` (stored in `vlan.dat`). Extended range is `1006-4094` (stored in the running configuration).
    * *Default VLAN:* VLAN 1 by default; all access ports belong to it out-of-the-box.
* **Access Ports:** Assigned to a single data VLAN, or a data + voice VLAN pairing for IP phone deployments.
* **Trunk Ports & 802.1Q:** Trunks carry multi-VLAN traffic over a single physical link. 802.1Q inserts a **4-byte tag** into the standard Ethernet frame header.
* **Native VLAN:** Frames belonging to the Native VLAN are sent across a trunk line entirely **untagged**. Both sides of a trunk *must* match their Native VLAN configuration, or traffic leakage and loop errors occur.
* **Inter-VLAN Connectivity:** Can be achieved via an external router using subinterfaces (**Router-on-a-Stick**) or via Layer 3 Switch routing tables utilizing SVIs.

### Discovery Protocols
* **CDP (Cisco Discovery Protocol):** Cisco proprietary; Layer 2; enabled by default; broadcasts device information to directly connected neighbors.
* **LLDP (Link Layer Discovery Protocol):** IEEE 802.1AB industry standard alternative. Requires explicit enablement (`lldp run` globally, or `lldp transmit` / `lldp receive` on interfaces).

### EtherChannel (Link Aggregation)
* Combines up to 8 parallel physical links into 1 logical interface to load-balance traffic and prevent Spanning Tree loops.
* **LACP (Link Aggregation Control Protocol):** Open industry standard. Modes:
    * `Active`: Initiates negotiation.
    * `Passive`: Listens; only responds if requested by an active partner.
    * *Rule:* At least one side must be set to `Active` to successfully form an LACP bundle.

### Spanning Tree Protocol (STP / Rapid PVST+)
Prevents Layer 2 loop topologies in redundant networks by placing specific ports into a blocking state.
* **The Election Process:**
    1.  **Elect one Root Bridge:** The switch with the lowest **Bridge ID** (Priority + MAC address).
    2.  **Select Root Ports (RP):** Every non-Root switch selects exactly *one* port closest to the Root Bridge based on path cost.
    3.  **Select Designated Ports (DP):** Every network segment selects exactly *one* forwarding port based on the lowest path cost back to the Root.
    4.  **Block remaining links:** Ports left over are put into the Alternate/Backup **Blocking** state.
* **STP Port Toolkits:**
    * **PortFast:** Configured on access ports connected to end hosts. Moves the port straight from blocking to forwarding instantly, skipping delay cycles.
    * **BPDU Guard:** Shuts down a PortFast-enabled port (places it into `err-disabled`) if an unexpected BPDU frame is received on it.
    * **Root Guard:** Prevents downstream rogue switches from claiming the Root Bridge status.
    * **Loop Guard:** Prevents alternate or root ports from becoming designated forwarding ports if they stop receiving BPDUs.

### Cisco Wireless Architectures & AP Modes
* **Autonomous AP Architecture:** Standalone deployment where each individual AP must be manually configured box-by-box.
* **Centralized/Cloud AP Architecture:** Split-MAC architecture; APs terminate to a central Wireless LAN Controller (WLC) using **CAPWAP (Control and Provisioning of Wireless Access Points)** tunnels.
    * *Control Plane:* CAPWAP UDP Port 5246.
    * *Data Plane:* CAPWAP UDP Port 5247.
* **AP Operation Modes:**
    * `Local` (Default): Actively serves wireless clients while scanning channels.
    * `FlexConnect`: Allows local switching of data traffic if the WAN link to the central WLC drops.
    * `Monitor`: Dedicated sensor mode; acts as an IDS to locate rogue APs and evaluate RF coverage.
    * `Sniffer`: Captures wireless frames on a specific channel and forwards them to a network analyzer.

### Wireless LAN GUI Configuration Parameters
* **WLAN Creation:** Define the SSID name, map it to an internal VLAN interface, and choose status (Enabled/Disabled).
* **Security Settings:** Choose layer security (WPA2/WPA3), key management (PSK or 802.1X Enterprise), and encryption.
* **QoS Profiles:** Assign prioritization bands to traffic types.
    * `Platinum`: Voice traffic.
    * `Gold`: Video traffic.
    * `Silver` (Default): Best-effort data.
    * `Bronze`: Low-priority guest access.
 
## Cisco WLC GUI Architecture & Configuration Navigation Flow

When you log into a Cisco WLC via HTTP/HTTPS, you are taken to a dashboard interface. To configure client connectivity, you must navigate through a specific sequence of tabs located at the top of the GUI:

[ Monitor ] ──► [ WLANs ] ──► [ Controller ] ──► [ Wireless ] ──► [ Security ] ──► [ Management ]
                    │
            (Create New WLAN)

### Step 1: The WLANs Tab (Creation & Mapping)
This is where you initially build or modify a wireless network broadcast interface.
* **WLANs > Create New:** You must specify the **Profile Name** (administrative tracking name) and the **SSID** (the actual text string broadcasted to user devices over the air).
* **General Sub-Tab:**
    * **Status Checkbox:** By default, a newly created WLAN is *Disabled*. You must explicitly check **Enabled** for the APs to start active beaconing and broadcasting the SSID.
    * **Interface/Interface Group:** This maps the wireless network directly to a wired VLAN gateway (e.g., mapping the "Guest_SSID" to the internal dynamic `VLAN_100_Guest` interface).

### Step 2: The Security Tab (Layer 2 vs. Layer 3 Encryption)
Once the SSID is mapped to a back-end interface, navigate to the Security sub-tab to determine access restrictions. It is divided into sub-sections:
* **Layer 2 Security Dropdown:**
    * Changing this from *None* to **WPA+WPA2** or **WPA3** activates over-the-air privacy.
    * **Auth Key Management (AKM):** Here you choose the deployment type:
        * **PSK (Pre-Shared Key):** Used for personal/home setups. You must enter a text password (ASCII or Hex).
        * **802.1X:** Used for Enterprise environments. This tells the WLC to forward client authentication requests outward to an external **AAA/RADIUS server** via a shared secret.
* **Layer 3 Security Dropdown:** Used if you want to enforce a **Web Policy / Captive Portal** (typical for guest Wi-Fi networks where users must agree to terms or type a guest password on a browser webpage before getting internet access).

### Step 3: The QoS Tab (Traffic Prioritization Profiles)
This dropdown tab dictates how the WLC prioritizes different traffic classes using Wi-Fi Multimedia (WMM) markings. You must memorize these 4 distinct system profiles:
* **Platinum (Voice):** Highest priority; explicitly reserved for time-sensitive voice-over-IP (VoIP) traffic lines.
* **Gold (Video):** High priority; optimized for video streaming applications and low-latency conferencing tools.
* **Silver (Best Effort):** The default setting; handles normal web browsing, file transfers, and general user data traffic.
* **Bronze (Background):** Lowest priority; typical layout choice for guest networks or non-critical automated software backup traffic to ensure they do not choke corporate resources.

### Step 4: The Advanced Tab (Specialized Features)
This tab houses high-level features that change client optimization behaviors:
* **FlexConnect Local Switching:** If you have lightweight APs at a remote branch site running in FlexConnect mode, checking this box allows the AP to bridge client data traffic straight into the local branch switch locally, rather than tunneling it back across the WAN to the central WLC.
* **Aironet IE:** A Cisco-proprietary extension that communicates AP capabilities to Cisco wireless clients. If non-Cisco clients struggle to connect to an SSID, this is often the feature that needs to be disabled.

### Wireless Client Association Process
Before a wireless client can transmit data over an access point, it must complete this strict sequence:
1.  **Discovery (Scanning):** The client listens for **Beacons** broadcast by the AP (Passive Scanning) or sends out **Probe Requests** looking for specific SSIDs (Active Scanning).
2.  **Authentication:** The client proves identity to the AP via open-system or pre-shared key frame exchanges.
3.  **Association:** The client sends an **Association Request** frame; the AP verifies capabilities and replies with an **Association Response** frame, assigning an Association ID to anchor the connection slot.

### Domain 2: Essential Commands
* `show vlan brief` - Verify assigned VLAN groupings and active access ports.
* `show interfaces trunk` - Check active trunking lines, native matching, and encapsulation parameters.
* `show spanning-tree` - Identify the Root Bridge ID, path costs, port states, and system priorities.
* `show etherchannel summary` - Check logical interface bundling states and protocol health.
* `show cdp neighbors` / `show lldp neighbors` - Display direct neighbor status via local discovery advertisements.
* `switchport mode access` -> `switchport access vlan 10` - Assign an access port to a single data VLAN.
* `switchport voice vlan 150` - Enable an access port to support an IP Phone voice deployment simultaneously.
* `switchport mode trunk` -> `switchport trunk native vlan 99` - Force an interface to trunk and alter the Native assignment.
* `spanning-tree mode rapid-pvst` - Migrate from legacy STP to Rapid PVST+.
* `spanning-tree vlan 10 priority 4096` - Force a local switch to claim the Root Bridge role for a VLAN.
* `spanning-tree portfast` -> `spanning-tree bpduguard enable` - Optimize and protect edge ports connected to hosts.
* `channel-group 1 mode active` - Combine interfaces into a single logical channel bundle using LACP parameters.

---

## Domain 3: IP Connectivity & Routing (25% of Exam)

### The Routing Table Components
When a router evaluates where to forward an incoming packet, it analyzes these specific structural attributes:
* **Routing Protocol Code:** Indicates how the route was learned (e.g., `C` = Connected, `S` = Static, `O` = OSPF, `D` = EIGRP).
* **Prefix & Network Mask:** The destination subnet block address.
* **Next Hop / Exit Interface:** The next gateway IP or local port used to forward the traffic out.
* **Administrative Distance (AD):** The trustworthiness rating of the routing source. Lower numbers are preferred.
* **Metric:** The cost value calculation calculated by the protocol to choose the best path among identical sources.
* **Gateway of Last Resort:** The default gateway route (`0.0.0.0/0`) used if no explicit prefix match exists.

### Path Selection Hierarchy
1.  **Longest Prefix Match:** The router searches for the route entry that matches the destination IP with the longest, most specific subnet mask. **This always wins first.**
2.  **Administrative Distance (AD):** If multiple routes to the exact same prefix exist from different sources, the router picks the path with the **lowest AD**.
3.  **Metric:** If the AD is identical (same routing protocol), the router selects the path with the **lowest metric**.

### Administrative Distance Quick Reference
* Directly Connected: `0`
* Static Route: `1`
* EIGRP (Internal): `90`
* OSPF: `110`
* RIP: `120`

### Static Routing Options
* **Default Route:** `ip route 0.0.0.0 0.0.0.0 <Next-Hop-IP>`
* **Host Route:** `ip route 192.168.1.50 255.255.255.255 <Next-Hop-IP>`
* **Floating Static Route:** A backup route with a manually raised AD configured to remain hidden unless the primary dynamic protocol drops.
    * *Example:* `ip route 0.0.0.0 0.0.0.0 172.16.1.1 115` (OSPF's AD of 110 beats this backup path unless it fails).

### Single-Area OSPFv2 Operations
* Link-state interior gateway routing protocol; uses the **Dijkstra Shortest Path First (SPF)** algorithm.
* **Metric:** Cost, calculated as $\text{Cost} = \frac{\text{Reference Bandwidth (Default } 100 \text{ Mbps)}}{\text{Interface Bandwidth}}$.
* **Neighbor Adjacency Requirements:** Neighbors must explicitly match their Area ID, Subnet, Hello/Dead Timers, MTU sizes, and Authentication parameters over a shared link.
* **DR/BDR Elections (On Broadcast Networks):**
    * Router with the highest **Interface Priority** becomes the Designated Router (DR).
    * If priorities tie, the highest **Router ID (RID)** wins.
    * *Note:* A priority setting of `0` permanently exempts a router from the election process.

### Domain 3: Essential Commands
* `show ip route` / `show ipv6 route` - View the internal dynamic and static forwarding tables.
* `show ip ospf neighbor` - Verify active OSPF neighbors, states, and DR/BDR roles.
* `show ip ospf interface brief` - Display a summary of active OSPF-enabled interfaces, timers, and area values.
* `ip route 0.0.0.0 0.0.0.0 192.168.1.254` - Create a standard default route pointing to a next-hop gateway address.
* `ipv6 route ::/0 2001:db8:acad:1::fe` - Create an IPv6 default route.
* `ip route 10.50.50.12 255.255.255.255 192.168.1.254` - Define a specific host route targeting a single system endpoint.
* `ip route 0.0.0.0 0.0.0.0 172.16.1.1 125` - Create a floating static default route with an increased backup AD.
* `router ospf 1` -> `router-id 1.1.1.1` - Enable the global routing process and assign a custom hardcoded OSPF ID.
* `network 192.168.10.0 0.0.0.255 area 0` - Network statement syntax method to inject active interfaces into OSPF Area 0.
* `ip ospf 1 area 0` - Alternate method to activate OSPF directly within a specific interface configuration window.

---

## Domain 4: IP Services (10% of Exam)

### NAT (Network Address Translation)
* **Static NAT:** Manual 1-to-1 mapping between an internal private IP and an external public IP.
* **Dynamic NAT:** Maps internal private IPs to an available pool of public addresses dynamically.
* **PAT (Port Address Translation / NAT Overload):** Maps thousands of internal private IPs to a single public IP address by tracking unique Layer 4 source port numbers.

### Core Infrastructure Services
* **NTP (Network Time Protocol):** Synchronizes time clocks across network infrastructure components operating in client/server mode. Uses **Stratum levels** to indicate distance from an atomic reference clock source (Stratum 1 is directly attached to the clock; higher numbers are less accurate).
* **DHCP (Dynamic Host Configuration Protocol):** Automates client IP assignment.
    * *The Exchange Process:* `DORA` (Discover $\rightarrow$ Offer $\rightarrow$ Request $\rightarrow$ Acknowledgment).
    * *DHCP Relay Agent:* The interface command `ip helper-address <DHCP-Server-IP>` intercepts local broadcast DHCP Discovers and forwards them as a unicast packet directly to a remote DHCP server across network boundaries.
* **DNS (Domain Name System):** Resolves human-readable domain names into IP addresses.
* **SNMP (Simple Network Management Protocol):** Monitored by a central Network Management Station (NMS) using managers and agents.
    * *Trap:* An unsolicited alert sent immediately by an agent to the manager when an event occurs. Unconfirmed.
    * *Inform:* A confirmed trap that requires an active acknowledgment response back from the NMS manager.
* **Syslog Features:** Logs messages across 8 levels (`0-7`) using facilities and severity ratings. Remember the phrase: **"Every Awesome Cisco Engineer Will Need Dog Crying"**
    * `0`: Emergency (System unusable)
    * `1`: Alert (Immediate action needed)
    * `2`: Critical
    * `3`: Error
    * `4`: Warning
    * `5`: Notice
    * `6`: Informational
    * `7`: Debugging (Highest CPU load)
* **Remote Access Protocols:** Telnet (Insecure, clear-text, TCP port 23) vs. **SSH (Secure Shell, encrypted, TCP port 22)**.
* **File Transfer Protocols:** **TFTP** (UDP port 69, connectionless, lightweight, no authentication) vs. **FTP** (TCP ports 20/21, connection-oriented, requires user authentication credentials).

### Quality of Service (QoS)
* **Classification & Marking:** Identifies traffic types and marks headers using Class Selector (CS) or DiffServ Code Points (DSCP) inside the IPv4 Type of Service (ToS) byte.
* **Congestion Management:** *Queuing* schedules marked packets into queues (e.g., Strict Priority Queuing for real-time voice lines).
* **Traffic Conditioning:**
    * *Shaping:* Buffers excess micro-burst traffic in memory queues to produce a smooth output rate line.
    * *Policing:* Immediately drops or re-marks traffic that exceeds a specified maximum profile limit.

### Domain 4: Essential Commands
* `show ip nat translations` - Check active NAT translations and logical port mapping statistics.
* `show ntp status` / `show ntp associations` - Verify time tracking status, system stratum, and reference clock connectivity.
* `show dhcp lease` - Check status of local interface IP track assignments dynamically leased from an upstream provider.
* `ip nat inside source list 1 interface GigabitEthernet0/1 overload` - Map an internal ACL list to an exit interface to execute PAT.
* `ip nat inside` / `ip nat outside` - Interface-level markers used to delineate NAT boundaries.
* `ip helper-address 10.10.100.5` - Enable a DHCP Relay Agent over an internal access gateway interface.
* `ip address dhcp` - Configure an interface to dynamically acquire its addressing from an external provider.
* `logging host 10.10.200.20` - Direct logging messages immediately to an external remote storage syslog host.

---

## Domain 5: Security Fundamentals (15% of Exam)

### Core Security Concepts
* **Threat:** Any potential danger or event that could cause harm to an asset.
* **Vulnerability:** A weakness or flaw in code, configuration, or physical access control that leaves an asset exposed to exploit.
* **Exploit:** The specific mechanism, script, or action used to take advantage of a vulnerability.
* **Mitigation:** Policies, software updates, or security features designed to reduce the risk or impact of an attack.
* **Security Program Elements:** User awareness training, clear access control policies, physical security safeguards, and multi-factor authentication (MFA) deployment options.

### Access Control Lists (ACLs)
* Every ACL ends with an invisible, implicit `deny any` statement. If a packet does not match a permit rule, it is dropped.
* **Standard ACLs (1-99 / 1300-1999):** Inspect **Source IP addresses only**. Place them as close to the **destination** network as possible.
* **Extended ACLs (100-199 / 2000-2699):** Inspect Source/Destination IPs, Protocols (TCP/UDP), and destination port numbers. Place them as close to the **source** network as possible.

### Layer 2 Security Features
* **Port Security:** Restricts switchport input to authorized MAC addresses.
    * *Sticky MAC:* Learns MAC addresses dynamically and writes them directly into the running configuration.
    * *Violation Modes:*
        * `Protect`: Drops unauthorized frames silently.
        * `Restrict`: Drops unauthorized frames, increments the violation counter, and generates a syslog alert.
        * `Shutdown` (Default): Drops frames, logs alerts, and places the interface into an `err-disabled` state.
* **DHCP Snooping:** Configures switchports as trusted or untrusted. Untrusted ports are blocked from sending DHCP Server messages (Offers/ACKs), preventing rogue DHCP servers. Builds a binding tracking database.
* **Dynamic ARP Inspection (DAI):** Intercepts incoming ARP requests on untrusted ports and validates their logical bindings against the DHCP Snooping database to prevent man-in-the-middle spoofing attacks.

### AAA Framework
* **Authentication:** Who are you? (Username/Password validation).
* **Authorization:** What are you allowed to do? (Privilege levels/Command restrictions).
* **Accounting:** What did you do? (Logs commands and access session runtimes).
* *Protocols:* **TACACS+** (Cisco proprietary, encrypts the entire packet, uses TCP port 49, separates AAA functions) vs. **RADIUS** (Open standard, encrypts only the password field, uses UDP, combines Authorization and Authentication).

### VPN Architectures
* **Remote Access VPN:** Connects individual teleworkers securely to the corporate gateway using software clients (AnyConnect over SSL/TLS or IPsec).
* **Site-to-Site VPN:** Connects entire branch locations together over an encrypted tunnel using an **IPsec framework** (includes AH, ESP, IKE phases).

### Domain 5: Essential Commands
* `show access-lists` - View configured Access Control List statements and match counters.
* `show port-security interface GigabitEthernet0/5` - Verify switchport authorization settings, sticky MAC counts, and violation modes.
* `show ip dhcp snooping binding` - View active, learned logical hardware mappings inside the validation tracking table.
* `access-list 10 permit 192.168.50.0 0.0.0.255` - Configure a standard access list entry filtering source blocks.
* `access-list 101 permit tcp 192.168.10.0 0.0.0.255 any eq 443` - Configure an extended access list matching source, destination, protocol, and port criteria.
* `ip access-group 101 in` - Interface-level command to apply an ACL rule to incoming traffic streams.
* `switchport port-security` -> `switchport port-security mac-address sticky` - Initiate port security and write dynamic entries to running config.
* `switchport port-security violation shutdown` - Force a port into an `err-disabled` state upon security violation.
* `ip dhcp snooping` -> `ip dhcp snooping vlan 10` - Globally initiate DHCP validation tracking frameworks over targeted networks.
* `ip dhcp snooping trust` - Interface-level command to mark an uplink port as trusted to receive DHCP Server replies.
* `ip arp inspection vlan 10` - Globally initiate Dynamic ARP Inspection logic over targeted networks.

---

## Domain 6: Automation & Programmability (10% of Exam)

### Controller-Based Architectures
* **Traditional Networking:** Distributed control plane; every device must be configured box-by-box via a CLI.
* **Software-Defined Networking (SDN):** Centralizes the control plane onto a software **Controller**, leaving physical switches to act as simple data-forwarding engines.
    * **Data Plane:** Handles local frame/packet forwarding (ASICs, MAC lookup tables, routing tables).
    * **Control Plane:** Calculates the paths and programming tables (OSPF calculations, Spanning Tree logic).
    * **Management Plane:** Access via SSH, HTTPS, or web console.
* **APIs (Application Programming Interfaces):**
    * **Northbound APIs:** Link the SDN controller up to applications, management consoles, and automation scripts (typically RESTful APIs utilizing HTTP).
    * **Southbound APIs:** Link the controller down to program the actual physical switches and routers (e.g., OpenFlow, NETCONF, RESTCONF).

### AI & Machine Learning in Network Operations
* **Predictive AI:** Analyzes historical network telemetry data to establish baselines and forecast potential equipment or link failures before they occur.
* **Generative AI:** Assists administrators by drafting configuration scripts, parsing dense syslog archives, and providing natural-language troubleshooting suggestions.

### REST-Based APIs & Data Serialization
* **REST Characteristics:** Stateless operations; relies on client-server architecture; uses uniform resource identifiers (URIs).
* **HTTP Verbs:**
    * `GET`: Read data.
    * `POST`: Create data.
    * `PUT` / `PATCH`: Update or modify configuration data.
    * `DELETE`: Remove data.
* **Data Encoding Formats:**
    * **JSON:** Light data exchange format; enclosed in curly braces `{}`; uses key-value pairs separated by colons.
    * **YAML:** Human-readable format focused on minimal presentation; uses strict indentation and dashes for lists.
    * **XML:** Uses nested, user-defined tags (`<tag>data</tag>`) to structure information.

### Configuration Management Tools
* **Ansible:** Open-source automation tool.
    * *Architecture:* **Agentless** (Pushes configurations over standard SSH directly to nodes without requiring software on the switch).
    * *Files:* Uses **Playbooks** written in YAML format.
* **Puppet & Chef:** **Agent-based** systems (require client software installed on targets to pull configurations periodically from a master server).
