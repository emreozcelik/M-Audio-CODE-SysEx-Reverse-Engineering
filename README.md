# M-Audio CODE MIDI Keyboard: SysEx Reverse Engineering

### Overview:
This project demonstrates the reverse engineering of the SysEx (System Exclusive) protocol for the M-Audio CODE MIDI keyboard. It shows how to communicate with the device, including the handshake process, available commands, and examples of reading/writing data such as presets, settings, and pad/button colors. The goal was to gain control over the MIDI functionality and reverse-engineer its communication protocol.

### Tools Used:

+ **HxD:** Used for editing and preparing HEX data.<br/>
+ **MIDI-OX:** A diagnostic tool for reading, analyzing, sending MIDI messages, and writing to the device.

---

### Handshake:

Before sending commands, the device requires a handshake to establish communication. This ensures that the MIDI device and the connected system are synchronized. Without it, the device won’t recognize or respond to commands, making the handshake essential for communication.

```
f0 00 01 05 7f 31 05 6d 00 01 01 f7     <- Handshake
f0 7e 7f 06 01 f7                       <- Get SysEx ID
f0 00 01 05 7f 31 05 6d 00 01 01 f7     <- Handshake
...
```

---

### SysEx Commands:

Here is a list of some commands used to interact with the device.

<div align="center">

| Command | Description | Port |
| :--- | :--- | :--- |
| `61` | Write Preset | 1 |
| `62` | Read Preset | 4 |
| `63` | Reply from The Read Preset Operation | 4 |
| `67` | Write a Value to the Byte | 1 |
| `68` | Read a Value from the Byte | 4 |
| `69` | Reply from The Read Value Operation | 4 |
| `6a` | Write Global Settings | 1 |
| `6b` | Read Global Settings | 4 |
| `6c` | Reply from The Read Global Settings Operation | 4 |

Port 4 is dedicated for all read operations, allowing the retrieval of data from the MIDI device. Port 1 is reserved for all write operations, enabling data to be transmitted to the device.

</div>

---

### Examples:

##### Write to Preset 1 and RAM (Command 61 - Port 1):
```
f0 00 01 05 7f 31 05 6d 00 01 01 f7         <- Handshake
f0 00 01 05 7f 31 05 61 0a 3b 01 ... f7     <- Preset Write Operation, 01 denotes the first preset.
f0 00 01 05 7f 31 05 61 0a 3b 00 ... f7     <- Preset Write Operation, 00 denotes the RAM.
```

`f0 00 01 05 7f 31 05 61 0a 3b xx yy f7`

> xx = ID

> yy = Preset Bytes

##### Read from Preset 6 and RAM (Command 62 - Port 4):
```
f0 00 01 05 7f 31 05 6d 00 01 01 f7     <- Handshake
f0 00 01 05 7f 31 05 62 06 f7           <- Preset Read Operation, 06 denotes the sixth preset.
f0 00 01 05 7f 31 05 62 06 f7           <- Preset Read Operation, 00 denotes the RAM.
```

`f0 00 01 05 7f 31 05 62 xx f7`

> xx = ID

The RAM is denoted by the number 00. All other operations are performed entirely in RAM.

##### Write to Bytes 0, 127, 128, 255 (Command 67 - Port 1):
```
f0 00 01 05 7f 31 05 6d 00 01 01 f7                 <- Handshake
f0 00 01 05 7f 31 05 67 00 00 00 00 00 00 01 f7     <- 0
f0 00 01 05 7f 31 05 67 00 00 00 00 7f 00 01 f7     <- 127
f0 00 01 05 7f 31 05 67 00 00 00 01 00 00 01 f7     <- 128
f0 00 01 05 7f 31 05 67 00 00 00 01 7f 00 01 f7     <- 255
```

`f0 00 01 05 7f 31 05 67 00 00 00 xx yy zz ww f7`

> xx = 127 Byte Offset

> yy = 1 Byte Offset

> zz = Second Byte Value

> ww = Value

##### Read from Bytes 0, 127, 128, 255 (Command 68 - Port 4):
```
f0 00 01 05 7f 31 05 6d 00 01 01 f7         <- Handshake
f0 00 01 05 7f 31 05 68 00 00 00 00 00 f7   <- 0 
f0 00 01 05 7f 31 05 68 00 00 00 00 7f f7   <- 127
f0 00 01 05 7f 31 05 68 00 00 00 01 00 f7   <- 128
f0 00 01 05 7f 31 05 68 00 00 00 01 7f f7   <- 255
```

`f0 00 01 05 7f 31 05 68 00 00 00 xx yy f7`

> xx = 127 Byte Offset

> yy = 1 Byte Offset

##### Write Global Settings (Command 6a - Port 1):
```
f0 00 01 05 7f 31 05 6d 00 01 01 f7          	<- Handshake
f0 00 01 05 7f 31 05 6a 00 04 00 00 00 01 f7	<- The ID is the third digit from the end, and the value is the last digit.
f0 00 01 05 7f 31 05 6a 00 04 00 04 00 00 f7
```

`f0 00 01 05 7f 31 05 6a 00 04 00 xx 00 yy f7`

> xx = ID

> yy = Value

##### Read Global Settings (Command 6b - Port 4):
```
f0 00 01 05 7f 31 05 6d 00 01 01 f7         <- Handshake
f0 00 01 05 7f 31 05 6b 00 02 00 05 f7      <- The last digit represents the ID of the Global Setting.
f0 00 01 05 7f 31 05 6b 00 02 00 00 f7 
```

`f0 00 01 05 7f 31 05 6b 00 02 00 xx f7`

> xx = ID

---

### Addresses to Read Pad and Button Colors:

<details align="center"><summary>Expand the List</summary>
<p align="center">

### Pads

Pad 1 Color 1: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 3c f7`

Pad 1 Color 2: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 3d f7`

Pad 2 Color 1: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 47 f7`

Pad 2 Color 2: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 48 f7`

Pad 3 Color 1: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 52 f7`

Pad 3 Color 2: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 53 f7`

Pad 4 Color 1: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 5d f7`

Pad 4 Color 2: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 5e f7`

---
 
Pad 5 Color 1: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 68 f7`

Pad 5 Color 2: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 69 f7`

Pad 6 Color 1: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 73 f7`

Pad 6 Color 2: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 74 f7`

Pad 7 Color 1: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 7e f7`

Pad 7 Color 2: `f0 00 01 05 7f 31 05 68 0a 3b 00 00 7f f7`

Pad 8 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 01 09 f7`
 
Pad 8 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 01 0a f7` 

---
 
Pad 9 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 01 14 f7` 

Pad 9 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 01 15 f7` 

Pad 10 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 01 1f f7`

Pad 10 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 01 20 f7` 

Pad 11 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 01 2a f7`

Pad 11 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 01 2b f7` 

Pad 12 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 01 35 f7` 

Pad 12 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 01 36 f7` 

---

Pad 13 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 01 40 f7` 

Pad 13 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 01 41 f7` 

Pad 14 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 01 4b f7` 

Pad 14 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 01 4c f7` 

Pad 15 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 01 56 f7` 

Pad 15 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 01 57 f7` 

Pad 16 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 01 61 f7` 

Pad 16 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 01 62 f7`

### Buttons
 
Button 1 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 01 6c f7` 

Button 1 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 01 6d f7` 

Button 2 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 01 7a f7` 

Button 2 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 01 7b f7` 

Button 3 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 02 08 f7` 

Button 3 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 02 09 f7` 

Button 4 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 02 16 f7` 

Button 4 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 02 17 f7` 

Button 5 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 02 24 f7` 

Button 5 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 02 25 f7` 

Button 6 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 02 32 f7` 

Button 6 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 02 33 f7` 

Button 7 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 02 40 f7` 

Button 7 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 02 41 f7` 

Button 8 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 02 4e f7` 

Button 8 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 02 4f f7` 

Button 9 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 02 5c f7` 

Button 9 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 02 5d f7` 

---

Button 10 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 02 6a f7` 

Button 10 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 02 6b f7` 

Button 11 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 02 78 f7` 

Button 11 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 02 79 f7` 

Button 12 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 03 06 f7` 

Button 12 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 03 07 f7` 

Button 13 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 03 14 f7` 

Button 13 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 03 15 f7` 

Button 14 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 03 22 f7` 

Button 14 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 03 23 f7` 

Button 15 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 03 30 f7` 

Button 15 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 03 31 f7` 

Button 16 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 03 3e f7` 

Button 16 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 03 3f f7` 

Button 17 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 03 4c f7` 

Button 17 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 03 4d f7` 

Button 18 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 03 5a f7` 

Button 18 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 03 5b f7` 

---

Button 19 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 03 68 f7` 

Button 19 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 03 69 f7` 

Button 20 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 03 76 f7` 

Button 20 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 03 77 f7` 

Button 21 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 04 04 f7` 

Button 21 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 04 05 f7` 

Button 22 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 04 12 f7` 

Button 22 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 04 13 f7` 

Button 23 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 04 20 f7` 

Button 23 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 04 21 f7` 

Button 24 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 04 2e f7` 

Button 24 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 04 2f f7` 

Button 25 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 04 3c f7`
 
Button 25 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 04 3d f7` 

Button 26 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 04 4a f7` 

Button 26 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 04 4b f7` 

Button 27 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 04 58 f7` 

Button 27 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 04 59 f7` 

---

Button 28 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 04 66 f7` 

Button 28 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 04 67 f7` 

Button 29 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 04 74 f7` 

Button 29 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 04 75 f7` 

Button 30 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 05 02 f7` 

Button 30 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 05 03 f7` 

Button 31 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 05 10 f7` 

Button 31 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 05 11 f7` 

Button 32 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 05 1e f7` 

Button 32 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 05 1f f7` 

Button 33 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 05 2c f7` 

Button 33 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 05 2d f7` 

Button 34 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 05 3a f7` 

Button 34 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 05 3b f7` 

Button 35 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 05 48 f7` 

Button 35 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 05 49 f7` 

Button 36 Color 1: `f0 00 01 05 7f 31 05 68 00 00 00 05 56 f7`

Button 36 Color 2: `f0 00 01 05 7f 31 05 68 00 00 00 05 57 f7`

</p>
</details>

---

### Colors:

<table align="center">
<tr><th>Pads</th><th>Buttons</th></tr>
<tr><td valign="top">

| Color | Code |
| :--- | :--- |
| `00` | Off |
| `01` | Chartreuse |
| `02` | Green |
| `03` | Aqua |
| `04` | Cyan |
| `05` | Azura |
| `06` | Blue |
| `07` | Violet |
| `08` | Magenta |
| `09` | Rose |
| `0A` | Red |
| `0B` | Orange |
| `0C` | Yellow |
| `0D` | White |

</td><td valign="top" style="width: 333px;">

| Color | Code |
| :--- | :--- |
| `00` | Off |
| `01` | Green |
| `02` | Cyan |
| `03` | Blue |
| `04` | Magenta |
| `05` | Red |
| `06` | Yellow |
| `07` | White |

</td></tr></table>

---

### Changing Pad and Button Colors:

##### Example: Changing the Color of the 16th Pad

`f0 00 01 05 7f 31 05 67 00 00 00 01 61 00 0a f7` 

> The address 01 61 was used, which corresponds to the 16th pad. The color red is represented by 0a. It’s important to note that 00 was added before the color code.

##### Example: Changing the Color of the 4th Button

`f0 00 01 05 7f 31 05 67 00 00 00 02 16 00 04 f7`

> The address 02 16 was used, which corresponds to the 4th button in the first bank, and it was set to Magenta.
