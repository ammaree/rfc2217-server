# RFC2217 UART Esptool Example

This example sets up an RFC2217 server that emulates a serial port for esptool communication. It accepts client connections and forwards data between the RFC2217 client and UART, while also implementing the DTR/RTS control signals that esptool uses for programming ESP devices.

The example is specifically designed to work with esptool for remote programming of ESP devices over the network. It implements the BOOT and EN GPIO control signals that esptool uses to put ESP devices into download mode, supports dynamic baudrate changes, and implements UART buffer purging for reliable programming.

The example can be used with any ESP chip. You need to connect the target ESP device to the UART port of the ESP board running this example.

Network connection is achieved using `protocols_examples_common` component, which provides a simple API for connecting to Wi-Fi or Ethernet. You can configure the connection method and the credentials in menuconfig.

## Hardware Setup

Connect the target ESP device to the UART port of the ESP board running this example:

- Target ESP TX → ESP board RX (GPIO 5)
- Target ESP RX → ESP board TX (GPIO 4)  
- Target ESP GND → ESP board GND
- Target ESP BOOT → ESP board BOOT (GPIO 26)
- Target ESP EN → ESP board EN (GPIO 25)

## How to Use the Example

Build and flash the example as usual. For example, when using an ESP32 chip:

```shell
idf.py set-target esp32
idf.py flash monitor
```

By default, the example uses UART1, GPIO 4 for TX and GPIO 5 for RX. It also configures GPIO 26 as BOOT and GPIO 25 as EN for esptool control signals. If you want to use different UART or GPIOs, you can change the configuration in menuconfig.

Note the IP address printed on the console when the example runs. You will need it to connect to the device. For example:

```text
I (4547) example_common: Connected to example_netif_sta
I (4557) example_common: - IPv4 address: 192.168.0.180,
```

After flashing, the device will start an RFC2217 server. You can now use esptool to program a target ESP device remotely over the network.

## Programming with Esptool

Now you can use esptool with the RFC2217 URL to program the target device remotely:

```shell
esptool.py --chip esp32 --port rfc2217://192.168.0.180:3333 write_flash 0x1000 your_firmware.bin
```

Replace `192.168.0.180` with the IP address of your ESP board and adjust the chip type and flash parameters as needed.

## Testing with Miniterm

You can also test the connection using `miniterm` from pySerial:

```shell
python -m serial.tools.miniterm rfc2217://192.168.0.180:3333 115200
```

Characters typed in miniterm will be forwarded to the target ESP device, and responses will be sent back.

To exit miniterm, press `Ctrl+]`.

## Example output

```text
I (4565) example_common: - IPv4 address: 192.168.0.180,
I (4565) example_common: - IPv6 address: fe80:0000:0000:0000:1206:1cff:fe98:49dc, type: ESP_IP6_ADDR_IS_LINK_LOCAL
I (4585) app_main: Starting RFC2217 server on port 3333
I (4585) app_main: Waiting for client to connect
I (9245) rfc2217_server: TCP receive thread started, socket: 55
I (9255) app_main: Client connected, starting data transfer
I (48895) rfc2217_server: Connection closed
I (48895) rfc2217_server: TCP receive thread done
I (48915) app_main: Client disconnected
I (48915) app_main: Waiting for client to connect
```

The control signal mapping is:

- DTR → BOOT GPIO (default: GPIO 26)
- RTS → EN GPIO (default: GPIO 25)
