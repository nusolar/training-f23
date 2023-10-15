# Exercise - Parsing from a distance

To complete your telemetry pipeline, adjust your code to accept CAN frames as packets
over an XBee network instead of from a file. This exercise uses the same CAN
configuration as Part 1 of section 4.2 (one MPPT at base address 0x600).

You will need to review the information in the
[hardware reference for the XBee devices](../../hardware/xbee.md). In
particular, you will need to install the `digi-xbee` Python package.
You can install by running the following command in the terminal:

```bash
$ pip install digi-xbee
```

This will install the `digi-xbee` package in your current Python environment,
which is probably the global environment. Going forward, you will want to
install packages into per-project virtual environments, but installing this
one globally is fine.

You will also need some help setting up the XBee on your laptop and coordinating
with another laptop sending the CAN traffic.
