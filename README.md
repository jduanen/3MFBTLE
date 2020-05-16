# 3MFBTLE
3M Filtrete Bluetooth LE Sensor

The sensor is a yellow plastic disc attached to filters pairs with Android/iOS app and reports lifetime of filter.
Senses both elapsed time since installation, and "filter life is determined by measuring changes in pressure that the sensor on the filter detects inside your home, and filter life is based on actual air flow and usage of your heating & cooling system."
See https://www.filtrete.com/3M/en_US/filtrete/about-us/smart-faq/

## Hardware

### Housing

![Housing External](https://github.com/jduanen/3MFBTLE/images/housing1.jpg
![Housing Internal](https://github.com/jduanen/3MFBTLE/images/housing2.jpg

### PCB

![PCB Front](https://github.com/jduanen/3MFBTLE/images/pcb_front.jpg
![PCB Back](https://github.com/jduanen/3MFBTLE/images/pcb_back.jpg

![nRF51802 Schematic](https://github.com/jduanen/3MFBTLE/docs/nrf51802_qcaa_schematic.pdf


### Components

* Coin Battery: 3V
* nRF51802 QCAA: Nordic Semi Bluetooth Low Energy and 2.4 GHz SoC for cost sensitive applications
  - https://nordicsemi.com/Products/Low-power-short-range-wireless/nRF51802
  - BTLE 4.2, 256KB Flash, 16KB RAM, M0-Cortex, +4 dBm, AES Encryption, UART, Vdd=1.8V-3V
  - low-cost variant of the nRF51822 (less RX sensitivity, more power)
  - compatible with the S130 Central and Peripheral protocol stack
* 16MHz Xtal: for SoC
* 32.768KHz Xtal: for TOD clock
* 2SMPB-02B: Omron Digital Barometric Pressure Sensor
  - https:omronfs.omron.com/en_US/ecb/products/pdf/en-2smpb-02b.pdf
  - high accuracy measurement of barometric pressure and temperature
  - on-board 24b ADC
  - calibration parameters stored in OTP
  - measures absolute air pressure, 30-110 kPa (800 kPa max), +-4 Pa accuracy, 0.06 Pa resolution
  - measures absolute temperature, 0-40C, +-2C accuracy, 0.0002C resolution
  - Vdd=1.8V typ., 3.6V max
  - measurement times from 5.5msec to 33.7msec (more oversampling for higher accuracy), selectable filtering
  - can be used for indoor navigation, stair detection, drop detection, weather monitoring, etc.
  - I2C/SPI bus interface
  
