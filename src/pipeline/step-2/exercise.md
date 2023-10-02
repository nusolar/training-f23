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
leading "0x" is omitted.

Your job is to use the MPPT datasheet to construct a Python program that processes
this file into something human-readable.

---

<details>
<summary><b>Hint</b></summary>

You may find the
[binascii.unhexlify](https://docs.python.org/3/library/binascii.html#binascii.unhexlify)
and [struct.unpack](https://docs.python.org/3/library/struct.html#struct.unpack)
functions helpful in converting from a hex string to an integer or float.

</details>

---

<details>
<summary><b>Solution</b></summary>

There are many possible ways to write the code to solve this exercise, so
you're not expected to have written code that looks the same as this.
This is only one of the possible solutions and is here as a reference.

```python
# `pathlib` is a standard library module for working with paths. It's not at all
# necessary for this exercise, but it tends to be my go-to module when I find
# myself working with file paths.
from pathlib import Path 

# We're going to use the `unhexlify` function to convert hex strings into the
# actual bytes they represent.
from binascii import unhexlify

# We'll also use the `unpack` function to decode `float`s and `int`s from
# raw bytes.
from struct import unpack

def decode_mppt_data(offset, data):
    if offset == 0:
        # Input measurements
        return {
            "Input Voltage (V)": unpack("<f", data[:4])[0],
            "Input Current (A)": unpack("<f", data[4:])[0],
        }
    elif offset == 1:
        # Output measurements
        return {
            "Output Voltage (V)": unpack("<f", data[:4])[0],
            "Output Current (A)": unpack("<f", data[4:])[0],
        }
    elif offset == 2:
        # Temperature
        return {
            "Mosfet Temperature (℃ )"    : unpack("<f", data[:4])[0],
            "Controller Temperature (℃ )": unpack("<f", data[4:])[0],
        }
    elif offset == 3:
        # Auxiliary power supply
        return {
            "12V (V)": unpack("<f", data[:4])[0],
            "3V (V)" : unpack("<f", data[4:])[0],
        }
    elif offset == 4:
        # Limits
        return {
            "Max. Output Voltage (V)": unpack("<f", data[:4])[0],
            "Max. Input Current (C)" : unpack("<f", data[4:])[0],
        }
    elif offset == 5:
        # Status
        return {
            "CAN RX error counter"   : unpack("<B", data[:1])[0],
            "CAN TX error counter"   : unpack("<B", data[1:2])[0],
            "CAN TX overflow counter": unpack("<B", data[2:3])[0],
            "Error flags"            : unpack("<B", data[3:4])[0],
            "Limit flags"            : unpack("<B", data[4:5])[0],
            "Mode"                   : unpack("<B", data[5:6])[0],
            "Test counter"           : unpack("<B", data[7:])[0],
        }
    elif offset == 6:
        # Power connector
        return {
            "Output Voltage (Battery side of fuse) (V)": unpack("<f", data[:4])[0],
            "Power connector Temperature (℃ )" : unpack("<f", data[4:])[0],
        }
    else:
        # Unknown message, throw an error to the caller.
        raise Exception(f"No message matches offset 0x{offset:x}")

def decode_data(base_address, offset, data):
    if base_address == 0x600:
        # If the base address belongs to a known MPPT, decode the data using
        # a MPPT-specific helper function, and return the decoded message.
        return { "sender": base_address, "message": decode_mppt_data(offset, data) } 
    else:
        # Otherwise, the message doesn't match any known device. Throw an error
        # to the caller.
        raise Exception(f"No device matches base address 0x{base_address:x}")

with Path("mppt_data.txt").open("r") as f:
    for line in f:
        # Take the line, remove any trailing whitespace, and then split across
        # the space dividing the frame id from the data.
        left, right = line.strip().split(" ")

        # Convert the frame id hex string into an actual number.
        frame_id = int(left, 16)

        # Convert the data hex string into bytes.
        data = unhexlify(right)

        # Decompose the frame id into a base address and offset.
        base_address, offset = divmod(frame_id, 16)
        base_address *= 16

        # Decode the message and print it.
        print(decode_data(base_address, offset, data))
```

The result of running this program on the MPPT data provided above can
be found in [this output file](../../misc/mppt_data_decoded.txt).

</details>

---

## Part 2 - Chaos on the CAN network
This scenario is under development 👷🚧.
