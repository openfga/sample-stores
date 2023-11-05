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
model
  # We are using the 1.1 schema with type restrictions
  schema 1.1

# There are users
type user

# There are device groups
type device_group
  relations
    # Users can be IT admins on a group of devices
    define it_admin: [user]
    # Users can be Security Guards on a group of devices
    define security_guard: [user]

# There are devices
type device
  relations
    # Users can be IT admins on a device
    define it_admin: [user, device_group#it_admin]
    # Users can be Security Guards on a device
    define security_guard: [user, device_group#security_guard]
    # IT Admins can rename devices
    define can_rename_device: it_admin
    # Both IT Admins & Security Guards can view live video feeds
    define can_view_live_video: it_admin or security_guard
    # Both IT Admins & Security Guards can view recorded video
    define can_view_recorded_video: it_admin or security_guard
```

See the tuples and tests in the [store.yaml](./store.fga.yaml) file.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `iot` directory, run `fga model test --tests store.yaml`
