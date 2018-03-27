# Communication overview

The LW316 has two methods of communication:
- Binary packet interface over serial UART.
- Register based I2C interface.

When the LW316 first receives power it will wait for one of the interfaces to be selected. Sending an initial command over either serial or I2C will indicate which interface to activate. See [Serial interface](serial.md) and [I2C interface](i2c.md) for more detail about each.