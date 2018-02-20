---
title: Device42 API Reference

language_tabs:
  - shell

toc_footers:
 - <a href='https://www.device42.com/' target="_blank">Device42 Website</a>
 - <a href='https://docs.device42.com/' target="_blank">Device42 Documentation</a>
---

# API Overview

Restful API are supported in Device42 as one of the primary methods of entering, editing and retrieving data.

The API documentation has samples in curl for now and we will be adding other programming languages in future. You can refer to our github repositories at https://github.com/device42 for some sample code in python.

The RESTful API's enforce the role-based security that is created with the Device42 app. If you want a user to have access via the API, but not via the UI - deselect "Staff Status" for that user from UI Tools > Admins & Permissions > Administrators.

If you have any questions/suggestions, please send us a note to support at device42.com.

# Sample API Code

**Sample Code with API Calls in the Python and .Net Programming Languages**

The following programs written in the Python programming language are available at: [Device42 sample programs:](https://github.com/device42/Device42-AutoDiscovery-Scripts)

api-sample.py: Runs against a single windows system and uploads info to device42 appliance

* ad-sample.py: Can run against Active directory computers, servers or a given list and upload discovered systems’ info to device42 appliance.
* d42_api_linux_upload_sample_script.py: Runs on a single \*nix based system and uploads info to device42 appliance.
* sample-script-facter-facts-to-d42: Runs on puppet master and uploads nodes info from facter fact files to device42 appliance.
* d42_api_solaris_sample_script.py: Runs on an individual solaris system and uploads info to device42 appliance.

The csv2d42apis.py sample program shows how to create a CSV file of data to import into device42. It reads a CSV file, matches columns to arguments for APIs and sends data to device42 via POST or PUT. This program can be found at: [Device42 sample CSV importer.](https://github.com/device42/API_Helpers).

The Auto Discovery Client source code can be found at [Device42 Auto Discovery Client](https://docs.device42.com/auto-discovery/auto-discovery-client/). This is a .net program that uses the device42 Restful API’s to load the discovered data.

* Note: when sending data, if specifying content-type use "application/x-www-form-urlencoded".

# Response & Status Messages

**Responses**

The response to most API calls will have the following format:

{"msg":["PartModel added/updated", **17, "RAM Acme 123456", true, true**],"code":0}

Using the example above:

"17" = ID of the object

"RAM Acme 123456" = representation of the object

"true, true" = object added and is new.

"true, false" = object updated.

"false, false" = no changes.

Please let us know if you notice something odd with a response and we will fix it :)

**Status Codes:**

Code 0 = success: e.g. = {“msg”: “device added or updated”, **“code”: 0**}

"HTTP/1.1 200 OK" = success - all other responses are errors and will display [error code](/#error-codes) e.g.: HTTP/1.1 500 INTERNAL SERVER ERROR

# API Get Limits and Offsets

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/devices/?limit=50 --insecure
```

> The above command returns the first 50 devices.

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/macs/?offset=100 --insecure
```

> The above command will start results at the 100th mac address

```shell
curl -X GET -u 'admin:adm!nd42' "https://yourdevice42address/api/1.0/assets/?offset=42&limit=42" --insecure
```

> The above command will return 42 assets, starting at the 42nd.

In Device42's global settings we have the option to enforce API GET limits and this setting is recommended for better performance. To set this value from Device42, go to Tools>Settings>Global Settings and the click Edit in the top right. Toward the bottom of the screen you will see the API section. First check the "Enforce API GET Limits" checkbox, then enter a value for API GET Limit. A good limit to start with is 500 or 1000, depending on the performance you experience.

After setting this limit all GET calls below can have the results augmented via "offset" and "limit" parameters to tell Device42 which value to start returning results from, and how many results to return at maximum.

# Buildings

## Get All Buildings

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/buildings/ --insecure
```

> The above command returns results like this:

```json
{
  "buildings": [
    {
      "address": "879 main st",
      "building_id": 3,
      "contact_name": "roger",
      "contact_phone": "1234567890",
      "custom_fields": [],
      "groups": "Prod_East:no, Corp:yes",
      "name": "Las Vegas Office",
      "notes": "super critical"
    },
    {
      "address": "123 main st",
      "building_id": 1,
      "contact_name": "roger",
      "contact_phone": "1234567890",
      "custom_fields": [],
      "groups": "Prod_East:no, Corp:yes",
      "name": "New Haven DC",
      "notes": "super critical"
    },
    {
      "address": "456 main st",
      "building_id": 2,
      "contact_name": "roger",
      "contact_phone": "1234567890",
      "custom_fields": [],
      "groups": "Prod_East:no, Corp:yes",
      "name": "Palm Beach Bldg",
      "notes": "super critical"
    }
  ]
}
```

Retrieve information about all buildings.

**`GET /api/1.0/buildings/`**

### Query string parameters (Added in v6.2.0)

| Parameter | Description                               |
| --------- | ----------------------------------------- |
| name      | filter by name of the building (contains) |

## Create/Update Building

```shell
curl -v -X POST -d "name=main office&address=123 main st&contact_name=roger&contact_phone=1234567890&notes=super critical" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/buildings/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["Building added/updated successfully", 4, "main office"],
  "code": 0
}
```

Create/update buildings.

**`POST /api/1.0/buildings/`**

### Parameters

| Parameter     | Use      | Description                                                                                                                                                                                                                                                                                                     |
| ------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name          | required | Name of building.                                                                                                                                                                                                                                                                                               |
| address       | optional |
| contact_name  | optional |
| contact_phone | optional |
| notes         | optional |
| groups        | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. If this parameter is present with no value, all groups are deleted. |

## Delete Building

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/buildings/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the building with the building id supplied as the required argument.

**`DELETE /api/1.0/buildings/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | building id |

## Custom Fields

```shell
curl -X PUT -d "value=42&key=bldgID&name=main office" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/custom_fields/building/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["custom key pair values added or updated", 4, "main office"],
  "code": 0
}
```

Create/update custom fields for buildings.

**`PUT /api/1.0/custom_fields/building/`**

### Parameters

| Parameter          | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| name               | required                        | Name of the building.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| key                | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type               | optional                        | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value              | optional                        |
| notes              | optional                        |
| groups             | optional                        | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. If this parameter is present with no value, all groups are deleted.                                                                                                                                                                                  |
| clear_value        | optional                        | yes to clear existing value for that field (added in v6.1.0)                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| bulk_fields        | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2 (added in v6.4.1)                                                                                                                                                                                                                                                                                                                                                                           |

<aside class="notice">
value2 has been deprecated since v6.1.0
</aside>
# Rooms

## Get All Rooms

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/rooms/ --insecure
```

> The above command returns results like this:

```json
{
  "rooms": [
    {
      "building": "New Haven DC",
      "building_id": 1,
      "custom_fields": [],
      "groups": "Prod_East:no, Corp:yes",
      "name": "1st floor",
      "notes": "super critical",
      "room_id": 1
    },
    {
      "building": "Las Vegas Office",
      "building_id": 3,
      "custom_fields": [],
      "groups": "Prod_East:no, Corp:yes",
      "name": "3rd Floor",
      "notes": "super critical",
      "room_id": 6
    }
  ]
}
```

Retrieve information about all rooms.

**`GET /api/1.0/rooms/`**

### Query string parameters (Added in v6.2.0)

| Parameter   | Description                                                                  |
| ----------- | ---------------------------------------------------------------------------- |
| name        | filter by name of the room (contains)                                        |
| building_id | filter by ID of the building                                                 |
| building    | filter by name of the building (exact), only used if building_id not present |

## Get a Specific Room

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/rooms/<id>/ --insecure
```

> The above command returns results like this:

```json
{
  "building": "New Haven",
  "reverse_yaxis": "no",
  "name": "NHDC1",
  "notes": "Demo Room",
  "devices": [
    {
      "device": {
        "uuid": "",
        "serial_no": "",
        "asset_no": "",
        "name": "CRAC2",
        "device_url": "/api/1.0/devices/id/123/",
        "type": "other",
        "device_id": 123
      }
    }
  ],
  "grid_cols": 10,
  "room_id": 1,
  "reverse_xaxis": "no",
  "grid_rows": 9,
  "building_id": 1,
  "racks": [
    {
      "rack": {
        "orientation": "left",
        "numbering_start_from_bottom": "yes",
        "custom_fields": [],
        "size": 42,
        "available_u": 35,
        "row": "1",
        "rack_id": 20,
        "rack_middle_option": "Start at",
        "number_between_us": true,
        "tags": [],
        "col_size": 1,
        "start_col": 3,
        "row_size": 1,
        "first_number": 0,
        "manufacturer": "APC Inc.",
        "building": "New Haven",
        "start_row": 6,
        "name": "NH-DC1-01",
        "room": "NHDC1",
        "notes": "",
        "asset_no": "",
        "rack_url": "/api/1.0/racks/20/"
      }
    },
    {
      "rack": {
        "building": "New Haven",
        "number_between_us": true,
        "orientation": "right",
        "tags": [],
        "numbering_start_from_bottom": "yes",
        "col_size": 1,
        "rack_middle_option": "Start at",
        "asset_no": "",
        "name": "NH-DC1-18",
        "available_u": 32,
        "rack_url": "/api/1.0/racks/90/",
        "room": "NHDC1",
        "start_col": 8,
        "row_size": 1,
        "start_row": 6,
        "first_number": 1,
        "size": 42,
        "rack_id": 90,
        "notes": "",
        "custom_fields": [],
        "row": ""
      }
    },
    {
      "rack": {
        "building": "New Haven",
        "number_between_us": true,
        "orientation": "right",
        "tags": [],
        "numbering_start_from_bottom": "yes",
        "col_size": 1,
        "rack_middle_option": "Start at",
        "asset_no": "",
        "name": "NH-DC1-26",
        "available_u": 40,
        "rack_url": "/api/1.0/racks/106/",
        "room": "NHDC1",
        "start_col": 8,
        "row_size": 1,
        "start_row": 5,
        "first_number": 1,
        "size": 42,
        "rack_id": 106,
        "notes": "",
        "custom_fields": [],
        "row": ""
      }
    }
  ],
  "assets": [
    {
      "asset_id": 30,
      "serial_no": "",
      "type": "Breaker Panel",
      "name": "Power1",
      "asset_no": "0084"
    }
  ]
}
```

Retrieve detailed information about a specific rooms includes racks, devices and objects directly related to that room.

**`GET /api/1.0/rooms/<ID>/`**

### URL Parameters

| Parameter | Description                    |
| --------- | ------------------------------ |
| ID        | The ID of the room to retrieve |

## Create or update a Room

```shell
curl -X POST -d "name=2nd Floor" -d "building=main office"  -d "notes=super critical"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/rooms/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["Room added successfully", 7, "2nd Floor"],
  "code": 0
}
```

Create or update a room.

**`POST /api/1.0/rooms/`**

### URL Parameters

| Parameter                 | Use      | Description                                                                                                                                                                                                                                                                                                     |
| ------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                      | required | Name of the room                                                                                                                                                                                                                                                                                                |
| building_id or building   | required | [Building ID](#get-all-buildings) Building name can be used if unique, and must already exists.                                                                                                                                                                                                                 |
| notes                     | optional |
| horizontal_grid_numbering | optional | numeric or alphabetic, numeric by default                                                                                                                                                                                                                                                                       |
| horizontal_grid_start     | optional |
| vertical_grid_numbering   | optional | numeric or alphabetic, numeric by default                                                                                                                                                                                                                                                                       |
| vertical_grid_start       | optional |
| uom                       | optional | unit of measurement                                                                                                                                                                                                                                                                                             |
| height                    | optional | room height                                                                                                                                                                                                                                                                                                     |
| grid_rows                 | optional | number of rows in the room grid                                                                                                                                                                                                                                                                                 |
| grid_cols                 | optional | number of columns in the room grid                                                                                                                                                                                                                                                                              |
| raised_floor              | optional | yes/no                                                                                                                                                                                                                                                                                                          |
| raised_floor_height       | optional | height of raised floor                                                                                                                                                                                                                                                                                          |
| groups                    | optional |
| reverse_xaxis             | optional | "yes" reverses the numbering order on the x-axis                                                                                                                                                                                                                                                                |
| reverse_yaxis             | optional | "yes" reverses the numbering order on the y-axis                                                                                                                                                                                                                                                                |
| groups                    | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. If this parameter is present with no value, all groups are deleted. |

<aside>room name and building name combination can be used to update an existing room</aside>
## Update a Room

```shell
curl -X PUT -d "name=3nd Floor" -d "id=7" -d "building=main office"  -d "notes=super critical"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/rooms/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["Room updated successfully", 7, "3nd Floor"],
  "code": 0
}
```

Update information about an existing room.

**`PUT /api/1.0/rooms/`**

### URL Parameters

| Parameter                 | Use      | Description                                                                                                                                                                                                                                                                                                     |
| ------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id                        | required | id of the room                                                                                                                                                                                                                                                                                                  |
| name                      | optional | Name of the room                                                                                                                                                                                                                                                                                                |
| building_id or building   | optional | [Building ID](#get-all-buildings) Building name can be used if unique, and must already exists.                                                                                                                                                                                                                 |
| notes                     | optional |
| horizontal_grid_numbering | optional | numeric or alphabetic, numeric by default                                                                                                                                                                                                                                                                       |
| horizontal_grid_start     | optional |
| vertical_grid_numbering   | optional | numeric or alphabetic, numeric by default                                                                                                                                                                                                                                                                       |
| vertical_grid_start       | optional |
| uom                       | optional | unit of measurement                                                                                                                                                                                                                                                                                             |
| height                    | optional | room height                                                                                                                                                                                                                                                                                                     |
| grid_rows                 | optional | number of rows in the room grid                                                                                                                                                                                                                                                                                 |
| grid_cols                 | optional | number of columns in the room grid                                                                                                                                                                                                                                                                              |
| raised_floor              | optional | yes/no                                                                                                                                                                                                                                                                                                          |
| raised_floor_height       | optional | height of raised floor                                                                                                                                                                                                                                                                                          |
| groups                    | optional |
| reverse_xaxis             | optional | "yes" reverses the numbering order on the x-axis                                                                                                                                                                                                                                                                |
| reverse_yaxis             | optional | "yes" reverses the numbering order on the y-axis                                                                                                                                                                                                                                                                |
| groups                    | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. If this parameter is present with no value, all groups are deleted. |

## Delete Room

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/rooms/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the room with the room id supplied as the required argument.

**`DELETE /api/1.0/rooms/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | room id     |

## Custom Fields

````shell
curl -X PUT -d "id=7&key=roomID&value=42" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/custom_fields/room/ --insecure
``

> The above command returns JSON structured like this:

```json
{
  "msg": ["custom key pair values added or updated", 7, "3nd Floor @ main office"],
  "code": 0
}
````

Create/update custom fields for rooms.

**`PUT /api/1.0/custom_fields/room/`**

**See Notices Below**

### URL Parameters

| Parameter          | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| id                 | required                        | [Room ID](#rooms) or UI > Tools > Export > Room                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| key                | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type               | optional                        | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value              | optional                        |
| notes              | optional                        |
| clear_value        | optional                        | yes to clear existing value for that field (added in v6.1.0)                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| bulk_fields        | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2 (added in v6.4.1)                                                                                                                                                                                                                                                                                                                                                                           |

<aside class="notice">
value2 has been deprecated since v6.1.0
</aside>

## Add device to room

```shell
curl -X POST -d "room_id=7" -d "name=main office" -d "type=CRAC" -d "wall=middle" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device/room/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["device added to room", 1, "main office - 3nd Floor"],
  "code": 0
}
```

Add a device to a room.

**`POST /api/1.0/device/room/`**

### URL Parameters

| Parameter | Use      | Description                                                                                                                              |
| --------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| room_id   | required | [Room ID](#rooms) or UI > Tools > Export > Room                                                                                          |
| name      | required | The name of the device.                                                                                                                  |
| type      | required | Must be either ‘CRAC’ or ‘UPS’.                                                                                                          |
| wall      | optional | Can be one of ‘left’, ‘right’, ‘top’, ‘bottom’, ‘middle’. Choose ‘middle’ if you do not want the object placed along one of the 4 walls. |

<aside class="notice">
Note: for server room only, not for storage rooms.
</aside>

## Delete Device from its Room

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device/room/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the device from its room with the device id supplied as the required argument.

**`DELETE /api/1.0/device/room/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | device id   |

## Add asset to room

```shell
curl -X POST -d "room_id=7&asset=bp122&type=Breaker Panel&wall=left" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/assets/room/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["Asset added to room", 1, "bp122  (in 3nd Floor)"],
  "code": 0
}
```

Add an asset to a room.

**`POST /api/1.0/assets/room/`**

### URL Parameters

| Parameter | Required | Description                                                                                                                              |
| --------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| room_id   | required | [Room ID](#rooms) or UI > Tools > Export > Room                                                                                          |
| asset     | required | The name of the asset.                                                                                                                   |
| type      | required | Must be ‘Breaker Panel’, ‘AC’ or ‘DMARC’.                                                                                                |
| wall      | optional | Can be one of ‘left’, ‘right’, ‘top’, ‘bottom’, ‘middle’. Choose ‘middle’ if you do not want the object placed along one of the 4 walls. |

## Delete an Asset from its Room

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/asset/room/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the asset with the asset id supplied as the required argument from its room.

**`DELETE /api/1.0/asset/room/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | device id   |

# Racks

## Get All Racks

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/racks/ --insecure
```

> The above command returns results like this:

```json
{
  "limit": 2,
  "offset": 0,
  "racks": [
    {
      "asset_no": "",
      "available_u": 45.0,
      "building": "Building",
      "col_size": 1.0,
      "custom_fields": [],
      "first_number": 1,
      "groups": "",
      "manufacturer": "APC Inc.",
      "name": "RA1",
      "notes": "",
      "number_between_us": true,
      "numbering_start_from_bottom": "yes",
      "rack_id": 255,
      "rack_middle_option": "End at",
      "rack_url": "/api/1.0/racks/255/",
      "room": "Main room",
      "row": "",
      "row_size": 1.0,
      "size": 45,
      "start_col": 4.0,
      "start_row": 2.0,
      "tags": []
    },
    {
      "asset_no": "",
      "available_u": 42.0,
      "building": "Building",
      "col_size": 1.0,
      "custom_fields": [],
      "first_number": 1,
      "groups": "",
      "manufacturer": "APC Inc.",
      "name": "RA2",
      "notes": "",
      "number_between_us": true,
      "numbering_start_from_bottom": "yes",
      "rack_id": 256,
      "rack_middle_option": "End at",
      "rack_url": "/api/1.0/racks/256/",
      "room": "Main room",
      "row": "",
      "row_size": 1.0,
      "size": 42,
      "start_col": 29.0,
      "start_row": 10.0,
      "tags": []
    }
  ],
  "total_count": 275
}
```

This API will retrieve basic information about all racks.

**`GET /api/1.0/racks/`**

## Get a Specific Rack

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/racks/<id>/ --insecure
```

> The above command returns results like this:

```json
{
  "asset_no": "",
  "assets": [],
  "available_u": 10.0,
  "building": "New Haven",
  "custom_fields": [
    {
      "key": "Master Key?",
      "notes": "",
      "value": ""
    }
  ],
  "devices": [
    {
      "depth": "Full Depth",
      "device": {
        "asset_no": "",
        "device_id": 215,
        "device_url": "/api/1.0/devices/id/215/",
        "is_it_blade_host": "no",
        "is_it_switch": "no",
        "is_it_virtual_host": "no",
        "name": "USNHCS-DCM06",
        "serial_no": "",
        "type": "physical",
        "uuid": ""
      },
      "file_names": [
        "/var/www/graphics/images/HP--ProLiant-DL380p-Gen8.png",
        "/var/www/graphics/images/HP--ProLiant-DL380p-Gen8-B.png"
      ],
      "orientation": "Front",
      "reversed": "no",
      "size": 2.0,
      "start_at": 1.0,
      "where": "Rack Mounted",
      "width": 2520,
      "xpos": 0
    },
    {
      "depth": "Full Depth",
      "device": {
        "asset_no": "",
        "device_id": 217,
        "device_url": "/api/1.0/devices/id/217/",
        "is_it_blade_host": "no",
        "is_it_switch": "no",
        "is_it_virtual_host": "no",
        "name": "USNHCS-DCM10",
        "serial_no": "",
        "type": "physical",
        "uuid": ""
      },
      "file_names": [
        "/var/www/graphics/images/HP--ProLiant-DL380p-Gen8.png",
        "/var/www/graphics/images/HP--ProLiant-DL380p-Gen8-B.png"
      ],
      "orientation": "Back",
      "reversed": "no",
      "size": 2.0,
      "start_at": 5.0,
      "where": "Rack Mounted",
      "width": 2520,
      "xpos": 0
    },
    {
      "depth": "Full Depth",
      "device": {
        "asset_no": "0051",
        "device_id": 38,
        "device_url": "/api/1.0/devices/id/38/",
        "is_it_blade_host": "yes",
        "is_it_switch": "no",
        "is_it_virtual_host": "no",
        "name": "USNHCC-1-1478",
        "serial_no": "1478",
        "type": "physical",
        "uuid": ""
      },
      "file_names": ["", ""],
      "orientation": "Front",
      "reversed": "no",
      "size": 6.0,
      "start_at": 9.0,
      "where": "Rack Mounted",
      "width": 2520,
      "xpos": 0
    }
  ],
  "first_number": 0,
  "manufacturer": "APC Inc.",
  "name": "NH-DC1-02",
  "notes": "",
  "number_between_us": true,
  "numbering_start_from_bottom": "yes",
  "pdus": [
    {
      "bootstrap_power": "0.00 W",
      "depth": "Half Depth",
      "file_names": ["/var/www/graphics/images/apc-rack-pdu-front.png", ""],
      "name": "USNHCTPDU-03",
      "num_ports": "32",
      "orientation": "Back",
      "pdu_id": 16,
      "power_draw": " ",
      "reversed": "no",
      "size": 1.0,
      "start_at": null,
      "total_ports": 32,
      "where": "Right",
      "width": 2520,
      "xpos": 0
    }
  ],
  "rack_id": 21,
  "rack_middle_option": "Start at",
  "rack_url": "/api/1.0/racks/21/",
  "room": "NHDC1",
  "row": "",
  "row_size": 1.0,
  "size": 42,
  "start_col": 29.0,
  "start_row": 10.0,
  "tags": []
}
```

Retrieve detailed information about a specific rack including all racked devices, assets and PDUs.

**`GET /api/1.0/racks/<ID>/`**

### URL Parameters

| Parameter | Description                    |
| --------- | ------------------------------ |
| ID        | The ID of the rack to retrieve |

## Create / Update Racks

```shell
curl -X POST -d "name=34&size=42&room_id=7&numbering_start_from_bottom=no&first_number=4&row=12&manufacturer=apc&notes=deos it have anything?" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/racks/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["rack added.", 29, "34"], "code": 0 }
```

Create a rack.

**`POST /api/1.0/racks/`**

**See notice below**

### URL Parameters

| Parameter                   | Use      | Description                                                                                                                                                                                                                                                                                                     |
| --------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                        | required | Rack name - must be unique within a room or updating requires rack_id vs. rack name.                                                                                                                                                                                                                            |
| size                        | required |
| rack_id                     | optional | To change a rack using ID (This is higher priority to change a rack since v5.7.4)                                                                                                                                                                                                                               |
| room                        | optional | Required if changing a rack without rack_id. Name of room. See notice below.                                                                                                                                                                                                                                    |
| building                    | optional | Building name. See notice below.                                                                                                                                                                                                                                                                                |
| new_name                    | optional | Use to rename a rack.                                                                                                                                                                                                                                                                                           |
| room_id                     | optional | [Room ID](#rooms) or UI > Tools > Export > Room See notice below.                                                                                                                                                                                                                                               |
| numbering_start_from_bottom | optional | default is yes, no to change, otherwise ignored.                                                                                                                                                                                                                                                                |
| first_number                | optional | default 0, add to change.                                                                                                                                                                                                                                                                                       |
| row                         | optional | this row field is for the name of the rows, and not related to the grid positioning of the rack                                                                                                                                                                                                                 |
| manufacturer                | optional | name of the hardware manufacturer.                                                                                                                                                                                                                                                                              |
| notes                       | optional |
| start_row                   | optional | Starting row for rack, for grid positioning                                                                                                                                                                                                                                                                     |
| start_col                   | optional | Starting column for the rack, for grid positioning                                                                                                                                                                                                                                                              |
| row_size                    | optional | How many rows long the rack is                                                                                                                                                                                                                                                                                  |
| col_size                    | optional | how many racks wide the rack is                                                                                                                                                                                                                                                                                 |
| orientation                 | optional | orientation of the rack in room layout view. Possible values: right, left, up or down.                                                                                                                                                                                                                          |
| groups                      | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. If this parameter is present with no value, all groups are deleted. |

<aside class="notice">
If room name is not unique, use building and room name.  If building and room name together are not unique, rack_id must be used - otherwise first combination of room name or room and building name will be used.
</aside>

<aside class="notice">
Racks must have a unique name within the room, or within an individual row. For instance, you can have "Rack 1" in Row A as well as "Rack 1" in Row B, but you cannot have two "Rack 1"'s within a single row, or within a room if you are not using row values.
</aside>

## Delete Rack

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/racks/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the rack with the rack id supplied as the required argument.

**`DELETE /api/1.0/racks/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | rack id     |

## Custom Fields

```shell
curl -X PUT -d "id=29&key=rackID&value=42" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/custom_fields/rack/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "custom key pair values added or updated",
    29,
    "34 - 12 (in 3nd Floor @ main office)"
  ],
  "code": 0
}
```

Create/update custom fields for racks.

**`PUT /api/1.0/custom_fields/rack/`**

**See Notices Below**

### URL Parameters

| Parameter          | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| id                 | required                        | [Rack ID](#get-all-racks) or UI > Tools > Export > Rack                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| key                | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type               | optional                        | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value              | optional                        |
| notes              | optional                        |
| clear_value        | optional                        | yes to clear existing value for that field (added in v6.1.0)                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| bulk_fields        | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2 (added in v6.4.1)                                                                                                                                                                                                                                                                                                                                                                           |

<aside class="notice">
value2 has been deprecated since v6.1.0
</aside>

<aside class="notice">
If the key exists it will be updated, otherwise it will be created.
</aside>

## [Add/Update Devices to Racks](#add-update-a-device-in-a-rack)

# Assets

## Get All Assets

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/assets/?limit=2 --insecure
```

> The above command returns results like this:

```json
{
  "assets": [
    {
      "asset_contracts": [],
      "asset_id": 16,
      "asset_no": "0075",
      "asset_purchases": [],
      "building": "New Haven",
      "custom_fields": "",
      "depth": "Half Depth",
      "imagefile_id": 2,
      "name": "DC1R5PP1",
      "notes": "",
      "orientation": "Front",
      "patch_panel_model_id": 5,
      "rack": "NH-DC1-05",
      "rack_id": 24,
      "room": "NHDC1",
      "row": "2",
      "serial_no": "",
      "service_level": "In Service",
      "size": 2.0,
      "start_at": 40.0,
      "type": "Patch Panel",
      "vendor": "Black Box Corp.",
      "vendor_id": 36,
      "where": "Rack Mounted"
    },
    {
      "asset_contracts": [],
      "asset_id": 17,
      "asset_no": "0071",
      "asset_purchases": [],
      "building": "New Haven",
      "custom_fields": "",
      "depth": "Half Depth",
      "imagefile_id": 2,
      "name": "DC1R6PP1",
      "notes": "",
      "orientation": "Front",
      "patch_panel_model_id": 3,
      "rack": "NH-DC1-06",
      "rack_id": 25,
      "room": "NHDC1",
      "row": "2",
      "serial_no": "",
      "service_level": "In Service",
      "size": 2.0,
      "start_at": 40.0,
      "type": "Patch Panel",
      "vendor": "Black Box Corp.",
      "vendor_id": 36,
      "where": "Rack Mounted"
    }
  ],
  "limit": 2,
  "offset": 0,
  "total_count": 209
}
```

Retrieve basic information about all assets.

**`GET /api/1.0/assets/`**

### Query string parameters

| Parameter         | Description                                                                                            |
| ----------------- | ------------------------------------------------------------------------------------------------------ |
| asset_no          | Asset # or asset tag                                                                                   |
| serial_no         | Serial #                                                                                               |
| last_updated_lt   | last updated less than date YYYY-MM-DD format                                                          |
| last_updated_gt   | last updated greater than date YYYY-MM-DD format                                                       |
| first_added_lt    | first added less than date YYYY-MM-DD format                                                           |
| first_added_gt    | first added greater date YYYY-MM-DD format                                                             |
| asset_id          | ID of the assets (Added in v9.2.0)                                                                     |
| service_level     | service level of the asset (Added in v9.2.0)                                                           |
| customer          | customer name, use blank to get assets associated with no customers (Added in v9.2.0)                  |
| type              | Asset type                                                                                             |
| tags              | comma separated tags, OR filter (Added in v9.2.0)                                                      |
| tags_and          | comma separated tags, AND filter (Added in v9.2.0)                                                     |
| asset_no_contains | search for any asset that contains matching asset # (Added in v9.2.0)                                  |
| custom_fields_and | comma separated key value pairs in format of "key1:value1, key2:value2". AND filter. (Added in v9.2.0) |
| custom_fields_or  | comma separated key value pairs in format of "key1:value1, key2:value2". OR filter. (Added in v9.2.0)  |
| related_device_id | ID of the related device (added in v9.3.0)                                                             |

## Get a Specific Asset

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/assets/<id>/ --insecure
```

> The above command returns results like this:

```json
{
  "asset_contracts": [],
  "asset_id": 17,
  "asset_no": "0071",
  "asset_purchases": [],
  "building": "New Haven",
  "custom_fields": "",
  "depth": "Half Depth",
  "imagefile_id": 2,
  "name": "DC1R6PP1",
  "notes": "",
  "orientation": "Front",
  "patch_panel_model_id": 3,
  "rack": "NH-DC1-06",
  "rack_id": 25,
  "room": "NHDC1",
  "row": "2",
  "serial_no": "",
  "service_level": "In Service",
  "size": 2.0,
  "start_at": 40.0,
  "type": "Patch Panel",
  "vendor": "Black Box Corp.",
  "vendor_id": 36,
  "where": "Rack Mounted"
}
```

Retrieve detailed information about a specific asset.

**`GET /api/1.0/assets/<asset-id#>/`**

### Parameters

| Parameter | Description                     |
| --------- | ------------------------------- |
| asset-id# | The ID of the asset to retrieve |

## Create Assets

```shell
curl -X PUT  -d "asset_id=1&name=1&type=patch panel&vendor=dell&imagefile_id=1&service_level=In Production&rack_id=1&start_at=2&size=8&customer=abc inc&serial_no=1234&asset_no=d42"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/assets/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg": ["asset added/edited. Asset Rack Info successfully added/edited. "42"test"],"code":0}
```

Create assets.

**`POST /api/1.0/assets/`**

### Parameters

| Parameter                   | Use      | Description                                                                                                                                                                                                                                                                                                      |
| --------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type                        | required | You can choose an existing one or add a new type here. see below for other parameters                                                                                                                                                                                                                            |
| name                        | optional |
| service_level               | optional |
| serial_no                   | optional |
| asset_no                    | optional |
| customer                    | optional | name of the customer. Must exist already.                                                                                                                                                                                                                                                                        |
| location                    | optional |
| notes                       | optional |
| building                    | optional |
| vendor                      | optional | name of the vendor. Must exist already.                                                                                                                                                                                                                                                                          |
| imgfile_id                  | optional | image file id. You can see these from Tools > Import > Hardware Import for now.                                                                                                                                                                                                                                  |
| imgfile                     | optional | name of the image file (Added in v5.8.2). Use instead of imgfile_id                                                                                                                                                                                                                                              |
| back_image_id               | optional | back image file id. You can see these from Tools > Import > Hardware Import for now.                                                                                                                                                                                                                             |
| back_image                  | optional | name of the back image file. Use instead of back_image_id.                                                                                                                                                                                                                                                       |
| rack_id                     | optional | Required if building name, room name or rack name are not provided. [Rack ID](#get-all-racks) or UI > Tools > Export > Rack                                                                                                                                                                                      |
| building, room, rack or row | optional | building is building name, room is room name, rack is rack name and row is optional. This is used if rack_id is not provided and a unique rack is found with that combination. This could be just rack for rack name, if the rack name is unique. Otherwise add row, room or building to identify a unique rack. |
| start_at                    | optional | Required if adding to rack. U Start location.                                                                                                                                                                                                                                                                    |
| size                        | optional | Required if adding to rack. in U                                                                                                                                                                                                                                                                                 |
| orientation                 | optional | back if back facing. Otherwise ignored. Optional.                                                                                                                                                                                                                                                                |
| where                       | optional | location in rack, one of 'top', 'bottom', 'left', 'right', 'mounted'. Note: If mounted a size must be provided or available from the hardware model.                                                                                                                                                             |
| x_pos                       | optional | A number between 0 and 2520 representing the position within the u slot in increments of 252, which is equal to 1/10th of the width of the rack. 0 will place a device flush left, 1260 will place the left side of a device in center.                                                                          |
| depth                       | optional | Half depth by default. full to override. Optional.                                                                                                                                                                                                                                                               |
| device_id                   | optional | ID of the related device                                                                                                                                                                                                                                                                                         |
| type                        | optional | Filter by asset type                                                                                                                                                                                                                                                                                             |
| tags                        | optional | add tags (comma separate) (added in v9.3.0)                                                                                                                                                                                                                                                                      |
| tags_remove                 | optional | remove tags (comma separate) (added in v9.3.0)                                                                                                                                                                                                                                                                   |
| category                    | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission.                                                                      |

### Optional arguments for asset type “Patch Panel”:

| Parameter            | Use      | Description                                                                                  |
| -------------------- | -------- | -------------------------------------------------------------------------------------------- |
| patch_panel_model_id | optional | [Patch Panel Model ID](#get-all-patch-panel-models) or UI Tools > Export > Patch Panel Model |
| patch_panel_model    | optional | Name of the patch panel model (use instead of ID, Added in v5.8.2)                           |
| numbering_start_from | optional | This is starting # for patch panel ports. Defaults to 1 if not entered.                      |

### Optional arguments for asset type “Patch Panel Module”:

| Parameter                   | Use                                                                                       | Description                                                                                               |
| --------------------------- | ----------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| patch_panel_module_model_id | available via UI Tools > Export > Patch Panel Module Model                                |
| patch_panel_module_model    | optional                                                                                  | Name of the patch panel module model (use instead of ID, Added in v5.8.2)                                 |
| module_host_id              | Host for the patch panel model. [Asset ID](#get-all-assets) or UI Tools > Export > Asset. |
| module_host                 | optional                                                                                  | Name of the Module host. Must be unique asset name for this to work. (use instead of ID, Added in v5.8.2) |
| slot_no                     | If adding a new one, required for this type.                                              |
| numbering_start_from        | This is starting # for patch panel ports. Defaults to 1 if not entered.                   |

## Modify Assets

```shell
curl -X PUT  -d "asset_id=1&name=1&type=patch panel&vendor=dell&imagefile_id=1&service_level=In Production&rack_id=1&start_at=2&size=8&customer=abc inc&serial_no=1234&asset_no=d42"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/assets/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg":["asset added/edited. Asset Rack Info successfully added/edited."42"test"],"code": 0}
```

Modify assets.

**`PUT /api/1.0/assets/`**

### Parameters

| Parameter | Use                                | Description                                                               |
| --------- | ---------------------------------- | ------------------------------------------------------------------------- |
| asset_id  | required, if asset not provided    | [Asset ID](#get-all-assets) or UI Tools > Export > Asset.                 |
| asset     | required, if asset_id not provided | Name of the Asset. Must be a unique asset with that name. Added in v5.8.2 |
| type      | optional                           |

All other are same as [/api/1.0/assets POST call](#create-assets) as described above

## Delete Asset

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/assets/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the asset with the asset id supplied as the required argument.

**`DELETE /api/1.0/assets/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | asset id    |

## Custom Fields

```shell
curl -X PUT -d "value=er42&key=GSMID&id=12" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/custom_fields/asset/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "custom key pair values added or updated",
    29,
    "34 - 12 (in 3nd Floor @ main office)"
  ],
  "code": 0
}
```

Create/update custom fields for assets.

**`PUT /api/1.0/custom_fields/asset/`**

**See Notices Below**

### URL Parameters

| Parameter          | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| id or name         | required                        | Preferably use ID of the asset. If name is unique, you can use name as well.                                                                                                                                                                                                                                                                                                                                                                                                                     |
| key                | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type               | optional                        | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value              | optional                        |
| notes              | optional                        |
| clear_value        | optional                        | yes to clear existing value for that field (added in v6.1.0)                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| bulk_fields        | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2 (added in v6.4.1)                                                                                                                                                                                                                                                                                                                                                                           |

<aside class="notice">
value2 has been deprecated since v6.1.0
</aside>

# Devices

## Get All Devices With Brief Output

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/devices/ --insecure
```

> The above command returns results like this:

```json
{
  "Devices": [
    {
      "asset_no": null,
      "device_id": 34,
      "device_url": "/api/1.0/devices/id/34/",
      "name": "320",
      "serial_no": null,
      "uuid": "07FCE572-B2B3-B44C-BB1C-6799B509CC31"
    },
    {
      "asset_no": null,
      "device_id": 36,
      "device_url": "/api/1.0/devices/id/36/",
      "name": "323p1",
      "serial_no": null,
      "uuid": "22D4DEBD-6EAA-D441-89AE-047A9A60E9FB"
    },
    {
      "asset_no": null,
      "device_id": 39,
      "device_url": "/api/1.0/devices/id/39/",
      "name": "d250",
      "serial_no": null,
      "uuid": "76CE2AFC-58E3-1B4E-AB7A-6FECB480154B"
    },
    {
      "asset_no": null,
      "device_id": 33,
      "device_url": "/api/1.0/devices/id/33/",
      "name": "d313p1",
      "serial_no": null,
      "uuid": "BC1E0971-9889-8946-A92B-8F1D830C1AF2"
    }
  ]
}
```

Retrieve basic information about all devices.

**`GET /api/1.0/devices/`**

### Query string parameters (Added in v5.7.6)

| Parameter          | Description                                                                                                                         |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| type               | filter by device type (physical, virtual, blade, other, cluster or unknown)                                                         |
| device_sub_type    | filter by device sub type (Added in v14.7.2)                                                                                        |
| device_sub_type_id | filter by device sub type id (Added in v14.7.2)                                                                                     |
| service_level      | filter by service level name                                                                                                        |
| in_service         | filter by whether in service or not. Use yes or no.                                                                                 |
| customer           | filter by customer name                                                                                                             |
| tags               | filter by tags. comma separated for multiple tags (This is an OR filter, gets all the devices for all comma separated tags)         |
| blade_host_name    | filter by blade host name                                                                                                           |
| virtual_host_name  | filter by virtual host name                                                                                                         |
| building_id        | filter by building ID (Added in v5.9.0)                                                                                             |
| building           | filter by building name. Only works if building ID is not present (Added in v5.9.0)                                                 |
| room_id            | filter by room ID (Added in v5.9.0)                                                                                                 |
| room               | filter by room name. Only works if room ID is not present (Added in v5.9.0)                                                         |
| rack_id            | filter by rack ID (Added in v5.9.0)                                                                                                 |
| rack               | filter by rack name. Only works if rack ID is not present (Added in v5.9.0)                                                         |
| serial_no          | filter by serial # (Added in v6.0.0)                                                                                                |
| serial_no_contains | filter by partial serial match (Added in 9.7.1)                                                                                     |
| object_category    | filter by object category                                                                                                           |
| object_category_id | filter by object category ID                                                                                                        |
| asset_no           | filter by asset # (Added in v6.0.0)                                                                                                 |
| name               | filter by name (Added in v6.0.0)                                                                                                    |
| tags_and           | filter by all the tags, separated by comma. (This is an AND filter and all tags have to match for filter, this was added in v6.3.1) |
| uuid               | filter by uuid (exact match) (Added in v6.3.2)                                                                                      |
| is_it_switch       | filter by whether switch or not. Use yes or no. (Added in v6.3.2)                                                                   |
| is_it_virtual_host | filter by whether virtual host or not. Use yes or no. (Added in v6.3.2)                                                             |
| is_it_blade_host   | filter by whether blade host or not. Use yes or no. (Added in v6.3.2)                                                               |
| hardware           | filter by name of hardware model, comma separated for multiple hardware models (or filter). (Added in v6.3.2)                       |
| hardware_ids       | filter by ID of hardware models, comma separated                                                                                    |
| os                 | filter by OS name (added in v8.3.0)                                                                                                 |
| virtual_subtype    | filter by virtual subtype (added in v8.3.2)                                                                                         |
| last_updated_lt    | last updated less than date YYYY-MM-DD format                                                                                       |
| last_updated_gt    | last updated greater than date YYYY-MM-DD format                                                                                    |
| first_added_lt     | first added less than date YYYY-MM-DD format                                                                                        |
| first_added_gt     | first added greater date YYYY-MM-DD format                                                                                          |
| custom_fields_and  | filter by custom fields, and filter, format of key1:value1,key2:value2                                                              |
| custom_fields_or   | filter by custom fields, or filter, format of key1:value1,key2:value2                                                               |

## Get All Devices With Detailed Output (added in v6.3.4)

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/devices/all/?limit=1 --insecure
```

> The above command returns results like this:

```json
{
	"total_count": 8305,
	"limit": 1,
{
    "aliases": [
        "NHCTWS001.fqdn"
    ],
    "asset_no": "0019",
    "building": "New Haven",
    "category": "Development Machines",
    "cpucore": 2,
    "cpucount": 1,
    "cpuspeed": 3300.0,
    "custom_fields": [
        {
            "key": "Skynet",
            "notes": "",
            "value": "T1000"
        },
        {
            "key": "Agent email address",
            "notes": "",
            "value": "mh@skynet.net"
        },
        {
            "key": "Rating",
            "notes": "",
            "value": "5"
        },
        {
            "key": "Project",
            "notes": "",
            "value": "ABQ - ABQ Test"
        }
    "customer": "Infrastructure Services",
    "customer_id": 9,
    "device_external_links": [
        {
            "link": "http://{{device.name}}",
            "notes": "Dev Test web server"
        },
        {
            "link": "https://{{device.name}}",
            "notes": "Dev Test SSL"
        }
    ],
    "device_id": 136,
    "device_purchase_line_items": [
        {
            "line_cancel_policy": "",
            "line_contract_type": "Warranty",
            "line_cost": 400.0,
            "line_cost_center": "5001245",
            "line_end_date": "2017-01-09",
            "line_frequency": "One Time",
            "line_item_type": "Device",
            "line_no": 1,
            "line_notes": "",
            "line_quantity": 1,
            "line_renew_date": "2017-01-09",
            "line_start_date": "2014-01-09",
            "line_type": "Contract",
            "purchase_id": 9,
            "purchase_order_no": "010914Warr"
        },
        {
            "line_cost": 1.0,
            "line_cost_center": "5001245",
            "line_customer": "R&D",
            "line_item_type": "Device",
            "line_no": 1,
            "line_notes": "",
            "line_quantity": 1,
            "line_type": "One time Purchase/Tax etc.",
            "purchase_id": 3,
            "purchase_order_no": "R&D8152013HW"
        }
    ],
    "hdd_details": [
        {
            "description": "",
            "hdd": {
                "bytes": "GB",
                "description": "SATA 6 Gb/s 64MB Cache",
                "hd_id": 6,
                "location": "IT Lab 2nd Floor New Haven, CT",
                "manufacturer_id": 6,
                "notes": "",
                "partno": "WD5000HHTZ",
                "rpm": {
                    "id": 2,
                    "name": "10k"
                },
                "size": 500.0,
                "type": {
                    "id": 1,
                    "name": "SATA"
                }
            },
            "hddcount": 1,
            "raid_group": "",
            "raid_type": "Raid 1"
        },
        {
            "description": "",
            "hdd": {
                "bytes": "GB",
                "description": "SATA 6 Gb/s 64MB Cache",
                "hd_id": 22,
                "location": "IT Lab 2nd Floor New Haven, CT",
                "manufacturer_id": 6,
                "notes": "",
                "partno": "WD5000HHTZ",
                "rpm": {
                    "id": 2,
                    "name": "10k"
                },
                "size": 500.0,
                "type": {
                    "id": 1,
                    "name": "SATA"
                }
            },
            "hddcount": 2,
            "raid_group": "",
            "raid_type": "Raid 1"
        }
    ],
    "groups": "Prod_East:no, Corp:yes",
    "hddcount": 2,
    "hddraid": null,
    "hddraid_type": null,
    "hddsize": "",
    "hw_depth": 1,
    "hw_model": "1021M-UR+B",
    "hw_size": 1.0,
    "id": 136,
    "in_service": true,
    "ip_addresses": [
        {
            "ip": "10.1.10.11",
            "label": "",
            "macaddress": "00:14:bf:aa:46:18",
            "subnet": "Infra-10.1.10.0/24(Infrastructure Services)",
            "subnet_id": 4,
            "type": 1
        }
    ],
    "last_updated": "2016-07-05T22:08:34.084Z",
    "mac_addresses": [
        {
            "mac": "00:11:22:33:44:55",
            "port": "FastEthernet0/4 @ nh-lab-switch-01",
            "port_name": "",
            "vlan": null
        }
    ],
    "manufacturer": "Super Micro Computer, Inc.",
    "name": "NHCTWS001",
    "notes": "",
    "orientation": 1,
    "os": "Microsoft Windows 7 Professional",
    "osarch": 64,
    "osver": "64bit",
    "osverno": "7.1",
    "rack": "NH-DC1-11",
    "rack_id": 39,
    "ram": 4096.0,
    "room": "NHDC1",
    "row": "2",
    "serial_no": "409357205",
    "service_level": "Production",
    "start_at": 24.0,
    "tags": [
        "abc",
        "sql"
    ],
    "type": "physical",
    "ucs_manager": null,
    "uuid": "",
    "virtual_host_name": null,
    "virtual_subtype": "Hyper-V",
    "where": 5,
    "xpos": 0
}
```

Retrieve basic information about all devices.

**`GET /api/1.0/devices/all/`**

### Query string parameters

| Parameter    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| include_cols | do not return all columns just the ones specified. For example, ?include_cols=name, device_id, rack will only result in name, device_id, and rack included in the output. The following column names can be part of include_cols: name, device_id, rack, name, device_id, serial_no, asset_no, uuid, notes, in_service, service_level, type, id, last_updated, tags, customer_id, customer, hw_model, hw_size, manufacturer, hw_depth, rack, start_at, rack_id, orientation, row, room, building, blade_host_name, blade_host_id, slot_number, virtual_host_name, location, device_sub_type, os, osarch, osver, osverno, custom_fields, device_purchase_line_items, device_external_links, ip_addresses, mac_addresses, cpucount, cpucore, cpuspeed, ram, hddcount, hddsize, hddraid, hddraid_type, hdd_details, pdu_mapping_url,modules, vms, devices, aliases, xpos, ucs_manager |
| limit        | return this number of devices                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| offset       | start with this device (e.g. limit=100&offset=50 means start with the 50th device and return the next 100 devices)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| blankasnull  | return null for any blank values                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |

In addition to these parameters, all the parameters from the Get All Devices With Brief Output call above can be used with this api call. Also, please be sure to use the limit and offset parameters with this call if you have more than 1000 devices. Otherwise, you risk overtaxing the system.

</aside>

## Get Device by Device Id

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/devices/id/<device-id#>/ --insecure
```

> The above command returns results like this:

```json
{
    "aliases": [
        "NHCTWS001.fqdn"
    ],
    "asset_no": "0019",
    "building": "New Haven",
    "category": "Production DBs",
    "cpucore": 2,
    "cpucount": 1,
    "cpuspeed": 3300.0,
    "custom_fields": [
        {
            "key": "Skynet",
            "notes": "",
            "value": "T1000"
        },
        {
            "key": "Agent email address",
            "notes": "",
            "value": "mh@skynet.net"
        },
        {
            "key": "Rating",
            "notes": "",
            "value": "5"
        },
        {
            "key": "Project",
            "notes": "",
            "value": "ABQ - ABQ Test"
        }
    "customer": "Infrastructure Services",
    "customer_id": 9,
    "device_external_links": [
        {
            "link": "http://{{device.name}}",
            "notes": "Dev Test web server"
        },
        {
            "link": "https://{{device.name}}",
            "notes": "Dev Test SSL"
        }
    ],
    "device_id": 136,
    "device_purchase_line_items": [
        {
            "line_cancel_policy": "",
            "line_contract_type": "Warranty",
            "line_cost": 400.0,
            "line_cost_center": "5001245",
            "line_end_date": "2017-01-09",
            "line_frequency": "One Time",
            "line_item_type": "Device",
            "line_no": 1,
            "line_notes": "",
            "line_quantity": 1,
            "line_renew_date": "2017-01-09",
            "line_start_date": "2014-01-09",
            "line_type": "Contract",
            "purchase_id": 9,
            "purchase_order_no": "010914Warr"
        },
        {
            "line_cost": 1.0,
            "line_cost_center": "5001245",
            "line_customer": "R&D",
            "line_item_type": "Device",
            "line_no": 1,
            "line_notes": "",
            "line_quantity": 1,
            "line_type": "One time Purchase/Tax etc.",
            "purchase_id": 3,
            "purchase_order_no": "R&D8152013HW"
        }
    ],
    "hdd_details": [
        {
            "description": "",
            "hdd": {
                "bytes": "GB",
                "description": "SATA 6 Gb/s 64MB Cache",
                "hd_id": 6,
                "location": "IT Lab 2nd Floor New Haven, CT",
                "manufacturer_id": 6,
                "notes": "",
                "partno": "WD5000HHTZ",
                "rpm": {
                    "id": 2,
                    "name": "10k"
                },
                "size": 500.0,
                "type": {
                    "id": 1,
                    "name": "SATA"
                }
            },
            "hddcount": 1,
            "raid_group": "",
            "raid_type": "Raid 1"
        },
        {
            "description": "",
            "hdd": {
                "bytes": "GB",
                "description": "SATA 6 Gb/s 64MB Cache",
                "hd_id": 22,
                "location": "IT Lab 2nd Floor New Haven, CT",
                "manufacturer_id": 6,
                "notes": "",
                "partno": "WD5000HHTZ",
                "rpm": {
                    "id": 2,
                    "name": "10k"
                },
                "size": 500.0,
                "type": {
                    "id": 1,
                    "name": "SATA"
                }
            },
            "hddcount": 2,
            "raid_group": "",
            "raid_type": "Raid 1"
        }
    ],
    "hddcount": 2,
    "hddraid": null,
    "hddraid_type": null,
    "hddsize": "",
    "hw_depth": 1,
    "hw_model": "1021M-UR+B",
    "hw_size": 1.0,
    "id": 136,
    "in_service": true,
    "ip_addresses": [
        {
            "ip": "10.1.10.11",
            "label": "",
            "macaddress": "00:14:bf:aa:46:18",
            "subnet": "Infra-10.1.10.0/24(Infrastructure Services)",
            "subnet_id": 4,
            "type": 1
        }
    ],
    "last_updated": "2016-07-05T22:08:34.084Z",
    "mac_addresses": [
        {
            "mac": "00:11:22:33:44:55",
            "port": "FastEthernet0/4 @ nh-lab-switch-01",
            "port_name": "",
            "vlan": null
        }
    ],
    "manufacturer": "Super Micro Computer, Inc.",
    "name": "NHCTWS001",
    "notes": "",
    "orientation": 1,
    "os": "Microsoft Windows 7 Professional",
    "osarch": 64,
    "osver": "64bit",
    "osverno": "7.1",
    "rack": "NH-DC1-11",
    "rack_id": 39,
    "ram": 4096.0,
    "room": "NHDC1",
    "row": "2",
    "serial_no": "409357205",
    "service_level": "Production",
    "start_at": 24.0,
    "tags": [
        "abc",
        "sql"
    ],
    "type": "physical",
    "ucs_manager": null,
    "uuid": "",
    "virtual_host_name": null,
    "where": 5,
    "xpos": 0
}
```

Retrieve detailed information about a specific device using device id.

**`GET /api/1.0/devices/id/<device-id#>/`**

### URL Parameters

| Parameter  | Description                      |
| ---------- | -------------------------------- |
| device-id# | The ID of the device to retrieve |

### Query string parameters

| Parameter | Description                                                                                                                           |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| follow    | use yes if you want to see virtuals in a virtual host, modules in a blade chassis and devices in a clustered device (Added in v5.7.4) |

## Get Devices by Customer Id

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/devices/customer_id/<customer-id#>/ --insecure
```

> The above command returns results like this:

```json
{
  "Devices": [
    {
      "aliases": ["device.domain"],
      "asset_no": null,
      "category": "Development Machine",
      "cpucore": 3,
      "cpucount": 1,
      "cpuspeed": "",
      "custom_fields": [],
      "customer": "Finance Group",
      "customer_id": 3,
      "device_external_links": [],
      "device_purchase_line_items": [],
      "hdd_details": null,
      "hddcount": null,
      "hddraid": null,
      "hddraid_type": null,
      "hddsize": null,
      "hw_depth": 1,
      "hw_model": "ProLiant DL360 G7",
      "hw_size": 1.0,
      "id": 30,
      "in_service": false,
      "ip_addresses": [],
      "last_updated": "2014-01-07T02:23:36.350Z",
      "mac_addresses": [
        {
          "mac": "00:15:5d:0b:72:0b"
        }
      ],
      "manufacturer": null,
      "name": "d42-231",
      "notes": null,
      "os": null,
      "ram": 512.0,
      "serial_no": null,
      "service_level": "QA",
      "type": "virtual",
      "ucs_manager": null,
      "uuid": "6BB7DC86-D744-8943-B991-B6BF82B55F99",
      "virtual_host_name": "HYPER01"
    },
    {
      "asset_no": null,
      "category": null,
      "cpucore": 3,
      "cpucount": 1,
      "cpuspeed": "",
      "custom_fields": [],
      "customer": "Finance Group",
      "customer_id": 3,
      "device_external_links": [],
      "device_purchase_line_items": [],
      "hddcount": "",
      "hddsize": "",
      "hw_model": null,
      "hw_size": null,
      "id": 37,
      "in_service": false,
      "ip_addresses": [],
      "last_updated": "2014-01-07T02:23:30.141Z",
      "mac_addresses": [
        {
          "mac": "00:15:5d:0b:72:0d"
        }
      ],
      "manufacturer": null,
      "name": "D42_250_IMPORETD",
      "notes": null,
      "os": null,
      "ram": 512.0,
      "serial_no": null,
      "service_level": "QA",
      "type": "virtual",
      "uuid": "D3260495-90E8-D940-8D21-D2108DC29E86",
      "virtual_host_name": "HYPER01"
    }
  ]
}
```

Retrieve all devices associated with a specific customer.

**`GET /api/1.0/devices/customer_id/<customer-id#>/`**

### URL Parameters

| Parameter    | Description               |
| ------------ | ------------------------- |
| include_cols | see /api/1.0/devices/all/ |
| customer-id# | The ID of the customer    |

## Get Device by Device Name

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/devices/name/<device-name#>/ --insecure
```

> The above command returns results like this:

```json
{
  "aliases": ["D42-esxiII.device42.pvt"],
  "asset_no": "",
  "building": "West Haven Office",
  "category": Hypervisors,
  "cpucore": 8,
  "cpucount": 2,
  "cpuspeed": 2270.0,
  "custom_fields": [],
  "customer": "autodiscovery",
  "customer_id": 1,
  "device_external_links": [],
  "device_id": 534,
  "device_purchase_line_items": [],
  "hdd_details": null,
  "hddcount": "",
  "hddraid": null,
  "hddraid_type": null,
  "hddsize": "",
  "hw_depth": 2,
  "hw_model": "ProLiant DL360 G7",
  "hw_size": 1.0,
  "id": 534,
  "in_service": true,
  "ip_addresses": [
    {
      "ip": "192.168.11.70",
      "label": "vmk0",
      "macaddress": "78:e3:b5:0d:e1:94",
      "subnet": "West Haven Network-192.168.11.0/24",
      "subnet_id": 1,
      "type": 1
    },
    {
      "ip": "192.168.11.71",
      "label": "mgmt",
      "subnet": "West Haven Network-192.168.11.0/24",
      "subnet_id": 1,
      "type": 1
    },
    {
      "ip": "fe80::7ae3:b5ff:fe0d:e194",
      "label": "vmk0",
      "macaddress": "78:e3:b5:0d:e1:94",
      "subnet": "undefined-::/0",
      "subnet_id": 3,
      "type": null
    }
  ],
  "last_updated": "2016-09-21T03:59:07.621Z",
  "mac_addresses": [
    {
      "mac": "78:e3:b5:0d:e1:94",
      "port": "GigabitEthernet4/0/13 @ wh-lab-sw-01",
      "port_name": "vmnic0, vmk0",
      "vlan": "VLAN0020"
    },
    {
      "mac": "78:e3:b5:0d:e1:92",
      "port": null,
      "port_name": "vmnic3",
      "vlan": null
    },
    {
      "mac": "78:e3:b5:0d:e1:90",
      "port": null,
      "port_name": "vmnic2",
      "vlan": null
    },
    {
      "mac": "78:e3:b5:0d:e1:96",
      "port": null,
      "port_name": "vmnic1",
      "vlan": null
    }
  ],
  "manufacturer": "HP",
  "name": "D42-esxiII",
  "notes": "",
  "orientation": 1,
  "os": "VMware ESXi",
  "osver": "5.5.0",
  "osverno": "2403361",
  "rack": "Lab Rack",
  "rack_id": 1,
  "ram": 32768.0,
  "room": "Server Closet",
  "serial_no": "CZ3128LL62",
  "service_level": "Production",
  "start_at": 40.0,
  "tags": ["labrack"],
  "type": "physical",
  "ucs_manager": null,
  "uuid": "32393735-3733-5a43-3331-32384c4c3632",
  "virtual_host_name": null,
  "where": 5,
  "xpos": 0
}
```

Retrieve detailed information about a specific device by device name

**`GET /api/1.0/devices/name/<device-name>/`**

### URL Parameters

| Parameter    | Description                                                                                                                           |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| include_cols | see /api/1.0/devices/all/                                                                                                             |
| device-name  | The name of the device to retrieve                                                                                                    |
| follow       | use yes if you want to see virtuals in a virtual host, modules in a blade chassis and devices in a clustered device (Added in v5.7.4) |

## Get Device by Device Serial Number

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/devices/serial/<device-serial#>/ --insecure
```

> The above command returns results like this:

```json
{
  "aliases": ["D42-esxiII.device42.pvt"],
  "asset_no": "",
  "building": "West Haven Office",
  "cpucore": 8,
  "cpucount": 2,
  "cpuspeed": 2270.0,
  "custom_fields": [],
  "customer": "autodiscovery",
  "customer_id": 1,
  "device_external_links": [],
  "device_id": 534,
  "device_purchase_line_items": [],
  "hdd_details": null,
  "hddcount": "",
  "hddraid": null,
  "hddraid_type": null,
  "hddsize": "",
  "hw_depth": 2,
  "hw_model": "ProLiant DL360 G7",
  "hw_size": 1.0,
  "id": 534,
  "in_service": true,
  "ip_addresses": [
    {
      "ip": "192.168.11.70",
      "label": "vmk0",
      "macaddress": "78:e3:b5:0d:e1:94",
      "subnet": "West Haven Network-192.168.11.0/24",
      "subnet_id": 1,
      "type": 1
    },
    {
      "ip": "192.168.11.71",
      "label": "mgmt",
      "subnet": "West Haven Network-192.168.11.0/24",
      "subnet_id": 1,
      "type": 1
    },
    {
      "ip": "fe80::7ae3:b5ff:fe0d:e194",
      "label": "vmk0",
      "macaddress": "78:e3:b5:0d:e1:94",
      "subnet": "undefined-::/0",
      "subnet_id": 3,
      "type": null
    }
  ],
  "last_updated": "2016-09-21T03:59:07.621Z",
  "mac_addresses": [
    {
      "mac": "78:e3:b5:0d:e1:94",
      "port": "GigabitEthernet4/0/13 @ wh-lab-sw-01",
      "port_name": "vmnic0, vmk0",
      "vlan": "VLAN0020"
    },
    {
      "mac": "78:e3:b5:0d:e1:92",
      "port": null,
      "port_name": "vmnic3",
      "vlan": null
    },
    {
      "mac": "78:e3:b5:0d:e1:90",
      "port": null,
      "port_name": "vmnic2",
      "vlan": null
    },
    {
      "mac": "78:e3:b5:0d:e1:96",
      "port": null,
      "port_name": "vmnic1",
      "vlan": null
    }
  ],
  "manufacturer": "HP",
  "name": "D42-esxiII",
  "notes": "",
  "orientation": 1,
  "os": "VMware ESXi",
  "osver": "5.5.0",
  "osverno": "2403361",
  "rack": "Lab Rack",
  "rack_id": 1,
  "ram": 32768.0,
  "room": "Server Closet",
  "serial_no": "CZ3128LL62",
  "service_level": "Production",
  "start_at": 40.0,
  "tags": ["labrack"],
  "type": "physical",
  "ucs_manager": null,
  "uuid": "32393735-3733-5a43-3331-32384c4c3632",
  "virtual_host_name": null,
  "where": 5,
  "xpos": 0
}
```

Retrieve detailed information about a specific device by device serial number.

**`GET /api/1.0/devices/serial/<device-serial#>/`**

### URL Parameters

| Parameter      | Description                           |
| -------------- | ------------------------------------- |
| include_cols   | see /api/1.0/devices/all/             |
| device-serial# | The serial# of the device to retrieve |

## Get Device by Device Asset Number

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/devices/asset/<device-asset#>/ --insecure
```

> The above command returns results like this:

```json
{
  "aliases": [],
  "asset_no": "",
  "category": null,
  "cpucore": 1,
  "cpucount": 4,
  "cpuspeed": 1800.0,
  "custom_fields": [
    {
      "key": "Agent email address",
      "notes": "",
      "value": ""
    },
    {
      "key": "Move Group",
      "notes": "",
      "value": ""
    },
    {
      "key": "Project",
      "notes": "",
      "value": ""
    },
    {
      "key": "Rating",
      "notes": "",
      "value": ""
    },
    {
      "key": "Rating Date",
      "notes": "",
      "value": ""
    },
    {
      "key": "Skynet",
      "notes": "",
      "value": ""
    },
    {
      "key": "username",
      "notes": "",
      "value": ""
    }
  ],
  "customer": null,
  "device_external_links": [],
  "device_id": 879,
  "device_purchase_line_items": [],
  "hdd_details": null,
  "hddcount": "",
  "hddraid": null,
  "hddraid_type": null,
  "hddsize": "",
  "hw_depth": null,
  "hw_model": null,
  "hw_size": null,
  "id": 879,
  "in_service": true,
  "ip_addresses": [
    {
      "ip": "172.16.2.86",
      "label": "eth0",
      "macaddress": "0a:50:8f:c4:11:49",
      "subnet": "undefined-::/0",
      "subnet_id": 8,
      "type": null
    },
    {
      "ip": "fe80::850:8fff:fec4:1149",
      "label": "eth0",
      "macaddress": "0a:50:8f:c4:11:49",
      "subnet": "undefined-::/0",
      "subnet_id": 8,
      "type": null
    }
  ],
  "last_updated": "2016-07-05T22:08:34.246Z",
  "mac_addresses": [
    {
      "mac": "0a:50:8f:c4:11:49",
      "port": null,
      "port_name": "eth0",
      "vlan": null
    }
  ],
  "manufacturer": null,
  "name": "oracle-db-1",
  "notes": "Oracle, Tomcat, apache application dependencies.",
  "os": "Enterprise Linux Enterprise Linux Server release 5.4",
  "osver": "5.4",
  "osverno": "5.4",
  "ram": 16384.0,
  "serial_no": "",
  "service_level": "Production",
  "tags": ["demo"],
  "type": "virtual",
  "ucs_manager": null,
  "uuid": "",
  "virtual_host_name": "openstack-dev2",
  "virtual_subtype": "Internal VM"
}
```

Retrieve detailed information about a specific device by device asset number.

**`GET /api/1.0/devices/asset/<device-asset#>/`**

### Parameters

| Parameter     | Description                                 |
| ------------- | ------------------------------------------- |
| include_cols  | see /api/1.0/devices/all/                   |
| device-asset# | The device asset# of the device to retrieve |

## Get Device Impact List

```shell
curl -X GET -u 'apiuser:apipw' "https://device42/api/1.0/device/impactlist/879/" --insecure |python -m json.tool
```

> The above command returns JSON structured like this:

```json
{
    ...truncated
                            "children": [
                                {
                                    "children": [
                                        {
                                            "children": [
                                                {
                                                    "children": [
                                                        {
                                                            "children": [
                                                                {
                                                                    "duplicate": "yes",
                                                                    "name": "site2.com - webserver.dev"
                                                                },
                                                                {
                                                                    "duplicate": "yes",
                                                                    "name": "site1.com - webserver.dev"
                                                                }
                                                            ],
                                                            "name": "Dependant Apps"
                                                        }
                                                    ],
                                                    "duplicate": "yes",
                                                    "name": "Apache HTTP Server - Production Site"
                                                }
                                            ],
                                            "name": "Dependant Apps"
                                        }
                                    ],
                                    "duplicate": "yes",
                                    "name": "Apache Tomcat server"
                                }
                            ],
                            "name": "Dependant Apps"
                        }
                    ],
                    "duplicate": "yes",
                    "name": "Oracle Database Server 1"
                }
            ],
                    "name": "Application Components"
                },
                {
                    "children": [
                        {
                            "children": [
                                {
                                    "name": "Oracle Database Server 1"
        ...truncated
```

This API endpoint retrieves the impact list of a device by ID.

**`GET /api/1.0/device/impactlist/<device-id>/`** (added in 11.8.1)

### Parameters

| Parameter | Use      | Description |
| --------- | -------- | ----------- |
| device-id | required | Device ID.  |

## Create/Update Device by Name

```shell
curl -X POST -d "name=db-080-westport&type=cluster&in_service=no&virtual_host=yui&service_level=production&macaddress=aabbccedffff" -u 'admin:adm!nd42' https://yourdevice42address/api/device/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["device added or updated", 46, "db-080-westport", true, true],
  "code": 0
}
```

This API is used to create a new device with the name supplied as the required argument or update an existing device that has the name, serial # or uuid of the required argument. For information purposes, this is also the API that is used by the Device42 auto-discovery tool.

**`POST /api/1.0/devices/`** (Added in v5.7.4)

**`POST /api/1.0/device/`** (Added in v5.7.3)

**`POST /api/device/`**

### Parameters

| Parameter                 | Use      | Description                                                                                                                                                                                                                                                                            |
| ------------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                      | required | Device name. If device with name already exists - the existing device is updated. If an existing device based on serial # or uuid are found - name is ignored. In that case existing name is not changed and you can use new_name if you wish to change the name.                      |
| new_name                  | optional | As mentioned above, this can be used to change the name of an existing device                                                                                                                                                                                                          |
| serial_no                 | optional | the serial # of the device. If a device with matching serial # is found - that device is updated. Serial # with less than 3 characters in length are ignored. Also, certain generic serial #s like 123456789 or 'not specified' are ignored.                                           |
| uuid                      | optional | the uuid of the device. If a device with matching uuid is found - that device is updated.                                                                                                                                                                                              |
| asset_no                  | optional | the asset # of the device.                                                                                                                                                                                                                                                             |
| manufacturer              | optional | the hardware manufacturer for the device.                                                                                                                                                                                                                                              |
| hardware                  | optional | the name of the hardware model for the device. Use in conjunction with the manufacturer argument. However, an update will only occur if no hardware model is previously assigned to the device.                                                                                        |
| new_hardware              | optional | If you want to change the hardware model for a device. If the device is rack mounted, it will unmount the device before changing hardware model and attempt to mount it back to same rack location (Changed in v6.3.2)                                                                 |
| is_it_switch              | optional | ‘yes’ indicates if device is a network switch                                                                                                                                                                                                                                          |
| is_it_virtual_host        | optional | ‘yes’ indicates if device is a virtual host                                                                                                                                                                                                                                            |
| is_it_blade_host          | optional | ‘yes’ indicates if device is a blade host                                                                                                                                                                                                                                              |
| in_service                | optional | ‘yes’ indicates if device is in service, ‘no’ indicates not in service                                                                                                                                                                                                                 |
| type                      | optional | is the type for device. Valid values are ‘physical’, ‘virtual’, ‘blade’, ‘cluster’, or ‘other’.                                                                                                                                                                                        |
| service_level             | optional | the service level name for the device. The service level must be pre-defined in the device42 appliance before it can be referenced in an api call.                                                                                                                                     |
| virtual_host              | optional | Is the name for the host of the virtual machine. Two conditions must be met for this value to successfully update: <br> 1. The device must be a virtual machine. <br> 2. The virtual host must already exist in the device42 application and must be already marked as a virtual host. |
| blade_host                | optional | the name of the host for the blade machine. Two conditions must be met for this value to successfully update: <br> 1. The device must be a blade type. 2. The blade host must already exist and must already be marked as a blade host.                                                |
| slot_no                   | optional | slot # for blade device.                                                                                                                                                                                                                                                               |
| storage_room_id           | optional | ID of the room to assign device to storage room. Added in v5.5.0                                                                                                                                                                                                                       |
| storage_room              | optional | name of the room to assign device to, only used if the room name is unique. Added in v5.5.0                                                                                                                                                                                            |
| os                        | optional | the name of the operating system (os, osver, and osverno all required if updating any of the three)                                                                                                                                                                                    |
| osver                     | optional | the version of the operating system (os, osver, and osverno all required if updating any of the three)                                                                                                                                                                                 |
| osarch                    | optional | The architecture of the operating system (32 or 64)                                                                                                                                                                                                                                    |
| osverno                   | optional | the version # or build # of the operating system (os, osver, and osverno all required if updating any of the three)                                                                                                                                                                    |
| memory                    | optional | the total memory(RAM) in MB. Just numbers.                                                                                                                                                                                                                                             |
| cpucount                  | optional | total # of CPUs. (If updating, cpupower and cpucore required)                                                                                                                                                                                                                          |
| cpupower                  | optional | CPU speed in MHz, just numbers. (If updating, cpucount and cpucore required)                                                                                                                                                                                                           |
| cpucore                   | optional | # of cores/CPU. Integer. (If updating, cpupower and cpucount required)                                                                                                                                                                                                                 |
| hddcount                  | optional | total # of HDD (hddcount, hddsize, hddraid, and hddraid_type all required when updating any of the 4)                                                                                                                                                                                  |
| hddsize                   | optional | HDD Size in GB (hddcount, hddsize, hddraid, and hddraid_type all required when updating any of the 4)                                                                                                                                                                                  |
| hddraid                   | optional | Raid. Possible values: software or hardware. none to clear. (none added in v9.1.0) (hddcount, hddsize, hddraid, and hddraid_type all required when updating any of the 4)                                                                                                              |
| hddraid_type              | optional | Raid Type. Possible Values: raid 0, raid 1, raid 3, raid 5, raid 6, raid 10, raid 50, raidz, raidz2. none to clear (none added in v9.1.0) (hddcount, hddsize, hddraid, and hddraid_type all required when updating any of the 4)                                                       |
| macaddress                | optional | the mac address value. Use multiple POST/PUT to add multiple mac addresses.                                                                                                                                                                                                            |
| devices_in_cluster        | optional | comma separated device names for devices in cluster. Only valid for device type cluster.                                                                                                                                                                                               |
| appcomps                  | optional | comma separated application component names on this device.                                                                                                                                                                                                                            |
| customer                  | optional | name of the customer. Customer record must already exist.                                                                                                                                                                                                                              |
| contract_id               | optional | **ID for the contract. Available via GET /api/1.0/contracts/ – DEPRECATED in v550.**                                                                                                                                                                                                   |
| contract                  | optional | **Name of the contract. Used only if contract name is unique. – DEPRECATED in v550.**                                                                                                                                                                                                  |
| aliases                   | optional | optional. Comma separated aliases for the device. Must not exist, ignored otherwise.                                                                                                                                                                                                   |
| subtype                   | optional | Only for device type other. Must exist internally first.                                                                                                                                                                                                                               |
| virtual_subtype           | optional | Only for device type virtual. Default is internal VM. Otherwise, possible vales are: ec2, azure_vm, azure_sql, azure_storage, other, outscale, linode, digitalOcean, vmware, citrix, kvm, hyperv, docker, lxc, rackspace_vm, softlayer_vm, ovirt, joyent_vm, virtualbox                |
| notes                     | optional |
| tags                      | optional | comma separated tags (Added in v5.9.2)                                                                                                                                                                                                                                                 |
| virtual_host_clear        | optional | yes to clear virtual host for a VM (added in v9.1.0)                                                                                                                                                                                                                                   |
| tags_remove               | optional | comma separated tags to remove (added in v9.1.0)                                                                                                                                                                                                                                       |
| devices_in_cluster_remove | optional | comma separated device names for removing devices in cluster. Only valid for device type cluster. (added in v9.2.0)                                                                                                                                                                    |
| object_category           | optional | If multitenancy is on, a category can be assigned to control access to this object, e.g. Prod_East specifies that the users with access to the Prod_East category will be able to access this device.                                                                                  |
| new_object_category       | optional | Used to change category on a device if it is already set.                                                                                                                                                                                                                              |

## Create/Update Multi-Serial Device by Name

```shell
curl -X POST -d "name=server-42&type=physical&in_service=no&service_level=production&serial_no=OIUER431" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/multiserials/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["device added or updated", 7246, "server-42", true, true], "code": 0 }
```

This API is used to create a new device with the name supplied as the required argument or update an existing device that with that name. This is similar to /api/1.0/devices/ POST call - but you can add duplicate serial numberss and UUID for high density servers that share the same serial # and/or UUID.

**`POST /api/1.0/multiserial/`** (Added in v5.8.2)

### Parameters

Parameters are same as POST call for /api/1.0/devices/

## Create/Update Multi-Node Device by Name

```shell
curl -X POST -d "name=server-42&type=physical&in_service=no&service_level=production&serial_no=OIUER431" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/multinodes/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["device added or updated", 7246, "server-42", true, true], "code": 0 }
```

This API is used to create a new device with the name supplied as the required argument or update an existing device that with that name. This is similar to /api/1.0/devices/ POST call - but you can add duplicate serial #s and UUID for high density servers that share the same serial # and/or UUID.

**`POST /api/1.0/multinodes/`** (Added in v5.8.2)

### Parameters

Parameters are same as POST call for /api/1.0/devices/

## Create/Update Device Cloud Instance Information

```shell
curl -X POST -d "device_id=314&instance_id=Instancei-2ee44eeb&vendor=Amazon" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device/cloud_instance/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["cloudinstance added/updated", 17, "cloud-001", true, true],
  "code": 0
}
```

Update cloud instance information for devices.

**`POST /api/1.0/device/cloud_instance/`**

### Required Parameters

Require one of the following parameters

| Parameter | Description |
| --------- | ----------- |
| device    | Device name |
| device_id | Device ID   |

### Optional Parameters

The following additional parameters can be defined

| Parameter   | Description                            |
| ----------- | -------------------------------------- |
| instance_id | The cloud instance ID of the device    |
| vendor      | The cloud vendor                       |
| status      | Instance status (ie, running, stopped) |
| location    | Location/region of instance deployment |
| notes       | Any additional notes                   |

## Update Device by Name, ID, Serial or Asset

```shell
curl -X PUT -d "serial=FE89ERERW&in_service=no" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["device added or updated", 46, "db-080-westport", true, false],
  "code": 0
}
```

Update an existing devices by name, serial, ID or asset number.

**`PUT /api/1.0/device/`**

### Required Parameters

Require one of the following parameters

| Parameter | Description                                                                                                                                                                                                  |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| name      | Updates the device found with that name. Otherwise returns "device not found"                                                                                                                                |
| serial    | If the serial number is provided, it must be a unique serial number or a “device not found” error will result. If it is a unique serial number, then the device that has that serial number will be updated. |
| asset     | If the asset number is provided, it must be a unique asset number or a “device not found” error will result. If it is a unique asset number, then the device that has that asset number will be updated.     |
| device_id | update the device by Device42 ID                                                                                                                                                                             |

### Optional Parameters

Parameters are the same as the /api/1.0/devices/ POST call

## Delete Device

```shell
curl -X DELETE -d "id=abc1006" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/devices/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "abc1006" }
```

This API is used to delete a device with the device id supplied as the required argument.

**`DELETE /api/1.0/devices/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | Device id   |

## Create/Update Custom Fields

```shell
curl -X PUT -d "name=nh-switch-01&key=Project&value=Manhattan" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device/custom_field/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["custom key pair values added or updated", 8, "nh-switch-01"],
  "code": 0
}
```

Create / update existing custom fields for device

**`PUT /api/1.0/device/custom_field/`**

**See Notices Below**

### Required Parameters

One of the following parameters is required

| Parameter | Description                                                                                   |
| --------- | --------------------------------------------------------------------------------------------- |
| name      | name of the device, or                                                                        |
| asset     | if there is more than 1 device with the same asset #, this will return “device not found”, or |
| serial    | if there is more than 1 device with the same serial #, this will return “device not found”    |

<aside class="notice">
*You can update an existing device with any of the above parameters. However, the parameters will be processed name first, asset second, and serial third. The first one found will be used.*
</aside>

### Other Parameters

| Parameter          | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| key                | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type               | optional                        | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value              | optional                        |
| notes              | optional                        |
| clear_value        | optional                        | yes to clear existing value for that field (added in v6.1.0)                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| bulk_fields        | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2 (added in v6.4.1)                                                                                                                                                                                                                                                                                                                                                                           |

<aside class="notice">
value2 has been deprecated since v6.1.0
</aside>

## Get Device Mountpoints

```shell
 curl -X GET -u 'admin:adm!nd42' https://device42/api/1.0/device/mountpoints/ --insecure
```

> The above command returns results like this:

```json
{
  "mountpoint_details": [
    {
      "capacity": 238504.0,
      "device": "greg-ws",
      "filesystem": "",
      "free_capacity": 77187.07,
      "fstype": "NTFS",
      "id": 25,
      "label": "C:",
      "last_updated": "2016-09-16T15:17:19.110Z",
      "mountpoint": "C:"
    },
    {
      "capacity": null,
      "device": "greg-ws",
      "filesystem": "",
      "free_capacity": null,
      "fstype": null,
      "id": 26,
      "label": "D:",
      "last_updated": "2016-09-16T15:17:19.217Z",
      "mountpoint": "D:"
    }
  ],
  "total_count": 2
}
```

Get device mountpoints - introduced in version 10.5.0

**`GET /api/1.0/device/mountpoints/`**

### Query string parameters (Added in v10.5.0)

You can filter device mountpoints by following parameters in the query string

| Parameter | Description      |
| --------- | ---------------- |
| device_id | id of the device |

## Create/Update Device Mountpoints

```shell
 curl -X POST -d "mountpoint=/home/user&filesystem=ext4&device=heartofgold.local" -u 'admin:adm!nd42' https://device42/api/1.0/device/mountpoints/ --insecure
```

> The above command returns results like this:

```json
{
  "msg": ["mount point added/updated", 521, "/home/user", true, true],
  "code": 0
}
```

Create/Update device mountpoints - introduced in version 10.5.0

**`POST /api/1.0/device/mountpoints/`**

### Parameters

| Parameter     | Use      | Description                       |
| ------------- | -------- | --------------------------------- |
| mountpoint    | required | path of the mountpoint            |
| filesytem     | optional | mountpoint filesystem             |
| fstype        | optional | mountpoint filesystem type        |
| capacity      | optional | capacity of mountpoint in MB      |
| free_capacity | optional | free capacity of mountpoint in MB |
| label         | optional |
| device        | required | device mountpoint is assigned to  |

## Get Device URLs

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device/url/?device=nh-fujitsu-02 --insecure
```

> The above command returns results like this:

```json
{
  "device_weblinks": [
    {
      "device": "NHCTWS001",
      "host": " /v:{{device.name}}",
      "id": 2,
      "notes": "TESTSERVER NHCT",
      "port": "",
      "type": "rdp",
      "url_suffix": " /f /restrictedadmin"
    },
    {
      "device": "NHCTWS001",
      "host": "{{device.name}}",
      "id": 3,
      "notes": "Dev Test web server",
      "port": "",
      "type": "HTTP",
      "url_suffix": ""
    },
    {
      "device": "NHCTWS001",
      "host": "{{device.name}}",
      "id": 4,
      "notes": "Dev Test SSL",
      "port": "",
      "type": "HTTPS",
      "url_suffix": ""
    },
    {
      "device": "NHCTWS001",
      "host": "route-server.ip.att.net",
      "id": 5,
      "notes": "Demo Use Route Server USA/NY AT&T",
      "port": "",
      "type": "TELNET",
      "url_suffix": ""
    },
    {
      "device": "NHCTWS001",
      "host": "filezilla.ftptest.org",
      "id": 6,
      "notes": "Test FTP Site for Demo Use",
      "port": "",
      "type": "FTP",
      "url_suffix": ""
    },
    {
      "device": "NHCTWS001",
      "host": "docs.device42.com",
      "id": 8,
      "notes": "",
      "port": "",
      "type": "HTTP",
      "url_suffix": "/how-to-videos/adding-links-and-other-urls-to-devices/"
    }
  ]
}
```

Get device URLs - introduced in version 7.0.0

**`GET /api/1.0/device/url/`**

### Query string parameters (Added in v7.0.0)

You can filter device urls by following parameters in the query string

| Parameter | Description        |
| --------- | ------------------ |
| device    | name of the device |

## Add Url to Device

```shell
curl -X POST -d "device=nh-switch-01&type=http&host=www.device42.com&port=8080&url_suffix=awesome&notes=cool"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device/url/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "Device url saved succesfully.",
    2,
    "http://www.device42.com:8080/awesome"
  ],
  "code": 0
}
```

Use this API to associate a url (e.g. an http, https, or telnet url) with a device.

**`POST /api/1.0/device/url/`**

### Parameters

| Parameter  | Use      | Description                                                                                 |
| ---------- | -------- | ------------------------------------------------------------------------------------------- |
| type       | required | the url type (e.g. http, https, telnet). This must be an existing url type in device42.     |
| device     | required | name of the device to which this url belongs (required parameter)                           |
| host       | optional | fqdn portion of the url. e.g. for http://www.device42.com/awesome, host is www.device42.com |
| port       | optional | port number if any                                                                          |
| url_suffix | optional | url suffix if any. e.g. it is “awesome” based on example given above.                       |
| notes      | optional |

## Update Device URL

```shell
curl -X PUT -d "device=nh-switch-01&type=http&host=www.device42.com&port=8080&url_suffix=awesome&notes=cool"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device/url/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["Device url updated.", 2, "http://www.example.com:8080/awesome"],
  "code": 0
}
```

Use this API to update and existing url (e.g. an http, https, or telnet url) with a device.

**`PUT /api/1.0/device/url/`** or **`PUT /api/1.0/device/url/<ID>/`** (Added in v7.0.0)

### Parameters

| Parameter  | Use                                     | Description                                                                             |
| ---------- | --------------------------------------- | --------------------------------------------------------------------------------------- |
| id         | required if type and device not present | ID of the URL                                                                           |
| type       | required if no ID                       | the url type (e.g. http, https, telnet). This must be an existing url type in device42. |
| device     | required if no ID                       | name of the device to which this url belongs (required parameter)                       |
| host       | optional                                | fqdn portion of the url. e.g. for https://example.com/awesome, host is example.com      |
| port       | optional                                | port number if any                                                                      |
| url_suffix | optional                                | url suffix if any. e.g. it is “awesome” based on example given above.                   |
| notes      | optional                                |

## Delete a device url

```shell
curl -X DELETE -u'admin:adm!nd42' https://yourdevice42address/api/1.0/device/url/142/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "deleted": "true",
  "id": "142"
}
```

This API call will delete a device url (Introduced in v7.0.0)

## Add/Update a device in a rack

```shell
curl -X POST -d "device=nh-switch-01&building=New Haven DC&room=1st floor&rack=RA1&start_at=2&orientation=back" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device/rack/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["device added or updated in the rack", 2, "[2.0] - RA1 -1st floor"],
  "code": 0
}
```

This API will add a new or existing device to a rack or will update an existing device that is already in the rack. (Introduced in v321)

**`POST /api/1.0/device/rack/`**

**See Notices Below**

### Parameters

| Parameter                                    | Use      | Description                                                                                                                                                                                                                                                                                                      |
| -------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| device or device_id or serial_no or asset_no | required | required, name of the new or existing device, device ID, serial # or asset # of the existing device.                                                                                                                                                                                                             |
| hw_model                                     | optional | If the hw_model doesn't exist or doesn't have a type, we will add it as type "regular (rack mountable)" (changed in v6.6.0)                                                                                                                                                                                      |
| size                                         | optional | size of the hardware model, only for new hardware model or if hardware model doesn't have size. required for new hardware model (added in v6.6.0)                                                                                                                                                                |
| manufacturer                                 | optional | manufacturer of the hardware model. Only for new hardware model being added(added in v6.6.0)                                                                                                                                                                                                                     |
| rack_id                                      | required | required if building name, room name or rack name are not provided. This is the id of the rack. It can be obtained from Tools > Import > Import Racked Devices.                                                                                                                                                  |
| building, room, rack or row                  | required | building is building name, room is room name, rack is rack name and row is optional. This is used if rack_id is not provided and a unique rack is found with that combination. This could be just rack for rack name, if the rack name is unique. Otherwise add row, room or building to identify a unique rack. |
| start_at                                     | required | This is the starting U location for the device in the rack. Starting with v535, you can use “auto” as value to automatically mount the device in next available slot.                                                                                                                                            |
| where                                        | optional | location in rack, one of 'above', 'below', 'left', 'right', 'mounted'. Note: If mounted a size must be provided or available from the hardware model.                                                                                                                                                            |
| x_pos                                        | optional | A number between 0 and 2520 representing the position within the u slot. 0 is flush left. 2520 is flush right.                                                                                                                                                                                                   |

orientation | optional | Could be front or back, assumed front is no value is supplied.

<aside class="notice">

* If the device is new, it will be created and added to rack.
* If the device is existing but not in the rack, it will be added to the rack.
* If the device exists and is already in the rack, the parameters will be updated.

</aside>

## Remove/Delete a device from a rack

```shell
curl -X DELETE -u'admin:adm!nd42' https://yourdevice42address/api/1.0/device/rack/142/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "deleted": "true",
  "id": "142"
}
```

This API call will remove/delete a device from a rack (Introduced in v6.3.2)

**`DELETE /api/1.0/device/rack/{DEVICE_ID}/`**

### Parameters

| Parameter | Description                                  |
| --------- | -------------------------------------------- |
| Device ID | ID of the device to be removed from the rack |

# Object Categories

## Get all Object Categories

```shell
curl -X GET -u 'admin:adm!nd42' "https://device42address/api/1.0/object_categories/"
```

> The above command returns JSON structured like this:

```json
{
  "limit": 1000,
  "object_categories": [
    {
      "description":
        "Devices that the Network Administration team should have full access to",
      "name": "Network Admin Team"
    },
    {
      "description": "Devices the DB administration team uses",
      "name": "DB Servers"
    }
  ],
  "offset": 0,
  "total_count": 3
}
```

This call will get information about object categories.

**`GET /api/1.0/object_categories/`**

### Query string parameters

You can filter Object Categories by following parameters in the query string

| Parameter | Description                      |
| --------- | -------------------------------- |
| name      | filter object categories by name |

## Create/update Object Categories

```shell
curl -X POST -d "name=Web Apps&description=Servers for web team"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/object_catgories/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "Object category added/updated successfully",
    4,
    "Web Apps",
    true,
    true
  ],
  "code": 0
}
```

This call will create/update information about object categories.

**`POST /api/1.0/object_categories/`**

### Parameters

| Parameter   | Use      | Description                               |
| ----------- | -------- | ----------------------------------------- |
| name        | required | Name of object category to create/update. |
| description | optional | Description of object category.           |

# Hardware Models

## Get all hardware models

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/hardwares/ --insecure
```

> The above command returns JSON structured like this:

```json
{
    "models": [
        {
            "back_image_id": 746,
            "back_image_url": "/var/www/graphics/images/dellback.png",
            "blade_size": null,
            "depth": "Full Depth",
            "front_image_id": 747,
            "front_image_url": "/var/www/graphics/images/dellfront.png",
            "hardware_id": 317,
            "manufacturer": "Dell Inc.",
            "name": "Precision 3420",
            "notes": "",
            "part_no": "",
            "size": 2.0,
            "spec_url": "",
            "type": "physical",
            "watts": null,
            "width_ratio_raw": 2520
        },....
```

This call will get information about hardware models.

**`GET /api/1.0/hardwares/`**

### Query string parameters

You can filter parts by following parameters in the query string

| Parameter      | Description                                                 |
| -------------- | ----------------------------------------------------------- |
| name           | filter by hardware models that contain full or partial name |
| type           | could be physical, blade or other                           |
| device_subtype | Subtype of "other" type devices                             |
| size           | filter by exact size                                        |
| depth          | could be full or half                                       |
| part_no        | filter by part #                                            |
| watts          | filter by exact watts                                       |
| manufacturer   | name of the manufacturer                                    |

## Create/update

```shell
curl -X POST -d "name=PE 1950&type=1&size=1&depth=1&part_no=123&watts=265&spec_url=www.dell.com&manufacturer=dell&notes=hellp"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/hardwares/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["hardware model added or updated", 1, "PE 1950"], "code": 0 }
```

This call will create/update information about hardware models.

**`POST /api/1.0/hardwares/`**

### Parameters

| Parameter      | Use       | Description                                                                                     |
| -------------- | --------- | ----------------------------------------------------------------------------------------------- |
| name           | required  | If similar hardware name already exists, first matching entry is updated.                       |
| new_name       | optional  | Hardware model must already exist by "name", renames model                                      |
| type           | optional  | 1=Regular, 2=Blade, 3=Other                                                                     |
| device_subtype | optional  | Subtype of "other" type devices                                                                 |
| size           | optional  | size in U for hardware type regular.                                                            |
| width_ratio    | optional  | Default=1. Can be 1/2, 1/3,... 1/10, etc.                                                       |
| depth          | optional  | 2 = Half depth, leave empty or 1 for full depth. Since v5.8.2 you can also use "full" or "half" |
| blade_size     | optional  | 1=Full Height 2=Half Height 3=Double Half Height 4=Double Full Height                           |
| part_no        | optional  |
| watts          | optional  | Per power supply.                                                                               |
| spec_url       | optional  | Specification url for the hardware model.                                                       |
| manufacturer   | optional  |
| front_image_id | optional  |
| front_image    | optional  | name of the image file (Added in v5.8.2)                                                        |
| back_image_id  | optional  |
| back_image     | optional  | name of the image file (Added in v5.8.2)                                                        |
| notes          | optional. |

## Delete Hardware Model

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/hardwares/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the hardware model with the hardware model id supplied as the required argument.

**`DELETE /api/1.0/hardwares/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description       |
| --------- | ----------------- |
| id        | hardware model id |

# Operating Systems

## Get all operating systems

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/operatingsystems/ --insecure
```

> The above command returns JSON structured like this:

```json
{
{
  "operatingsystems": [
        {
            "aliases": [],
            "category": null,
            "id": 1,
            "licensed_count": 5,
            "manufacturer": null,
            "name": "Microsoft Windows 7 Professional",
            "not_licensed_count": 0,
            "notes": "",
            "total_count": 5
        },
        {
            "aliases": [],
            "category": null,
            "id": 32,
            "licensed_count": 1,
            "manufacturer": null,
            "name": "Oracle Linux Server release 6.3",
            "not_licensed_count": 0,
            "notes": "",
            "total_count": 1
        },....
```

This call will get information about operating systems.

**`GET /api/1.0/operatingsystems/`** (added in v8.3.0)

| Parameter          | Description                                                            |
| ------------------ | ---------------------------------------------------------------------- |
| licensed_count     | Number of licensed instances of operating system                       |
| not_licensed_count | Number of discovered instances of operating system not set to licensed |
| total_count        | Total number of discovered instances of operating system               |
| category           | Filter by OS category (ie: Linux, Windows)                             |

## Create/update OS

```shell
curl -X POST -d "name=ESX6.0&manufacturer=VMware, Inc." -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/operatingsystems/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["Operating System added/updated", 77, "ESX6.0", false, false],
  "code": 0
}
```

This call will create/update information about operating systems.

**`POST /api/1.0/operatingsystems/`**

### Parameters

| Parameter    | Use       | Description    |
| ------------ | --------- | -------------- |
| name         | required  | name of the OS |
| manufacturer | optional  |
| notes        | optional. |

## Delete Operating System

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/operatingsystems/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the operating system with the operating system id supplied as the required argument.

**`DELETE /api/1.0/operatingsystems/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description         |
| --------- | ------------------- |
| id        | opearting system id |

## Get operating systems by devices

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device_os/ --insecure
```

> The above command returns JSON structured like this:

```json
{
    "device_os": [
       {
            "count_in_licensing": "yes",
            "device": "NHCTS003",
            "device_id": 120,
            "device_os_id": 2,
            "id": 2,
            "license_key": "",
            "os": "Microsoft Windows Server 2008 STD",
            "os_id": 2,
            "osver": "",
            "osverno": ""
        },
        {
            "count_in_licensing": "yes",
            "device": "NHCTWS001",
            "device_id": 136,
            "device_os_id": 3,
            "id": 3,
            "license_key": "",
            "os": "Microsoft Windows 7 Professional",
            "os_id": 1,
            "osver": "64bit",
            "osverno": "7.1"
        },
        {
            "count_in_licensing": "yes",
            "device": "NHCTWS002",
            "device_id": 142,
            "device_os_id": 5,
            "id": 5,
            "license_key": "",
            "os": "Microsoft Windows 7 Professional",
            "os_id": 1,
            "osver": "64bit",
            "osverno": ""
        },
        {
            "count_in_licensing": "yes",
            "device": "USNHCTVSDC1",
            "device_id": 150,
            "device_os_id": 7,
            "id": 7,
            "license_key": "",
            "os": "Microsoft Windows Server 2008 STD",
            "os_id": 2,
            "osver": "",
            "osverno": ""
        },....
```

This call will get information about operating systems and the devices they're discovered on.

**`GET /api/1.0/device_os/`** (added in v10.0)

| Parameter | Description           |
| --------- | --------------------- |
| os        | Operating system name |
| os_id     | Operating system ID   |

## Create/Update operating systems on devices

```shell
curl -X POST -d "device_os_id=9&device_id=9&os=Ubuntu 16.04" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device_os/ --insecure
```

> The above command returns JSON structured like this:

```json
{
{"msg": ["device_os added/updated", 9, "Production-server (Ubuntu Linux 16.04 (64-bit))", true, true], "code": 0}
```

This call will create or update operating systems and assign them to a device.

| Parameter          | Required | Description                             |
| ------------------ | -------- | --------------------------------------- |
| device_os_id       | Yes      | ID of specific operating system         |
| device_id          | Yes      | ID of device OS is assigned to          |
| os                 | No       | Operating system name                   |
| osver              | No       | Operating system version name           |
| osverno            | No       | Operating system version number         |
| license_key        | No       | OS license key                          |
| count_in_licensing | No       | Whether or not to count OS in licensing |

**`POST /api/1.0/device_os/`** (added in v10.0)

## Delete Operating system by device

```shell
curl -X delete -d "device_os_id=9" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/device_os/9/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "9" }
```

This API is used to delete the operating system with the operating system id supplied as the required argument.

**`DELETE /api/1.0/device_os/<ID>/`** (added in v10.0)

| Parameter    | Required | Description                     |
| ------------ | -------- | ------------------------------- |
| device_os_id | Yes      | ID of specific operating system |

# PDU

## Get all PDU Models

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/pdu_models/ --insecure
```

> The above command returns results like this:

```json
{
    "pdu_models": [
        {
            "back_image": {
                "id": 22,
                "imgfile": "/var/www/graphics/images/apc-rack-pdu-front.png"
            },
            "depth": "Half Depth",
            "description": "",
            "front_image": {
                "id": 66,
                "imgfile": "/var/www/graphics/images/d42-apc-rack-pdu-front.png"
            },
            "manufacturer": "Server Technology",
            "name": "CXG-8H1A113",
            "notes": "",
            "pdu_model_id": 7,
            "port_count": 8,
            "ports in pdu model": [
                {
                    "pdu_port_count": 12,
                    "pdu_port_type": "Custom"
                },
                {
                    "pdu_port_count": 12,
                    "pdu_port_type": "Custom"
                }
            ],
            "sequential_numbering_for_ports": false,
            "size": 1.0,
            "type": "pdu",
            "width_ratio": 2520
        },
         ......(truncated)
    ]
}
```

GET method retrieves all PDU Models.

**`GET /api/1.0/pdu_models/`**

**`GET /api/1.0/power_unit_models/`** (Added in v9.8.0)

## Create / Update PDU Models

```shell
curl -X POST  -d "name=APC24G&size=4.8&depth=full&manufacturer=APC&imgfile_id=140&sequential_numbering_for_ports=no"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/pdu_models/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["PDU Model added/updated", 16, "APC24G", true, false], "code": 0 }
```

Create / Update PDU Models.

**`POST /api/1.0/pdu_models/`** (Added in v5.8.1)

**`POST /api/1.0/power_unit_models/`** (Added in v9.8.0)

**See notice below**

### Parameters

| Parameter                      | Use      | Description                                                         |
| ------------------------------ | -------- | ------------------------------------------------------------------- |
| pdu_model_id                   | optional | ID of the PDU model you want to update                              |
| name                           | optional | Name of the PDU model you want to create or update                  |
| manufacturer                   | optional | Name of the manufacturer                                            |
| size                           | optional |
| sequential_numbering_for_ports | optional | Could be "yes" or "no". Yes if ports are numbered starting from 1.  |
| depth                          | optional | Possible values half or full                                        |
| imgfile_id                     | optional | ID of the image file                                                |
| imgfile                        | optional | name of the image file (Added in v5.8.2). Use instead of imgfile_id |
| back_image_id                  | optional | back image file id.                                                 |
| back_image                     | optional | name of the back image file.                                        |

<aside class="notice">
Updating existing PDU Model requires pdu_model_id or name. name is required to create new PDU Model.
</aside>

## Create PDU Model Ports

```shell
curl -X POST  -d "pdu_model_name=APC24G&count=10&type=NEMA 5-15R"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/pdu_models/ports/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["PDU Model updated", 16, "APC24G", true, true], "code": 0 }
```

Create PDU Model Ports.

**`POST /api/1.0/pdu_models/ports/`** (Added in v5.8.1)

**`POST /api/1.0/power_unit_models/ports/`** (Added in v9.8.0)

### Parameters

| Parameter      | Use      | Description                                                                       |
| -------------- | -------- | --------------------------------------------------------------------------------- |
| pdu_model_id   | optional | ID of the PDU model you want to add ports to. Either ID or name below is required |
| pdu_model_name | optional | Name of the PDU model you want to add ports to.                                   |
| count          | required | Count for # of ports                                                              |
| type           | required | Type of the port. If not already existing, a new port type is created             |

## Get all PDUs

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/pdus/ --insecure
```

> The above command returns results like this:

```json
{
        "pdus": [
        {
            "building": "New Haven",
            "building_id": 1,
            "category": "Prod_East",
            "device mapping": "",
            "name": "NHCTPDU01",
            "notes": "",
            "pdu_id": 4,
            "pdu_model": {
                "back_image": null,
                "depth": "Half Depth",
                "description": "",
                "front_image": {
                    "id": 22,
                    "imgfile": "/var/www/graphics/images/apc-rack-pdu-front.png"
                },
                "manufacturer": "APC Inc.",
                "name": "APC 8841",
                "notes": "",
                "pdu_model_id": 4,
                "port_count": 12,
                "ports in pdu model": [
                    {
                        "pdu_port_count": 12,
                        "pdu_port_type": "IEC 320 C19"
                    }
                ],
                "sequential_numbering_for_ports": true,
                "size": 1.0,
                "type": "pdu",
                "width_ratio": 2520
            },
            "rack": "NH-DC1-01",
            "rack_id": 20,
            "room": "NHDC1",
            "room_id": 1,
            "type": "pdu"
        }
    ]
}...
```

GET method retrieves all PDUs.

**`GET /api/1.0/pdus/`**

**`GET /api/1.0/power_units/`** (Added in v9.8.0)

## Get PDU by ID

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/pdus/<id>/ --insecure
```

> The above command returns results like this:

```json
{
  "building": "",
  "building_id": "",
  "device mapping": {
    "aliases": [],
    "asset_no": "",
    "building": "New Haven",
    "cpucore": null,
    "cpucount": null,
    "cpuspeed": null,
    "custom_fields": [
      {
        "key": "Agent email address",
        "notes": "",
        "value": ""
      },
      {
        "key": "Move Group",
        "notes": "",
        "value": ""
      },
      {
        "key": "Rating",
        "notes": "",
        "value": ""
      },
      {
        "key": "Rating Date",
        "notes": "",
        "value": ""
      },
      {
        "key": "Project",
        "notes": "",
        "value": ""
      },
      {
        "key": "Skynet",
        "notes": "",
        "value": ""
      },
      {
        "key": "username",
        "notes": "",
        "value": ""
      }
    ],
    "customer": null,
    "device_external_links": [],
    "device_id": 122,
    "device_purchase_line_items": [],
    "device_sub_type": "UPS",
    "hdd_details": null,
    "hddcount": null,
    "hddraid": null,
    "hddraid_type": null,
    "hddsize": null,
    "hw_depth": null,
    "hw_model": null,
    "hw_size": null,
    "id": 122,
    "in_service": true,
    "ip_addresses": [],
    "last_updated": "2016-07-05T22:08:34.849Z",
    "mac_addresses": [],
    "manufacturer": null,
    "name": "UPS1",
    "notes": "",
    "os": null,
    "pdu_mapping_url": "/api/1.0/power_units/232/",
    "ram": null,
    "room": "NHDC1",
    "serial_no": "",
    "service_level": "Production",
    "tags": [],
    "type": "other",
    "ucs_manager": null,
    "uuid": "",
    "virtual_host_name": null
  },
  "name": "UPS1",
  "notes": "",
  "pdu_id": 232,
  "pdu_model": "",
  "rack": "",
  "rack_id": "",
  "room": "",
  "room_id": "",
  "type": "ups"
}
```

Retrieve detailed information about a specific PDU by PDU ID. This also includes end point connections.

**`GET /api/1.0/pdus/<ID>/`**

**`GET /api/1.0/power_units/<ID>/`** (Added in v9.8.0)

### URL Parameters

| Parameter | Description                   |
| --------- | ----------------------------- |
| ID        | The ID of the PDU to retrieve |

## Create PDUs

```shell
curl -X POST -d "pdu_model_id=1&name=PDU42&notes=test0" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/pdus/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["pdu successfully added", 30, "PDU42"], "code": 0 }
```

This call will create PDUs.

**`POST /api/1.0/pdus/`**

**`POST /api/1.0/power_units/`** (Added in v9.8.0)

### Parameters

| Parameter    | Use      | Description                                                                                                                                                                        |
| ------------ | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name         | required |
| pdu_model_id | optional | This is the id of the PDU model for this PDU. You can get this list from /api/1.0/pdu_models/                                                                                      |
| pdu_model    | optional | Name of the PDU model. You can use this instead of the ID above. (Added in v5.8.2)                                                                                                 |
| device       | optional | If you want to associate asset information with this PDU, use device type ‘other’ (already existing in device42)                                                                   |
| notes        | optional |
| category     | optional | If multitenancy is on, admin groups that have access to this category, will have access to the power unit. If this parameter is present with no value, all categories are deleted. |

## Update PDUs

```shell
curl -X PUT -d "pdu_model_id=1&name=PDU42&notes=test0&port_number=1&outlet_name=TEST" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/pdus/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["pdu successfully edited", 30, "PDU42"], "code": 0 }
```

This call will update existing PDUs.

**`PUT /api/1.0/pdus/`** (Added in v5.8.2)

**`PUT /api/1.0/power_units/`** (Added in v9.8.0)

### Parameters

| Parameter       | Use                               | Description                                                                                                                                                                        |
| --------------- | --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| pdu_id          | required, unless name is provided | ID of the PDU to be edited                                                                                                                                                         |
| name            | optional                          | Use instead of PDU ID. Must be unique.                                                                                                                                             |
| new_name        | optional                          | Use to rename PDU.                                                                                                                                                                 |
| pdu_model_id    | optional                          | This is the id of the PDU model for this PDU. You can get this list from /api/1.0/pdu_models/                                                                                      |
| pdu_model       | optional                          | Name of the PDU model. You can use this instead of the ID above. (Added in v5.8.2)                                                                                                 |
| type            | optional                          | Type of power unit (pdu, ups, ats)                                                                                                                                                 |
| device          | optional                          | If you want to associate asset information with this PDU, use device type ‘other’ (already existing in device42)                                                                   |
| port_number     | optional                          | port number                                                                                                                                                                        |
| outlet_name     | optional                          | outlet name                                                                                                                                                                        |
| notes           | optional                          |
| rated_power     | rated power                       |
| object_category | optional                          |
| storage_room_id | optional                          | ID of storage room to assign power unit to                                                                                                                                         |
| storage_room    | optional                          | Name of storage room to apply power unit to                                                                                                                                        |
| category        | optional                          | If multitenancy is on, admin groups that have access to this category, will have access to the power unit. If this parameter is present with no value, all categories are deleted. |

## Delete PDU

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/pdus/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the pdu with the pdu id supplied as the required argument.

**`DELETE /api/1.0/pdus/<ID>/`** (Added in v6.3.4)

**`DELETE /api/1.0/power_units/<ID>/`** (Added in v9.8.0)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | PDU id      |

## Add / Update PDUs in Racks

```shell
curl -X POST  -d "pdu_id=1&rack_id=1&where=left"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/pdus/rack/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["PDU Rack Info successfully added/edited.", 19, "a3cpdu001"],
  "code": 0
}
```

This call will add / update PDUs in or around a Rack.

**`POST /api/1.0/pdus/rack/`**

**`POST /api/1.0/power_units/rack/`** (Added in v9.8.0)

### Parameters

| Parameter                   | Use                             | Description                                                                                                                                                                                                                                                                                                      |
| --------------------------- | ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| pdu_id                      | required/see below              | Available from /api/1.0/pdus/ or Tools                                                                                                                                                                                                                                                                           | Export | PDU. |
| pdu                         | required if pdu_id not supplied | name of the PDU. only works if the name is unique in the system                                                                                                                                                                                                                                                  |
| rack_id                     | required/see below              | This is the id of the rack. It can be obtained from Tools > Import > Import Racked Devices. Or /api/1.0/racks/ or Tools                                                                                                                                                                                          | Export | Rack |
| building, room, rack or row | optional                        | building is building name, room is room name, rack is rack name and row is optional. This is used if rack_id is not provided and a unique rack is found with that combination. This could be just rack for rack name, if the rack name is unique. Otherwise add row, room or building to identify a unique rack. |
| where                       | required                        | Allowed values: left, right, above, below or mounted.                                                                                                                                                                                                                                                            |
| start_at                    | optional                        | This is the starting U location for the PDU.                                                                                                                                                                                                                                                                     |
| orientation                 | optional                        | back for rear facing, otherwise front is default.                                                                                                                                                                                                                                                                |
| x_pos                       | optional                        | A number between 0 and 2520 representing the position within the u slot. 0 is flush left. 2520 is flush right.                                                                                                                                                                                                   |

## Delete PDU from a rack

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/pdu/rack/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete from its rack the device with the device id supplied as the required argument. (The device itself is not deleted).

**`DELETE /api/1.0/pdu/rack/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | device id   |

## Update PDU Ports w/ no names

```shell
curl -X POST  -d "parent_pdu_id=1&name=1&device=nh-linux-01&port_type=nema 5-15r"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/pdus/ports/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg": ["PDU port detailed added successfully."365"test"],"code": 0}
```

Assign a name and/or an object (see below) objects to a pdu port. It will pick the lowest port id # available (or first available port in order created).

**`POST /api/1.0/pdus/ports/`**

**`POST /api/1.0/power_units/ports/`** (Added in v9.8.0)

### Parameters

| Parameter                           | Use                                     | Description                                                                                        |
| ----------------------------------- | --------------------------------------- | -------------------------------------------------------------------------------------------------- |
| parent_pdu_id                       | required, unless name is given as below | Available from /api/1.0/pdus/ or Tools                                                             | Export | PDU - parent_pdu_id = pdu_id. |
| parent_pdu                          | optional                                | name of the parent PDU. Must be unique name. Added in v5.8.2                                       |
| port_type                           | required                                | Verbose name of the port type. Must exist already.                                                 |
| device, device_id, pdu_id, asset_id | optional                                | any of these 4 values, parsed in that order, to depict which object the port belongs to.           |
| name                                | optional                                | PDU Port name, typically the PDU Port number when autodiscovered.                                  |
| outlet_name                         | optional                                | outlet name.                                                                                       |
| watts                               | optional                                | wattage of power supply connected to this port, overrides hardware model wattage - if applicable.  |
| psu_label                           | optional                                | typically used when device has multiple power supplies, e.g.: power supply 1, power supply 2, etc. |

## Update PDU Ports w/ names

```shell
curl -X PUT  -d "parent_pdu_id=1&name=1&outlet_name=1&device=nh-linux-01&psu_label=PSU1&watts=300"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/pdus/ports/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg":["PDU port detailed edited successfully."366"2"],"code": 0}
```

This call requires the name of an existing pdu port and enables you to add new or edit existing values for that particular PDU port.

**`PUT /api/1.0/pdus/ports/`**

**`PUT /api/1.0/power_units/ports/`** (Added in v9.8.0)

### Parameters

| Parameter                           | Use                                     | Description                                                                                        |
| ----------------------------------- | --------------------------------------- | -------------------------------------------------------------------------------------------------- |
| parent_pdu_id                       | required, unless name is given as below | Available from /api/1.0/pdus/ or Tools                                                             | Export | PDU - parent_pdu_id = pdu_id. |
| parent_pdu                          | optional                                | name of the parent PDU. Must be unique name. Added in v5.8.2                                       |
| name                                | required                                | name of the PDU port.                                                                              |
| outlet_name                         | optional                                | name of the PDU outlet.                                                                            |
| device, device_id, pdu_id, asset_id | optional                                | any of these 4 values, parsed in that order, to depict which object the port belongs to.           |
| watts                               | optional                                | wattage of power supply connected to this port, overrides hardware model wattage - if applicable.  |
| psu_label                           | optional                                | typically used when device has multiple power supplies, e.g.: power supply 1, power supply 2, etc. |

# Patch Panels

## Get Patch Panel details by ID

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/patch_panel/<id>/ --insecure
```

> The above command returns results like this:

```json
{
  "asset_contracts": [],
  "asset_id": 29,
  "asset_no": "0010",
  "asset_purchases": [],
  "building": "New Haven",
  "custom_fields": "",
  "customer_id": 1,
  "depth": "Half Depth",
  "imagefile_id": 2,
  "name": "DC1R1PP1",
  "no_ports": 12,
  "no_ports_in_row": 12,
  "notes": "Notes, Notes, Notes...",
  "numbering_direction": "left-right",
  "numbering_start_location": "top-left",
  "orientation": "Back",
  "patch_panel_model_id": 6,
  "ports": [
    {
      "back_connection_id": 109,
      "back_pp_id": 16,
      "cable_color": "black",
      "color": "#659a39",
      "content_type": "device",
      "device_id": 153,
      "device_name": "USNHCTVH002",
      "full_path":
        "USNHCTVH002 <-> [DC1R1PP1-29:1] <-> DC1R5PP1-109:1 <-> NHCTCORE01(gbE/1)",
      "number": 1,
      "obj_label": "L1",
      "object_id": 153,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    },
    {
      "back_connection_id": 110,
      "back_pp_id": 16,
      "cable_color": "black",
      "color": "#659a39",
      "content_type": "device",
      "device_id": 191,
      "device_name": "USNHCTC0001",
      "full_path":
        "USNHCTC0001 <-> [DC1R1PP1-29:2] <-> DC1R5PP1-110:2 <-> NHCTCORE01(gbE/2)",
      "number": 2,
      "obj_label": "",
      "object_id": 191,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    },
    {
      "back_connection_id": 111,
      "back_pp_id": 16,
      "cable_color": "black",
      "color": "#ff7373",
      "full_path":
        "None <-> [DC1R1PP1-29:3] <-> DC1R5PP1-111:3 <-> NHCTCORE01(gbE/3)",
      "number": 3,
      "object_id": null,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    },
    {
      "back_connection_id": 182,
      "back_pp_id": 17,
      "cable_color": "black",
      "color": "#ff7373",
      "full_path":
        "None <-> [DC1R1PP1-29:4] <-> DC1R6PP1-182:26 <-> NHCTCORE02(gbE/9)",
      "number": 4,
      "object_id": null,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    },
    {
      "back_connection_id": 183,
      "back_pp_id": 17,
      "cable_color": "black",
      "color": "#659a39",
      "content_type": "device",
      "device_id": 191,
      "device_name": "USNHCTC0001",
      "full_path":
        "USNHCTC0001 <-> [DC1R1PP1-29:5] <-> DC1R6PP1-183:27 <-> NHCTCORE02(gbE/7)",
      "number": 5,
      "obj_label": "",
      "object_id": 191,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    },
    {
      "back_connection_id": 184,
      "back_pp_id": 17,
      "cable_color": "black",
      "color": "#659a39",
      "content_type": "device",
      "device_id": 182,
      "device_name": "D42DEMODEVICE4",
      "full_path":
        "D42DEMODEVICE4 <-> [DC1R1PP1-29:6] <-> DC1R6PP1-184:28 <-> NHCTCORE02(gbE/8)",
      "number": 6,
      "obj_label": "",
      "object_id": 182,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    },
    {
      "back_connection_id": 204,
      "back_pp_id": 17,
      "cable_color": "black",
      "color": "#659a39",
      "content_type": "netport",
      "full_path":
        "NHCTCORE01(fiber/8) <-> [DC1R1PP1-29:7] <-> DC1R6PP1-204:48 <-> NHCTCORE02(gbE/24)",
      "netport_device_id": 105,
      "netport_device_name": "NHCTCORE01",
      "netport_id": 324,
      "netport_name": "fiber/8",
      "number": 7,
      "obj_label": "",
      "object_id": 324,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    },
    {
      "back_connection_id": 116,
      "back_pp_id": 16,
      "cable_color": "black",
      "color": "#659a39",
      "content_type": "device",
      "device_id": 133,
      "device_name": "NHCTCC01",
      "full_path":
        "NHCTCC01 <-> [DC1R1PP1-29:8] <-> DC1R5PP1-116:8 <-> NHCTCORE01(gbE/9)",
      "number": 8,
      "obj_label": "",
      "object_id": 133,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    },
    {
      "back_connection_id": 115,
      "back_pp_id": 16,
      "cable_color": "black",
      "color": "#659a39",
      "content_type": "device",
      "device_id": 105,
      "device_name": "NHCTCORE01",
      "full_path":
        "NHCTCORE01 <-> [DC1R1PP1-29:9] <-> DC1R5PP1-115:7 <-> NHCTCORE01(gbE/8)",
      "number": 9,
      "obj_label": "",
      "object_id": 105,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    },
    {
      "back_connection_id": 720,
      "back_pp_id": 51,
      "cable_color": "black",
      "color": "#659a39",
      "content_type": "netport",
      "full_path":
        "NHCTCORE01(fiber/10) <-> [DC1R1PP1-29:10] <-> DC1R3PP1-720:48 <-> chassis-02(ge1/1)",
      "netport_device_id": 105,
      "netport_device_name": "NHCTCORE01",
      "netport_id": 326,
      "netport_name": "fiber/10",
      "number": 10,
      "obj_label": "",
      "object_id": 326,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    },
    {
      "back_connection_id": 1248,
      "back_pp_id": 226,
      "cable_color": "black",
      "color": "#ff7373",
      "content_type": "device",
      "device_id": 120,
      "device_name": "NHCTS003",
      "full_path":
        "NHCTS003 <-> [DC1R1PP1-29:11] <-> test42-panel-1248:24 <-> None",
      "number": 11,
      "obj_label": "ServerNic1",
      "object_id": 120,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    },
    {
      "back_connection_id": 1247,
      "back_pp_id": 226,
      "cable_color": "black",
      "color": "#ff7373",
      "content_type": "device",
      "device_id": 182,
      "device_name": "D42DEMODEVICE4",
      "full_path":
        "D42DEMODEVICE4 <-> [DC1R1PP1-29:12] <-> test42-panel-1247:23 <-> None",
      "number": 12,
      "obj_label": "connection 3",
      "object_id": 182,
      "panel_id": 29,
      "panel_name": "DC1R1PP1"
    }
  ],
  "rack": "NH-DC1-04",
  "rack_id": 23,
  "room": "NHDC1",
  "row": "1",
  "serial_no": "4242424242",
  "service_level": "In Service",
  "size": 2.0,
  "start_at": 40.0,
  "type": 1,
  "vendor": "Black Box Corp.",
  "vendor_id": 36,
  "where": "Rack Mounted"
}
```

Retrieve detailed information about a specific Patch Panel by Patch Panel ID. This also includes end point connections.

**`GET /api/1.0/patch_panel/<ID>/`**

### URL Parameters

| Parameter | Description                           |
| --------- | ------------------------------------- |
| ID        | The ID of the Patch Panel to retrieve |

## Get all Patch Panel Ports

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/patch_panel_ports/<patch_panel_id>/ --insecure
```

> The above command returns results like this:

```json
[
  {
    "name": 1,
    "ppport_id": 481
  },
  {
    "name": 2,
    "ppport_id": 482
  }
]
```

Retrieves patch panel ports for specified patch panel.

**`GET /api/1.0/patch_panel_ports/<patch_panel_id>/`**

## Get all Patch Panel Models

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/patch_panel_models/ --insecure
```

> The above command returns results like this:

```json
[
  {
    "module_position": "horizontal",
    "name": "m1",
    "numbering_direction": "left-right",
    "numbering_start_location": "top-left",
    "patch_panel_model_id": 1,
    "type": "Modular",
    "type_id": 2
  },
  {
    "name": "p1",
    "number_of_ports": 48,
    "number_of_ports_in_row": 24,
    "numbering_direction": "left-right",
    "numbering_start_location": "top-left",
    "patch_panel_model_id": 2,
    "port_type": "Fiber LC",
    "type": "Singular",
    "type_id": 1
  }
]
```

Retrieves all patch panel models.

**`GET api/1.0/patch_panel_models/`**

## Create/Update Patch Panel Model

```shell
curl -X POST  -d "name=48-port-ethernet&manufacturer=Black Box&port_type=RJ 45&number_of_ports=48&number_of_ports_in_row=24"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/patch_panel_models/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "Patch Panel Model added/updated",
    12,
    "48-port-ethernet",
    true,
    false
  ],
  "code": 0
}
```

Create Patch Panel Model.

**`POST /api/1.0/patch_panel_models/`** (Added in v5.8.1)

### Parameters

| Parameter              | Use      | Description                                                                                                      |
| ---------------------- | -------- | ---------------------------------------------------------------------------------------------------------------- |
| patch_panel_model_id   | optional | ID of the Patch panel model you want to edit. Either ID or name below is required                                |
| name                   | optional | Name of the Patch panel model you want to add edit or create                                                     |
| manufacturer           | optional | Name of the Manufacturer                                                                                         |
| type                   | optional | Possible values: singular or modular. singular by default                                                        |
| port_type              | optional | Name of the port type. created if doesn't exist already                                                          |
| paired_ports           | optional | Possible values yes or no                                                                                        |
| imgfile_id             | optional | ID of the image file                                                                                             |
| imgfile                | optional | name of the image file (Added in v5.8.2). Use instead of imgfile_id                                              |
| number_of_ports        | optional | # of ports. required for creating a new patch panel type singular. Ignored for patch panel type modular          |
| number_of_ports_in_row | optional | # of ports in a row. required for creating a new patch panel type singular. Ignored for patch panel type modular |
| module_position        | optional | for Modular Patch Panel Models. Possible values are horizontal or vertical (Added in v5.8.2)                     |

## Get all Patch Panel Module Models

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/patch_panel_module_models/ --insecure
```

> The above command returns results like this:

```json
[
  {
    "name": "r1",
    "number_of_ports": 12,
    "number_of_ports_in_row": 6,
    "patch_panel_module_model_id": 1,
    "port_type": "Fiber LC"
  },
  {
    "name": "CAT24",
    "number_of_ports": 24,
    "number_of_ports_in_row": 24,
    "patch_panel_module_model_id": 2,
    "port_type": "RJ45"
  }
]
```

Retrieves all patch panel module models.

**`GET api/1.0/patch_panel_module_models/`**

## Create/Update Patch Panel Module Model

```shell
curl -X POST  -d "name=6-port-module&port_type=RJ 45&number_of_ports=6&number_of_ports_in_row=6"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/patch_panel_module_models/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "Patch Panel Module Model added/updated",
    2,
    "6-port-module",
    true,
    false
  ],
  "code": 0
}
```

Create/Update Patch Panel Module Model.

**`POST /api/1.0/patch_panel_module_models/`** (Added in v5.8.2)

### Parameters

| Parameter                   | Use      | Description                                                                                                      |
| --------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------- |
| patch_panel_module_model_id | optional | ID of the Patch panel module model you want to edit. Either ID or name below is required                         |
| name                        | optional | Name of the Patch panel module model you want to add edit or create                                              |
| port_type                   | optional | Name of the port type. created if doesn't exist already                                                          |
| paired_ports                | optional | Possible values yes or no                                                                                        |
| number_of_ports             | optional | # of ports. required for creating a new patch panel type singular. Ignored for patch panel type modular          |
| number_of_ports_in_row      | optional | # of ports in a row. required for creating a new patch panel type singular. Ignored for patch panel type modular |

## Update Patch Panel Ports

```shell
curl -X POST -d "patch_panel_id=49&number=1&mac_id=123&label=nh-linux-23 cat2 2/7&back_connection_id=962&cable_color=blue&cable_type=cat5e" -u 'admin:adm!nd42' https://yourdevice42ipaddress/api/1.0/patch_panel_ports/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "patch port details edited successfully.",
    490,
    "DC1R1PP1 : 10 (testtest)"
  ],
  "code": 0
}
```

**`POST /api/1.0/patch_panel_ports/`**

**See Notices Below**

### Parameters

| Parameter                       | Use                                                  | Description                                                                                                                                                 |
| ------------------------------- | ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| patch_panel_id                  | required if patch_panel not passed                   | Available via GET at /api/1.0/assets/ or Tools > Export > Asset or via GUI                                                                                  |
| patch_panel                     | required if patch_panel_id not passed                | Must be a unique asset name for this to work (Added in v5.8.2)                                                                                              |
| number                          | required                                             | Port number for the patch panel.                                                                                                                            |
| module_slot                     | optional                                             | change patch panel port for a certain module slot # (Added in v5.8.2)                                                                                       |
| mac_id                          | optional                                             | mac_id references port_id value. mac_id available via GET at /api/1.0/macs/ or Tools > Export > MAC Address \* Please see note below                        |
| mac_address                     | optional                                             | mac_address or hwaddress                                                                                                                                    |
| switchport_id                   | optional                                             | ID for the switch port. Available via GET at /api/1.0/switchports/ or Tools > Export > Switch Port                                                          |
| switch and switchport           | optional                                             | switch and switch port names - must both be used.                                                                                                           |
| patch_panel_port_id             | optional                                             | Or use front_patch_panel and front_port combination. Available via GET at /api/1.0/patch_panel_ports/<patch_panel_id>/ or Tools > Export > Patch Panel Port |
| front_patch_panel_id            | Optional                                             | Name of the front patch panel - if a patch panel port is connected in front (Added in v5.8.2)                                                               |
| front_patch_panel               | Optional                                             | Name of the front patch panel - if a patch panel port is connected in front (Added in v5.8.2)                                                               |
| front_port                      | Optional                                             | Number of the port on the front patch panel - if a patch panel port is connected in front (Added in v5.8.2)                                                 |
| label                           | optional                                             | label for port                                                                                                                                              |
| obj_label1                      | optional                                             | object label 1                                                                                                                                              |
| obj_label2                      | optional                                             | object label 2                                                                                                                                              |
| back_connection_id              | optional                                             | see notice below ID for the back connection port. Available via GET at /api/1.0/patch_panel_ports/<patch_panel_id>/ or Tools > Export > Patch Panel Port    |
| back_switchport_id              | optional                                             | see notice below. If back connection type is switch.                                                                                                        |
| back_switch and back_switchport | optional                                             | see notice below. If back connection type is switch, use switch and switchport names in combination.                                                        |
| back_patch_panel_id             | Optional, or you can use the name as mentioned below | ID of the back patch panel - if a patch panel port is connected in back (Added in v5.8.2)                                                                   |
| back_patch_panel                | Optional, will work if name is unique                | Name of the back patch panel - if a patch panel port is connected in back (Added in v5.8.2)                                                                 |
| back_port                       | Optional                                             | Number of the port on the back patch panel - if a patch panel port is connected in back (Added in v5.8.2)                                                   |
| clear_front                     | optional                                             | "yes" will clear front connection for port                                                                                                                  |
| clear_back                      | optional                                             | "yes" will clear back connection on port                                                                                                                    |
| cable_type                      | optional                                             | named value of the cable type. Must already exist.                                                                                                          |

<aside class="notice">
As of 12.0, device and device_id have been removed.</br>
As of 12.0 mac_address references "hwaddress" and mac_id references "port_id"
</aside>

# Parts Management

## Get all Parts

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/parts/ --insecure
```

> The above command returns results like this:

```json
{
    "parts": [
        {
            "asset_no": "",
            "assignment": "device",
            "change_perm": "yes",
            "count": 1,
            "custom_fields": [],
            "date_changed": null,
            "description": "",
            "device": "AAA switch 2",
            "device_id": 388,
            "firmware": "",
            "first_added": "2014-02-20T18:13:54.099Z",
            "last_updated": "2014-11-14T22:03:08.717Z",
            "part_id": 1,
            "partmodel": {
                "available": 0,
                "description": "",
                "in_devices": 1,
                "in_rma": 0,
                "in_storage_racks": 0,
                "in_storage_rooms": 0,
                "in_transit": 0,
                "length": "",
                "location": "IT Lab 2nd Floor New Haven, CT",
                "manufacturer": "Kingston Technology Corp.",
                "modelno": "",
                "name": "Kingston 8GB DDR31600",
                "notes": "",
                "partmodel_id": 3,
                "partno": "KVR16E11/8",
                "ramsize": 8192,
                "ramspeed": "1600",
                "ramtype": "DDR3 ",
                "total_count": 1,
                "type": "RAM",
                "used": 1
            },
            "serial_no": "",
            "tags": []
        },
        {
            "asset_no": "",
            "assignment": "device",
            "change_perm": "yes",
            "count": 1,
            "custom_fields": [],
            "date_changed": null,
            "description": "",
            "device": "NHCTWS001",
            "device_id": 136,
            "firmware": "",
            "first_added": "2014-02-20T18:13:54.139Z",
            "last_updated": "2014-03-06T19:42:25.955Z",
            "part_id": 2,
            "partmodel": {
                "available": 3,
                "description": "SATA 6 Gb/s 64MB Cache",
                "hddrpm": "10k",
                "hddsize": 500.0,
                "hddtype": "SATA",
                "in_devices": 2,
                "in_rma": 0,
                "in_storage_racks": 0,
                "in_storage_rooms": 3,
                "in_transit": 0,
                "length": "",
                "location": "IT Lab 2nd Floor New Haven, CT",
                "manufacturer": "Western Digital",
                "modelno": "",
                "name": "WD500GB SATA",
                "notes": "",
                "partmodel_id": 6,
                "partno": "WD5000HHTZ",
                "total_count": 5,
                "type": "Hard Disk",
                "used": 2
            },
            "raid_group": "",
            "raid_type": "Raid 1",
            "serial_no": "74638456047",
            "tags": []
        }
}
```

Get all Parts - introduced in version 5.7.2

**`GET /api/1.0/parts/`**

### Query string parameters (Added in v6.3.0)

You can filter parts by following parameters in the query string

| Parameter         | Description                                                                                                              |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------ |
| type              | type of the partmodel, cpu. mem, hdd for CPU. Memory and Harddisk. For others it must match the type name for part model |
| device            | name of the device, where part is checked out to                                                                         |
| device_id         | id of the device, where part is checked out to                                                                           |
| device_serial     | serial number of the device, where part is checked out to                                                                |
| room              | name of the room, where part is checked out to                                                                           |
| room_id           | id of the room, where part is checked out to                                                                             |
| part_id           | id of the part (added in v6.3.3)                                                                                         |
| partmodel_id      | id of the part model (added in v6.3.3)                                                                                   |
| serial_no         | Serial number of the part (added in v10.0)                                                                               |
| device_serial     | Serial number of the device, where part is checked out to (added in v6.5.0)                                              |
| last_updated_lt   | last updated less than date (added in v7.0.0)                                                                            |
| last_updated_gt   | last updated greater than date (added in v7.0.0)                                                                         |
| rack_id           | id of the rack                                                                                                           |
| rack              | name of the rack                                                                                                         |
| asset_no          | asset number                                                                                                             |
| custom_fields_and | filter by custom fields, and filter, format of key1:value1,key2:value2                                                   |
| custom_fields_or  | filter by custom fields, or filter, format of key1:value1,key2:value2                                                    |
| tags              | filter by tags                                                                                                           |

## Get all Part Models

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/partmodels/ --insecure
```

> The above command returns results like this:

```json
{
  "limit": 2,
  "offset": 0,
  "partmodels": [
    {
      "available": 2,
      "cores": 4,
      "cpuspeed": 3400.0,
      "description": "3.4GHz (3.9GHz Turbo)",
      "in_devices": 0,
      "in_rma": 0,
      "in_storage_racks": 0,
      "in_storage_rooms": 2,
      "in_transit": 0,
      "length": "",
      "location": "IT Lab 2nd Floor New Haven, CT",
      "manufacturer": "Intel Corp.",
      "modelno": "",
      "name": "Core i7-3770",
      "notes":
        "Socket:LGA 1155\r\nSpeedDual-channel DDR3 Memory Controller supports DDR3-1333 and DDR3-1600 memory\r\nIntel HD Graphics 4000\r\nLimited Warranty period (parts): 3 years\r\nLimited Warranty period (labor): 3 years\r\n",
      "partmodel_id": 1,
      "partno": "BX80637I73770",
      "threads": null,
      "total_count": 2,
      "type": "CPU",
      "used": 0
    },
    {
      "available": 0,
      "description": "",
      "in_devices": 1,
      "in_rma": 0,
      "in_storage_racks": 0,
      "in_storage_rooms": 0,
      "in_transit": 0,
      "length": "",
      "location": "IT Lab 2nd Floor New Haven, CT",
      "manufacturer": "Kingston Technology Corp.",
      "modelno": "",
      "name": "Kingston 8GB DDR31600",
      "notes": "",
      "partmodel_id": 3,
      "partno": "KVR16E11/8",
      "ramsize": 8192,
      "ramspeed": "1600",
      "ramtype": "DDR3 ",
      "total_count": 1,
      "type": "RAM",
      "used": 1
    }
  ],
  "total_count": 71
}
```

Get all Part Models - introduced in version 5.7.2

**`GET /api/1.0/partmodels/`**

### Parameters for Part Models

| Parameter | Use      | Description                          |
| --------- | -------- | ------------------------------------ |
| name      | optional | Name of part model - new or existing |

## Create / Update Parts

```shell
curl -X POST -d "type=hard disk&name=WesternDigitalRe250&assignment=room&room=testing-room&description=3.5 inch form factor&firmware=v342&count=3" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/parts/
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "Part added/updated",
    24,
    "Hard Disk WesternDigitalRe250, wd250abyz, PN: wdre250s",
    true,
    true
  ],
  "code": 0
}
```

Create / Update Parts - introduced in version 5.7.2

**`POST /api/1.0/parts/`**

### Parameters for Parts

| Parameter    | Use      | Description                                                                                                                                                        |
| ------------ | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| type         | required | Part type - new or existing                                                                                                                                        |
| name         | optional | Name of part model - new or existing                                                                                                                               |
| count        | optional |
| part_id      | optional | Use for updating existing part                                                                                                                                     |
| partmodel_id | optional |
| serial_no    | optional | Use for updating existing part. Caution, will update first matching serial ir multiple parts with same serial exist. Use part_id/partmodel_id to uniquely identify |
| firmware     | optional |
| assignment   | optional | room, device, rma - required if assigning device                                                                                                                   |
| room         | optional | Room name - required if assigned to room                                                                                                                           |
| device       | optional | Device name - required if assigned to device                                                                                                                       |
| date_changed | optional | Update the Date Changed field, using format YYYY-MM-DD HH:MM:SS                                                                                                    |
| description  | optional |

### Additional Parameters for CPU models

| Parameter | Use      | Description                          |
| --------- | -------- | ------------------------------------ |
| cores     | optional | # of cores                           |
| cpuspeed  | optional | enter in MHZ, e.g.: 3.5 GHZ use 3500 |
| threads   | optional | # of threads                         |

### Additional Parameters for RAM models

| Parameter | Use      | Description                        |
| --------- | -------- | ---------------------------------- |
| ramsize   | optional | enter in MB, e.g.: 8 GB enter 8192 |
| ramtype   | optional | e.g.: DDR3                         |
| ramspeed  | optional | e.g.: 1600                         |

### Additional Parameters for HDD models

| Parameter  | Use      | Description                                         |
| ---------- | -------- | --------------------------------------------------- |
| type       | required | Type has to be hdd or harddisk for these parameters |
| hddsize    | optional | enter in GB, e.g.: 250 GB enter 250                 |
| hddtype    | optional | new or existing                                     |
| hddrpm     | optional | new or existing                                     |
| raid_type  | optional | type of RAID                                        |
| raid_group | optional | RAID group name                                     |

<aside class="notice">
Type has to be hdd or harddisk for HDD Model parameters to work
</aside>

## Create/updated custom fields for parts.

```shell
curl -X PUT -d "value=42&key=The_Answer&id=12" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/custom_fields/part/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "custom key pair values added or updated",
    2,
    "CPU NA, PN: 1000base-lx-sfp",
    true,
    false
  ],
  "code": 0
}
```

Create/update existing custom fields for parts.

**`PUT /api/1.0/custom_fields/part/`**

### URL Parameters

| Parameter          | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| id                 | required                        | ID of part.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| key                | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type               | optional                        | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value              | optional                        |
| notes              | optional                        |
| clear_value        | optional                        | yes to clear existing value for that field (added in v6.1.0)                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| bulk_fields        | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2 (added in v6.4.1)                                                                                                                                                                                                                                                                                                                                                                           |

## Delete Part

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/parts/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the part with the part id supplied as the required argument.

**`DELETE /api/1.0/parts/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | part id     |

## Create / Update Part Models

```shell
curl -X POST  -d "type=riser card&name=intel 123456&partno=123456&location=underthetable" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/partmodels/
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "PartModel added/updated",
    17,
    "Riser Card intel 123456, PN: 123456",
    true,
    true
  ],
  "code": 0
}
```

Create / Update Part Models - introduced in version 5.7.2

**`POST /api/1.0/partmodels/`**

### Parameters for Part Models

| Parameter      | Use      | Description                                  |
| -------------- | -------- | -------------------------------------------- |
| type           | required | New or existing                              |
| name           | optional | Name of part model                           |
| description    | optional |
| partmodel_id   | optional | use for updating existing part model         |
| modelno        | optional | Model # of the part model                    |
| partno         | optional | Part # of the part model                     |
| manufacturer   | optional | New or existing                              |
| media_type     | optional | Type of media                                |
| connector_type | optional | Type of connector, ie rj45                   |
| length         | optional | Cable length                                 |
| connectivity   | optional | New or existing (not used for CPU, RAM, HDD) |
| total_count    | optional |
| location       | optional |
| notes          | optional |

### Additional Parameters for CPU models

| Parameter | Use      | Description                          |
| --------- | -------- | ------------------------------------ |
| cores     | optional | # of cores                           |
| cpuspeed  | optional | enter in MHZ, e.g.: 3.5 GHZ use 3500 |
| threads   | optional | # of threads                         |

### Additional Parameters for RAM models

| Parameter | Use      | Description                        |
| --------- | -------- | ---------------------------------- |
| ramsize   | optional | enter in MB, e.g.: 8 GB enter 8192 |
| ramtype   | optional | e.g.: DDR3                         |
| ramspeed  | optional | e.g.: 1600                         |

### Additional Parameters for HDD models

| Parameter | Use      | Description                         |
| --------- | -------- | ----------------------------------- |
| hddsize   | optional | enter in GB, e.g.: 250 GB enter 250 |
| hddtype   | optional | new or existing                     |
| hddrpm    | optional | new or existing                     |

## Delete Part Model

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/partmodels/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the part model with the part model id supplied as the required argument.

**`DELETE /api/1.0/partmodels/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description   |
| --------- | ------------- |
| id        | part model id |

# Purchasing

## Get All Purchases

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/purchases/ --insecure
```

> The above command returns results like this:

```json
{
  "limit": 1,
  "offset": 0,
  "purchases": [
    {
      "completed": "no",
      "cost": 8500.0,
      "groups": null,
      "custom_fields": [
        {
          "key": "Test Custom Field",
          "notes": "",
          "value": ""
        }
      ],
      "customer": "R&D",
      "line_items": [
        {
          "devices": [
            {
              "asset_no": "0019",
              "device_id": 136,
              "device_url": "/api/1.0/devices/id/136/",
              "name": "NHCTWS001",
              "serial_no": "409357205",
              "type": "physical",
              "uuid": ""
            }
          ],
          "line_completed": "no",
          "line_cost": 1.0,
          "line_cost_center": "5001245",
          "line_customer": "R&D",
          "line_item_type": "Device",
          "line_no": 1,
          "line_notes": "",
          "line_quantity": 1,
          "line_type": "One time Purchase/Tax etc."
        },
        {
          "devices": [
            {
              "asset_no": "0020",
              "device_id": 142,
              "device_url": "/api/1.0/devices/id/142/",
              "name": "NHCTWS002",
              "serial_no": "5038603",
              "type": "physical",
              "uuid": ""
            }
          ],
          "line_completed": "no",
          "line_cost": 1416.0,
          "line_cost_center": "5001245",
          "line_customer": "R&D",
          "line_item_type": "Device",
          "line_no": 2,
          "line_notes": "",
          "line_quantity": 1,
          "line_type": "One time Purchase/Tax etc."
        },
        {
          "devices": [
            {
              "asset_no": "0021",
              "device_id": 143,
              "device_url": "/api/1.0/devices/id/143/",
              "name": "NHCTWS003",
              "serial_no": "5038604",
              "type": "physical",
              "uuid": ""
            }
          ],
          "line_completed": "no",
          "line_cost": 1416.0,
          "line_cost_center": "5001245",
          "line_customer": "R&D",
          "line_item_type": "Device",
          "line_no": 3,
          "line_notes": "",
          "line_quantity": 1,
          "line_type": "One time Purchase/Tax etc."
        },
        {
          "devices": [
            {
              "asset_no": "0022",
              "device_id": 144,
              "device_url": "/api/1.0/devices/id/144/",
              "name": "NHCTWS004",
              "serial_no": "5038605",
              "type": "physical",
              "uuid": ""
            }
          ],
          "line_completed": "no",
          "line_cost": 1416.0,
          "line_cost_center": "5001245",
          "line_customer": "R&D",
          "line_item_type": "Device",
          "line_no": 4,
          "line_notes": "",
          "line_quantity": 1,
          "line_type": "One time Purchase/Tax etc."
        },
        {
          "devices": [
            {
              "asset_no": "0023",
              "device_id": 145,
              "device_url": "/api/1.0/devices/id/145/",
              "name": "NHCTWS005",
              "serial_no": "5038606",
              "type": "physical",
              "uuid": ""
            }
          ],
          "line_completed": "no",
          "line_cost": 1416.0,
          "line_cost_center": "5001245",
          "line_customer": "R&D",
          "line_item_type": "Device",
          "line_no": 5,
          "line_notes": "",
          "line_quantity": 1,
          "line_type": "One time Purchase/Tax etc."
        },
        {
          "devices": [
            {
              "asset_no": "0024",
              "device_id": 146,
              "device_url": "/api/1.0/devices/id/146/",
              "name": "NHCTWS006",
              "serial_no": "5038607",
              "type": "physical",
              "uuid": ""
            }
          ],
          "line_completed": "no",
          "line_cost": 1416.0,
          "line_cost_center": "5001245",
          "line_customer": "R&D",
          "line_item_type": "Device",
          "line_no": 6,
          "line_notes": "",
          "line_quantity": 1,
          "line_type": "One time Purchase/Tax etc."
        }
      ],
      "line_items_one_time_cost": 7081.0,
      "notes": "R&D Workstations",
      "order_no": "R&D8152013HW",
      "po_date": "2013-08-15",
      "purchase_id": 3,
      "vendor": "CDW"
    }
  ],
  "total_count": 11
}
```

GET method retrieves all Purchases.

**`GET /api/1.0/purchases/`**

### Query String Parameters

you can filter purchases by the following parameters in the query string.

| Parameter   | Description            |
| ----------- | ---------------------- |
| purchase_id | Device42 Purchase ID   |
| order_no    | Order number           |
| vendor      | Vendor name            |
| cost_center | Cost Center            |
| building    | Associated Building    |
| completed   | Line Item is completed |

## Create / Update Purchases

```shell
curl -X POST -d "order_no=i856&vendor=device42 llc&line_type=contract&line_start_date=2012-12-21&line_end_date=2013-01-01&line_renew_date=2014-01-01&line_notes=test&line_devices=nh-sample-56,nh-sample-57"  -u 'admin:adm!nd42'  https://yourdevice42ipaddress/api/1.0/purchases/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["purchase updated successfully", 11, "thisisatest"], "code": 0 }
```

**`POST /api/1.0/purchases/`**

**See Notices Below**

### Parameters

| Parameter              | Use      | Description                                                                                                                                                                                                                                 |
| ---------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| order_no               | required | order number / name for the purchase. Can be new or existing.                                                                                                                                                                               |
| purchase_id            | optional | Can be used instead of order_no to update existing purchase. Available via GET at /api/1.0/purchases or Tools > Export > Purchase                                                                                                           |
| line_type              | required | required for any new line being added for both device or contract.                                                                                                                                                                          |
| line_no                | required | required for existing line items, use existing line # to change existing line item.                                                                                                                                                         |
| vendor                 | optional |
| cost                   | optional |
| po_date                | optional |
| cost_center            | optional |
| completed              | optional | yes or no (Added in v5.7.6)                                                                                                                                                                                                                 |
| notes                  | optional | (added in v9.0.2)                                                                                                                                                                                                                           |
| line_name              | optional |
| line_quantity          | optional | can be calculated automatically from # of objects associated                                                                                                                                                                                |
| line_cost              | optional | cost for single object / item.                                                                                                                                                                                                              |
| line_cost_center       | optional |
| line_customer          | optional |
| line_item_type         | optional | device, asset, device_os, rack, shipping, tax, other. Default is device.                                                                                                                                                                    |
| line_asset_ids         | optional | Comma separated asset_id. Only applicable if line_item_type is asset.                                                                                                                                                                       |
| line_start_date        | optional | Date in YYYY-MM-DD format                                                                                                                                                                                                                   |
| line_end_date          | optional | Date in YYYY-MM-DD format                                                                                                                                                                                                                   |
| line_frequency         | optional | Possible values: one time , monthly, quarterly, bi-annually , annually                                                                                                                                                                      |
| line_renew_date        | optional | Date in YYYY-MM-DD format.                                                                                                                                                                                                                  |
| line_cancel_policy     | optional |
| line_contract_type     | optional |
| line_service_type      | optional | new service type will be created, if it doesn't exist (added in v9.0.2)                                                                                                                                                                     |
| line_contract_id       | optional | (added in v9.0.2)                                                                                                                                                                                                                           |
| line_notes             | optional |
| line_completed         | optional | yes or no (Added in v5.7.6)                                                                                                                                                                                                                 |
| line_devices           | optional | see notice below.                                                                                                                                                                                                                           |
| line_device_serial_nos | optional | see notice below.                                                                                                                                                                                                                           |
| line_device_asset_nos  | optional | see notice below.                                                                                                                                                                                                                           |
| line_asset_ids         | optional | ID for associated assets                                                                                                                                                                                                                    |
| line_circuits          | optional | circuit ID name                                                                                                                                                                                                                             |
| line_circuit_ids       | optional | D42 ID for the circuit(s)                                                                                                                                                                                                                   |
| line_building_ids      | optional | D42 ID for the building(s)                                                                                                                                                                                                                  |
| line_certificate_ids   | optional | D42 ID for the certificate(s)                                                                                                                                                                                                               |
| line_software_ids      | optional | D42 ID for the software                                                                                                                                                                                                                     |
| line_room_ids          | optional | D42 ID for the room(s)                                                                                                                                                                                                                      |
| line_rack_ids          | optional | D42 ID for the rack(s)                                                                                                                                                                                                                      |
| line_part_ids          | optional | D42 ID for the part(s)                                                                                                                                                                                                                      |
| line_device_os_ids     | optional | D42 ID for the Device OS                                                                                                                                                                                                                    |
| groups                 | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. |

<aside class="notice">

* Comma separated device names. Only applicable if line_item_type is device. It will create a new device if device with that name doesn’t exist.
* Comma separated serial numbers. Only applicable if line_item_type is device. This will only work if a device with that serial # exists in device42.
* Comma separated asset numbers. Only applicable if line_item_type is device. This will only work if a device with that asset # exists in device42.

</aside>

## Delete Purchase

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/purchases/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the purchase order with the purchase order id supplied as the required argument.

**`DELETE /api/1.0/purchases/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | purchase id |

## Custom Fields

```shell
curl -X PUT -d "value=US&key=region&order_no=PO1234" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/custom_fields/purchase/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "custom key pair values added or updated",
    623,
    "PO1234",
    true,
    false
  ],
  "code": 0
}
```

Create/update custom fields for purchases.

**`PUT /api/1.0/custom_fields/purchase/`** (added in v7.2.0)

### URL Parameters

| Parameter          | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| order_no           | required                        | Order number for the purchase                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| key                | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type               | optional                        | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value              | optional                        |
| notes              | optional                        |
| clear_value        | optional                        | yes to clear existing value for that field (added in v6.1.0)                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| bulk_fields        | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2 (added in v6.4.1)                                                                                                                                                                                                                                                                                                                                                                           |

# Asset/Device Life Cycle

## Get Life Cycle Events

```shell
curl -X GET -d "type=purchased" -u'admin:adm!nd42'https://yourdevice42address/api/1.0/lifecycle_event/
```

> The above command returns results like this:

```json
{
  "lifecycle_events": [
    {
      "date": "2013-09-05T04:00:00Z",
      "asset_id": 224,
      "asset_name": "heart of gold",
      "enduser": "H. Dent",
      "notes": "R&D Dev Server ",
      "type": "Purchased",
      "id": 4
    },
    {
      "date": "2013-09-06T04:00:00Z",
      "device": "NHCTS003",
      "enduser": "J. Pinkman",
      "notes": "Received, applied QR tag, server stored in IT Lab",
      "type": "Inventoried",
      "id": 7
    },
    {
      "date": "2013-09-10T04:00:00Z",
      "device": "NHCTS003",
      "enduser": "B. Kenobi",
      "notes":
        "Installed in New Haven DC, server ready for use. Device record updated. Used the force. ",
      "type": "Installed",
      "id": 8
    }
  ]
}
```

Retrieve Life Cycle Events using filters - introduced in version 5.5.7

**`GET /api/1.0/lifecycle_event/`**

### Query string parameters

| Parameter | Description                              |
| --------- | ---------------------------------------- |
| type      | filter by existing event type            |
| device    | filter by device name                    |
| asset     | filter by asset name                     |
| enduser   | filter by end user name                  |
| date_gt   | filter by date greater than (YYYY-MM-DD) |
| date_lt   | filter by date less than (YYYY-MM-DD)    |

## Create LifeCycle Events

```shell
curl -X PUT -d "asset_no=d42&date=2013-02-02 17:15&type=Installed&user=john smith&notes=test 123"   -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/lifecycle_event/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "lifecycle event added successfully.",
    3,
    "Installed on 2013-02-02 17:15:00"
  ],
  "code": 0
}
```

Use this API to create life cycle events for devices or assets.

**`PUT /api/1.0/lifecycle_event/`**

### Parameters

| Parameter                                                | Use      | Description                                                                                                                      |
| -------------------------------------------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------- |
| date                                                     | required | in YYYY-MM-DD format. or YYYY-MM-DD HH:MM format.                                                                                |
| type                                                     | required | must be defined already in device42.                                                                                             |
| device or device_id or asset_id or serial_no or asset_no | required | For Device could be name, ID, serial # or asset tag # (of the device). For asset, it could be ID (D42 ID for an existing asset). |
| user                                                     | optional | enduser name                                                                                                                     |
| notes                                                    | optional |

# IPAM

## Get all VRF Groups

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vrf_group/ --insecure
```

> The above command returns results like this:

```json
[
  {
    "buildings": [],
    "description": "Infrastructure",
    "groups": "",
    "id": 1,
    "name": "Infrastructure Services"
  },
  {
    "buildings": [],
    "description": "",
    "groups": "",
    "id": 6,
    "name": "OVH"
  },
  {
    "buildings": ["West Haven"],
    "description": "",
    "groups": "",
    "id": 5,
    "name": "West Haven Office"
  }
]
```

GET method retrieves all VRF Groups.

**`GET /api/1.0/vrf_group/`**

## Create/Update VRF Group

```shell
curl -X POST  -d "name=blue&description=blue"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vrf_group/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["VRF Group added/updated", 1, "blue", true, false], "code": 0 }
```

Create/Update VRF Group.

**`POST /api/1.0/vrf_group/`** (Added in v5.8.1)

### Parameters

| Parameter   | Use      | Description                                                                                                                                                                                                                                                                                                     |
| ----------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name        | required | Name of the VRF Group you want to add edit or create                                                                                                                                                                                                                                                            |
| description | optional |
| groups      | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. If this parameter is present with no value, all groups are deleted. |
| buildings   | optional | list of building names for the VRF Group                                                                                                                                                                                                                                                                        |

## Update a Specific VRF Group

```shell
curl -X PUT  -d "description=Research Dept"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vrf_group/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["VRF Group added/updated", 3, "green", true, false], "code": 0 }
```

Update VRF Group

**'PUT /api/1.0/vrf_group/<ID>/'** (Added in v5.8.1)

### Parameters

| Parameter   | Use      | Description                                                                                                                                                                                                                                                                                                     |
| ----------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name        | optional | Name of the VRF Group                                                                                                                                                                                                                                                                                           |
| description | optional |
| groups      | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. If this parameter is present with no value, all groups are deleted. |
| buildings   | optional | list of building names for the VRF Group                                                                                                                                                                                                                                                                        |

## Delete VRF Group

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vrf_group/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the vrf group with the vrf group id supplied as the required argument.

**`DELETE /api/1.0/vrf_group/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | VRF group   |

## Get all Subnet Categories

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/subnet_category/ --insecure
```

> The above command returns results like this:

```json
[
  {
    "description": "",
    "id": 1,
    "groups": "AccessToBlue:yes",
    "name": "East Region"
  },
  {
    "description": null,
    "id": 3,
    "groups": "",
    "name": "North Region"
  },
  {
    "description": "test",
    "id": 2,
    "groups": "ChangeObjects:yes",
    "name": "West Region"
  }
]
```

GET method retrieves all Subnet Categories.

**`GET /api/1.0/subnet_category/`**

## Create Subnet Category.

**`POST /api/1.0/subnet_category/`** (Added in v9.6.0)

### Parameters

| Parameter     | Use      | Description                                                                                                                                                                                                                                                                                                     |
| ------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name          | required | Name of the Subnet Category you want to add edit or create                                                                                                                                                                                                                                                      |
| description   | optional |
| is_it_default | optional | yes or no. Defaults to no. Only one category can be yes. If yes, this is the subnet category to use if none specified in an API or auto-discovery update.                                                                                                                                                       |
| groups        | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. If this parameter is present with no value, all groups are deleted. |

## Update a Specific Subnet Category

```shell
curl -X PUT  -d "description=Eastern Region of US"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/subnet_category/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["Subnet category added/updated", 3, "blue", true, false], "code": 0 }
```

Create Subnet Category.

**`PUT /api/1.0/subnet_category/<ID>`** (Added in v9.6.0)

### Parameters

| Parameter     | Use      | Description                                                                                                                                                                                                                                                                                                     |
| ------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name          | optional | Name of the Subnet Category                                                                                                                                                                                                                                                                                     |
| description   | optional |
| is_it_default | optional | yes or no. Defaults to no. Only one category can be yes. If yes, this is the subnet category to use if none specified in an API or auto-discovery update.                                                                                                                                                       |
| groups        | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. If this parameter is present with no value, all groups are deleted. |

## Delete Subnet Category

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/subnet_category/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the subnet category with the subnet category id supplied as the required argument.

**`DELETE /api/1.0/subnet_category/<ID>/`** (Added in v9.6.0)

### Required Parameter

| Parameter | Description        |
| --------- | ------------------ |
| id        | Subnet category id |

## Get all Subnets

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/subnets/ --insecure
```

> The above command returns results like this:

```json
{
  "limit": 2,
  "offset": 0,
  "subnets": [
    {
      "allocated": "no",
      "allow_broadcast_address": "no",
      "allow_network_address": "no",
      "assigned": "no",
      "can_edit": "yes",
      "category_id": null,
      "category_name": null,
      "custom_fields": [],
      "customer_id": 9,
      "description": "Infrastructure Devices",
      "gateway": null,
      "mask_bits": 24,
      "name": "Infra",
      "network": "10.1.10.0",
      "notes": "",
      "parent_subnet_id": null,
      "parent_vlan_id": null,
      "parent_vlan_name": "",
      "parent_vlan_number": "",
      "range_begin": "10.1.10.5",
      "range_end": "10.1.10.254",
      "service_level": "Production",
      "subnet_id": 4,
      "tags": [],
      "vrf_group_id": 1,
      "vrf_group_name": "Infrastructure Services"
    },
    {
      "allocated": "no",
      "allow_broadcast_address": "no",
      "allow_network_address": "no",
      "assigned": "no",
      "can_edit": "yes",
      "category_id": null,
      "category_name": null,
      "custom_fields": [],
      "customer_id": 9,
      "description": "Network Devices",
      "gateway": "10.1.2.1",
      "mask_bits": 24,
      "name": "NetComm",
      "network": "10.1.2.0",
      "notes": "",
      "parent_subnet_id": 33,
      "parent_vlan_id": null,
      "parent_vlan_name": "",
      "parent_vlan_number": "",
      "range_begin": "10.1.2.1",
      "range_end": "10.1.2.254",
      "service_level": "Production",
      "subnet_id": 5,
      "tags": [],
      "vrf_group_id": 3,
      "vrf_group_name": "Chicago Data Center"
    }
  ],
  "total_count": 32
}
```

GET method retrieves all Subnets.

**`GET /api/1.0/subnets/`**

### Query string parameters (Added in v5.9.3)

You can filter subnets by following parameters in the query string

| Parameter         | Description                                                                              |
| ----------------- | ---------------------------------------------------------------------------------------- |
| name              | name of the subnet                                                                       |
| vrf_group_id      | ID of the VRF group                                                                      |
| vrf_group         | VRF group name                                                                           |
| parent_subnet_id  | ID of the parent subnet                                                                  |
| parent_subnet     | parent subnet name                                                                       |
| customer_id       | ID of the customer (Added in v6.3.0)                                                     |
| customer          | name of the customer, processed if customer_id not present (Added in v6.3.0)             |
| subnet_id         | ID of the subnet (added in v7.2.0)                                                       |
| mask_bits         | mask bits (added in v7.2.0)                                                              |
| mask_bits_lt      | less than mask bits (added in v7.2.0)                                                    |
| mask_bits_gt      | greater than mask bits (added in v7.2.0)                                                 |
| description       | matching description (added in v7.2.0)                                                   |
| range_begin       | Range Begin (added in v7.2.0)                                                            |
| range_end         | Range End (added in v7.2.0)                                                              |
| gateway           | Gateway (added in v7.2.0)                                                                |
| tags              | associated tags (or filter) (added in v7.2.0)                                            |
| tags_and          | associated tags (and filter) (added in v7.2.0)                                           |
| custom_fields_and | filter by custom fields, and filter, format of key1:value1,key2:value2 (added in v7.2.0) |
| custom_fields_or  | filter by custom fields, or filter, format of key1:value1,key2:value2 (added in v7.2.0)  |
| service_level     | filter by name of service level (added in v9.3.0)                                        |
| category          | name of the category                                                                     |
| category_id       | ID of the category                                                                       |
| vlan_id           | ID of the vlan                                                                           |

## Get Subnet by Subnet ID

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/subnets/<subnet_id>/ --insecure
```

> The above command returns the same output as Get All Subnets above

```json
{
  "allocated": "no",
  "allow_broadcast_address": "no",
  "allow_network_address": "no",
  "assigned": "no",
  "can_edit": "yes",
  "category_id": null,
  "category_name": null,
  "custom_fields": [],
  "customer_id": 9,
  "description": "Network Devices",
  "gateway": "10.1.2.1",
  "mask_bits": 24,
  "name": "NetComm",
  "network": "10.1.2.0",
  "notes": "",
  "parent_subnet_id": 33,
  "parent_vlan_id": null,
  "parent_vlan_name": "",
  "parent_vlan_number": "",
  "range_begin": "10.1.2.1",
  "range_end": "10.1.2.254",
  "service_level": "Production",
  "subnet_id": 5,
  "tags": [],
  "vrf_group_id": 3,
  "vrf_group_name": "Chicago Data Center"
}
```

GET method retrieves the subnet with the specified subnet id.

**`GET /api/1.0/subnets/<subnet_id>`**

## Get all Subnets by Customer

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/subnets/customer_id/<customer_id#>/ --insecure
```

> The above command returns results like this:

```json
[
  {
    "mask_bits": 24,
    "name": "NetComm",
    "network": "10.1.2.0",
    "subnet_id": 5
  },
  {
    "mask_bits": 25,
    "name": null,
    "network": "10.1.2.0",
    "subnet_id": 13
  }
]
```

GET method retrieves all Subnets by customer ID #.

**`GET /api/1.0/subnets/customer_id/<customer_id#>/`**

## Create / Update Subnets

```shell
curl -X POST  -d "network=10.1.2.0&mask_bits=24&name=Finance Group PCs&description=restricted access&gateway=10.10.40.1"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/subnets/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg":["subnet successfully added/updated"5"NetComm-10.1.2.0/24(Infrastructure Services)"],"code": 0}
```

Create / Update Subnets.

**`PUT or POST /api/1.0/subnets/`**

**See notice below**

### Parameters

| Parameter                         | Use                                                                                                      | Description                                                                                         |
| --------------------------------- | -------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| network                           | required                                                                                                 | Required for creation, Cannot be modified after subnet creation.                                    |
| mask_bits                         | required                                                                                                 | Cannot be modified after subnet creation.                                                           |
| vrf_group_id                      | optional                                                                                                 |
| name                              | optional                                                                                                 |
| description                       | optional                                                                                                 |
| gateway                           | optional                                                                                                 |
| range_begin                       | optional                                                                                                 |
| range_end                         | optional                                                                                                 |
| parent_vlan_id                    | optional                                                                                                 |
| parent_subnet_id                  | optional                                                                                                 | Change the parent subnet of the subnet. Note: Must be valid parent.                                 |
| customer_id or customer           | optional                                                                                                 | Use customer_id or customer name. Processed either one in that order.                               |
| notes                             | optional                                                                                                 |
| service_level_id or service_level | optional. Use ID or name. Processed either one in that order. Must exist in D42 before.(added in v9.3.0) |
| assigned                          | optional                                                                                                 | 'yes' if assigned. 'no' (default) if unassigned.                                                    |
| allocated                         | optional                                                                                                 | 'yes' if allocated. 'no' (default) if unallocated.                                                  |
| auto_add_ips                      | optional. Applies ONLY to NEW subnets. Existing subnets will ignore this param.                          | If 'yes', addresses within subnet will be automatically added to Device42. (Only available in POST) |
| category                          | optional                                                                                                 | name of the category.                                                                               |
| category_id                       | optional                                                                                                 | ID of the category.                                                                                 |

<aside class="notice">
Best to use vrf_group_id or vrf group name to update existing subnet, otherwise first matching subnet is updated.
</aside>

## Update a Specific Subnet

```shell
curl -X PUT  -d "network=10.1.2.0&mask_bits=24&name=Finance Group PCs&description=restricted access&gateway=10.10.40.1"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/subnets/<subnet_id>/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "subnet successfully added/updated",
    345,
    "Finance Group PCs-3.3.3.0/24",
    true,
    false
  ],
  "code": 0
}
```

Create / Update Subnets.

**`PUT /api/1.0/subnets/<subnet_id>/`**

**See notice below**

### Parameters

<aside class="notice">
See Create/Update Subnets for parameters that can be used with this api call.
</aside>

## Create Child Subnet

```shell
curl -X POST -d "mask_bits=25&parent_subnet_id=5" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/subnets/create_child/  --insecure
```

> The above command returns JSON structured like this:

```json
{subnet_id: 14 mask_bits: "25"network: "10.1.2.128"}
```

Create child Subnet.

**`POST /api/1.0/subnets/create_child/`**

### Parameters

| Parameter                 | Use                                      | Description                                                                                                            |
| ------------------------- | ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| mask_bits                 | required                                 | e.g. 24                                                                                                                |
| parent_subnet_id          | required, if vrf_group not present       | ID of the parent subnet. Can be obtained via /api/1.0/subnets/ or Tools > Export > Subnet                              |
| vrf_group or vrf_group_id | required if parent_subnet_id not present | ID or name of the vrf group (added in v9.0.0)                                                                          |
| parent_mask_bits          | optional                                 | only if searching within a VRF and you want to restrict to certain parents with particular mask bits (added in v9.0.0) |
| ipv6                      | required for ipv6                        | if creating an ipv6 subnet, &ipv6 will need to be passed,                                                              |

<aside class="notice">
With this API call, you can create child subnets using just the mask bits and parent subnet ID as discussed below. The parent subnet must belong to a VRF group.
</aside>

## Delete Subnet

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/subnets/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the subnet with the subnet id supplied as the required argument.

**`DELETE /api/1.0/subnets/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | subnet id   |

## Subnet Custom Fields

```shell
curl -X PUT -d "value=APAC&key=region&network=9.0.0.0&mask_bits=8&vrf_group=green" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/custom_fields/subnet/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "custom key pair values added or updated",
    2681,
    "9.0.0.0/8",
    true,
    false
  ],
  "code": 0
}
```

Create/update custom fields for subnets.

**`PUT /api/1.0/custom_fields/subnet/`** (added in v7.2.0)

### URL Parameters

| Parameter                       | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| network,mask_bits and vrf_group | required                        | Combination of network, mask bits and vrf group name required.                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| key                             | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type                            | optional                        | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name              | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value                           | optional                        |
| notes                           | optional                        |
| clear_value                     | optional                        | yes to clear existing value for that field                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| bulk_fields                     | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2                                                                                                                                                                                                                                                                                                                                                                                             |

## Get all VLANs

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vlans/ --insecure
```

> The above command returns results like this:

```json
{
    "vlans": [
        {
            "description": "342 TEST VLAN",
            "name": "342",
            "notes": "TESTTEST",
            "number": 342,
            "switches": [],
            "tags": [],
            "vlan_id": 20
        },
        {
            "description": null,
            "name": "CRMConsultant",
            "notes": null,
            "number": 20,
            "switches": [
                {
                    "asset_no": null,
                    "device_id": 1,
                    "device_url": "/api/1.0/devices/id/1/",
                    "name": "nh-lab-switch-01",
                    "serial_no": "FAB0447Y35M",
                    "uuid": null
                }
            ],
            "tags": [],
            "vlan_id": 13
        },
        {
            "description": null,
            "name": "default",
            "notes": null,
            "number": 1,
            "switches": [
                {
                    "asset_no": null,
                    "device_id": 1,
                    "device_url": "/api/1.0/devices/id/1/",
                    "name": "nh-lab-switch-01",
                    "serial_no": "FAB0447Y35M",
                    "uuid": null
                }
            ],
            "tags": [],
            "vlan_id": 14
        },
        {
            "description": null,
            "name": "fddi-default",
            "notes": null,
            "number": 1002,
            "switches": [
                {
                    "asset_no": null,
                    "device_id": 1,
                    "device_url": "/api/1.0/devices/id/1/",
                    "name": "nh-lab-switch-01",
                    "serial_no": "FAB0447Y35M",
                    "uuid": null
                }
            ],
            "tags": [],
            "vlan_id": 18
        },
        {
            "description": null,
            "name": "fddinet-default",
            "notes": null,
            "number": 1004,
            "switches": [
                {
                    "asset_no": null,
                    "device_id": 1,
                    "device_url": "/api/1.0/devices/id/1/",
                    "name": "nh-lab-switch-01",
                    "serial_no": "FAB0447Y35M",
                    "uuid": null
                }
            ],
            "tags": [],
            "vlan_id": 17
        },
        {
            "description": null,
            "name": "Native-VM-Nexus",
            "notes": null,
            "number": 12,
            "switches": [
                {
                    "asset_no": null,
                    "device_id": 1,
                    "device_url": "/api/1.0/devices/id/1/",
                    "name": "nh-lab-switch-01",
                    "serial_no": "FAB0447Y35M",
                    "uuid": null
                }
            ],
            "tags": [],
            "vlan_id": 19
        },
        {
            "description": "New Haven, CT LAN",7
            "name": "NHCTVLAN42",
            "notes": "",
            "number": 42,
            "switches": [
                {
                    "asset_no": "0052",
                    "device_id": 105,
                    "device_url": "/api/1.0/devices/id/105/",
                    "name": "NHCTCORE01",
                    "serial_no": "",
                    "uuid": ""
                },
                {
                    "asset_no": "0053",
                    "device_id": 106,
                    "device_url": "/api/1.0/devices/id/106/",
                    "name": "NHCTCORE02",
                    "serial_no": "",
                    "uuid": ""
                },
                {
                    "asset_no": "0054",
                    "device_id": 111,
                    "device_url": "/api/1.0/devices/id/111/",
                    "name": "NHCTIDFS01",
                    "serial_no": "",
                    "uuid": ""
                },
                {
                    "asset_no": "0055",
                    "device_id": 112,
                    "device_url": "/api/1.0/devices/id/112/",
                    "name": "NHCTIDFS02",
                    "serial_no": "",
                    "uuid": ""
                },
                {
                    "asset_no": "0056",
                    "device_id": 113,
                    "device_url": "/api/1.0/devices/id/113/",
                    "name": "NHCTIDFS03",
                    "serial_no": "",
                    "uuid": ""
                },
                {
                    "asset_no": "10125",
                    "device_id": 134,
                    "device_url": "/api/1.0/devices/id/134/",
                    "name": "USNHCTIDS01",
                    "serial_no": "2947502-48",
                    "uuid": ""
                }
            ],
            "tags": [],
            "vlan_id": 11
        },
        {
            "description": null,
            "name": "token-ring-default",
            "notes": null,
            "number": 1003,
            "switches": [
                {
                    "asset_no": null,
                    "device_id": 1,
                    "device_url": "/api/1.0/devices/id/1/",
                    "name": "nh-lab-switch-01",
                    "serial_no": "FAB0447Y35M",
                    "uuid": null
                }
            ],
            "tags": [],
            "vlan_id": 16
        },
        {
            "description": null,
            "name": "trnet-default",
            "notes": null,
            "number": 1005,
            "switches": [
                {
                    "asset_no": null,
                    "device_id": 1,
                    "device_url": "/api/1.0/devices/id/1/",
                    "name": "nh-lab-switch-01",
                    "serial_no": "FAB0447Y35M",
                    "uuid": null
                }
            ],
            "tags": [],
            "vlan_id": 15
        },
        {
            "description": null,
            "name": "VLAN0010",
            "notes": null,
            "number": 10,
            "switches": [
                {
                    "asset_no": null,
                    "device_id": 1,
                    "device_url": "/api/1.0/devices/id/1/",
                    "name": "nh-lab-switch-01",
                    "serial_no": "FAB0447Y35M",
                    "uuid": null
                }
            ],
            "tags": [],
            "vlan_id": 12
        }
    ]
}
```

GET method retrieves all VLANs.

**`GET /api/1.0/vlans/`**

### Query string parameters

| Parameter | Description                      |
| --------- | -------------------------------- |
| vlan_id   | Device42 ID of vlan              |
| number    | vlan number                      |
| tags      | Comma separated tags, OR filter  |
| tags_and  | Comma separated tags, AND filter |

## Get VLAN by ID

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vlans/<id>/ --insecure
```

> The above command returns results like this:

```json
{
  "description": "",
  "name": "VLAN0010",
  "notes": "",
  "number": 10,
  "switches": [
    {
      "asset_no": "",
      "device_id": 1,
      "device_url": "/api/1.0/devices/id/1/",
      "name": "nh-lab-switch-01",
      "serial_no": "FAB0447Y35M",
      "type": "physical",
      "uuid": ""
    }
  ],
  "vlan_id": 12
}
```

GET method retrieves VLAN by specific VLAN ID.

**`GET /api/1.0/vlans/<id>/`**

## Create VLANS

```shell
curl -X POST  -d "number=10&name=VLAN10&description=Guests"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vlans/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["vlan successfully added", 22, "test", true, true], "code": 0 }
```

Create VLANS.

**`POST /api/1.0/vlans/`**

### Parameters

| Parameter             | Use                                                              | Description                                                                                      |
| --------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| number                | required                                                         | VLAN #                                                                                           |
| name                  | optional                                                         | If left blank, name will be created as VLANxxxx, e.g.: Number VLAN# 342 will get named VLAN0342. |
| description           | optional                                                         |
| switch_id or switches | optional. Comma separated values for switch_ids or switch names. |

notes : optional

## Update VLANS

```shell
curl -X PUT  -d "number=342&description=342 Test VLAN&notes=TESTTEST"-u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vlans/20/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg":["vlan successfully updated"20"342"],"code": 0}
```

Update VLANS by ID in url.

**`PUT /api/1.0/vlans/<id>/`**

### Parameters

| Parameter             | Use                                                              | Description |
| --------------------- | ---------------------------------------------------------------- | ----------- |
| number                | optional                                                         | VLAN #      |
| name                  | optional                                                         |
| description           | optional                                                         |
| switch_id or switches | optional. Comma separated values for switch_ids or switch names. |
| notes                 | optional                                                         |

Update VLANS by id in parameters.

**`PUT /api/1.0/vlans/`**

### Parameters

| Parameter             | Use                                                              | Description                                      |
| --------------------- | ---------------------------------------------------------------- | ------------------------------------------------ |
| id                    | required                                                         | ID of the VLAN (This is Device42 ID of the VLAN) |
| number                | optional                                                         | VLAN #                                           |
| name                  | optional                                                         |
| description           | optional                                                         |
| switch_id or switches | optional. Comma separated values for switch_ids or switch names. |
| notes                 | optional                                                         |

## Smart Merge VLANS

```shell
curl -X POST -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vlans/smart_merge_all/
```

> The above command returns JSON structured like this:

```json
{ "msg": ["all vlans merged successfully", "", "", false, false], "code": 0 }
```

Update VLANS by ID in url.

**`POST /api/1.0/vlans/smart_merge_all/`**

### Parameters

| Parameter             | Use                                                              | Description |
| --------------------- | ---------------------------------------------------------------- | ----------- |
| number                | optional                                                         | VLAN #      |
| name                  | optional                                                         |
| description           | optional                                                         |
| switch_id or switches | optional. Comma separated values for switch_ids or switch names. |
| notes                 | optional                                                         |

Update VLANS by id in parameters.

## Delete VLAN

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vlan/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the vlan with the vlan id supplied as the required argument.

**`DELETE /api/1.0/vlans/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description                               |
| --------- | ----------------------------------------- |
| id        | vlan id (This is Device42 ID of the VLAN) |

## Get all IPs

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/ips/ --insecure
```

> The above command returns results like this:

```json
{
  "ips": [
    {
      "available": "no",
      "custom_fields": [
        {
          "key": "test",
          "notes": "",
          "value": ""
        }
      ],
      "device": "USNHCTVH001",
      "device_id": 116,
      "id": 38,
      "ip": "10.1.10.5",
      "label": "",
      "last_updated": "2015-11-12T17:31:57.985Z",
      "mac_address": null,
      "mac_id": null,
      "notes": "",
      "subnet": "Infra-10.1.10.0/24(Infrastructure Services)",
      "subnet_id": 4,
      "type": "Reserved"
    },
    {
      "available": "yes",
      "custom_fields": [
        {
          "key": "test",
          "notes": "",
          "value": ""
        }
      ],
      "device": null,
      "device_id": null,
      "id": 39,
      "ip": "10.1.10.6",
      "label": "",
      "last_updated": "2016-04-08T01:34:27.592Z",
      "mac_address": null,
      "mac_id": null,
      "notes": "",
      "subnet": "Infra-10.1.10.0/24(Infrastructure Services)",
      "subnet_id": 4,
      "type": null
    },
    {
      "available": "no",
      "custom_fields": [
        {
          "key": "test",
          "notes": "",
          "value": ""
        }
      ],
      "device": "NHCTS003",
      "device_id": 120,
      "id": 40,
      "ip": "10.1.10.7",
      "label": "",
      "last_updated": "2013-09-06T18:56:29.515Z",
      "mac_address": null,
      "mac_id": null,
      "notes": "",
      "subnet": "Infra-10.1.10.0/24(Infrastructure Services)",
      "subnet_id": 4,
      "type": "Static"
    },
    {
      "available": "no",
      "custom_fields": [
        {
          "key": "test",
          "notes": "",
          "value": ""
        }
      ],
      "device": "NHCTS003",
      "device_id": 120,
      "id": 41,
      "ip": "10.1.10.8",
      "label": "",
      "last_updated": "2013-09-06T15:47:47.662Z",
      "mac_address": null,
      "mac_id": null,
      "notes": "",
      "subnet": "Infra-10.1.10.0/24(Infrastructure Services)",
      "subnet_id": 4,
      "type": null
    }
  ],
  "limit": 4,
  "offset": 0,
  "total_count": 1004
}
```

GET method retrieves all IPs. (Added in v5.9.3)
By default the limit is maximum 1000 IPs per call. Use total_count and offset if over 1000 IPs.

**`GET /api/1.0/ips/`**

### Query string parameters

You can filter IPs by following parameters in the query string

| Parameter         | Description                                                                              |
| ----------------- | ---------------------------------------------------------------------------------------- |
| offset            | what offset the IPs should start by                                                      |
| limit             | limit to this much IPs. Max. value is 1000 and 1000 is default as well.                  |
| subnet_id         | ID of the subnet                                                                         |
| subnet            | name of the subnet                                                                       |
| device            | Device name                                                                              |
| mac               | mac address                                                                              |
| available         | could be yes or no                                                                       |
| type              | could be static, dhcp or reserved                                                        |
| last_updated_lt   | last updated less than date                                                              |
| last_updated_gt   | last updated greater than date                                                           |
| ip                | ip address (added in v6.3.0)                                                             |
| first_added_lt    | first added less than date (added in v7.2.0)                                             |
| first_added_gt    | first added greater than date (added in v7.2.0)                                          |
| ip_id             | D42 ID of the IP                                                                         |
| label             | label on the IP (added in v7.2.0)                                                        |
| tags              | associated tags (or filter) (added in v7.2.0)                                            |
| tags_and          | associated tags (and filter) (added in v7.2.0)                                           |
| custom_fields_and | filter by custom fields, and filter, format of key1:value1,key2:value2 (added in v7.2.0) |
| custom_fields_or  | filter by custom fields, or filter, format of key1:value1,key2:value2 (added in v7.2.0)  |

### Values returned

| Value       | Description                                                                |
| ----------- | -------------------------------------------------------------------------- |
| total_count | Count of IPs returned (use with offset as max results are limited to 1000) |
| offset      | Offset used                                                                |
| limit       | Current limit                                                              |
| ips         | Details for all the IPs                                                    |

## Get all IPs in a Subnet

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/ips/subnet_id/<subnet_id#>/ --insecure
```

> The above command returns results like this:

```json
{
  "ips": [
    {
      "available": "No",
      "device": "USNHCTVH001",
      "ip": "10.1.10.5",
      "label": "",
      "type": "Reserved"
    },
    {
      "available": "Yes",
      "ip": "10.1.10.6",
      "label": ""
    },
    {
      "available": "No",
      "device": "NHCTS003",
      "ip": "10.1.10.7",
      "label": "",
      "type": "Static"
    },
    {
      "available": "No",
      "device": "NHCTS003",
      "ip": "10.1.10.8",
      "label": ""
    },
    {
      "available": "No",
      "device": "NHCTCORE01",
      "ip": "10.1.10.9",
      "label": ""
    },
    {
      "available": "No",
      "device": "NHCTCORE02",
      "ip": "10.1.10.10",
      "label": ""
    },
    {
      "available": "No",
      "device": "NHCTWS001",
      "ip": "10.1.10.11",
      "label": "",
      "mac_address": "00:14:bf:aa:46:18",
      "type": "Static"
    }
  ]
}
```

GET method retrieves all IPs by Subnet ID.

**`GET /api/1.0/ips/subnet_id/<subnet_id#>/`**

## Find Specific IP Address

```shell
curl -X POST \
    -d "query=ip" \
    -d "string=192.168.1.1" \
    -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/search/ --insecure

or

curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/search/?'query=ip&string=10.10.10.10' --insecure
```

> The above command returns JSON structured like this:

```json
{
    "ips": [
        {
            "available": "No",
            "device": "openstack-dev2",
            "ip": "10.0.0.3",
            "label": "br100",
            "mac_address": "00:30:48:63:41:b6"
        }
    ]
}

or
"no matching IP found"
```

Find a specific IP Address.

**`POST /api/1.0/search/`**

**`GET /api/1.0/search/`**(GET call added in v5.8.1)

**See notice below**

### Parameters

| Parameter | Use      | Description                  |
| --------- | -------- | ---------------------------- |
| query     | required | enter "ip"                   |
| string    | required | the IP address to search for |

<aside class="notice">
Depending on the string, you will either get “no matching IP found” or “ips” list with dictionary of all the matching IPs with device name, mac address, label and availability.
</aside>

## Suggest next available Subnet

```shell
curl -X GET -u 'user:samsonite' 'https://d42/api/1.0/suggest_subnet/32/?mask_bits=24'
```

> The above command returns JSON structured like this:

```json
{ "ip": "10.16.0.0", "mask": 24 }
```

Suggest next available subnet.

**`GET /api/1.0/subnet/ID/`**

### Parameters

| Parameter | Use      | Description                                                       |
| --------- | -------- | ----------------------------------------------------------------- |
| ID        | required | ID of the parent subnet                                           |
| mask_bits | required | mask bits of new subnet. Can't be further than 8 bits from parent |

## Suggest next available IP Address

```shell
curl -i -H "Accept: text/plain" -X POST \
    -d "subnet=192.168.1.0/24" \
    -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/suggest_ip/ --insecure

or

curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/suggest_ip/?subnet=192.168.1.0/24 --insecure
```

> The above command returns JSON structured like this:

```json
{ "ip": "192.168.1.4" }
```

Suggest next available IP Address. Can be IPv4 or IPv6.

**`POST /api/1.0/suggest_ip/`**

**`GET /api/1.0/suggest_ip/`**(GET call added in v5.8.1)

**See notice below**

### Parameters

| Parameter    | Use      | Description                                                                                                              |
| ------------ | -------- | ------------------------------------------------------------------------------------------------------------------------ |
| subnet_id    | optional | ID of the subnet to suggest the next available IP in                                                                     |
| subnet       | optional | enter in network/mask notation, e.g.: 192.168.1.0/24                                                                     |
| name         | optional | name of the existing subnet. Must be unique name (with combination of VRF group, if supplied).                           |
| vrf_group_id | optional | ID of the VRF group - useful if you have overlapping subnets in your Device42 instance                                   |
| vrf_group    | optional | name of the VRF group - useful if you have overlapping subnets in your Device42 instance                                 |
| reserve_ip   | optional | If value of yes is passed, the suggested IP is reserved. Return value also adds reserved as yes or no. (added in v7.2.0) |
| number       | optional | when specified with reserve_ip=yes, number=NN will give the next NN available addresses                                  |

<aside class="notice">
subnet_id, subnet or name is required. This API will take subnet_id,subnet in form of network/mask bits, or name of the subnet and optionally VRF group name or ID and output what IP can be used next. It fill find the first available IP. If no IP is found marked available in Device42, it will calculate the next IP that can be used in that subnet. This doesn’t make any change to Device42, it merely suggests that IP. You can then do a post /api/1.0/ips call to save/reserve that IP.
</aside>

<aside class="notice">
As of 13.4.5, if there is a race condition for suggest IP one of the threads will succeed and others will fail with status 500 and a message similar to following:

{"msg": "Reserved IP address no longer available 9.9.8.34", "code": 1}

Please be sure to have automation scripts look for 500 errors and re-try as part of any automation processes

</aside>

## Create / Update IP Addresses

```shell
curl -X POST -d "ipaddress=192.168.1.1&tag=testtest" -u 'admin:adm!nd42' https://yourdevice42address/api/ip/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg":["ip added or updated"76"192.168.1.1"true,false],"code": 0}
```

Create / Update IP Addresses.

**`POST /api/1.0/ips/`** (Added in v5.7.3)

**`POST /api/ip/`**

**See notice below**

### Parameters

| Parameter    | Use      | Description                                                                                                                                                                                                                                                                                                                               |
| ------------ | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ipaddress    | required | If a matching IP address is found, it will update the first matched IP address(unless you specify a vrf_group or vrf_group_id, then it matches or adds IP to that VRF group)                                                                                                                                                              |
| label        | optional | label for the interface (tag still works for backward compatibility, label added in v7.2.0)                                                                                                                                                                                                                                               |
| subnet       | optional | name of the subnet you want to add the IP to. Must be unique. The reason it must be unique is to handle overlapping subnet ranges. The unique name enable selection of the correct subnet. Ignored if vrf_group_id or vrf_group is present in the arguments. Works only when adding a new IP. For existing IPs, use VRF group parameters. |
| macaddress   | optional | MAC address – can be new or existing                                                                                                                                                                                                                                                                                                      |
| device       | optional | device name, can be new or existing device                                                                                                                                                                                                                                                                                                |
| type         | optional | Could be static, dhcp or reserved                                                                                                                                                                                                                                                                                                         |
| notes        | optional |
| vrf_group_id | optional | Added in v5.1.2. ID of the VRF group you want this IP to be associated with.                                                                                                                                                                                                                                                              |
| vrf_group    | optional | Name of the VRF group you want this IP to be associated with. Processed only if vrf_group_id is not present in the arguments.                                                                                                                                                                                                             |
| available    | optional | If yes - then IP is marked as available and device and mac address associations are cleared. Added in v5.7.2                                                                                                                                                                                                                              |
| clear_all    | optional | If yes - then IP is marked as available and device and mac address associations are cleared. Also notes and label fields are cleared. Added in v5.7.2                                                                                                                                                                                     |
| tags         | optional | Update IP address tags (note, different than the antiquated tag endpoint. See label parameter above)                                                                                                                                                                                                                                      |

<aside class="notice">
If the IP address exists, this API call will update the record. Otherwise, it will create the record.
</aside>

## Delete IP Address

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/ips/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete an IP Address with the IP Address id supplied as the required argument.

**`DELETE /api/1.0/ips/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description   |
| --------- | ------------- |
| id        | IP Address id |

## IP Custom Fields

```shell
curl -X PUT -d "value=APAC&key=region&ip_address=9.0.0.1&subnet_id=2681" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/custom_fields/ip_address/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "custom key pair values added or updated",
    13545,
    "9.0.0.1",
    true,
    false
  ],
  "code": 0
}
```

Create/update custom fields for ip address.

**`PUT /api/1.0/custom_fields/ip_address/`** (added in v7.2.0)

### URL Parameters

| Parameter                | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| ip_address and subnet_id | required                        | Combination of IP address and subnet ID.                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| key                      | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type                     | optional                        | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name       | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value                    | optional                        |
| notes                    | optional                        |
| clear_value              | optional                        | yes to clear existing value for that field                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| bulk_fields              | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2                                                                                                                                                                                                                                                                                                                                                                                             |

## Get all MAC Addresses

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/macs/ --insecure
```

> The above command returns results like this:

```json
{
  "limit": 3,
  "macaddresses": [
    {
      "device": {
        "asset_no": "",
        "device_id": 11,
        "device_url": "/api/1.0/devices/id/11/",
        "name": "DeviceTOM4",
        "serial_no": "",
        "type": "physical",
        "uuid": ""
      },
      "macaddress": "00:0a:cd:1f:c9:2f",
      "macaddress_id": 24,
      "port_name": "vmnic0"
    },
    {
      "device": {
        "asset_no": "",
        "device_id": 104,
        "device_url": "/api/1.0/devices/id/104/",
        "name": "xbox360",
        "serial_no": "R9KL913",
        "type": "other",
        "uuid": "54FBFD81-50BE-11CB-B693-F8E74796DC19"
      },
      "macaddress": "74:e5:0b:4e:1e:3c",
      "macaddress_id": 26,
      "port": {
        "description": "FastEthernet0/1",
        "devices": "xbox360",
        "is_connected": "yes",
        "mac_ids": "26; 48; 47; 38; 35",
        "macs":
          "74:e5:0b:4e:1e:3c; c4:85:08:b0:54:e6; 48:f8:b3:5b:71:02; 4c:80:93:23:18:dd; 00:13:49:23:c5:bb",
        "normalized_port": "FastEthernet0000/0001",
        "obj_label": "",
        "port": "FastEthernet0/1",
        "switch": {
          "asset_no": "",
          "device_id": 1,
          "device_url": "/api/1.0/devices/id/1/",
          "name": "nh-lab-switch-01",
          "serial_no": "FAB0447Y35M",
          "type": "physical",
          "uuid": ""
        },
        "switchport_id": 8,
        "tags": [],
        "type": null,
        "vlan_ids": "14",
        "vlans": "default"
      },
      "port_name": "",
      "vlan_id": 14
    },
    {
      "device": {
        "asset_no": "",
        "device_id": 195,
        "device_url": "/api/1.0/devices/id/195/",
        "name": "nh-esxi-02",
        "serial_no": "2ZKJML1",
        "type": "physical",
        "uuid": ""
      },
      "macaddress": "00:21:9b:99:69:ef",
      "macaddress_id": 30,
      "port": {
        "description": "FastEthernet0/15",
        "devices": "nh-esxi-02",
        "is_connected": "yes",
        "mac_ids": "59; 30; 57; 56; 55; 54",
        "macs":
          "00:0c:29:c6:ef:89; 00:21:9b:99:69:ef; 00:0c:29:db:31:81; 00:0c:29:19:1f:98; 00:0c:29:2c:75:76; 00:0c:29:5f:1d:5f",
        "normalized_port": "FastEthernet0000/0015",
        "obj_label": "vmnic0",
        "port": "FastEthernet0/15",
        "switch": {
          "asset_no": "",
          "device_id": 1,
          "device_url": "/api/1.0/devices/id/1/",
          "name": "nh-lab-switch-01",
          "serial_no": "FAB0447Y35M",
          "type": "physical",
          "uuid": ""
        },
        "switchport_id": 14,
        "tags": [],
        "type": null,
        "vlan_ids": "14",
        "vlans": "default"
      },
      "port_name": "vmnic0",
      "vlan_id": 14
    }
  ],
  "offset": 0,
  "total_count": 159
}
```

GET method retrieves all MAC Addresses.

**`GET /api/1.0/macs/`**

### Query string parameters (Added in v5.9.2)

| Parameter       | Description                                      |
| --------------- | ------------------------------------------------ |
| mac             | filter by mac address                            |
| device          | Device name (added in v7.0.0)                    |
| device_id       | Device ID                                        |
| last_updated_lt | last updated less than date (added in v7.0.0)    |
| last_updated_gt | last updated greater than date (added in v7.0.0) |

## Get MAC Address with ID

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/macs/<id>/ --insecure
```

> The above command returns results like this:

```json
{
  "device": {
    "asset_no": "",
    "device_id": 365,
    "device_url": "/api/1.0/devices/id/365/",
    "name": "nh-win2k3r2-vm-",
    "serial_no": "6417-7755-9741-3863-0032-1151-91",
    "type": "virtual",
    "uuid": "8ED6DBA8-2FC0-DF4B-B35B-12999E9AA851"
  },
  "macaddress": "00:15:5d:0b:9e:02",
  "macaddress_id": 61,
  "port": {
    "description": "FastEthernet0/21",
    "devices": "nh-hyperv-02",
    "is_connected": "yes",
    "mac_ids": "61; 60",
    "macs": "00:15:5d:0b:9e:02; 00:1c:c4:44:d1:32",
    "normalized_port": "FastEthernet0000/0021",
    "obj_label": "Hyper-V Virtual Ethernet Adapte",
    "port": "FastEthernet0/21",
    "switch": {
      "asset_no": "",
      "device_id": 1,
      "device_url": "/api/1.0/devices/id/1/",
      "name": "nh-lab-switch-01",
      "serial_no": "FAB0447Y35M",
      "type": "physical",
      "uuid": ""
    },
    "switchport_id": 10,
    "tags": [],
    "type": null,
    "vlan_ids": "14",
    "vlans": "default"
  },
  "port_name": "Microsoft Virtual Machine Bus N",
  "vlan_id": 14
}
```

GET method retrieves MAC address by specific MAC ID.

**`GET /api/1.0/macs/<id>/`**

<aside class="notice">
/macs/ endpoint has been deprecated since v12.0. Please use /switchports/ instead for enhanced information.
</aside>

## Create / Update MAC Addresses

```shell
curl -X POST -d "macaddress=00:14:bf:aa:46:18&port_name=Test" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/macs/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg": ["mac address successfully added/updated"41"00:14:bf:aa:46:18"true,false],"code": 0}
```

Create / Update MAC Addresses.

**`POST /api/1.0/macs/`**

**See notice below**

### Parameters

| Parameter                  | Use                                                                                                                                                                                           | Description                                                                                                                                     |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| macaddress                 | required                                                                                                                                                                                      |
| port_name                  | optional                                                                                                                                                                                      | Interface name. (Please note: This is NOT the switchport name.)                                                                                 |
| override                   | optional                                                                                                                                                                                      | Value can be smart, no, or yes. See notice below.                                                                                               |
| vlan_id                    | optional                                                                                                                                                                                      | [GET VLAN ID's](#get-all-vlans) or UI Tools > Export > VLAN                                                                                     |
| device                     | optional                                                                                                                                                                                      | Name of the device.                                                                                                                             |
| port_id or port and switch | Get the port id with [GET Switch Port ID](#get-all-switch-ports). To use the port and switch option, in the UI do IPAM > MAC Addresses (select the mac) > Actions > Do a reimportable export. | For the port and switch option, port refers to the switchport name (not the interface name) and switch refers to the device name of the switch. |

<aside class="notice">
**smart** – will detect if the port_name passed exist or not, if not – it is added to the current port name. Helpful, if you want to track all the port names for that mac address (e.g. eth0 & bond0).
**yes** – change the port name. This is default behavior even if you don’t pass this parameter
**no** – will not change the port name
</aside>

<aside class="notice">
/macs/ endpoint has been deprecated since v12.0. Please use /switchports/ instead for enhanced information.
</aside>

## Delete Mac Address

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/macs/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the mac address with the mac address id supplied as the required argument.

**`DELETE /api/1.0/macs/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description    |
| --------- | -------------- |
| id        | Mac address id |

<aside class="notice">
/macs/ endpoint has been deprecated since v12.0. Please use /switchports/ instead for enhanced information.
</aside>

## Get all Switch Ports

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/switchports/ --insecure
```

> The above command returns results like this:

```json
{
  "limit": 3,
  "offset": 0,
  "switchports": [
    {
      "description": "FastEthernet0/3",
      "devices": "tuan-pc",
      "is_connected": "yes",
      "mac_ids": "126",
      "macs": "ec:55:f9:fa:44:48",
      "normalized_port": "FastEthernet0000/0003",
      "obj_label": "Bluetooth Device (Personal Area",
      "port": "FastEthernet0/3",
      "switch": {
        "asset_no": "",
        "device_id": 1,
        "device_url": "/api/1.0/devices/id/1/",
        "name": "nh-lab-switch-01",
        "serial_no": "FAB0447Y35M",
        "type": "physical",
        "uuid": ""
      },
      "switchport_id": 1,
      "tags": [],
      "type": null,
      "vlan_ids": "14",
      "vlans": "default"
    },
    {
      "description": "FastEthernet0/6",
      "devices": "NHCTWS001",
      "is_connected": "yes",
      "mac_ids": "",
      "macs": "",
      "normalized_port": "FastEthernet0000/0006",
      "obj_label": "",
      "port": "FastEthernet0/6",
      "switch": {
        "asset_no": "",
        "device_id": 1,
        "device_url": "/api/1.0/devices/id/1/",
        "name": "nh-lab-switch-01",
        "serial_no": "FAB0447Y35M",
        "type": "physical",
        "uuid": ""
      },
      "switchport_id": 2,
      "tags": [],
      "type": null,
      "vlan_ids": "14",
      "vlans": "default"
    },
    {
      "description": "FastEthernet0/7",
      "devices": "",
      "is_connected": "no",
      "mac_ids": "",
      "macs": "",
      "normalized_port": "FastEthernet0000/0007",
      "obj_label": "",
      "port": "FastEthernet0/7",
      "switch": {
        "asset_no": "",
        "device_id": 1,
        "device_url": "/api/1.0/devices/id/1/",
        "name": "nh-lab-switch-01",
        "serial_no": "FAB0447Y35M",
        "type": "physical",
        "uuid": ""
      },
      "switchport_id": 3,
      "tags": [],
      "type": null,
      "vlan_ids": "14",
      "vlans": "default"
    }
  ],
  "total_count": 349
}
```

GET method retrieves all Switch Ports.

**`GET /api/1.0/switchports/`**

### Query string parameters

You can filter Switchports by following parameters in the query string

| Parameter       | Description                                                                                       |
| --------------- | ------------------------------------------------------------------------------------------------- |
| switch_id       | ID of the network device port is on                                                               |
| switch2_id      | ID of the 2nd network device port is on                                                           |
| last_updated_lt | last updated less than date                                                                       |
| last_updated_gt | last updated greater than date                                                                    |
| first_added_lt  | first added less than date                                                                        |
| first_added_gt  | first added greater than date                                                                     |
| tags            | filter by switch port tags                                                                        |
| tags_and        | filter by tags, must contain all tags                                                             |
| include_cols    | do not return all columns just the ones specified. For example, ?include_cols=description,devices |

## Get all Switch Templates

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/switch_templates/ --insecure
```

> The above command returns results like this:

```json
[
  {
    "switch_type": "Modular/Distributed (Single)",
    "template_id": 2,
    "template_name": "m3"
  },
  {
    "switch_type": "Single Clustered",
    "template_id": 3,
    "template_name": "m2"
  },
  {
    "switch_type": "Single",
    "template_id": 4,
    "template_name": "48FE"
  },
  {
    "switch_type": "Modular/Distributed (Single)",
    "template_id": 5,
    "template_name": "6509"
  },
  {
    "switch_type": "Modular/Distributed (Single)",
    "template_id": 6,
    "template_name": "FiberLC12"
  },
  {
    "switch_type": "Single",
    "template_id": 1,
    "template_name": "Cisco3750MH"
  }
]
```

GET method retrieves all Switch Templates.

**`GET /api/1.0/switch_templates/`**

## Create / Update Switch Ports

```shell
curl -X POST -d "port=FastEthernet0/20&switch=nh-switch-01&up_admin=no"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/switchports/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg": ["switchport successfully added/updated"4"FastEthernet0/20"],"code": 0}
```

Create / Update Switch Port by Port.

**`POST /api/1.0/switchports/`**

**See notice below**

### Parameters

| Parameter                     | Use                                                                                                  | Description                                                                                                |
| ----------------------------- | ---------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| port                          | required                                                                                             |
| new_port                      | optional                                                                                             | rename exisiting port                                                                                      |
| switch                        | optional                                                                                             | d42 will look for existing port and switch combination and if it exists, will update that port.            |
| description                   | optional                                                                                             |
| type                          | optional                                                                                             | Must exist in device42(add via UI)                                                                         |
| vlan_ids                      | Comma separated vlan ids on that port. Available via GET at /api/1.0/vlans/ or Tools > Export > VLAN |
| up                            | optional                                                                                             | = yes for up. = no for down.                                                                               |
| up_admin                      | optional                                                                                             | Whether port is administratively up or down. = yes for up. = no for down.                                  |
| count                         | optional                                                                                             | Whether to include the port in total count or not. = yes or no.                                            |
| remote_port_id                | optional                                                                                             | ID of the remote connected switch port.                                                                    |
| remote_device and remote_port | optional                                                                                             | Name of the switch and port for remote connected switch port.                                              |
| module                        | optional                                                                                             | name of the blade that port belongs to. Blade device must be part of the switch. (added in v5.8.1)         |
| device2                       | optional                                                                                             | name of the device2 that port belongs to. (added in v5.8.1)                                                |
| device                        | optional                                                                                             | name of the directly connected device (Added in v5.8.2) (used to connect remote port for legacy support)   |
| label                         | optional                                                                                             | label for the directly connected device (Added in v5.8.2) (used to connect remote port for legacy support) |
| tags                          | optional                                                                                             | add or update tags to a switchport                                                                         |
| tags_remove                   | optional                                                                                             | remove tags from a switchport                                                                              |
| mtu                           | optional                                                                                             | add value for mtu                                                                                          |
| name                          | optional                                                                                             | add name of port                                                                                           |
| speed                         | optional                                                                                             | update port speed                                                                                          |
| remote_port_clear             | optional                                                                                             | if set to yes, will clear the remote port                                                                  |

<aside class="notice">
Remote switch port can be associated using remote_port_id, or by using combination of remote_device and remote_port
</aside>

## Create or update using Switch Templates

```shell
curl -X POST -d "device=2924-c03&switch_template_id=3&device_ids=5742,5743"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/switches/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg": ["switchports successfully added"337"oxctrouter"],"code": 0}
```

Add Switch/Switch Ports w/ Templates.

**`POST /api/1.0/switches/`**

### Parameters

| Parameter           | Use      | Description                                                                                                                                     |
| ------------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| device or device_id | required | Name of new or existing device, or id for existing device. Existing device must be a network switch. If stacked switches, must be type cluster. |
| switch_template_id  | required | [GET all Switch Templates](#get-all-switch-templates)                                                                                           |

###Parameters for switch template type stacked
Parameter | Description
--------- | -------- |
devices | comma separated names of new devices.
device_ids | comma separated values of existing devices.

The # of devices must match # of devices in the stack template.

###Parameters for switch template type modular single

If FEX attached to the modular switch, use:

| Parameter | Description                                |
| --------- | ------------------------------------------ |
| assets    | comma separated names of new assets.       |
| asset_ids | comma separated values of existing assets. |

The # of assets must match # of assets in the template.

###Parameters for switch template type modular paired
Parameter | Description
--------- | -------- |
device2 or device2_id | Required. name of new or existing device. id for existing device. Existing device must be a network switch.
assets or asset_ids | If FEX part of the switch. comma separated values. assets for new assets and asset_ids for existing assets.

## Delete Switchport

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/switchports/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the switch port with the switch port id supplied as the required argument.

**`DELETE /api/1.0/switchports/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description    |
| --------- | -------------- |
| id        | Switch port id |

## Custom Fields

```shell
curl -X PUT -d "id=1027&key=FieldName&value=427" -u'admin:adm!d42' https://yourdevice42address/api/1.0/custom_fields/switchport/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["custom key pair values added or updated", 4, "1027"], "code": 0 }
```

Create / Update Switch Port Custom Fields.

**`PUT /api/1.0/custom_fields/switchport/`**

**See notice below**

### Parameters

| Parameter          | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| id                 | required                        | ID of the switchport                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| port               | optional                        | required if ID not used                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| device_name        | optional                        | required if switchport ID not used                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| key                | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type               | optional                        | This is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value              | optional                        |
| notes              | optional                        |
| clear_value        | optional                        | yes to clear existing value for that field                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| bulk_fields        | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2                                                                                                                                                                                                                                                                                                                                                                                             |

<aside class="notice">
When specifying port/switchport to add/update with a Custom Field, use 'id' of the port in the call or `port` and `device_name`.
</aside>

## Get DNS Records

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/dns/records/?type=A --insecure
```

> The above command returns results like this:

```json
{
{
  "total_count": 39,
  "records": [
    {
      "name": "@",
      "prio": null,
      "dns_zone": "device42.pvt",
      "dns_zone": "device42.pvt",
      "content": "nh-win2k8r2-vm-03 hostmaster 107489 900 600 86400 3600",
      "ttl": 3600,
      "change_date": null,
      "nameserver": "192.168.11.161",
      "type": "SOA",
      "id": 1
    },
    {
      "name": "@",
      "prio": null,
      "dns_zone": "device42.pvt",
      "content": "192.168.11.161",
      "ttl": 600,
      "change_date": null,
      "nameserver": "192.168.11.161",
      "type": "A",
      "id": 2
    },
    {,......
}
```

GET method retrieves all DNS Records. (Added in v6.5.0)

**`GET /api/1.0/dns/records/`**

### Query string parameters

You can filter dns records by following parameters in the query string

| Parameter  | Description                          |
| ---------- | ------------------------------------ |
| domain     | name of the domain                   |
| type       | type of the record. e.g. AAAA        |
| name       | name on the DNS record               |
| nameserver | Nameserver                           |
| content    | Content (e.g. IP address for type A) |
| tags       | Match ANY tags                       |
| tags_and   | Match ALL tags                       |

### Include Columns

You can show certain columns in the output by using following parameters in the query string following by "include_cols="

| Parameter   | Included by default? |
| ----------- | -------------------- |
| name        | yes                  |
| dns_zone    | yes                  |
| content     | yes                  |
| ttl         | yes                  |
| change_date | yes                  |
| type        | yes                  |
| id          | yes                  |

### Blank values as null

If you want to return null for all the blank, use following parameter in the query string

| Parameter    | Value |
| ------------ | ----- |
| blanksasnull | yes   |

## Create / Update DNS Records

```shell
curl -X POST -d "domain=d42.net&nameserver=ns1&name=oxrouter&type=A&ttl=3" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/dns/records/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg": ["DNS record added/updated successfully"37"oxrouter"],"code": 0}
```

Create DNS Records.

**`POST /api/1.0/dns/records/`**

**See notices below**

### Parameters

| Parameter   | Use      | Description                                                                              |
| ----------- | -------- | ---------------------------------------------------------------------------------------- |
| domain      | required | Name of domain.                                                                          |
| type        | required | Type of record. See notice below.                                                        |
| nameserver  | optional | If you have overlapping domain names, this is required to differentiate between the two. |
| name        | optional | Record value. (e.g. www) use @ for blank.                                                |
| content     | optional | Content (e.g. IP address for type A)                                                     |
| prio        | optional | Priority for MX record.                                                                  |
| ttl         | optional | TTL value.                                                                               |
| tags        | optional | set tags for record.                                                                     |
| tags_remove | optional | remove tags for record.                                                                  |

<aside class="notice">
Type of records: SOA, NS, MX, A, AAAA, CNAME, PTR, TXT, SPF, SRV, CERT, DNSKEY, DS, KEY, NSEC, RRSIG, HINFO, LOC, NAPTR, RP, AFSDB, SSHFP
</aside>

## Delete DNS Records

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/dns/records/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the DNS record with the DNS Record id supplied as the required argument.

**`DELETE /api/1.0/dns/records/<ID>/`** (Added in v10.2.0)

### Required Parameter

| Parameter | Description   |
| --------- | ------------- |
| id        | DNS Record id |

## Create / Update DNS Zones

```shell
curl -X POST -d "name=d42&nameserver=10.42.42.42" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/dns/zones/
```

> The above command returns JSON structured like this:

```json
{"msg": ["DNS record added/updated successfully"37"oxrouter"],"code": 0}
```

Create DNS Zones.

**`POST /api/1.0/dns/zones/`**

**See notices below**

### Parameters

| Parameter    | Use      | Description                        |
| ------------ | -------- | ---------------------------------- |
| name         | required | Name of DNS zone                   |
| nameserver   | required | IP Address/hostname of name server |
| notes        | optional | Notes about the entry              |
| vrf_group    | optional | VRF group name for records         |
| vrf_group_id | optional | VRF group ID                       |
| tags         | optional | Tags for grouping zone entries     |
| tags_remove  | optional |

## Get all IP NAT Records

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/ipnat/ --insecure
```

> The above command returns results like this:

```json
{
  "ipnats": [
    {
      "id": 1,
      "ip_address_from": "10.1.2.1",
      "ip_address_to": "42.42.42.42",
      "name": "test",
      "notes": "",
      "two_way_relation": "False"
    }
  ]
}
```

GET method retrieves all Switch Templates.

**`GET /api/1.0/ipnat/`**

## Create IP NAT

```shell
curl -X POST -d "name=OX Global NAT&ip_address_from=71.235.116.75&ip_address_to=192.168.1.3"  -u 'admin:adm!nd42' https://D42IP/api/1.0/ipnat/  --insecure
```

> The above command returns JSON structured like this:

```json
{"msg": ["IP Nat added."1"OX Global NAT"],"code": 0}
```

Create IP NAT.

**`POST /api/1.0/ipnat/`** (Added in v7.0.0)

### Parameters

| Parameter           | Use      | Description                                                                                                                                     |
| ------------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| name                | required | Name of the NAT IP entry                                                                                                                        |
| ip_address_from     | required | The external IP address                                                                                                                         |
| ip_address_from_end | optional | used for mapping a range of addresses rather than a single IP                                                                                   |
| source_port_start   | optional | Starting port for source IP range                                                                                                               |
| source_port_end     | optional | used for mapping a range of ports rather than a single port                                                                                     |
| ip_address_to       | required | The internal IP address                                                                                                                         |
| ip_address_to_end   | optional | used for mapping a range of addresses rather than a single IP                                                                                   |
| target_port_start   | optional | Starting port for target IP range                                                                                                               |
| target_port_end     | optional | used for mapping a range of ports rather than a single port                                                                                     |
| two_way_relation    | optional | true if the internal IP addressed is masked with the external IP address for outbound traffic in addition to inbound traffic. Default is false. |
| protocol            | optional | the transport protocol, ie TCP                                                                                                                  |
| notes               | optional |
| vrf_group_from      | optional | Originating VRF Group Name                                                                                                                      |
| vrf_group_id_from   | optional | Originating VRF Group ID                                                                                                                        |
| vrf_group_to        | optional | Destination VRF Group Name                                                                                                                      |
| vrf_group_id_to     | optional | Destination VRF Group ID                                                                                                                        |

## Update IP NAT

```shell
curl -X PUT -d "id=1,name=OX Global NAT&ip_address_from=71.235.116.75&ip_address_to=192.168.1.3"  -u 'admin:adm!nd42' https://D42IP/api/1.0/ipnat/  --insecure
```

> The above command returns JSON structured like this:

```json
{"msg": ["IP Nat updated."1"OX Global NAT"],"code": 0}
```

Update IP NAT (Added in v7.0.0)

**`PUT /api/1.0/ipnat/`**
or
**`PUT /api/1.0/ipnat/<ID>/`**

### Parameters

| Parameter        | Use                        | Description                                                                                                                                     |
| ---------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| id               | required if not in the URL | ID of the NAT entry                                                                                                                             |
| name             | optional                   | Name of the NAT IP entry                                                                                                                        |
| ip_address_from  | optional                   | The external IP address                                                                                                                         |
| ip_address_to    | optional                   | The internal IP address                                                                                                                         |
| two_way_relation | optional                   | true if the internal IP addressed is masked with the external IP address for outbound traffic in addition to inbound traffic. Default is false. |
| notes            | optional                   |

## Delete IP NAT

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/ipnat/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the IP NAT.

**`DELETE /api/1.0/ipnat/<ID>/`** (Added in v7.0.0)

# Application Components

## Get Application Components

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/appcomps/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "appcomps": [
    {
      "appcomp_id": 33,
      "appcomp_url": "/api/1.0/appcomps/33/",
      "name": "Device42 Production"
    },
    {
      "appcomp_id": 15,
      "appcomp_url": "/api/1.0/appcomps/15/",
      "name": "VMWare Farm"
    },
    {
      "appcomp_id": 35,
      "appcomp_url": "/api/1.0/appcomps/35/",
      "name": "Corp Wiki"
    },
    {
      "appcomp_id": 8,
      "appcomp_url": "/api/1.0/appcomps/8/",
      "name": "Database Server"
    },
    {
      "appcomp_id": 6,
      "appcomp_url": "/api/1.0/appcomps/6/",
      "name": "Application Server"
    },
    {
      "appcomp_id": 36,
      "appcomp_url": "/api/1.0/appcomps/36/",
      "name": "Web Server"
    }
  ]
}
```

**`GET /api/1.0/appcomps/`**

### Query String Parameters

| Parameter | Description              |
| --------- | ------------------------ |
| device_id | filter by id of device   |
| device    | filter by name of device |

## Get Application Components by ID

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/appcomps/ID/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "appcomp_id": 15,
  "custom_fields": [
    {
      "key": "Project",
      "notes": "",
      "value": "Migration"
    }
  ],
  "dependent":
    "mobileapp.com - prodserver-001, retailsite.com - prodserver-031",
  "depends_on": "Apache Tomcat Server",
  "device": "USNHCTVMWPROD",
  "group_owner": "US Infrastructure Operations",
  "groups_affected": "",
  "name": "VMWare Farm",
  "notes": "",
  "what": ""
}
```

**`GET /api/1.0/appcomps/<appcomp_id>/`**

## Create / Update Application Components

```shell
curl -X POST -d "name=SQLServer92&device=up404&notes=some notes here" -u 'admin:adm!nd42'  https://yourdevice42ipaddress/api/1.0/appcomps/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg": ["Application Component added or updated."35"Corp Wiki"true,true],"code": 0}
```

**`POST /api/1.0/appcomps/`**

**See Notices Below**

### Parameters

| Parameter          | Use      | Description                                                                                                                                                                      |
| ------------------ | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name               | required | unique name for component.                                                                                                                                                       |
| device             | required | device (name) that this component is dependent on.                                                                                                                               |
| group_owner        | optional | Name of group that is responsible for this component - must match group name exactly.                                                                                            |
| what               | optional | Description of business impact due to loss of component.                                                                                                                         |
| depends_on         | optional | Names of app components this component depends on, separated by commas - must match component names exactly.                                                                     |
| dependents         | optional | Names of app components that depend on this component separated by commas - must match component names exactly.                                                                  |
| groups_affected    | optional | Names of affected groups separated by commas - must match group names exactly.                                                                                                   |
| notes              | optional |
| device_reason      | optional | string for the device reason on this appcomp (added in v6.6.0)                                                                                                                   |
| depends_on_reasons | optional | list of string pairs for dependent appcomps on this appcomp e.g. => depend_appcomp_name1:reason1, depend_appcomp_name2:reason2,...depend_appcomp_nameN:reason3 (added in v6.6.0) |

<aside class="notice">
A new component will be inserted if the name does not exist and will update if the application component name already exists.
</aside>

## Delete Application Component

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/appcomps/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the application component with the application component id supplied as the required argument.

**`DELETE /api/1.0/appcomps/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description   |
| --------- | ------------- |
| id        | IP Address id |

## Custom Fields

```shell
curl -X PUT -d "id=30&custom_field=Project&value=Intranet Site&value2=In Dev&show_on_chart=yes&notes=Test notes" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/custom_fields/appcomp/ --insecure
```

> The above command returns JSON structured like this:

```json
{"msg": ["custom key pair values added or updated"35"Corp Wiki"],"code": 0}
```

API to create/update custom fields for application components.

**`PUT /api/1.0/custom_fields/appcomp/`**

### Parameters

| Parameter           | Use      | Description                                                                                          |
| ------------------- | -------- | ---------------------------------------------------------------------------------------------------- |
| id or name          | required | ID or name of application component                                                                  |
| custom_field or key | required | Existing custom field name or new name                                                               |
| type                | optional | Data type, i.e: text, date, number                                                                   |
| value               | optional | Custom field value                                                                                   |
| show_on_chart       | optional | Show the field on impact charts                                                                      |
| notes               | optional | Custom field notes                                                                                   |
| clear_value         | optional | yes to clear existing value for that field                                                           |
| bulk_fields         | optional | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2 |

# Services

## Get Services

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/services/ --insecure
```

> The above command returns JSON structured like this:

```json
{
    "services": [
        {
            "category": null,
            "description": "",
            "displayname": "DB service",
            "id": 1,
            "name": "DB service",
            "notes": "",
            "service_type": "tracked",
            "tags": [],
            "vendor": null
        },
        {
            "category": null,
            "description": "",
            "displayname": "D42 Main app",
            "id": 2,
            "name": "D42 Main app",
            "notes": "",
            "service_type": "tracked",
            "tags": [],
            "vendor": null
        },....
        ]
}
```

**`GET /api/1.0/services/`**

### Query string parameters

You can filter services by following parameters in the query string

| Parameter  | Description           |
| ---------- | --------------------- |
| service_id | D42 ID of the service |
| name       | name of the service   |
| categroy   | name of the category  |
| vendor     | vendor name           |

## Create / Update Services

```shell
curl -X POST -d "category=security&displayname=DisplayLinkManager&description=&notes=&service_type=ignored&vendor=&name=DisplayLinkService" -u 'admin:adm!nd42'  https://yourdevice42ipaddress/api/1.0/services/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["service added/updated", 2, "DisplayLinkManager", true, false],
  "code": 0
}
```

**`POST /api/1.0/services/`**

### Parameters

| Parameter    | Use      | Description                                       |
| ------------ | -------- | ------------------------------------------------- |
| name         | required | name for service                                  |
| display_name | optional | if not provided, the name is used as display name |
| category     | optional | Name of category                                  |
| service_type | optional | could be ignored or tracked. Default is tracked.  |
| vendor       | optional | Names of vendor                                   |
| description  | optional |
| notes        | optional |

## Delete Service

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/services/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the service with the service id supplied as the required argument.
Note: You will only be able to delete the service if the supplied username has the correct permissions.

**`DELETE /api/1.0/services/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | service id  |

## Get Service details

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/service_details/ --insecure
```

> The above command returns JSON structured like this:

```json
{
    "total_count": 543,
    "service_details": [
        {
            "appcomp": "Device42 Production",
            "appcomp_id": "Device42 Production",
            "device": "Device42 Appliance",
            "device_id": 445,
            "first_detected": "2015-02-27T19:20:29.493Z",
            "last_updated": "2015-02-27T19:20:44.029Z",
            "service_detail_id": 3,
            "service_display_name": "DB service",
            "service_id": 1,
            "service_name": "DB service",
            "startmode": "automatic",
            "state": "running",
            "user": null,
            "user_id": null,
            "vendor": null,
            "vendor_id": null
        },...
```

**`GET /api/1.0/service_details/`**

**`GET /api/1.0/service_schedules/` (This is to get schedules. POST and DELETE use service_detail call**

### Query string parameters

You can filter service details by following parameters in the query string

| Parameter         | Description                        |
| ----------------- | ---------------------------------- |
| device_id         | filter by id of the device         |
| device            | filter by name of the device       |
| service_id        | filter by id of the service        |
| service_detail_id | filter by id of the service in use |
| user_id           | filter by id of the user           |

## Create / Update service details

```shell
curl -X POST -d "service_name=trackredditusageapp&service_display_name=Reddit Tracker&vendor=Internal&description=This service tracks total time spent on reddit per user&service_type=tracked&category=timepass&startmode=Automatic&state=running&device=hulkserver&user=theboss_user" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/service_details/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["service_detail added/updated", 1, "Reddit Tracker", true, true],
  "code": 0
}
```

**`POST /api/1.0/service_details/`**

### Parameters

| Parameter            | Use                    | Description                                                                                                         |
| -------------------- | ---------------------- | ------------------------------------------------------------------------------------------------------------------- |
| service_name         | required               | The executable name of the service                                                                                  |
| service_display_name | optional               | The user freindly display name of the service                                                                       |
| vendor               | optional               | The vendor that created the server, linked to Organization                                                          |
| description          | optional               | A verbose description of the server                                                                                 |
| service_type         | optional               | The service type, either "Tracked" or "Ignored"                                                                     |
| category             | optional               | The named catgeory grouping of this service                                                                         |
| startmode            | optional               | The start mode of this service - valid values are 'Automatic', 'Manual', 'Disabled' and 'Unknown'                   |
| state                | optional               | The current running state of this service. Valid values are 'Running', 'Started', 'Paused', 'Stopped' and 'Unknown' |
| device               | optional               | The device that this service runs on                                                                                |
| user                 | optional               | The user that this service runs under                                                                               |
| appcomp              | optional               | The application component that depends on this service                                                              |
| install_date         | optional               |
| status               | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| status               | required for schedules | only for schedule based services where startmode = 'Scheduled'                                                      |
| install_Date         | optional               | (note capital D for schedules) only for schedule based services where startmode = 'Scheduled'                       |
| at_logon             | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| at_startup           | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| event_based          | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| idle_time            | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| other_trigger        | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| other_type           | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| minutes              | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| hours                | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| days                 | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| weeks                | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| day_of_month         | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| month_of_year        | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |
| day_of_week          | optional               | only for schedule based services where startmode = 'Scheduled'                                                      |

<aside>caption is required in args for scheduled service details</aside>
## Delete Service detail

```shell
curl -X DELETE -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/service_details/1/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "1" }
```

This API is used to delete the service detail with the service detail id supplied as the required argument.
Note: You will only be able to delete the service if the supplied username has the correct permissions.

**`DELETE /api/1.0/service_details/<ID>/`**

### Required Parameter

| Parameter | Description       |
| --------- | ----------------- |
| id        | service detail id |

## Get Service ports

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/service_ports/ --insecure
```

> The above command returns JSON structured like this:

```json
{
    "total_count": 565,
    "service_ports": [
        {
            "description": "",
            "device_name": "app21.device42.pvt",
            "discovered_process": "",
            "discovered_service": "mysqld",
            "id": 1,
            "listening_ip": "127.0.0.1",
            "mapped_service": null,
            "port": 3306,
            "remote_ips": "192.241.191.123"
        },
        {
            "description": "",
            "device_name": "app21.device42.pvt",
            "discovered_process": "",
            "discovered_service": "postgres",
            "id": 2,
            "listening_ip": "127.0.0.1",
            "mapped_service": null,
            "port": 5432,
            "remote_ips": ""
        },...
```

**`GET /api/1.0/service_ports/`**

### Query string parameters

You can filter service ports by following parameters in the query string

| Parameter | Description                      |
| --------- | -------------------------------- |
| id        | filter by id of the service port |

## Create / Update service ports

```shell
curl -X POST -d "port=80&device_name=hulkserver&listening_ip=0.0.0.0&description=reddit voting stats web server port&discovered_service=trackredditusageapp&remote_ips=192.168.1.2,192.168.1.3" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/service_ports/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["service port added/updated", 1, "0.0.0.0:80", true, true],
  "code": 0
}
```

**`POST /api/1.0/service_ports/`**

### Parameters

| Parameter          | Use      | Description                                                                         |
| ------------------ | -------- | ----------------------------------------------------------------------------------- |
| port               | required | the listening port on this device                                                   |
| device_name        | required | the name of the device                                                              |
| listening_ip       | required | the local IP address that listening on this port                                    |
| description        | optional | the description of this listening port                                              |
| discovered_service | optional | the name of the discovered service listening on this IP/port                        |
| discovered_process | optional | the process name that has a handle to the port                                      |
| mapped_service     | optional | the name of the mapped service listening on this IP/port                            |
| remote_ips         | optional | the comma separated list of remote IPs that are connected to this listening IP/port |

## Delete Service port

```shell
curl -X DELETE -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/service_ports/1/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "1" }
```

This API is used to delete the service port with the service port id supplied as the required argument.
Note: You will only be able to delete the service port if the supplied username has the correct permissions.

**`DELETE /api/1.0/service_ports/<ID>/`**

### Required Parameter

| Parameter | Description     |
| --------- | --------------- |
| id        | service port id |

## Get Listener Connection Statistics

```shell
curl -X GET -u 'admin:adm!nd42' https://device42/api/1.0/listener_connection_stats/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "client_ips": "76.127.207.96",
  "client_stats": [
    {
      "avg_time_span": 5,
      "first_found": "2016-09-16T14:45:14",
      "ip": "76.127.207.96",
      "last_found": "2016-09-16T14:57:56",
      "last_time_span": 7,
      "latest_contiguous_stats": {
        "avg_time_span": 5,
        "detected_connections": 3,
        "ephemeral_ports": "(17741:25283:23991)",
        "first_found": "2016-09-16T14:45:14",
        "last_found": "2016-09-16T14:57:56",
        "last_time_span": 7,
        "max_time_span": 7,
        "min_time_span": 0,
        "std_dev_time_span": 29,
        "total_detected": 2
      },
      "max_time_span": 7,
      "min_time_span": 0,
      "std_dev_time_span": 29,
      "total_detected": 2
    }
  ],
  "description": "",
  "id": 2207,
  "listener_device_id": 2440,
  "listener_device_name": "prod-retail-webfrontend",
  "listener_service": "sshd",
  "listening_ip": "0.0.0.0",
  "port": 22
}
```

**`GET /api/1.0/listener_connection_stats/`** (Added in v10.5.0)

### Optional Parameter

| Parameter            | Description                            |
| -------------------- | -------------------------------------- |
| listener_device_name | Name of device with listening services |
| port                 | Listening port                         |

## Get Network Shares

```shell
curl -X GET -u 'user:password' https://8.67.5.309/api/1.0/network_shares/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "networkshare_details": [
    {
      "caption": "Remote Admin",
      "description": "Remote Admin",
      "device_name": "ovh01-w2012-04",
      "first_detected": "2016-04-08T16:14:04.612Z",
      "id": 5,
      "install_date": null,
      "last_updated": "2016-04-08T16:14:04.612Z",
      "name": "ADMIN$",
      "path": "C:\\Windows",
      "status": "OK",
      "type": "Disk Drive"
    },
    {
      "caption": "Default share",
      "description": "Default share",
      "device_name": "ovh01-w2012-04",
      "first_detected": "2016-04-08T16:14:04.646Z",
      "id": 6,
      "install_date": null,
      "last_updated": "2016-04-08T16:14:04.646Z",
      "name": "C$",
      "path": "C:\\",
      "status": "OK",
      "type": "Disk Drive"
    },
    ...truncated
  ]
}
```

**`GET /api/1.0/network_shares/`**

# Software

## Get Software details

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/software_details/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "limit": 3,
  "offset": 0,
  "software_details": [
    {
      "count_in_licensing": true,
      "device": "d42laptop01",
      "device_id": 351,
      "first_detected": "2015-05-20T12:33:13.343Z",
      "id": 108,
      "install_date": null,
      "last_updated": "2015-05-20T12:33:13.343Z",
      "license_use_count": 1.0,
      "software": "Hyper-V Tools",
      "software_alias": null,
      "software_id": 108,
      "user": null,
      "user_id": null,
      "vendor": "Probus-IT",
      "vendor_id": 62,
      "version": "2.03.00.04"
    },
    {
      "count_in_licensing": true,
      "device": "d42laptop01",
      "device_id": 351,
      "first_detected": "2015-05-20T12:33:13.389Z",
      "id": 109,
      "install_date": "2014-06-12",
      "last_updated": "2015-05-20T12:33:13.957Z",
      "license_use_count": 1.0,
      "software": "Microsoft Visual Studio 2010 Tools for Office Runtime (x64)",
      "software_alias":
        "Microsoft Visual Studio 2010 Tools for Office Runtime (x64) 10.0.40308",
      "software_id": 109,
      "user": null,
      "user_id": null,
      "vendor": "Microsoft Corporation",
      "vendor_id": 38,
      "version": "10.0.40308"
    },
    {
      "count_in_licensing": true,
      "device": "d42laptop01",
      "device_id": 351,
      "first_detected": "2015-05-20T12:33:13.428Z",
      "id": 110,
      "install_date": null,
      "last_updated": "2015-05-20T12:33:13.428Z",
      "license_use_count": 1.0,
      "software": "Notepad2 (Notepad Replacement)",
      "software_alias": null,
      "software_id": 110,
      "user": null,
      "user_id": null,
      "vendor": "Florian Balmer",
      "vendor_id": 63,
      "version": "4.2.25"
    }
  ],
  "total_count": 2775
}
```

**`GET /api/1.0/software_details/`**

### Query string parameters

You can filter software details by following parameters in the query string

| Parameter          | Description                         |
| ------------------ | ----------------------------------- |
| device_id          | filter by id of the device          |
| device             | filter by name of the device        |
| software_id        | filter by id of the software        |
| software_detail_id | filter by id of the software in use |

## Create / Update software details

```shell
curl -X POST -d "software=Some Enterprise DB&version=42.42&device=bulky server&user=that guy&vendor=That big blue&install_date=2014-01-01" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/software_details/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["software_detail added/updated", 1, "Some Enterprise DB", true, true],
  "code": 0
}
```

**`POST /api/1.0/software_details/`**

### Parameters

| Parameter          | Use      | Description                                                        |
| ------------------ | -------- | ------------------------------------------------------------------ |
| software           | required | the name of the software                                           |
| device             | required | The device where this software is installed                        |
| version            | optional | The version number of the software                                 |
| user               | optional | The user assigned to this software                                 |
| vendor             | optional | The vendor that created the server, linked to Organization         |
| install_date       | optional | The date that the software was installed                           |
| count_in_licensing | optional | Boolean value, should this software detail be counted in licensing |
| license_key        | optional | The license key associated with this software intallation          |
| license_key_count  | optional | The number of licenses this software key supports                  |
| license_use_count  | optional | the number of licenses that are in use for this software instance  |

## Delete Software detail

```shell
curl -X DELETE -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/software_details/1/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "1" }
```

This API is used to delete the software detail with the software detail id supplied as the required argument.
Note: You will only be able to delete the software if the supplied username has the correct permissions.

**`DELETE /api/1.0/software_details/<ID>/`**

### Required Parameter

| Parameter | Description        |
| --------- | ------------------ |
| id        | software detail id |

## Get Software Component details

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/software/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "total_count": 2016,
  "software": [
    {
      "aliases": "3pay, 4pay",
      "category": "past_time2",
      "discovered_count": null,
      "id": 583,
      "licensed_count": null,
      "licensing_model": "Individual - Device/Perpetual",
      "name": "2pay",
      "notes": "test",
      "software_type": "prohibited",
      "tags": ["def"],
      "track_licensed_count_by_keys": "yes",
      "vendor": "cisco"
    }
  ]
}
```

**`GET /api/1.0/software/`**

### Query string parameters

You can filter software details by following parameters in the query string

| Parameter       | Description                                                         |
| --------------- | ------------------------------------------------------------------- |
| name            | The name of the software component                                  |
| category        | Filter by user-defined software categories                          |
| vendor          | Software Vendor                                                     |
| licensing_model | Filter by license model                                             |
| software_type   | Filter by software type (managed, unmanaged, prohibited or ignored) |
| tags            | Filter by tags (OR)                                                 |
| tags_and        | Filter by tags (AND)                                                |

## Create / Update Software Components

```shell
curl -X POST -d "name=New_Software&category=accounting&licensing_model=Perpetual&software_type=Managed" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/software_details/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["software added/updated", 1, "New_Software", true, true], "code": 0 }
```

**`POST /api/1.0/software/`**

### Parameters

| Parameter               | Use      | Description                                                         |
| ----------------------- | -------- | ------------------------------------------------------------------- |
| name                    | required | The name of the software(new or existing)                           |
| id                      | required | The ID of the software, required if not using NAME                  |
| category                | optional | Filter by user-defined software categories(new or existing)         |
| vendor                  | optional | Software Vendor(new or existing)                                    |
| licensing_model         | optional | licensing model defined should already exist in Device42            |
| software_type           | optional | Filter by software type (managed, unmanaged, prohibited or ignored) |
| tags                    | optional |
| tags_remove             | optional | remove tags from component                                          |
| track_licensed_by_count | optional | whether or not to track software by discovered count                |
| notes                   | optional |
| aliases                 | optional | any software aliases                                                |

## Delete Software Component

```shell
curl -X DELETE -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/software/1/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "1" }
```

This API is used to delete the software detail with the software detail id supplied as the required argument.
Note: You will only be able to delete the software if the supplied username has the correct permissions.

**`DELETE /api/1.0/software/<ID>/`**

### Required Parameter

| Parameter | Description           |
| --------- | --------------------- |
| id        | software component id |

## Get Software License Keys

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/software/license_keys/ --insecure
```

> The above command returns JSON structured like this:

```json
{
    "software_license_keys": [
        {
            "count": 1,
            "id": 1,
            "key": "124124",
            "notes": "",
            "software_id": 114,
            "software_name": "accountsservice"
        },
        {
            "count": 1,
            "id": 2,
            "key": "124124",
            "notes": "",
            "software_id": 114,
            "software_name": "accountsservice"
       }
],
```

**`GET /api/1.0/software/license_keys/`**

### Query string parameters

You can filter software license keys by following parameters in the query string

| Parameter     | Description                              |
| ------------- | ---------------------------------------- |
| software_id   | filter by id of the software component   |
| software_name | filter by name of the software component |

## Create / Update Software Licenses

```shell
curl -X POST -d "software_id=1668&software_name=apache2&key=1618" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/software/license_keys/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": ["software license key added/updated", 2, "1618", true, true],
  "code": 0
}
```

**`POST /api/1.0/software/license_keys/`**

### Parameters

| Parameter     | Use                      | Description                               |
| ------------- | ------------------------ | ----------------------------------------- |
| id            | required                 | The ID of the software_license_key object |
| software_id   | required if not using id | the id of software component              |
| software_name | required if not using id | software component name                   |
| key           | optional                 | software license key                      |

## Delete Software License Keys

```shell
curl -X DELETE -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/software/license_keys/1/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "1" }
```

This API is used to delete the software license key with the software license key id supplied as the required argument.
Note: You will only be able to delete the software key if the supplied username has the correct permissions.

**`DELETE /api/1.0/software/license_keys/<ID>/`**

### Required Parameter

| Parameter | Description             |
| --------- | ----------------------- |
| id        | software license key id |

# Certificates

## Get certificates

```shell
curl -X GET -u'admin:adm!nd42' https://yourdevice42url/api/1.0/certificates/?certificate_id=3
```

> The above command returns JSON structured like this:

```json
{
  "total_count": 1,
  "certificate_details": [
    {
      "content_commitment_usage": false,
      "crl_sign_usage": false,
      "custom_fields": [],
      "data_encipherment_usage": false,
      "days_to_expiry": 1077,
      "decipher_only_usage": false,
      "digital_signature_usage": true,
      "encipher_only_usage": false,
      "extended_key_usage":
        "SERVERAUTH(1.3.6.1.5.5.7.3.1)\nCLIENTAUTH(1.3.6.1.5.5.7.3.2)\n",
      "id": 3,
      "issued_by": null,
      "issued_to": "registration.device42.com",
      "key_agreement_usage": false,
      "key_cert_sign_usage": false,
      "key_encipherment_usage": true,
      "parent_cert": null,
      "serial_number": "77eb9b55e9228635f2157fd374b8da8",
      "signature_algorithm": "sha256WithRSAEncryption",
      "signature_hash": "708489795",
      "subject":
        "/OU=Domain Control Validated/OU=PositiveSSL/CN=registration.device42.com",
      "valid_from": "2014-08-10",
      "valid_to": "2019-08-09",
      "vendor": null,
      "version": 2
    }
  ]
}
```

**`GET /api/1.0/certificates/`**

This API is used to return the certificates you have added or discovered in Device42.

### Query String Parameters

You can filter the certificates returned by the following parameters in the query string:

| Parameter      | Description           |
| -------------- | --------------------- |
| certificate_id | ID of the certificate |

## Create/Update certificates

```shell
curl -X POST -d "dns=www.yourweburl.com&issued_to=device42.com&valid_from=2015-01-01&valid_to=2042-12-31&subject=description here" -u 'AzureDiamond:hunter2' https://yourdevice42address/api/1.0/certificates --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "certificate added/updated",
    "test.device42.com",
    "test.device42.com",
    true,
    true
  ],
  "code": 0
}
```

This API is used to update or add new certificates in bulk. Note: If you enter the DNS for the certificate, that is the only required field. Otherwise issued_to, valid_from, valid_to, and subject are required.

### Required Parameter

| Parameter                | Use        | Description                                                                                                                                                                       |
| ------------------------ | ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| issued_to                | required\* | Entity certificate is issued to, required if no dns value.                                                                                                                        |
| dns                      | optional   | dns address of the site certificate is issued for. If entered, is the only required field.                                                                                        |
| issued_by                | optional   | The entity issuing the certficate.                                                                                                                                                |
| valid_from               | required\* | The start date of the certificate, required if no dns value.                                                                                                                      |
| valid_to                 | required\* | The date the certificate expires, required if no dns value.                                                                                                                       |
| version                  | optional   | The version of the encoded certificate.                                                                                                                                           |
| serial_number            | optional   | Used to uniquely identify the certificate.                                                                                                                                        |
| subject                  | required\* | The person, or entity identified. Required if no dns value.                                                                                                                       |
| signature_algorithm      | optional   | The algorithm used to create the signature.                                                                                                                                       |
| signature_hash           | optional   | The actual signature to verify that it came from the issuer.                                                                                                                      |
| digital_signature_usage  | optional   | When the subject public key is used for verifying digital signatures.                                                                                                             |
| content_commitment_usage | optional   | When the subject public key is used to verify digital signatures.                                                                                                                 |
| key_encipherment_usage   | optional   | When the subject public key is used for enciphering private or secret keys.                                                                                                       |
| data_encipherment_usage  | optional   | When the subject public key is used for directly enciphering raw user data without the use of an intermediate symmetric cipher.                                                   |
| key_agreement_usage      | optional   | When the subject public key is used for key agreement.                                                                                                                            |
| key_cert_sign_usage      | optional   | When the subject public key is used for verifying signatures on public key certificates.                                                                                          |
| crl_sign_usage           | optional   | When the subject public key is used for verifying signatures on certificate revocation lists.                                                                                     |
| encipher_only_usage      | optional   | When the encipherOnly bit is asserted and the keyAgreement bit is also set, the subject public key may be used only for enciphering data while performing key agreement.          |
| decipher_only_usage      | optional   | When the decipherOnly bit is asserted and the keyAgreement bit is also set, the subject public key may be used only for deciphering data while performing key agreement.          |
| extended_key_usage       | optional   | Indicates the purpose of the public key contained in the certificate. It contains a list of OIDs, each of which indicates an allowed use.                                         |
| vendor                   | optional   | The name of the vendor that provided this certificate.                                                                                                                            |
| groups                   | optional   | Use only if Multitenancy is on. List of groups separated by commas. Each group has name followed by colon followed by yes or no for change_permission, eg. CorpUS:yes, CorpNY:no. |

# Passwords

## Get all Password Accounts

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/passwords/ --insecure
```

> The above command returns results like this:

```json
{
  "Passwords": [
    {
      "category": "Service",
      "custom_fields": [],
      "first_added": "2013-09-20T19:36:21.585Z",
      "id": 4,
      "label": "Patch Management Service Account",
      "last_pw_change": "2014-03-18T13:03:04Z",
      "notes": "",
      "password": "",
      "username": "usnhctpatchmanager",
      "view_edit_groups": "",
      "view_edit_users": "admin",
      "view_groups": "sys_eng",
      "view_users": "Ford Prefect"
    }
  ]
}
```

Retrieve all accounts (usernames) and other information.

**Passwords will not be retrieved.**

**`GET /api/1.0/passwords/`**

## Get Usernames and Passwords

```shell
curl -X GET -u'admin:adm!d42' https://yourdevice42address/api/1.0/passwords/?username=sharepointadmin&plain_text=yes
```

> The above command returns results like this:

```json
{
  "Passwords": [
    {
      "username": "sharepointadmin",
      "last_pw_change": "2013-11-05T19:29:35Z",
      "notes": "",
      "label": "sharepoint admin account",
      "first_added": "2013-11-05T19:07:05.534Z",
      "password": "L8tirgcd&Loh",
      "id": 5
    }
  ]
}
```

Retrieve accounts (usernames) and passwords.

**`GET /api/1.0/passwords/`**

### Parameters

| Parameter  | Description                                                                                                    |
| ---------- | -------------------------------------------------------------------------------------------------------------- |
| category   | Retrieve all passwords for a specified category. ?category= (added in 10.1.0)                                  |
| label      | Retrieve all the passwords for a specified username. ?label=                                                   |
| username   | Retrieve all the passwords for a specified username. ?username=                                                |
| device     | Retrieve all the passwords for a specified device name. ?device=                                               |
| appcomp    | Retrieve all the passwords for a specified application component name. ?appcomp=                               |
| id         | Retrieve all the passwords for a specified [GET password id](#get-all-password-accounts). ?id=                 |
| plain_text | Decrypt the password and return the plain text version. ?plain_text=yes will decrypt and display the password. |

## Create / Update Passwords

```shell
curl -X POST -d "username=adminmh&password=adminmh1234&label=alabel&devices=nhctws001,nhctws002" -u'admin:adm!d42'https://192.168.1.126/api/1.0/passwords/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": "Password added with username adminmh (and label alabel)", "code": 0 }
```

Create / Update Passwords

**`POST /api/1.0/passwords/`**

### Parameters

| Parameter             | Use                                                                                  | Description                                                                                |
| --------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------ |
| username and password | required                                                                             | Create a new password.                                                                     |
| id                    | optional                                                                             | Required to modify existing password. [GET password id](#get-all-password-accounts).       |
| username              | optional                                                                             | The name for the password (often the username is used but this can be any name).           |
| password              | optional                                                                             | The password.                                                                              |
| label                 | optional                                                                             | An arbitrary label.                                                                        |
| category              | optional                                                                             | A password category. If it doesn't exist a new one will be created.                        |
| devices               | optional                                                                             | A comma separated list of device names.                                                    |
| appcomps              | optional                                                                             | A comma separated list of application component names.                                     |
| notes                 | optional                                                                             |
| view_users            | optional                                                                             | A comma separated list of users that have permission to view this password.                |
| view_edit_users       | A comma separated list of users that have permission to view and edit this password. |
| view_groups           | optional                                                                             | A comma separated list of user groups that have permission to view this password.          |
| view_edit_users       | optional                                                                             | A comma separated list of user groups that have permission to view and edit this password. |

## Delete Password

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/passwords/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the password with the password id supplied as the required argument.
Note: You will only be able to delete the password if the supplied username has the correct permissions.

**`DELETE /api/1.0/passwords/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | password id |

## Custom Fields

```shell
curl -X POST -d "name=Skynet&key=illbe&value=back" -u'admin:adm!d42' https://yourdevice42address/api/1.0/custom_fields/password/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["custom key pair values added or updated", 4, "Skynet"], "code": 0 }
```

Create / Update Custom Fields.

**`POST /api/1.0/custom_fields/password/`**

### Parameters

| Parameter          | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| username           | required                        | Name of the password.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| key                | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type               | optional                        | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value              | optional                        |
| notes              | optional                        |
| clear_value        | optional                        | yes to clear existing value for that field                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| bulk_fields        | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2                                                                                                                                                                                                                                                                                                                                                                                             |

<aside class="notice">
value2 has been deprecated since v6.1.0
</aside>

# Customers

## Get all Customers

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/customers/ --insecure
```

> The above command returns results like this:

```json
{
  "Customers": [
    {
      "Contacts": [
        {
          "address": "123 main st",
          "email": "rick@d42.com",
          "name": "Random Guy",
          "phone": "555-555-5555",
          "type": "Technical"
        }
      ],
      "Custom Fields": [
        {
          "key": "custID",
          "notes": null,
          "value": "42",
          "value2": null
        }
      ],
      "contact_info": "555 Technical Lane, Cool City.",
      "devices_url": "/api/1.0/devices/customer_id/1/",
      "groups": "Prod_East:no, Corp:yes",
      "id": 1,
      "name": "ABC, Inc.",
      "notes": "some notes here",
      "subnets_url": "/api/1.0/subnets/customer_id/1/"
    },
    {
      "Contacts": [],
      "Custom Fields": [],
      "contact_info": "124124 wewer",
      "devices_url": "/api/1.0/devices/customer_id/6/",
      "groups": "Prod_East:no, Corp:yes",
      "id": 6,
      "name": "NEF",
      "notes": null,
      "subnets_url": "/api/1.0/subnets/customer_id/6/"
    },
    {
      "Contacts": [],
      "Custom Fields": [],
      "contact_info": null,
      "devices_url": "/api/1.0/devices/customer_id/2/",
      "groups": "Prod_East:no, Corp:yes",
      "id": 2,
      "name": "Networking Team",
      "notes": null,
      "subnets_url": "/api/1.0/subnets/customer_id/2/"
    },
    {
      "Contacts": [],
      "Custom Fields": [],
      "contact_info": "",
      "devices_url": "/api/1.0/devices/customer_id/8/",
      "groups": "Prod_East:no, Corp:yes",
      "id": 8,
      "name": "R&D",
      "notes": "",
      "subnets_url": "/api/1.0/subnets/customer_id/8/"
    },
    {
      "Contacts": [],
      "Custom Fields": [],
      "contact_info": "",
      "devices_url": "/api/1.0/devices/customer_id/13/",
      "groups": "Prod_East:no, Corp:yes",
      "id": 13,
      "name": "Sirius Cybernetics Corp",
      "notes": "",
      "subnets_url": "/api/1.0/subnets/customer_id/13/"
    },
    {
      "Contacts": [
        {
          "address": "",
          "email": "",
          "name": "Tony Stark",
          "phone": "",
          "type": "Technical"
        }
      ],
      "Custom Fields": [],
      "contact_info": "",
      "devices_url": "/api/1.0/devices/customer_id/12/",
      "groups": "Prod_East:no, Corp:yes",
      "id": 12,
      "name": "Stark Industries",
      "notes": "",
      "subnets_url": "/api/1.0/subnets/customer_id/12/"
    }
  ]
}
```

Get all Customers

**`GET /api/1.0/customers/`**

### Parameters

| Parameter    | Use      | Description                                |
| ------------ | -------- | ------------------------------------------ |
| include_cols | optional | used to specify columns returned in output |

## Create / Update Customers

```shell
curl -X POST -d"name=Skynet&notes=badnews" -u'admin:adm!nd42' https://yourdevice42address/api/1.0/customers/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["Customer added or updated.", 4, "Skynet", true, false], "code": 0 }
```

Create / Update Customers.

**`POST /api/1.0/customers/`**

### Parameters

| Parameter    | Use      | Description                                                                                                                                                                                                                                                                                                     |
| ------------ | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name         | required | Customer name.                                                                                                                                                                                                                                                                                                  |
| contact_info | optional | Text field.                                                                                                                                                                                                                                                                                                     |
| notes        | optional |
| type         | optional | customer or department                                                                                                                                                                                                                                                                                          |
| groups       | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. If this parameter is present with no value, all groups are deleted. |

<aside class="notice">
If the name exists, the record will be updated. If not, it will be created.
</aside>

## Delete Customer

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/customers/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the customer with the customer id supplied as the required argument.

**`DELETE /api/1.0/customers/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description   |
| --------- | ------------- |
| id        | IP Address id |

## Create / Update Customer Contacts

```shell
curl -X POST -d "name=mickeymouse&phone=555-555-5555&address=123 main st&type=Technical&customer=skynet&email=rick@d42.com" -u 'admin:adm!nd42' https://192.168.87.7/api/1.0/customers/contacts/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "customer contact record added/updated successfully",
    5,
    "mickeymouse"
  ],
  "code": 0
}
```

Create / Update Customer Contacts
**`POST /api/1.0/customers/contacts/`**

### Parameters

| Parameter | Use      | Description                       |
| --------- | -------- | --------------------------------- |
| name      | required | Customer contact name.            |
| type      | required | Contact type, must already exist. |
| customer  | required | Customer name.                    |
| email     | optional | Text field.                       |
| phone     | optional | Text field.                       |
| address   | optional | Text field.                       |

## Custom Fields

```shell
curl -X PUT -d "name=Skynet&key=illbe&value=back" -u'admin:adm!d42' https://yourdevice42address/api/1.0/custom_fields/customer/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["custom key pair values added or updated", 4, "Skynet"], "code": 0 }
```

Create / Update Custom Fields.

**`PUT /api/1.0/custom_fields/customer/`**

### Parameters

| Parameter          | Use                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| name               | required                        | Name of name of the customer.                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| key                | required                        | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type               | optional                        | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name | required for type=related_field | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value              | optional                        |
| notes              | optional                        |
| clear_value        | optional                        | yes to clear existing value for that field                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| bulk_fields        | optional                        | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2                                                                                                                                                                                                                                                                                                                                                                                             |

<aside class="notice">
value2 has been deprecated since v6.1.0
</aside>

# Vendors

## Get all Vendors

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vendors/ --insecure
```

> The above command returns results like this:

```json
{
  "vendors": [
    {
      "account_no": "1234",
      "contact_info": 8004553432,
      "escalation_1": no,
      "escalation_2": no,
      "groups": "Prod_East:no, Corp:yes",
      "home_page": www.acer.com,
      "name": "Acer Incoporated",
      "notes": acer,
      "vendor_id": 10
    },
    {
      "account_no": "",
      "contact_info": "",
      "escalation_1": "",
      "escalation_2": "",
      "groups": "Prod_East:no, Corp:yes",
      "home_page": "",
      "name": "CDW",
      "notes": "test",
      "vendor_id": 39
    }
  ]
}
```

Get all Vendors

**`GET /api/1.0/vendors/`**

## Create / Update Vendors

```shell
curl -X POST -d "name=device42 llc&home_page=http://example.com/&contact_info=support@device42.com&escalation_1=+12037796342&notes=awesome"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vendors/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["Vendor added or updated.", 39, "CDW", false, false], "code": 0 }
```

Create / Update Vendors.

**`POST /api/1.0/vendors/`**

### Parameters

| Parameter    | Use      | Description                                                                                                                                                                                                                                                                                                     |
| ------------ | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name         | required | vendor name.                                                                                                                                                                                                                                                                                                    |
| account_no   | optional |
| home_page    | optional |
| contact_info | optional |
| escalation_1 | optional |
| escalation_2 | optional |
| notes        | optional |
| groups       | optional | If multitenancy is on, admin groups that have access to this object are specified here, e.g. Prod_East:no,Corp:yes specifies that the admin groups for this object are Prod_East with view only permission and Corp with change permission. If this parameter is present with no value, all groups are deleted. |

## Delete Vendor

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/vendors/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the vendor with the vendor id supplied as the required argument.

**`DELETE /api/1.0/vendors/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description   |
| --------- | ------------- |
| id        | IP Address id |

# Telco Circuits

## Get all Circuits

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/circuits/ --insecure
```

> The above command returns results like this:

```json
{
    "circuits": [
        {
            "ID": 1,
            "bandwidth": "",
            "circuit_id": "L3-01",
            "custom_fields": [
                {
                    "key": "Region",
                    "notes": "",
                    "value": "APAC"
                },
                {
                    "key": "Disconnect Date",
                    "notes": "",
                    "value": ""
                },
                {
                    "key": "Phone",
                    "notes": "",
                    "value": ""
                },
                {
                    "key": "Email",
                    "notes": "",
                    "value": ""
                }
            ],
            "customer": "ABC Inc.",
            "dmarc": "DM100",
            "dmarc_id": 56,
            "end_point_id": "124",
            "end_point_type": "patch_panel_port",
            "notes": "",
            "order_date": null,
            "origin_id": "",
            "origin_type": "",
            "provision_date": null,
            "turn_on_date": null,
            "type": "Dedicated Ethernet",
            "vendor": ""
        }, (truncated)
```

Get all Circuits

**`GET /api/1.0/circuits/`**

### Values

| Value                      | Description                                                                                   |
| -------------------------- | --------------------------------------------------------------------------------------------- |
| ID                         | ID for the cricuit (D42 ID)                                                                   |
| circuit_id                 | circuit id                                                                                    |
| type                       |
| order_date                 |
| provision_date             |
| turn_on_date               |
| notes                      |
| bandwidth                  | bandwidth in kbps (mutiply factor of 1024)                                                    |
| vendor                     | name of the vendor                                                                            |
| customer                   | customer name                                                                                 |
| dramc_id                   | ID of the DMARC                                                                               |
| dmarc                      | DMARC name                                                                                    |
| origin_type                | Type of origin point. Could be device, circuit, switchport, vendor or patch_panel_port        |
| origin_id                  | ID of the origin point                                                                        |
| origin_device              | if origin type is device, this is name of the device. Absent otherwise                        |
| origin_circuit_id          | if origin type is circuit, this is circuit_id of the circuit. Absent otherwise                |
| origin_switchport          | if origin type is switchport, this is name of the switch port. Absent otherwise               |
| origin_switch              | if origin type is switchport, this is name of the switch. Absent otherwise                    |
| origin_patch_panel_port    | if origin type is patch_panel_port, this is name of the patch panel port. Absent otherwise    |
| origin_patch_panel         | if origin type is patch_panel_port, this is name of the patch panel. Absent otherwise         |
| origin_patch_panel_id      | if origin type is patch_panel_port, this is ID of the patch panel. Absent otherwise           |
| origin_vendor              | if origin type is vendor, this is name of the vendor. Absent otherwise                        |
| end_point_type             | Type of end point. Could be device, circuit, switchport, vendor or patch_panel_port           |
| end_point_id               | ID of the end point                                                                           |
| end_point_device           | if end_point type is device, this is name of the device. Absent otherwise                     |
| end_point_circuit_id       | if end_point type is circuit, this is circuit_id of the circuit. Absent otherwise             |
| end_point_switchport       | if end_point type is switchport, this is name of the switch port. Absent otherwise            |
| end_point_switch           | if end_point type is switchport, this is name of the switch. Absent otherwise                 |
| end_point_patch_panel_port | if end_point type is patch_panel_port, this is name of the patch panel port. Absent otherwise |
| end_point_patch_panel      | if end_point type is patch_panel_port, this is name of the patch panel. Absent otherwise      |
| end_point_patch_panel_id   | if end_point type is patch_panel_port, this is ID of the patch panel. Absent otherwise        |
| end_point_vendor           | if end_point type is vendofr, this is name of the vendor. Absent otherwise                    |

## Create / Update Circuits

```shell
curl -X POST -d "circuit_id=chicago-main&type=internet&bandwidth=51200&vendor=L3&origin_type=circuit&origin_id=42&end_point_device=881 router"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/circuits/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["Circuit added/updated", 49, "chicago-main", true, true], "code": 0 }
```

Create / Update Circuits.

**`POST /api/1.0/circuits/`**

### Parameters

| Parameter                  | Use                                | Description                                                                                                                |
| -------------------------- | ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| ID                         | required if circuit_id not present | ID for the cricuit (D42 ID)                                                                                                |
| circuit_id                 | required if ID not present         | circuit id                                                                                                                 |
| type                       | required for new                   |
| order_date                 | optional                           |
| provision_date             | optional                           |
| turn_on_date               | optional                           |
| notes                      | optional                           |
| bandwidth                  | optional                           | bandwidth in kbps (mutiply factor of 1024)                                                                                 |
| vendor                     | optional                           | name of the vendor                                                                                                         |
| customer                   | optional                           | customer name                                                                                                              |
| dramc_id                   | optional                           | ID of the DMARC                                                                                                            |
| dmarc                      | optional                           | DMARC name                                                                                                                 |
| origin_type                | optional                           | Type of origin point. Could be device, circuit, switchport or patch_panel_port. Don't use if using named objects as below. |
| origin_id                  | optional                           | ID of the origin point. You can use origin_type and origin_id combination or named objects as below.                       |
| origin_device              | optional                           | if origin type is device, this is name of the device.                                                                      |
| origin_circuit_id          | optional                           | if origin type is circuit, this is circuit_id of the circuit.                                                              |
| origin_switchport          | optional                           | if origin type is switchport, this is name of the switch port.                                                             |
| origin_switch              | optional                           | if origin type is switchport, this is name of the switch.                                                                  |
| origin_patch_panel_port    | optional                           | if origin type is patch_panel_port, this is name of the patch panel port.                                                  |
| origin_patch_panel_id      | optional                           | if origin type is patch_panel_port, this is ID of the patch panel.                                                         |
| end_point_type             | optional                           | Type of end point. Could be device, circuit, switchport or patch_panel_port. Don't use if using named objects as below.    |
| end_point_id               | optional                           | ID of the end point. You can use end_point_type and end_point_id combination or named objects as below.                    |
| end_point_device           | optional                           | if end_point type is device, this is name of the device.                                                                   |
| end_point_circuit_id       | optional                           | if end_point type is circuit, this is circuit_id of the circuit.                                                           |
| end_point_switchport       | optional                           | if end_point type is switchport, this is name of the switch port.                                                          |
| end_point_switch           | optional                           | if end_point type is switchport, this is name of the switch.                                                               |
| end_point_patch_panel_port | optional                           | if end_point type is patch_panel_port, this is name of the patch panel port.                                               |
| end_point_patch_panel_id   | optional                           | if end_point type is patch_panel_port, this is ID of the patch panel.                                                      |

## Delete Circuit

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/circuits/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the circuit with the circuit id supplied as the required argument.

**`DELETE /api/1.0/circuits/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | circuit id  |

## Custom Fields

```shell
curl -X PUT -d "value=42&key=rating&circuit_id=chicago-main" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/custom_fields/circuit/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "msg": [
    "custom key pair values added or updated",
    1,
    "chicago-main",
    true,
    true
  ],
  "code": 0
}
```

Create/update custom fields for circuits. (Added in v5.9.3)

**`PUT /api/1.0/custom_fields/circuit/`**

### Parameters

| Parameter          | Use                                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| circuit_id         | required, if no ID provided         | circuit_id of the crcuit                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| id                 | required, if no circuit_id provided | D42 ID for that circuit                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| key                | required                            | Can be new or existing. This is the custom field name.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| type               | optional                            | this is the custom field type. If left blank, default is text. Additional options: number,date(text formatted yyyy-mm-dd), related_field(with related_field_name), boolean or url                                                                                                                                                                                                                                                                                                                |
| related_field_name | required for type=related_field     | The existing field to relate this custom field to. Can be: appcomp (for application components), asset, building, certificate, circuit, cusotmer, device, endusers, hardware (for device hardware models), ip_address, natip, netport (for ports), os, part, partmodel, password, pdu (for power units), pdu_model (for power unit models), ports, purchase, purchaselineitem (for a line item on a purchase order), rack, room, software, vlan (for subnets), switch_vlan (for vlans), vrfgroup |
| value              | optional                            |
| notes              | optional                            |
| clear_value        | optional                            | yes to clear existing value for that field (added in v6.1.0)                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| bulk_fields        | optional                            | comma separated key value pairs, with key and value separated by colon. e.g.key1:value1, key2:value2 (added in v6.4.1)                                                                                                                                                                                                                                                                                                                                                                           |

<aside class="notice">
value2 has been deprecated since v6.1.0
</aside>

# Power Circuits

## Get All Power Circuits

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/powercircuits/ --insecure
```

> The above command will return results like this:

```json
{
  "power_circuits": [
    {
      "amps": null,
      "asset_connections": [],
      "bcpm": null,
      "bcpm_id": null,
      "breakerpanel": "Primary Breaker Panel",
      "breakerpanel_id": 10,
      "connection": null,
      "device_connections": [],
      "label": "Testing Circuits",
      "number": "Main Power Circuit",
      "power_circuit_id": 85,
      "powerunit_connections": [],
      "voltage": null
    }
  ],
  "total_count": 1
}
```

Retrieve information about all power circuits.

**`GET /api/1.0/powercircuits`**

### Query String Parameters

| Parameter       | Description                   |
| --------------- | ----------------------------- |
| breakerpanel_id | Breaker panel ID              |
| bcpm_id         | Branch Circuit Power Meter ID |

## Create/Update Power Circuit

```shell
curl -X POST -d "breakerpanel_id=10&number=4&label=Server Room" -u 'user:password' https://d42url/api/1.0/powercircuits/ --insecure | python -m json.tool
```

> The above command will return results like this:

```json
{
  "code": 0,
  "msg": ["power circuit added/updated", 89, "Server Room/4", true, true]
}
```

Create or update Circuits

**`POST /api/1.0/powercircuits/`**

### Parameters

| Parameter                | Use      | Description                                                      |
| ------------------------ | -------- | ---------------------------------------------------------------- |
| breakerpanel_id          | required | ID of the breaker panel for the circuit                          |
| number                   | required | Number of the circuit on the breaker panel                       |
| label                    | optional | Label of the circuit on the breaker panel                        |
| connection               | optional | Description of the connection for the circuit                    |
| voltage                  | optional | The rated voltage on this circuit                                |
| amps                     | optional | The rated amps on this circuit                                   |
| powerunit_connection_ids | optional | A comma-separated list of PU IDs connected to this circuit       |
| device_connection_ids    | optional | A comma-separated list of device IDs connected to this circuit   |
| device_connection_names  | optional | A comma-separated list of device names connected to this circuit |
| asset_connection_ids     | optional | A comma-separated list of asset IDs connected to this circuit    |

## Delete Power Circuit

```shell
curl -X DELETE -d "id=89" -u 'admin:password' https://3.141.592.65/api/1.0/powercircuits/89/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "deleted": "true",
  "id": "89"
}
```

This API is used to delete the power circuit with the ID supplied as the required argument.

**`DELETE /api/1.0/powercircuits/<ID>/`**

### Required Parameters

| Parameter | Description      |
| --------- | ---------------- |
| id        | Power circuit ID |

# Service Levels

## Get all Service Levels

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/service_level/ --insecure
```

> The above command returns results like this:

```json
[
  {
    "id": 1,
    "name": "Production"
  },
  {
    "id": 2,
    "name": "QA"
  },
  {
    "id": 3,
    "name": "Development"
  },
  {
    "id": 4,
    "name": "Test"
  }
]
```

Get all Service Levels (devices)

**`GET /api/1.0/service_level/`**

## Create Service Level

```shell
curl -X POST  -d "name=Decommissioned"  -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/service_level/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["Service level added", 11, "Decommissioned", true, true], "code": 0 }
```

Create Service Level.

**`POST /api/1.0/service_level/`** (Added in v5.8.1)

### Parameters

| Parameter | Use      | Description               |
| --------- | -------- | ------------------------- |
| name      | required | Name of the Service Level |

# Admin Users/Groups

## Get Admin Users

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/adminusers/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "adminusers": [
    {
      "groups": [],
      "id": 1,
      "username": "admin"
    },
    {
      "groups": ["System Generated Read, Add and Edit", "Test Group"],
      "id": 4,
      "username": "test user"
    },
    {
      "groups": ["System Generated Read, Add, Edit and Delete"],
      "id": 2,
      "username": "greg"
    },
    {
      "groups": [
        "System Generated Read, Add, Edit and Delete",
        "System Generated Read Only"
      ],
      "id": 3,
      "username": "Peter"
    },
    {
      "groups": ["System Generated Read Only"],
      "id": 5,
      "username": "The New Guy"
    }
  ]
}
```

Get all Admin Users

**`GET /api/1.0/admingroups/`**

## Get Admin Groups

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/admingroups/ --insecure
```

> The above command returns JSON structured like this:

```json
{
  "admingroups": [
    {
      "id": 6,
      "members": ["cert"],
      "name": "Cert Group"
    },
    {
      "id": 4,
      "members": ["cert"],
      "name": "System Generated Read, Add and Edit"
    },
    {
      "id": 5,
      "members": ["greg", "test"],
      "name": "System Generated Read, Add, Edit and Delete"
    },
    {
      "id": 2,
      "members": [],
      "name": "System Generated Read and Add"
    },
    {
      "id": 3,
      "members": [],
      "name": "System Generated Read and Edit"
    },
    {
      "id": 1,
      "members": ["test", "vlan"],
      "name": "System Generated Read Only"
    },
    {
      "id": 7,
      "members": [],
      "name": "test group"
    }
  ]
}
```

Create/Update Admin Users

**`POST /api/1.0/adminusers/`**

## Create/Edit Admin Users

```shell
curl -X POST -d "username=peter&password=secretpassword&groups=sysadmins" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/adminusers/
```

> The above command returns JSON structured like this:

```json
{ "msg": ["administrator added or updated", 30, "test", true, true], "code": 0 }
```

Create/Update Admin Users

**`POST /api/1.0/adminusers/`**

### Parameters

| Parameter | Use      | Description                                                                                                                              |
| --------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| username  | required | admin user name                                                                                                                          |
| password  | required | new user password                                                                                                                        |
| groups    | optional | Admin groups to assign user to. Note: groups with commas need to be wrapped in quotes. ie: "System Generated Read, Add, Edit and Delete" |

# End Users

## Get End Users

```shell
curl -X GET -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/endusers/
```

> The above command returns JSON structured like this:

```json
{
  "values": [
    {
      "domain": internal.dev,
      "name": "Thom Yorke",
      "notes": "OK Computer",
      "email": "tyorke@somedomain.net",
      "contact": "phone: 203-555-5555",
      "location": "Not Here",
      "adusername": "None",
      "id": 132
    },
    {
      "domain": null,
      "name": "Thomas Jefferson",
      "notes": "Accounting Admin",
      "email": "tjefferson@here.tld",
      "contact": "extension 1776",
      "location": "remote",
      "adusername": "None",
      "id": 133
    }
  ]
}
```

Get all End Users

## Create / Update End Users

```shell
curl -X POST -d "name=Milton&email=milton@initech.com&contact=nobody knows&location=basement&notes=he has the red stapler"-u 'admin:adm!nd42' https://yourdevice42address/api/1.0/endusers/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": ["enduser added or updated", 11, "Milton"], "code": 0 }
```

Create / Update End Users

**`POST /api/1.0/endusers/`**

### Parameters

| Parameter | Use      | Description  |
| --------- | -------- | ------------ |
| name      | required | enduser name |
| email     | optional |
| contact   | optional |
| location  | optional |
| notes     | optional |

## Delete End User

```shell
curl -X DELETE -d "id=114" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/endusers/<ID>/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "deleted": "true", "id": "114" }
```

This API is used to delete the end user with the end user id supplied as the required argument.

**`DELETE /api/1.0/endusers/<ID>/`** (Added in v6.3.4)

### Required Parameter

| Parameter | Description |
| --------- | ----------- |
| id        | end user id |

# Reports

## Run a report and retrieve results

```shell
curl -X GET -u  'admin:adm!nd42' https://yourdevice42address/api/1.0/reports/id/56/?csv_format=xls --insecure
```

> The above command returns JSON structured like this:

```json
{
  "file_url":
    "/var/www/graphics/images/temp/report-201523February0521AM528423.txt"
}
```

**`GET /api/1.0/reports/id/`**

### Option Parameter

| Parameter  | Description                                                                                                                                                  |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| csv_format | If this parameter is 'xls' or is not present, an Excel file(.xlsx) will be produced. If this parameter is 'tab', a tab-delimited file(.txt) will be created. |

<aside class="notice">
The file create by this api call can be retrieved from the Device42 server (at https://youraddress/var/www/grraphics/images/temp/report_name_returned_by_api_call) for 24 hours.  After 24 hours, it will be deleted.
</aside>

# History (Depreciated)

## Get History

```shell
curl -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/history/  --insecure
```

> The above command returns results like this:

```json
[
    {
        "action": "Add/Change(API)",
        "action_time": "2014-03-19T13:09:35.474Z",
        "change_message": "added/edited via api with values: {u'domain': u'd42.net', u'type': u'A', u'nameserver': u'ns1', u'name': u'oxrouter', u'ttl': u'3'}.",
        "content_type": "DNS A or AAAA Record",
        "obj_repr": "oxrouter",
        "user": "admin"
    },
    {
        "action": "Addition",
        "action_time": "2014-03-19T13:08:33.990Z",
        "change_message": "{'name': u'oxrouter', 'date_updated': datetime.datetime(2014, 3, 19, 13, 8, 33, 985769, tzinfo=<UTC>), 'dns_zone_id': 5, 'date_added': datetime.datetime(2014, 3, 19, 13, 8, 33, 985736, tzinfo=<UTC>), 'type': u'A', 'id': 37}",
        "content_type": "DNS A or AAAA Record",
        "obj_repr": "oxrouter",
        "user": "admin"
    },
    {
        "action": "Addition",
        "action_time": "2014-03-19T13:07:48.032Z",
        "change_message": "{'date_updated': datetime.datetime(2014, 3, 19, 13, 7, 48, 22468, tzinfo=<UTC>), 'date_added': datetime.datetime(2014, 3, 19, 13, 7, 48, 22431, tzinfo=<UTC>), 'nameserver': u'ns1', 'id': 5, 'name': u'd42.net'}",
        "content_type": "DNS Zone",
        "obj_repr": "d42.net - ns1",
        "user": "admin"
    },
    {
        "action": "Add/Change(API)",
        "action_time": "2014-03-19T13:06:14.693Z",
        "change_message": "added/edited via api with values: {u'content': u'CHANGE', u'domain': u'OXCT.D42', u'type': u'A', u'nameserver': u'ns1.oxct.d42', u'name': u'OXCTGW'}.",
        "content_type": "DNS A or AAAA Record",
        "obj_repr": "OXCTGW",
        "user": "admin"
    },
```

Retrieves all history for the past one week. (Note, as of 10.3.0 Device42 now has a more robust audit log available at /api/1.0/auditlogs/

**`GET /api/1.0/history/`**

## Get History by # of Weeks

```shell
url -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/history/6/  --insecure
```

> The above command returns results like this:

```json
    {
        "action": "Add/Change(API)",
        "action_time": "2014-03-05T17:35:57.424Z",
        "change_message": "added/edited via api with values: {u'count': u'6', u'notes': u'Bin Phase 2 inv shelf 1- Shelf 2', u'connectivity': u'PCI', u'device': u'dummy', u'model_no': u'Dell DRAC PCI', u'type': u'Riser Card', u'manufacturer': u'Dell'}.",
        "content_type": "Additional Hardware Component",
        "obj_repr": "Riser Card, Dell DRAC PCI",
        "user": "admin"
    },
    {
        "action": "Add/Change(API)",
        "action_time": "2014-03-05T17:31:00.163Z",
        "change_message": "added/edited via api with values: {u'count': u'6', u'connectivity': u'PCI', u'location': u'Bin Phase 2 inv shelf 1- Shelf 2', u'device': u'dummy', u'model_no': u'Dell DRAC PCI', u'type': u'Riser Card', u'manufacturer': u'Dell'}.",
        "content_type": "Additional Hardware Component",
        "obj_repr": "Riser Card, Dell DRAC PCI",
        "user": "admin"
    },
    {
        "action": "Add/Change(API)",
        "action_time": "2014-03-05T17:28:59.541Z",
        "change_message": "added/edited via api with values: {u'count': u'6', u'connectivity': u'PCI', u'location': u'Bin Phase 2 inv shelf 1- Shelf 2', u'device': u'dummy', u'model_no': u'Dell DRAC PCI', u'type': u'Riser Card', u'manufacturer': u'Dell'}.",
        "content_type": "Additional Hardware Component",
        "obj_repr": "Riser Card, Dell DRAC PCI",
        "user": "admin"
    },
    {
        "action": "Addition",
        "action_time": "2014-03-05T17:28:24.365Z",
        "change_message": "{'name': u'Dummy', 'type_id': 2, 'in_service': True, 'virtual_subtype': 1, 'service_level_id': 1, 'last_edited': datetime.datetime(2014, 3, 5, 17, 28, 24, 346448, tzinfo=<UTC>), 'id': 323}",
        "content_type": "Device",
        "obj_repr": "Dummy",
        "user": "admin"
    },
    {
        "action": "Change",
        "action_time": "2014-03-05T15:54:29.005Z",
        "change_message": "Changed notes to key.",
        "content_type": "Device",
        "obj_repr": "D42DEMODEVICE3",
        "user": "admin"
    },
    {
        "action": "Change",
        "action_time": "2014-03-05T15:51:07.748Z",
        "change_message": "Changed name to USNHCC-1-1478.",
        "content_type": "Device",
        "obj_repr": "USNHCC-1-1478",
        "user": "admin"
    },
    {
        "action": "Deletion",
        "action_time": "2014-03-05T15:50:28.027Z",
        "change_message": "",
        "content_type": "Device",
        "obj_repr": "BATS-test-01",
        "user": "admin"
    }
]
```

Retrieves history for specified number of weeks in the past.

**`GET /api/1.0/history/<number of weeks>/`**

# Audit Log

## Get Audit Logs

```shell
curl -u 'user:samsonite' https://theurlattheendoftheuniverse/api/1.0/auditlogs/ --insecure
```

> The above command returns results like this:

```json
{
            "action": "Update",
            "action_time": "2016-07-31T03:59:28.190Z",
            "api_data": "",
            "changed_fields": {
                "switch_vlan_id": "9"
            },
            "from": "Task",
            "object_fields": {
                "device_id": null,
                "first_added": "2016-05-26 14:20:00.213402+00",
                "id": "889",
                "mac_address": "00:13:60:ec:ec:05",
                "port_id": "47",
                "port_name": "",
                "switch_vlan_id": "11"
            },
            "object_id": 889,
            "object_type": "MAC Address",
            "url": "Background Task",
            "user": "SnmpDiscovery"
        }, (truncated)
```

Retrieves the audit history for all changes made in Device42.

**`GET /api/1.0/auditlogs/`**

### Option Parameters

| Parameter      | Description                                                                                                                                       |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| object_id      | Filters by object ID (ie, device ID, asset ID)                                                                                                    |
| content_type   | Returns changes done to a particular content type                                                                                                 |
| action_time_gt | Filters actions that have happened past the time entered (ie, greater than 2 weeks) in YYYY-MM-DDTHH:MM:ss.uuuuuu (ie 2016-10-27T13:52:01.213416) |
| action_time_lt | Returns actions within the last X amount of days in YYYY-MM-DDTHH:MM:ss.uuuuuu (ie 2016-10-27T13:52:01.213416)                                    |

# Auto-Discovery

## Get Network Jobs

```shell
curl -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/networks/  --insecure
```

> The above command returns results like this:

```json
{
    "jobs": [
        {
            "end_ip": null,
            "job_id": 54,
            "name": "job54",
            "start_ip": "192.168.11.21"
        },
        {
            "end_ip": "192.168.11.33",
            "job_id": 44,
            "name": "job44",
            "start_ip": "192.168.11.29"
        }, (truncated)
```

Retrieves all auto-discovery jobs for networking devices.

**`GET /api/1.0/auto_discovery/networks/`** (Added in v5.10.0)

## Add or update network job

```shell
curl -X POST -d "name=Create_by_API&snmp_string=sonu1&ip_address=192.168.11.210"-u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/networks/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": "network auto-discovery job added", "code": 0 }
```

**`POST /api/1.0/auto_discovery/networks/`** (Added in v5.10.0)

### Parameters

| Parameter                           | Use                         | Description                                                                                                                                         |
| ----------------------------------- | --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                                | required                    | name of the job                                                                                                                                     |
| ip_address                          | required, if new            | IP Address                                                                                                                                          |
| end_ip_address                      | optional                    | End IP address (added in v8.2.0)                                                                                                                    |
| snmp_string                         | required, if new            |
| snmp_strings                        | optional                    | Can be comma separated list of community strings to use multiple community strings                                                                  |
| snmp_string_ids                     | optional                    | Can be comma separated list of community string IDs to use multiple community strings                                                               |
| schedule_time                       | optional                    | Time in HH:MM format if you want to schedule the job. Note: Must be formatted as text NOT date. For multiple schedules, separate with a slash (/).  |
| schedule_days                       | optional                    | Comma separated days of week, where Monday = 0. e.g. 0,1,2 wil set the job for Mon, Tue and Wed. For multiple schedules, separate with a slash (/). |
| strip_domain_name                   | optional                    | yes to strip domain names (added in v8.3.2)                                                                                                         |
| autodiscover_cdp_devices            | optional                    | yes to enable CDP/LLDP (added in v8.3.2)                                                                                                            |
| use_name_alias_port_descr           | optional                    | yes to use alias for port description during discovery (added in v8.3.2)                                                                            |
| clear_existing_schedule             | optional                    | yes to clear any existing schedules (added in v8.3.2)                                                                                               |
| groups                              | optional                    | name of one or more groups separated by commas (added in v10.4.0)                                                                                   |
| mask_bits                           | optional                    | mask bits (integer only) (added in v10.4.0)                                                                                                         |
| snmp_port                           | optional                    | snmp port (integer only) (added in v10.4.0)                                                                                                         |
| snmp_version                        | optional                    | v1, v2c, or v3 (v2c is default) (added in v10.4.0)                                                                                                  |
| snmp_string_id                      | optional                    | The id of the password for the community string                                                                                                     |
| snmpv3_auth_password_id             | optional                    | The id of the password for the auth password                                                                                                        |
| snmpv3_privacy_protocol_password_id | optional                    | The id of the password for the privacy protocol password                                                                                            |
| snmpv3_user                         | optional                    | name of snmp v3 user (added in v10.4.0)                                                                                                             |
| snmpv3_auth_mode                    | optional                    | one of: noAuthNoPriv, authNoPriv, authPriv (added in v10.4.0)                                                                                       |
| snmpv3_auth_protocol                | optional                    | one of: MD5, SHA (added in v10.4.0)                                                                                                                 |
| snmpv3_auth_password                | optional                    | password (added in v10.4.0)                                                                                                                         |
| snmpv3_privacy_protocol             | optional                    | one of: DES, 3DES, AES, AES128, AES196, AES256 (added in v10.4.0)                                                                                   |
| snmpv3_privacy_protocol_password    | optional                    | password (added in v10.4.0)                                                                                                                         |
| snmpv3_context                      | optional (added in v10.4.0) |
| get_all_switch_ports                | optional                    | yes or no to get all switch ports (added in v10.4.0)                                                                                                |
| delete_older_mac_association_after  | optional                    | number of days (added in v10.4.0)                                                                                                                   |
| delete_switch_port_not_found        | optional                    | yes or no to delete switch ports not found (added in v10.4.0)                                                                                       |
| vlans_to_ignore                     | optional                    | list of vlan ids to ignore separated by commas (added in v10.4.0)                                                                                   |
| port_name_prefix_to_ignore_macs     | optional (added in v10.4.0) |
| debug_level                         | optional                    | one of: No debug, Normal Debug, Extended Debug (added in v10.4.0)                                                                                   |
| hostname_precedence                 | optional                    | yes or no (added in v10.4.0)                                                                                                                        |
| service_level                       | optional                    | name of service level (added in v10.4.0)                                                                                                            |
| skip_vlan_indexing                  | optional                    | yes or no (added in v10.4.0)                                                                                                                        |
| vrfgroup                            | optional                    | name of vrf group for discovered subnets (added in v10.4.0)                                                                                         |
| category                            | optional                    | name of subnet category for discovered subnets (added in v10.4.0)                                                                                   |
| discover_services                   | optional                    | yes or no (added in v10.4.0)                                                                                                                        |

## Execute network job

```shell
curl -X PUT -d "name=Create_by_API&run=yes"-u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/networks/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": "Task is scheduled for running", "code": 0 }
```

Execute network job

**`PUT /api/1.0/auto_discovery/networks/`** (Added in v5.10.0)

### Parameters

| Parameter | Use                    | Description        |
| --------- | ---------------------- | ------------------ |
| name      | required, if no job_id | name of the job    |
| job_id    | required, if no name   | D42 ID for the job |
| run       | required               | yes to start       |

## Add or update blade autodiscovery job

**`POST /api/1.0/auto_discovery/blade_disc/`** (Added in v10.4.0)

### Parameters

| Parameter                               | Use              | Description                                                                                                                                         |
| --------------------------------------- | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                                    | required         | name of the job                                                                                                                                     |
| ip_address                              | required, if new | IP Address                                                                                                                                          |
| end_ip_address                          | optional         | End IP address                                                                                                                                      |
| snmp_string                             | required, if new |
| schedule_time                           | optional         | Time in HH:MM format if you want to schedule the job. Note: Must be formatted as text NOT date. For multiple schedules, separate with a slash (/).  |
| schedule_days                           | optional         | Comma separated days of week, where Monday = 0. e.g. 0,1,2 wil set the job for Mon, Tue and Wed. For multiple schedules, separate with a slash (/). |
| strip_domain_name                       | optional         | yes to strip domain names                                                                                                                           |
| clear_existing_schedule                 | optional         | yes to clear any existing schedules                                                                                                                 |
| groups                                  | optional         | name of one or more groups separated by commas                                                                                                      |
| snmp_port                               | optional         | snmp port (integer only)                                                                                                                            |
| snmp_version                            | optional         | v1, v2c, or v3 (v2c is default)                                                                                                                     |
| snmpv3_user                             | optional         | name of snmp v3 user                                                                                                                                |
| snmpv3_auth_mode                        | optional         | one of: noAuthNoPriv, authNoPriv, authPriv                                                                                                          |
| snmpv3_auth_protocol                    | optional         | one of: MD5, SHA                                                                                                                                    |
| snmpv3_auth_password                    | optional         | password                                                                                                                                            |
| snmpv3_privacy_protocol                 | optional         | one of: DES, 3DES, AES, AES128, AES196, AES256                                                                                                      |
| snmpv3_privacy_protocol_password        | optional         | password                                                                                                                                            |
| snmpv3_context                          | optional         |
| debug_level                             | optional         | one of: No debug, Normal Debug, Extended Debug                                                                                                      |
| hostname_precedence                     | optional         | yes or no                                                                                                                                           |
| service_level                           | optional         | name of service level                                                                                                                               |
| name_to_use_for_newly_discovered_module | optional         | name to use                                                                                                                                         |
| toggle_in_service_on_module_power_state | optional         | yes or no                                                                                                                                           |
| module_not_found                        | optional         | action to take on module not found. One of: Remove Host Association, Change Service Level, Delete Module (default is no action)                     |

## Add or update power autodiscovery job

**`POST /api/1.0/auto_discovery/power_disc/`** (Added in v10.4.0)

### Parameters

| Parameter                               | Use                                   | Description                                                                                                                                         |
| --------------------------------------- | ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                                    | required                              | name of the job                                                                                                                                     |
| appliance                               | required                              | name of the monitoring appliance                                                                                                                    |
| ip_address                              | required, if new                      | IP Address                                                                                                                                          |
| end_ip_address                          | optional                              | End IP address                                                                                                                                      |
| snmp_string                             | required, if new                      |
| schedule_time                           | optional                              | Time in HH:MM format if you want to schedule the job. Note: Must be formatted as text NOT date. For multiple schedules, separate with a slash (/).  |
| schedule_days                           | optional                              | Comma separated days of week, where Monday = 0. e.g. 0,1,2 wil set the job for Mon, Tue and Wed. For multiple schedules, separate with a slash (/). |
| strip_domain_name                       | optional                              | yes to strip domain names                                                                                                                           |
| clear_existing_schedule                 | optional                              | yes to clear any existing schedules                                                                                                                 |
| vrfgroup                                | optional                              | name vrf group for discovered power units                                                                                                           |
| object_category                         | optional                              | category to assign to discovered power units                                                                                                        |
| category                                | optional                              | subnet category to assign to addresses of discovered units                                                                                          |
| groups                                  | optional                              | name of one or more groups separated by commas                                                                                                      |
| snmp_port                               | optional                              | snmp port (integer only)                                                                                                                            |
| snmp_version                            | optional                              | v1, v2c, or v3 (v2c is default)                                                                                                                     |
| snmp_string_id                          | optional                              | The id of the password for the community string                                                                                                     |
| snmpv3_auth_password_id                 | optional                              | The id of the password for the auth password                                                                                                        |
| snmpv3_privacy_protocol_password_id     | optional                              | The id of the password for the privacy protocol password                                                                                            |
| snmpv3_user                             | optional                              | name of snmp v3 user                                                                                                                                |
| snmpv3_auth_mode                        | optional                              | one of: noAuthNoPriv, authNoPriv, authPriv                                                                                                          |
| snmpv3_auth_protocol                    | optional                              | one of: MD5, SHA                                                                                                                                    |
| snmpv3_auth_password                    | optional                              | password                                                                                                                                            |
| snmpv3_privacy_protocol                 | optional                              | one of: DES, 3DES, AES, AES128, AES196, AES256                                                                                                      |
| snmpv3_privacy_protocol_password        | optional                              | password                                                                                                                                            |
| snmpv3_context                          | optional                              |
| hostname_precedence                     | optional                              | yes or no                                                                                                                                           |
| service_level                           | optional                              | name of service level                                                                                                                               |
| name_to_use_for_newly_discovered_module | optional                              | name to use                                                                                                                                         |
| toggle_in_service_on_module_power_state | optional                              | yes or no                                                                                                                                           |
| module_not_found                        | optional                              | action to take on module not found. One of: Remove Host Association, Change Service Level, Delete Module (default is no action)                     |
| name_to_use_for_newly_discovered_pdu    | optional                              | one of: Name discovered by SNMP, Name plus serial number, Name plus serial number plus IP                                                           |
| polling_interval                        | polling interval in minutes (integer) |

## Add or update vServer autodiscovery job

**`POST /api/1.0/auto_discovery/vserver/`** (Added in v11.1.0)

### Parameters

| Parameter                              | Use      | Description                                                                                                                                         |
| -------------------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                                   | required | name of the job                                                                                                                                     |
| server                                 | required | IP or FQDN of target server                                                                                                                         |
| platform                               | required | One of the following: windows, winrm, docker api, docker, nutanix, linux, unix, aix hmc, openvz, lxc, kvm, libvirt, ovirt, redhat, citrix xen       |
| end_ip_address                         | optional | If discovery supports IP address ranges                                                                                                             |
| clear_existing_schedule                | optional | Used to change discovery schedule rather than append to it. "Yes" is accepted value.                                                                |
| schedule_time                          | optional | Time in HH:MM format if you want to schedule the job. Note: Must be formatted as text NOT date. For multiple schedules, separate with a slash (/).  |
| schedule_days                          | optional | Comma separated days of week, where Monday = 0. e.g. 0,1,2 wil set the job for Mon, Tue and Wed. For multiple schedules, separate with a slash (/). |
| groups                                 | optional | name of one or more groups separated by commas                                                                                                      |
| object_category                        | optional | category of discovered vservers and vms                                                                                                             |
| debug_level                            | optional | one of: No debug, Normal Debug, Extended Debug                                                                                                      |
| service_level                          | optional | name of service level                                                                                                                               |
| url_prefix                             | optional | http or https                                                                                                                                       |
| port                                   | optional | specify port                                                                                                                                        |
| url_suffix                             | optional | append suffix to discovery url                                                                                                                      |
| username                               | optional | username to use in discovery                                                                                                                        |
| password                               | optional | password to use in discovery                                                                                                                        |
| strip_domain_suffix                    | optional | yes or no, Strip domain suffix if discovered on VMs or hypervisor                                                                                   |
| prepend_vmhostname                     | optional | yes or no                                                                                                                                           |
| ignore_powered_off                     | optional | yes or no, ignore powered off VMs                                                                                                                   |
| discover_vms                           | optional | yes or no, discover VMs or strictly hypervisors                                                                                                     |
| vm_name_to_use                         | optional | "found from vm tools" or "as named on vserver"                                                                                                      |
| add_multiple_vm_names_as_alias         | optional | add any additional names found as device alias                                                                                                      |
| toggle_service_level_on_vm_power_state | optional |                                                                                                                                                     |
| get_guest_os_info                      | optional |                                                                                                                                                     |
| ignore_guest_uuid                      | optional |                                                                                                                                                     |
| vm_not_found                           | optional | "Remove Host Association", "Change Service Level" or "Delete VM"                                                                                    |
| track_vm_name_change                   | Optional |                                                                                                                                                     |
| hostname_precedence                    | optional |                                                                                                                                                     |
| hostidlist                             | optional |                                                                                                                                                     |
| vm_add_disk                            | optional |                                                                                                                                                     |
| host_allow_duplicate_serials           | optional |                                                                                                                                                     |
| ignore_host_serial                     | optional |                                                                                                                                                     |
| ignore_host_uuid                       | optional |                                                                                                                                                     |
| overwrite_object_categories            | optional |                                                                                                                                                     |
| remote_collector_id                    | optional |                                                                                                                                                     |
| use_service_account                    | optional |                                                                                                                                                     |
| ignore_ipv6                            | optional | yes or no                                                                                                                                           |
| ignore_virt_subtype                    | optional | yes or no                                                                                                                                           |
| device_name_format                     | optional | One of the following: hostname, hostname_plus_domain, hostname_add_domain_alias, hostname_plus_domain_add_hostname_alias                            |
| discover_parts                         | optional |                                                                                                                                                     |
| capture_hosts_file                     | optional |                                                                                                                                                     |
| discover_software                      | optional |                                                                                                                                                     |
| initial_software_type                  | optional | One of the following: managed, unmanaged, prohibited, ignored                                                                                       |
| discover_services                      | optional |                                                                                                                                                     |
| discover_applications                  | optional |                                                                                                                                                     |
| store_config_files                     | optional |                                                                                                                                                     |
| discover_cloudid                       | optional |                                                                                                                                                     |
| customer_id                            | optional |                                                                                                                                                     |
| vrf_group_id                           | optional |                                                                                                                                                     |
| service_level_device_id                | optional |                                                                                                                                                     |
| sudo_retry                             | optional |                                                                                                                                                     |
| alternate_sudo                         | optional |                                                                                                                                                     |
| use_domain_server                      | optional |                                                                                                                                                     |
| ldap_server                            | optional |                                                                                                                                                     |
| use_fqdn                               | optional |                                                                                                                                                     |
| ldap_filter_type                       | optional | One of the following: all, servers, custom, custom_ldap_filter                                                                                      |
| ldap_unpwd_id                          | optional |                                                                                                                                                     |
| alternate_sudo_password_id             | optional |                                                                                                                                                     |
| polling_interval                       | optional | polling interval in minutes (integer)                                                                                                               |
| enable_resources_monitoring            | optional |                                                                                                                                                     |

## Execute VServer job

```shell
curl -X PUT -d "name=Production VCenter&run=yes"-u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/vserver/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": "Task is scheduled for running", "code": 0 }
```

Execute vserver job

**`PUT /api/1.0/auto_discovery/vserver/`**

### Parameters

| Parameter | Use                    | Description        |
| --------- | ---------------------- | ------------------ |
| name      | required, if no job_id | name of the job    |
| job_id    | required, if no name   | D42 ID for the job |
| run       | required               | yes to start       |

## Add or update cloud autodiscovery job

**`POST /api/1.0/auto_discovery/cloud/`** (Added in v11.1.0)

### Parameters

| Parameter                      | Use                 | Description                                                                                                                                         |
| ------------------------------ | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                           | required            | Name of discovery job                                                                                                                               |
| cloud_type                     | required            | Amazon AWS, Microsoft Azure, Outscale, Linode, DigitalOcean, CloudStack, OpenStack, Joyent, or Amazon API                                           |
| secret_key_name                | required\*          | AWS Access key, Azure Management Cert uploaded via UI, Cloudstack Password                                                                          |
| vendor                         | optional            |
| vrfgroup                       | optional            |
| outscale_regions               | optional            | Comma separated region names for Outscale                                                                                                           |
| notes                          | optional            |
| remove_unfound_instances       | optional            |
| strip_domain_name              | optional            |
| add_suffix                     | optional            |
| match_name_only_for_virtuals   | optional            |
| match_name_only_for_hypervisor | optional            |
| groups                         | optional            | name of one or more groups separated by commas                                                                                                      |
| object_category                | optional            | category of discovered vservers and vms                                                                                                             |
| debug_level                    | optional            | one of: No debug, Normal Debug, Extended Debug                                                                                                      |
| clear_existing_schedule        | optional            | Used to change discovery schedule rather than append to it. "Yes" is accepted value.                                                                |
| schedule_time                  | optional            | Time in HH:MM format if you want to schedule the job. Note: Must be formatted as text NOT date. For multiple schedules, separate with a slash (/).  |
| schedule_days                  | optional            | Comma separated days of week, where Monday = 0. e.g. 0,1,2 wil set the job for Mon, Tue and Wed. For multiple schedules, separate with a slash (/). |
| access_key                     | formerly required\* | Required for Amazon - AWS Account ID (Deprecated in 12.0)                                                                                           |

### Amazon AWS Specific Parameters

| Parameter      | Use      | Description                          |
| -------------- | -------- | ------------------------------------ |
| secret_key     | required | Amazon Secret Key                    |
| account_id     | required | Amazon Account ID                    |
| amazon_regions | optional | Comma separated region names for AWS |

### Azure Specific Parameters

| Parameter       | Use      | Description           |
| --------------- | -------- | --------------------- |
| subscription_id | required | Azure subscription ID |

### Openstack Specific Parameters

| Parameter | Use      | Description                   |
| --------- | -------- | ----------------------------- |
| ip        | required | Openstack IP Address          |
| project   | required | Openstack Project Name        |
| tenant    | optional | Project name (OpenStack only) |
| username  | required | Openstack username            |
| password  | required | Openstack password            |

### Cloudstack Specific Parameters

| Parameter | Use      | Description           |
| --------- | -------- | --------------------- |
| url       | required | Cloudstack IP Address |

### Linode Specific Parameters

| Parameter | Use      | Description      |
| --------- | -------- | ---------------- |
| api_token | required | Linode API token |

### Digital Ocean Specific Parameters

| Parameter | Use      | Description             |
| --------- | -------- | ----------------------- |
| token_key | required | Digital Ocean Token Key |

### Joyent Specific Parameters

| Parameter      | Use      | Description |
| -------------- | -------- | ----------- |
| joyent_keyname | optional | Joyent only |

### AWS API Specific Parameters

| Parameter      | Use      | Description      |
| -------------- | -------- | ---------------- |
| aws_api_region | optional | Only for AWS API |

## Add or update other SNMP autodiscovery job

**`POST /api/1.0/auto_discovery/snmp_disc/`** (Added in v10.4.0)

### Parameters

| Parameter                           | Use              | Description                                                                                                                                         |
| ----------------------------------- | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| name                                | required         | name of the job                                                                                                                                     |
| ip_address                          | required, if new | IP Address                                                                                                                                          |
| end_ip_address                      | optional         | End IP address                                                                                                                                      |
| snmp_string                         | required, if new |
| schedule_time                       | optional         | Time in HH:MM format if you want to schedule the job. Note: Must be formatted as text NOT date. For multiple schedules, separate with a slash (/).  |
| schedule_days                       | optional         | Comma separated days of week, where Monday = 0. e.g. 0,1,2 wil set the job for Mon, Tue and Wed. For multiple schedules, separate with a slash (/). |
| strip_domain_name                   | optional         | yes to strip domain names                                                                                                                           |
| clear_existing_schedule             | optional         | yes to clear any existing schedules                                                                                                                 |
| groups                              | optional         | name of one or more groups separated by commas                                                                                                      |
| snmp_port                           | optional         | snmp port (integer only)                                                                                                                            |
| snmp_version                        | optional         | v1, v2c, or v3 (v2c is default)                                                                                                                     |
| snmp_string_id                      | optional         | The id of the password for the community string                                                                                                     |
| snmpv3_auth_password_id             | optional         | The id of the password for the auth password                                                                                                        |
| snmpv3_privacy_protocol_password_id | optional         | The id of the password for the privacy protocol password                                                                                            |
| snmpv3_user                         | optional         | name of snmp v3 user                                                                                                                                |
| snmpv3_auth_mode                    | optional         | one of: noAuthNoPriv, authNoPriv, authPriv                                                                                                          |
| snmpv3_auth_protocol                | optional         | one of: MD5, SHA                                                                                                                                    |
| snmpv3_auth_password                | optional         | password                                                                                                                                            |
| snmpv3_privacy_protocol             | optional         | one of: DES, 3DES, AES, AES128, AES196, AES256                                                                                                      |
| snmpv3_privacy_protocol_password    | optional         | password                                                                                                                                            |
| snmpv3_context                      | optional         |
| debug_level                         | optional         | one of: No debug, Normal Debug, Extended Debug                                                                                                      |
| service_level                       | optional         | name of service level                                                                                                                               |

## Add IPMI/Redfish Auto Discovery job

```shell
curl -X POST -d "zonename=device42.com&nameserver=192.168.11.210"-u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/ipmi/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": "IPMI auto-discovery job added", "code": 0 }
```

Add IPMI/Redfish Auto Discovery

**`POST /api/1.0/auto_discovery/ipmi/`**

### Parameters

| Parameter                   | Use      | Description                                                                                     |
| --------------------------- | -------- | ----------------------------------------------------------------------------------------------- |
| name                        | required | name of the discovery job                                                                       |
| ip_start                    | required | starting IP address                                                                             |
| ip_end                      | required | ending IP address, use same as start for single address                                         |
| bmc_user                    | required | username for discovery                                                                          |
| bmc_password                | required | password for discovery                                                                          |
| hostname_to_use             | required | one of: 1 (Serial # / Reverse DNS / IP) 2 (Discovered Name / Serial # / Reverse DNS / IP)       |
| update_model_if_found       | optional | one of: yes (default) no (do no add hardware model if found)                                    |
| run_as_operator             | optional | yes or no                                                                                       |
| object_category             | optional |
| overwrite_object_categories | optional | yes or no                                                                                       |
| clear_existing_schedule     | optional | defaults to no, yes to clear existing schedules                                                 |
| groups                      | optional |
| debug_level                 | optional | on or off                                                                                       |
| discovery_type              | optional | IPMI or Redfish. IPMI by default                                                                |
| remote_collector_id         | optional |
| schedule_time               | optional | Time in HH:MM format if you want to schedule the job                                            |
| schedule_days               | optional | Comma separated days of week, where Monday = 0. e.g. 0,1,2 wil set the job for Mon, Tue and Wed |

## Execute IPMI/Redfish Auto Discovery Job

```shell
curl -X PUT -d "job_id=2&run=yes"-u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/ipmi/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": "Task is scheduled for running", "code": 0 }
```

Execute DNS job

**`PUT /api/1.0/auto_discovery/ipmi/`**

### Parameters

| Parameter | Use      | Description        |
| --------- | -------- | ------------------ |
| job_id    | required | D42 ID for the job |
| run       | required | yes to start       |

## Get DNS Auto Discovery Jobs

```shell
curl -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/dns/  --insecure
```

> The above command returns results like this:

```json
{
"jobs": [
        {
        "nameserver": "192.168.11.104",
        "zonename": "10.10.in-addr.arpa",
        "job_id": 10,
        "schedule": "M T W Th F S Su 09:44:18 ; M T F S Su 09:44:23 ; "
        },
        {
        "nameserver": "192.168.11.104",
        "zonename": "3.2.1.in-addr.arpa",
        "job_id": 9,
        "schedule": ""
        },....(truncated)
```

Retrieves all auto-discovery jobs for dns.

**`GET /api/1.0/auto_discovery/dns/`** (Added in v7.0.0)

## Add DNS Auto Discovery job

```shell
curl -X POST -d "zonename=device42.com&nameserver=192.168.11.210"-u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/dns/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": "DNS Zone auto-discovery job added", "code": 0 }
```

Add DNS Auto Discovery

**`POST /api/1.0/auto_discovery/dns/`** (Added in v7.0.0)

### Parameters

| Parameter     | Use      | Description                                                                                     |
| ------------- | -------- | ----------------------------------------------------------------------------------------------- |
| zonename      | required | name of the zone                                                                                |
| nameserver    | required | IP/FQDN of the nameserver                                                                       |
| schedule_time | optional | Time in HH:MM format if you want to schedule the job                                            |
| schedule_days | optional | Comma separated days of week, where Monday = 0. e.g. 0,1,2 wil set the job for Mon, Tue and Wed |

## Execute DNS Auto Discovery Job

```shell
curl -X PUT -d "job_id=2&run=yes"-u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/dns/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": "Task is scheduled for running", "code": 0 }
```

Execute DNS job

**`PUT /api/1.0/auto_discovery/dns/`** (Added in v7.0.0)

### Parameters

| Parameter | Use      | Description        |
| --------- | -------- | ------------------ |
| job_id    | required | D42 ID for the job |
| run       | required | yes to start       |

## Get Certificate Auto Discovery Jobs

```shell
curl -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/certificate/  --insecure
```

> The above command returns results like this:

```json
{
  "jobs": [
    {
      "end_ip_address": "10.42.42.100",
      "follow_chain": "no",
      "job_id": 1,
      "name": "D42",
      "ports": "443,",
      "start_ip_address": "10.42.42.0"
    },
    {
      "end_ip_address": "10.42.0.10",
      "follow_chain": "yes",
      "job_id": 2,
      "name": "prod certs",
      "ports": "443,4433",
      "start_ip_address": "10.42.0.1"
    }
  ]
}
```

Retrieves all auto-discovery jobs for dns.

**`GET /api/1.0/auto_discovery/certificate/`** (Added in v7.0.0)

## Add Certificate Auto Discovery job

```shell
curl -X POST -d "name=NAME&start_ip_address=10.0.0.1&end_ip_address=10.0.1.1&ports=443&follow_chain=no&debug_level=Debug Off" -u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/certificate/
```

> The above command returns JSON structured like this:

```json
{ "msg": "Certificate auto-discovery job added", "code": 0 }
```

Add DNS Auto Discovery

**`POST /api/1.0/auto_discovery/dns/`** (Added in v7.0.0)

### Parameters

| Parameter        | Use      | Description                                                                                     |
| ---------------- | -------- | ----------------------------------------------------------------------------------------------- |
| name             | required | name of the discovery job                                                                       |
| start_ip_address | required | beginning of IP address range                                                                   |
| end_ip_address   | required | end of IP address range                                                                         |
| ports            | optional | default is 443, comma separated                                                                 |
| follow_chain     | optional | defaults to no                                                                                  |
| debug_level      | optional | default is off                                                                                  |
| schedule_time    | optional | Time in HH:MM format if you want to schedule the job                                            |
| schedule_days    | optional | Comma separated days of week, where Monday = 0. e.g. 0,1,2 wil set the job for Mon, Tue and Wed |

## Execute Certificate Auto Discovery Job

```shell
curl -X PUT -d "job_id=2&run=yes"-u 'admin:adm!nd42' https://yourdevice42address/api/1.0/auto_discovery/certificate/ --insecure
```

> The above command returns JSON structured like this:

```json
{ "msg": "Task is scheduled for running", "code": 0 }
```

Execute Certificate job

**`PUT /api/1.0/auto_discovery/certificate/`**

### Parameters

| Parameter | Use      | Description                               |
| --------- | -------- | ----------------------------------------- |
| job_id    | required | D42 ID for the job, if name not used      |
| name      | required | name of discovery job, if job_id not used |
| run       | required | yes to start                              |

# Get Healthstatus

```shell
curl  http://yourdevice42address:4242/healthstats/

curl https://d42:4343/healthstats/
```

> The above commands return results like this:

```json
{
  "backup_status": [
    {
      "id": 1,
      "job_name": "Demo Backup",
      "status": "Backup not run since last reboot. Please check."
    }
  ],
  "cpu_used_percent": "2.51904",
  "dbsize": "84 MB",
  "disk_used_percent": "8",
  "memory_in_MB": {
    "buffers": 92,
    "cached": 254,
    "memfree": 1728,
    "memtotal": 3948,
    "swapfree": 8187,
    "swaptotal": 8187
  }
}
```

This will return the health status of Device42 including the status of recent backups.

<aside class="notice">
If your appliance manager is using https, you'll want to point to port 4343 with this call. Otherwise, the Appliance Manager endpoint will be at port 4242.
</aside>
# Backup Schedules

## Set Backup Schedules via API

```shell
curl -X POST -d "name=Daily NFS backup&method=3&schedule_time=23:00&schedule_days=0,1,2,3,4,5,6" -u 'd42admin:default' https://device42url:4343/api/1.0/backup_schedules/ --insecure
```

> The above command returns results like this:

```json
{ "msg": ["backup schedule added/updated", 4, "test3"] }
```

This will create a scheduled backup nightly at 11:00 PM, using the NFS server settings.

### Parameters

| Parameter     | Use      | Description                                                       |
| ------------- | -------- | ----------------------------------------------------------------- |
| name          | required | name of backup job                                                |
| method        | required | Backup method: 1 for mail,2 for sftp,3 for nfs or 4 for amazon s3 |
| schedule_time | required | time to perform backup (0:00 to 23:59)                            |
| schedule_days | optional | days to perform backup (comma separated 0,1,2,3... 0 = Sunday)    |

<aside class="notice">
Note: this API endpoint is sent to the Appliance Manager URL rather than main Device42 URL. That is at http://device42url:4242 without SSL and https://device42url:4343 with SSL. The username is d42admin.
</aside>

## Get Backup Schedules

```shell
curl -X GET -u 'd42admin:default' https://device42url:4343/api/1.0/backup_schedules/ --insecure
```

> The above command returns results like this:

```json
{
  "jobs": [
    {
      "job_id": 1,
      "method": 3,
      "name": "test"
    }
  ]
}
```

<aside class="notice">
Note: this API endpoint is sent to the Appliance Manager URL rather than main Device42 URL. That is at http://device42url:4242 without SSL and https://device42url:4343 with SSL. The username is d42admin.
</aside>

# Set Standby or Production Mode

```shell
curl -X POST -d "appliance_mode=production" -u 'd42admin:default' https://device42url:4343/api/1.0/appliancemode/ --insecure
```

> The above command returns results like this:

```json
{
  "msg": "Appliance Manager standby mode set to True. Background jobs disabled"
}
```

This will set the Device42 appliance to Standby or Production mode. Used in conjunction with automatic backups and restores for a warm HA solution.

### Parameters

| Parameter      | Use      | Description           |
| -------------- | -------- | --------------------- |
| appliance_mode | required | standby or production |

<aside class="notice">
Note: this API endpoint is sent to the Appliance Manager URL rather than main Device42 URL. That is at http://device42url:4242 without SSL and https://device42url:4343 with SSL.
</aside>

# Get all Tags

```shell
curl -X GET -u 'admin:password' 'https://api.device42.com/api/1.0/tags/'
```

> The above command returns results like this:

```json
{
  "limit": 1000,
  "offset": 0,
  "tags": [
    {
      "name": "1 tag"
    },
    {
      "name": "2 tag"
    },
    {
      "name": "red tag"
    },
    {
      "name": "blue tag"
    }
  ],
  "total_count": 30
}
```

This will return a full list of tags available in your Device42 instance.

**`GET /api/1.0/tags/`**

#Error Codes

The Device42 API uses the following error codes:

| Error Code | Meaning                                                                                                            |
| ---------- | ------------------------------------------------------------------------------------------------------------------ |
| 400        | Bad Request -- Your request sucks                                                                                  |
| 401        | Unauthorized -- Your credentials suck                                                                              |
| 403        | Forbidden -- The resource requested is hidden                                                                      |
| 404        | Not Found -- The specified resource could not be found                                                             |
| 405        | Method Not Allowed -- You tried to access a resource with an invalid method                                        |
| 410        | Gone -- The resource requested has been removed from our servers                                                   |
| 500        | Internal Server Error -- Some parameter missing or issue with the server. Check with returned "msg" from the call. |
| 503        | Service Unavailable -- Please check if your Device42 instance is working normally.                                 |
