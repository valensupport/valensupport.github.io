---
layout: page
title: Collected test plans
permalink: test_plans.html
sidebar: nav_sidebar
toc: false
last_updated: May, 2023
summary: A collection of example integrations with Valen Analytics
---

Here is a collection of sample integrations for Valen's Web Services. 

### InsureRight (Workers Comp) score submission

{:.tests}
|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Scoring|`curl -X POST -u "[username]":"[password]" -H "content-type: application/xml" -d @[filePATH] https://insureright.valen.com/api/2/document/insureright/scoring`| 200 OK: Scoring Response here|
|Retrieve PDF|`curl -X GET -u "[username]":"[password]" -H "accept: application/octet-stream" https://insureright.valen.com/api/2/reports/insureright/scoring/[scoreID]/predict`| 200 OK: PDF contents will be returned in native PDF format|
|Retrieve Without Params|`curl -X GET -u "[username]":"[password]" -H "content-type: application/xml" https://insureright.valen.com/solutions/scores/query/insureright/scoring`|200 OK: Response will be a list of scored records. Max is 1000 records long|


### CA Batch score submission

{:.tests}
|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Batch Submit|`curl -X POST -u "[username]":"[password]"` `-H "content-type: multipart/form-data"` `--form "batch-file=@[filePATH]"` `--url "https://insureright.valen.com/api/2/batch/ca/scoring"`|200 OK GUID|
|Basic Batch Retrieve|`curl -X GET` `--url "https://insureright.valen.com/api/2/batch/ca/scoring/[GUID]"` `-u "[username]":"[password]"`|404 or 200 OK and Zip File|


### Workers Comp Claims score submission

{:.tests}
|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Scoring|`curl -X POST -u "[username]":"[password]" -H "content-type: application/xml" -d @[filePATH] https://insureright.valen.com/api/2/document/wc_ce/scoring`| 200 OK: Scoring Response here|
|Basic Batch Submit|`curl -X POST -u "[username]":"[password]"`  `-H "content-type: multipart/form-data"` `--form "batch-file=@[filePATH]"` `--url "https://insureright.valen.com/api/2/batch/wc_ce/scoring"`|200 OK GUID|
|Basic Batch Retrieve|`curl -X GET` `--url "https://insureright.valen.com/api/2/batch/wc_ce/scoring/[GUID]"` `-u "[username]":"[password]"`|404 or 200 OK and Zip File|
