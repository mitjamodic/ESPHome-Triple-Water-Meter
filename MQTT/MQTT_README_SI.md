### Kaj je MQTT in zakaj ga uporabiti

MQTT podpora omogoča, da watermeter naprava deluje kot **splošen vir
podatkov**, neodvisno od Home Assistanta.

MQTT je priporočljiv, če: - želiš podatke uporabljati v **Node-RED** -
zapisuješ podatke v **InfluxDB / Grafana** - uporabljaš drug sistem
(ioBroker, OpenHAB, lastne aplikacije) - želiš večjo zanesljivost in
lažje spremljanje statusa naprave

⚠️ **MQTT ni obvezen** za delovanje z Home Assistantom.

MQTT je implementiran kot ločen ESPHome paket.

Če želiš MQTT podporo:
- prenesi `mqtt.yaml`
- shrani `mqtt.yaml` v mapo `MQTT` kjer je glavni YAML
- vključi ga preko `packages`


------------------------------------------------------------------------

### Osnovna MQTT konfiguracija

MQTT konfiguracija je definirana v `mqtt.yaml` datoteki, občutljivi
podatki pa se hranijo v `secrets.yaml`.

#### `secrets.yaml`

``` yaml
mqtt_broker: 192.168.1.10
mqtt_user: mqtt_user
mqtt_pass: mqtt_password
```

#### ESPHome konfiguracija

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

### MQTT teme

Vse MQTT teme so združene pod osnovno temo:

    watermeter/

Primeri objav:

    watermeter/sensor/counter_s1_total/state
    watermeter/sensor/counter_s1_daily/state
    watermeter/sensor/counter_s2_total/state
    watermeter/status

------------------------------------------------------------------------

### Status naprave

Topic:

    watermeter/status

Payload: - `online` - `offline`

------------------------------------------------------------------------

### Home Assistant -- pomembno opozorilo

Če uporabljaš Home Assistant preko **ESPHome integracije**, priporočamo:

``` yaml
discovery: false
```

Po spremembi je potrebno **ročno odstraniti MQTT napravo** iz:

    Settings → Devices & Services → MQTT → Devices

------------------------------------------------------------------------

### Izklop MQTT

Če MQTT ne uporabljaš, odstrani ali zakomentiraj `mqtt:` blok.