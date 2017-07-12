---
layout: page
title: Collected test plans
permalink: test_plans.html
sidebar: nav_sidebar
toc: false
last_updated: December, 2016
summary: A collection of test plans for acceptance of integration with Valen Analytics
---

Here is a collection of the integration test plans for Valen's Web Services. It's organized into suites based on the Solution. Cross-solution APIs like Profile and Search are repeated on a solution-by-solution basis for ease of test replication.

### InsureRight score submission

{:.tests}
|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Scoring|curl --request POST -u user:password --header "content-type: application/xml" --data "" --url "https://insureright.valen.com/solutions/insureright/scoring"`| 200 OK: Scoring Response here|
|Retrieve PDF|`curl --request GET  -u [username]:[password] --header "content-type: application/xml" --url 'https://insureright.valen.com/solutions/insureright/scoring/[scoreID]?format=pdf'`| 200 OK: PDF contents will be returned in Base 64 format|
|Retrieve Without Params|`curl -X GET -u [username]:[password] --header "content-type: application/xml" --url 'https://insureright.valen.com/solutions/scores/query/insureright/scoring'`|200 OK: Response will be a list of scored records. Max is 1000 records long|


### CA Batch submission

{:.tests}
|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Batch Submit|curl -X POST -u "[username]":"[password]" -H "content-type: multipart/form-data" --form "batch-file=@[filePATH]" --url "https://insureright.valen.com/api/2/batch/insureright/scoring"|200 OK GUID|
|Basic Batch Retrieve|curl -X GET --url 'https://insureright.valentech.com/api/2/batch/insureright/scoring/[GUID]' -u "[username]":"[password]"|404 or 200 OK and Zip File|