---
layout: page
title: Batch and Profiling requests
permalink: batch_request.html
sidebar: nav_sidebar
last_updated: December, 2016
summary: A guide for building requests take advantage of Valen's batch processing APIs
---

### Overview
The batch API allows integrations to process batch files containing many rows of data instead of running each individual record. 

The batch API accepts a zip file as a payload. What the zip file contains depends for which solution the integration is written. _need some sort of information here_


### Building a Batch Request

Batch and Profile requests are POSTs using multi-part form data.

Constructing the URI is similar to the scoring URI with a few key differences.

First we start with our base URL, `https://insureright.valen.com/solutions/`. We append the action, version, and solution, for example: `batch/5/ca` or `batch/5/insureright`

A sample batch request might look like this:

`https://insureright.valen.com/solutions/batch/5/insureright/scoring`


*need bit about creating a GUID and a subscription to get profile/batch finished events. POST can contain GUID to allow that to happen.*

*Also need an example of submitting a profiled batch*

##### Request Requirements

A batch request requires a username and password, a content-type:multipart/form-data header, and a zip file as a form target.

##### Response Structure

### Batch Workflow


### Test Plan

{:.tests}
|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Batch to Scoring|curl --request POST --url https://insureright.valen.com/solutions/batch/version/insureright/scoring –u [username]:[password] --header 'content-type: multipart/form-data; --form 'file=@[object Object]'|200 OK|
|Basic profile|curl --request POST --url https://insureright.valentech.com/solutions/batch/5/profile –u [username]:[password] --header 'content-type: multipart/form-data --form 'batch-file=@[object Object]'|200 OK: GUID|
|Retrieve Profile|


create subscription for preliminary profile broad cast 
LONG POLL GET
http://localhost:8080/solutions/longpoll/subscribe?filter=name,preliminaryProfile,batchKey,ac5b3d51-50f8-40d1-e378-18f8db2fabc9
 
NOTE have to make a guid IFF you want notification that the profile is complete
 
post batchfile to profile with specified batch key
POST
http://localhost:8080/solutions/batch/5/profile?batchKey=ac5b3d51-50f8-40d1-e378-18f8db2fabc9
 
would like notification re batch activity
LOONG POLL GET
http://localhost:8080/solutions/longpoll/subscribe?filter=name,profileUpdatedEvent,customer,valen
note this subscription get all customer wide batch activity

submit a profiled files as manage claim_detail
GET
http://localhost:8080/solutions/batch/version/manage/claim_detail/51956d71-62be-c223-8cb0-518de3e7d048/submit
 
GET
http://localhost:8080/solutions/batch/<version>/<solution>/<submission>/<profile key>/submit
 
POST regular submit batch:
http://localhost:8080/solutions/batch/1/insureright/scoring?batchKey=fdb46259-6eb0-4f6d-860d-a7886b05b138
 
 
get profile:
http://localhost:8080/solutions/batch/version/unstructured/<profile key>/profile
 
 
subscription API might need own docs, iff we want to expose it, else, dont mention subscription