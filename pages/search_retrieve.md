---
layout: page
title: Search and Retrieval requests
permalink: search_retrieve.html
sidebar: nav_sidebar
last_updated: December, 2016
summary: A guide for accessing the APIs for searching and retrieval of records
---

### Overview

The search and retrieve APIs are not solution specific. The solution is still specified as part of the request. They fit into the **solution** portion of our request link.

`[server]/solutions/[solution]/[submission]`

Instead of being a single value, the solution for the query looks like this:

`/scores/query/insureright`

That means a sample search looks like this:

`https://insureright.valen.com/solutions/scores/query/insureright/scoring`

This particulary query is directed to the InsureRight solution, searching submitted scoring data.

### Building a Search Request

The query parameter of the search request can be blank, which will pull back a maximum of 1000 rows, or can be set to match any of the fields found in a given solution's data dictionary.

##### Request Requiremen6ts

##### Response Structure

### Test Plan

{:.tests}
|Test|Request|Expected Response|
|----|-------|-----------------|
|Retrieve Without Params|`curl --request GET --url 'https://insureright.valen.com/solutions/scores/query/insureright/scoring' –u [username]:[password] --header 'content-type: application/xml'`|200 OK: Response will be a list of scored records. Max is 1000 records long|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.retrieve_with_query }}">Retrieve With Query</a>|`curl --request GET --url 'https://insureright.valen.com/solutions/scores/query/insureright/scoring?element=insured%2Cpolicy_state_code%2Cvalue%2Ccontains%2CKY' –u [username]:[password] --header 'content-type: application/xml'`|200 OK: Response will be a list of scored records limited according to query param|
|Retrieve with Score ID|`curl --request GET --url 'https://insureright.valen.com/solutions/insureright/scoring/[scoreID]?format=pdf' –u [username]:[password] --header 'content-type: application/xml'`|200 OK: Record corresponding to score ID|