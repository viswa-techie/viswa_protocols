# AUTOMOTIVE ETHERNET — LABS & DEBUGGING GUIDE
# ════════════════════════════════════════════════════════════════════
# Protocol: Automotive Ethernet | Document: 07 of 08
# 8 Progressive Labs + 7 Debug Scenarios
# ════════════════════════════════════════════════════════════════════

---

# LAB 1: PHY LINK ESTABLISHMENT & BASIC CONNECTIVITY

## Objective
Establish 100BASE-T1 link between two Linux boards, verify PHY status.

## Hardware Required
- 2× SBC with 100BASE-T1 PHY (e.g., NXP i.MX8 + TJA1100 EVK, or RaspberryPi + 100BASE-T1 HAT)
- 1× Automotive Ethernet cable (single pair, 100Ω, <15m)
- Oscilloscope (optional, for signal verification)

## Steps

### Step 1: Verify PHY Detection
```bash
# On both boards — check kernel detected PHY
dmesg | grep -i "phy\|ethernet\|tja\|marvell"
# Expected: "NXP TJA1100 at MDIO address 0" or similar

# Check interface exists
ip link show
# Expected: eth0 (or similar) listed

# Check PHY ID
cat /sys/bus/mdio_bus/devices/*/phy_id
# Expected: 0x0180dc40 (TJA1100) or vendor-specific
```

### Step 2: Configure Master/Slave
```bash
# Board A = MASTER
ethtool --set-priv-flags eth0 master-slave-cfg master
# OR via sysfs/Device Tree:
# phy-mode = "rgmii"; nxp,master;

# Board B = SLAVE
ethtool --set-priv-flags eth0 master-slave-cfg slave
```

### Step 3: Bring Up Link
```bash
# Both boards
ip link set eth0 up

# Verify link
ethtool eth0 | grep -i "link\|speed"
# Expected: Speed: 100Mb/s, Link detected: yes
```

### Step 4: Assign IP and Ping
```bash
# Board A
ip addr add 192.168.1.10/24 dev eth0

# Board B
ip addr add 192.168.1.20/24 dev eth0

# Test connectivity
ping -c 5 192.168.1.20    # From Board A
# Expected: 5 packets transmitted, 5 received, 0% loss
```

### Step 5: Check Error Counters
```bash
ethtool -S eth0 | grep -i "error\|drop\|crc\|miss"
# Expected: All zeros (clean link)
```

## Success Criteria
- Link up at 100 Mbps
- Ping works with 0% loss
- Zero error counters

## Common Issues
| Problem | Solution |
|---------|----------|
| No link | Check master/slave config (must be different) |
| Link flaps | Cable quality, check connector seating |
| PHY not detected | MDIO bus issue, check clocks and reset pin |

---

# LAB 2: BANDWIDTH TESTING WITH IPERF3

## Objective
Measure throughput, verify line-rate performance, identify bottlenecks.

## Steps

### Step 1: Install iperf3
```bash
# Both boards
apt install iperf3   # or: opkg install iperf3 (Yocto)
```

### Step 2: TCP Throughput Test
```bash
# Board B (server)
iperf3 -s

# Board A (client)
iperf3 -c 192.168.1.20 -t 30 -i 1
# Expected output:
# [  5]   0.00-30.00  sec   337 MBytes  94.2 Mbits/sec  (100BASE-T1)
# Theoretical max: ~94.1 Mbps TCP (with overhead)
```

### Step 3: UDP Throughput Test
```bash
# Board A (client, UDP)
iperf3 -c 192.168.1.20 -u -b 95M -t 10
# Check: packet loss should be <0.01%
# Expected: ~94 Mbps with 0% loss

# Overload test (try 100M on 100M link)
iperf3 -c 192.168.1.20 -u -b 100M -t 10
# Expected: Some packet loss (exceeds line rate)
```

### Step 4: Bi-directional Test
```bash
iperf3 -c 192.168.1.20 -d -t 10
# Tests both directions simultaneously (full-duplex verification)
# Expected: ~94 Mbps in EACH direction (full-duplex = 2×)
```

### Step 5: Monitor During Test
```bash
# While iperf runs, on another terminal:
watch -n 1 "ethtool -S eth0 | grep -E 'rx_bytes|tx_bytes|drop'"
```

## Key Metrics
| Metric | 100BASE-T1 Expected | 1000BASE-T1 Expected |
|--------|--------------------|--------------------|
| TCP throughput | 94 Mbps | 940 Mbps |
| UDP throughput | 95+ Mbps | 950+ Mbps |
| Latency (ping) | <1 ms | <0.5 ms |
| Jitter | <0.1 ms | <0.05 ms |

---

# LAB 3: VLAN CONFIGURATION & ISOLATION

## Objective
Create VLANs, verify traffic isolation between domains.

## Hardware
- 2 boards + 1 Ethernet switch (managed, VLAN-capable)
- OR: 3 boards connected via switch

## Steps

### Step 1: Create VLAN Interfaces
```bash
# Board A (ADAS domain)
ip link add link eth0 name eth0.10 type vlan id 10
ip addr add 10.0.10.1/24 dev eth0.10
ip link set eth0.10 up

# Board B (ADAS domain)
ip link add link eth0 name eth0.10 type vlan id 10
ip addr add 10.0.10.2/24 dev eth0.10
ip link set eth0.10 up

# Board C (IVI domain — different VLAN)
ip link add link eth0 name eth0.20 type vlan id 20
ip addr add 10.0.20.1/24 dev eth0.20
ip link set eth0.20 up
```

### Step 2: Configure Switch Ports (if hardware switch)
```bash
# Using switch management (example: ip link bridge)
bridge vlan add dev swp0 vid 10 pvid untagged  # Port to Board A
bridge vlan add dev swp1 vid 10 pvid untagged  # Port to Board B
bridge vlan add dev swp2 vid 20 pvid untagged  # Port to Board C
# Trunk port (carries all VLANs):
bridge vlan add dev swp3 vid 10
bridge vlan add dev swp3 vid 20
```

### Step 3: Test Isolation
```bash
# Board A → Board B (same VLAN 10): should work
ping -I eth0.10 10.0.10.2
# Expected: SUCCESS

# Board A → Board C (different VLAN 20): should fail
ping -I eth0.10 10.0.20.1
# Expected: FAIL (no route / destination unreachable)
```

### Step 4: Verify with tcpdump
```bash
# On Board C, listen for any frames from Board A
tcpdump -i eth0 -n src 10.0.10.1
# Expected: NO packets (VLAN isolation working)
```

### Step 5: Set QoS Priority
```bash
# Set PCP=5 for ADAS VLAN traffic
ip link set eth0.10 type vlan egress-qos-map 0:5 1:5 2:5 3:5 4:5 5:5 6:6 7:7
# Verify
cat /proc/net/vlan/eth0.10
```

## Verification
- Same-VLAN communication: ✓
- Cross-VLAN isolation: ✓ (no traffic leaks)
- PCP correctly tagged (verify with tcpdump -e or Wireshark)

---

# LAB 4: PACKET CAPTURE & PROTOCOL ANALYSIS

## Objective
Capture automotive Ethernet traffic, analyze SOME/IP and DoIP frames.

## Steps

### Step 1: Basic Capture
```bash
# Capture all traffic to file
tcpdump -i eth0 -w /tmp/automotive_capture.pcap -c 1000

# Capture VLAN-tagged only
tcpdump -i eth0 -w /tmp/vlan_traffic.pcap vlan

# Capture specific EtherType (PTP)
tcpdump -i eth0 -w /tmp/ptp.pcap ether proto 0x88f7
```

### Step 2: Live Analysis
```bash
# Show frame details
tcpdump -i eth0 -n -e -v
# -e: show Ethernet header (MAC, VLAN tag)
# -v: verbose (show IP details)

# Filter DoIP traffic
tcpdump -i eth0 -n port 13400

# Filter SOME/IP-SD multicast
tcpdump -i eth0 -n dst 224.244.224.245

# Show hex dump of frames
tcpdump -i eth0 -XX -c 5
```

### Step 3: Generate Test Traffic (SOME/IP-like)
```python
#!/usr/bin/env python3
"""Minimal SOME/IP OFFER message generator for testing"""
import socket
import struct

SOMEIP_SD_MCAST = "224.244.224.245"
SOMEIP_SD_PORT = 30490

# SOME/IP-SD header (simplified)
service_id = 0xFFFF  # SD
method_id = 0x8100   # SD
client_id = 0x0000
session_id = 0x0001
proto_version = 0x01
iface_version = 0x01
msg_type = 0x02      # Notification
return_code = 0x00
length = 28          # Payload length

header = struct.pack(">HHIHH BBBB",
    service_id, method_id, length + 8,
    client_id, session_id,
    proto_version, iface_version, msg_type, return_code)

# Minimal SD payload (OFFER entry)
flags = 0xC0  # Reboot + Unicast
sd_payload = struct.pack(">BBHI", flags, 0, 0, 0)  # Simplified

sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
sock.setsockopt(socket.IPPROTO_IP, socket.IP_MULTICAST_TTL, 2)
sock.sendto(header + sd_payload, (SOMEIP_SD_MCAST, SOMEIP_SD_PORT))
print("Sent SOME/IP-SD test message")
sock.close()
```

### Step 4: Analyze in Wireshark
```bash
# Transfer pcap to analysis machine
scp /tmp/automotive_capture.pcap user@analysis-pc:~/

# Wireshark filters:
# someip                    — All SOME/IP
# doip                      — All DoIP
# someip.serviceid == 0x1234 — Specific service
# vlan.id == 10             — ADAS VLAN
```

### Step 5: Continuous Monitoring
```bash
# Statistics every 5 seconds
while true; do
    echo "=== $(date) ==="
    ethtool -S eth0 | grep -E "rx_packets|tx_packets|rx_errors|tx_errors"
    sleep 5
done
```

---

# LAB 5: SOME/IP SERVICE COMMUNICATION (vsomeip)

## Objective
Implement SOME/IP service provider and consumer using vsomeip.

## Prerequisites
```bash
# Install vsomeip
apt install libvsomeip3 libvsomeip3-dev  # Ubuntu/Debian
# OR build from source:
git clone https://github.com/COVESA/vsomeip.git
cd vsomeip && mkdir build && cd build
cmake .. -DENABLE_SIGNAL_HANDLING=1
make -j$(nproc) && sudo make install
```

## Steps

### Step 1: Service Provider (server.cpp)
```cpp
#include <vsomeip/vsomeip.hpp>
#include <iostream>

#define SERVICE_ID 0x1234
#define INSTANCE_ID 0x5678
#define METHOD_ID 0x0001

std::shared_ptr<vsomeip::application> app;

void on_request(const std::shared_ptr<vsomeip::message> &request) {
    std::cout << "Received request from client "
              << std::hex << request->get_client() << std::endl;

    // Create response
    auto response = vsomeip::runtime::get()->create_response(request);
    std::string payload_str = "Hello from service!";
    auto payload = vsomeip::runtime::get()->create_payload(
        reinterpret_cast<const vsomeip::byte_t*>(payload_str.c_str()),
        payload_str.size());
    response->set_payload(payload);
    app->send(response);
}

int main() {
    app = vsomeip::runtime::get()->create_application("service-app");
    app->init();
    app->offer_service(SERVICE_ID, INSTANCE_ID);
    app->register_message_handler(SERVICE_ID, INSTANCE_ID, METHOD_ID, on_request);
    app->start();
    return 0;
}
```

### Step 2: Service Consumer (client.cpp)
```cpp
#include <vsomeip/vsomeip.hpp>
#include <iostream>
#include <thread>

#define SERVICE_ID 0x1234
#define INSTANCE_ID 0x5678
#define METHOD_ID 0x0001

std::shared_ptr<vsomeip::application> app;
bool service_available = false;

void on_availability(vsomeip::service_t service, vsomeip::instance_t instance, bool available) {
    std::cout << "Service " << std::hex << service << " is "
              << (available ? "AVAILABLE" : "NOT available") << std::endl;
    service_available = available;
}

void on_response(const std::shared_ptr<vsomeip::message> &response) {
    auto payload = response->get_payload();
    std::string data(reinterpret_cast<const char*>(payload->get_data()), payload->get_length());
    std::cout << "Response: " << data << std::endl;
}

void send_request() {
    while (!service_available) std::this_thread::sleep_for(std::chrono::milliseconds(100));
    auto request = vsomeip::runtime::get()->create_request();
    request->set_service(SERVICE_ID);
    request->set_instance(INSTANCE_ID);
    request->set_method(METHOD_ID);
    app->send(request);
}

int main() {
    app = vsomeip::runtime::get()->create_application("client-app");
    app->init();
    app->request_service(SERVICE_ID, INSTANCE_ID);
    app->register_availability_handler(SERVICE_ID, INSTANCE_ID, on_availability);
    app->register_message_handler(SERVICE_ID, INSTANCE_ID, METHOD_ID, on_response);
    std::thread sender(send_request);
    app->start();
    sender.join();
    return 0;
}
```

### Step 3: vsomeip Configuration (vsomeip-local.json)
```json
{
    "unicast": "192.168.1.10",
    "logging": { "level": "debug", "console": "true" },
    "applications": [
        { "name": "service-app", "id": "0x1001" },
        { "name": "client-app", "id": "0x1002" }
    ],
    "services": [
        {
            "service": "0x1234",
            "instance": "0x5678",
            "unreliable": "30509"
        }
    ],
    "routing": "service-app",
    "service-discovery": {
        "enable": "true",
        "multicast": "224.244.224.245",
        "port": "30490",
        "protocol": "udp"
    }
}
```

### Step 4: Build & Run
```bash
# Build
g++ -o server server.cpp -lvsomeip3 -lpthread
g++ -o client client.cpp -lvsomeip3 -lpthread

# Run (set config)
export VSOMEIP_CONFIGURATION=vsomeip-local.json

# Terminal 1: Start server
./server

# Terminal 2: Start client
./client
# Expected: "Service 0x1234 is AVAILABLE" then "Response: Hello from service!"
```

### Step 5: Verify with tcpdump
```bash
tcpdump -i eth0 -n port 30490 or port 30509
# Should see: UDP multicast (SD) + UDP unicast (service data)
```

---

# LAB 6: DoIP DIAGNOSTIC SESSION

## Objective
Simulate DoIP communication — vehicle identification and diagnostic request.

## Steps

### Step 1: DoIP Gateway Simulator (Python)
```python
#!/usr/bin/env python3
"""Minimal DoIP gateway simulator"""
import socket
import struct
import threading

DOIP_PORT = 13400
VIN = b"WBA12345678901234"

def build_doip_header(payload_type, payload):
    """Build DoIP generic header"""
    version = 0x02
    inv_version = 0xFD
    return struct.pack(">BBHI", version, inv_version, payload_type, len(payload)) + payload

def handle_tcp_client(conn, addr):
    print(f"TCP connection from {addr}")
    while True:
        data = conn.recv(4096)
        if not data:
            break
        # Parse DoIP header
        ver, inv, ptype, plen = struct.unpack(">BBHI", data[:8])
        payload = data[8:8+plen]
        print(f"Received: type=0x{ptype:04X}, len={plen}")

        if ptype == 0x0005:  # Routing Activation Request
            # Send Routing Activation Response (success)
            src_addr = struct.unpack(">H", payload[:2])[0]
            resp_payload = struct.pack(">HHI", src_addr, 0x0010, 0x10)  # tester, entity, ISO
            resp = build_doip_header(0x0006, resp_payload)
            conn.send(resp)
            print("Sent Routing Activation Response (OK)")

        elif ptype == 0x8001:  # Diagnostic Message
            src = struct.unpack(">H", payload[:2])[0]
            dst = struct.unpack(">H", payload[2:4])[0]
            uds_data = payload[4:]
            print(f"Diagnostic: src=0x{src:04X} dst=0x{dst:04X} UDS={uds_data.hex()}")
            # Echo back positive response (UDS service + 0x40)
            uds_resp = bytes([uds_data[0] + 0x40]) + b"\x00" * 4
            resp_payload = struct.pack(">HH", dst, src) + uds_resp
            resp = build_doip_header(0x8001, resp_payload)
            conn.send(resp)
            print(f"Sent UDS response: {uds_resp.hex()}")
    conn.close()

def udp_server():
    """Handle vehicle identification (UDP)"""
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.setsockopt(socket.SOL_SOCKET, socket.SO_BROADCAST, 1)
    sock.bind(("0.0.0.0", DOIP_PORT))
    print(f"DoIP UDP listening on port {DOIP_PORT}")
    while True:
        data, addr = sock.recvfrom(4096)
        ver, inv, ptype, plen = struct.unpack(">BBHI", data[:8])
        if ptype == 0x0001:  # Vehicle Identification Request
            print(f"Vehicle ID request from {addr}")
            # Vehicle Identification Response
            resp_payload = VIN + struct.pack(">HH", 0x0010, 0x0010)
            resp_payload += b"\x00" * 6  # EID
            resp_payload += b"\x00" * 6  # GID
            resp_payload += b"\x00"      # Further action
            resp = build_doip_header(0x0004, resp_payload)
            sock.sendto(resp, addr)

def tcp_server():
    """Handle diagnostic connections (TCP)"""
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    sock.bind(("0.0.0.0", DOIP_PORT))
    sock.listen(5)
    print(f"DoIP TCP listening on port {DOIP_PORT}")
    while True:
        conn, addr = sock.accept()
        threading.Thread(target=handle_tcp_client, args=(conn, addr)).start()

if __name__ == "__main__":
    threading.Thread(target=udp_server, daemon=True).start()
    tcp_server()
```

### Step 2: DoIP Tester Client (Python)
```python
#!/usr/bin/env python3
"""DoIP diagnostic tester"""
import socket
import struct

GATEWAY_IP = "192.168.1.20"
DOIP_PORT = 13400
TESTER_ADDR = 0x0E80
TARGET_ADDR = 0x0010

def build_doip_header(payload_type, payload):
    return struct.pack(">BBHI", 0x02, 0xFD, payload_type, len(payload)) + payload

# Step 1: UDP Vehicle Identification
print("=== Vehicle Identification ===")
udp = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
udp.settimeout(2)
req = build_doip_header(0x0001, b"")
udp.sendto(req, (GATEWAY_IP, DOIP_PORT))
try:
    data, addr = udp.recvfrom(4096)
    vin = data[8:25].decode('ascii', errors='replace')
    print(f"VIN: {vin}")
except socket.timeout:
    print("No response (timeout)")
udp.close()

# Step 2: TCP Connection + Routing Activation
print("\n=== Routing Activation ===")
tcp = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
tcp.connect((GATEWAY_IP, DOIP_PORT))
activation = struct.pack(">HBI", TESTER_ADDR, 0x00, 0x00000000)
tcp.send(build_doip_header(0x0005, activation))
resp = tcp.recv(4096)
print(f"Activation response: {resp[8:].hex()}")

# Step 3: Diagnostic Request (UDS Read DTC - Service 0x19)
print("\n=== Diagnostic Request (Read DTC) ===")
uds_request = bytes([0x19, 0x02, 0xFF])  # ReadDTCInformation
diag_payload = struct.pack(">HH", TESTER_ADDR, TARGET_ADDR) + uds_request
tcp.send(build_doip_header(0x8001, diag_payload))
resp = tcp.recv(4096)
print(f"Diagnostic response: {resp[8:].hex()}")

tcp.close()
print("\nDoIP session complete!")
```

### Step 3: Run
```bash
# Terminal 1: Gateway simulator
python3 doip_gateway.py

# Terminal 2: Tester
python3 doip_tester.py
# Expected: VIN displayed, routing activation OK, diagnostic response received
```

---

# LAB 7: TSN TIME SYNCHRONIZATION (gPTP)

## Objective
Set up gPTP (802.1AS) time synchronization between two nodes.

## Prerequisites
```bash
apt install linuxptp    # Provides ptp4l, phc2sys, pmc
```

## Steps

### Step 1: Check Hardware Timestamp Support
```bash
ethtool -T eth0
# Look for:
#   hardware-transmit     (SOF_TIMESTAMPING_TX_HARDWARE)
#   hardware-receive      (SOF_TIMESTAMPING_RX_HARDWARE)
#   hardware-raw-clock    (SOF_TIMESTAMPING_RAW_HARDWARE)
# If NO hardware timestamps: can still use software (less accurate)
```

### Step 2: Create gPTP Configuration
```bash
cat > /etc/automotive_gPTP.conf << 'EOF'
[global]
# 802.1AS profile
gmCapable               1
priority1               248
priority2               248
domainNumber            0
transportSpecific       0x1
network_transport       L2
time_stamping           hardware
tx_timestamp_timeout    10
masterOnly              0
slaveOnly               0

[eth0]
logAnnounceInterval     0
logSyncInterval         -3
logMinPdelayReqInterval 0
announceReceiptTimeout  3
neighborPropDelayThresh 800
fault_reset_interval    4
EOF
```

### Step 3: Run ptp4l (Grand Master — Board A)
```bash
# Board A: Force master
ptp4l -i eth0 -f /etc/automotive_gPTP.conf -m --masterOnly 1
# Output shows: selected local clock as best master
# port 1: MASTER
```

### Step 4: Run ptp4l (Slave — Board B)
```bash
# Board B: Slave
ptp4l -i eth0 -f /etc/automotive_gPTP.conf -m --slaveOnly 1
# Expected output:
# port 1: SLAVE
# master offset    12 s2 freq   -423 path delay    876
#                  ^^              offset in ns (should be <1000)
```

### Step 5: Sync System Clock
```bash
# On slave node: sync system clock to PTP hardware clock
phc2sys -s eth0 -c CLOCK_REALTIME -O 0 -m
# Expected: offset decreases to <100ns
```

### Step 6: Verify Synchronization
```bash
# Check PTP clock status
pmc -u -b 0 'GET CURRENT_DATA_SET'
# Shows: offsetFromMaster, meanPathDelay

# Compare times between nodes (should be <1µs difference)
# Board A:
date +%s.%N

# Board B:
date +%s.%N
# Difference should be <1 microsecond after sync
```

## Success Criteria
- ptp4l reports offset < 1000ns (1µs)
- Stable offset (not drifting)
- Path delay reasonable (typically 100-1000ns for short cable)

---

# LAB 8: 10BASE-T1S MULTIDROP BUS (Simulation)

## Objective
Simulate 10BASE-T1S multidrop Ethernet bus behavior (PLCA).

## Note
Real 10BASE-T1S hardware (TI DP83TD510E, NXP TJA1103) needed for true bus operation. This lab simulates the concept using Linux bridges and traffic control.

## Steps

### Step 1: Simulate Bus with Linux Bridge
```bash
# Create bridge (simulates shared bus)
ip link add t1s_bus type bridge
ip link set t1s_bus up

# Create virtual nodes (veth pairs)
for i in 1 2 3 4; do
    ip link add node${i}_ext type veth peer name node${i}_int
    ip link set node${i}_ext master t1s_bus
    ip link set node${i}_ext up
    
    # Each node in its own namespace
    ip netns add node${i}
    ip link set node${i}_int netns node${i}
    ip netns exec node${i} ip addr add 192.168.100.${i}/24 dev node${i}_int
    ip netns exec node${i} ip link set node${i}_int up
    ip netns exec node${i} ip link set lo up
done

ip link set t1s_bus up
```

### Step 2: Simulate PLCA (Rate Limiting = Deterministic Access)
```bash
# Each node limited to 10Mbps / 4 nodes ≈ 2.5Mbps guaranteed slot
for i in 1 2 3 4; do
    tc qdisc add dev node${i}_ext root tbf rate 2500kbit burst 64kb latency 10ms
done
```

### Step 3: Test Multi-Node Communication
```bash
# Node 1 → Node 2 (unicast)
ip netns exec node1 ping -c 3 192.168.100.2
# Expected: Success

# Node 1 → All (broadcast)
ip netns exec node1 ping -b -c 3 192.168.100.255
# Expected: All nodes receive

# Simultaneous traffic (all nodes sending)
for i in 1 2 3 4; do
    ip netns exec node${i} iperf3 -c 192.168.100.1 -u -b 2M -t 5 &
done
wait
# Each should get ~2.5Mbps (fair sharing)
```

### Step 4: Verify CAN-Like Behavior
```bash
# 10BASE-T1S key properties (simulated):
# ✓ Multiple nodes on same "bus" (bridge)
# ✓ All nodes can hear all traffic (bridge floods)
# ✓ Deterministic access (rate limiting simulates PLCA)
# ✓ No switch required (bridge = wire-level connectivity)

# Check all nodes can reach each other
for src in 1 2 3 4; do
    for dst in 1 2 3 4; do
        if [ $src -ne $dst ]; then
            ip netns exec node${src} ping -c 1 -W 1 192.168.100.${dst} > /dev/null 2>&1
            echo "Node${src} → Node${dst}: $?"
        fi
    done
done
```

### Step 5: Cleanup
```bash
for i in 1 2 3 4; do
    ip netns del node${i}
    ip link del node${i}_ext 2>/dev/null
done
ip link del t1s_bus
```

---

# DEBUG SCENARIO 1: NO LINK ESTABLISHED

## Symptoms
- `ethtool eth0` shows "Link detected: no"
- No traffic visible on wire (scope shows nothing or garbage)

## Diagnostic Steps
```bash
# 1. Is PHY powered and detected?
dmesg | grep -i "phy\|ethernet"
ls /sys/bus/mdio_bus/devices/

# 2. Can we read PHY registers?
ethtool -d eth0
# If this fails → MDIO bus problem

# 3. Check PHY register 0 (control)
#    Bit 15 = reset (should be 0 after init)
#    Bit 11 = power down (should be 0)

# 4. Check PHY register 1 (status)
#    Bit 2 = link status (0 = no link)

# 5. Master/slave configuration?
# Read register 9 (master/slave control)
# Bit 14: 1=force manual, Bit 13: 1=master, 0=slave
# BOTH ENDS SAME = NO LINK!

# 6. Physical checks:
#    - Cable connected? (obvious but common)
#    - Correct pair? (pin 1-2 for data)
#    - Cable length < 15m?
#    - Termination present at both ends?
```

## Resolution Table
| Root Cause | Fix |
|-----------|-----|
| Both PHYs same role | Set one master, one slave |
| PHY in power-down | Clear power-down bit in register 0 |
| MDIO bus not working | Check MDC clock, MDIO pull-up, PHY address |
| PHY in reset | Release reset pin, check Device Tree |
| No reference clock | Provide 25 MHz to PHY |
| Cable open/short | Replace cable, check connectors |

---

# DEBUG SCENARIO 2: LINK UP BUT NO PING

## Symptoms
- `ethtool eth0` shows "Link detected: yes, 100Mb/s"
- `ping 192.168.1.20` → "Destination Host Unreachable" or timeout

## Diagnostic Steps
```bash
# 1. IP configuration correct?
ip addr show eth0
# Verify: correct IP, correct subnet mask, interface UP

# 2. Are both on same subnet?
# Board A: 192.168.1.10/24
# Board B: 192.168.1.20/24
# ✓ Same /24 subnet

# 3. ARP working? (Layer 2 connectivity)
arping -I eth0 192.168.1.20
# If arping works but ping doesn't → firewall issue
# If arping fails → Layer 2 problem

# 4. Check ARP table
ip neigh show
# Should show 192.168.1.20 → MAC address of Board B

# 5. VLAN mismatch?
# If one side has VLAN tag and other doesn't → won't communicate
cat /proc/net/vlan/config

# 6. Firewall blocking?
iptables -L -n
nft list ruleset
# Flush if testing: iptables -F

# 7. Capture at raw level
tcpdump -i eth0 -e -n arp
# Send ping, check if ARP request goes out and response comes back
```

## Resolution Table
| Root Cause | Fix |
|-----------|-----|
| Wrong IP/subnet | Correct IP configuration |
| VLAN mismatch | Match VLAN IDs on both ends |
| Firewall | Allow ICMP and relevant traffic |
| Interface DOWN | `ip link set eth0 up` |
| Duplicate IP | Change one node's IP |
| MTU mismatch | Match MTU on both ends |

---

# DEBUG SCENARIO 3: INTERMITTENT PACKET LOSS

## Symptoms
- Ping works but shows 2-5% packet loss
- iperf3 shows periodic drops
- ethtool shows increasing error counters

## Diagnostic Steps
```bash
# 1. Error counters
ethtool -S eth0 | grep -i "error\|drop\|crc\|overrun\|miss"
# Key counters:
#   rx_crc_errors → cable/connector issue
#   rx_missed → driver can't keep up (CPU overload)
#   tx_dropped → queue full

# 2. Is it periodic? (EMI from motor/switching)
ping -i 0.01 192.168.1.20 | ts '%H:%M:%.S'
# Look for pattern (e.g., every 16ms = motor PWM frequency)

# 3. PHY signal quality
# Read PHY vendor-specific SQI register
ethtool --phy-statistics eth0  # If supported
# SQI < 5 → marginal link, check cable

# 4. Link re-training events
dmesg | grep -i "link\|carrier"
journalctl -u NetworkManager | grep "carrier"
# Frequent link up/down = severe issue

# 5. Temperature correlation
cat /sys/class/thermal/thermal_zone*/temp
# If errors increase at high temp → PHY marginal at temperature

# 6. Buffer overflow check
cat /proc/net/softnet_stat
# Column 2 = dropped (ring buffer full)
# If increasing → increase ring buffers:
ethtool -G eth0 rx 4096 tx 4096
```

## Resolution Table
| Root Cause | Fix |
|-----------|-----|
| CRC errors (cable) | Replace cable, check connectors, add shielding |
| EMI interference | Re-route cable away from motors, add common-mode choke |
| Buffer overflow | Increase ring buffers, optimize ISR handling |
| Temperature | Verify PHY cooling, check spec compliance |
| Connector oxidation | Clean/replace connector |
| Near-end crosstalk | Improve PCB layout, separate TX/RX traces |

---

# DEBUG SCENARIO 4: VLAN TRAFFIC NOT ISOLATED

## Symptoms
- Traffic from VLAN 10 visible on VLAN 20 port
- Security concern: domains not properly isolated

## Diagnostic Steps
```bash
# 1. Verify VLAN configuration on switch
bridge vlan show
# Each port should show only its assigned VLANs

# 2. Check port mode (access vs trunk)
# Trunk ports carry ALL VLANs (tagged) → security risk if misconfigured
bridge vlan show dev swp0
# Should show: vid 10 PVID untagged (access port for VLAN 10 only)

# 3. Is VLAN 1 (default) present on all ports?
# Many switches default all ports to VLAN 1 → traffic leak!
bridge vlan del dev swp0 vid 1  # Remove default VLAN

# 4. IGMP snooping issue?
# Multicast might flood to all ports regardless of VLAN
bridge -d link show | grep mcast
# Ensure IGMP snooping enabled:
echo 1 > /sys/class/net/br0/bridge/multicast_snooping

# 5. MAC flooding attack?
# If MAC table full → switch floods to all ports
bridge fdb show | wc -l
# If near capacity → possible attack or misconfiguration

# 6. Capture on "isolated" port
tcpdump -i eth0 -e vlan 10   # On VLAN 20 port
# Should see NOTHING — if frames appear, isolation broken
```

---

# DEBUG SCENARIO 5: gPTP TIME SYNC FAILURE

## Symptoms
- ptp4l shows "master offset" growing or oscillating wildly
- Offset >10µs (should be <1µs)
- TSN gate scheduling desynchronized

## Diagnostic Steps
```bash
# 1. Check ptp4l status
ptp4l -i eth0 -f /etc/ptp4l.conf -m
# Look for:
#   "port 1: SLAVE" (should be SLAVE if not GM)
#   "master offset XXX" (should converge to <1000ns)

# 2. Check port state
pmc -u -b 0 'GET PORT_DATA_SET'
# portState should be SLAVE or MASTER (not LISTENING or FAULTY)

# 3. Hardware timestamps working?
ethtool -T eth0 | grep hardware
# If NO hardware timestamps → SW timestamps only (ms accuracy, not µs)

# 4. Check Peer Delay measurement
pmc -u -b 0 'GET CURRENT_DATA_SET'
# meanPathDelay should be stable (100-1000ns for short cable)
# If 0 or very large → Peer Delay mechanism broken

# 5. Is switch transparent clock working?
# If going through switch, switch must support 802.1AS
# and correct residence time in correction field

# 6. Clock source quality
pmc -u -b 0 'GET PARENT_DATA_SET'
# Check grandmaster identity and quality

# 7. Competing grand masters?
# Multiple GMs = instability. Check priority settings.
pmc -u -b 0 'GET DEFAULT_DATA_SET'
```

## Resolution Table
| Root Cause | Fix |
|-----------|-----|
| No HW timestamps | Use NIC with HW timestamp support |
| Wrong transport | Set `network_transport L2` for automotive |
| Switch not 802.1AS aware | Replace with TSN-capable switch |
| Multiple GMs | Set consistent priority1/priority2 |
| Kernel clock adjustment | Ensure NTP not competing with PTP |
| PHY delay asymmetry | Calibrate per-port delay offsets |

---

# DEBUG SCENARIO 6: SOME/IP SERVICE NOT DISCOVERED

## Symptoms
- Client prints "Service NOT available"
- No OFFER messages seen on network
- Service provider running but consumers can't find it

## Diagnostic Steps
```bash
# 1. Is SD multicast reaching client?
tcpdump -i eth0 -n dst 224.244.224.245 port 30490
# Should see OFFER messages from provider
# If nothing → multicast not reaching client

# 2. Multicast group joined?
ip maddr show eth0
# Should include: 224.244.224.245 (SOME/IP-SD group)
# If not: application hasn't joined group

# 3. IGMP snooping blocking?
# Switch may not forward multicast if IGMP join not seen
# Check switch IGMP table or disable snooping for test:
echo 0 > /sys/class/net/br0/bridge/multicast_snooping

# 4. vsomeip configuration check
cat $VSOMEIP_CONFIGURATION
# Verify: unicast IP correct, service-discovery enabled
# Verify: multicast address matches (224.244.224.245)

# 5. Firewall blocking multicast?
iptables -L -n | grep -i "drop\|reject"
# Allow: iptables -A INPUT -d 224.244.224.245 -j ACCEPT

# 6. Routing issue
ip route show
# Multicast route needed: 224.0.0.0/4 → eth0
ip route add 224.0.0.0/4 dev eth0

# 7. Same VLAN?
# Provider and consumer must be on same VLAN for multicast
```

---

# DEBUG SCENARIO 7: POOR THROUGHPUT DESPITE LINK UP

## Symptoms
- Link up at 100 Mbps but iperf3 shows only 30-50 Mbps
- High CPU utilization during transfer
- Latency spikes

## Diagnostic Steps
```bash
# 1. Check for half-duplex (should never be for automotive)
ethtool eth0 | grep Duplex
# Must be "Full" — half-duplex cuts throughput in half

# 2. Check interrupt coalescing
ethtool -c eth0
# If rx-usecs = 0 → interrupt per packet (high CPU)
# Optimize: ethtool -C eth0 rx-usecs 50 rx-frames 32

# 3. Ring buffer size
ethtool -g eth0
# If small → overflows under load
# Increase: ethtool -G eth0 rx 4096 tx 4096

# 4. CPU utilization
top -H   # Check for ksoftirqd high usage
# If CPU-bound: enable NAPI polling, GRO, checksum offload

# 5. Offload features
ethtool -k eth0
# Enable if available:
ethtool -K eth0 rx-checksumming on tx-checksumming on
ethtool -K eth0 gro on gso on tso on

# 6. MTU mismatch
ip link show eth0 | grep mtu
# Both ends must have same MTU
# Larger MTU = fewer packets = less overhead (if supported)

# 7. TCP window size
sysctl net.core.rmem_max
sysctl net.core.wmem_max
# Increase if needed:
sysctl -w net.core.rmem_max=16777216
sysctl -w net.core.wmem_max=16777216

# 8. Check for TX queue drops
tc -s qdisc show dev eth0
# "dropped" counter > 0 → queue too small or shaping too aggressive
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
