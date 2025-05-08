# 3MFBTLE: 3M Filtrete Bluetooth LE Sensor

The sensor is a yellow plastic disc attached to filters pairs with Android/iOS app and reports lifetime of filter.
Senses both elapsed time since installation, and "filter life is determined by measuring changes in pressure that the sensor on the filter detects inside your home, and filter life is based on actual air flow and usage of your heating & cooling system."
See https://www.filtrete.com/3M/en_US/filtrete/about-us/smart-faq/

## Hardware

N.B. As of 2022, there is (at least one) new revision of the PCB. It is marked something like this: 94V-0 MT-JF2. Almost all of the components (as well as the PCB and antenna) have changed. It was to be expected given that the Omron pressure/temperature sensor seems to have been discontinued.

### Housing

![Housing Top](https://github.com/jduanen/3MFBTLE/blob/master/images/housing1.jpg)

![Housing Bottom](https://github.com/jduanen/3MFBTLE/blob/master/images/housing2.jpg)

The sensor is glued to the upper side of the filter.  Airflow goes through filter first, then past the sensor and into the central air intake.
The sensor PCB has three small holes next to the pressure sensor.  The rectangular part of the enclosure that attaches to the top of the filter has a rectangular opening above the three PCB holes.  The sensor is glued to the filter with a ring of hot glue that surrounds the rectangular opening.  The disc-shaped part of the enclosure has a U-shaped structure below the three holes in the PCB, and the open end faces a slot in the part of the enclosure that holds the PCB.  This slot faces the semicircular divot in the enclosure.
It would appear the the airflow goes through the filter below where the sensor is mounted, through the PCB, and is drawn out of the slot and the divot.

### PCB

![PCB Top](https://github.com/jduanen/3MFBTLE/blob/master/images/pcb_front.jpg)

![PCB Bottom](https://github.com/jduanen/3MFBTLE/blob/master/images/pcb_back.jpg)

### Components

* Coin Battery: 3V
* nRF51802 QCAA: Nordic Semi Bluetooth Low Energy and 2.4 GHz SoC for cost sensitive applications
  - <https://nordicsemi.com/Products/Low-power-short-range-wireless/nRF51802>
  - <https://github.com/jduanen/3MFBTLE/blob/master/docs/nrf51802_qcaa_schematic.pdf>
  - BTLE 4.2, 256KB Flash, 16KB RAM, M0-Cortex, +4 dBm, AES Encryption, UART, Vdd=1.8V-3V
  - low-cost variant of the nRF51822 (less RX sensitivity, more power)
  - compatible with the S130 Central and Peripheral protocol stack
* 16MHz Xtal: for SoC
* 32.768KHz Xtal: for TOD clock
* 2SMPB-02B: Omron Digital Barometric Pressure Sensor
  - <https:omronfs.omron.com/en_US/ecb/products/pdf/en-2smpb-02b.pdf>
  - <https://github.com/jduanen/3MFBTLE/blob/master/docs/en-2smpb-02b.pdf>
  - high accuracy measurement of barometric pressure and temperature
  - on-board 24b ADC
  - calibration parameters stored in OTP
  - measures absolute air pressure, 30-110 kPa (800 kPa max), +-4 Pa accuracy, 0.06 Pa resolution
  - measures absolute temperature, 0-40C, +-2C accuracy, 0.0002C resolution
  - Vdd=1.8V typ., 3.6V max
  - measurement times from 5.5msec to 33.7msec (more oversampling for higher accuracy), selectable filtering
  - can be used for indoor navigation, stair detection, drop detection, weather monitoring, etc.
  - I2C/SPI bus interface

## Schematic

*TBD*

## BTLE Protocol
  
| Characteristic UUID |  Description  |  Name  |  Data  | Comments |
| ------------------- |:-------------:| ------:| ------:|:-------- |
| 00002a19-0000-1000-8000-00805f9b34fb | Battery Level: 100% to 0% | ? | 1B Decimal | Standard Service (see chart below) |
| 41690001-7276-697a-466c-747253656e73 | BTLE MAC Address | ? | 6B Hex | D0 6B 88 D1 C0 C6 |
| 41690002-7276-697a-466c-747253656e73 | Realtime Clock | ? | 4B Decimal | TOD in secs, POSIX Epoch time format |
| 41690003-7276-697a-466c-747253656e73 | *Unknown* | ? | 16B Hex | All zeros |
| 41690004-7276-697a-466c-747253656e73 | *Unknown* | ? | 1B Hex | Write-only? |
| 41690005-7276-697a-466c-747253656e73 | *Unknown* | ? | 6B Hex | 00 0E 10 00 3C, constant value |
| 41690006-7276-697a-466c-747253656e73 | Filter Dimensions | SIZE | 8B ASCII | "20020010", means 20x20x1 |
| 41690007-7276-697a-466c-747253656e73 | Filter Model | MODELNO | 6B ASCII | "1900" |
| 41690008-7276-697a-466c-747253656e73 | Part Number | UPC | 14B ASCII | "638060137512" |
| 41690009-7276-697a-466c-747253656e73 | Seconds Since Reset | ? | 4B Hex | monotonic counter, increasing in increments of 60 |
| 4169000a-7276-697a-466c-747253656e73 | Device Name | ? | 16B ASCII | "Filtrete D06B88D" |
| 4169000b-7276-697a-466c-747253656e73 | *Unknown* | ? | 15B ASCII | "1C0C6", looks like last part of MAC address, Device ID? |
| 4169000c-7276-697a-466c-747253656e73 | *Unknown* | ? | 2B Hex | 0E 20, constant value |
| 4169000d-7276-697a-466c-747253656e73 | *Unknown* | ? | 20B Hex | looks like a byte array event count log |
| 4169000e-7276-697a-466c-747253656e73 | *Unknown* | ? | 14B ASCII | "00000000000000" |
| 4169000f-7276-697a-466c-747253656e73 | Time and Date string | ? | 20B ASCII | "2018-04-02 20:03:13.", looks time of mfgr. |

### Battery Level

![Battery Level](https://github.com/jduanen/3MFBTLE/blob/master/images/3MTBTLE_battery.png)

## Test Pads

There are 10 pogo-pin pads on the top of the PCB, and a set of test pads along the antenna feed line.

The group of four pads closest to the 2SMPB-02B part are arranged in a 2x2 square pattern and are connected to the sensor's SPI bus -- i.e., signals named CSB (pin 2), SDI (pin 3), SCK (pin 4), and SDO (pin 5).  The CSB signal selects the bus mode (i.e., 3-/4-wire SPI or I2C), while the other three pins implement the SPI bus connection between the SoC and the sensor.

| Sensor Pin | SoC Pin |
| ---------- | ------- |
| CSB | P0.00 |
| SDI | P0.02 |
| SCK | P0.03 |
| SDO | P0.04 |

The other group of six pads consists of two signals for the serial port, two for the programming port, and a pair that connect to Vcc and GND.  The serial port uses the SoC's pin P0.14 as the Rx signal, and P0.16 as the Tx signal.  The SoC's SWDIO/nRESET and SWCLK pins are used to program the device and these signals are connected to the next pair of pads.
The SWDIO and SWDCLK pins are used for JTAG debugging and programming the on-chip flash memory.

![PCB Top_Annotated](https://github.com/jduanen/3MFBTLE/blob/master/images/pcb_front_annotated.jpg)

On the bottom of the PCB there are six pogo-pin pads and pads for a 10-pin flatpack part.
*TBD*

![PCB Bottom_Annotated](https://github.com/jduanen/3MFBTLE/blob/master/images/pcb_back_annotated.jpg)

## Serial Port

The pads named *Serial_Tx* and *Serial_Rx* on the schematic are 3V serial connections using P0.14 and P0.16 pins on the nRF51802, respectively.

Connect a 3.3V FTDI USB to serial part up to the Tx and Rx pads, set a terminal emulator to 19200-8-n-1, and type a character and the SoC will prompt with the string: "Waiting for AT."

| AT Command | Response |
| ---------- | -------- |
| ATI | 3M Filtrete XXXXXXXXXXX |

## Disassembling the Firmware

It is possible to dump the firmware via the (SWCLK/SWDIO) JTAG port using an ST-LinkV2 USB dongle and OpenOCD.

Strings found in the firmware include:
```
OK waiting for AT
OK NAME=%.*s
OK SKU=%.*s
OK UPC=%.*s
OK SIZE=%.*s
OK LOTNUM=%.*s
OK MODELNO=%.*s
ERR register S=%d unknown
ERR writing to S%d not allowed
OK writing NAME=%.*s
ERR invalid NAME=%.*s
OK writing SKU=%.*s
ERR invalid SKU=%.*s
OK writing UPC=%.*s
ERR invalid UPC=%.*s
OK writing MODELNO=%.*s
ERR invalid MODELNO=%.*s
OK writing SIZE=%.*s
ERR invalid SIZE=%.*s
OK writing LOTNO=%.*s
ERR invalid LOTNO=%.*s
OK %.*s
OK init successful. HW=%3d, FW=%3d
OK 3M Filtrete HW=%3d, FW=%3d
OK commited to flash
OK ready to write
ERR invalid command state (%d)

 Filtrete 000103040506         

 1500++1602500100051111539378Filtrete                       
@@@@@@@@@AAAAA@@@@@@@@@@@@@@@@@@
          
OK 0x%02X%02X%02X%02X%02X%02X%02X%02X%02X%02X%02X%02X%02X%02X%02X%02X
INVALID DEVICE NAME
*B Z
200200101900  00638060137512Filtrete E2CDFD9EB4CE          000000000000002018-04-02 20:03:13.
```

## The Filtrete Android App

The device reverts to it's factory state when power is removed.
The Android app pairs with the device and apparently initializes it as several values change after pairing.

By enabling Bluetooth HCI snooping on the Android device, it is possible to dump a trace of the BTLE transactions and view them using Wireshark.

The transactions performed on the device by the Android app during the pairing operation include: 
*TBD*
  
## Random Notes and Speculation
  
I noticed that the filter directly below the sensor was much lighter than the surrounding area.  This could be due to the fact that the airflow through this region is restricted by the presence of the sensor.
Perhaps there is a difference in pressure caused by the different degrees of dirtiness between the unrestricted airflow region at the sensor's divot, and the restricted airflow where the air comes into the sensor.
This may not be the case, and there may be sufficent airflow through the sensor and the pressure being measured reflects how dirty the filter is.

The 3M literature says that the coin battery lasts for a year, and the filters should be replaced every 30-90 days.

I have two filters in my house and noticed that they both tracked identically from 100% to 10%, despite the fact that the upstairs filter saw significantly greater use than the downstairs one.  This leads me to believe that the first order effect here is time and only if a filter becomes totally clogged will the airflow enter into the lifetime calculation.

It appears that the sensor samples at 60sec intervals -- all of the monotonically increasing counts (that look like timers) that I observed were in multiples of 60.

I think that the Android app sets the date/time of installation and the device counts elapsed seconds.

