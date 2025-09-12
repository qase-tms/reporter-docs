---
title: Defect
excerpt: ''
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Created Defect

Payload example:

```json
{
  "event_name": "defect.created",
  "timestamp": 1650532563,
  "payload": {
    "id": 1,
    "title": "Example defect",
    "severity": "normal",
    "actual_result": "Something",
    "attachments": []
  },
  "team_member_id": 40,
  "project_code": "ID"
}
```

> 📘 This event is being sent when a new defect is created.

### Event name

`defect.created`

### Payload fields

| Parameter      | Type    | Description                  |
| -------------- | ------- | ---------------------------- |
| id             | integer | Defect ID                    |
| title          | string  | Defect title                 |
| severity       | string  | Level of severity            |
| actual\_result | string  | A text description of defect |
| attachments    | array   | An array of attachments ids. |

## Updated Defect

Payload example:

```json
{
  "event_name": "defect.updated",
  "timestamp": 1650532605,
  "payload": {
    "id": 1
  },
  "team_member_id": 40,
  "project_code": "ID"
}
```

> 📘 This event is being sent when a defect is updated.

### Event name

`defect.updated`

### Payload fields

| Parameter | Type    | Description |
| --------- | ------- | ----------- |
| id        | integer | Defect ID   |

## Deleted Defect

Payload example:

```json
{
  "event_name": "defect.deleted",
  "timestamp": 1650533058,
  "payload": {
    "id": 1
  },
  "team_member_id": 40,
  "project_code": "ID"
}
```

> 📘 This event is being sent when a defect is deleted.

### Event name

`defect.deleted`

### Payload fields

| Parameter | Type    | Description |
| --------- | ------- | ----------- |
| id        | integer | Defect ID   |

## Resolved Defect

Payload example:

```json
{
  "event_name": "defect.resolved",
  "timestamp": 1650533030,
  "payload": {
    "id": 1
  },
  "team_member_id": 40,
  "project_code": "ID"
}
```

> 📘 This event is being sent when a defect is resolved.

### Event name

`defect.resolved`

### Payload fields

| Parameter | Type    | Description |
| --------- | ------- | ----------- |
| id        | integer | Defect ID   |
