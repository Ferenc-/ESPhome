# Notes

## ESP8266

Before you begin ensure that the a `CP2101` is used for flashing and NOT an alternative, also ensure that the `V3V` pin is in use.


Do not use Arduino IDE to upload binaries, Arduino IDE has a weird bug, so export with `Sketch` > `Export compiled Binary` and `Sketch` > `Show Sketch Folder`,
then the `bin` file is in the `build` directory, that needs to be uploaded with `esptool`.

Don't forget to close Arduino IDE before switching to `esptool`.
Otherwise Arduino IDE will keep your `/dev/ttyUSB0` device busy,
and won't allow `esptool` to use it.
 
```bash
### Chip ID
myvenv/bin/esptool --port /dev/ttyUSB0 --chip esp8266 --no-stub chip-id

### Erase Flash if needed
myvenv/bin/esptool --port /dev/ttyUSB0 --chip esp8266 erase_flash

### Flash new build
myvenv/bin/esptool --port /dev/ttyUSB0 --chip esp8266 write_flash --flash_mode dio --flash_size detect 0x0 esp8266-midea-dehumidifier.ino.bin
```

Once flashed via serial, flashing via [OTA](https://github.com/JAndrassy/ArduinoOTA)
works just fine from Arduino IDE, but keep in mind that re-flashing does not wipe the configuration data,
which is often the blocker of a successful startup/connection.

## WT32-ETH01

```bash
### There is no Chip ID so use MAC address instead for ID
myvenv_latest/bin/esptool --port /dev/ttyUSB0 --chip esp32 --no-stub read-mac
```

```bash
esphome clean-all
```
