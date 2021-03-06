---
title: OpenZWave (beta)
description: Instructions on how to integrate OpenZWave with Home Assistant.
ha_category:
  - Switch
ha_release: '0.110'
ha_iot_class: Local Push
ha_config_flow: true
ha_codeowners:
  - '@cgarwood'
  - '@marcelveldt'
  - '@MartinHjelmare'
ha_domain: ozw
---

This integration allows you to utilize OpenZWave's ozwdaemon to control a Z-Wave network over MQTT.

## Requirements

- MQTT server and the [MQTT integration](/integrations/mqtt/) set up in Home Assistant.
- The [ozwdaemon](https://github.com/OpenZWave/qt-openzwave) installed and running in your network.
  For Home Assistant Supervisor there's an official add-on named OpenZWave available from the add-on store.
- Supported Z-Wave dongle compatible with OpenZWave 1.6. See this [list](/docs/z-wave/controllers/#supported-z-wave-usb-sticks--hardware-modules) of controllers. The Z-Wave controller dongle should be connected to the same host as where the ozwdaemon is running.

## Configuration

This integration can be configured using the integrations in the
Home Assistant frontend.

Menu: **Configuration** -> **Integrations**.

Click on the `+` sign to add an integration and click on **OpenZWave (beta)**.
After completing the configuration flow, the OpenZWave integration will be
available.

### Secure network key

The secure network key is set in the settings for the ozwdaemon and
not in the integration configuration.

## Services

### Service `ozw.add_node`

This service will set the controller into inclusion mode and should be used to
add a device (node) to the Z-Wave network. Call the service and then perform
the device-specific procedure, according to the device manual, to add your
device to the network. Make sure the controller is connected to the host where
the ozwdaemon is running, when performing this operation.

| Service Data Attribute | Required | Description                                                                                                                                                                                                                                      |
| ---------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `secure`               | no       | Add the new node with secure communications. [Secure network key must be set](#secure-network-key). This process will fallback to add_node (unsecure) for unsupported devices. Note that insecure devices can't directly talk to secure devices. |
| `instance_id`          | no       | The OZW Instance/Controller to use, defaults to 1.                                                                                                                                                                                               |

### Service `ozw.remove_node`

This service will set the controller into exclusion mode and should be used to
remove a device (node) from the Z-Wave network. Call the service and then
perform the device-specific procedure, according to the device manual,
to remove your device from the network. Make sure the controller is connected
to the host where the ozwdaemon is running, when performing
this operation.

| Service Data Attribute | Required | Description                                        |
| ---------------------- | -------- | -------------------------------------------------- |
| `instance_id`          | no       | The OZW Instance/Controller to use, defaults to 1. |

### Service `ozw.set_usercode`

This service will set the usercode to X at code slot Y.
Valid usercodes are at least 4 digits.

| Service Data Attribute | Required | Description                                          |
| ---------------------- | -------- | ---------------------------------------------------- |
| `entity_id`            | no       | Lock entity or list of entities to set the usercode. |
| `code_slot`            | yes      | The code slot to set the usercode into.              |
| `usercode`             | yes      | The code to set in the slot.                         |

### Service `ozw.clear_usercode`

This service will clear the usercode in code slot X.
Valid code slots are between 1-254.

| Service Data Attribute | Required | Description                                            |
| ---------------------- | -------- | ------------------------------------------------------ |
| `entity_id`            | no       | Lock entity or list of entities to clear the usercode. |
| `code_slot`            | yes      | The code slot to clear the usercode from.              |

### Service `ozw.set_config_parameter`

This service will set the specified configuration parameter to the value specified to
allow device-specific configurations. Example of this would be setting notification
LED colors on switches.

| Service Data Attribute | Required | Description                                                                                                     |
| ---------------------- | -------- | --------------------------------------------------------------------------------------------------------------- |
| `instance_id`          | no       | The OZW Instance/Controller to use, defaults to 1.                                                              |
| `node_id`              | yes      | Node id of the device to set configuration parameter to (integer).                                              |
| `parameter`            | yes      | Parameter number to set (integer).                                                                              |
| `value`                | yes      | Value to set for parameter. (String or integer value for list, string for bool parameters, integer for others). |
