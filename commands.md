# Command list

> If a command is not readable or writable then it can only be received from the LW316 and not sent to it.

|ID|Name|Description|RW|Read bytes|Write bytes|Persists|
|---|---|---|---|---|---|---|
|0	|[Product name](command_detail?id=_0-product-name)		        	|Product name									|R	|16	|-	|-|
|1	|[Hardware version](command_detail?id=_1-hardware-version)	    	|Hardware revision								|R	|4	|-	|-|
|2	|[Firmware version](command_detail?id=_2-firmware-version)	    	|Firmware revision								|R	|4	|-	|-|
|3	|[Serial number](command_detail?id=_3-serial-number)		    	|Serial number									|R	|16	|-	|-|
|4	|[Command count](command_detail?id=_4-command-count)		    	|Number of available command descriptors        |R	|1	|-	|-|
|5	|[Command descriptor](command_detail?id=_5-command-descriptor)  	|JSON description of specified command			|RW	|512|1	|-|
|6	|[Get all descriptors](command_detail?id=_6-get-all-descriptors)	|Send a command descriptor for every command   	|W	|-	|0	|-|
|7	|[UTF8 text message](command_detail?id=_7-utf8-text-message)		|Human readable text message					|-	|-	|-	|-|
|9	|[User data](command_detail?id=_9-user-data)				        |16 byte store for user data					|RW	|16	|16	|Y|
|10	|[Token](command_detail?id=_10-token)					            |Next usable safety token      				|R	|2	|-	|-|
|11	|[Start bootloader](command_detail?id=_11-start-bootloader)		    |Start bootloader				    			|W	|-	|2	|-|
|12	|[Save parameters](command_detail?id=_12-save-parameters)		    |Store persistable parameters					|W	|-	|2	|-|
|30	|[Stream](command_detail?id=_30-stream)				                |Current data stream type						|RW	|4	|4	|N|
|31	|[Stream statistics](command_detail?id=_31-statistics)	    	    |Enable/disable statistics streaming			|RW	|1	|1	|N|
|35	|[Statistics data](command_detail?id=_35-statistics-data)		    |General status information						|-	|-	|-	|-|
|40	|[Distance data](command_detail?id=_40-distance-data)			    |Distance data for all 16 beams					|-	|-	|-	|-|
|70	|[Median filter size](command_detail?id=_70-median-filter-size)	    |Number of distance samples used for median filter		|RW	|1	|1	|Y|
|71	|[Smoothing factor](command_detail?id=_71-smoothing-factor)		    |Strength of distance sample smoothing 		|RW	|1	|1	|Y|