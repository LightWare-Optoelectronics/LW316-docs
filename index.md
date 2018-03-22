---
title: "LW316 documentation"
keywords: sample homepage
sidebar: mydoc_sidebar
permalink: index.html
summary: These brief instructions will help you get started quickly with the theme. The other topics in this help provide additional information and detail about working with other aspects of this theme and Jekyll.
toc: false
---

# LW316 documentation

## 1. Version support
Hardware and firmware versions of the LW316 that this document applies to.

|Product|Hardware|Firmware|Supported|
|----|--------|--------|:-------:|
|LW316|0       |0.0.0   |-       |
|LW316|0       |0.1.0   |-       |
|LW316|0       |0.2.0   |Yes      |

## 2. Communication workflow

### 2.1 Overview

The LWNx devices have two methods of communication, binary packet interface over ttl uart and a register based I2C interface. The binary packet structure unifies the previous HMI, MMI ascii and MMI binary architecture into a single interface.

After powerup of the LW316 you need to send an initial command over either serial or i2c to indicate which interface you are using. This first command will not get a response. I recommend doing a read for the product name (Command ID 0) until the appropriate response is received, at which point you can be guaranteed that correct interface has been activated.

Read vs Writing

### 2.2 Serial interface

3V to 5V compat? The LW316 has a fixed Baudrate of 921600, which is required for the data streaming. Future versions of firmware will allow a selectable baud rate.
Use packets to communicate, see: 

Communication over this interface is strictly done with binary capable packets.

Request/response vs streaming.
Reading and writing are set with a bit in the binary request packet.

### 2.3 I2C interface

The LW316 does not automaticaly increment the register address after an i2c read command. You must explicitly do an i2c read command for each register you want to read.

Data returned for the Uint32 and Uint16 types is little-endian.

## 3. Commands

### 3.1 Overview
|ID|Name|Description|RW|Read bytes|Write bytes|
|---|---|---|---|---|---|
|0|Product name|Product name|R|16|-|
|1|Hardware version|Hardware revision|R|4|-|
|2|Firmware version|Firmware revision|R|4|-|
|3|Serial number|Serial number|R|16|-|
|4|User data|Free 16 byte field for user data.|RW|16|16|

### 3.2 Command detail

#### 3.2.1 [0] Product name
Read a 16 byte string indicating the product model name. This will always be `LW316` followed by a null terminator. You can use this to verify the LW316 is connected and operational over the selected interface. When the bootloader is active this will return `BOOT` followed by a null terminator.

#### 3.2.2 [1] Hardware version
A simple `uint32` which represents the current hardware configuration. Different versions of firmware will be available for different hardware revisions.

#### 3.2.3 [2] Firmware version
Semver stored in a `uint32`. Major, minor, patch. Used to determine API compatability. The `Version support` section at the top of this document details which firmware versions this document applies to.

## 5. Serial binary packets

Composition

Packet
| Start | Flags L | Flags H | ID | Data N | CRC L | CRC H |

| Bytes: | 1 | 2 | 1 | N (0 .. 1000) | 2 |
|-------|---|---|---|---|---|
|-| Start | Flags | ID | Data | CRC |

**Packet composition**

${{
    'type': 'pkt-dgram',
    'name': 'Packet',
    'bytes': [
        {
            'name': 'Start',
            'size': 2
        }
    ]	
}}

| Name | Description | Size | Data type |
|------|-------------|------|-----------|
| Start | Always (0xFE)| 1 | uint8 |
| Flags | Flag composition here that details things| 2 | uint16 |

### CRC code examples
Some stuff about the crc. CRC 16 CCITT 0x1021

#### C/C++
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

#### Javascript
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

## 4. Updating the firmware

Download LightWare Studio. Use firmware management utility to download and update.

> **NOTE:** You can use `keywords`.
