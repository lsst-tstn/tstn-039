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


Operation
==========

- SW101: Controls the incoming power to the control cabinet. Interlocked to door per NEC requirements; disconnects all incoming AC power when ‘OFF’.
- CB101: Circuit breaker for incoming power. Inline before any energy consuming devices. UL489 rated per NEC requirements. Supplies power to PDU1 which powers LASER. Continuously ‘ON’ when circuit breaker is in the ‘on’ position.
- CB102: Circuit breaker for continuously powered devices. Inline before PS001 and PS004. UL508 rated per NEC requirements.
- K1 Contactor: Supplies power to distribution terminals for all non-continuously powered devices. Controlled by the safety relay SR001, and activated when LASER enclosure lid is closed.
- CB201: Circuit breaker for PS002, which supplies 24VDC to the temperature controller.
- CB202: Circuit breaker for PS003, which supplies 24VDC to the Moxa Ethernet-to-serial server, and the LASER Enclosure electronics.
- CB203: Circuit breaker for Solid state relay 1. (SSR1) When activated by the temperature controller, provides AC power to Heaters 1 and 2, low power heat element. (Htrx_Lo) This relay is controlled by the Omron temperature controller primary output, ‘Control Output 1’. (This output also controls SSR5 as the fans must be running when the heater element is energized)
- CB204: Circuit breaker for Solid state relay 2. (SSR2) When activated by the temperature controller, provides AC power to Heaters 1 and 2, high power heat element. (Htrx_Hi) This relay is controlled by the Omron temperature controller output, ‘Auxiliary Output 1’. This output is controlled by an absolute low temperature alarm in the test configuration. (15 C)
- CB205: Circuit breaker for Solid state relay 3. (SSR3) When activated by the temperature controller, provides AC power to the cooling fan. (and heaters 1 and 2, fans through SSR4) This relay is controlled by the Omron temperature controller output, ‘Auxiliary Output 2’. 
- CB206: Circuit breaker for Solid state relays 4 & 5. (SSR4 and SSR5) When activated by the temperature controller, provides AC power to both the cooling and heater fans
- Temperature Controller: Temp_Ctrl. This Omron E5DC controller is set to operate in ‘Reverse’ and ‘On/OFF’ modes; meaning that it will heat when the ambient temperature is below the set-point (or Process value, PV) and cool when above the set-point. 

 In this mode the ‘Contol Output’ on pins 3 and 4 control the heaters and are presently wired to activate the 200W heater elements, in both heaters, through SSR1 and both heater fans via SSR5. The fans MUST be rotating whenever the heater elements are energized. (SSR1 and SSR5 are wired in parallel to the controller) 

 “On/OFF’ control mode will energize the cooling fans when the ambient temperature is above the set-point. The controller will activate ‘Auxiliary Output 2’ to enable cooling via pin 13. ‘Auxiliary Output 2’ is wired in parallel to SSR3 and SSR4 to operate the cooling fan and both heater fans.

 The temperature controller has a configurable ‘Auxiliary Output 1’; which is presently programmed to Alarm1, which in-turn has been set to an absolute value of 15 C. This alarm will activate the ‘Auxiliary Output 1’ and will energize SSR2, powering the 300W elements on the heaters. Enabling both heater elemsnts at or below 15 C will speed-up the heating process in very cold environments. This action will occur in tandem with the heating action and, therefore, SSR1, SSR2 and SSR5 will be energized.

- Safety relay SR001: This device is, technically, NOT part of the GIS system. This is a safety control, which ensures that the main power will be disconnected from the LASER enclosure when the lid is opened. The relay is presently configured in ‘Automatic start’ mode with ‘detection of shorts across contacts’, and will be wired to a lever-actuated, 2-pole, normally open switch mounted to the LASER enclosure; which activates when the lid is opened. Opening the lid on the LASER Enclosure will de-energize all of the electrical components, with the exception of the LASER power supply. This will ensure that service personnel will be safe, when working inside the Enclosure and that the crystal heaters inside the LASER will still be powered. Loss of Power to the enclosure will also remove power from the LASER’s safety interlock relay and the LASER cannot be operated. (The lever-switch can be purposefully defeated to allow powered service of the LASER if required… with appropriate site-specified safety procedures) 

- Ethernet switch EthSw1: Powered at all times except when the interlock safety switch is ‘OFF’. Required for communications to internal devices. Supplies Ethernet ports for the Ethernet-to-Serial server, PDU, and Raspberry pi. Not enough power for POE! The power supply for this switch was chosen to minimize heat generation within the Thermal control cabinet and will not supply POE. (or very limited power to one port)
- Ethernet-to-Serial Server, E2S001: Moxa 5450I, 4 port Eth to Serial server. Port 1 is RS232 for LASER communications and port 2 is RS485 communications to the Omron Temperature controller. Ports 3 and 4 are reserved for future expansion.
- PDU1: power distribution unit. Port 3 is presently used for the power supplied to the Thermal controls within the cabinet. PS001, PS004 SR001, EthSw1 are NOT powered through this device. 2 other ports are used to supply power to the LASER power supply. (PS81120 series)


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
