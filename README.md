# STM32F411 PCM5102A 24bit USB Audio DAC

This is an inexpensive USBAudio DAC that supports 24-bit resolution audio at 44.1kHz, 48kHz or 96kHz. It uses an STM32F411 Black Pill with two different i2s PCM5102A DAC modules - they differ in size and design. The software is based on this [**STM32F4xx USB to I2S DAC Audio Bridge**](https://github.com/har-in-air/STM32F411_USB_AUDIO_DAC) and the [**issue as discussed here**](https://github.com/har-in-air/STM32F411_USB_AUDIO_DAC/issues/7).

Two sizes of the DAC were made - the larger size use a PCM5102A DAC with three LDO regulators but no mute control pin, and the (much) smaller sized DAC use two LDO regulators and has a mute control pin. 

The volume control code modifications as discussed in the issue linked to above, was added to a windows port of the code. The Windows 10 STM32CubeIDE project is included here as F411_USB_I2S.zip and the binaries are in build.zip - the hex file can directly be uploaded via the STM32CubeProgrammer using a standard ST-Link module. Note all licenses applicable are the original source code licenses. 

The [**current source**](https://github.com/har-in-air/STM32F411_USB_AUDIO_DAC/issues/14) build binaries are included here as build-24March2023.zip, but now built with STM32CubeIDE version 1.12. Previous source (Feb 2023) binaries are here as build-linux3.tar.gz (Linux Mint 21.1 with STM32CubeIDE 1.11.2), and build-win10.zip (based on project files STM32F411_USB_AUDIO_DAC-win10.zip), built on Windows 10 using the same version of STM32CubeIDE.

For [**detailed instructions in setting up STMCube refer to this document:**](https://github.com/TobiasVanDyk/STM32F411-PCM5102A-24bit-USB-Audio-DAC/blob/main/Linux-Mint-211-and-Windows-10-compiling-and-uploading-the-STM32F411-USB-Audio-DAC-firmware.pdf) Installing STM32CubeIDE in Linux Mint 21.1 and in Windows 10, and compiling and uploading the STM32F411 USB Audio DAC firmware.

The USB DAC with the volume control identifies as PCM5102A DAC and the older non-volume version as USB to I2S DAC - refer to the screen dump shown below.

<p align="left">
<img src="images/dac1.jpg" height="110" /> 
<img src="images/dac2.jpg" height="110" /> 
<img src="images/dac3.jpg" height="110" /> 
</p>

<img src="images/MuteFix.jpg" width="16" height="16"/> The larger sized PCM5102A DAC module used does not have a hardware mute breakout pin. Two changes to bsp_audio.c gives an alternative "volume mute" - the compiled version is in MuteFix.zip. This version uses only the red and green LEDs to indicate the three frequency modes - those changes are in main.c
``` 
uint8_t BSP_AUDIO_OUT_SetMute(uint8_t mute) {
	if (mute) {
		BSP_AUDIO_OUT_Pause();  // AUDIO_MUTE_ON();
		}
	else {
		BSP_AUDIO_OUT_Resume(); // AUDIO_MUTE_OFF();
		}
	return AUDIO_OK;
	}

``` 

**Connections for the larger-sized DAC:**
``` 
F411    PCM5102A    LED    Description 
--------------------------------------------------------------------
5V      VCC
GND     GND            
B13     BCK                I2S_BCK (Bit Clock)
B15     Data               I2S_SDI (Data Input)
B12     LRCK               I2S_WS (LR Clock)
-------------------------------------------------------------------- 
B3                 RED     Fs = 96kHz (all 220R to 3v3)
B6                 GRN     Fs = 48kHz (Red+Green for 44.1kHz)
3v3                LED through-hole common
 
C13             On-board   Diagnostic Blue LED
--------------------------------------------------------------------
``` 
<p align="left">
<img src="images/dac8.jpg" height="180" /> 
<img src="images/dac9.jpg" height="180" /> 
</p>

The smaller DAC is similarly-sized as the DAC used by [**Har-In-Air**](https://github.com/har-in-air/STM32F411_USB_AUDIO_DAC) - but only two SMD LEDs are used for the 3 frequency indicators. Refer to [**SMD-LED-Resistor**](https://github.com/TobiasVanDyk/STM32F411-PCM5102A-24bit-USB-Audio-DAC/blob/main/images/SMD-LED-Resistor.png) for an approximate layout for the SMD resistors and LEDs. Use a 4-pin JST male connector at the bottom of the STM32F411 board.

The source+build and the build-folder-only are included here as Small-USBAUDIODAC-Jan2024.zip and Small-build-Jan2024.zip - insignificant changes were made to main.c, usbd_desc.c, and bsp_audio.c.

The 3D case files are in the STL folder named Small*.stl. It is based on this [**PCB-holder SCAD model**](https://www.thingiverse.com/thing:4061855). Various length and width parameters for other MCU-boards (namely an RP2040 Pico, STM32F103 Blue Pill, STM32F411 Black Pill, and a Teensy 4.1) are in the file [**pcbholder-params**](https://github.com/TobiasVanDyk/STM32F411-PCM5102A-24bit-USB-Audio-DAC/blob/main/stl/pcbholder-params.txt) also in the STL folder.

**Connections for the smaller-sized DAC:**
``` 
F411    PCM5102A    LED    Description 
--------------------------------------------------------------------------------------
5V      VCC
GND     GND            
B13     BCK                I2S_BCK (Bit Clock)
B15     DIN                I2S_SDI (Data Input)
B12     LCK                I2S_WS (LR Clock)
B8      XMT                Mute Control

Bridge the SCK pads on top with solder to connect it to earth
No other solder bridges are required for the 4 pad-sets on the other side of the PCB
--------------------------------------------------------------------------------------- 
B3                 GRN     Fs = 96kHz (both SMD-LEDs 1k ohm to 3v3)
B6                 RED     Fs = 48kHz (Red+Green for 44.1kHz)
3v3                LED SMD-1206 common
 
C13             On-board   Diagnostic Blue LED
---------------------------------------------------------------------------------------
``` 
<p align="left">
<img src="images/Pic3.png" height="210" /> 
<img src="images/Pic6.png" height="210" /> 
</p>



The [**STM32F11 can be obtained here**](https://www.robotics.org.za/STM32F411CEU6-MOD) and the [**larger PCM5102A DAC module here**](https://www.robotics.org.za/PCM5102) or [**the smaller size DAC here**](https://botshop.co.za/products/pcm5102-dac-i2s-interface-decoder-sound-card-board-digital-audio-gy-pcm5102-phat-format-player-module-for-raspberry-pi).

<p align="left">
<img src="images/mcu.jpg" height="210" />   
<img src="images/dac.jpg" height="210" />
<img src="images/dac-small.jpg" height="210" />
</p>

This DAC is the perfect companion for the [**1976 November Wireless World An Advanced Preamplifier by Douglas Self**](https://github.com/TobiasVanDyk/Building-the-Advanced-Preamplifier-1976-Douglas-Self). 

There are a number of variations of the (Chinese) PCM5102A modules - where available schematics of single, double and triple (as used here) LDO regulator modules are shown below.

<p align="left">
<img src="images/pcm5102a-singleLDOa.jpg" height="140" /> 
<img src="images/pcm5102a-singleLDOb.png" height="140" /> 
<img src="images/CJMCU-5102.png" height="140" />
<img src="images/pcm5102a-doubleLDO.jpg" height="140" /> 
</p>
<p align="left">
<img src="images/pcm5102a-singleLDOtop.jpg" height="160" /> 
<img src="images/pcm5102a-singleLDObottom.jpg" height="160" /> 
<img src="images/pcm5102a-doubleLDOtop.jpg" height="160" /> 
<img src="images/pcm5102a-doubleLDObottom.jpg" height="160" />	
</p>
