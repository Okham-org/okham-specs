# ONC — Open Navigation Contract Examples

> **Non-Normative**: This document provides illustrative examples only.

## Minimal Example

```json
{
  "navigation": {
    "id": "helpdeskv2.navigation",
    "version": "0.2.0"
  },
  "screens": [
    {
      "id": "TicketList",
      "title": "Tickets",
      "componentRef": "helpdesk_ticket_list@0.1.0",
      "intent": "analyze"
    },
    {
      "id": "TicketDetail",
      "title": "Ticket Detail",
      "componentRef": "helpdesk_ticket_detail@0.1.0",
      "intent": "decide"
    }
  ],
  "routes": [
    { "path": "/tickets", "screenId": "TicketList" },
    {
      "path": "/tickets/:ticketId",
      "screenId": "TicketDetail",
      "params": [{ "name": "ticketId", "type": "helpdesk.ticket" }],
      "guards": ["policy.ticket.close.requires-approval"]
    }
  ],
  "nav": {
    "items": [
      { "id": "nav.tickets", "label": "Tickets", "route": "/tickets" }
    ]
  },
  "guards": [
    { "id": "policy.ticket.close.requires-approval", "policyRef": "policy.ticket.close.requires-approval" }
  ]
}
```
