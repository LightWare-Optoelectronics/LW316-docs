# Command list

> If a command is not readable or writable then it can only be received from the LW316 and not sent to it.

|ID|Name|Description|RW|Read bytes|Write bytes|Persits|
|---|---|---|---|---|---|---|
|0	|[Product name](command_detail?id=_0-product-name)			|Product name									|R	|16	|-	|-|
|1	|[Hardware version](/)		|Hardware revision								|R	|4	|-	|-|
|2	|[Firmware version](/)		|Firmware revision								|R	|4	|-	|-|
|3	|[Serial number](/)			|Serial number									|R	|16	|-	|-|
|4	|[Command count](/)			|												|R	|1	|-	|-|
|5	|[Command descriptor](/)	|												|RW	|512|1	|-|
|6	|[Get all descriptors](/)	|												|W	|-	|0	|-|
|7	|[UTF8 text message](/)		|												|-	|-	|-	|-|
|9	|[User data](/)				|												|RW	|16	|16	|Y|
|10	|[Token](/)					|												|R	|2	|-	|-|
|11	|[Start bootloader](/)		|												|W	|-	|2	|-|
|12	|[Save parameters](/)		|												|W	|-	|2	|-|
|30	|[Stream](/)				|												|RW	|4	|4	|N|
|31	|[Stream statistics](/)		|												|RW	|1	|1	|N|
|35	|[Statistics data](/)		|												|-	|-	|-	|-|
|40	|[Distance data](/)			|												|-	|-	|-	|-|
|70	|[Median filter size](/)	|												|-	|-	|-	|-|
|71	|[Smoothing factor](/)		|												|-	|-	|-	|-|