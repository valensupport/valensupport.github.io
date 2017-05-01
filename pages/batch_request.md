---
layout: page
title: Batch and Profiling requests
permalink: batch_request.html
sidebar: nav_sidebar
last_updated: December, 2016
summary: A guide for building requests take advantage of Valen's batch processing APIs
---

## Batch Application Overview

The Batch Application allows clients to transmit data to Valen for scoring with the Predict predictive analytic models. Data is provided using a pre-defined format, with data for each level (unit and term for Commercial Auto, insured and class for Workers Comp.) contained in a separate delimited file. The Predict Batch Application may be accessed using the InsureRight Graphical User Interface (GUI) or directly via API
Once initiated, the Batch Application validates the transmission, parses the input data from the delimited flat files, scores the data, and creates the resulting scoring data, which includes scores from each of the models and explains for the predictions in terms of influential data elements.

##### Audience

This guide is intended for use by developers who wish to integrate batch services. It is assumed the audience will be able to create the required data in the specified format and have been assigned the correct access credentials to process the InsureRight Batch Application

 
### Batch Application Flow

The input data for the Batch Application can be transmitted via the GUI or AP, as a compressed “.zip” file format.

Each carrier can define their own naming convention according to their system requirements and constraints, providing the word “batch” is contained somewhere in the file name and the format is “.zip”. (See: Batch Application Processing Requirements ).

### Submission File Details

Client systems must transmit data as a single compressed .zip file. The zipped file must contain the two data files containing the transmission data which correspond to all required data nodes. There are two nodes defined in the data structure for submitting data for InsureRight/Workers Compensation, so the following files must exist in the batch transmission:
insured.[csv, psv, txt]
class.[csv, psv, txt]

Likewise, Commercial Auto has two nodes, term and unit:
term.[csv, psv, txt]
unit.[csv, psv, txt]

Please note that the files within the .zip file may be delivered in .csv, .psv, or .txt formats. The only constraint is that the formats be the same in each .zip instance.

 
#### Input Data File Examples
Here is a sample for commercial auto. The importance of these examples is in the headers and the structure. The data is randomly generated. The exact headers:

##### Term

total_discretion_factor,units_total_1,claim_count_liab_3,underwriter,new_renew_flag,policy_number,term_effective_date,available_units_total_1yr,mailing_address,claim_count_liab_1,agency,mailing_city,available_history_liab_2,dot_number,available_history_liab_1,vehicle_business_primary,available_history_1,vehicle_size_primary,claim_count_liab_2,claim_count_1,mailing_state_code,available_history_liab_3,insured_name,mailing_zip_code
,,,,R,pmmvbtjtksbnegxy,11/23/2013,N,,,,,Y,,N,,Y,,,48390,,N,qltqdrrfrjolxckbcfcfeggtvxcidwauawcpwnrepbrwl,
1673.886548,,89043,wdkwrtmscjobgqpwkpkmobkwghgsmswatkjfgdstxexxqppwrqeetryovtftyfwdiyksfkrmjmageemfnbsqptnaxdqbe,R,lyualmjrntwcwcwmihtis,11/23/2013,Y,plunrdqseusmguwgfnrtkrkhxvxrhfgxirjukxpypouwaelpehsuslbmijivvjt,76804,,,Y,,N,qmxqsvemcqejmhrmipwoamfkahhdh,N,,88492,,,N,,40150

##### Unit

unit_vin,policy_number,term_effective_date,unit_premium_initial_total,unit_classification,unit_premium_initial_liab,unit_reference,garage_zip_code,unit_model_year
bvtnvusbqtvdvt,pmmvbtjtksbnegxy,11/23/2013,64710.59521,qkdco,,ekvjpoplcfcrcdp,40150,
,pmmvbtjtksbnegxy,11/23/2013,56551.61986,hcdt,96360.59676,fayebeetqbunmehrffuhlrqvrbqkeodw,40150,


## Batch API

### Overview
The batch API allows integrations to process batch files containing many rows of data instead of running each individual record.

The batch API accepts a zip file as a payload. What the zip file contains depends for which solution the integration is written. _need some sort of information here_


### Building a Batch Request

Batch and Profile requests are POSTs using multi-part form data.

Constructing the URI is similar to the scoring URI with a few key differences.

First we start with our base URL, `https://insureright.valen.com/solutions/`. We append the action, version, and solution, for example: `batch/5/ca` or `batch/5/insureright`

A sample batch url might look like this:

`https://insureright.valen.com/solutions/batch/5/insureright/scoring`

##### Request Requirements

A batch request requires a username and password, a content-type:multipart/form-data header, and a zip file as a form target.

##### Response Structure

### Batch Workflow
Submitting a batch requires a little prep work if you want to know when the batch finishes processing. The first step is to create an identification number. We use GUIDs because there's a reasonable expectation any generated GUID will be unique. More information can be found at `https://en.wikipedia.org/wiki/Universally_unique_identifier`

Once you've created a GUID, send a long polling request to Valen, using the GUID as a batch key.

`https://insureright.valen.com/solutions/longpoll/subscribe?filter=name,preliminaryProfile,batchKey,xxxxxxxx-50f8-40d1-e378-18f8db2fabc9`

_preliminaryProfile argument is probably wrong_

Next, post the batch file, again using the GUID as a batch key

`https://insureright.valen.com/solutions/batch/version/insureright/scoring?batchKey=xxxxxxxx-50f8-40d1-e378-18f8db2fabc9`

Using the GUID in this fashion will link the polling request to the batch processing job.

_how to retrieve response from polling?_

_how to retrieve batch results_

### Test Plan

{:.tests}
|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Batch to Scoring|curl --request POST --url https://insureright.valen.com/solutions/batch/version/insureright/scoring –u [username]:[password] --header 'content-type: multipart/form-data; --form 'file=@[object Object]'|200 OK|
|Basic profile|curl --request POST --url https://insureright.valentech.com/solutions/batch/5/profile –u [username]:[password] --header 'content-type: multipart/form-data --form 'batch-file=@[object Object]'|200 OK: GUID|
|Retrieve Profile|