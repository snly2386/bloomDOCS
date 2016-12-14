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











/api/sources
------------


Returns the current datasources and their status.

### Response Fields

The result will be an array of data sources.

| Field         | Description
| ------------- |:-------------:|
| source        | Identifier of datasource. The only datasource is 'NPI’ for now              |
| updated       | Timestamp of the last time the datasource was updated                       |
| check         | Timestamp of the last time BloomAPI checked for additional data for this  datasource                                                                                    |
| status        |  Legacy field. Will always `READY`                                          |


/api/sources/:source/:id
------------

Returns a specific element of a particular source. For example, the NPI’s :id would be an NPI to return information for.

```
    Example

    The following would return the details of the NPI 1376954206.
```

``` javascript
  GET http://www.bloomapi.com/api/sources/usgov.hhs.npi/1376954206
```

DATA SOURCES
=======

#### usgov.hhs.npi

Each field’s description is directly from the NPI dissemination documentation present in the dissemination files. Source fields are the name of the column in the source CSVs before they are translated for bloomapi.

Field metadata will be included soon

#### usgov.hhs.hcpcs

CMS HCPCS codes as documented at <https://www.cms.gov/Medicare/Coding/HCPCSReleaseCodeSets/index.html>

Field metadata will be included soon

#### usgov.hhs.pecos

Provider Enrollment and Certification data as documented by CMS

Field metadata will be included soon

#### usgov.hhs.medicare_specialty_codes

Medicare Specialty Code to Provider Taxonomy Code mapping from CMS

Field metadata will be included soon

#### nucc.hcpt

Provider Taxonomy Codes as provided by NUCC

Field Metadata will be included soon

#### usgov.hhs.icd_9_cm

ICD 9 CM Diagnosis Codes as provided by CMS

#### usgov.hhs.icd_10_cm

2015 ICD 10 CM Codes as provided by CMS

#### usgov.hhs.icd_9_gems

2015 ICD 9 to 10 GEMS as provided by CMS

#### usgov.hhs.icd_10_gems

2015 ICD 10 to 9 GEMS as provided by CMS

#### Other

BloomAPI Public Data is great at loading complex datasets quickly. We also support loading from many other datasources that require private data such as:

* Claims and Claims Line Feed
* AHRQ inpatient/ outpatient/ emergency databases
* HL7v2
* CCDs (HL7v3)

If you’d like access to an API with data from one of the above or would like another datasource to be included, please contact support

DEPLOY
=======

Deploy your own copy of BloomAPI to gain higher performance or high availability to meet your own SLAs. Alternatively deploy it to gain an on-site database containing the most recent copies of datasets such as the NPI.

Note that if you are just looking to setup bloomapi locally — the detailed setup instructions in the Contribute page may be easier to follow. This section is more about deploying BloomAPI in a production environment.

A BloomAPI deployment is made up of four core components

1. API service
1. Data worker/ processor service
3. PostgreSQL (version 9.3)
4. ElasticSearch (version 1.4)

Both the API service and the Data worker are written in Go and can be compiled using the `go install` command. Dependencies in each project are managed using the Godep tool.

Once Go (1.3+) has been installed and configured with a GOPATH, and Godep has been installed, you can clone the repositories using

`go get github.com/untoldone/bloomapi`

`go get github.com/gocodo/bloomnpi`

Once fetched, change into each of the bloomapi/ npi source directory roots and run godep restore.

Run `go install github.com/untoldone/bloomapi github.com/gocodo/bloomnpi` to build the code into your $GOPATH/bin directory. If you are running on a mac, but want to cross-compile for a linux system, you can run make from the bloomapi/ npi source directories. If cross-compiling, the go binaries `gox and gonative` must also be installed and configured.

Once compiled, copy the bloomapi binary from $GOPATH/bin, and config.toml, bootstrap.sql, and drop.sql from the bloomapi source directory to an install directory of your choice. For the bloomnpi binary, copy the binary, config.toml, and the sql directory to a different install directory. Create a data directory in the destination for bloomnpi as well.

Edit both `config.toml` files to point to your postgresql and elasticsearch deployments.

Once compiled and configured, you must bootstrap your environment as follows

`[bloomapi install path]/bloomapi bootstrap`

`[bloomnpi install path]/bloomnpi bootstrap`

`[bloomnpi install path]/bloomnpi fetch`

`[bloomnpi install path]/bloomnpi search-index`

This will download and index available datasources.

It will likely make sense to add fetch and search-index as a cron job to ensure your datasets are updated on a regular basis.

Finally, to start the api, run `bloomapi server`. This will start the server on the port specified in your config.toml file.
