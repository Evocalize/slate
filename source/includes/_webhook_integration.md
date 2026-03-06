# Webhooks

## Client Leads Webhook

Evocalize can notify your organization via webhook any time a new lead is ingested from an upstream platform such as
Facebook or Google. Upon notification from the upstream platform that new lead data has been created, we combine that
with Evocalize data and your data, and send it to you, allowing you to handle leads in near real-time.

### Setup

You will need to work with your account manager and supply Evocalize with an endpoint url where you would like to be
notified with the payload specified below. Your endpoint url must use SSL (https) and accept a POST. You will need to
have a `ClientKeyID` and a `ClientSecret`. These are the same values that you will use to call our API for other
operations.

### Policies

- ℹ️ **Duplicate Leads: Each lead may be delivered more than once. For example, if any update sent to your server fails,
  we will retry several times with decreasing frequency until we get a success. While this helps ensure you receive all
  of your leads, it can create duplicates on your end so your server should handle deduplication in these cases. All
  leads have a unique `leads[].id` field you can use for the deduplication process.**
- Any response code in the range of 200 - 299 is considered a success, any other response code is considered a failure
  and we will attempt to redeliver the lead notification.
- The HTTPS request will time out after 5 seconds. If this happens, delivery is considered a failure and we will attempt
  to redeliver the lead notification.
- The time between retry attempts is exponential, with a maximum of 15 minutes between tries. After three hours have
  elapsed since the first attempt, we will no longer try to deliver the leads. If something has happened causing your
  platform to stop ingesting leads for more than 3 hours, contact your Evocalize account representative.

### Headers

- `X-Evocalize-Payload-Version` - The version of the payload.
- `X-Evocalize-Client-Key-Id` - Provided by Evocalize, your `ClientKeyId` from above.
- `X-Evocalize-Timestamp` - Timestamp of when the request was sent.
- `X-Evocalize-Signature` - The signature of the payload, see Signature below.

### Signature Validation - Optional

> Signature validation

```kotlin
// Concatenate all the parts
val toValidate = StringBuilder()
    .append(request.servletPath)
    .append("\n")
    .append(request.body())
    .append("\n")
    .append(request.headers["X-Evocalize-Timestamp"])
    .append("\n")
    .append(myClientSecret)
    .toString()

// expectedSignature will match request.headers["X-Evocalize-Signature"]
val expectedSignature = Hashing.sha256().hashString(toValidate, Charsets.UTF_8)
```

For extra security, partners may have the need or desire to validate each individual request signature. Validating the
request signature provides extra security over relying solely on the `ClientKeyId` because it uses the `ClientSecret`
above. The `ClientSecret` is never sent over the wire. The methodology is simple. Join the following values into one
string, separated by a newline, then hash them using SHA-256

- urlPath
- requestBody
- X-Evocalize-Timestamp header
- ClientSecret

Since we require the use of the https protocol for our webhooks, it is up to each partner to choose if they would like
to validate the signature.

### Payload

> Payload Example (Facebook Lead)

```json
{
  "leads": [
    {
      "id": "facebook:1584659102387",
      "facebook": {
        "summary": {
          "leadgenId": "1584659102387",
          "pageId": "1584659102389",
          "leadFormId": "1584659102390",
          "adgroupId": "1584659102388",
          "adId": "1584659102388",
          "retailerItemId": "1584659102388",
          "createdAtEpochSeconds": 1584659108
        },
        "leadForm": [
          {
            "name": "field_one",
            "values": [
              "value one"
            ]
          }
        ],
        "leadFormDisclaimerResponses": [
          {
            "consentText": "Example Consent Text",
            "isConsentChecked": true
          }
        ]
      },
      "content": [
        [
          {
            "friendlyName": "Catalog Field Name",
            "name": "columnName",
            "value": "field value"
          }
        ]
      ],
      "program": {
        "projectId": 643545619583019092,
        "programId": 643545619499133011,
        "programName": "programName-1584659102374",
        "programDescription": "verbose program description-1584659102375",
        "programVariables": {
          "customValue1": "custom value for your application",
          "customValue2": 42
        }
      },
      "order": {
        "architectureName": "Example Architecture Name",
        "architectureId": 643545620052781143,
        "productName": "Single Image Ad",
        "productId": 643545619583019092,
        "orderId": 643545620052781143,
        "userId": "email-1584659102362@example.com",
        "groupId": "3775e2cc-e8f0-4402-b947-e8f2112fc1b4",
        "orderItem": {
          "orderItemId": 643545620774201440
        }
      }
    }
  ]
}
```

> Payload Example (Google Lead)

```json
{
  "leads": [
    {
      "id": "google:1584659102387",
      "google": {
        "summary": {
          "campaignId": 1652440203600,
          "formId": 1652440203601,
          "adgroupId": 1652440203602,
          "creativeId": 1652440203603,
          "gclId": "gclId-1652440203604",
          "leadId": "1652440203598"
        },
        "leadForm": [
          {
            "name": "field_one",
            "values": [
              "value one"
            ]
          }
        ]
      },
      "content": [
        [
          {
            "friendlyName": "Catalog Field Name",
            "name": "columnName",
            "value": "field value"
          }
        ]
      ],
      "program": {
        "projectId": 643545619583019092,
        "programId": 643545619499133011,
        "programName": "programName-1584659102374",
        "programDescription": "verbose program description-1584659102375",
        "programVariables": {
          "customValue1": "custom value for your application",
          "customValue2": 42
        }
      },
      "order": {
        "architectureName": "Example Architecture Name",
        "architectureId": 643545620052781143,
        "productName": "Single Image Ad",
        "productId": 643545619583019092,
        "orderId": 643545620052781143,
        "userId": "email-1584659102362@example.com",
        "groupId": "3775e2cc-e8f0-4402-b947-e8f2112fc1b4",
        "orderItem": {
          "orderItemId": 643545620774201440
        }
      }
    }
  ]
}
```

> Payload Example (TikTok Lead)

```json
{
  "leads": [
    {
      "id": "tikTok:8a2bd6e1-5bfb-4e4e-b6fa-cc48c737bd8a",
      "tikTok": {
        "summary": {
          "formId": 1652440203717,
          "campaignId": 1652440203718,
          "adGroupId": 1652440203720,
          "adId": 1652440203722,
          "createdAtEpochSeconds": 1652440203724
        },
        "leadForm": [
          {
            "name": "field_one",
            "values": [
              "value_one"
            ]
          }
        ]
      },
      "content": [
        [
          {
            "friendlyName": "Catalog Field Name",
            "name": "columnName",
            "value": "field value"
          }
        ]
      ],
      "program": {
        "projectId": 643545619583019092,
        "programId": 643545619499133011,
        "programName": "programName-1584659102374",
        "programDescription": "verbose program description-1584659102375",
        "programVariables": {
          "customValue1": "custom value for your application",
          "customValue2": 42
        }
      },
      "order": {
        "architectureName": "Example Architecture Name",
        "architectureId": 643545620052781143,
        "productName": "Single Image Ad",
        "productId": 643545619583019092,
        "orderId": 643545620052781143,
        "userId": "email-1584659102362@example.com",
        "groupId": "3775e2cc-e8f0-4402-b947-e8f2112fc1b4",
        "orderItem": {
          "orderItemId": 643545620774201440
        }
      }
    }
  ]
}
```

Special consideration should be taken not to fail if a new field is present in your data that you did not expect. We
will notify you when a field is removed from our specification, but we will not notify you when a field is added to the
specification. If a field is removed from the specification, the payloadVersion will increment.

The potential values of leadForm and content depends upon your imported data and program setup. Your account
representative can help with this.

| Field                                         | Nullable | Type   | Description                                                                |
|-----------------------------------------------|----------|--------|----------------------------------------------------------------------------|
| leads                                         | false    | array  | A list of lead data.                                                       |
| leads[]                                       | false    | object |                                                                            |
| leads[].id                                    | false    | string | The id of this lead.                                                       |
| leads[].facebook                              | true     | object | Facebook data for this lead. Details for this object are provided below.   |
| leads[].google                                | true     | object | Google data for this lead. Details for this object are provided below.     |
| leads[].tikTok                                | true     | object | TikTok data for this lead. Details for this object are provided below.     |
| leads[].content                               | false    | array  | An array of content items associated with this ad.                         |
| leads[].content[]                             | false    | array  | An array of content fields for the item.                                   |
| leads[].content[][]                           | false    | object |                                                                            |
| leads[].content[][].friendlyName              | true     | string | The friendly name of this content field.                                   |
| leads[].content[][].name                      | false    | string | The actual name of this content field. It will be in snake_case.           |
| leads[].content[][].value                     | true     | string | The value for this content field. It could be null.                        |
| leads[].program                               | false    | object | Details about the program that published the ad associated with this lead. |
| leads[].program.projectId                     | false    | long   | The evocalize project id associated with this lead.                        |
| leads[].program.programId                     | false    | long   | The evocalize program id associated with this lead.                        |
| leads[].program.programName                   | false    | long   | The name of the program that published this ad.                            |
| leads[].program.programDescription            | true     | string | The description of the program that published this ad.                     |
| leads[].program.programVariables<sup>\*</sup> | true     | object | A key-value map containing values specific to your application.            |
| leads[].order                                 | true     | object |                                                                            |
| leads[].order.architectureId                  | false    | long   | The id of the architecture associated to the order.                        |
| leads[].order.archietctureName                | false    | string | The name of the architecture associated to the order.                      |
| leads[].order.productName                     | false    | string | The name of the product associated to the order.                           |
| leads[].order.productId                       | false    | long   | The id of the product associated to the order.                             |
| leads[].order.orderId                         | false    | long   | The order id associated with the program.                                  |
| leads[].order.userId                          | false    | string | The user id associated with this order. This is the id in your system.     |
| leads[].order.groupId                         | false    | string | The group id associated with this order. This is the id in your system.    |
| leads[].order.orderItem                       | false    | object |                                                                            |
| leads[].order.orderItem.orderItemId           | false    | long   | The order item id associated with the order project.                       |

<sup>\*</sup>If you have values that you need our API to return for this field, please contact your Evocalize account
representative.

### Facebook Lead Data

| Field                                                           | Nullable | Type    | Description                                                                                              |
|-----------------------------------------------------------------|----------|---------|----------------------------------------------------------------------------------------------------------|
| leads[].facebook.summary                                        | false    | object  | Summary data for this Facebook lead.                                                                     |
| leads[].facebook.summary.leadgenId                              | false    | string  | Facebook's leadgen_id.                                                                                   |
| leads[].facebook.summary.pageId                                 | false    | string  | The Facebook page that the ad for this lead is associated with.                                          |
| leads[].facebook.summary.leadFormId                             | false    | string  | The Facebook lead form id associated with this lead.                                                     |
| leads[].facebook.summary.adgroupId                              | false    | string  | The Facebook adgroup id associated with this lead.                                                       |
| leads[].facebook.summary.adId                                   | false    | string  | The Facebook ad id associated with this lead.                                                            |
| leads[].facebook.summary.retailerItemId                         | false    | string  | (Dynamic Ads Only) Id of the content (in catalog) that the user clicked on before submitting their lead. |
| leads[].facebook.summary.createdAtEpochSeconds                  | false    | long    | The time this lead was created, as a unix timestamp in seconds.                                          |
| leads[].facebook.leadForm                                       | false    | array   | Values that a person filled out on the lead form.                                                        |
| leads[].facebook.leadForm[]                                     | false    | object  |                                                                                                          |
| leads[].facebook.leadForm[].name                                | false    | string  | The name of the Facebook lead form field.                                                                |
| leads[].facebook.leadForm[].values                              | false    | array   | An array of the values the user filled out.                                                              |
| leads[].facebook.leadForm[].values[]                            | false    | string  | Each element is a value that the end user selected or entered. In most cases there is only one element.  |
| leads[].facebook.leadFormDisclaimerResponses                    | true     | array   | Values that a person filled out on the lead form custom disclaimer.                                      |
| leads[].facebook.leadFormDisclaimerResponses[]                  | false    | object  |                                                                                                          |
| leads[].facebook.leadFormDisclaimerResponses[].consentText      | false    | string  | The consent text of the Facebook lead form custom disclaimer checkbox.                                   |
| leads[].facebook.leadFormDisclaimerResponses[].isConsentChecked | false    | boolean | Indicates if the Facebook lead form custom disclaimer checkbox was checked.                              |

### Google Lead Data

| Field                              | Nullable | Type   | Description                                                                                             |
|------------------------------------|----------|--------|---------------------------------------------------------------------------------------------------------|
| leads[].google.summary             | false    | object | Summary data for this Google lead.                                                                      |
| leads[].google.summary.campaignId  | false    | string | Google's campaign ID.                                                                                   |
| leads[].google.summary.formId      | false    | string | The Google lead form ID.                                                                                |
| leads[].google.summary.adgroupId   | false    | string | The Google ad group ID.                                                                                 |
| leads[].google.summary.creativeId  | true     | string | The Google creative ID.                                                                                 |
| leads[].google.summary.gclId       | false    | string | The Google click id .                                                                                   |
| leads[].google.summary.leadId      | false    | long   | The Google lead ID.                                                                                     |
| leads[].google.leadForm            | false    | array  | Values that a person filled out on the lead form.                                                       |
| leads[].google.leadForm[]          | false    | object |                                                                                                         |
| leads[].google.leadForm[].name     | false    | string | The name of the facebook lead form field.                                                               |
| leads[].google.leadForm[].values   | false    | array  | An array of the values the user filled out.                                                             |
| leads[].google.leadForm[].values[] | false    | string | Each element is a value that the end user selected or entered. In most cases there is only one element. |

### TikTok Lead Data

| Field                                        | Nullable | Type   | Description                                                                                             |
|----------------------------------------------|----------|--------|---------------------------------------------------------------------------------------------------------|
| leads[].tikTok.summary                       | false    | object | Summary data for this facebook lead.                                                                    |
| leads[].tikTok.summary.leadgenId             | false    | string | Facebook's leadgen_id.                                                                                  |
| leads[].tikTok.summary.pageId                | false    | string | The facebook page that the ad for this lead is associated with.                                         |
| leads[].tikTok.summary.leadFormId            | false    | string | The facebook lead form id associated with this lead.                                                    |
| leads[].tikTok.summary.adgroupId             | false    | string | The facebook adgroup id associated with this lead.                                                      |
| leads[].tikTok.summary.adId                  | false    | string | The facebook ad id associated with this lead.                                                           |
| leads[].tikTok.summary.createdAtEpochSeconds | false    | long   | The time this lead was created, as a unix timestamp in seconds.                                         |
| leads[].tikTok.leadForm                      | false    | array  | Values that a person filled out on the lead form.                                                       |
| leads[].tikTok.leadForm[]                    | false    | object |                                                                                                         |
| leads[].tikTok.leadForm[].name               | false    | string | The name of the facebook lead form field.                                                               |
| leads[].tikTok.leadForm[].values             | false    | array  | An array of the values the user filled out.                                                             |
| leads[].tikTok.leadForm[].values[]           | false    | string | Each element is a value that the end user selected or entered. In most cases there is only one element. |

## Lead Concierge Webhook Events

Evocalize can notify your organization via webhook when lead concierge events occur, such as status changes and call
outcomes. This allows you to track the lifecycle of leads being handled by the concierge service in near real-time.

### Setup

You will need to work with your account manager and supply Evocalize with an endpoint url where you would like to
receive lead concierge event notifications. This is a separate endpoint from the Client Leads Webhook. Your endpoint
url must use SSL (https) and accept a POST. You will need to have a `ClientKeyID` and a `ClientSecret`. These are the
same values that you will use to call our API for other operations.

### Policies

- Any response code in the range of 200 - 299 is considered a success, any other response code is considered a failure
  and we will attempt to redeliver the event notification.
- The HTTPS request will time out after 5 seconds. If this happens, delivery is considered a failure and we will attempt
  to redeliver the event notification.
- The time between retry attempts is exponential, with a maximum of 15 minutes between tries. After three hours have
  elapsed since the first attempt, we will no longer try to deliver the event. If something has happened causing your
  platform to stop ingesting events for more than 3 hours, contact your Evocalize account representative.
- Each event may be delivered more than once. Use the `idempotencyKey` field to deduplicate events on your end.

### Headers

- `X-Evocalize-Payload-Version` - The version of the payload.
- `X-Evocalize-Client-Key-Id` - Provided by Evocalize, your `ClientKeyId` from above.
- `X-Evocalize-Timestamp` - Timestamp of when the request was sent.
- `X-Evocalize-Signature` - The signature of the payload. Refer to the [Signature Validation](#signature-validation-optional) section above for details.

### Payload

> Lead Concierge Event Payload Example

```json
{
    "idempotencyKey": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "orderData": {
        "architectureId": 3799517520646443763,
        "architectureName": "Org 3799517520210236106 Arch 1772769684906",
        "productId": 3799517520763884281,
        "productName": "Product Name 1772769684910",
        "orderId": 3799517520663220980,
        "userId": "email-1772769684902@example.com",
        "groupId": "34e3fbfc-ef96-43ef-b46f-726d8047a059",
        "orderItem": {
            "orderItemId": 3799517521485304607
        }
    },
    "programData": {
        "projectId": 3799517520898102019,
        "programId": 3799517520629666546,
        "programName": "programName-1772769684904",
        "programDescription": "verbose program description-1772769684905",
        "programVariables": {
            "var1": "foo"
        }
    },
    "leadConciergeData": {
        "internalLeadId": "100",
        "conciergeProviderLeadId": "1095675601",
        "conciergeEventId": "1772769685200",
        "leadType": "1",
        "currentStatus": "completed",
        "previousStatus": "NEW",
        "currentOutcome": "COMPLETED_CLOSED_OTHER",
        "summary": "Test call summary",
        "transcript": "Test transcript content"
    }
}
```

Special consideration should be taken not to fail if a new field is present in your data that you did not expect. We
will notify you when a field is removed from our specification, but we will not notify you when a field is added to the
specification. If a field is removed from the specification, the payloadVersion will increment.

| Field                                          | Nullable | Type   | Description                                                                |
|------------------------------------------------|----------|--------|----------------------------------------------------------------------------|
| idempotencyKey                                 | false    | string | A unique key for this event. Use this to deduplicate events on your end.   |
| orderData                                      | false    | object | Details about the order associated with this lead concierge event.         |
| orderData.architectureId                       | false    | long   | The id of the architecture associated to the order.                        |
| orderData.architectureName                     | false    | string | The name of the architecture associated to the order.                      |
| orderData.productId                            | false    | long   | The id of the product associated to the order.                             |
| orderData.productName                          | false    | string | The name of the product associated to the order.                           |
| orderData.orderId                              | false    | long   | The order id associated with the program.                                  |
| orderData.userId                               | false    | string | The user id associated with this order. This is the id in your system.     |
| orderData.groupId                              | false    | string | The group id associated with this order. This is the id in your system.    |
| orderData.orderItem                            | false    | object |                                                                            |
| orderData.orderItem.orderItemId                | false    | long   | The order item id associated with the order project.                       |
| programData                                    | false    | object | Details about the program associated with this lead concierge event.       |
| programData.projectId                          | false    | long   | The evocalize project id associated with this event.                       |
| programData.programId                          | false    | long   | The evocalize program id associated with this event.                       |
| programData.programName                        | false    | string | The name of the program that published this ad.                            |
| programData.programDescription                 | true     | string | The description of the program that published this ad.                     |
| programData.programVariables<sup>\*</sup>      | true     | object | A key-value map containing values specific to your application.            |
| leadConciergeData                              | false    | object | Details about the lead concierge event.                                    |
| leadConciergeData.internalLeadId               | false    | string | The internal lead id.                                                      |
| leadConciergeData.conciergeProviderLeadId      | false    | string | The lead id from the concierge provider.                                   |
| leadConciergeData.conciergeEventId             | false    | string | The unique identifier for this concierge event.                            |
| leadConciergeData.leadType                     | false    | string | The id for the concierge campaign.                               |
| leadConciergeData.currentStatus                | false    | string | The current status of the lead. Possible values: `UNKNOWN`, `NEW`, `OPEN`, `CALLBACK_TO_TRANSFER`, `CLOSED`, `CLOSED_INCOMPLETE`, `LOST`, `SCHEDULE_COMPLETED`, `OUT_OF_TERRITORY`, `BAD_NUMBER`, `CANCELLED`, `DNC_TEXT`, `DNC_CALL`, `DNC_BOTH`. |
| leadConciergeData.previousStatus               | false    | string | The previous status of the lead. Possible values: `UNKNOWN`, `NEW`, `OPEN`, `CALLBACK_TO_TRANSFER`, `CLOSED`, `CLOSED_INCOMPLETE`, `LOST`, `SCHEDULE_COMPLETED`, `OUT_OF_TERRITORY`, `BAD_NUMBER`, `CANCELLED`, `DNC_TEXT`, `DNC_CALL`, `DNC_BOTH`. |
| leadConciergeData.currentOutcome               | true     | string | The call outcome associated with the current status.                       |
| leadConciergeData.summary                      | true     | string | A summary of the concierge interaction.                                    |
| leadConciergeData.transcript                   | true     | string | The transcript of the concierge interaction.                               |

<sup>\*</sup>If you have values that you need our API to return for this field, please contact your Evocalize account
representative.
