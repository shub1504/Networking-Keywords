# Networking-Keywords
**1. Bridge:**  A Linux bridge behaves like a network switch. It forwards packets between interfaces that are connected to it. It's usually used for forwarding packets on routers, on gateways, or between VMs and network namespaces on a host.

**CREATING A BRIDGE:**

    ip link add br0 type bridge
    ip link set eth0 master br0
    ip link set tap1 master br0
    ip link set tap2 master br0
    ip link set veth1 master br0

**2. VLAN:**  A VLAN, aka virtual LAN, separates broadcast domains by adding tags to network packets. VLANs allow network administrators to group hosts under the same switch or between different switches.

**CREATING A VLAN:**
            
            ip link add link eth0 name eth0.2 type vlan id 2
            ip link add link eth0 name eth0.3 type vlan id 3

**3. VXLAN**  VXLAN (Virtual eXtensible Local Area Network) is a tunneling protocol designed to solve the problem of limited VLAN IDs (4,096) in IEEE 802.1q.

**Here's how to use VXLAN:**

       ip link add vx0 type vxlan id 100 local 1.1.1.1 remote 2.2.2.2 dev eth0 dstport 4789

**4. MACVLAN**  With VLAN, you can create multiple interfaces on top of a single one and filter packages based on a VLAN tag. With MACVLAN, you can create multiple interfaces with different Layer 2 (that is, Ethernet MAC) addresses on top of a single one.

**Here's how to set up a MACVLAN:**

      ip link add macvlan1 link eth0 type macvlan mode bridge
      ip link add macvlan2 link eth0 type macvlan mode bridge
      ip netns add net1
      ip netns add net2
      ip link set macvlan1 netns net1
      ip link set macvlan2 netns net2

**5. IPUVLAN**  IPVLAN is similar to MACVLAN with the difference being that the endpoints have the same MAC address.
* IPVLAN supports L2 and L3 mode. 
* IPVLAN L2 mode acts like a MACVLAN in bridge mode. The parent interface looks like a bridge or switch.
* In IPVLAN L3 mode, the parent interface acts like a router and packets are routed between endpoints, which gives better scalability.

**Here's how to set up an IPVLAN instance:**
         
          ip netns add ns0
          ip link add name ipvl0 link eth0 type ipvlan mode l2
          ip link set dev ipvl0 netns ns0
  

**6. VETH:**  The VETH (virtual Ethernet) device is a local Ethernet tunnel. Packets transmitted on one device in the pair are immediately received on the other device. When either device is down, the link state of the pair is down.

**Here's how to set up a VETH configuration:**
       
       ip netns add net1
       ip netns add net2
       ip link add veth1 netns net1 type veth peer name veth2 netns net2

**7. VCAN**  Similar to the network loopback devices, the VCAN (virtual CAN) driver offers a virtual local CAN (Controller Area Network) interface, so users can send/receive CAN messages via a VCAN interface. CAN is mostly used in the automotive field nowadays.

**Here's how to create a VCAN:**

         ip link add dev vcan1 type vcan


**8. VXCAN**  Similar to the VETH driver, a VXCAN (Virtual CAN tunnel) implements a local CAN traffic tunnel between two VCAN network devices. When you create a VXCAN instance, two VXCAN devices are created as a pair. When one end receives the packet, the packet appears on the device's pair and vice versa. VXCAN can be used for cross-namespace communication.

* Use a VXCAN configuration when you want to send CAN message across namespaces.

**Here's how to set up a VXCAN instance:**
         
          ip netns add net1
          ip netns add net2
          ip link add vxcan1 netns net1 type vxcan peer name vxcan2 netns net2
