# Controlling a UV1800 spectrometer with external commands



## Introduction

The Shimadzu UV1800 UV-VIS spectrometer can be controlled by a computer. The commands are sent over USB (virtual serial port). For controlling the spectrometer a suitable driver from Shimadzu has to be installed and the spectrometer needs to be in the PC-control mode (F4).
The basic procedure of controlling the spectrometer is described in section 17.3 ("Controlling with External Commands") of the instruction manual operation guide. But the commands listed there are limited.
Some additional commands as used by the UVProbe-Software are listed here.
<br> All information is supplied without guarantee.

## Structure of commands
The commands from the UVProbe software are composed of different parts: One letter for the command encoding, followed by the arguments (for some commands) and a checksum.

## Checksum calculation
The checksum is calculated as the sum of all bytes. This sum is and-linked with 0x7F and or-linked with 0x40. 
Example: Go to wavelength 420.0 nm (4200 Å = 0x1068 Å): W1068f.

| ASCII | W  | 1  | 0  | 6  | 8  | f  |
| :---: | :-: | :-: | :-: | :-: | :-: | :-: |
|  HEX  | 57 | 31 | 30 | 36 | 38 | 66 |

Sum:  57+31+30+36+38 = 126

126&7F = 26

26|40 = 66 = 'f'

## Format of wavelength
The unit of a wavelength value is Ångström and the value needs to be hexadecimal. To set a wavelength of 420.0 nm, set the hexadecimal value 1068.

## Format of returned signal values
example: R000006x <br>
Letter corresponding to command + 6 digit hexadecimal vale (devide by  0x10000 to get the scaled value) + checksum

## Incomplete command list
| Command | Protocol | Name | Processing Content and Usage Notes |
| :---: | :-: | :-: | :-: | 
|Jn | A | Scanning speed | Sets the scanning speed. The parameter n corresponds with the speed number as shown below: <br>n = 1: Fast<br>n = 2: Medium<br>n = 3: Slow<br>n = 4: Very Slow<br>example: J1\{ |
| Vn | A | Measurement mode | Sets the measurement mode. The parameter n corresponds with the mode number as shown below: <br> n = 1: \%T<br>n = 2: Abs<br>n = 3: Energy<br>example: V2H |
| Hn | A | Light source switching wavelength | Sets the wavelength to switch between WI lamp and D2 lamp. <br>example: H0d48H |
|(n | A | S/R-Exchange | Sets the mapping of the signal and reference beam. The parameter n corresponds with the mode number as shown below.<br>n = 0: (normal) sample beam next to the operator<br>n = 1: inverse <br>example: (0X|
| X | A | Auto zero | Performs auto-zeroing (sets the absorbance under the current conditions to zero, or the current transmittance to 100 \%).<br>example: XX| 
| Wn | A | Wavelength setting	(go to λ) | Sets the wavelength. n must meet the following condition: <br>76C <= n <= 1068 (190<=λ<=1100 nm )<br> example: W1068f |
| W | B | Get wavelength | Returns the current wavelength. <br> example: WW  <br> answer: W1068f |
| R | B | Get live signal | Returns the current signal (T, Abs or Energy). <br> example: RR  <br> answer: R000006x |
| Ln1n2n3 | B' | Start measurement | n1, n2 : 4 digit values for the start and stop wavelength. n3: index for measurement interval ( 1 => 0.05; 2=> 0.1; 3 => 0.2; 4  => 0.5; 5 => 1; 6 => 2 nm) <br> Returns the signal for all data points. <br> example: L1f400fa04b  <br> answer: LFFFFFBl |
| Mn1n2n3 | B' | Start baseline measurement | n1, n2 : 4 digit values for the start and stop wavelength. <br> Returns the wavelength for each position. <br> example: M1f400fa0o  <br> answer: M0F64m |
| Y | B | Get status of WI lamp | 0 => off; 1 => on |
| Z | B | Get status of D2 lamp | 0 => off; 1 => on |
| Yn | A | Set power state of WI lamp | n= 0 => off; 1 => on <br> example: Y0I|
| Zn | A | Set power state of D2 lamp | n= 0 => off; 1 => on |
| =n | B | Get operating hours of lamp  | n = 0 => WI; n = 1 => D2 <br> operating hours = answer (hex) / 8 <br> example: =0m <br> answer: =0CE4i |
| * | B' | Start kinetic measurement | Returns current signal. <br> example: *j <br> answer: *0F64J |

## Commands without explanation
| Command | Protocol | Name | Processing Content and Usage Notes |
| :---: | :-: | :-: | :-: | 
| A | B? | check for spetrometer? |  check for spectrometer?  <br> example: AHello Out There!F <br> answer: AHello Out There!F |
| B | B | ? | ? <br> example: BB <br> answer: B4v |
| C | B | ? | ? <br> example: CC <br> answer: C9\| |
| U | B | ? | after baseline measurement <br> example: UU <br> answer: U0000U\| |
| , | ? | ? | (in the maintenance window) |
