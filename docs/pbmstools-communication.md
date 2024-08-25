# PBmsTools 2.4

## Realtime monitoring

### Get Analog Information

```
>>> ~25014642E00201FD30\r
<<< ~25014600F07A0001100CC70CC80CC70CC70CC70CC50CC60CC70CC70CC60CC70CC60CC60CC70CC60CC7060B9B0B990B990B990BB30BBCFF1FCCCD12D303286A008C2710E1E4\r
                 00001122222222222222222222222222222222222222222222222222222222222222223344444444444444444444444455556666777788999900001111

0 Responding Bus Id
1 Cell Count (this example has 16 cells)
2 Cell Voltage (repeated Cell Count times) - stored as v * 1000, so 56 is 56000
3 Temperature Count (this example has 6 temperatures)
4 Temperature (repeated Temperature Count times) - stored as (value * 10) + 2730, to decode (value - 2730) / 10.0 = value
5 Current - stored as value * 100
6 Total Voltage - stored as value * 1000
7 Remaining Capacity - stored as value * 100
8 [Constant] = 03
9 Full Capacity - stored as value * 100
0 Cycle Count
1 Design Capacity - stored as value * 100
```

### Get Status

```
>>> ~25014644E00201FD2E\r
<<< ~25014600004C000110000000000000000000000000000000000600000000000000000000000E000000000000EF3A\r
                 0000112222222222222222222222222222222233444444444444556677889900112233445566

0 Responding Bus Id
1 Cell Count (this example has 16 cells)
2 Cell Warning (repeated Cell Count times) see: DecodeWarningValue
3 Temperature Count (this example has 6 temperatures)
4 Temperature Warning (repeated Temperature Count times) see: DecodeWarningValue
5 Charge Current Warning see: DecodeWarningValue
6 Total Voltage Warning see: DecodeWarningValue
7 Discharge Current Warning see: DecodeWarningValue
8 Protection Status 1 see: DecodeProtectionStatus1Value
9 Protection Status 2 see: DecodeProtectionStatus2Value
0 System Status see: DecodeSystemStatusValue
1 Configuration Status see: DecodeConfigurationStatusValue
2 Fault Status see: DecodeFaultStatusValue
3 Balance Status (high byte) set bits indicate those cells are balancing
4 Balance Status (low byte) set bits indicate those cells are balancing
5 Warning Status 1 see: DecodeWarningStatus1Value
6 Warning Status 2 see: DecodeWarningStatus2Value
```

### Get Hardware Version

```
>>> ~250146C10000FD9A\r
<<< ~25014600602850313653313030412D313831322D312E30302000F58E\r
                 1111111111111111111111111111111111111111

1 Hardware Version string (may be ' ' padded at the end), the length header value will tell you how long it is, should be 20 'actual character' bytes (40 ASCII hex chars)
```

### Get Serial Number

```
>>> ~250146C20000FD99\r
<<< ~25014600B05031383132313031333830333039442020202020202020202020202020202020202020202020202020EE0F\r
                 11111111111111111111111111111111111111111111111111111111111111111111111111111111

1 Serial Number string (may be ' ' padded at the end), the length header value will tell you how long it is, should be 20 or 40 'actual character' bytes (40 or 80 ASCII hex chars)
```

### Switch control section

#### Sound Alarm

```
# on
>>> ~25004699E0020DFD12\r
<<< ~25004600C0040D01FCC3\r

# off
>>> ~25004699E0020CFD13\r
<<< ~25004600C0040C00FCC5\r
```

#### LED Alarm Switch

```
# on
>>> ~25004699E00206FD20\r
<<< ~25004600C0040602FCD0\r

# off
>>> ~25004699E00207FD1F\r
<<< ~25004600C0040722FCCD\r
```

#### Charge Current Limiter Switch

```
# on
>>> ~25004699E0020AFD15\r
<<< ~25004600C0040A22FCC3\r

# off
>>> ~25004699E0020BFD14\r
<<< ~25004600C0040B32FCC1\r
```

#### Charge MOSFET Switch

```
# on
>>> ~2500469AE00200FD1E\r
<<< ~25004600E00226FD30\r

# off
>>> ~2500469AE00201FD1D\r
<<< ~25004600E00224FD32\r
```

#### Discharge MOSFET Switch

```
# on
>>> ~2500469BE00200FD1D\r
<<< ~25004600E00204FD34\r

# off
>>>  ~2500469BE00201FD1C\r
<<<  ~25004609E00204FD2B\r
```

#### Reboot (labeled as "Shutdown")

```
>>> ~2500469CE00201FD1B\r
<<< ~250046000000FDAF\r
```

## "Memory information" tab

### Read Log History

This appears to be a "history" table. I'm not sure what prompts the battery to create a "history record" entry - the number of entries per day varies from 2-6 at a glance and there is sometimes a week or two missing between records
My battery contained 400 records (and it's been on for over a year continuous, so I believe this is the limit)
The last 4 (ASCII hex digits) request payload digits are a "count up" starting at 0000 and ending at 0x0190 = 400 dec, record index is zero-based with newest first (lowest payload value)
I haven't decoded the response yet, but it contains

Date/Time<br>
Pack Amps (-in/out)<br>
Pack Voltage<br>
Remaing Capacity (Ah)<br>
Full Capacity (Ah)<br>
MaxVolt (cell) (mV)<br>
MinVolt (cell) (mV)<br>
Alarm Type<br>
Protect Type<br>
Fault Type<br>
Cell Voltage 1-16<br>
Temperatures 1-6<br>

```
>>> ~250046A1C004018FFCA7\r
<<< ~25004600709018021D020038100D970D990D9A0D990D990D970D990D980D990D980D800D980D980D990D980D98060B740B750B770B760B710B79FF6ED9D7286A286A0000000000060043FFFFFFFFDDE3\r
            the values in this response:
                2024-2-29 2:00:56 - 1.460
                55.767
                103.460
                103.460
                3482
                3456
                3479    3481    3482    3481    3481    3479    3481    3480    3481    3480    3456    3480    3480    3481    3480    3480
                20.2    20.3    20.5    20.4    19.9    20.7

<<< ~250046000000FDAF\r
# this means "no more records available"
```

## "Parameter Setting" tab

"Parameter Setting" tab of PBmsTools 2.4 with DIP set to address 00. PBmsTools 2.4 is broken and can't address packs other than 00 for configuration.

### Cell Over Voltage Configuration

```
>>> ~250046D10000FD9A\r
<<< ~25004600F010010E100E740D340AFA35\r
                 ??11112222333344
>>> ~250046D0F010010E100E740D340AFA21\r
<<< ~250046000000FDAF\r

1 Cell OV Alarm (V): 3.60 - stored as v * 100, so 3.6 is 3600 - valid range reported by PBmsTools as 2.5-4.5 in steps of 0.01
2 Cell OV Protect (V): 3.70 - stored as v * 100, so 3.7 is 3700 - valid range reported by PBmsTools as 2.5-4.5 in steps of 0.01
3 Cell OVP Release (V): 3.38  - stored as v * 100, so 3.38 is 3380 - valid range reported by PBmsTools as 2.5-4.5 in steps of 0.01
4 Cell OVP Delay Time (ms): 1000 - stored in 100ms steps, so 1000ms is 10 - valid range reported by PBmsTools as 1000 to 5000 in steps of 500
```

### Pack Over Voltage Configuration

```
>>> ~250046D50000FD96\r
<<< ~25004600F01001E100E740D2F00AFA24\r
                 ??11112222333344
>>> ~250046D4F01001E10AE740D2F00AF9FB\r
<<< ~250046000000FDAF\r

1 Pack OV Alarm (V): 57.6 - stored as v * 100, so 57.6 is 57600 - valid range reported by PBmsTools as 20-65 in steps of 0.01
2 Pack OV Protect (V): 59.2 - stored as v * 100, so 59.2 is 59200 - valid range reported by PBmsTools as 20-65 in steps of 0.01
3 Pack OVP Release (V): 54.0 - stored as v * 100, so 54.0 is 54000 - valid range reported by PBmsTools as 20-65 in steps of 0.01
4 Pack OVP Delay Time (ms): 1000 - stored in 100ms steps, so 1000ms is 10 - valid range reported by PBmsTools as 1000 to 5000 in steps of 500
```

### Cell Under Voltage Configuration

```
>>> ~250046D30000FD98\r
<<< ~25004600F010010AF009C40B540AFA24\r
                 ??11112222333344
>>> ~250046D2F010010AF009C40B540AFA0E\r
<<< ~250046000000FDAF\r

1 Cell UV Alarm (V): 2.8 - stored as v * 100, so 2.8 is 2800 - valid range reported by PBmsTools as 2-3.5 in steps of 0.01
2 Cell UV Protect (V): 2.5 - stored as v * 100, so 2.5 is 2500 - valid range reported by PBmsTools as 2-3.5 in steps of 0.01
3 Cell UVP Release (V): 2.9 - stored as v * 100, so 2.9 is 2900 - valid range reported by PBmsTools as 2-3.5 in steps of 0.01
4 Cell UVP Delay Time (ms): 1000 - stored in 100ms steps, so 1000ms is 10 - valid range reported by PBmsTools as 1000 to 5000 in steps of 500
```


### Pack Under Voltage Configuration

```
>>> ~250046D70000FD94\r
<<< ~25004600F01001AF009C40B5400AFA24\r
                 ??11112222333344
>>> ~250046D6F01001AF009C40B5400AFA0A\r
<<< ~250046000000FDAF\r

1 Pack UV Alarm (V): 44.8 - stored as v * 100, so 44.8 is 44800 - valid range reported by PBmsTools as 15-50 in steps of 0.01
2 Pack UV Protect (V): 40.0 - stored as v * 100, so 40.0 is 40000 - valid range reported by PBmsTools as 15-50 in steps of 0.01
3 Pack UVP Release (V): 46.4 - stored as v * 100, so 46.4 is 46400 - valid range reported by PBmsTools as 15-50 in steps of 0.01
4 Pack UVP Delay Time (ms): 1000 - stored in 100ms steps, so 1000ms is 10 - valid range reported by PBmsTools as 1000 to 5000 in steps of 500
```

### Charge Over Current Configuration

```
>>> ~250046D90000FD92\r
<<< ~25004600400C010068006E0AFB1D\r
                 ??1111222233
>>> ~250046D8400C010068006E0AFB01\r
<<< ~250046000000FDAF\r

1 Charge OC Alarm (A): 104 - stored directly in amps - valid range reported by PBmsTools as 1-150
2 Charge OC Protect (A): 110 - stored directly in amps - valid range reported by PBmsTools as 1-150
3 Charge OCP Delay Time (ms): 1000 - stored in 100ms steps, so 1000ms is 10 - valid range reported by PBmsTools as 500 to 10000 in steps of 500
```

### Discharge SLOW Over Current Configuration

```
>>> ~250046DB0000FD89\r
<<< ~25004600400C01FF97FF920AFAD3\r
                 ??1111222233
>>> ~250046DA400C010069006E0AFAF7\r
<<< ~250046000000FDAF\r

1 Discharge OC Alarm (A): 105 - stored as negative two's complement in amps, -105 is FF97 - valid range reported by PBmsTools as 1-150
2 Discharge OC 1 Protect (A): 110 - stored as negative two's complement in amps, -110 is FF92 - valid range reported by PBmsTools as 1-150
3 Discharge OC 1 Delay Time (ms): 1000 - stored in 100ms steps, so 1000ms is 10 - valid range reported by PBmsTools as 500 to 10000 in steps of 500
********* important *********: this is returned as the negative two's complement, but is STORED (written back) as the normal positive value!
```

### Dicharge FAST Over Current Configuration

```
>>> ~250046E30000FD97\r
<<< ~25004600400C009604009604FB32\r
                 ??1122xxxxxx
>>> ~250046E2A006009604FC4E\r
<<< ~250046000000FDAF\r

1 Discharge OC 2 Protect: 150 - stored directly in amps - valid range reported by PBmsTools as 5-300 in steps of 5, but since this is an 8 bit store location, the actual max is 255????????
2 Discharge OC 2 Delay Time (ms): 100 - stored in 25ms steps, so 100 is 4 (4x25=100), 400 is 16 (16x25=400) - valid range reported by PBmsTools as 100-2000 in steps of 100
x = apparently, garbage written by the firmware - it's not included in the PBmsTools write
```

### Short Circuit Protection Configuration

```
>>> ~250046E50000FD95\r
<<< ~25004600E0020CFD25\r
                 11
>>> ~250046E4E0020CFD0C\r
<<< ~250046000000FDAF\r

1 Delay Time (us): 300 - stored as ,  is  - valid range reported by PBmsTools as as 100-500 in steps of 50
```

### Cell Balancing Configuration

```
>>> ~250046B60000FD97\r
<<< ~2500460080080D48001EFBE9\r
                 11112222
>>> ~250046B580080D48001EFBD2\r
<<< ~250046000000FDAF\r

1 Balance Threshold (vV): 3.4 - stored as v * 100, so 3.4 is 3400 - valid range reported by PBmsTools as 3.3-4.5 in steps of 0.01
2 Balance Delta Cell (mv): 30 - stored directly, so 30 is 30 - valid range reported by PBmsTools as 20-500 in steps of 5
```

### Sleep Configuration

```
>>> ~250046A00000FD9E\r
<<< ~2500460080080C1C0005FBF3\r
                 1111??22
>>> ~250046A880080C1C0005FBDA\r
<<< ~250046000000FDAF\r

1 Sleep v-cell: 3.1 - stored as v * 100, so 3.1 is 3100 - valid range reported by PBmsTools as 20-65 in steps of 0.01
2 Delay Time (minute): 5 - stored directly - valid range reported by PBmsTools as 1-120
```

### Full Charge and Charge Low

```
>>> ~250046AF0000FD88.
<<< ~25004600600ADAC007D005FB60\r
                 1111222233
>>> ~250046AE600ADAC007D005FB3A\r
<<< ~250046000000FDAF\r

1 Pack Full Charge Voltage: 56.0 - stored as v * 1000, so 56 is 56000 - valid range reported by PBmsTools as 20-65 in steps of 0.01
2 Pack Full Charge Current (ma): 2000 - stored directly in ma - valid range reported by PBmsTools as 500-5000 in steps of 500
3 State of Charge Low Alarm (%): 5 - stored directly - valid range reported by PBmsTools as 1-100
```

### Charge / Discharge Over Temperature Protection Configuration

```
>>> ~250046DD0000FD87\r
<<< ~25004600501A010CA80CD00C9E0CDA0D020CD0F7BE\r
                 ??111122223333444455556666
>>> ~250046DC501A010CA80CD00C9E0CDA0D020CD0F797\r
<<< ~250046000000FDAF\r

1 Charge Over Temperature Alarm: 51 - stored as (value * 10) + 2730 = 3240, to decode (value - 2730) / 10.0 = 51 - valid range reported by PBmsTools as 20-100
2 Charge Over Temperature Protect: 55 - stored as (value * 10) + 2730 = 3280, to decode (value - 2730) / 10.0 = 55 - valid range reported by PBmsTools as 20-100
3 Charge Over Temperature Protection Release: 50 - stored as (value * 10) + 2730 = 3230, to decode (value - 2730) / 10.0 = 50 - valid range reported by PBmsTools as 20-100
4 Discharge Over Temperature Alarm: 56 - stored as (value * 10) + 2730 = 3290, to decode (value - 2730) / 10.0 = 56 - valid range reported by PBmsTools as 20-100
5 Discharge Over Temperature Protect: 60 - stored as (value * 10) + 2730 = 3330, to decode (value - 2730) / 10.0 = 60 - valid range reported by PBmsTools as 20-100
6 Discharge Over Temperature Protect Release: 55 - stored as (value * 10) + 2730 = 3280, to decode (value - 2730) / 10.0 = 55 - valid range reported by PBmsTools as 20-100
```

### Charge / Discharge Temperature Protection Configuration

```
>>> ~250046DF0000FD85\r
<<< ~25004600501A010AAA0A780AAA0A1409E20A14F7E5\r
                 ??111122223333444455556666
>>> ~250046DE501A010AAA0A780AAA0A1409E20A14F7BC\r
<<< ~250046000000FDAF\r

1 Charge Under Temperature Alarm: 0 - stored as (value * 10) + 2730 = , to decode (value - 2730) / 10.0 =  - valid range reported by PBmsTools as (-35)-30
2 Charge Under Temperature Protection: (-5) - stored as (value * 10) + 2730 = , to decode (value - 2730) / 10.0 =  - valid range reported by PBmsTools as (-35)-30
3 Charge Under Temperature Release: 0 - stored as (value * 10) + 2730 = , to decode (value - 2730) / 10.0 =  - valid range reported by PBmsTools as (-35)-30
4 Discharge Under Temperature Alarm: (-15) - stored as (value * 10) + 2730 = , to decode (value - 2730) / 10.0 =  - valid range reported by PBmsTools as (-35)-30
5 Discharge Under Temperature Protect: (-20) - stored as (value * 10) + 2730 = , to decode (value - 2730) / 10.0 =  - valid range reported by PBmsTools as (-35)-30
5 Discharge Under Temperature Release: (-15) - stored as (value * 10) + 2730 = , to decode (value - 2730) / 10.0 =  - valid range reported by PBmsTools as (-35)-30
```

```
>>> ~250046E10000FD99\r
<<< ~25004600200E010E2E0EF60DFCFA5D\r
                 ??????????????
>>> ~250046E0200E010E2E0EF60DFCFA48\r
<<< ~250046000000FDAF\r

????????? mystery ????????? this is sent during "Parameter Setting" tab Read/Write All but does not correspond to ANY value that I can find (and certainly not in the Parameter Setting tab) displayed in PBmsTools
```


```
>>> ~250046E70000FD93\r
<<< ~25004600501A0109E209B009E20D340D660D34F806\r
                 ??????????????????????????
>>> ~250046E6501A0109E209B009E20D340D660D34F7EB\r
<<< ~250046000000FDAF\r

????????? mystery ????????? this is sent during "Parameter Setting" tab Read/Write All but does not correspond to ANY value that I can find (and certainly not in the Parameter Setting tab) displayed in PBmsTools
```

## "System Configuration" tab

There are many other settings in "System Configuration" that can be written and/or calibrated here, 
none of which I am exposing because it would be a Very Bad Idea to mess with them.

"The charge limiter limits the charge current if it goes > 100A to 10A, this is useful for a multi-pack setup where the packs are not balanced"

### Charge Current Limiter Start Current 

```
>>> ~250046ED0000FD86\r
<<< ~25004600C0040064FCCE\r
                 ??11

1 Charge Current Limiter Start Current: 100 - stored directly
```

### Charge Current Limiter Current Limit Gear Switch

I don't know the exact amps values that correspond to "low" or "high", I believe one of them means "10 amps" from other documentation, 
but if the charge current limiter is activated due to pack SOC mismatch, this represents what the charge current will be limited to until it "catches up"

```
>>> ~25004699E00209FD1D\r # low
                 11
<<<  ~25004600C0040938FCC4\r
                  ????
>>>  ~25004699E00208FD1E\r # high
                  11
<<<  ~25004600C0040830FCCD\r
                  ????

1 The "low/high" switch command, see: enum SwitchCommand
```

### Read Remaining Capacity

```
>>> ~250046A60000FD98\r
<<<  ~25004600400C183C286A2710FB0E\r
                  111122223333

1 Remaining Capacity (mAh): 62040 - stored in 10mAh hours, so 62040 is 6204
2 Actual Capacity (mAh): 103460 - stored in 10mAh hours, so 103460 is 10346
3 Design Capacity (mAh): 100000 - stored in 10mAh hours, so 100000 is 10000
```

## Miscellaneous
