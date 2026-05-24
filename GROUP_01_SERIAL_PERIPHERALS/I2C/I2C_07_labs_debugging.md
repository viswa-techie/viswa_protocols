# I2C — LABS & DEBUGGING EXERCISES
# ════════════════════════════════════════════════════════════════════
# Protocol: I2C | Document: 07 of 08 — Hands-On Labs + Debug Scenarios
# 8 Progressive Labs + 7 Real-World Debug Exercises
# ════════════════════════════════════════════════════════════════════

---

# LAB 1: I2C DEVICE SCAN (BEGINNER)

## Objective
Use Linux i2c-tools to discover devices on the I2C bus.

## Hardware
- Any Linux board (Raspberry Pi, BeagleBone, Qualcomm DragonBoard)
- I2C device connected (EEPROM, sensor, or OLED)
- Pull-up resistors (4.7KΩ to 3.3V on SDA + SCL)

## Steps

### Step 1: List Available I2C Buses
```bash
# List all I2C adapters
i2cdetect -l

# Expected output:
# i2c-0  i2c  bcm2835 (i2c@7e205000)  I2C adapter
# i2c-1  i2c  bcm2835 (i2c@7e804000)  I2C adapter
```

### Step 2: Scan Bus for Devices
```bash
# Scan bus 1 (Raspberry Pi default user bus)
i2cdetect -y 1

# Expected output (with OLED at 0x3C and sensor at 0x68):
#      0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
# 00:          -- -- -- -- -- -- -- -- -- -- -- -- --
# 10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
# 20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
# 30: -- -- -- -- -- -- -- -- -- -- -- -- 3c -- -- --
# 40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
# 50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
# 60: -- -- -- -- -- -- -- -- 68 -- -- -- -- -- -- --
# 70: -- -- -- -- -- -- -- --
```

### Step 3: Check Adapter Capabilities
```bash
i2cdetect -F 1

# Shows: I2C, SMBus Quick Command, Block Read, etc.
```

### Step 4: Verify Device by Reading WHO_AM_I
```bash
# MPU6050 WHO_AM_I register (0x75) should return 0x68
i2cget -y 1 0x68 0x75

# Expected: 0x68
```

## Verification
- [ ] Correctly identified bus number
- [ ] Found expected device addresses
- [ ] WHO_AM_I matches datasheet

---

# LAB 2: EEPROM READ/WRITE (BEGINNER-INTERMEDIATE)

## Objective
Read and write data to an I2C EEPROM (AT24C256 or similar).

## Hardware
- AT24C256 EEPROM (address 0x50, A0=A1=A2=GND)
- 4.7KΩ pull-ups on SDA + SCL

## Steps

### Step 1: Verify EEPROM Present
```bash
i2cdetect -y 1
# Should show 0x50
```

### Step 2: Write a Byte
```bash
# Write 0xAB to address 0x00, 0x00 (2-byte internal address for 24C256)
i2ctransfer -y 1 w3@0x50 0x00 0x00 0xAB
# Wait 5ms for write cycle
sleep 0.01
```

### Step 3: Read Back
```bash
# Set address pointer (write 2-byte address)
# Then read 1 byte
i2ctransfer -y 1 w2@0x50 0x00 0x00 r1

# Expected: 0xab
```

### Step 4: Write a String
```bash
# Write "Hello" (0x48 0x65 0x6C 0x6C 0x6F) starting at address 0x0010
i2ctransfer -y 1 w7@0x50 0x00 0x10 0x48 0x65 0x6C 0x6C 0x6F
sleep 0.01
```

### Step 5: Read Back String
```bash
i2ctransfer -y 1 w2@0x50 0x00 0x10 r5
# Expected: 0x48 0x65 0x6c 0x6c 0x6f
```

### Step 6: C Program — Page Write
```c
#include <stdio.h>
#include <fcntl.h>
#include <linux/i2c-dev.h>
#include <sys/ioctl.h>
#include <unistd.h>
#include <string.h>

#define EEPROM_ADDR 0x50
#define PAGE_SIZE 64  // AT24C256 page size

int main() {
    int fd = open("/dev/i2c-1", O_RDWR);
    ioctl(fd, I2C_SLAVE, EEPROM_ADDR);
    
    // Write page at address 0x0000
    uint8_t buf[2 + PAGE_SIZE];
    buf[0] = 0x00;  // Address high byte
    buf[1] = 0x00;  // Address low byte
    memset(&buf[2], 0xAA, PAGE_SIZE);  // Fill with 0xAA
    
    write(fd, buf, 2 + PAGE_SIZE);
    usleep(10000);  // 10ms write cycle
    
    // Read back
    uint8_t addr[2] = {0x00, 0x00};
    write(fd, addr, 2);
    
    uint8_t readbuf[PAGE_SIZE];
    read(fd, readbuf, PAGE_SIZE);
    
    // Verify
    for (int i = 0; i < PAGE_SIZE; i++) {
        if (readbuf[i] != 0xAA) {
            printf("MISMATCH at offset %d: got 0x%02X\n", i, readbuf[i]);
            close(fd);
            return 1;
        }
    }
    printf("SUCCESS: %d bytes verified\n", PAGE_SIZE);
    close(fd);
    return 0;
}
```

## Key Learning
- EEPROM needs 2-byte address (for >256 byte EEPROMs)
- Write cycle takes ~5ms — must wait before reading back
- Page boundaries: writing beyond page wraps! AT24C256 page = 64 bytes.

---

# LAB 3: SENSOR REGISTER ACCESS (INTERMEDIATE)

## Objective
Configure and read an IMU (MPU6050) over I2C.

## Hardware
- MPU6050 breakout (address 0x68)
- 4.7KΩ pull-ups

## Steps

### Step 1: Verify Device
```bash
# WHO_AM_I register (0x75) should return 0x68
i2cget -y 1 0x68 0x75
```

### Step 2: Wake Up Device
```bash
# PWR_MGMT_1 register (0x6B): clear SLEEP bit
# Default after reset: 0x40 (SLEEP=1)
i2cset -y 1 0x68 0x6B 0x00
```

### Step 3: Configure Accelerometer
```bash
# ACCEL_CONFIG (0x1C): ±2g range
i2cset -y 1 0x68 0x1C 0x00
```

### Step 4: Read Acceleration (6 bytes: XH,XL,YH,YL,ZH,ZL)
```bash
# Accelerometer data starts at 0x3B
i2ctransfer -y 1 w1@0x68 0x3B r6

# Convert: accel_x = (byte[0] << 8 | byte[1]) / 16384.0  (for ±2g)
```

### Step 5: C Program — Continuous Read
```c
#include <stdio.h>
#include <fcntl.h>
#include <linux/i2c-dev.h>
#include <sys/ioctl.h>
#include <unistd.h>
#include <stdint.h>

#define MPU6050_ADDR 0x68

int main() {
    int fd = open("/dev/i2c-1", O_RDWR);
    ioctl(fd, I2C_SLAVE, MPU6050_ADDR);
    
    // Wake up
    uint8_t wake[] = {0x6B, 0x00};
    write(fd, wake, 2);
    usleep(100000);
    
    // Read loop
    for (int i = 0; i < 100; i++) {
        uint8_t reg = 0x3B;
        write(fd, &reg, 1);
        
        uint8_t data[6];
        read(fd, data, 6);
        
        int16_t ax = (data[0] << 8) | data[1];
        int16_t ay = (data[2] << 8) | data[3];
        int16_t az = (data[4] << 8) | data[5];
        
        printf("AX=%.2fg AY=%.2fg AZ=%.2fg\n",
               ax/16384.0, ay/16384.0, az/16384.0);
        usleep(50000);  // 50ms = 20Hz
    }
    close(fd);
    return 0;
}
```

## Verification
- [ ] WHO_AM_I returns 0x68
- [ ] After wake, power register reads 0x00
- [ ] Z-axis shows ~1g (gravity) when flat

---

# LAB 4: MULTI-DEVICE BUS (INTERMEDIATE)

## Objective
Communicate with multiple devices on the same I2C bus.

## Hardware
- EEPROM at 0x50
- Temperature sensor (LM75/TMP112) at 0x48
- OLED display (SSD1306) at 0x3C
- Shared 4.7KΩ pull-ups (ONE pair for entire bus)

## Steps

### Step 1: Verify All Devices
```bash
i2cdetect -y 1
# Should see: 0x3C, 0x48, 0x50
```

### Step 2: Read Temperature
```bash
# LM75: Temperature register = 0x00 (2 bytes, 9-bit resolution)
i2ctransfer -y 1 w1@0x48 0x00 r2

# Convert: temp = (byte[0] << 1 | byte[1] >> 7) * 0.5
# Example: 0x19 0x00 → 25 × 0.5 = 12.5°C... wait.
# Actually: 0x19 = 25, byte[1] bit7 = 0 → 25.0°C
```

### Step 3: Store Temperature in EEPROM
```bash
# Read temp
TEMP=$(i2ctransfer -y 1 w1@0x48 0x00 r2)
# Write to EEPROM at address 0x00,0x00
i2ctransfer -y 1 w4@0x50 0x00 0x00 $TEMP
```

### Step 4: C Program — Multi-Device
```c
#include <stdio.h>
#include <fcntl.h>
#include <linux/i2c-dev.h>
#include <linux/i2c.h>
#include <sys/ioctl.h>
#include <unistd.h>
#include <stdint.h>

int i2c_read_reg(int fd, uint8_t addr, uint8_t reg, uint8_t *buf, int len) {
    struct i2c_msg msgs[2] = {
        { .addr = addr, .flags = 0, .len = 1, .buf = &reg },
        { .addr = addr, .flags = I2C_M_RD, .len = len, .buf = buf },
    };
    struct i2c_rdwr_ioctl_data data = { .msgs = msgs, .nmsgs = 2 };
    return ioctl(fd, I2C_RDWR, &data);
}

int main() {
    int fd = open("/dev/i2c-1", O_RDWR);
    
    // Read temperature from LM75 (0x48)
    uint8_t temp[2];
    i2c_read_reg(fd, 0x48, 0x00, temp, 2);
    float temperature = temp[0] + (temp[1] >> 7) * 0.5;
    printf("Temperature: %.1f°C\n", temperature);
    
    // Read EEPROM byte (0x50)
    uint8_t eeprom_data;
    uint8_t eeprom_addr[2] = {0x00, 0x00};
    struct i2c_msg msgs[2] = {
        { .addr = 0x50, .flags = 0, .len = 2, .buf = eeprom_addr },
        { .addr = 0x50, .flags = I2C_M_RD, .len = 1, .buf = &eeprom_data },
    };
    struct i2c_rdwr_ioctl_data data = { .msgs = msgs, .nmsgs = 2 };
    ioctl(fd, I2C_RDWR, &data);
    printf("EEPROM[0x0000] = 0x%02X\n", eeprom_data);
    
    close(fd);
    return 0;
}
```

## Key Learning
- Use `I2C_RDWR` ioctl for multi-device without reopen
- ONE pair of pull-ups for entire bus (not per device!)
- Different devices have different register layouts

---

# LAB 5: DMA-BASED I2C TRANSFER (ADVANCED)

## Objective
Configure STM32 I2C with DMA for efficient multi-byte transfers.

## Hardware
- STM32F4 board
- MPU6050 at 0x68

## Steps

### Step 1: I2C + DMA Initialization
```c
// Enable clocks
RCC->AHB1ENR |= RCC_AHB1ENR_DMA1EN | RCC_AHB1ENR_GPIOBEN;
RCC->APB1ENR |= RCC_APB1ENR_I2C1EN;

// GPIO config: PB6=SCL, PB7=SDA, AF4, Open-Drain
GPIOB->MODER |= (2<<12)|(2<<14);
GPIOB->OTYPER |= (1<<6)|(1<<7);
GPIOB->AFR[0] |= (4<<24)|(4<<28);

// I2C config: Fast mode
I2C1->CR2 = 42;        // 42 MHz APB1
I2C1->CCR = 35;        // 400 kHz
I2C1->TRISE = 13;
I2C1->CR2 |= I2C_CR2_DMAEN;  // Enable DMA requests
I2C1->CR1 = I2C_CR1_PE;

// DMA config: DMA1 Stream 0 Channel 1 (I2C1_RX)
DMA1_Stream0->CR = 0;
DMA1_Stream0->CR |= (1 << 25);  // Channel 1
DMA1_Stream0->CR |= DMA_SxCR_MINC;  // Memory increment
DMA1_Stream0->CR |= DMA_SxCR_TCIE;  // Transfer complete IRQ
DMA1_Stream0->PAR = (uint32_t)&I2C1->DR;
```

### Step 2: DMA Read Function
```c
volatile uint8_t dma_buffer[14];  // 6 accel + 2 temp + 6 gyro
volatile uint8_t dma_complete = 0;

void i2c_dma_read(uint8_t addr, uint8_t reg, uint8_t *buf, uint16_t len) {
    // Set register pointer (polling for address phase)
    I2C1->CR1 |= I2C_CR1_START;
    while(!(I2C1->SR1 & I2C_SR1_SB));
    I2C1->DR = (addr << 1);
    while(!(I2C1->SR1 & I2C_SR1_ADDR));
    (void)I2C1->SR2;
    I2C1->DR = reg;
    while(!(I2C1->SR1 & I2C_SR1_BTF));
    
    // Configure DMA for read
    DMA1_Stream0->M0AR = (uint32_t)buf;
    DMA1_Stream0->NDTR = len;
    DMA1_Stream0->CR |= DMA_SxCR_EN;
    
    // Repeated START + Read
    I2C1->CR1 |= I2C_CR1_ACK | I2C_CR1_START;
    while(!(I2C1->SR1 & I2C_SR1_SB));
    I2C1->DR = (addr << 1) | 1;
    while(!(I2C1->SR1 & I2C_SR1_ADDR));
    (void)I2C1->SR2;
    
    // DMA handles the rest — CPU free!
    // DMA ISR will set dma_complete flag
}

void DMA1_Stream0_IRQHandler(void) {
    if (DMA1->LISR & DMA_LISR_TCIF0) {
        DMA1->LIFCR = DMA_LIFCR_CTCIF0;
        I2C1->CR1 |= I2C_CR1_STOP;
        dma_complete = 1;
    }
}
```

### Step 3: Usage
```c
int main(void) {
    // ... init ...
    while(1) {
        dma_complete = 0;
        i2c_dma_read(0x68, 0x3B, (uint8_t*)dma_buffer, 14);
        while(!dma_complete);  // Or use RTOS semaphore
        
        // Process data
        int16_t ax = (dma_buffer[0]<<8) | dma_buffer[1];
        // ... process all 14 bytes ...
    }
}
```

## Key Learning
- DMA frees CPU during multi-byte I2C transfers
- Address phase still needs CPU (or state machine)
- Critical for high-frequency sensor reads (1kHz+)

---

# LAB 6: LINUX I2C KERNEL DRIVER (ADVANCED)

## Objective
Write a minimal Linux I2C client driver for a temperature sensor.

## Steps

### Step 1: Device Tree Entry
```dts
&i2c1 {
    status = "okay";
    clock-frequency = <400000>;
    
    mytemp: temperature@48 {
        compatible = "lab,my-temp-sensor";
        reg = <0x48>;
    };
};
```

### Step 2: Kernel Module
```c
#include <linux/module.h>
#include <linux/i2c.h>
#include <linux/hwmon.h>
#include <linux/hwmon-sysfs.h>

struct my_temp_data {
    struct i2c_client *client;
    struct mutex lock;
};

static ssize_t temp_show(struct device *dev,
                         struct device_attribute *attr, char *buf) {
    struct my_temp_data *data = dev_get_drvdata(dev);
    int raw;
    
    mutex_lock(&data->lock);
    raw = i2c_smbus_read_word_swapped(data->client, 0x00);
    mutex_unlock(&data->lock);
    
    if (raw < 0)
        return raw;
    
    /* Convert: 12-bit, 0.0625°C/LSB, millidegrees for hwmon */
    int temp_mdeg = ((raw >> 4) * 625) / 10;
    return sprintf(buf, "%d\n", temp_mdeg);
}

static DEVICE_ATTR_RO(temp);

static struct attribute *my_temp_attrs[] = {
    &dev_attr_temp.attr,
    NULL,
};
ATTRIBUTE_GROUPS(my_temp);

static int my_temp_probe(struct i2c_client *client) {
    struct my_temp_data *data;
    struct device *hwmon_dev;
    
    data = devm_kzalloc(&client->dev, sizeof(*data), GFP_KERNEL);
    if (!data)
        return -ENOMEM;
    
    data->client = client;
    mutex_init(&data->lock);
    
    /* Verify device responds */
    int ret = i2c_smbus_read_byte(client);
    if (ret < 0) {
        dev_err(&client->dev, "Device not responding\n");
        return ret;
    }
    
    hwmon_dev = devm_hwmon_device_register_with_groups(
        &client->dev, "my_temp", data, my_temp_groups);
    
    return PTR_ERR_OR_ZERO(hwmon_dev);
}

static const struct of_device_id my_temp_of_ids[] = {
    { .compatible = "lab,my-temp-sensor" },
    { }
};
MODULE_DEVICE_TABLE(of, my_temp_of_ids);

static struct i2c_driver my_temp_driver = {
    .driver = {
        .name = "my-temp-sensor",
        .of_match_table = my_temp_of_ids,
    },
    .probe = my_temp_probe,
};
module_i2c_driver(my_temp_driver);

MODULE_LICENSE("GPL");
MODULE_DESCRIPTION("Lab I2C temperature sensor driver");
```

### Step 3: Build and Test
```bash
# Build (out-of-tree)
make -C /lib/modules/$(uname -r)/build M=$PWD modules

# Load
insmod my_temp.ko

# Verify
dmesg | grep my_temp
ls /sys/class/hwmon/hwmonN/
cat /sys/class/hwmon/hwmonN/temp
```

---

# LAB 7: I2C MUX AND ADDRESS CONFLICT RESOLUTION (ADVANCED)

## Objective
Use PCA9548A I2C multiplexer to connect two devices with the same address.

## Hardware
- PCA9548A at 0x70
- Two MPU6050 sensors (both at 0x68) — one on CH0, one on CH1

## Steps

### Step 1: Scan Before Mux Select
```bash
i2cdetect -y 1
# Shows: 0x70 (mux only)
```

### Step 2: Select Channel 0
```bash
# Write 0x01 to mux (channel 0 selected)
i2cset -y 1 0x70 0x01

# Now scan — should see 0x68 (sensor on CH0) + 0x70
i2cdetect -y 1
```

### Step 3: Read Sensor on CH0
```bash
i2cget -y 1 0x68 0x75  # WHO_AM_I → 0x68
```

### Step 4: Switch to Channel 1
```bash
i2cset -y 1 0x70 0x02  # Channel 1

i2cget -y 1 0x68 0x75  # Same address, different physical sensor!
```

### Step 5: Linux Device Tree with Mux
```dts
&i2c1 {
    status = "okay";
    clock-frequency = <400000>;
    
    i2c-mux@70 {
        compatible = "nxp,pca9548";
        reg = <0x70>;
        #address-cells = <1>;
        #size-cells = <0>;
        
        i2c@0 {  /* Channel 0 */
            reg = <0>;
            #address-cells = <1>;
            #size-cells = <0>;
            
            imu0: mpu6050@68 {
                compatible = "invensense,mpu6050";
                reg = <0x68>;
            };
        };
        
        i2c@1 {  /* Channel 1 */
            reg = <1>;
            #address-cells = <1>;
            #size-cells = <0>;
            
            imu1: mpu6050@68 {
                compatible = "invensense,mpu6050";
                reg = <0x68>;
            };
        };
    };
};
```

### Step 6: Verify in Linux
```bash
# Mux creates virtual buses
ls /dev/i2c-*
# i2c-1 (parent), i2c-2 (ch0), i2c-3 (ch1)

# Scan each virtual bus
i2cdetect -y 2  # Shows 0x68 (sensor on CH0)
i2cdetect -y 3  # Shows 0x68 (sensor on CH1)
```

---

# LAB 8: LOGIC ANALYZER CAPTURE & DECODE (INTERMEDIATE-ADVANCED)

## Objective
Capture I2C waveforms and decode protocol using logic analyzer.

## Tools
- Saleae Logic / PulseView (sigrok) / any logic analyzer
- Connect CH0 = SCL, CH1 = SDA

## Steps

### Step 1: Hardware Setup
```
Logic Analyzer:
  CH0 → SCL (also connect GND!)
  CH1 → SDA
  
Trigger: Falling edge on SDA while SCL HIGH (START condition)
Sample rate: ≥10× clock frequency (4 MHz for 400 kHz I2C)
```

### Step 2: Capture Write Transaction
```bash
# Generate traffic
i2cset -y 1 0x68 0x6B 0x00  # Wake MPU6050
```

### Step 3: Decode in PulseView
```
Add Protocol Decoder: I2C
  SCL → CH0
  SDA → CH1
  
Expected decode:
  [S] [0x68 W] [ACK] [0x6B] [ACK] [0x00] [ACK] [P]
```

### Step 4: Analyze Timing
Measure on scope/analyzer:
- [ ] Rise time SDA/SCL: Should be <300ns (fast mode)
- [ ] SCL frequency: Should be ~400 kHz
- [ ] Setup time (SDA change to SCL rise): >100ns
- [ ] Hold time (SCL fall to SDA change): >0

### Step 5: Capture Error Condition
```bash
# Try to read from non-existent device
i2cget -y 1 0x77 0x00  # Likely NACK

# In analyzer: See [S] [0x77 R] [NACK] [P]
```

### Step 6: Verify Clock Stretching
```
Look for SCL staying LOW longer than expected between bytes.
Master releases SCL → but SCL stays LOW → slave stretching!
Measure stretch duration — compare to device datasheet.
```

---

# DEBUG SCENARIOS

---

## DEBUG 1: "SDA Line Stuck LOW — Bus Completely Dead"

### Symptoms
- `i2cdetect` returns error or times out
- Multimeter: SDA = 0V, SCL = 3.3V (correct)
- No I2C communication possible

### Investigation
```bash
# Check if Linux can see the bus
dmesg | grep i2c
# "i2c i2c-1: sendbytes: NAK bailout"
# "i2c-bcm2835: bus stuck, attempting recovery"

# Check GPIO state
cat /sys/kernel/debug/gpio | grep -i i2c
# SDA should be hi (1), if lo (0) → stuck
```

### Root Cause Analysis
1. Check which device pulls SDA: Remove devices one at a time
2. If SDA releases when a device is removed → THAT device is the culprit
3. Most common: Slave lost sync (master glitch reset mid-transaction)

### Fix
```bash
# Method 1: Linux bus recovery (if driver supports it)
# Some drivers auto-recover. Check dmesg.

# Method 2: Manual GPIO bit-bang recovery
# Export SCL as GPIO, toggle 9 times
echo 5 > /sys/class/gpio/export
echo out > /sys/class/gpio/gpio5/direction
for i in $(seq 1 9); do
    echo 0 > /sys/class/gpio/gpio5/value; sleep 0.001
    echo 1 > /sys/class/gpio/gpio5/value; sleep 0.001
done
echo 5 > /sys/class/gpio/unexport

# Method 3: Power cycle the stuck device (if possible)
```

### Prevention
- Implement bus recovery in your I2C adapter driver
- Add watchdog timer on I2C transactions
- Use SMBus timeout (35ms) to prevent indefinite hangs
- Consider hardware reset GPIO to critical slaves

---

## DEBUG 2: "NACK on Address — Device Not Responding"

### Symptoms
```bash
$ i2cget -y 1 0x68 0x75
Error: Read failed
```
- dmesg: "i2c i2c-1: NAK"

### Systematic Diagnosis

```bash
# Step 1: Is ANYTHING on the bus?
i2cdetect -y 1
# If empty → hardware issue (pull-ups, power, wiring)

# Step 2: Verify power to device
# Measure VDD at device with multimeter

# Step 3: Verify address
# Check datasheet — is it 7-bit 0x68 or 8-bit 0xD0?
# Some datasheets list 8-bit address!

# Step 4: Check address pins
# If device has A0/A1/A2 pins, verify their state
# Floating pins → undefined address!

# Step 5: Try alternate addresses
for addr in 0x68 0x69 0xD0 0xD1; do
    i2cget -y 1 $addr 0x75 2>/dev/null && echo "Found at $addr"
done

# Step 6: Verify bus with scope
# Is START condition generated?
# Does device see the clock?
```

### Common Causes & Fixes
| Cause | Fix |
|-------|-----|
| Wrong address (7-bit vs 8-bit confusion) | Use 7-bit for Linux tools |
| Device in reset/sleep | Pull reset pin HIGH, send wake command |
| Address pin floating | Tie to VDD or GND with 10K resistor |
| Wrong I2C bus | Check DT, verify bus number |
| Pull-ups too weak | Replace with 4.7KΩ external |
| Solder joint issue | Reflow / inspect under microscope |

---

## DEBUG 3: "Works at 100 kHz, Fails at 400 kHz"

### Symptoms
- Standard mode (100 kHz): 100% reliable
- Fast mode (400 kHz): Intermittent NACK or data corruption

### Diagnosis
```
Scope measurements at 400 kHz:
1. Measure rise time on SDA and SCL
   - Spec: ≤300 ns for Fast mode
   - If measured: 450 ns → TOO SLOW!
   
2. Check pull-up value
   - If 10KΩ + 200pF bus → t_rise = 0.8473 × 10K × 200pF = 1.7µs
   - WAY too slow for 400 kHz!
   
3. Calculate correct pull-up:
   Rp = 300ns / (0.8473 × 200pF) = 1.77 KΩ → use 2.2 KΩ
```

### Fix
1. Replace pull-ups: 10KΩ → 2.2KΩ
2. Reduce bus capacitance: shorter traces, fewer devices
3. If still failing: check if ALL devices support Fast mode (one slow device = bus fails)
4. Verify device datasheet confirms 400 kHz support

---

## DEBUG 4: "Random Data Corruption Every ~1000 Reads"

### Symptoms
- 99.9% of reads correct
- Occasional wrong values (bit flip pattern)
- No NACK — device responds correctly

### Diagnosis
```bash
# Log reads and look for pattern
for i in $(seq 1 10000); do
    val=$(i2cget -y 1 0x68 0x75)
    if [ "$val" != "0x68" ]; then
        echo "ERROR at iteration $i: got $val"
    fi
done
```

### Analysis with Logic Analyzer
- Capture at error time → look for:
  - Glitch on SCL (noise-induced extra clock → bit shift)
  - SDA noise during SCL HIGH (wrong bit sampled)
  - Marginal rise time (SDA barely reaching VIH)

### Root Causes
1. **EMI**: Motor/relay switching injects noise
2. **Marginal rise time**: Works 99.9% but noise pushes signal below threshold occasionally
3. **Power supply ripple**: Causes threshold variation
4. **Crosstalk**: Adjacent signal coupling into SDA/SCL

### Fixes
```
1. Lower pull-up resistance (2.2KΩ instead of 4.7KΩ)
2. Add 100pF filter cap on SDA/SCL (careful: adds to bus cap!)
3. Series resistor (33Ω) to reduce edge rate and EMI pickup
4. Route SDA/SCL away from noisy signals
5. Add ferrite bead for HF noise rejection
6. Improve grounding (star ground, ground plane)
```

---

## DEBUG 5: "I2C Hangs After 2 Hours Continuous Operation"

### Symptoms
- Runs fine for hours, then all I2C transactions timeout
- Only power cycle recovers
- No obvious trigger

### Diagnosis
```bash
# Monitor bus health over time
while true; do
    result=$(i2cget -y 1 0x68 0x75 2>&1)
    if echo "$result" | grep -q "Error"; then
        echo "$(date): I2C FAILED"
        # Check SDA/SCL state
        cat /sys/kernel/debug/gpio | grep i2c
        break
    fi
    sleep 1
done
```

### Root Cause Investigation
1. **Thermal**: Device behavior changes at temperature
2. **Slow memory leak**: Driver allocates without freeing → eventually fails
3. **Race condition**: Rare timing window in multi-threaded access
4. **Clock glitch accumulation**: Noise causes occasional extra clock → eventually slave state machine gets stuck
5. **Power supply aging**: Marginal regulator degrades over hours under load

### Fix
```c
// Implement watchdog + automatic recovery
void i2c_watchdog_task(void) {
    while(1) {
        // Periodic health check
        int ret = i2c_smbus_read_byte(test_client);
        if (ret < 0) {
            log_error("I2C bus stuck! Recovering...");
            i2c_recover_bus(adapter);
            // If still stuck after recovery:
            gpio_reset_slave_devices();
        }
        sleep(10);  // Check every 10 seconds
    }
}
```

---

## DEBUG 6: "Address Conflict — Two Devices Fighting"

### Symptoms
- `i2cdetect` shows device at 0x68
- Reads return inconsistent data (mixing two devices)
- Sometimes correct, sometimes garbage

### Diagnosis
```bash
# Read WHO_AM_I multiple times
for i in $(seq 1 20); do
    i2cget -y 1 0x68 0x75
done
# If you see DIFFERENT values → two devices responding!
```

### Root Cause
Two devices with address 0x68 on same bus (e.g., MPU6050 + DS3231 RTC — both 0x68!)

### Fix
```bash
# Option 1: Change address pin on one device
# MPU6050: Pull AD0 HIGH → address becomes 0x69

# Option 2: Use I2C mux
# Route each device to different mux channel

# Option 3: Use different I2C bus
# Move one device to another I2C controller
```

---

## DEBUG 7: "I2C Works on Bench but Fails in Vehicle"

### Symptoms
- Perfect on development board
- Intermittent failures in actual vehicle ECU
- Fails during engine cranking or CAN bus heavy traffic

### Diagnosis Checklist
```
□ EMI: Vehicle electrical noise (ignition, motors, CAN)
  → Test with EMC probe on I2C lines
  
□ Temperature: ECU operating at 85°C+ near engine
  → Test in thermal chamber

□ Voltage: Battery sag during cranking (12V→6V)
  → Monitor 3.3V rail during cranking

□ Ground: Different ground potential between boards
  → Measure ground impedance

□ Vibration: Connector intermittent
  → Tap test while monitoring I2C

□ Cable length: Vehicle harness >> bench setup
  → Measure bus capacitance with scope/LCR meter
```

### Fixes
```
1. Add TVS diodes (PESD1I2C) on SDA/SCL near ECU connector
2. Add series ferrite beads (600Ω @ 100MHz) on I2C lines
3. Reduce clock to 100 kHz (more noise margin)
4. Add 100pF filter caps (if within bus cap budget)
5. Use bus buffer (P82B715) if cable is long
6. Improve ground (lower impedance, star topology)
7. Add brownout detection + bus recovery after power dip
8. Use shielded twisted pair for I2C cables
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
