Introduction
=======

**This API is in active development. If you’d like to receive beta access, please contact Bloom Support**

The Patient Health Record API is an API to retrieve structured health record data from healthcare providers (Covered Entities) on behalf of patients. The API helps organizations identify patient portals and make requests of them. Once the results are retrieved, the results may be fetched from BloomAPI.

Structured digital data is available for health systems that provide patient portals. Supply a patient’s username and password and BloomAPI will fetch structured results from these sources within minutes. BloomAPI currently supports more than 320 different health systems using this methodology.


API Usage
=======

Calling
---------------

BloomAPI is a http-based API. It can be queried at <http://www.bloomapi.com/api/> or at the url of your own deployment.

``` javascript
 Example 200 Response

 curl -XGET https://www.bloomapi.com/api/search/usgov.hhs.npi
 ```
Experimental JSONP is currently supported by providing a `callback` method through the parameter callback.

All responses are JSON objects with the following parameters.

| Name          | Description
| ------------- |:-------------:|
| meta          | Information related to the query such as number or results or warning messages |
| results       | Payload response such as the data being queried for                            |



 ``` javascript
  { "meta":
    {
      "count": 1923
    },
    "result": [
      {
        "npi": 1111111111,
        "type": "individual"
      }
    ]
  }
```


Authentication
---------------
In order to have access to either the development or production Patient Health Record API, a `secret` must be provided as a parameter for every request. In addition, every request must use `https`.

``` javascript
  Example usage of API Key

  curl -XGET https://www.bloomapi.com/api/yourchart/d8c21a1d-2972-46d4-9729-e691bbb6a068?secret=<secret key>
```


Errors
---------------
Errors are communicated through http return codes. Codes that are returned by BloomAPI include.

``` javascript
Example 400 Response
```

400 errors also include a JSON response object explaining the cause of the error.

``` javascript

{
  "error:": "Error message",
  "type": "parameter|fatal",
  "details": {
    "some-key": "An object that represents this error type"
  }
}

```

* 400: User error such as invalid parameters.
* 404: API endpoint or entity not found.
* 5xx: Server error. Likely caused by a bug in BloomAPI.


Endpoints
=======

GET /api/search/bloomapi.epic.mychart_locations
------------

Used to locate supported patient portals.

``` javascript
Get the list of patient portals or search for a patient portal by facility name

curl -XGET https://www.bloomapi.com/api/search/bloomapi.epic.mychart_locations?secret=<secret key>

or

curl -XGET https://www.bloomapi.com/api/search/bloomapi.epic.mychart_locations?key1=name&op1=prefix&value1=Polyclinic&secret=<secret key>


{
  "result": [
    {
      "mychart_branding": "MyChart",
      "mychart_user_label": "Username",
      "name": "Metro Health",
      "org_id": "530",
      "states": [
        {
          "name": "Michigan"
        }
      ],
      "timezone": "America/New_York"
    },
    ...
  ]
}
```

POST /api/yourchart
------------

A records release request can be created by specifying a organization’s patient portal returned from the `/api/search/bloomapi.epic.mychart_locations` endpoint.

To specify an portal to extract records from, send the `org_id` field from the desired `result` in the return from `/api/search/bloomapi.epic.mychart_locations`. The `org_id` in mandatory and must be sent as `orgId`. The request also requires two fields username, the patient’s `username`, and `password`, the patient’s password for the desired portal.

Arguments:

| Name          | type          | Description                  |
| ------------- |:-------------:|:----------------------------:|:---------|
| orgId         | integer       | ID for target patient portal | Required |
| username      | string        | Patient’s username for target| Required |
| password      | string        | Patient’s password for target| Required |

``` javascript
  Example Record Creation Request

  curl -XPOST "https://www.bloomapi.com/api/yourchart?secret=<secret key>" /
     -d "username=nick&password=supersecret&orgId=166"

  {
  "statusId" : "a4785854-8489-493a-9b8f-6bd886dba072"
  }
```

GET /api/yourchart/:status_id
------------

Gets the status of a patient portal request and the patient record when available.

The release will move through the following states:

1. `pending`: BloomAPI has received the request
2. `authenticated`: BloomAPI has authenticated with the patient portal. This will generally happen within 30 seconds of a request
3. `complete`: BloomAPI has retrieved records and they are made available in the response
There is an additional failure state that indicates an internal error has occurred with extracting records from this portal.

Here’s a current complete list of record types that can be requested:

| Name          | Description
| ------------- |:-------------:|
| Problems      | Problems list                         |
| Procedures    | Procedures list                       |
| History       | Health History                        |
| Medications   | Current/Historical Medications        |
| Immunizations | Immunization History                  |
| Vitals        | Historical Vitals                     |
| results       | Lab results                           |
| Encounters    | Descriptions of Historical Encounters |


``` javascript
  curl -XGET "https://www.bloomapi.com/api/yourchart/a4785854-8489-493a-9b8f-6bd886dba072?secret=<secret key>"

  Once pending

  {
    "state" : "authenticated"
  }

  Once Authenticated

  {
    "state" : "authenticated"
  }

  Once Retrieved

  {
    "state": "complete",
    "result": {
      "patients": [
        {
          "name": "Nick Smith",
          "accountID": "12334",
          "details": {
            "encounters": [
                ...
            ],
            "test-results": [
              ...
            ]
          }
        }
      ]
    }
  }
```
