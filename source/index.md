---
title: Xendo API Reference

language_tabs:
  - shell
  - python
  - ruby

toc_footers:
  - <a href='http://info.xen.do/developers/api'>Sign Up for a Developer Key</a>
  - <a href='http://help.xen.do'>Xendo Help Center</a>

includes:
  - errors

search: true
---

# Xendo API Introduction

> API Endpoint

```shell
https://xen.do/api/v1/ 
```


Xendo's a hosted Enterprise Search service that provides unified search across 30+ cloud apps like Google Apps, Salesforce, Box, Dropbox, Asana, Microsoft Exchange and more (see the full list here https://xen.do/integrations).

The Xendo API enables you to build full-text search into your applications and workflows so you can provide contextual information to your users such a list of apps that contain the search term they're looking for or providing auto-complete as they're typing a name.

# Authentication

Xendo uses API keys to allow access to the API.  To get started, [request access to our developer portal](http://info.xen.do/developers/api).  After you've been setup as a Xendo developer, you can [create an OAuth2 client](https://xen.do/developer/oauth2apps/) which which enables you to securely access Xendo's API endpoints.

You'll need to take a note of the `CLIENT ID` and `CLIENT SECRET` associated with your OAuth2 client.

## Step 1 - Authorization

Your authenticating web application should redirect to the following URL:

### Redirect URL

`https://xen.do/oauth2/authorize`

### GET Parameters

Parameter | Default | Description
--------- | ------- | -----------
response_type | code | Return an authorization code.
client_id | None | <Your Client ID>
redirect_uri | None | <Your Redirect URI>

<aside class="success">
You application will redirect to the `redirect_uri` with the authorization code. 
</aside>

## Step 2 - Obtain Access Token

Your application needs to submit a request to the following URL:

### HTTP Request

`POST https://xen.do/oauth2/access_token` 

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
grant_type | authorization_code | Request an authorization code.
client_id | None | <Your Client ID>
client_secret | None | <Your Client ID>
code | None | <Your Authorization Code from Step 1>
redirect_uri | None | <Your Redirect URI>


## Step 3 - Refreshing an Access Token

> The Authorization Bearer header must be passed with every API request.  For example:

```shell
curl -X GET "https://xen.do/api/v1/search/" 
    -H "Authorization: Bearer 17126fbae658371b7c0eeda04b2cfb3b57f4cb60" 
```

> Make sure to replace the Bearer token with your access token.


The Access Token is short lived and will expire. You'll need to use the Refresh Token to obtain a new Access Token:

### HTTP Request

`POST https://xen.do/oauth2/access_token` 

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
grant_type | refresh_token | Request an authorization code.
client_id | None | <Your Client ID>
client_secret | None | <Your Client ID>
refresh_token | None | <Your Refresh Token from Step 2>

<aside class="success">
The response will include a new access token and refresh token. 
</aside>

Once you have an access token, you can call Xendo's API endpoints (same as before) with an Authorization Bearer header.

# Search

## Search

> For example, searching for 'Winston Churchill' would look like this:

```shell
curl -X GET "https://xen.do/api/v1/search/?q=winston%20churchill"
    -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" 
```

> The above command returns JSON structured like this:

```json
{
    "response": {
        "start": 0, 
        "maxScore": 12.350116, 
        "numFound": 373, 
        "docs": [
            {
                "utc_xendo_sort_timestamp": "2015-07-02T20:58:34Z", 
                "description": "Winston Churchill and DevOps help scale enterprise security", 
                "author": "mike d. kail", 
                "url": "http://blog.conjur.net/winston-churchill-security-and-devops", 
                "imageUrl": "https://media.licdn.com/mpr/mprx/0_Z3_74LxSHKBPY8MGTlchRvjp5f", 
                "title": "Winston Churchill, Security, and DevOps", 
                "profileUrl": "https://www.linkedin.com/profile/view?id=13156", 
                "source": "linkedin", 
                "last_modified": "1435870714153", 
                "content_type": "message", 
                "score": 12.350116, 
                "source_content_type": "share", 
                "storm_user_id": "8", 
                "service_id": "12944", 
                "id": "12944-s6022478279817060352"
            }, 
            {
                "utc_xendo_sort_timestamp": "2015-08-06T04:56:47Z", 
                "score": 1.326375, 
                "author": "SeanEllis", 
                "url": "https://twitter.com/SeanEllis/status/629154126358667264", 
                "imageUrl": "https://pbs.twimg.com/profile_images/601169cxPsS_normal.png", 
                "profileUrl": "https://twitter.com/SeanEllis", 
                "source": "twitter", 
                "last_modified": "Thu Aug 06 04:56:47 UTC 2015", 
                "content_type": "message", 
                "message": "If you're going through hell, keep going. - Winston Churchill", 
                "source_content_type": "tweet", 
                "storm_user_id": "8", 
                "service_id": "350", 
                "id": "350-629154126358667264"
            }, 
            {
                "utc_xendo_sort_timestamp": "2015-08-03T19:05:08Z", 
                "score": 1.3218337, 
                "author": "briancarter", 
                "url": "https://twitter.com/briancarter/status/628280455117316096", 
                "imageUrl": "https://pbs.twimg.com/profile_images/KvxdX8oX_normal.jpeg", 
                "profileUrl": "https://twitter.com/briancarter", 
                "source": "twitter", 
                "last_modified": "Mon Aug 03 19:05:08 UTC 2015", 
                "content_type": "message", 
                "message": "Success is not final, failure is not fatal: it is the courage to continue that counts. - Winston Churchill", 
                "source_content_type": "tweet", 
                "storm_user_id": "8", 
                "service_id": "350", 
                "id": "350-628280455117316096"
            }, 
        ]
    }
}
```

This endpoint enables an authenticated user to search across their content.

### HTTP Request

`GET https://xen.do/api/v1/search/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
q | None | Search query.
sources | None | Comma-separated list of sources.  Supported types include `salesforce`, `gmail`, `box`, `dropbox`.
content_types | None | Comma-separated list of content types.  Supported types include `document`, `spreadsheet`, `contact`.
dates | None | Restrict the results to items within the specified date range expressed as `dates=<date_from>,<date_to>`.  Dates should be formatted `YYYY-MM-DDThh:mm:ssZ`.  Searching between a date range would look this `/api/v1/search/?q=*&dates=2015-10-20T23:00:00Z,2015-11-11T23:00:00Z`. Alternatively, the 'dates' parameter can accept a descriptive value: `NOW`, `MINUTES`, `HOURS`, `DAYS`, `MONTHS`, `YEARS` which can be used `/api/v1/search/?q=*&dates=n<date unit>`.  As an example, searching for content updates within the past 3 hours would be `/api/v1/search/?q=*&dates=3HOURS`. 
tags | None | Comma-separated list of tags which will be used to filter search results.
account | None | Restrict a search to a specific account.  Typically used when a user has multiple accounts in the same service, for example, multiple Gmail accounts or multiple Slack teams.
sort | By relevance | Override the default sort order.  Valid sort orders include: `date`, `author`, `name`, `title`.`subject`.  Ascending `:asc` or Descending `:desc` must be specified, for example: `sort=date:asc`.
page_size | 20 | Number of results to return per page.
p | 0 | Page of results.


## Facets

> For example, finding the Services and Content types that contain the term 'Winston Churchill' would look like this:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl -X GET "https://xen.do/api/v1/facets/?q=winston%20churchill" -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" 
```

> The above command returns JSON structured like this:

```json
{
  "response":
    {"numFound":6,"start":0,"docs":[]},
    "facet_counts": {
      "facet_queries":{},
      "facet_fields":{
        "source":["twitter",4,"googlecontacts",2],
        "content_type":["message",4,"contact",2]
      },
      "facet_dates":{},
      "facet_ranges":{},
      "facet_intervals":{},
      "facet_heatmaps":{}
    }
}
```

This endpoint enables search across all connected service accounts.

### HTTP Request

`GET https://xen.do/api/v1/facets/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
q | None | Search query.
facets | None | Comma-separated list of facets.  Supported types include `source`, `content_type`.
sources | None | Comma-separated list of sources.  Supported types include `salesforce`, `gmail`, `box`, `dropbox`.
content_types | None | Comma-separated list of content types.  Supported types include `document`, `spreadsheet`, `contact`.
dates | None | Restrict the results to items within the specified date range expressed as `dates=<date_from>,<date_to>`.  Dates should be formatted `YYYY-MM-DDThh:mm:ssZ`.  Searching between a date range would look this `/api/v1/search/?q=*&dates=2015-10-20T23:00:00Z,2015-11-11T23:00:00Z`. Alternatively, the 'dates' parameter can accept a descriptive value: `NOW`, `MINUTES`, `HOURS`, `DAYS`, `MONTHS`, `YEARS` which can be used `/api/v1/search/?q=*&dates=n<date unit>`.  As an example, searching for content updates within the past 3 hours would be `/api/v1/search/?q=*&dates=3HOURS`. 
facet_dates | None | If this parameter is specified, matching counds for the following date ranges will be returned: `1DAY`, `7DAYS`, `14DAYS`, `1MONTH`, `3MONTHS`, `6MONTHS`.


## Autosuggest

> List services and content types that contain the partial term 'winst':

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl -X GET "https://xen.do/api/v1/autosuggest/?q=winst" -H "Authorization: Bearer 17126fbae736771b7c0eeda04b2cfb3b57f4cb60" 
```

> The above command returns JSON structured like this:

```json
{
  "response": {
    "numFound":45,"start":0,"docs":[]
  },
  "facet_counts":{
    "facet_queries":{},
    "facet_fields":{
      "content_type":["message",23,"document",14,"contact",8],
      "source":["twitter",23,"github",11,"googlecontacts",8,"dropbox",3]
    },
    "facet_dates":{},
    "facet_ranges":{},
    "facet_intervals":{},
    "facet_heatmaps":{}
  }
}
```

This endpoint provides lists of sources and content types that contain the partial search term.  The list of facets can be used to populate a dropdown or picklist to help users refine their search, as they type.

### HTTP Request

`GET https://xen.do/api/v1/autosuggest/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
q | None | Search query.
sources | None | Comma-separated list of sources.  Supported types include `salesforce`, `gmail`, `box`, `dropbox`.
content_types | None | Comma-separated list of content types.  Supported types include `document`, `spreadsheet`, `contact`.



## Autocomplete

> Get suggestions for the term 'winston':

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl -X GET "https://xen.do/api/v1/typeahead-suggest/?q=winston" -H "Authorization: Bearer 17126fbae755871b7c0eeda04b2cfb3b57f4cb60" 
```

> The above command returns JSON structured like this:

```json
{
  "response":
  {
    "start":0,
    "maxScore":11.757046,
    "numFound":2,
    "docs":[
      {"title":"Winston Churchill, Security, and DevOps"},
      {"name":"Johnny Winston"}
    ]
  }
}
```

This endpoint provides a list of document titles and/or names that contain text matching the query parameter.  This endpoint is designed to be high performance in order to provide users with timely suggestions about likely matches for the term they're searching, as they type.

### HTTP Request

`GET https://xen.do/api/v1/typeahead-suggest/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
q | None | Search query or partical search query.




## Spelling

> Get suggested spelling corrections for 'winstin churchill':

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl -X GET "https://xen.do/api/v1/spell/?q=winstin churchill" -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" 
```

> The above command returns JSON structured like this:

```json
[
  {
    "hits":1,
    "collationQuery":"winston churchill",
    "misspellingsAndCorrections":["winstin","winston","churchill","churchill"]
  }
]
```

This endpoint provides spelling correction suggestions based on the authenticated user's document corpus.  This capability can be used to provide 'Did you mean' capabilities to help users find what they intended.

### HTTP Request

`GET https://xen.do/api/v1/spell/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
q | None | Search query or partical search query.




# Views

## List User Views

> Return a list of the authenticated user's views:

```shell
curl -X GET "https://xen.do/api/v1/user-view/"
    -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" 
```

> The above command returns JSON structured like this:

```json
{
  "meta": {
    "limit": 75,
    "next": null,
    "offset": 0,
    "previous": null,
    "total_count": 2
  },
  "objects": [
    {
      "created": "Tue, 26 May 2015 22:58:20 -0500",
      "description": "Most recent updates from across your accounts",
      "facets": "[]",
      "id": 448,
      "modified": "Tue, 22 Dec 2015 23:58:36 -0600",
      "ordering": 0,
      "query": "recent:content",
      "resource_uri": "/api/v1/user-view/448/",
      "short_label": "Recent updates",
      "sort_fields": "",
      "user": "/api/v1/user/22/"
    },
    {
      "created": "Wed, 25 Nov 2015 15:33:48 -0600",
      "description": "Results for 'Project X'",
      "facets": "[]",
      "id": 516,
      "modified": "Wed, 23 Dec 2015 13:33:02 -0600",
      "ordering": 1,
      "query": "project x",
      "resource_uri": "/api/v1/user-view/516/",
      "short_label": "Project X tracking",
      "sort_fields": "",
      "user": "/api/v1/user/22/"
    },
  ]
}
```

This endpoint enables an authenticated user to set and retrieve their saved views.  'Views' provide an intuitive way for users to encapsulate complex search queries with a memorable and meaningful label.  Using 'views', a user may search their content with one click rather than having to compose a search query.  'Views' are the foundation for alerts which may be configured to notify users about new content that matches a 'view'.

### HTTP Request

`GET https://xen.do/api/v1/user-view/`



## Get a specific View

> Return a specific view:

```shell
curl -X GET "https://xen.do/api/v1/user-view/448/"
    -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" 
```

> The above command returns JSON structured like this:

```json
{
  "id": 448,
  "created": "Tue, 26 May 2015 22:58:20 -0500",
  "description": "Most recent updates from across your accounts",
  "facets": "[]",
  "modified": "Tue, 22 Dec 2015 23:58:36 -0600",
  "ordering": 0,
  "query": "recent:content",
  "resource_uri": "/api/v1/user-view/448/",
  "short_label": "hello",
  "sort_fields": "",
  "user": "/api/v1/user/22/"
}
```

This endpoint enables an authenticated user to create, read, update and delete a 'view'.

### HTTP Request

`GET https://xen.do/api/v1/user-view/<ID>/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
ID | None | ID of the view.


# Services

## List User Services

> Retrieve a list of services for an authenticated user.  Service descriptions include display names and current indexing state:

```shell
curl -X GET "https://xen.do/api/v1/user-service/"
    -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" 
```

> The above command returns JSON structured like this:

```json
{
  "meta": {
    "limit": 75,
    "next": null,
    "offset": 0,
    "previous": null,
    "total_count": 9
  },
  "objects": [
    {
      "created": "Mon, 18 May 2015 15:01:34 -0500",
      "display_name": "julian",
      "exclude": null,
      "icon_url": "/static/images/services/icons/slack.png",
      "id": 259,
      "initial_indexing_progress": null,
      "items_total": 0,
      "last_indexed": null,
      "logo_url": "/static/images/services/slack.png",
      "next_indexing": "Mon, 18 May 2015 15:01:34 -0500",
      "notify_on_completion": false,
      "ordering": 7,
      "pretty_name": "Slack",
      "private_service": false,
      "resource_uri": "/api/v1/user-service/259/",
      "service_name": "slack",
      "source_connection_status": null,
      "status": "indexing",
      "token_expiration_date": null
    },
    {
      "created": "Fri, 1 May 2015 18:29:38 -0500",
      "display_name": "Julian Gay",
      "exclude": null,
      "icon_url": "/static/images/services/box.png",
      "id": 249,
      "initial_indexing_progress": null,
      "items_total": 40,
      "last_indexed": "Tue, 13 Oct 2015 20:47:12 -0500",
      "logo_url": "/static/images/services/box.png",
      "next_indexing": "Tue, 13 Oct 2015 21:07:12 -0500",
      "notify_on_completion": false,
      "ordering": 3,
      "pretty_name": "Box",
      "private_service": false,
      "resource_uri": "/api/v1/user-service/249/",
      "service_name": "box",
      "source_connection_status": "('Connection aborted.', 'nodename not provided, or not known')",
      "status": "indexing",
      "token_expiration_date": null
    },
    {
      "created": "Sat, 10 Oct 2015 23:22:04 -0500",
      "display_name": "@juliangay",
      "exclude": null,
      "icon_url": "/static/images/services/icons/twitter.png",
      "id": 274,
      "initial_indexing_progress": null,
      "items_total": 21201,
      "last_indexed": "Fri, 23 Oct 2015 05:02:00 -0500",
      "logo_url": "/static/images/services/twitter.png",
      "next_indexing": "Fri, 23 Oct 2015 05:22:00 -0500",
      "notify_on_completion": false,
      "ordering": 1,
      "pretty_name": "Twitter",
      "private_service": false,
      "resource_uri": "/api/v1/user-service/274/",
      "service_name": "twitter",
      "source_connection_status": null,
      "status": "indexing",
      "token_expiration_date": null
    },
   ]
}
```

This endpoint enables an authenticated user to list, create, update and delete their connected services.

### HTTP Request

`GET https://xen.do/api/v1/user-service/`



## Get a specific Service description

> Retrieve a specific service description and status for an authenticated user.  Service descriptions include display names and current indexing state:

```shell
curl -X GET "https://xen.do/api/v1/user-service/274/"
    -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" 
```

> The above command returns JSON structured like this:

```json
{
  "id": 274, 
  "service_name": "twitter", 
  "pretty_name": "Twitter", 
  "display_name": "@juliangay", 
  "icon_url": "/static/images/services/icons/twitter.png", 
  "created": "Sat, 10 Oct 2015 23:22:04 -0500", 
  "last_indexed": "Fri, 23 Oct 2015 05:02:00 -0500", 
  "items_total": 21201, 
  "exclude": null, 
  "initial_indexing_progress": null, 
  "logo_url": "/static/images/services/twitter.png", 
  "notify_on_completion": false, 
  "ordering": 1, 
  "private_service": false, 
  "resource_uri": "/api/v1/user-service/274/", 
  "source_connection_status": null, 
  "status": "indexing", 
  "next_indexing": "Fri, 23 Oct 2015 05:22:00 -0500", 
  "token_expiration_date": null
}
```

This endpoint enables an authenticated user to read, create, update and delete a connected service.

### HTTP Request

`GET https://xen.do/api/v1/user-service/<ID>/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
ID | None | The unique service ID of the user's connected account.


# Provisioning

## Users

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl -H "Authorization: Bearer 17126fbae733871b7c0eeda04b2cfb3b57f4cb60" -X GET https://xen.do/api/v1/search/?q=winston%20churchill
```

> The above command returns JSON structured like this:

```json
{
    "response": {
        "start": 0, 
        "maxScore": 12.350116, 
        "numFound": 373, 
        "docs": [
            {
                "utc_xendo_sort_timestamp": "2015-07-02T20:58:34Z", 
                "description": "Winston Churchill and DevOps help scale enterprise security", 
                "author": "mike d. kail", 
                "url": "http://blog.conjur.net/winston-churchill-security-and-devops", 
                "imageUrl": "https://media.licdn.com/mpr/mprx/0_Z3_74LxSHKBPY8MGTlchRvjp5f", 
                "title": "Winston Churchill, Security, and DevOps", 
                "profileUrl": "https://www.linkedin.com/profile/view?id=13156", 
                "source": "linkedin", 
                "last_modified": "1435870714153", 
                "content_type": "message", 
                "score": 12.350116, 
                "source_content_type": "share", 
                "storm_user_id": "8", 
                "id": "12944-s6022478279817060352"
            }, 
            {
                "utc_xendo_sort_timestamp": "2015-08-06T04:56:47Z", 
                "score": 1.326375, 
                "author": "SeanEllis", 
                "url": "https://twitter.com/SeanEllis/status/629154126358667264", 
                "imageUrl": "https://pbs.twimg.com/profile_images/601169cxPsS_normal.png", 
                "profileUrl": "https://twitter.com/SeanEllis", 
                "source": "twitter", 
                "last_modified": "Thu Aug 06 04:56:47 UTC 2015", 
                "content_type": "message", 
                "message": "If you're going through hell, keep going. - Winston Churchill", 
                "source_content_type": "tweet", 
                "storm_user_id": "8", 
                "id": "350-629154126358667264"
            }, 
            {
                "utc_xendo_sort_timestamp": "2015-08-03T19:05:08Z", 
                "score": 1.3218337, 
                "author": "briancarter", 
                "url": "https://twitter.com/briancarter/status/628280455117316096", 
                "imageUrl": "https://pbs.twimg.com/profile_images/KvxdX8oX_normal.jpeg", 
                "profileUrl": "https://twitter.com/briancarter", 
                "source": "twitter", 
                "last_modified": "Mon Aug 03 19:05:08 UTC 2015", 
                "content_type": "message", 
                "message": "Success is not final, failure is not fatal: it is the courage to continue that counts. - Winston Churchill", 
                "source_content_type": "tweet", 
                "storm_user_id": "8", 
                "id": "350-628280455117316096"
            }, 
        ]
    }
}
```

This endpoint enables search across all connected service accounts.

### HTTP Request

`GET https://xen.do/api/v1/search/`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
q | None | Search query.
sources | None | Comma-separated list of source services eg. salesforce,box,gmail,googledrive.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>