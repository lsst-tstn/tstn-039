:tocdepth: 1

.. sectnum::

.. Metadata such as the title, authors, and description are set in metadata.yaml


Abstract
========

This tech note describes the laser electronics system: requirements, design, operation, and safety considerations. Additionally, pointers to all supporting documentation are included. This electronics system was designed and built by Kenneth Brown and completed in February, 2023.

Description
===========
In order to complete monochromatic dome flats, we are using a high powered tunable laser from Ekspla. This laser is designed to be operated in a lab environment, meaning in room temperature air. In order to decrease the length of the optical fiber, which will run from the laser to a projector that will illuminate the calibration screen, the laser will be mounted on the dome structure. The laser must be housed in an enclosure which will be warmed up when the laser is needed, and then rapidly cooled when laser operation has completed so that the enclosure is at the ambient temperature of the dome.

Due to size restrictions on the laser platform, and a desire to remove heat sources from the laser enclosure, it was decided to move most of the electronics required for controlling and powering the laser in a separate enlcosure. However, the power supply for the laser and the thermal system are housed in the laser enclosure. Thereform, this document describes the electronics system for the laser, which is split between the laser enclosure and a separate electronics enclosure. The laser enclosure will be mounted on a platform built by the dome contractor EIE. The electronics enclosure will be located on a beam above the laser platform.

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
While the laser can be "turned off", it is highly recommened that the laser power supply stay connected to power. In order to prevent condensation on the crystals which perofme the sum frequency generation, they must be constantly maintained at an elevated temperature. 
This was measured to be ~15 W.

Power Requirements
------------------
The contractor shall supply 220V, 50 Hz, 16 Amps, singe phase, AC power at this location, with a single circuit breaker.

Thermal Requirements
--------------------

- Cooling timeframe ≈ 60 minutes
- Cooling airflow calculation = 0.11941262 m³/s or 253.023407 cfm (value increases based upon efficiency/obstructions etc.)
- Heating timeframe ≈ 30 minutes
- Heating power calculation = 1176 W (value increases based upon thermal coupling efficiency [convection])
- Average temperature tolerance= ±1 °C/hour (slow drifts acceptable)
- Normal operating time is ≈ 4 hours

Safety
------
Since this is a class 4 laser, the enable line of the laser will be wired into the domes GIS system

There is a safety switch on the lid of the laser enclosure such that when the laser enclosure is opened, power will be cut to the laser.

There is a safety switch on the door of the electronics enclosure. When the door is opened, power to the electronics enclosure, and thus the laser enclosure.

.. note::

  If the door to the electronics enclosure is opened, power will be completely turned off to the laser, including the crystals. If maintenance in the electronics enclosure is required, perhaps plug the laser into a backup power source so that the crystals will continue to be powered.



Design
======

.. figure:: /_static/Laser_block_final_26oct.png
 :name: block_diagram
 :target: ../_images/Laser_block_final_26oct.png
 :alt: block_diagram
 :scale: 50 %

This Thermal Control Cabinet was specifically designed to run on 230VAC, 50Hz, 1 Phase, 16A.
This Thermal Control Cabinet can be operated on 120VAC, 60Hz, 1 Phase, 15A for short periods of time.

.. figure:: /_static/as_built.png
 :name: as_built
 :target: ../_images/as_built.png
 :alt: as_built
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


Operation
==========

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

  The heaters and cooling Fan **MUST NOT** be run for more than brief periods on 120VAC, they may be permanently damaged! If there must be an extended period of usage at 120VAC, CB203, CB204, CB205, CB206 can be set to OFF. No heating/cooling will be available but all other functions will be available.


 

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
