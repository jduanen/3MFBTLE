# 3MFBTLE
3M Filtrete Bluetooth LE Sensor

The sensor is a yellow plastic disc attached to filters pairs with Android/iOS app and reports lifetime of filter.
Senses both elapsed time since installation, and "filter life is determined by measuring changes in pressure that the sensor on the filter detects inside your home, and filter life is based on actual air flow and usage of your heating & cooling system."
See https://www.filtrete.com/3M/en_US/filtrete/about-us/smart-faq/

## Hardware

### Housing

![Housing Top](https://github.com/jduanen/3MFBTLE/blob/master/images/housing1.jpg)

![Housing Bottom](https://github.com/jduanen/3MFBTLE/blob/master/images/housing2.jpg)

Glued to the upper side of the filter.  Airflow goes through filter first, then past the sensor and into the central air intake.
The sensor PCB has three small holes next to the pressure sensor.  The rectangular part of the enclosure that attaches to the top of the filter has a rectangular opening above the three PCB holes.  The sensor is glued to the filter with a ring of hot glue that surrounds the rectangular opening.  The disc-shaped part of the enclosure has a U-shaped structure below the three holes in the PCB, and the open end faces a slot in the part of the enclosure that holds the PCB.  This slot faces the semicircular divot in the enclosure.
It would appear the the airflow goes through the filter below where the sensor is mounted, through the PCB, and is drawn out of the slot and the divot.

### PCB

![PCB Top](https://github.com/jduanen/3MFBTLE/blob/master/images/pcb_front.jpg)


### Components

* Coin Battery: 3V
* nRF51802 QCAA: Nordic Semi Bluetooth Low Energy and 2.4 GHz SoC for cost sensitive applications
  - https://nordicsemi.com/Products/Low-power-short-range-wireless/nRF51802
  - https://github.com/jduanen/3MFBTLE/blob/master/docs/nrf51802_qcaa_schematic.pdf
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
  
  ## BTLE Protocol

  ## The Filtrete Android App
  
  ## Random Notes and Speculation
  
I noticed that the filter directly below the sensor was much lighter than the surrounding area.  This could be due to the fact that the airflow through this region is restricted by the presence of the sensor.
Perhaps there is a difference in pressure caused by the different degrees of dirtiness between the unrestricted airflow region at the sensor's divot, and the restricted airflow where the air comes into the sensor.
This may not be the case, and there may be sufficent airflow through the sensor and the pressure being measured reflects how dirty the filter is.

The 3M literature says that the coin battery lasts for a year, and the filters should be replaced every 30-90 days.

I have two filters in my house and noticed that they both tracked identically from 100% to 10%, despite the fact that the upstairs filter saw significantly greater use than the downstairs one.  This leads me to believe that the first order effect here is time and only if a filter becomes totally clogged will the airflow enter into the lifetime calculation.

It appears that the sensor samples at 60sec intervals -- all of the monotonically increasing counts (that look like timers) that I observed were in multiples of 60.
