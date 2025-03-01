# esphome-pace-bms

![GitHub actions](https://github.com/syssi/esphome-pace-bms/actions/workflows/ci.yaml/badge.svg)
![GitHub stars](https://img.shields.io/github/stars/syssi/esphome-pace-bms)
![GitHub forks](https://img.shields.io/github/forks/syssi/esphome-pace-bms)
![GitHub watchers](https://img.shields.io/github/watchers/syssi/esphome-pace-bms)
[!["Buy Me A Coffee"](https://img.shields.io/badge/buy%20me%20a%20coffee-donate-yellow.svg)](https://www.buymeacoffee.com/syssi)

ESPHome configuration to monitor and control a PACE Battery Management System (PACE-BMS) via RS485 (Modbus)

This project implements the `PACE_MODBUS` protocol. Please use the RS485 port next to the CAN bus port and enable the protocol using the `PbmsTools.exe` (System Config -> Inverter protocol -> RS485 Protocol).

## Supported devices

* Katbatt 6.4kWh LiFePO4 Stackable Battery, PACE BMS P16S200A (`P16S200A-21473-1.03A310771030800025A`), using RS485 protocol `PACE_MODBUS`
* PACE BMS P16S200A with firmware version 3 (`P16S200A-21382-3.00T213822131800670H`), using RS485 protocol `PACE_MODBUS` ([#20](https://github.com/syssi/esphome-pace-bms/issues/20))
* Gobel Power GP-SR1-LF280-RN150 51.2V 280Ah, PACE BMS S16A150 (`JLD-BMS-S16A150`), using RS485 protocol `ModbusC1636`
* Joyvoit Suns Energy Battery JVBW5KW, PACE BMS P16S100A (`P16S100A-21468-1.00`), using RS485 protocol `PACE_MODBUS` ([#19](https://github.com/syssi/esphome-pace-bms/issues/19))
* Orient Power Wall Mounted Battery 48V100AH, PACE BMS P16S100A-21236-2.01 ([#31](https://github.com/syssi/esphome-pace-bms/discussions/31))
* SOK 100Ah 48V Server Rack Berry, PACE BMS P16S100A (`P16S100A-1B470-4.07`), using RS485 protocol `PACE_MODBUS` ([#35](https://github.com/syssi/esphome-pace-bms/issues/35))
* SOK 100Ah 48V Server Rack Berry, PACE BMS P16S100A (`P16S100A-1B470-4.00`), using RS485 protocol `PACE_MODBUS` ([#38](https://github.com/syssi/esphome-pace-bms/discussions/38))
* MeritSun / i-finity LFP 200 - 48V (Modbus must be enabled by pbmstool.exe) ([#29](https://github.com/syssi/esphome-pace-bms/issues/29))
* Revov R100 51.2V 100Ah, PACE BMS P16S100A (`P16S100A-12720-4.05G`), using RS485 protocol `SRNE` ([#39](https://github.com/syssi/esphome-pace-bms/issues/39))
* NPP 51.2v 280AH FLCD-30048 NSFG280F10/DS, PACE BMS P16S200A (`P16S200A-21452-2.02`) using RS485 protocol `PACE_MODBUS` ([#60](https://github.com/syssi/esphome-pace-bms/discussions/60))
* PowMr 51.2V 100Ah, PACE BMS P16S150A (`P16S150A-41034-1.00`), using RS485 protocol `PACE_MODBUS` ([#65](https://github.com/syssi/esphome-pace-bms/issues/65))

## Untested devices

* Jakiper/BSLBATT 100Ah, PACE BMS P16S100A
* LIONTRON LiFePO4 LX48-100, 48V 100Ah
* Shenzen Delong 24V 100Ah, PACE BMS P16S200A-PC1547
* Hubble Lithium (AM2, AM4)
* Revov R9
* Greenrich U-P5000

## Schematics

```
                  RS485                      UART
┌────────────┐              ┌──────────┐                ┌─────────┐
│            │              │          │<----- RX ----->│         │
│    PACE    │<-----B- ---->│  RS485   │<----- TX ----->│ ESP32/  │
│    BMS     │<---- A+ ---->│  to TTL  │<----- GND ---->│ ESP8266 │
│            │<--- GND ---->│  module  │<----- 3.3V --->│         │<-- VCC
│            │              │          │                │         │<-- GND
└────────────┘              └──────────┘                └─────────┘

```

Please make sure to power the RS485 module with 3.3V because it affects the TTL (transistor-transistor logic) voltage between RS485 module and ESP.

### RJ45 jack

|  Pin  | Purpose | RS485-to-TTL pin | Color T-568B |
|:-----:|:--------|:-----------------|--------------|
| **1** | **B-**  | **B-**           | Orange-White |
| **2** | **A+**  | **A+**           | Orange       |
| **3** | **GND** | **GND**          | Green-White  |
|   4   | NC      |                  |              |
|   5   | NC      |                  |              |
|   6   | GND     |                  |              |
|   7   | A+      |                  |              |
|   8   | B-      |                  |              |

Please be aware of the different RJ45 pinout colors ([T-568A vs. T-568B](images/rj45-colors-t568a-vs-t568.png)).

### BMS address

| Module address | BMS Module ID | Dip1 | Dip2 | Dip3 | Dip4 |
|:---------------|:--------------|-----:|-----:|-----:|-----:|
| `0x00`         | 0             |  off |  off |  off |  off |
| `0x01`         | 1             |   on |  off |  off |  off |
| `0x02`         | 2             |  off |   on |  off |  off |
| `0x03`         | 3             |   on |   on |  off |  off |
| `0x04`         | 4             |  off |  off |   on |  off |
| `0x05`         | 5             |   on |  off |   on |  off |
| `0x06`         | 6             |  off |   on |   on |  off |
| `0x07`         | 7             |   on |   on |   on |  off |
| `0x08`         | 8             |  off |  off |  off |   on |
| `0x09`         | 9             |   on |  off |  off |   on |
| `0x0A`         | 10            |  off |   on |  off |   on |
| `0x0B`         | 11            |   on |   on |  off |   on |
| `0x0C`         | 12            |  off |  off |   on |   on |
| `0x0D`         | 13            |   on |  off |   on |   on |
| `0x0E`         | 14            |  off |   on |   on |   on |
| `0x0F`         | 15            |   on |   on |   on |   on |


## Requirements

* [ESPHome 2024.6.0 or higher](https://github.com/esphome/esphome/releases).
* Generic ESP32 or ESP8266 board

## Installation

Use the `esp32-example.yaml` as proof of concept:

```bash
# Install esphome
pip3 install esphome

# Clone this project
git clone https://github.com/syssi/esphome-pace-bms.git
cd esphome-pace-bms

# Create a secrets.yaml containing some setup specific secrets
cat > secrets.yaml <<EOF
wifi_ssid: MY_WIFI_SSID
wifi_password: MY_WIFI_PASSWORD

mqtt_host: MY_MQTT_HOST
mqtt_username: MY_MQTT_USERNAME
mqtt_password: MY_MQTT_PASSWORD
EOF

# Validate the configuration, create a binary, upload it, and start logs
# If you use a esp8266 run the esp8266-examle.yaml
esphome run esp32-example.yaml
```

## Example response all sensors enabled

```
TBD.
```

## Known issues and limitations

None.

## Protocol

See [docs/PACE-BMS-Modbus-Protocol-for-RS485-V1.3-20170627.pdf](docs/PACE-BMS-Modbus-Protocol-for-RS485-V1.3-20170627.pdf).

The protocol is Modbus RTU via RS485.

| Addr | Description                            | Len | R/W |   Type | Unit   |                                   |
|-----:|:---------------------------------------|----:|:----|-------:|:-------|:----------------------------------|
|    0 | Current                                |   2 | R   |  int16 | 10mA   |                                   |
|    1 | Voltage of pack                        |   2 | R   | uint16 | 10mV   |                                   |
|    2 | State of charge                        |   2 | R   |  uint8 | %      | 0~100%                            |
|    3 | SOH                                    |   2 | R   |  uint8 | %      | 0~100%                            |
|    4 | Remain capacity                        |   2 | R   | uint16 | 10mAH  |                                   |
|    5 | Full capacity                          |   2 | R   | uint16 | 10mAH  |                                   |
|    6 | Design capacity                        |   2 | R   | uint16 | 10mAH  |                                   |
|    7 | Charging cycles count                  |   2 | R   | uint16 |        |                                   |
|    9 | Warning flag                           |   2 | R   | uint16 | Hex    | See ^1                            |
|   10 | Protection flag                        |   2 | R   | uint16 | Hex    | See ^2                            |
|   11 | Status/Fault flag                      |   2 | R   | uint16 | Hex    | See ^3                            |
|   12 | Balance status                         |   2 | R   | uint16 | Hex    |                                   |
|   15 | Cell voltage 1                         |   2 | R   | uint16 | mV     |                                   |
|   16 | Cell voltage 2                         |   2 | R   | uint16 | mV     |                                   |
|   17 | Cell voltage 3                         |   2 | R   | uint16 | mV     |                                   |
|   18 | Cell voltage 4                         |   2 | R   | uint16 | mV     |                                   |
|   19 | Cell voltage 5                         |   2 | R   | uint16 | mV     |                                   |
|   20 | Cell voltage 6                         |   2 | R   | uint16 | mV     |                                   |
|   21 | Cell voltage 7                         |   2 | R   | uint16 | mV     |                                   |
|   22 | Cell voltage 8                         |   2 | R   | uint16 | mV     |                                   |
|   23 | Cell voltage 9                         |   2 | R   | uint16 | mV     |                                   |
|   24 | Cell voltage 10                        |   2 | R   | uint16 | mV     |                                   |
|   25 | Cell voltage 11                        |   2 | R   | uint16 | mV     |                                   |
|   26 | Cell voltage 12                        |   2 | R   | uint16 | mV     |                                   |
|   27 | Cell voltage 13                        |   2 | R   | uint16 | mV     |                                   |
|   28 | Cell voltage 14                        |   2 | R   | uint16 | mV     |                                   |
|   29 | Cell voltage 15                        |   2 | R   | uint16 | mV     |                                   |
|   30 | Cell voltage 16                        |   2 | R   | uint16 | mV     |                                   |
|   31 | Battery temperature 1                  |   2 | R   |  int16 | 0.1 ℃  |                                   |
|   32 | Battery temperature 2                  |   2 | R   |  int16 | 0.1 ℃  |                                   |
|   33 | Battery temperature 3                  |   2 | R   |  int16 | 0.1 ℃  |                                   |
|   34 | Battery temperature 4                  |   2 | R   |  int16 | 0.1 ℃  |                                   |
|   35 | MOSFET temperature                     |   2 | R   |  int16 | 0.1 ℃  | or invalid                        |
|   36 | Environment temperature                |   2 | R   |  int16 | 0.1 ℃  | or invalid                        |
|   60 | Pack OV alarm                          |   2 | RW  | uint16 | mV     |                                   |
|   61 | Pack OV protection                     |   2 | RW  | uint16 | mV     |                                   |
|   62 | Pack OV release protection             |   2 | RW  | uint16 | mV     |                                   |
|   63 | Pack OV protection delay time          |   2 | RW  |  uint8 | 0.1S   | 1~255                             |
|   64 | Cell OV alarm                          |   2 | RW  | uint16 | mV     |                                   |
|   65 | Cell OV protection                     |   2 | RW  | uint16 | mV     |                                   |
|   66 | Cell OV release protection             |   2 | RW  | uint16 | mV     |                                   |
|   67 | Cell OV protection delay time          |   2 | RW  |  uint8 | 0.1S   | 1~255                             |
|   68 | Pack UV alarm                          |   2 | RW  | uint16 | mV     |                                   |
|   69 | Pack UV protection                     |   2 | RW  | uint16 | mV     |                                   |
|   70 | Pack UV release protection             |   2 | RW  | uint16 | mV     |                                   |
|   71 | Pack UV protection delay time          |   2 | RW  |  uint8 | 0.1S   | 1~255                             |
|   72 | Cell UV alarm                          |   2 | RW  | uint16 | mV     |                                   |
|   73 | Cell UV protection                     |   2 | RW  | uint16 | mV     |                                   |
|   74 | Cell UV release protection             |   2 | RW  | uint16 | mV     |                                   |
|   75 | Cell UV protection delay time          |   2 | RW  |  uint8 | 0.1S   | 1~255                             |
|   76 | Charging OC alarm                      |   2 | RW  | uint16 | A      |                                   |
|   77 | Charging OC protection                 |   2 | RW  | uint16 | A      |                                   |
|   78 | Charging OC protection delay time      |   2 | RW  |  uint8 | 0.1S   | 1~255                             |
|   79 | Discharging OC alarm                   |   2 | RW  | uint16 | A      |                                   |
|   80 | Discharging OC protection              |   2 | RW  | uint16 | A      |                                   |
|   81 | Discharging OC protection delay time   |   2 | RW  |  uint8 | 0.1S   | 1~255                             |
|   82 | Discharging OC-2 protection            |   2 | RW  | uint16 | A      |                                   |
|   83 | Discharging OC-2 protection delay time |   2 | RW  |  uint8 | 0.025S | 1~255                             |
|   84 | Charging OT alarm                      |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   85 | Charging OT protection                 |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   86 | Charging OT release protection         |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   87 | Discharging OT alarm                   |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   88 | Discharging OT protection              |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   89 | Discharging OT release                 |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   90 | Charging UT alarm                      |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   91 | Charging UT protection                 |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   92 | Charging UT release protection         |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   93 | Discharging UT alarm                   |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   94 | Discharging UT protection              |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   95 | Discharging UT release protection      |   2 | RW  |  int16 | 0.1 ℃  |                                   |
|   96 | MOSFET OT alarm                        |   2 | RW  |  int16 | 0.1 ℃  | or invalid parameters in BMS-4820 |
|   97 | MOSFET OT protection                   |   2 | RW  |  int16 | 0.1 ℃  | or invalid parameters in BMS-4820 |
|   98 | MOSFET OT release protection           |   2 | RW  |  int16 | 0.1 ℃  | or invalid parameters in BMS-4820 |
|   99 | Environment OT alarm                   |   2 | RW  |  int16 | 0.1 ℃  | or invalid parameters in BMS-4820 |
|  100 | Environment OT protection              |   2 | RW  |  int16 | 0.1 ℃  | or invalid parameters in BMS-4820 |
|  101 | Environment OT release protection      |   2 | RW  |  int16 | 0.1 ℃  | or invalid parameters in BMS-4820 |
|  102 | Environment UT alarm                   |   2 | RW  |  int16 | 0.1 ℃  | or invalid parameters in BMS-4820 |
|  103 | Environment UT protection              |   2 | RW  |  int16 | 0.1 ℃  | or invalid parameters in BMS-4820 |
|  104 | Environment UT release protection      |   2 | RW  |  int16 | 0.1 ℃  | or invalid parameters in BMS-4820 |
|  105 | Balance start cell voltage             |   2 | RW  | uint16 | mV     |                                   |
|  106 | Balance start delta voltage            |   2 | RW  | uint16 | mV     |                                   |
|  107 | Pack full-charge voltage               |   2 | RW  | uint16 | mV     |                                   |
|  108 | Pack full-charge current               |   2 | RW  | uint16 | mA     |                                   |
|  109 | Cell sleep voltage                     |   2 | RW  | uint16 | mV     |                                   |
|  110 | Cell sleep delay time                  |   2 | RW  | uint16 | min    |                                   |
|  111 | Short circuit protect delay time       |   2 | RW  |  uint8 | 25uS   | Max 500uS                         |
|  112 | SOC alarm threshold                    |   2 | RW  |  uint8 | %      | 0~100%                            |
|  113 | Charging OC-2 protection               |   2 | RW  | uint16 | A      |                                   |
|  114 | Charging OC-2 protection delay time    |   2 | RW  |  uint8 | 0.025S | 1~255                             |
|  150 | Version information                    |  20 | R   | uint16 | ASCII  |                                   |
|  160 | Model SN                               |  20 | RW  | uint16 | ASCII  | BMS Manufacturer                  |
|  170 | PACK SN                                |  20 | RW  | uint16 | ASCII  | PACK Manufacturer                 |

## Known issues

None.

## Debugging

If this example doesn't work out of the box for your device please update your configuration to enable the debug output of the UART component and increase the log level to the see outgoing and incoming serial traffic:

```
logger:
  level: DEBUG
  # Don't write log messages to UART0 (GPIO1/GPIO3) if the BMS is connected to GPIO1/GPIO3
  baud_rate: 0

uart:
  - id: uart_0
    baud_rate: 9600
    tx_pin: ${tx_pin}
    rx_pin: ${rx_pin}
    debug:
      direction: BOTH
      dummy_receiver: false
```

## References

* https://www.akkudoktor.net/forum/bms-batterie-management-monitoring-system/pace-bms-informationssammlung/
* https://docs.google.com/document/d/1JqBizjyOrAge02pO-S6asDoAJgEdHV0Ujbs3BCCvXLo/edit
* https://powerforum.co.za/topic/13982-bms-pace-addon-for-home-assistant-as-used-by-hubbles-am-2-and-am-4/ (different protocol!)
* https://github.com/fancyui/Gobel-Power-RN-BMS-RS485-ModBus (different protocol!)
* https://github.com/Tertiush/bmspace (different protocol!)
