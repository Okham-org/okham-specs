# OPRC Examples (Non-Normative)

## Example 1: ticket_state (upsert state view)

```json
{
  "oprc": "0.0.1",
  "id": "ticket_state",
  "name": "Ticket State",
  "description": "Upserted state view of helpdesk tickets.",
  "status": "draft",
  "sources": [
    {
      "eventId": "helpdesk.ticket.created",
      "payloadType": "helpdesk.core.ticket"
    },
    {
      "eventId": "helpdesk.ticket.closed",
      "payloadType": "helpdesk.core.ticket"
    }
  ],
  "targets": [
    {
      "targetId": "ticket_state",
      "kind": "state",
      "mode": "upsert",
      "key": "/payload/id",
      "backend": "postgres-jsonb"
    }
  ],
  "mapping": {
    "rules": [
      {
        "targetId": "ticket_state",
        "targetPath": "status",
        "copy": "/payload/status"
      },
      {
        "targetId": "ticket_state",
        "targetPath": "category",
        "copy": "/payload/category"
      },
      {
        "targetId": "ticket_state",
        "targetPath": "updatedAt",
        "copy": "/payload/updatedAt"
      }
    ]
  }
}
```

## Example 2: ticket_timeline (append timeline)

```json
{
  "oprc": "0.0.1",
  "id": "ticket_timeline",
  "name": "Ticket Timeline",
  "description": "Append-only timeline of ticket events.",
  "status": "draft",
  "sources": [
    {
      "eventId": "helpdesk.ticket.created",
      "payloadType": "helpdesk.core.ticket"
    },
    {
      "eventId": "helpdesk.ticket.analyzed",
      "payloadType": "helpdesk.core.ticket"
    },
    {
      "eventId": "helpdesk.ticket.closed",
      "payloadType": "helpdesk.core.ticket"
    }
  ],
  "targets": [
    {
      "targetId": "ticket_timeline",
      "kind": "timeline",
      "mode": "append",
      "backend": "elasticsearch"
    }
  ],

  "mapping": {
    "rules": [
      {
        "targetId": "ticket_timeline",
        "targetPath": "eventId",
        "copy": "/event/id"
      },
      {
        "targetId": "ticket_timeline",
        "targetPath": "timestamp",
        "copy": "/event/timestamp"
      },
      {
        "targetId": "ticket_timeline",
        "targetPath": "ticketId",
        "copy": "/payload/id"
      },
      {
        "targetId": "ticket_timeline",
        "targetPath": "summary",
        "copy": "/payload/subject"
      }
    ]
  }
}
```
