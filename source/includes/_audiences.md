# Audience Manager API

The Audience Manager is a comprehensive audience management system capable of creating and importing custom audiences
for use in ad campaigns. Evocalize preconfigures your organization to create audiences across our supporting marketing
channels automatically. Evocalize currently supports creating Facebook audiences.

Audience placeholders are used to associate channel-agnostic properties, such as customer data, with Evocalize-created
and partner-imported channel audiences. Audience placeholders can be associated with multiple audiences spanning
multiple channels. Blueprints that support audience targeting are preconfigured to select the best audience per
channel ad campaign from the audience placeholder's associated audiences.

Contact your support representative for more information regarding the Audience Manager.

## Create Audience Placeholder

> Create Audience Placeholder Request Example

```json
{
  "name": "Audience Placeholder Name",
  "description": "Audience Placeholder Description",
  "userId": "test_user_id",
  "groupId": "seattle_office",
  "groupRole": "group_admin"
}

```

> Create Audience Placeholder Response

```json
{
  "data": {
    "placeholderId": "1234",
    "name": "Audience Placeholder Name",
    "description": "Audience Placeholder Description",
    "url": "https://api.example.com/uploadCsvToDestination"
  }
}
```

This endpoint allows you to create an audience placeholder and generate a URL for uploading your customer data.

Upon the upload completion of your customer data, Evocalize will automatically begin creating audiences on behalf of
your organization. Audiences created by this process will be associated with the audience placeholder created by this
API. Audience creation can take up to 24 hours to complete.

Audiences created by this endpoint can be scoped into users and groups. Exclude user and group request properties to
specify audiences are available to all users within the organization. Users who have access to these audiences can view
them in the CMP and create ad campaigns using them.

The customer data file must be a CSV with at least 300 data identifiers, such as emails, and the first row must contain
the data identifier name of each column.

URLs expire after 24 hours. See `Generate Audience Placeholder URL` for generating new URLs.

### HTTP Request

`POST management/v1/audience`

### Create Audience Placeholder Request Properties

| Field       | Required | Type   | Description                                                                           |
|-------------|----------|--------|---------------------------------------------------------------------------------------|
| name        | true     | String | Name of the audience placeholder.                                                     |
| description | false    | String | Description of the audience placeholder. Fallbacks to name if not present.            |
| userId      | false    | String | The ID of the user that would own this placeholder and associated channel audiences.  |
| groupId     | false    | String | The ID of the group that would own this placeholder and associated channel audiences. |
| groupRole   | false    | String | The role of the user within the group                                                 |

**Response Codes**:

- `200 OK` - Returning the newly created audience placeholder's ID and generated URL.
- `400 BAD REQUEST` - When the request is malformed, or you attempt to associate to a non-existent user or group.

## Generate Audience Placeholder URL

> Generate Audience Placeholder URL Response

```json
{
  "data": {
    "placeholderId": "1234",
    "name": "Audience Placeholder Name",
    "description": "Audience Placeholder Description",
    "url": "https://api.example.com/uploadCsvToNewDestination"
  }
}
```

This endpoint generates a new audience placeholder URL for uploading your customer data. The generated URL will expire
after 24 hours.

**Response Codes**:

- `200 OK` - Returning the audience placeholder's ID and generated URL.
- `403 Forbidden` - User has insufficient privileges or audience placeholder does not exist.

### HTTP Request

`POST management/v1/audience/{placeholderId}/url`

### URL Params

| URL Param     | Required | Type   | Description                                                           |
|---------------|----------|--------|-----------------------------------------------------------------------|
| placeholderId | true     | String | The ID of the audience placeholder that would generate a new URL for. |

## Add Existing Channel Audience

> Add Existing Channel Audience Request Example

```json
{
  "channelAudienceId": "test_facebook_audience_id",
  "channel": "Facebook",
  "name": "Audience Placeholder Name",
  "description": "Audience Placeholder Description",
  "userId": "test_user_id",
  "groupId": "seattle_office",
  "groupRole": "group_admin"
}
```

> Add Existing Channel Audience Response

```json
{
  "data": {
    "placeholderId": "1234",
    "name": "Audience Placeholder Name",
    "description": "Audience Placeholder Description",
    "channelAudiences": [
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

This endpoint allows importing existing active channel audiences for use in ad campaigns. Evocalize will verify the
audience to be imported is accessible and ready to be used in ad campaigns. Currently, Evocalize supports importing
Facebook audiences.

Audiences imported by this endpoint can be scoped into users and groups. Exclude user and group request properties to
specify audience is available to all users within the organization. Users who have access to this audience can view it
in the CMP and create ad campaigns using them.

### HTTP Request

`POST management/v1/audience/provided`

### Add Existing Channel Audience Request Properties

| Field             | Required | Type   | Description                                                                          |
|-------------------|----------|--------|--------------------------------------------------------------------------------------|
| channelAudienceId | true     | String | The ID of the channel audience to be imported.                                       |
| channel           | true     | String | Name of the channel.                                                                 |
| name              | true     | String | Name of the audience placeholder you are creating.                                   |
| description       | true     | String | Description of the audience placeholder you are creating.                            |
| userId            | false    | String | The ID of the user that would own this placeholder and associated channel audience.  |
| groupId           | false    | String | The ID of the group that would own this placeholder and associated channel audience. |
| groupRole         | false    | String | The role of the user within the group                                                |

**Response Codes**:

- `200 OK` - Returning the newly created audience placeholder ID associated with the imported channel audience.
- `400 BAD REQUEST` - When the request is malformed, the audience is not active or accessible to Evocalize, or you
  attempt to associate to a non-existent user or group.
- `403 Forbidden` - User has insufficient privileges.

**Valid channel types**:

- Facebook

## Add Audience Placeholder Permissions

> Add Audience Placeholder Permissions Request Example (User)

```json
{
  "userId": "test_user_id"
}
```

> Add Audience Placeholder Permissions Request Example (Group)

```json
{
  "group": "test_group_id",
  "groupRole": "group_admin"
}
```

> Add Audience Placeholder Permissions Response

```json
{
  "data": {
    "placeholderId": "1234",
    "name": "Audience Placeholder Name",
    "description": "Audience Placeholder Description"
  }
}
```

This endpoint allows adding permissions to an audience placeholder so that users who have access to the placeholder can
view associated audiences in the CMP and create ad campaigns using them.

### HTTP Request

`POST management/v1/audience/{placeholderId}/url`

### Add Audience Placeholder Permissions Request Properties

| Field             | Required | Type   | Description                                                                          |
|-------------------|----------|--------|--------------------------------------------------------------------------------------|
| userId            | false    | String | The ID of the user that would own this placeholder and associated channel audience.  |
| groupId           | false    | String | The ID of the group that would own this placeholder and associated channel audience. |
| groupRole         | false    | String | The role of the user within the group                                                |

**Response Codes**:

- `200 OK` - Returning the audience placeholder ID associated.
- `403 Forbidden` - User has insufficient privileges or audience placeholder does not exist.

## Get Audiences Placeholders For User

> Get Audience Placeholders For User Response

```json
{
  "data": [
    {
      "placeholderId": "1234",
      "name": "Audience Placeholder Name",
      "description": "Audience Placeholder Description",
      "channelAudiences": [
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

| URL Param | Required | type   | Description                                                  |
|-----------|----------| ------ |--------------------------------------------------------------|
| userId    | true     | String | The ID of the user you are wanting to retrieve audiences for |

**Response Codes**:

- `200 OK` - Returning the available placeholders and associated audiences for a user.
- `404 NOT FOUND` - When the request is malformed or the user does not exist.
