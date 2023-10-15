# Exercise - Persistence is key

For this exercise, use the CAN traffic parser you wrote in part 1 of
[section 4.2](../step-2/exercise.md) to parse same
[MPPT traffic log](../../misc/mppt_data.txt) as before—but now adapt your
code to store the CAN traffic in an SQLite database instead of printing to
the screen.

This exercise is very free-form. Choose whatever table structure you think
makes the most sense. Feel free to ignore the MPPT status message it's too
much trouble; certainly don't bother breaking down the error/limit flags
into individual bits.

> ### Why not a regular file?
> You might still be wondering what the point of using an SQL database is.
> After all, we could simply write the decoded CAN frames into a regular
> text file. The answer is that using an SQL database enforces structure
> of the decoded messages and integrates with observability platforms like
> Grafana. Also, databases are *much* more efficient than regular files
> when the amount of data gets very large.

## Advice
You might find it difficult to arrange all the data into just one table.
Since each CAN frame only contains a fraction of all the variables reported
by the MPPT, you would have to either leave some parts of each row empty,
leave parts empty initially but update them as you go along, or gather
the data until you have enough to insert a full row.

The one big table solution is possible, but complicates the problem more than
necessary. What if you tried creating a table for each CAN frame sent by the MPPT?


---
<details>
<summary>Solution</summary>


This solution is adapted from the solution in Part 1 of section 4.2.

```python
# We're going to use the `unhexlify` function to convert hex strings into the
# actual bytes they represent.
from binascii import unhexlify

# We'll also use the `unpack` function to decode `float`s and `int`s from
# raw bytes.
from struct import unpack

# We'll store the decoded data into an SQLite database
import sqlite3

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
        return {
            "sender" : base_address,
            "offset" : offset,
            "message": decode_mppt_data(offset, data)
        } 
    else:
        # Otherwise, the message doesn't match any known device. Throw an error
        # to the caller.
        raise Exception(f"No device matches base address 0x{base_address:x}")

with open("mppt_data.txt", "r") as f:
    # Connect to the database, creating it if it doesn't already exist.
    con = sqlite3.connect("decoded.db")

    table_names = [
        "input_measurements",
        "output_measurements",
        "temperature",
        "auxiliary_power_supply",
        "limits",
        "status",
        "power_connector"
    ]

    # Create the tables if they don't already exist. Note that this won't
    # clear them if they already exist.
    con.execute(f"CREATE TABLE IF NOT EXISTS {table_names[0]}(input_voltage REAL, input_current REAL);")
    con.execute(f"CREATE TABLE IF NOT EXISTS {table_names[1]}(output_voltage REAL, output_current REAL);")
    con.execute(f"CREATE TABLE IF NOT EXISTS {table_names[2]}(mosfet_temperature REAL, controller_temperature REAL);")
    con.execute(f"CREATE TABLE IF NOT EXISTS {table_names[3]}(twelve_v REAL, three_v REAL);")
    con.execute(f"CREATE TABLE IF NOT EXISTS {table_names[4]}(max_output_voltage REAL, max_input_current REAL);")
    con.execute(f"""
        CREATE TABLE IF NOT EXISTS {table_names[5]}(
            rx_error_counter INTEGER,
            tx_error_counter INTEGER,
            tx_overflow_counter INTEGER,
            error_flags INTEGER,
            limit_flags INTEGER,
            mode INTEGER,
            test_counter INTEGER
        );
    """)
    con.execute(f"CREATE TABLE IF NOT EXISTS {table_names[6]}(output_voltage REAL, power_connector_temperature REAL);")

    for line in f:
        # Take the line, remove any leading/trailing whitespace,
        # and then split across the space dividing the frame id from the data.
        left, right = line.strip().split(" ")

        # Convert the frame id hex string into an actual number.
        frame_id = int(left, 16)

        # Convert the data hex string into bytes.
        data = unhexlify(right)

        # Decompose the frame id into a base address and offset.
        base_address, offset = divmod(frame_id, 16)
        base_address *= 16

        # Decode the message
        decoded = decode_data(base_address, offset, data)

        # Find the right table for this message
        table_name = table_names[decoded["offset"]]

        # Get the list of values that will be the row added to the table
        values = decoded["message"].values()
        with con:
            # Insert the row into the right table
            con.execute(f"INSERT INTO {table_name} VALUES ({','.join(map(str, values))})")
```

</details>

---

