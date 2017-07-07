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
|Basic Scoring|`curl --request POST --url 'https://insureright.valen.com/solutions/insureright/scoring' –u **[username]:[password]** --header "content-type: application/xml" --data [data here]`| 200 OK: Scoring Response here|
|Retrieve PDF|`curl --request GET --url 'https://insureright.valen.com/solutions/insureright/scoring/[scoreID]?format=pdf' –u [username]:[password] --header 'content-type: application/xml'`| 200 OK: PDF contents will be returned in Base 64 format|
|Retrieve Without Params|`curl --request GET --url 'https://insureright.valen.com/solutions/scores/query/insureright/scoring' –u [username]:[password] --header 'content-type: application/xml'`|200 OK: Response will be a list of scored records. Max is 1000 records long|
|<a href="#" data-toggle="tooltip" data-original-title="{{ site.data.glossary.retrieve_with_query }}">Retrieve With Query</a>|`curl --request GET --url 'https://insureright.valen.com/solutions/scores/query/insureright/scoring?element=insured%2Cpolicy_state_code%2Cvalue%2Ccontains%2CKY' –u [username]:[password] --header 'content-type: application/xml'`|200 OK: Response will be a list of scored records limited according to query param|
|Retrieve with Score ID|`curl --request GET --url 'https://insureright.valen.com/solutions/insureright/scoring/[scoreID]?format=pdf' –u [username]:[password] --header 'content-type: application/xml'`|200 OK: Record corresponding to score ID|


### CA Batch submission

{:.tests}
|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Batch Submit|curl -X POST -u "[username]":"[password]" -H "content-type: multipart/form-data" --form "batch-file=@[filename]" --url "https://insureright.valen.com/api/2/batch/insureright/scoring"|200 OK GUID|
|Basic Batch Retrieve|curl -X GET --url 'https://insureright.valentech.com/api/2/batch/insureright/scoring/[GUID]' -u "[username]":"[password]"|404 or 200 OK and Zip File|