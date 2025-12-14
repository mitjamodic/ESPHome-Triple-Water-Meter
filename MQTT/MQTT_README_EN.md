### What is MQTT and why use it

MQTT support allows the watermeter device to act as a **generic data
source**, independent of Home Assistant.

MQTT is recommended if you: - use **Node-RED** - store data in
**InfluxDB / Grafana** - integrate with other platforms - require device
availability monitoring

⚠️ **MQTT is optional**.

MQTT is implemented as a separate ESPHome package.

To enable MQTT support:
- download `mqtt.yaml`
- place `mqtt.yaml` in `MQTT` folder in same directory as the main ESPHome YAML file
- include it using `packages`

------------------------------------------------------------------------

### Basic configuration

The MQTT configuration is defined in the `mqtt.yaml` file, while sensitive data is stored in `secrets.yaml`.

#### `secrets.yaml`

``` yaml
mqtt_broker: 192.168.1.10
mqtt_user: mqtt_user
mqtt_pass: mqtt_password
```

#### ESPHome configuration

``` yaml
packages:
  mqtt: !include MQTT/mqtt.yaml
```

#### `MQTT.yaml`

``` yaml
mqtt:
  broker: !secret mqtt_broker
  username: !secret mqtt_user
  password: !secret mqtt_pass
  topic_prefix: watermeter
  discovery: false
```

------------------------------------------------------------------------

### MQTT topics

    watermeter/

Examples:

    watermeter/sensor/counter_s1_total/state
    watermeter/status

------------------------------------------------------------------------

### Home Assistant notice

Disable discovery to prevent duplicate entities:

``` yaml
discovery: false
```

Remove the MQTT device manually if already discovered.

------------------------------------------------------------------------

### Disabling MQTT

Remove the `mqtt:` block if not required.