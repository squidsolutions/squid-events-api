# squid-events-api

The event-tracker API documentation.

## Event Model

This is a documentation of the event model. It's break-down in several sections coverring the different part of the model (schemas). Each schema provides a set of properties. The schemas can be combined to model rich type of events.

## Base model (EventModel)

Standard server-side properties common to every type of event

### SchemaName
* key: xx:schemaName
* method: EventModel.withSchemaName()
* type: String
* mandatory
* the name of the schema that event belongs to
* example: retrieval:pub_1.0

### EventType
* key: xx:eventType
* method: EventModel.withEventType()
* type: String
* mandatory
* the type of event; it is possible to have several different event types for the same schema
* example: display

### EventDate
* key: xx:eventDate
* method: EventModel.withEventDate()
* type: long
* mandatory
* the timestamp for the event, in GMT-0, format yyyy-MM-dd kk:mm:ss.SSS
* example:

### EventTimezone
* key: xx:eventTMZ
* method: EventModel.withEventTimeZone()
* type: string
* optional
* the Date timezone if the date is not GMT-0
* example:

### ServerIP
* key: xx:serverIP
* method: EventModel/withServerIP()
* type:string
* optional
* the server IP. Note that the Event Tracker server can set the value afterward.
* example:

## Usage model

