# M-Audio CODE | SysEx Reverse Engineering

### Handshake:

```
f0 00 01 05 7f 31 05 6d 00 01 01 f7 <- Handshake
f0 7e 7f 06 01 f7
f0 00 01 05 7f 31 05 6d 00 01 01 f7 <- Handshake
...
```

Before sending any commands, we must first handshake with the device.

### SysEx Commands:

| Command | Description | Port |
| :--- | :--- | :--- |
| `61` | Write Preset | 1 |
| `62` | Read Preset | 4 |
| `63` | Reply from The Read Operation | 4 |
| `67` | Write a Value to the Byte | 1 |
| `68` | Read a Value from the Byte | 4 |
| `69` | Reply from The Read Operation | 4 |
| `6a` | Write Global Settings | 1 |
| `6b` | Read Global Settings | 4 |
| `6c` | Reply from The Read Operation | 4 |

Port 4 is used for all read operations, while Port 1 is used for all write operations.

### Example Usages:

##### Write Preset 01 (Command 61 - Port 1):
```
f0 00 01 05 7f 31 05 6d 00 01 01 f7 <- Handshake
f0 00 01 05 7f 31 05 61 0a 3b 01 ... <- Preset Write Operation, 01 denotes the first preset.
```

`f0 00 01 05 7f 31 05 61 0a 3b xx yy f7`

> xx = ID

> yy = Preset Info

##### Read Preset 06 (Command 62 - Port 4):
```
f0 00 01 05 7f 31 05 6d 00 01 01 f7 <- Handshake
f0 00 01 05 7f 31 05 62 06 f7 <- Preset Read Operation, 06 denotes the sixth preset.
```

`f0 00 01 05 7f 31 05 62 xx f7`

> xx = ID

##### Write Global Settings (Command 6a - Port 1):
```
f0 00 01 05 7f 31 05 6a 00 04 00 00 00 01 f7 <- The ID is the third digit from the end, and the value is the last digit.
f0 00 01 05 7f 31 05 6a 00 04 00 04 00 00 f7
```

`f0 00 01 05 7f 31 05 6a 00 04 00 xx 00 yy f7`

> xx = ID

> yy = Value

##### Read Global Settings (Command 6b - Port 4):
```
f0 00 01 05 7f 31 05 6b 00 02 00 05 f7 <- The last digit represents the ID of the Global Setting.
f0 00 01 05 7f 31 05 6b 00 02 00 00 f7 
```

`f0 00 01 05 7f 31 05 6b 00 02 00 xx f7`

> xx = ID

Please keep in mind that the RAM is denoted by the number 00.

# WIP
