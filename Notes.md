# Notes

## ESP8266

Before you begin ensure that the a `CP2101` is used for flashing and NOT an alternative, also ensure that the `V3V` pin is in use.


Do not use Arduino IDE to upload binaries, Arduino IDE has a weird bug, so export with `Sketch` > `Export compiled Binary` and `Sketch` > `Show Sketch Folder`,
then the `bin` file is in the `build` directory, that needs to be uploaded with `esptool`.

```bash
### Chip ID
myvenv/bin/esptool.py --port /dev/ttyUSB0 --chip esp8266 --no-stub  chip_id

### Erase Flash if needed
myvenv/bin/esptool.py --port /dev/ttyUSB0 --chip esp8266 erase_flash

### Flash new build
myvenv/bin/esptool.py --port /dev/ttyUSB0 --chip esp8266 write_flash --flash_mode dio --flash_size detect 0x0 esp8266-midea-dehumidifier.ino.bin
```
