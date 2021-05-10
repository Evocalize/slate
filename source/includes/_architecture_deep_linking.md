# Architecture Deep Linking

## Content

The following information explains how to generate a deep link to take a user to view all of their content under a
specific architecture.

### Base URL

`/#/architecture/<ARCHITECTURE_ID>/content`

### Query Parameters

> Content Filter Example - adding an automatic filter on the status attribute where the value contains "Sold"

```
contentFilter=%7B%22status%22%3A%20%7B%22contains%22%3A%20%22Sold%22%7D%7D
```

> The raw value from the example above is

```
{
  "status": {
    "contains": "Sold"
  }
}
```

All query parameters must be properly encoded URI components. For further information on this, please see documentation
[here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)

| Query Param    | Required | type         | Description                                                             |
| -------------- | -------- | ------------ | ----------------------------------------------------------------------- |
| contentFilter  | false    | Encoded JSON | JSON enabling filtering of content                                      |
| sideNav        | false    | String       | "closed" or "open" to control whether or not the left nav is displayed. |

## Currently Running Programs

The link below is where you should send users to view all the purchases they have made for a
specific architecture.

### Base URL

`/#/architecture/<ARCHITECTURE_ID>/programs`

## Program Creation/Purchase Base

This URL will take a user to the order process without a Blueprint or any items selected (they will be
prompted to select both in the UI):

### Base URL

`/#/architecture/<ARCHITECTURE_ID>/programCreate`

### Query Parameters

> Send a user to the place order page with a specific blueprint selected

```
/#/architecture/<ARCHITECTURE_ID>/programCreate?productIds=<BLUEPRINT_ID>
```

> Send a user to the place order page with a blueprint selected and one of their items selected

```
/#/architecture/<ARCHITECTURE_ID>/programCreate?productIds=<BLUEPRINT_ID>&contentIds=<CONTENT_ID_1>
```

The following parameters can be used on their own or together to pre-select form field options for a
user. These can help reduce steps for users to place an order by pre-selecting things like content or a
Blueprint. Users can still change these values once the page has loaded.

| Query Param    | Required | type         | Description                                                             |
| -------------- | -------- | ------------ | ----------------------------------------------------------------------- |
| productIds     | false    | Int          | A single blueprint ID. Pre-selects the blueprint for program creation.  |
| contentIds     | false    | Int Array    | Comma-separated IDs. Pre-selects content to be used for the program.    |
| sideNav        | false    | String       | "closed" or "open" to control whether or not the left nav is displayed. |

## Automated Program Creation

This URL will take a user to the automated program creation screen.

### Base URL

`/#/architecture/<ARCHITECTURE_ID>/automatedProgramCreate`

| Query Param    | Required | type         | Description                                                             |
| -------------- | -------- | ------------ | ----------------------------------------------------------------------- |
| sideNav        | false    | String       | "closed" or "open" to control whether or not the left nav is displayed. |
