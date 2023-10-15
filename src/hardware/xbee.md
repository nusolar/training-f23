# Digi XBee® SX 1-Watt 900 MHz RF

![Xbee Wireless Transmitter](../images/xbee.jpg)

The Digi XBee RF modems are the wireless transmitters that allow for
communication between the car and the base station. We have two XBee
devices:

| Device ID | Baud Rate (bits per second) |
| --------- | --------------------------- |
| Router    | 9600                        |
| Node      | 57600                       |


Each XBee is limited to sending no more than 256 bytes of data per message.

Information for controlling XBee with Python can be found in the
[documentation for the XBee Python Library](https://xbplib.readthedocs.io/en/latest/).
Which is available on PyPI as the [digi-xbee](https://pypi.org/project/digi-xbee/)
package.

## Port identification

To set up an XBee for sending or receiving, you'll need to identify the port that
the XBee has attached to.

### macOS
Run the following command in the terminal:

```bash
$ ls /dev/tty.*
```

You might see a few different ports listed (Bluetooth, for example). The one that
contains "usbserial" is the XBee. For example, the port may look like `/dev/tty.usbserial-A21SPQED`.

### Windows
Open the command prompt and type `mode`. You should the XBee on one of the COM ports (`COM1`, `COM2`, etc.).

If you are using WSL2, you'll have to set up USB passthrough and identify the XBee from within the Linux
subsytem.
