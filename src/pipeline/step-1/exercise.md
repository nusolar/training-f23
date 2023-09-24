# Exercise - Thinking like a computer

It's time to put your newfound CAN bus knowledge to use. In this exercise, you'll
manually decode a few CAN frames in preparation for later automating this first stage of
the telemetry processing pipeline with Python.

For this exercise, assume we are working on a CAN bus containing the following four
devices:

1. An Elmar Solar MPPT with base address 0x600.
2. An Elmar Solar MPPT with base address 0x610.
3. An Elmar Solar MPPT with base address 0x620.
4. A Tritium WaveSculptor 22 Motor Controller with base address 0x400.

To decode the following CAN frames observed on this network, you will need to
refer to the relevant datasheets for each device, which can be found in the
corresponding sections of the Hardware Reference in this handbook (see the
sidebar on the left).

> ### A note on types
> In the CAN frame specifications for each device, you will find information
> about the "type" of a variable inside a message. For example, the
> "Identification Information" frame for a motor controller is specified as
> containing two variables of type `Uint32`. Similarly, you will also
> find references to `Uint8`, `Uint16`, etc. These types signify that the
> bit pattern in the data field should be parsed as an unsigned (i.e. nonnegative)
> integer, with bit-widths of 32, 8, 16, etc. Unsigned integers are not too hard
> to parse by hand; it's analagous to the discussion in
> [Section 3.1](../step-1/representing-data.md).
>
> You will also see messages where variables are given the type `float` (explicitly in
> the MPPT datasheet; implicitly in the motor controller specs). A `float` is a
> representation of fractional numbers, following the
> [IEEE 754 Standard](https://en.wikipedia.org/wiki/IEEE_754). 1.5, -2.0, 10000000.0, and
> 0.30000000000000004 can all be represented with floating point numbers. Although most
> signals are naturally of type `float` (temperature, velocity, voltage, etc. are not
> going to be integers), parsing `float`s by hand is entirely different than for integers
> and is very tedious. You can use an online converter like
> [this one](https://www.scadacore.com/tools/programming-calculators/online-hex-converter/)
> to decode `float`s from their hex representation (you can omit the "0x" in the converter).
> The result will be in the "little-endian" cell.

## Frames to decode

<div style="display: flex; justify-content: left;">

| Frame ID | Data                 |
| :------: | :------------------: |
| 0x625    | 0x0000000500018028   |

</div>

<br/>

<details>
<summary>Solution</summary>

This is a "Status" message sent by the MPPT at base address 0x620.
The signal values are:

```python
{
    "CAN_RX_error_counter": 0,
    "CAN_TX_error_counter": 0,
    "CAN_TX_overflow_counter": 0,
    "HW_overvoltage": 1,
    "HW_overcurrent": 0,
    "Twelve_V_undervoltage": 0,
    "Batter_full": 0,
    "Battery_low": 0,
    "Mosfet_overheat": 0,
    "Low_array_power": 0,
    "Global_MPPT": 0,
    "Local_MPPT": 0,
    "Duty_cycle_max": 0,
    "Duty_cycle_min": 0,
    "Mosfet_temperature_error": 0,
    "Output_voltage_max": 0,
    "Input_current_max": 0,
    "Input_current_min": 0,
    "Mode": 1,
    "Test_counter": 40
}
```

</details>

---

<br/>

<div style="display: flex; justify-content: left;">

| Frame ID | Data                |
| :------: | :-----------------: |
| 0x409    | 0xDA35EF3F68E75440  |

</div>

<br/>

<details>
<summary>Solution</summary>

This is a "3.3V & 1.9V Voltage Rail Measurement" message sent by the motor
controller at base address 0x400. The signal values are:

```python
{
    "Supply1V9": 1.868830919265747,
    "Supply3V3": 3.3266239166259766
}
```

</details>

---

<br/>

<div style="display: flex; justify-content: left;">

| Frame ID | Data                |
| :------: | :-----------------: |
| 0x612    | 0x506B76410276B141  |

</div>

<br/>

<details>
<summary>Solution</summary>
This is a "Temperature" message sent by the MPPT at base address 0x610.
The signal values are:


```python
{
    "Controller_temperature": 22.182621002197266,
    "Mosfet_temperature": 15.401199340820312
}
```
</details>
