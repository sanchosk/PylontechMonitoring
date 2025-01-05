# Pylontech Battery Monitoring via WiFi
Forked from irekzielinski/Pylontech-Battery-Monitoring

This project allows you to control and monitor Pylontech US2000B, US2000C, US3000C and US5000 batteries via console port over WiFi.
It it's a great starting point to integrate battery with your home automation.

**I ACCEPT NO RESPONSIBILTY FOR ANY DAMAGE CAUSED, PROCEED AT YOUR OWN RISK**

# Features:
  * Low cost (around 20$ in total).
  * Adds WiFi capability to your Pylontech US2000B/C , US3000C, US5000 battery.
  * Device exposes web interface that allows to:
    * send console commands and read response over WiFi (no PC needed)
    * battery information can be retrevied also in JSON format for easy parsing
  * MQTT support:
    * device pushes basic battery data like SOC, temperature, state, etc to selected MQTT server
  * Easy to modify code using Arduino IDE and flash new firmware over WiFi (no need to disconnect from the battery).
  * choose dhcp or static ip

See the project in action on [Youtube](https://youtu.be/7VyQjKU3MsU):</br>
<a href="http://www.youtube.com/watch?feature=player_embedded&v=7VyQjKU3MsU" target="_blank"><img src="http://img.youtube.com/vi/7VyQjKU3MsU/0.jpg" alt="See the project in action on YouTube" width="240" height="180" border="10" /></a>


# Parts needed and schematics:
  * [Wemos D1 mini microcontroller](https://www.amazon.co.uk/Makerfire-NodeMcu-Development-ESP8266-Compatible/dp/B071S8MWTY/).
  * [SparkFun MAX3232 Transceiver](https://www.sparkfun.com/products/11189).
  * US2000B: Cable with RJ10 connector (some RJ10 cables have only two wires, make sure to buy one that has all four wires present).
  * US2000C or US5000: Cable with RJ45 connector (see below for more details).
  * Capacitors C1: 10uF, C2: 0.1uF (this is not strictly required, but recommended as Wemos D1 can have large current spikes).

![Schematics](Schemetics.png)

# US2000C/US3000C/US5000 notes:
This battery uses RJ45 cable instead of RJ10. Schematics is the same only plug differs:
  * RJ45 Pin 3 (white-green) = R1IN
  * RJ45 Pin 6 (green)       = T1OUT
  * RJ45 Pin 8 (brown)       = GND
![image](https://user-images.githubusercontent.com/19826327/146428324-29e3f9bf-6cc3-415c-9d60-fa5ee3d65613.png)


# How to get going:
  * Get Wemos D1 mini
  * Install arduino IDE and ESP8266 libraries as [described here](https://averagemaker.com/2018/03/wemos-d1-mini-setup.html)
  * Open [PylontechMonitoring.ino](PylontechMonitoring.ino) in arduino IDE
  * Make sure to copy content of [libraries subdirectory](libraries) to [libraries of your Arduino IDE](https://forum.arduino.cc/index.php?topic=88380.0).
  * Configure all parameters in file Pylontech.h for your use
    * Upload project to your device
  * Connect Wemos D1 mini to the MAX3232 transreceiver
  * Connect transreceiver to RJ10/RJ45 as descibed in the schematics (all three lines need to be connected)
  * Connect RJ10/RJ45 to the serial port of the Pylontech US2000 battery. If you have multiple batteries - connect to the master one.
  * Connect Wemos D1 to the power via USB
  * Find what IP address was assigned to your Wemos by your router and open it in the web-browser
  * You should be able now to connunicate with the battery via WiFi

# Pylontech Battery Monitor

This project uses an **ESP8266** to read data from a Pylontech battery (via Serial) and publish it to an MQTT broker.  
It also supports an internal web interface and (optionally) OTA updates.

---

## Configuration Parameters

All configuration values are located in [`Pylontech.h`](./Pylontech.h). Below is a description of each parameter you can customize according to your environment.

| Parameter               | Description                                                                                                                                                                                                         | Example                               |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------|
| **`WIFI_SSID`**         | The Wi-Fi network name (SSID) your ESP8266 should connect to.                                                                                                                                                      | `#define WIFI_SSID "MyWiFi"`          |
| **`WIFI_PASS`**         | The password for your Wi-Fi network.                                                                                                                                                                               | `#define WIFI_PASS "MyPassword"`      |
| **`WIFI_HOSTNAME`**     | The hostname for your ESP8266. Useful for mDNS or other identification methods.                                                                                                                                    | `#define WIFI_HOSTNAME "MyBattery"`   |
| **`STATIC_IP`**         | Uncomment to enable a static IP configuration (instead of DHCP).                                                                                                                                                   | `//#define STATIC_IP`                 |
| **`ip`**                | The device’s static IP address (requires `#define STATIC_IP`).                                                                                                                                                     | `IPAddress ip(192, 168, 1, 50);`      |
| **`subnet`**            | The subnet mask (requires `#define STATIC_IP`).                                                                                                                                                                    | `IPAddress subnet(255, 255, 255, 0);` |
| **`gateway`**           | The default gateway for your network (requires `#define STATIC_IP`).                                                                                                                                              | `IPAddress gateway(192, 168, 1, 1);`  |
| **`dns`**               | The DNS server (requires `#define STATIC_IP`).                                                                                                                                                                     | `IPAddress dns(192, 168, 1, 1);`      |
| **`AUTHENTICATION`**    | Uncomment to enable HTTP Basic Authentication for the internal web interface.                                                                                                                                      | `//#define AUTHENTICATION`            |
| **`www_username`**      | The username for HTTP Basic Authentication (only if `#define AUTHENTICATION`).                                                                                                                                     | `const char* www_username = "admin";` |
| **`www_password`**      | The password for HTTP Basic Authentication (only if `#define AUTHENTICATION`).                                                                                                                                     | `const char* www_password = "secret";`|
| **`ENABLE_MQTT`**       | Uncomment to enable MQTT functionality.                                                                                                                                                                            | `#define ENABLE_MQTT`                 |
| **`GMT`**               | Set your time offset (in seconds) for NTP. Examples: **GMT+1** = 3600, **GMT+2** = 7200, **GMT-1** = -3600, etc.                                                                                                     | `#define GMT 7200`                    |
| **`MQTT_SERVER`**       | The hostname or IP address of your MQTT broker.                                                                                                                                                                    | `#define MQTT_SERVER "192.168.1.100"` |
| **`MQTT_PORT`**         | The port your MQTT broker listens on.                                                                                                                                                                             | `#define MQTT_PORT 1883`              |
| **`MQTT_USER`**         | The username for MQTT authentication (if required by the broker).                                                                                                                                                 | `#define MQTT_USER "mqttUser"`        |
| **`MQTT_PASSWORD`**     | The password for MQTT authentication (if required by the broker).                                                                                                                          


# Example of sensors in Home Assistant:

```
mqtt:

  sensor:
    - name: "Livello Carica Batteria"
      state_topic: "homeassistant/sensor/grid_battery/soc"
      unit_of_measurement: "%"
      device_class: battery
      
    - name: "Stato Batteria"
      state_topic: "homeassistant/sensor/grid_battery/base_state"
      
    - name: "Temperatura Batteria"
      state_topic: "homeassistant/sensor/grid_battery/temp"
      unit_of_measurement: "°C"
      device_class: temperature
      
    - name: "Num Batterie"
      state_topic: "homeassistant/sensor/grid_battery/battery_count"
      
    - name: "Potenza impegnata Batterie"
      state_topic: "homeassistant/sensor/grid_battery/getPowerDC"
      unit_of_measurement: "Wh"
      device_class: energy
      
    - name: "Potenza carica Batterie"
      state_topic: "homeassistant/sensor/grid_battery/powerIN"
      unit_of_measurement: "Wh"
      state_class: "total_increasing"
      device_class: "energy"
      icon: mdi:flash

      
    - name: "Potenza scarica Batterie"
      state_topic: "homeassistant/sensor/grid_battery/powerOUT"
      unit_of_measurement: "Wh"
      state_class: "total_increasing"
      device_class: "energy"
      icon: mdi:flash
```
