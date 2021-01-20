# Tasmota-Kettle

Configuration TuyaMCU:
```
Module 54
```

Add Temperature and Time Left as Tuya Sensors:
```
Backlog TuyaMCU 71,105; TuyaMCU 81,107; TuyaMCU 11,108
```

Rule2 Will create a sensor that shows what mode the Kettle is working

```
name: "Kettle Working Mode"
icon: mdi:kettle-alert
state_topic: tele/Kettle/RESULT
value_template: >
  {% if value_json.TuyaReceived['101'].DpIdData == "00" %} 45°
  {% elif value_json.TuyaReceived['101'].DpIdData == "01" %} 60°
  {% elif value_json.TuyaReceived['101'].DpIdData == "02" %} 85°
  {% elif value_json.TuyaReceived['101'].DpIdData == "03" %} 100°
  {% elif value_json.TuyaReceived['101'].DpIdData == "04" %} Boiling
  {% elif value_json.TuyaReceived['101'].DpIdData == "05" %}
    Custom {{ states('input_number.kettle_temp_choice') | int }}°
  {% elif value_json.TuyaReceived['101'].DpIdData == "06" %} Stop
  {% else %} 
   {{ states('sensor.kettle_working_mode') }}
  {% endif %}
unique_id: '%deviceid%_kettle_working_mode'
device:
  connections: [[mac,'%macaddr%']]
availability_topic: tele/%topic%/LWT
payload_available: Online
payload_not_available: Offline
```

Create rule2:
```
rule2 on system#boot do publish2
homeassistant/sensor/%macaddr%_kettle_working_mode/config
{"name":"Kettle Working Mode","icon":"mdi:kettle-alert","state_topic":"tele/Kettle/RESULT","value_template":"{% if value_json.TuyaReceived['101'].DpIdData == \"00\" %} 45° {% elif value_json.TuyaReceived['101'].DpIdData == \"01\" %} 60° {% elif value_json.TuyaReceived['101'].DpIdData == \"02\" %} 85° {% elif value_json.TuyaReceived['101'].DpIdData == \"03\" %} 100° {% elif value_json.TuyaReceived['101'].DpIdData == \"04\" %} Boiling {% elif value_json.TuyaReceived['101'].DpIdData == \"05\" %}\n  Custom {{ states('input_number.kettle_temp_choice') | int }}°\n{% elif value_json.TuyaReceived['101'].DpIdData == \"06\" %} Stop {% else %} \n {{ states('sensor.kettle_working_mode') }}\n{% endif %}\n","unique_id":"%deviceid%_kettle_working_mode","device":{"connections":[["mac","%macaddr%"]]},"availability_topic":"tele/%topic%/LWT","payload_available":"Online","payload_not_available":"Offline"}
endon
```

Rule3 

```
name: "Kettle Base Status"
icon: mdi:kettle-alert
state_topic: tele/Kettle/RESULT
value_template: >
  {% if value_json.TuyaReceived['106'].DpIdData == "01" %} Removed
  {% elif value_json.TuyaReceived['106'].DpIdData == "02" %} Idle
  {% elif value_json.TuyaReceived['106'].DpIdData == "03" %} Heating
  {% elif value_json.TuyaReceived['106'].DpIdData == "04" %} Cooling
  {% elif value_json.TuyaReceived['106'].DpIdData == "05" %} Hold_Temp
  {% else %} 
   {{ states('sensor.kettle_base_status') }}
  {% endif %}
unique_id: '%deviceid%_kettle_base_status'
device:
  connections: [[mac,'%macaddr%']]
availability_topic: tele/%topic%/LWT
payload_available: Online
payload_not_available: Offline
```

Create rule3:
```
rule3 on system#boot do publish2 homeassistant/sensor/%macaddr%_kettle_base_status/config
{"name":"Kettle Base Status","icon":"mdi:kettle-alert","state_topic":"tele/Kettle/RESULT","value_template":"{% if value_json.TuyaReceived['106'].DpIdData == \"01\" %} Removed {% elif value_json.TuyaReceived['106'].DpIdData == \"02\" %} Idle {% elif value_json.TuyaReceived['106'].DpIdData == \"03\" %} Heating {% elif value_json.TuyaReceived['106'].DpIdData == \"04\" %} Cooling {% elif value_json.TuyaReceived['106'].DpIdData == \"05\" %} Hold_Temp {% else %} \n {{ states('sensor.kettle_base_status') }}\n{% endif %}\n","unique_id":"%deviceid%_kettle_base_status","device":{"connections":[["mac","%macaddr%"]]},"availability_topic":"tele/%topic%/LWT","payload_available":"Online","payload_not_available":"Offline"}
endon
```

Rule1 
```
name: "Kettle Error Notification"
icon: mdi:kettle-alert
state_topic: tele/Kettle/RESULT
value_template: >
  {% if value_json.TuyaReceived['104'].DpIdData == "00" %} Good
  {% elif value_json.TuyaReceived['104'].DpIdData == "01" %} Empty
  {% elif value_json.TuyaReceived['104'].DpIdData == "04" %} Overheated
  {% else %} {{ states('sensor.kettle_error_notification') }}
  {% endif %}
unique_id: '%deviceid%_kettle_error_notification'
device:
  connections: [[mac,'%macaddr%']]
availability_topic: tele/%topic%/LWT
payload_available: Online
payload_not_available: Offline
```

Create rule1:
```
rule1 on system#boot do publish2 homeassistant/sensor/%macaddr%_kettle_error_notification/config
{"name":"Kettle Error Notification","icon":"mdi:kettle-alert","state_topic":"tele/Kettle/RESULT","value_template":"{% if value_json.TuyaReceived['104'].DpIdData == \"00\" %} Good {% elif value_json.TuyaReceived['104'].DpIdData == \"01\" %} Empty {% elif value_json.TuyaReceived['104'].DpIdData == \"04\" %} Overheated {% else %} {{ states('sensor.kettle_error_notification') }} {% endif %}\n","unique_id":"%deviceid%_kettle_error_notification","device":{"connections":[["mac","%macaddr%"]]},"availability_topic":"tele/%topic%/LWT","payload_available":"Online","payload_not_available":"Offline"}
endon
```

Enable the rules and restart
```
Backlog rule1 1; rule2 1; rule3 1; restart 1
```
