# Serial interface

## Overview

The serial interface has the following properties:
- Baud rate: `921600`
- Data: `8 bit`
- Parity: `none`
- Stop: `1 bit`
- Flow control: `none`

Communication is performed using encapsulated packets for both sending and receiving data. Every packet that is sent to the LW316 is known as a `request` and a correctly formatted request will always be replied to with a `reponse`. There are cases where the LW316 will send a request packet to the host, these packets are considered `streaming` packets as they can arrive continuously without a direct request from the host - they do not require a response.

Requests are made using one of the available [commands](commands.md) and are either flagged as `read` or `write`. When a read request is issued then the response will contain the requested data. When a write request is issued then the contents of the response will vary depending on the command.

## Packets

A packet for both requests and responses is composed of the following bytes:

<table>
    <tr>
        <td></td>
        <td colspan="3">Header</td>
        <td colspan="2">Payload</td>
        <td colspan="2">Checksum</td>
    </tr>
    <tr>
        <td>**Byte:**</td>
        <td>Start</td>
        <td>Flags Low</td>
        <td>Flags High</td>
        <td>ID</td>
        <td>Data 0 .. N</td>
        <td>CRC Low</td>
        <td>CRC High</td>
    </tr>
</table>

The `Start` byte is always 0xFE and indicates the beginning of a packet. It is important to veryify that the payload length is sane (0 to 1023 bytes) and that the checksum is valid before processing a packet, rather than just relying on the start byte.

The `Flags` bytes form a 16 bit integer that represents the payload length and read/write status of the packet. The payload length is inclusive of the ID byte and the required number of data bytes. The write bit is set to `1` to indicate write mode, or `0` to indicate read mode.

<table>
    <tr>
        <td>**Bit:**</td>
        <td>15</td><td>14</td><td>13</td><td>12</td><td>11</td><td>10</td><td>9</td><td>8</td><td>7</td><td>6</td><td>5</td><td>4</td><td>3</td><td>2</td><td>1</td><td>0</td>
    </tr>
    <tr>
        <td></td>
        <td colspan="10">Payload length (0 to 1023)</td>
        <td colspan="5">Reserved</td>
        <td>W</td>
    </tr>
</table>

The `ID` byte represents which command the request/response relates to.

There will be between 0 and 1023 `Data` bytes depending on the command type. Each command under the [detailed command descriptions](command_detail.md) section documents how the data bytes are used.

The `CRC` bytes form a 16 bit checksum value used to validate the integrity of the packet data. Every byte in the packet except for the CRC itself is included in the checkum calculation.

## Checksum
Each packet has a 2 byte checksum which is used to validate data integrity. The algorithm is `CRC-16-CCITT 0x1021` (idendtical to the one used for the XMODEM protocol).

The CRC must be correctly formed for the LW316 to accept and process packets. Below are some examples in various languages for CRC calculation:

**C/C++**
```c
uint16_t createCRC(uint8_t* Data, uint16_t Size)
{
    uint16_t crc = 0;

    for (uint32_t i = 0; i < Size; ++i)
    {
        uint16_t code = crc >> 8;
        code ^= Data[i];
        code ^= code >> 4;
        crc = crc << 8;
        crc ^= code;
        code = code << 5;
        crc ^= code;
        code = code << 7;
        crc ^= code;
    }

    return crc;
}
```

**Javascript**
```javascript
function createCRC(data, size) {
    let crc = 0;

    for (let i = 0; i < size; ++i) {
        let code = crc >>> 8 & 0xFF;
        code ^= data[i] & 0xFF;
        code ^= code >>> 4;
        crc = crc << 8 & 0xFFFF;
        crc ^= code;
        code = code << 5 & 0xFFFF;
        crc ^= code;
        code = code << 7 & 0xFFFF;
        crc ^= code;
    }

    return crc;
}
```
## Receiving packets

Here is the process for reading the raw serial byte stream and identifying packets. Once a packet has been succesfully read it can be processed based on its command ID.

![alt text](images/packet_reading.png "Request/response flowchart")

!> If the packet length or checksum is invalid then instead of continuing to look for the next start byte, it is possible to roll back to after the failed packet's start byte was found and continue from there. This method is technically more correct, however in practice has little appreciable impact.

## Handling request & response

Every request sent to the LW316 will receive a response, it is often useful to use the response as a way to determine if the request was received and processed.
Here is the recommended procedure for sending a command request and reading the response:

![alt text](images/serial_request_response_flow.png "Request/response flowchart")

> The values used for timeout or number of retries should be tuned to the specific application.

## Initiating communication

The LW316 boots up in a communication agnostic mode and a single serial packet is required to initiate the serial interface. Here is the recommended procedure for establishing serial communication with the LW316:

![alt text](images/initiate_serial.png "Initiate flowchart")