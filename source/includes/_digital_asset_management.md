# Digital Asset Management API

The Digital Asset Management API allows for the storage and permissions of various media assets. Media assets are
references to media files that are suitable for being used in an advertising creative in at least one of the marking
channels supported by the Evocalize platform. Currently, we support image and video assets.

This API can be used to import partner-owned advertising media into their Media Library. You can control who can use
media assets by leveraging our standard access control system. `READ` is currently the only valid permission for media
assets. See the [Access Control](#access-control) section for more details.

## Create Or Update Media

> Create Or Update Request Example

```json
{
  "id": "1234",
  "name": "Image Name",
  "type": "image",
  "url": "https://api.example.com/image.png",
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

> Create Or Update Response

```json
{
  "data": {
    "id": "1234",
    "name": "Image Name",
    "type": "image",
    "url": "https://api.example.com/image.png"
  }
}
```

This endpoint allows you to create or update media within your organization.

Users having the ability to read the media will be able to see it in their Media Library via the CMP and create/edit ad
campaigns with it. After creation of media, the media type is immutable. On update, the URL and name can change but not
the media type.

### HTTP Request

`POST management/v1/media`

### Create or Update Media Request Properties

| Field  | Required | Type       | Description                                                                                         |
|--------|----------|------------|-----------------------------------------------------------------------------------------------------|
| id     | true     | String     | ID of the media you are created/updating. This must be unique per media                             |
| name   | true     | String     | Name of the media you are creating/updating.                                                        |
| type   | true     | String     | The media type associated with the URL. Cannot change on updates. Valid types are `image`, `video`. |
| url    | true     | String     | A public URL to the media you would like to import.                                                 |
| grants | false    | Grant List | A list of grants that define access to the media asset.                                             |                                                                                          |

**Valid media types**:

- image
- video

**Response Codes**:

- `200 OK` - Returning the newly created or updated media.
- `400 BAD REQUEST` - When the request is malformed, or you attempt to associate to a non-existent user.

## Get Media

> Get Media Response

```json
{
  "data": {
    "id": "1234",
    "name": "Image Name",
    "type": "image",
    "url": "https://api.example.com/image.png"
  }
}
```

This endpoint returns the media details, specified by `id` in the request path.

### HTTP Request

`GET management/v1/media/{id}`

### URL Params

| URL Param | Required | type   | Description                                            |
|-----------|----------|--------|--------------------------------------------------------|
| id        | true     | String | The ID of the media asset you are wanting to retrieve. |

**Response Codes**:

- `200 OK` - Returning the media details.
- `403 FORBIDDEN` - User has insufficient privileges or media asset does not exist.

## Delete Media

> Delete Media response

```
No Body returned
``` 

This allows deleting media from your organization.

Once media is deleted, users will not be able to use it in new ad campaigns.
However, active ad campaigns referencing deleted media will continue using the media until completion.

Media created via the Create Or Update Media API can only be deleted via this API.
Users owning the media cannot delete the media from their Media Library via the CMP.

### HTTP Request

`DELETE management/v1/media/{id}`

**Response Codes**:

- `200 OK`

## Update Media Permissions

> Update Media Permissions Request Example (A group with an additional user)

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

> Update Media Permissions Response

```json
{
  "data": {
    "id": "1234",
    "name": "Image Name",
    "type": "image",
    "url": "https://api.example.com/image.png"
  }
}
```

You can control who can use media assets by leveraging our standard access control system.
This endpoint allows for creating/updating grants on the media asset. Grants are upserted
based off of the grantee, so only grants for the given grantees in the request will be touched. Any
grantees not included in your request will remain untouched.
`READ` is currently the only valid permission for media assets. You can also provide an empty
permissions list in a grant to remove access to the media asset. See the [Access Control](#access-control)
section for more details.

### HTTP Request

`POST management/v1/media/{id}/grants`

### Add Media Permissions Request Properties

| Field  | Required | Type       | Description                                             |
|--------|----------|------------|---------------------------------------------------------|
| grants | false    | Grant List | A list of grants that define access to the media asset. |

**Response Codes**:

- `200 OK` - Returning the media asset details.
- `403 FORBIDDEN` - User has insufficient privileges or media asset does not exist.

## Get Media Permissions

> Get Media Permissions Response

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

This endpoint returns all grants that exist on the media, specified by `id`
in the request path.

### HTTP Request

`GET management/v1/media/{id}/grants`

### URL Params

| URL Param | Required | type   | Description                                                       |
|-----------|----------|--------|-------------------------------------------------------------------|
| id        | true     | String | The ID of the media asset you are wanting to retrieve grants for. |

**Response Codes**:

- `200 OK` - Returning the grants associated with the media asset.
- `403 FORBIDDEN` - User has insufficient privileges or media asset does not exist.
