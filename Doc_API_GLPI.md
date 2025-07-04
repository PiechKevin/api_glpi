# GLPI REST API: Documentation

## Glossary

```
Endpoint
Resource available though the API. The endpoint is the URL where your API can
be accessed by a client application
```
```
Method
HTTP verbs to indicate the desired action to be performed on the identified
resource. See:
https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods
```
```
itemtype
A GLPI type, could be an asset, an ITIL or a configuration object, etc. This type
must be a class who inherits CommonDTBM GLPI class.
```
```
searchOption
A column identifier (integer) of an itemtype (ex: 1 -> id, 2 -> name, ...). See List
searchOptions endpoint.
```
```
JSON Payload
content of HTTP Request in JSON format (HTTP body)
```
```
Query string
URL parameters
```
```
User token
Used in login process instead of login/password couple. It represent the user
with a string. You can find user token in the settings tabs of users.
```
```
Session token
A string describing a valid session in glpi. Except initSession endpoint who
provide this token, all others require this string to be used.
```
```
App(lication) token
An optional way to filter the access to the API. On API call, it will try to find an API
client matching your IP and the app token (if provided). You can define an API
client with an app token in general configuration for each of your external
applications to identify them (each API client have its own history).
```
## Important

```
You should always provide a Content-Type header in your HTTP calls.
Currently, the API supports:
```
```
application/json
multipart/form-data (for files upload, see Add item(s) endpoint.
```
```
GET requests must have an empty body. You must pass all parameters in URL.
Failing to do so will trigger an HTTP 400 response.
```
```
By default, sessions used in this API are read-only. Only some methods have
write access to session:
```
```
initSession
killSession
```
```
changeActiveEntities
changeActiveProfile
You could pass an additional parameter "session_write=true" to bypass this
default. This read-only mode allow to use this API with parallel calls. In write
mode, sessions are locked and your client must wait the end of a call before
the next one can execute.
```
```
You can filter API access by enable the following parameters in GLPI General
Configuration (API tab):
```
```
IPv4 range
```
```
IPv6 address
```
### App-Token parameter: if not empty, you should pass this parameter in

```
all of your API calls
```
```
Session and App tokens could be provided in query string instead of header
parameters.
```
## Init session

```
URL: apirest.php/initSession/
```
```
Description: Request a session token to uses other API endpoints.
Method: GET
```
```
Parameters: (Headers)
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
### a couple login & password: 2 parameters to login with user

```
authentication. You should pass this 2 parameters in http basic auth. It
consists in a Base64 string with login and password separated by ":" A
valid Authorization header is:
"Authorization: Basic base64({login}:{password})"
OR
```
### an user_token defined in User Preference (See 'Remote access key') You

```
should pass this parameter in 'Authorization' HTTP header. A valid
Authorization header is:
"Authorization: user_token
q56hqkniwot8wntb3z1qarka5atf365taaa2uyjrn"
```
```
Parameters: (query string)
```
### get_full_session (default: false): Get the full session, useful if you want

```
to login and access session data in one request.
```
```
Returns:
```
### 200 (OK) with the session_token string.

```
400 (Bad Request) with a message indicating an error in input
parameter.
401 (UNAUTHORIZED)
```
```
Examples usage (CURL):
```
## Kill session

```
URL: apirest.php/killSession/
Description: Destroy a session identified by a session token.
```
```
Method: GET
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Returns:
200 (OK).
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
Example usage (CURL):
```
## Lost password

```
This endpoint allows to request password recovery and password reset. This
endpoint works under the following conditions: * GLPI has notifications enabled *
the email address of the user belongs to a user account.
```
```
Reset password request:
```
```
URL: apirest.php/lostPassword/
Description: Sends a notification to the user to reset his password
```
```
Method: PUT or PATCH
Parameters: (JSON Payload)
```
### email: email address of the user to recover. Mandatory.

```
Returns:
200 (OK).
```
```
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
Password reset :
```
```
URL: apirest.php/lostPassword/
Description: Sends a notification to the user to reset his password
```
```
Method: PUT or PATCH
Parameters: (JSON Payload)
```
### email: email address of the user to recover. Mandatory.

### password_forget_token: reset token

### password: the new password for the user

```
Returns:
200 (OK).
```
```
400 (Bad Request) with a message indicating an error in input
parameter.
```
## Get my profiles

```
URL: apirest.php/getMyProfiles/
Description: Return all the profiles associated to logged user.
```
```
Method: GET
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Returns:
200 (OK) with an array of all profiles.
```
```
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
Example usage (CURL):
```
## Get active profile

```
URL: apirest.php/getActiveProfile/
Description: Return the current active profile.
```
```
Method: GET
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Returns:
200 (OK) with an array representing current profile.
```
```
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
Example usage (CURL):
```
## Change active profile

```
URL: apirest.php/changeActiveProfile/
```
```
Description: Change active profile to the profiles_id one. See getMyProfiles
endpoint for possible profiles.
Method: POST
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (JSON Payload)
```
### profiles_id: (default 'all') ID of the new active profile. Mandatory.

```
Returns:
200 (OK).
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
404 (Not found) with a message indicating an error ig the profile does
not exists or usable.
```
```
Example usage (CURL):
```
## Get my entities

```
URL: apirest.php/getMyEntities/
```
```
Description: Return all the possible entities of the current logged user (and for
current active profile).
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (query string)
```
### is_recursive (default: false): Also display sub entities of the active entity.

```
Optionnal
```
```
Returns:
200 (OK) with an array of all entities (with id and name).
```
```
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
Example usage (CURL):
```
## Get active entities

```
URL: apirest.php/getActiveEntities/
```
```
Description: Return active entities of current logged user.
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Returns:
200 (OK) with an array with 3 keys:
```
### active_entity: current set entity.

### active_entity_recursive: boolean, if we see sons of this entity.

### active_entities: array all active entities (active_entity and its sons).

```
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
Example usage (CURL):
```
## Change active entities

```
URL: apirest.php/changeActiveEntities/
```
```
Description: Change active entity to the entities_id one. See getMyEntities
endpoint for possible entities.
Method: POST
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (JSON Payload)
```
### entities_id: (default 'all') ID of the new active entity ("all" => load all

```
possible entities). Optional.
```
### is_recursive: (default false) Also display sub entities of the active entity.

```
Optional.
```
```
Returns:
200 (OK).
```
```
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
Example usage (CURL):
```
## Get full session

```
URL: apirest.php/getFullSession/
```
```
Description: Return the current php $_SESSION.
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Returns:
200 (OK) with an array representing the php session.
```
```
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
Example usage (CURL):
```
## Get GLPI config

```
URL: apirest.php/getGlpiConfig/
```
```
Description: Return the current $CFG_GLPI.
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Returns:
200 (OK) with an array representing the php global variable $CFG_GLPI.
```
```
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
Example usage (CURL):
```
## Get an item

```
URL: apirest.php/:itemtype/:id
```
```
Description: Return the instance fields of itemtype identified by id.
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (query string)
```
### id: unique identifier of the itemtype. Mandatory.

### expand_dropdowns (default: false): show dropdown name instead of id.

```
Optional.
```
### get_hateoas (default: true): Show relations of the item in a links

```
attribute. Optional.
```
### get_sha1 (default: false): Get a sha1 signature instead of the full answer.

```
Optional.
```
### with_devices: Only for [Computer, NetworkEquipment, Peripheral,

```
Phone, Printer], retrieve the associated components. Optional.
```
### with_disks: Only for Computer, retrieve the associated file-systems.

```
Optional.
```
### with_softwares: Only for Computer, retrieve the associated software's

```
installations. Optional.
```
### with_connections: Only for Computer, retrieve the associated direct

```
connections (like peripherals and printers) .Optional.
```
### with_networkports: Retrieve all network connections and advanced

```
information. Optionnal.
```
### with_infocoms: Retrieve financial and administrative information.

```
Optional.
```
### with_contracts: Retrieve associated contracts. Optional.

### with_documents: Retrieve associated external documents. Optional.

### with_tickets: Retrieve associated ITIL tickets. Optional.

### with_problems: Retrieve associated ITIL problems. Optional.

### with_changes: Retrieve associated ITIL changes. Optional.

### with_notes: Retrieve Notes. Optional.

### with_logs: Retrieve historical. Optional.

### add_keys_names: Retrieve friendly names. Array containing fkey(s)

```
and/or "id". Optional.
```
```
Returns:
200 (OK) with item data (Last-Modified header should contain the date
of last modification of the item).
401 (UNAUTHORIZED).
```
```
404 (NOT FOUND).
```
```
Example usage (CURL):
```
```
Note: To download a document see Download a document file.
```
## Get all items

```
URL: apirest.php/:itemtype/
```
```
Description: Return a collection of rows of the itemtype.
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (query string)
```
### expand_dropdowns (default: false): show dropdown name instead of id.

```
Optional.
```
### get_hateoas (default: true): Show relation of item in a links attribute.

```
Optional.
```
### only_id (default: false): keep only id keys in returned data. Optional.

### range (default: 0-49): a string with a couple of number for start and end

```
of pagination separated by a '-'. Ex: 150-199. Optional.
```
### sort (default 1): name of the field to sort by. Optional.

### order (default ASC): ASC - Ascending sort / DESC Descending sort.

```
Optional.
```
### searchText (default NULL): array of filters to pass on the query (with key

```
= field and value the text to search)
```
### is_deleted (default: false): Return deleted element. Optional.

### add_keys_names: Retrieve friendly names. Array containing fkey(s)

```
and/or "id". Optional.
```
### with_networkports: Retrieve all network connections and advanced

```
information. Optionnal.
```
```
Returns:
```
```
200 (OK) with items data.
206 (PARTIAL CONTENT) with items data defined by range.
```
```
401 (UNAUTHORIZED).
and theses headers:
```
### Content-Range offset – limit / count

### Accept-Range itemtype max

```
Example usage (CURL):
```
## Get sub items

```
URL: apirest.php/:itemtype/:id/:sub_itemtype
Description: Return a collection of rows of the sub_itemtype for the identified
item.
```
```
Method: GET
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (query string)
id: unique identifier of the parent itemtype. Mandatory.
```
### expand_dropdowns (default: false): show dropdown name instead of id.

```
Optional.
```
### get_hateoas (default: true): Show item's relations in a links attribute.

```
Optional.
```
### only_id (default: false): keep only id keys in returned data. Optional.

### range (default: 0-49): a string with a couple of number for start and end

```
of pagination separated by a '-' char. Ex: 150-199. Optional.
```
### sort (default 1): id of the "searchoption" to sort by. Optional.

### order (default ASC): ASC - Ascending sort / DESC Descending sort.

```
Optional.
```
### add_keys_names: Retrieve friendly names. Array containing fkey(s)

```
and/or "id". Optional.
```
```
Returns:
```
```
200 (OK) with the items data.
```
```
401 (UNAUTHORIZED).
and theses headers:
```
### Content-Range offset – limit / count

### Accept-Range itemtype max

```
Example usage (CURL):
```
## Get multiple items

```
URL: apirest.php/getMultipleItems
```
```
Description: Virtually call Get an item for each line in input. So, you can have a
ticket, a user in the same query.
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (query string)
```
### items: items to retrieve. Mandatory. Each line of this array should

```
contains two keys: * itemtype * items_id
```
### expand_dropdowns (default: false): show dropdown name instead of id.

```
Optional.
```
### get_hateoas (default: true): Show relations of the item in a links

```
attribute. Optional.
```
### get_sha1 (default: false): Get a sha1 signature instead of the full answer.

```
Optional.
```
### with_devices: Only for [Computer, NetworkEquipment, Peripheral,

```
Phone, Printer], retrieve the associated components. Optional.
```
### with_disks: Only for Computer, retrieve the associated file-systems.

```
Optional.
```
### with_softwares: Only for Computer, retrieve the associated software's

```
installations. Optional.
```
### with_connections: Only for Computer, retrieve the associated direct

```
connections (like peripherals and printers) .Optional.
```
### with_networkports: Retrieve all network connections and advanced

```
information. Optionnal.
```
### with_infocoms: Retrieve financial and administrative information.

```
Optional.
```
### with_contracts: Retrieve associated contracts. Optional.

### with_documents: Retrieve associated external documents. Optional.

### with_tickets: Retrieve associated ITIL tickets. Optional.

### with_problems: Retrieve associated ITIL problems. Optional.

### with_changes: Retrieve associated ITIL changes. Optional.

### with_notes: Retrieve Notes. Optional.

### with_logs: Retrieve historical. Optional.

### add_keys_names: Retrieve friendly names. Array containing fkey(s)

```
and/or "id". Optional.
```
```
Returns:
200 (OK) with item data (Last-Modified header should contain the date
of last modification of the item).
```
```
401 (UNAUTHORIZED).
404 (NOT FOUND).
```
```
Example usage (CURL):
```
## List searchOptions

```
URL: apirest.php/listSearchOptions/:itemtype
```
```
Description: List the searchoptions of provided itemtype. To use with Search
items.
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (query string)
```
### raw: return searchoption uncleaned (as provided by core)

```
Returns:
200 (OK) with all searchoptions of specified itemtype (format:
searchoption_id: {option_content}).
```
```
401 (UNAUTHORIZED).
```
```
Example usage (CURL):
```
## Search items

```
URL: apirest.php/search/:itemtype/
```
```
Description: Expose the GLPI searchEngine and combine criteria to retrieve a
list of elements of specified itemtype. > Note: you can use 'AllAssets' itemtype
to retrieve a combination of all asset's types.
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (query string)
```
### criteria: array of criterion objects to filter search. Optional. You can

```
optionally precise meta=true to pass a searchoption of another itemtype
(meta-criteria). Each criterion object must provide at least:
```
### link: (optional for 1st element) logical operator in [AND, OR,

#### AND NOT, AND NOT].

```
And you can pass a direct searchoption usage :
```
### field: id of the searchoption.

### meta: boolean, is this criterion a meta one?

### itemtype: for meta=true criterion, precise the itemtype to use.

### searchtype: type of search in [contains¹, equals², notequals²,

```
lessthan, morethan, under, notunder].
```
### value: the value to search.

```
Or a list of sub-nodes with the key:
```
### criteria: nested criteria inside this criteria.

```
Ex:
```
### metacriteria (optional): array of meta-criterion objects to filter search.

```
Optional. A meta search is a link with another itemtype (ex: Computer
with software). Deprecated: Now criteria support meta flag, you should
use it instead direct metacriteria option.
```
```
Each meta-criterion object must provide:
```
### link: logical operator in [AND, OR, AND NOT, AND NOT].

```
Mandatory.
```
### itemtype: second itemtype to link.

### field: id of the searchoption.

### searchtype: type of search in [contains¹, equals², notequals²,

```
lessthan, morethan, under, notunder].
```
### value: the value to search.

```
Ex:
```
### sort (default 1): id of the searchoption to sort by. Optional.

### order (default ASC): ASC - Ascending sort / DESC Descending sort.

```
Optional.
```
### range (default 0-49): a string with a couple of number for start and end

```
of pagination separated by a '-'. Ex: 150-199. Optional.
```
### forcedisplay: array of columns to display (default empty = use display

```
preferences and searched criteria). Some columns will be always
presents (1: id, 2: name, 80: Entity). Optional.
```
### rawdata (default false): a boolean for displaying raws data of the Search

```
engine of GLPI (like SQL request, full searchoptions, etc)
```
### withindexes (default false): a boolean to retrieve rows indexed by items

```
id. By default this option is set to false, because order of JSON objects
(which are identified by index) cannot be garrantued (from
http://json.org/ : An object is an unordered set of name/value pairs). So,
we provide arrays to guarantying sorted rows.
```
### uid_cols (default false): a boolean to identify cols by the 'uniqid' of the

```
searchoptions instead of a numeric value (see List searchOptions and
'uid' field)
```
### giveItems (default false): a boolean to retrieve the data with the html

```
parsed from core, new data are provided in data_html key.
```
### ¹ - contains will use a wildcard search per default. You can restrict at the

### beginning using the ^ character, and/or at the end using the $ character.

### ² - equals and notequals are designed to be used with dropdowns. Do

```
not expect those operators to search for a strictly equal value (see ¹
above).
```
```
Returns:
```
```
200 (OK) with all rows data with this format:
```
```
206 (PARTIAL CONTENT) with rows data (pagination doesn't permit to
display all rows).
```
#### 401 (UNAUTHORIZED).

```
and theses headers:
```
### Content-Range offset – limit / count

### Accept-Range itemtype max

```
Example usage (CURL):
```
## Add item(s)

```
URL: apirest.php/:itemtype/
```
```
Description: Add an object (or multiple objects) into GLPI.
Method: POST
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (JSON Payload)
```
### input: an object with fields of itemtype to be inserted. You can add

```
several items in one action by passing an array of objects. Mandatory.
Important: In case of 'multipart/data' content_type (aka file upload), you
should insert your parameters into a 'uploadManifest' parameter. Theses
serialized data should be a JSON string.
```
```
Returns:
```
```
201 (OK) with id of added items.
```
```
207 (Multi-Status) with id of added items and errors.
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
401 (UNAUTHORIZED).
And additional header can be provided on creation success:
```
```
Location when adding a single item.
Link on bulk addition.
```
```
Examples usage (CURL):
```
```
Note: To upload a document see Upload a document file.
```
## Update item(s)

```
URL: apirest.php/:itemtype/:id
Description: Update an object (or multiple objects) existing in GLPI.
```
```
Method: PUT or PATCH
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (JSON Payload)
```
### id: the unique identifier of the itemtype passed in URL. You could skip

```
this parameter by passing it in the input payload.
```
### input: Array of objects with fields of itemtype to be updated. Mandatory.

```
You could provide in each object a key named 'id' to identify the item(s)
to update.
```
```
Returns:
200 (OK) with update status for each item.
```
```
207 (Multi-Status) with id of added items and errors.
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
401 (UNAUTHORIZED).
```
```
Example usage (CURL):
```
## Delete item(s)

```
URL: apirest.php/:itemtype/:id
Description: Delete an object existing in GLPI.
```
```
Method: DELETE
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (query string)
```
### id: unique identifier of the itemtype passed in the URL. You could skip

```
this parameter by passing it in the input payload. OR
```
### input Array of id who need to be deleted. This parameter is passed by

```
payload.
id parameter has precedence over input payload.
```
### force_purge (default false): boolean, if the itemtype have a trashbin, you

```
can force purge (delete finally). Optional.
```
### history (default true): boolean, set to false to disable saving of deletion

```
in global history. Optional.
```
```
Returns:
```
### 200 (OK) in case of multiple deletion.

### 204 (No Content) in case of single deletion.

```
207 (Multi-Status) with id of deleted items and errors.
```
```
400 (Bad Request) with a message indicating an error in input
parameter.
401 (UNAUTHORIZED).
```
```
Example usage (CURL):
```
## Get available massive actions for an itemtype

```
URL: apirest.php/getMassiveActions/:itemtype/
```
```
Description: Show the availables massive actions for a given itemtype.
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (query string)
```
### is_deleted (default false): Show specific actions for items in the

```
trashbin.
```
```
Returns:
200 (OK).
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
401 (UNAUTHORIZED).
```
```
Example usage (CURL):
```
## Get available massive actions for an item

```
URL: apirest.php/getMassiveActions/:itemtype/:id
```
```
Description: Show the availables massive actions for a given item.
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (query string)
```
```
Returns:
200 (OK).
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
401 (UNAUTHORIZED).
```
```
Example usage (CURL):
```
## Get massive action parameters

```
URL: apirest.php/getMassiveActionParameters/:itemtype/
```
```
Description: Show the availables parameters for a given massive action.
Warning: experimental endpoint, some required parameters may be
missing from the returned content.
```
```
Method: GET
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (query string)
Returns:
200 (OK).
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
401 (UNAUTHORIZED).
```
```
Example usage (CURL):
```
## Apply massive action

```
URL: apirest.php/applyMassiveAction/:itemtype/
```
```
Description: Run the given massive action
Method: POST
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Parameters: (json payload)
```
### ids items to execute the action on. Mandatory.

### specific action parameters some actions require specific parameters to

```
run. You can check them through the 'getMassiveActionParameters'
endpoint.
```
```
Returns:
200 (OK) All items were processed.
```
```
207 (Multi-Status) Not all items were successfully processed.
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
401 (UNAUTHORIZED).
422 (Unprocessable Entity) All items failed to be processed.
```
```
Example usage (CURL):
```
## Special cases

## Upload a document file

```
See Add item(s) and apply specific instructions below.
```
```
Uploading a file requires use of 'multipart/data' content_type. The input data must
be send in a 'uploadManifest' parameter and use the JSON format.
```
```
Examples usage (CURL):
```
## Download a document file

```
URL: apirest.php/Document/:id
```
```
Description: Download a document.
Method: GET
```
```
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
### Accept: must be application/octet-stream. This header OR the

### parameter alt is mandatory

```
Parameters: (query string)
```
### id: unique identifier of the itemtype passed in the URL. You could skip

```
this parameter by passing it in the input payload.
```
### alt: must be 'media'. This parameter or the header Accept is mandatory.

```
id parameter has precedence over input payload.
```
```
Returns:
```
### 200 (OK) in case of multiple deletion.

```
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
401 (UNAUTHORIZED).
```
```
Example usage (CURL):
```
```
The body of the answer contains the raw file attached to the document.
```
```
The body of the answer contains the raw file attached to the document.
```
## Get a user's profile picture

```
URL: apirest.php/User/:id/Picture
Description: Get a user's profile picture.
```
```
Method: GET
Parameters: (Headers)
```
### Session-Token: session var provided by initSession endpoint.

```
Mandatory.
```
### App-Token: authorization string provided by the GLPI API configuration.

```
Optional.
```
```
Returns:
200 (OK) with the raw image in the request body.
204 (No content) if the request is correct but the specified user doesn't
have a profile picture.
```
```
400 (Bad Request) with a message indicating an error in input
parameter.
```
```
Example usage (CURL):
```
```
The body of the answer contains the raw image.
```
## Sanitized content

```
By default, the API will return sanitized content.
This mean that all HTML special characters will be encoded.
You can disable this feature by adding the following header to your request:
```
```
X-GLPI-Sanitized-Content: false
```
## Errors

## ERROR_ITEM_NOT_FOUND

```
The desired resource (itemtype-id) was not found in the GLPI database.
```
## ERROR_BAD_ARRAY

```
The HTTP body must be an an array of objects.
```
## ERROR_METHOD_NOT_ALLOWED

```
You specified an inexistent or not not allowed resource.
```
## ERROR_RIGHT_MISSING

```
The current logged user miss rights in his profile to do the provided action. Alter this
profile or choose a new one for the user in GLPI main interface.
```
## ERROR_SESSION_TOKEN_INVALID

```
The Session-Token provided in header is invalid. You should redo an Init session
request.
```
## ERROR_SESSION_TOKEN_MISSING

```
You miss to provide Session-Token in header of your HTTP request.
```
## ERROR_APP_TOKEN_PARAMETERS_MISSING

```
The current API requires an App-Token header for using its methods.
```
## ERROR_WRONG_APP_TOKEN_PARAMETER

```
It seems the provided application token doesn't exists in GLPI API configuration.
```
## ERROR_NOT_DELETED

```
You must mark the item for deletion before actually deleting it
```
## ERROR_NOT_ALLOWED_IP

```
We can't find an active client defined in configuration for your IP. Go to the GLPI
Configuration > Setup menu and API tab to check IP access.
```
## ERROR_LOGIN_PARAMETERS_MISSING

```
One of theses parameter(s) is missing:
```
```
login and password
```
```
or user_token
```
## ERROR_LOGIN_WITH_CREDENTIALS_DISABLED

```
The GLPI setup forbid the login with credentials, you must login with your
user_token instead. See your personal preferences page or setup API access in GLPI
main interface.
```
## ERROR_GLPI_LOGIN_USER_TOKEN

```
The provided user_token seems invalid. Check your personal preferences page in
GLPI main interface.
```
## ERROR_GLPI_LOGIN

```
We cannot login you into GLPI. This error is not relative to API but GLPI core. Check
the user administration and the GLPI logs files (in files/_logs directory).
```
## ERROR_ITEMTYPE_NOT_FOUND_NOR_COMMONDBTM

```
You asked a inexistent resource (endpoint). It's not a predefined (initSession,
getFullSession, etc) nor a GLPI CommonDBTM resources.
```
## ERROR_SQL

```
We suspect an SQL error. This error is not relative to API but to GLPI core. Check the
GLPI logs files (in files/_logs directory).
```
## ERROR_RANGE_EXCEED_TOTAL

```
The range parameter you provided is superior to the total count of available data.
```
## ERROR_GLPI_ADD

```
We cannot add the object to GLPI. This error is not relative to API but to GLPI core.
Check the GLPI logs files (in files/_logs directory).
```
## ERROR_GLPI_PARTIAL_ADD

```
Some of the object you wanted to add triggers an error. Maybe a missing field or
rights. You'll find with this error a collection of results.
```
## ERROR_GLPI_UPDATE

```
We cannot update the object to GLPI. This error is not relative to API but to GLPI
core. Check the GLPI logs files (in files/_logs directory).
```
## ERROR_GLPI_PARTIAL_UPDATE

```
Some of the object you wanted to update triggers an error. Maybe a missing field or
rights. You'll find with this error a collection of results.
```
## ERROR_GLPI_DELETE

```
We cannot delete the object to GLPI. This error is not relative to API but to GLPI
core. Check the GLPI logs files (in files/_logs directory).
```
## ERROR_GLPI_PARTIAL_DELETE

```
Some of the objects you want to delete triggers an error, maybe a missing field or
rights. You'll find with this error, a collection of results.
```
## ERROR_MASSIVEACTION_KEY

```
Missing or invalid massive action key. Run 'getMassiveActions' endpoint to see
available keys.
```
## ERROR_MASSIVEACTION_NO_IDS

```
No ids supplied when trying to run a massive action.
```
## ERROR_FIELD_NOT_FOUND

```
The field specified as the key for the searchText parameter does not exist. This field
must refer to a column in the table corresponding to the element in the query.
```
## ERROR_UNKNOWN

```
An unknown error has occurred. This may be due to an unexpected condition
encountered by the server or an issue that does not fit into any of the predefined
error categories. Check the server logs for more details or contact the support
team.
```
## Servers configuration

```
By default, you can use http://path/to/glpi/apirest.php without any additional
configuration.
```
```
You'll find below some examples to configure your web server to redirect your
http://.../glpi/api/ URL to the apirest.php file.
```
## Apache Httpd

```
We provide in root .htaccess of GLPI an example to enable API URL rewriting.
```
```
You need to uncomment (removing #) theses lines:
```
```
By enabling URL rewriting, you could use API with this URL : http://path/to/glpi/api/.
You need also to enable rewrite module in apache httpd and permit GLPI's .htaccess
to override server configuration (see AllowOverride directive).
```
```
Note for apache+fpm users:
```
```
You may have difficulties to pass Authorization header in this configuration. You
have two options :
```
```
pass the user_token or credentials (login/password) in the http query (as GET
parameters).
add env to your virtualhost: SetEnvIf Authorization "(.*)"
HTTP_AUTHORIZATION=$1.
```
## Nginx

```
This example of configuration was achieved on ubuntu with php7 fpm.
```
## Summary

#### GLPI REST API:

```
Documentation
Summary
```
```
Glossary
Important
```
```
Init session
Kill session
```
```
Lost password
Get my profiles
```
```
Get active profile
Change active
profile
```
```
Get my entities
Get active entities
```
```
Change active
entities
Get full session
```
```
Get GLPI config
Get an item
```
```
Get all items
Get sub items
```
```
Get multiple items
List
searchOptions
```
```
Search items
Add item(s)
```
```
Update item(s)
Delete item(s)
```
```
Get available
massive actions
for an itemtype
Get available
massive actions
for an item
```
```
Get massive
action parameters
Apply massive
action
```
```
Special cases
Upload a
document file
```
```
Download a
document file
Get a user's
profile picture
```
```
Sanitized content
Errors
```
```
ERROR_ITEM_NOT
ERROR_BAD_ARRA
```
```
ERROR_METHOD_N
ERROR_RIGHT_MIS
```
```
ERROR_SESSION_T
ERROR_SESSION_T
```
```
ERROR_APP_TOKE
ERROR_WRONG_A
```
```
ERROR_NOT_DELE
ERROR_NOT_ALLO
```
```
ERROR_LOGIN_PAR
ERROR_LOGIN_WIT
```
```
ERROR_GLPI_LOGI
ERROR_GLPI_LOGI
```
```
ERROR_ITEMTYPE
ERROR_SQL
```
```
ERROR_RANGE_EX
ERROR_GLPI_ADD
```
```
ERROR_GLPI_PART
ERROR_GLPI_UPDA
```
```
ERROR_GLPI_PART
ERROR_GLPI_DELE
```
```
ERROR_GLPI_PART
ERROR_MASSIVEA
```
```
ERROR_MASSIVEA
ERROR_FIELD_NOT
```
```
ERROR_UNKNOWN
Servers
configuration
```
```
Apache Httpd
Nginx
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Authorization: Basic Z2xwaTpnbHBp" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/initSession'
```
#### < 200 OK

#### < {

```
"session_token": "83af7e620c83a50a18d3eac2f6ed05a3ca0bea62"
}
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Authorization: user_token
q56hqkniwot8wntb3z1qarka5atf365taaa2uyjrn" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/initSession?get_full_session=true'
```
#### < 200 OK

#### < {

```
"session_token": "83af7e620c83a50a18d3eac2f6ed05a3ca0bea62",
"session": {
'glpi_plugins': ...,
'glpicookietest': ...,
'glpicsrftokens': ...,
...
}
}
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/killSession'
```
#### < 200 OK

```
$ curl -X PUT \
-H 'Content-Type: application/json' \
-d '{"email": "user@domain.com"}' \
'http://path/to/glpi/apirest.php/lostPassword'
```
#### < 200 OK

```
$ curl -X PUT \
-H 'Content-Type: application/json' \
-d '{"email": "user@domain.com", \
"password_forget_token":
"b0a4cfe81448299ebed57442f4f21929c80ebee5" \
"password": "NewPassword" \
}' \
'http://path/to/glpi/apirest.php/lostPassword'
```
#### < 200 OK

```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getMyProfiles'
```
#### < 200 OK

#### < {

```
'myprofiles': [
{
'id': 1
'name': "Super-admin",
'entities': [
...
],
...
},
....
]
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getActiveProfile'
```
#### < 200 OK

#### < {

```
'name': "Super-admin",
'entities': [
...
]
}
```
```
$ curl -X POST \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"profiles_id": 4}' \
'http://path/to/glpi/apirest.php/changeActiveProfile'
```
#### < 200 OK

```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getMyEntities'
```
#### < 200 OK

#### < {

```
'myentities': [
{
'id': 71
'name': "my_entity"
},
....
]
}
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getActiveEntities'
```
#### < 200 OK

#### < {

```
'active_entity': {
'id': 1 ,
'active_entity_recursive': true,
'active_entities': [
{"id":1},
{"id":71},...
]
}
}
```
```
$ curl -X POST \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"entities_id": 1, "is_recursive": true}' \
'http://path/to/glpi/apirest.php/changeActiveEntities'
```
#### < 200 OK

```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getFullSession'
```
#### < 200 OK

#### < {

```
'session': {
'glpi_plugins': ...,
'glpicookietest': ...,
'glpicsrftokens': ...,
...
}
}
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getGlpiConfig'
```
#### < 200 OK

#### < {

```
'cfg_glpi': {
'languages': ...,
'glpitables': ...,
'unicity_types':...,
...
}
}
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/Computer/71?expand_dropdowns=true'
```
#### < 200 OK

#### < {

```
"id": 71 ,
"entities_id": "Root Entity",
"name": "adelaunay-ThinkPad-Edge-E320",
"serial": "12345",
"otherserial": "test2",
"contact": "adelaunay",
"contact_num": null,
"users_id_tech": " ",
"groups_id_tech": " ",
"comment": "test222222qsdqsd",
"date_mod": "2015-09-25 09:33:41",
"autoupdatesystems_id": " ",
"locations_id": "00:0e:08:3b:7d:04",
"networks_id": " ",
"computermodels_id": "1298A8G",
"computertypes_id": "Notebook",
"is_template": 0 ,
"template_name": null,
"manufacturers_id": "LENOVO",
"is_deleted": 0 ,
"is_dynamic": 1 ,
"users_id": "adelaunay",
"groups_id": " ",
"states_id": "Production",
"ticket_tco": "0.0000",
"uuid": "",
"date_creation": null,
"links": [{
"rel": "Entity",
"href": "http://path/to/glpi/api/Entity/0"
}, {
"rel": "Location",
"href": "http://path/to/glpi/api/Location/3"
}, {
"rel": "Domain",
"href": "http://path/to/glpi/api/Domain/18"
}, {
"rel": "ComputerModel",
"href": "http://path/to/glpi/api/ComputerModel/11"
}, {
"rel": "ComputerType",
"href": "http://path/to/glpi/api/ComputerType/3"
}, {
"rel": "Manufacturer",
"href": "http://path/to/glpi/api/Manufacturer/260"
}, {
"rel": "User",
"href": "http://path/to/glpi/api/User/27"
}, {
"rel": "State",
"href": "http://path/to/glpi/api/State/1"
}]
}
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/Computer/?expand_dropdowns=true'
```
#### < 206 OK

```
< Content-Range: 0 -49/
< Accept-Range: 990
< [
{
"id": 34 ,
"entities_id": "Root Entity",
"name": "glpi",
"serial": "VMware-42 01 f4 65 27 59 a9 fb-11 bc cd b8 64 68
1f 4b",
"otherserial": null,
"contact": "teclib",
"contact_num": null,
"users_id_tech": "&nbsp;",
"groups_id_tech": "&nbsp;",
"comment": "x86_64/00-09-15 08:03:28",
"date_mod": "2011-12-16 17:52:55",
"autoupdatesystems_id": "FusionInventory",
"locations_id": "&nbsp;",
"networks_id": "&nbsp;",
"computermodels_id": "VMware Virtual Platform",
"computertypes_id": "Other",
"is_template": 0 ,
"template_name": null,
"manufacturers_id": "VMware, Inc.",
"is_deleted": 0 ,
"is_dynamic": 1 ,
"users_id": "&nbsp;",
"groups_id": "&nbsp;",
"states_id": "Production",
"ticket_tco": "0.0000",
"uuid": "4201F465-2759-A9FB-11BC-CDB864681F4B",
"links": [{
"rel": "Entity",
"href": "http://path/to/glpi/api/Entity/0"
}, {
"rel": "AutoUpdateSystem",
"href": "http://path/to/glpi/api/AutoUpdateSystem/1"
}, {
"rel": "Domain",
"href": "http://path/to/glpi/api/Domain/12"
}, {
"rel": "ComputerModel",
"href": "http://path/to/glpi/api/ComputerModel/1"
}, {
"rel": "ComputerType",
"href": "http://path/to/glpi/api/ComputerType/2"
}, {
"rel": "Manufacturer",
"href": "http://path/to/glpi/api/Manufacturer/1"
}, {
"rel": "State",
"href": "http://path/to/glpi/api/State/1"
}]
},
{
"id": 35 ,
"entities_id": "Root Entity",
"name": "mavm1",
"serial": "VMware-42 20 d3 04 ac 49 ed c8-ea 15 50 49 e1 40
0f 6c",
"otherserial": null,
"contact": "teclib",
"contact_num": null,
"users_id_tech": "&nbsp;",
"groups_id_tech": "&nbsp;",
"comment": "x86_64/01-01-04 19:50:40",
"date_mod": "2012-05-24 06:43:35",
...
}
]
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/User/2/Log'
```
#### < 200 OK

```
< Content-Range: 0 -49/
< Accept-Range: 990
< [
{
"id": 22117 ,
"itemtype": "User",
"items_id": 2 ,
"itemtype_link": "Profile",
"linked_action": 17 ,
"user_name": "glpi (27)",
"date_mod": "2015-10-13 10:00:59",
"id_search_option": 0 ,
"old_value": "",
"new_value": "super-admin (4)"
}, {
"id": 22118 ,
"itemtype": "User",
"items_id": 2 ,
"itemtype_link": "",
"linked_action": 0 ,
"user_name": "glpi (2)",
"date_mod": "2015-10-13 10:01:22",
"id_search_option": 80 ,
"old_value": "Root entity (0)",
"new_value": "Root entity > my entity (1)"
}, {
...
}
]
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"items": [{"itemtype": "User", "items_id": 2}, {"itemtype":
"Entity", "items_id": 0}]}' \
'http://path/to/glpi/apirest.php/getMultipleItems?items\[0\]\
[itemtype\]\=User&items\[0\]\[items_id\]\=2&items\[1\]\
[itemtype\]\=Entity&items\[1\]\[items_id\]\=0'
```
#### < 200 OK

```
< Content-Range: 0 -49/
< Accept-Range: 990
< [{
"id": 2 ,
"name": "glpi",
...
}, {
"id": 0 ,
"name": "Root Entity",
...
}]
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/listSearchOptions/Computer'
< 200 OK
< {
"common": "Characteristics",
```
#### 1 : {

```
'name': 'Name'
'table': 'glpi_computers'
'field': 'name'
'linkfield': 'name'
'datatype': 'itemlink'
'uid': 'Computer.name'
},
2 : {
'name': 'ID'
'table': 'glpi_computers'
'field': 'id'
'linkfield': 'id'
'datatype': 'number'
'uid': 'Computer.id'
},
3 : {
'name': 'Location'
'table': 'glpi_locations'
'field': 'completename'
'linkfield': 'locations_id'
'datatype': 'dropdown'
'uid': 'Computer.Location.completename'
},
...
}
```
#### ...

```
"criteria":
[
{
"field": 1 ,
"searchtype": 'contains',
"value": ''
}, {
"link": 'AND',
"field": 31 ,
"searchtype": 'equals',
"value": 1
}, {
"link": 'AND',
"meta": true,
"itemtype": 'User',
"field": 1 ,
"searchtype": 'equals',
"value": 1
}, {
"link": 'AND',
"criteria" : [
{
"field": 34 ,
"searchtype": 'equals',
"value": 1
}, {
"link": 'OR',
"field": 35 ,
"searchtype": 'equals',
"value": 1
}
]
}
]
...
```
#### ...

```
"metacriteria":
[
{
"link": 'AND',
"itemtype": 'Monitor',
"field": 2 ,
"searchtype": 'contains',
"value": ''
}, {
"link": 'AND',
"itemtype": 'Monitor',
"field": 3 ,
"searchtype": 'contains',
"value": ''
}
]
...
```
#### {

```
"totalcount": ":numberofresults_without_pagination",
"range": ":start-:end",
"data": [
{
":searchoptions_id": "value",
...
},
{
...
}
],
"rawdata": {
...
}
}
```
```
curl -g -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/search/Monitor?\
criteria\[0\]\[link\]\=AND\
\&criteria\[0\]\[itemtype\]\=Monitor\
\&criteria\[0\]\[field\]\=23\
\&criteria\[0\]\[searchtype\]\=contains\
\&criteria\[0\]\[value\]\=GSM\
\&criteria\[1\]\[link\]\=AND\
\&criteria\[1\]\[itemtype\]\=Monitor\
\&criteria\[1\]\[field\]\=1\
\&criteria\[1\]\[searchtype\]\=contains\
\&criteria\[1\]\[value\]\=W2\
\&range\=0-2\&&forcedisplay\[0\]\=1'
```
#### < 200 OK

```
< Content-Range: 0 -2/
< Accept-Range: 990
< {"totalcount":2,"count":2,"data":{"11":{"1":"W2242","80":"Root
Entity","23":"GSM"},"7":{"1":"W2252","80":"Root
Entity","23":"GSM"}}}%
```
```
$ curl -X POST \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"input": {"name": "My single computer", "serial": "12345"}}' \
'http://path/to/glpi/apirest.php/Computer/'
```
#### < 201 OK

```
< Location: http://path/to/glpi/api/Computer/
< {"id": 15 }
```
```
$ curl -X POST \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"input": [{"name": "My first computer", "serial": "12345"},
{"name": "My 2nd computer", "serial": "67890"}, {"name": "My 3rd
computer", "serial": "qsd12sd"}]}' \
'http://path/to/glpi/apirest.php/Computer/'
```
#### < 207 OK

```
< Link:
http://path/to/glpi/api/Computer/8,http://path/to/glpi/api/Computer
/
< [ {"id":8, "message": ""}, {"id":false, "message": "You don't
have permission to perform this action."}, {"id":9, "message": ""}
]
```
```
$ curl -X PUT \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"input": {"otherserial": "xcvbn"}}' \
'http://path/to/glpi/apirest.php/Computer/10'
```
#### < 200 OK

```
[{"10":true, "message": ""}]
```
```
$ curl -X PUT \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"input": {"id": 11, "otherserial": "abcde"}}' \
'http://path/to/glpi/apirest.php/Computer/'
```
#### < 200 OK

```
[{"11":true, "message": ""}]
```
```
$ curl -X PUT \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"input": [{"id": 16, "otherserial": "abcde"}, {"id": 17,
"otherserial": "fghij"}]}' \
'http://path/to/glpi/apirest.php/Computer/'
```
#### < 207 OK

```
[{"8":true, "message": ""},{"2":false, "message": "Item not
found"}]
```
```
$ curl -X DELETE \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/Computer/16?force_purge=true'
```
#### < 200 OK

```
[{"16":true, "message": ""}]
```
```
$ curl -X DELETE \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"input": {"id": 11}, "force_purge": true}' \
'http://path/to/glpi/apirest.php/Computer/'
```
#### < 200 OK

```
[{"11":true, "message": ""}]
```
```
$ curl -X DELETE \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"input": [{"id": 16}, {"id": 17}]}' \
'http://path/to/glpi/apirest.php/Computer/'
```
#### < 207 OK

```
[{"16":true, "message": ""},{"17":false, "message": "Item not
found"}]
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getMassiveActions/Computer'
```
#### < 200 OK

#### [

#### {

```
"key": "MassiveAction:update",
"label": "Update"
},
{
"key": "MassiveAction:clone",
"label": "Clone"
},
{
"key": "Infocom:activate",
"label": "Enable the financial and administrative information"
},
{
"key": "MassiveAction:delete",
"label": "Put in trashbin"
},
{
"key": "Appliance:add_item",
"label": "Associate to an appliance"
},
{
"key": "Item_OperatingSystem:update",
"label": "Operating systems"
},
{
"key": "Computer_Item:add",
"label": "Connect"
},
{
"key": "Item_SoftwareVersion:add",
"label": "Install"
},
{
"key": "KnowbaseItem_Item:add",
"label": "Link knowledgebase article"
},
{
"key": "Document_Item:add",
"label": "Add a document"
},
{
"key": "Document_Item:remove",
"label": "Remove a document"
},
{
"key": "Contract_Item:add",
"label": "Add a contract"
},
{
"key": "Contract_Item:remove",
"label": "Remove a contract"
},
{
"key": "MassiveAction:amend_comment",
"label": "Amend comment"
},
{
"key": "MassiveAction:add_note",
"label": "Add note"
},
{
"key": "Lock:unlock",
"label": "Unlock components"
}
]
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getMassiveActions/Computer?
is_deleted=1'
```
#### < 200 OK

#### [

#### {

```
"key": "MassiveAction:purge_item_but_devices",
"label": "Delete permanently but keep devices"
},
{
"key": "MassiveAction:purge",
"label": "Delete permanently and remove devices"
},
{
"key": "MassiveAction:restore",
"label": "Restore"
},
{
"key": "Lock:unlock",
"label": "Unlock components"
}
]
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getMassiveActions/Computer/3'
```
#### < 200 OK

#### [

#### {

```
"key": "MassiveAction:update",
"label": "Update"
},
{
"key": "MassiveAction:clone",
"label": "Clone"
},
{
"key": "Infocom:activate",
"label": "Enable the financial and administrative information"
},
{
"key": "MassiveAction:delete",
"label": "Put in trashbin"
},
{
"key": "Appliance:add_item",
"label": "Associate to an appliance"
},
{
"key": "Item_OperatingSystem:update",
"label": "Operating systems"
},
{
"key": "Computer_Item:add",
"label": "Connect"
},
{
"key": "Item_SoftwareVersion:add",
"label": "Install"
},
{
"key": "KnowbaseItem_Item:add",
"label": "Link knowledgebase article"
},
{
"key": "Document_Item:add",
"label": "Add a document"
},
{
"key": "Document_Item:remove",
"label": "Remove a document"
},
{
"key": "Contract_Item:add",
"label": "Add a contract"
},
{
"key": "Contract_Item:remove",
"label": "Remove a contract"
},
{
"key": "MassiveAction:amend_comment",
"label": "Amend comment"
},
{
"key": "MassiveAction:add_note",
"label": "Add note"
},
{
"key": "Lock:unlock",
"label": "Unlock components"
}
]
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getMassiveActions/Computer/4'
```
#### < 200 OK

#### [

#### {

```
"key": "MassiveAction:purge_item_but_devices",
"label": "Delete permanently but keep devices"
},
{
"key": "MassiveAction:purge",
"label": "Delete permanently and remove devices"
},
{
"key": "MassiveAction:restore",
"label": "Restore"
},
{
"key": "Lock:unlock",
"label": "Unlock components"
}
]
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getMassiveActionParameters/Compute
r/MassiveAction:add_note'
```
#### < 200 OK

#### [

#### {

```
"name": "add_note",
"type": "text"
}
]
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/getMassiveActionParameters/Compute
r/Contract_Item:add'
```
#### < 200 OK

#### [

#### {

```
"name": "peer_contracts_id",
"type": "dropdown"
}
]
```
```
$ curl -X POST \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"ids": [2, 3], "input": {"amendment": "newtext"}}'
'http://path/to/glpi/apirest.php/applyMassiveAction/Computer/Massiv
eAction:amend_comment'
```
#### < 200 OK

#### {

```
"ok": 2 ,
"ko": 0 ,
"noright": 0 ,
"messages": []
}
```
```
$ curl -X POST \
-H 'Content-Type: multipart/form-data' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-F 'uploadManifest={"input": {"name": "Uploaded document",
"_filename" : ["file.txt"]}};type=application/json' \
-F 'filename[0]=@file.txt' \
'http://path/to/glpi/apirest.php/Document/'
```
#### < 201 OK

```
< Location: http://path/to/glpi/api/Document/
< {"id": 1 , "message": "Document move succeeded.", "upload_result":
{...}}
```
```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-H "Accept: application/octet-stream" \
-d '{"input": {"id": 11}}' \
'http://path/to/glpi/apirest.php/Document/'
```
#### < 200 OK

```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
-d '{"input": {"id": 11}}' \
'http://path/to/glpi/apirest.php/Document/&alt=media'
```
#### < 200 OK

```
$ curl -X GET \
-H 'Content-Type: application/json' \
-H "Session-Token: 83af7e620c83a50a18d3eac2f6ed05a3ca0bea62" \
-H "App-Token: f7g3csp8mgatg5ebc5elnazakw20i9fyev1qopya7" \
'http://path/to/glpi/apirest.php/User/2/Picture/'
```
#### < 200 OK

```
#<IfModule mod_rewrite.c>
# RewriteEngine On
# RewriteCond %{REQUEST_FILENAME} !-f
# RewriteCond %{REQUEST_FILENAME} !-d
# RewriteRule api/(.*)$ apirest.php/$
#</IfModule>
```
```
server {
listen 80 default_server;
listen [::]:80 default_server;
```
```
# change here to match your GLPI directory
root /var/www/html/glpi/;
```
```
index index.html index.htm index.nginx-debian.html index.php;
```
```
server_name localhost;
```
```
location / {
try_files $uri $uri/ =404;
autoindex on;
}
```
```
location /api {
rewrite ^/api/(.*)$ /apirest.php/$1 last;
}
```
```
location ~ [^/]\.php(/|$) {
fastcgi_pass unix:/run/php/php7.0-fpm.sock;
```
```
# regex to split $uri to $fastcgi_script_name and
$fastcgi_path
fastcgi_split_path_info ^(.+\.php)(/.+)$;
```
```
# Check that the PHP script exists before passing it
try_files $fastcgi_script_name =404;
```
```
# Bypass the fact that try_files resets $fastcgi_path_info
# # see: http://trac.nginx.org/nginx/ticket/
set $path_info $fastcgi_path_info;
fastcgi_param PATH_INFO $path_info;
```
```
fastcgi_param PATH_TRANSLATED
$document_root$fastcgi_script_name;
fastcgi_param SCRIPT_FILENAME
$document_root$fastcgi_script_name;
```
```
include fastcgi_params;
```
```
# allow directory index
fastcgi_index index.php;
}
}
```

