---
layout: page
title: Batch and Profiling requests
permalink: batch_request.html
sidebar: nav_sidebar
last_updated: December, 2016
summary: A guide for building requests take advantage of Valen's batch processing APIs
---

### Overview

The batch and profiling APIs are not solution specific. The solution is still required for batch purposes, but not for profiling. 

For batch uploads, the **solution** looks like this:
`/batch/insureright`

A sample batch request might look like this:

`https://insureright.valen.com/solutions/batch/insureright/scoring`

And a sample profile might look like this:

`https://insureright.valentech.com/solutions/batch/version/profile`

### Building a Batch Request

Batch and Profile requests are POSTs using multi-part form data.

##### Request Requirements

##### Response Structure

### Test Plan

{:.tests}
|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Batch to Scoring|curl --request POST --url https://insureright.valen.com/solutions/batch/version/insureright/scoring –u [username]:[password] --header 'content-type: multipart/form-data; --form 'file=@[object Object]'|200 OK|
|Basic profile|curl --request POST --url https://insureright.valentech.com/solutions/batch/5/profile –u [username]:[password] --header 'content-type: multipart/form-data --form 'batch-file=@[object Object]'|200 OK: GUID|
|Retrieve Profile|