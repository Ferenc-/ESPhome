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
Check the Flashing Mode

![alt text](WT32-ETH-in-flashing-mode.jpg "WT32 ETH in Flashing Mode")

* **To be able to flash the device, `GPIO0` must be connected to ground (`GND`) while the device boots up.**
  Flashing will not work if you connect these wires after the device has already been booted up.
* `GPIO0` to `GND` (orange) is like holding boot on the WT32-ETH01. **Make sure you remove this after flashing** otherwise the device won't boot!

If the device is essentially bricked and doesn't respond on USB nor on ETH,
then keep pulling out and putting the serial adaper into the laptop's USB,
and keep trying the erase:

```bash
python -m esptool --chip esp32 --port /dev/ttyUSB0 erase-flash
```

Once that is successfull, ensure that you can ID the chip by MAC:

```bash
### There is no Chip ID so use MAC address instead for ID
myvenv_latest/bin/esptool --port /dev/ttyUSB0 --chip esp32 --no-stub read-mac
```

```bash
esphome clean-all
esphome run wt32-eth01-SML-Hichi-IR.yaml --device 192.168.1.126
```

## Hichi IR ttl

Connectors:

* Brown: VCC 3.3 - 5V
* White: GND
* Green: onto TX
* Yellow: onto RX

First always test it with directly connected TTL (i.e. `ttyUSB0`):

```bash
stty -F /dev/ttyUSB0 9600
stty -F /dev/ttyUSB0 raw
cat /dev/ttyUSB0 | hexdump -e '16/1 "%02x " "\n"'
```

If that works then the IR is functional an properly connected.
Start reading locally first via 
[test_asyncio.py](https://github.com/Ferenc-/pysml/blob/master/examples/test_asyncio.py):

```bash
./test_asyncio.py /dev/ttyUSB0
```

once that works set up ESPHome `stream-server` and read via network:

```bash
./test_asyncio.py socket://192.168.1.126:2001
```

## Holley DTZ541-ZEBA

If the meter is within secure perimeter, you might want to at least temporarily
disable the `PIN`:

![alt text](Pin-Off.png "PIN turned OFF")

But you definitely need to enable the `INFO`(`InF`) interface:

![alt text](INFO-On.png "INFO turned ON")
