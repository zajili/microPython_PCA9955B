Introduction
============

.. image:: https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/ruff/main/assets/badge/v2.json
    :target: https://github.com/astral-sh/ruff
    :alt: Ruff

MicroPython helper library for the NXP 16-Channel I²C-Bus Constant-Current LED Driver
(PCA9955B).

This project is a MicroPython port of
`CircuitPython_PCA9955B <https://github.com/noelanderson/CircuitPython_PCA9955B>`_
by Noel Anderson. The public API (``PCA9955``, ``LedChannel``, ``Group``,
``SubAddress`` ...) is kept as close as possible to the original, so code written
for the CircuitPython library needs only minor changes.

Differences from the CircuitPython library
------------------------------------------

* Uses the MicroPython ``machine`` module (``machine.I2C`` / ``machine.Pin``)
  instead of ``busio`` / ``digitalio`` and Adafruit Bus Device.
* No dependency on Adafruit CircuitPython, Blinka or ``adafruit_bus_device``.
* Not distributed through the Adafruit Library Bundle, ``circup`` or PyPI.
  Copy the package to the board instead (see below).
* No Sphinx documentation or example scripts. See the usage example below and
  the docstrings in the source.

Current limitations
-------------------

* **The I²C bus is currently hard-coded** in ``PCA9955.__init__``
  (``I2C(1, scl=Pin(3), sda=Pin(2), freq=400_000)``). The ``i2c`` argument is
  accepted for API compatibility but is not used yet. Edit the pins and bus id
  in ``pca9955b/pca9955b.py`` to match your board until this is fixed.
* The ``oe_pin`` and ``reset_pin`` arguments are accepted but not wired up yet,
  so the ``output_enable`` property and ``reset()`` currently do nothing.

Dependencies
============

This driver depends on:

* `MicroPython <https://micropython.org/>`_ (uses ``machine`` and ``micropython.const``)

Tested on MicroPython with an I²C bus at 400 kHz. Please ensure the firmware for
your board provides ``machine.I2C`` and ``machine.Pin``.

Installing
==========

Clone or download this repository, then copy the ``pca9955b`` folder to the root
(or ``/lib``) of your board's filesystem, for example with
`mpremote <https://docs.micropython.org/en/latest/reference/mpremote.html>`_:

.. code-block:: shell

    git clone https://github.com/zajili/microPython_PCA9955B.git
    cd microPython_PCA9955B
    mpremote cp -r pca9955b :

The folder contains ``__init__.py``, ``pca9955b.py`` and ``pca9955b_groups.py``.

Usage Example
=============

.. code-block:: python

    import time
    from machine import Pin, I2C
    from pca9955b import PCA9955, LedChannel

    # NOTE: the bus is currently hard-coded inside the driver (see "Current limitations").
    i2c = I2C(1, scl=Pin(3), sda=Pin(2), freq=400_000)

    leds = PCA9955(i2c)  # default address 0x3F

    leds.gain = 0xFF        # output current for all channels
    leds.brightness = 0xFF  # PWM for all channels

    led = leds.channels[0]

    # Full on / off
    led.state = LedChannel.ON
    time.sleep(1)
    led.state = LedChannel.OFF
    time.sleep(1)

    # Individual PWM
    led.state = LedChannel.PWM
    for value in range(256):
        led.brightness = value
        time.sleep_ms(5)

Support
=======

Questions, bug reports and feature requests: please open an
`issue <https://github.com/zajili/microPython_PCA9955B/issues>`_.

Contributing
============

Contributions are welcome! Please open an
`issue <https://github.com/zajili/microPython_PCA9955B/issues>`_ or a pull request.

Credits and License
===================

Original CircuitPython library © 2024-2025 Noel Anderson.
MicroPython port © 2026 zajili.

Released under the MIT License. See ``LICENSE`` for details.
