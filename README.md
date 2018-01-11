## LwIP TCP/IP with FreeRTOS

This application guides STM32Cube HAL API users to run LwIP TCP/IP stack with FreeRTOS by testing on `Nucleo-F429ZI`. 
I'm using STM32CubeMX code generator to test the LwIP TCP/IP stack + FreeRTOS and compile with AC6.

The example from repository STM32Cube_FW_F4_V1.18.0\Projects\STM32F429ZI-Nucleo\Applications\LwIP\LwIP_HTTP_Server_Netconn_RTOS
is a good reference. It is working fine out-of-box, but however, if you are using STM32CubeMX to generate the code. You must be
beware of the following tweak in order to it works with LAN8720a PHY transcevier.

## Case 1 
From STM32CubeMX, goto `Configuration` tab --> select `ETH` --> goto `Parameter Setting`. Now look at the `PHY Address`, by default it
is set to `1`, which is not the right. It is because RXER/PHYAD0 at LAN8720a is connected to `Ground` with a 10K resistor (kindly refer
to Nucleo-F429ZI schematic). So therefore the `PHY Address` needs to set to `0`.

## Case 2
At `ETH Configuration`, goto `Advanced Parameters` tab --> goto `Extended: External PHY Configuration`. By refer to the LAN8720a
datasheet, 

**PHY Special Control/Status Register** = 31 (0xF1)

**PHY Speed mask** = 4 (0x0004) - means for 10MB/s and 100MB/s

**PHY Duplex mask** = 16 (0x0010)

**PHY Interrupt Source Flag Register** = 29 (0x1D)

**PHY Link down inturrupt** = 16 (0x10) - at Bits4

## Case 3
Remember to increase your FreeRTOS stack size for example at StartDefaultTask
``` c

	osThreadDef(defaultTask, StartDefaultTask, osPriorityNormal, 0, 128*5);
	
```
Or else, insufficient stack size will cause you board to hang
	
## Special Note :
There is a retarget printf in this project where you can find at `__io_putchar` inside syscall.c. The retarget is used for UART3 which connected to ST-Link V2.
To debug Lwip stack, please insert `LWIP_DEBUG` at `Preprocessor` in the AC6. In order to debug Lwip error messages, please refer to < opt.h >
and active those error messages that you want. For example
``` c

	#define ICMP_DEBUG   LWIP_DBG_ON //LWIP_DBG_OFF
	
```