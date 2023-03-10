# Documentation - API - ASK

With the TELL interfaces, one can **send information to** Traffic Control.

These interfaces are accessible via `<server_domain>/api/tell/<interface>`.

## Overview

- [Format of the request payload and response](#format-of-the-request-payload-and-response)
- [Interfaces](#interfaces)
    - [aircraft_location](#aircraft_location)
    - [aircraft_power](#aircraft_power)
    - [flight_data](#flight_data)

## Format of the request payload and response

### Request

The **payload** that is sent to Traffic Control is a JSON formatted string. It
can be transmitted via GET or POST.

| FIELD     | TYPE              | REQ / OPT | INFORMATION                                |
|-----------|-------------------|-----------|--------------------------------------------|
| drone_id  | string            | required  | ID of the drone.                           |
| data_type | string (constant) | required  | The value is specified for each interface. |
| data      | dictionary        | optional  | Additional data.                           |

<details><summary>Sample payload without data field.</summary><p>

```json
{
    "drone_id": "demo_drone",
    "data_type": "register_drone"
}
```

</details>

<details><summary>Sample payload with data field.</summary><p>

```json
{
    "drone_id": "demo_drone",
    "data_type": "aircraft_power",
    "data": {
        "battery_remaining": 4500,
        "battery_remaining_percent": 42,
        "remaining_flight_time": 550,
        "remaining_flight_radius": 4320.5
    }
}
```

</details>

### Response

The response from Traffic Control is a JSON formatted string.

| FIELD         | TYPE       | VALUE SET? | INFORMATION               |
|---------------|------------|------------|---------------------------|
| executed      | boolean    | always     | Was the command executed? |
| errors        | list       | always     |                           |
| warnings      | list       | always     |                           |
| response_data | dictionary | optional   |                           |

If one or more errors occured, they are added to the errors list.

| FIELD   | TYPE   | VALUE SET? | INFORMATION                   |
|---------|--------|------------|-------------------------------|
| err_id  | int    | always     |                               |
| err_msg | string | always     | Human readable error message. |

<details><summary>Sample error</summary><p>

```json
{
    "err_id": 1,
    "err_msg": "I am an error!"
}
```

</details>

If one or more warnings occured, they are added to the warnings list.

| FIELD    | TYPE   | VALUE SET? | INFORMATION                     |
|----------|--------|------------|---------------------------------|
| warn_id  | int    | always     |                                 |
| warn_msg | string | always     | Human readable warning message. |

<details><summary>Sample warning</summary><p>

```json
{
    "warn_id": 1,
    "warn_msg": "I am a warning!"
}
```

</details>

<details><summary>Sample response: Successful</summary><p>

```json
{
    "executed": true,
    "errors": [],
    "warnings": []
}
```

</details>

<details><summary>Sample response: Successful with one warning</summary><p>

```json
{
    "executed": true,
    "errors": [],
    "warnings": [
        {
            "warn_id": 1,
            "warn_msg": "New Traffic Control version available. Please update!"
        }
    ]
}
```

</details>


## Interfaces

### aircraft_location

One can send information about the location of a drone.

#### Request

The `data_type` is `aircraft_location`.

**Payload - data field (required)**

| FIELD                    | TYPE    | REQ / OPT | INFORMATION                                  |
|--------------------------|---------|-----------|----------------------------------------------|
| gps_signal_level         | int     | required  | 0 (no gps signal) - 5 (very good gps signal) |
| gps_satellites_connected | int     | required  | Number of gps-satellites connected.          |
| gps_valid                | boolean | required  | Whether the drone has a (valid) gps-signal.  |
| gps_lat                  | float   | required  | Latitude.                                    |
| gps_lon                  | float   | required  | Longitude.                                   |
| altitude                 | float   | required  | Altitude in meters.                          |
| velocity_x               | float   | required  | Velocity X in meters / second.               |
| velocity_y               | float   | required  | Velocity Y in meters / second.               |
| velocity_z               | float   | required  | Velocity Z in meters / second.               |
| pitch                    | float   | required  | [-180;180].                                  |
| yaw                      | float   | required  | [-180;180].                                  |
| roll                     | float   | required  | [-180;180].                                  |

<details><summary>Sample payload</summary><p>

```json
{
    "drone_id": "demo_drone",
    "data_type": "aircraft_location",
    "data": {
        "gps_signal_level": 5,
        "gps_satellites_connected": 12,

        "gps_valid": true,
        "gps_lat": 48.26586,
        "gps_lon": 11.67436,

        "altitude": 42,

        "velocity_x": 0,
        "velocity_y": 0,
        "velocity_z": 0,

        "pitch": 0,
        "yaw": 0,
        "roll": 0
    }
}
```

</details>

#### Response

Standard response. The `response_data` field is never set.

### aircraft_power

One can send information about the state of charge and range of a drone.

#### Request

The `data_type` is `aircraft_power`.

**Payload - data field (required)**

| FIELD                     | TYPE  | REQ / OPT | INFORMATION |
|---------------------------|-------|-----------|-------------|
| battery_remaining         | int   | required  | In mAh.     |
| battery_remaining_percent | int   | required  | In %.       |
| remaining_flight_time     | int   | required  | In seconds. |
| remaining_flight_radius   | float | required  | In meters.  |

<details><summary>Sample payload</summary><p>

```json
{
    "drone_id": "demo_drone",
    "data_type": "aircraft_power",
    "data": {
        "battery_remaining": 4500,
        "battery_remaining_percent": 42,
        "remaining_flight_time": 550,
        "remaining_flight_radius": 4320.5
    }
}
```

</details>

#### Response

Standard response. The `response_data` field is never set.

### flight_data

One can send information about the takeoff / landing time and coordinates of a
drone.

#### Request

The `data_type` is `flight_data`.

**Payload - data field (required)**

| FIELD             | TYPE     | REQ / OPT | INFORMATION                 |
|-------------------|----------|-----------|-----------------------------|
| takeoff_time      | int      | required  | UNIX timestamp.             |
| takeoff_gps_valid | boolean  | required  | GPS-coordinates valid?      |
| takeoff_gps_lat   | float    | required  | Latitude.                   |
| takeoff_gps_lon   | float    | required  | Longitude.                  |
| landing_time      | int      | required  | UNIX timestamp.             |
| landing_gps_valid | boolean  | required  | GPS-coordinates valid?      |
| landing_gps_lat   | float    | required  | Latitude.                   |
| landing_gps_lon   | float    | required  | Longitude.                  |
| operation_modes   | [string] | required  | The last X Operation Modes. |

<details><summary>Sample payload</summary><p>

```json
{
	"drone_id": "demo_drone",
	"data_type": "flight_data",
	"data": {
		"takeoff_time": 1678264333,
		"takeoff_gps_valid": "true",
		"takeoff_gps_lat": 48.26586,
		"takeoff_gps_lon": 11.67436,
		"landing_time": 1678264389,
		"landing_gps_valid": "true",
		"landing_gps_lat": 48.26586,
		"landing_gps_lon": 11.67436,
		"operation_modes": ["OnGround", "Landing", "Hovering", "TakeOff", "OnGround"]
	}
}
```

</details>

#### Response

Standard response. The `response_data` field is never set.
