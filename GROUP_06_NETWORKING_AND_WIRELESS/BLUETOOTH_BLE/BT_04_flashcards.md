# BLUETOOTH / BLE — FLASHCARDS (Spaced Repetition)
# ════════════════════════════════════════════════════════════════════
# Protocol: Bluetooth Classic & BLE | Document: 04 of 08
# 250+ cards across 10 decks | Format: Q → A (concise)
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: BLUETOOTH FUNDAMENTALS (30 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What frequency band does Bluetooth use? | 2.4 GHz ISM band (2400–2483.5 MHz) |
| 2 | Who manages the Bluetooth specification? | Bluetooth SIG (Special Interest Group) |
| 3 | What year was Bluetooth 1.0 released? | 1999 |
| 4 | What does BR/EDR stand for? | Basic Rate / Enhanced Data Rate |
| 5 | What is the maximum data rate for EDR? | 3 Mbps (8DPSK modulation) |
| 6 | What version introduced BLE? | Bluetooth 4.0 (2010) |
| 7 | What is a dual-mode device? | Device supporting both BR/EDR and BLE simultaneously |
| 8 | What is a BD_ADDR? | Bluetooth Device Address — 48-bit unique identifier (like MAC) |
| 9 | What is the range of BD_ADDR OUI? | Upper 24 bits = company ID (LAP+UAP+NAP format) |
| 10 | What is Bluetooth Class 1 TX power? | Up to +20 dBm (~100m range) |
| 11 | What is Bluetooth Class 2 TX power? | Up to +4 dBm (~10m range) |
| 12 | What is Bluetooth Class 3 TX power? | Up to 0 dBm (~1m range) |
| 13 | What does FHSS stand for? | Frequency Hopping Spread Spectrum |
| 14 | What version introduced Secure Connections? | Bluetooth 4.1 (LE SC in 4.2) |
| 15 | What version introduced LE 2M PHY? | Bluetooth 5.0 (2016) |
| 16 | What version introduced LE Audio? | Bluetooth 5.2 (2020) |
| 17 | What version introduced Channel Sounding? | Bluetooth 6.0 (2024) |
| 18 | Name the Bluetooth Classic link types | SCO (synchronous), eSCO (extended SCO), ACL (asynchronous) |
| 19 | What is a piconet? | 1 Master + up to 7 active slaves network |
| 20 | What is a scatternet? | Multiple overlapping piconets with bridge devices |
| 21 | What is TDD in Bluetooth Classic? | Time-Division Duplex — master TX in even slots, slave TX in odd |
| 22 | What is the slot duration in BT Classic? | 625 µs |
| 23 | How many hops per second in BT Classic? | 1600 |
| 24 | What is AFH? | Adaptive Frequency Hopping — avoids channels with interference |
| 25 | Minimum channels AFH must keep? | 20 (out of 79) |
| 26 | What is the difference between ACL and SCO? | ACL: asynchronous, retransmission, variable size. SCO: synchronous, fixed interval, no retransmission |
| 27 | What is an Active Member Address (AMA)? | 3-bit address for active slave in piconet (1-7) |
| 28 | What is a Parked Member Address (PMA)? | 8-bit address for parked slave (up to 255) |
| 29 | What modulation does Basic Rate use? | GFSK (Gaussian Frequency Shift Keying) at 1 Mbps |
| 30 | What modulations does EDR use? | π/4-DQPSK (2 Mbps) and 8DPSK (3 Mbps) |

---

## DECK 2: PHYSICAL LAYER & RADIO (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | How many channels does BT Classic use? | 79 channels (1 MHz spacing) |
| 2 | How many channels does BLE use? | 40 channels (2 MHz spacing) |
| 3 | Which BLE channels are for advertising? | Ch 37 (2402 MHz), Ch 38 (2426 MHz), Ch 39 (2480 MHz) |
| 4 | Why are BLE advertising channels positioned where they are? | To minimize overlap with WiFi channels 1, 6, and 11 |
| 5 | What is the BLE 1M PHY data rate? | 1 Mbps |
| 6 | What is the BLE 2M PHY data rate? | 2 Mbps |
| 7 | What is LE Coded PHY S=2 data rate? | 500 Kbps |
| 8 | What is LE Coded PHY S=8 data rate? | 125 Kbps |
| 9 | What does S=8 mean in LE Coded PHY? | Each bit encoded with 8 symbols → more robust, longer range |
| 10 | What is the advantage of Coded PHY? | 4× range (S=8) due to FEC coding gain (~12 dB link budget improvement) |
| 11 | What does T_IFS stand for? | Inter-Frame Space — 150 µs between consecutive packets |
| 12 | What is whitening in Bluetooth? | XOR with LFSR sequence to avoid long runs of 0s/1s (DC balance) |
| 13 | What CRC does BLE use? | 24-bit CRC |
| 14 | What is the BLE preamble for 1M PHY? | 1 byte (0xAA) |
| 15 | What is the BLE preamble for 2M PHY? | 2 bytes (0xAAAA) |
| 16 | What is the Access Address for advertising? | 0x8E89BED6 (fixed) |
| 17 | What is the Access Address for data channels? | Random 32-bit value assigned per connection |
| 18 | What determines BT Classic hop sequence? | Master's BD_ADDR + clock |
| 19 | What determines BLE hop sequence? | Channel map + hop increment (from CONNECT_IND) + connection event counter |
| 20 | What is the maximum BLE advertising PDU payload (legacy)? | 31 bytes |
| 21 | What is the max BLE advertising PDU payload (extended, BT 5.0)? | 255 bytes (with chaining, up to 1650 bytes total) |
| 22 | What is the sensitivity requirement for BLE 1M PHY? | -70 dBm (BER ≤ 0.1%) |
| 23 | What is the maximum TX power for BLE? | +20 dBm (though most devices use 0 to +10 dBm) |
| 24 | What frequency does BLE Channel 0 use? | 2404 MHz |
| 25 | What is the channel bandwidth for BLE data channels? | 2 MHz |

---

## DECK 3: BLE PROTOCOL & LINK LAYER (30 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What are the BLE Link Layer states? | Standby, Advertising, Scanning, Initiating, Connection (Master/Slave), Synchronization, Isochronous Broadcasting |
| 2 | What PDU type is used for connectable undirected advertising? | ADV_IND |
| 3 | What PDU type is for non-connectable advertising? | ADV_NONCONN_IND |
| 4 | What PDU type is for scannable advertising? | ADV_SCAN_IND |
| 5 | What PDU starts a BLE connection? | CONNECT_IND (sent by initiator/central) |
| 6 | What info does CONNECT_IND contain? | Access Address, CRC init, TX window, interval, latency, timeout, channel map, hop increment |
| 7 | What is the BLE connection interval range? | 7.5 ms to 4 seconds (multiples of 1.25 ms) |
| 8 | What is Slave Latency? | Number of connection events slave can skip without responding |
| 9 | What is Supervision Timeout? | Max time between successfully received packets before link is lost (100ms-32s) |
| 10 | What is the relationship between CI, latency, and timeout? | Timeout > CI × (1 + Latency) × 2 |
| 11 | What is DLE? | Data Length Extension — allows up to 251 bytes per LL PDU (vs 27 default) |
| 12 | Default LE data payload (no DLE)? | 27 bytes |
| 13 | Maximum LE data payload (with DLE)? | 251 bytes |
| 14 | What are the LLID values in BLE data PDU header? | 01=L2CAP continuation, 10=L2CAP start/complete, 11=LL Control |
| 15 | What is NESN in BLE? | Next Expected Sequence Number — ARQ acknowledgment mechanism |
| 16 | What is SN in BLE? | Sequence Number — identifies current packet for ARQ |
| 17 | How does BLE ARQ work? | If received SN ≠ expected NESN → NAK (resend). If match → ACK (advance) |
| 18 | What is the MIC field in BLE? | Message Integrity Check — 4 bytes, present when encryption enabled |
| 19 | What LL control PDU updates connection parameters? | LL_CONNECTION_UPDATE_IND |
| 20 | What LL control PDU changes PHY? | LL_PHY_REQ / LL_PHY_RSP / LL_PHY_UPDATE_IND |
| 21 | What LL control PDU negotiates DLE? | LL_LENGTH_REQ / LL_LENGTH_RSP |
| 22 | What is a connection event? | A point in time where master and slave exchange PDUs |
| 23 | Can multiple packets be sent in one connection event? | Yes, as long as both sides have data and time permits |
| 24 | What is Extended Advertising? | BT 5.0 feature: larger payloads, secondary channels, multiple advertising sets |
| 25 | What are periodic advertisements? | Fixed-interval broadcasts without connection (receivers sync to schedule) |
| 26 | What is an Advertising Set? | Independent advertising instance (own parameters, data, random address) |
| 27 | What is the minimum advertising interval? | 20 ms (connectable), 100 ms (non-connectable) |
| 28 | What is passive scanning? | Listening for advertisements without sending scan requests |
| 29 | What is active scanning? | Listening + sending SCAN_REQ to get additional data (SCAN_RSP) |
| 30 | What is a Resolvable Private Address? | Randomized address containing hash of IRK — only bonded peers can resolve identity |

---

## DECK 4: GATT & ATT (30 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What does GATT stand for? | Generic Attribute Profile |
| 2 | What does ATT stand for? | Attribute Protocol |
| 3 | What is the relationship between GATT and ATT? | GATT defines the framework (services/characteristics). ATT is the protocol for accessing attributes. |
| 4 | What is a GATT Server? | Device that stores data and accepts requests from clients |
| 5 | What is a GATT Client? | Device that reads/writes/subscribes to data on a server |
| 6 | GATT hierarchy (top to bottom)? | Profile → Service → Characteristic → Descriptor |
| 7 | What is an Attribute Handle? | 16-bit value (0x0001-0xFFFF) uniquely identifying an attribute in the database |
| 8 | What is the ATT MTU? | Maximum Transmission Unit for ATT — determines max single operation payload |
| 9 | Default ATT MTU? | 23 bytes (yielding 20 bytes usable after 3-byte ATT header) |
| 10 | Maximum ATT MTU? | 517 bytes |
| 11 | What is a Primary Service? | Main service visible to GATT clients during service discovery |
| 12 | What is a Secondary Service? | Auxiliary service only referenced by other services (not independently discoverable) |
| 13 | What is a Characteristic Declaration? | Attribute that describes properties, handle, and UUID of a characteristic value |
| 14 | Name common characteristic properties | Read, Write, Write Without Response, Notify, Indicate, Authenticated Signed Writes |
| 15 | Difference between Write and Write Without Response? | Write: requires ATT acknowledgment. WWR: no acknowledgment (faster, fire-and-forget) |
| 16 | What is a Notification? | Server pushes characteristic value to client without requiring acknowledgment |
| 17 | What is an Indication? | Server pushes value to client WITH required acknowledgment (confirmation) |
| 18 | What is CCCD? | Client Characteristic Configuration Descriptor (0x2902) — enables/disables notify/indicate |
| 19 | CCCD value for enabling notifications? | 0x0001 |
| 20 | CCCD value for enabling indications? | 0x0002 |
| 21 | What is a 16-bit UUID? | Bluetooth SIG-assigned short UUID (e.g., 0x180D = Heart Rate Service) |
| 22 | Bluetooth Base UUID template? | 0000XXXX-0000-1000-8000-00805F9B34FB |
| 23 | UUID for Heart Rate Service? | 0x180D |
| 24 | UUID for Battery Service? | 0x180F |
| 25 | UUID for Generic Access Service? | 0x1800 |
| 26 | UUID for Device Information Service? | 0x180A |
| 27 | What is Service Discovery? | Client enumerates services/characteristics on server (Read By Group Type, etc.) |
| 28 | What is the ATT Read Request limit? | Returns up to (MTU - 1) bytes per response |
| 29 | What is ATT Read Blob? | Read long attributes (>MTU) by specifying offset |
| 30 | What is L2CAP CoC for BLE? | Credit-based connection-oriented channel — higher throughput than GATT for bulk data |

---

## DECK 5: BLUETOOTH CLASSIC PROFILES (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What is A2DP? | Advanced Audio Distribution Profile — high-quality audio streaming |
| 2 | A2DP mandatory codec? | SBC (Sub-Band Codec) |
| 3 | What is AVDTP? | Audio/Video Distribution Transport Protocol — sets up A2DP streams |
| 4 | A2DP roles? | Source (phone sending audio) and Sink (speaker/car receiving audio) |
| 5 | What is HFP? | Hands-Free Profile — phone calls through car/headset |
| 6 | HFP roles? | Audio Gateway (AG = phone) and Hands-Free (HF = car) |
| 7 | What transport does HFP use for AT commands? | RFCOMM (serial emulation over L2CAP) |
| 8 | What link type does HFP use for voice? | SCO or eSCO (synchronous) |
| 9 | What is AVRCP? | Audio/Video Remote Control Profile — play/pause/skip/browse |
| 10 | AVRCP 1.6 feature? | Browsing: navigate folders, playlists, cover art |
| 11 | What is PBAP? | Phone Book Access Profile — sync contacts to car |
| 12 | PBAP data format? | vCard (2.1 or 3.0) |
| 13 | What is MAP? | Message Access Profile — SMS/MMS notification and access |
| 14 | What is SPP? | Serial Port Profile — emulates RS-232 serial over Bluetooth |
| 15 | What is RFCOMM? | Protocol that emulates serial port (used by HFP, SPP, OBEX) |
| 16 | What is SDP? | Service Discovery Protocol — finds available services on remote device |
| 17 | What is OBEX? | Object Exchange protocol — file transfer (used by PBAP, MAP, OPP) |
| 18 | What is HID profile? | Human Interface Device — keyboard, mouse, gamepad |
| 19 | What is PAN profile? | Personal Area Network — internet sharing over BT |
| 20 | What codec gives best BT audio quality? | LDAC at 990 Kbps (Sony) or aptX Adaptive at 420 Kbps (Qualcomm) |
| 21 | aptX HD bitrate? | 576 Kbps |
| 22 | AAC over A2DP bitrate? | Up to 256 Kbps (VBR) |
| 23 | What is mSBC? | Modified SBC for HFP wideband speech (16 kHz, eSCO transparent) |
| 24 | What is CVSD? | Continuously Variable Slope Delta — narrowband voice codec (8 kHz) for HFP |
| 25 | What is the PSM for RFCOMM? | PSM 0x0003 |

---

## DECK 6: BLUETOOTH SECURITY (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What is SSP? | Secure Simple Pairing — BT 2.1+ pairing using ECDH (P-192) |
| 2 | What are the 4 SSP association models? | Numeric Comparison, Passkey Entry, Just Works, Out-of-Band (OOB) |
| 3 | What is Numeric Comparison? | Both devices display 6-digit number, user confirms they match |
| 4 | What is Just Works? | No user interaction — vulnerable to MITM (for no-display devices) |
| 5 | What is Passkey Entry? | User enters 6-digit PIN displayed on one device into the other |
| 6 | What is OOB pairing? | Key material exchanged out-of-band (NFC tap, QR code) |
| 7 | What is LE Secure Connections? | BLE 4.2+ pairing using ECDH P-256 curve — stronger than Legacy |
| 8 | What is the Link Key? | 128-bit key generated during Classic pairing, stored for bonding |
| 9 | What is the LTK? | Long-Term Key — used for BLE encryption after LE SC pairing |
| 10 | What is the IRK? | Identity Resolving Key — resolves Resolvable Private Addresses |
| 11 | What is the CSRK? | Connection Signature Resolving Key — verifies signed data |
| 12 | What encryption does BT Classic use? | E0 stream cipher (legacy) or AES-CCM (Secure Connections) |
| 13 | What encryption does BLE use? | AES-128-CCM |
| 14 | What is the KNOB attack? | Key Negotiation — forces minimum encryption key entropy (brute-forceable) |
| 15 | What is the BIAS attack? | Impersonation of paired device exploiting legacy one-way auth |
| 16 | What is BlueBorne? | RCE exploit via crafted L2CAP packets without pairing (2017) |
| 17 | What is BrakTooth? | Family of LMP vulnerabilities causing crash/RCE on BT controllers |
| 18 | How to mitigate relay attacks on BLE? | Use UWB for Time-of-Flight ranging (BLE RSSI easily relayed) |
| 19 | What is bonding? | Storing pairing keys so re-pairing is not needed on reconnection |
| 20 | What address type prevents BLE tracking? | Resolvable Private Address (RPA) — changes periodically |
| 21 | How often should RPA rotate? | Recommended every 15 minutes (configurable) |
| 22 | What is Secure Connections Only mode? | Reject devices that don't support Secure Connections |
| 23 | Minimum key size per BT spec? | 7 bytes (56 bits) — should enforce 16 bytes in practice |
| 24 | What is cross-transport key derivation? | Derive BLE LTK from Classic Link Key (or vice versa) — single pairing for both |
| 25 | What is BLURtooth? | Vulnerability in cross-transport key derivation allowing key overwrite |

---

## DECK 7: LE AUDIO & ISOCHRONOUS (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What codec does LE Audio mandate? | LC3 (Low Complexity Communication Codec) |
| 2 | LC3 frame durations? | 7.5 ms and 10 ms |
| 3 | LC3 sample rates? | 8, 16, 24, 32, 44.1, 48 kHz |
| 4 | LC3 vs SBC quality at same bitrate? | LC3 is significantly better (or equal quality at 50% bitrate) |
| 5 | What is CIS? | Connected Isochronous Stream — point-to-point isochronous BLE link |
| 6 | What is CIG? | Connected Isochronous Group — groups multiple CIS with synchronized timing |
| 7 | What is BIS? | Broadcast Isochronous Stream — one-to-many isochronous broadcast |
| 8 | What is BIG? | Broadcast Isochronous Group — groups multiple BIS |
| 9 | What is Auracast? | Bluetooth broadcast audio — share audio to unlimited receivers (BIS-based) |
| 10 | What is BAP? | Basic Audio Profile — defines audio stream setup/config for LE Audio |
| 11 | What is TMAP? | Telephony and Media Audio Profile — call + music over LE Audio |
| 12 | What is VCP? | Volume Control Profile — synchronized volume across devices |
| 13 | What is MCP? | Media Control Profile — play/pause/skip for LE Audio |
| 14 | What is CCP? | Call Control Profile — manage calls over LE Audio |
| 15 | What is HAP? | Hearing Access Profile — streaming to hearing aids |
| 16 | What is CAP? | Common Audio Profile — foundation for audio profiles |
| 17 | What is CSIP? | Coordinated Set Identification Profile — identifies paired devices (L+R earbuds) |
| 18 | CIS vs A2DP: latency? | CIS: 20-30ms total. A2DP: 100-150ms typical |
| 19 | Can CIS be bidirectional? | Yes — both Central→Peripheral and Peripheral→Central in same CIS |
| 20 | How is Auracast encrypted? | Broadcast Code (shared via QR, NFC, or user entry) encrypts BIG |
| 21 | What replaces A2DP in LE Audio? | BAP + TMAP (Unicast Audio using CIS) |
| 22 | What replaces HFP in LE Audio? | BAP + TMAP + CCP (conversational audio using bidirectional CIS) |
| 23 | LE Audio multi-stream benefit? | True stereo: separate CIS per ear → independent processing, better switching |
| 24 | What Android version added LE Audio? | Android 13 (API 33) |
| 25 | What is LC3plus? | Enhanced LC3 with higher quality (optional, Fraunhofer + Ericsson) |

---

## DECK 8: ANDROID & LINUX BT STACK (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | What is Fluoride? | Android's native Bluetooth stack (replaced Bluez in Android 4.2) |
| 2 | What is Gabeldorsche? | Rewrite/evolution of Fluoride in Rust (ongoing, Android 13+) |
| 3 | Main Java BT class in Android? | BluetoothAdapter (get state, start scan, manage connections) |
| 4 | How to get BluetoothAdapter? | BluetoothManager.getAdapter() or BluetoothAdapter.getDefaultAdapter() |
| 5 | What HAL does Android BT use? | AIDL HAL: android.hardware.bluetooth |
| 6 | Where is btsnoop log on Android? | /data/misc/bluetooth/logs/btsnoop_hci.log |
| 7 | How to enable btsnoop? | Developer Options → Enable Bluetooth HCI snoop log |
| 8 | What is btmon? | Linux BlueZ HCI monitor (real-time packet capture) |
| 9 | What is hcitool? | Linux command-line tool for HCI commands (scan, connect, etc.) |
| 10 | What is bluetoothctl? | Linux interactive BT management tool (BlueZ) |
| 11 | What is gatttool? | Linux command-line BLE GATT client |
| 12 | Android BT service process? | com.android.bluetooth (runs Fluoride stack) |
| 13 | Key system property for BT? | persist.bluetooth.enablenewavrcp, bluetooth.profile.*.enabled |
| 14 | What is BluetoothProfile in Android API? | Interface representing a BT profile (A2DP, HFP, HID, etc.) |
| 15 | How does Android app receive BT events? | BroadcastReceiver (BluetoothDevice.ACTION_FOUND, ACTION_BOND_STATE_CHANGED, etc.) |
| 16 | Linux kernel BT subsystem? | net/bluetooth/ (hci_core, l2cap_core, sco, rfcomm) |
| 17 | Kernel driver for Qualcomm BT? | btqca (drivers/bluetooth/btqca.c) — handles firmware loading |
| 18 | What is the BT firmware file for QCA? | .tlv (rampatch) and .bin (NVM) in /lib/firmware/qca/ |
| 19 | What transport does SA8295P use for BT HCI? | UART (hci_uart, typically at 3.2 Mbps) |
| 20 | What is the BluetoothSocket in Android? | API for RFCOMM/L2CAP socket communication (SPP-like) |
| 21 | How does Android scan for BLE? | BluetoothLeScanner.startScan(filters, settings, callback) |
| 22 | What permission is needed for BT in Android 12+? | BLUETOOTH_CONNECT, BLUETOOTH_SCAN, BLUETOOTH_ADVERTISE |
| 23 | What is CompanionDeviceManager? | Android API for pairing companion devices (avoids location permission for BLE scan) |
| 24 | What is nRF Connect? | Nordic Semiconductor's mobile app for BLE scanning/GATT browsing/testing |
| 25 | BlueZ D-Bus interface for GATT? | org.bluez.GattService1, org.bluez.GattCharacteristic1 |

---

## DECK 9: AUTOMOTIVE BLUETOOTH (30 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | Primary BT chip for SA8295P automotive? | QCA6698AQ (combo WiFi 6E + BT 5.2) |
| 2 | How does car manage multi-phone BT? | Priority system (driver > passenger) + connection policy manager |
| 3 | Max paired phones typical in automotive? | 8-12 (varies by OEM) |
| 4 | Max simultaneously connected phones? | Typically 2 (HFP from both, A2DP from one) |
| 5 | What happens to A2DP during phone call? | AVDTP Suspend issued → music pauses → SCO activated for call |
| 6 | Audio routing priority in automotive? | Emergency > Phone call > Navigation > Media |
| 7 | What is PBAP used for in cars? | Download phone contacts + call history for display on head unit |
| 8 | PBAP sync time for 1000 contacts? | Typically 10-30 seconds (vCard download) |
| 9 | What is MAP used for in cars? | SMS/MMS notifications displayed on head unit |
| 10 | What audio path does BT use in SA8295P? | BT chip → PCM/I2S → Audio DSP → Amplifier |
| 11 | What is the typical A2DP latency in automotive? | 100-200ms (encoding + transmission + decoding + DSP) |
| 12 | How does AEC work with BT audio? | Estimates and cancels speaker output from microphone input (removes echo) |
| 13 | What is BLE Digital Key? | Phone unlocks/starts car via BLE (CCC 3.0 standard) |
| 14 | Why does Digital Key need UWB? | BLE RSSI can be relayed; UWB Time-of-Flight ensures physical proximity |
| 15 | BT reconnection on vehicle start goal? | <3 seconds to audio playback |
| 16 | What speeds up BT reconnection? | Cached clock offset, AFH map, codec config, parallel profile setup |
| 17 | What is automotive BT coexistence challenge? | WiFi hotspot (2.4 GHz) + BT audio + BLE sensors on same radio |
| 18 | Solution for WiFi/BT coexistence in car? | Use WiFi on 5/6 GHz, PTA arbitration, AFH for BT |
| 19 | What is TPMS over BLE? | Tire Pressure Monitoring via BLE advertisements from tire sensors |
| 20 | What is LE Audio advantage for automotive? | Lower latency, multi-stream zones, Auracast for rear passengers |
| 21 | What is AAOS CarBluetoothService? | Android Automotive OS service managing car-specific BT policies |
| 22 | BT antenna location in car? | Typically shark-fin (roof) or behind dashboard trim |
| 23 | Why does BT audio stutter in cars? | Common: WiFi interference, weak signal, CPU load, buffer underrun |
| 24 | How to debug BT issues on AAOS? | btsnoop log + adb logcat -b bluetooth + dumpsys bluetooth_manager |
| 25 | What is aptX Adaptive? | Qualcomm codec: dynamic bitrate (280-420 Kbps), low latency mode |
| 26 | PCM format for BT audio? | Typically: 16-bit, 48 kHz, stereo (A2DP) or 16 kHz mono (HFP WBS) |
| 27 | What triggers auto-reconnection? | Vehicle power-on, BT module restart, previously bonded device in range |
| 28 | What is phone-as-key latency requirement? | <500ms from approach to unlock response |
| 29 | How does car detect phone proximity? | BLE scanning → RSSI threshold → trigger deeper authentication |
| 30 | What version of AVRCP supports browsing? | AVRCP 1.4+ (folder browsing, now playing list) |

---

## DECK 10: TOOLS & DEBUGGING (25 cards)

| # | Front (Question) | Back (Answer) |
|---|-------------------|---------------|
| 1 | Tool to analyze btsnoop log? | Wireshark (File → Open → btsnoop_hci.log) |
| 2 | Command to capture BT on Linux live? | btmon (or hcidump, deprecated) |
| 3 | Command to scan for Classic devices? | hcitool scan (or bluetoothctl → scan on) |
| 4 | Command to scan for BLE devices? | hcitool lescan (or bluetoothctl → scan on) |
| 5 | Command to connect to BLE GATT? | gatttool -b <MAC> -I (then: connect) |
| 6 | Command to read GATT characteristic? | gatttool: char-read-hnd <handle> |
| 7 | Android command to dump BT state? | adb shell dumpsys bluetooth_manager |
| 8 | Android BT logcat tag? | adb logcat -b bluetooth (or tag: bt_*, bluetooth) |
| 9 | HCI reset command opcode? | 0x0C03 (OGF=0x03, OCF=0x0003) |
| 10 | HCI LE Set Scan Enable opcode? | 0x200C |
| 11 | How to check BT firmware version? | HCI Read Local Version: opcode 0x1001 |
| 12 | How to check RSSI in Android? | BluetoothGatt.readRemoteRssi() or btsnoop analysis |
| 13 | Wireshark filter for HFP AT commands? | btrfcomm contains "AT" |
| 14 | Wireshark filter for A2DP media? | btavdtp.signal_id == 0x07 (for Start) |
| 15 | How to force SBC codec for testing? | Disable other codecs in Developer Options |
| 16 | nRF Connect app main uses? | BLE scan, GATT explore, read/write characteristics, log advertising |
| 17 | Ellisys BT Tracker? | Professional BT protocol analyzer (hardware sniffer) |
| 18 | What is hcidump? | Legacy Linux tool for raw HCI packet dump (replaced by btmon) |
| 19 | How to check AFH channel map? | HCI Read AFH Channel Map: opcode 0x1406 |
| 20 | How to power cycle BT on Android? | Settings → BT off/on, or: adb shell svc bluetooth disable/enable |
| 21 | How to clear BT bond info? | Settings → Forget device, or: adb shell rm /data/misc/bluedroid/bt_config.conf |
| 22 | BT certification testing body? | Bluetooth SIG (BQTF — Bluetooth Qualification Test Facility) |
| 23 | What is PTS (Profile Tuning Suite)? | Bluetooth SIG tool for profile interoperability testing |
| 24 | What does "reason 0x08" mean in disconnect? | Connection timeout (supervision timeout expired) |
| 25 | What does "reason 0x13" mean in disconnect? | Remote user terminated connection |

---

## DECK BONUS: NUMBERS TO MEMORIZE (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | BLE advertising interval minimum | 20 ms (connectable) |
| 2 | BLE connection interval minimum | 7.5 ms |
| 3 | BLE connection interval maximum | 4 seconds |
| 4 | T_IFS (Inter-Frame Space) | 150 µs |
| 5 | BT Classic slot duration | 625 µs |
| 6 | BT Classic channels | 79 |
| 7 | BLE total channels | 40 |
| 8 | BLE data channels | 37 |
| 9 | BLE advertising channels | 3 (ch 37, 38, 39) |
| 10 | Default ATT MTU | 23 bytes |
| 11 | Max ATT MTU | 517 bytes |
| 12 | DLE max payload | 251 bytes |
| 13 | Max piconet active slaves | 7 |
| 14 | Max parked slaves | 255 |
| 15 | BD_ADDR size | 48 bits (6 bytes) |
| 16 | BT Classic EDR max rate | 3 Mbps |
| 17 | BLE 2M PHY rate | 2 Mbps |
| 18 | BLE Coded S=8 rate | 125 Kbps |
| 19 | BLE CRC size | 24 bits (3 bytes) |
| 20 | LC3 frame duration options | 7.5 ms or 10 ms |

---

END OF DOCUMENT 04 — FLASHCARDS
