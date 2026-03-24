# Leads

## Opt-Out Lead

> Opt-Out Lead Request

```
POST management/v1/opt-out/12345
```

> Opt-Out Lead Response

```
HTTP 200 OK
(empty body)
```

This endpoint opts a lead out of being a part of the lead concierge process. All interactions with the lead will cease.

### HTTP Request

`POST management/v1/opt-out/{internalLeadId}`

### URL Parameters

| Parameter      | Required | Type | Description                              |
|----------------|----------|------|------------------------------------------|
| internalLeadId | true     | Long | The internal ID of the lead to opt out.  |

**Response Codes**:

- `200 OK` - Successfully opted the lead out.
- `404 Not Found` - The specified lead could not be found.
