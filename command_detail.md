# Detailed command descriptions

## 0 Product name
> Read 16 bytes (null terminated string)

Read a 16 byte string indicating the product model name. This will always be `LW316` followed by a null terminator. You can use this to verify the LW316 is connected and operational over the selected interface. When the bootloader is active this will return `BOOT` followed by a null terminator.

## 1 Hardware version
> Read 4 bytes (uint32)

A simple `uint32` which represents the current hardware configuration. Different versions of firmware will be available for different hardware revisions.

## 2 Firmware version
> Read 4 bytes (semver packed)

Semver stored in a `uint32`. Major, minor, patch. Used to determine API compatability. The [version support](?id=version-support) section details which firmware versions this document applies to.

## 4. Median filter size

The median filter is used to find the average distance while rejecting outliers. The size of the filter is set through this command. 

Min: 3
Max: 32

!> Setting to 0 will switch the filter off entirely.

## 5. Smoothing factor
> Read 1 byte (uint8)

> Write 1 byte (uint8)

Smooths the output over time.

0 - 20

|Value|Smoothing|
|:---:|:---:|
| 0 | 00.00% |
| 1 | 50.00% |
| 2 | 75.00% |
| 3 | 77.50% |
| 4 | 83.75% |
| 5 | 86.875% |
| 6 | 88.4375% |
| 7 | 89.21875% |
| 8 | 89.609375% |
| 9 | 0 |
| 10 | 0 |
| 11 | 0 |
| 12 | 0 |
| 13 | 0 |
| 14 | 0 |
| 15 | 0 |
| 16 | 0 |
| 17 | 0 |
| 18 | 0 |
| 19 | 0 |
| 20 | 95% |

## 20. Opcode descriptor

### Read

```json
{
	"id": 0,
	"name": "Product name" 
}

```

### Write