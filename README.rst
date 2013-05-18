::

           ___  __        __     __                          ___
    |\/| |  |  /__` |  | |__) | /__` |__| |    | __  |\/| | |__  \  /
    |  | |  |  .__/ \__/ |__) | .__/ |  | |    |     |  | | |___  \/
                    __                __        __ 
                   /  `  /\  |\ |    |__) |  | /__`
                   \__, /~~\ | \|    |__) \__/ .__/


This project aims to document the OBD-II codes for Mitsubishi I-Miev
electric vehicle.

Conventions used in this document:

- Message ID is always 3 hex characters
- Data bytes are zero-indexed: D0, D1, etc

OBD-II Traffic analysis
~~~~~~~~~~~~~~~~~~~~~~~

Periodically occurring PIDs:

- 1000ms (1 fps):
  01C [#note_testmode]_
- 200ms (5 fps):
  568 [#note_testmode]_
- 100ms (10 fps):
  101, 286, 298, 29A, 2F2, 374, 375, 384, 385, 389 [#note_testmode]_,
  38A [#note_testmode]_, 3A4, 408, 412_, 695, 696, 697, 6FA, 75A, 75B
- 50ms (20 fps):
  38D, 564, 565, 5A1, 6D0, 6D1, 6D2, 6D3, 6D4, 6D5, 6D6, 6DA
- 40ms (25 fps):
  424, 6E1, 6E2, 6E3, 6E4
- 20ms (50 fps):
  119, 149, 156, 200, 208_, 210, 212, 215, 231, 300, 308, 325, 346, 418
- 10ms (100fps):
  236_, 285, 288, 373

.. [#note_testmode] Possibly only sent in debug mode.

PID descriptions
~~~~~~~~~~~~~~~~

.. _208:

208 - Brake pedal

Break pedal sensor data is transmitted every 20ms. Data bits seem
to be following:

-D0: `0x00` (const?)
-D1: `0x20` (const?)
-D2-D3: pedal position, 60:00 is zero position, max seems to be around 61:bf
-D4: `0xc0` (const?)
-D5: `0x00` (const?)
-D6: `0xc0` (const?)
-D7: `0x00` (const?)

.. _236:

236 - Steering wheel sensor
---------------------------

Steering wheel sensor data is transmitted every 10ms. Data bits:

- D0-D1: Steering wheel position with 0.5 degree accuracy, center point ``(0.0 degrees) = D0:0x10, D1:0x00``. ``(((D0 * 256) + D1) - 4096) / 2 = steering wheel position in degrees)``. Negative angle - right, positive angle left.
- D2-D3: possibly represents rate of change, defaults to ``D2:0x10, D3:0x00`` when steering weel is at rest.
- D4: counter, but only high-nibble bits (4-7) are used, ``D4[0:3]=0``
- D5: ``0x00`` (const?)
- D6: ``0x00`` (const?)
- D7: TODO

.. _412:

412 - Speed + Odometer value
----------------------------

Transmitted every 100ms. Data bits:

- D0: ``0x7f`` (const?)
- D1: speed in km/h
- D2-D4: Odometer display in km. ``((((D2 * 256) + D3) * 256) + D4) = km/h``
- D5: ``0x00`` (const?)
- D6: ``0x21`` (const?)
- D7: ``0x06`` (const?)
