---
layout: page
title: Batch and Profiling requests
permalink: batch_request.html
sidebar: nav_sidebar
last_updated: December, 2016
summary: A guide for building requests take advantage of Valen's batch processing APIs
---

## Batch Application Overview

The Batch Application allows clients to transmit data to Valen for scoring with the Predict predictive analytic models. Data is provided using a pre-defined format, with data for each level (unit and term for Commercial Auto, insured and class for Workers Comp.) contained in a separate delimited file. The Predict Batch Application may be accessed using the InsureRight Graphical User Interface (GUI) or directly via API.

Once initiated, the Batch Application validates the transmission, parses the input data from the delimited flat files, scores the data, and creates the resulting scoring data, which includes scores from each of the models and explains for the predictions in terms of influential data elements.

##### Audience

This guide is intended for use by developers who wish to integrate batch services. It is assumed the audience will be able to create the required data in the specified format and have been assigned the correct access credentials to process the InsureRight Batch Application

### Batch Application Flow

The input data for the Batch Application can be transmitted via the GUI or API, as a compressed “.zip” file format.

Each carrier can define their own naming convention according to their system requirements and constraints, providing the word “batch” is contained somewhere in the file name and the format is “.zip”. (See: Batch Application Processing Requirements ). 

The zip file cannot be password protected. It cannot contain subfolders and should contain two files, see below.

### Submission File Details

Client systems must transmit data as a single compressed .zip file. The zipped file must contain the two data files containing the transmission data which correspond to all required data nodes. There are two nodes defined in the data structure for submitting data for InsureRight/Workers Compensation, so the following files must exist in the batch transmission:
insured.[csv, psv, txt]
class.[csv, psv, txt]

Likewise, Commercial Auto has two nodes, term and unit:
term.[csv, psv, txt]
unit.[csv, psv, txt]

Please note that the files within the .zip file may be delivered in .csv, .psv, or .txt formats. The only constraint is that the formats be the same in each .zip instance.

#### Input Data File Examples
Here is a sample for commercial auto. The importance of these examples is in the headers and the structure. The data is randomly generated. The exact headers will differ depending on solution. Check the data dictionary for exact column names:

##### Term

>total_discretion_factor,units_total_1,claim_count_liab_3,underwriter,new_renew_flag,policy_number,term_effective_date,available_units_total_1yr,mailing_address,claim_count_liab_1,agency,mailing_city,available_history_liab_2,dot_number,available_history_liab_1,vehicle_business_primary,available_history_1,vehicle_size_primary,claim_count_liab_2,claim_count_1,mailing_state_code,available_history_liab_3,insured_name,mailing_zip_code
,,,,R,pmmvbtjtksbnegxy,11/23/2013,N,,,,,Y,,N,,Y,,,48390,,N,qltqdrrfrjolxckbcfcfeggtvxcidwauawcpwnrepbrwl,
1673.886548,,89043,wdkwrtmscjobgqpwkpkmobkwghgsmswatkjfgdstxexxqppwrqeetryovtftyfwdiyksfkrmjmageemfnbsqptnaxdqbe,R,lyualmjrntwcwcwmihtis,11/23/2013,Y,plunrdqseusmguwgfnrtkrkhxvxrhfgxirjukxpypouwaelpehsuslbmijivvjt,76804,,,Y,,N,q

##### Unit

>unit_vin,policy_number,term_effective_date,unit_premium_initial_total,unit_classification,unit_premium_initial_liab,unit_reference,garage_zip_code,unit_model_year
bvtnvusbqtvdvt,pmmvbtjtksbnegxy,11/23/2013,64710.59521,qkdco,,ekvjpoplcfcrcdp,40150,,pmmvbtjtksbnegxy,11/23/2013,56551.61986,hcdt,96360.59676,fayebeetqbunmehrffuhlrqvrbqkeodw,40150,

## Batch API

### Overview
The batch API allows integrations to process batch files containing many rows of data instead of running each individual record.

The batch API is a multipart/form-data that accepts a zip file as a payload. See the section above for instructions on creating the zip file.

The batch API will return a zip file of results in response to a request once processing is finished.

### Building a Batch Request

Batch requests are POSTs using multi-part form data.

To construct the URI we start with our base URL, `https://insureright.valen.com/api/`. We append the version, the action, and solution, for example: `2/batch/ca` or `2/batch/insureright`

The generalized form looks like this: `https://insureright.valen.com/api/2/batch/[solution]/[submission]`
A sample batch request url for a submission to workers compensation might look like this: `https://insureright.valen.com/api/2/batch/insureright/scoring`

##### Request Requirements

A batch request requires a username and password, a content-type:multipart/form-data header, and a zip file as a form target. 
As stated above, the zip file cannot be password protected and cannot contain subdirectories. If they do, the system will return an error. The files contained in the zip must be psv, csv, or tsv format. If they are not, the system will return an error.

##### Response Structure
The response will contain a GUID. This is a unique identifier for the submitted batch and can be used to retrieve the batch.

### Batch Workflow
In order to submit a compressed batch file containing delimited data for scoring, first you must `POST` the
file to a web-service endpoint and retrieve a UUID which can the be used to poll for the results.

#### Batch Scoring Submission
In order to submit a batch, you must submit an `HTTPS` `POST` to the following endpoint:
 
  `POST`: `multipart-form-data`
  `https://insureright.valen.com/api/2/batch/[solution]/[submission]`

The solution and submission are encoded as part of the path. The submission; however,
will always be scoring, as below:

  `POST`: `multipart-form-data`
  `https://insureright.valen.com/api/2/batch/[solution]/scoring`
 
The content type of the body must be `multipart/form-data` where one part has the
name `batch-file`. If there is no such part, or if the `content-type` of the scoring
request is not `multipart-form-data` the request will fail with a status code `400`.

If the request is successful, a GUID will be returned in the response.

##### Batch Scoring Submission Summary

  parameters: solution, submission in path and 'batch-file' in form.
  consumes: multipart/form-data
  produces: text/plain

#### Batch Scoring Retrieval
Using the GUID returned from the submission, you will poll for the results a the following
endpoint:

  `GET`
  `https://insureright.valen.com/api/2/batch/ca/scoring/[GUID]`

The `GET` will either return a `404`, a zip file containing results, or an error code. See the status code section for more information about what those mean. The `404` response is expected and may mean the batch is still processing or the GUID is invalid. If a `404` is received, continue making the request again on an interval of 5 minutes between requests.

A warning: large batches can take quite a while to finish processing.

#### Batch Status Codes
Status codes returned from a batch submission may include

|Status|Message|Description|
|----|-------|-----------------|
|400|error opening zipfile Password protected zipfile not supported| 
|400|error opening zipfile zipfile could not be opened|
|400|File contains subdirectories, provide zips with only files|
|403|403:Forbidden|Credentials or authorization problem.|
|200||Returns a zip file with the results from batch processing.|

Status codes returned from a batch results retrieval request may include

|Status|Message|Description|
|----|-------|-----------------|
|403|403:Forbidden|Credentials or authorization problem.|
|404|404:Not Found|Either batch processing has not completed or the GUID is invalid.|
|200||Returns a zip file with the results from batch processing.|

### Test Plan

|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Batch Submit|curl --request POST --url 'https://insureright.valen.com/api/2/batch/insureright/scoring' –u [username]:[password] --header 'content-type: multipart/form-data; --form 'file=@[object Object]'|200 OK GUID|
|Basic Batch Retrieve|curl -X GET --url 'https://insureright.valentech.com/api/2/batch/ca/scoring/[GUID]' -u [username]:[password]|404 or 200 OK and Zip File|