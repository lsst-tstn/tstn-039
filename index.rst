:tocdepth: 1

.. sectnum::

.. Metadata such as the title, authors, and description are set in metadata.yaml


Abstract
========

This tech note describes the laser electronics system: requirements, design, operation, and safety considerations. Additionally, pointers to all supporting documentation are included. This electronics system was designed and built by Kenneth Brown and completed in February, 2023.

Description
===========
In order to complete monochromatic dome flats, we are using a high powered tunable laser from Ekspla. This laser is designed to be operated in a lab environment, meaning in room temperature air. In order to decrease the length of the optical fiber, which will run from the laser to a projector that will illuminate the calibration screen, the laser will be mounted on the dome structure. The laser must be housed in an enclosure which will be warmed up when the laser is needed, and then rapidly cooled when laser operation has completed so that the enclosure is at the ambient temperature of the dome.

Due to size restrictions on the laser platform, and a desire to remove heat sources from the laser enclosure, it was decided to move most of the electronics required for controlling and powering the laser in a separate enclosure. However, the power supply for the laser and the thermal system are housed in the laser enclosure. Therefore, this document describes the electronics system for the laser, which is split between the laser enclosure and a separate electronics enclosure. The laser enclosure will be mounted on a platform built by the dome contractor EIE. The electronics enclosure will be located on a beam above the laser platform.

.. figure:: /_static/mounting.png
 :name: mounting
 :target: ../_images/mounting.png
 :alt: mounting
 :scale: 50 %

Additionally, both the laser and electronics enclosures must meet the safety requirements for operating a class-4 laser.

Requirements
============

Operational Requirements
------------------------
While the laser can be "turned off", it is highly recommended that the laser power supply stay connected to power. In order to prevent condensation on the crystals which perform the sum frequency generation, they must be constantly maintained at an elevated temperature. The laser was measured to pull ~15 W when in this state. 

Power Requirements
------------------
The contractor shall supply 220V, 50 Hz, 16 Amps, single phase, AC power at this location, with a single circuit breaker.

The maximum power needed for the entire laser system has been estimated at approximately 700W, with a standby power (system not in use, but still powered) of approximately 145W.

Thermal Requirements
--------------------

- Cooling timeframe ≈ 60 minutes
- Cooling airflow calculation = 0.11941262 m³/s or 253.023407 cfm (value increases based upon efficiency/obstructions etc.)
- Heating timeframe ≈ 30 minutes
- Heating power calculation = 1176 W (value increases based upon thermal coupling efficiency [convection])
- Average temperature tolerance= ±1 °C/hour (slow drifts acceptable)
- Normal operating time is ≈ 4 hours
- Laser ambient temperature = 18 to 25C
-	Operational Temperature Range = -3 to 19C

The laser was designed to operate in a laboratory environment, at an ambient temperature of 18 to 25C. Since the dome of the telescope has a typical ambient temperature of -3 to 19C, the laser will need to be temperature controlled in order for it operate properly. The laser will be stored in an insulated box with a thermal couple, two small flexible heaters and a small cooling fan. A temperature controller will be used to read out the temperature of the laser and turn on either the heaters or the cooling fan to maintain the laser at the desired temperature. The insulated box will help to maintain the temperature of the laser and to keep heater from dissipating into the dome environment. Also the inclusion of the insulation lowers the amount of power needed to heat the laser.

Safety
------
Since this is a class 4 laser, the enable line of the laser will be wired into the domes GIS system

A limit switch has been placed underneath the lid of the laser enclosure. The limit switch was selected such that when the laser enclosure is opened, all the electronics within the laser electronics cabinet and the laser enclosure will be shut off except for the PDU, the laser, and the Ethernet Switch. This way the laser is still powered and on network when service is required to the laser.

There is a safety disconnect switch on the door of the electronics enclosure. When the door is opened, all power is removed for all of the electronics in the electronics enclosure and the laser enclosure, including the laser.

.. note::

  If the door to the electronics enclosure is opened, power will be completely turned off to the laser, including the crystals. If maintenance in the electronics enclosure is required, perhaps plug the laser into a backup power source so that the crystals will continue to be powered.


Design
======

.. figure:: /_static/Laser_Electronics_Block_Diagram.PNG
 :name: Laser Block Diagram
 :target: ../_images/Laser_Electronics_Block_Diagram.PNG
 :alt: Laser Block Diagram
 :scale: 50 %

Laser Enclosure
---------------
The laser enclosure was designed to house the laser, the laser power supply, and several electronics, which includes a Temperature scanner, Raspberry Pi, Power Meter, and Humidity Scanner. The laser and power supply are held on a rack to elevate the laser and the remaining electronics are mounted underneath. A large cooling fan is mounted in the side of enclosure to cooling the entirety of the enclosure when the temperature is above a set point. 

The laser will be enclosed in an insulated box to maintain temperature. The flexible heaters and the small cooling fan inside this insulated box with the laser, each turning on or off when the laser is outside a certain set point. 

The laser enclosure is designed to turn off all the electronics, excluding the laser and its power supply, when the enclosure is opened. A limit switch mounted to the door will be deactivated when the door is opened, cutting power to the electronics. 

.. note::

  When the laser enclosure is opened, the limit switch can be reactivated if necessary for testing purposes. This should only be done by the appropriate personnel.

.. figure:: /_static/Laser Enclosure.png
 :name: Laser Enclosure
 :target: ../_images/Laser Enclosure.png
 :alt: Laser Enclosure
 :scale: 100 %

Electronics Cabinet
-------------------
The Electronics Cabinet was specifically designed to run on 220VAC, 50Hz, 1 Phase, 16A. This Electronics Cabinet can be operated on 120VAC, 60Hz, 1 Phase, 15A for short periods of time.

The Electronics cabinet must be powered down via the disconnect switch on the door in order to open the enclosure. This will shut off power to all electronics in the cabinet and the laser enclosure.

The cabinet includes a PDU, a temperature controller for the laser, a Network Switch to provide Ethernet to the PDU and the Raspberry Pi (located in the laser enclosure), an Ethernet to Serial server for the laser and the temperature controller, a safety relay to shut off power to the electronics when the laser enclosure is opened, multiple solid state relays to controller the heaters and cooling fans (located in laser enclosure) and 12VDC and 24VDC power supplies. 

The facility will provide 220VAC for the electronics cabinet along with a single Ethernet cable, which will run to the Network Switch within the cabinet. When the laser enclosure is opened, power to the electronics cabinet will be removed with the exception of the PDU and the Network Switch to allow for the laser to remain powered and on network.

.. figure:: /_static/Laser Electronics Cabinet.jpg
 :name: Electronics Cabinet
 :target: ../_images/Laser Electronics Cabinet.jpg
 :alt: Electronics Cabinet
 :scale: 50 %

-	SW101: Controls the incoming power to the control cabinet. Interlocked to door per NEC requirements; disconnects all incoming AC power when ‘OFF’.
-	CB101: Circuit breaker for incoming power. Inline before any energy consuming devices. UL489 rated per NEC requirements. Supplies power to PDU1 which powers LASER. Continuously ‘ON’ when circuit breaker is in the ‘on’ position.
-	CB102: Circuit breaker for continuously powered devices. Inline before PS1 and PS4. UL508 rated per NEC requirements.
-	K1 Contactor: Supplies power to distribution terminals for all non-continuously powered devices. Controlled by the safety relay SR001, and activated when LASER enclosure lid is closed.
-	CB201: Circuit breaker for PS2
-	CB202: Circuit breaker for PS3 
-	CB203: Circuit breaker for PS5
-	CB204: Circuit breaker for Solid state relay 3 (SSR3).
-	CB205: Circuit breaker for Solid state relays 4 (SSR4). 
-	PS1: Power Supply which supplies 24VDC power to Cisco Network Switch. 
-	PS2: Power supply which supplies 24VDC to the temperature controller.
-	PS3: Power Supply which supplies 24VDC to the Moxa Ethernet-to-serial server, and the LASER Enclosure electronics (SEL RTD scanner and Humidity Sensor).
-	PS4: Power Supply which supplies 24VDC to the Safety Relay
-	PS5: Power Supply which supplies 12VDC to the 50W and 25W heaters in the laser enclosure, the laser power meter and computer to control the power meter. 
-	PS6: Power Supply which supplies 5VDC to the Raspberry Pi and DIN-102R within the laser enclosure. 
-	SSR1 and SSR2: Solid state relays activated by the temperature controller, provides 12VDC to Heater 1 (50W Heater) and Heater 2 (25W Heater) respectively. These relays are controlled by the Omron temperature controller primary output, ‘Control Output 1’. 
-	SSR3: Solid State Relay activated by the Raspberry Pi, provides AC power to the large cooling fan (Fan 1). 
-	SSR4: Solid state relay activated by the temperature controller, provides AC power to the small cooling fan (Fan 2).

-	Temperature Controller: Temp_Ctrl. This Omron E5DC controller is set to operate in ‘Reverse’ and ‘On/OFF’ modes; meaning that it will heat when the ambient temperature is below the set-point (or Process value, PV) and cool when above the set-point. 
In this mode the ‘Control Output’ on pins 3 and 4 control the flexible rectangular heaters and are presently wired to activate the 75W heater elements, through SSR1 and SSR2. (SSR1 and SSR2 are wired in parallel to the controller). 

“On/OFF’ control mode will energize the cooling fans when the ambient temperature is above the set-point. The controller will activate ‘Auxiliary Output 2’ to enable cooling via pin 13. ‘Auxiliary Output 2’ is wired to SSR4 to operate the small cooling fan.

The temperature controller has a configurable ‘Auxiliary Output 1’; which is presently programmed to Alarm1, which in-turn has been set to an absolute value of 15 C. This alarm will activate the ‘Auxiliary Output 1’ and will energize SSR1 and SSR2, powering the 75W elements on the heaters. Enabling both heater elements at or below 15 C will speed-up the heating process in very cold environments. 

-	Safety relay SR001: This device is, technically, NOT part of the GIS system. This is a safety control, which ensures that the main power will be disconnected from the LASER enclosure when the lid is opened. The relay is presently configured in ‘Automatic start’ mode with ‘detection of shorts across contacts’, and will be wired to a lever-actuated, 2-pole, normally open switch mounted to the LASER enclosure, which activates when the lid is opened. Opening the lid on the LASER Enclosure will de-energize all of the electrical components, with the exception of the LASER power supply. This will ensure that service personnel will be safe, when working inside the Enclosure and that the crystal heaters inside the LASER will still be powered. Loss of Power to the enclosure will also remove power from the LASER’s safety interlock relay and the LASER cannot be operated. (The lever-switch can be purposefully defeated to allow powered service of the LASER if required… with appropriate site-specified safety procedures) 

-	Ethernet switch EthSw1: Powered at all times except when the safety disconnect switch on the door of the electronics cabinet is ‘OFF’. Required for communications to internal devices. Supplies Ethernet ports for the Ethernet-to-Serial server, PDU, and Raspberry pi. Not enough power for POE! The power supply for this switch was chosen to minimize heat generation within the Thermal control cabinet and will not supply POE. (or very limited power to one port)

-	Ethernet-to-Serial Server, E2S001: Moxa 5450I, 4 port Eth to Serial server. Port 1 is RS232 for LASER communications and port 2 is RS485 communications to the Omron Temperature controller. Ports 3 and 4 are reserved for future expansion.

-	PDU1: power distribution unit. Port 3 is presently used for the power supplied to the Thermal controls within the cabinet. PS1, PS4 SR1, EthSw1 are NOT powered through this device. 2 other ports are used to supply power to the LASER power supply. (PS81120 series)
-	Raspberry Pi: RPi. 
-	RTD Scanner:
-	Humidity Sensor:
-	Power Meter:
-	

Small Cooling Fan
-----------------
Circuit-Test Electronics `CFA1158038HS <http://www.circuittest.com/Media/Data/Specifications/S-AC_Fans_A0838_Series.pdf>`__, AC powered cooling fan. This fan is for cooling the laser when the temperature exceeds the set point temperature. Activated when the temperature of the laser rises above the set temperature and turns off when the temperature drops below the set point. The Omron temperature sensor sends a signal to a SSR when the temperature is below the PV. 

Large Cooling Fan
-----------------
Mechatronics Fan Group `UF200BMB23-H2C2A-B1 <https://www.mechatronics.com/pdf/UF200BMB-H2.pdf>`__, AC powered cooling fan, mounted within the wall of the Laser Enclosure. This fan is for cooling the laser enclosure when the temperature of the laser enclosure exceeds a set temperature. Activated when the temperature of the laser enclosure is higher than set point. The RTD scanner has multiple thermistors connected to multiple places in the laser enclosure, the temperatures are then read by the Raspberry Pi. The large cooling fan is then turned on when the Raspberry Pi reads the average temperature is higher than the set point and sends a signal to a SSR which turns on the large cooling fan.

Operation
==========

Thermal Chamber Setup
---------------------

Since there is no way to connect to the TTS in the thermal chamber, everything is run from a laptop with static IP addresses. The omron controller is controlled manually and the laser is operated with the hand paddle.


.. table:: IP Addresses

   +--------------+-------------------+---------------------------------------+-------------------+-----------------+
   | Component    | MAC address       | DHCP name                             | Static IP Address | TTS IP Adddress | 
   +--------------+-------------------+---------------------------------------+-------------------+-----------------+
   | PDU          | 0C 73 EB B0 67 36 | laserpdu.tu.lsst.org                  | 192.168.1.100     | 140.252.147.98  |
   +--------------+-------------------+---------------------------------------+-------------------+-----------------+
   | Moxa         | 00 90 E8 A3 B1 13 | lanToSerialConverterLaser.tu.lsst.org | 192.168.127.254   | 140.252.147.121 |
   +--------------+-------------------+---------------------------------------+-------------------+-----------------+
   | Cisco Switch | 20 CF AE 64 5E A0 | laserNetworkSwitch.tu.lsst.org        | 192.168.1.35      | 140.252.147.99  |
   +--------------+-------------------+---------------------------------------+-------------------+-----------------+
   | Raspberry Pi | E4 5f 01 35 d2 0d | laserRpi.tu.lsst.org                  | 168.254.40.34     | 140.252.147.122 |
   +--------------+-------------------+---------------------------------------+-------------------+-----------------+

.. table:: The PDU outlet numbering

   +--------+--------------------+
   | Outlet | Name               |
   +--------+--------------------+
   | 3      | Thermal Controls   |
   +--------+--------------------+
   | 7      | Laser (1/2)        |
   +--------+--------------------+ 
   | 8      | Laser (2/2)        |
   +--------+--------------------+     

.. note::

  The heaters and cooling fans **MUST NOT** be run for more than brief periods on 120VAC, they may be permanently damaged! If there must be an extended period of usage at 120VAC, CB203, CB204, CB205, CB206 can be set to OFF. No heating/cooling will be available but all other functions will be available.

Additional Documentation
========================
Initial documentation for the whole laser system, including the laser enclosure, was done on `Confluence <https://confluence.lsstcorp.org/display/LTS/Calibration+LASER+Cabinet>`__, and additional notes for the electronics was documented `here <https://confluence.lsstcorp.org/display/LTS/Requirements+and+Notes+for+Enclosure+Thermal+Controls>`__.

Docushare: https://docushare.lsst.org/docushare/dsweb/View/Collection-13124


.. Make in-text citations with: :cite:`bibkey`.
.. Uncomment to use citations
.. .. rubric:: References
.. 
.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :style: lsst_aa
