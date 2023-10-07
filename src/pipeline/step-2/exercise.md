# Exercise - An automated autopsy

This exercise is broken into two parts. In the first section, you will craft a
Python parser to interpret real CAN frames sent by an isolated MPPT under
idle operation. Values sent by this MPPT will be boring and predictable. In part
two, you'll adapt your parser to handle CAN data from multiple connected devices.
The data in part two will be generated to contain a mystery—at some point in this
fabricated scenario, a failure will occur in the car. Your job is to analyze the
CAN traffic to determine when and where the failure first appeared, and what might
have caused it.

## Part 1 - Isolated MPPT

[This data file](../../misc/mppt_data.txt) contains CAN frames sent by an MPPT
(base address 0x600) on it's own CAN network. Frames in this file appear in a
`[Frame ID] [Data]` text format, separated by newlines. Frame IDs and data are
given in hex, although the leading "0x" is omitted.

Your job is to use the MPPT datasheet to construct a Python program that processes
this file into something human-readable.

Remember that variables in CAN frames are stored in little-endian byte-order.

---

<details>
<summary><b>Hint</b></summary>

You may find the
[binascii.unhexlify](https://docs.python.org/3/library/binascii.html#binascii.unhexlify)
and [struct.unpack](https://docs.python.org/3/library/struct.html#struct.unpack)
functions helpful in converting from a hex string to an integer or float.

You can also use `int(some_hex_string, 16)` to parse a hex string to an integer. This
can also be done with `unpack` and `unhexlify`, but `unhexlify` requires an even number
of hex digits, so it can be a bit nicer to just use `int` directly for, say, the frame
id, which is 3 hex digits.

</details>

---

<details>
<summary><b>Example</b></summary>

The first line in the data file is `604 6666DA420000E040`. We can recognize this as a
frame sent by the MPPT at base address 0x600 with an id offset of 0x4.

The relevant section of the MPPT datasheet tells us that this is the "Limits" message,
which contains the following variables.

| Variable            | Byte | Type  | Unit   |
| ------------------- | ---- | ----- | ------ |
| Max. Output Voltage | 0-3  | FLOAT | Volt   |
| Max. Input Current  | 4-7  | FLOAT | Ampere |

Here's how we can parse this frame in Python.

Let's start by storing the first frame in a variable and then splitting it
into the frame id and the data:

```python
raw_frame = "604 6666DA420000E040"
frame_id, data = raw_frame.split(" ")
```

The `frame_id, data = ...` syntax we used above is called "unpacking" or
"destructing", and it is a clean way of breaking down a list (or any iterable)
into multiple variables. In this case, `raw_frame.split(" ")` returned a list
of 2 strings, the first of which we assigned to `frame_id`, and the second to
`data`. It would have been equivalent to write:

```python
split = raw_frame.split(" ")
frame_id = split[0]
data = split[1]
```

At this point, both the `frame_id` and `data` variable are strings (text).
Because in general we need to examine the offset of the frame id from the
base address to determine the message type, we will need to convert `frame_id`
to an integer. Similarly, we will need to end up converting portions of
data into the types specified by the MPPT datasheet.

To convert `frame_id` into an integer, we can use
[Python's builtin `int`](https://docs.python.org/3/library/functions.html#int)
function with two arguments to parse a string into an integer.

```python
frame_id = int(frame_id, 16)
```

We can then determine the offset from the base address programmatically:

```python
offset = frame_id - 0x600
```

Notice that Python allows you to write integers directly in hex using the `0x` prefix.

Of course, in this case we already know that the offset is 4. But, in general, you'll
need to determine the offset and then examine the data accordingly. Let's say we're
choosing to do this in one big `if ... elif ... elif ... else` block.

```python
if offset == 0:
    ... # handle offset 0
elif offset == 1
    ... # handle offset 1

... # handle more offsets

elif offset == 4
    ... # handle offset 4

... # handle more offsets
```

For the frame we're examining in this example, we know that we're going to end up inside
the branch for `offset == 4`. The MPPT specs tell us that a broadcast message with offset
4 contains two 32-bit floats in the data field. We can break the data into halves using
Python's slicing syntax:

```python
first_half = data[:8]
second_half = data[8:]
```

And then we can use `unhexlify` to convert each half from a string to the represented bytes:
```python
from binascii import unhexlify

first_half = unhexlify(first_half)
second_half = unhexlify(second_half)
```

Finally, we can use `unpack` to decode the bytes into little-endian floats (the `"<f"`
given to `unpack` is a format specifier meaning little-endian 32-bit float; you can find
more info in the docs for `unpack`).

```python
from struct import unpack

max_output_voltage = unpack("<f", first_half)[0]
max_input_current = unpack("<f", second_half)[0]
```

At this point, we've decoded the CAN frame! You can now print the decoded information to
the screen, append it to a list, store it in a dictionary, or use whatever format you
think works best.

</details>

---

<details>
<summary><b>Solution</b></summary>

There are many possible ways to write the code to solve this exercise, so
you're not expected to have written code that looks the same as this.
This is only one of the possible solutions and is here as a reference.

```python
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
            "Mosfet Temperature (deg. Celsius)"    : unpack("<f", data[:4])[0],
            "Controller Temperature (deg. Celsius)": unpack("<f", data[4:])[0],
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
            "Power connector Temperature (deg. Celsius)" : unpack("<f", data[4:])[0],
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

with open("mppt_data.txt", "r") as f:
    for line in f:
        # Take the line, remove any leading/trailing whitespace,
        # and then split across the space dividing the frame id from the data.
        left, right = line.strip().split(" ")

        # Convert the frame id hex string into an actual number.
        # Note that `int(left, 16)` is equivalent to
        # `unpack("<B", unhexlify(left))[0]`.
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

In this exercise, you'll will be given a dump of CAN traffic from the following
made-up scenario.

> Because the CAN data for this scenario has been fabricated, you shouldn't expect
> to see super-realistic and -consistent data reported across all the devices on
> the network. Rather, the tampered data has been made in a way to (hopefully) make
> localizing a fault easier.

While working on an electrical line in SC7s, members of the Electrical team heard a pop
from within the car. A few seconds later, the top-shell was lifted, and one of the devices
attached to the CAN line was found damaged and unresponsive. The car was then immediately
powered off.

This incident occurred during winter, while the car was idle (not driving) and being worked-on
in North Garage. The Electrical team reported the following CAN network configuration:
* MPPT at base address 0x600
* MPPT at base address 0x610
* MPPT at base address 0x620
* Motor controller at base address 0x400
* Driver controls at base address 0x500
* Battery management system

They were also able to provide
[this log of CAN traffic in the minutes leading up to the incident](../../misc/can_traffic.txt).

__By analyzing this CAN traffic, can you figure out which device was damaged, what type of damage
was sustained, and may have caused the failure?__

## Advice

Many of the messages in the given data file come from the battery management system (BMS), which
works a little differently than the other devices you are familiar with. CAN frames broadcast
from the BMS are programmable and do not necessarily follow the base address + offset pattern
used by the other devices. For this exercise, __the BMS is not relevant to electrical failure__,
which means you should ignore any messages from the BMS (i.e. the ones that are not from
the MPPTs, motor controller, or driver controls).

You may also not need to decode every frame from the other devices. It's possible to complete
this exercise by identifying oddities in the raw frames, and limiting your focus only to those
oddities.
