# Media Library API

The Evocalize CMP Media Library allows for the storage and permissions of various media assets. Media assets are
references to media files that are suitable for being used in an advertising creative in at least one of the marking
channels supported by the Evocalize platform. Currently, we support image and video assets.

This API can be used to import partner-owned advertising media into their Media Library.

## Create Or Update Media

> Create Or Update Request Example

```json
{
  "id": "1234",
  "name": "Image Name",
  "type": "image",
  "url": "https://api.example.com/image.png",
  "userId": "test_user_id" // only present on create
}
```

> Create Or Update Response

```json
{
  "id": "1234",
  "name": "Image Name",
  "type": "image",
  "url": "https://api.example.com/image.png",
  "userId": "test_user_id" // only present on create
}
```

This endpoint allows you to create or update media within your organization.

On create, this API allows assigning user ownership to a media. Users owning the media will be able to see it in their
Media Library via the CMP and create/edit ad campaigns with it. Media cannot change user ownership once it is created.

On update, the URL and name can change but not the media type.

### HTTP Request

`POST management/v1/media`

### Create or Update Media Request Properties

| Field  | Required | Type   | Description                                                                                         |
|--------|----------|--------|-----------------------------------------------------------------------------------------------------|
| id     | true     | String | ID of the media you are created/updating. This must be unique per media                             |
| name   | true     | String | Name of the media you are creating/updating.                                                        |
| type   | true     | String | The media type associated with the URL. Cannot change on updates. Valid types are `image`, `video`. |
| url    | true     | String | A public URL to the media you would like to import.                                                 |
| userId | true     | String | The ID of the user that would own this media. Ignored on updates.                                   |

**Valid media types**:

- image
- video

**Response Codes**:

- `200 OK` - Returning the newly created or updated media.
- `400 BAD REQUEST` - When the request is malformed, or you attempt to associate to a non-existent media.

## Delete Media

> Delete Media response

```
No Body returned
``` 

This allows deleting media from your organization.

Once media is deleted, users will not be able use it in new ad campaigns.
However, active ad campaigns referencing deleted media will continue using the media until completion.

Media created via the Create Or Update Media API can only be deleted via this API.
Users owning the media cannot delete the media from their Media Library via the CMP.

### HTTP Request

`DELETE management/v1/media/{id}`

**Response Codes**:

- `202 ACCEPTED`
