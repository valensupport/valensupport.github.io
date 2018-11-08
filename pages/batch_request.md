---
layout: page
title: Batch requests
permalink: batch_request.html
sidebar: nav_sidebar
last_updated: July 2017
summary: A guide for building requests to take advantage of Valen's batch processing APIs
---

## Batch Application Overview

The Batch Application allows clients to transmit data to Valen for scoring with the Predict predictive analytic models. Data is provided using a pre-defined format, contained in separate delimited files. The Predict Batch Application may be accessed using the InsureRight Graphical User Interface (GUI) or directly via API.

Once initiated, the Batch Application validates the transmission, parses the input data from the delimited flat files, scores the data, and creates the resulting scoring data, which includes scores from each of the models and explains for the predictions in terms of influential data elements.

##### Audience

This guide is intended for use by developers who wish to integrate batch services. It is assumed the audience will be able to create the required data in the specified format and have been assigned the correct access credentials to process the InsureRight Batch Application

### Batch Application Flow

The input data for the Batch Application can be transmitted via the GUI or API, as a compressed “.zip” file format.

Each carrier can define their own naming convention according to their system requirements and constraints, provided the format is “.zip”. (See: Batch Application Processing Requirements ). 

The zip file cannot be password protected. It cannot contain subfolders and should contain one file per relation, see below.

### Scoring Submission File Details

Client systems must transmit data as a single compressed .zip file. The zipped file must contain related files containing the transmission data. Each line of business requires different files in order to be processed correctly:

##### Workers Compensation

insured.[csv, psv, txt]
class.[csv, psv, txt]

##### Commercial Auto

term.[csv, psv, txt]
unit.[csv, psv, txt]

Please note that the files within the .zip file may be delivered in .csv, .psv, or .tsv formats.

#### Input Data File Examples
Here is a sample for workers compensation. The importance of these examples is in the headers and the structure. The exact headers will differ depending on client solution. Login to access the data dictionary for exact column names. This text can be pasted into files `insured.csv` and `class.csv`. A zip file containing these two files will be a valid file for testing purposes:

The data is presented in pipe delimted (`.psv`) format to accomodate commas in the address (also useful if commas are in the insured name). The files in this case would be `insured.psv` and `class.psv`. Again, this data can be used as valid test data. The same file is duplicated later, just with commas as the delimeter.

**NOTE**: _It is possible to escape characters within the delimters with quotation marks. An insured name with commas inside a csv might look like this: `datavalue1, "insured name, containing commas", datavalue2`. This would resolve correctly in the Valen system_

##### Insured

>original_policy_term_number|term_effective_date|experience_mod_factor_initial|new_renew_flag|insured_name|policy_address|policy_city_name|policy_state_code|policy_zip_code|available_history_1|available_history_2|available_history_3|non_zero_claim_count_1|non_zero_claim_count_2|non_zero_claim_count_3|underwriter
>1234Test|7/7/2017|1|N|Test Submit|1234 Anystreet, Anytown, CO|Anytown|CO|80309|Y|N|Y|0||3|Test Person

##### Class

>original_policy_term_number|term_effective_date|state_code|class_code|payroll_amount_initial
>1234Test|7/7/2017|CO|4771|12345
>1234Test|7/7/2017|CO|8810|349

## Batch API

### Overview
The batch API allows integrations to process batch files containing many rows of data instead of running each individual record.

The batch API is a multipart/form-data that accepts a zip file as a payload. See the section above for instructions on creating the zip file.

The batch API will return a zip file of results in response to a request once processing is finished.

### Building a Batch Request

Batch requests are POSTs using multi-part form data.

To construct the URI we start with our base URL, `https://insureright.valen.com/api/`. We append the version, the action, and solution, for example: `2/batch/ca` or `2/batch/insureright`

The generalized form looks like this: `https://insureright.valen.com/api/2/batch/[solution]/[submission]`
A sample batch request url for a submission to workers compensation would look like this: `https://insureright.valen.com/api/2/batch/insureright/scoring`

A sample batch request url for a contributory submission to workers compensation would look like this: `https://insureright.valen.com/api/2/batch/profile`
For contributory batch submission, InsureRight will pick the correct solution based on the schema of the supplied data in the zip file.

##### Request Requirements

A batch request requires a username and password, a content-type:multipart/form-data header, and a zip file as a form target. 
As stated above, the zip file cannot be password protected and cannot contain subdirectories. If they do, the system will return an error. The files contained in the zip must be psv, csv, or tsv format. If they are not, the system will return an error.

##### Response Structure
The response will contain a GUID. This is a unique identifier for the submitted batch and can be used to retrieve the batch for scoring. Note that for contributory data, there is no support for retrieving the batch. However, you may login to InsureRight and go to the Batch->Profile->History tab to see the results of a Batch Contributory submission.

### Batch Workflow
In order to submit a compressed batch file containing delimited data for scoring, first you must `POST` the file to a web-service endpoint and retrieve a GUID which can the be used to poll for the results.

#### Scoring Batch Submission
In order to submit a batch, you must submit an `HTTPS` `POST` to the following endpoint:
 
  `POST`: `multipart-form-data`
  `https://insureright.valen.com/api/2/batch/[solution]/[submission]`

The solution and submission are encoded as part of the path.

  `POST`: `multipart-form-data`
  `https://insureright.valen.com/api/2/batch/[solution]/scoring`
 
The content type of the body must be `multipart/form-data` where one part has the name `batch-file`. If there is no such part, or if the `content-type` of the scoring request is not `multipart-form-data` the request will fail with a status code `400`.

If the request is successful, a GUID will be returned in the response. This GUID is used as the batch-key and is necessary for retrieving the results in the next step.

##### Scoring Batch Submission Summary

  parameters: solution, submission in path and 'batch-file' in form.
  consumes: multipart/form-data
  produces: text/plain

#### Scoring Batch Retrieval
Using the GUID returned from the submission, you will poll for the results a the following
endpoint:

  `GET`
  `https://insureright.valen.com/api/2/batch/insureright/scoring/[GUID]`

The `GET` will either return a `404`, a zip file containing results, or an error code. The `404` response is expected and may mean the batch is still processing or the GUID is invalid. If a `404` is received, continue making the request again on an interval of 5 minutes between requests.

A warning: large batches can take quite a while to finish processing.


### Contributory Batch Submission


In order to submit a batch, you must submit an `HTTPS` `POST` to the following endpoint:
 
  `POST`: `multipart-form-data`
  `https://insureright.valen.com/api/2/batch/[submission]`

The submission is encoded as part of the path.

  `POST`: `multipart-form-data`
  `https://insureright.valen.com/api/2/batch/profile`
 
The content type of the body must be `multipart/form-data` where one part has the name `batch-file`. If there is no such part, or if the `content-type` of the scoring request is not `multipart-form-data` the request will fail with a status code `400`.

If the request is successful, a GUID will be returned in the response. This GUID is used as the batch-key but at this point in time*cannot* be used to get the status of the batch job.


#### Batch Status Codes
Status codes returned from a batch submission may include

|Status|Message|Description|
|----|-------|-----------------|
|400|error opening zipfile Password protected zipfile not supported| 
|400|error opening zipfile zipfile could not be opened|
|400|File contains subdirectories, provide zips with only files|
|403|403:Forbidden|Credentials or authorization problem.|
|200||Returns a zip file with the results from batch processing.|
|405|Method Not allowed|Bad method used, batch requests need to be a POST|

Status codes returned from a batch results retrieval request may include:

|Status|Message|Description|
|----|-------|-----------------|
|403|403:Forbidden|Credentials or authorization problem.|
|404|404:Not Found|Either batch processing has not completed or the GUID is invalid.|
|200||Returns a zip file with the results from batch processing.|

### Test Plan

|Test|Request|Expected Response|
|----|-------|-----------------|
|Basic Batch Submit Scoring|curl -X POST -u "[username]":"[password]" -H "content-type: multipart/form-data" --form "batch-file=@[filename]" --url "https://insureright.valen.com/api/2/batch/insureright/scoring"|200 OK GUID|
|Basic Batch Retrieve Scoring|curl -X GET --url 'https://insureright.valentech.com/api/2/batch/insureright/scoring/[GUID]' -u "[username]":"[password]"|404 or 200 OK and Zip File|
|Basic Batch Submit Contributory Data|curl -X POST -u "[username]":"[password]" -H "content-type: multipart/form-data" --form "batch-file=@[filename]" --url "https://insureright.valen.com/api/2/batch/profile"|200 OK GUID|


### Sample Files

An example of Workers Compensation files can found above. Here are other samples.

#### Workers Comp

The data is presented in comma delimited (`.csv`) format. In order to accomodate commas in the address or insured name, values may be surrounded by double quotes. It is also possible to avoid this by using pipes or tabs to separate the values. The files in this case would be `insured.csv` and `class.csv`. Again, this data can be used as valid test data.

**NOTE**: _It is possible to escape characters within the delimeters with quotation marks. An insured name with commas inside a csv might look like this: `datavalue1, "insured name, containing commas", datavalue2`. This would resolve correctly in the Valen system._

##### Insured

>original_policy_term_number,term_effective_date,experience_mod_factor_initial,new_renew_flag,insured_name,policy_address, policy_city_name,policy_state_code,policy_zip_code,available_history_1,available_history_2,available_history_3, non_zero_claim_count_1,non_zero_claim_count_2,non_zero_claim_count_3,underwriter

>1234Test,7/7/2017,1,N,Test Submit,"1234 Anystreet, Anytown, CO",Anytown,CO,80309,Y,N,Y,0,,3,Test Person

##### Class

>original_policy_term_number,term_effective_date,state_code,class_code,payroll_amount_initial

>1234Test,7/7/2017,CO,4771,12345
>1234Test,7/7/2017,CO,8810,349

#### Commercial Auto

##### Term

>units_total_1,underwriter,new_renew_flag,policy_number,term_effective_date,mailing_address,claim_count_liab_1, available_history_liab_1,agency,mailing_city,available_history_liab_2,claim_count_liab_2,dot_number,vehicle_business_primary, available_units_total_1yr,available_history_1,claim_count_1,vehicle_size_primary,mailing_state_code,available_history_liab_3, claim_count_liab_3,insured_name,mailing_zip_code

>1,Test Person,R,test123,6/14/2016,"1234 Test Street, Anytown CO 80309",1,Y,,Anytown,Y,0,98765432,Anytown,Y,Y,3,L,CO,Y,0,CU Buffs,80309

##### Unit

>unit_vin,policy_number,term_effective_date,unit_premium_initial_total,unit_classification,unit_premium_initial_liab,unit_reference,garage_zip_code,unit_model_year

>1ABCD23EFG567890,test123,6/14/2016,12345,truck,1234,1234ABCD,80309,1987

#### Workers Comp Claims

##### Claim

>claim_number,claim_identifier,date_of_injury,claim_adjuster,claim_type_code,claim_severity_code,injured_worker_city, injured_worker_state,policy_num,claimant_gender,claimant height,claimant_weight,claimant_age, body_part_code,cause_of_injury_code,ICD_diagnosis_code,primary_work_class_code,specific_activity_desc,litigation_flag, modified_duty_available,officer_partner_owner,written_seatbelt_pol_in_place,wages_continued,were_safety_rules_violated, written_premium,medical_loss_paid,total_incurred,outstanding_reserve

>20140007263,12345,6/6/14,Bob,WC - Med Only,M1,Columbia,MO,2003444,M,70,175,55,0066,Burn,V72.81,9600,working by stove,N,Y, N, Y,Y,N,21345,200,200,0

