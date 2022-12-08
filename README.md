# Infinite Flight Live API Overview

*Sourced from the original:*

- [Web](https://infiniteflight.com/guide/developer-reference#infinite-flight-connect-api)
- [In the main docs repo](https://github.com/infiniteflight/infiniteflight-docs/tree/main/developer-reference/live-api)

---

The Live API is our HTTP API used for requesting data from Infinite Flight Servers. Current features include:

- List available servers for pilots and ATC in-game.
- List active flights for each server.
- List active ATC for each server.
- Retrieve flight plans for each flight.
- Retrieve stats for users.

## Obtaining an API key

An API key is required to use the Live API. Contact [hello@infiniteflight.com](mailto:hello@infiniteflight.com) to let us know what you plan on building and to request a key.

## How to use the API

The Live API uses HTTP and the endpoints require you to make a GET or POST request to the endpoint URL. See individual endpoint documentation for specifics

## Conditions of Use

- All apps are required to have a timeout feature if the app isn’t being used. We don’t want users to have the app running for hours if no-one is looking. If no action is taken for 15 minutes, have your apps stop downloading new content unless the user presses a button (FlightRadar24 does this on their site).

- No permanent storing of data retrieved via the API is permitted. (You cannot keep the data in your own database). Caching is permitted.

  If you need additional functionality from the API, please contact us.

## Prerequisites

This documentation is aimed at someone with knowledge of:

- HTTP
- JSON
- Infinite Flight System (Grades, Servers, etc.)

If you are not knowledgeable in these areas, you are of course welcome to browse the documentation. You can also check out these resources to help you learn.

- [W3Schools - What is HTTP?](https://www.w3schools.com/whatis/whatis_http.asp)
- [W3Schools - JSON Introduction](https://www.w3schools.com/js/js_json_intro.asp)
- [Infinite Flight User Guide](/guide/getting-started-guide/home-user-interface/user-profile#the-grade-table)

# Get Sessions

Retrieve active sessions (servers) in Infinite Flight.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/sessions`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": [
    {
      "maxUsers": 1000,
      "id": "89573c7f-d398-4281-bcc0-3e9b7f6b8492",
      "name": "Sample Server",
      "userCount": 187,
      "type": 0
    }
  ]
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type          | Description                                                                                                                                                                                       |
| ----------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer       | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [SessionInfo] | Array of SessionInfo objects                                                                                                                                                                      |

#### SessionInfo

| Name        | Type    | Description                                                                    |
| ----------- | ------- | ------------------------------------------------------------------------------ |
| `id`        | string  | The unique identifier for the server. Use this to request flights and ATC data |
| `name`      | string  | Name of the server                                                             |
| `maxUsers`  | integer | Maximum number of users the server can accept                                  |
| `userCount` | integer | Connected users to the server                                                  |
| `type`      | integer | _Enum:_ `"Unrestricted = 0"`, `"Restricted = 1"`                               |

# Get Flights

Retrieve a list of all flights for a session.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/sessions/{sessionId}/flights`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name        | Located in | Description                                           | Required | Schema        |
| ----------- | ---------- | ----------------------------------------------------- | -------- | ------------- |
| `sessionId` | path       | ID of the session returned from the Sessions endpoint | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": [
    {
      "username": "Cameron",
      "callsign": "EC-CAM",
      "latitude": 30.123479009207056,
      "longitude": 31.413340256981044,
      "altitude": 597.8003749797689,
      "speed": 185.3844049009562,
      "verticalSpeed": 2167.31591796875,
      "track": 162.13836669921875,
      "lastReport": "2020-10-02 00:46:19Z",
      "flightId": "348d1ba8-1e60-48ca-8278-42f019147de8",
      "userId": "3f8b28bf-bbb1-4024-80ae-2a0ea9b30685",
      "aircraftId": "de510d3d-04f8-46e0-8d65-55b888f33129",
      "liveryId": "c875c0e9-19c2-420d-8fb4-32c151bd797c",
      "heading": 159.33542,
      "virtualOrganization": "IFATC [IFATC]"
    }
  ]
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type          | Description                                                                                                                                                                                       |
| ----------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer       | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [FlightEntry] | Array of FlightEntry objects                                                                                                                                                                      |

#### FlightEntry

| Name                  | Type          | Description                                                                                        |
| --------------------- | ------------- | -------------------------------------------------------------------------------------------------- |
| `flightId`            | string (uuid) | Unique identifier for the flight                                                                   |
| `userId`              | string (uuid) | Unique identifier for the user                                                                     |
| `aircraftId`          | string (uuid) | Unique identifier for the aircraft type                                                            |
| `liveryId`            | string (uuid) | Unique identifier for the livery and aircraft combination.                                         |
| `username`            | string        | The user's forum username if the account is linked. If the account isn't linked, this will be null |
| `virtualOrganization` | string        | The virtual organization of the user's forum account if linked. Can be null if not set             |
| `callsign`            | string        | Callsign for the flight                                                                            |
| `latitude`            | double        | Current decimal latitude of the aircraft                                                           |
| `longitude`           | double        | Current decimal longitude of the aircraft                                                          |
| `altitude`            | double        | Current altitude of the aircraft in feet                                                           |
| `speed`               | double        | Current groundspeed of the aircraft in knots                                                       |
| `verticalSpeed`       | double        | Current vertical speed of the aircraft in ft/min                                                   |
| `track`               | double        | Track of the aircraft in degrees                                                                   |
| `heading`             | float         | Heading of the aircraft in degrees                                                                 |
| `lastReport`          | string        | Last position report time of the flight in the following format: `YYYY-MM-DD HH:mm:ssZ`            |

# Get Flight Route

Retrieve the flown route of a specific flight with position, altitude, speed and track information at different points in time.

Please note, this is currently only supported on the Expert Server and Training Server.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/sessions/{sessionId}/flights/{flightId}/route`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name       | Located in | Description                                                | Required | Schema        |
| ---------- | ---------- | ---------------------------------------------------------- | -------- | ------------- |
| `flightId` | path       | ID of the flight. The flight must be in an active session. | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": [
    {
      "latitude": 25.52992361245416,
      "longitude": -80.33701239710909,
      "altitude": 18429.91132829714,
      "track": 75.00002,
      "groundSpeed": 194.6716372048416,
      "date": "2021-01-06T16:20:27.3275657Z"
    },
    {
      "latitude": 25.59595843191219,
      "longitude": -79.96882929194611,
      "altitude": 27448.993829140254,
      "track": 77.0936,
      "groundSpeed": 229.58654094765924,
      "date": "2021-01-06T16:23:27.4572883Z"
    },
    {
      "latitude": 25.621774462118506,
      "longitude": -79.56246802075516,
      "altitude": 30999.01751208178,
      "track": 110.30767,
      "groundSpeed": 235.4484678810845,
      "date": "2021-01-06T16:26:27.5268907Z"
    }
  ]
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type             | Description                                                                                                                                                                                       |
| ----------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer          | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [PositionReport] | Array of PositionReport objects                                                                                                                                                                   |

#### PositionReport

| Name          | Type   | Description                                                                        |
| ------------- | ------ | ---------------------------------------------------------------------------------- |
| `latitude`    | double | Decimal latitude of the aircraft at this position.                                 |
| `longitude`   | double | Decimal longitude of the aircraft at this position.                                |
| `altitude`    | double | Altitude of the aircraft in feet.                                                  |
| `track`       | double | Track / Course of aircraft in degrees                                              |
| `groundSpeed` | double | Ground speed of the aircraft in knots                                              |
| `date`        | string | Position report time of the flight in the following format: `YYYY-MM-DD HH:mm:ssZ` |

# Get Detailed Flight Plan

Retrieve the flight plan for a specific active flight.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/sessions/{sessionId}/flights/{flightId}/flightplan`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name       | Located in | Description                                                                             | Required | Schema        |
| ---------- | ---------- | --------------------------------------------------------------------------------------- | -------- | ------------- |
| `flightId` | path       | ID of the flight. The flight must be in an active session and have a filed flight plan. | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": {
      "flightPlanId": "4a57de08-a3b1-48ba-a081-adeff0a5b503",
      "flightId": "0b8cc273-d97d-4223-afac-907d09d8ca8b",
      "waypoints": [
        "AMAHE",
        "L12R"
      ],
      "lastUpdate": "2021-01-06 15:35:04Z",
      "flightPlanItems": [
        {
          "name": "AMAHE",
          "type": 0,
          "children": null,
          "identifier": null,
          "altitude": -1,
          "location": {
            "latitude": 26.92723722,
            "longitude": -77.47139889,
            "altitude": 0
          }
        },
        {
          "name": "L12R",
          "type": 2,
          "children": [
            {
              "name": "ZESTY",
              "type": 0,
              "children": null,
              "identifier": "ZESTY",
              "altitude": 4000,
              "location": {
                "latitude": 44.97196917,
                "longitude": -93.429735,
                "altitude": 0
              }
            },
            {
              "name": "RW12R",
              "type": 0,
              "children": null,
              "identifier": "RW12R",
              "altitude": -1,
              "location": {
                "latitude": 44.887794494628906,
                "longitude": -93.23413848876953,
                "altitude": 0
              }
            }
          ],
          "identifier": "L12R",
          "altitude": 0,
          "location": {
            "latitude": 0,
            "longitude": 0,
            "altitude": 0
          }
        }
      ]
    }
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type             | Description                                                                                                                                                                                       |
| ----------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer          | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [FlightPlanInfo] | Array of FlightPlanInfo objects                                                                                                                                                                   |

#### FlightPlanInfo

| Name              | Type             | Description                                                                                                                                                        |
| ----------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `flightPlanId`    | string (uuid)    | Unique identifier for the flight plan                                                                                                                              |
| `flightId`        | string (uuid)    | Unique identifier for the flight. Associate with the response from the Get Flights endpoint                                                                        |
| `waypoints`       | [string]         | **Deprecated**. An array of waypoint names. You can correlate these with data from the [Airport Editing Project](https://github.com/infiniteflightairportediting/) |
| `lastUpdate`      | string           | Last report time of the flight plan in the following format: `YYYY-MM-DD HH:mm:ssZ`                                                                                |
| `flightPlanItems` | [FlightPlanItem] | An array of FlightPlanItems which contain waypoint and procedure data for points in a flight plan.                                                                 |

#### FlightPlanItem

| Name         | Type             | Description                                                                                                                                                                                                    |
| ------------ | ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`       | string           | Name of the waypoint or the procedure. In the `children` array, this is the name of a waypoint inside a procedure.                                                                                             |
| `type`       | integer          | Type of procedure for this item. Only use this if the FlightPlanItem's `children` field is populated and not null. *Enum:* `"Sid = 0"`, `"STAR = 1"`,  `"Approach = 2"`, `"Track = 3"`, `"Unknown = 5"`        |
| `children`   | [FlightPlanItem] | An array of FlightPlanItems containing waypoint information about a procedure. Only present if this item defines a procedure (SID/STAR/Approach/Track). If not, assume this is a Fix/VOR/Custom User Waypoint. |
| `identifier` | string           | Identifier for the waypoint or the procedure. This is not unique.                                                                                                                                              |
| `altitude`   | integer          | The altitude in feet for this waypoint. This is optionally defined by the user and defaults to `-1` if not set.                                                                                                |
| `location`   | Coordinate       | A Coordinate object defining the position of this waypoint.                                                                                                                                                    |

#### Coordinate

| Name        | Type   | Description                               |
| ----------- | ------ | ----------------------------------------- |
| `latitude`  | double | Current decimal latitude of the aircraft  |
| `longitude` | double | Current decimal longitude of the aircraft |
| `altitude`  | double | Current decimal altitude of the aircraft  |

# Get Active ATC Frequencies

Retrieve active Air Traffic Control frequencies for a session

## Resource

**GET** `https://api.infiniteflight.com/public/v2/sessions/{sessionId}/atc`

## Authorization

Include your API key (`<apikey>`) by either:

-   Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
-   Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name        | Located in | Description                                           | Required | Schema        |
| ----------- | ---------- | ----------------------------------------------------- | -------- | ------------- |
| `sessionId` | path       | ID of the session returned from the Sessions endpoint | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
    "errorCode": 0,
    "result": [
        {
            "frequencyId": "c2d7decc-2803-c905-5d88-81bc07626b1f",
            "userId": "3f8b28bf-bbb1-4024-80ae-2a0ea9b30685",
            "username": "Cameron",
            "virtualOrganization": null,
            "airportName": "LEPA",
            "type": 1,
            "latitude": 39.551575,
            "longitude": 2.736811,
            "startTime": "2020-10-02 15:47:25Z"
        }
    ]
}
```

#### LiveAPIResponse

_Response Type:_ `application/json`

| Name        | Type                | Description                                                                                                                                                                                       |
| ----------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer             | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [ActiveATCFacility] | Array of ActiveATCFacility objects                                                                                                                                                                |

#### ActiveATCFacility

| Name                  | Type          | Description                                                                                                                                                                                                                                                                    |
| --------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `frequencyId`         | string (uuid) | Unique identifier for the open frequency                                                                                                                                                                                                                                       |
| `userId`              | string (uuid) | Unique identifier for the user controlling the frequency                                                                                                                                                                                                                       |
| `username`            | string        | The user's forum username if the account is linked. If the account isn't linked, this will be null                                                                                                                                                                             |
| `virtualOrganization` | string        | _(not currently in use)_                                                                                                                                                                                                                                                       |
| `airportName`         | string        | The 4-character ICAO identifier for the airport. `null` for center                                                                                                                                                                                                             |
| `type`                | integer       | The type of frequency opened - not all of these are in use. _Enum:_ `"Ground = 0"`, `"Tower = 1"`, `"Unicom = 2"`, `"Clearance = 3"`, `"Approach = 4"`, `"Departure = 5"`, `"Center = 6"`, `"ATIS = 7"`, `"Aircraft = 8"`, `"Recorded = 9"`, `"Unknown = 10"`, `"Unused = 11"` |
| `latitude`            | float         | Decimal latitude of the airport                                                                                                                                                                                                                                                |
| `longitude`           | float         | Decimal longitude of the airport                                                                                                                                                                                                                                               |
| `startTime`           | string        | Time at which the frequency was opened, in the following format: `YYYY-MM-DD HH:mm:ssZ`                                                                                                                                                                                        |

# Get User Stats

Retrieve user statistics for up to 25 users at once, including their grade, flight time and username.

## Resource

**POST** `https://api.infiniteflight.com/public/v2/users`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

*Request Content-Type:* `application/json`

| Name             | Located in        | Description                                                                                        | Required | Schema          |
| ---------------- | ----------------- | -------------------------------------------------------------------------------------------------- | -------- | --------------- |
| `userIds`        | POST request body | An array of user ID strings retrieved from another endpoint                                        | No\\\*   | [string (uuid)] |
| `discourseNames` | POST request body | An array of IFC Usernames. Not case sensitive.                                                     | No\\\*   | [string]        |
| `userHashes`     | POST request body | An array of user hashes retrieved in-app or from another endpoint. All letters must be upper case. | No\\\*   | [string]        |

*\*At least one search parameter is required*

#### Sample Body

```json
{
  "userIds": [
    "2a11e620-1cc1-4ac6-90d1-18c4ed9cb913",
    "5917d076-88a5-40e7-95e0-8818748f8e99"
  ],
  "discourseNames": [
      "KaiM",
      "Laura"
  ],
  "userHashes": [
      "F0081CAA",
      "E2087C9F"
  ],
}
```

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": [
    {
      "onlineFlights": 2449,
      "violations": 102,
      "xp": 572128,
      "landingCount": 898,
      "flightTime": 45983,
      "atcOperations": 548,
      "atcRank": 7,
      "grade": 5,
      "hash": "5F0973A9",
      "violationCountByLevel": {
        "level1": 102,
        "level2": 0,
        "level3": 0
      },
      "roles": [
        1,
        2,
        64
      ],
      "userId": "2a11e620-1cc1-4ac6-90d1-18c4ed9cb913",
      "virtualOrganization": null,
      "discourseUsername": "Cameron",
      "groups": [
        "8c93a113-0c6c-491f-926d-1361e43a5833",
        "d07afad8-79df-4363-b1c7-a5a1dde6e3c8",
        "df0f6341-5f6a-40ef-8b73-087a0ec255b5"
      ],
      "errorCode": 0
    },
    {
      "onlineFlights": 21,
      "violations": 0,
      "xp": 29984,
      "landingCount": 24,
      "flightTime": 2717,
      "atcOperations": 0,
      "atcRank": null,
      "grade": 1,
      "hash": "56099EA4",
      "userId": "66e362c0-894b-495b-93a6-75f9befa502d",
      "virtualOrganization": null,
      "discourseUsername": null,
       "violationCountByLevel": {
        "level1": 22,
        "level2": 0,
        "level3": 0
      },
      "roles": [
        64
      ],
      "groups": [],
      "errorCode": 0
    }
  ]
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type        | Description                                                                                                                                                                                       |
| ----------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer     | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [UserStats] | Array of UserStats objects                                                                                                                                                                        |



#### UserStats

| Name                    | Type            | Description                                                                                            |
| ----------------------- | --------------- | ------------------------------------------------------------------------------------------------------ |
| `userId`                | string (uuid)   | Unique identifier for the user                                                                         |
| `virtualOrganization`   | string          | The virtual organization of the user's forum account if linked. Can be null if not set                 |
| `discourseUsername`     | string          | The user's forum username if the account is linked. If the account isn't linked, this will be null     |
| `groups`                | [string (uuid)] | **Deprecated - will be removed in a future update** A list of groups the user can be a part of.        |
| `roles`                 | [integer]       | A list of roles a user has been assigned. See below for a list of main roles.                          |
| `errorCode`             | integer         | Status code of user query. Not in use for this endpoint                                                |
| `onlineFlights`         | integer         | Number of flights carried out in multiplayer                                                           |
| `violations`            | integer         | Number of Level 1, 2 and 3 violations the user received in multiplayer                                 |
| `violationCountByLevel` | dict            | A dictionary with a count of violations issued to the user, split up by levels (Level 1/2/3).          |
| `xp`                    | double          | Total XP obtained in multiplayer                                                                       |
| `landingCount`          | integer         | Total landings carried out in multiplayer                                                              |
| `flightTime`            | double          | Total flight time in minutes in multiplayer                                                            |
| `atcOperations`         | integer         | Total number of ATC Operations.                                                                        |
| `atcRank`               | integer         | ATC Rank on the Expert Server. See below for the ranks. Can be null if user isn't an IFATC controller. |
| `grade`                 | integer         | The grade of the user, from 1 to 5.                                                                    |
| `hash`                  | string          | A short-form user identifier, shown in the app to identify anonymous users.                            |

#### Roles

The main roles are as follows.

| ID  | Name                  |
| --- | --------------------- |
| 1   | Infinite Flight Staff |
| 2   | Moderators            |
| 64  | IFATC Members         |

#### ATC Ranks

The ATC Ranks are as follows.

| ID  | Name           |
| --- | -------------- |
| 0   | Observer       |
| 1   | ATC Trainee    |
| 2   | ATC Apprentice |
| 3   | ATC Specialist |
| 4   | ATC Officer    |
| 5   | ATC Supervisor |
| 6   | ATC Recruiter  |
| 7   | ATC Manager    |

# Get User Grade

Retrieve the full grade table and detailed statistics for a user.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/users/{userId}`

## Authorization

Include your API key (`<apikey>`) by either:

-   Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
-   Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name     | Located in | Description    | Required | Schema        |
| -------- | ---------- | -------------- | -------- | ------------- |
| `userId` | path       | ID of the User | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": {
    "totalXP": 217242,
    "total12MonthsViolations": 4,
    "gradeDetails": {
        "grades": [...],
        "gradeIndex": 2,
        "ruleDefinitions": [...]
    },
    "atcOperations": 14641,
    "atcRank": 3,
    "lastLevel1ViolationDate": "2020-12-16T03:20:10.283484",
    "lastReportViolationDate": "0001-01-01T00:00:00",
    "violationCountByLevel": {
      "level1": 9,
      "level2": 0,
      "level3": 0
    },
    "roles": [
      41,
      43,
      53,
      61,
      64,
      68
    ],
    "userId": "b0018209-e010-40a0-afe1-00ecd5856c5e",
    "virtualOrganization": "IFATC [IFATC]",
    "discourseUsername": "KaiM",
    "groups": [
      "df0f6341-5f6a-40ef-8b73-087a0ec255b5"
    ],
    "errorCode": 0
  }
}
```

#### LiveAPIResponse

_Response Type:_ `application/json`

| Name        | Type      | Description                                                                                                                                                                                       |
| ----------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer   | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | GradeInfo | GradeInfo Object                                                                                                                                                                                  |

#### GradeInfo

| Name                      | Type               | Description                                                                                                                       |
| ------------------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------- |
| `userId`                  | string (uuid)      | Unique identifier for the user                                                                                                    |
| `virtualOrganization`     | string             | The virtual organization of the user's forum account if linked. Can be null if not set                                            |
| `discourseUsername`       | string             | The user's forum username if the account is linked. If the account isn't linked, this will be null                                |
| `groups`                  | [string (uuid)]    | **Deprecated - will be removed soon.** A list of groups the user can be a part of.                                                |
| `roles`                   | [integer]          | A list of roles a user has been assigned. See below for a list of main roles.                                                     |
| `errorCode`               | integer            | Status code of user query. Not in use for this endpoint.                                                                          |
| `gradeDetails`            | GradeConfiguration | Full Grade Table                                                                                                                  |
| `violationCountByLevel`   | dict               | A dictionary with a count of violations issued to the user, split up by levels (Level 1/2/3).                                     |
| `totalXP`                 | double             | Total XP obtained in multiplayer                                                                                                  |
| `atcOperations`           | integer            | Total number of ATC Operations.                                                                                                   |
| `atcRank`                 | integer            | ATC Rank on the Expert Server. See below for the ranks. Can be null if user isn't an IFATC controller.                            |
| `total12MonthsViolations` | integer            | Total amount of Level 1, 2, and 3 violations received in the last 12 months                                                       |
| `lastLevel1ViolationDate` | string (datetime)  | Date of the user's last Level 1 violation                                                                                         |
| `lastReportViolationDate` | string (datetime)  | Date of the user's last Level 2 or 3 violation (report). Defaults to `0001-01-01T00:00:00` if the user does not have any reports. |

#### Roles

The main roles are as follows.

| ID  | Name                  |
| --- | --------------------- |
| 1   | Infinite Flight Staff |
| 2   | Moderators            |
| 64  | IFATC Members         |

#### Groups

The main groups are as follows.

| ID                                   | Name          |
| ------------------------------------ | ------------- |
| d07afad8-79df-4363-b1c7-a5a1dde6e3c8 | Staff         |
| 8c93a113-0c6c-491f-926d-1361e43a5833 | Moderators    |
| df0f6341-5f6a-40ef-8b73-087a0ec255b5 | IFATC Members |

#### GradeConfiguration

| Name              | Type                  | Description                                            |
| ----------------- | --------------------- | ------------------------------------------------------ |
| `grades`          | [Grade]               | Array containing all grades                            |
| `gradeIndex`      | integer               | The Index of the `grades` property that the user holds |
| `ruleDefinitions` | [GradeRuleDefinition] | Definiton for the rules required for each grade        |

#### Grade

| Name    | Type        | Description                                                                    |
| ------- | ----------- | ------------------------------------------------------------------------------ |
| `rules` | [GradeRule] | Rules to be met to obtain the grade                                            |
| `index` | integer     | Index of the Grade in the `grades` property of the `GradeConfiguration` object |
| `name`  | string      | Name of the Grade                                                              |
| `state` | integer     | _Enum:_ `"Fail = 0"`, `"OK = 1"`, `"Warning = 2"`                              |

#### GradeRule

| Name                   | Type                | Description                                                     |
| ---------------------- | ------------------- | --------------------------------------------------------------- |
| `ruleIndex`            | integer             | Index of the rule in the `rules` property of the `Grade` object |
| `referenceValue`       | double              | The requirement value                                           |
| `userValue`            | double              | The value of the user for this property                         |
| `state`                | integer             | _Enum:_ `"Fail = 0"`, `"OK = 1"`, `"Warning = 2"`               |
| `userValueString`      | string              | The value of the user, nicely formatted                         |
| `referenceValueString` | string              | The requirement value, nicely formatted                         |
| `definition`           | GradeRuleDefinition | Definition of the rule                                          |

#### GradeRuleDefinition

| Name          | Type    | Description                                                                                                                                  |
| ------------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`        | string  | Name of the rule                                                                                                                             |
| `description` | string  | Description of the rule                                                                                                                      |
| `property`    | string  | The property of the `GradeInfo` object the rule relates to                                                                                   |
| `operator`    | integer | _Enum:_ `"GreaterThan = 0"`, `"LesserThan = 1"`, `"GreaterThanOrEqual = 2"`, `"LesserThanOrEqual = 3"`, `"Equal = 4"`, `"DifferentThan = 5"` |
| `period`      | double  | Time period in which the rule must be met                                                                                                    |
| `order`       | integer | Order of the Rule within the `rules` property of the `Grade` object                                                                          |
| `group`       | integer | Not in use for this endpoint.                                                                                                                |

#### ViolationEntry

| Name   | Type   | Description                     |
| ------ | ------ | ------------------------------- |
| `type` | double | Type of Violation               |
| `date` | string | Date the Violation was Received |

#### ReportEntry

| Name           | Type          | Description                                 |
| -------------- | ------------- | ------------------------------------------- |
| `type`         | integer       | Type of Report                              |
| `creationTime` | string        | Time the Report was Created                 |
| `creatorId`    | string (uuid) | User ID of the Report Issuer                |
| `description`  | string        | Reason for the Report                       |
| `flightId`     | string (uuid) | ID of the Flight the Report was received in |

# Get Airport ATIS

Retrieve the ATIS for an airport on a specific server if it is active.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/sessions/{sessionId}/airport/{airportIcao}/atis`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name          | Located in | Description                             | Required | Schema        |
| ------------- | ---------- | --------------------------------------- | -------- | ------------- |
| `airportIcao` | query      | ICAO of the airport to get the ATIS for | Yes      | string        |
| `sessionId`   | query      | Session (Server) ID of the Live Server  | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
    "errorCode":0,
    "result":"Manchester Airport, ATIS information DELTA, time 2355 ZULU, Wind 350 at 6 Visibility 21, Temperature 2, Dew Point 0, QNH 1024. Remarks, no pattern work allowed, no light aircraft accepted at this time. Landing Runways 05L and 05R, Departing Runways 05L and 05R. Advise on initial contact, you have information DELTA."
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type    | Description                                                                                                                                                                                       |
| ----------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | string  | The ATIS, or `null` if it is not available                                                                                                                                                        |

# Get Airport Status

Retrieve active ATC status information for an airport, and the number of inbound and outbound aircraft.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/sessions/{sessionId}/airport/{airportIcao}/status`

## Authorization

Include your API key (`<apikey>`) by either:

-   Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
-   Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name          | Located in | Description                               | Required | Schema        |
| ------------- | ---------- | ----------------------------------------- | -------- | ------------- |
| `airportIcao` | query      | ICAO of the airport to get the status for | Yes      | string        |
| `sessionId`   | query      | Session (Server) ID of the Live Server    | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": {
    "airportIcao": "VTBS",
    "inboundFlightsCount": 40,
    "inboundFlights": [
      "4f559855-fecc-4a8a-a95e-1d097eed9b72",
	  ...
    ],
    "outboundFlightsCount": 19,
    "outboundFlights": [
      "59e9509b-214a-4f8c-9d45-29c4f7ea01d7",
	  ...
    ],
    "atcFacilities": [
      {
        "frequencyId": "23bea566-20a0-2858-a40e-179d0699afc1",
        "userId": "05328fc4-b651-45e9-8e1f-328095329484",
        "username": "Rhys_V",
        "virtualOrganization": null,
        "airportName": "VTBS",
        "type": 4,
        "latitude": 13.680815,
        "longitude": 100.74768,
        "startTime": "2021-02-08 09:59:58Z"
      }
    ]
  }
}
```

#### LiveAPIResponse

_Response Type:_ `application/json`

| Name        | Type    | Description                                                                                                                                                                                       |
| ----------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | string  | An `AirportStatus` object                                                                                                                                                                         |

#### Airport Status

| Name                   | Type                | Description                                                                                                       |
| ---------------------- | ------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `airportIcao`          | string              | ICAO of the airport                                                                                               |
| `inboundFlightsCount`  | integer             | Number of aircraft inbound to this airport (must have final waypoint in flight plan set as the airport ICAO)      |
| `inboundFlights`       | [string (uuid)]     | A list of flight identifiers inbound to this airport. Use this to get flight plans or flight route information    |
| `outboundFlightsCount` | integer             | Number of aircraft departing this airport (must have first waypoint in flight plan set as the airport ICAO)       |
| `outboundFlights`      | [string (uuid)]     | A list of flight identifiers outbound from this airport. Use this to get flight plans or flight route information |
| `atcFacilities`        | [ActiveATCFacility] | Array of ActiveATCFacility objects                                                                                |

#### ActiveATCFacility

| Name                  | Type          | Description                                                                                                                                                                                                                                                                    |
| --------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `frequencyId`         | string (uuid) | Unique identifier for the open frequency                                                                                                                                                                                                                                       |
| `userId`              | string (uuid) | Unique identifier for the user controlling the frequency                                                                                                                                                                                                                       |
| `username`            | string        | The user's forum username if the account is linked. If the account isn't linked, this will be null                                                                                                                                                                             |
| `virtualOrganization` | string        | _(not currently in use)_                                                                                                                                                                                                                                                       |
| `airportName`         | string        | The 4-character ICAO identifier for the airport. `null` for center                                                                                                                                                                                                             |
| `type`                | integer       | The type of frequency opened - not all of these are in use. _Enum:_ `"Ground = 0"`, `"Tower = 1"`, `"Unicom = 2"`, `"Clearance = 3"`, `"Approach = 4"`, `"Departure = 5"`, `"Center = 6"`, `"ATIS = 7"`, `"Aircraft = 8"`, `"Recorded = 9"`, `"Unknown = 10"`, `"Unused = 11"` |
| `latitude`            | float         | Decimal latitude of the airport                                                                                                                                                                                                                                                |
| `longitude`           | float         | Decimal longitude of the airport                                                                                                                                                                                                                                               |
| `startTime`           | string        | Time at which the frequency was opened, in the following format: `YYYY-MM-DD HH:mm:ssZ`                                                                                                                                                                                        |

# Get World Status

Retrieve active ATC status information and inbound/outbound aircraft information for all airports with activity on a specific server.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/sessions/{sessionId}/world`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name        | Located in | Description                            | Required | Schema        |
| ----------- | ---------- | -------------------------------------- | -------- | ------------- |
| `sessionId` | query      | Session (Server) ID of the Live Server | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": [
    {
      "airportIcao": "KLAX",
      "inboundFlightsCount": 103,
      "inboundFlights": [
        "e1cf6c27-6f18-43a0-8bf8-cec5406e93a0",
        ...
      ],
      "outboundFlightsCount": 39,
      "outboundFlights": [
        "e2ded8f8-ff70-454a-911b-f193eb47636f",
        ...
      ],
      "atcFacilities": [
        {
          "frequencyId": "ee835f12-6cf5-eaa1-04bf-2018c7c01ae0",
          "userId": "51f94d83-54e8-4674-ae2d-046a42c04f7a",
          "username": "Manav_Suri",
          "virtualOrganization": null,
          "airportName": "KLAX",
          "type": 0,
          "latitude": 33.943123,
          "longitude": -118.40881,
          "startTime": "2021-02-09 09:53:02Z"
        },
        ...
      ]
    },
  ]
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type            | Description                                                                                                                                                                                       |
| ----------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer         | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [AirportStatus] | An array of `AirportStatus` objects                                                                                                                                                               |

#### AirportStatus

| Name                   | Type                | Description                                                                                                       |
| ---------------------- | ------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `airportIcao`          | string              | ICAO of the airport                                                                                               |
| `inboundFlightsCount`  | integer             | Number of aircraft inbound to this airport (must have final waypoint in flight plan set as the airport ICAO)      |
| `inboundFlights`       | [string (uuid)]     | A list of flight identifiers inbound to this airport. Use this to get flight plans or flight route information    |
| `outboundFlightsCount` | integer             | Number of aircraft departing this airport (must have first waypoint in flight plan set as the airport ICAO)       |
| `outboundFlights`      | [string (uuid)]     | A list of flight identifiers outbound from this airport. Use this to get flight plans or flight route information |
| `atcFacilities`        | [ActiveATCFacility] | Array of ActiveATCFacility objects                                                                                |

#### ActiveATCFacility

| Name                  | Type          | Description                                                                                                                                                                                                                                                                    |
| --------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `frequencyId`         | string (uuid) | Unique identifier for the open frequency                                                                                                                                                                                                                                       |
| `userId`              | string (uuid) | Unique identifier for the user controlling the frequency                                                                                                                                                                                                                       |
| `username`            | string        | The user's forum username if the account is linked. If the account isn't linked, this will be null                                                                                                                                                                             |
| `virtualOrganization` | string        | *(not currently in use)*                                                                                                                                                                                                                                                       |
| `airportName`         | string        | The 4-character ICAO identifier for the airport. `null` for center                                                                                                                                                                                                             |
| `type`                | integer       | The type of frequency opened - not all of these are in use. *Enum:* `"Ground = 0"`, `"Tower = 1"`, `"Unicom = 2"`, `"Clearance = 3"`, `"Approach = 4"`, `"Departure = 5"`, `"Center = 6"`, `"ATIS = 7"`, `"Aircraft = 8"`, `"Recorded = 9"`, `"Unknown = 10"`, `"Unused = 11"` |
| `latitude`            | float         | Decimal latitude of the airport                                                                                                                                                                                                                                                |
| `longitude`           | float         | Decimal longitude of the airport                                                                                                                                                                                                                                               |
| `startTime`           | string        | Time at which the frequency was opened, in the following format: `YYYY-MM-DD HH:mm:ssZ`                                                                                                                                                                                        |

# Get Oceanic Tracks

Retrieves a list of Oceanic Tracks active in Infinite Flight multiplayer sessions.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/tracks`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

*No parameters required.*

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": [
    {
      "name": "A",
      "path": [
        "DINIM",
        "51/20",
        "51/30",
        "50/40",
        "49/50",
        "JOOPY"
      ],
      "eastLevels": null,
      "westLevels": [
        350,
        370,
        390
      ],
      "type": "North Atlantic Tracks",
      "lastSeen": "2021-01-06T18:49:33.6300772Z"
    }
  ]
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type           | Description                                                                                                                                                                                       |
| ----------- | -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer        | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [OceanicTrack] | An array of tracks that are active in Infinite Flight.                                                                                                                                            |

#### OceanicTrack

| Name         | Type      | Description                                                                                                              |
| ------------ | --------- | ------------------------------------------------------------------------------------------------------------------------ |
| `name`       | string    | Name of the track. This is normally represented by letters.                                                              |
| `path`       | [string]  | You can correlate these with data from the [Airport Editing Project](https://github.com/infiniteflightairportediting/)   |
| `eastLevels` | [integer] | An array of Flight Level altitudes that aircraft can fly eastbound on using this track.                                  |
| `westLevels` | [integer] | An array of Flight Level altitudes that aircraft can fly westbound on using this track.                                  |
| `type`       | string    | Type of Oceanic Track. Infinite Flight supports `North Atlantic Tracks` in addition to custom tracks defined for events. |
| `lastSeen`   | string    | Last date and time at which the Oceanic Tracks were updated, in the following format: `YYYY-MM-DDTHH:mm:ssZ`             |


# Get User Flights

Retrieves the online flight logbook for a given user.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/users/{userId}/flights`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name     | Located in | Description                   | Required        | Schema        |
| -------- | ---------- | ----------------------------- | --------------- | ------------- |
| `userId` | path       | ID of the User                | Yes             | string (uuid) |
| `page`   | query      | Index of the page to retrieve | No, default `1` | integer       |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": {
    "pageIndex": 1,
    "totalPages": 80,
    "totalCount": 799,
    "hasPreviousPage": false,
    "hasNextPage": true,
    "data": [
      {
        "id": "9aeb16a3-ac69-41d3-9dd4-d62be72b1525",
        "created": "2022-01-10T10:37:41.965626",
        "userId": "b0018209-e010-40a0-afe1-00ecd5856c5e",
        "aircraftId": "849366e1-cb11-4d72-9034-78b11cd026b0",
        "liveryId": "a071518d-995a-4b3c-b65b-656da0d6ed86",
        "callsign": "VH-KAI",
        "server": "Casual Server",
        "dayTime": 2.5355167,
        "nightTime": 0,
        "totalTime": 2.5355167,
        "landingCount": 0,
        "originAirport": "YTYA",
        "destinationAirport": "YTYA",
        "xp": 25
      },
      ...
    ]
  }
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type          | Description                                                                                                                                                                                       |
| ----------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer       | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | PaginatedList | A page from the logbook.                                                                                                                                                                          |

#### Paginated List

| Name              | Type         | Description                                  |
| ----------------- | ------------ | -------------------------------------------- |
| `pageIndex`       | integer      | The index of the current page                |
| `totalPages`      | integer      | The total number of pages available          |
| `totalCount`      | integer      | The total amount of entries for this dataset |
| `hasPreviousPage` | boolean      | Whether there is a page before this one      |
| `hasNextPage`     | boolean      | Whether there is a page after this one       |
| `data`            | [UserFlight] | The entries in the current page              |

#### UserFlight

| Name                 | Type              | Description                                                                           |
| -------------------- | ----------------- | ------------------------------------------------------------------------------------- |
| `id`                 | string (uuid)     | The ID of the flight                                                                  |
| `created`            | string (datetime) | The time the flight was created                                                       |
| `userId`             | string (uuid)     | The ID of the user who flew the flight                                                |
| `aircraftId`         | string (uuid)     | The ID of the aircraft flown                                                          |
| `liveryId`           | string (uuid)     | The ID of the livery flown. **This is currently only supported on the Casual server** |
| `callsign`           | string            | The callsign of the user during this flight                                           |
| `server`             | string            | The name of the server the flight was flown on                                        |
| `dayTime`            | float             | The flight time during the day, in minutes                                            |
| `nightTime`          | float             | The flight time during the night, in minutes                                          |
| `totalTime`          | float             | The total flight time of the flight, in minutes                                       |
| `landingCount`       | integer           | The number of landings conducted during the flight                                    |
| `originAirport`      | string            | The ICAO code of the departure airport. Can be null                                   |
| `destinationAirport` | string            | The ICAO code of the arrival airport. Can be null                                     |
| `xp`                 | integer           | The number of XP earned during the flight                                             |

# Get User Flights

Retrieves a flight from the logbook of a given user.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/users/{userId}/flights/{flightId}`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name       | Located in | Description      | Required | Schema        |
| ---------- | ---------- | ---------------- | -------- | ------------- |
| `userId`   | path       | ID of the User   | Yes      | string (uuid) |
| `flightId` | path       | ID of the Flight | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": {
    "id": "9aeb16a3-ac69-41d3-9dd4-d62be72b1525",
    "created": "2022-01-10T10:37:41.965626",
    "userId": "b0018209-e010-40a0-afe1-00ecd5856c5e",
    "aircraftId": "849366e1-cb11-4d72-9034-78b11cd026b0",
    "liveryId": "a071518d-995a-4b3c-b65b-656da0d6ed86",
    "callsign": "VH-KAI",
    "server": "Casual Server",
    "dayTime": 2.5355167,
    "nightTime": 0,
    "totalTime": 2.5355167,
    "landingCount": 0,
    "originAirport": "YTYA",
    "destinationAirport": "YTYA",
    "xp": 25
  }
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type       | Description                                                                                                                                                                                       |
| ----------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer    | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | UserFlight | A flight from the logbook.                                                                                                                                                                        |

#### UserFlight

| Name                 | Type              | Description                                                                           |
| -------------------- | ----------------- | ------------------------------------------------------------------------------------- |
| `id`                 | string (uuid)     | The ID of the flight                                                                  |
| `created`            | string (datetime) | The time the flight was created                                                       |
| `userId`             | string (uuid)     | The ID of the user who flew the flight                                                |
| `aircraftId`         | string (uuid)     | The ID of the aircraft flown                                                          |
| `liveryId`           | string (uuid)     | The ID of the livery flown. **This is currently only supported on the Casual server** |
| `callsign`           | string            | The callsign of the user during this flight                                           |
| `server`             | string            | The name of the server the flight was flown on                                        |
| `dayTime`            | float             | The flight time during the day, in minutes                                            |
| `nightTime`          | float             | The flight time during the night, in minutes                                          |
| `totalTime`          | float             | The total flight time of the flight, in minutes                                       |
| `landingCount`       | integer           | The number of landings conducted during the flight                                    |
| `originAirport`      | string            | The ICAO code of the departure airport. Can be null                                   |
| `destinationAirport` | string            | The ICAO code of the arrival airport. Can be null                                     |
| `xp`                 | integer           | The number of XP earned during the flight                                             |

# Get User ATC Session

Retrieves an ATC session from the log of a given user.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/users/{userId}/atc/{atcSessionId}`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name           | Located in | Description           | Required | Schema        |
| -------------- | ---------- | --------------------- | -------- | ------------- |
| `userId`       | path       | ID of the User        | Yes      | string (uuid) |
| `atcSessionId` | path       | ID of the ATC Session | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": {
    "id": "857b1686-455d-481a-8bdf-c46cd7711691",
    "atcSessionGroupId": "1fcff4cb-48e6-46dc-8c87-4202bcdd7cc0",
    "facility": {
        "id": "4fda33c2-91b9-1a57-98f2-a765abd4c019",
        "airportIcao": "VRMM",
        "latitude": 4.191753387451172,
        "longitude": 73.52915954589844,
        "frequencyType": 4
    },
    "created": "2020-07-03T10:27:08.021137",
    "updated": "2020-07-03T10:37:49.026714",
    "operations": 9,
    "totalTime": 86.15142588333333
  }
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type           | Description                                                                                                                                                                                       |
| ----------- | -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer        | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | UserAtcSession | An ATC session from the logbook.                                                                                                                                                                  |

#### UserAtcSession

| Name                | Type              | Description                                                                                           |
| ------------------- | ----------------- | ----------------------------------------------------------------------------------------------------- |
| `id`                | string (uuid)     | The ID of the session                                                                                 |
| `atcSessionGroupId` | string (uuid)     | Identifies a group of sessions (for when a controller opens multiple frequencies at the same airport) |
| `facility`          | ATCFacility       | Details of the facility that was opened                                                               |
| `created`           | string (datetime) | The time at which the frequency was first opened                                                      |
| `updated`           | string (datetime) | The time at which last report was received                                                            |
| `operations`        | integer           | The number of operations earned during the session                                                    |
| `totalTime`         | double            | The duration of the session in minutes                                                                |

#### ATCFacility

| Name            | Type          | Description                                                                                                                                                                                                                              |
| --------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`            | string (uuid) | ID of the ATC Facility                                                                                                                                                                                                                   |
| `airportIcao`   | string        | The ICAO of the airport at which the facility is located                                                                                                                                                                                 |
| `latitude`      | double        | Latitude of the ATC Facility                                                                                                                                                                                                             |
| `longitude`     | double        | Longitude of the ATC Facility                                                                                                                                                                                                            |
| `frequencyType` | ATCEntityType | Type of ATC Facility. _Enum:_ `"Ground = 0"`, `"Tower = 1"`, `"Unicom = 2"`, `"Clearance = 3"`, `"Approach = 4"`, `"Departure = 5"`, `"Center = 6"`, `"ATIS = 7"`, `"Aircraft = 8"`, `"Recorded = 9"`, `"Unknown = 10"`, `"Unused = 11"` |

# Get User ATC Sessions

Retrieves the ATC session log for a given user.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/users/{userId}/atc`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name     | Located in | Description                   | Required        | Schema        |
| -------- | ---------- | ----------------------------- | --------------- | ------------- |
| `userId` | path       | ID of the User                | Yes             | string (uuid) |
| `page`   | query      | Index of the page to retrieve | No, default `1` | integer       |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": {
    "pageIndex": 1,
    "totalPages": 80,
    "totalCount": 791,
    "hasPreviousPage": false,
    "hasNextPage": true,
    "data": [
      {
        "id": "857b1686-455d-481a-8bdf-c46cd7711691",
        "atcSessionGroupId": "1fcff4cb-48e6-46dc-8c87-4202bcdd7cc0",
        "facility": {
          "id": "4fda33c2-91b9-1a57-98f2-a765abd4c019",
          "airportIcao": "VRMM",
          "latitude": 4.191753387451172,
          "longitude": 73.52915954589844,
          "frequencyType": 4
        },
        "created": "2020-07-03T10:27:08.021137",
        "updated": "2020-07-03T10:37:49.026714",
        "operations": 9,
        "totalTime": 86.15142588333333
      },
      ...
    ]
  }
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type          | Description                                                                                                                                                                                       |
| ----------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer       | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | PaginatedList | A page from the logbook.                                                                                                                                                                          |

#### Paginated List

| Name              | Type             | Description                                  |
| ----------------- | ---------------- | -------------------------------------------- |
| `pageIndex`       | integer          | The index of the current page                |
| `totalPages`      | integer          | The total number of pages available          |
| `totalCount`      | integer          | The total amount of entries for this dataset |
| `hasPreviousPage` | boolean          | Whether there is a page before this one      |
| `hasNextPage`     | boolean          | Whether there is a page after this one       |
| `data`            | [UserAtcSession] | The entries in the current page              |

#### UserAtcSession

| Name                | Type              | Description                                                                                           |
| ------------------- | ----------------- | ----------------------------------------------------------------------------------------------------- |
| `id`                | string (uuid)     | The ID of the session                                                                                 |
| `atcSessionGroupId` | string (uuid)     | Identifies a group of sessions (for when a controller opens multiple frequencies at the same airport) |
| `facility`          | ATCFacility       | Details of the facility that was opened                                                               |
| `created`           | string (datetime) | The time at which the frequency was first opened                                                      |
| `updated`           | string (datetime) | The time at which last report was received                                                            |
| `operations`        | integer           | The number of operations earned during the session                                                    |
| `totalTime`         | double            | The duration of the session in minutes                                                                |

#### ATCFacility

| Name            | Type          | Description                                                                                                                                                                                                                              |
| --------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`            | string (uuid) | ID of the ATC Facility                                                                                                                                                                                                                   |
| `airportIcao`   | string        | The ICAO of the airport at which the facility is located                                                                                                                                                                                 |
| `latitude`      | double        | Latitude of the ATC Facility                                                                                                                                                                                                             |
| `longitude`     | double        | Longitude of the ATC Facility                                                                                                                                                                                                            |
| `frequencyType` | ATCEntityType | Type of ATC Facility. _Enum:_ `"Ground = 0"`, `"Tower = 1"`, `"Unicom = 2"`, `"Clearance = 3"`, `"Approach = 4"`, `"Departure = 5"`, `"Center = 6"`, `"ATIS = 7"`, `"Aircraft = 8"`, `"Recorded = 9"`, `"Unknown = 10"`, `"Unused = 11"` |

# Get NOTAMs

Retrieve a list of all NOTAMs for a session.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/sessions/{sessionId}/notams`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name        | Located in | Description                                           | Required | Schema        |
| ----------- | ---------- | ----------------------------------------------------- | -------- | ------------- |
| `sessionId` | path       | ID of the session returned from the Sessions endpoint | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": [
    {
      "id": "53671e16-f937-47ca-a5db-c13b6a882851",
      "title": "Special Airport Procedures",
      "author": "Infinite Flight",
      "type": 0,
      "sessionId": "7e5dcd44-1fb5-49cc-bc2c-a9aab1f6a856",
      "radius": 3,
      "message": "Special Airport Procedures in Effect:\n\nNo straight in approached allowed RWY 13\n\nCC NDB 4500ft - 040° HDG Descending to SC NDB - Visual Right Turn Abeam Checkerboard to RWY 13\n\nPublished Approach Procedures (Found Online) Recommended",
      "longitude": 114.20680199460077,
      "latitude": 22.31646633312086,
      "icao": "VHHX",
      "floor": 0,
      "ceiling": 10000,
      "startTime": "2022-02-14T16:34:14.916",
      "endTime": "2100-01-01T16:34:00"
    },
    ...
  ]
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type          | Description                                                                                                                                                                                       |
| ----------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer       | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [NotamResult] | Array of FlightEntry objects                                                                                                                                                                      |

#### NotamResult

| Name        | Type              | Description                                                                     |
| ----------- | ----------------- | ------------------------------------------------------------------------------- |
| `id`        | string (uuid)     | Unique identifier for the NOTAM                                                 |
| `title`     | string            | Short title for NOTAM                                                           |
| `author`    | string            | Name of NOTAM author                                                            |
| `type`      | NotamType         | Type of NOTAM. _Enum:_ `"NOTAM" = 0, "TFR" = 1`                                 |
| `sessionId` | string (uuid)     | The ID of the session on which the NOTAM is published. `null` for all sessions. |
| `radius`    | float             | Radius of the NOTAM in NM                                                       |
| `message`   | string            | Main message for NOTAM                                                          |
| `latitude`  | double            | Decimal latitude of the center of the NOTAM                                     |
| `longitude` | double            | Decimal longitude of the center of the NOTAM                                    |
| `icao`      | string            | ICAO of the nearest airport to the NOTAM                                        |
| `floor`     | integer           | Lowest altitude of NOTAM in feet                                                |
| `ceiling`   | integer           | Highest altitude of NOTAM in feet                                               |
| `startTime` | string (datetime) | Time at which the NOTAM comes into effect                                       |
| `endTime`   | string (datetime) | Time at which the NOTAM expires                                                 |

# Get Aircraft

Retrieve a list of all aircraft models.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/aircraft`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": [
    {
      "id": "81d9ccd4-9c03-493a-811e-8fad3e57bd05",
      "name": "A-10"
    },
    ...
  ]
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type              | Description                                                                                                                                                                                       |
| ----------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer           | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [AircraftPackage] | Array of AircraftPackage objects                                                                                                                                                                  |

#### AircraftPacakge

| Name   | Type          | Description                     |
| ------ | ------------- | ------------------------------- |
| `id`   | string (uuid) | Unique identifier for the model |
| `name` | string        | Name of the aircraft            |

# Get Aircraft Liveries

Retrieve a list of all liveries for an aircraft model.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/aircraft/{aircraftId}/liveries`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Parameters

| Name         | Located in | Description                                                  | Required | Schema        |
| ------------ | ---------- | ------------------------------------------------------------ | -------- | ------------- |
| `aircraftId` | path       | ID of the aircraft model returned from the aircraft endpoint | Yes      | string (uuid) |

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": [
    {
      "id": "701c826b-a160-4b1a-a89f-08d0afb4af1b",
      "aircraftID": "710c84ae-6fdc-4c4a-ac3b-4031c3036e98",
      "aircraftName": "Airbus A220-300",
      "liveryName": "airBaltic"
    },
    ...
  ]
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type         | Description                                                                                                                                                                                       |
| ----------- | ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer      | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [LiveryData] | Array of LiveryData objects                                                                                                                                                                       |

#### LiveryData

| Name           | Type          | Description                      |
| -------------- | ------------- | -------------------------------- |
| `id`           | string (uuid) | Unique identifier for the livery |
| `aircraftID`   | string (uuid) | ID of the aircraft model         |
| `aircraftName` | string        | Name of the aircraft model       |
| `liveryName`   | string        | Name of the livery               |

# Get All Liveries

Retrieve a list of all aircraft liveries.

## Resource

**GET** `https://api.infiniteflight.com/public/v2/aircraft/liveries`

## Authorization

Include your API key (`<apikey>`) by either:

- Adding the `apikey` query parameter. For example, `?apikey=<apikey>`.
- Sending a bearer authorization header with your API key. For example, `Authorization: Bearer <apikey>`.

## Response

#### Sample Response

```json
{
  "errorCode": 0,
  "result": [
    {
      "id": "1d7dff42-46a5-4c47-a46c-bd39ab9cea8d",
      "aircraftID": "982dd974-5be7-4369-90c6-bd92863632ba",
      "aircraftName": "Airbus A318",
      "liveryName": "Generic"
    },
    ...
  ]
}
```

#### LiveAPIResponse

*Response Type:* `application/json`

| Name        | Type         | Description                                                                                                                                                                                       |
| ----------- | ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `errorCode` | integer      | _Enum:_ `"Ok = 0"`, `"UserNotFound = 1"`, `"MissingRequestParameters = 2"`, `"EndpointError = 3"`, `"NotAuthorized = 4"`, `"ServerNotFound = 5"`, `"FlightNotFound = 6"`, `"NoAtisAvailable = 7"` |
| `result`    | [LiveryData] | Array of LiveryData objects                                                                                                                                                                       |

#### LiveryData

| Name           | Type          | Description                      |
| -------------- | ------------- | -------------------------------- |
| `id`           | string (uuid) | Unique identifier for the livery |
| `aircraftID`   | string (uuid) | ID of the aircraft model         |
| `aircraftName` | string        | Name of the aircraft model       |
| `liveryName`   | string        | Name of the livery               |

# Live API Samples & Client Libraries

Below you can find a range of samples in a variety of languages to help you get started with the Live API. Current languages are PHP, NodeJS, Kotlin and C# - but we welcome contributions. Simply Fork and make a Pull Request to [velocity23/infiniteflightlive-samples](https://github.com/Velocity23/infiniteflightlive-samples) on GitHub or get in touch with us on the forum or Discord to get your project added.

- [NodeJS](https://github.com/Velocity23/infiniteflightlive-samples/tree/master/NodeJS)
- [C\#](https://github.com/Velocity23/infiniteflightlive-samples/tree/master/C%23)
- [Kotlin](https://github.com/A-FitzGerald/InfiniteLiveKt) (thanks @A-FitzGerald)
- [PHP](https://github.com/Velocity23/infiniteflightlive-samples/tree/master/PHP)
- [Python](https://github.com/SPEEDBIRD101-CLXVA/InfiniteFlight-LiveApi) (thanks @SPEEDBIRD101)
- [Swift](https://github.com/sqeezelemon/SwiftyLiveApi) (thanks @sqeezelemon)
