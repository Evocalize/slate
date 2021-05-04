# Webhooks

## Client Leads Webhook

Evocalize can notify your organization via webhook any time a new lead is ingested from an upstream platform such as Facebook or Google. Upon notification from the upstream platform that new lead data has been created, we combine that with Evocalize data and your data, and send it to you, allowing you to handle leads in near real-time.

### Setup

You will need to work with your account manager and supply Evocalize with an endpoint url where you would like to be notified with the payload specified below. Your endpoint url must use SSL (https) and accept a POST. You will need to have a `ClientKeyID` and a `ClientSecret`. These are the same values that you will use to call our API for other operations.

### Policies

- Each lead may be delivered more than once. This is rare, but it is possible. The leads[].id is unique to a particular lead and will not change between deliveries if a lead is delivered multiple times.
- Any response code in the range of 200 - 299 is considered a success, any other response code is considered a failure and we will attempt to redeliver the lead notification.
- The HTTPS request will time out after 5 seconds. If this happens, delivery is considered a failure and we will attempt to redeliver the lead notification.
- The time between retry attempts is exponential, with a maximum of 15 minutes between tries. After three hours have elapsed since the first attempt, we will no longer try to deliver the leads. If something has happened causing your platform to stop ingesting leads for more than 3 hours, contact your Evocalize account representative.

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

For extra security, partners may have the need or desire to validate each individual request signature. Validating the request signature provides extra security over relying solely on the `ClientKeyId` because it uses the `ClientSecret` above. The `ClientSecret` is never sent over the wire. The methodology is simple. Join the following values into one string, separated by a newline, then hash them using SHA-256

- urlPath
- requestBody
- X-Evocalize-Timestamp header
- ClientSecret

Since we require the use of the https protocol for our webhooks, it is up to each partner to choose if they would like to validate the signature.

### Payload

> Payload Example

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
          "createdAtEpochSeconds": 1584659108
        },
        "leadForm": [
          {
            "name": "field_one",
            "values": ["value one"]
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

Special consideration should be taken not to fail if a new field is present in your data that you did not expect. We will notify you when a field is removed from our specification, but we will not notify you when a field is added to the specification. If a field is removed from the specification, the payloadVersion will increment.

The potential values of leadForm and content depends upon your imported data and program setup. Your account representative can help with this.

| Field                                          | Nullable | Type   | Description                                                                                             |
| ---------------------------------------------- | -------- | ------ | ------------------------------------------------------------------------------------------------------- |
| leads                                          | false    | array  | A list of lead data.                                                                                    |
| leads[]                                        | false    | object |                                                                                                         |
| leads[].id                                     | false    | string | The id of this lead.                                                                                    |
| leads[].facebook                               | false    | object | Facebook data for this lead.                                                                            |
| leads[].facebook.summary                       | false    | object | Summary data for this facebook lead.                                                                    |
| leads[].facebook.summary.leadgenId             | false    | string | Facebook's leadgen_id.                                                                                  |
| leads[].facebook.summary.pageId                | false    | string | The facebook page that the ad for this lead is associated with.                                         |
| leads[].facebook.summary.leadFormId            | false    | string | The facebook lead form id associated with this lead.                                                    |
| leads[].facebook.summary.adgroupId             | false    | string | The facebook adgroup id associated with this lead.                                                      |
| leads[].facebook.summary.adId                  | false    | string | The facebook ad id associated with this lead.                                                           |
| leads[].facebook.summary.createdAtEpochSeconds | false    | long   | The time this lead was created, as a unix timestamp in seconds.                                         |
| leads[].facebook.leadForm                      | false    | array  | Values that a person filled out on the lead form.                                                       |
| leads[].facebook.leadForm[]                    | false    | object |                                                                                                         |
| leads[].facebook.leadForm[].name               | false    | string | The name of the facebook lead form field.                                                               |
| leads[].facebook.leadForm[].values             | false    | array  | An array of the values the user filled out.                                                             |
| leads[].facebook.leadForm[].values[]           | false    | string | Each element is a value that the end user selected or entered. In most cases there is only one element. |
| leads[].content                                | false    | array  | An array of content items associated with this ad.                                                      |
| leads[].content[]                              | false    | array  | An array of content fields for the item.                                                                |
| leads[].content[][]                            | false    | object |                                                                                                         |
| leads[].content[][].friendlyName               | true     | string | The friendly name of this content field.                                                                |
| leads[].content[][].name                       | false    | string | The actual name of this content field. It will be in snake_case.                                        |
| leads[].content[][].value                      | true     | string | The value for this content field. It could be null.                                                     |
| leads[].program                                | false    | object | Details about the program that published the ad associated with this lead.                              |
| leads[].program.projectId                      | false    | long   | The evocalize project id associated with this lead.                                                     |
| leads[].program.programId                      | false    | long   | The evocalize program id associated with this lead.                                                     |
| leads[].program.programName                    | false    | long   | The name of the program that published this ad.                                                         |
| leads[].program.programDescription             | true     | string | The description of the program that published this ad.                                                  |
| leads[].program.programVariables               | true     | object | An object map containing values specific to your application. If you have values that you need our API to return for this field, please contact your Evocalize account representative. |
| leads[].order                                  | true     | object |                                                                                                         |
| leads[].order.orderId                          | false    | long   | The order id associated with the program.                                                               |
| leads[].order.userId                           | false    | string | The user id associated with this order. This is the id in your system.                                  |
| leads[].order.groupId                          | false    | string | The group id associated with this order. This is the id in your system.                                 |
| leads[].order.orderItem                        | false    | object |                                                                                                         |
| leads[].order.orderItem.orderItemId            | false    | long   | The order item id associated with the order project.                                                    |
