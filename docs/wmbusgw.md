# How to use `wmbusgw` with `wmbusmeters` HA addon

You can find wmbusmeters Home Assistant addon here:
https://github.com/weetmuts/wmbusmeters

## 1. `wmbusgw` configuration
You can use UDP or TCP as transport layer.

UDP example is below:
```yaml
wmbusgw:
  clients:
    - name: "wmbusmeters_udp"
      ip_address: "10.1.2.3"
      port: 7011
      format: RTLWMBUS
      transport: UDP
```


TCP example is below:
```yaml
wmbusgw:
  clients:
    - name: "wmbusmeters_udp"
      ip_address: "10.1.2.3"
      port: 7011
      format: RTLWMBUS
      transport: TCP
```

  - **ip_address**: IP address of Home Assistant
  - **port**: port where telegrams will be sent
  - **format**: telegram format should be set to ``RTLWMBUS``


## 2. `wmbusmeters` addon configuration

> **_NOTE:_**  [Pull request](https://github.com/weetmuts/wmbusmeters/pull/752) for wmbusmeters is under review. Until its acceptance/merge you have to install HA addon from [unofficial repo](https://github.com/SzczepanLeon/wmbusmeters): 

After instalation pleas go to addon configuration and enable port forwarding by clicking `Show disabled ports`:
![](https://github.com/SzczepanLeon/esphome-components/blob/main/docs/disabled_ports.png)
On right side please write port number from `wmbusgw` configuration (in this example *7011*).


In `wmbusmeters` HA addon configuration please change device to:

UDP case:
```yaml
device=rtlwmbus:CMD(/usr/bin/nc -lku 9011)
```


TCP case:
```yaml
device=rtlwmbus:CMD(/usr/bin/nc -lku 9022)
```

Add meters:
```yaml
meters:  
  - |-  
    name=water_from_izar  
    driver=izar  
    id=123456  
    key=  
  - |-  
    name=water_from_apator  
    driver=apator162  
    id=04175054  
    key=00000000000000000000000000000000
```

Add MQTT configuration:
```yaml
mqtt:  
  host: 10.1.2.44  
  port: "1883"  
  user: myUser  
  password: myPass
```


## 3. Home Assistant configuration
Now you can add meter in HA:

```yaml
mqtt:
  sensor:
  - name: "Main water meter"
    unique_id: main_waterMeter_total_m3
    object_id: main_waterMeter_total_m3
    state_topic: "wmbusmeters/water_from_izar"
    value_template: "{{ value_json['total_m3']|default(0.000) }}"
    unit_of_measurement: m³
    device_class: water
    state_class: total_increasing
    icon: mdi:water
```

You can also enable and use MQTT auto discovery feature (very limited).
![](https://github.com/SzczepanLeon/esphome-components/blob/main/docs/mqtt_discovery.png)

And check discovered metters in MQTT integration.