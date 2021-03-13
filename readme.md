# Room Presence
This automates turning on and off entities based on a trigger like one or more presence or door sensors but can be any entity. After the timeout expires it will turn off the entities.

## Installation

Setup NetDaemon runtime - https://netdaemon.xyz/docs/started/installation

### HACS
1. Add this repo as a custom repository and select Integration type
2. Click Install
3. Restart NetDaemon

### Manual Setup
1. Copy the files from this repo into apps/RoomPresence folder
2. Restart NetDaemon

## Config

### Basic
```yaml
presence:
  class: Presence.RoomPresence
  rooms:
  - name: Study
    presence_entity_ids:
      - binary_sensor.study
    control_entity_ids:
      - light.study
```
### Advanced
```yaml
presence:
  class: Presence.RoomPresence
  rooms:
  - name: Master
    timeout: 300
    nightTimeout: 90
    presence_entity_ids:
    - binary_sensor.master_motion
    - binary_sensor.master_motion_2
    control_entity_ids:
    - light.master
    - light.master_wall
    lux_entity_id: sensor.master_lux
    lux_limit_entity_id: input_number.master_lux_limit
    night_time_entity_id: input_select.house_mode
    night_time_entity_states:
    - night
    - sleeping
    night_control_entity_ids:
    - light.master_2
    circadian_switch_entity_id: switch.circadian_lighting_circadian_master
```

### Parameters
|Parameters|Description|Type|Comments|
|----------|-----------|----|--------|
|`name`|Name of your room|String|Required|
|`control_entity_ids`|This is a list of EntityIds that you want to control|List|Required|
|`presence_entity_ids`|This is a list of EntityIds that you want to use as a trigger|List|Required|
|`keep_alive_entity_ids`|This is a list of EntityIds that you want to use to keep your timer alive even if your presence entities are off|List|Optional|
|`night_control_entity_ids`|This is a list of EntityIds that you want to control at Night time|List|Optional|
|`night_time_entity_id`|This is an Entity that indicates if it is Night time|String|Required when using `night_time_entity_id`|
|`night_time_entity_states`|This is a list of states that indicates Night time|List|Required when using `night_time_entity_id`|
|`timeout`|This is the minimum amount of time your control entities will stay on|Int|Optional <br><br>Default: 300 seconds. <br><br>Each time there is a new Presence event the timeout resets|
|`nightTimeout`|This is the minimum amount of time your control entities will stay on at Night time|Int|Optional<br><br>Default: 60 Seconds|
|`override_timeout`|This is the minimum amount of time your control entities will stay on during override|Int|Optional<br><br>Default: 900 Seconds|
|`lux_entity_id`|This is the Entity that provides Lux information|String|Optional|
|`lux_limit`|When the lux is below this threshold your control entities will turn on|Int|Optional<br><br>Default: 40<br><br>This is a configurable lux threshold and will take precedence `lux_limit_entity_id`|
|`lux_limit_entity_id`|When the lux is below this threshold your control entities will turn on|String|Optional<br><br>This is an Entity that provides the lux threshold|
|`circadian_switch_entity_id`|This disables Circadian Lighting by switching off the Entity when the Brightness/Colour Temp is changed manually|String|Optional<br><br>Re-enables once Room state is Idle|

## Room Presence States
- **Idle**<br>When there is no Presence
- **Active**<br>When there is/was Presence and there is an active timer and control entities have turned on. After timeout they will turn off
- **Override**<br>When the Guard Dog finds control entities that was turned on manually. After override timeout they will turn off
- **Disabled**<br>When the room's enabled entity has been turned off

## Remarks
Two entities are created for each room
- `sensor.room_presence_<roomName>`<br>This provides the state, it also provides additional information as attributes.
- `switch.room_presence_enabled_<roomName>`<br>This is to enable/disable the automation for the specific room