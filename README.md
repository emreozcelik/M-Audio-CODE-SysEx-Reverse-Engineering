# M-Audio CODE | SysEx Reverse Engineering

#### Handshake:

```
f0 00 01 05 7f 31 05 6d 00 01 01 f7 <- Handshake
f0 7e 7f 06 01 f7
f0 00 01 05 7f 31 05 6d 00 01 01 f7 <- Handshake
```

We must first handshake with the device before sending any commands.

#### SysEx Commands:

| Command | Description | Port |
| :--- | :--- | :--- |
| `61` | Write Preset | 1 |
| `62` | Read Preset | 4 |
| `63` | Reply Preset | 4 |
| `67` | Write at Location | 1 |
| `68` | Read at Location | 4 |
| `69` | Reply at Location | 4 |
| `6a` | Write Global Settings | 1 |
| `6b` | Read Global Settings | 4 |
| `6c` | Reply Global Settings | 4 |

Port 4 is used for all read operations, while Port 1 is used for all write operations.
