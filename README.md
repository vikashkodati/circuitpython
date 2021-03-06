# Adafruit CircuitPython

[![Build Status](https://travis-ci.org/adafruit/circuitpython.svg?branch=master)](https://travis-ci.org/adafruit/circuitpython)
[![Doc Status](https://readthedocs.org/projects/circuitpython/badge/?version=latest)](http://circuitpython.readthedocs.io/)
[![Gitter](https://badges.gitter.im/adafruit/circuitpython.svg)](https://gitter.im/adafruit/circuitpython?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)
[![Discord](https://img.shields.io/discord/327254708534116352.svg)](https://discord.gg/nBQh6qu)

**[Status](#status)** |
**[Supported Boards](#supported-boards)** |
**[Download](#download)** |
**[Documentation](#documentation)** |
**[Contributing](#contributing)** |
**[Differences from Micropython](#differences-from-micropython)** |
**[Project Structure](#project-structure)**

**Adafruit CircuitPython** is an open source derivative of
[MicroPython](http://www.micropython.org) for use on educational
development boards designed and sold by [Adafruit](https://www.adafruit.com).

**CircuitPython**, a MicroPython derivative, implements Python 3.x on
microcontrollers such as the SAMD21 and ESP8266.

## Status

This project is in beta. Most APIs should be stable going forward.

## Supported Boards

### Designed for CircuitPython

* [Adafruit CircuitPlayground Express][]
* [Adafruit Feather M0 Express][]
* [Adafruit Metro M0 Express][]
* [Adafruit Gemma M0][]
* [Adafruit Trinket M0][]

### Other

* [Adafruit Feather HUZZAH][]
* [Adafruit Feather M0 Basic][]
* [Adafruit Feather M0 Bluefruit LE][] (uses M0 Basic binaries)
* [Adafruit Feather M0 Adalogger][] (MicroSD card supported using the [Adafruit CircuitPython SD library](https://github.com/adafruit/Adafruit_CircuitPython_SD))
* [Arduino Zero][]

## Download

Official binaries are available through the
[latest GitHub releases](https://github.com/adafruit/circuitpython/releases).
Continuous (one per commit) builds are available
[here](https://adafruit-circuit-python.s3.amazonaws.com/index.html?prefix=bin)
and includes experimental hardware support.

## Documentation

Guides and videos are available through the [Adafruit Learning System](https://learn.adafruit.com/)
under the [CircuitPython category](https://learn.adafruit.com/category/circuitpython)
and [MicroPython category](https://learn.adafruit.com/category/micropython).
An API reference is also available on [Read the Docs](http://circuitpython.readthedocs.io/en/latest/?).

## Contributing

See [CONTRIBUTING.md](https://github.com/adafruit/circuitpython/blob/master/CONTRIBUTING.md)
for full guidelines but please be aware that by contributing to this
project you are agreeing to the [Code of Conduct][]. Contributors who
follow the [Code of Conduct][] are welcome to submit pull requests and
they will be promptly reviewed by project admins. Please join the
[Gitter chat](https://gitter.im/adafruit/circuitpython) or
[Discord](https://discord.gg/nBQh6qu) too.

---

## Differences from [MicroPython][]

CircuitPython:

* includes a port for Atmel SAMD21 (Commonly known as M0
  in Adafruit product names.)
* supports only Atmel SAMD21 and ESP8266 ports.
* tracks MicroPython's releases (not master).

### Behavior

* The order that files are run and the state that is shared between them.
  CircuitPython's goal is to clarify the role of each file and make each
  file independent from each other.
  * `boot.py` (or `settings.py`) runs only once on start up before
    USB is initialized. This lays the ground work for configuring USB
    at startup rather than it being fixed. Since serial is not
    available, output is written to `boot_out.txt`.
  * `code.py` (or `main.py`) is run after every reload until it
    finishes or is interrupted. After it is done running, the vm and hardware is
    reinitialized. **This means you cannot read state from `code.py`
    in the REPL anymore.** CircuitPython's goal for this change includes
    reduce confusion about pins and memory being used.
  * After `code.py` the REPL can be entered by pressing any key. It no
    longer shares state with `code.py` so it is a fresh vm.
  * Autoreload state will be maintained across reload.
* Adds a safe mode that does not run user code after a hard crash or
  brown out. The hope is that this will make it easier to fix code that
  causes nasty crashes by making it available through mass storage after
  the crash. A reset (the button) is needed after its fixed to get back
  into normal mode.

### API

* Unified hardware APIs:
  [`audioio`](https://circuitpython.readthedocs.io/en/latest/shared-bindings/audioio/__init__.html),
  [`analogio`](https://circuitpython.readthedocs.io/en/latest/shared-bindings/analogio/__init__.html),
  [`busio`](https://circuitpython.readthedocs.io/en/latest/shared-bindings/busio/__init__.html),
  [`digitalio`](https://circuitpython.readthedocs.io/en/latest/shared-bindings/digitalio/__init__.html),
  [`pulseio`](https://circuitpython.readthedocs.io/en/latest/shared-bindings/pulseio/__init__.html),
  [`touchio`](https://circuitpython.readthedocs.io/en/latest/shared-bindings/touchio/__init__.html),
  [`microcontroller`](https://circuitpython.readthedocs.io/en/latest/shared-bindings/microcontroller/__init__.html),
  [`board`](https://circuitpython.readthedocs.io/en/latest/shared-bindings/board/__init__.html),
  [`bitbangio`](https://circuitpython.readthedocs.io/en/latest/shared-bindings/bitbangio/__init__.html) (Only available on atmel-samd21 and ESP8266 currently.)
* No `machine` API on Atmel SAMD21 port.

### Modules

* No module aliasing. (`uos` and `utime` are not available as `os` and
  `time` respectively.) Instead `os`, `time`, and `random` are CPython
  compatible.
* New `storage` module which manages file system mounts. (Functionality
  from `uos` in MicroPython.)
* Modules with a CPython counterpart, such as `time`, `os` and `random`,
  are strict [subsets](https://circuitpython.readthedocs.io/en/latest/shared-bindings/time/__init__.html)
  of their [CPython version](https://docs.python.org/3.4/library/time.html?highlight=time#module-time).
  Therefore, code from CircuitPython is runnable on CPython but not
  necessarily the reverse.
* tick count is available as [`time.monotonic()`](https://circuitpython.readthedocs.io/en/latest/shared-bindings/time/__init__.html#time.monotonic)

### atmel-samd21 features

* RGB status LED
* Auto-reload after file write over mass storage. (Disable with
  `samd.disable_autoreload()`)
* Wait state after boot and main run, before REPL.
* Main is one of these: `code.txt`, `code.py`, `main.py`, `main.txt`
* Boot is one of these: `settings.txt`, `settings.py`, `boot.py`,
  `boot.txt`

---

## Project Structure

Here is an overview of the top-level source code directories.

### Core

The core code of [MicroPython][] is shared amongst ports including
CircuitPython:

* `docs` High level user documentation in Sphinx reStructuredText
  format.
* `drivers` External device drivers written in Python.
* `examples` A few example Python scripts.
* `extmod` Shared C code used in multiple ports' modules.
* `lib` Shared core C code including externally developed libraries such
  as FATFS.
* `logo` The MicroPython logo.
* `mpy-cross` A cross compiler that converts Python files to byte code
  prior to being run in MicroPython. Useful for reducing library size.
* `py` Core Python implementation, including compiler, runtime, and
  core library.
* `shared-bindings` Shared definition of Python modules, their docs and
  backing C APIs. Ports must implement the C API to support the
  corresponding module.
* `shared-module` Shared implementation of Python modules that may be
  based on `common-hal`.
* `supervisor` Code and headers related to the supervisor which monitors and
  runs individual MicroPython virtual machines (from code in `py`).
* `tests` Test framework and test scripts.
* `tools` Various tools, including the pyboard.py module.

### Ports

Ports include the code unique to a microcontroller line and also
variations based on the board.

* `ports/atmel-samd` Support for SAMD21 based boards such as [Arduino Zero][],
  [Adafruit Feather M0 Basic][], and [Adafruit Feather M0 Bluefruit LE][].
* `ports/bare-arm` A bare minimum version of MicroPython for ARM MCUs.
* `ports/cc3200` Support for boards based [CC3200](http://www.ti.com/product/CC3200)
  from TI such as the [WiPy 1.0](https://www.pycom.io/solutions/py-boards/wipy1/).
* `ports/esp8266` Support for boards based on ESP8266 WiFi modules such as the
  [Adafruit Feather HUZZAH][].
* `ports/minimal` A minimal MicroPython port. Start with this if you want
  to port MicroPython to another microcontroller.
* `ports/pic16bit` Support for 16-bit PIC microcontrollers.
* `ports/qemu-arm` Support for ARM emulation through [QEMU](https://qemu.org).
* `ports/stm32` Support for boards based on STM32 microcontrollers including
  the MicroPython flagship [PyBoard](https://store.micropython.org/store/#/products/PYBv1_1).
* `ports/teensy` Support for the Teensy line of boards such as the
  [Teensy 3.1](https://www.pjrc.com/teensy/teensy31.html).
* `ports/unix` Support for UNIX.
* `ports/windows` Support for [Windows](https://www.microsoft.com/en-us/windows/).
* `ports/zephyr` Support for [Zephyr](https://www.zephyrproject.org/), a
  real-time operating system by the Linux Foundation.

CircuitPython only maintains the `atmel-samd` and `esp8266` ports. The
rest are here to maintain compatibility with the
[MicroPython][] parent project.

**[⬆ back to top](#adafruit-circuitpython)**

[Adafruit CircuitPlayground Express]: https://www.adafruit.com/product/3333
[Adafruit Feather M0 Express]: https://www.adafruit.com/product/3403
[Adafruit Metro M0 Express]: https://www.adafruit.com/product/3505
[Adafruit Gemma M0]: https://www.adafruit.com/product/3501
[Adafruit Trinket M0]: https://www.adafruit.com/product/3500
[Adafruit Feather HUZZAH]: https://www.adafruit.com/products/2821
[Adafruit Feather M0 Basic]: https://www.adafruit.com/products/2772
[Adafruit Feather M0 Bluefruit LE]: https://www.adafruit.com/products/2995
[Adafruit Feather M0 Adalogger]: https://www.adafruit.com/product/2796
[Arduino Zero]: https://www.arduino.cc/en/Main/ArduinoBoardZero
[MicroPython]: https://github.com/micropython/micropython
[Code of Conduct]: https://github.com/adafruit/circuitpython/blob/master/CODE_OF_CONDUCT.md
