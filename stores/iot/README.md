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

To try this out, you need the following tools installed:
- `docker`
- `git`
- `curl`
- `jq`

1. Ensure you have [openfga](https://github.com/openfga/openfga) up and running.
```
docker run -p 127.0.0.1:8080:8080 openfga/openfga run
```

2. Clone this repository
```
git clone https://github.com/openfga/sample-stores.git openfga-sample-stores && cd openfga-sample-stores
```

3. Navigate into the iot sample store
```
cd stores/iot
```

4. Loading data into your store
```shell
FGA_API_URI=http://localhost:8080

# Create the store
STORE_ID=$(curl -X POST $FGA_API_URI/stores -d @store.json | jq -r '.id')
# Post the authorization model
AUTHORIZATION_MODEL_ID=$(curl -X POST $FGA_API_URI/stores/$STORE_ID/authorization-models -d @authorization-model.json | jq -r '.authorization_model_id')
# Write the tuples (Note: max tuples per write request = 10)
cat tuples.json | jq -c '. | _nwise(10) | { "writes": { "tuple_keys": . } }' | (
    while read id; do
        curl -X POST $FGA_API_URI/stores/$STORE_ID/write -d $id
    done
)
# Update the assertions for that authorization model
cat assertions.json | jq '. |= { "assertions": .  }' | curl -X PUT $FGA_API_URI/stores/$STORE_ID/assertions/$AUTHORIZATION_MODEL_ID -d @-

# Run sample checks (here we are just looping through our assertions and issuing checks for them)
cat assertions.json | jq -c '. | _nwise(1) | { "tuple_key": { "user": .[0].tuple_key.user, "relation": .[0].tuple_key.relation, "object": .[0].tuple_key.object } }' | (
    while read body; do
        FGA_ALLOWED=$(curl -s -X POST $FGA_API_URI/stores/$STORE_ID/check -d $body | jq '.allowed')
        FGA_USER=$(echo $body | jq -rc '.tuple_key.user')
        FGA_RELATION=$(echo $body | jq -rc '.tuple_key.relation')
        FGA_OBJECT=$(echo $body | jq -rc '.tuple_key.object')
        echo "check(user=$FGA_USER, relation=$FGA_RELATION, object=$FGA_OBJECT); allowed: $FGA_ALLOWED"
    done
)
```
