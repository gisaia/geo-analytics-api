# Geo Analytics Collection API

The Geo Analytics Collection API let you change the collection references of the Geo Analytics service.

## URL Schema
The table below lists the URL endpoints.

| PATH Template                     | Description                                                          |
| --------------------------------- | -------------------------------------------------------------------- |
| /collections/               | List  the collections configured in the Geo Analytics service with the technical details |
| /collections/`{collection}` | Get, add or delete a collection reference in the Geo Analytics service                   |
| /collections/_export        | Export all collections as a JSON file                                |
| /collections/_import        | Import collections from a JSON file posted as a multipart parameter  |

## Managing collections

### /collections/

| Method     | Input Data                    | Output Data                            | Description                                                                        |
| ---------- | ----------------------------- | ---------------------------------------| ---------------------------------------------------------------------------------- |
| **GET**    | `None`                        | Array of `CollectionReference` as JSON | Return the full description of all the Collection Reference stored in the Geo Analytics service |


### /collections/{collection}

The following methods let you get, add and delete **collection references** from the Geo Analytics service.
A **collection reference** is the description of the way Geo Analytics service API will serve geospatial data.

| Method     | Input Data                    | Output Data                   | Description                                             |
| ---------- | ----------------------------- | ----------------------------- | ------------------------------------------------------- |
| **GET**    | `None`                        | `CollectionReference` as JSON | Return the full description of the Collection Reference |
| **PUT**    | `CollectionReference` as JSON | `CollectionReference` as JSON | Add or update a CollectionReference                     |
| **DELETE** | `None`                        | `None`                        | Delete a CollectionReference                            |

### /collections/_export

| Method     | Input Data                    | Output Data                                 | Description                                                                        |
| ---------- | ----------------------------- | ------------------------------------------- | ---------------------------------------------------------------------------------- |
| **GET**    | `None`                        | JSON array of `CollectionReference` as file | Return the full description of all the Collection Reference stored in the Geo Analytics service |

### /collections/_import

| Method     | Input Data                                                              | Output Data                            | Description                                                                                                                               |
| ---------- | ----------------------------------------------------------------------- | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| **POST**   | JSON array of `CollectionReference` as file posted in a multipart param | Array of `CollectionReference` as JSON | Return the full description of all the Collection Reference saved in the Geo Analytics service from this request (already stored collections will be updated) |

## Filtering

A coma-separated list of columns can be passed in request header `column-filter`. Wildcards are supported.

A column filter stands for the fields that are available to a request body:
- if a request body field doesn't belong to the column filter, a 403 is returned with the message `The field '%s' isn't available` or `The fields '%s' aren't available`;
- only fields that belong to the column filter can be returned.

A column filter can be related to a collection, e.g. `mycollection:myfield` or it can be related to every collection, e.g. `myfield`. Trying to access a collection with no available field returns a 403.
Collection names can be omitted or end with a '*'.

Examples of `column-filter`:

- `mycollection:params.city,mycollection:params.country` makes available `params.city` and `params.country` for collection `mycollection`
- `mycollection*:params.city` makes available `params.city` for all collections whose names start with `mycollection`
- `params`, `params*`, `params.*`, `*params` make available `params.city`, `params.country`, `params.weight`, and so on. for every collection
- `params`, `:params`, `*:params` make available `params` and subfields for every collection
- `*` makes all fields available
- `*.*` makes only subfields available, e.g. `params.city` and `params.country` but not `id`

If no column filter, or a blank column filter is provided, then no filtering is done.

The following endpoints use this header:

| Endpoint | Filtering result |
| --------------------------------------------------- | --------------------------------------------------- |
| /collections/               | Only allowed collections are listed |
| /collections/_export        | Only allowed collections are exported |
| /collections/_import        | Import is done only if the collection name is allowed |
