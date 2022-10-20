# Audience Manager API

The Audience Manager is a comprehensive audience management system capable of creating, importing, and managing custom
audiences for use in marketing campaigns. Evocalize currently supports the creation and management of Meta audiences.

Audience Placeholders are used to associate channel-agnostic audience properties, such as customer data, with multiple
audiences spanning multiple channels. Audience Placeholders can be associated with Evocalize-created or partner-provided
audiences. Blueprints that support audience targeting are preconfigured to select the best audience per channel from the
Audience Placeholder's associated audiences.

Contact your Client Success representative for more information regarding the Audience Manager.

### Evocalize-Created Audiences

Audience Placeholders can be associated with audiences created by Evocalize using customer data collected and provided
by your organization. Evocalize configures a set of rules for your organization for determining the type of audience(s)
to create per channel, the account to create the audience under, and the account(s) to share the audience with.

The creation process starts with creating an Audience Placeholder using the `Create Audience Placeholder API`. Once an
Audience Placeholder has been created, your organization will need to upload a CSV containing your audience data to the
URL in the API response. Upon completion of uploading your customer data, Evocalize will automatically begin creating
audiences on behalf of your organization. Audiences created by this process will be associated with the Audience
Placeholder created by this API. Audience creation can take up to 24 hours to complete.

> Audience Data CSV Example

```text
email
emailAddress01@example.com
emailAddress02@example.com
emailAddress03@example.com
``` 

Audience data needs to be uploaded in the form of a CSV so Evocalize can assign users to the newly created audiences.
Moreover, the CSV must be in plain text and the first row must contain the data identifier name of each column. Ideally,
the CSV must have at least 300 data identifiers to ensure a minimum of 100 matches. Below is a list of the supported
data identifiers.

| Data Identifier | Header | Description                    |
|-----------------|--------|--------------------------------|
| Email Address   | email  | The email address of the user. |

### Partner-Provided Audiences

Audience Placeholders can be associated with active, existing channel-specific audiences provided by your organization.
See `Add Existing Channel Audience API` for more information.

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

This endpoint allows you to create an Audience Placeholder and generate a URL for uploading your customer data. The
generated URL will expire after 24 hours. See `Generate Audience Placeholder URL` for generating new URLs.

Audience Placeholders and associated audiences can be scoped to users, groups, or all users in the organization. Specify
one of the following in your request to limit the scope:

1. If `userId` is present, the user can view and use the Audience Placeholder and associated audiences
2. If `groupId` is present, all users in the group can view and use the Audience Placeholder and associated audiences
3. If `groupId` and `groupRole` are present, the users in the group and with role can view and use the Audience
   Placeholder and
   associated audiences.
4. If neither `userId` nor `groupId` are present, all users in the organization can view and use the Audience
   Placeholder and associated audiences.

### HTTP Request

`POST management/v1/audience`

### Create Audience Placeholder Request Properties

| Field       | Required | Type   | Description                                                                                                                            |
|-------------|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------|
| name        | true     | String | The name of the Audience Placeholder.                                                                                                  |
| description | false    | String | The description of the Audience Placeholder. Visible to users in the CMP. Fallbacks to the Audience Placeholder's name if not present. |
| userId      | false    | String | The ID of the user that would own this Audience Placeholder and associated channel audiences.                                          |
| groupId     | false    | String | The ID of the group that would own this Audience Placeholder and associated channel audiences.                                         |
| groupRole   | false    | String | The role of the user within the group.                                                                                                 |

**Response Codes**:

- `200 OK` - Returning the newly created Audience Placeholder's ID and generated URL.
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

This endpoint generates a new Audience Placeholder URL for uploading your customer data. The generated URL will expire
after 24 hours.

**Response Codes**:

- `200 OK` - Returning the Audience Placeholder's ID and a URL for uploading your customer data.
- `403 Forbidden` - User has insufficient privileges or Audience Placeholder does not exist.

### HTTP Request

`POST management/v1/audience/{placeholderId}/url`

### URL Params

| URL Param     | Required | Type   | Description                                                           |
|---------------|----------|--------|-----------------------------------------------------------------------|
| placeholderId | true     | String | The ID of the Audience Placeholder that would generate a new URL for. |

## Add Existing Channel Audience

> Add Existing Channel Audience Request Example

```json
{
  "channel": "Facebook",
  "channelAudienceId": "test_facebook_audience_id",
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

This endpoint allows importing existing active channel audiences for use in marketing campaigns. Evocalize will verify
the audience to be imported is accessible and ready to be used in marketing campaigns. An Audience Placeholder will be
created and associated with this existing channel audience Currently, Evocalize supports importing Meta audiences.

The imported audience can be scoped to users, groups, or all users in the organization. Specify one of the following in
your request to limit the scope to the imported audience and associated Audience Placeholder:

1. If `userId` is present, the user can view and use the Audience Placeholder and associated audiences
2. If `groupId` is present, all users in the group can view and use the Audience Placeholder and associated audiences
3. If `groupId` and `groupRole` are present, the users in the group and with role can view and use the Audience
   Placeholder and
   associated audiences.
4. If neither `userId` nor `groupId` are present, all users in the organization can view and use the Audience
   Placeholder and associated audiences.

### HTTP Request

`POST management/v1/audience/provided`

### Add Existing Channel Audience Request Properties

| Field             | Required | Type   | Description                                                                                                                  |
|-------------------|----------|--------|------------------------------------------------------------------------------------------------------------------------------|
| channel           | true     | String | The name of the channel the audience was created under.                                                                      |
| channelAudienceId | true     | String | The ID of the channel-specific audience to be imported.                                                                      |
| name              | false    | String | The name of the Audience Placeholder you are creating. Fallbacks to the channel audience's name if not present               |
| description       | false    | String | The description of the Audience Placeholder you are creating. Fallbacks to the channel audience's description if not present |
| userId            | false    | String | The ID of the user that would own this Audience Placeholder and associated channel audience.                                 |
| groupId           | false    | String | The ID of the group that would own this Audience Placeholder and associated channel audience.                                |
| groupRole         | false    | String | The role of the user within the group.                                                                                       |

**Response Codes**:

- `200 OK` - Returning the newly created Audience Placeholder ID associated with the imported channel audience.
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

This endpoint allows adding permissions to an Audience Placeholder so that users who have access can view associated
audiences in the CMP and create ad campaigns using them. Specify one of the following in your request to limit the scope
to the associated Audience Placeholder

1. If `userId` is present, the user can view and use the Audience Placeholder and associated audiences
2. If `groupId` is present, all users in the group can view and use the Audience Placeholder and associated audiences
3. If `groupId` and `groupRole` are present, the users in the group and with role can view and use the Audience
   Placeholder and
   associated audiences.
4. If neither `userId` nor `groupId` are present, all users in the organization can view and use the Audience
   Placeholder and associated audiences.

### HTTP Request

`POST management/v1/audience/{placeholderId}/url`

### Add Audience Placeholder Permissions Request Properties

| Field             | Required | Type   | Description                                                                                   |
|-------------------|----------|--------|-----------------------------------------------------------------------------------------------|
| userId            | false    | String | The ID of the user that would own this Audience Placeholder and associated channel audience.  |
| groupId           | false    | String | The ID of the group that would own this Audience Placeholder and associated channel audience. |
| groupRole         | false    | String | The role of the user within the group.                                                        |

**Response Codes**:

- `200 OK` - Returning the Audience Placeholder ID associated.
- `403 Forbidden` - User has insufficient privileges or Audience Placeholder does not exist.

## Get Audiences Placeholders For User

Returns a list of Audience Placeholders and associated channel audiences available to the user.

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

| URL Param | Required | type   | Description                                                   |
|-----------|----------|--------|---------------------------------------------------------------|
| userId    | true     | String | The ID of the user you are wanting to retrieve audiences for. |

### Get Audience Placeholders For User Response Properties

| Field            | Nullable | Type       | Description                                                                        |
|------------------|----------|------------|------------------------------------------------------------------------------------|
| placeholderId    | false    | String     | The ID of the Audience Placeholder that is user-accessible.                        |
| name             | false    | String     | The name of the Audience Placeholder.                                              |
| description      | false    | String     | The description of the Audience Placeholder.                                       |
| channelAudiences | false    | JSON ARRAY | JSON Array of ChannelAudience objects. List includes created and active audiences. |

### Channel Audience Properties

| Field             | Nullable | Type   | Description                                                                                                                                |
|-------------------|----------|--------|--------------------------------------------------------------------------------------------------------------------------------------------|
| channel           | false    | String | The name of the channel that audience is created under.                                                                                    |
| channelAudienceId | false    | String | The ID of the channel-specific audience.                                                                                                   |
| name              | false    | String | The name of the channel-specific audience.                                                                                                 |
| description       | false    | String | The description of the channel-specific audience.                                                                                          |
| type              | false    | String | The type of the channel-specific audience.                                                                                                 |
| potentialSize     | false    | String | The approximate size of the channel-specific audience. Returns `-1` for inactive lookalike audiences and `< 1000` when size is below 1000. |
| status            | false    | String | The status of the channel-specific audience.                                                                                               |

**Audience Statuses**:

- `active` - Audience is active and ready to be used in marketing campaigns.
- `inactive` - Audience is inactive and cannot be used in marketing campaigns. Updates can be applied to activate
  audience.
- `pending` - Audience is processing creation or updates. Audiences can be used in marketing campaigns but will not be
  applied until processing is finished.
- `deleted` - Audience is deleted and cannot be used in marketing campaigns. Updates cannot be applied to activate
  audience.

**Response Codes**:

- `200 OK` - Returning the available Audience Placeholders and associated channel audiences for a user.
- `404 NOT FOUND` - When the request is malformed or the user does not exist.
