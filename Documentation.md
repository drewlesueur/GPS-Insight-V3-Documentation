FORMAT: 1A
HOST: https://api.gpsinsight.com/driver/

# GPS Insight V3

Driver-centric API for GPS Insight.

## Group Session

## Authenticate [/session/authenticate/]

### Authenticate Driver [POST /session/authenticate/]

+ Request (application/json)
    The auth_code will be sent to the driver's phone number, if the phone is registered to the right ref_id
    
    + Body
    
            {
                "ref_id": 76,
                "phone_number": 2085678934
            }

+ Response 200

        {
            "success": true
        }

+ Request (application/json)
    If the serial_number has a driver assigned, response can contain a session token, doing the session generation in one step.

    + Headers
    
            Get-Record: true
    + Body
    
            {
                "ref_id": 76,
                "serial_number": 123456789101112
            }

+ Response 200

        {
            "session": <session_token>
        }


### Session from Auth Code [GET /session/authorize/]

    
+ Request (application/json)
    The authcode comes from the Authenticate method, but is not delivered as a response in the API.  It is only delivered to the driver's phone.

    + Body
            {
                "auth_code": "string",
            }

+ Response 200

        {
            "session": <session_token>
        }

### FUTURE: Session Data Refresh [GET /session]

+ Request

    + Headers
    
            Session: <Valid session token>
            
+ Response 200

        {
            "session": {
                "type":  "driver",
                "ref_id":  "driver refid",
                "account_id": "20054",
                "identifier": "phone_number",
                "phone_number": "6025551234",            
            },
            
            "driver": {
                "first_name": "Ned",
                "last_name": "Ryerson",
                "phone_number": "2125551220"
            },
            
            "vehicle": {
                'vehicle': "Vehicle 001",
                'country': "US",
                'license_state': "AZ",
                'license_number': "VEH001",
                'color': "ORNG",
                'make': "MAZDA",
                'model': "3 5D",
                'model_year': "2005",
                'odometer': "85135",            
            },
            
            "permission_data": {}
        }

## Group Messaging

## Messages [/message/]

### Get All Messages [GET /message/{?since}{?until}]

+ Parameters
    
    + since: `2015-08-05T08:40:51` (String,optional) - ISO8601 date
    + until: `2015-08-05T08:40:51` (String,optional) - ISO8601 date
    + output_unix_time: `1` (Boolean,optional) - Resulting times as epoch time

+ Request

    + Headers
    
            Session: <Valid session token>

+ Response 200 (application/json)

        [
            {
                "id": "870h8dfhs9d8fg",
                "timestamp": "2015-08-05T08:40:51",
                "read_dt": null,
                "deleted_dt": null,
                "from": "John Somebody",
                "message": "Some message that is really important."
            }
        ]

### Get a Message [GET /message/{id}]

+ Parameters
    
    + id: `870h8dfhs9d8fg` (String, optional)

+ Request

    + Headers
    
            Session: <Valid session token>

+ Response 200 (application/json)

        [
            {
                "id": "870h8dfhs9d8fg",
                "timestamp": "2015-08-05T08:40:51",
                "from": "John Somebody",
                "message": "Some message that is really important."
            }
        ]

### Send a Message [POST /message]

+ Request (application/json)

    + Headers
    
            Session: <Valid session token>
            Get-Record: false
    
    + Body
    
            {
                "to": "user",
                "message": "A message",
                "from_device": "device_id",
                "created_time": "(optional) defaults to time of request",
                "read_dt": "(optional) automatically set read timestamp to provided time",
                "deleted_dt": "(optional) automatically set deleted timestamp to provided time",
                "parent_message_id": "(optional) relate to existing message",
                "message_type": "(optional) type of message - default 'm'",
            }

+ Response 200 (application/json)

        {
            "id": 123456
        }

+ Request (application/json)

    + Headers
    
            Session: <Valid session token>
            Get-Record: true
    
    + Body
    
            {
                "to": "user",
                "from": "Sum Yung Gai",
                "from_type": "driver",
                "device_type": "mobile",
                "message": "A message"
            }

+ Response 200 (application/json)

        {
            "id": 123456
            "to": "user",
            "from": "Sum Yung Gai",
            "from_type": "driver",
            "device_type": "mobile",
            "message": "A message"
        }


## Message Receipt [/message/receipt/]

### Send Message Receipt [POST /message/receipt/{id}]

+ Parameter

    + id: 1234 (String) - ID of message read

+ Request

    + Headers
    
            Session: <Valid session token>

+ Response 200


### Send Multiple Receipts [POST /message/receipt/]

+ Request

    + Headers
    
            Session: <Valid session token>
            
    + Body
    
            [
                {
                    "id": 123
                },{
                    "id": 124
                },{
                    "id": 125
                }
            ]

+ Response 200

## Group Dispatching

## Dispatch [/dispatch{?since}{?until}]

### Get All Dispatches [GET]

+ Parameters

    + since: `2015-08-05T08:40:51` (String, optional) - ISO8601 date
    + until: `2017-06-15T08:00:00` (String, optional) - ISO8601 date

+ Request

    + Headers
    
            Session: <Valid session token>

+ Response 200 (application/json)

        [
            {
                "id": "<Dispatch ID>",
                "to_type": "driver",
                "to": "Driver001",
                "latitude": 121.191,
                "longitude": -100.231,
                "address": "3210 N Butte St. Bremerton, WA 96753",
                "note": "A really important note.",
                "timestamp": "2015-08-05T08:40:51",
                "read_dt": null,
                "deleted_dt": null
            }
        ]

## Dispatch Receipt [/dispatch/receipt/{id}]

### Send Dispatch Receipt [POST]

+ Parameters

    + id: 1234 (String) - ID of message read

+ Request

    + Headers
    
            Session: <Valid session token>
            
+ Response 200

## FUTURE: Driver Assignment

### FUTURE: Assign Driver [POST /assign]

+ Parameters

    + vin: CA1814003214 (String) - VIN to assign to active driver
    + effective: 2017-01-15T16:40:30-07:00 (String) - ISO8601 date
    + expires (optional): 2017-01-31T23:59:59-07:00 (String, optional) - ISO8601 date

+ Request

    + Headers
    
            Session: <Valid session token>

+ Response 200

        {
            'vehicle': "Vehicle 001",
            'country': "US",
            'license_state': "AZ",
            'license_number': "VEH001",
            'color': "ORNG",
            'make': "MAZDA",
            'model': "3 5D",
            'model_year': "2005",
            'odometer': "85135",            
        }
        
+ Reponse 400
        
        Specified VIN unknown to this account
        

### FUTURE: Unassign Driver [POST /unassign]

+ Parameters

    + vin: CA1814003214 (optional, String) - VIN to assign to active driver
    + expires (optional): 2017-01-31T23:59:59-07:00 (String, optional) - ISO8601 date

+ Request

    + Headers
    
            Session: <Valid session token>

+ Response 200

        {
            "success": true
        }

# DVIRs [/dvir{?limit}]

## Retrieve DVIRs [GET]

Retrieves the list of dvirs

+ Parameters

    + limit (optional, number) ... Maximum number of DVIRs to retreive

+ Request

    + Headers
    
            Session: <Valid session token>

+ Response 200 (application/json)

            {
                "dvir_id": 1,
                "vin": "CA1234567890",
                "defect_code_id": 1,
                "is_trailer": false,
                "trailer_name": "",
                "shipment": "(only for trailers)",
                "safety_status": true,
                "inspection_type": "pre",
                "notes": "example notes",
                "created_by": 130323,
                start_dt: "2017-03-02 22:10:20-07:00",
                end_dt: "2017-03-02 22:20:20-07:00"
                defects: [{
                    "defect_id": 2,
                    "defect_code_id":  1,
                    "name": "odometer",
                    "notes": "the miles aren't coming off",
                    "repaired_by": "Alan",
                    "repaired_dt": "1986-06-11 4:00:00-07:00",
                    "repaired_notes": "fixed",
                    "approved_by": "Matthew",
                    "approved_dt": "1986-06-11 6:00:00-07:00",
                    "approved_notes": "looks good",
                    "priority":  1
                }]
            }

## Create a DVIR [POST]

+ Request (application/json)

    + Headers
    
            Session: <Valid session token>
    
    + Body
    
            {
                "vin": "CA1234567890",
                "defect_code_id": 1,
                "is_trailer": false,
                "trailer_name": "",
                "shipment": "(only for trailers)",
                "safety_status": true,
                "inspection_type": "pre",
                "notes": "example notes",
                "created_by": 130323,
                start_dt: "2017-03-02 22:10:20-07:00",
                end_dt: "2017-03-02 22:20:20-07:00"
                defects: [{
                    "defect_code_id":  1,
                    "name": "odometer",
                    "notes": "the miles aren't coming off",
                    "repaired_by": "Alan",
                    "repaired_dt": "1986-06-11 4:00:00-07:00",
                    "repaired_notes": "fixed",
                    "approved_by": "Matthew",
                    "approved_dt": "1986-06-11 6:00:00-07:00",
                    "approved_notes": "looks good",
                    "priority":  1
                }]
            }

+ Response 200 (application/json)

        {
            "success": true
        }
        
## UPDATE a DVIR Record [/{dvir_id}]

+ Request (application/json)

    + Headers
    
            Session: <Valid session token>
    
    + Body
            {
                "notes": "Adding notes about notes.."
            }

+ Response 200 (application/json)

        {
            "success": true
        }

# DVIR Defect Code [/dvir_defect_code{?code_type}]

## Retrieve DVIR Defect codes [GET]
Retrieves the list of DVIR defect codes

+ Parameters

    + code_type: vehicle (optional, String) - Limit the resulting codes to a particular type

+ Request

    + Headers
    
            Session: <Valid session token>

+ Response 200 (application/json)

            [
                {
                    "defect_code_id": 1,
                    "name": "wheels",
                    "code_type": "vehicle"
                },
                {
                    "defect_code_id": 2,
                    "name": "hood",
                    "code_type": "vehicle"
                },
            ]

## DVIR Defects [{dvir_id}/defect/{dvir_defect_id}]

## Update a Defect Record [POST]

+ Request (application/json)

    + Headers
    
            Session: <Valid session token>
    
    + Body
            {
                "repaired_by": "Johnny Wrench",
                "repaired_dt": "2016-10-01T09:50:00-7:00",
                "repaired_notes": "Just a transmutation of your 110 into your 220."    
            }

+ Response 200 (application/json)

        {
            "success": true
        }
