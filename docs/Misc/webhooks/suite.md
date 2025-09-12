---
title: Test Suite
excerpt: This page contains information about events called in the entity Test Suite
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Created Test Suite

Payload example:

```json
{
  "event_name": "suite.created",
  "timestamp": 1650540917,
  "payload": {
    "id": 312,
    "title": "d",
    "description": null,
    "preconditions": null,
    "parent_id": null
  },
  "team_member_id": 40,
  "project_code": "ID"
}
```

> 📘 This event is being sent when a new test suite is created.

### Event name

`suite.created`

### Payload fields

| Parameter     | Type    | Description              |
| ------------- | ------- | ------------------------ |
| id            | integer | Test suite ID            |
| title         | string  | Test suite title         |
| description   | string  | Test suite description   |
| preconditions | string  | Test suite preconditions |
| parent\_id    | integer | ID of parent test suite  |

## Cloned Test Suite

Payload example:

```json
{
  "event_name": "suite.cloned",
  "timestamp": 1650541053,
  "payload": {
    "id": 314,
    "from_id": 13
  },
  "team_member_id": 40,
  "project_code": "ID"
}
```

> 📘 This event is being sent when a test suite is cloned.

### Event name

`suite.cloned`

### Payload fields

| Parameter | Type    | Description              |
| --------- | ------- | ------------------------ |
| id        | integer | Test suite ID (clone)    |
| from\_id  | integer | Test suite ID (original) |

## Deleted Test Suite

Payload example:

```json
{
  "event_name": "suite.deleted",
  "timestamp": 1650540989,
  "payload": {
    "id": 313
  },
  "team_member_id": 40,
  "project_code": "ID"
}
```

> 📘 This event is being sent when a test suite is deleted.

### Event name

`suite.deleted`

### Payload fields

| Parameter | Type    | Description   |
| --------- | ------- | ------------- |
| id        | integer | Test suite ID |

## Updated Test Suite

Payload example:

```json
{
  "event_name": "suite.updated",
  "timestamp": 1650540955,
  "payload": {
    "id": 11
  },
  "team_member_id": 40,
  "project_code": "ID"
}
```

> 📘 This event is being sent when a test suite is updated. It returns only ID of test suite and notifies that fields were updated.

### Event name

`suite.updated`

### Payload fields

| Parameter | Type    | Description   |
| --------- | ------- | ------------- |
| id        | integer | Test suite ID |
