# Access Control
Many portions of our API use common access control models
to control which users can access various objects (ex audiences or content items). The
system works by defining grantees, permissions, and grants.

## Grantee
A grantee represents something that is being granted permissions
on an object. The types of grantees that currently exist are defined
by the `RestGranteeType` enum:

### RestGranteeType
* `USER` - refers to a single user
* `GROUP` - refers to all users in a group
* `GROUP_ROLE` - refers to all users in a group who have a specific role
* `ORGANIZATION` - refers to all users in the organization
* `USER_IN_GROUP` - refers to a specific user, but only when they have
  a specific group selected.

### Grantee Properties
The model used for a grantee has the following properties

| Property Name | Required | Type            | Description                                                                                                                        |
|---------------|----------|-----------------|------------------------------------------------------------------------------------------------------------------------------------|
| type          | true     | RestGranteeType | What type of grantee this is.                                                                                                      |
| userId        | false    | String          | Only used for USER and USER_IN_GROUP grantees. Defines what user the grantee refers to.                                            |
| groupId       | false    | String          | Only used for GROUP, GROUP_ROLE, and USER_IN_GROUP grantees. Defines what group the grantee refers to.                             |
| groupRole     | false    | String          | Only used for GROUP_ROLE grantees. Defines what group role the grantee refers to. Valid values are "group_user" and "group_admin". |

Note that the only required property for ORGANIZATION grantees is the `type` property.

## Grantees
Portions of our API allow you to specify multiple [Grantees](#grantee).
When this is the case, we utilize an object with a single `grantees` property.

| Property Name | Required | Type                                | Description         |
|---------------|----------|-------------------------------------|---------------------|
| grantees      | true     | List of [Grantee](#grantee) objects | A list of grantees. |

## Permissions
What permissions are available depends on which portion
of the API you are using. For example, our audiences API
currently only supports `READ` permissions. See the
docs for the specific resources you are accessing to
see what permissions are available.

## Grant
A grant is used to assign permissions to a grantee on a specific object.
You'll notice after reading the Grantee documentation (located above) that a user
can actually match multiple grantees. For example, a user would match
a USER grantee that refers to that specific user, and it would also
match an ORGANIZATION grantee (since that user is part of your organization).
When multiple grants exist for a user on a single object (ex an audience),
then the permissions are summed. In other words, the user has all permissions
from those grants on the target object.
The model used for a grant has the following properties

| Property Name | Required | Type        | Description                                                                                                                                                                                                                                                                                                    |
|---------------|----------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| grantee       | true     | Grantee     | The grantee that is assigned permissions on the target object.                                                                                                                                                                                                                                                 |
| permissions   | true     | String List | What permissions the grantee has on the target object.                                                                                                                                                                                                                                                         |
| objectId      | false    | String      | The ID of the target object (the object that the grantee is receiving permissions on). This property usually only has a value if the grant is part of a response (instead of a request) or if the endpoint is a bulk endpoint. Otherwise, the resource in the endpoint's path is implicitly the target object. |

## Grants
Portions of our API allow you to specify multiple [Grants](#grant).
When this is the case, we utilize an object with a single `grants` property.

| Property Name | Required | Type                            | Description       |
|---------------|----------|---------------------------------|-------------------|
| grants        | true     | List of [Grant](#grant) objects | A list of grants. |
