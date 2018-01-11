## LwIP TCP/IP with FreeRTOS

This application guides STM32Cube HAL API users to run LwIP TCP/IP stack with FreeRTOS by testing on 'Nucleo-F429ZI'. 
I'm using STM32CubeMX code generator to test the LwIP TCP/IP stack + FreeRTOS.

The example from repository STM32Cube_FW_F4_V1.18.0\Projects\STM32F429ZI-Nucleo\Applications\LwIP\LwIP_HTTP_Server_Netconn_RTOS
is a good reference. It is working fine out-of-box, but however, if you are using STM32CubeMX to generate the code. You must be
beware of the following tweak in order to work with LAN8720a PHY transcevier.

#Case 1 
From STM32CubeMX, goto 'Configuration' tab --> select 'ETH' --> goto 'Parameter Setting'. Now look at the 'PHY Address', by default it
is set to '1', which is not the right. It is because RXER/PHYAD0 at LAN8720a is connected to Ground with a 10K resistor. So therefore
the 'PHY Address' needs to set to be '0'.

#Case 2
At 'ETH Configuration', goto 'Advanced Parameters' tab --> goto 'Extended: External PHY Configuration'. By refer to the LAN8720a
datasheet, 

**PHY Special Control/Status Register** = 31 (0xF1)

**PHY Speed mask** = 4 (0x0004) - means for 10MB/s and 100MB/s

**PHY Duplex mask** = 16 (0x0010)

**PHY Interrupt Source Flag Register ** = 29 (0x1D)

**PHY Link down inturrupt** = 16 (0x10) - at Bits4