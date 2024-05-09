# Audience Manager API

The Audience Manager is a comprehensive audience management system capable of creating, importing, and managing custom
audiences for use in marketing campaigns. Evocalize currently supports the creation and management of Meta and Google audiences.

Audience Placeholders are used to associate channel-agnostic audience properties, such as customer data, with multiple
audiences spanning multiple channels. Audience Placeholders can be associated with Evocalize-created or partner-provided
audiences. Blueprints that support audience targeting are preconfigured to select the best audience per channel from the
Audience Placeholder's associated audiences.

Contact your Client Success representative for more information regarding the Audience Manager.

### Evocalize-Created Audiences

Audience Placeholders can be associated with audiences created by Evocalize using customer data collected and provided
by your organization. Evocalize configures a set of rules for your organization for determining the type of audience(s)
to create per channel, the account to create the audience under, and the account(s) to share the audience with.

The creation process for an Audience Placeholder and associated audiences is:

1. Create an Audience Placeholder using the `Create Audience Placeholder API`. A successful response should include the
   newly created Audience Placeholder with a `pending` status. No audiences are associated with it.
2. Upload your customer data using the `Upload Audience CSV Data API`. Upon completion of uploading your customer data,
   Evocalize will automatically begin creating audiences on behalf of your organization. A successful response should
   include the Audience Placeholder updated to a `processing` status.
3. Audience creation can take up to 24 hours to complete. Channel-specific audiences created by this process will be
   associated with the Audience Placeholder. Once audience creation is complete, the Audience Placeholder will be
   updated to a `completed` status. Moreover, Audience Placeholder and associated audiences can be used in blueprints
   supporting audience targeting. You can check the status of the Audience Placeholder and associated audiences using
   the `Get Audience Placeholder By ID API`.

### Partner-Provided Audiences

Audience Placeholders can be associated with active, existing channel-specific audiences provided by your organization.
See `Add Existing Channel Audience API` for more information. Audience Placeholders that are created from a
partner-provided audience can immediately be used in blueprints supporting audience targeting.

## Create Audience Placeholder

> Create Audience Placeholder Request Example

```json
{
  "name": "Audience Placeholder Name",
  "description": "Audience Placeholder Description",
  "tags": [
    "Tag 1"
  ],
  "grants": [
    {
      "grantee": {
        "type": "USER",
        "userId": "exampleUserId"
      },
      "permissions": [
        "READ"
      ]
    }
  ]
}

```

> Create Audience Placeholder Response

```json
{
  "data": {
    "placeholderId": "1234",
    "name": "Audience Placeholder Name",
    "description": "Audience Placeholder Description",
    "status": "pending",
    "tags": [
      "Tag 1"
    ]
  }
}
```

This endpoint allows you to create an Audience Placeholder which will be used to associate audiences created
by Evocalize. Successfully created Audience Placeholders will have a `pending` status.

You can control who can use audience placeholders by leveraging our standard access control system.
`READ` is currently the only valid permission for audience placeholders. See the [Access Control](#access-control)
section for more details.

### HTTP Request

`POST management/v1/audience`

### Create Audience Placeholder Request Properties

| Field       | Required | Type        | Description                                                                                                                            |
|-------------|----------|-------------|----------------------------------------------------------------------------------------------------------------------------------------|
| name        | true     | String      | The name of the Audience Placeholder.                                                                                                  |
| description | false    | String      | The description of the Audience Placeholder. Visible to users in the CMP. Fallbacks to the Audience Placeholder's name if not present. |
| tags        | false    | String List | A list of tags to add to the Audience Placeholder.                                                                                     |
| grants      | false    | Grant List  | A list of grants that define access to the audience placeholder.                                                                       |                                                                                          

**Response Codes**:

- `200 OK` - Returning the newly created Audience Placeholder details including the ID.
- `400 BAD REQUEST` - When the request is malformed, or you attempt to associate to a non-existent user or group.

## Upload Audience Data CSV

After Evocalize creates audiences on behalf of your organization, these audiences will need to be assigned users using
customer data collected by your organization. This customer data can be provided to Evocalize in the form of a CSV and
uploaded via this endpoint.

Requirements:

1. The CSV must contain a header row which is the first row containing the data identifier name of each column.
2. Every data identifier in the CSV must be hashed as SHA-256 and comply with the requirements listed in the table
   below. Moreover, there must be at least one data identifier present in your CSV.
3. In the case of incomplete data for a specific data identifier, omit the header and column data for that data
   identifier. For example, if you only have a list of email addresses then include only the email address header and
   column data in your CSV.
4. The CSV must have at least 300 data identifiers to ensure a minimum of 100 matches.
5. Although any data identifiers can be present in your CSV, we encourage including a minimum of email or phone. Emails
   can drive stronger match rates compared to other supported data identifiers.

> Audience Data CSV Example (Plain Text)

```text
email,phone,firstname,lastname
emailaddress@example.com,15129876543,test,user
``` 

> Audience Data CSV Example (SHA-256)

```text
email,phone,firstname,lastname
d42d751294f09be1b195bdea0f5049af7c7da93a99f4c688705ddcacdd16b4c1,88fb2986c52c22fbc4038a49ddd37b3650d0c52806fe520b288672770dc14730,9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08,04f8996da763b7a969b1028ee3007569eaf3a635486ddab211d512c85b9df8fb
```

> Multipart Form Request Example

```text
POST management/v1/audience/{placeholderId}/csv/upload
Content-Type: multipart/form-data; boundary=AUDIENCE-CSV-BOUNDARY
# Authentication Headers

--AUDIENCE-CSV-BOUNDARY
Content-Disposition: form-data; name="file"; filename="hashed_audience_data.csv"
Content-Type: text/csv

email,phone,firstname,lastname
d42d751294f09be1b195bdea0f5049af7c7da93a99f4c688705ddcacdd16b4c1,88fb2986c52c22fbc4038a49ddd37b3650d0c52806fe520b288672770dc14730,9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08,04f8996da763b7a969b1028ee3007569eaf3a635486ddab211d512c85b9df8fb
--AUDIENCE-CSV-BOUNDARY--
```

| Data Identifier | Header    | Description                                                                                        |
|-----------------|-----------|----------------------------------------------------------------------------------------------------|
| Email Address   | email     | Email must be lowercase, and not have any white space characters.                                  |
| Phone           | phone     | Phone must contain only digits, not have any leading zeros, and be prefixed with the country code. |
| First Name      | firstname | First name must be lowercase, have no punctuations, and be UTF-8 formatted.                        |
| Last Name       | lastname  | Last name must be lowercase, have no punctuations, and be UTF-8 formatted.                         |

> Upload Audience Data CSV Response

```json
{
  "data": {
    "placeholderId": "1234",
    "name": "Audience Placeholder Name",
    "description": "Audience Placeholder Description",
    "status": "processing",
    "tags": [
      "Tag 1"
    ]
  }
}
```

This multipart form endpoint can be used to upload your audience data in the form of a CSV. The data must be hashed as
SHA-256. When uploading your CSV via this multipart endpoint, specify the CSV under the `file` key in the multipart
form.

### HTTP Request (Multipart Form)

`POST management/v1/audience/{placeholderId}/csv/upload`

### URL Params

| URL Param     | Required | Type   | Description                         |
|---------------|----------|--------|-------------------------------------|
| placeholderId | true     | String | The ID of the Audience Placeholder. |

### Upload Audience Data CSV Multipart Form Request

| Field | Required | Type | Description                                             |
|-------|----------|------|---------------------------------------------------------|
| file  | true     | CSV  | A CSV file containing the SHA-256 hashed audience data. |

**Response Codes**:

- `200 OK` - Returning the Audience Placeholder's ID and a URL for uploading your customer data.
- `400 BAD REQUEST` - When the request is malformed, the audience is already processing changes, or the CSV is invalid.
- `403 FORBIDDEN` - User has insufficient privileges or Audience Placeholder does not exist.

## Add Existing Channel Audience

> Add Existing Channel Audience Request Example

```json
{
  "channel": "Facebook",
  "channelAudienceId": "test_facebook_audience_id",
  "name": "Audience Placeholder Name",
  "description": "Audience Placeholder Description",
  "tags": [
    "Tag 1"
  ],
  "grants": [
    {
      "grantee": {
        "type": "USER",
        "userId": "exampleUserId"
      },
      "permissions": [
        "READ"
      ]
    }
  ]
}
```

> Add Existing Channel Audience Response

```json
{
  "data": {
    "placeholderId": "1234",
    "name": "Audience Placeholder Name",
    "description": "Audience Placeholder Description",
    "status": "completed",
    "tags": [
      "Tag 1"
    ],
    "audiences": [
      {
        "channel": "Facebook",
        "channelAudienceId": "test_audience_id",
        "name": "Facebook Audience Name",
        "description": "Facebook Audience Description",
        "type": "custom",
        "potentialSize": "2700000",
        "status": "active"
      }
    ]
  }
}
```

This endpoint allows importing existing active channel audiences for use in marketing campaigns. Evocalize will verify
the audience to be imported is accessible and ready to be used in marketing campaigns. An Audience Placeholder will be
created and associated with this existing channel audience Currently, Evocalize supports importing Meta audiences.

You can control access to the imported audience by leveraging our standard access control system.
`READ` is currently the only valid permission for imported audiences. See the [Access Control](#access-control)
section for more details.

### HTTP Request

`POST management/v1/audience/provided`

### Add Existing Channel Audience Request Properties

| Field             | Required | Type                 | Description                                                                                                                  |
|-------------------|----------|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| channel           | true     | String               | The name of the channel the audience was created under.                                                                      |
| channelAudienceId | true     | String               | The ID of the channel-specific audience to be imported.                                                                      |
| name              | false    | String               | The name of the Audience Placeholder you are creating. Fallbacks to the channel audience's name if not present               |
| description       | false    | String               | The description of the Audience Placeholder you are creating. Fallbacks to the channel audience's description if not present |
| tags              | false    | String List          | A list of tags to add to the Audience Placeholder.                                                                           |
| grants            | false    | [Grant](#grant) List | A list of grants that define access to the audience placeholder.                                                             |

**Response Codes**:

- `200 OK` - Returning the newly created Audience Placeholder ID associated with the imported channel audience.
- `400 BAD REQUEST` - When the request is malformed, the audience is not active or accessible to Evocalize, or you
  attempt to associate to a non-existent user or group.
- `403 FORBIDDEN` - User has insufficient privileges or Audience Placeholder does not exist.

**Valid channel types**:

- Facebook

## Update Audience Placeholder Permissions

> Update Audience Placeholder Permissions Request Example (A group with an additional user)

```json
{
  "grants": [
    {
      "grantee": {
        "type": "GROUP",
        "groupId": "exampleGroupId"
      },
      "permissions": [
        "READ"
      ]
    },
    {
      "grantee": {
        "type": "USER",
        "userId": "exampleUserId"
      },
      "permissions": [
        "READ"
      ]
    }
  ]
}
```

> Update Audience Placeholder Permissions Response

```json
{
  "data": {
    "placeholderId": "1234",
    "name": "Audience Placeholder Name",
    "description": "Audience Placeholder Description",
    "status": "completed",
    "tags": [
      "Tag 1"
    ]
  }
}
```

You can control who can use audience placeholders by leveraging our standard access control system.
This endpoint allows for creating/updating grants on the audience placeholder. Grants are upserted
based off of the grantee, so only grants for the given grantees in the request will be touched. Any
grantees not included in your request will remain untouched.
`READ` is currently the only valid permission for audience placeholders. You can also provide an empty
permissions list in a grant to remove access to the audience placeholder. See the [Access Control](#access-control)
section for more details.

### HTTP Request

`POST management/v1/audience/{placeholderId}/grants`

### Add Audience Placeholder Permissions Request Properties

| Field  | Required | Type                 | Description                                                      |
|--------|----------|----------------------|------------------------------------------------------------------|
| grants | false    | [Grant](#grant) List | A list of grants that define access to the audience placeholder. |

**Response Codes**:

- `200 OK` - Returning the Audience Placeholder details.
- `403 FORBIDDEN` - User has insufficient privileges or Audience Placeholder does not exist.

## Get Audience Placeholder Permissions

> Get Audience Placeholder Permissions Response

```json
{
  "data": {
    "grants": [
      {
        "grantee": {
          "type": "GROUP",
          "groupId": "exampleGroupId"
        },
        "permissions": [
          "READ"
        ]
      },
      {
        "grantee": {
          "type": "USER",
          "userId": "exampleUserId"
        },
        "permissions": [
          "READ"
        ]
      }
    ]
  }
}
```

This endpoint returns all grants that exist on the placeholder, specified by `placeholderId`
in the request path.

### HTTP Request

`GET management/v1/audience/{placeholderId}/grants`

### URL Params

| URL Param     | Required | type   | Description                                                     |
|---------------|----------|--------|-----------------------------------------------------------------|
| placeholderId | true     | String | The ID of the Audience Placeholder you are wanting to retrieve. |

### Request Params

| Param     | Required | Description                                                                                             |
|-----------|----------|---------------------------------------------------------------------------------------------------------|
| pageToken | false    | The page token (taken from `nextPageToken` provided in the response) for the page you want to retrieve. |

**Response Codes**:

- `200 OK` - Returning the grants associated with the Audience Placeholder ID.
- `403 FORBIDDEN` - User has insufficient privileges or Audience Placeholder does not exist.

### Response properties

An example is shown to the right. The `data` property contains a json object holding all the grants you retrieved.
See [Access Control](#access-control) for full details. `READ` is the only valid permission for audience access.

| Field  | Nullable | Type                            | Description                     |
|--------|----------|---------------------------------|---------------------------------|
| grants | false    | List of [Grant](#grant) objects | A list of the grants retrieved. |

## Get Audience Placeholders

Returns Audience Placeholders and their associated audiences.


### HTTP Request

`GET management/v1/audience

### Request Params

| Param     | Required | Description                                                                                             |
|-----------|----------|---------------------------------------------------------------------------------------------------------|
| pageToken | false    | The page token (taken from `nextPageToken` provided in the response) for the page you want to retrieve. |

**Response Codes**:

- `200 OK` - Returning the available Audience Placeholders and associated audiences for a user.
- `404 NOT FOUND` - When the request is malformed.

## Get Audience Placeholder By ID

Returns the Audience Placeholder and associated audiences.


> Get Audience Placeholder By ID Response (Audience Creation Pending, Requires CSV Upload)

```json
{
  "data": {
    "placeholderId": "1234",
    "name": "Audience Placeholder Name",
    "description": "Audience Placeholder Description",
    "status": "pending",
    "tags": [
      "Tag 1"
    ],
    "audiences": []
  }
}
```

> Get Audience Placeholder By ID Response (Audience Creation In Progress)

```json
{
  "data": {
    "placeholderId": "1234",
    "name": "Audience Placeholder Name",
    "description": "Audience Placeholder Description",
    "status": "processing",
    "tags": [
      "Tag 1"
    ],
    "audiences": [
      {
        "channel": "Facebook",
        "channelAudienceId": "test_audience_id",
        "name": "Facebook Audience Name",
        "description": "Facebook Audience Description",
        "type": "custom",
        "potentialSize": null,
        "status": "pending"
      }
    ]
  }
}
```

> Get Audience Placeholder By ID Response (Audience Creation Completed)

```json
{
  "data": {
    "placeholderId": "1234",
    "name": "Audience Placeholder Name",
    "description": "Audience Placeholder Description",
    "status": "completed",
    "tags": [
      "Tag 1"
    ],
    "audiences": [
      {
        "channel": "Facebook",
        "channelAudienceId": "test_audience_id",
        "name": "Facebook Audience Name",
        "description": "Facebook Audience Description",
        "type": "custom",
        "potentialSize": "2700000",
        "status": "active"
      }
    ]
  }
}
```

### HTTP Request

`GET management/v1/audience/{placeholderId}`

### URL Params

| URL Param     | Required | type   | Description                                                     |
|---------------|----------|--------|-----------------------------------------------------------------|
| placeholderId | true     | String | The ID of the Audience Placeholder you are wanting to retrieve. |

### Get Audience Placeholder By ID Response Properties

| Field         | Nullable | Type        | Description                                                                 |
|---------------|----------|-------------|-----------------------------------------------------------------------------|
| placeholderId | false    | String      | The ID of the Audience Placeholder.                                         |
| name          | false    | String      | The name of the Audience Placeholder.                                       |
| description   | false    | String      | The description of the Audience Placeholder.                                |
| status        | false    | String      | The status of the Audience Placeholder.                                     |
| tags          | false    | String List | A list of tags to add to the Audience Placeholder.                          |
| audiences     | false    | JSON ARRAY  | JSON Array of Audience objects. List includes created and active audiences. |

**Placeholder Statuses**:

- `pending` - Audience Placeholder has no associated audiences and requires a CSV upload to begin creating audiences.
- `processing` - Audience Placeholder is processing changes from the most recent successfully uploaded audience data
  CSV.
- `completed` - Audience Placeholder has completed processing changes from the most recent successfully uploaded
  audience data CSV.

### Audience Response Properties

| Field             | Nullable | Type   | Description                                                                                                                                                                     |
|-------------------|----------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| channel           | false    | String | The name of the channel that audience is created under.                                                                                                                         |
| channelAudienceId | false    | String | The ID of the channel-specific audience.                                                                                                                                        |
| name              | false    | String | The name of the channel-specific audience.                                                                                                                                      |
| description       | false    | String | The description of the channel-specific audience.                                                                                                                               |
| type              | false    | String | The type of the channel-specific audience.                                                                                                                                      |
| potentialSize     | true     | String | The approximate size of the channel-specific audience. Returns `null` if audience is being created, `-1` for inactive lookalike audiences and `< 1000` when size is below 1000. |
| status            | false    | String | The status of the channel-specific audience.                                                                                                                                    |

**Audience Statuses**:

- `active` - Audience is active and ready to be used in marketing campaigns.
- `inactive` - Audience is inactive and cannot be used in marketing campaigns. Updates can be applied to activate
  audience.
- `pending` - Audience is processing creation or updates. Audiences can be used in marketing campaigns but will not be
  applied until processing is finished.
- `deleted` - Audience is deleted and cannot be used in marketing campaigns. Updates cannot be applied to activate
  audience.

**Response Codes**:

- `200 OK` - Returning the available Audience Placeholders and associated audiences for a user.
- `404 NOT FOUND` - When the request is malformed.

## Get Audiences Placeholders For User

Returns a list of Audience Placeholders and associated audiences available to the user.

> Get Audience Placeholders For User Response

```json
{
  "data": [
    {
      "placeholderId": "1234",
      "name": "Audience Placeholder Name",
      "description": "Audience Placeholder Description",
      "status": "completed",
      "tags": [
        "Tag 1"
      ],
      "audiences": [
        {
          "channel": "Facebook",
          "channelAudienceId": "test_audience_id",
          "name": "Facebook Audience Name",
          "description": "Facebook Audience Description",
          "type": "custom",
          "potentialSize": "2700000",
          "status": "active"
        }
      ]
    }
  ]
}
```

### HTTP Request

`GET management/v1/user/{userId}/audiences`

### URL Params

| URL Param | Required | type   | Description                                                   |
|-----------|----------|--------|---------------------------------------------------------------|
| userId    | true     | String | The ID of the user you are wanting to retrieve audiences for. |

### Get Audience Placeholders For User Response Properties

| Field         | Nullable | Type        | Description                                                                 |
|---------------|----------|-------------|-----------------------------------------------------------------------------|
| placeholderId | false    | String      | The ID of the Audience Placeholder that is user-accessible.                 |
| name          | false    | String      | The name of the Audience Placeholder.                                       |
| description   | false    | String      | The description of the Audience Placeholder.                                |
| status        | false    | String      | The status of the Audience Placeholder.                                     |
| tags          | false    | String List | A list of tags to add to the Audience Placeholder.                          |
| audiences     | false    | JSON ARRAY  | JSON Array of Audience objects. List includes created and active audiences. |

### Audience Response Properties

| Field             | Nullable | Type   | Description                                                                                                                                                                     |
|-------------------|----------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| channel           | false    | String | The name of the channel that audience is created under.                                                                                                                         |
| channelAudienceId | false    | String | The ID of the channel-specific audience.                                                                                                                                        |
| name              | false    | String | The name of the channel-specific audience.                                                                                                                                      |
| description       | false    | String | The description of the channel-specific audience.                                                                                                                               |
| type              | false    | String | The type of the channel-specific audience.                                                                                                                                      |
| potentialSize     | true     | String | The approximate size of the channel-specific audience. Returns `null` if audience is being created, `-1` for inactive lookalike audiences and `< 1000` when size is below 1000. |
| status            | false    | String | The status of the channel-specific audience.                                                                                                                                    |

**Audience Statuses**:

- `active` - Audience is active and ready to be used in marketing campaigns.
- `inactive` - Audience is inactive and cannot be used in marketing campaigns. Updates can be applied to activate
  audience.
- `pending` - Audience is processing creation or updates. Audiences can be used in marketing campaigns but will not be
  applied until processing is finished.
- `deleted` - Audience is deleted and cannot be used in marketing campaigns. Updates cannot be applied to activate
  audience.

**Response Codes**:

- `200 OK` - Returning the available Audience Placeholders and associated audiences for a user.
- `404 NOT FOUND` - When the request is malformed or the user does not exist.
