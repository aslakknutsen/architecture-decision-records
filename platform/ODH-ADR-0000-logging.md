# Open Data Hub - Cross platform structured logging

|                |            |
| -------------- | ---------- |
| Date           | 2024-02-08 |
| Scope          | Open Data Hub |
| Status         | Draft |
| Authors        | [name](@github-username) |
| Supersedes     | N/A |
| Superseded by: | N/A |
| Tickets        | |
| Other docs:    | none |

## What

Coherent minimal logging strategy across The Opendatahub Platform and it's components.

## Why

Improve log search usability across all components over the cluster(s). As more components are added the more "things" can happen. 
With a central logging infrastructure in place it will be much easier to reason about what is going on in the clusters if/when components
of the platform inform in a unified way. 

## Goals

* Usage of structured logging via json(to console log) to be aggregated by a central logging framework e.g. Openshift Logging 
* Unify important/bare minimal keys across components for easy search
* * what has happened to namespace x the last n days
* * which resources has been created in the namespace in the last n days

## Non-Goals

* A complete list of everything

## How

By updating the components of the Opendatahub Platform to log metadata in a unified way via structured logging to the console log we can deploy
a central logging system to capture and store the output which can later be used by system admins or developers to debug the systems overall behavior. 

### Proposed keys

#### General

| key                                | Meaning                                                            | Example         | Type   |
|------------------------------------|--------------------------------------------------------------------|-----------------|--------|
| msg                                | The core message to express. *note*: avoid adding duplicate metadata here| | |
| err                                | the error if any | | |
| request_id                         | entry point Request-ID from HTTP header or generated if not applicable or missing to "join" all log messages to a single 'execution | | |

#### Namespace related, inherit from General

| key                                | Meaning                                                            | Example         | Type   |
|------------------------------------|--------------------------------------------------------------------|-----------------|--------|
| namespace                          | the namespace that is involved in this action/error                |                 |        |


#### Resource related, inherit from Namespace if applicable(cluster scoped)

| key                                | Meaning                                                            | Example          | Type   |
|------------------------------------|--------------------------------------------------------------------|------------------|--------|
| resource_kind                      | the Kind of the Object                                             | InferenceService |        |
| resource_name                      | the Name of the object                                             |                  |        |
| resource_action                    | which action has been taken, aka verb                              | create|update    |        |

#### User related, inherit from General

| key                                | Meaning                                                            | Example         | Type   |
|------------------------------------|--------------------------------------------------------------------|-----------------|--------|
| user_id                            | a value to identify a User in some way, aka from Token             |                 |        |

#### Examples

##### User related action with error on namespace resource creation 

```json
{
    "request_id": "x-54080-32",
    "user_id": "aslak",
    "namespace": "kserve-demo",
    "resource_kind": "InferenceService",
    "resource_name": "my-first-model",
    "resource_action": "create",
    "msg": "failed to create resource",
    "err": "my-first-model already exist; caught /../ba.go:356... ... ... ",
}
```

##### A Controller reconciles a resource

```json
{
    "request_id": "x-54080-xx",
    "namespace": "kserve-demo",
    "resource_kind": "AuthConfig",
    "resource_name": "anonymous",
    "resource_action": "update",
    "msg": "resource updated",
}
```

## Open Questions

* How much can we influence upstream components?
* * Unify on logging style not framework
* * More of a runtime consern then a "framework" concern, might be hard sell to change upstream? 

## Alternatives

* Alternative to Structured json logging would be custom log parser format handlers in the central logging system. This would possible require custom formats pr 
components which are hard to maintain as there is no link between the outputer and the format parser. As well as the log lines them selves might miss the metadata
desribed here per line to make any choerent search.

## Security and Privacy Considerations

* Logging of sensitive data?
* * Un related to format and structure, but more general concern

## Risks

Optional section. Talk about any risks here.

## Stakeholder Impacts

| Group                         | Key Contacts     | Date       | Impacted? |
| ----------------------------- | ---------------- | ---------- | --------- |
| group or team name            | key contact name | date       | ? |


## References

* optional bulleted list

## Reviews

| Reviewed by                   | Date       | Notes |
| ----------------------------- | ---------  | ------|
| name                          | date       | ? |
