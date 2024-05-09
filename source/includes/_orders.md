# Orders

## Place Purchase Order

> Place Purchase Order Request

```json
{
  "userId": "test_user_id",
  "groupId": "test_group_id",
  "productCode": "pid_1234567890",
  "orderType": "purchase",
  "paymentAmount": 100,
  "schedule": {
    "startTimestamp": 1735718400,
    "endTimestamp": 1738396800
  },
  "contentItemIds": [],
  "variableValues": {
    "accountId": "1234567890",
    "pageId": "1234567890",
    "headline": "headline test",
    "bodyText": "body text test",
    "image": "https://image.test",
    ...
  }
}
```

> Place Purchase Order Response

```json
{
  "data": {
    "id": "1234567890",
    "userId": "test_user_id",
    "groupId": "test_group_id",
    "productCode": "pid_1234567890",
    "status": "pending",
    "orderType": "purchase",
    "paymentAmount": 100,
    "schedule": {
      "startTimestamp": 1735718400,
      "endTimestamp": 1738396800
    },
    "contentItemIds": [],
    "variableValues": {
      "accountId": "1234567890",
      "headline": "headline test",
      "bodyText": "body text test",
      "image": "https://image.test",
      ...
    }
  }
}
```

This endpoint allows you to place a one-time purchase order.

### HTTP Request

`POST management/v1/order`

### Place Purchase Order Request Properties

| Field                   | Required | Type        | Description                                                                               |
|-------------------------|----------|-------------|-------------------------------------------------------------------------------------------|
| userId                  | true     | String      | The ID of the user placing the order.                                                     |
| groupId                 | true     | String      | The ID of the group associated with the user.                                             |
| productCode             | true     | String      | Code identifying the product for the order.                                               |
| orderType               | true     | String      | Must be set to "purchase" for one-time purchase orders.                                   |                                                                                          
| paymentAmount           | true     | Float       | The total payment amount, in USD, for the order.                                          |                                                                                          
| schedule                | true     | Object      | Contains scheduling information for the order.                                            |                                                                                          
| schedule.startTimestamp | true     | Int         | Start time of the purchase period in Pacific Time (Unix timestamp format in seconds).     |                                                                                          
| schedule.endTimestamp   | true     | Int         | End time of the purchase period in Pacific Time (Unix timestamp format in seconds).       |                                                                                          
| contentItemIds          | false    | String List | An array of content item IDs representing specific content items to include in the order. |                                                                                          
| variableValues          | false    | Map         | A map providing creative data required to supply the product.                             |                                                                                          

### General Constraints

- `userId` and `groupId` must exist and the user should be associated with the group.
- `productCode` must correspond to an active product code or product ID. To pass a product ID, use the
  format `pid_<PRODUCT_ID>`.

### Scheduling

- `startTimestamp` must precede `endTimestamp` by at least 24 hours.
- `startTimestamp` should be a future date and cannot be set to a date that has already passed.

### Payment

- `paymentAmount` should be denominated in USD, formatted to two decimal places, and pass the minimum and maximum spend
  required to place an order.
- For more details regarding minimum and maximum spend requirements for a specific product, contact your Client Success
  representative.

### Content

- If `contentItemIds` are specified, Evocalize will provide the necessary creative data using those content item IDs for
  the product.
- Certain variables may become immutable after placing the order due to specific product requirements. Make sure to
  manage these immutable variable values correctly when placing the order.

**Response Codes**:

- `200 OK` - Successfully created the purchase order.
- `400 BAD REQUEST` - Request is malformed or contains invalid data.

## Place Subscription Order

> Place Subscription Order Request

```json
{
  "userId": "test_user_id",
  "groupId": "test_group_id",
  "productCode": "pid_1234567890",
  "orderType": "subscription",
  "paymentAmount": 100,
  "schedule": {
    "startTimestamp": 1735718400
  },
  "contentItemIds": [],
  "variableValues": {
    "accountId": "1234567890",
    "headline": "headline test",
    "bodyText": "body text test",
    "image": "https://image.test",
    ...
  }
}
```

> Place Subscription Order Response

```json
{
  "data": {
    "id": "1234567890",
    "userId": "test_user_id",
    "groupId": "test_group_id",
    "productCode": "pid_1234567890",
    "status": "pending",
    "orderType": "subscription",
    "paymentAmount": 100,
    "schedule": {
      "startTimestamp": 1735718400
    },
    "contentItemIds": [],
    "variableValues": {
      "accountId": "1234567890",
      "headline": "headline test",
      "bodyText": "body text test",
      "image": "https://image.test",
      ...
    }
  }
}
```

This endpoint allows you to place a subscription order, which is scheduled to start at the beginning of the month, runs
until the specified monthly budget is exhausted, and renews at the start of each new month.

### HTTP Request

`POST management/v1/order`

### Place Subscription Order Request Properties

| Field                   | Required | Type        | Description                                                                               |
|-------------------------|----------|-------------|-------------------------------------------------------------------------------------------|
| userId                  | true     | String      | The ID of the user placing the order.                                                     |
| groupId                 | true     | String      | The ID of the group associated with the user.                                             |
| productCode             | true     | String      | Code identifying the product for the order.                                               |
| orderType               | true     | String      | Must be set to "subscription" for subscription orders.                                    |                                                                                          
| paymentAmount           | true     | Float       | The total payment amount, in USD, for the order.                                          |                                                                                          
| schedule                | true     | Object      | Contains scheduling information for the order.                                            |                                                                                          
| schedule.startTimestamp | true     | Int         | Start time of the subscription period in Pacific Time (Unix timestamp format in seconds). |                                                                                          
| contentItemIds          | false    | String List | An array of content item IDs representing specific content items to include in the order. |                                                                                          
| variableValues          | false    | Map         | A map providing creative data required to supply the product.                             |

### General Constraints

- `userId` and `groupId` must exist and the user should be associated with the group.
- `productCode` must correspond to an active product code or product ID. To pass a product ID, use the
  format `pid_<PRODUCT_ID>`.

### Scheduling

- `startTimestamp` should be a future date and cannot be set to a date that has already passed.

### Payment

- `paymentAmount` should be in USD, rounded to two decimal places, and fall within the available price range. For more
  details regarding the available price range, contact your Client Success representative.

### Content

- If `contentItemIds` are specified, Evocalize will provide the necessary creative data using those content item IDs for
  the product.
- Certain variables, like marketing account IDs, may become immutable after placing the order due to specific product
  requirements. Make sure these immutable variable values are handled appropriately when placing the order.

**Response Codes**:

- `200 OK` - Successfully created the purchase order.
- `400 BAD REQUEST` - Request is malformed or contains invalid data.
- `404 NOT FOUND` - A specified ID does not exist.

## Edit Purchase Order

> Edit Purchase Order Request - Adjust Payment Amount

```json
{
  "paymentAmount": 200
}
```

> Edit Purchase Order Response - Adjust Payment Amount

```json
{
  "data": {
    "id": "1234567890",
    "userId": "test_user_id",
    "groupId": "test_group_id",
    "productCode": "pid_1234567890",
    "status": "pending",
    "orderType": "purchase",
    "paymentAmount": 200,
    "schedule": {
      "startTimestamp": 1735718400,
      "endTimestamp": 1738396800
    },
    "contentItemIds": [],
    "variableValues": {
      "accountId": "1234567890",
      "headline": "headline test",
      "bodyText": "body text test",
      "image": "https://image.test",
      ...
    }
  }
}
```

> Edit Purchase Order Request - Adjust Schedule Start

```json
 {
  "schedule": {
    "startTimestamp": 1735804380
  }
}
```

> Edit Purchase Order Response - Adjust Schedule Start

```json
{
  "data": {
    "id": "1234567890",
    "userId": "test_user_id",
    "groupId": "test_group_id",
    "productCode": "pid_1234567890",
    "status": "pending",
    "orderType": "purchase",
    "paymentAmount": 100,
    "schedule": {
      "startTimestamp": 1735804380,
      "endTimestamp": 1738396800
    },
    "contentItemIds": [],
    "variableValues": {
      "accountId": "1234567890",
      "headline": "headline test",
      "bodyText": "body text test",
      "image": "https://image.test",
      ...
    }
  }
}
```

> Edit Purchase Order Request - Adjust Schedule End

```json
 {
  "schedule": {
    "endTimestamp": 1738482780
  }
}
```

> Edit Purchase Order Response - Adjust Schedule End

```json
{
  "data": {
    "id": "1234567890",
    "userId": "test_user_id",
    "groupId": "test_group_id",
    "productCode": "pid_1234567890",
    "status": "pending",
    "orderType": "purchase",
    "paymentAmount": 100,
    "schedule": {
      "startTimestamp": 1735718400,
      "endTimestamp": 1738482780
    },
    "contentItemIds": [],
    "variableValues": {
      "accountId": "1234567890",
      "headline": "headline test",
      "bodyText": "body text test",
      "image": "https://image.test",
      ...
    }
  }
}
```

> Edit Purchase Order Request - Adjust Variable Value

```json
 {
  "variableValues": {
    "headline": "headline test updated",
    ...
  }
}
```

> Edit Purchase Order Response - Adjust Variable Value

```json
{
  "data": {
    "id": "1234567890",
    "userId": "test_user_id",
    "groupId": "test_group_id",
    "productCode": "pid_1234567890",
    "status": "pending",
    "orderType": "purchase",
    "paymentAmount": 100,
    "schedule": {
      "startTimestamp": 1735718400,
      "endTimestamp": 1738396800
    },
    "contentItemIds": [],
    "variableValues": {
      "accountId": "1234567890",
      "headline": "headline test updated",
      "bodyText": "body text test",
      "image": "https://image.test",
      ...
    }
  }
}
```

This endpoint allows you to edit a one-time purchase order.

### HTTP Request

`PUT management/v1/order/{orderId}`

| URL Param | Required | Type   | Description                                |
|-----------|----------|--------|--------------------------------------------|
| orderId   | true     | String | The ID of the purchase order to be edited. |

### Edit Purchase Order Request Properties

| Field                   | Required | Type   | Description                                                                           |
|-------------------------|----------|--------|---------------------------------------------------------------------------------------|
| paymentAmount           | false    | Float  | The total payment amount, in USD, for the order.                                      |                                                                                          
| schedule                | false    | Object | Contains scheduling information for the order.                                        |                                                                                          
| schedule.startTimestamp | false    | Int    | Start time of the purchase period in Pacific Time (Unix timestamp format in seconds). |                                                                                          
| schedule.endTimestamp   | false    | Int    | End time of the purchase period in Pacific Time (Unix timestamp format in seconds).   |                                                                                          
| variableValues          | false    | Map    | A map providing new creative data required to supply the product.                     |

### General Constraints

- At least one of the fields must be present in the request.

### Scheduling

- Orders expiring in less than 24 hours cannot be edited.
- Orders that have a scheduled start date already past the current date cannot have their schedule start adjusted.
- `startTimestamp` and `endTimestamp` can be adjusted in the same api call or separately in separate api calls.
- `startTimestamp` should be a future date and cannot be set to a date that has already passed.
- `endTimestamp` should be a date after the order's schedule start or `startTimestamp` if specified. The rule that
  scheduled end must be 24 hours after the scheduled start applies as well.

### Payment

- Order payments cannot be updated if the order is set expire in less than 48 hours. Extending the order past the end
  period is required to adjust the budget.
- `paymentAmount` should be denominated in USD, rounded to two decimal places, and pass the spend requirements. For more
  details regarding spend requirements for a specific product, contact your Client Success representative.

### Content

- `variableValues` should contain the information necessary for adding or overwriting existing values. If a field is
  absent in the request's variable values but present in the current order, the existing variable values will remain
  unchanged.
- Make sure not to include immutable variables.

**Response Codes**:

- `200 OK` - Successfully edited the purchase order.
- `400 BAD REQUEST` - Request is malformed or contains invalid data.
- `404 NOT FOUND` - The order ID does not exist.

## Edit Subscription Order

> Edit Subscription Order Request - Adjust Payment Amount

```json
{
  "paymentAmount": 200
}
```

> Edit Subscription Order Response - Adjust Payment Amount

```json
{
  "data": {
    "id": "1234567890",
    "userId": "test_user_id",
    "groupId": "test_group_id",
    "productCode": "pid_1234567890",
    "status": "pending",
    "orderType": "subscription",
    "paymentAmount": 200,
    "schedule": {
      "startTimestamp": 1735718400
    },
    "contentItemIds": [],
    "variableValues": {
      "accountId": "1234567890",
      "headline": "headline test",
      "bodyText": "body text test",
      "image": "https://image.test",
      ...
    }
  }
}
```

> Edit Subscription Order Request - Adjust Schedule Start

```json
 {
  "schedule": {
    "startTimestamp": 1735804380
  }
}
```

> Edit Subscription Order Response - Adjust Schedule Start

```json
{
  "data": {
    "id": "1234567890",
    "userId": "test_user_id",
    "groupId": "test_group_id",
    "productCode": "pid_1234567890",
    "status": "pending",
    "orderType": "subscription",
    "paymentAmount": 100,
    "schedule": {
      "startTimestamp": 1735804380
    },
    "contentItemIds": [],
    "variableValues": {
      "accountId": "1234567890",
      "headline": "headline test",
      "bodyText": "body text test",
      "image": "https://image.test",
      ...
    }
  }
}
```

> Edit Subscription Order Request - Adjust Variable Value

```json
 {
  "variableValues": {
    "headline": "headline test updated",
    ...
  }
}
```

> Edit Subscription Order Response - Adjust Variable Value

```json
{
  "data": {
    "id": "1234567890",
    "userId": "test_user_id",
    "groupId": "test_group_id",
    "productCode": "pid_1234567890",
    "status": "pending",
    "orderType": "subscription",
    "paymentAmount": 100,
    "schedule": {
      "startTimestamp": 1735718400
    },
    "contentItemIds": [],
    "variableValues": {
      "accountId": "1234567890",
      "headline": "headline test updated",
      "bodyText": "body text test",
      "image": "https://image.test",
      ...
    }
  }
}
```

This endpoint allows you to edit a subscription order.

### HTTP Request

`PUT management/v1/order/{orderId}`

### URL Params

| URL Param | Required | Type   | Description                                    |
|-----------|----------|--------|------------------------------------------------|
| orderId   | true     | String | The ID of the subscription order to be edited. |

### Edit Subscription Order Request Properties

| Field                   | Required | Type   | Description                                                                               |
|-------------------------|----------|--------|-------------------------------------------------------------------------------------------|
| paymentAmount           | false    | Float  | The total payment amount, in USD, for the order.                                          |                                                                                          
| schedule                | false    | Object | Contains scheduling information for the order.                                            |                                                                                          
| schedule.startTimestamp | false    | Int    | Start time of the subscription period in Pacific Time (Unix timestamp format in seconds). |                                                                                          
| variableValues          | false    | Map    | A map providing new creative data required to supply the product.                         |

### General Constraints

- At least one of the fields must be present in the request.

### Scheduling

- Orders that have a scheduled start date already past the current date cannot have their schedule start adjusted.
- `startTimestamp` should be a future date and cannot be set to a date that has already passed.

### Payment

- `paymentAmount` should be denominated in USD, rounded to two decimal places, and pass the spend requirements. For more
  details regarding spend requirements for a specific product, contact your Client Success representative.

### Content

- `variableValues` should contain the information necessary for adding or overwriting existing values. If a field is
  absent in the request's variable values but present in the current order, the existing variable values will remain
  unchanged.
- Make sure not to include immutable variables.

**Response Codes**:

- `200 OK` - Successfully edited the subscription order.
- `400 BAD REQUEST` - Request is malformed or contains invalid data.
- `404 NOT FOUND` - The order ID does not exist.

## Cancel Order

> Cancel Order Order Response

```json
{
  "data": {
    "orderId": "1234567890",
    "refundAmount": 90
  }
}
```

This endpoint allows you to cancel a purchase or subscription order.

### HTTP Request

`POST management/v1/order/{orderId}/cancel`

### URL Params

| URL Param | Required | Type   | Description                          |
|-----------|----------|--------|--------------------------------------|
| orderId   | true     | String | The ID of the order to be cancelled. |

### Place Purchase Order Response Properties

| Field        | Nullable | Type   | Description                                 |
|--------------|----------|--------|---------------------------------------------|
| orderId      | false    | String | The ID of the order that was cancelled      |
| refundAmount | false    | Float  | The refunded amount of the cancelled order. |

**Response Codes**:

- `200 OK` - Successfully cancelled the order.
- `400 BAD REQUEST` - Request is malformed or contains invalid data.
- `404 NOT FOUND` - The order ID does not exist.

## Get Order By ID

Returns the order information given an order ID.


> Get Order By ID Response - Purchase

```json
{
  "data": {
    "id": "1234567890",
    "userId": "test_user_id",
    "groupId": "test_group_id",
    "productCode": "pid_1234567890",
    "status": "active",
    "orderType": "purchase",
    "paymentAmount": 100,
    "schedule": {
      "startTimestamp": 1735718400,
      "endTimestamp": 1738396800
    },
    "contentItemIds": [],
    "variableValues": {
      "accountId": "1234567890",
      "headline": "headline test",
      "bodyText": "body text test",
      "image": "https://image.test",
      ...
    }
  }
}
```

> Get Order By ID Response - Subscription

```json
{
  "data": {
    "id": "1234567890",
    "userId": "test_user_id",
    "groupId": "test_group_id",
    "productCode": "pid_1234567890",
    "status": "active",
    "orderType": "subscription",
    "paymentAmount": 100,
    "schedule": {
      "startTimestamp": 1735718400
    },
    "contentItemIds": [],
    "variableValues": {
      "accountId": "1234567890",
      "headline": "headline test",
      "bodyText": "body text test",
      "image": "https://image.test",
      ...
    },
    "subscriptionDetails": {
      "renewsOnTimestamp": 1735718400,
      "endsOnTimestamp": null,
      "subscriptionAmount": 100,
      "status": "active"
    }
  }
}
```

### HTTP Request

`GET management/v1/order/{orderId}`

### URL Params

| URL Param | Required | type   | Description                                      |
|-----------|----------|--------|--------------------------------------------------|
| orderId   | true     | String | The ID of the order you are wanting to retrieve. |

### Get Order By ID Response Properties

| Field                   | Nullable | Type        | Description                                                                                        |
|-------------------------|----------|-------------|----------------------------------------------------------------------------------------------------|
| id                      | false    | String      | The ID of the order.                                                                               |
| userId                  | false    | String      | The ID of the user associated to the order.                                                        |
| groupId                 | false    | String      | The ID of the group associated the user.                                                           |
| productCode             | false    | String      | Code identifying the product for the order.                                                        |
| orderType               | false    | String      | The type of order which can be `purchase` or `subscription`.                                       |                                                                                          
| paymentAmount           | false    | Float       | The total payment amount, in USD, for the order.                                                   |                                                                                          
| status                  | false    | String      | The status of the order.                                                                           |                                                                                          
| schedule                | false    | Object      | Contains scheduling information for the order.                                                     |                                                                                          
| schedule.startTimestamp | false    | Int         | Start time of the purchase/subscription period in Pacific Time (Unix timestamp format in seconds). |                                                                                          
| schedule.endTimestamp   | true     | Int         | End time of the purchase period in Pacific Time (Unix timestamp format in seconds).                |                                                                                          
| contentItemIds          | false    | String List | An array of content item IDs representing specific content items included in the order.            |                                                                                          
| variableValues          | false    | Map         | A map containing the creative data required to supply the product.                                 |                                                                                          
| subscriptionDetails     | true     | Object      | Details related to the subscription if the order is a subscription.                                |                                                                                          

**Order Statuses**:

- `pending` - The order is pending.
- `active` - The order was successfully placed and is active.
- `failed` - An error occurred for this order.
- `cancelled` - The order was cancelled.
- `completed` - The order was successfully placed and has expired.

### Subscription Details Response Properties

| Field              | Nullable | Type   | Description                                                                                 |
|--------------------|----------|--------|---------------------------------------------------------------------------------------------|
| renewsOnTimestamp  | true     | Int    | Renewal time of the subscription period in Pacific Time (Unix timestamp format in seconds). |
| endsOnTimestamp    | true     | Int    | End time of the subscription period in Pacific Time (Unix timestamp format in seconds).     |
| subscriptionAmount | false    | Float  | The payment amount for the subscription interval.                                           |
| status             | false    | String | The status of the subscription interval.                                                    |                                                                                          

**Subscription Detail Statuses**:

- `will_not_renew` - The subscription interval will not be renewed.
- `active` - The subscription interval is active.
- `pending` - The subscription interval is pending and processing renewal.

## Get Order Refund Preview By ID

Returns the order refund preview information given an order ID.

> Get Order Refund Preview By ID Response

```json
{
  "data": {
    "orderId": "1234567890",
    "subTotal": 100.0000,
    "cancellationFee": 0.0000,
    "totalRefund": 100.0000,
    "cancellationType": "immediate",
    "daysRemaining": 30,
    "nextIntervalPaidAmount": 0
  }
}
```

### HTTP Request

`GET management/v1/order/{orderId}/refundpreview`

### URL Params

| URL Param | Required | type   | Description                                            |
|-----------|----------|--------|--------------------------------------------------------|
| orderId   | true     | String | The ID of the order you are wanting to preview refund. |

### Get Order By ID Response Properties

| Field                  | Nullable | Type   | Description                                                            |
|------------------------|----------|--------|------------------------------------------------------------------------|
| orderId                | false    | String | The ID of the order.                                                   |
| subTotal               | false    | Float  | The refund amount of the order before applying fees.                   |
| cancellationFee        | false    | Float  | The cancellation fee of the order.                                     |
| totalRefund            | false    | Float  | The refund amount of the order after applying fees.                    |
| cancellationType       | false    | String | The type of order cancellation which can be `immediate` or `deferred`. |                                                                                          
| daysRemaining          | false    | Int    | The remaining days for this order.                                     |                                                                                          
| nextIntervalPaidAmount | true     | String | The future interval amount to be refunded without fee.                 |