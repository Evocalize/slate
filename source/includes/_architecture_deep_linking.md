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

The link below is where you should send users to view all the purchases they have made for a specific architecture.

### Base URL

`/#/architecture/<ARCHITECTURE_ID>/programs`

### Query Parameters

| Query Param    | Required | type         | Description                                                             |
| -------------- | -------- | ------------ | ----------------------------------------------------------------------- |
| sideNav        | false    | String       | "closed" or "open" to control whether or not the left nav is displayed. |

## Program Creation/Purchase Base

This URL will take a user to the order process without a Blueprint or any items selected (they will be prompted to
select both in the UI):

### Base PATH

`/#/architecture/<ARCHITECTURE_ID>/programCreate`

### Query Parameters

> Send a user to the place order page with a specific blueprint selected

```javascript
redirectUser('https://your-white-label-domain.com/#/architecture/<ARCHITECTURE_ID>/programCreate?productIds=<BLUEPRINT_ID>');
```

> Send a user to the place order page with a blueprint selected and one of their items selected

```javascript
redirectUser('https://your-white-label-domain.com/#/architecture/<ARCHITECTURE_ID>/programCreate?productIds=<BLUEPRINT_ID>&contentIds=<CONTENT_ID_1>');
```

The following parameters can be used on their own or together to pre-select form field options for a user. These can
help reduce steps for users to place an order by pre-selecting things like content or a Blueprint. Users can still
change these values once the page has loaded.

| Query Param    | Required | type         | Description                                                             |
| -------------- | -------- | ------------ | ----------------------------------------------------------------------- |
| productIds     | false    | Int          | A single blueprint ID. Pre-selects the blueprint for program creation.  |
| contentIds     | false    | Int Array    | Comma-separated IDs. Pre-selects content to be used for the program.    |
| sideNav        | false    | String       | "closed" or "open" to control whether or not the left nav is displayed. |

## Automated Program Creation

This URL will take a user to the automated program creation screen.

### Base URL

`/#/architecture/<ARCHITECTURE_ID>/automatedProgramCreate`

### Query Parameters

> Filter JSON example

```json
[
    {
        "column": "agent_name",
        "rules": [
            { "operator": "eq", "value": "Jim FakeName" },
            { "operator": "eq", "value": "Jane FakeName" }
        ]
    },
    {
        "column": "price",
        "rules": [
            { "operator": "range", "value": { "gt": 1000000, "lt": 2000000 } }
        ]
    }
]
```

> Remove unnecessary white space. As long as all the white space is url encoded this step
> is not required, but it results in a shorter url. Take care not to remove space in string 
> values e.g. "New York" vs "NewYork"

```json
[{"column":"agent_name","rules":[{"operator":"eq","value":"Jim FakeName"},{"operator":"eq","value":"Jane FakeName"}]},{"column":"price","rules":[{"operator":"range","value":{"gt":1000000,"lt":2000000}}]}]
```

> Convert to url parameter
 
```
filters=%5B%7B%22column%22%3A%22agent_name%22%2C%22rules%22%3A%5B%7B%22operator%22%3A%22eq%22%2C%22value%22%3A%22Jim%20FakeName%22%7D%2C%7B%22operator%22%3A%22eq%22%2C%22value%22%3A%22Jane%20FakeName%22%7D%5D%7D%2C%7B%22column%22%3A%22price%22%2C%22rules%22%3A%5B%7B%22operator%22%3A%22range%22%2C%22value%22%3A%7B%22gt%22%3A1000000%2C%22lt%22%3A2000000%7D%7D%5D%7D%5D 
```

| Query Param    | Required | type         | Description                                                                                                      |
| -------------- | -------- | ------------ | ---------------------------------------------------------------------------------------------------------------- |
| sideNav        | false    | String       | "closed" or "open" to control whether or not the left nav is displayed.                                          |
| filters        | false    | encoded JSON | A json array of filters with the column you are filtering, along with an array of rules with operator and value. |
| showWizard     | false    | boolean      | Whether or not the automated programs wizard should be displayed. Defaults to true if not provided.              |

**Filters**

As explained in the query param table above, `filters` is a json array of filters with the column you are filtering, along
with an array of rules with operator and value:

* "column": the content column you are filtering e.g. "num_bath" or "city"
* "rules": list of operator value pairs eg: rules: `[{"operator":"contains","value":"Seattle"}]`
    * "operator" (meaning - Column|Types):
        * "eq" - (equals - String|Number|Date)
        * "contains" - (contains - String)
        * "gt" - (greater than - Number)
        * "lt" - (less than - Number)
        * "range" - (between 2 - Number)
        * "after" - (after - Date)
        * "before" - (before - Date)
        * "is_true" - true
        * "is_false" - false
    * value - the value e.g
        * use empty string "" for Boolean (true false) types e.g. {"operator":"is_true","value":""}
        * use no "" for number types e.g. {"operator":"gt","value":100}
        * range is a special case. eg. between 100-2,000: "value":{"gt":100,"lt":2000}
