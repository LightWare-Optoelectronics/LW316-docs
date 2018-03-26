# Detailed command descriptions

---
## 0. Product name


A `16 byte string` indicating the product model name. This will always be `LW316` followed by a null terminator. You can use this to verify the LW316 is connected and operational over the selected interface. When the bootloader is active this will return `BOOT` followed by a null terminator.

|Read|Write|Persists|
|---|---|---|
| `16 byte string` | - | - |

---
## 1. Hardware version

The hardware revision number as a `uint32`.

|Read|Write|Persists|
|---|---|---|
| `uint32` | - | - |

---
## 2. Firmware version

The version of currently installed firmware represented as `4 bytes`. This can be used to identify the product for API compatability. The [product support](/?id=product-support) section details which firmware versions this document applies to.

| 1 | 2 | 3 | 4 |
|---|---|---|---|
|Patch|Minor|Major|Reserved|

|Read|Write|Persists|
|---|---|---|
| `4 bytes` | - | - |

---
## 3. Serial number

A `16 byte string` (null terminated) of the serial identifier assigned during production.

|Read|Write|Persists|
|---|---|---|
| `16 byte string` | - | - |

---
## 4. Command count

Number of available command descriptors. Note that this includes reserved command IDs. Command descriptors can be read/requested using the `Command descriptor` or `Get all descriptors` commands.

|Read|Write|Persists|
|---|---|---|
| `uint8` | - | - |

---
## 5. Command descriptor

Reading the `Command descriptor` will return a `512 byte string` (null terminated) which contains JSON describing the command with the ID that was last written to `Command descriptor`.

!> Note that the serial interface will trim the byte output to the null terminator instead of emitting all 512 bytes, however over I2C all 512 bytes should be read to avoid missing the end of the descriptor.

An example of a JSON descriptor:
```json
{
	"id": 0,
	"name": "Product name",
	"cat": "Product",
	"access": "",
	"read": {
		"type": "",
		"value": ""
	},
	"write" : {
		"type": ""
	}
}

```

!> Fill in JSON types

Writing a `uint8` to the `Command descriptor` will set the ID of the next command to be described.

|Read|Write|Persists|
|---|---|---|
| `512 byte string` | `uint8` | - |

---
## 6. Get all descriptors

Writing this command will instruct the LW316 to send a `Command descriptor` for all available commands, including reserved ones.

|Read|Write|Persists|
|---|---|---|
| - | `0 bytes` | - |

---
## 7. UTF8 text message

*Serial interface only*

A null terminated ASCII string. The LW316 will send this command when it needs to communicate a human readable message.

|Read|Write|Persists|
|---|---|---|
| - | - | - |

---
## 9. User data

This command allows 16 bytes to be stored and read for any purpose.

|Read|Write|Persists|
|---|---|---|
| `16 bytes` | `16 bytes` | Yes |

---
## 10. Token

Current safety token required for saving parameters or starting the bootloader. Once a token has been used it will expire and a new token is created. 

|Read|Write|Persists|
|---|---|---|
| `uint16` | - | - |

---
## 11. Start bootloader

The bootloader enables user applications to perform firmware upgrades.

Writing the current safety token with this command will start the bootloader.

|Read|Write|Persists|
|---|---|---|
| - | `uint16` | - |

---
## 12. Save parameters

Several commands write to parameters that can persist across power cycles. These parameters will only persist once the `Save parameters` command has been written with the appropriate `token`. The safety token is used to prevent unintentional writes and once a successful save has completed the token will expire.

|Read|Write|Persists|
|---|---|---|
| - | `uint16` | - |

---
## 30. Stream

The LW316 can continuously output data without individual request commands being issued. Reading from the `Stream` command will indicate what type of data is being streamed. Writing to the `Stream` command will set the type of data to be streamed.

|Value|Streamed data|
|---|---|
| 0 | disabled |
| 1 | [40. Distance data](command_detail?id=_40-distance-data) |

!> Streaming commands will typically only output on the serial interface. While it is possible to read and write the Stream command over I2C, the resulting streamed data will not be retrievable.

|Read|Write|Persists|
|---|---|---|
| `uint8` | `uint8` | No |

---
## 31. Statistics

Reading `Statistics` will retrieve a `uint8` indicating the state of statistics streaming. Writing to `Statistics` will enable/disable streaming of [35. Statistics data](command_detail?id=_35-statistics-data) packets.

|Value|Streamed data|
|---|---|
| 0 | disabled |
| 1 | enabled |

!> Streaming commands will typically only output on the serial interface. While it is possible to read and write the Statistics command over I2C, the resulting streamed data will not be retrievable.

|Read|Write|Persists|
|---|---|---|
| `uint8` | `uint8` | No |

---
## 35. Statistics data

*Serial interface only*

Statistics data is primarily used for debugging.

**8 bytes of data:**

|Offset|Type|Description|
|------|----|-----------|
|0x00 | `uint16` | Temperature in 100ths of a degree |
|0x02 | `uint16` | Bias voltage [mV] |
|0x04 | `uint16` | Bias voltage target [mV] |
|0x06 | `uint16` | Noise counts |

|Read|Write|Persists|
|---|---|---|
| - | - | - |

---
## 40. Distance data

*Serial interface only*

This command contains distance data for each beam.

**32 bytes of data:**

|Offset|Type|Description|
|------|----|-----------|
|0x00 | `uint16` | Beam 0 distance [mm] |
|0x02 | `uint16` | Beam 1 distance [mm] |
|0x04 | `uint16` | Beam 2 distance [mm] |
|0x06 | `uint16` | Beam 3 distance [mm] |
|0x08 | `uint16` | Beam 4 distance [mm] |
|0x0A | `uint16` | Beam 5 distance [mm] |
|0x0C | `uint16` | Beam 6 distance [mm] |
|0x0E | `uint16` | Beam 7 distance [mm] |
|0x10 | `uint16` | Beam 8 distance [mm] |
|0x12 | `uint16` | Beam 9 distance [mm] |
|0x14 | `uint16` | Beam 10 distance [mm] |
|0x16 | `uint16` | Beam 11 distance [mm] |
|0x18 | `uint16` | Beam 12 distance [mm] |
|0x1A | `uint16` | Beam 13 distance [mm] |
|0x1C | `uint16` | Beam 14 distance [mm] |
|0x1E | `uint16` | Beam 15 distance [mm] |

|Read|Write|Persists|
|---|---|---|
| - | - | - |

---
## 70. Median filter size

The median filter is used to find the average distance while rejecting outliers. The size determines how many distance samples are used to find the average. A larger filter size results in a more consistent output at the expense of immediate accuracy. The filter operates independantly for each beam.

- Min: `3`
- Max: `32`
- `0` indicates the filter is disabled.

Reading `Median filter size` will get a `uint8` of the current size. Writing `Median filter size` with a `uint8` will set the size.

|Read|Write|Persists|
|---|---|---|
| `uint8` | `uint8` | Yes |

---
## 71. Smoothing factor

Smooths the distance output independantly for each beam over time.

|Value|Smoothing|
|:---:|:---:|
| 0 | 0% |
| 1 | *invalid* |
| 2 | 50% |
| 3 | 67% |
| 4 | 75% |
| 5 | 80% |
| 6 | 83% |
| 7 | 86% |
| 8 | 88% |
| 9 | 89% |
| 10 | 90% |
| 11 | 90.9% |
| 12 | 91.7% |
| 13 | 92.4% |
| 14 | 92.9% |
| 15 | 93.3% |
| 16 | 93.7% |
| 17 | 94.2% |
| 18 | 94.5% |
| 19 | 94.8% |
| 20 | 95.0% |

Reading `Smoothing factor` will get a `uint8` of the current value. Writing `Smoothing factor` with a `uint8` will set the value.

!> Setting smoothing to 1 results in a bugged result. This will be fixed in the next firmware version.

|Read|Write|Persists|
|---|---|---|
| `uint8` | `uint8` | Yes |