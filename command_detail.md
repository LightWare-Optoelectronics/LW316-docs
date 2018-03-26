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
| 4 bytes | - | - |

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

Reading the `Command descriptor` will return a `512 byte string` (null terminated) which contains JSON describing the command whose ID was last written to `Command descriptor`.

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

Writing a `uint8` to the `Command descriptor` will set the ID of the next command to be described.

|Read|Write|Persists|
|---|---|---|
| `512 byte string` | `uint8` | - |

## 6. Get all descriptors

?> Write 0 bytes



## 7. UTF8 text message

*Serial interface only*

The LW316 will send this command when it needs to communicate a human readable message.

## 9. User data

## 10. Token

?> Read 2 bytes (uint16)

Current safety token.

Used for saving parameters or starting the bootloader. Once a token has been used it will expire and a new token is created.

## 11. Start bootloader

> The bootloader enables user applications to perform firmware upgrades, rather than being forced to use LightWare Studio.

## 12. Save parameters

?> Write 2 bytes (uint16)

Certain commands write to parameters that can persist across power cycles. These parameters will only persist once the `Save parameters` command has been issued with the appropriate `token`.

Uses flash, therefore safety token attempt to prevent unintentional writes.

## 30. Stream

Data type that the LW316 will continuously ouput. The rate and format of the data is dependant on its type.

!> Streaming commands will typically only output on the serial interface. While it is possible to read and write the Stream command over I2C, the resulting streamed data will not be retrievable.

## 31. Statistics

Streaming state of statistics data.

!> Streaming commands will typically only output on the serial interface. While it is possible to read and write the Statistics command over I2C, the resulting streamed data will not be retrievable.

## 35. Statistics data

*Serial interface only*

## 40. Distance data

*Serial interface only*



## 70. Median filter size

The median filter is used to find the average distance while rejecting outliers. The size determines how many distance samples are used to find the average. A larger filter size results in a more consistent output at the expense of immediate accuracy.

- Min: 3
- Max: 32

---

?> Read 1 byte (uint8)

- Get the filter size.

---

?> Write 1 byte (uint8)

- Set the filter size from 3 to 32. 

!> Setting to 0 will switch the filter off entirely.

## 71. Smoothing factor

Smooths the output over time.

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

?> Read 1 byte (uint8)

- Get the smoothing factor.

?> Write 1 byte (uint8)

- Set the smoothing factor from 0 to 20. 
- Smoothing is completely disabled when set to 0.

!> Setting smoothing to 1 results in a bugged result. This will be fixed in the next firmware version.