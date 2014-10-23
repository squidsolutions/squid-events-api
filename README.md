# squid-events-api

This is the event-tracker API documentation.
It presents the supported event types, and the event model used to describe them.

If you are looking to implement event-tracking in your application, consider using a SDK for your plateform:
* for Java, Scala: https://github.com/squidsolutions/squid-events-client-java
* for .NET: https://github.com/squidsolutions/squid-events-client-dotnet
* for Ruby: 
* for PHP: 

## Event Types

The API defines 3 types of events

### Session Event

This event is created when a new session is created by the application. It allows to define the session origine, the browser type, and also to assign a unique browser ID.

This event includes properties from the following models:
* Session Model
* Account Model
* Usage Model

Following is a example of creating a Session event based on the Session model (in Java code):
```
// create the new session event
EventModel event = new StartSessionEvent()
    .withReferrerURL("https://google.com")
    .withUserAgent("chrome")
    .withAccountID("university")
    .withAuthenticationMethod("IP")
    .withSessionID("123")
    .withPageViewURL("http://myapp/landingpage")
    .withUserID("abc");
```

### Search Event

This event is created when a user performs a search in the application. It allows to define the search parameter and also to collect information regarding the search results.

This event includes properties from the following models:
* Search Model
* Account Model
* Usage Model

Following is a example of creating a Search event based on the Search model (in Java code):
```
// create the search event
EventModel searchEvent = new SearchEvent()
    .withEngine("quick")
    .withFilters("format=pdf")
    .withResultCount(100)
    .withResultID("x1234")
    .withResultPage(1)
    .withTerms("some search terms")
    .withSessionID("123")
    .withUserID("abc");
```


### Retrieval Event

This event is created when a user requests an article. It allows to define the article & display  properties, the entitlement rule and account owner.

This event includes properties from the following models:
* Retrieval Model
* Account Model
* Usage Model

It can also include reference to Article model.

Following is a example of creating a Retrieval event based on the Retrieval model and Article Model (in Java code):
```
// first create an Article model with the displayed article properties
ArticleModel article = new ArticleModel()
	.withContentType("article")
	.withDiscipline("science")
	.withJournal("nature")
// more properties here...
;
// then create the retrieval event
EventModel event = new RetrievalEvent()
	.withContentOwnerID("myUniversity")
	.withContentType("article")
	.withDisplayFormat("PDF")
	.withEntitlement("myUnivertsity")
	.withContentReferenceArticle(article)// reference the article
	.withClientIP("127.0.0.1")
	.withSessionID("1234")
	.withUserID("sergio")
	.withPageViewURL("http://myapp/contentPage")
	.withServerIP("127.0.0.1");
```

## Event Model

This is a documentation of the event model. 
It's break-down in several sections coverring the different part of the model (schemas). 
Each schema provides a set of properties. 
The schemas are organized in a simple hierarchy to combine properties.

![Event model hierarchy](event-hierarchy.png?raw=true "Event Model Hierarchy")

### Base model

Standard server-side properties common to every type of event

#### SchemaName
* key: xx:schemaName
* method: EventModel.withSchemaName()
* type: String
* mandatory
* the name of the schema that event belongs to
* example: retrieval:pub_1.0

#### EventType
* key: xx:eventType
* method: EventModel.withEventType()
* type: String
* optional
* the type of event; it is possible to have several different event types for the same schema
* example: display

#### EventDate
* key: xx:eventDate
* method: EventModel.withEventDate()
* type: long
* mandatory
* the timestamp for the event, in GMT-0, format yyyy-MM-dd kk:mm:ss.SSS
* example:

#### EventTimezone
* key: xx:eventTMZ
* method: EventModel.withEventTimeZone()
* type: string
* optional
* the Date timezone if the date is not GMT-0
* example:

#### ServerIP
* key: xx:serverIP
* method: EventModel/withServerIP()
* type:string
* optional
* the server IP. Note that the Event Tracker server can set the value afterward.
* example:

### Usage model

Standard usage properties common to every event related to tracking user behavior.
It extends from the base model.

#### SessionID
* key: ux:sessionID
* methos: withSessionID()
* string(256)
* optional
* this is a persistent ID with session scope, that can be used to group events in the same session. It can be a server-side session identification, or client-side cookie
* example:

#### TransactionID
* key: ux:transactionID
* method: withTransactionID()
* string(256)
* optional
* this is a persistent ID that can span multiple application server. It can be used to correlate events submitted from different systems but used to fulfil the same user request
* example: 

#### UserID
* key: ux:userID
* method: withUserID()
* string(256)
* optional
* this is an internal ID that possibly identify uniquely the user
* example: if the user can sign-in the application, that can be it’s user ID

#### ClientIP:
* key: ux:clientIP
* method: withClientIP()
* string(100)
* optional 
* Client’s IP (V4 or V6)
* example: 

#### ReferrerURL:
* key: ss:referrer
* method: withReferrerURL()
* string(4000)
* optional
* this is the full referrer’s URL (domain, path, query string + anchors ...) requested
* example: 

#### pageViewURL:
* key: ux:pageViewURL
* method: withPageViewURL()
* string(4000)
* mandatory
* this is the full URL (domain, path, query string + anchors ...) requested
* example: 

#### HttpReturnCode:
* key: ux:httpReturnCode
* method: withHttpReturnCode()
* smallint
* optional
* HTTP return code send back by the server to the client
* example: 

#### ErrorCode:
* key: ux:errorCode
* method: withErrorCode()
* string(100)
* optional
* Any error text that would be interesting to report, send back to the client from the server
* example: 

### Account Model

Properties allowing to identify the institution Account that the user belongs to.

It inhrits from:
 * Usage model

#### AccountID
* key: pub:accountID
* method: withAccountID()
* string(256)
* mandatory
* this is an internal ID of the institution account. It must be a valid reference from the meta-data.
* example:

#### AuthenticationMethod
* key: pub:authMethod
* method: withAuthenticationMethod()
* string(50)
* optional
* this is a reference code that define how the user gain access to the Account
* example: it can be IP based authentication, guest account, sign-in...

### Session Model

Properties to track session level events. 
It extends from the Account model.

#### BrowserID
* key: ss:browserID
* method: withBrowserID()
* string(256)
* optional
* this is a persistent ID associated with the browser (or rich application) generating the event on the client side. Usually it will be retrieve from a cookie
* example:

#### UserAgent:
* key: ss:userAgent
* method: withUserAgent()
* string(4000)
* optional
* UserAgent string send back by the client
* example: 

### Search Model

Properties to track search events.
It extends from the Account model.

#### SearchTerms:
* key: sx:terms
* method: withTerms()
* string(65000)
* mandatory
* Search term entered by the user, 
* example: 

#### SearchFilters:
* key: sx:filters
* method: withFilters()
* string(65000) - List of key/values 
* optional
* Any set of key/value pairs that identify a filter & options (multiple choice supported) selected to filter the search results
* example: 

#### SearchEngine:
* key: sx:engine
* method: withEngine()
* string(100)
* optional
* Type of search engine used to resolve the search 
* example: quick/basic, advanced, ...

#### SearchResultCount:
* key: sx:resultCount
* method: withResultCount()
* Recall size, number of results
* optional
* Total number of search results
* example: 

#### SearchResultID
* key: sx:resultID
* method: withResultID()
* string(256)
* optional
* UUID associated with the original search event. Can be used to link event initiated from the search result page, for example going to next page or reference retrieval.
* example:

#### SearchResultPage:
* key: sx:resultPage
* method: withResultPage()
* smallint
* optional
* Page number currently displayed. Should be 1 for the first result page, then 2 if the user click next. You can use the SearchResultID to group several pages within the same search
* example: 

### Retrieval Model

Properties to track retrieval events.
It extends from the Account model.

#### SearchOriginID
* key: rt:searchOriginID
* method: withSearchOriginID()
* string(256)
* optional
* identify the search that leads to that display, see SearchresultID
* example:

#### ContentReferenceID
* key: rt:contentRefID
* method: withContentReferenceID()
* string(256)
* optional
* the reference of the content. Must be a valid reference in the meta-data source.
* example: ID of an image, video, article, journal…

#### ContentReferenceArticle
* key: rt:contentRefArticle
* method: withContentReferenceArticle()
* Object - Article Model  
* optional
* if the content is not referenced by an ID, provide a Article model object with actual definition
* example:

#### ContentType
* key: rt:contentType
* method: withContentType()
* String(256)
* optional
* the content type of the artifact retrieved
* example: image, video, article, journal…

#### DisplayFormat:
* key: rt:displayFormat
* method: withDisplayFormat()
* string(32)
* optional
* the display format value
* example: JPEG, HTML, ABSTRACT, PDF

#### ContentOwnerID:
* key: rt:contentOwner
* method: withContentOwnerID()
* string(256)
* optional
* the account ID that owns the content. It must be a valid reference in the meta-data source.
* example:

#### Entitlement
* key: rt:contentEntitlement
* method: withEntitlement()
* string(256)
* optional
* define the entitlement for that account/display
* example

### Article Model

Properties allowing to extensively describe an article, when not able to reference it by ID.

#### ContentType
* key: art:contentType
* method: withContentType()
* string(256)
* optional
* type of content that has been retrieved.
* example: Dissertation, Newspapers, Book...

#### Language
* key: art:language
* method: withLanguage()
* string(64)
* optional
* International code of the language in which the content is made.
* Example: Swahili

#### Discipline
* key: art:discipline
* method: withDiscipline()
* string(256)
* optional
* Academic subject matter the content relates to
* Example: mechanical engineering

#### ReferenceSource
* key: art:source
* method: withReferenceSource()
* string(256)
* optional
* Name of the source from which the reference was imported
* Examples: Google Scholar, Pubmed

#### ReferenceSourceType
* key: art:sourceType
* method: withReferenceSourceType()
* string(256):
* optional
* Name of category of grouping of reference sources
* example:

#### PublicationTitle
* key: art:pubTitle
* method: withPublicationTitle()
* string(1000)
* optional
* Name of the journal the article was published in
* Example: International Journal of Sexuality and Gender Studies

#### ArticleDBID
* key: art:dbid
* method: withDBID()
* string(64)
* optional
* Identification of the database the journal belongs to
* Example: ABI Inform

#### ArticleISSN:
* key: art:issn
* method: withISSN()
* string(64)
* optional
* When applicable: ISSN if the article is published in a periodical
* Example:

#### ArticleISBN:
* key: art:isbn
* method: withISBN()
* string(64)
* optional
* When applicable: ISBN if the article is published in a book
* Example:

#### ArticleDOI:
* key: art:doi
* method: withDOI()
* string(64)
* optional
* When applicable: Digital Object Identifier of the article
* example:

#### ArticleCustomID
* key: art:customID
* method: withCustomID()
* string(64)
* optional
* Internal ID of the article in your database
* example:




