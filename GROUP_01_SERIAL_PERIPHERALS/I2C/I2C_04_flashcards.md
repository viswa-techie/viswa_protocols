# I2C — FLASHCARDS (Spaced Repetition)
# ════════════════════════════════════════════════════════════════════
# Protocol: I2C | Document: 04 of 08 — 250+ Flashcards in 10 Decks
# Format: Q: (front) / A: (back) — optimized for Anki import
# ════════════════════════════════════════════════════════════════════

---

# DECK 1: IDENTITY & BASICS (25 cards)

Q: What does I2C stand for?
A: Inter-Integrated Circuit (I²C / IIC)

Q: Who invented I2C and when?
A: Philips Semiconductor (now NXP) in 1982

Q: How many signal wires does I2C use?
A: 2 — SDA (data) + SCL (clock). Also needs shared GND.

Q: What is the current I2C specification?
A: UM10204 Rev 7.0 (October 2021) — free from NXP

Q: Is I2C synchronous or asynchronous?
A: Synchronous — master provides clock (SCL)

Q: Is I2C half-duplex or full-duplex?
A: Half-duplex — single bidirectional SDA line

Q: What topology does I2C use?
A: Multi-master, multi-slave, shared 2-wire bus

Q: Name the 5 I2C speed modes.
A: Standard (100k), Fast (400k), Fast+ (1M), High-Speed (3.4M), Ultra-Fast (5M)

Q: What is the maximum bus capacitance for standard/fast mode?
A: 400 pF

Q: How many usable addresses in 7-bit mode?
A: 112 (128 minus 16 reserved)

Q: What electrical output type does I2C use?
A: Open-drain (or open-collector)

Q: Why does I2C need pull-up resistors?
A: Open-drain can only pull LOW; pull-ups provide the HIGH state

Q: What is TWI?
A: Two-Wire Interface — Atmel's name for I2C (identical protocol)

Q: What is SMBus?
A: System Management Bus — Intel's stricter I2C subset with timeout (35ms) and PEC (CRC-8)

Q: Name the I2C successor protocol.
A: MIPI I3C — backward compatible, up to 12.5 MHz, in-band interrupts

Q: What is DDC?
A: Display Data Channel — I2C at 100 kHz used for HDMI/VGA EDID data

Q: What is SCCB?
A: Serial Camera Control Bus — OmniVision's I2C-like protocol for camera sensors

Q: What layer does I2C operate at?
A: Physical (Layer 1) + Data Link (Layer 2) of OSI model

Q: Can I2C detect errors?
A: Yes — ACK/NACK after every byte (not CRC though; SMBus adds PEC)

Q: What is PMBus?
A: Power Management Bus — extends SMBus for power supply control/monitoring

Q: What year did I2C become patent-free?
A: 2007 (NXP released patents)

Q: What was the original application for I2C?
A: Connecting ICs inside Philips TV sets (CPU ↔ EEPROM ↔ tuner)

Q: Standard I2C clock frequency?
A: 100 kHz (Standard mode)

Q: Fast mode clock frequency?
A: 400 kHz

Q: Fast-mode Plus clock frequency?
A: 1 MHz

---

# DECK 2: ADDRESSING (25 cards)

Q: How is an I2C address transmitted?
A: 7 bits of address + 1 R/W̄ bit = 8-bit first byte after START

Q: What does R/W̄ = 0 mean?
A: Write — master sends data to slave

Q: What does R/W̄ = 1 mean?
A: Read — master requests data from slave

Q: Device address 0x68 in write mode = ?
A: 0xD0 (0x68 << 1 | 0 = 1101_0000)

Q: Device address 0x68 in read mode = ?
A: 0xD1 (0x68 << 1 | 1 = 1101_0001)

Q: What is the general call address?
A: 0x00 with W bit — broadcasts to all devices

Q: What command resets all devices via general call?
A: General call (0x00) + second byte 0x06

Q: What addresses are reserved for 10-bit addressing prefix?
A: 0x78–0x7B (binary 1111 0xx)

Q: What addresses are HS master codes?
A: 0x04–0x07 (binary 0000 1xx)

Q: How does 10-bit addressing work?
A: Byte 1: 11110 + A9:A8 + R/W̄; Byte 2: A7:A0 (full 10-bit address)

Q: How many devices with 10-bit addressing?
A: 1024 addresses (minus reserved)

Q: EEPROM (24Cxx) typical address?
A: 0x50–0x57 (A2/A1/A0 pins select)

Q: DS3231 RTC address?
A: 0x68 (fixed)

Q: MPU6050 IMU address?
A: 0x68 (AD0=0) or 0x69 (AD0=1)

Q: PCF8574 GPIO expander address?
A: 0x20–0x27 (A2/A1/A0 pins)

Q: SSD1306 OLED address?
A: 0x3C (SA0=0) or 0x3D (SA0=1)

Q: LM75 temperature sensor address?
A: 0x48–0x4F (A2/A1/A0 pins)

Q: How do you solve I2C address conflicts?
A: Change address pins, use I2C mux (PCA9548A), or use separate bus

Q: PCA9548A mux address?
A: 0x70–0x77 (A2/A1/A0 pins)

Q: What is an address pin on I2C devices?
A: Hardware pin (A0/A1/A2) tied HIGH/LOW to configure device address — allows multiple identical devices

Q: How many identical AT24C256 EEPROMs on one bus?
A: 8 (addresses 0x50–0x57 using A2/A1/A0 pins)

Q: What happens if two devices have the same address?
A: Both respond simultaneously → data corruption. Must resolve via mux or address pin change.

Q: Reserved address 0x7C–0x7F purpose?
A: Device ID mechanism and future reserved

Q: Which bit is transmitted first in the address?
A: MSB (A6) first

Q: 0x50 write in binary on the wire?
A: 1010_000_0 (7-bit addr: 1010000, R/W=0)

---

# DECK 3: PROTOCOL MECHANICS (30 cards)

Q: What is the START condition?
A: SDA transitions HIGH→LOW while SCL is HIGH

Q: What is the STOP condition?
A: SDA transitions LOW→HIGH while SCL is HIGH

Q: What is a Repeated START?
A: START condition issued without prior STOP (keeps bus locked)

Q: When does SDA normally change?
A: Only when SCL is LOW (during data transfer)

Q: Exception to "SDA changes only when SCL LOW"?
A: START and STOP conditions — SDA changes while SCL is HIGH

Q: How many bits per transferred byte?
A: 8 data bits + 1 ACK/NACK bit = 9 clock pulses

Q: Which bit order? MSB or LSB first?
A: MSB first

Q: What is ACK?
A: Receiver pulls SDA LOW during 9th clock → "byte received OK"

Q: What is NACK?
A: Receiver leaves SDA HIGH during 9th clock → "error/done"

Q: Who sends ACK after address byte?
A: Slave with matching address

Q: Who sends ACK after write data byte?
A: Slave (receiver of data)

Q: Who sends NACK on last read byte?
A: Master (tells slave: "stop sending, I'm done")

Q: What does NACK after address mean?
A: No device at that address (or device busy/error)

Q: Why use Repeated START for register reads?
A: Keeps bus locked between write (register addr) and read (data) phases

Q: Write transaction structure?
A: S → [Addr+W] → ACK → [Reg] → ACK → [Data] → ACK → P

Q: Read transaction structure?
A: S → [Addr+W] → ACK → [Reg] → ACK → Sr → [Addr+R] → ACK → [Data] → NACK → P

Q: What is bus idle state?
A: Both SDA and SCL HIGH (pull-ups holding)

Q: When is a data bit valid/sampled?
A: When SCL is HIGH (SDA must be stable)

Q: What is bus busy?
A: Period between START and STOP conditions

Q: What is t_BUF?
A: Bus free time between STOP and next START (1.3µs Fast mode)

Q: Multi-byte read: master sends ACK or NACK?
A: ACK for all bytes EXCEPT the last (NACK signals last byte)

Q: Can a slave initiate communication?
A: No — only masters generate START/clock. Slaves use interrupt GPIO for attention.

Q: What is bus free (idle) condition?
A: Both SDA and SCL HIGH for t_BUF duration

Q: Write then immediate read = what pattern?
A: Write register address → Repeated START → Read data (no STOP in between)

Q: General call + 0x06 does what?
A: Software reset of all devices on bus

Q: How long is one byte transfer at 400 kHz?
A: 9 bits × 2.5µs = 22.5 µs (including ACK clock)

Q: Maximum raw throughput at 400 kHz?
A: ~44.4 KB/s (400000/9 = 44444 bytes/s), ~35 KB/s with overhead

Q: What makes START unique vs. data?
A: SDA changes while SCL HIGH — never happens during normal data transfer

Q: What is a combined format transaction?
A: Write + Repeated START + Read in one atomic transaction (no bus release)

Q: Can multiple STARTs occur without STOP?
A: Yes — Repeated STARTs can chain multiple operations atomically

---

# DECK 4: ELECTRICAL & PHYSICAL (25 cards)

Q: What is open-drain?
A: Output can only actively pull LOW (MOSFET to GND). HIGH via external pull-up.

Q: What is wired-AND?
A: If ANY device pulls LOW, line is LOW. HIGH only when ALL release. Natural AND logic.

Q: Default pull-up for standard mode (100kHz, ~200pF)?
A: 4.7 KΩ

Q: Pull-up formula (max Rp)?
A: Rp_max = t_rise / (0.8473 × C_bus)

Q: Pull-up formula (min Rp)?
A: Rp_min = (VDD - V_OL) / I_OL = (3.3-0.4)/3mA ≈ 967Ω

Q: V_OL maximum?
A: 0.4V (device must pull below this)

Q: V_IL threshold at 3.3V?
A: 0.3 × VDD = 0.99V (below this = LOW)

Q: V_IH threshold at 3.3V?
A: 0.7 × VDD = 2.31V (above this = HIGH)

Q: Maximum rise time in fast mode?
A: 300 ns

Q: Maximum rise time in standard mode?
A: 1000 ns (1 µs)

Q: Bus capacitance per device (typical)?
A: 5–10 pF (I/O pin capacitance)

Q: PCB trace capacitance (typical)?
A: ~1 pF per cm on FR4

Q: Maximum bus capacitance standard/fast?
A: 400 pF

Q: What happens if pull-up too high (e.g., 47KΩ)?
A: Rise time too slow → fails at target speed → corrupted data

Q: What happens if pull-up too low (e.g., 220Ω)?
A: Sink current exceeds device spec → device can't pull LOW properly → V_OL exceeded

Q: Can you use internal MCU pull-ups for I2C?
A: Not recommended — typically 20-50KΩ, too weak. Only OK for slow prototyping.

Q: How does MOSFET level shifting work?
A: Gate tied to low-V side VDD. Low-side pull LOW → MOSFET conducts → pulls high-side. High-side pull LOW → body diode → pulls low-side.

Q: Common level shift MOSFET?
A: BSS138 (N-channel, SOT-23)

Q: Why is I2C easy to level-shift?
A: Open-drain: bidirectional MOSFET with pull-ups on each side works automatically

Q: I_OL spec for standard/fast mode?
A: 3 mA (device must sink this at V_OL ≤ 0.4V)

Q: I_OL for Fast-mode Plus?
A: 20 mA (much stronger drivers needed)

Q: What limits I2C cable length?
A: Bus capacitance (400pF limit) and signal integrity (noise pickup)

Q: Practical max cable length for I2C?
A: 1–2 meters without buffers; up to 10m+ with differential converters

Q: What is an I2C bus buffer?
A: Device (P82B715) that regenerates I2C signals to extend distance/capacitance

Q: Multiple pull-ups on same bus — effect?
A: Parallel resistance → lower total R → may be too strong → excessive sink current

---

# DECK 5: ARBITRATION & CLOCK STRETCHING (25 cards)

Q: What is I2C arbitration?
A: Process where multiple masters determine which one gets bus control

Q: How does arbitration work?
A: Bit-by-bit: master driving HIGH but reading LOW = lost (wired-AND). Loser backs off.

Q: Is I2C arbitration destructive?
A: No! Winner's data is preserved perfectly on the bus.

Q: Which master wins arbitration?
A: The one sending more LOW bits (lower address wins since 0 dominates in wired-AND)

Q: When does arbitration occur?
A: During address phase (and rarely data phase if same address)

Q: What must loser do after losing arbitration?
A: Stop driving, wait for STOP on bus, then retry

Q: Can arbitration damage the bus?
A: No — open-drain means no electrical conflict (wired-AND property)

Q: What is clock synchronization in multi-master?
A: SCL LOW when ANY master pulls LOW; HIGH only when ALL release → syncs to slowest

Q: What is clock stretching?
A: Slave holds SCL LOW to pause master while processing data

Q: Who can stretch the clock?
A: Only the slave (holds SCL LOW after data bit)

Q: What must master do regarding clock stretching?
A: Check SCL actually went HIGH after releasing it; wait if slave holds it LOW

Q: Does I2C spec define clock stretch timeout?
A: No! I2C has no timeout. Bus can hang forever. (SMBus adds 35ms timeout)

Q: SMBus clock timeout?
A: 35 ms — if SCL LOW longer, transaction aborted

Q: Why is clock stretching dangerous?
A: Hung slave holds SCL LOW forever → entire bus stuck → no timeout in I2C spec

Q: What devices commonly clock-stretch?
A: EEPROMs (during write), bit-banged slaves, ADCs (during conversion)

Q: How to detect if master ignores clock stretching?
A: Fast-mode communication fails with slow slaves; data appears corrupted

Q: Does Linux i2c-gpio support clock stretching?
A: Yes — it reads back SCL GPIO state after releasing it

Q: Do all hardware I2C controllers support clock stretching?
A: Most do, but some cheap ones don't — check datasheet carefully

Q: Lower address = higher or lower priority?
A: Higher priority (more zeros early → more likely to win arbitration)

Q: Can data phase have arbitration?
A: Yes, if two masters address the same slave — extremely rare in practice

Q: Multi-master write to same device?
A: Arbitration in data phase — master writing different data bit loses

Q: How to avoid arbitration delays?
A: Use single master (most systems) or schedule masters to different time slots

Q: I2C vs CAN arbitration?
A: Similar principle (dominant/recessive) but I2C uses SDA, CAN uses single wire with different physical layer

Q: How does slave influence clock?
A: Only via clock stretching (holding SCL LOW). Cannot generate own clock.

Q: Arbitration lost flag in STM32?
A: ARLO bit in I2C_SR1 register

---

# DECK 6: REGISTERS & HARDWARE (25 cards)

Q: STM32 I2C enable bit?
A: PE (Peripheral Enable) in I2C_CR1

Q: STM32 START generation bit?
A: START bit in I2C_CR1

Q: STM32 STOP generation bit?
A: STOP bit in I2C_CR1

Q: STM32 I2C data register?
A: I2C_DR — 8-bit read/write

Q: SB flag in I2C_SR1 means?
A: Start Bit sent — set when START condition generated

Q: ADDR flag in I2C_SR1 means?
A: Address sent and ACK received

Q: BTF flag in I2C_SR1 means?
A: Byte Transfer Finished — data byte transfer complete

Q: AF flag in I2C_SR1 means?
A: Acknowledge Failure (NACK received)

Q: BUSY bit in I2C_SR2 means?
A: Bus is busy (START detected, not yet STOP)

Q: How to clear ADDR flag on STM32?
A: Read SR1 then read SR2 (sequential read clears it)

Q: CCR register purpose?
A: Clock Control Register — sets SCL frequency

Q: TRISE register purpose?
A: Configures maximum rise time for proper timing

Q: CCR formula standard mode?
A: CCR = APB_CLK / (2 × f_SCL) = 42MHz / (2 × 100kHz) = 210

Q: CCR formula fast mode (duty 2:1)?
A: CCR = APB_CLK / (3 × f_SCL) = 42MHz / (3 × 400kHz) = 35

Q: TRISE formula standard mode?
A: (APB_MHz × 1000/1000) + 1 = 42 + 1 = 43

Q: TRISE formula fast mode?
A: (APB_MHz × 300/1000) + 1 = (42×300/1000)+1 = 13

Q: GPIO mode for I2C pins?
A: Alternate Function (AF) + Open-Drain + Pull-Up

Q: STM32 I2C alternate function number?
A: AF4 (for I2C1/2/3 on most STM32F4)

Q: What is SWRST in I2C_CR1?
A: Software Reset — resets I2C peripheral (for error recovery)

Q: DMA enable bit?
A: DMAEN in I2C_CR2

Q: Interrupt enable for events?
A: ITEVTEN in I2C_CR2 (event interrupts: SB, ADDR, BTF, STOPF)

Q: Interrupt enable for buffer?
A: ITBUFEN in I2C_CR2 (buffer interrupts: TXE, RXNE)

Q: OVR flag means?
A: Overrun/Underrun — DMA/CPU didn't read data before next byte arrived

Q: BERR flag means?
A: Bus Error — misplaced START/STOP detected

Q: MSL bit in SR2 means?
A: Master/Slave — 1=master mode, 0=slave mode

---

# DECK 7: LINUX & DEVICE TREE (30 cards)

Q: Linux I2C userspace device node?
A: /dev/i2c-N (N = bus number)

Q: Linux header for I2C userspace?
A: #include <linux/i2c-dev.h>

Q: ioctl to set slave address?
A: ioctl(fd, I2C_SLAVE, addr)

Q: i2cdetect command?
A: `i2cdetect -y <bus>` — scans for devices

Q: i2cget command?
A: `i2cget -y <bus> <addr> <reg>` — reads one register

Q: i2cset command?
A: `i2cset -y <bus> <addr> <reg> <val>` — writes one register

Q: i2cdump command?
A: `i2cdump -y <bus> <addr>` — dumps all registers

Q: i2ctransfer command?
A: `i2ctransfer -y <bus> w1@<addr> <reg> r<n>` — raw transfer

Q: Device Tree property for I2C speed?
A: clock-frequency = <400000>; (in Hz)

Q: Device Tree property for device address?
A: reg = <0x48>; (7-bit address)

Q: Key DT property for driver binding?
A: compatible = "vendor,device-name";

Q: How to enable I2C bus in DT?
A: status = "okay"; in the i2c controller node

Q: Linux I2C core file?
A: drivers/i2c/i2c-core-base.c

Q: Linux I2C dev file (userspace access)?
A: drivers/i2c/i2c-dev.c

Q: Qualcomm I2C driver file?
A: drivers/i2c/busses/i2c-qup.c (legacy) or i2c-qcom-geni.c (GENI)

Q: Raspberry Pi I2C driver?
A: drivers/i2c/busses/i2c-bcm2835.c

Q: I2C mux driver for PCA9548A?
A: drivers/i2c/muxes/i2c-mux-pca954x.c

Q: Linux I2C API for register read (SMBus)?
A: i2c_smbus_read_byte_data(client, reg)

Q: Linux I2C API for register write (SMBus)?
A: i2c_smbus_write_byte_data(client, reg, val)

Q: Linux I2C raw transfer API?
A: i2c_transfer(adapter, msgs, num_msgs)

Q: struct i2c_msg fields?
A: addr (u16), flags (u16: I2C_M_RD etc.), len (u16), buf (u8*)

Q: I2C_M_RD flag means?
A: Read transfer (slave→master). Absence = write.

Q: How to register I2C adapter in Linux?
A: i2c_add_adapter() or i2c_add_numbered_adapter()

Q: How to register I2C client driver?
A: module_i2c_driver(my_driver) macro

Q: Linux I2C bus recovery function?
A: i2c_recover_bus(adapter)

Q: i2c-gpio DT compatible?
A: "i2c-gpio"

Q: sysfs path for I2C devices?
A: /sys/bus/i2c/devices/

Q: Add device at runtime via sysfs?
A: echo "name addr" > /sys/bus/i2c/devices/i2c-N/new_device

Q: Remove device at runtime via sysfs?
A: echo "addr" > /sys/bus/i2c/devices/i2c-N/delete_device

Q: What is regmap in context of I2C?
A: Kernel abstraction for register access — handles I2C/SPI transport, caching, locking, byte order

---

# DECK 8: ERRORS & DEBUGGING (25 cards)

Q: Most common I2C hardware problem?
A: Missing or wrong pull-up resistors

Q: What causes "bus stuck LOW"?
A: Slave holding SDA LOW (crashed mid-transaction, waiting for clocks)

Q: How to recover from bus stuck?
A: 9 clock pulses on SCL, then STOP condition

Q: Why 9 clock pulses for recovery?
A: Worst case: slave in middle of byte (8 data + 1 ACK = 9 bits to finish)

Q: NACK on address — most common cause?
A: Wrong address or device not powered/present

Q: Linux error code for NACK?
A: -ENXIO or -EREMOTEIO

Q: How to check if bus is stuck in software?
A: Read SDA GPIO — if LOW while bus should be idle → stuck

Q: What causes intermittent I2C failures?
A: Marginal rise time, EMI, power supply noise, loose connections

Q: How to verify pull-up is correct?
A: Measure rise time on scope — must be <300ns (fast mode) or <1µs (standard)

Q: What tool best diagnoses I2C issues?
A: Logic analyzer / oscilloscope (see actual waveforms)

Q: What is "7-bit vs 8-bit address confusion"?
A: Datasheets vary: some show 7-bit (0x68), others show 8-bit with R/W included (0xD0/0xD1)

Q: Clock glitch effect on I2C?
A: Slave miscounts bits → expects wrong number of clocks → state mismatch → bus stuck

Q: Power glitch effect on I2C?
A: Master resets mid-transaction → slave still waiting for clocks → SDA stuck LOW

Q: EMI effect on I2C?
A: Noise on SDA sampled as wrong bit → data corruption; noise on SCL → clock glitch

Q: What is OVR (overrun) error?
A: New byte arrived before previous read from DR → data lost

Q: EEPROM write — common mistake?
A: Writing across page boundary → wraps within page → data corrupted

Q: EEPROM ACK polling?
A: After write, repeatedly send address until device ACKs (write cycle complete, ~5ms)

Q: Multiple pull-ups — what goes wrong?
A: Parallel resistance too low → excessive current → can't achieve V_OL < 0.4V

Q: Long I2C cable — what goes wrong?
A: Capacitance exceeds 400pF → slow rise → fails. Also noise pickup.

Q: Bus stuck after power cycle — why?
A: Master reset during slave's ACK → slave still holding SDA LOW → needs clock recovery

Q: What is "NAK storm"?
A: Software continuously polling non-existent device → floods bus with useless NACK traffic

Q: Race condition in RTOS + I2C?
A: Two tasks access bus without mutex → interleaved START/data → corruption

Q: Temperature effect on I2C?
A: Timing margins shrink at extreme temperatures → marginal designs fail

Q: How to isolate which device causes bus stuck?
A: Use I2C mux → disable channels one by one → find culprit

Q: STM32 BUSY flag stuck bug?
A: Known errata — requires specific peripheral reset sequence (toggle PE, SWRST)

---

# DECK 9: AUTOMOTIVE & REAL-WORLD (25 cards)

Q: Name 3 I2C devices in automotive head unit.
A: PMIC (power), touch controller, temperature sensor

Q: Qualcomm I2C controller name?
A: QUP (Qualcomm Universal Peripheral) — configurable as I2C/SPI/UART

Q: What is GENI in Qualcomm?
A: Generic Interface — newer QUP (v3) with programmable firmware serial engine

Q: Typical automotive PMIC I2C address?
A: 0x25 (vendor-specific, e.g., PCA9450)

Q: Why use I2C mux in automotive?
A: Address conflicts, bus segmentation, fault isolation between subsystems

Q: PCA9548A — what is it?
A: 8-channel I2C multiplexer/switch (address 0x70–0x77)

Q: I2C in AUTOSAR — standardized?
A: No standard I2C Handler like SPI. Usually vendor-specific MCAL or CDD.

Q: Android sensor access path?
A: Sensor HW → Sensor HAL (AIDL) → SensorService → SensorManager → App

Q: SELinux and I2C in Android?
A: /dev/i2c-N access restricted to HAL processes only via SELinux policy

Q: I2C for audio in automotive?
A: Control path for audio codecs (volume, routing, EQ) — not audio data itself

Q: Why separate I2C buses in automotive?
A: Fault isolation — one bus failure doesn't affect critical devices on another bus

Q: ESD protection for automotive I2C?
A: TVS diodes on SDA/SCL lines (e.g., PESD1I2C)

Q: Series resistors on I2C — why?
A: Limit overcurrent, reduce ringing/EMI (33-100Ω typical)

Q: I2C mux for camera EEPROMs — why?
A: Multiple cameras have EEPROM at 0x50; mux selects one at a time

Q: What monitors I2C bus health in production?
A: Transaction success/fail counters, NACK rates, bus recovery events

Q: Automotive temperature range for I2C?
A: -40°C to +105°C (or +125°C for under-hood)

Q: I2C for LED ambient lighting?
A: LED drivers (LP5009, IS31FL3731) controlled via I2C for color/brightness

Q: IO expander in automotive — purpose?
A: Expand GPIO count for switches, buttons, indicator LEDs via I2C (PCA9555)

Q: Fuel gauge over I2C?
A: Battery monitoring IC (BQ27426) reports charge level, voltage, current via I2C

Q: I2C watchdog concept?
A: Timer that triggers bus recovery if transaction doesn't complete within expected time

Q: Diagnostic reporting for I2C failures?
A: AUTOSAR DEM (Diagnostic Event Manager) or UDS service for field diagnosis

Q: I2C in hypervisor environment?
A: Passthrough (exclusive to one VM) or virtio-I2C (shared with arbitration in hypervisor)

Q: AAOS CarService and I2C?
A: CarService uses Vehicle HAL which may access I2C sensors through Sensor HAL path

Q: I2C bus capacitance in automotive — challenge?
A: Long harness wires + multiple ECUs → easily exceed 400pF → need buffers/mux

Q: Safe state when I2C fails?
A: Use last-known-good values or defined failsafe values (e.g., default brightness)

---

# DECK 10: COMPARISONS & TRADE-OFFS (25 cards)

Q: I2C vs SPI — when choose I2C?
A: Many devices, few pins needed, low/medium speed OK, need multi-master or ACK

Q: I2C vs SPI — when choose SPI?
A: Need high speed, full-duplex, streaming data, deterministic latency

Q: I2C vs UART — key difference?
A: I2C is synchronous (clock) + multi-device bus; UART is async, point-to-point

Q: I2C vs CAN — when choose CAN?
A: Long distance (40m), high noise immunity needed, automotive networking

Q: I2C max speed vs SPI max speed?
A: I2C: 3.4 MHz (HS); SPI: 200+ MHz (typical 50-100 MHz)

Q: I2C pin count for 10 devices vs SPI?
A: I2C: 2 pins total; SPI: 3 + 10 CS = 13 pins

Q: I2C error detection vs SPI?
A: I2C: ACK/NACK per byte; SPI: none built-in

Q: SMBus vs I2C — key addition?
A: 35ms timeout + PEC (CRC-8) + SMBALERT# + minimum 10 kHz clock

Q: I2C vs I3C — speed improvement?
A: I2C max 3.4 MHz; I3C up to 12.5 MHz (HDR mode)

Q: I2C vs I3C — interrupt handling?
A: I2C: separate GPIO interrupt; I3C: In-Band Interrupt (IBI) on SDA

Q: I2C vs I3C — addressing?
A: I2C: static (fixed in hardware); I3C: dynamic (assigned at runtime)

Q: I2C vs 1-Wire — wires?
A: I2C: 2 + GND; 1-Wire: 1 + GND (parasitic power possible)

Q: I2C half-duplex limit — impact?
A: Can't read and write simultaneously; round-trip for register read

Q: I2C open-drain advantage over push-pull?
A: Multi-master safe, no bus damage, easy level shifting, wired-AND logic

Q: I2C open-drain disadvantage?
A: Slow rise time (limited by Rp×C), limits maximum speed

Q: Push-pull advantage (SPI, I3C UFm)?
A: Fast rise/fall, no pull-ups needed, higher speed achievable

Q: Why can't SPI do multi-master easily?
A: Push-pull outputs → two masters driving opposite → short circuit / bus damage

Q: I2C bus utilization at 400 kHz with 10 sensors?
A: Each read ~225µs → 10 reads = 2.25ms → 100 reads/s = 22.5% utilization

Q: When does I2C become bottleneck?
A: High-frequency sensor polling (>1kHz per sensor) or large data (display buffers, firmware)

Q: I2C for display data — good idea?
A: No! Too slow for pixels. Use I2C for control (brightness, mode) but MIPI-DSI/LVDS for pixel data.

Q: I2C for audio data — good idea?
A: No! Use I2S/TDM for audio streams. I2C only for codec control registers.

Q: I2C reliability vs SPI?
A: I2C has ACK for every byte (better error detection). SPI is silent — no confirmation.

Q: I2C scalability vs SPI?
A: I2C: 112 devices, 2 pins; SPI: limited by CS pins and board space

Q: SCCB vs I2C?
A: SCCB: no clock stretching, no multi-master, 2-phase write (simpler but less robust)

Q: I2C for PMIC — why standard choice?
A: Low speed OK (rare config writes), 2 pins, ACK confirms command received, multiple PMICs addressable

---

END OF DOCUMENT 04 — FLASHCARDS
