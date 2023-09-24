# Exercise - An automated autopsy

This exercise is broken into two parts. In the first section, you will craft a
Python parser to interpret real CAN frames sent by an isolated MPPT under
idle operation. Values sent by this MPPT will be boring and predictable. In part
two, you'll adapt your parser to handle CAN data from multiple connected devices.
The data in part two will be generated to contain a mystery—at some point in this
fabricated scenario, a fire will break out in the car. Your job is to analyze the
CAN traffic to determine when and where the failure first appeared, and what might
have caused it.

## Part 1 - Isolated MPPT

[This data file](../../misc/mppt_data.txt) contains CAN frames sent by an MPPT on
it's own CAN network. Frames in this file appear in a `[Frame ID] [Data]` text
format, separated by newlines. Frame IDs and data are given in hex, although the
leading "0x" is ommited.

Your job is to use the MPPT datasheet to construct a Python program that processes
this file into something human-readable.

<details>
<summary>Hint</summary>

You may find the
[binascii.unhexlify](https://docs.python.org/3/library/binascii.html#binascii.unhexlify)
and [struct.unpack](https://docs.python.org/3/library/struct.html#struct.unpack)
functions helpful in converting from a hex string to an integer or float.

</details>


## Part 2 - Chaos on the CAN network
This scenario is under development 👷🚧.
