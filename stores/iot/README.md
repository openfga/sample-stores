# OpenFGA IoT Sample Store

* **Title**: **IoT** 
* **Documentation**: https://openfga.dev/docs/modeling/advanced/iot
* **Playground**: https://play.fga.dev/sandbox/?store=iot

## Table of Content
- [Use-Case](#use-case)
  - [Requirements](#requirements)
  - [Scenario](#scenario)
  - [Expected Outcomes](#expected-outcomes)
- [Modeling in OpenFGA](#modeling-in-openfga)
  - [Model](#model)
  - [Tuples](#tuples)
  - [Assertions](#assertions)
- [Try It Out](#try-it-out)

## Use-Case

### Requirements

- Security guards have access to view live and recorded video from devices.
- IT Admins can view live and recorded videos, as well as rename devices.
- Devices can be grouped into groups. Security guards with access to the Device Group are Security Guards on each device in that group. Similarly for IT Admins.

### Scenario

There are four users: Anne, Beth, Charles, Dianne

These users have the following roles and permissions:
- Anne is a Security Guard with access to only Device 1
- Beth is an IT Admin with access to only Device 1
- Device Group 1 includes Device 2 and Device 3
- Charles is a Security Guard with access to Device 1 and everything in Device Group 1
- Dianne is an IT Admin with access to Device 1 and everything in Device Group 1

### Expected Outcomes

- Anne **is not** an IT Admin on Device 1
- Anne can view the recorded video of Device 1
- Dianne can rename Device 2
- Charles **cannot** rename Device 2

## Modeling in OpenFGA

### Model

```python
# There are users
type user
# There are devices
type device
  relations
    # Users can be IT admins on a device
    define it_admin as self
    # Users can be Security Guards on a device
    define security_guard as self
    # IT Admins can rename devices
    define can_rename_device as it_admin
    # Both IT Admins & Security Guards can view live video feeds
    define can_view_live_video as it_admin or security_guard
    # Both IT Admins & Security Guards can view recorded video
    define can_view_recorded_video as it_admin or security_guard
# There are groups of devices
type device_group
  relations
    # Users can be IT admins on a group of devices
    define it_admin as self
    # Users can be Security Guards on a group of devices
    define security_guard as self
```

> Note: The OpenFGA API accepts a JSON syntax for the authorization model that is different from the DSL shown above
>       To switch between the two syntaxes, you can use the [@openfga/syntax-transformer npm package](https://www.npmjs.com/package/@openfga/syntax-transformer) or the [Auth0 FGA Playground](https://play.fga.dev)

You can see a representation of this model in the JSON syntax accepted by the OpenFGA API in [authorization-model.json](./authorization-model.json).

### Tuples

| User                               | Relation       | Object              | Description                                                       |
|------------------------------------|----------------|---------------------|-------------------------------------------------------------------|
| beth                               | it_admin       | device:1            | Beth is an IT Admin on Device 1                                   |
| dianne                             | it_admin       | device:1            | Dianne is an IT Admin on Device 1                                 |
| anne                               | security_guard | device:1            | Anne is a Security Guard on Device 1                              |
| charles                            | security_guard | device:1            | Charles is a Security Guard on Device 1                           |
| device_group:group1#it_admin       | it_admin       | device:2            | IT Admins on Device Group 1 are IT Admins on Device 2             |
| device_group:group1#security_guard | security_guard | device:2            | Security Guards on Device Group 1 are Security Guards on Device 2 |
| device_group:group1#it_admin       | it_admin       | device:3            | IT Admins on Device Group 1 are IT Admins on Device 3             |
| device_group:group1#security_guard | security_guard | device:3            | Security Guards on Device Group 1 are Security Guards on Device 3 |
| dianne                             | it_admin       | device_group:group1 | Diane is a Security Guard on Device Group 1                       |
| charles                            | security_guard | device_group:group1 | Charles is a Security Guard on Device Group 1                     |

These are represented in this file: [tuples.json](./tuples.json).

### Assertions

| User    | Relation                | Object   | Allowed? |
|---------|-------------------------|----------|----------|
| anne    | it_admin                | device:1 | No       |
| anne    | can_view_recorded_video | device:1 | Yes      |
| dianne  | can_rename_device       | device:2 | Yes      |
| charles | can_rename_device       | device:2 | No       |

These are represented in this file: [assertions.json](./assertions.json).

## Try It Out

Use `iot` as the SAMPLE_STORE, and follow the rest of the instructions on [Try it out section in the main README](https://github.com/openfga/sample-stores#try-it-out).
